# A7-Java. Implementación de Patrones de Diseño Clásicos

## 📋 Descripción

Versión Java del sistema que implementa los **6 patrones de diseño más importantes**: Singleton, Factory, Builder, Strategy, Observer y Decorator. Demuestra cómo Java implementa estos patrones con sus propios idioms.

## 🎯 Los 6 Patrones

1. **Singleton** - Una única instancia global
2. **Factory Method** - Creación flexible de objetos
3. **Builder** - Construcción paso a paso
4. **Strategy** - Algoritmos intercambiables
5. **Observer** - Notificación de cambios
6. **Decorator** - Agregar funcionalidad dinámicamente

## 📊 Python vs Java en Patrones

| Patrón        | Python                | Java                           |
| ------------- | --------------------- | ------------------------------ |
| **Singleton** | `__new__` override    | `private constructor` + static |
| **Factory**   | Diccionario + lambdas | Factory class + methods        |
| **Builder**   | Fluent interface      | Fluent interface + inner class |
| **Strategy**  | ABC + subclases       | Interface + implementations    |
| **Observer**  | List de callbacks     | Interface + List               |
| **Decorator** | Función decorator     | Wrapper class                  |

## 💻 Implementación Java - Sistema de Restaurante

### 1. Singleton - Configuración

```java
// Configuracion.java
package restaurante;

/**
 * Patrón SINGLETON: Garantiza una única instancia de configuración.
 */
public class Configuracion {
    private static Configuracion instancia;
    private String nombreRestaurante;
    private double impuesto;
    private double propina;

    // Constructor privado previene instanciación externa
    private Configuracion() {
        this.nombreRestaurante = "Restaurante El Buen Sabor";
        this.impuesto = 0.16;
        this.propina = 0.10;
    }

    // Método estático para obtener la única instancia
    public static synchronized Configuracion obtenerInstancia() {
        if (instancia == null) {
            instancia = new Configuracion();
        }
        return instancia;
    }

    // Getters y Setters
    public String getNombreRestaurante() { return nombreRestaurante; }
    public void setNombreRestaurante(String nombre) { this.nombreRestaurante = nombre; }
    public double getImpuesto() { return impuesto; }
    public double getPropina() { return propina; }
}
```

### 2. Factory Method - Creación de Platos

```java
// Plato.java - Interfaz base
package restaurante;

public interface Plato {
    String getNombre();
    double getPrecio();
    String getDescripcion();
    int getTiempoPreparacion(); // minutos
}
```

```java
// Entrada.java
package restaurante;

public class Entrada implements Plato {
    private String nombre;
    private double precio;
    private String descripcion;

    public Entrada(String nombre, double precio, String descripcion) {
        this.nombre = nombre;
        this.precio = precio;
        this.descripcion = descripcion;
    }

    @Override
    public String getNombre() { return nombre; }

    @Override
    public double getPrecio() { return precio; }

    @Override
    public String getDescripcion() { return descripcion; }

    @Override
    public int getTiempoPreparacion() { return 10; }
}
```

```java
// PlatoFuerte.java
package restaurante;

public class PlatoFuerte implements Plato {
    private String nombre;
    private double precio;
    private String descripcion;

    public PlatoFuerte(String nombre, double precio, String descripcion) {
        this.nombre = nombre;
        this.precio = precio;
        this.descripcion = descripcion;
    }

    @Override
    public String getNombre() { return nombre; }

    @Override
    public double getPrecio() { return precio; }

    @Override
    public String getDescripcion() { return descripcion; }

    @Override
    public int getTiempoPreparacion() { return 25; }
}
```

```java
// FabricaPlatos.java - Factory Method
package restaurante;

/**
 * Patrón FACTORY METHOD: Crea diferentes tipos de platos sin acoplar código.
 */
public class FabricaPlatos {

    public static Plato crearEntrada(String nombre) {
        switch (nombre.toLowerCase()) {
            case "ensalada cesar":
                return new Entrada("Ensalada César", 85.0, "Lechuga, crutones, aderezo césar");
            case "sopa de tortilla":
                return new Entrada("Sopa de Tortilla", 65.0, "Sopa tradicional mexicana");
            default:
                throw new IllegalArgumentException("Entrada desconocida: " + nombre);
        }
    }

    public static Plato crearPlatoFuerte(String nombre) {
        switch (nombre.toLowerCase()) {
            case "tacos al pastor":
                return new PlatoFuerte("Tacos al Pastor", 120.0, "3 tacos con piña y cilantro");
            case "enchiladas verdes":
                return new PlatoFuerte("Enchiladas Verdes", 110.0, "Enchiladas con salsa verde");
            default:
                throw new IllegalArgumentException("Plato fuerte desconocido: " + nombre);
        }
    }
}
```

### 3. Builder - Construcción de Pedidos

```java
// Pedido.java
package restaurante;

import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.List;

public class Pedido {
    private String cliente;
    private List<Plato> platos;
    private String mesa;
    private String comentarios;
    private LocalDateTime hora;
    private MetodoPago metodoPago;

    // Constructor privado - solo el Builder puede crear instancias
    private Pedido(Builder builder) {
        this.cliente = builder.cliente;
        this.platos = builder.platos;
        this.mesa = builder.mesa;
        this.comentarios = builder.comentarios;
        this.hora = LocalDateTime.now();
        this.metodoPago = builder.metodoPago;
    }

    public double calcularTotal() {
        Configuracion config = Configuracion.obtenerInstancia();
        double subtotal = platos.stream().mapToDouble(Plato::getPrecio).sum();
        double impuesto = subtotal * config.getImpuesto();
        double propina = subtotal * config.getPropina();
        return subtotal + impuesto + propina;
    }

    public String getCliente() { return cliente; }
    public List<Plato> getPlatos() { return platos; }
    public String getMesa() { return mesa; }
    public MetodoPago getMetodoPago() { return metodoPago; }

    /**
     * Patrón BUILDER: Construcción paso a paso con fluent interface.
     */
    public static class Builder {
        private String cliente;
        private List<Plato> platos = new ArrayList<>();
        private String mesa;
        private String comentarios = "";
        private MetodoPago metodoPago;

        public Builder cliente(String cliente) {
            this.cliente = cliente;
            return this;
        }

        public Builder agregarPlato(Plato plato) {
            this.platos.add(plato);
            return this;
        }

        public Builder mesa(String mesa) {
            this.mesa = mesa;
            return this;
        }

        public Builder comentarios(String comentarios) {
            this.comentarios = comentarios;
            return this;
        }

        public Builder metodoPago(MetodoPago metodoPago) {
            this.metodoPago = metodoPago;
            return this;
        }

        public Pedido build() {
            if (cliente == null || platos.isEmpty()) {
                throw new IllegalStateException("Cliente y platos son obligatorios");
            }
            return new Pedido(this);
        }
    }
}
```

### 4. Strategy - Métodos de Pago

```java
// MetodoPago.java - Interfaz Strategy
package restaurante;

/**
 * Patrón STRATEGY: Define familia de algoritmos intercambiables.
 */
public interface MetodoPago {
    boolean procesar(double monto);
    String getNombre();
}
```

```java
// PagoEfectivo.java
package restaurante;

public class PagoEfectivo implements MetodoPago {
    @Override
    public boolean procesar(double monto) {
        System.out.println(String.format("💵 Procesando pago en efectivo: $%.2f", monto));
        return true;
    }

    @Override
    public String getNombre() {
        return "Efectivo";
    }
}
```

```java
// PagoTarjeta.java
package restaurante;

public class PagoTarjeta implements MetodoPago {
    private String numeroTarjeta;

    public PagoTarjeta(String numeroTarjeta) {
        this.numeroTarjeta = numeroTarjeta;
    }

    @Override
    public boolean procesar(double monto) {
        System.out.println(String.format("💳 Procesando pago con tarjeta: $%.2f", monto));
        System.out.println(String.format("   Tarjeta: ****%s",
            numeroTarjeta.substring(numeroTarjeta.length() - 4)));
        return true;
    }

    @Override
    public String getNombre() {
        return "Tarjeta de Crédito";
    }
}
```

### 5. Observer - Notificaciones de Pedidos

```java
// ObservadorPedido.java - Interfaz Observer
package restaurante;

/**
 * Patrón OBSERVER: Define observadores que se notifican de cambios.
 */
public interface ObservadorPedido {
    void actualizar(Pedido pedido, String evento);
}
```

```java
// NotificadorCliente.java
package restaurante;

public class NotificadorCliente implements ObservadorPedido {
    @Override
    public void actualizar(Pedido pedido, String evento) {
        System.out.println(String.format("📱 Cliente '%s': %s",
            pedido.getCliente(), evento));
    }
}
```

```java
// PantallaCocina.java
package restaurante;

public class PantallaCocina implements ObservadorPedido {
    @Override
    public void actualizar(Pedido pedido, String evento) {
        System.out.println(String.format("👨‍🍳 Cocina - Mesa %s: %s",
            pedido.getMesa(), evento));
    }
}
```

```java
// GestorPedidos.java - Subject del Observer
package restaurante;

import java.util.ArrayList;
import java.util.List;

/**
 * Subject del patrón OBSERVER: Mantiene lista de observadores y los notifica.
 */
public class GestorPedidos {
    private List<ObservadorPedido> observadores = new ArrayList<>();
    private List<Pedido> pedidos = new ArrayList<>();

    public void agregarObservador(ObservadorPedido observador) {
        observadores.add(observador);
    }

    public void crearPedido(Pedido pedido) {
        pedidos.add(pedido);
        notificar(pedido, "Pedido creado");
    }

    public void marcarListo(Pedido pedido) {
        notificar(pedido, "Pedido listo para servir");
    }

    public void marcarEntregado(Pedido pedido) {
        notificar(pedido, "Pedido entregado");
    }

    private void notificar(Pedido pedido, String evento) {
        for (ObservadorPedido observador : observadores) {
            observador.actualizar(pedido, evento);
        }
    }
}
```

### 6. Decorator - Extras para Platos

```java
// DecoradorPlato.java - Wrapper base
package restaurante;

/**
 * Patrón DECORATOR: Agrega funcionalidad a platos sin modificar clases.
 */
public abstract class DecoradorPlato implements Plato {
    protected Plato platoBase;

    public DecoradorPlato(Plato platoBase) {
        this.platoBase = platoBase;
    }
}
```

```java
// ConQuesoDerretido.java
package restaurante;

public class ConQuesoDerretido extends DecoradorPlato {
    public ConQuesoDerretido(Plato platoBase) {
        super(platoBase);
    }

    @Override
    public String getNombre() {
        return platoBase.getNombre() + " + Queso Derretido";
    }

    @Override
    public double getPrecio() {
        return platoBase.getPrecio() + 25.0;
    }

    @Override
    public String getDescripcion() {
        return platoBase.getDescripcion() + ", con queso derretido";
    }

    @Override
    public int getTiempoPreparacion() {
        return platoBase.getTiempoPreparacion() + 5;
    }
}
```

```java
// ExtraPicante.java
package restaurante;

public class ExtraPicante extends DecoradorPlato {
    public ExtraPicante(Plato platoBase) {
        super(platoBase);
    }

    @Override
    public String getNombre() {
        return platoBase.getNombre() + " 🌶️ EXTRA PICANTE";
    }

    @Override
    public double getPrecio() {
        return platoBase.getPrecio() + 15.0;
    }

    @Override
    public String getDescripcion() {
        return platoBase.getDescripcion() + ", extra picante";
    }

    @Override
    public int getTiempoPreparacion() {
        return platoBase.getTiempoPreparacion();
    }
}
```

### 7. Sistema Principal - Integrando Todos los Patrones

```java
// SistemaRestaurante.java
package restaurante;

public class SistemaRestaurante {

    public static void main(String[] args) {
        System.out.println("╔══════════════════════════════════════════════════════════════╗");
        System.out.println("║          SISTEMA DE RESTAURANTE - PATRONES EN JAVA           ║");
        System.out.println("╚══════════════════════════════════════════════════════════════╝\n");

        // ═══ SINGLETON ═══
        System.out.println("═══ SINGLETON: Configuración Global ═══\n");
        Configuracion config = Configuracion.obtenerInstancia();
        System.out.println("✓ Restaurante: " + config.getNombreRestaurante());
        System.out.println("✓ Impuesto: " + (config.getImpuesto() * 100) + "%");
        System.out.println("✓ Propina: " + (config.getPropina() * 100) + "%\n");

        // ═══ FACTORY METHOD ═══
        System.out.println("═══ FACTORY METHOD: Creación de Platos ═══\n");
        Plato ensalada = FabricaPlatos.crearEntrada("ensalada cesar");
        Plato tacos = FabricaPlatos.crearPlatoFuerte("tacos al pastor");

        System.out.println("✓ " + ensalada.getNombre() + " - $" + ensalada.getPrecio());
        System.out.println("✓ " + tacos.getNombre() + " - $" + tacos.getPrecio() + "\n");

        // ═══ DECORATOR ═══
        System.out.println("═══ DECORATOR: Personalización de Platos ═══\n");
        Plato tacosPersonalizados = new ExtraPicante(
            new ConQuesoDerretido(tacos)
        );
        System.out.println("✓ " + tacosPersonalizados.getNombre());
        System.out.println("  Precio: $" + tacosPersonalizados.getPrecio());
        System.out.println("  Descripción: " + tacosPersonalizados.getDescripcion() + "\n");

        // ═══ BUILDER ═══
        System.out.println("═══ BUILDER: Construcción de Pedido ═══\n");
        Pedido pedido = new Pedido.Builder()
            .cliente("Juan Pérez")
            .mesa("5")
            .agregarPlato(ensalada)
            .agregarPlato(tacosPersonalizados)
            .metodoPago(new PagoTarjeta("4532 1234 5678 9010"))
            .comentarios("Sin cebolla en los tacos")
            .build();

        System.out.println("✓ Pedido creado para: " + pedido.getCliente());
        System.out.println("  Mesa: " + pedido.getMesa());
        System.out.println("  Platos: " + pedido.getPlatos().size());
        System.out.println("  Total: $" + String.format("%.2f", pedido.calcularTotal()) + "\n");

        // ═══ OBSERVER ═══
        System.out.println("═══ OBSERVER: Sistema de Notificaciones ═══\n");
        GestorPedidos gestor = new GestorPedidos();
        gestor.agregarObservador(new NotificadorCliente());
        gestor.agregarObservador(new PantallaCocina());

        gestor.crearPedido(pedido);
        System.out.println();
        gestor.marcarListo(pedido);
        System.out.println();
        gestor.marcarEntregado(pedido);

        // ═══ STRATEGY ═══
        System.out.println("\n═══ STRATEGY: Procesamiento de Pago ═══\n");
        MetodoPago metodoPago = pedido.getMetodoPago();
        boolean pagoExitoso = metodoPago.procesar(pedido.calcularTotal());

        if (pagoExitoso) {
            System.out.println("✓ Pago procesado exitosamente");
        }

        // ═══ RESUMEN ═══
        System.out.println("\n╔══════════════════════════════════════════════════════════════╗");
        System.out.println("║              PATRONES DE DISEÑO IMPLEMENTADOS                ║");
        System.out.println("╠══════════════════════════════════════════════════════════════╣");
        System.out.println("║ ✓ SINGLETON   - Configuración única compartida              ║");
        System.out.println("║ ✓ FACTORY     - Creación flexible de platos                 ║");
        System.out.println("║ ✓ BUILDER     - Construcción fluida de pedidos              ║");
        System.out.println("║ ✓ DECORATOR   - Personalización sin modificar clases        ║");
        System.out.println("║ ✓ STRATEGY    - Métodos de pago intercambiables             ║");
        System.out.println("║ ✓ OBSERVER    - Notificaciones automáticas                  ║");
        System.out.println("╚══════════════════════════════════════════════════════════════╝\n");
    }
}
```

## 🔧 Compilación y Ejecución

```bash
# Compilar todos los archivos
javac -d . *.java

# Ejecutar
java restaurante.SistemaRestaurante
```

## 🔍 Análisis de Cada Patrón en Java

### 1. Singleton

**Idiom Java:** Constructor privado + método estático synchronized
**Alternativa Java:** Enum (thread-safe automáticamente)

### 2. Factory Method

**Idiom Java:** Método estático que retorna interfaz
**Mejora:** Usar Java 8+ con Method References

### 3. Builder

**Idiom Java:** Inner static class con fluent interface
**Estándar:** Patrón muy común en Java (StringBuilder, Stream.builder())

### 4. Strategy

**Idiom Java:** Interface + múltiples implementaciones
**Mejora Java 8+:** Usar lambdas para estrategias simples

### 5. Observer

**Idiom Java:** Interfaces Observer/Observable (deprecated en Java 9+)
**Mejora moderna:** Usar Reactive Streams o CompletableFuture

### 6. Decorator

**Idiom Java:** Clase wrapper que implementa misma interfaz
**Ejemplo real:** Java I/O (BufferedReader wraps Reader)

## 🎓 Puntos Clave Java

1. **`synchronized`**: Thread-safety en Singleton
2. **Inner static class**: Común en Builder pattern
3. **`interface`**: Base para Strategy, Observer
4. **Composition**: Clave en Decorator
5. **Generics**: No usados aquí pero útiles en patrones genéricos
6. **Lambdas**: Simplifican Strategy y Observer

## 🚀 Extensiones

1. Usar Enum para Singleton thread-safe
2. CompletableFuture para Observer asíncrono
3. Reactive Streams (RxJava) para eventos
4. Spring Framework para inyección de dependencias
5. Tests con JUnit + Mockito
6. Abstract Factory para familias de productos
7. Prototype pattern con Cloneable
8. Template Method pattern
