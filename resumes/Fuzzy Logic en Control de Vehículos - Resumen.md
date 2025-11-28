# Resumen: Using Fuzzy Logic in Automated Vehicle Control

**Autores:** José E. Naranjo, Carlos González, Ricardo García, Teresa de Pedro, Miguel A. Sotelo
**Fuente:** IEEE Intelligent Systems, Enero/Febrero 2007

## Introducción

El paper presenta el programa AUTOPIA desarrollado en España, que utiliza lógica difusa para automatizar vehículos de producción en masa (Citroën Berlingo) en condiciones reales de carretera.

### Objetivos del Proyecto AUTOPIA:
1. Implementar conducción automática usando vehículos reales probados en carreteras reales
2. Desarrollar sistema modular con componentes aplicables inmediatamente a la industria automotriz

## Equipamiento de Vehículos Automatizados

**Vehículos:** Dos furgonetas eléctricas Citroën Berlingo

**Componentes principales:**
- Sistema de control basado en lógica difusa embebida
- Cámara CCD a color
- GPS diferencial de alta precisión (RTK DGPS)
- Computadora PC a bordo
- WLAN (red inalámbrica)
- Dos servomotores DC
- Tarjeta I/O analógica/digital
- Sistema de visión en computadora separada

## Sistema de Guiado con Lógica Difusa

### Razones para usar Lógica Difusa:
- Los modelos matemáticos de conducción son altamente complejos y no pueden linealizarse con precisión
- Método bien probado para este tipo de sistemas
- Proporciona buenos resultados
- Permite incorporar conocimiento procedural humano en algoritmos de control
- Permite imitar el comportamiento de conducción humana

## 1. Control de Dirección (Lateral)

**Variables de entrada fuzzy:**
- **Lateral_Error:** Distancia del vehículo a la trayectoria de referencia (izquierda/derecha)
- **Angular_Error:** Ángulo entre orientación del vehículo y vector de velocidad

**Variable de salida fuzzy:**
- **Steering_Wheel:** Dirección de giro del volante (izquierda/derecha, rango: -540° a 540°)

**Reglas fuzzy (solo 2 reglas simples):**
```
IF Angular_Error left OR Lateral_Error left THEN Steering_Wheel right
IF Angular_Error right OR Lateral_Error right THEN Steering_Wheel left
```

**Comportamiento adaptativo:**
- En carreteras rectas: alta velocidad, giros suaves del volante
- En curvas cerradas: reducción rápida de velocidad, giros rápidos del volante
- Se logra cambiando los parámetros de las funciones de pertenencia según la situación
- Las reglas permanecen constantes, pero las definiciones de valores lingüísticos cambian

## 2. Control de Velocidad (Longitudinal)

**Variables de entrada fuzzy:**
- **Speed_Error:** Diferencia entre velocidad real y velocidad objetivo
- **Acceleration:** Variación de velocidad en un intervalo de tiempo

**Variables de salida fuzzy:**
- **Throttle:** Presión del acelerador (2-4 voltios)
- **Brake:** Presión del freno (0-240 grados del motor de actuación)

**Reglas para control del acelerador:**
```
IF Speed_Error MORE THAN null THEN Throttle up
IF Speed_Error LESS THAN null THEN Throttle down
IF Acceleration MORE THAN null THEN Throttle up
IF Acceleration LESS THAN null THEN Throttle down
```

**Reglas para control del freno:**
```
IF Speed_Error MORE THAN nullf THEN Brake down
IF Speed_Error LESS THAN nullf THEN Brake up
IF Acceleration LESS THAN nullf THEN Brake up
```

**Coordinación de pedales:**
- Los controladores de acelerador y freno son independientes pero trabajan cooperativamente
- Asimetría en definiciones de variables para considerar diferencias en comportamiento de aceleración/frenado
- Emula la conducción humana

## 3. ACC + Stop&Go (Control de Crucero Adaptativo)

**Concepto:** Mantener distancia de seguridad definida por el usuario respecto al vehículo delantero

**Time-headway:** Distancia de seguridad dependiente de velocidad
- A mayor velocidad, mayor brecha requerida entre vehículos
- Ejemplo: gap de 2 segundos = 22.2m a 40 km/h, 55.5m a 100 km/h

**Ventajas sobre sistemas comerciales:**
- Sistemas ACC comerciales (desde 1995, ej: Mitsubishi Preview Distance Control):
  - No funcionan a velocidades < 40 km/h
  - Solo gestionan acelerador automáticamente

- **Sistema AUTOPIA:**
  - Automatiza acelerador Y freno
  - Funciona en todo el rango de velocidades del vehículo
  - Permite maniobras stop-and-go
  - Sensor: GPS (vehículos se comunican vía WLAN)

**Aplicaciones:**
- Aumenta comodidad de conducción
- Regula velocidad del tráfico
- Rompe embotellamientos más rápidamente
- Previene colisiones traseras en situaciones stop-and-go

## 4. Maniobras de Adelantamiento

**Condiciones previas:**
1. Vehículo en modo de conducción en carril recto
2. Carril izquierdo libre
3. Espacio suficiente para adelantamiento

**Proceso (5 pasos):**
1. Vehículo en modo carril recto
2. Cambio a modo cambio-de-carril → movimiento a carril izquierdo
3. Cambio a modo carril recto hasta pasar el obstáculo/vehículo
4. Cambio a modo cambio-de-carril → retorno a carril derecho
5. Cambio a modo carril recto, continúa conducción normal

**Cálculo de inicio:**
- Función de velocidad relativa y longitud del vehículo
- Punto de cambio de carril: A + l (donde A = distancia de cambio, l = longitud del vehículo = 4m)
- A depende de velocidad relativa entre vehículos

**Transiciones:**
- Paso 2→3: cuando Angular_Error < 2° y Lateral_Error < 0.8m
- Paso 3→4: cuando parte trasera del vehículo que adelanta pasa el frente del adelantado con separación l
- Paso 4→5: igual que 2→3

## 5. Detección de Vehículos Basada en Visión

**Objetivo:** Complementar navegación GPS con capacidad reactiva visual

**Proceso en dos etapas:**

### Etapa 1: Búsqueda y Detección
- **ROI (Region of Interest):** Área rectangular predefinida en sección central de imagen (reduce tiempo de ejecución)
- **Análisis de bordes verticales y simetría de color:**
  - Vehículos tienen formas rectangulares y simétricas artificiales
  - Bordes verticales fácilmente distinguibles
  - Se calcula mapa de simetría del ROI
  - Se consideran pares de bordes verticales con alta simetría
  - Se descartan combinaciones que producen formas no realistas

### Etapa 2: Validación Temporal y Seguimiento
- **Consistencia temporal:** Objeto debe detectarse en múltiples frames consecutivos (t = 0.5s)
- Filtro para eliminar detecciones incorrectas por ruido
- Sistema anota posición (x,y) de cada objeto candidato
- Incrementa contador de tiempo si distancia respecto a candidatos previos < dv = 1m
- Objeto validado como vehículo real cuando contador alcanza t = 0.5s
- **Tiempo de ejecución del algoritmo:** 100 ms

**Seguimiento:**
- Usa ROI del vehículo detectado como plantilla
- Detección de actualizaciones de posición por correlación
- Área de búsqueda limitada alrededor de posición del vehículo (eficiente y rápido)
- Filtrado con estimador de mínimos cuadrados recursivos con decaimiento exponencial
- Sistema mantiene posición estimada previa durante 5 iteraciones consecutivas sin validación antes de considerar track perdido
- Si se pierde: reinicia etapa de detección

## 6. Navegación Adaptativa - Detección de Frenado

**Problema:** Vehículo delantero frena repentinamente dentro de límites de gap de seguridad

**Solución:** Detectar activación de luces de freno del vehículo delantero

**Proceso:**
- Búsqueda detallada de luces de freno dentro del ROI del vehículo
- **Información a priori:** Luces de freno = dos luces rojas simétricas cerca de lados traseros izquierdo/derecho
- Una vez localizadas: monitoreo continuo de luminancia
- En caso de activación repentina: alarma al navegador → frenado de emergencia
- **Seguridad redundante:** Sistema también calcula continuamente distancia al vehículo delantero

## Resultados y Trabajo Futuro

**Resultados experimentales:**
- Experimentos realizados con vehículos reales en carreteras reales (dentro de circuito privado)
- Controladores fuzzy imitan perfectamente comportamiento humano:
  - Conducción y seguimiento de ruta
  - Maniobras complejas multi-vehículo (ACC, adelantamiento)

**Planes futuros:**
- Experimentos con tres vehículos automáticos en situaciones más complejas (intersecciones, rotondas)
- Colaboración europea bajo contrato UE CyberCars-2:
  - Conducción cooperativa con +6 vehículos
  - Nuevos sensores: detección de peatones, señales de tráfico, monitoreo de infraestructura
  - Sistemas de comunicación inalámbrica: vehículo-a-vehículo, vehículo-a-infraestructura, transmisión de información intra-vehículo
  - Uso de Galileo y GPS-2 (próxima generación) para mejorar precisión de localización

## Conclusiones Clave

1. **Flexibilidad de control fuzzy:** Permite integrar información sensorial diversa
2. **Visión complementaria:** Crucial para reaccionar a condiciones de tráfico real, complementa navegación GPS
3. **Simplicidad de reglas:** Pocas reglas fuzzy simples generan comportamiento complejo similar al humano
4. **Adaptabilidad:** Mismo conjunto de reglas con funciones de pertenencia variables permite diferentes comportamientos según contexto
5. **Conocimiento procedural:** Lógica fuzzy incorpora efectivamente conocimiento humano de conducción en algoritmos de control
6. **Aplicabilidad industrial:** Componentes modulares diseñados para aplicación inmediata en industria automotriz
