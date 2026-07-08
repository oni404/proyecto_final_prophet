# Predicción de Generación Eléctrica en Panamá con Prophet

**Universidad Tecnológica de Panamá — Facultad de Ingeniería de Sistemas Computacionales**
**Curso:** Inteligencia Artificial · **Tipo de proyecto:** Machine Learning (series de tiempo)

Modelo predictivo basado en [Prophet](https://facebook.github.io/prophet/) que estima la
generación eléctrica mensual (MWh) de tres distribuidoras panameñas —**EDECHI**, **EDEMET**
y **ENSA**— a partir del histórico 2019–2025, y proyecta valores para 2026.

---

## 1. Contenido del repositorio

```
proyecto/
├── README.md                     <- Este archivo
├── requirements.txt               <- Dependencias del proyecto
├── .gitignore
├── config/
│   └── config.example.yaml        <- Configuración de ejemplo (sin credenciales)
├── data/
│   └── dataset.csv                <- Dataset original (Año, Distribuidor, Enero..Diciembre)
├── notebooks/
│   └── final_modelo_prophet.ipynb <- Notebook principal: EDA, entrenamiento, validación,
│                                      gráficos y sistema experto interactivo
├── models/
│   ├── README.md
│   └── modelo_prophet_*.pkl       <- Modelos Prophet entrenados (se generan al ejecutar)
└── evidence/
```

## 2. Descripción del proyecto

- **Dataset:** `data/dataset.csv`, formato ancho (una fila por Año-Distribuidor, una
  columna por mes), 2019–2025.
- **Preprocesamiento:** codificación numérica manual de `Distribuidor`
  (`EDEMET=0, EDECHI=1, ENSA=2`), reestructuración a formato largo `(ds, y)` requerido
  por Prophet, y tratamiento de anomalías (ceros de cierre de 2023 y caída atípica de
  EDECHI en 2022) como valores faltantes (`NaN`), que Prophet interpola.
- **Modelo:** un `Prophet` independiente por distribuidora, con estacionalidad anual y
  `Distribuidor_Num` como regresor externo (`add_regressor`).
- **Validación:** entrenamiento 2019–2024, prueba 2025, métricas MAE / RMSE / MAPE.
- **Producción:** modelos reentrenados con todo el histórico (2019–2025) para proyectar 2026.
- **Sistema experto:** menú de consola con dos opciones — predicción puntual
  (distribuidora + mes) y consolidado nacional anual 2026.

## 3. Requisitos

- Python 3.9 – 3.11 (Prophet no siempre soporta versiones muy recientes de Python).
- Ver `requirements.txt` para las versiones de librerías.

## 4. Instalación

```bash
# 1) Crear y activar un entorno virtual (recomendado)
python -m venv venv
source venv/bin/activate        # En Windows: venv\Scripts\activate

# 2) Instalar dependencias
pip install -r requirements.txt
```

> **Nota sobre Prophet:** en algunos sistemas Prophet requiere que primero se instale
> `cmdstanpy` y su backend `CmdStan`. Si `pip install -r requirements.txt` falla al
> compilar Prophet, ejecuta:
> ```bash
> pip install cmdstanpy
> python -m cmdstanpy.install_cmdstan
> ```
> y vuelve a intentar `pip install prophet`.

## 5. Ejecución

### Notebook

```bash
jupyter notebook notebooks/final_modelo_prophet.ipynb
```

Ejecutar todas las celdas en orden (`Kernel → Restart & Run All`). El notebook:

1. Carga y limpia el dataset.
2. Genera el EDA (tablas reestructuradas y matriz/heatmap de correlación).
3. Prepara los datos en formato Prophet.
4. Valida el modelo contra 2025 (imprime MAE/RMSE/MAPE por distribuidora).
5. Entrena los modelos de producción (2019–2025).
6. Grafica real vs. predicho.
7. Corre una demostración no interactiva de las predicciones 2026 (para que
   "Run All" no se detenga esperando input de consola).
8. Guarda los modelos entrenados en `models/`.

Para usar el **sistema experto interactivo por consola** (menú con `input()`),
descomenta la línea `menu_interactivo(modelos_finales, codigos_distribuidor)` al
final de la última celda de código y ejecútala en un Jupyter local (no funciona
en modos de ejecución automática como `nbconvert --execute` o CI).

## 6. Configuración

`config/config.example.yaml` documenta todos los parámetros ajustables (ruta del
dataset, mapeo de distribuidoras, año de corte train/test, hiperparámetros de
Prophet). Cópialo como `config/config.yaml` si quieres personalizar valores sin
modificar el script. **No contiene credenciales ni información sensible.**

## 7. Evidencias de ejecución

Incluye evidencia real delnotebook en ejecución.

## 8. Modelos entrenados

Ver `models/README.md`. Los archivos `.pkl` se generan al correr el notebook o
`src/pipeline.py`; no se incluyen datos sensibles, solo el objeto Prophet serializado.

## 9. Autoría y créditos

Proyecto final del curso de Inteligencia Artificial — Universidad Tecnológica de
Panamá. Dataset: generación eléctrica mensual por distribuidora en Panamá, 2019–2025.
