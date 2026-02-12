# A1. Sistema de Biblioteca - Clase Libro y Autor (JAVA)

## 📋 Descripción de la Actividad

Esta es la **versión en Java** de la actividad A1. Implementa el mismo sistema de biblioteca utilizando las características de Java para POO: clases, objetos, atributos y métodos.

## 💻 Solución Completa en Java

### Clase Autor

```java
// Autor.java

import java.time.LocalDate;
import java.time.Period;
import java.time.format.DateTimeFormatter;

/**
 * Clase que representa a un autor de libros
 */
public class Autor {
    // Atributos de instancia
    private String nombre;
    private String nacionalidad;
    private String fechaNacimiento; // Formato: "DD/MM/AAAA"

    /**
     * Constructor que inicializa un autor con sus datos personales
     *
     * @param nombre           Nombre completo del autor
     * @param nacionalidad     País de origen
     * @param fechaNacimiento  Fecha en formato "DD/MM/AAAA"
     */
    public Autor(String nombre, String nacionalidad, String fechaNacimiento) {
        this.nombre = nombre;
        this.nacionalidad = nacionalidad;
        this.fechaNacimiento = fechaNacimiento;
    }

    /**
     * Calcula la edad del autor basándose en su fecha de nacimiento
     *
     * @return Edad en años
     */
    public int edad() {
        // Convertir string a LocalDate
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd/MM/yyyy");
        LocalDate fechaNac = LocalDate.parse(this.fechaNacimiento, formatter);

        // Calcular edad
        LocalDate hoy = LocalDate.now();
        Period periodo = Period.between(fechaNac, hoy);

        return periodo.getYears();
    }

    /**
     * Genera una presentación formal del autor
     *
     * @return Texto de presentación
     */
    public String presentacion() {
        return String.format("%s es un autor de nacionalidad %s, nacido el %s (%d años).",
            this.nombre, this.nacionalidad, this.fechaNacimiento, this.edad());
    }

    // Getters
    public String getNombre() {
        return nombre;
    }

    public String getNacionalidad() {
        return nacionalidad;
    }

    public String getFechaNacimiento() {
        return fechaNacimiento;
    }

    // Setters
    public void setNombre(String nombre) {
        this.nombre = nombre;
    }

    public void setNacionalidad(String nacionalidad) {
        this.nacionalidad = nacionalidad;
    }

    public void setFechaNacimiento(String fechaNacimiento) {
        this.fechaNacimiento = fechaNacimiento;
    }

    /**
     * Representación en string del autor
     */
    @Override
    public String toString() {
        return String.format("%s (%s)", this.nombre, this.nacionalidad);
    }
}
```

### Clase Libro

```java
// Libro.java

import java.time.Year;

/**
 * Clase que representa un libro en la biblioteca
 */
public class Libro {
    // Atributo de clase (static) - contador de libros totales
    private static int totalLibros = 0;

    // Atributos de instancia
    private String isbn;
    private String titulo;
    private String autor;
    private int anioPublicacion;
    private boolean disponible;

    /**
     * Constructor que inicializa un libro con su información bibliográfica
     *
     * @param isbn            Código ISBN único
     * @param titulo          Título del libro
     * @param autor           Nombre del autor
     * @param anioPublicacion Año de publicación
     * @param disponible      Estado inicial (true por defecto)
     */
    public Libro(String isbn, String titulo, String autor, int anioPublicacion, boolean disponible) {
        this.isbn = isbn;
        this.titulo = titulo;
        this.autor = autor;
        this.anioPublicacion = anioPublicacion;
        this.disponible = disponible;

        // Incrementar contador de libros
        Libro.totalLibros++;
    }

    /**
     * Constructor sobrecargado con disponible = true por defecto
     */
    public Libro(String isbn, String titulo, String autor, int anioPublicacion) {
        this(isbn, titulo, autor, anioPublicacion, true);
    }

    /**
     * Marca el libro como prestado (no disponible)
     *
     * @return true si se pudo prestar, false si ya estaba prestado
     */
    public boolean prestar() {
        if (this.disponible) {
            this.disponible = false;
            System.out.printf("✓ Libro '%s' prestado exitosamente.%n", this.titulo);
            return true;
        } else {
            System.out.printf("✗ El libro '%s' no está disponible.%n", this.titulo);
            return false;
        }
    }

    /**
     * Marca el libro como devuelto (disponible)
     *
     * @return true si se pudo devolver, false si ya estaba disponible
     */
    public boolean devolver() {
        if (!this.disponible) {
            this.disponible = true;
            System.out.printf("✓ Libro '%s' devuelto exitosamente.%n", this.titulo);
            return true;
        } else {
            System.out.printf("✗ El libro '%s' ya estaba disponible.%n", this.titulo);
            return false;
        }
    }

    /**
     * Genera información completa del libro
     *
     * @return Información formateada del libro
     */
    public String mostrarInfo() {
        String estado = this.disponible ? "Disponible ✓" : "Prestado ✗";
        int antiguedad = Year.now().getValue() - this.anioPublicacion;

        StringBuilder info = new StringBuilder();
        info.append("\n");
        info.append("╔══════════════════════════════════════════════════════════════╗\n");
        info.append("║                    INFORMACIÓN DEL LIBRO                     ║\n");
        info.append("╚══════════════════════════════════════════════════════════════╝\n");
        info.append(String.format("  ISBN:            %s%n", this.isbn));
        info.append(String.format("  Título:          %s%n", this.titulo));
        info.append(String.format("  Autor:           %s%n", this.autor));
        info.append(String.format("  Año:             %d (hace %d años)%n", this.anioPublicacion, antiguedad));
        info.append(String.format("  Estado:          %s%n", estado));
        info.append("═══════════════════════════════════════════════════════════════\n");

        return info.toString();
    }

    /**
     * Verifica si el libro tiene más de 50 años
     *
     * @return true si es antiguo, false en caso contrario
     */
    public boolean esAntiguo() {
        int antiguedad = Year.now().getValue() - this.anioPublicacion;
        return antiguedad > 50;
    }

    // Getters
    public String getIsbn() {
        return isbn;
    }

    public String getTitulo() {
        return titulo;
    }

    public String getAutor() {
        return autor;
    }

    public int getAnioPublicacion() {
        return anioPublicacion;
    }

    public boolean isDisponible() {
        return disponible;
    }

    /**
     * Método estático para obtener el total de libros creados
     */
    public static int getTotalLibros() {
        return totalLibros;
    }

    // Setters
    public void setIsbn(String isbn) {
        this.isbn = isbn;
    }

    public void setTitulo(String titulo) {
        this.titulo = titulo;
    }

    public void setAutor(String autor) {
        this.autor = autor;
    }

    public void setAnioPublicacion(int anioPublicacion) {
        this.anioPublicacion = anioPublicacion;
    }

    /**
     * Representación en string del libro
     */
    @Override
    public String toString() {
        String estado = this.disponible ? "✓" : "✗";
        return String.format("%s [%s] %s - %s (%d)",
            estado, this.isbn, this.titulo, this.autor, this.anioPublicacion);
    }
}
```

### Clase Principal con Main

```java
// SistemaBiblioteca.java

import java.util.ArrayList;
import java.util.List;

/**
 * Clase principal para demostrar el sistema de biblioteca
 */
public class SistemaBiblioteca {

    public static void main(String[] args) {
        System.out.println("╔═══════════════════════════════════════════════════════════════╗");
        System.out.println("║          SISTEMA DE BIBLIOTECA - GESTIÓN DE LIBROS           ║");
        System.out.println("╚═══════════════════════════════════════════════════════════════╝\n");

        // ─────────────────────────────────────────────────────────────
        // 1. CREAR AUTORES
        // ─────────────────────────────────────────────────────────────
        System.out.println("1. CREANDO AUTORES");
        System.out.println("─".repeat(63));

        Autor autor1 = new Autor("Gabriel García Márquez", "Colombia", "06/03/1927");
        Autor autor2 = new Autor("Isabel Allende", "Chile", "02/08/1942");
        Autor autor3 = new Autor("Jorge Luis Borges", "Argentina", "24/08/1899");

        System.out.printf("✓ Autor 1: %s%n", autor1);
        System.out.printf("✓ Autor 2: %s%n", autor2);
        System.out.printf("✓ Autor 3: %s%n", autor3);

        System.out.printf("%n%s%n", autor1.presentacion());
        System.out.printf("%s%n", autor2.presentacion());
        System.out.printf("%s%n", autor3.presentacion());

        // ─────────────────────────────────────────────────────────────
        // 2. CREAR LIBROS
        // ─────────────────────────────────────────────────────────────
        System.out.println("\n\n2. CREANDO LIBROS");
        System.out.println("─".repeat(63));

        Libro libro1 = new Libro(
            "978-0307474728",
            "Cien años de soledad",
            "Gabriel García Márquez",
            1967
        );

        Libro libro2 = new Libro(
            "978-1501117015",
            "La casa de los espíritus",
            "Isabel Allende",
            1982
        );

        Libro libro3 = new Libro(
            "978-0142437223",
            "Ficciones",
            "Jorge Luis Borges",
            1944
        );

        Libro libro4 = new Libro(
            "978-0060883287",
            "El amor en los tiempos del cólera",
            "Gabriel García Márquez",
            1985,
            false  // Ya prestado
        );

        // Crear lista de libros
        List<Libro> libros = new ArrayList<>();
        libros.add(libro1);
        libros.add(libro2);
        libros.add(libro3);
        libros.add(libro4);

        System.out.println("\nLista de libros en el sistema:");
        for (int i = 0; i < libros.size(); i++) {
            System.out.printf("  %d. %s%n", i + 1, libros.get(i));
        }

        System.out.printf("%n📚 Total de libros en el sistema: %d%n", Libro.getTotalLibros());

        // ─────────────────────────────────────────────────────────────
        // 3. OPERACIONES CON LIBROS
        // ─────────────────────────────────────────────────────────────
        System.out.println("\n\n3. OPERACIONES CON LIBROS");
        System.out.println("─".repeat(63));

        // Mostrar información detallada de un libro
        System.out.println("\n📖 Información detallada del libro 1:");
        System.out.println(libro1.mostrarInfo());

        // Intentar prestar libros
        System.out.println("\n🔄 Intentando prestar libros:");
        libro1.prestar();  // Éxito
        libro1.prestar();  // Fallo (ya prestado)
        libro4.prestar();  // Fallo (ya prestado)

        // Devolver libros
        System.out.println("\n🔄 Devolviendo libros:");
        libro1.devolver();  // Éxito
        libro1.devolver();  // Fallo (ya disponible)

        // ─────────────────────────────────────────────────────────────
        // 4. VERIFICAR LIBROS ANTIGUOS
        // ─────────────────────────────────────────────────────────────
        System.out.println("\n\n4. VERIFICACIÓN DE LIBROS ANTIGUOS (>50 años)");
        System.out.println("─".repeat(63));

        for (Libro libro : libros) {
            String esAntiguo = libro.esAntiguo() ? "SÍ" : "NO";
            System.out.printf("%-45s Antiguo: %s%n", libro.getTitulo(), esAntiguo);
        }

        // ─────────────────────────────────────────────────────────────
        // 5. ESTADÍSTICAS FINALES
        // ─────────────────────────────────────────────────────────────
        System.out.println("\n\n5. ESTADÍSTICAS FINALES");
        System.out.println("─".repeat(63));

        int disponibles = 0;
        int prestados = 0;
        int antiguos = 0;

        for (Libro libro : libros) {
            if (libro.isDisponible()) {
                disponibles++;
            } else {
                prestados++;
            }
            if (libro.esAntiguo()) {
                antiguos++;
            }
        }

        System.out.printf("📚 Total de libros:       %d%n", Libro.getTotalLibros());
        System.out.printf("✓ Libros disponibles:    %d%n", disponibles);
        System.out.printf("✗ Libros prestados:      %d%n", prestados);
        System.out.printf("📅 Libros antiguos:       %d%n", antiguos);

        System.out.println("\n╚═══════════════════════════════════════════════════════════════╝");
    }
}
```

## 🔧 Compilación y Ejecución

### Opción 1: Línea de comandos

```bash
# Compilar todas las clases
javac Autor.java Libro.java SistemaBiblioteca.java

# Ejecutar el programa
java SistemaBiblioteca
```

### Opción 2: IDE (IntelliJ IDEA, Eclipse, NetBeans)

1. Crear un nuevo proyecto Java
2. Crear las tres clases en el paquete por defecto
3. Ejecutar la clase `SistemaBiblioteca` (tiene el método `main`)

## 📊 Comparación Python vs Java

| Aspecto               | Python                 | Java                             |
| --------------------- | ---------------------- | -------------------------------- |
| **Constructor**       | `__init__(self, ...)`  | `public NombreClase(...)`        |
| **Atributos**         | `self.atributo`        | `this.atributo` (con tipo)       |
| **Visibilidad**       | Convención `_`         | `private`, `public`, `protected` |
| **Métodos**           | `def metodo(self):`    | `public tipo metodo()`           |
| **toString**          | `__str__`, `__repr__`  | `@Override toString()`           |
| **Fechas**            | `datetime` module      | `java.time.LocalDate`            |
| **Formateo**          | f-strings `f"{var}"`   | `String.format()`, `printf`      |
| **Getters/Setters**   | Opcional (@property)   | Explícitos y recomendados        |
| **Atributo estático** | `NombreClase.atributo` | `static tipo atributo`           |

## ✅ Puntos Clave de Java

1. **Tipos explícitos**: Todos los atributos y parámetros deben declarar su tipo
2. **Modificadores de acceso**: Usar `private` para encapsulación, `public` para interfaz
3. **Getters y Setters**: Convención estándar en Java
4. **Sobrecarga de constructores**: Múltiples constructores con diferentes parámetros
5. **@Override**: Anotación para indicar sobreescritura de métodos
6. **static**: Atributos y métodos compartidos por todas las instancias
7. **StringBuilder**: Para construir strings complejos eficientemente

## 🎓 Conceptos Aplicados

- ✅ Definición de clases en Java
- ✅ Constructores y sobrecarga
- ✅ Atributos de instancia con tipos
- ✅ Atributos de clase (static)
- ✅ Métodos de instancia
- ✅ Encapsulación con private
- ✅ Getters y Setters
- ✅ @Override toString()
- ✅ java.time para manejo de fechas
- ✅ ArrayList para colecciones

## 🚀 Extensiones Posibles

1. Crear clase `Biblioteca` con ArrayList de libros
2. Implementar interfaces para comparación (Comparable)
3. Agregar manejo de excepciones personalizadas
4. Implementar serialización para persistencia
5. Crear GUI con JavaFX o Swing
6. Conectar con base de datos (JDBC)
