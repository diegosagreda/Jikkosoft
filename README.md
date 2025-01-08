# Jikkosoft
Prueba Tecnica Dev Mid

## 1. Esquema de Base de Datos para una Plataforma de Blogs

### Descripción General
Este esquema de base de datos está diseñado para una plataforma de blogs sencilla que soporta usuarios, publicaciones de blog, comentarios y etiquetas.

### Tablas Principales

#### 1. Tabla `users` (usuarios)

- **id**: Clave primaria
- **username**: Nombre de usuario del sistema.
- **email**: Correo electrónico del usuario
- **password**: Contraseña del usuario
- **created_at**: Marca de tiempo

```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### 2. Tabla `posts` (publicaciones de blog)

- **id**: Clave primaria 
- **user_id**: Llave foránea referencia al usuario que creó la publicación.
- **title**: Título de la publicación.
- **body**: Contenido o cuerpo de la publicación.
- **created_at**: Fecha y hora en que la publicación fue creada.

```sql
CREATE TABLE posts (
    id SERIAL PRIMARY KEY,
    user_id INT,
    title VARCHAR(255) NOT NULL,
    body TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```

#### 3. Tabla `comments` (comentarios)

- **id**: Clave primaria
- **post_id**: Llave foránea que referencia la publicación a la que pertenece el comentario.
- **user_id**: Llave foránea que referencia al usuario que realizó el comentario.
- **body**: Contenido del comentario.
- **created_at**: Fecha y hora en que el comentario fue creado.

```sql
CREATE TABLE comments (
    id SERIAL PRIMARY KEY,
    post_id INT,
    user_id INT,
    body TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (post_id) REFERENCES posts(id) ON DELETE CASCADE,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```

#### 4. Tabla `tags` (etiquetas)

- **id**: Clave primaria 
- **name**: Nombre de la etiqueta

```sql
CREATE TABLE tags (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50) NOT NULL UNIQUE
);
```

#### 5. Tabla `post_tags` (relación muchos a muchos entre publicaciones y etiquetas)
La tabla `post_tags` es una tabla intermedia que implementa la relación muchos a muchos entre las publicaciones y las etiquetas. Esto permite que una publicación pueda tener múltiples etiquetas, y que una etiqueta pueda ser asignada a varias publicaciones.

- **post_id**: Llave foránea que referencia a una publicación.
- **tag_id**: Llave foránea que referencia a una etiqueta.

```sql
CREATE TABLE post_tags (
    post_id INT,
    tag_id INT,
    PRIMARY KEY (post_id, tag_id),
    FOREIGN KEY (post_id) REFERENCES posts(id) ON DELETE CASCADE,
    FOREIGN KEY (tag_id) REFERENCES tags(id) ON DELETE CASCADE
);
```

### Relaciones entre las Tablas

1. **Relación usuarios-publicaciones**:
   - Un **usuario** puede crear múltiples **publicaciones**.
   - Relación: **uno a muchos** (`users.id -> posts.user_id`).

2. **Relación publicaciones-comentarios**:
   - Una **publicación** puede tener múltiples **comentarios**.
   - Relación: **uno a muchos** (`posts.id -> comments.post_id`).

3. **Relación usuarios-comentarios**:
   - Un **usuario** puede dejar múltiples **comentarios**.
   - Relación: **uno a muchos** (`users.id -> comments.user_id`).

4. **Relación publicaciones-etiquetas**:
   - Una **publicación** puede tener múltiples **etiquetas**, y una **etiqueta** puede estar asignada a múltiples **publicaciones**.
   - Relación: **muchos a muchos** (`posts.id <-> tags.id`, a través de `post_tags`).

### Consideraciones de Integridad Referencial
- Las llaves foráneas están configuradas con la política `ON DELETE CASCADE`, lo que significa que si se elimina un usuario o una publicación, todos los comentarios y publicaciones relacionadas serán eliminados automáticamente, garantizando la integridad referencial de la base de datos.




## 2. Función para encontrar los índices de dos números que sumen el valor objetivo

### Descripción General

Esta sección describe una función escrita en Java que toma una lista de enteros y un entero de destino como parámetros, y devuelve los índices de los dos números en la lista que sumados dan como resultado el entero de destino.

### Implementación

```java
import java.util.HashMap;
import java.util.Map;

public class TwoSumSolution {

    public static int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> numMap = new HashMap<>();
        
        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i];
            if (numMap.containsKey(complement)) {
                return new int[] { numMap.get(complement), i };
            }
            numMap.put(nums[i], i);
        }
        
        throw new IllegalArgumentException("No solution found");
    }

    public static void main(String[] args) {
        int[] nums = {2, 7, 11, 15};
        int target = 9;
        int[] result = twoSum(nums, target);
        System.out.println("Indices: [" + result[0] + ", " + result[1] + "]");
    }
}
```

### Explicación

1. La función `twoSum` utiliza un `HashMap` para almacenar los números de la lista junto con sus índices.
2. Para cada número en el array, calcula el complemento que se necesita para alcanzar el objetivo (es decir, `target - nums[i]`).
3. Si encuentra el complemento en el mapa, devuelve los índices del número actual y su complemento.
4. Si no se encuentra ninguna solución, lanza una excepción.

Este método tiene una complejidad de tiempo de O(n), ya que recorre el array una vez y realiza búsquedas en el mapa en tiempo constante.


## 3. Sistema de gestión de bibliotecas en Java

### Descripción General

Esta sección implementa un sistema de gestión de bibliotecas simple en Java, con clases para libros, miembros y la biblioteca misma. Los miembros pueden prestar y devolver libros, y la biblioteca mantiene una lista de libros disponibles y miembros registrados.

### Implementación

```java
import java.util.ArrayList;
import java.util.List;

// Clase Libro
class Libro {
    private String titulo;
    private String autor;
    private String ISBN;
    
    public Libro(String titulo, String autor, String ISBN) {
        this.titulo = titulo;
        this.autor = autor;
        this.ISBN = ISBN;
    }

    public String getTitulo() {
        return titulo;
    }

    public String getAutor() {
        return autor;
    }

    public String getISBN() {
        return ISBN;
    }

    @Override
    public String toString() {
        return "Libro: " + titulo + " por " + autor + " (ISBN: " + ISBN + ")";
    }
}

// Clase Miembro
class Miembro {
    private String nombre;
    private String ID;
    private List<Libro> librosPrestados;

    public Miembro(String nombre, String ID) {
        this.nombre = nombre;
        this.ID = ID;
        this.librosPrestados = new ArrayList<>();
    }

    public String getNombre() {
        return nombre;
    }

    public String getID() {
        return ID;
    }

    public void prestarLibro(Libro libro) {
        librosPrestados.add(libro);
    }

    public void devolverLibro(Libro libro) {
        librosPrestados.remove(libro);
    }

    public List<Libro> getLibrosPrestados() {
        return librosPrestados;
    }

    @Override
    public String toString() {
        return "Miembro: " + nombre + " (ID: " + ID + ")";
    }
}

// Clase Biblioteca
class Biblioteca {
    private List<Libro> libros;
    private List<Miembro> miembros;

    public Biblioteca() {
        this.libros = new ArrayList<>();
        this.miembros = new ArrayList<>();
    }

    public void agregarLibro(Libro libro) {
        libros.add(libro);
    }

    public void registrarMiembro(Miembro miembro) {
        miembros.add(miembro);
    }

    public void prestarLibro(Miembro miembro, Libro libro) {
        if (libros.contains(libro)) {
            miembro.prestarLibro(libro);
            libros.remove(libro);
            System.out.println("Libro prestado: " + libro.getTitulo() + " a " + miembro.getNombre());
        } else {
            System.out.println("El libro no está disponible.");
        }
    }

    public void devolverLibro(Miembro miembro, Libro libro) {
        miembro.devolverLibro(libro);
        libros.add(libro);
        System.out.println("Libro devuelto: " + libro.getTitulo() + " por " + miembro.getNombre());
    }

    public List<Libro> getLibrosDisponibles() {
        return libros;
    }

    public List<Miembro> getMiembros() {
        return miembros;
    }
    
    public static void main(String[] args) {
        // Creación de la biblioteca
        Biblioteca biblioteca = new Biblioteca();
        
        // Creación de libros
        Libro libro1 = new Libro("Cien Años de Soledad", "Gabriel García Márquez", "123456789");
        Libro libro2 = new Libro("El Principito", "Antoine de Saint-Exupéry", "987654321");
        
        // Agregar libros a la biblioteca
        biblioteca.agregarLibro(libro1);
        biblioteca.agregarLibro(libro2);
        
        // Creación de miembros
        Miembro miembro1 = new Miembro("Diego", "001");
        
        // Registro de miembro
        biblioteca.registrarMiembro(miembro1);
        
        // Prestamo de libro
        biblioteca.prestarLibro(miembro1, libro1);
        
        // Devolución de libro
        biblioteca.devolverLibro(miembro1, libro1);
    }
}
```

### Explicación:

1. **Clase `Libro`**: Representa un libro con atributos como `titulo`, `autor` y `ISBN`.
2. **Clase `Miembro`**: Representa a un miembro de la biblioteca que puede prestar y devolver libros.
3. **Clase `Biblioteca`**: Gestiona los libros y miembros de la biblioteca, permitiendo préstamos y devoluciones de libros.







