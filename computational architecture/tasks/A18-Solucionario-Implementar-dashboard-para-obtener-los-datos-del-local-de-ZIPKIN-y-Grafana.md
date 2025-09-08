# A18: Solucionario - Implementar dashboard para obtener datos de ZIPKIN y Grafana

## 📝 Resumen de la Actividad
**Objetivo:** Configurar un dashboard funcional en Grafana que consuma datos locales generados por ZIPKIN para visualizar información de trazas y métricas del sistema.

---

## 🎯 Solución Paso a Paso

### 1. Verificación de Instalaciones Previas

#### **🔍 Verificar ZIPKIN (Puerto 9411)**
```bash
# Verificar que ZIPKIN esté ejecutándose
curl http://localhost:9411/health
# Respuesta esperada: {"status":"UP"}

# Verificar interfaz web
curl -I http://localhost:9411
# Respuesta esperada: HTTP/1.1 200 OK
```

#### **🔍 Verificar Grafana (Puerto 3000)**
```bash
# Verificar que Grafana esté ejecutándose
curl http://localhost:3000/api/health
# Respuesta esperada: {"commit":"...","database":"ok","version":"..."}

# Verificar acceso web
curl -I http://localhost:3000
# Respuesta esperada: HTTP/1.1 200 OK
```

#### **📋 Credenciales por Defecto:**
- **Grafana:** admin / admin (cambiar en primer acceso)
- **ZIPKIN:** Sin autenticación requerida

---

### 2. Configuración de Fuente de Datos en Grafana

#### **🔗 Conectar ZIPKIN a Grafana**

**Opción A: Usando Jaeger Data Source (Recomendado)**

1. **Instalar Plugin de Jaeger:**
   ```bash
   # Desde interfaz de Grafana o CLI
   grafana-cli plugins install jaegertracing-jaeger-datasource
   # Reiniciar Grafana después de la instalación
   ```

2. **Configurar Data Source:**
   - **URL:** http://localhost:9411
   - **Tipo:** Jaeger
   - **Nombre:** Zipkin-Local
   - **Access:** Server (default)

**Opción B: Usando Prometheus + Zipkin Exporter**

1. **Instalar Zipkin Prometheus Exporter:**
   ```bash
   # Descargar y ejecutar
   wget https://repo1.maven.org/maven2/io/zipkin/zipkin-server/2.24.0/zipkin-server-2.24.0-exec.jar
   java -jar zipkin-server-2.24.0-exec.jar --zipkin.collector.prometheus.enabled=true
   ```

2. **Configurar Prometheus (prometheus.yml):**
   ```yaml
   global:
     scrape_interval: 15s
   
   scrape_configs:
     - job_name: 'zipkin'
       static_configs:
         - targets: ['localhost:9411']
       metrics_path: '/prometheus'
       scrape_interval: 5s
   ```

#### **⚙️ Pasos en Grafana UI:**

1. **Acceder a Configuración:**
   - Login a http://localhost:3000
   - Ir a Configuration → Data Sources
   - Clic en "Add data source"

2. **Seleccionar Tipo:**
   - Buscar "Jaeger" o "Prometheus"
   - Seleccionar según opción elegida

3. **Configurar Conexión:**
   ```
   Name: Zipkin-Tracing
   URL: http://localhost:9411
   Access: Server (default)
   ```

4. **Probar Conexión:**
   - Clic en "Save & Test"
   - Verificar mensaje: "Data source is working"

---

### 3. Generación de Datos de Prueba

#### **📊 Script para Generar Trazas de Prueba**

**Crear archivo `generate_traces.py`:**
```python
#!/usr/bin/env python3
import requests
import json
import time
import random
from datetime import datetime

# Configuración de ZIPKIN
ZIPKIN_URL = "http://localhost:9411/api/v2/spans"

def generate_trace_data():
    """Genera datos de traza sintéticos para testing"""
    trace_id = f"{random.randint(100000000000, 999999999999):012x}"
    span_id = f"{random.randint(100000000, 999999999):08x}"
    
    # Servicios simulados
    services = ["user-service", "payment-service", "order-service", "inventory-service"]
    operations = ["GET /users", "POST /payment", "PUT /orders", "GET /inventory"]
    
    service = random.choice(services)
    operation = random.choice(operations)
    
    # Duración aleatoria (1-1000ms)
    duration = random.randint(1000, 1000000)  # microsegundos
    timestamp = int(time.time() * 1000000)    # microsegundos
    
    span_data = {
        "traceId": trace_id,
        "id": span_id,
        "name": operation,
        "timestamp": timestamp,
        "duration": duration,
        "localEndpoint": {
            "serviceName": service,
            "ipv4": "127.0.0.1",
            "port": random.randint(8080, 8090)
        },
        "tags": {
            "http.method": operation.split()[0],
            "http.path": operation.split()[1],
            "http.status_code": str(random.choice([200, 201, 400, 404, 500])),
            "component": service
        }
    }
    
    return [span_data]

def send_traces_to_zipkin():
    """Envía trazas a ZIPKIN"""
    for i in range(50):  # Generar 50 trazas
        trace_data = generate_trace_data()
        
        try:
            response = requests.post(
                ZIPKIN_URL,
                json=trace_data,
                headers={'Content-Type': 'application/json'}
            )
            
            if response.status_code == 202:
                print(f"✅ Traza {i+1} enviada correctamente")
            else:
                print(f"❌ Error enviando traza {i+1}: {response.status_code}")
                
        except Exception as e:
            print(f"❌ Error de conexión: {e}")
        
        time.sleep(1)  # Esperar 1 segundo entre trazas

if __name__ == "__main__":
    print("🚀 Iniciando generación de trazas de prueba...")
    send_traces_to_zipkin()
    print("✅ Generación de trazas completada")
```

**Ejecutar script:**
```bash
python3 generate_traces.py
```

#### **🔄 Alternativa con curl:**
```bash
# Script bash para generar trazas simples
#!/bin/bash
for i in {1..20}; do
    TRACE_ID=$(openssl rand -hex 8)
    SPAN_ID=$(openssl rand -hex 4)
    TIMESTAMP=$(date +%s)000000
    
    curl -X POST http://localhost:9411/api/v2/spans \
      -H 'Content-Type: application/json' \
      -d "[{
        \"traceId\": \"$TRACE_ID\",
        \"id\": \"$SPAN_ID\",
        \"name\": \"test-operation\",
        \"timestamp\": $TIMESTAMP,
        \"duration\": 50000,
        \"localEndpoint\": {
          \"serviceName\": \"test-service\",
          \"ipv4\": \"127.0.0.1\",
          \"port\": 8080
        }
      }]"
    
    echo "Traza $i enviada"
    sleep 1
done
```

---

### 4. Creación del Dashboard en Grafana

#### **📊 Panel 1: Conteo de Trazas por Servicio**

**Configuración del Panel:**
1. **Crear nuevo dashboard:** Dashboards → New → New Dashboard
2. **Agregar panel:** Add → Visualization
3. **Configurar Query:**
   ```
   Data Source: Zipkin-Tracing
   Query Type: Search
   Service: All
   Time Range: Last 1 hour
   ```

**Panel Settings:**
- **Título:** "Trazas por Servicio"
- **Tipo:** Bar Chart
- **Eje Y:** Count
- **Eje X:** Service Name
- **Intervalo de actualización:** 30s

#### **📈 Panel 2: Latencia Promedio por Operación**

**Query Configuration:**
```
Data Source: Zipkin-Tracing / Prometheus
Metric: histogram_quantile(0.95, zipkin_span_duration_seconds)
Group By: operation_name
```

**Panel Settings:**
- **Título:** "Latencia P95 por Operación"
- **Tipo:** Time Series
- **Unidad:** milliseconds
- **Umbral de alerta:** > 500ms (rojo)

#### **🔍 Panel 3: Distribución de Códigos de Estado HTTP**

**Configuración Avanzada:**
```json
{
  "query": {
    "serviceName": "",
    "operationName": "",
    "tags": {
      "http.status_code": ""
    },
    "minDuration": "",
    "maxDuration": "",
    "limit": 100
  }
}
```

**Panel Settings:**
- **Título:** "Códigos de Estado HTTP"
- **Tipo:** Pie Chart
- **Colores:** Verde (2xx), Amarillo (4xx), Rojo (5xx)
- **Legend:** Porcentajes

---

### 5. Dashboard Avanzado - Observabilidad Completa

#### **🎯 Diseño del Dashboard Principal**

**Layout Recomendado (12 columnas x 8 filas):**
```
┌─────────────────────────────────────────┐
│          Panel 1: Service Health        │
├─────────────────┬───────────────────────┤
│   Panel 2:      │     Panel 3:          │
│   Request Rate  │     Error Rate        │
├─────────────────┼───────────────────────┤
│         Panel 4: Latency Trends         │
├─────────────────────────────────────────┤
│         Panel 5: Trace Details          │
└─────────────────────────────────────────┘
```

#### **⚙️ Panel 1: Service Health Overview**

**Configuración JSON:**
```json
{
  "title": "Service Health Overview",
  "type": "stat",
  "targets": [
    {
      "datasource": "Zipkin-Tracing",
      "queryType": "search",
      "serviceName": "",
      "limit": 1000
    }
  ],
  "fieldConfig": {
    "defaults": {
      "color": {
        "mode": "thresholds"
      },
      "thresholds": {
        "steps": [
          {"color": "green", "value": null},
          {"color": "yellow", "value": 80},
          {"color": "red", "value": 90}
        ]
      }
    }
  }
}
```

#### **📊 Panel 2: Request Rate (RPS)**

**Query Prometheus:**
```promql
sum(rate(zipkin_spans_total[5m])) by (service_name)
```

**Configuración:**
- **Tipo:** Graph
- **Eje Y:** Requests/sec
- **Intervalo:** 5m
- **Aggregation:** Sum

#### **❌ Panel 3: Error Rate**

**Query para Error Rate:**
```promql
sum(rate(zipkin_spans_total{error="true"}[5m])) / sum(rate(zipkin_spans_total[5m])) * 100
```

**Alertas configuradas:**
- **Warning:** > 5% error rate
- **Critical:** > 10% error rate

#### **⏱️ Panel 4: Latency Trends**

**Múltiples Percentiles:**
```promql
histogram_quantile(0.50, zipkin_span_duration_seconds) # P50
histogram_quantile(0.95, zipkin_span_duration_seconds) # P95
histogram_quantile(0.99, zipkin_span_duration_seconds) # P99
```

#### **🔍 Panel 5: Trace Details Table**

**Columnas configuradas:**
- Trace ID
- Service Name
- Operation
- Duration
- Status Code
- Timestamp

---

### 6. Configuración de Alertas

#### **🚨 Alerta 1: Alta Latencia**

**Condición:**
```
Query: histogram_quantile(0.95, zipkin_span_duration_seconds) > 1
For: 2m
```

**Configuración:**
- **Nombre:** High Latency Alert
- **Evaluación:** Cada 1m
- **Condición:** P95 > 1 segundo por 2 minutos
- **Notificación:** Email + Slack

#### **⚠️ Alerta 2: Error Rate Elevado**

**Condición:**
```
Query: (sum(rate(zipkin_spans_error_total[5m])) / sum(rate(zipkin_spans_total[5m]))) * 100 > 5
For: 1m
```

#### **📧 Configurar Notificaciones:**

**Slack Webhook:**
```json
{
  "webhook_url": "https://hooks.slack.com/services/...",
  "channel": "#alerts",
  "username": "Grafana",
  "title": "{{ .RuleName }}",
  "text": "{{ .Message }}"
}
```

---

### 7. Exportar e Importar Dashboard

#### **💾 Exportar Dashboard (JSON)**

**Pasos:**
1. Dashboard Settings → JSON Model
2. Copiar JSON completo
3. Guardar como `zipkin-dashboard.json`

**Estructura JSON del Dashboard:**
```json
{
  "dashboard": {
    "id": null,
    "title": "ZIPKIN Observability Dashboard",
    "tags": ["zipkin", "tracing", "observability"],
    "time": {
      "from": "now-1h",
      "to": "now"
    },
    "panels": [
      {
        "title": "Service Health",
        "type": "stat",
        "gridPos": {"h": 8, "w": 12, "x": 0, "y": 0}
      }
    ],
    "refresh": "30s",
    "version": 1
  }
}
```

#### **📥 Importar Dashboard:**

**Método 1: UI**
1. Dashboards → Import
2. Upload JSON file
3. Configurar data sources
4. Import

**Método 2: API**
```bash
curl -X POST \
  http://localhost:3000/api/dashboards/db \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer YOUR_API_KEY' \
  -d @zipkin-dashboard.json
```

---

### 8. Pruebas de Funcionamiento

#### **✅ Checklist de Verificación**

**Conectividad:**
- [ ] ZIPKIN accesible en puerto 9411
- [ ] Grafana accesible en puerto 3000
- [ ] Data source configurado correctamente
- [ ] Test connection exitoso

**Datos:**
- [ ] Trazas generadas y visibles en ZIPKIN
- [ ] Métricas aparecen en Grafana panels
- [ ] Tiempo real de actualización funcional
- [ ] Filtros y queries responden correctamente

**Dashboard:**
- [ ] 3+ paneles configurados y funcionales
- [ ] Visualizaciones claras y útiles
- [ ] Títulos descriptivos en cada panel
- [ ] Configuración de tiempo global funcional

**Alertas (Opcional):**
- [ ] Alertas configuradas para métricas críticas
- [ ] Notificaciones funcionando
- [ ] Umbrales apropiados definidos

#### **🧪 Script de Prueba Automatizada:**

```python
import requests
import time

def test_zipkin_connection():
    """Probar conectividad con ZIPKIN"""
    try:
        response = requests.get("http://localhost:9411/health")
        return response.status_code == 200
    except:
        return False

def test_grafana_connection():
    """Probar conectividad con Grafana"""
    try:
        response = requests.get("http://localhost:3000/api/health")
        return response.status_code == 200
    except:
        return False

def test_data_flow():
    """Probar flujo de datos end-to-end"""
    # Enviar traza de prueba
    trace_data = [{
        "traceId": "test123456789012",
        "id": "span1234",
        "name": "test-operation",
        "timestamp": int(time.time() * 1000000),
        "duration": 50000,
        "localEndpoint": {"serviceName": "test-service"}
    }]
    
    response = requests.post(
        "http://localhost:9411/api/v2/spans",
        json=trace_data
    )
    
    return response.status_code == 202

# Ejecutar pruebas
print("🔍 Ejecutando pruebas de conectividad...")
print(f"ZIPKIN: {'✅ OK' if test_zipkin_connection() else '❌ FAIL'}")
print(f"Grafana: {'✅ OK' if test_grafana_connection() else '❌ FAIL'}")
print(f"Data Flow: {'✅ OK' if test_data_flow() else '❌ FAIL'}")
```

---

### 9. Documentación de Resultados

#### **📸 Capturas Requeridas:**

1. **Dashboard Principal:**
   - Vista completa con todos los paneles
   - Datos actualizados y visibles
   - Timestamps correctos

2. **Configuración de Data Source:**
   - Pantalla de configuración
   - Prueba de conexión exitosa
   - Configuración de URL y parámetros

3. **Paneles Individuales:**
   - Cada panel con datos reales
   - Configuración de queries visible
   - Métricas funcionando

4. **ZIPKIN UI:**
   - Trazas visibles en ZIPKIN
   - Interfaz mostrando servicios
   - Detalles de trazas individuales

#### **📋 Reporte de Configuración:**

**Template de Documentación:**
```markdown
## Configuración Implementada

### Entorno
- SO: [Ubuntu 20.04 / Windows 10 / macOS]
- ZIPKIN Version: [2.24.0]
- Grafana Version: [9.0.0]
- Browser: [Chrome 104.0]

### Data Sources Configurados
| Nombre | Tipo | URL | Estado |
|--------|------|-----|--------|
| Zipkin-Local | Jaeger | http://localhost:9411 | ✅ Activo |

### Paneles Implementados
1. **Service Health**: Estado general de servicios
2. **Request Rate**: Tasa de peticiones por segundo
3. **Error Rate**: Porcentaje de errores

### Pruebas Realizadas
- [✅] Generación de 50 trazas de prueba
- [✅] Visualización en tiempo real
- [✅] Filtrado por servicio funcional
```

---

### 10. Troubleshooting Común

#### **🔧 Problemas Frecuentes y Soluciones:**

**Error: "Data source is not working"**
```bash
# Verificar puertos
netstat -tulpn | grep :9411
netstat -tulpn | grep :3000

# Verificar logs
docker logs zipkin  # Si se usa Docker
sudo journalctl -u grafana-server
```

**Error: "No data points"**
```bash
# Verificar que hay trazas en ZIPKIN
curl http://localhost:9411/api/v2/services

# Generar datos de prueba
python3 generate_traces.py
```

**Error: "Panel query failed"**
- Verificar sintaxis de query
- Comprobar data source seleccionado
- Revisar permisos de acceso

**Dashboard no se actualiza:**
- Verificar auto-refresh configurado
- Comprobar time range apropiado
- Validar conectividad de red

---

## 📋 Entregables

### **📄 Documento Final Requerido:**

**Estructura:**
1. **Portada y objetivos**
2. **Configuración del entorno**
3. **Proceso de conexión data sources**
4. **Creación de dashboard paso a paso**
5. **Paneles implementados (3 mínimo)**
6. **Capturas de funcionamiento**
7. **Análisis de resultados**
8. **Conclusiones y recomendaciones**

**Archivos adicionales:**
- Dashboard JSON exportado
- Scripts de generación de datos
- Screenshots del dashboard funcionando

---

## 📋 Notas de Evaluación

**Criterios Cumplidos:**
- ✅ Conectividad exitosa con fuente de datos
- ✅ Dashboard con 3+ paneles funcionales
- ✅ Visualizaciones apropiadas y útiles
- ✅ Datos reales visibles y actualizados
- ✅ Documentación completa del proceso
- ✅ Evidencia visual del funcionamiento

**Calificación Esperada:** 9-10 puntos (Excelente)

**Puntos Extra Posibles:**
- Configuración de alertas funcionales
- Dashboard exportable e importable
- Scripts automatizados de prueba
- Análisis avanzado de métricas de observabilidad
