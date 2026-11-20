# Sistemas Inteligentes — Proyecto Bimestral

## Clustering No Supervisado: DBSCAN y Clustering Jerárquico Aglomerativo

**Curso:** Sistemas Inteligentes  
**Dataset:** `app_mod.xlsx` — Pacientes pediátricos con sospecha de apendicitis  
**Condición de grupo:** Usar PCA y UMAP para clusterizar (`condiciones.txt`)

---

## Contenido del repositorio

| Archivo | Descripción |
|---|---|
| `proyecto_clustering.ipynb` | Notebook principal con todo el análisis, gráficos y resultados ejecutados |
| `app_mod.xlsx` | Dataset de 782 pacientes pediátricos con variables clínicas y de laboratorio |
| `condiciones.txt` | Condiciones específicas asignadas al grupo (PCA + UMAP obligatorios) |
| `guia_defensa_oral.md` | Banco de preguntas y respuestas para la defensa oral (separado del notebook) |

---

## Estructura del Notebook

1. **Punto 1 — EDA (1 pt):** Explicación de la base, diccionario de 17 variables, análisis de nulos, distribuciones y matriz de correlación.
2. **Punto 2 — Filtros y Preprocesamiento (sin nota):** Eliminación de `Diagnosis`, codificación binaria, imputación con mediana, `StandardScaler`, reducción con PCA y UMAP.
3. **Punto 3 — DBSCAN (2.5 pts):** Gráfica de k-distancias para selección de ε, `silhouette_score`, número de objetos por cluster, `silhouette_samples`, interpretación clínica.
4. **Punto 4 — Clustering Aglomerativo (2.5 pts):** Dendrograma (Ward), `silhouette_score` para selección de k, número de objetos, `silhouette_samples`, interpretación clínica.
5. **Punto 5 — Conclusiones (1 pt):** Comparación entre ambos experimentos e impacto de PCA vs UMAP.

---

## Cómo ejecutar

```bash
# Crear entorno virtual e instalar dependencias
python3 -m venv .venv
source .venv/bin/activate
pip install pandas numpy scipy scikit-learn matplotlib seaborn openpyxl umap-learn ipykernel jupyterlab

# Registrar kernel y abrir el notebook
python -m ipykernel install --user --name=proyectobim --display-name "Python (ProyectoBim)"
jupyter lab proyecto_clustering.ipynb
```
