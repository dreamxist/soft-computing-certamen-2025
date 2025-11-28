# Resumen: Towards Enhanced Autonomous Driving Takeovers: Fuzzy Logic Perspective for Predicting Situational Awareness

**Autores:** Goran Ferenc, Dragoje Timotijević, Ivana Tanasijević, Danijela Simić
**Publicación:** Applied Sciences 2024, 14, 5697
**DOI:** https://doi.org/10.3390/app14135697

---

## 1. Objetivo del Estudio

Este paper investiga la aplicación de **lógica difusa (fuzzy logic)** para mejorar la conciencia situacional (Situational Awareness - SA) en Sistemas Avanzados de Asistencia al Conductor (ADAS). El objetivo es predecir si un conductor puede tomar el control del vehículo de manera efectiva durante las transiciones de conducción automatizada a manual.

---

## 2. Motivación

- Los modelos de **Machine Learning (ML)** tradicionales logran alta precisión pero tienen limitaciones:
  - Son computacionalmente intensivos
  - Requieren grandes datasets
  - Tienen poca interpretabilidad ("caja negra")

- La **lógica difusa** ofrece ventajas:
  - Maneja incertidumbre e imprecisión de manera natural
  - Es más interpretable
  - Puede procesar información más rápidamente
  - Funciona bien con datasets limitados

---

## 3. Metodología

### 3.1 Sistema de Inferencia Difusa Mamdani (MFIS)
El estudio utiliza un sistema difuso tipo Mamdani en cascada con las siguientes etapas:

1. **Fuzzificación:** Conversión de valores numéricos a conjuntos difusos
2. **Motor de Inferencia:** Aplicación de reglas difusas SI-ENTONCES
3. **Defuzzificación:** Conversión de salida difusa a valor numérico (método del centroide)

### 3.2 Variables Predictoras (14 en total)
Los predictores se agrupan en 4 categorías:

**Grupo Time-Decision:**
- videoLength (duración del video de simulación)
- decisionTime (tiempo de decisión del conductor)
- correctDecision (decisión correcta según la situación)
- decisionMade (decisión real tomada)

**Grupo Criticality:**
- difficulty (dificultad percibida de la situación)
- danger (nivel de peligro percibido)

**Grupo Eye (métricas oculares):**
- backMirror (uso del espejo retrovisor)
- road (atención a la carretera)
- pupilMean (diámetro promedio de la pupila)
- pupilChange (tasa de cambio del diámetro pupilar)
- fMean (duración promedio de fijación)
- fMax (duración máxima de fijación)

**Grupo Experience:**
- drivingFrequency (frecuencia de conducción)
- age (edad del conductor)

### 3.3 Arquitectura del Sistema FuzzyMain
Sistema en cascada de 3 niveles:

**Cascada 1:** Módulos especializados
- FuzzyDecision (4 inputs → 1 output)
- FuzzyCriticality (2 inputs → 1 output)
- FuzzyEye (6 inputs → 1 output)
- FuzzyExperience (2 inputs → 1 output)

**Cascada 2:** Combinación intermedia
- FuzzyDC: combina outputs de Decision y Criticality
- FuzzyEE: combina outputs de Eye y Experience

**Cascada 3:** Decisión final
- FuzzyAll: produce el valor final crispSA (Situational Awareness predicha)

---

## 4. Dataset

- **Fuente:** Delft University of Technology
- **Participantes:** 32 conductores (edad 22-29 años)
- **Videos:** 28 escenarios de conducción simulada (1-20 segundos)
- **Escenarios:**
  - No-hazard (velocidad constante)
  - Hazard (requiere evitar colisión)
- **Métrica SA:** Calculada a partir de 3 errores normalizados:
  - Error en número de vehículos
  - Error en distancia
  - Error en velocidad
- **Rango SA:** 0 (peor) a 1 (perfecto)
- **Clasificación:**
  - Low: SA < 0.3 (conductor no puede tomar control)
  - Middle: 0.3 ≤ SA ≤ 0.6 (puede tomar control con tiempo extra)
  - High: SA > 0.6 (listo para tomar control inmediato)

---

## 5. Resultados

### 5.1 Configuraciones Evaluadas
Se probaron 3 configuraciones del módulo FuzzyAll:

| Modelo | Input Sets | Output Sets | Reglas | RMSE | MAE | Correlación |
|--------|-----------|-------------|---------|------|-----|-------------|
| Fuzzy Logic 3-5 | 3 | 5 | 9 | 0.126 | 0.101 | 0.690 |
| Fuzzy Logic 3-7 | 3 | 7 | 9 | 0.137 | 0.113 | 0.700 |
| Fuzzy Logic 5-7 | 5 | 7 | 25 | 0.135 | 0.111 | 0.690 |

### 5.2 Comparación con Modelos ML

| Modelo | RMSE | MAE | Correlación |
|--------|------|-----|-------------|
| **Fuzzy Logic 3-5** | **0.126** | **0.101** | **0.690** |
| Medium Tree | 0.124 | 0.098 | 0.412 |
| Linear SVM | 0.122 | 0.097 | 0.436 |
| Linear Regression | 0.121 | 0.097 | 0.447 |
| Quadratic SVM | 0.121 | 0.094 | 0.458 |
| Gaussian SVM | 0.115 | 0.089 | 0.529 |
| Random Forest | 0.111 | 0.088 | 0.566 |
| XGBoost | 0.110 | 0.087 | 0.788 |
| LightGBM (all) | 0.109 | 0.086 | 0.796 |
| **LightGBM (best)** | **0.108** | **0.085** | **0.801** |

### 5.3 Eficiencia Computacional
- **Tiempo de ejecución:** ~4 ms por predicción
- **Plataforma:** Windows 10, CPU 4 GHz, 16 GB RAM, SSD
- Suficientemente rápido para aplicaciones en tiempo real

---

## 6. Ventajas de la Lógica Difusa

1. **Precisión comparable:** RMSE de 0.126 vs 0.108 del mejor modelo ML
2. **Interpretabilidad:** Reglas comprensibles y ajustables por expertos
3. **Eficiencia:** Tiempo de ejecución de 4 ms, adecuado para tiempo real
4. **Robustez:**
   - Funciona bien con datasets pequeños
   - Menos propenso a overfitting
   - Resistente a variaciones en los datos
5. **Simplicidad:** Menor complejidad computacional que ML durante inferencia

---

## 7. Limitaciones

- **RMSE ligeramente superior** al mejor modelo ML (0.126 vs 0.108)
- **Correlación menor** (0.690 vs 0.801)
- **Diseño manual:** Las reglas y funciones de membresía requieren conocimiento experto

---

## 8. Conclusiones

El sistema de lógica difusa propuesto:

✅ Logra precisión comparable a modelos ML avanzados
✅ Ofrece interpretabilidad superior
✅ Es eficiente para aplicaciones en tiempo real (4 ms)
✅ Funciona bien con datos limitados
✅ Proporciona un enfoque práctico y robusto para ADAS

**Implicación práctica:** La lógica difusa es una alternativa viable y eficiente para la predicción de conciencia situacional en vehículos autónomos, especialmente cuando la interpretabilidad y el tiempo real son críticos.

---

## 9. Trabajo Futuro

1. Integrar componentes del sistema difuso en sistemas híbridos con ML
2. Explorar diferentes configuraciones de reglas para subsistemas
3. Optimizar en plataformas embebidas
4. Validar en condiciones de conducción más diversas

---

## 10. Conceptos Técnicos Clave

### Funciones de Membresía
- **Trapezoidal:** Para categorías con mesetas
- **Triangular:** Para categorías intermedias
- **Gaussiana:** Para distribuciones suaves

### Operadores Difusos
- **AND:** Operador mínimo
- **OR:** Operador máximo
- **Agregación:** Operador máximo

### Defuzzificación
- **Método del centroide:** Centro de gravedad del área bajo la función de membresía

### Validación
- **10-fold cross-validation**
- **Métricas:** RMSE, MAE, Correlación

---

**Financiamiento:** EU AWARE2ALL project (Horizon Europe, Grant No. 101076868)
