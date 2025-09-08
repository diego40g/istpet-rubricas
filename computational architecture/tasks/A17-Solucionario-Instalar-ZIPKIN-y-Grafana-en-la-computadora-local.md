# A17: Solucionario - Instalar ZIPKIN y Grafana en la computadora local

## 📝 Resumen de la Actividad
**Objetivo:** Instalar y configurar correctamente ZIPKIN (para trazabilidad de servicios distribuidos) y Grafana (para monitoreo de métricas) en la computadora local.

---

## 🎯 Solución Paso a Paso

### 1. Preparación del Entorno

#### **🖥️ Requisitos del Sistema**

**Mínimos:**
- **RAM:** 4GB disponible
- **Storage:** 2GB espacio libre
- **CPU:** 2 núcleos
- **OS:** Windows 10+, Ubuntu 18.04+, macOS 10.14+
- **Java:** OpenJDK 11+ (para ZIPKIN)
- **Conectividad:** Internet para descarga inicial

**Recomendados:**
- **RAM:** 8GB o más
- **Storage:** 5GB espacio libre
- **CPU:** 4+ núcleos
- **SSD:** Para mejor rendimiento

#### **📋 Software Previo Requerido**

**Java Development Kit (JDK):**
```bash
# Ubuntu/Debian
sudo apt update
sudo apt install openjdk-11-jdk

# CentOS/RHEL
sudo yum install java-11-openjdk-devel

# Windows (usando Chocolatey)
choco install openjdk11

# macOS (usando Homebrew)
brew install openjdk@11
```

**Verificar instalación de Java:**
```bash
java -version
# Output esperado: openjdk version "11.0.x"
```

---

### 2. Instalación de ZIPKIN

#### **🔄 Método 1: JAR Ejecutable (Recomendado)**

**Paso 1: Descargar ZIPKIN**
```bash
# Crear directorio de trabajo
mkdir ~/zipkin-setup
cd ~/zipkin-setup

# Descargar la última versión
curl -sSL https://zipkin.io/quickstart.sh | bash -s
# O descarga manual:
wget https://repo1.maven.org/maven2/io/zipkin/zipkin-server/2.24.0/zipkin-server-2.24.0-exec.jar
```

**Paso 2: Ejecutar ZIPKIN**
```bash
# Ejecutar con configuración por defecto
java -jar zipkin-server-2.24.0-exec.jar

# Con configuraciones personalizadas
java -jar zipkin-server-2.24.0-exec.jar \
  --zipkin.storage.type=elasticsearch \
  --zipkin.storage.elasticsearch.hosts=http://localhost:9200
```

**Paso 3: Verificar instalación**
```bash
# Probar conectividad
curl http://localhost:9411/health
# Respuesta esperada: {"status":"UP"}

# Verificar interfaz web
curl -I http://localhost:9411
# Respuesta esperada: HTTP/1.1 200 OK
```

#### **🐳 Método 2: Docker (Alternativo)**

**Paso 1: Instalar Docker**
```bash
# Ubuntu
sudo apt update
sudo apt install docker.io
sudo systemctl enable docker
sudo systemctl start docker

# Agregar usuario al grupo docker
sudo usermod -aG docker $USER
# Cerrar sesión y volver a entrar
```

**Paso 2: Ejecutar ZIPKIN con Docker**
```bash
# Ejecutar contenedor de ZIPKIN
docker run -d -p 9411:9411 --name zipkin openzipkin/zipkin

# Verificar que está ejecutándose
docker ps | grep zipkin

# Ver logs del contenedor
docker logs zipkin
```

**Paso 3: Configurar persistencia (opcional)**
```bash
# Crear volumen para datos
docker volume create zipkin-data

# Ejecutar con almacenamiento persistente
docker run -d -p 9411:9411 \
  -v zipkin-data:/zipkin/data \
  --name zipkin \
  openzipkin/zipkin
```

#### **📊 Configuración de ZIPKIN**

**Archivo de configuración `zipkin.properties`:**
```properties
# Puerto del servidor
server.port=9411

# Configuración de almacenamiento
zipkin.storage.type=mem
# Para Elasticsearch: zipkin.storage.type=elasticsearch
# Para MySQL: zipkin.storage.type=mysql

# Configuración de colectores
zipkin.collector.http.enabled=true
zipkin.collector.grpc.enabled=true

# Configuración de interfaz web
zipkin.ui.enabled=true
zipkin.ui.base-path=/

# Métricas y monitoreo
management.metrics.export.prometheus.enabled=true
```

---

### 3. Instalación de Grafana

#### **💻 Método 1: Paquete Nativo (Linux)**

**Ubuntu/Debian:**
```bash
# Agregar repositorio oficial
sudo apt-get install -y software-properties-common
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"

# Actualizar e instalar
sudo apt-get update
sudo apt-get install grafana

# Habilitar e iniciar servicio
sudo systemctl enable grafana-server
sudo systemctl start grafana-server
```

**CentOS/RHEL:**
```bash
# Agregar repositorio
sudo tee /etc/yum.repos.d/grafana.repo << EOF
[grafana]
name=grafana
baseurl=https://packages.grafana.com/oss/rpm
repo_gpgcheck=1
enabled=1
gpgcheck=1
gpgkey=https://packages.grafana.com/gpg.key
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
EOF

# Instalar
sudo yum install grafana

# Iniciar servicio
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

#### **🪟 Método 2: Windows**

**Instalación manual:**
1. Descargar desde: https://grafana.com/grafana/download
2. Extraer archivo ZIP
3. Ejecutar `bin/grafana-server.exe`

**PowerShell automatizado:**
```powershell
# Descargar Grafana
$url = "https://dl.grafana.com/oss/release/grafana-9.0.0.windows-amd64.zip"
$output = "$env:USERPROFILE\Downloads\grafana.zip"
Invoke-WebRequest -Uri $url -OutFile $output

# Extraer
Expand-Archive -Path $output -DestinationPath "C:\grafana"

# Ejecutar
cd "C:\grafana\grafana-9.0.0"
.\bin\grafana-server.exe
```

#### **🍎 Método 3: macOS**

**Homebrew:**
```bash
# Instalar Homebrew si no está disponible
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Instalar Grafana
brew update
brew install grafana

# Iniciar servicio
brew services start grafana
```

#### **🐳 Método 4: Docker (Multiplataforma)**

```bash
# Ejecutar Grafana con Docker
docker run -d \
  -p 3000:3000 \
  --name grafana \
  -e "GF_SECURITY_ADMIN_PASSWORD=admin" \
  grafana/grafana:latest

# Con persistencia de datos
docker run -d \
  -p 3000:3000 \
  --name grafana \
  -v grafana-data:/var/lib/grafana \
  -e "GF_SECURITY_ADMIN_PASSWORD=admin" \
  grafana/grafana:latest
```

---

### 4. Configuración Inicial

#### **🔐 Acceso a Grafana**

**Primera conexión:**
1. Abrir navegador en: http://localhost:3000
2. **Usuario:** admin
3. **Contraseña:** admin
4. Cambiar contraseña en primer acceso

**Configuración de seguridad:**
```bash
# Archivo de configuración: /etc/grafana/grafana.ini
[security]
admin_user = admin
admin_password = nueva_contraseña_segura

[server]
http_port = 3000
domain = localhost
```

#### **⚙️ Configuración de ZIPKIN**

**Panel web de ZIPKIN:**
- **URL:** http://localhost:9411
- **Interfaz:** Web UI integrada
- **API:** http://localhost:9411/api/v2/

**Enviar datos de prueba:**
```bash
# Enviar traza de prueba con curl
curl -X POST http://localhost:9411/api/v2/spans \
  -H 'Content-Type: application/json' \
  -d '[{
    "traceId": "1234567890123456",
    "id": "1234567890123456",
    "name": "test-span",
    "timestamp": '$(date +%s)'000000,
    "duration": 100000,
    "localEndpoint": {
      "serviceName": "test-service",
      "ipv4": "127.0.0.1",
      "port": 8080
    }
  }]'
```

---

### 5. Verificación de Funcionamiento

#### **✅ Lista de Verificación ZIPKIN**

**Conectividad básica:**
```bash
# Test 1: Health check
curl http://localhost:9411/health
# Esperado: {"status":"UP"}

# Test 2: API endpoints
curl http://localhost:9411/api/v2/services
# Esperado: [] o lista de servicios

# Test 3: Interfaz web
curl -I http://localhost:9411
# Esperado: HTTP/1.1 200 OK
```

**Funcionalidad avanzada:**
```bash
# Test 4: Buscar trazas
curl "http://localhost:9411/api/v2/traces?limit=10"

# Test 5: Obtener dependencias
curl http://localhost:9411/api/v2/dependencies?endTs=$(date +%s)000
```

#### **✅ Lista de Verificación Grafana**

**Acceso y autenticación:**
1. Navegar a http://localhost:3000
2. Login exitoso con credenciales
3. Dashboard principal visible
4. Configuración accesible

**Funcionalidades core:**
```bash
# Test API de Grafana
curl -u admin:admin http://localhost:3000/api/health
# Esperado: {"commit":"...","database":"ok","version":"..."}

# Test datasources
curl -u admin:admin http://localhost:3000/api/datasources
# Esperado: [] (lista vacía inicialmente)
```

---

### 6. Configuración de Data Source Básico

#### **🔗 Conectar Grafana con Prometheus (Para métricas de ZIPKIN)**

**Instalar Prometheus (opcional):**
```bash
# Descargar Prometheus
wget https://github.com/prometheus/prometheus/releases/download/v2.37.0/prometheus-2.37.0.linux-amd64.tar.gz
tar xvfz prometheus-*.tar.gz
cd prometheus-*

# Configurar prometheus.yml
cat << EOF > prometheus.yml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'zipkin'
    static_configs:
      - targets: ['localhost:9411']
    metrics_path: '/prometheus'
    scrape_interval: 5s
EOF

# Ejecutar Prometheus
./prometheus --config.file=prometheus.yml --storage.tsdb.path=./data
```

**Configurar data source en Grafana:**
1. Configuration → Data Sources → Add data source
2. Seleccionar "Prometheus"
3. URL: http://localhost:9090
4. Save & Test

#### **📊 Panel de Prueba Simple**

**Crear dashboard básico:**
1. Dashboards → New → New Dashboard
2. Add → Visualization
3. Query: `up{job="zipkin"}`
4. Título: "ZIPKIN Status"
5. Save dashboard

---

### 7. Scripts de Automatización

#### **🚀 Script de Instalación Completa (Linux)**

**Archivo `install_observability_stack.sh`:**
```bash
#!/bin/bash

echo "🚀 Instalando Stack de Observabilidad (ZIPKIN + Grafana)"

# Verificar Java
if ! command -v java &> /dev/null; then
    echo "📦 Instalando Java 11..."
    sudo apt update
    sudo apt install -y openjdk-11-jdk
fi

# Instalar ZIPKIN
echo "📡 Instalando ZIPKIN..."
mkdir -p ~/observability
cd ~/observability
wget -O zipkin.jar https://repo1.maven.org/maven2/io/zipkin/zipkin-server/2.24.0/zipkin-server-2.24.0-exec.jar

# Crear script de inicio para ZIPKIN
cat << EOF > start_zipkin.sh
#!/bin/bash
cd ~/observability
java -jar zipkin.jar
EOF
chmod +x start_zipkin.sh

# Instalar Grafana
echo "📊 Instalando Grafana..."
sudo apt-get install -y software-properties-common
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
sudo apt-get update
sudo apt-get install -y grafana

# Habilitar servicios
sudo systemctl enable grafana-server
sudo systemctl start grafana-server

# Crear servicios systemd para ZIPKIN
sudo tee /etc/systemd/system/zipkin.service << EOF
[Unit]
Description=Zipkin Server
After=network.target

[Service]
Type=simple
User=$USER
WorkingDirectory=$HOME/observability
ExecStart=/usr/bin/java -jar zipkin.jar
Restart=always

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl enable zipkin
sudo systemctl start zipkin

echo "✅ Instalación completada!"
echo "🔗 ZIPKIN: http://localhost:9411"
echo "🔗 Grafana: http://localhost:3000 (admin/admin)"
```

#### **🔄 Script de Verificación**

**Archivo `verify_installation.sh`:**
```bash
#!/bin/bash

echo "🔍 Verificando instalación del stack de observabilidad..."

# Verificar Java
echo -n "Java: "
if command -v java &> /dev/null; then
    echo "✅ $(java -version 2>&1 | head -n1)"
else
    echo "❌ No instalado"
fi

# Verificar ZIPKIN
echo -n "ZIPKIN: "
if curl -s http://localhost:9411/health | grep -q "UP"; then
    echo "✅ Funcionando en puerto 9411"
else
    echo "❌ No accesible"
fi

# Verificar Grafana
echo -n "Grafana: "
if curl -s http://localhost:3000/api/health | grep -q "ok"; then
    echo "✅ Funcionando en puerto 3000"
else
    echo "❌ No accesible"
fi

# Verificar puertos
echo "📡 Puertos en uso:"
netstat -tulpn | grep -E "(9411|3000)" | while read line; do
    echo "  $line"
done

# Test de datos
echo -n "Test de datos ZIPKIN: "
if curl -s -X POST http://localhost:9411/api/v2/spans \
   -H 'Content-Type: application/json' \
   -d '[{"traceId":"test123","id":"span123","name":"test","timestamp":'$(date +%s)'000000,"duration":1000}]' \
   | grep -q "202"; then
    echo "✅ Acepta datos correctamente"
else
    echo "❌ Error enviando datos"
fi

echo "🎯 Verificación completada"
```

---

### 8. Configuración de Servicios del Sistema

#### **🔧 Configurar como Servicios del Sistema (Linux)**

**Servicio systemd para ZIPKIN:**
```bash
# Crear archivo de servicio
sudo tee /etc/systemd/system/zipkin.service << EOF
[Unit]
Description=Zipkin Distributed Tracing System
Documentation=https://zipkin.io/
After=network.target
Wants=network.target

[Service]
Type=simple
User=zipkin
Group=zipkin
ExecStart=/usr/bin/java -jar /opt/zipkin/zipkin.jar
WorkingDirectory=/opt/zipkin
StandardOutput=journal
StandardError=journal
Restart=always
RestartSec=3
TimeoutStopSec=10

[Install]
WantedBy=multi-user.target
EOF

# Crear usuario del sistema
sudo useradd --system --home-dir /opt/zipkin --shell /bin/false zipkin
sudo mkdir -p /opt/zipkin
sudo cp ~/observability/zipkin.jar /opt/zipkin/
sudo chown -R zipkin:zipkin /opt/zipkin

# Habilitar e iniciar
sudo systemctl daemon-reload
sudo systemctl enable zipkin
sudo systemctl start zipkin
```

#### **🪟 Configurar como Servicio (Windows)**

**Script PowerShell para servicio:**
```powershell
# Instalar NSSM (Non-Sucking Service Manager)
choco install nssm

# Crear servicio para ZIPKIN
nssm install Zipkin "C:\Program Files\Java\jdk-11\bin\java.exe"
nssm set Zipkin Parameters "-jar C:\zipkin\zipkin.jar"
nssm set Zipkin DisplayName "Zipkin Tracing Server"
nssm set Zipkin Description "Distributed request tracing system"
nssm set Zipkin Start SERVICE_AUTO_START

# Iniciar servicio
nssm start Zipkin
```

---

### 9. Troubleshooting Común

#### **🔧 Problemas Frecuentes y Soluciones**

**ZIPKIN no inicia:**
```bash
# Verificar Java
java -version

# Verificar puerto disponible
netstat -tulpn | grep 9411

# Verificar logs
journalctl -u zipkin -f

# Ejecutar en modo debug
java -jar zipkin.jar --debug
```

**Grafana no accesible:**
```bash
# Verificar estado del servicio
sudo systemctl status grafana-server

# Verificar logs
sudo journalctl -u grafana-server -f

# Verificar configuración
sudo cat /etc/grafana/grafana.ini | grep -E "(port|domain)"

# Reiniciar servicio
sudo systemctl restart grafana-server
```

**Problemas de conectividad:**
```bash
# Verificar firewall (Ubuntu)
sudo ufw status
sudo ufw allow 9411
sudo ufw allow 3000

# Verificar iptables
sudo iptables -L | grep -E "(9411|3000)"
```

**Error de memoria Java:**
```bash
# Aumentar memoria heap para ZIPKIN
java -Xmx2g -jar zipkin.jar

# Verificar memoria disponible
free -h
```

---

### 10. Documentación de la Instalación

#### **📄 Template de Reporte**

**Estructura del documento:**
```markdown
# Reporte de Instalación - ZIPKIN y Grafana

## Información del Sistema
- **SO:** Ubuntu 20.04 LTS
- **Arquitectura:** x86_64
- **RAM:** 8GB
- **Java Version:** OpenJDK 11.0.16

## Proceso de Instalación

### ZIPKIN
- **Método utilizado:** JAR ejecutable
- **Versión:** 2.24.0
- **Puerto:** 9411
- **Estado:** ✅ Funcionando

### Grafana
- **Método utilizado:** APT package
- **Versión:** 9.0.0
- **Puerto:** 3000
- **Estado:** ✅ Funcionando

## Verificaciones Realizadas
[Incluir screenshots y outputs de comandos]

## Problemas Encontrados
[Documentar issues y sus soluciones]

## Configuraciones Aplicadas
[Detallar configuraciones especiales]
```

#### **📸 Capturas Requeridas**

1. **Terminal con instalación exitosa:**
   - Comandos ejecutados
   - Outputs de verificación
   - Versiones instaladas

2. **ZIPKIN Web UI:**
   - Página principal
   - Health check visible
   - Interfaz funcional

3. **Grafana Login:**
   - Pantalla de login
   - Dashboard principal tras login
   - Configuración accesible

4. **Verificación de servicios:**
   - Output de `systemctl status`
   - Puertos en escucha
   - Logs sin errores

---

## 📋 Entregables

### **📄 Documento Final Requerido:**

**Contenido mínimo:**
1. **Especificaciones del sistema utilizado**
2. **Proceso de instalación paso a paso**
3. **Capturas de pantalla del proceso**
4. **Verificaciones de funcionamiento**
5. **Problemas encontrados y soluciones**
6. **URLs de acceso finales**
7. **Configuraciones aplicadas**
8. **Recomendaciones para uso futuro**

**Archivos adicionales:**
- Scripts de instalación utilizados
- Archivos de configuración modificados
- Log de errores (si los hubo)

---

## 📋 Notas de Evaluación

**Criterios Cumplidos:**
- ✅ ZIPKIN instalado y funcionando en puerto 9411
- ✅ Grafana instalado y funcionando en puerto 3000
- ✅ Acceso web verificado para ambas herramientas
- ✅ Datos de prueba funcionando en ZIPKIN
- ✅ Panel básico creado en Grafana
- ✅ Documentación completa del proceso
- ✅ Evidencia visual de funcionamiento

**Calificación Esperada:** 9-10 puntos (Excelente)

**Criterios de Excelencia Adicionales:**
- Configuración como servicios del sistema
- Scripts de automatización funcionales
- Configuración de data source básico
- Troubleshooting documentado
- Configuraciones de seguridad aplicadas
