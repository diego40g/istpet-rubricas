# A6-Java. Refactorización Aplicando Principios SOLID

## 📋 Descripción

Versión Java del sistema que aplica los **5 principios SOLID**. Demuestra cómo refactorizar código violando SOLID a uno limpio y mantenible en Java.

## 🎯 Los 5 Principios

- **S** - Single Responsibility Principle
- **O** - Open/Closed Principle
- **L** - Liskov Substitution Principle
- **I** - Interface Segregation Principle
- **D** - Dependency Inversion Principle

## 📊 Python vs Java en SOLID

| Principio | Python                | Java                             |
| --------- | --------------------- | -------------------------------- |
| **SRP**   | Clases pequeñas       | Clases con responsabilidad única |
| **OCP**   | ABC + herencia        | Interface + abstract class       |
| **LSP**   | Duck typing           | Type system fuerte               |
| **ISP**   | Protocol              | Interfaces segregadas            |
| **DIP**   | Constructor injection | Constructor injection + IoC      |

## 💻 Implementación Java - Sistema de Biblioteca

### 1. Entidades (SRP - Responsabilidad Única)

```java
// Libro.java
package biblioteca;

public class Libro {
    private String id;
    private String titulo;
    private String autor;
    private String isbn;
    private boolean disponible;

    public Libro(String id, String titulo, String autor, String isbn) {
        this.id = id;
        this.titulo = titulo;
        this.autor = autor;
        this.isbn = isbn;
        this.disponible = true;
    }

    // Getters y Setters
    public String getId() { return id; }
    public String getTitulo() { return titulo; }
    public String getAutor() { return autor; }
    public String getIsbn() { return isbn; }
    public boolean isDisponible() { return disponible; }
    public void setDisponible(boolean disponible) { this.disponible = disponible; }
}
```

```java
// Usuario.java
package biblioteca;

public class Usuario {
    private String id;
    private String nombre;
    private String email;

    public Usuario(String id, String nombre, String email) {
        this.id = id;
        this.nombre = nombre;
        this.email = email;
    }

    public String getId() { return id; }
    public String getNombre() { return nombre; }
    public String getEmail() { return email; }
}
```

```java
// Prestamo.java
package biblioteca;

import java.time.LocalDate;

public class Prestamo {
    private String id;
    private Libro libro;
    private Usuario usuario;
    private LocalDate fechaPrestamo;
    private LocalDate fechaDevolucion;
    private boolean activo;

    public Prestamo(String id, Libro libro, Usuario usuario, LocalDate fechaDevolucion) {
        this.id = id;
        this.libro = libro;
        this.usuario = usuario;
        this.fechaPrestamo = LocalDate.now();
        this.fechaDevolucion = fechaDevolucion;
        this.activo = true;
    }

    // Getters
    public String getId() { return id; }
    public Libro getLibro() { return libro; }
    public Usuario getUsuario() { return usuario; }
    public LocalDate getFechaPrestamo() { return fechaPrestamo; }
    public LocalDate getFechaDevolucion() { return fechaDevolucion; }
    public boolean isActivo() { return activo; }
    public void setActivo(boolean activo) { this.activo = activo; }
}
```

### 2. Interfaces Segregadas (ISP)

```java
// Repositorio.java - Interface segregada para persistencia
package biblioteca;

import java.util.List;
import java.util.Optional;

public interface Repositorio<T> {
    void guardar(T entidad);
    Optional<T> buscarPorId(String id);
    List<T> buscarTodos();
    void eliminar(String id);
}
```

```java
// Notificador.java - Interface segregada para notificaciones
package biblioteca;

public interface Notificador {
    boolean enviar(String destinatario, String asunto, String mensaje);
}
```

```java
// CalculadorMultas.java - Interface segregada para cálculos
package biblioteca;

import java.time.LocalDate;

public interface CalculadorMultas {
    double calcular(LocalDate fechaDevolucion, LocalDate fechaReal);
}
```

### 3. Implementaciones (OCP - Open/Closed)

```java
// RepositorioMemoria.java - Implementación en memoria
package biblioteca;

import java.util.*;
import java.util.concurrent.ConcurrentHashMap;

public class RepositorioMemoria<T> implements Repositorio<T> {
    private final Map<String, T> almacenamiento = new ConcurrentHashMap<>();
    private final java.util.function.Function<T, String> obtenerIdFn;

    public RepositorioMemoria(java.util.function.Function<T, String> obtenerIdFn) {
        this.obtenerIdFn = obtenerIdFn;
    }

    @Override
    public void guardar(T entidad) {
        String id = obtenerIdFn.apply(entidad);
        almacenamiento.put(id, entidad);
    }

    @Override
    public Optional<T> buscarPorId(String id) {
        return Optional.ofNullable(almacenamiento.get(id));
    }

    @Override
    public List<T> buscarTodos() {
        return new ArrayList<>(almacenamiento.values());
    }

    @Override
    public void eliminar(String id) {
        almacenamiento.remove(id);
    }
}
```

```java
// NotificadorEmail.java - Implementación de notificador
package biblioteca;

public class NotificadorEmail implements Notificador {
    @Override
    public boolean enviar(String destinatario, String asunto, String mensaje) {
        System.out.println("📧 Email enviado a: " + destinatario);
        System.out.println("   Asunto: " + asunto);
        return true;
    }
}
```

```java
// CalculadorMultaEstandar.java - Estrategia de multa
package biblioteca;

import java.time.LocalDate;
import java.time.temporal.ChronoUnit;

public class CalculadorMultaEstandar implements CalculadorMultas {
    private static final double MULTA_POR_DIA = 10.0;

    @Override
    public double calcular(LocalDate fechaDevolucion, LocalDate fechaReal) {
        if (fechaReal.isAfter(fechaDevolucion)) {
            long diasRetraso = ChronoUnit.DAYS.between(fechaDevolucion, fechaReal);
            return diasRetraso * MULTA_POR_DIA;
        }
        return 0.0;
    }
}
```

### 4. Servicios (DIP - Dependency Inversion)

```java
// ServicioPrestamos.java - Depende de abstracciones, no de concretos
package biblioteca;

import java.time.LocalDate;
import java.util.UUID;

/**
 * DIP: ServicioPrestamos depende de INTERFACES (Repositorio, Notificador, CalculadorMultas)
 * no de implementaciones concretas. Esto permite cambiar implementaciones fácilmente.
 */
public class ServicioPrestamos {
    private final Repositorio<Prestamo> repositorioPrestamos;
    private final Repositorio<Libro> repositorioLibros;
    private final Notificador notificador;
    private final CalculadorMultas calculadorMultas;

    // Inyección de dependencias por constructor
    public ServicioPrestamos(Repositorio<Prestamo> repositorioPrestamos,
                             Repositorio<Libro> repositorioLibros,
                             Notificador notificador,
                             CalculadorMultas calculadorMultas) {
        this.repositorioPrestamos = repositorioPrestamos;
        this.repositorioLibros = repositorioLibros;
        this.notificador = notificador;
        this.calculadorMultas = calculadorMultas;
    }

    public Prestamo realizarPrestamo(Libro libro, Usuario usuario, int diasPrestamo) {
        if (!libro.isDisponible()) {
            throw new IllegalStateException("Libro no disponible");
        }

        LocalDate fechaDevolucion = LocalDate.now().plusDays(diasPrestamo);
        String id = "PREST-" + UUID.randomUUID().toString().substring(0, 8);

        Prestamo prestamo = new Prestamo(id, libro, usuario, fechaDevolucion);
        repositorioPrestamos.guardar(prestamo);

        libro.setDisponible(false);
        repositorioLibros.guardar(libro);

        notificador.enviar(
            usuario.getEmail(),
            "Préstamo realizado",
            String.format("Has solicitado el libro '%s'. Fecha de devolución: %s",
                libro.getTitulo(), fechaDevolucion)
        );

        return prestamo;
    }

    public double devolverLibro(String prestamoId) {
        Prestamo prestamo = repositorioPrestamos.buscarPorId(prestamoId)
            .orElseThrow(() -> new IllegalArgumentException("Préstamo no encontrado"));

        if (!prestamo.isActivo()) {
            throw new IllegalStateException("Préstamo ya cerrado");
        }

        double multa = calculadorMultas.calcular(
            prestamo.getFechaDevolucion(),
            LocalDate.now()
        );

        prestamo.setActivo(false);
        prestamo.getLibro().setDisponible(true);

        repositorioPrestamos.guardar(prestamo);
        repositorioLibros.guardar(prestamo.getLibro());

        if (multa > 0) {
            notificador.enviar(
                prestamo.getUsuario().getEmail(),
                "Multa por retraso",
                String.format("Tienes una multa de $%.2f por devolución tardía", multa)
            );
        }

        return multa;
    }
}
```

### 5. Sistema Principal (Facade)

```java
// SistemaBiblioteca.java
package biblioteca;

public class SistemaBiblioteca {

    public static void main(String[] args) {
        System.out.println("╔══════════════════════════════════════════════════════════════╗");
        System.out.println("║          SISTEMA BIBLIOTECA - SOLID EN JAVA                  ║");
        System.out.println("╚══════════════════════════════════════════════════════════════╝\n");

        // ═══ CONFIGURAR DEPENDENCIAS (DIP) ═══
        Repositorio<Libro> repoLibros = new RepositorioMemoria<>(Libro::getId);
        Repositorio<Usuario> repoUsuarios = new RepositorioMemoria<>(Usuario::getId);
        Repositorio<Prestamo> repoPrestamos = new RepositorioMemoria<>(Prestamo::getId);
        Notificador notificador = new NotificadorEmail();
        CalculadorMultas calculador = new CalculadorMultaEstandar();

        // ═══ CREAR SERVICIO (DIP - Inyección de Dependencias) ═══
        ServicioPrestamos servicio = new ServicioPrestamos(
            repoPrestamos,
            repoLibros,
            notificador,
            calculador
        );

        // ═══ CREAR ENTIDADES (SRP) ═══
        Libro libro = new Libro("L001", "Clean Code", "Robert Martin", "978-0132350884");
        Usuario usuario = new Usuario("U001", "Juan Pérez", "juan@email.com");

        repoLibros.guardar(libro);
        repoUsuarios.guardar(usuario);

        System.out.println("✓ Libro registrado: " + libro.getTitulo());
        System.out.println("✓ Usuario registrado: " + usuario.getNombre() + "\n");

        // ═══ REALIZAR PRÉSTAMO ═══
        System.out.println("═══ REALIZANDO PRÉSTAMO ═══\n");
        Prestamo prestamo = servicio.realizarPrestamo(libro, usuario, 14);
        System.out.println("✓ Préstamo ID: " + prestamo.getId());
        System.out.println("  Fecha devolución: " + prestamo.getFechaDevolucion());

        // ═══ DEVOLVER LIBRO ═══
        System.out.println("\n═══ DEVOLVIENDO LIBRO ═══\n");
        double multa = servicio.devolverLibro(prestamo.getId());
        System.out.println("✓ Libro devuelto");
        System.out.println("  Multa: $" + multa);

        System.out.println("\n╔══════════════════════════════════════════════════════════════╗");
        System.out.println("║                   PRINCIPIOS SOLID APLICADOS                 ║");
        System.out.println("╠══════════════════════════════════════════════════════════════╣");
        System.out.println("║ ✓ SRP: Cada clase tiene una responsabilidad única           ║");
        System.out.println("║ ✓ OCP: Extensible con nuevas implementaciones               ║");
        System.out.println("║ ✓ LSP: Sustitución correcta de implementaciones             ║");
        System.out.println("║ ✓ ISP: Interfaces segregadas y específicas                  ║");
        System.out.println("║ ✓ DIP: Dependencias inyectadas por interfaces               ║");
        System.out.println("╚══════════════════════════════════════════════════════════════╝\n");
    }
}
```

## 🔧 Compilación y Ejecución

```bash
# Crear estructura
mkdir -p biblioteca

# Compilar
javac -d . *.java

# Ejecutar
java biblioteca.SistemaBiblioteca
```

## 🔍 Análisis de SOLID en Java

### S - Single Responsibility

✅ **Bien aplicado:**

- `Libro`: Solo datos del libro
- `ServicioPrestamos`: Solo lógica de préstamos
- `NotificadorEmail`: Solo envía emails

### O - Open/Closed

✅ **Bien aplicado:**

- Nuevos `Notificador` sin modificar código
- Nuevos `CalculadorMultas` sin cambiar servicio
- Nuevos `Repositorio` intercambiables

### L - Liskov Substitution

✅ **Bien aplicado:**

- Cualquier `Repositorio<T>` funciona igual
- Cualquier `Notificador` es intercambiable
- Cualquier `CalculadorMultas` produce resultados válidos

### I - Interface Segregation

✅ **Bien aplicado:**

- `Repositorio<T>`: Solo operaciones CRUD
- `Notificador`: Solo envío de notificaciones
- `CalculadorMultas`: Solo cálculo de multas

### D - Dependency Inversion

✅ **Bien aplicado:**

- `ServicioPrestamos` depende de **interfaces**
- Inyección de dependencias por constructor
- Fácil cambiar implementaciones en `main()`

## 📊 Beneficios Obtenidos

| Aspecto            | Sin SOLID        | Con SOLID                   |
| ------------------ | ---------------- | --------------------------- |
| **Testing**        | Difícil          | Fácil (mocks de interfaces) |
| **Mantenimiento**  | Frágil           | Robusto                     |
| **Extensibilidad** | Requiere cambios | Solo agregar clases         |
| **Reutilización**  | Baja             | Alta                        |

## 🎓 Puntos Clave Java

1. **Interfaces** son clave para SOLID en Java
2. **Constructor injection** para DIP
3. **Generics** para reutilización (`Repositorio<T>`)
4. **Optional** para valores que pueden no existir
5. **Lambda functions** para flexibilidad
6. **ConcurrentHashMap** para thread-safety

## 🚀 Extensiones

1. Spring Framework para IoC/DI automática
2. JPA/Hibernate para persistencia real
3. JUnit + Mockito para testing
4. API REST con Spring Boot
5. Validaciones con Bean Validation
6. Logging con SLF4J
7. Métricas con Micrometer
8. Documentación con Swagger
