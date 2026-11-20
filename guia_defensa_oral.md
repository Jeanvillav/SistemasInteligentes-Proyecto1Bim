# 🗣️ Guía Maestra para la Defensa Oral del Proyecto de Clustering
## Proyecto Bimestral — Sistemas Inteligentes

Esta guía contiene las preguntas clave que el docente o tribunal suele formular durante la defensa oral, explicadas de forma directa, honesta y técnicamente sólida según los resultados reales del notebook (`proyecto_clustering.ipynb`) y la presentación PPT (`Presentacion_Clustering_Apendicitis.pptx`).

---

### 1. "¿Por qué retiraron la variable 'Diagnosis' antes de aplicar el clustering?"
> **Cómo responder con solvencia:**  
> "Porque el clustering es una técnica de **aprendizaje no supervisado**. Su objetivo es comprobar si los datos clínicos y de laboratorio (leucocitos, PCR, ecografía, síntomas) se agrupan de forma natural según perfiles médicos afines, **sin conocer la respuesta de antemano**. Si hubiéramos dejado `Diagnosis`, se habría producido **fuga de información (*data leakage*)** y los algoritmos habrían hecho trampa. Apartamos `Diagnosis` en un vector independiente y la usamos únicamente al final para validar qué tan bien se corresponden los clusters con la patología real mediante tablas de contingencia."

---

### 2. "¿Por qué estandarizaron con StandardScaler y qué pasaría si no lo hicieran?"
> **Cómo responder:**  
> "Tanto DBSCAN como el enlace de Ward calculan **distancias euclidianas**. En el dataset tenemos variables con escalas muy dispares: la PCR llega hasta 365 mg/L y los leucocitos a 25 mil, mientras que la temperatura varía en apenas 2 grados (36 a 38 °C) y los síntomas binarios solo valen 0 o 1.  
> Si no estandarizamos, una diferencia de 50 mg/L en la PCR pesaría 2500 veces más en la distancia euclidiana que tener o no tener dolor en fosa ilíaca derecha. `StandardScaler` transforma cada variable para que tenga media 0 y desviación estándar 1, permitiendo que todas aporten equitativamente."

---

### 3. "¿Qué decisiones tomaron con los valores atípicos y datos faltantes en el EDA?"
> **Cómo responder:**  
> "Identificamos dos tipos de situaciones:
> 1. **Errores de registro imposibles:** Había 8 registros con incongruencias biológicas severas (edades en 0 o 0.04 años cuando el estudio es de 1.5 a 18 años, y una temperatura de 26.9 °C incompatible con la vida). En el preprocesamiento se corrigieron o descartaron para no distorsionar las vecindades.
> 2. **Atípicos biológicos reales:** La PCR tiene 85 valores por encima del rango intercuartílico (hasta 365 mg/L). Estos no son errores, sino cuadros inflamatorios severos (apendicitis flemosa o perforada), por lo que **se conservaron**.
> 3. **Datos faltantes:** La ecografía (`Appendix_Diameter`) falta en el 36.3 % de pacientes porque en urgencias no siempre hay ecografista disponible o el gas intestinal no permite ver el apéndice. Se imputaron con la **mediana**, que es mucho más robusta frente a la asimetría de los datos clínicos que la media."

---

### 4. "¿Por qué el profesor les pidió usar PCA y UMAP y qué diferencia encontraron?"
> **Cómo responder:**  
> "Porque son dos paradigmas opuestos de reducción:
> - **PCA es lineal:** busca ejes ortogonales de máxima varianza global. Necesitamos 9 componentes para alcanzar el 81.1 % de la varianza. Su primer componente (`PC1`) representa un verdadero **'eje de gravedad inflamatoria'** donde pesan Alvarado, PAS, leucocitos y neutrófilos.
> - **UMAP es no lineal:** preserva la topología de vecindades locales. En lugar de proyectar varianza continua, UMAP agrupó a los pacientes que compartían combinaciones discretas de síntomas binarios.
> **La gran conclusión:** PCA funcionó mucho mejor para segmentar pacientes por niveles de gravedad clínica (78 %, 40 % y 26 % de riesgo), mientras que UMAP creó islas muy nítidas pero artificialmente dominadas por síntomas 0/1."

---

### 5. "¿Cómo eligieron el valor de Épsilon (ε) en DBSCAN y qué representa la gráfica de k-distancias?"
> **Cómo responder:**  
> "Usamos la gráfica de distancias al $k$-ésimo vecino más próximo, fijando de forma rigurosa **$k = \text{min\_samples} = 10$** para que la gráfica represente exactamente la condición de densidad del modelo.
> - Ordenamos las 10-distancias de menor a mayor. La curva se mantiene plana en regiones densas y sube bruscamente cuando entra en zonas dispersas.
> - Determinamos el codo matemáticamente mediante el método **Kneedle** (máxima distancia a la recta secante):
>   - En PCA: el codo dio $\varepsilon = 2.847$, detectando 2 clusters y 24 pacientes como ruido.
>   - En UMAP: el codo dio $\varepsilon = 0.470$, detectando 4 clusters y 11 pacientes de ruido.
> Además, construimos una **tabla de sensibilidad** variando $\varepsilon$ en $\pm 10\%$ y $\pm 20\%$, demostrando que en UMAP el algoritmo es extremadamente sensible (pasa de 16 clusters a 2 con pequeñas variaciones de radio)."

---

### 6. "¿Qué significa que el 'ruido' en DBSCAN sobre PCA fueran 24 pacientes?"
> **Cómo responder (¡Esta respuesta impresiona al tribunal!):**  
> "Al revisar quiénes eran esos 24 pacientes marcados como ruido (etiqueta `-1`) en PCA, descubrimos algo clínicamente fascinante: no eran datos basura, sino **los casos más graves de toda la sala de urgencias**.
> - Tenían una PCR promedio de **191.8 mg/L** (frente a 23 mg/L en el Cluster 0).
> - Un Alvarado promedio de **8.1** y leucocitos de **17.5 mil**.
> - El **91.7 % (22 de 24) tenía apendicitis confirmada**.
> En el espacio continuo de PCA, estos pacientes estaban tan alejados en el extremo de alta inflamación que no tenían 10 vecinos cerca. DBSCAN los aisló como ruido, demostrando que en triaje médico el 'ruido' de DBSCAN funciona como una **alerta roja de máxima prioridad quirúrgica**."

---

### 7. "¿Por qué el experimento con mejor silueta (Ward + UMAP = 0.761) NO es el mejor clínicamente?"
> **Cómo responder:**  
> "Porque el coeficiente de silueta mide únicamente **geometría matemática** (distancia intra-cluster vs inter-cluster), no utilidad diagnóstica.
> - En UMAP, los 41 pacientes que no tenían dolor en fosa ilíaca derecha formaron una isla completamente separada en el gráfico 2D. El algoritmo cortó esa isla y por eso la silueta dio 0.761.
> - Sin embargo, al cruzarlo con el diagnóstico real, el Cluster 0 tiene 60.6 % de apendicitis y la base entera tiene 59.4 %: **no discrimina el riesgo del paciente**.
> - En cambio, **Ward sobre PCA** obtuvo una silueta baja (0.184) porque los datos médicos forman un continuo biológico sin fronteras vacías; pero sus 3 clusters separan a los pacientes en tres niveles reales de sospecha: **78.4 % (alto), 40.2 % (medio) y 26.3 % (bajo)**. Por eso, una silueta alta no garantiza un modelo clínicamente útil."

---

### 8. "¿Cómo determinaron el número de clusters (k) en el Clustering Aglomerativo y cómo se lee el Dendrograma?"
> **Cómo responder:**  
> "Utilizamos dos criterios combinados:
> 1. **Evaluación cuantitativa con silueta:** Calculamos el `silhouette_score` para $k \in [2, 7]$. En PCA el pico se dio en $k=3$ (0.184) y en UMAP en $k=2$ (0.761).
> 2. **Corte del dendrograma:** Usamos el enlace de **Ward**, que minimiza el incremento de varianza interna en cada fusión. Trazamos una línea horizontal roja en la altura donde se observa el mayor salto vertical sin fusiones intermedias:
>    - En PCA cortamos a una distancia de **33.07**, cortando exactamente 3 ramas verticales ($k=3$).
>    - En UMAP cortamos a una distancia de **95.68**, cortando exactamente 2 ramas ($k=2$).
> Ambos criterios coincidieron perfectamente."

---

### 9. "¿Qué representa un valor negativo en el gráfico de silueta individual (silhouette_samples)?"
> **Cómo responder:**  
> "El coeficiente individual $s(i) = \frac{b(i) - a(i)}{\max(a(i), b(i))}$ compara la distancia promedio a los puntos de su propio cluster ($a$) con la distancia al cluster ajeno más cercano ($b$).
> - Si $s(i) < 0$, significa que $b(i) < a(i)$: el paciente está geométricamente **más cerca de los integrantes de otro cluster que de los de su propio grupo**.
> - En nuestros gráficos tipo cuchillas, la presencia de barras negativas diagnostica zonas de solapamiento o frontera ambigua entre grupos, lo cual es muy común en pacientes con cuadros clínicos moderados o dudosos."

---

### 10. "¿Cuál de los modelos recomendarían para un hospital y cuáles son sus limitaciones?"
> **Cómo responder:**  
> "Recomendamos una estrategia combinada según el objetivo:
> - **Para triaje rápido y estratificación de riesgo:** **Ward sobre PCA (k = 3)**. Es estable, determinista y clasifica al niño en riesgo alto (78 %), medio (40 %) o bajo (26 %), ayudando a decidir si va directo a quirófano, a observación o a alta.
> - **Para alertas tempranas críticas:** **DBSCAN sobre PCA**, porque etiqueta como ruido a los pacientes con inflamación extrema que no pueden esperar.
> **Limitaciones:** El clustering es no supervisado, por lo que ningún cluster ofrece certeza del 100 % (incluso en el cluster de alto riesgo hay un 22 % de niños sin apendicitis). Además, la distancia euclidiana mezcla variables continuas y binarias; como mejora a futuro, recomendamos evaluar la **métrica de Gower** o algoritmos como **HDBSCAN**."
