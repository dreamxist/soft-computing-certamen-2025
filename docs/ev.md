# Resumen Clase 03: Introducción a la Computación Evolutiva

## 1. Método de la Caja Negra

Sistema compuesto por:
- **Entrada**: Información de entrada del sistema
- **Modelo**: Transforma/utiliza la entrada
- **Salida**: Resultado generado

### Tipos de Problemas

| Tipo | Conocido | Desconocido | Objetivo |
|------|----------|-------------|----------|
| **Optimización** | Modelo, Salida esperada | Entrada | Encontrar configuración de entrada |
| **Modelado** | Entradas, Salidas | Modelo | Aprender relación entrada-salida |
| **Simulación** | Entrada, Modelo | Salida | Predecir/calcular salida |

---

## 2. Optimización Combinatoria

> Campo de la matemática que busca encontrar el objeto óptimo dentro de un conjunto finito de objetos.

### Tipos de Problemas de Búsqueda

1. **FOP (Free Optimisation Problem)**: Optimiza función sin restricciones
2. **CSP (Constraint Satisfaction Problem)**: Cumple restricciones sin optimizar
3. **CSOP/COP (Constraint Optimisation Problem)**: Optimiza Y cumple restricciones

### Conceptos de Búsqueda

- **Espacio de Búsqueda**: Conjunto de todas las posibles soluciones
- **Función de Evaluación**: Cuantifica y compara candidatos (fitness, objetivo)
- **Restricciones**: Condiciones que debe satisfacer una solución

---

## 3. Paradigmas de Búsqueda

### Aproximación Sistémica vs Búsqueda Local

| Sistémica | Local |
|-----------|-------|
| Completa | Incompleta |
| Garantiza óptimo o inexistencia | No garantiza óptimo |
| Computacionalmente costosa | Computacionalmente rápida |

### Constructiva vs Perturbativa

- **Constructiva**: Construye solución desde cero
- **Perturbativa**: Modifica solución existente

---

## 4. Heurísticas y Metaheurísticas

### Heurísticas
- Directrices basadas en conocimiento del problema
- Función miope (no ve más allá del criterio)
- Ejemplo: Algoritmo Greedy

### Metaheurísticas
> Proceso automático para buscar una buena solución respecto a un conjunto de candidatos finito pero intratable.

**Conceptos clave:**
- **Explotación/Intensificación**: Concentrar búsqueda en zona visitada
- **Exploración/Diversificación**: Ampliar búsqueda a mayor espacio
- **Vecindario**: Soluciones generadas desde candidato aplicando movimiento
- **Movimiento**: Perturbación aplicable al candidato

**Metaheurísticas clásicas:**
- Hill-Climbing
- Iterative Local Search
- Tabú Search
- Simulated Annealing
- GRASP

---

## 5. Computación Evolutiva

> Metaheurísticas bio-inspiradas basadas en la **Teoría de la Evolución y Selección Natural** de Darwin.

### Características
- Búsqueda con múltiples candidatos simultáneos (población)
- Perturbación con operadores evolutivos
- Gran auge desde los 90s

---

## 6. Framework de Algoritmos Evolutivos

### Representación

| Concepto | Descripción |
|----------|-------------|
| **Fenotipo** | Solución en el mundo real |
| **Genotipo** | Solución en el mundo evolutivo (cromosoma) |
| **Gen** | Elemento/variable de la solución |
| **Alelo** | Valor en un gen |
| **Codificar** | Mundo real → mundo evolutivo |
| **Decodificar** | Mundo evolutivo → mundo real |

### Función de Fitness
- Mide calidad de genotipos
- Mide adaptación de individuos al ambiente
- Se evalúa decodificando al espacio de fenotipos

### Población
- Multiconjunto de candidatos a solución
- Individuos estáticos, poblaciones dinámicas
- Tamaño generalmente constante
- **Diversidad**: Medida de dispersión de soluciones diferentes

---

## 7. Operadores Evolutivos

### Selección de Padres
Filtrar miembros según calidad para ser padres.

**Métodos:**
- Aleatoria
- Ranking
- Torneos
- Ruleta (Roulette-Wheel)

### Mutación
- Perturbación aplicada a **un padre** → genera **un hijo**
- Operación unaria y estocástica
- Conecta distintos sectores del espacio de búsqueda

**Tipos:** Bit-flip, swap, inversión, etc.

### Recombinación/Cruzamiento
- Mezcla información genética de **dos padres** → uno o dos hijos
- Estocástica (probabilidad de recombinación)
- Punto de cruzamiento variable

### Supervivencia
- **Elitismo**: Mantener mejores individuos
- **Ranking de Fitness**: Ordenar por fitness, seleccionar top n
- **Edad**: Eliminar individuos según generaciones vividas

---

## 8. Criterios de Inicio y Término

### Inicio
- Población inicial (generalmente aleatoria)
- Se recomienda alta diversidad inicial

### Término
- **Convergencia**: % población con mismo fitness
- Tiempo sin mejoras
- Total de evaluaciones fitness
- Número de iteraciones
- Tiempo de ejecución

---

## 9. Tipos de Algoritmos Evolutivos

### Programación Evolutiva (EP)
- Fogel et al. (1966)
- Sin recombinación (cada individuo es especie diferente)
- Mutación gaussiana
- Auto-adaptación vía mutación

### Estrategias de Evolución (ES)
- Rechenberg y Schwefel (1960s-70s)
- Con recombinación
- Estrategias (μ, λ) y (μ + λ)
- Calibración de parámetros on-line

### Algoritmos Genéticos (GA)
- Holland (1973), De Jong (1975)
- Representación: Bit-vector
- Cruzamiento a 1 punto + Bit-flip mutation
- Selección: Roulette-Wheel
- Supervivencia por edad

### Programación Genética (GP)
- Koza (1990)
- Representación: Árboles
- Intercambio de sub-árboles
- Uso: lenguajes, código, expresiones

---

## 10. Ejemplo: Problema de las N-Reinas

> Ubicar n reinas en tablero n×n sin que hagan jaque entre ellas.

### Representación optimizada
- **Vector de enteros** de tamaño n
- Gen: columna del tablero
- Alelo: fila donde está la reina
- Ventaja: elimina checks en columnas automáticamente

### Como FOP
- Función evaluación: Cantidad de reinas libres de check
- Solución: f(s) = n

### Como CSP
- Restricción: `is_checked(s) = false`

### Como CSOP
- Restricciones: no repetir fila ni columna
- Función evaluación: minimizar checks en diagonales

---

## 11. Implementación con DEAP (Python)

### Estructura básica

```python
from deap import creator, base, tools

# 1. Crear clase Fitness (minimizar = -1.0)
creator.create("Fitness", base.Fitness, weights=(-1.0,))

# 2. Crear clase Individuo
creator.create("Individuo", list, fitness=creator.Fitness)

# 3. Registrar funciones en toolbox
toolbox = base.Toolbox()
toolbox.register("fill_int", random.randint, 0, board_size-1)
toolbox.register("fill_ind", tools.initRepeat, creator.Individuo,
                 toolbox.fill_int, n=board_size)
toolbox.register("fill_pop", tools.initRepeat, list, toolbox.fill_ind)

# 4. Operadores
toolbox.register("seleccion", tools.selTournament, k=4, tournsize=4)
toolbox.register("rex", tools.cxTwoPoint)
toolbox.register("mux", tools.mutUniformInt, low=0, up=board_size-1)
```

### Función de Evaluación
```python
def evaluate(individual):
    check = 0
    # Contar checks en filas
    for i, val in enumerate(individual):
        check += individual[i+1:].count(val)
    # Contar checks en diagonales
    # ... (verificar diagonales)
    return (check,)  # Tupla!
```

### Flujo del algoritmo
1. Crear población inicial
2. Evaluar población
3. **Loop:**
   - Seleccionar padres
   - Clonar padres
   - Aplicar recombinación
   - Aplicar mutación
   - Evaluar hijos
   - Reemplazar (supervivencia)
4. Verificar criterio de término

---

## Referencias

- "Introduction to Evolutionary Computing" - Gusz Eiben & James Smith
- DEAP Documentation: https://deap.readthedocs.io/
