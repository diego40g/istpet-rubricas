# A3. Sistema de Empleados con Seguridad - Encapsulamiento y Propiedades (JAVA)

## 📋 Descripción

**Versión en Java** de la actividad A3 que demuestra encapsulamiento, getters/setters, validaciones y protección de datos.

## 💻 Solución en Java

### Clase HistorialSalarial

```java
// HistorialSalarial.java
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

public class HistorialSalarial {
    private double salarioAnterior;
    private double salarioNuevo;
    private LocalDateTime fecha;
    private String razon;

    public HistorialSalarial(double salarioAnterior, double salarioNuevo, String razon) {
        this.salarioAnterior = salarioAnterior;
        this.salarioNuevo = salarioNuevo;
        this.fecha = LocalDateTime.now();
        this.razon = razon;
    }

    public double getPorcentajeCambio() {
        return ((salarioNuevo - salarioAnterior) / salarioAnterior) * 100;
    }

    @Override
    public String toString() {
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd/MM/yyyy HH:mm");
        return String.format("[%s] $%.2f → $%.2f (%.1f%%) - %s",
            fecha.format(formatter), salarioAnterior, salarioNuevo,
            getPorcentajeCambio(), razon);
    }

    // Getters
    public double getSalarioAnterior() { return salarioAnterior; }
    public double getSalarioNuevo() { return salarioNuevo; }
    public LocalDateTime getFecha() { return fecha; }
    public String getRazon() { return razon; }
}
```

### Clase Empleado

```java
// Empleado.java
import java.util.ArrayList;
import java.util.List;
import java.util.regex.Pattern;

/**
 * Clase que representa un empleado con encapsulamiento completo
 */
public class Empleado {
    // ═══════════════════════════════════════════════════════════
    // ATRIBUTOS PRIVADOS (Encapsulamiento)
    // ═══════════════════════════════════════════════════════════

    private String nombre;
    private String apellido;
    private String rfc;           // Protegido
    private String curp;          // Protegido
    private String numeroSeguroSocial;  // Muy protegido
    private String puesto;
    private String departamento;
    private double salario;       // Con validaciones
    private int edad;             // Con validaciones
    private String email;         // Con validaciones
    private String telefono;      // Con validaciones

    // Historial de cambios salariales
    private List<HistorialSalarial> historialSalarial;

    // Constantes
    private static final double SALARIO_MINIMO = 7468.00; // 2024
    private static final double SALARIO_MAXIMO = 1000000.00;
    private static final int EDAD_MINIMA = 18;
    private static final int EDAD_MAXIMA = 70;

    // ═══════════════════════════════════════════════════════════
    // CONSTRUCTOR
    // ═══════════════════════════════════════════════════════════

    public Empleado(String nombre, String apellido, String rfc, String curp,
                    String nss, String puesto, String departamento,
                    double salario, int edad, String email, String telefono) {
        this.historialSalarial = new ArrayList<>();

        // Usar setters para validar
        setNombre(nombre);
        setApellido(apellido);
        setRfc(rfc);
        setCurp(curp);
        setNumeroSeguroSocial(nss);
        setPuesto(puesto);
        setDepartamento(departamento);
        setSalario(salario, "Salario inicial");
        setEdad(edad);
        setEmail(email);
        setTelefono(telefono);
    }

    // ═══════════════════════════════════════════════════════════
    // GETTERS Y SETTERS CON VALIDACIONES
    // ═══════════════════════════════════════════════════════════

    // --- NOMBRE ---
    public String getNombre() {
        return nombre;
    }

    public void setNombre(String nombre) {
        if (nombre == null || nombre.trim().isEmpty()) {
            throw new IllegalArgumentException("El nombre no puede estar vacío");
        }
        if (!nombre.matches("[a-zA-ZáéíóúÁÉÍÓÚñÑ ]+")) {
            throw new IllegalArgumentException("El nombre solo puede contener letras");
        }
        this.nombre = nombre.trim();
    }

    // --- APELLIDO ---
    public String getApellido() {
        return apellido;
    }

    public void setApellido(String apellido) {
        if (apellido == null || apellido.trim().isEmpty()) {
            throw new IllegalArgumentException("El apellido no puede estar vacío");
        }
        this.apellido = apellido.trim();
    }

    // --- RFC (Registro Federal de Contribuyentes) ---
    public String getRfc() {
        // Solo mostrar últimos 4 caracteres
        return "****" + rfc.substring(rfc.length() - 4);
    }

    public String getRfcCompleto() {
        return rfc;
    }

    public void setRfc(String rfc) {
        if (rfc == null || rfc.trim().isEmpty()) {
            throw new IllegalArgumentException("El RFC no puede estar vacío");
        }

        // Validar formato: AAAA######AAA
        rfc = rfc.trim().toUpperCase();
        if (!rfc.matches("[A-Z]{4}\\d{6}[A-Z0-9]{3}")) {
            throw new IllegalArgumentException(
                "RFC inválido. Formato: 4 letras + 6 dígitos + 3 caracteres");
        }

        this.rfc = rfc;
    }

    // --- CURP ---
    public String getCurp() {
        return "****" + curp.substring(curp.length() - 4);
    }

    public String getCurpCompleto() {
        return curp;
    }

    public void setCurp(String curp) {
        if (curp == null || curp.trim().isEmpty()) {
            throw new IllegalArgumentException("El CURP no puede estar vacío");
        }

        curp = curp.trim().toUpperCase();
        if (curp.length() != 18) {
            throw new IllegalArgumentException("El CURP debe tener 18 caracteres");
        }

        this.curp = curp;
    }

    // --- NÚMERO DE SEGURO SOCIAL (Muy protegido) ---
    public String getNumeroSeguroSocial() {
        // Máxima protección: solo mostrar últimos 2 dígitos
        return "**-**-****" + nss.substring(nss.length() - 2);
    }

    private String nss; // Atributo renombrado para claridad

    public void setNumeroSeguroSocial(String nss) {
        if (nss == null || nss.trim().isEmpty()) {
            throw new IllegalArgumentException("El NSS no puede estar vacío");
        }

        nss = nss.replaceAll("[^0-9]", ""); // Solo números
        if (nss.length() != 11) {
            throw new IllegalArgumentException("El NSS debe tener 11 dígitos");
        }

        this.nss = nss;
    }

    // --- SALARIO (con historial) ---
    public double getSalario() {
        return salario;
    }

    public void setSalario(double nuevoSalario, String razon) {
        if (nuevoSalario < SALARIO_MINIMO) {
            throw new IllegalArgumentException(
                String.format("Salario no puede ser menor a $%.2f", SALARIO_MINIMO));
        }

        if (nuevoSalario > SALARIO_MAXIMO) {
            throw new IllegalArgumentException(
                String.format("Salario no puede ser mayor a $%.2f", SALARIO_MAXIMO));
        }

        // Registrar en historial si no es el primer salario
        if (this.salario > 0 && nuevoSalario != this.salario) {
            HistorialSalarial cambio = new HistorialSalarial(
                this.salario, nuevoSalario, razon);
            historialSalarial.add(cambio);
        }

        this.salario = nuevoSalario;
    }

    // --- EDAD ---
    public int getEdad() {
        return edad;
    }

    public void setEdad(int edad) {
        if (edad < EDAD_MINIMA || edad > EDAD_MAXIMA) {
            throw new IllegalArgumentException(
                String.format("Edad debe estar entre %d y %d años",
                    EDAD_MINIMA, EDAD_MAXIMA));
        }
        this.edad = edad;
    }

    // --- EMAIL ---
    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        if (email == null || email.trim().isEmpty()) {
            throw new IllegalArgumentException("El email no puede estar vacío");
        }

        // Validación básica de email
        String emailRegex = "^[A-Za-z0-9+_.-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}$";
        if (!email.matches(emailRegex)) {
            throw new IllegalArgumentException("Email inválido");
        }

        this.email = email.trim().toLowerCase();
    }

    // --- TELÉFONO ---
    public String getTelefono() {
        return telefono;
    }

    public void setTelefono(String telefono) {
        if (telefono == null || telefono.trim().isEmpty()) {
            throw new IllegalArgumentException("El teléfono no puede estar vacío");
        }

        telefono = telefono.replaceAll("[^0-9]", "");
        if (telefono.length() != 10) {
            throw new IllegalArgumentException("El teléfono debe tener 10 dígitos");
        }

        this.telefono = telefono;
    }

    // Getters simples (sin validación en set)
    public String getPuesto() { return puesto; }
    public void setPuesto(String puesto) { this.puesto = puesto; }

    public String getDepartamento() { return departamento; }
    public void setDepartamento(String departamento) {
        this.departamento = departamento;
    }

    // ═══════════════════════════════════════════════════════════
    // MÉTODOS DE NEGOCIO
    // ═══════════════════════════════════════════════════════════

    public void aumentarSalario(double porcentaje) {
        if (porcentaje <= 0 || porcentaje > 100) {
            throw new IllegalArgumentException(
                "Porcentaje debe estar entre 0 y 100");
        }

        double nuevoSalario = salario * (1 + porcentaje / 100.0);
        setSalario(nuevoSalario, String.format("Aumento del %.1f%%", porcentaje));

        System.out.printf("✓ Aumento del %.1f%% aplicado. Nuevo salario: $%.2f%n",
                         porcentaje, nuevoSalario);
    }

    public void ajustarSalario(double nuevoSalario, String razon) {
        setSalario(nuevoSalario, razon);
        System.out.printf("✓ Salario ajustado a $%.2f - %s%n", nuevoSalario, razon);
    }

    public List<HistorialSalarial> getHistorialSalarial() {
        return new ArrayList<>(historialSalarial); // Retornar copia
    }

    public void mostrarHistorialSalarial() {
        if (historialSalarial.isEmpty()) {
            System.out.println("No hay historial de cambios salariales");
            return;
        }

        System.out.println("\n📊 Historial Salarial:");
        System.out.println("─".repeat(70));
        for (HistorialSalarial cambio : historialSalarial) {
            System.out.println(cambio);
        }
        System.out.println("─".repeat(70));
    }

    public String getNombreCompleto() {
        return nombre + " " + apellido;
    }

    public String getTelefonoFormateado() {
        return String.format("(%s) %s-%s",
            telefono.substring(0, 3),
            telefono.substring(3, 6),
            telefono.substring(6));
    }

    @Override
    public String toString() {
        return String.format("%s - %s (%s) - $%.2f",
            getNombreCompleto(), puesto, departamento, salario);
    }

    public String mostrarInformacionCompleta() {
        StringBuilder info = new StringBuilder();
        info.append("\n╔══════════════════════════════════════════════════════════════╗\n");
        info.append("║                  INFORMACIÓN DEL EMPLEADO                    ║\n");
        info.append("╚══════════════════════════════════════════════════════════════╝\n");
        info.append(String.format("  Nombre:          %s%n", getNombreCompleto()));
        info.append(String.format("  RFC:             %s%n", getRfc()));
        info.append(String.format("  CURP:            %s%n", getCurp()));
        info.append(String.format("  NSS:             %s%n", getNumeroSeguroSocial()));
        info.append(String.format("  Edad:            %d años%n", edad));
        info.append(String.format("  Puesto:          %s%n", puesto));
        info.append(String.format("  Departamento:    %s%n", departamento));
        info.append(String.format("  Salario:         $%.2f%n", salario));
        info.append(String.format("  Email:           %s%n", email));
        info.append(String.format("  Teléfono:        %s%n", getTelefonoFormateado()));
        info.append("═══════════════════════════════════════════════════════════════\n");

        return info.toString();
    }
}
```

### Clase Main

```java
// SistemaEmpleados.java

public class SistemaEmpleados {

    public static void main(String[] args) {
        System.out.println("╔═══════════════════════════════════════════════════════════════╗");
        System.out.println("║       SISTEMA DE EMPLEADOS - ENCAPSULAMIENTO Y SEGURIDAD     ║");
        System.out.println("╚═══════════════════════════════════════════════════════════════╝\n");

        // ─────────────────────────────────────────────────────────────
        // 1. CREAR EMPLEADO CON VALIDACIONES
        // ─────────────────────────────────────────────────────────────
        System.out.println("1. CREANDO EMPLEADO");
        System.out.println("─".repeat(63));

        try {
            Empleado emp1 = new Empleado(
                "Juan Carlos",
                "Pérez García",
                "PEGJ850301ABC",
                "PEGJ850301HDFRNN09",
                "12-34-56789-01",
                "Desarrollador Senior",
                "Tecnología",
                25000.00,
                38,
                "juan.perez@empresa.com",
                "5551234567"
            );

            System.out.println("✓ Empleado creado exitosamente");
            System.out.print(emp1.mostrarInformacionCompleta());

            // ─────────────────────────────────────────────────────────────
            // 2. DEMOSTRAR ENCAPSULAMIENTO
            // ─────────────────────────────────────────────────────────────
            System.out.println("\n2. ENCAPSULAMIENTO DE DATOS SENSIBLES");
            System.out.println("─".repeat(63));

            System.out.println("RFC mostrado:  " + emp1.getRfc());
            System.out.println("CURP mostrado: " + emp1.getCurp());
            System.out.println("NSS mostrado:  " + emp1.getNumeroSeguroSocial());

            // ─────────────────────────────────────────────────────────────
            // 3. MODIFICAR SALARIO
            // ─────────────────────────────────────────────────────────────
            System.out.println("\n\n3. GESTIÓN SALARIAL");
            System.out.println("─".repeat(63));

            System.out.printf("Salario actual: $%.2f%n", emp1.getSalario());

            emp1.aumentarSalario(10);
            emp1.aumentarSalario(5);
            emp1.ajustarSalario(30000, "Promoción a líder de equipo");

            emp1.mostrarHistorialSalarial();

            // ─────────────────────────────────────────────────────────────
            // 4. VALIDACIONES
            // ─────────────────────────────────────────────────────────────
            System.out.println("\n\n4. PRUEBAS DE VALIDACIÓN");
            System.out.println("─".repeat(63));

            // Intentar valores inválidos
            try {
                emp1.setEdad(15); // Muy joven
            } catch (IllegalArgumentException e) {
                System.out.println("❌ Error esperado: " + e.getMessage());
            }

            try {
                emp1.setEmail("correo_invalido"); // Email mal formado
            } catch (IllegalArgumentException e) {
                System.out.println("❌ Error esperado: " + e.getMessage());
            }

            try {
                emp1.setSalario(5000, "Reducción"); // Bajo el mínimo
            } catch (IllegalArgumentException e) {
                System.out.println("❌ Error esperado: " + e.getMessage());
            }

            System.out.println("\n✓ Todas las validaciones funcionando correctamente");

        } catch (IllegalArgumentException e) {
            System.out.println("❌ Error: " + e.getMessage());
        }

        System.out.println("\n╚═══════════════════════════════════════════════════════════════╝");
    }
}
```

## 🔧 Compilación y Ejecución

```bash
javac HistorialSalarial.java Empleado.java SistemaEmpleados.java
java SistemaEmpleados
```

## 📊 Conceptos Java vs Python

| Python                 | Java                       |
| ---------------------- | -------------------------- |
| `@property`            | Getter method              |
| `@atributo.setter`     | Setter method              |
| `__atributo` (privado) | `private` modifier         |
| Validación en setter   | Validación en setter       |
| Exception simple       | `IllegalArgumentException` |
| f-strings              | `String.format()`          |

## ✅ Conceptos Aplicados

- ✅ Encapsulamiento con `private`
- ✅ Getters y Setters
- ✅ Validaciones exhaustivas
- ✅ Excepciones personalizadas
- ✅ Regex para validaciones
- ✅ Inmutabilidad de colecciones
- ✅ StringBuilder para formateo
- ✅ Protección de datos sensibles
