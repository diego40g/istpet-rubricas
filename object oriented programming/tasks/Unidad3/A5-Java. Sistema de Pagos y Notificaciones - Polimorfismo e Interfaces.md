# A5-Java. Sistema de Pagos y Notificaciones - Polimorfismo e Interfaces

## 📋 Descripción de la Actividad

Esta es la **versión Java** del sistema de pagos y notificaciones que demuestra **polimorfismo** e **interfaces**. En Java, las interfaces son más explícitas y estrictas que en Python, lo que permite ver claramente los contratos entre componentes.

## 🎯 Objetivos de Aprendizaje

- Implementar interfaces con `interface` en Java
- Aplicar polimorfismo con interfaces
- Comprender diferencias entre interfaces y clases abstractas
- Usar enumeraciones para estados
- Implementar múltiples interfaces en una clase
- Trabajar con colecciones y streams

## 📊 Comparación Python vs Java

| Característica           | Python                   | Java                             |
| ------------------------ | ------------------------ | -------------------------------- |
| **Interfaces**           | `ABC` o `Protocol`       | `interface`                      |
| **Implementar**          | Herencia implícita       | `implements` explícito           |
| **Métodos abstractos**   | `@abstractmethod`        | Automáticos en interface         |
| **Múltiples interfaces** | Herencia múltiple        | `implements I1, I2, I3`          |
| **Default methods**      | Métodos concretos en ABC | `default` en interface (Java 8+) |

## 💻 Solución Completa en Java

### Paso 1: Enumeraciones

```java
// EstadoPago.java
package pagos;

public enum EstadoPago {
    PENDIENTE("Pendiente"),
    PROCESANDO("Procesando"),
    EXITOSO("Exitoso"),
    FALLIDO("Fallido"),
    CANCELADO("Cancelado"),
    REEMBOLSADO("Reembolsado");

    private final String valor;

    EstadoPago(String valor) {
        this.valor = valor;
    }

    public String getValor() {
        return valor;
    }
}
```

```java
// CanalNotificacion.java
package pagos;

public enum CanalNotificacion {
    EMAIL("Email"),
    SMS("SMS"),
    PUSH("Push"),
    WHATSAPP("WhatsApp"),
    SLACK("Slack");

    private final String valor;

    CanalNotificacion(String valor) {
        this.valor = valor;
    }

    public String getValor() {
        return valor;
    }
}
```

### Paso 2: Resultado de Pago (Clase de Datos)

```java
// ResultadoPago.java
package pagos;

import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.HashMap;
import java.util.Map;

public class ResultadoPago {
    private boolean exito;
    private String referencia;
    private String metodo;
    private double monto;
    private EstadoPago estado;
    private LocalDateTime fecha;
    private String error;
    private Map<String, String> datosAdicionales;

    public ResultadoPago(boolean exito, String referencia, String metodo,
                         double monto, EstadoPago estado) {
        this.exito = exito;
        this.referencia = referencia;
        this.metodo = metodo;
        this.monto = monto;
        this.estado = estado;
        this.fecha = LocalDateTime.now();
        this.error = "";
        this.datosAdicionales = new HashMap<>();
    }

    public ResultadoPago(boolean exito, String error) {
        this(exito, "", "", 0.0, EstadoPago.FALLIDO);
        this.error = error;
    }

    @Override
    public String toString() {
        if (exito) {
            return String.format("✓ Pago exitoso - Ref: %s", referencia);
        }
        return String.format("✗ Pago fallido: %s", error);
    }

    public String generarRecibo() {
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd/MM/yyyy HH:mm:ss");

        StringBuilder recibo = new StringBuilder();
        recibo.append("╔══════════════════════════════════════════════════════════════╗\n");
        recibo.append("║                     RECIBO DE PAGO                           ║\n");
        recibo.append("╚══════════════════════════════════════════════════════════════╝\n\n");
        recibo.append(String.format("  Referencia:    %s\n", referencia));
        recibo.append(String.format("  Método:        %s\n", metodo));
        recibo.append(String.format("  Monto:         $%.2f\n", monto));
        recibo.append(String.format("  Fecha:         %s\n", fecha.format(formatter)));
        recibo.append(String.format("  Estado:        %s\n", estado.getValor()));
        recibo.append("\n═══════════════════════════════════════════════════════════════\n");

        return recibo.toString();
    }

    // Getters y Setters
    public boolean isExito() { return exito; }
    public String getReferencia() { return referencia; }
    public String getMetodo() { return metodo; }
    public double getMonto() { return monto; }
    public EstadoPago getEstado() { return estado; }
    public LocalDateTime getFecha() { return fecha; }
    public String getError() { return error; }
    public void setError(String error) { this.error = error; }
    public Map<String, String> getDatosAdicionales() { return datosAdicionales; }
}
```

### Paso 3: Interface de Método de Pago (Polimorfismo)

```java
// MetodoPago.java
package pagos;

/**
 * Interface para métodos de pago.
 * POLIMORFISMO: Diferentes implementaciones con comportamiento común.
 */
public interface MetodoPago {

    /**
     * Procesa un pago.
     * @param monto Monto a pagar
     * @param descripcion Descripción del pago
     * @return Resultado del procesamiento
     */
    ResultadoPago procesarPago(double monto, String descripcion);

    /**
     * Valida que el método de pago sea válido.
     * @return true si es válido
     */
    boolean validar();

    /**
     * Obtiene información segura del método (sin datos sensibles).
     * @return Información para mostrar al usuario
     */
    String obtenerInfoSegura();

    /**
     * Obtiene el nombre del método de pago.
     * @return Nombre del método
     */
    String obtenerNombre();
}
```

### Paso 4: Implementaciones de Métodos de Pago

```java
// TarjetaCredito.java
package pagos;

import java.util.UUID;
import java.util.regex.Pattern;

public class TarjetaCredito implements MetodoPago {
    private String numero;
    private String titular;
    private String cvv;
    private String fechaExpiracion;

    public TarjetaCredito(String numero, String titular, String cvv, String fechaExpiracion) {
        this.numero = numero;
        this.titular = titular;
        this.cvv = cvv;
        this.fechaExpiracion = fechaExpiracion;
    }

    @Override
    public ResultadoPago procesarPago(double monto, String descripcion) {
        if (!validar()) {
            return new ResultadoPago(false, "Tarjeta inválida");
        }

        if (monto <= 0) {
            return new ResultadoPago(false, "Monto inválido");
        }

        // Simular procesamiento
        String referencia = "TC-" + UUID.randomUUID().toString().substring(0, 8).toUpperCase();

        return new ResultadoPago(true, referencia, obtenerNombre(),
                                 monto, EstadoPago.EXITOSO);
    }

    @Override
    public boolean validar() {
        // Validar formato de tarjeta (algoritmo de Luhn simplificado)
        String numeroLimpio = numero.replaceAll("\\s+", "");
        return Pattern.matches("\\d{16}", numeroLimpio) && cvv.matches("\\d{3,4}");
    }

    @Override
    public String obtenerInfoSegura() {
        String ultimos4 = numero.substring(numero.length() - 4);
        return String.format("Tarjeta ****%s (%s)", ultimos4, titular);
    }

    @Override
    public String obtenerNombre() {
        return "Tarjeta de Crédito";
    }
}
```

```java
// PayPal.java
package pagos;

import java.util.UUID;
import java.util.regex.Pattern;

public class PayPal implements MetodoPago {
    private String email;
    private String password; // En producción, nunca almacenar así

    public PayPal(String email, String password) {
        this.email = email;
        this.password = password;
    }

    @Override
    public ResultadoPago procesarPago(double monto, String descripcion) {
        if (!validar()) {
            return new ResultadoPago(false, "Email de PayPal inválido");
        }

        String referencia = "PP-" + UUID.randomUUID().toString().substring(0, 8).toUpperCase();
        return new ResultadoPago(true, referencia, obtenerNombre(),
                                 monto, EstadoPago.EXITOSO);
    }

    @Override
    public boolean validar() {
        String emailRegex = "^[A-Za-z0-9+_.-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}$";
        return Pattern.matches(emailRegex, email);
    }

    @Override
    public String obtenerInfoSegura() {
        return String.format("PayPal (%s)", email);
    }

    @Override
    public String obtenerNombre() {
        return "PayPal";
    }
}
```

```java
// Criptomoneda.java
package pagos;

import java.util.UUID;

public class Criptomoneda implements MetodoPago {
    private String wallet;
    private String tipoMoneda; // BTC, ETH, etc.

    public Criptomoneda(String wallet, String tipoMoneda) {
        this.wallet = wallet;
        this.tipoMoneda = tipoMoneda;
    }

    @Override
    public ResultadoPago procesarPago(double monto, String descripcion) {
        if (!validar()) {
            return new ResultadoPago(false, "Wallet inválida");
        }

        String referencia = String.format("%s-%s",
            tipoMoneda.toUpperCase(),
            UUID.randomUUID().toString().substring(0, 8).toUpperCase());

        return new ResultadoPago(true, referencia, obtenerNombre(),
                                 monto, EstadoPago.EXITOSO);
    }

    @Override
    public boolean validar() {
        return wallet != null && wallet.length() >= 26;
    }

    @Override
    public String obtenerInfoSegura() {
        String inicio = wallet.substring(0, 6);
        String fin = wallet.substring(wallet.length() - 4);
        return String.format("%s Wallet: %s...%s", tipoMoneda.toUpperCase(), inicio, fin);
    }

    @Override
    public String obtenerNombre() {
        return "Criptomoneda " + tipoMoneda.toUpperCase();
    }
}
```

### Paso 5: Procesador de Pagos (Usa Polimorfismo)

```java
// ProcesadorPagos.java
package pagos;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * Procesa pagos de forma POLIMÓRFICA.
 * No le importa la implementación específica del MetodoPago.
 */
public class ProcesadorPagos {
    private List<ResultadoPago> historial;

    public ProcesadorPagos() {
        this.historial = new ArrayList<>();
    }

    /**
     * Procesa un pago con cualquier método que implemente MetodoPago.
     * POLIMORFISMO en acción.
     */
    public ResultadoPago procesar(MetodoPago metodo, double monto, String descripcion) {
        System.out.println(String.format("\n💳 Procesando pago de $%.2f con %s...",
            monto, metodo.obtenerNombre()));
        System.out.println("   " + metodo.obtenerInfoSegura());

        ResultadoPago resultado = metodo.procesarPago(monto, descripcion);
        historial.add(resultado);

        System.out.println("   " + resultado);
        return resultado;
    }

    public Map<String, Object> generarEstadisticas() {
        Map<String, Object> stats = new HashMap<>();

        long exitosos = historial.stream().filter(ResultadoPago::isExito).count();
        long fallidos = historial.size() - exitosos;

        double totalProcesado = historial.stream()
            .filter(ResultadoPago::isExito)
            .mapToDouble(ResultadoPago::getMonto)
            .sum();

        stats.put("total_transacciones", historial.size());
        stats.put("total_exitosos", exitosos);
        stats.put("total_fallidos", fallidos);
        stats.put("total_procesado", totalProcesado);

        return stats;
    }

    public String generarReporte() {
        Map<String, Object> stats = generarEstadisticas();

        StringBuilder reporte = new StringBuilder();
        reporte.append("╔══════════════════════════════════════════════════════════════╗\n");
        reporte.append("║              REPORTE DE PROCESAMIENTO DE PAGOS               ║\n");
        reporte.append("╚══════════════════════════════════════════════════════════════╝\n\n");
        reporte.append("RESUMEN GENERAL:\n");
        reporte.append(String.format("  Total procesado:       $%.2f\n", stats.get("total_procesado")));
        reporte.append(String.format("  Transacciones totales: %d\n", stats.get("total_transacciones")));
        reporte.append(String.format("  Exitosas:              %d\n", stats.get("total_exitosos")));
        reporte.append(String.format("  Fallidas:              %d\n", stats.get("total_fallidos")));

        long total = (long) stats.get("total_transacciones");
        long exitosos = (long) stats.get("total_exitosos");
        double tasaExito = total > 0 ? (exitosos * 100.0 / total) : 0;
        reporte.append(String.format("  Tasa de éxito:         %.1f%%\n", tasaExito));

        return reporte.toString();
    }

    public List<ResultadoPago> getHistorial() {
        return new ArrayList<>(historial);
    }
}
```

### Paso 6: Interface de Notificador

```java
// Notificador.java
package pagos;

/**
 * Interface para notificadores.
 * Permite múltiples implementaciones sin cambiar código cliente.
 */
public interface Notificador {
    boolean enviar(String destinatario, String mensaje);
    boolean esDisponible();
    CanalNotificacion getCanal();
}
```

### Paso 7: Implementaciones de Notificadores

```java
// NotificadorEmail.java
package pagos;

public class NotificadorEmail implements Notificador {

    @Override
    public boolean enviar(String destinatario, String mensaje) {
        if (!esDisponible()) {
            return false;
        }

        System.out.println(String.format("📧 Email enviado a %s", destinatario));
        System.out.println(String.format("   Mensaje: %s", mensaje.substring(0, Math.min(50, mensaje.length())) + "..."));
        return true;
    }

    @Override
    public boolean esDisponible() {
        return true; // Simular que siempre está disponible
    }

    @Override
    public CanalNotificacion getCanal() {
        return CanalNotificacion.EMAIL;
    }
}
```

```java
// NotificadorSMS.java
package pagos;

public class NotificadorSMS implements Notificador {

    @Override
    public boolean enviar(String destinatario, String mensaje) {
        if (!esDisponible()) {
            return false;
        }

        // SMS tiene límite de 160 caracteres
        String mensajeCorto = mensaje.length() > 160 ?
            mensaje.substring(0, 157) + "..." : mensaje;

        System.out.println(String.format("📱 SMS enviado a %s", destinatario));
        System.out.println(String.format("   Mensaje: %s", mensajeCorto));
        return true;
    }

    @Override
    public boolean esDisponible() {
        return true;
    }

    @Override
    public CanalNotificacion getCanal() {
        return CanalNotificacion.SMS;
    }
}
```

```java
// NotificadorWhatsApp.java
package pagos;

public class NotificadorWhatsApp implements Notificador {

    @Override
    public boolean enviar(String destinatario, String mensaje) {
        if (!esDisponible()) {
            return false;
        }

        System.out.println(String.format("💬 WhatsApp enviado a %s", destinatario));
        System.out.println(String.format("   Mensaje: %s", mensaje.substring(0, Math.min(50, mensaje.length())) + "..."));
        return true;
    }

    @Override
    public boolean esDisponible() {
        return true;
    }

    @Override
    public CanalNotificacion getCanal() {
        return CanalNotificacion.WHATSAPP;
    }
}
```

### Paso 8: Gestor de Notificaciones

```java
// GestorNotificaciones.java
package pagos;

import java.util.ArrayList;
import java.util.List;

public class GestorNotificaciones {
    private List<Notificador> notificadores;

    public GestorNotificaciones() {
        this.notificadores = new ArrayList<>();
    }

    public void registrarNotificador(Notificador notificador) {
        notificadores.add(notificador);
        System.out.println(String.format("✓ Notificador registrado: %s",
            notificador.getCanal().getValor()));
    }

    /**
     * Envía notificación por todos los canales registrados.
     * POLIMORFISMO: Funciona con cualquier implementación de Notificador.
     */
    public void notificarTodos(String destinatario, String mensaje) {
        System.out.println(String.format("\n📢 Enviando notificación a: %s", destinatario));

        for (Notificador notificador : notificadores) {
            if (notificador.esDisponible()) {
                notificador.enviar(destinatario, mensaje);
            } else {
                System.out.println(String.format("   ⚠️  Canal %s no disponible",
                    notificador.getCanal().getValor()));
            }
        }
    }

    public int cantidadNotificadores() {
        return notificadores.size();
    }
}
```

### Paso 9: Clase Principal de Demostración

```java
// SistemaPagos.java
package pagos;

public class SistemaPagos {

    public static void main(String[] args) {
        System.out.println("╔══════════════════════════════════════════════════════════════╗");
        System.out.println("║     SISTEMA DE PAGOS Y NOTIFICACIONES - JAVA                 ║");
        System.out.println("║        Polimorfismo e Interfaces en Java                     ║");
        System.out.println("╚══════════════════════════════════════════════════════════════╝\n");

        // ═══ CREAR MÉTODOS DE PAGO (POLIMORFISMO) ═══
        System.out.println("═══ CREANDO MÉTODOS DE PAGO (Polimorfismo) ═══\n");

        MetodoPago tarjeta = new TarjetaCredito(
            "4532 1234 5678 9010",
            "Juan Pérez",
            "123",
            "12/25"
        );

        MetodoPago paypal = new PayPal(
            "juan.perez@email.com",
            "password123"
        );

        MetodoPago cripto = new Criptomoneda(
            "1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa",
            "BTC"
        );

        System.out.println("✓ " + tarjeta.obtenerInfoSegura());
        System.out.println("✓ " + paypal.obtenerInfoSegura());
        System.out.println("✓ " + cripto.obtenerInfoSegura());

        // ═══ PROCESAR PAGOS POLIMÓRFICAMENTE ═══
        System.out.println("\n═══ PROCESANDO PAGOS (Polimorfismo) ═══");

        ProcesadorPagos procesador = new ProcesadorPagos();

        // El procesador trata todos los métodos de forma uniforme
        ResultadoPago r1 = procesador.procesar(tarjeta, 1500.00, "Compra en tienda online");
        ResultadoPago r2 = procesador.procesar(paypal, 850.50, "Suscripción mensual");
        ResultadoPago r3 = procesador.procesar(cripto, 2300.00, "Compra de software");

        // ═══ GENERAR RECIBOS ═══
        System.out.println("\n═══ RECIBO GENERADO ═══\n");
        System.out.println(r1.generarRecibo());

        // ═══ SISTEMA DE NOTIFICACIONES (POLIMORFISMO) ═══
        System.out.println("═══ CONFIGURANDO NOTIFICACIONES (Polimorfismo) ═══\n");

        GestorNotificaciones gestor = new GestorNotificaciones();
        gestor.registrarNotificador(new NotificadorEmail());
        gestor.registrarNotificador(new NotificadorSMS());
        gestor.registrarNotificador(new NotificadorWhatsApp());

        // ═══ ENVIAR NOTIFICACIONES ═══
        gestor.notificarTodos(
            "juan.perez@email.com",
            "Su pago de $1500.00 ha sido procesado exitosamente. " +
            "Referencia: " + r1.getReferencia()
        );

        // ═══ REPORTE FINAL ═══
        System.out.println("\n" + procesador.generarReporte());

        System.out.println("\n✅ Sistema completado exitosamente");
        System.out.println("\n╚══════════════════════════════════════════════════════════════╝\n");
    }
}
```

## 🔧 Compilación y Ejecución

```bash
# Crear estructura
mkdir -p pagos

# Compilar todos los archivos
javac -d . *.java

# Ejecutar
java pagos.SistemaPagos
```

## 🔍 Comparación Detallada: Python vs Java

### Interfaces

**Python (ABC):**

```python
from abc import ABC, abstractmethod

class MetodoPago(ABC):
    @abstractmethod
    def procesar_pago(self, monto: float) -> ResultadoPago:
        pass
```

**Java:**

```java
public interface MetodoPago {
    ResultadoPago procesarPago(double monto, String descripcion);
    boolean validar();
}
```

### Polimorfismo

**Python:**

```python
def procesar(metodo: MetodoPago, monto: float):
    return metodo.procesar_pago(monto)
```

**Java:**

```java
public ResultadoPago procesar(MetodoPago metodo, double monto) {
    return metodo.procesarPago(monto, "");
}
```

## ✅ Puntos Clave Java

1. **`interface`**: Contrato explícito que las clases deben cumplir
2. **`implements`**: Palabra clave para implementar interfaces
3. **Múltiples interfaces**: `class X implements I1, I2, I3`
4. **`@Override`**: Documenta que se implementa método de interface
5. **Tipos estáticos**: Compilador verifica que se implementen todos los métodos
6. **Polimorfismo**: Variables de tipo interface pueden referenciar cualquier implementación
7. **Enums**: Más potentes que en Python
8. **Streams**: API funcional para colecciones

## 🎓 Conceptos Aplicados

- ✅ Interfaces explícitas
- ✅ Polimorfismo por interface
- ✅ Múltiples implementaciones
- ✅ Enumeraciones
- ✅ Colecciones (List, Map)
- ✅ Streams y lambdas
- ✅ Encapsulación
- ✅ Sobrescritura de métodos
- ✅ Validaciones
- ✅ Pattern matching (regex)

## 🚀 Extensiones Posibles

1. Integrar con Stripe/PayPal real
2. Agregar patrón Strategy para descuentos
3. Implementar patrón Observer para notificaciones asíncronas
4. Sistema de retry con exponential backoff
5. Logging con Log4j
6. Tests con JUnit y Mockito
7. Persistencia con JPA/Hibernate
8. API REST con Spring Boot
9. Seguridad con Spring Security
10. Métricas con Micrometer
