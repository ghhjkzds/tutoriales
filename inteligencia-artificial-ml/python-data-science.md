# 🐍 Python para Data Science - Tutorial Completo

## Introducción

Python se ha convertido en el lenguaje de programación más popular para Data Science debido a su simplicidad, versatilidad y el poderoso ecosistema de bibliotecas disponibles. Este tutorial te guiará desde los conceptos básicos hasta técnicas avanzadas de análisis de datos.

---

## 📋 Tabla de Contenidos

1. [Configuración del Entorno](#configuración-del-entorno)
2. [Fundamentos de Python para Data Science](#fundamentos-de-python-para-data-science)
3. [NumPy - Computación Numérica](#numpy---computación-numérica)
4. [Pandas - Manipulación de Datos](#pandas---manipulación-de-datos)
5. [Matplotlib y Seaborn - Visualización](#matplotlib-y-seaborn---visualización)
6. [Análisis Exploratorio de Datos (EDA)](#análisis-exploratorio-de-datos-eda)
7. [Preprocesamiento de Datos](#preprocesamiento-de-datos)
8. [Introducción a Machine Learning con Scikit-learn](#introducción-a-machine-learning-con-scikit-learn)
9. [Proyectos Prácticos](#proyectos-prácticos)
10. [Recursos Adicionales](#recursos-adicionales)

---

## Configuración del Entorno

### Instalación de Python

#### Windows
1. Descarga Python desde [python.org](https://www.python.org/downloads/)
2. Durante la instalación, marca la opción "Add Python to PATH"
3. Verifica la instalación:
```bash
python --version
```

#### macOS
```bash
# Usando Homebrew
brew install python3
```

#### Linux (Ubuntu/Debian)
```bash
sudo apt update
sudo apt install python3 python3-pip python3-venv
```

### Creación de un Entorno Virtual

Es una buena práctica crear entornos virtuales para cada proyecto:

```bash
# Crear entorno virtual
python -m venv datascience_env

# Activar en Windows
datascience_env\Scripts\activate

# Activar en macOS/Linux
source datascience_env/bin/activate
```

### Instalación de Bibliotecas Esenciales

```bash
pip install numpy pandas matplotlib seaborn scikit-learn jupyter
```

### Jupyter Notebook

Jupyter Notebook es la herramienta preferida para Data Science:

```bash
# Instalar Jupyter
pip install jupyter

# Iniciar Jupyter Notebook
jupyter notebook
```

---

## Fundamentos de Python para Data Science

### Tipos de Datos Básicos

```python
# Números
entero = 42
flotante = 3.14159
complejo = 2 + 3j

# Cadenas de texto
texto = "Hola, Data Science!"

# Booleanos
verdadero = True
falso = False

# Listas (mutables)
lista = [1, 2, 3, 4, 5]

# Tuplas (inmutables)
tupla = (1, 2, 3)

# Diccionarios
diccionario = {"nombre": "Python", "version": 3.11}

# Conjuntos
conjunto = {1, 2, 3, 4, 5}
```

### Estructuras de Control

```python
# Condicionales
edad = 25
if edad >= 18:
    print("Mayor de edad")
elif edad >= 13:
    print("Adolescente")
else:
    print("Niño")

# Bucle for
for i in range(5):
    print(f"Iteración {i}")

# Bucle while
contador = 0
while contador < 5:
    print(contador)
    contador += 1

# List comprehensions
cuadrados = [x**2 for x in range(10)]
pares = [x for x in range(20) if x % 2 == 0]
```

### Funciones

```python
# Función básica
def saludar(nombre):
    return f"Hola, {nombre}!"

# Función con argumentos por defecto
def potencia(base, exponente=2):
    return base ** exponente

# Función lambda
cuadrado = lambda x: x ** 2

# Función con múltiples retornos
def estadisticas(numeros):
    return min(numeros), max(numeros), sum(numeros) / len(numeros)

minimo, maximo, promedio = estadisticas([1, 2, 3, 4, 5])
```

---

## NumPy - Computación Numérica

NumPy es la biblioteca fundamental para computación científica en Python.

### Creación de Arrays

```python
import numpy as np

# Crear arrays desde listas
arr1 = np.array([1, 2, 3, 4, 5])
arr2 = np.array([[1, 2, 3], [4, 5, 6]])

# Arrays especiales
zeros = np.zeros((3, 4))           # Matriz de ceros
ones = np.ones((2, 3))             # Matriz de unos
identity = np.eye(3)               # Matriz identidad
rango = np.arange(0, 10, 2)        # [0, 2, 4, 6, 8]
lineal = np.linspace(0, 1, 5)      # 5 valores entre 0 y 1
aleatorios = np.random.rand(3, 3)  # Valores aleatorios entre 0 y 1
normales = np.random.randn(100)    # Distribución normal estándar
```

### Operaciones con Arrays

```python
import numpy as np

a = np.array([1, 2, 3, 4, 5])
b = np.array([10, 20, 30, 40, 50])

# Operaciones elemento a elemento
suma = a + b
resta = b - a
producto = a * b
division = b / a
potencia = a ** 2

# Operaciones matemáticas
print(np.sqrt(a))      # Raíz cuadrada
print(np.exp(a))       # Exponencial
print(np.log(a))       # Logaritmo natural
print(np.sin(a))       # Seno

# Estadísticas
print(np.mean(a))      # Media
print(np.median(a))    # Mediana
print(np.std(a))       # Desviación estándar
print(np.var(a))       # Varianza
print(np.sum(a))       # Suma
print(np.min(a))       # Mínimo
print(np.max(a))       # Máximo
```

### Indexación y Slicing

```python
import numpy as np

arr = np.array([[1, 2, 3, 4],
                [5, 6, 7, 8],
                [9, 10, 11, 12]])

# Acceso a elementos
print(arr[0, 0])       # Primer elemento: 1
print(arr[1, 2])       # Fila 1, columna 2: 7

# Slicing
print(arr[0, :])       # Primera fila completa
print(arr[:, 0])       # Primera columna completa
print(arr[0:2, 1:3])   # Submatriz

# Indexación booleana
print(arr[arr > 5])    # Elementos mayores que 5

# Reshape
nuevo_shape = arr.reshape(2, 6)
aplanado = arr.flatten()
```

### Álgebra Lineal

```python
import numpy as np

A = np.array([[1, 2], [3, 4]])
B = np.array([[5, 6], [7, 8]])

# Producto matricial
producto = np.dot(A, B)
# o también
producto = A @ B

# Transpuesta
transpuesta = A.T

# Determinante
det = np.linalg.det(A)

# Inversa
inversa = np.linalg.inv(A)

# Autovalores y autovectores
autovalores, autovectores = np.linalg.eig(A)

# Resolver sistema de ecuaciones Ax = b
b = np.array([1, 2])
x = np.linalg.solve(A, b)
```

---

## Pandas - Manipulación de Datos

Pandas es la biblioteca esencial para manipulación y análisis de datos.

### Series y DataFrames

```python
import pandas as pd
import numpy as np

# Crear una Serie
serie = pd.Series([1, 2, 3, 4, 5], index=['a', 'b', 'c', 'd', 'e'])

# Crear un DataFrame desde diccionario
datos = {
    'nombre': ['Ana', 'Luis', 'María', 'Carlos'],
    'edad': [25, 30, 22, 35],
    'ciudad': ['Madrid', 'Barcelona', 'Valencia', 'Sevilla'],
    'salario': [30000, 45000, 28000, 55000]
}
df = pd.DataFrame(datos)

# Crear DataFrame desde array NumPy
matriz = np.random.randn(5, 3)
df2 = pd.DataFrame(matriz, columns=['A', 'B', 'C'])
```

### Lectura y Escritura de Datos

```python
import pandas as pd

# Leer CSV
df = pd.read_csv('datos.csv')

# Leer Excel
df = pd.read_excel('datos.xlsx', sheet_name='Hoja1')

# Leer JSON
df = pd.read_json('datos.json')

# Leer desde URL
url = 'https://ejemplo.com/datos.csv'
df = pd.read_csv(url)

# Guardar datos
df.to_csv('salida.csv', index=False)
df.to_excel('salida.xlsx', index=False)
df.to_json('salida.json')
```

### Exploración de Datos

```python
import pandas as pd

# Información básica
print(df.shape)          # Dimensiones (filas, columnas)
print(df.columns)        # Nombres de columnas
print(df.dtypes)         # Tipos de datos
print(df.info())         # Información completa
print(df.describe())     # Estadísticas descriptivas

# Visualizar datos
print(df.head())         # Primeras 5 filas
print(df.tail())         # Últimas 5 filas
print(df.sample(5))      # 5 filas aleatorias

# Valores únicos
print(df['ciudad'].unique())
print(df['ciudad'].nunique())
print(df['ciudad'].value_counts())
```

### Selección y Filtrado

```python
import pandas as pd

# Selección de columnas
print(df['nombre'])           # Una columna
print(df[['nombre', 'edad']]) # Múltiples columnas

# Selección con loc (etiquetas)
print(df.loc[0])                      # Fila 0
print(df.loc[0:2, 'nombre':'ciudad']) # Rango de filas y columnas
print(df.loc[df['edad'] > 25])        # Filtrar por condición

# Selección con iloc (índices numéricos)
print(df.iloc[0])         # Primera fila
print(df.iloc[0:2, 0:2])  # Primeras 2 filas, primeras 2 columnas

# Filtrado
mayores_30 = df[df['edad'] > 30]
en_madrid = df[df['ciudad'] == 'Madrid']
combinado = df[(df['edad'] > 25) & (df['salario'] > 30000)]
```

### Manipulación de Datos

```python
import pandas as pd
import numpy as np

# Añadir columnas
df['bonus'] = df['salario'] * 0.1
df['edad_categoria'] = np.where(df['edad'] < 30, 'Joven', 'Adulto')

# Eliminar columnas
df = df.drop('bonus', axis=1)

# Renombrar columnas
df = df.rename(columns={'nombre': 'empleado', 'ciudad': 'ubicacion'})

# Ordenar
df_ordenado = df.sort_values('salario', ascending=False)
df_multi_orden = df.sort_values(['ciudad', 'edad'])

# Valores nulos
print(df.isnull().sum())        # Contar nulos por columna
df_limpio = df.dropna()         # Eliminar filas con nulos
df_rellenado = df.fillna(0)     # Rellenar nulos con 0
df['columna'] = df['columna'].fillna(df['columna'].mean())  # Rellenar con media
```

### Agrupación y Agregación

```python
import pandas as pd

# Agrupar por una columna
por_ciudad = df.groupby('ciudad')

# Agregaciones
print(por_ciudad['salario'].mean())   # Media por ciudad
print(por_ciudad['salario'].sum())    # Suma por ciudad
print(por_ciudad.size())              # Tamaño de grupos

# Múltiples agregaciones
resumen = df.groupby('ciudad').agg({
    'salario': ['mean', 'min', 'max'],
    'edad': 'mean'
})

# Tablas dinámicas
pivot = pd.pivot_table(df, 
                       values='salario', 
                       index='ciudad', 
                       aggfunc='mean')
```

### Combinación de DataFrames

```python
import pandas as pd

df1 = pd.DataFrame({'id': [1, 2, 3], 'nombre': ['A', 'B', 'C']})
df2 = pd.DataFrame({'id': [2, 3, 4], 'valor': [100, 200, 300]})

# Merge (como JOIN en SQL)
inner_join = pd.merge(df1, df2, on='id')                    # Inner join
left_join = pd.merge(df1, df2, on='id', how='left')         # Left join
right_join = pd.merge(df1, df2, on='id', how='right')       # Right join
outer_join = pd.merge(df1, df2, on='id', how='outer')       # Outer join

# Concatenar
vertical = pd.concat([df1, df1])                  # Concatenación vertical
horizontal = pd.concat([df1, df2], axis=1)        # Concatenación horizontal
```

---

## Matplotlib y Seaborn - Visualización

### Matplotlib Básico

```python
import matplotlib.pyplot as plt
import numpy as np

# Gráfico de línea básico
x = np.linspace(0, 10, 100)
y = np.sin(x)

plt.figure(figsize=(10, 6))
plt.plot(x, y, label='sin(x)', color='blue', linewidth=2)
plt.xlabel('X')
plt.ylabel('Y')
plt.title('Función Seno')
plt.legend()
plt.grid(True)
plt.savefig('grafico.png', dpi=300)
plt.show()
```

### Múltiples Gráficos

```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(0, 10, 100)

fig, axes = plt.subplots(2, 2, figsize=(12, 10))

# Gráfico 1: Línea
axes[0, 0].plot(x, np.sin(x), 'b-')
axes[0, 0].set_title('Seno')

# Gráfico 2: Barras
categorias = ['A', 'B', 'C', 'D']
valores = [23, 45, 12, 38]
axes[0, 1].bar(categorias, valores, color='green')
axes[0, 1].set_title('Gráfico de Barras')

# Gráfico 3: Dispersión
x_scatter = np.random.randn(50)
y_scatter = np.random.randn(50)
axes[1, 0].scatter(x_scatter, y_scatter, alpha=0.7)
axes[1, 0].set_title('Dispersión')

# Gráfico 4: Histograma
datos = np.random.randn(1000)
axes[1, 1].hist(datos, bins=30, edgecolor='black')
axes[1, 1].set_title('Histograma')

plt.tight_layout()
plt.show()
```

### Seaborn para Visualizaciones Estadísticas

```python
import seaborn as sns
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np

# Cargar dataset de ejemplo
tips = sns.load_dataset('tips')

# Configurar estilo
sns.set_theme(style="whitegrid")

# Gráfico de distribución
plt.figure(figsize=(10, 6))
sns.histplot(data=tips, x='total_bill', kde=True)
plt.title('Distribución de Cuenta Total')
plt.show()

# Boxplot
plt.figure(figsize=(10, 6))
sns.boxplot(data=tips, x='day', y='total_bill')
plt.title('Cuenta por Día')
plt.show()

# Gráfico de violín
plt.figure(figsize=(10, 6))
sns.violinplot(data=tips, x='day', y='total_bill', hue='sex')
plt.title('Distribución por Día y Sexo')
plt.show()

# Scatter plot con regresión
plt.figure(figsize=(10, 6))
sns.regplot(data=tips, x='total_bill', y='tip')
plt.title('Propina vs Cuenta Total')
plt.show()

# Mapa de calor de correlaciones
plt.figure(figsize=(8, 6))
correlaciones = tips.select_dtypes(include=[np.number]).corr()
sns.heatmap(correlaciones, annot=True, cmap='coolwarm', center=0)
plt.title('Matriz de Correlaciones')
plt.show()

# Pairplot
sns.pairplot(tips, hue='sex')
plt.show()
```

---

## Análisis Exploratorio de Datos (EDA)

### Proceso Completo de EDA

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

# 1. Cargar datos
df = pd.read_csv('dataset.csv')

# 2. Primera inspección
print("="*50)
print("INFORMACIÓN BÁSICA")
print("="*50)
print(f"Forma del dataset: {df.shape}")
print(f"\nPrimeras filas:")
print(df.head())
print(f"\nTipos de datos:")
print(df.dtypes)

# 3. Estadísticas descriptivas
print("\n" + "="*50)
print("ESTADÍSTICAS DESCRIPTIVAS")
print("="*50)
print(df.describe())

# 4. Valores nulos
print("\n" + "="*50)
print("ANÁLISIS DE VALORES NULOS")
print("="*50)
nulos = df.isnull().sum()
porcentaje_nulos = (nulos / len(df)) * 100
print(pd.DataFrame({'Nulos': nulos, 'Porcentaje': porcentaje_nulos}))

# 5. Análisis de variables categóricas
print("\n" + "="*50)
print("VARIABLES CATEGÓRICAS")
print("="*50)
categoricas = df.select_dtypes(include=['object']).columns
for col in categoricas:
    print(f"\n{col}:")
    print(df[col].value_counts())

# 6. Visualizaciones
fig, axes = plt.subplots(2, 2, figsize=(14, 10))

# Distribución de variable numérica principal
numeric_cols = df.select_dtypes(include=[np.number]).columns
if len(numeric_cols) > 0:
    sns.histplot(data=df, x=numeric_cols[0], kde=True, ax=axes[0, 0])
    axes[0, 0].set_title(f'Distribución de {numeric_cols[0]}')

# Boxplots de outliers
if len(numeric_cols) > 0:
    df[numeric_cols].boxplot(ax=axes[0, 1])
    axes[0, 1].set_title('Boxplots de Variables Numéricas')
    axes[0, 1].tick_params(axis='x', rotation=45)

# Matriz de correlación
if len(numeric_cols) > 1:
    corr_matrix = df[numeric_cols].corr()
    sns.heatmap(corr_matrix, annot=True, cmap='coolwarm', ax=axes[1, 0])
    axes[1, 0].set_title('Matriz de Correlación')

# Variable categórica principal
if len(categoricas) > 0:
    df[categoricas[0]].value_counts().plot(kind='bar', ax=axes[1, 1])
    axes[1, 1].set_title(f'Distribución de {categoricas[0]}')
    axes[1, 1].tick_params(axis='x', rotation=45)

plt.tight_layout()
plt.savefig('eda_resumen.png', dpi=300)
plt.show()
```

### Detección de Outliers

```python
import pandas as pd
import numpy as np

def detectar_outliers_iqr(df, columna):
    """Detecta outliers usando el método IQR."""
    Q1 = df[columna].quantile(0.25)
    Q3 = df[columna].quantile(0.75)
    IQR = Q3 - Q1
    
    limite_inferior = Q1 - 1.5 * IQR
    limite_superior = Q3 + 1.5 * IQR
    
    outliers = df[(df[columna] < limite_inferior) | (df[columna] > limite_superior)]
    return outliers, limite_inferior, limite_superior

def detectar_outliers_zscore(df, columna, umbral=3):
    """Detecta outliers usando Z-score."""
    media = df[columna].mean()
    std = df[columna].std()
    z_scores = np.abs((df[columna] - media) / std)
    
    outliers = df[z_scores > umbral]
    return outliers

# Ejemplo de uso
# outliers_iqr, li, ls = detectar_outliers_iqr(df, 'precio')
# outliers_zscore = detectar_outliers_zscore(df, 'precio')
```

---

## Preprocesamiento de Datos

### Limpieza de Datos

```python
import pandas as pd
import numpy as np

# Manejo de valores nulos
df['columna'].fillna(df['columna'].mean(), inplace=True)     # Rellenar con media
df['columna'].fillna(df['columna'].median(), inplace=True)   # Rellenar con mediana
df['columna'].fillna(method='ffill', inplace=True)           # Forward fill
df['columna'].fillna(method='bfill', inplace=True)           # Backward fill

# Eliminar filas con nulos
df_limpio = df.dropna()
df_limpio = df.dropna(subset=['columna1', 'columna2'])       # Solo en columnas específicas

# Eliminar duplicados
df_sin_duplicados = df.drop_duplicates()
df_sin_duplicados = df.drop_duplicates(subset=['columna1'])  # Por columna específica

# Convertir tipos de datos
df['fecha'] = pd.to_datetime(df['fecha'])
df['numero'] = pd.to_numeric(df['numero'], errors='coerce')
df['categoria'] = df['categoria'].astype('category')
```

### Codificación de Variables Categóricas

```python
import pandas as pd
from sklearn.preprocessing import LabelEncoder, OneHotEncoder

# Label Encoding (para variables ordinales)
le = LabelEncoder()
df['categoria_encoded'] = le.fit_transform(df['categoria'])

# One-Hot Encoding (para variables nominales)
df_encoded = pd.get_dummies(df, columns=['categoria'], prefix='cat')

# Usando sklearn
ohe = OneHotEncoder(sparse_output=False)
categorias_encoded = ohe.fit_transform(df[['categoria']])
```

### Escalado de Características

```python
from sklearn.preprocessing import StandardScaler, MinMaxScaler, RobustScaler
import pandas as pd

# Estandarización (Z-score)
scaler = StandardScaler()
df_scaled = pd.DataFrame(
    scaler.fit_transform(df[['col1', 'col2']]),
    columns=['col1', 'col2']
)

# Normalización Min-Max (0-1)
minmax_scaler = MinMaxScaler()
df_normalized = pd.DataFrame(
    minmax_scaler.fit_transform(df[['col1', 'col2']]),
    columns=['col1', 'col2']
)

# Robust Scaler (robusto a outliers)
robust_scaler = RobustScaler()
df_robust = pd.DataFrame(
    robust_scaler.fit_transform(df[['col1', 'col2']]),
    columns=['col1', 'col2']
)
```

### División de Datos

```python
from sklearn.model_selection import train_test_split

# Separar características y variable objetivo
X = df.drop('target', axis=1)
y = df['target']

# División train/test
X_train, X_test, y_train, y_test = train_test_split(
    X, y, 
    test_size=0.2,      # 20% para test
    random_state=42,    # Reproducibilidad
    stratify=y          # Mantener proporciones de clases
)

# División train/validation/test
X_temp, X_test, y_temp, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
X_train, X_val, y_train, y_val = train_test_split(X_temp, y_temp, test_size=0.25, random_state=42)
# Resultado: 60% train, 20% validation, 20% test
```

---

## Introducción a Machine Learning con Scikit-learn

### Flujo de Trabajo de ML

```python
from sklearn.model_selection import train_test_split, cross_val_score
from sklearn.preprocessing import StandardScaler
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score, classification_report, confusion_matrix
import pandas as pd
import numpy as np

# 1. Preparar datos
X = df.drop('target', axis=1)
y = df['target']

# 2. Dividir datos
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# 3. Escalar características
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)

# 4. Entrenar modelo
modelo = LogisticRegression()
modelo.fit(X_train_scaled, y_train)

# 5. Predecir
y_pred = modelo.predict(X_test_scaled)

# 6. Evaluar
print("Accuracy:", accuracy_score(y_test, y_pred))
print("\nClassification Report:")
print(classification_report(y_test, y_pred))
print("\nConfusion Matrix:")
print(confusion_matrix(y_test, y_pred))

# 7. Validación cruzada
scores = cross_val_score(modelo, X_train_scaled, y_train, cv=5)
print(f"\nCV Scores: {scores}")
print(f"CV Mean: {scores.mean():.4f} (+/- {scores.std()*2:.4f})")
```

### Algoritmos de Clasificación

```python
from sklearn.linear_model import LogisticRegression
from sklearn.tree import DecisionTreeClassifier
from sklearn.ensemble import RandomForestClassifier, GradientBoostingClassifier
from sklearn.svm import SVC
from sklearn.neighbors import KNeighborsClassifier
from sklearn.naive_bayes import GaussianNB

# Regresión Logística
log_reg = LogisticRegression(max_iter=1000)

# Árbol de Decisión
tree = DecisionTreeClassifier(max_depth=5, random_state=42)

# Random Forest
rf = RandomForestClassifier(n_estimators=100, random_state=42)

# Gradient Boosting
gb = GradientBoostingClassifier(n_estimators=100, random_state=42)

# SVM
svm = SVC(kernel='rbf', C=1.0)

# K-Nearest Neighbors
knn = KNeighborsClassifier(n_neighbors=5)

# Naive Bayes
nb = GaussianNB()

# Comparar modelos
modelos = {
    'Logistic Regression': log_reg,
    'Decision Tree': tree,
    'Random Forest': rf,
    'Gradient Boosting': gb,
    'SVM': svm,
    'KNN': knn,
    'Naive Bayes': nb
}

for nombre, modelo in modelos.items():
    modelo.fit(X_train_scaled, y_train)
    score = modelo.score(X_test_scaled, y_test)
    print(f"{nombre}: {score:.4f}")
```

### Algoritmos de Regresión

```python
from sklearn.linear_model import LinearRegression, Ridge, Lasso, ElasticNet
from sklearn.tree import DecisionTreeRegressor
from sklearn.ensemble import RandomForestRegressor, GradientBoostingRegressor
from sklearn.svm import SVR
from sklearn.metrics import mean_squared_error, mean_absolute_error, r2_score
import numpy as np

# Regresión Lineal
lin_reg = LinearRegression()

# Ridge (L2 regularization)
ridge = Ridge(alpha=1.0)

# Lasso (L1 regularization)
lasso = Lasso(alpha=1.0)

# ElasticNet (L1 + L2)
elastic = ElasticNet(alpha=1.0, l1_ratio=0.5)

# Random Forest Regressor
rf_reg = RandomForestRegressor(n_estimators=100, random_state=42)

# Gradient Boosting Regressor
gb_reg = GradientBoostingRegressor(n_estimators=100, random_state=42)

# Entrenar y evaluar
modelo = LinearRegression()
modelo.fit(X_train, y_train)
y_pred = modelo.predict(X_test)

# Métricas de regresión
print(f"MSE: {mean_squared_error(y_test, y_pred):.4f}")
print(f"RMSE: {np.sqrt(mean_squared_error(y_test, y_pred)):.4f}")
print(f"MAE: {mean_absolute_error(y_test, y_pred):.4f}")
print(f"R2 Score: {r2_score(y_test, y_pred):.4f}")
```

### Optimización de Hiperparámetros

```python
from sklearn.model_selection import GridSearchCV, RandomizedSearchCV
from sklearn.ensemble import RandomForestClassifier

# Grid Search
param_grid = {
    'n_estimators': [50, 100, 200],
    'max_depth': [None, 5, 10, 15],
    'min_samples_split': [2, 5, 10],
    'min_samples_leaf': [1, 2, 4]
}

rf = RandomForestClassifier(random_state=42)

grid_search = GridSearchCV(
    rf, 
    param_grid, 
    cv=5, 
    scoring='accuracy',
    n_jobs=-1,
    verbose=1
)

grid_search.fit(X_train_scaled, y_train)

print(f"Mejores parámetros: {grid_search.best_params_}")
print(f"Mejor score: {grid_search.best_score_:.4f}")

# Usar el mejor modelo
mejor_modelo = grid_search.best_estimator_
```

---

## Proyectos Prácticos

### Proyecto 1: Análisis de Ventas

```python
"""
Proyecto: Análisis de Datos de Ventas
Objetivo: Analizar tendencias de ventas y generar insights
"""
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

# Crear datos de ejemplo
np.random.seed(42)
fechas = pd.date_range('2023-01-01', periods=365, freq='D')
ventas = pd.DataFrame({
    'fecha': fechas,
    'producto': np.random.choice(['A', 'B', 'C', 'D'], 365),
    'region': np.random.choice(['Norte', 'Sur', 'Este', 'Oeste'], 365),
    'cantidad': np.random.randint(10, 100, 365),
    'precio_unitario': np.random.uniform(10, 50, 365)
})
ventas['total'] = ventas['cantidad'] * ventas['precio_unitario']
ventas['mes'] = ventas['fecha'].dt.month
ventas['dia_semana'] = ventas['fecha'].dt.day_name()

# Análisis
print("=== RESUMEN DE VENTAS ===")
print(f"Total de ventas: ${ventas['total'].sum():,.2f}")
print(f"Venta promedio: ${ventas['total'].mean():,.2f}")
print(f"Venta máxima: ${ventas['total'].max():,.2f}")

# Ventas por producto
ventas_producto = ventas.groupby('producto')['total'].sum().sort_values(ascending=False)
print(f"\nVentas por producto:\n{ventas_producto}")

# Visualización
fig, axes = plt.subplots(2, 2, figsize=(14, 10))

# Ventas por mes
ventas_mes = ventas.groupby('mes')['total'].sum()
axes[0, 0].bar(ventas_mes.index, ventas_mes.values)
axes[0, 0].set_title('Ventas por Mes')
axes[0, 0].set_xlabel('Mes')
axes[0, 0].set_ylabel('Total Ventas ($)')

# Ventas por producto
ventas_producto.plot(kind='bar', ax=axes[0, 1], color='green')
axes[0, 1].set_title('Ventas por Producto')
axes[0, 1].tick_params(axis='x', rotation=0)

# Ventas por región
ventas_region = ventas.groupby('region')['total'].sum()
axes[1, 0].pie(ventas_region.values, labels=ventas_region.index, autopct='%1.1f%%')
axes[1, 0].set_title('Distribución por Región')

# Tendencia de ventas
ventas_diarias = ventas.groupby('fecha')['total'].sum()
axes[1, 1].plot(ventas_diarias.index, ventas_diarias.values, alpha=0.7)
axes[1, 1].set_title('Tendencia de Ventas Diarias')
axes[1, 1].tick_params(axis='x', rotation=45)

plt.tight_layout()
plt.savefig('analisis_ventas.png', dpi=300)
plt.show()
```

### Proyecto 2: Predicción con Machine Learning

```python
"""
Proyecto: Clasificación de Clientes
Objetivo: Predecir si un cliente realizará una compra
"""
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split, cross_val_score
from sklearn.preprocessing import StandardScaler, LabelEncoder
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import classification_report, confusion_matrix, roc_curve, auc
import matplotlib.pyplot as plt
import seaborn as sns

# Crear dataset de ejemplo
np.random.seed(42)
n_samples = 1000

datos_clientes = pd.DataFrame({
    'edad': np.random.randint(18, 70, n_samples),
    'ingreso_anual': np.random.normal(50000, 20000, n_samples),
    'visitas_web': np.random.randint(1, 50, n_samples),
    'tiempo_sitio': np.random.uniform(1, 30, n_samples),
    'productos_vistos': np.random.randint(1, 20, n_samples),
    'compras_previas': np.random.randint(0, 10, n_samples)
})

# Variable objetivo (compra = 1, no compra = 0)
probabilidad = (
    0.01 * datos_clientes['ingreso_anual'] / 1000 +
    0.02 * datos_clientes['visitas_web'] +
    0.03 * datos_clientes['productos_vistos'] +
    0.1 * datos_clientes['compras_previas']
)
datos_clientes['compra'] = (probabilidad + np.random.randn(n_samples) * 2 > 2).astype(int)

# Preparar datos
X = datos_clientes.drop('compra', axis=1)
y = datos_clientes['compra']

# División y escalado
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)

# Entrenar modelo
modelo = RandomForestClassifier(n_estimators=100, random_state=42)
modelo.fit(X_train_scaled, y_train)

# Evaluación
y_pred = modelo.predict(X_test_scaled)
y_prob = modelo.predict_proba(X_test_scaled)[:, 1]

print("=== RESULTADOS DEL MODELO ===")
print(f"\nAccuracy: {modelo.score(X_test_scaled, y_test):.4f}")
print("\nClassification Report:")
print(classification_report(y_test, y_pred))

# Validación cruzada
cv_scores = cross_val_score(modelo, X_train_scaled, y_train, cv=5)
print(f"\nCV Scores: {cv_scores}")
print(f"CV Mean: {cv_scores.mean():.4f} (+/- {cv_scores.std()*2:.4f})")

# Importancia de características
importancias = pd.DataFrame({
    'caracteristica': X.columns,
    'importancia': modelo.feature_importances_
}).sort_values('importancia', ascending=False)

print("\nImportancia de Características:")
print(importancias)

# Visualizaciones
fig, axes = plt.subplots(1, 3, figsize=(15, 5))

# Matriz de confusión
cm = confusion_matrix(y_test, y_pred)
sns.heatmap(cm, annot=True, fmt='d', cmap='Blues', ax=axes[0])
axes[0].set_title('Matriz de Confusión')
axes[0].set_xlabel('Predicho')
axes[0].set_ylabel('Real')

# Curva ROC
fpr, tpr, _ = roc_curve(y_test, y_prob)
roc_auc = auc(fpr, tpr)
axes[1].plot(fpr, tpr, label=f'ROC (AUC = {roc_auc:.2f})')
axes[1].plot([0, 1], [0, 1], 'k--')
axes[1].set_xlabel('False Positive Rate')
axes[1].set_ylabel('True Positive Rate')
axes[1].set_title('Curva ROC')
axes[1].legend()

# Importancia de características
axes[2].barh(importancias['caracteristica'], importancias['importancia'])
axes[2].set_xlabel('Importancia')
axes[2].set_title('Importancia de Características')

plt.tight_layout()
plt.savefig('modelo_clasificacion.png', dpi=300)
plt.show()
```

---

## Recursos Adicionales

### Bibliotecas Recomendadas

| Biblioteca | Uso |
|------------|-----|
| **NumPy** | Computación numérica |
| **Pandas** | Manipulación de datos |
| **Matplotlib** | Visualización básica |
| **Seaborn** | Visualización estadística |
| **Scikit-learn** | Machine Learning |
| **Statsmodels** | Estadística y econometría |
| **SciPy** | Computación científica |
| **Plotly** | Visualizaciones interactivas |

### Datasets para Practicar

1. **Kaggle**: [kaggle.com/datasets](https://www.kaggle.com/datasets)
2. **UCI ML Repository**: [archive.ics.uci.edu/ml](https://archive.ics.uci.edu/ml)
3. **Google Dataset Search**: [datasetsearch.research.google.com](https://datasetsearch.research.google.com)
4. **Datasets de Seaborn**: `sns.load_dataset('nombre')`
5. **Datasets de Sklearn**: `from sklearn.datasets import load_iris`

### Cursos y Tutoriales

- [Python for Data Science - freeCodeCamp](https://www.freecodecamp.org/)
- [Data Science Handbook - Jake VanderPlas](https://jakevdp.github.io/PythonDataScienceHandbook/)
- [Kaggle Learn](https://www.kaggle.com/learn)
- [Real Python](https://realpython.com/)

### Buenas Prácticas

1. **Documentación**: Siempre documenta tu código y análisis
2. **Reproducibilidad**: Usa semillas aleatorias (`random_state`)
3. **Validación**: Siempre valida tus modelos con datos no vistos
4. **Visualización**: Un buen gráfico vale más que mil números
5. **Versionado**: Usa Git para versionar tu código y datos

---

## 🎯 Conclusión

Este tutorial te ha proporcionado las bases fundamentales para comenzar tu camino en Data Science con Python. Recuerda que la práctica constante es la clave del éxito. ¡Sigue experimentando con diferentes datasets y técnicas!

---

## 📝 Ejercicios Recomendados

1. **Principiante**: Carga un dataset de tu interés y realiza un EDA completo
2. **Intermedio**: Implementa un modelo de clasificación para el dataset Titanic
3. **Avanzado**: Construye un pipeline completo de ML con optimización de hiperparámetros

---

*Tutorial creado para la comunidad de estudiantes de informática. ¡Happy coding!* 🚀
