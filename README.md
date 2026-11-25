# Detección y Mitigación Óptima de Ciberamenazas mediante Modelos Estocásticos y Optimización

## Descripción del Problema
En entornos de ciberseguridad, las fases de un ciberataque permanecen ocultas detrás de grandes volúmenes de tráfico de red. Actuar demasiado tarde permite la fuga de información crítica (**exfiltración de datos**), mientras que aplicar medidas defensivas agresivas de forma indiscriminada satura la capacidad de procesamiento de la red y genera altos costos operativos por falsos positivos.

Este proyecto resuelve el problema mediante un **marco matemático integral** que conecta seis herramientas de Investigación Operativa sobre tráfico real de red (dataset [*MedSec-25*](https://www.kaggle.com/datasets/abdullah001234/medsec-25-iomt-cybersecurity-dataset), 554.479 flujos). El flujo parte desde la inferencia no supervisada del estado del atacante hasta la asignación óptima de recursos del servidor de inspección (IPS), garantizando la máxima protección posible sin colapsar la infraestructura.

## Hilo Conductor del Proyecto
`Datos Crudos` $\rightarrow$ `HMM` $\rightarrow$ `DTMC` $\rightarrow$ `Teoría de Decisiones` $\rightarrow$ `Teoría de Juegos` $\rightarrow$ `Teoría de Colas` $\rightarrow$ `Programación No Lineal`

## Estructura de Notebooks (`.ipynb`)

* **`procesamiento_datos.ipynb`**: Limpia el dataset original, reconstruye sesiones cronológicas agrupando por pares de IP (`Src IP` $\rightarrow$ `Dst IP`), aplica transformación logarítmica $\ln(x+1)$ y estandarización $Z$-score sobre las variables observables, y exporta los archivos estructurados en `datos_procesados/`.
* **`HMM.ipynb`**: Entrena un Modelo Oculto de Markov Gaussiano (`GaussianHMM` de 4 estados ocultos) para descubrir de forma no supervisada las fases del ataque (*Reconocimiento*, *Acceso Inicial*, *Pivote* y *Exfiltración*) y exporta la matriz de transición $A$ ($4 \times 4$) a `resultados_hmm.json`.
* **`teoria_decisiones.ipynb`**: Modela el ataque como una Cadena de Markov en Tiempo Discreto (**DTMC**) fijando la exfiltración como estado absorbente para calcular la ventana de reacción (tiempos medios de absorción). Luego aplica el **Criterio de Bayes** sobre una matriz de costos operativos para definir la política óptima de respuesta a un paso (*Monitorear*, *Inspeccionar* o *Bloquear*).
* **`teoria_juegos.ipynb`**: Plantea un juego matricial de suma cero entre el Defensor y un Atacante estratégico. Al comprobar la ausencia de punto de silla, resuelve mediante Programación Lineal el **Equilibrio de Nash en Estrategias Mixtas**, determinando las probabilidades óptimas de bloqueo e inspección para acotar el daño esperado.
* **`teoria_colas.ipynb`**: Modela el motor de Inspección Profunda de Paquetes (IPS) como un sistema de colas **$M/M/1$** utilizando las tasas reales de llegada del tráfico y la política derivada del Equilibrio de Nash, evaluando la utilización del servidor ($\rho$), latencia ($W$) y umbrales de saturación.
* **`programacion_no_lineal.ipynb`**: Integra las probabilidades estacionarias del HMM, los costos de impacto y la restricción hiperbólica de congestión del servidor IPS para resolver mediante **SLSQP** la asignación óptima de capacidad de procesamiento ($x_i^*$) en cada fase del ataque sin saturar la red.

## Requisitos e Instalación
1. Clonar el repositorio e instalar las dependencias:
   ```bash
   pip install -r requirements.txt