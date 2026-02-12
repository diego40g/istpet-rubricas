# A2. Sistema de Playlist Musical - Constructores y Métodos Especiales (JAVA)

## 📋 Descripción de la Actividad

Esta es la **versión en Java** de la actividad A2. Implementa un sistema de playlist musical utilizando constructores múltiples, métodos estáticos, sobrecarga de operadores (donde aplique en Java) y colecciones.

## 💻 Solución Completa en Java

### Clase Cancion

```java
// Cancion.java

import java.time.LocalDate;
import java.time.format.DateTimeFormatter;

/**
 * Clase que representa una canción musical
 */
public class Cancion {
    // Atributos de instancia
    private String titulo;
    private String artista;
    private String album;
    private int duracionSegundos;
    private String genero;
    private LocalDate fechaLanzamiento;

    /**
     * Constructor principal completo
     */
    public Cancion(String titulo, String artista, String album,
                   int duracionSegundos, String genero, LocalDate fechaLanzamiento) {
        this.titulo = titulo;
        this.artista = artista;
        this.album = album;
        this.duracionSegundos = duracionSegundos;
        this.genero = genero;
        this.fechaLanzamiento = fechaLanzamiento;
    }

    /**
     * Constructor simplificado sin álbum ni fecha
     */
    public Cancion(String titulo, String artista, int duracionSegundos, String genero) {
        this(titulo, artista, "Desconocido", duracionSegundos, genero, LocalDate.now());
    }

    /**
     * Factory method: Crea canción desde formato "minutos:segundos"
     */
    public static Cancion desdeMinutosSegundos(String titulo, String artista,
                                                String duracion, String genero) {
        String[] partes = duracion.split(":");
        int minutos = Integer.parseInt(partes[0]);
        int segundos = Integer.parseInt(partes[1]);
        int duracionTotal = (minutos * 60) + segundos;

        return new Cancion(titulo, artista, duracionTotal, genero);
    }

    /**
     * Factory method: Crea canción desde string de fecha
     */
    public static Cancion conFechaString(String titulo, String artista, String album,
                                         int duracionSegundos, String genero,
                                         String fechaString) {
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd/MM/yyyy");
        LocalDate fecha = LocalDate.parse(fechaString, formatter);

        return new Cancion(titulo, artista, album, duracionSegundos, genero, fecha);
    }

    /**
     * Método estático: Convierte segundos a formato "MM:SS"
     */
    public static String formatearDuracion(int segundos) {
        int minutos = segundos / 60;
        int segs = segundos % 60;
        return String.format("%d:%02d", minutos, segs);
    }

    /**
     * Convierte la duración a minutos decimales
     */
    public double getDuracionMinutos() {
        return duracionSegundos / 60.0;
    }

    /**
     * Obtiene la duración formateada
     */
    public String getDuracionFormateada() {
        return formatearDuracion(this.duracionSegundos);
    }

    /**
     * Calcula años desde el lanzamiento
     */
    public int getAniosDesdeLA

zamiento() {
        if (fechaLanzamiento == null) {
            return 0;
        }
        return LocalDate.now().getYear() - fechaLanzamiento.getYear();
    }

    /**
     * Verifica si es una canción clásica (>20 años)
     */
    public boolean esClasica() {
        return getAniosDesdeLanzamiento() > 20;
    }

    // Getters
    public String getTitulo() { return titulo; }
    public String getArtista() { return artista; }
    public String getAlbum() { return album; }
    public int getDuracionSegundos() { return duracionSegundos; }
    public String getGenero() { return genero; }
    public LocalDate getFechaLanzamiento() { return fechaLanzamiento; }

    // Setters
    public void setTitulo(String titulo) { this.titulo = titulo; }
    public void setArtista(String artista) { this.artista = artista; }
    public void setAlbum(String album) { this.album = album; }
    public void setDuracionSegundos(int duracionSegundos) {
        this.duracionSegundos = duracionSegundos;
    }
    public void setGenero(String genero) { this.genero = genero; }
    public void setFechaLanzamiento(LocalDate fechaLanzamiento) {
        this.fechaLanzamiento = fechaLanzamiento;
    }

    @Override
    public String toString() {
        return String.format("%s - %s [%s] (%s)",
            titulo, artista, getDuracionFormateada(), genero);
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;

        Cancion cancion = (Cancion) obj;
        return titulo.equals(cancion.titulo) && artista.equals(cancion.artista);
    }

    @Override
    public int hashCode() {
        return titulo.hashCode() + artista.hashCode();
    }
}
```

### Clase Playlist

```java
// Playlist.java

import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;
import java.util.Collections;
import java.util.Comparator;

/**
 * Clase que representa una playlist musical con funcionalidades avanzadas
 */
public class Playlist implements Iterable<Cancion> {
    // Atributos
    private String nombre;
    private String creador;
    private List<Cancion> canciones;
    private int reproduccionesActuales;

    /**
     * Constructor principal
     */
    public Playlist(String nombre, String creador) {
        this.nombre = nombre;
        this.creador = creador;
        this.canciones = new ArrayList<>();
        this.reproduccionesActuales = 0;
    }

    /**
     * Factory method: Crea playlist vacía rápida
     */
    public static Playlist playlistVacia(String nombre) {
        return new Playlist(nombre, "Anónimo");
    }

    /**
     * Factory method: Crea playlist desde lista de canciones
     */
    public static Playlist desdeCanciones(String nombre, String creador,
                                          List<Cancion> canciones) {
        Playlist playlist = new Playlist(nombre, creador);
        playlist.canciones.addAll(canciones);
        return playlist;
    }

    /**
     * Agrega una canción a la playlist
     */
    public void agregar(Cancion cancion) {
        if (cancion == null) {
            throw new IllegalArgumentException("La canción no puede ser null");
        }
        this.canciones.add(cancion);
        System.out.printf("✓ '%s' agregada a '%s'%n", cancion.getTitulo(), this.nombre);
    }

    /**
     * Elimina una canción por índice
     */
    public boolean eliminar(int indice) {
        if (indice < 0 || indice >= canciones.size()) {
            System.out.println("❌ Índice inválido");
            return false;
        }

        Cancion eliminada = canciones.remove(indice);
        System.out.printf("✓ '%s' eliminada de '%s'%n", eliminada.getTitulo(), this.nombre);
        return true;
    }

    /**
     * Elimina una canción por objeto
     */
    public boolean eliminar(Cancion cancion) {
        boolean resultado = canciones.remove(cancion);
        if (resultado) {
            System.out.printf("✓ '%s' eliminada de '%s'%n", cancion.getTitulo(), this.nombre);
        } else {
            System.out.println("❌ Canción no encontrada");
        }
        return resultado;
    }

    /**
     * Obtiene canción por índice (equivalente a __getitem__ en Python)
     */
    public Cancion obtener(int indice) {
        if (indice < 0 || indice >= canciones.size()) {
            throw new IndexOutOfBoundsException("Índice fuera de rango");
        }
        return canciones.get(indice);
    }

    /**
     * Obtiene cantidad de canciones (equivalente a __len__ en Python)
     */
    public int size() {
        return canciones.size();
    }

    /**
     * Verifica si está vacía
     */
    public boolean estaVacia() {
        return canciones.isEmpty();
    }

    /**
     * Combina esta playlist con otra (equivalente a __add__ en Python)
     */
    public Playlist combinar(Playlist otra) {
        String nuevoNombre = this.nombre + " + " + otra.nombre;
        Playlist combinada = new Playlist(nuevoNombre, this.creador);

        combinada.canciones.addAll(this.canciones);
        combinada.canciones.addAll(otra.canciones);

        return combinada;
    }

    /**
     * Calcula duración total en segundos
     */
    public int getDuracionTotal() {
        return canciones.stream()
                       .mapToInt(Cancion::getDuracionSegundos)
                       .sum();
    }

    /**
     * Obtiene duración total formateada
     */
    public String getDuracionFormateada() {
        int totalSegundos = getDuracionTotal();
        int horas = totalSegundos / 3600;
        int minutos = (totalSegundos % 3600) / 60;
        int segundos = totalSegundos % 60;

        return String.format("%02d:%02d:%02d", horas, minutos, segundos);
    }

    /**
     * Reproduce la playlist (incrementa contador)
     */
    public void reproducir() {
        if (canciones.isEmpty()) {
            System.out.println("❌ La playlist está vacía");
            return;
        }

        this.reproduccionesActuales++;
        System.out.printf("🎵 Reproduciendo '%s' (reproducción #%d)%n",
                         this.nombre, this.reproduccionesActuales);

        for (int i = 0; i < canciones.size(); i++) {
            System.out.printf("  %d. %s%n", i + 1, canciones.get(i));
        }
    }

    /**
     * Mezcla aleatoriamente las canciones
     */
    public void mezclar() {
        Collections.shuffle(canciones);
        System.out.println("🔀 Playlist mezclada");
    }

    /**
     * Ordena canciones por título
     */
    public void ordenarPorTitulo() {
        canciones.sort(Comparator.comparing(Cancion::getTitulo));
        System.out.println("✓ Ordenado por título");
    }

    /**
     * Ordena canciones por artista
     */
    public void ordenarPorArtista() {
        canciones.sort(Comparator.comparing(Cancion::getArtista));
        System.out.println("✓ Ordenado por artista");
    }

    /**
     * Ordena canciones por duración
     */
    public void ordenarPorDuracion() {
        canciones.sort(Comparator.comparingInt(Cancion::getDuracionSegundos));
        System.out.println("✓ Ordenado por duración");
    }

    /**
     * Filtra canciones por género
     */
    public List<Cancion> filtrarPorGenero(String genero) {
        List<Cancion> filtradas = new ArrayList<>();
        for (Cancion cancion : canciones) {
            if (cancion.getGenero().equalsIgnoreCase(genero)) {
                filtradas.add(cancion);
            }
        }
        return filtradas;
    }

    /**
     * Busca canciones por palabra clave en título
     */
    public List<Cancion> buscar(String palabraClave) {
        List<Cancion> resultados = new ArrayList<>();
        String palabraLower = palabraClave.toLowerCase();

        for (Cancion cancion : canciones) {
            if (cancion.getTitulo().toLowerCase().contains(palabraLower)) {
                resultados.add(cancion);
            }
        }

        return resultados;
    }

    /**
     * Obtiene estadísticas de la playlist
     */
    public String getEstadisticas() {
        if (canciones.isEmpty()) {
            return "Playlist vacía";
        }

        int totalCanciones = canciones.size();
        double duracionPromedio = getDuracionTotal() / (double) totalCanciones;

        // Encontrar canción más larga y más corta
        Cancion masLarga = canciones.stream()
            .max(Comparator.comparingInt(Cancion::getDuracionSegundos))
            .orElse(null);

        Cancion masCorta = canciones.stream()
            .min(Comparator.comparingInt(Cancion::getDuracionSegundos))
            .orElse(null);

        StringBuilder stats = new StringBuilder();
        stats.append(String.format("📊 Estadísticas de '%s':%n", nombre));
        stats.append(String.format("   Total canciones:    %d%n", totalCanciones));
        stats.append(String.format("   Duración total:     %s%n", getDuracionFormateada()));
        stats.append(String.format("   Duración promedio:  %.1f segundos%n", duracionPromedio));
        stats.append(String.format("   Canción más larga:  %s (%s)%n",
                                  masLarga.getTitulo(), masLarga.getDuracionFormateada()));
        stats.append(String.format("   Canción más corta:  %s (%s)%n",
                                  masCorta.getTitulo(), masCorta.getDuracionFormateada()));
        stats.append(String.format("   Reproducciones:     %d%n", reproduccionesActuales));

        return stats.toString();
    }

    // Getters
    public String getNombre() { return nombre; }
    public String getCreador() { return creador; }
    public List<Cancion> getCanciones() { return new ArrayList<>(canciones); }
    public int getReproducciones() { return reproduccionesActuales; }

    // Implementación de Iterable
    @Override
    public Iterator<Cancion> iterator() {
        return canciones.iterator();
    }

    @Override
    public String toString() {
        return String.format("Playlist '%s' por %s (%d canciones, %s)",
                           nombre, creador, canciones.size(), getDuracionFormateada());
    }
}
```

### Clase Principal con Main

```java
// SistemaPlaylist.java

import java.time.LocalDate;
import java.util.List;

public class SistemaPlaylist {

    public static void main(String[] args) {
        System.out.println("╔═══════════════════════════════════════════════════════════════╗");
        System.out.println("║         SISTEMA DE PLAYLIST MUSICAL - DEMOSTRACIÓN           ║");
        System.out.println("╚═══════════════════════════════════════════════════════════════╝\n");

        // ─────────────────────────────────────────────────────────────
        // 1. CREAR CANCIONES CON DIFERENTES CONSTRUCTORES
        // ─────────────────────────────────────────────────────────────
        System.out.println("1. CREANDO CANCIONES");
        System.out.println("─".repeat(63));

        // Constructor completo
        Cancion cancion1 = new Cancion(
            "Bohemian Rhapsody",
            "Queen",
            "A Night at the Opera",
            354,
            "Rock",
            LocalDate.of(1975, 10, 31)
        );

        // Constructor simplificado
        Cancion cancion2 = new Cancion(
            "Billie Jean",
            "Michael Jackson",
            293,
            "Pop"
        );

        // Factory method: desde minutos:segundos
        Cancion cancion3 = Cancion.desdeMinutosSegundos(
            "Stairway to Heaven",
            "Led Zeppelin",
            "8:02",
            "Rock"
        );

        // Factory method: con fecha string
        Cancion cancion4 = Cancion.conFechaString(
            "Imagine",
            "John Lennon",
            "Imagine",
            183,
            "Rock",
            "11/10/1971"
        );

        System.out.println("✓ Canción 1: " + cancion1);
        System.out.println("✓ Canción 2: " + cancion2);
        System.out.println("✓ Canción 3: " + cancion3);
        System.out.println("✓ Canción 4: " + cancion4);

        // ─────────────────────────────────────────────────────────────
        // 2. INFORMACIÓN DE CANCIONES
        // ─────────────────────────────────────────────────────────────
        System.out.println("\n\n2. INFORMACIÓN DE CANCIONES");
        System.out.println("─".repeat(63));

        System.out.printf("'%s' dura %.2f minutos%n",
                         cancion1.getTitulo(), cancion1.getDuracionMinutos());
        System.out.printf("'%s' tiene %d años%n",
                         cancion4.getTitulo(), cancion4.getAniosDesdeLanzamiento());
        System.out.printf("'%s' es clásica: %b%n",
                         cancion1.getTitulo(), cancion1.esClasica());

        // ─────────────────────────────────────────────────────────────
        // 3. CREAR PLAYLISTS
        // ─────────────────────────────────────────────────────────────
        System.out.println("\n\n3. CREANDO PLAYLISTS");
        System.out.println("─".repeat(63));

        Playlist rock = new Playlist("Rock Clásico", "Juan Pérez");
        rock.agregar(cancion1);
        rock.agregar(cancion3);
        rock.agregar(cancion4);

        Playlist pop = Playlist.playlistVacia("Pop Hits");
        pop.agregar(cancion2);

        System.out.printf("%n%s%n", rock);
        System.out.printf("%s%n", pop);

        // ─────────────────────────────────────────────────────────────
        // 4. OPERACIONES CON PLAYLIST
        // ─────────────────────────────────────────────────────────────
        System.out.println("\n\n4. OPERACIONES CON PLAYLIST");
        System.out.println("─".repeat(63));

        // Acceso por índice
        System.out.println("\n📀 Primera canción: " + rock.obtener(0));

        // Iterar sobre playlist
        System.out.println("\n🎵 Todas las canciones de Rock:");
        int i = 1;
        for (Cancion c : rock) {
            System.out.printf("  %d. %s%n", i++, c);
        }

        // Combinar playlists
        System.out.println("\n🔗 Combinando playlists:");
        Playlist combinada = rock.combinar(pop);
        System.out.println(combinada);

        // ─────────────────────────────────────────────────────────────
        // 5. FUNCIONALIDADES AVANZADAS
        // ─────────────────────────────────────────────────────────────
        System.out.println("\n\n5. FUNCIONALIDADES AVANZADAS");
        System.out.println("─".repeat(63));

        // Reproducir
        System.out.println("\n▶️  Reproducción:");
        rock.reproducir();

        // Ordenar
        System.out.println("\n📊 Ordenando por duración:");
        rock.ordenarPorDuracion();
        for (Cancion c : rock) {
            System.out.printf("  %s - %s%n", c.getTitulo(), c.getDuracionFormateada());
        }

        // Mezclar
        System.out.println();
        rock.mezclar();

        // Buscar
        System.out.println("\n🔍 Buscando 'Heaven':");
        List<Cancion> resultados = rock.buscar("Heaven");
        for (Cancion c : resultados) {
            System.out.println("  Encontrado: " + c);
        }

        // ─────────────────────────────────────────────────────────────
        // 6. ESTADÍSTICAS
        // ─────────────────────────────────────────────────────────────
        System.out.println("\n\n6. ESTADÍSTICAS");
        System.out.println("─".repeat(63));
        System.out.println();
        System.out.print(rock.getEstadisticas());

        System.out.println("\n╚═══════════════════════════════════════════════════════════════╝");
    }
}
```

## 🔧 Compilación y Ejecución

```bash
# Compilar
javac Cancion.java Playlist.java SistemaPlaylist.java

# Ejecutar
java SistemaPlaylist
```

## 📊 Comparación Python vs Java

| Característica Python | Equivalente Java                 |
| --------------------- | -------------------------------- |
| `@classmethod`        | Métodos estáticos factory        |
| `@staticmethod`       | `static` methods                 |
| `@property`           | Getters con convención `get/set` |
| `__getitem__`         | Método `obtener(int)`            |
| `__len__`             | Método `size()`                  |
| `__add__`             | Método `combinar()`              |
| `__iter__`            | `implements Iterable<T>`         |
| `__str__`             | `@Override toString()`           |
| List comprehension    | Stream API o bucles              |

## ✅ Conceptos Java Aplicados

- ✅ Sobrecarga de constructores
- ✅ Factory methods (static)
- ✅ Métodos estáticos
- ✅ Genéricos (List<Cancion>)
- ✅ Interfaz Iterable
- ✅ Iterator pattern
- ✅ Stream API
- ✅ LocalDate para fechas
- ✅ Comparator para ordenamiento
- ✅ Collections.shuffle()
- ✅ @Override
- ✅ StringBuilder

## 🚀 Diferencias Clave

1. **Factory Methods**: En Java se usan métodos `static` en lugar de `@classmethod`
2. **Propiedades**: Java usa getters/setters explícitos
3. **Iteración**: Implementar `Iterable<T>` para foreach
4. **Operadores**: No hay sobrecarga, se crean métodos explícitos
5. **Colecciones**: `ArrayList<>` con tipo genérico
6. **Streams**: API funcional para operaciones en colecciones
