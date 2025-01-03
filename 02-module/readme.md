# Módulo 2: Estructuras de Datos y POO

## Introducción

En este módulo profundizaremos en las estructuras de datos de Python y en la Programación Orientada a Objetos (POO). Aplicaremos estos conceptos para expandir nuestro Sistema de Gestión de Biblioteca Personal, añadiendo funcionalidades más complejas y una mejor organización del código.

## Contenido

### 1. Estructuras de Datos

#### 1.1 Listas
```python
# Creación de listas
libros = ["Don Quijote", "Cien años de soledad", "Rayuela"]
autores = list(("Cervantes", "García Márquez", "Cortázar"))

# Operaciones comunes
libros.append("El Aleph")              # Añadir al final
libros.insert(0, "Pedro Páramo")       # Añadir en posición
libro_eliminado = libros.pop()         # Eliminar y retornar último elemento
libros.remove("Rayuela")               # Eliminar por valor

# Slicing
primeros_dos = libros[0:2]            # Obtener subconjunto
reverso = libros[::-1]                # Invertir lista

# Métodos útiles
libros.sort()                         # Ordenar lista
libros.reverse()                      # Invertir orden
total_libros = len(libros)           # Obtener longitud
```

#### 1.2 Tuplas
```python
# Tuplas - inmutables
coordenadas = (40.7128, -74.0060)
libro_info = ("Don Quijote", "Cervantes", 1605)

# Desempaquetado
titulo, autor, año = libro_info

# Métodos de tuplas
posicion = libro_info.index("Cervantes")  # Encontrar índice
conteo = libro_info.count("Don Quijote")  # Contar ocurrencias
```

#### 1.3 Diccionarios
```python
# Creación de diccionarios
libro = {
    "titulo": "Don Quijote",
    "autor": "Cervantes",
    "año": 1605,
    "disponible": True
}

# Operaciones comunes
libro["editorial"] = "Penguin"        # Añadir/modificar valor
del libro["año"]                      # Eliminar par clave-valor
año = libro.get("año", "Desconocido") # Obtener valor con valor por defecto

# Métodos útiles
claves = libro.keys()                 # Obtener claves
valores = libro.values()              # Obtener valores
items = libro.items()                 # Obtener pares clave-valor

# Diccionario por comprensión
libros_disponibles = {k: v for k, v in libro.items() if v == True}
```

#### 1.4 Conjuntos (Sets)
```python
# Creación de conjuntos
generos = {"Ficción", "Aventura", "Romance"}
mas_generos = set(["Misterio", "Ficción", "Terror"])

# Operaciones de conjuntos
todos_generos = generos.union(mas_generos)
generos_comunes = generos.intersection(mas_generos)
generos_unicos = generos.difference(mas_generos)

# Métodos de conjuntos
generos.add("Fantasía")               # Añadir elemento
generos.remove("Romance")             # Eliminar elemento
generos.discard("No existe")          # Eliminar si existe
```

#### 1.5 Comprensión de Listas
```python
# Comprensión de listas básica
numeros = [1, 2, 3, 4, 5]
cuadrados = [n**2 for n in numeros]

# Comprensión con condición
pares = [n for n in numeros if n % 2 == 0]

# Comprensión de listas anidada
matriz = [[i*j for j in range(3)] for i in range(3)]

# Ejemplo práctico con libros
titulos = [libro["titulo"].upper() for libro in biblioteca 
           if libro["disponible"]]
```

### 2. Programación Orientada a Objetos

#### 2.1 Clases y Objetos
```python
class Libro:
    # Atributo de clase
    tipo_contenido = "texto"
    
    def __init__(self, titulo, autor, isbn):
        # Atributos de instancia
        self.titulo = titulo
        self.autor = autor
        self.isbn = isbn
        self.disponible = True
        self.prestamos = []
    
    def __str__(self):
        return f"{self.titulo} por {self.autor}"
    
    def prestar(self, usuario):
        if self.disponible:
            self.disponible = False
            self.prestamos.append({
                "usuario": usuario,
                "fecha": datetime.now()
            })
            return True
        return False
    
    @property
    def tiempo_prestado(self):
        if not self.disponible and self.prestamos:
            ultimo_prestamo = self.prestamos[-1]["fecha"]
            return datetime.now() - ultimo_prestamo
        return None
```

#### 2.2 Herencia
```python
class RecursoDigital(Libro):
    def __init__(self, titulo, autor, isbn, formato, tamaño):
        super().__init__(titulo, autor, isbn)
        self.formato = formato
        self.tamaño = tamaño
        
    def descargar(self):
        if self.disponible:
            return f"Descargando {self.titulo} en formato {self.formato}"
        return "Recurso no disponible"

class AudioLibro(RecursoDigital):
    def __init__(self, titulo, autor, isbn, formato, tamaño, duracion):
        super().__init__(titulo, autor, isbn, formato, tamaño)
        self.duracion = duracion
```

#### 2.3 Polimorfismo
```python
def procesar_recurso(recurso):
    print(f"Procesando: {recurso}")
    if isinstance(recurso, AudioLibro):
        print(f"Duración: {recurso.duracion}")
    elif isinstance(recurso, RecursoDigital):
        print(f"Formato: {recurso.formato}")
    else:
        print("Recurso físico estándar")
```

#### 2.4 Encapsulamiento
```python
class Biblioteca:
    def __init__(self):
        self._libros = {}  # Atributo protegido
        self.__usuarios = set()  # Atributo privado
    
    def agregar_libro(self, libro):
        if not isinstance(libro, Libro):
            raise TypeError("Solo se pueden agregar instancias de Libro")
        self._libros[libro.isbn] = libro
    
    def _validar_usuario(self, usuario):  # Método protegido
        return usuario in self.__usuarios
    
    def prestar_libro(self, isbn, usuario):
        if not self._validar_usuario(usuario):
            return "Usuario no registrado"
        libro = self._libros.get(isbn)
        if libro and libro.prestar(usuario):
            return "Préstamo exitoso"
        return "Libro no disponible"
```

### 3. Manejo de Excepciones

```python
class BibliotecaError(Exception):
    """Clase base para excepciones de la biblioteca"""
    pass

class LibroNoEncontrado(BibliotecaError):
    """Se lanza cuando no se encuentra un libro"""
    pass

class PrestamoError(BibliotecaError):
    """Se lanza cuando hay un error en el préstamo"""
    pass

def prestar_libro(self, isbn, usuario):
    try:
        if not self._validar_usuario(usuario):
            raise PrestamoError("Usuario no registrado")
        
        libro = self._libros.get(isbn)
        if not libro:
            raise LibroNoEncontrado(f"No se encontró el libro con ISBN: {isbn}")
            
        if not libro.prestar(usuario):
            raise PrestamoError("El libro no está disponible")
            
        return "Préstamo exitoso"
        
    except BibliotecaError as e:
        return f"Error: {str(e)}"
    except Exception as e:
        return f"Error inesperado: {str(e)}"
```

## Ejemplo Práctico: Sistema de Biblioteca Mejorado

```python
from datetime import datetime, timedelta
from typing import List, Dict, Optional

class Usuario:
    def __init__(self, nombre: str, id: str):
        self.nombre = nombre
        self.id = id
        self.prestamos_activos: List[Libro] = []

class SistemaBiblioteca:
    def __init__(self):
        self.libros: Dict[str, Libro] = {}
        self.usuarios: Dict[str, Usuario] = {}
    
    def registrar_usuario(self, usuario: Usuario) -> bool:
        if usuario.id not in self.usuarios:
            self.usuarios[usuario.id] = usuario
            return True
        return False
    
    def buscar_libros(self, criterio: str) -> List[Libro]:
        return [libro for libro in self.libros.values()
                if criterio.lower() in libro.titulo.lower() or 
                   criterio.lower() in libro.autor.lower()]
    
    def obtener_estadisticas(self) -> Dict:
        total_libros = len(self.libros)
        libros_prestados = len([l for l in self.libros.values() 
                              if not l.disponible])
        return {
            "total_libros": total_libros,
            "prestados": libros_prestados,
            "disponibles": total_libros - libros_prestados
        }

# Uso del sistema
if __name__ == "__main__":
    biblioteca = SistemaBiblioteca()
    
    # Registrar usuarios
    usuario1 = Usuario("Ana García", "U001")
    biblioteca.registrar_usuario(usuario1)
    
    # Agregar libros
    libro1 = Libro("Don Quijote", "Cervantes", "ISBN001")
    libro2 = AudioLibro("1984", "Orwell", "ISBN002", "MP3", "500MB", "10:30:00")
    
    biblioteca.libros[libro1.isbn] = libro1
    biblioteca.libros[libro2.isbn] = libro2
    
    # Realizar búsqueda
    resultados = biblioteca.buscar_libros("quijote")
    for libro in resultados:
        print(libro)
    
    # Obtener estadísticas
    stats = biblioteca.obtener_estadisticas()
    print(f"Estadísticas de la biblioteca: {stats}")
```

## Recursos Adicionales

- [Python Documentation - Data Structures](https://docs.python.org/3/tutorial/datastructures.html)
- [Python Documentation - Classes](https://docs.python.org/3/tutorial/classes.html)
- [Real Python - OOP in Python](https://realpython.com/python3-object-oriented-programming/)
- [Python Type Hints](https://docs.python.org/3/library/typing.html)

## Siguiente Módulo

En el siguiente módulo aprenderemos sobre manipulación de datos y archivos, donde implementaremos la persistencia de datos para nuestro sistema de biblioteca.
