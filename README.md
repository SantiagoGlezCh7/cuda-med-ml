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



## 🐛 El Desafío: Conflictos de Entorno (PyTorch vs. Numba)

Durante el desarrollo inicial, **descubrí un conflicto de dependencias fundamental** al intentar instalar `numba` (que requiere un `cudatoolkit` específico de `conda-forge`) junto con `pytorch` (que impone su propio paquete `pytorch-cuda` y bibliotecas asociadas).

Conda no pudo resolver una configuración compatible que satisficiera ambos paquetes simultáneamente en el mismo entorno. Esto llevó a errores persistentes como `LibMambaUnsatisfiableError` o `FileNotFoundError: nvvm.dll`.

**💡 La Solución Adoptada:** La estrategia más robusta y que recomiendo es mantener **entornos Conda separados**:

1.  Un entorno dedicado exclusivamente a **Numba y código CUDA** (el descrito en este `README`, necesario para ejecutar este proyecto).
2.  Otro entorno separado para **PyTorch** (para proyectos futuros de Machine Learning).

---
## 🚀 Configuración del Entorno (Numba CUDA para Este Proyecto)

Para ejecutar el código de este repositorio (`Montecarlo_cuda.ipynb`), es **esencial** crear y configurar un entorno Conda específico para Numba de la siguiente manera:

### Requisitos Previos

1.  **GPU NVIDIA:** Indispensable contar con una tarjeta gráfica NVIDIA compatible con CUDA.
2.  **NVIDIA CUDA Toolkit (Instalación del Sistema):** Aunque Conda instalará sus propias bibliotecas, recomiendo tener el CUDA Toolkit oficial instalado en el sistema (descargado desde [NVIDIA Developer](https://developer.nvidia.com/cuda-toolkit)). Esto asegura que los *drivers* de la GPU estén actualizados. La instalación de Conda manejará las bibliotecas necesarias para Numba.
3.  **Miniconda/Anaconda:** Necesitas una instalación de Conda.

### Pasos de Configuración

4. Crear el Entorno Conda (`env_numba`):

Abre tu terminal (Anaconda Prompt) y ejecuta este comando. Usamos `conda-forge` como canal prioritario para asegurar la compatibilidad:  

conda create -n env_numba -c conda-forge python=3.10 numba cudatoolkit jupyterlab ipykernel numpy

- `-n env_numba`: Nombre del entorno.
- `-c conda-forge`: Prioriza este canal. Es crucial para la compatibilidad Numba/cudatoolkit.
- `python=3.10`: Versión de Python (ajusta si es necesario).
- `numba cudatoolkit`: Instala Numba y las bibliotecas CUDA correspondientes.
- `jupyterlab ipykernel numpy`: Herramientas para ejecutar el notebook.

5. Activar el Entorno:

Cada vez que trabajes en este proyecto, activa el entorno:

conda activate env_numba

(Tu terminal debería mostrar `(env_numba)` al inicio).

 6. Conectar el Entorno a Jupyter

Para que Jupyter Notebook/Lab reconozca este entorno, regístralo como un "Kernel":
python -m ipykernel install --user --name="env_numba" --display-name="Python (Numba Only)"

 `--display-name="Python (Numba Only)"`: Este es el nombre que verás en Jupyter.

---
## ▶️ Cómo Ejecutar el Notebook

1. Clona este repositorio a tu máquina local.
2. Abre tu terminal (Anaconda Prompt).
3. Crea el entorno (`env_numba`)
4. Activa el entorno Conda: `conda activate env_numba`.
6. Navega a la carpeta donde clonaste el repositorio.
7. Inicia JupyterLab: `jupyter-lab`.
8. Abre el notebook `Montecarlo_cuda.ipynb`.
9. **¡Verifica y Selecciona el Kernel Correcto!** Dentro de Jupyter, ve al menú **Kernel > Change Kernel...** y asegúrate de seleccionar **"Python (Numba Only)"**. Si está seleccionado otro, cámbialo.
10. Ejecuta las celdas del notebook en orden.




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

El código fuente de este proyecto ( `.ipynb`) se distribuye bajo la **Licencia MIT**.

El software de terceros del que depende (como el NVIDIA CUDA Toolkit) mantiene sus propias licencias y acuerdos (EULA).
