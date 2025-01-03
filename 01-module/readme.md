# Módulo 1: Fundamentos de Python y Configuración

## Introducción

Bienvenido al primer módulo del Curso Práctico de Python. En esta sección aprenderemos los fundamentos esenciales de Python y configuraremos nuestro entorno de desarrollo. Todos los conceptos serán aplicados en la construcción inicial de nuestro primer proyecto: Sistema de Gestión de Biblioteca Personal.

## Contenido

### 1. Configuración del Entorno de Desarrollo

#### 1.1 Instalación de Python
1. Descarga Python desde [python.org](https://python.org)
2. Verifica la instalación:
```bash
python --version
# o
python3 --version
```

#### 1.2 Instalación y Configuración de VS Code
1. Descarga VS Code desde [code.visualstudio.com](https://code.visualstudio.com)
2. Extensiones recomendadas:
   - Python (Microsoft)
   - Python Extension Pack
   - GitLens

#### 1.3 Entorno Virtual
```bash
# Crear un entorno virtual
python -m venv env

# Activar el entorno virtual
# En Windows:
env\Scripts\activate
# En macOS/Linux:
source env/bin/activate

# Instalar dependencias iniciales
pip install ipython
```

### 2. Git y Control de Versiones

#### 2.1 Configuración Inicial
```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tu@email.com"
```

#### 2.2 Comandos Básicos de Git
```bash
# Inicializar repositorio
git init

# Verificar estado
git status

# Añadir cambios
git add .

# Crear commit
git commit -m "Inicio del proyecto biblioteca"

# Subir cambios
git push origin main
```

### 3. Variables y Tipos de Datos

#### 3.1 Variables
En Python, las variables se crean al asignarles un valor:
```python
# Variables básicas
nombre = "Ana"
edad = 25
altura = 1.65
es_estudiante = True

# Imprimiendo variables
print(f"Nombre: {nombre}, Edad: {edad}")

# Verificar tipo de variable
print(type(nombre))  # <class 'str'>
print(type(edad))    # <class 'int'>
```

#### 3.2 Tipos de Datos Básicos
```python
# Strings (cadenas de texto)
nombre = "Python"
descripcion = '''Este es un
texto en múltiples líneas'''

# Números
entero = 42
decimal = 3.14
complejo = 3 + 4j

# Booleanos
verdadero = True
falso = False

# None (null)
variable_nula = None
```

### 4. Operadores

#### 4.1 Operadores Aritméticos
```python
a = 10
b = 3

suma = a + b          # 13
resta = a - b         # 7
multiplicacion = a * b # 30
division = a / b      # 3.333...
division_entera = a // b  # 3
modulo = a % b        # 1
potencia = a ** b     # 1000
```

#### 4.2 Operadores de Comparación
```python
x = 5
y = 10

igual = x == y        # False
diferente = x != y    # True
mayor = x > y         # False
menor = x < y         # True
mayor_igual = x >= y  # False
menor_igual = x <= y  # True
```

### 5. Estructuras de Control

#### 5.1 Condicionales
```python
edad = 18

if edad >= 18:
    print("Eres mayor de edad")
elif edad >= 13:
    print("Eres adolescente")
else:
    print("Eres menor de edad")

# Operador ternario
mensaje = "Adulto" if edad >= 18 else "Menor"
```

#### 5.2 Bucles
```python
# Bucle for
for i in range(5):
    print(i)  # Imprime 0, 1, 2, 3, 4

# Bucle while
contador = 0
while contador < 5:
    print(contador)
    contador += 1
```

### 6. Funciones

#### 6.1 Definición de Funciones
```python
def saludar(nombre):
    """
    Esta función saluda a una persona
    Args:
        nombre (str): Nombre de la persona
    Returns:
        str: Mensaje de saludo
    """
    return f"¡Hola, {nombre}!"

# Uso de la función
mensaje = saludar("María")
print(mensaje)  # ¡Hola, María!
```

#### 6.2 Funciones con Parámetros por Defecto
```python
def crear_perfil(nombre, edad=18, ciudad="Desconocida"):
    return {
        "nombre": nombre,
        "edad": edad,
        "ciudad": ciudad
    }

# Diferentes formas de llamar la función
perfil1 = crear_perfil("Juan")
perfil2 = crear_perfil("Ana", 25)
perfil3 = crear_perfil("Carlos", ciudad="Madrid")
```

### 7. Módulos

#### 7.1 Importación de Módulos
```python
# Importar módulo completo
import math

# Usar funciones del módulo
raiz = math.sqrt(16)  # 4.0
pi = math.pi          # 3.141592...

# Importar funciones específicas
from datetime import datetime, timedelta

# Obtener fecha actual
fecha_actual = datetime.now()
```

#### 7.2 Crear y Usar Módulos Propios
```python
# archivo: biblioteca_utils.py
def validar_isbn(isbn):
    return len(isbn) == 13 and isbn.isdigit()

def formatear_titulo(titulo):
    return titulo.title()

# archivo: main.py
from biblioteca_utils import validar_isbn, formatear_titulo

isbn = "1234567890123"
titulo = "el quijote"

if validar_isbn(isbn):
    print(formatear_titulo(titulo))  # El Quijote
```

## Ejercicio Práctico: Iniciando el Sistema de Biblioteca

Aplicaremos los conceptos aprendidos creando la estructura básica de nuestro sistema de biblioteca:

```python
class Libro:
    def __init__(self, titulo, autor, isbn):
        self.titulo = titulo
        self.autor = autor
        self.isbn = isbn
        self.disponible = True

    def __str__(self):
        estado = "disponible" if self.disponible else "prestado"
        return f"{self.titulo} por {self.autor} ({estado})"

class Biblioteca:
    def __init__(self):
        self.libros = []

    def agregar_libro(self, libro):
        self.libros.append(libro)
        print(f"Libro '{libro.titulo}' agregado con éxito")

    def listar_libros(self):
        for libro in self.libros:
            print(libro)

# Ejemplo de uso
if __name__ == "__main__":
    # Crear biblioteca
    mi_biblioteca = Biblioteca()

    # Agregar algunos libros
    libro1 = Libro("Don Quijote", "Miguel de Cervantes", "1234567890123")
    libro2 = Libro("Cien años de soledad", "Gabriel García Márquez", "9876543210987")

    mi_biblioteca.agregar_libro(libro1)
    mi_biblioteca.agregar_libro(libro2)

    # Listar libros
    print("\nLibros en la biblioteca:")
    mi_biblioteca.listar_libros()
```

## Recursos Adicionales

- [Documentación oficial de Python](https://docs.python.org/3/)
- [Tutorial de Git](https://git-scm.com/docs)
- [VS Code y Python](https://code.visualstudio.com/docs/python/python-tutorial)
- [Python PEP 8 -- Guía de Estilo](https://www.python.org/dev/peps/pep-0008/)

## Siguiente Módulo

En el siguiente módulo profundizaremos en estructuras de datos y programación orientada a objetos, expandiendo las funcionalidades de nuestro sistema de biblioteca.
