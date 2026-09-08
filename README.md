# LaLiga – Optimizador de Calendarios y Jornadas mediante Algoritmo Genético

![Python Version](https://img.shields.io/badge/python-3.9%2B-blue.svg)
![Streamlit](https://img.shields.io/badge/streamlit-1.28%2B-FF4B4B.svg)
![License](https://img.shields.io/badge/license-MIT-green.svg)
![Status](https://img.shields.io/badge/status-Active-brightgreen.svg)
[![Streamlit App](https://static.streamlit.io/badges/streamlit_badge_black_white.svg)](https://geneticalgorithm-hxkebfdpdeecjunrpbpzdf.streamlit.app)

Optimizador interactivo desarrollado en **Python** y **Streamlit** que utiliza **Algoritmos Genéticos (GA)** para maximizar la audiencia televisiva estimada en partidos de LaLiga (primera división de fútbol español), garantizando el cumplimiento de restricciones operativas y de emisión.

---

## Descripción del Proyecto

El proyecto aborda un problema de optimización combinatoria complejo: determinar la programación horaria óptima para los 10 partidos de una jornada individual o para los 380 partidos de una temporada completa (38 jornadas).

### Características Principales
- **Estudio de Jornada Única (`laliga.py`)**: Asignación óptima de slots horarios para 10 partidos con visualización en tiempo real de la evolución de audiencia.
- **Optimización de Temporada Completa (`pages/1_page.py`)**: Simulación y optimización combinada de las 38 jornadas de la liga.
- **Generación Flexible**: Soporta creación manual de enfrentamientos o generación aleatoria respetando categorías de equipos.
- **Cálculo de Audiencias por Categoría**: Matriz de atracción entre equipos de categorías **A**, **B** y **C**, ponderada por coeficientes franja horaria y penalizaciones por coincidencia horaria.

---

## Fundamento Algorítmico y Matemático

El espacio de búsqueda de una jornada con 10 partidos y 12 slots horarios disponibles equivale a $12^{10} \approx 6.19 \times 10^{10}$ combinaciones posibles. Para la temporada completa (38 jornadas), el espacio asciende a $(12^{10})^{38} \approx 10^{410}$, haciendo inviable la búsqueda exhaustiva.

### 1. Función de Fitness ($\mathcal{F}$)
La función objetivo maximiza la audiencia total aplicando penalizaciones por incumplimiento de días obligatorios de emisión (Viernes, Sábado, Domingo, Lunes):

$$\text{Audiencia}(p, d, h, c) = \text{Base}(C_{\text{local}}, C_{\text{vis}}) \times \mu(d, h) \times (1 - \pi(c))$$

$$\mathcal{F} = \sum_{i=1}^{N_{\text{partidos}}} \text{Audiencia}(p_i, d_i, h_i, c_i) - \phi \cdot \Delta_{\text{días}}$$

Donde:
- $\text{Base}$: Audiencia base entre categorías de equipos (A-A, A-B, etc.).
- $\mu(d, h)$: Coeficiente multiplicador de franja horaria (día $d$, hora $h$).
- $\pi(c)$: Penalización porcentual por coincidencia de $c$ partidos en la misma franja.
- $\phi$: Factor de penalización por días obligatorios sin partidos asignados ($5,000,000$).
- $\Delta_{\text{días}}$: Cantidad de días requeridos no cubiertos.

### 2. Operadores Genéticos
- **Población Inicial**: Vectores de asignación aleatoria de slots horarios.
- **Selección**: Selección por torneo (*Tournament Selection*) con tamaño parametrizable $k$.
- **Cruce (Crossover)**: Cruce monopunto (*Single-Point Crossover*) sobre el vector de slots o jornadas.
- **Mutación**:
  - *Mutación Aleatoria*: Cambia el slot horario de un partido con probabilidad $p_m$.
  - *Mutación por Intercambio*: Intercambia las franjas de dos partidos distintos dentro del mismo individuo.
- **Elitismo**: Conservación directa de las 2 mejores soluciones de la generación previa.

---

## Estructura del Repositorio

```text
genetic_algorithm/
├── .gitignore              # Filtro de archivos excluidos de Git
├── README.md               # Documentación principal del proyecto
├── README.txt              # Nota informativa original del proyecto
├── laliga.py               # Aplicación principal de Streamlit (Jornada Única)
├── liga.pdf                # Documento teórico con análisis combinatorio y Big O
├── requirements.txt        # Dependencias necesarias para ejecutar el proyecto
└── pages/
    └── 1_page.py           # Página auxiliar de Streamlit (Temporada Completa)
```

---

## Guía de Instalación y Ejecución

### Requisitos Previos
- **Python 3.9** o superior instalado en el sistema.

### 1. Clonar el Repositorio
```bash
git clone https://github.com/tu-usuario/genetic_algorithm.git
cd genetic_algorithm
```

### 2. Crear y Activar el Entorno Virtual

- **En Windows (PowerShell / CMD):**
  ```powershell
  python -m venv venv
  .\venv\Scripts\activate
  ```

- **En macOS / Linux:**
  ```bash
  python3 -m venv venv
  source venv/bin/activate
  ```

### 3. Instalar Dependencias
```bash
pip install -r requirements.txt
```

### 4. Ejecutar la Aplicación Streamlit
```bash
streamlit run laliga.py
```

La interfaz se abrirá automáticamente en tu navegador web en `http://localhost:8501`.

---

## Parámetros de Configuración

| Parámetro | Rango por Defecto | Descripción |
| :--- | :--- | :--- |
| **Tamaño de Población ($P$)** | $100 - 2000$ | Número de individuos evaluados por generación. |
| **Generaciones Máximas ($G$)** | $50 - 1000$ | Límite superior de iteraciones del algoritmo genético. |
| **Paciencia / Parada Temprana** | $5 - 30$ gen | Generaciones sin mejora sustancial ($<0.1\%$) para emitir alerta. |
| **Tasa de Mutación 1 (Aleatoria)** | $0 - 100$% | Probabilidad de reasignar aleatoriamente la franja horaria de un partido. |
| **Tasa de Mutación 2 (Intercambio)** | $0 - 100$% | Probabilidad de intercambiar franjas entre dos partidos. |
| **Tamaño de Torneo ($k$)** | $5$ | Cantidad de individuos competidores en la fase de selección. |

---

## Complejidad Computacional

Dada una población de tamaño $P$, $G$ generaciones y $N$ partidos por evaluación:
- **Complejidad Temporal**: $\mathcal{O}(P \cdot G \cdot N)$
- **Complejidad Espacial**: $\mathcal{O}(P \cdot N)$

El análisis matemático exhaustivo sobre la cota superior y la justificación de la heurística GA frente a algoritmos de fuerza bruta se encuentra detallado en el documento `liga.pdf`.

---

## Licencia

Este proyecto se distribuye bajo la licencia **MIT**. Consulta el archivo de licencia para más detalles.


## Author

**Sergio Mínguez Cruces** · [github.com/Sergio-repogit](https://github.com/Sergio-repogit)
