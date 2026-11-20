# 🗣️ Guía Rápida para la Defensa Oral: Proyecto de Clustering (Apendicitis Pediátrica)

Esta guía contiene las preguntas más frecuentes que suele hacer el docente en la sustentación oral, explicadas de forma directa, sencilla y sin rodeos técnicos innecesarios para que puedas responder con total seguridad.

---

### 1. "¿Por qué quitaron la columna 'Diagnosis' antes de clusterizar?"
> **Cómo responder:**  
> "Porque el clustering es un método **no supervisado**. Su objetivo es ver si la máquina es capaz de descubrir grupos naturales de pacientes guiándose únicamente por los síntomas, exámenes de sangre y ecografías, sin saber el resultado final. Si le dejábamos la columna de diagnóstico, el modelo habría hecho trampa (fuga de datos o *data leakage*). Lo que hicimos fue apartar esa columna y usarla solo al final para comprobar si los grupos que encontró el algoritmo coincidían con la realidad médica."

---

### 2. "¿Por qué fue obligatorio estandarizar los datos con StandardScaler?"
> **Cómo responder:**  
> "Porque los algoritmos de clustering calculan distancias euclidianas (distancias en línea recta). Si no estandarizamos, una variable con números grandes como la PCR (que llega a 50 o 100) o los leucocitos (12,000) dominaría por completo el cálculo y aplastaría a variables pequeñas pero importantísimas como la temperatura (37 grados) o los síntomas que valen 0 y 1. `StandardScaler` pone a todas las variables a jugar en igualdad de condiciones, dejándolas con media 0 y varianza 1."

---

### 3. "¿Por qué usaron la mediana y no la media para rellenar los datos nulos?"
> **Cómo responder:**  
> "Porque en medicina las pruebas de laboratorio tienen valores atípicos extremos cuando hay infecciones graves (por ejemplo, pacientes con PCR disparada). La media se deja distorsionar fácilmente por esos valores extremos, mientras que la **mediana** es mucho más resistente y representa mejor el valor típico de un paciente."

---

### 4. "¿Cómo eligieron el valor de Épsilon (ε) en DBSCAN y qué representa la gráfica de k-distancias?"
> **Cómo responder:**  
> "No lo elegimos al azar ni a ojo. Usamos el método formal del codo sobre las distancias al 5to vecino más cercano (5-NN):
> - Calculamos para cada paciente qué tan lejos está su quinto vecino.
> - Al ordenar esas distancias de menor a mayor, se forma una curva que va subiendo suavemente en las zonas densas y luego se dispara hacia arriba cuando llega a los pacientes aislados.
> - El punto exacto donde la curva 'dobla el codo' (alrededor de 0.55) es nuestro épsilon óptimo. Marca la frontera entre lo que es un grupo denso y lo que empezaría a considerarse disperso o ruido."

---

### 5. "¿Qué significa el Coeficiente de Silueta y qué pasaría si un punto tiene valor negativo?"
> **Cómo responder:**  
> "El coeficiente de silueta mide qué tan bien ubicado está un paciente en su grupo comparando dos cosas: qué tan cerca está de sus compañeros de grupo ($a$) frente a qué tan lejos está del grupo vecino más cercano ($b$).
> - Va de -1 a +1. Un valor de **0.78** (el que obtuvimos) indica una separación excelente.
> - Si un punto diera un valor negativo, significaría que está más cerca de los pacientes de otro grupo que de los suyos propios, lo que sería señal de una mala clasificación o de un caso ambiguo ubicado justo en la frontera."

---

### 6. "¿Por qué excluyeron el ruido (-1) al calcular la silueta en DBSCAN?"
> **Cómo responder:**  
> "Porque los puntos marcados como ruido en DBSCAN no son un cluster en sí mismos; son simplemente pacientes atípicos o casos aislados. Si los evaluáramos como si fueran un grupo más, la fórmula de la silueta asumiría erróneamente que todos los puntos de ruido deberían estar pegados entre sí, lo cual distorsionaría y penalizaría de forma injusta la medición de los verdaderos grupos."

---

### 7. "¿Qué diferencia hay entre el enlace de Ward y otros como Single o Average en el Dendrograma?"
> **Cómo responder:**  
> "- **Single linkage** une grupos fijándose solo en los dos puntos más cercanos, lo que suele provocar un efecto de 'cadena' donde los grupos quedan largos y desparramados.
> - **Average linkage** promedia todas las distancias, pero puede ser inestable.
> - **Ward linkage** busca en cada unión que el aumento en la varianza interna sea el mínimo posible. Eso obliga al algoritmo a formar grupos compactos, homogéneos y esféricos, que es justo lo que buscamos al segmentar pacientes."

---

### 8. "¿Por qué fue tan importante UMAP frente a PCA?"
> **Cómo responder:**  
> "Porque PCA es una técnica puramente lineal: proyecta los datos en líneas rectas y aplasta las relaciones complejas, dejando una nube de puntos totalmente solapada donde la silueta apenas llegaba a 0.19. En cambio, las enfermedades y la biología humana no son lineales. UMAP modela las vecindades respetando la curvatura de los datos, lo que permitió desenredar a los pacientes y facilitó que tanto DBSCAN como Ward encontraran los dos grupos naturales con una silueta de casi 0.79."

---

### 9. "¿Qué representan clínicamente los dos grupos que encontraron los algoritmos?"
> **Cómo responder:**  
> "Los algoritmos separaron a los pacientes según su forma de manifestar la enfermedad:
> - **Cluster 0 (Presentación Clásica / Alta Sospecha):** El 100% presenta dolor localizado en la fosa ilíaca derecha, apéndice engrosado (7.8 mm de promedio) y puntajes altos de Alvarado. Este grupo concentró al **96.8% de todos los pacientes que realmente tenían apendicitis**.
> - **Cluster 1 (Presentación Atípica / Baja Sospecha):** Pacientes sin dolor en la fosa ilíaca derecha, con scores de Alvarado y PAS notablemente menores y apéndices de grosor normal."

---

### 10. "¿Cuál de los dos modelos recomendarías para implementar en el triaje de urgencias de un hospital?"
> **Cómo responder:**  
> "Para triaje médico recomendaría **DBSCAN**. Su gran fortaleza es que si llega un paciente con síntomas contradictorios o exámenes extraños, no lo fuerza a entrar en una categoría a ciegas, sino que lo aísla como caso atípico (ruido). En un hospital, esos casos raros son justamente los que requieren que un cirujano los revise con lupa o les pida una tomografía para no cometer un error."

