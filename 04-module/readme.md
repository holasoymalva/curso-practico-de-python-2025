# Módulo 4: Análisis de Datos con Python

## Introducción

En este módulo aprenderemos a analizar datos utilizando las principales bibliotecas de Python para análisis de datos: NumPy, Pandas, Matplotlib y Seaborn. Aplicaremos estos conocimientos para analizar los datos de nuestra biblioteca y crear visualizaciones informativas.

## Contenido

### 1. NumPy: Computación Numérica

#### 1.1 Fundamentos de NumPy
```python
import numpy as np

# Crear arrays
arr1 = np.array([1, 2, 3, 4, 5])
arr2 = np.zeros(5)
arr3 = np.ones((3, 3))
arr4 = np.arange(0, 10, 2)  # [0, 2, 4, 6, 8]

# Operaciones básicas
suma = arr1 + 2          # Suma elemento a elemento
multiplicacion = arr1 * 2  # Multiplicación elemento a elemento
potencia = arr1 ** 2     # Potencia elemento a elemento

# Estadísticas básicas
media = np.mean(arr1)
mediana = np.median(arr1)
desviacion = np.std(arr1)
```

#### 1.2 Arrays Multidimensionales
```python
# Crear matriz
matriz = np.array([[1, 2, 3],
                   [4, 5, 6],
                   [7, 8, 9]])

# Reshaping
arr_reshaped = arr1.reshape(5, 1)
matriz_plana = matriz.flatten()

# Indexación y slicing
elemento = matriz[0, 1]      # Elemento en fila 0, columna 1
fila = matriz[0, :]         # Primera fila completa
columna = matriz[:, 0]      # Primera columna completa
submatriz = matriz[0:2, 1:] # Submatriz 2x2
```

### 2. Pandas: Análisis de Datos

#### 2.1 Series y DataFrames
```python
import pandas as pd

# Crear Series
s = pd.Series([1, 2, 3, 4, 5], index=['a', 'b', 'c', 'd', 'e'])

# Crear DataFrame
datos_libros = {
    'titulo': ['Don Quijote', 'Rayuela', '1984', 'Ficciones'],
    'autor': ['Cervantes', 'Cortázar', 'Orwell', 'Borges'],
    'año': [1605, 1963, 1949, 1944],
    'prestamos': [25, 15, 30, 10]
}

df = pd.DataFrame(datos_libros)
```

#### 2.2 Carga y Manipulación de Datos
```python
# Leer datos
df_csv = pd.read_csv('biblioteca.csv')
df_excel = pd.read_excel('biblioteca.xlsx')
df_json = pd.read_json('biblioteca.json')

# Información básica
print(df.info())           # Información del DataFrame
print(df.describe())       # Estadísticas descriptivas
print(df.head())          # Primeras 5 filas

# Selección de datos
autores = df['autor']                     # Seleccionar columna
libros_antiguos = df[df['año'] < 1950]    # Filtrar por condición
libros_populares = df[df['prestamos'] > 20]  # Filtrar por préstamos
```

#### 2.3 Agregación y Agrupación
```python
# Agrupar por autor
por_autor = df.groupby('autor').agg({
    'prestamos': ['sum', 'mean'],
    'titulo': 'count'
}).reset_index()

# Ordenar datos
mas_prestados = df.sort_values('prestamos', ascending=False)
mas_antiguos = df.sort_values('año')

# Pivotear datos
tabla_pivot = df.pivot_table(
    values='prestamos',
    index='autor',
    columns='año',
    aggfunc='sum',
    fill_value=0
)
```

### 3. Visualización de Datos

#### 3.1 Matplotlib Básico
```python
import matplotlib.pyplot as plt

# Gráfico de líneas
plt.figure(figsize=(10, 6))
plt.plot(df['año'], df['prestamos'], marker='o')
plt.title('Préstamos por Año de Publicación')
plt.xlabel('Año')
plt.ylabel('Número de Préstamos')
plt.grid(True)
plt.show()

# Gráfico de barras
plt.figure(figsize=(10, 6))
plt.bar(df['autor'], df['prestamos'])
plt.title('Préstamos por Autor')
plt.xticks(rotation=45)
plt.show()
```

#### 3.2 Seaborn: Visualizaciones Estadísticas
```python
import seaborn as sns

# Configuración de estilo
sns.set_style("whitegrid")

# Gráfico de dispersión
plt.figure(figsize=(10, 6))
sns.scatterplot(data=df, x='año', y='prestamos', hue='autor')
plt.title('Relación entre Año y Préstamos')
plt.show()

# Gráfico de violín
plt.figure(figsize=(10, 6))
sns.violinplot(data=df, x='autor', y='prestamos')
plt.title('Distribución de Préstamos por Autor')
plt.show()

# Heatmap de correlación
plt.figure(figsize=(8, 8))
sns.heatmap(df.corr(), annot=True, cmap='coolwarm')
plt.title('Matriz de Correlación')
plt.show()
```

### 4. Ejemplo Práctico: Análisis de Biblioteca

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from datetime import datetime

class AnalizadorBiblioteca:
    def __init__(self, archivo_datos):
        self.df = pd.read_csv(archivo_datos)
        self.preparar_datos()
    
    def preparar_datos(self):
        # Convertir fechas
        self.df['fecha_prestamo'] = pd.to_datetime(self.df['fecha_prestamo'])
        self.df['fecha_devolucion'] = pd.to_datetime(self.df['fecha_devolucion'])
        
        # Calcular duración de préstamos
        self.df['duracion_prestamo'] = (
            self.df['fecha_devolucion'] - self.df['fecha_prestamo']
        ).dt.days
    
    def estadisticas_generales(self):
        stats = {
            'total_libros': len(self.df['isbn'].unique()),
            'total_prestamos': len(self.df),
            'prestamos_activos': sum(self.df['fecha_devolucion'].isna()),
            'duracion_promedio': self.df['duracion_prestamo'].mean()
        }
        return pd.Series(stats)
    
    def libros_mas_prestados(self, n=10):
        return self.df.groupby('titulo')['isbn'].count()\
                     .sort_values(ascending=False)\
                     .head(n)
    
    def analisis_temporal(self):
        prestamos_mensuales = self.df.set_index('fecha_prestamo')\
                                   .resample('M')['isbn'].count()
        
        plt.figure(figsize=(12, 6))
        prestamos_mensuales.plot(kind='line', marker='o')
        plt.title('Evolución de Préstamos Mensuales')
        plt.xlabel('Fecha')
        plt.ylabel('Número de Préstamos')
        plt.grid(True)
        plt.show()
    
    def visualizar_patrones_prestamos(self):
        # Gráfico de calor por día y hora
        self.df['hora'] = self.df['fecha_prestamo'].dt.hour
        self.df['dia_semana'] = self.df['fecha_prestamo'].dt.day_name()
        
        prestamos_hora_dia = pd.crosstab(
            self.df['dia_semana'],
            self.df['hora']
        )
        
        plt.figure(figsize=(12, 8))
        sns.heatmap(prestamos_hora_dia, cmap='YlOrRd', annot=True)
        plt.title('Patrones de Préstamos por Día y Hora')
        plt.show()

# Uso del analizador
if __name__ == "__main__":
    analizador = AnalizadorBiblioteca('prestamos_biblioteca.csv')
    
    # Obtener estadísticas generales
    print("Estadísticas Generales:")
    print(analizador.estadisticas_generales())
    
    # Ver libros más prestados
    print("\nLibros Más Prestados:")
    print(analizador.libros_mas_prestados())
    
    # Análisis temporal
    analizador.analisis_temporal()
    
    # Visualizar patrones
    analizador.visualizar_patrones_prestamos()
```

### 5. Ejercicios Prácticos con Datos de la Biblioteca

1. Análisis de Préstamos:
```python
# Cargar datos
prestamos_df = pd.read_csv('prestamos.csv')

# Análisis temporal de préstamos
prestamos_por_mes = prestamos_df.groupby(
    pd.Grouper(key='fecha_prestamo', freq='M')
)['id'].count()

# Visualizar tendencia
plt.figure(figsize=(12, 6))
prestamos_por_mes.plot(kind='line', marker='o')
plt.title('Tendencia de Préstamos Mensuales')
plt.xlabel('Mes')
plt.ylabel('Número de Préstamos')
plt.grid(True)
plt.show()
```

2. Análisis de Usuarios:
```python
# Análisis de comportamiento de usuarios
usuarios_df = pd.read_csv('usuarios.csv')
prestamos_por_usuario = prestamos_df.merge(
    usuarios_df,
    on='usuario_id'
)

# Segmentación de usuarios
segmentacion = prestamos_por_usuario.groupby('usuario_id').agg({
    'id': 'count',
    'duracion_prestamo': 'mean'
}).rename(columns={
    'id': 'total_prestamos',
    'duracion_prestamo': 'duracion_promedio'
})

# Visualizar segmentación
plt.figure(figsize=(10, 6))
sns.scatterplot(
    data=segmentacion,
    x='total_prestamos',
    y='duracion_promedio'
)
plt.title('Segmentación de Usuarios')
plt.xlabel('Total de Préstamos')
plt.ylabel('Duración Promedio de Préstamos (días)')
plt.show()
```

## Recursos Adicionales

- [NumPy Documentation](https://numpy.org/doc/)
- [Pandas Documentation](https://pandas.pydata.org/docs/)
- [Matplotlib Documentation](https://matplotlib.org/stable/contents.html)
- [Seaborn Documentation](https://seaborn.pydata.org/)
- [Python Data Science Handbook](https://jakevdp.github.io/PythonDataScienceHandbook/)

## Siguiente Módulo

En el siguiente módulo aprenderemos sobre desarrollo web básico con Flask/FastAPI, donde crearemos una API REST para nuestro sistema de biblioteca.
