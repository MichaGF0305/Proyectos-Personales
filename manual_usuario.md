# Manual de Usuario

## Índice

- [Manual de Usuario](#manual-de-usuario)
  - [Índice](#índice)
  - [Descripción del Proyecto](#descripción-del-proyecto)
  - [Requisitos](#requisitos)
  - [Estructura de Carpetas](#estructura-de-carpetas)
  - [Configuración Inicial](#configuración-inicial)
  - [Carga de Datos](#carga-de-datos)
  - [Uso de la Clase `GAOptimization`](#uso-de-la-clase-gaoptimization)
    - [6.1 Parámetros de Configuración](#61-parámetros-de-configuración)
    - [6.2 Ejecución de un Experimento Individual](#62-ejecución-de-un-experimento-individual)
  - [Uso de la Clase `FlujoGeneral`](#uso-de-la-clase-flujogeneral)
    - [7.1 Configuración de Parámetros por Cluster](#71-configuración-de-parámetros-por-cluster)
    - [7.2 Ejecución Automática de Todos los Experimentos](#72-ejecución-automática-de-todos-los-experimentos)
  - [Interpretación de Resultados](#interpretación-de-resultados)
  - [Gestión de Logs](#gestión-de-logs)
  - [Validación y Métricas](#validación-y-métricas)
  - [Exportación de Resultados](#exportación-de-resultados)
  - [Contribuyentes](#contribuyentes)

---

## Descripción del Proyecto

Este repositorio implementa un algoritmo genético (GA) para optimizar rutas de compra en base a un catálogo de productos, precios y distancias entre tiendas. El objetivo es minimizar el costo total (productos + transporte).

---

## Requisitos

- Python ≥3.8  
- Google Colab o Jupyter Notebook  
- Librerías:
  - pandas  
  - numpy  
  - matplotlib  
  - seaborn  
  - psutil  
  - openpyxl  
  - google‑colab (solo en Colab)  

Instalación rápida:
```bash
pip install pandas numpy matplotlib seaborn psutil openpyxl
```

---

## Estructura de Carpetas

```
Proyecto - Compra Genética/
├── problemset/problemset.xlsx
├── [Carpetas de experimentos: Small, Medium, Big]
│   └── logs/
└── Validación/
    └── Lo Mejor/
```

---

## Configuración Inicial

1. Montar Google Drive (si se usa Colab):
    ```python
    from google.colab import drive
    drive.mount('/content/drive')
    ```
2. Definir rutas:
    ```python
    import os
    my_drive = "/content/drive/MyDrive/"
    compra_genetica = os.path.join(my_drive, "Proyecto - Compra Genética")
    problem_set_path = os.path.join(compra_genetica, "problemset/problemset.xlsx")
    ```

---

## Carga de Datos

Leer el problemset:
```python
import pandas as pd
problem_set = pd.read_excel(problem_set_path)
```

Leer catálogo, grafo y precio de combustible:
```python
from GAOptimization import read_catalogo_grafo_fuel
catalogo, grafo, fuel_price = read_catalogo_grafo_fuel(indice=0)
```

---

## Uso de la Clase `GAOptimization`

### 6.1 Parámetros de Configuración

| Parámetro | Descripción | Ejemplo |
|-----------|-------------|---------|
| project_path | Ruta base del proyecto | `compra_genetica` |
| indice | Índice fila en problemset | `12` |
| pop_size | Tamaño de población | `30` |
| penalty_factor | Factor de penalización por rutas desconectadas | `10.0` |
| selection_params | Método y tasa de selección (`hard` o `soft`) | `{"method":"soft","selection_rate":0.4}` |
| crossover_params | Método (`uniform` o `sub_tree`), tasa de crossover e inheritance | `{"method":"uniform","crossover_rate":0.88,"inheritance_rate":0.5}` |
| mutation_params | Método (`swap` o `scramble`), tasa de mutación | `{"method":"swap","mutation_rate":0.1}` |
| epoch | Número de iteraciones | `150` |
| log_path | Carpeta destino para archivos de log | `".../logs"` |

### 6.2 Ejecución de un Experimento Individual

```python
ga = GAOptimization(
    project_path=compra_genetica,
    indice=12,
    pop_size=30,
    penalty_factor=10.0,
    selection_params={"method":"soft","selection_rate":0.4},
    crossover_params={"method":"uniform","crossover_rate":0.88,"inheritance_rate":0.5},
    mutation_params={"method":"swap","mutation_rate":0.1},
    epoch=150,
    log_path=os.path.join(compra_genetica, "pruebas/logs")
)
best_score, best_solution = ga.run()
print(best_score, best_solution)
```

---

## Uso de la Clase `FlujoGeneral`

### 7.1 Configuración de Parámetros por Cluster

Define diccionarios `small_params`, `medium_params`, `big_params` con la misma estructura de parámetros que `GAOptimization`.

### 7.2 Ejecución Automática de Todos los Experimentos

```python
from FlujoGeneral import FlujoGeneral
fg = FlujoGeneral(compra_genetica, GAOptimization, problem_set, "Experimento", small_params, medium_params, big_params)
results_df, output_folder = fg.flujo_general()
```

Generará JSON con soluciones óptimas y un DataFrame resumen.

---

## Interpretación de Resultados

El DataFrame `results_df` contiene columnas clave para evaluar desempeño y compararlo con valores óptimos del problemset.

---

## Gestión de Logs

Cada ejecución crea un archivo `IDX.txt` en `log_path`. Para borrar logs:
```bash
rm -rf "/.../logs/*"
```

---

## Validación y Métricas

Se calculan errores (puntual, MAE, MSE, ENP) comparando resultados vs valores óptimos, con gráficos para visualización.

---

## Exportación de Resultados

Exportar DataFrame final a CSV:
```python
results_df.to_csv("hard_uniform_swap_df.csv", index=False)
```

---

## Contribuyentes

- Darwin Méndez — GitHub: @Daarwinmendez  
- Camily García — GitHub: @CamyG18  
- Michael García — GitHub: @MichaGF0305  
- Roither Sánchez — GitHub: @XTrollaX

