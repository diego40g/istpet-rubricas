# A4-Java. Sistema de E-commerce - Herencia y Composición

## 📋 Descripción de la Actividad

Esta es la **versión Java** del sistema de e-commerce que combina **herencia** y **composición**. Demuestra las diferencias y similitudes entre Python y Java al implementar jerarquías de clases y relaciones de composición.

## 🎯 Objetivos de Aprendizaje

- Aplicar herencia en Java con `extends`
- Usar composición con relaciones "tiene un"
- Implementar clases abstractas con `abstract`
- Comprender las diferencias entre Python y Java en POO
- Usar constructores y `super()` en Java
- Trabajar con interfaces y enumeraciones

## 📊 Comparación Python vs Java

| Característica       | Python                  | Java                       |
| -------------------- | ----------------------- | -------------------------- |
| **Herencia**         | `class Hijo(Padre):`    | `class Hijo extends Padre` |
| **Abstracto**        | `from abc import ABC`   | `abstract class`           |
| **Método abstracto** | `@abstractmethod`       | `abstract tipo metodo()`   |
| **Composición**      | Mismo concepto          | Mismo concepto             |
| **Constructor**      | `__init__(self, ...)`   | `public Clase(...)`        |
| **Super**            | `super().__init__(...)` | `super(...)`               |

## 💻 Solución Completa en Java

### Paso 1: Crear las Enumeraciones

```java
// EstadoPedido.java
package ecommerce;

public enum EstadoPedido {
    PENDIENTE("Pendiente"),
    PAGADO("Pagado"),
    ENVIADO("Enviado"),
    ENTREGADO("Entregado"),
    CANCELADO("Cancelado");

    private final String valor;

    EstadoPedido(String valor) {
        this.valor = valor;
    }

    public String getValor() {
        return valor;
    }
}
```

```java
// TipoProducto.java
package ecommerce;

public enum TipoProducto {
    FISICO("Físico"),
    DIGITAL("Digital"),
    SERVICIO("Servicio");

    private final String valor;

    TipoProducto(String valor) {
        this.valor = valor;
    }

    public String getValor() {
        return valor;
    }
}
```

### Paso 2: Crear la Jerarquía de Productos (HERENCIA)

```java
// Producto.java
package ecommerce;

/**
 * Clase base abstracta para todos los productos.
 * Demuestra HERENCIA en Java: ProductoFisico extends Producto
 */
public abstract class Producto {
    private static int contadorId = 1;

    private String id;
    private String nombre;
    private String descripcion;
    private double precio;
    private int stockDisponible;
    private boolean activo;

    // Constructor
    public Producto(String nombre, String descripcion, double precio, int stockDisponible) {
        this.id = "PROD-" + String.format("%05d", contadorId++);
        this.nombre = nombre;
        this.descripcion = descripcion;
        this.precio = precio;
        this.stockDisponible = stockDisponible;
        this.activo = true;
    }

    // Métodos abstractos que las subclases DEBEN implementar
    public abstract double calcularCostoTotal(int cantidad);
    public abstract boolean puedeSerEnviado();
    public abstract TipoProducto obtenerTipo();

    // Métodos concretos compartidos por todas las subclases
    public boolean verificarDisponibilidad(int cantidad) {
        return this.stockDisponible >= cantidad;
    }

    public void reducirStock(int cantidad) {
        if (verificarDisponibilidad(cantidad)) {
            this.stockDisponible -= cantidad;
        } else {
            throw new IllegalStateException("Stock insuficiente");
        }
    }

    public void aumentarStock(int cantidad) {
        this.stockDisponible += cantidad;
    }

    // Getters y Setters
    public String getId() { return id; }
    public String getNombre() { return nombre; }
    public void setNombre(String nombre) { this.nombre = nombre; }
    public String getDescripcion() { return descripcion; }
    public void setDescripcion(String descripcion) { this.descripcion = descripcion; }
    public double getPrecio() { return precio; }
    public void setPrecio(double precio) { this.precio = precio; }
    public int getStockDisponible() { return stockDisponible; }
    public boolean isActivo() { return activo; }
    public void setActivo(boolean activo) { this.activo = activo; }

    @Override
    public String toString() {
        return String.format("%s - $%.2f", nombre, precio);
    }
}
```

```java
// ProductoFisico.java
package ecommerce;

/**
 * Producto físico que requiere envío.
 * HEREDA de Producto y agrega atributos específicos.
 */
public class ProductoFisico extends Producto {
    private double pesoKg;
    private double[] dimensiones; // [largo, ancho, alto]

    public ProductoFisico(String nombre, String descripcion, double precio,
                          double pesoKg, double[] dimensiones, int stockDisponible) {
        super(nombre, descripcion, precio, stockDisponible); // Llama al constructor padre
        this.pesoKg = pesoKg;
        this.dimensiones = dimensiones;
    }

    @Override
    public double calcularCostoTotal(int cantidad) {
        return getPrecio() * cantidad;
    }

    @Override
    public boolean puedeSerEnviado() {
        return true; // Los productos físicos siempre requieren envío
    }

    @Override
    public TipoProducto obtenerTipo() {
        return TipoProducto.FISICO;
    }

    public double calcularCostoEnvio(String destinoCp) {
        double costoBase = 50.0;
        double pesoVolumetrico = (dimensiones[0] * dimensiones[1] * dimensiones[2]) / 5000.0;
        double pesoCobrable = Math.max(pesoKg, pesoVolumetrico);
        double costoPorKg = 15.0;

        return costoBase + (pesoCobrable * costoPorKg);
    }

    public String obtenerDimensionesStr() {
        return String.format("%.0fx%.0fx%.0f cm", dimensiones[0], dimensiones[1], dimensiones[2]);
    }

    // Getters
    public double getPesoKg() { return pesoKg; }
    public double[] getDimensiones() { return dimensiones; }
}
```

```java
// ProductoDigital.java
package ecommerce;

import java.time.LocalDateTime;

/**
 * Producto digital descargable (no requiere envío).
 */
public class ProductoDigital extends Producto {
    private String urlDescarga;
    private String formatoArchivo;
    private double tamanoMB;
    private int limiteDescargas;
    private LocalDateTime vigenciaLicencia;

    public ProductoDigital(String nombre, String descripcion, double precio,
                           String urlDescarga, String formatoArchivo, double tamanoMB,
                           int limiteDescargas, LocalDateTime vigenciaLicencia) {
        super(nombre, descripcion, precio, Integer.MAX_VALUE); // Stock "infinito"
        this.urlDescarga = urlDescarga;
        this.formatoArchivo = formatoArchivo;
        this.tamanoMB = tamanoMB;
        this.limiteDescargas = limiteDescargas;
        this.vigenciaLicencia = vigenciaLicencia;
    }

    @Override
    public double calcularCostoTotal(int cantidad) {
        // Descuento por volumen en productos digitales
        double descuento = cantidad >= 10 ? 0.20 : (cantidad >= 5 ? 0.10 : 0.0);
        return getPrecio() * cantidad * (1 - descuento);
    }

    @Override
    public boolean puedeSerEnviado() {
        return false; // Es descargable
    }

    @Override
    public TipoProducto obtenerTipo() {
        return TipoProducto.DIGITAL;
    }

    public String generarLinkDescarga() {
        return String.format("%s?token=%s", urlDescarga, java.util.UUID.randomUUID());
    }

    // Getters
    public String getFormatoArchivo() { return formatoArchivo; }
    public double getTamanoMB() { return tamanoMB; }
    public int getLimiteDescargas() { return limiteDescargas; }
    public LocalDateTime getVigenciaLicencia() { return vigenciaLicencia; }
}
```

```java
// Servicio.java
package ecommerce;

import java.time.LocalDate;

/**
 * Servicio contratado (no es físico ni digital).
 */
public class Servicio extends Producto {
    private int duracionHoras;
    private boolean requierePresencial;
    private LocalDate fechaRealizacion;

    public Servicio(String nombre, String descripcion, double precio,
                    int duracionHoras, boolean requierePresencial,
                    LocalDate fechaRealizacion) {
        super(nombre, descripcion, precio, 1); // Solo 1 "en stock"
        this.duracionHoras = duracionHoras;
        this.requierePresencial = requierePresencial;
        this.fechaRealizacion = fechaRealizacion;
    }

    @Override
    public double calcularCostoTotal(int cantidad) {
        // Servicios no permiten cantidad > 1
        if (cantidad > 1) {
            throw new IllegalArgumentException("Los servicios no permiten cantidad múltiple");
        }
        return getPrecio();
    }

    @Override
    public boolean puedeSerEnviado() {
        return false;
    }

    @Override
    public TipoProducto obtenerTipo() {
        return TipoProducto.SERVICIO;
    }

    // Getters
    public int getDuracionHoras() { return duracionHoras; }
    public boolean isRequierePresencial() { return requierePresencial; }
    public LocalDate getFechaRealizacion() { return fechaRealizacion; }
}
```

### Paso 3: Crear Componentes con COMPOSICIÓN

```java
// DireccionEnvio.java
package ecommerce;

/**
 * Componente: Dirección de envío.
 * Demuestra COMPOSICIÓN: Pedido TIENE UNA DireccionEnvio
 */
public class DireccionEnvio {
    private String calle;
    private String numero;
    private String colonia;
    private String ciudad;
    private String estado;
    private String codigoPostal;
    private String pais;
    private String referencia;

    public DireccionEnvio(String calle, String numero, String colonia,
                          String ciudad, String estado, String codigoPostal,
                          String pais, String referencia) {
        this.calle = calle;
        this.numero = numero;
        this.colonia = colonia;
        this.ciudad = ciudad;
        this.estado = estado;
        this.codigoPostal = codigoPostal;
        this.pais = pais;
        this.referencia = referencia;
    }

    @Override
    public String toString() {
        return String.format("%s %s, %s, %s, %s, CP: %s, %s",
            calle, numero, colonia, ciudad, estado, codigoPostal, pais);
    }

    // Getters
    public String getCodigoPostal() { return codigoPostal; }
    public String getCiudad() { return ciudad; }
    public String getEstado() { return estado; }
}
```

```java
// MetodoPago.java
package ecommerce;

/**
 * Componente: Método de pago.
 */
public class MetodoPago {
    private String tipo; // "Tarjeta", "PayPal", "Transferencia"
    private String identificador; // Últimos 4 dígitos, email, etc.

    public MetodoPago(String tipo, String identificador) {
        this.tipo = tipo;
        this.identificador = identificador;
    }

    @Override
    public String toString() {
        return String.format("%s (...%s)", tipo, identificador);
    }

    // Getters
    public String getTipo() { return tipo; }
    public String getIdentificador() { return identificador; }
}
```

```java
// ItemCarrito.java
package ecommerce;

/**
 * Componente: Item en el carrito.
 */
public class ItemCarrito {
    private Producto producto;
    private int cantidad;

    public ItemCarrito(Producto producto, int cantidad) {
        if (!producto.verificarDisponibilidad(cantidad)) {
            throw new IllegalStateException("Stock insuficiente para " + producto.getNombre());
        }
        this.producto = producto;
        this.cantidad = cantidad;
    }

    public double calcularSubtotal() {
        return producto.calcularCostoTotal(cantidad);
    }

    // Getters
    public Producto getProducto() { return producto; }
    public int getCantidad() { return cantidad; }
    public void setCantidad(int cantidad) { this.cantidad = cantidad; }
}
```

```java
// Carrito.java
package ecommerce;

import java.util.ArrayList;
import java.util.List;

/**
 * COMPOSICIÓN: Carrito TIENE una lista de ItemCarrito.
 */
public class Carrito {
    private List<ItemCarrito> items;

    public Carrito() {
        this.items = new ArrayList<>();
    }

    public void agregarProducto(Producto producto, int cantidad) {
        // Verificar si ya existe
        for (ItemCarrito item : items) {
            if (item.getProducto().getId().equals(producto.getId())) {
                item.setCantidad(item.getCantidad() + cantidad);
                return;
            }
        }
        items.add(new ItemCarrito(producto, cantidad));
    }

    public void eliminarProducto(String productoId) {
        items.removeIf(item -> item.getProducto().getId().equals(productoId));
    }

    public double calcularTotal() {
        return items.stream()
                    .mapToDouble(ItemCarrito::calcularSubtotal)
                    .sum();
    }

    public void vaciar() {
        items.clear();
    }

    public boolean estaVacio() {
        return items.isEmpty();
    }

    public List<ItemCarrito> getItems() {
        return new ArrayList<>(items); // Copia defensiva
    }
}
```

```java
// Pedido.java
package ecommerce;

import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.List;

/**
 * COMPOSICIÓN: Pedido TIENE Carrito, DireccionEnvio, MetodoPago.
 */
public class Pedido {
    private static int contadorNumero = 1;

    private String numeroPedido;
    private String clienteNombre;
    private LocalDateTime fechaPedido;
    private Carrito carrito;
    private DireccionEnvio direccionEnvio;
    private MetodoPago metodoPago;
    private EstadoPedido estado;
    private double subtotal;
    private double costoEnvio;
    private double total;

    public Pedido(String clienteNombre, Carrito carrito,
                  DireccionEnvio direccionEnvio, MetodoPago metodoPago) {
        this.numeroPedido = "PED-" + String.format("%06d", contadorNumero++);
        this.clienteNombre = clienteNombre;
        this.fechaPedido = LocalDateTime.now();
        this.carrito = carrito;
        this.direccionEnvio = direccionEnvio;
        this.metodoPago = metodoPago;
        this.estado = EstadoPedido.PENDIENTE;

        calcularCostos();
    }

    private void calcularCostos() {
        this.subtotal = carrito.calcularTotal();
        this.costoEnvio = calcularCostoEnvio();
        this.total = subtotal + costoEnvio;
    }

    private double calcularCostoEnvio() {
        double costoTotal = 0.0;
        for (ItemCarrito item : carrito.getItems()) {
            if (item.getProducto() instanceof ProductoFisico) {
                ProductoFisico producto = (ProductoFisico) item.getProducto();
                costoTotal += producto.calcularCostoEnvio(direccionEnvio.getCodigoPostal());
            }
        }
        return costoTotal;
    }

    public void cambiarEstado(EstadoPedido nuevoEstado) {
        this.estado = nuevoEstado;
        System.out.println(String.format("Pedido %s cambió a: %s",
            numeroPedido, nuevoEstado.getValor()));
    }

    public String generarTicket() {
        StringBuilder ticket = new StringBuilder();
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd/MM/yyyy HH:mm");

        ticket.append("╔══════════════════════════════════════════════════════════════╗\n");
        ticket.append("║                     TICKET DE COMPRA                         ║\n");
        ticket.append("╚══════════════════════════════════════════════════════════════╝\n\n");
        ticket.append(String.format("PEDIDO: %s\n", numeroPedido));
        ticket.append(String.format("FECHA:  %s\n", fechaPedido.format(formatter)));
        ticket.append(String.format("CLIENTE: %s\n", clienteNombre));
        ticket.append(String.format("ESTADO: %s\n\n", estado.getValor()));
        ticket.append("──────────────────────────────────────────────────────────────\n");
        ticket.append("PRODUCTOS:\n");
        ticket.append("──────────────────────────────────────────────────────────────\n");

        for (ItemCarrito item : carrito.getItems()) {
            ticket.append(String.format("%2d x %-30s $%10.2f\n",
                item.getCantidad(),
                item.getProducto().getNombre(),
                item.calcularSubtotal()));
        }

        ticket.append("\n──────────────────────────────────────────────────────────────\n");
        ticket.append(String.format("SUBTOTAL:        $%43.2f\n", subtotal));
        ticket.append(String.format("ENVÍO:           $%43.2f\n", costoEnvio));
        ticket.append("══════════════════════════════════════════════════════════════\n");
        ticket.append(String.format("TOTAL:           $%43.2f\n", total));
        ticket.append("══════════════════════════════════════════════════════════════\n\n");
        ticket.append("DIRECCIÓN DE ENVÍO:\n");
        ticket.append(direccionEnvio.toString() + "\n\n");
        ticket.append("MÉTODO DE PAGO:\n");
        ticket.append(metodoPago.toString() + "\n");

        return ticket.toString();
    }

    // Getters
    public String getNumeroPedido() { return numeroPedido; }
    public EstadoPedido getEstado() { return estado; }
    public double getTotal() { return total; }
}
```

### Paso 4: Clase Principal de Demostración

```java
// SistemaEcommerce.java
package ecommerce;

import java.time.LocalDate;
import java.time.LocalDateTime;

public class SistemaEcommerce {

    public static void main(String[] args) {
        System.out.println("╔══════════════════════════════════════════════════════════════╗");
        System.out.println("║          SISTEMA DE E-COMMERCE - JAVA                        ║");
        System.out.println("║        Herencia y Composición en Java                        ║");
        System.out.println("╚══════════════════════════════════════════════════════════════╝\n");

        // ═══ CREAR PRODUCTOS (HERENCIA) ═══
        System.out.println("═══ CREANDO PRODUCTOS (Herencia) ═══\n");

        ProductoFisico laptop = new ProductoFisico(
            "Laptop Dell XPS 15",
            "Laptop de alta gama para desarrollo",
            25000.0,
            2.5,
            new double[]{35.0, 25.0, 2.0},
            10
        );

        ProductoDigital curso = new ProductoDigital(
            "Curso de Java Avanzado",
            "Curso completo de Java con certificación",
            1500.0,
            "https://cursos.com/java-advanced",
            "MP4",
            4500.0,
            5,
            LocalDateTime.now().plusYears(1)
        );

        Servicio consultoria = new Servicio(
            "Consultoría de Software",
            "Consultoría personalizada de arquitectura",
            3500.0,
            4,
            true,
            LocalDate.now().plusDays(7)
        );

        System.out.println("✓ Producto Físico: " + laptop);
        System.out.println("  Tipo: " + laptop.obtenerTipo().getValor());
        System.out.println("  Requiere envío: " + laptop.puedeSerEnviado());
        System.out.println("  Dimensiones: " + laptop.obtenerDimensionesStr());

        System.out.println("\n✓ Producto Digital: " + curso);
        System.out.println("  Tipo: " + curso.obtenerTipo().getValor());
        System.out.println("  Requiere envío: " + curso.puedeSerEnviado());
        System.out.println("  Formato: " + curso.getFormatoArchivo());

        System.out.println("\n✓ Servicio: " + consultoria);
        System.out.println("  Tipo: " + consultoria.obtenerTipo().getValor());
        System.out.println("  Duración: " + consultoria.getDuracionHoras() + " horas");

        // ═══ CREAR CARRITO (COMPOSICIÓN) ═══
        System.out.println("\n═══ CREANDO CARRITO (Composición) ═══\n");

        Carrito carrito = new Carrito();
        carrito.agregarProducto(laptop, 1);
        carrito.agregarProducto(curso, 2);

        System.out.println("✓ Carrito creado con " + carrito.getItems().size() + " productos");
        System.out.println("  Total: $" + String.format("%.2f", carrito.calcularTotal()));

        // ═══ CREAR PEDIDO (COMPOSICIÓN COMPLEJA) ═══
        System.out.println("\n═══ CREANDO PEDIDO (Composición) ═══\n");

        DireccionEnvio direccion = new DireccionEnvio(
            "Av. Insurgentes",
            "1234",
            "Del Valle",
            "Ciudad de México",
            "CDMX",
            "03100",
            "México",
            "Edificio azul"
        );

        MetodoPago metodoPago = new MetodoPago("Tarjeta de Crédito", "4532");

        Pedido pedido = new Pedido("Juan Pérez", carrito, direccion, metodoPago);

        System.out.println(pedido.generarTicket());

        // ═══ CAMBIAR ESTADOS ═══
        System.out.println("═══ PROCESANDO PEDIDO ═══\n");

        pedido.cambiarEstado(EstadoPedido.PAGADO);
        pedido.cambiarEstado(EstadoPedido.ENVIADO);
        pedido.cambiarEstado(EstadoPedido.ENTREGADO);

        System.out.println("\n✅ Sistema de E-commerce completado exitosamente");
        System.out.println("\n╚══════════════════════════════════════════════════════════════╝\n");
    }
}
```

## 🔧 Compilación y Ejecución

### Compilar todos los archivos:

```bash
# Crear estructura de directorios
mkdir -p ecommerce

# Compilar todos los archivos Java
javac -d . *.java

# Ejecutar el programa principal
java ecommerce.SistemaEcommerce
```

### Usando un IDE (IntelliJ IDEA / Eclipse):

1. Crear nuevo proyecto Java
2. Crear paquete `ecommerce`
3. Copiar todos los archivos `.java` al paquete
4. Ejecutar `SistemaEcommerce.java`

## 🔍 Comparación Detallada: Python vs Java

### Herencia

**Python:**

```python
class Producto(ABC):
    @abstractmethod
    def calcular_costo_total(self, cantidad: int) -> float:
        pass

class ProductoFisico(Producto):
    def calcular_costo_total(self, cantidad: int) -> float:
        return self.precio * cantidad
```

**Java:**

```java
public abstract class Producto {
    public abstract double calcularCostoTotal(int cantidad);
}

public class ProductoFisico extends Producto {
    @Override
    public double calcularCostoTotal(int cantidad) {
        return getPrecio() * cantidad;
    }
}
```

### Composición

**Python:**

```python
class Pedido:
    def __init__(self, carrito: Carrito, direccion: DireccionEnvio):
        self.carrito = carrito
        self.direccion = direccion
```

**Java:**

```java
public class Pedido {
    private Carrito carrito;
    private DireccionEnvio direccion;

    public Pedido(Carrito carrito, DireccionEnvio direccion) {
        this.carrito = carrito;
        this.direccion = direccion;
    }
}
```

## ✅ Puntos Clave Java

1. **`extends`**: Palabra clave para herencia
2. **`abstract`**: Clases y métodos abstractos
3. **`@Override`**: Anotación para sobrescribir métodos
4. **`super(...)`**: Llamar constructor padre
5. **Getters/Setters**: Acceso controlado a atributos privados
6. **Encapsulación**: Uso de `private`, `public`, `protected`
7. **Tipos estáticos**: Todo debe tener tipo explícito
8. **Enums**: Más estructurados que en Python

## 🎓 Conceptos Aplicados

- ✅ Herencia con `extends`
- ✅ Clases abstractas
- ✅ Métodos abstractos y concretos
- ✅ Composición de objetos
- ✅ Polimorfismo
- ✅ Enumeraciones
- ✅ Encapsulación
- ✅ Constructores con `super()`
- ✅ Getters y Setters
- ✅ Sobrescritura de métodos

## 🚀 Extensiones Posibles

1. Agregar interfaz `Descargable` para productos digitales
2. Implementar patrón Observer para notificaciones
3. Crear sistema de reseñas con herencia
4. Agregar persistencia con JDBC
5. Implementar API REST con Spring Boot
6. Sistema de autenticación
7. Integración con base de datos
8. Tests unitarios con JUnit
9. Sistema de cupones de descuento
10. Dashboard administrativo
