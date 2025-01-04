# Módulo 5: Desarrollo Web Básico

## Introducción

En este módulo aprenderemos a crear una API REST para nuestro Sistema de Gestión de Biblioteca utilizando FastAPI y Flask. Implementaremos endpoints para gestionar libros, usuarios y préstamos, y crearemos una interfaz básica para interactuar con nuestra API.

## Contenido

### 1. Introducción a APIs REST

#### 1.1 Conceptos Básicos
- **REST**: Representational State Transfer
- **API**: Application Programming Interface
- **Métodos HTTP**: GET, POST, PUT, DELETE
- **Códigos de Estado HTTP**: 200, 201, 400, 404, 500, etc.
- **Formato JSON**: JavaScript Object Notation

#### 1.2 Estructura de una API REST
```plaintext
/api/v1/
    /libros
        GET    - Obtener todos los libros
        POST   - Crear nuevo libro
    /libros/{id}
        GET    - Obtener un libro específico
        PUT    - Actualizar un libro
        DELETE - Eliminar un libro
    /usuarios
        GET    - Obtener todos los usuarios
        POST   - Crear nuevo usuario
    /prestamos
        GET    - Obtener todos los préstamos
        POST   - Crear nuevo préstamo
```

### 2. Implementación con FastAPI

#### 2.1 Configuración Inicial
```python
# Instalar FastAPI
# pip install fastapi uvicorn

from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import List, Optional

# Modelo de datos
class Libro(BaseModel):
    id: Optional[int] = None
    titulo: str
    autor: str
    isbn: str
    disponible: bool = True

# Inicializar FastAPI
app = FastAPI(title="Biblioteca API")

# Almacenamiento en memoria (para ejemplo)
libros_db = []
```

#### 2.2 Implementación de Endpoints Básicos
```python
# Obtener todos los libros
@app.get("/api/v1/libros", response_model=List[Libro])
async def obtener_libros():
    return libros_db

# Obtener un libro específico
@app.get("/api/v1/libros/{libro_id}", response_model=Libro)
async def obtener_libro(libro_id: int):
    if libro_id >= len(libros_db):
        raise HTTPException(status_code=404, detail="Libro no encontrado")
    return libros_db[libro_id]

# Crear un nuevo libro
@app.post("/api/v1/libros", response_model=Libro)
async def crear_libro(libro: Libro):
    libro.id = len(libros_db)
    libros_db.append(libro)
    return libro

# Actualizar un libro
@app.put("/api/v1/libros/{libro_id}", response_model=Libro)
async def actualizar_libro(libro_id: int, libro: Libro):
    if libro_id >= len(libros_db):
        raise HTTPException(status_code=404, detail="Libro no encontrado")
    libro.id = libro_id
    libros_db[libro_id] = libro
    return libro

# Eliminar un libro
@app.delete("/api/v1/libros/{libro_id}")
async def eliminar_libro(libro_id: int):
    if libro_id >= len(libros_db):
        raise HTTPException(status_code=404, detail="Libro no encontrado")
    libros_db.pop(libro_id)
    return {"message": "Libro eliminado"}
```

### 3. Implementación con Flask

#### 3.1 Configuración Inicial
```python
# Instalar Flask
# pip install flask flask-restful

from flask import Flask, request, jsonify
from flask_restful import Api, Resource

app = Flask(__name__)
api = Api(app)

# Almacenamiento en memoria
libros = {}
```

#### 3.2 Implementación de Recursos
```python
class LibroResource(Resource):
    def get(self, libro_id=None):
        if libro_id is None:
            return jsonify(list(libros.values()))
        return libros.get(libro_id, {"error": "Libro no encontrado"}), 404

    def post(self):
        nuevo_libro = request.get_json()
        libro_id = len(libros)
        libros[libro_id] = nuevo_libro
        return nuevo_libro, 201

    def put(self, libro_id):
        if libro_id not in libros:
            return {"error": "Libro no encontrado"}, 404
        libro = request.get_json()
        libros[libro_id] = libro
        return libro

    def delete(self, libro_id):
        if libro_id not in libros:
            return {"error": "Libro no encontrado"}, 404
        del libros[libro_id]
        return "", 204

# Registrar recursos
api.add_resource(LibroResource, '/api/v1/libros', '/api/v1/libros/<int:libro_id>')
```

### 4. Integración con Frontend

#### 4.1 HTML Básico
```html
<!DOCTYPE html>
<html>
<head>
    <title>Biblioteca</title>
    <link rel="stylesheet" href="static/style.css">
</head>
<body>
    <div class="container">
        <h1>Gestión de Biblioteca</h1>
        
        <!-- Formulario para agregar libros -->
        <form id="libro-form">
            <input type="text" id="titulo" placeholder="Título">
            <input type="text" id="autor" placeholder="Autor">
            <input type="text" id="isbn" placeholder="ISBN">
            <button type="submit">Agregar Libro</button>
        </form>

        <!-- Lista de libros -->
        <div id="libros-lista"></div>
    </div>
    <script src="static/app.js"></script>
</body>
</html>
```

#### 4.2 JavaScript para Interactuar con la API
```javascript
// static/app.js
async function obtenerLibros() {
    const response = await fetch('/api/v1/libros');
    const libros = await response.json();
    mostrarLibros(libros);
}

async function agregarLibro(evento) {
    evento.preventDefault();
    
    const libro = {
        titulo: document.getElementById('titulo').value,
        autor: document.getElementById('autor').value,
        isbn: document.getElementById('isbn').value,
        disponible: true
    };

    await fetch('/api/v1/libros', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
        },
        body: JSON.stringify(libro)
    });

    obtenerLibros();
}

function mostrarLibros(libros) {
    const listaDiv = document.getElementById('libros-lista');
    listaDiv.innerHTML = libros.map(libro => `
        <div class="libro-card">
            <h3>${libro.titulo}</h3>
            <p>Autor: ${libro.autor}</p>
            <p>ISBN: ${libro.isbn}</p>
            <p>Estado: ${libro.disponible ? 'Disponible' : 'Prestado'}</p>
            <button onclick="eliminarLibro(${libro.id})">Eliminar</button>
        </div>
    `).join('');
}

async function eliminarLibro(id) {
    await fetch(`/api/v1/libros/${id}`, {
        method: 'DELETE'
    });
    obtenerLibros();
}

document.getElementById('libro-form').addEventListener('submit', agregarLibro);
obtenerLibros();
```

### 5. Despliegue Básico

#### 5.1 Preparación para Producción
```python
# config.py
class Config:
    DEBUG = False
    TESTING = False
    DATABASE_URI = 'sqlite:///biblioteca.db'

class DevelopmentConfig(Config):
    DEBUG = True

class ProductionConfig(Config):
    DATABASE_URI = 'postgresql://user:pass@localhost/biblioteca'
```

#### 5.2 Script de Inicio
```python
# run.py
import uvicorn
from app.main import app
from app.config import DevelopmentConfig, ProductionConfig
import os

if __name__ == "__main__":
    env = os.getenv("FLASK_ENV", "development")
    config = DevelopmentConfig if env == "development" else ProductionConfig
    
    uvicorn.run(
        "app.main:app",
        host="0.0.0.0",
        port=8000,
        reload=config.DEBUG
    )
```

#### 5.3 Dockerfile
```dockerfile
FROM python:3.9

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .

ENV FLASK_ENV=production

CMD ["python", "run.py"]
```

#### 5.4 Docker Compose
```yaml
version: '3'
services:
  web:
    build: .
    ports:
      - "8000:8000"
    environment:
      - FLASK_ENV=production
    depends_on:
      - db
  
  db:
    image: postgres:13
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: biblioteca
```

## Recursos Adicionales

- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [Flask Documentation](https://flask.palletsprojects.com/)
- [RESTful API Design Guidelines](https://restfulapi.net/)
- [Docker Documentation](https://docs.docker.com/)
- [Postman - API Testing Tool](https://www.postman.com/)

## Siguiente Paso

¡Felicitaciones! Has completado el curso básico de Python. Ahora puedes:
- Experimentar con diferentes endpoints
- Agregar más funcionalidades a la API
- Mejorar la interfaz de usuario
- Implementar autenticación y autorización
- Explorar opciones de despliegue en la nube
