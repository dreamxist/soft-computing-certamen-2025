# Clase 04: Introducción a Redes Neuronales y Deep Learning

**Autores:** Dr. Nicolás Gálvez Ramírez, Dr. Patricio Olivares Roncagliolo
**Universidad:** Universidad Técnica Federico Santa María
**Fuentes:** "Hands-on Machine Learning with Scikit-Learn, Keras & TensorFlow", "Stanford - CS229 Notes"

---

## 1. Machine Learning - Conceptos Fundamentales

### ¿Qué es Machine Learning?
Conjunto de técnicas a través de los cuales un computador puede aprender de los datos.

### Bibliotecas principales en Python
- **Scikit-Learn**: Biblioteca de código libre para ML con múltiples algoritmos supervisados y no supervisados
- **TensorFlow/Keras**: Para Deep Learning
- **PyTorch**: Alternativa para Deep Learning

### Objetos de Scikit-Learn
- **Estimadores**: Método `fit()` para estimación de parámetros
- **Transformadores**: Método `transform()` para transformación de datos
- **Predictores**: Método `predict()` para realizar predicciones

---

## 2. División de Datos y Evaluación

### Training/Test Split
- **Training set**: Datos para entrenar el modelo
- **Test set**: Datos para evaluar la generalización

```python
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=42, stratify=y)
```

### Tipos de Errores
- **Falso Positivo (Tipo 1)**: Clasificar como positivo incorrectamente
- **Falso Negativo (Tipo 2)**: Clasificar como negativo incorrectamente

### Matriz de Confusión
Permite contar la cantidad de veces que instancias de la clase A son clasificadas como clase B.
- TP: True Positives
- TN: True Negatives
- FP: False Positives
- FN: False Negatives

---

## 3. Métricas de Clasificación

### Accuracy (Exactitud)
$$\text{accuracy} = \frac{TP + TN}{TP + TN + FP + FN}$$

**Limitación:** Asume que las clases son comparables en cantidad (puede ser engañoso con datos desbalanceados).

### Sensitivity/Recall (Sensibilidad)
$$\text{sensitivity} = \frac{TP}{TP + FN}$$

### Specificity (Especificidad)
$$\text{specificity} = \frac{TN}{TN + FP}$$

### Precision (Precisión)
$$\text{precision} = \frac{TP}{TP + FP}$$

---

## 4. Perceptrón

Red neuronal más sencilla basada en TLU (Threshold Logic Unit):

1. Recibe entradas numéricas: $\mathbf{x}=(x_1, x_2, ..., x_n)$
2. Pondera por pesos: $\mathbf{w}=(w_1, w_2, ..., w_n)$
3. Calcula suma ponderada: $z = \mathbf{x}^T \mathbf{w}$
4. Aplica función de activación: $\hat{y} = h(z)$

```python
from sklearn.linear_model import Perceptron
per = Perceptron()
per.fit(X_train, y_train)
```

---

## 5. Gradient Descent (Descenso del Gradiente)

Algoritmo para calcular el mínimo local de una función de costo.

### Función de Costo (SSE)
$$J(\mathbf{w}) = \frac{1}{2} \sum_{i=1}^{m} \left( h(\mathbf{w}^T \mathbf{x}^{(i)}) - y^{(i)} \right)^2$$

### Regla de Actualización
$$w_j := w_j - \alpha \nabla_{w_j}J(\mathbf{w})$$

---

## 6. Backpropagation

- Algoritmo para calcular gradientes usando la regla de la cadena
- Las derivadas se "propagan hacia atrás" reutilizando cálculos de capas posteriores
- Permite entrenar redes con múltiples capas eficientemente

---

## 7. Redes Multicapas y Deep Learning

### Estructura
- **Capa de entrada** (input layer)
- **Capas ocultas** (hidden layers)
- **Capa de salida** (output layer)

**Deep Neural Network (DNN):** Red con muchas capas ocultas.

---

## 8. Funciones de Activación

### Linear
$$\text{Linear}(x) = x$$

### Sigmoide
$$\sigma(x) = \frac{1}{1 + e^{-x}}$$
*Útil para clasificación (valores entre 0 y 1)*

### ReLU (Rectified Linear Unit)
$$\text{ReLU}(x) = \max(0, x)$$
*Popular en capas intermedias*

### Leaky ReLU
$$\text{LeakyReLU}(x) = \begin{cases} x, & \text{si } x > 0 \\ \alpha x, & \text{si } x \leq 0 \end{cases}$$

### Tanh (Tangente Hiperbólica)
$$\tanh(x) = \frac{e^x - e^{-x}}{e^x + e^{-x}}$$

### Softmax
$$\text{softmax}(x_i) = \frac{e^{x_i}}{\sum_{j=1}^{N} e^{x_j}}$$
*Para clasificación multiclase*

---

## 9. Creación de Redes en Keras/TensorFlow

### Pasos
1. Definir arquitectura (modelo y capas)
2. Compilar modelo (optimizador, función de pérdida, métricas)
3. Entrenar modelo
4. Usar

### Ejemplo - Clasificación Multiclase (Fashion MNIST)

```python
import tensorflow as tf

model = tf.keras.models.Sequential()
model.add(tf.keras.layers.Flatten(input_shape=[28, 28]))  # Capa entrada
model.add(tf.keras.layers.Dense(300, activation='relu'))   # Capa oculta 1
model.add(tf.keras.layers.Dense(100, activation='relu'))   # Capa oculta 2
model.add(tf.keras.layers.Dense(10, activation='softmax')) # Capa salida

model.compile(loss="sparse_categorical_crossentropy",
              optimizer="sgd",
              metrics=["accuracy"])

history = model.fit(X_train, y_train, epochs=30, validation_data=(X_val, y_val))
```

**Notas importantes:**
- Para clasificación multiclase: tantas neuronas en salida como clases
- Función de activación de salida: **softmax**
- Función de pérdida: **sparse_categorical_crossentropy**

---

## 10. Regularización en Redes Neuronales

### Problema
Las redes neuronales son propensas al **overfitting** debido a su gran cantidad de parámetros.

### Técnicas de Regularización

#### Regularización L1
$$J = \frac{1}{m}\sum_{i=1}^{m} \left( \hat{y}^{(i)} - y^{(i)} \right)^{2} + \alpha \frac{1}{n} \sum_{j=1}^{n} |w_j|$$

#### Regularización L2
$$J = \frac{1}{m}\sum_{i=1}^{m} \left( \hat{y}^{(i)} - y^{(i)} \right)^{2} + \alpha \frac{1}{2n} \sum_{j=1}^{n} w_j^2$$

```python
from tensorflow.keras.regularizers import l2
model.add(Dense(19, activation='relu', kernel_regularizer=l2(0.01)))
```

#### Dropout
Cada neurona tiene probabilidad $p$ de ser excluida en cada iteración.
- **40%-50%** para redes convolucionales
- **20%-30%** para redes recurrentes
- **10%-50%** para otras redes

```python
from tensorflow.keras.layers import Dropout
model.add(Dropout(0.3))
```

#### Early Stopping
Detener entrenamiento cuando el error de validación deja de mejorar.

```python
from tensorflow.keras.callbacks import EarlyStopping
early_stopping = EarlyStopping(patience=5)
model.fit(X_train, y_train, callbacks=[early_stopping])
```

---

## 11. Redes Neuronales Recurrentes (RNN)

### Características
- Conexiones en sentido inverso (además de feedforward)
- Procesan **secuencias de datos** temporalmente relacionados
- Toman en cuenta información previa

### Unidades de Procesamiento
- **LSTM** (Long Short Term Memory)
- **GRU** (Gated Recurrent Unit)

### Arquitecturas
- **Many to One**: Múltiples entradas → una salida
- **One to Many**: Una entrada → múltiples salidas
- **Many to Many**: Múltiples entradas → múltiples salidas

### Aplicaciones
- Predicción de series temporales
- NLP (Natural Language Processing)

### Ejemplo LSTM

```python
from tensorflow.keras.layers import Dense, LSTM

model = Sequential()
model.add(LSTM(units=50, return_sequences=False))
model.add(Dense(units=1, activation='relu'))
model.compile(loss='mean_squared_error', optimizer='adam')
```

---

## 12. Redes Neuronales Convolucionales (CNN)

### Características
- Procesan datos **espacialmente relacionados**
- Utilizadas principalmente para **imágenes**
- Pixeles cercanos están correlacionados

### Operación de Convolución
$$(f * g)(t) = \int_{-\infty}^{\infty} f(\tau) \cdot g(t-\tau)d\tau$$

Filtros convolucionales 2D aplicados sobre imágenes.

### Capas de Pooling
Reducen dimensionalidad y varianza espacial.

- **Max Pooling**: Obtiene el máximo de cada zona
- **Average Pooling**: Obtiene el promedio de cada zona

### Ejemplo CNN (CIFAR-10)

```python
from tensorflow.keras import layers, models

model = models.Sequential()
# Capas convolucionales
model.add(layers.Conv2D(32, (3, 3), activation='relu', input_shape=(32, 32, 3)))
model.add(layers.MaxPooling2D((2, 2)))
model.add(layers.Conv2D(64, (3, 3), activation='relu'))
model.add(layers.MaxPooling2D((2, 2)))
model.add(layers.Conv2D(64, (3, 3), activation='relu'))
# Capas de clasificación
model.add(layers.Flatten())
model.add(layers.Dense(64, activation='relu'))
model.add(layers.Dense(10, activation='softmax'))

model.compile(optimizer='sgd',
              loss='sparse_categorical_crossentropy',
              metrics=['accuracy'])
```

---

## 13. Datasets Utilizados en la Clase

| Dataset | Descripción | Uso |
|---------|-------------|-----|
| `breast_cancer` | Diagnóstico de cáncer | Clasificación binaria |
| `titanic.csv` | Supervivientes del Titanic | Clasificación binaria |
| `fashion_mnist` | Imágenes de ropa | Clasificación multiclase |
| `nba.csv` | Estadísticas NBA | Clasificación (5 años carrera) |
| `sunspots.csv` | Manchas solares | Series temporales |
| `cifar10` | Imágenes variadas | Clasificación multiclase (CNN) |

---

## 14. Resumen de Hiperparámetros Importantes

| Parámetro | Descripción | Valores típicos |
|-----------|-------------|-----------------|
| `epochs` | Iteraciones de entrenamiento | 10-100+ |
| `batch_size` | Muestras por actualización | 32, 64, 128 |
| `learning_rate` | Tasa de aprendizaje | 0.001, 0.01 |
| `dropout_rate` | Probabilidad dropout | 0.2-0.5 |
| `l2_lambda` | Factor regularización L2 | 0.01, 0.001 |
| `patience` | Épocas sin mejora (early stop) | 5-10 |

---

## 15. Flujo de Trabajo Típico

1. **Cargar y explorar datos**
2. **Preprocesamiento**: escalamiento, encoding, limpieza
3. **División**: train/validation/test
4. **Definir arquitectura** del modelo
5. **Compilar**: loss, optimizer, metrics
6. **Entrenar** con datos de training y validación
7. **Evaluar** con datos de test
8. **Ajustar** hiperparámetros si es necesario
