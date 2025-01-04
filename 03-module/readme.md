# Módulo 3: Manipulación de Datos y Archivos

## Introducción

En este módulo aprenderemos a trabajar con archivos y diferentes formatos de datos en Python. Implementaremos la persistencia de datos para nuestro Sistema de Gestión de Biblioteca Personal, permitiendo guardar y cargar información de libros, usuarios y préstamos.

## Contenido

### 1. Trabajando con Archivos de Texto

#### 1.1 Lectura y Escritura Básica
```python
# Lectura de archivo
with open('libros.txt', 'r', encoding='utf-8') as archivo:
    contenido = archivo.read()  # Lee todo el archivo
    
# Lectura línea por línea
with open('libros.txt', 'r', encoding='utf-8') as archivo:
    for linea in archivo:
        print(linea.strip())

# Escritura en archivo
with open('nuevo_libro.txt', 'w', encoding='utf-8') as archivo:
    archivo.write("Don Quijote,Cervantes,1605\n")
    archivo.write("Cien años de soledad,García Márquez,1967\n")

# Agregar contenido al final
with open('libros.txt', 'a', encoding='utf-8') as archivo:
    archivo.write("Rayuela,Cortázar,1963\n")
```

#### 1.2 Modos de Apertura y Contexto
```python
# Modos de apertura
'r'  # Lectura (default)
'w'  # Escritura (sobrescribe)
'a'  # Agregar al final
'r+' # Lectura y escritura
'b'  # Modo binario

# Manejo de errores en archivos
try:
    with open('archivo_inexistente.txt', 'r') as archivo:
        contenido = archivo.read()
except FileNotFoundError:
    print("El archivo no existe")
except PermissionError:
    print("No tienes permisos para acceder al archivo")
```

### 2. Trabajando con CSV

#### 2.1 Lectura y Escritura de CSV
```python
import csv

# Lectura de CSV
with open('biblioteca.csv', 'r', encoding='utf-8') as archivo:
    lector_csv = csv.reader(archivo)
    next(lector_csv)  # Saltar encabezados
    for fila in lector_csv:
        titulo, autor, isbn = fila
        print(f"Libro: {titulo} por {autor}")

# Escritura en CSV
libros = [
    ['Título', 'Autor', 'ISBN'],
    ['Don Quijote', 'Cervantes', '123456789'],
    ['Rayuela', 'Cortázar', '987654321']
]

with open('nuevos_libros.csv', 'w', newline='', encoding='utf-8') as archivo:
    escritor_csv = csv.writer(archivo)
    escritor_csv.writerows(libros)
```

#### 2.2 Trabajando con DictReader y DictWriter
```python
# Lectura usando diccionarios
with open('biblioteca.csv', 'r', encoding='utf-8') as archivo:
    lector = csv.DictReader(archivo)
    for fila in lector:
        print(f"Título: {fila['titulo']}, Autor: {fila['autor']}")

# Escritura usando diccionarios
libros = [
    {'titulo': 'Don Quijote', 'autor': 'Cervantes', 'isbn': '123456789'},
    {'titulo': 'Rayuela', 'autor': 'Cortázar', 'isbn': '987654321'}
]

with open('libros_dict.csv', 'w', newline='', encoding='utf-8') as archivo:
    campos = ['titulo', 'autor', 'isbn']
    escritor = csv.DictWriter(archivo, fieldnames=campos)
    escritor.writeheader()
    escritor.writerows(libros)
```

### 3. Trabajando con JSON

#### 3.1 Lectura y Escritura de JSON
```python
import json

# Crear datos para JSON
biblioteca_data = {
    "nombre": "Biblioteca Central",
    "libros": [
        {
            "titulo": "Don Quijote",
            "autor": "Cervantes",
            "isbn": "123456789",
            "disponible": True
        },
        {
            "titulo": "Rayuela",
            "autor": "Cortázar",
            "isbn": "987654321",
            "disponible": False
        }
    ]
}

# Escribir JSON
with open('biblioteca.json', 'w', encoding='utf-8') as archivo:
    json.dump(biblioteca_data, archivo, indent=4, ensure_ascii=False)

# Leer JSON
with open('biblioteca.json', 'r', encoding='utf-8') as archivo:
    datos = json.load(archivo)
    print(f"Biblioteca: {datos['nombre']}")
    for libro in datos['libros']:
        print(f"Libro: {libro['titulo']}")
```

### 4. Implementación del CRUD

#### 4.1 Sistema de Persistencia para la Biblioteca
```python
import json
from typing import List, Dict, Optional
from datetime import datetime

class SistemaPersistencia:
    def __init__(self, archivo_json: str):
        self.archivo_json = archivo_json
        self.datos = self._cargar_datos()

    def _cargar_datos(self) -> Dict:
        try:
            with open(self.archivo_json, 'r', encoding='utf-8') as archivo:
                return json.load(archivo)
        except FileNotFoundError:
            return {"libros": [], "usuarios": [], "prestamos": []}

    def _guardar_datos(self) -> None:
        with open(self.archivo_json, 'w', encoding='utf-8') as archivo:
            json.dump(self.datos, archivo, indent=4, ensure_ascii=False)

    def agregar_libro(self, libro: Dict) -> bool:
        try:
            self.datos["libros"].append(libro)
            self._guardar_datos()
            return True
        except Exception as e:
            print(f"Error al agregar libro: {e}")
            return False

    def obtener_libro(self, isbn: str) -> Optional[Dict]:
        return next(
            (libro for libro in self.datos["libros"] 
             if libro["isbn"] == isbn),
            None
        )

    def actualizar_libro(self, isbn: str, nuevos_datos: Dict) -> bool:
        for i, libro in enumerate(self.datos["libros"]):
            if libro["isbn"] == isbn:
                self.datos["libros"][i].update(nuevos_datos)
                self._guardar_datos()
                return True
        return False

    def eliminar_libro(self, isbn: str) -> bool:
        longitud_inicial = len(self.datos["libros"])
        self.datos["libros"] = [
            libro for libro in self.datos["libros"] 
            if libro["isbn"] != isbn
        ]
        if len(self.datos["libros"]) < longitud_inicial:
            self._guardar_datos()
            return True
        return False
```

#### 4.2 Implementación con Múltiples Formatos
```python
from abc import ABC, abstractmethod
import csv
import json
import pickle

class AlmacenamientoDatos(ABC):
    @abstractmethod
    def guardar_datos(self, datos: Dict) -> bool:
        pass

    @abstractmethod
    def cargar_datos(self) -> Dict:
        pass

class AlmacenamientoJSON(AlmacenamientoDatos):
    def __init__(self, archivo: str):
        self.archivo = archivo

    def guardar_datos(self, datos: Dict) -> bool:
        try:
            with open(self.archivo, 'w', encoding='utf-8') as f:
                json.dump(datos, f, indent=4, ensure_ascii=False)
            return True
        except Exception as e:
            print(f"Error guardando JSON: {e}")
            return False

    def cargar_datos(self) -> Dict:
        try:
            with open(self.archivo, 'r', encoding='utf-8') as f:
                return json.load(f)
        except FileNotFoundError:
            return {}

class AlmacenamientoCSV(AlmacenamientoDatos):
    def __init__(self, archivo: str):
        self.archivo = archivo

    def guardar_datos(self, datos: Dict) -> bool:
        try:
            with open(self.archivo, 'w', newline='', encoding='utf-8') as f:
                writer = csv.DictWriter(f, fieldnames=datos[0].keys())
                writer.writeheader()
                writer.writerows(datos)
            return True
        except Exception as e:
            print(f"Error guardando CSV: {e}")
            return False

    def cargar_datos(self) -> List[Dict]:
        try:
            with open(self.archivo, 'r', encoding='utf-8') as f:
                return list(csv.DictReader(f))
        except FileNotFoundError:
            return []

class Biblioteca:
    def __init__(self, almacenamiento: AlmacenamientoDatos):
        self.almacenamiento = almacenamiento
        self.datos = self.almacenamiento.cargar_datos()

    def guardar_cambios(self):
        return self.almacenamiento.guardar_datos(self.datos)
```

### 5. Ejemplo Práctico: Implementación Completa

```python
# Ejemplo de uso del sistema de persistencia
if __name__ == "__main__":
    # Inicializar sistema
    sistema = SistemaPersistencia("biblioteca.json")
    
    # Agregar un nuevo libro
    nuevo_libro = {
        "titulo": "1984",
        "autor": "George Orwell",
        "isbn": "9780451524935",
        "disponible": True
    }
    
    if sistema.agregar_libro(nuevo_libro):
        print("Libro agregado exitosamente")
    
    # Buscar un libro
    libro = sistema.obtener_libro("9780451524935")
    if libro:
        print(f"Libro encontrado: {libro['titulo']}")
    
    # Actualizar libro
    actualizacion = {"disponible": False}
    if sistema.actualizar_libro("9780451524935", actualizacion):
        print("Libro actualizado")
    
    # Eliminar libro
    if sistema.eliminar_libro("9780451524935"):
        print("Libro eliminado")

    # Ejemplo con múltiples formatos
    biblioteca_json = Biblioteca(
        AlmacenamientoJSON("biblioteca.json")
    )
    biblioteca_csv = Biblioteca(
        AlmacenamientoCSV("biblioteca.csv")
    )
```

## Recursos Adicionales

- [Python File Objects](https://docs.python.org/3/tutorial/inputoutput.html#reading-and-writing-files)
- [Python CSV Documentation](https://docs.python.org/3/library/csv.html)
- [Python JSON Documentation](https://docs.python.org/3/library/json.html)
- [Real Python - Working with Files](https://realpython.com/working-with-files-in-python/)

## Siguiente Módulo

En el siguiente módulo aprenderemos sobre análisis de datos con Python, donde utilizaremos bibliotecas como Pandas y NumPy para analizar los datos de nuestra biblioteca.
