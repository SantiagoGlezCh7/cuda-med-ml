# Entendiendo las Diferencias Fundamentales: CPU vs. GPU

Este documento resume mis notas y conclusiones sobre las diferencias arquitectónicas y de casos de uso entre una Unidad Central de Procesamiento (CPU) y una Unidad de Procesamiento Gráfico (GPU). Este repositorio no es solo teórico; lo utilizo como mi bitácora para aplicar estos conceptos en proyectos de cómputo de alto rendimiento.

---

## 🎯 Objetivos del Proyecto

El propósito de este repositorio es doble:

1.  **Documentar mi aprendizaje:** Servir como una referencia personal detallada sobre la arquitectura de cómputo paralelo.
2.  **Aplicar el conocimiento:** Implementar y optimizar algoritmos que demuestren el poder de la aceleración por GPU. Específicamente, me enfocaré en dos áreas clave:
    * **Simulación de Monte Carlo:** Desarrollaré una simulación para analizar cómo el paralelismo masivo de una GPU puede acelerar drásticamente los métodos estocásticos.
    * **Machine Learning en Medicina:** Abordaré un problema del sector salud, aplicando técnicas de machine learning donde la velocidad de procesamiento de grandes volúmenes de datos es crítica.

---

## 🚀 Requisitos e Instalación

Para explorar los ejemplos o ejecutar el código de este repositorio, necesitarás un entorno específico que combine hardware y software.

### Requisitos Previos de Hardware y Software

1.  **GPU NVIDIA:** Es indispensable contar con una tarjeta gráfica NVIDIA compatible con CUDA.
2.  **NVIDIA CUDA Toolkit:** Este proyecto *depende* del NVIDIA CUDA Toolkit. Deberás descargarlo e instalarlo por separado desde el [sitio oficial de NVIDIA Developer](https://developer.nvidia.com/cuda-toolkit).
    * La instalación y uso del CUDA Toolkit están sujetos al Acuerdo de Licencia de Usuario Final (EULA) de NVIDIA.
3.  **Python:** Se recomienda Python 3.8 o superior.

### Configuración del Entorno

1.  **Jupyter Notebook:** Los análisis y ejemplos de este proyecto están presentados en formato de Jupyter Notebooks (`.ipynb`). Es la herramienta que he elegido para ejecutar y visualizar el código interactivamente.
    * Puedes instalar Jupyter Notebook usando:
      ```bash
      pip install jupyterlab
      ```
2.  **Bibliotecas de Python:** El código utiliza Numba para la compilación JIT (Just-in-Time) de CUDA Python, junto con otras bibliotecas estándar.
    * Instala las bibliotecas necesarias:
      ```bash
      pip install numba numpy pandas
      ```
3.  **Ejecución:**
    * Clona este repositorio.
    * Navega a la carpeta del proyecto y ejecuta:
      ```bash
      jupyter-lab
      ```
    * Esto abrirá JupyterLab en tu navegador, permitiéndote abrir y ejecutar los notebooks.

---

## 🧠 El Dilema Principal: Latencia vs. Rendimiento

Mi conclusión clave es que toda la arquitectura se reduce a una filosofía de diseño:

* **CPU (Diseño Orientado a la Latencia):** Una CPU está optimizada para ser extremadamente rápida en tareas **secuenciales**. Su objetivo es minimizar la latencia, es decir, el tiempo que tarda en completar una sola tarea.

* **GPU (Diseño Orientado al Rendimiento):** Una GPU está optimizada para el **rendimiento** (throughput). Su objetivo es ejecutar una cantidad masiva de tareas **en paralelo**, incluso si cada tarea individual tarda un poco más.

---

## 🏛️ Diferencias Arquitectónicas

La filosofía de diseño se refleja directamente en el hardware.

### La CPU: El Especialista Veloz

Una CPU tiene unos pocos núcleos (cores) que son increíblemente potentes. Gran parte del espacio físico del chip se dedica a:

* **Lógica de Control (Control):** Unidades complejas para manejar la ejecución de instrucciones de manera eficiente.
* **Caché (Cache):** Grandes cantidades de memoria caché para tener los datos listos instantáneamente y no tener que esperar a la memoria principal (DRAM).

Esto la hace ideal para código secuencial, donde cada paso depende del anterior.

### La GPU: El Ejército Paralelo

Una GPU adopta el enfoque opuesto. Dedica la gran mayoría de su espacio físico a la computación pura:

* **ALU (Unidades Aritmético-Lógicas):** Cientos o miles de núcleos de procesamiento más simples diseñados para ejecutar la misma instrucción sobre diferentes datos (paralelismo).
* **Menos Caché y Control:** Tiene unidades de control y caché mucho más simples en comparación con una CPU.

Una GPU está diseñada para ejecutar miles de hilos (threads) simultáneamente. En el modelo CUDA, estos hilos se organizan en bloques (blocks) y cuadrículas (grids), permitiendo que una sola instrucción opere sobre miles de puntos de datos a la vez.

---

## 🤝 Cuándo Usar Cada Uno: Cómputo Heterogéneo

En la práctica, las aplicaciones más eficientes no eligen entre una u otra, sino que utilizan ambas. Este modelo se conoce como **cómputo heterogéneo**.

En mi trabajo y estudio, he visto este patrón:

1.  **La CPU actúa como el "Host":** Maneja la lógica general de la aplicación, las tareas secuenciales y la E/S. Prepara los datos.
2.  **Transferencia de Datos:** La CPU copia los datos necesarios del host (su memoria RAM) al "dispositivo" (la memoria de la GPU).
3.  **La GPU actúa como el "Dispositivo":** La CPU lanza un *kernel* (una función de la GPU) para que se ejecute en el dispositivo. La GPU entonces procesa todos los datos en paralelo masivo, logrando una aceleración de 10X o más en estas partes del código.
4.  **Sincronización y Resultados:** La CPU puede continuar con otras tareas y luego esperar (sincronizar) a que la GPU termine. Finalmente, copia los resultados de vuelta a la memoria del host para su verificación o uso posterior.

En resumen, la CPU es el director de orquesta que maneja la lógica compleja y secuencial, mientras que la GPU es la orquesta completa que ejecuta las partes de trabajo pesado y masivamente paralelas.

---

## 📜 Licencia

El código fuente de este proyecto (los archivos `.py` y `.ipynb`) se distribuye bajo la **Licencia MIT**.

El software de terceros del que depende (como el NVIDIA CUDA Toolkit) mantiene sus propias licencias y acuerdos (EULA).
