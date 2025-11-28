# Introducción a Lógica Difusa (Fuzzy Logic)

Este repositorio contiene material educativo y ejemplos prácticos sobre sistemas de lógica difusa, implementados con Python y la librería scikit-fuzzy.

## Tabla de Contenidos

- [Conceptos Fundamentales](#conceptos-fundamentales)
- [Arquitectura de un Sistema Difuso](#arquitectura-de-un-sistema-difuso)
- [Componentes Técnicos](#componentes-técnicos)
- [Implementación con SciKit-Fuzzy](#implementación-con-scikit-fuzzy)
- [Ejemplos Incluidos](#ejemplos-incluidos)
- [Requisitos](#requisitos)
- [Ejecución](#ejecución)

## Conceptos Fundamentales

### ¿Qué es la Lógica Difusa?

La lógica difusa es una extensión de la lógica booleana que permite trabajar con valores de verdad graduales entre 0 y 1, en lugar de los valores binarios tradicionales (verdadero/falso). Esto permite modelar el razonamiento humano y la incertidumbre de manera más natural.
 
### ¿Cuándo usar Lógica Difusa?

- Cuando el conocimiento experto es lingüístico más que numérico
- Sistemas con múltiples entradas y salidas interdependientes
- Problemas con fronteras difusas entre categorías
- Control de sistemas complejos no lineales

## Arquitectura de un Sistema Difuso

Un sistema de inferencia difusa típico consta de cuatro etapas principales:

```
[Entrada Numérica] → [Fuzzificación] → [Motor de Inferencia] → [Defuzzificación] → [Salida Numérica]
```

### 1. Fuzzificación

Convierte valores numéricos de entrada en grados de pertenencia a conjuntos difusos.

**Ejemplo:** La temperatura de 24°C puede pertenecer simultáneamente a:
- "Ambiente" con un grado de 0.7
- "Calurosa" con un grado de 0.3

### 2. Motor de Inferencia

Aplica reglas difusas del tipo "SI-ENTONCES" para determinar las salidas difusas basándose en las entradas difusificadas.

**Ejemplo de regla:**
```
SI la calidad es "excelente" Y la atención es "joya" ENTONCES la propina es "extra"
```

### 3. Defuzzificación

Convierte la salida difusa en un valor numérico concreto usando métodos como:
- Centroide (centro de gravedad)
- Bisector
- Media de los máximos
- Menor/mayor de los máximos

## Componentes Técnicos

### Variables Difusas

#### Antecedentes (Entradas)
Variables que representan las condiciones del sistema:
- **Universo de discurso:** Rango de valores numéricos posibles
- **Términos lingüísticos:** Categorías difusas (ej: "bajo", "medio", "alto")
- **Funciones de membresía:** Definen cómo se mapean valores numéricos a grados de pertenencia

#### Consecuentes (Salidas)
Variables que representan las acciones o decisiones del sistema con la misma estructura que los antecedentes.

### Funciones de Membresía

Definen la forma en que un valor numérico pertenece a un conjunto difuso. Tipos comunes:

| Tipo | Función | Uso Típico |
|------|---------|------------|
| **Triangular** | `trimf(x, [a, b, c])` | Categorías intermedias bien definidas |
| **Trapezoidal** | `trapmf(x, [a, b, c, d])` | Categorías con mesetas |
| **Gaussiana** | `gaussmf(x, mean, sigma)` | Distribuciones naturales suaves |
| **Sigmoidal** | `smf(x, a, b)` / `zmf(x, a, b)` | Transiciones suaves en extremos |
| **Pi** | `pimf(x, a, b, c, d)` | Forma de campana con meseta |

### Operadores Difusos

**Conectores lógicos:**
- **AND (`&`):** Mínimo (intersección)
- **OR (`|`):** Máximo (unión)
- **NOT (`~`):** Complemento (1 - membresía)

### Reglas Difusas

Estructura: `SI <antecedentes> ENTONCES <consecuentes>`

Los antecedentes se pueden combinar con operadores lógicos:
```python
regla1 = ctrl.Rule(temperatura['alta'] & humedad['alta'], ventilador['rápido'])
regla2 = ctrl.Rule(temperatura['baja'] | modo['apagado'], ventilador['lento'])
```

## Implementación con SciKit-Fuzzy

### Estructura Básica

```python
import numpy as np
import skfuzzy as fuzz
from skfuzzy import control as ctrl

# 1. Definir antecedentes
temperatura = ctrl.Antecedent(np.arange(0, 41, 1), 'temperatura')
humedad = ctrl.Antecedent(np.arange(0, 101, 1), 'humedad')

# 2. Definir consecuentes
velocidad = ctrl.Consequent(np.arange(0, 101, 1), 'velocidad')

# 3. Definir funciones de membresía
temperatura['fría'] = fuzz.trimf(temperatura.universe, [0, 0, 20])
temperatura['templada'] = fuzz.trimf(temperatura.universe, [15, 25, 35])
temperatura['caliente'] = fuzz.trimf(temperatura.universe, [30, 40, 40])

# 4. Auto-generación de membresías (3, 5 o 7 categorías)
velocidad.automf(3)  # Genera: 'poor', 'average', 'good'
velocidad.automf(names=['lenta', 'media', 'rápida'])  # Personalizado

# 5. Definir reglas
regla1 = ctrl.Rule(temperatura['caliente'] & humedad['alta'], velocidad['rápida'])
regla2 = ctrl.Rule(temperatura['fría'], velocidad['lenta'])

# 6. Crear sistema de control
sistema = ctrl.ControlSystem([regla1, regla2])
simulacion = ctrl.ControlSystemSimulation(sistema)

# 7. Calcular salida
simulacion.input['temperatura'] = 30
simulacion.input['humedad'] = 70
simulacion.compute()

print(f"Velocidad: {simulacion.output['velocidad']}%")

# 8. Visualizar
temperatura.view(sim=simulacion)
velocidad.view(sim=simulacion)
```

### Métodos Importantes

| Método | Descripción |
|--------|-------------|
| `.universe` | Retorna el universo de discurso (array de valores) |
| `.automf(n)` | Genera automáticamente n funciones de membresía triangulares |
| `.view()` | Visualiza las funciones de membresía |
| `.view(sim=...)` | Visualiza con los valores de una simulación específica |

## Ejemplos Incluidos

### 1. Sistema de Propinas (02_SciKit_Fuzzy.ipynb)

**Problema:** Determinar la propina a dejar en un restaurante basándose en la calidad de la comida y la atención.

**Variables de Entrada:**
- **Calidad de la comida** (escala 1-7):
  - Categorías: pésima, malita, piola, de pana, basadísimo
- **Atención del mesero** (escala 1-7):
  - Categorías: amarga, pesada, noesni, tela, joya

**Variable de Salida:**
- **Propina** (0-15%):
  - Categorías: nada, poca, normal, extra

**Reglas de Ejemplo:**
```
SI calidad es "pésima" Y atención es "amarga" ENTONCES propina es "nada"
SI calidad es "basadísimo" Y atención es "joya" ENTONCES propina es "extra"
SI calidad es "de pana" O atención es "tela" ENTONCES propina es "normal"
```

**Caso de Prueba:**
- Entrada: calidad = 4.32, atención = 5.123
- Salida: propina ≈ 8.1%

### 2. Ejercicio: Sistema de Aire Acondicionado

**Problema:** Controlar un aire acondicionado en modo AUTO considerando temperatura actual y deseada.

**Especificaciones:**
- Rango de operación: 17°C - 30°C
- Modos: frío (13°C), calor (42°C), stand by
- Velocidades: baja [0.1-0.7 °C/min], media [0.5-1.2 °C/min], alta [0.8-1.9 °C/min]

**Categorías de temperatura:**
- Fría: < 15°C
- Fresca: 12.5°C - 20°C
- Ambiente: 18°C - 25°C
- Calurosa: > 23°C

**Reglas de control:**
- Diferencia negativa → modo calor
- Diferencia positiva → modo frío
- Sin diferencia → stand by
- Muy alejado → velocidad alta
- Cercano/en temperatura → no usar velocidad alta

## Requisitos

```bash
pip install numpy scikit-fuzzy matplotlib
```

**Dependencias:**
- `numpy >= 1.x`: Operaciones numéricas
- `scikit-fuzzy >= 0.4.x`: Sistema de lógica difusa
- `matplotlib >= 3.x`: Visualización de funciones de membresía

## Ejecución

### Notebooks Jupyter

```bash
jupyter notebook 02_SciKit_Fuzzy.ipynb
```

### Desde Python

```python
# Importar y ejecutar ejemplos
from skfuzzy import control as ctrl
# ... (código del ejemplo)
```

## Proceso de Fuzzificación y Defuzzificación

### ¿Cuándo ocurre la Fuzzificación?

La fuzzificación ocurre cuando se asignan valores numéricos a las entradas del sistema:

```python
simulacion.input['calidad'] = 4.32      # Valor numérico
simulacion.input['atención'] = 5.123    # Valor numérico
```

Internamente, el sistema convierte estos valores en grados de pertenencia a cada conjunto difuso.

### ¿Cuándo ocurre la Defuzzificación?

La defuzzificación ocurre al ejecutar:

```python
simulacion.compute()  # Aquí se aplican las reglas y se defuzzifica
print(simulacion.output['propina'])  # Valor numérico resultante
```

El método por defecto es el **centroide** (centro de gravedad del área bajo la función de membresía resultante).

## Ventajas de la Lógica Difusa

1. **Modelado intuitivo:** Usa lenguaje natural (bajo, medio, alto)
2. **Robustez:** Maneja incertidumbre y datos imprecisos
3. **Flexibilidad:** Fácil agregar/modificar reglas sin rediseñar el sistema
4. **Control no lineal:** Excelente para sistemas complejos
5. **Menor complejidad:** Menos reglas que sistemas expertos tradicionales

## Recursos Adicionales

- [Documentación SciKit-Fuzzy](https://pythonhosted.org/scikit-fuzzy/)
- [API Reference - Membership Functions](https://pythonhosted.org/scikit-fuzzy/api/skfuzzy.membership.html)
- [Fuzzy Logic Toolbox - MathWorks](https://www.mathworks.com/help/fuzzy/)

## Estructura del Repositorio

```
.
├── README.md                      # Este archivo
├── 02_SciKit_Fuzzy.ipynb         # Tutorial principal con ejemplo de propinas
├── 02_SistemasDifusos.ipynb      # Teoría de sistemas difusos
└── images/                        # Diagramas y visualizaciones
    ├── fuzzylogic_system.png
    └── fuzzy_steps.png
```

## Autor

Material educativo - Universidad Técnica Federico Santa María
Curso: Soft Computing

---

**Nota:** Este material es con fines educativos para comprender los fundamentos de la lógica difusa y su implementación práctica en Python.
