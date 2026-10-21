# 📡 SimuTelecom — Simulador Web Modular de Señales y Sistemas

**SimuTelecom** es un entorno interactivo basado en diagramas de bloques y ejecución en tiempo real para el diseño, procesamiento y análisis de señales en los dominios temporal y frecuencial. 

La aplicación se ejecuta íntegramente en el navegador web (PC, tablet o móvil) sobre tecnologías estándar de frontend, sin necesidad de instalación de software, compiladores ni dependencias de backend.

---

## 🚀 Características Principales

### 1. Generación de Señales
* **Señales Continuas:** Generador con interfaz adaptativa según la forma de onda seleccionada:
  * Sinusoidales: Coseno y Seno con control de amplitud y frecuencia.
  * Pulsos y Singularidades: Pulso rectangular (ancho configurable), escalón unitario $u(t)$ e impulso delta de Dirac $\delta(t)$.
  * Exponencial decreciente causal: $x(t) = A \cdot e^{-t/\tau} \cdot u(t)$ con constante de decaimiento $\tau$ ajustable.
* **Señales Discretas:** Generador manual de secuencias numéricas sobre índices enteros $n$. Permite definir listas de muestras arbitrarias y el índice de inicio $n_0$, representadas mediante gráficos de tallo (*stem*).
* **Señales Digitales en Banda Base:** Conversor de cadenas binarias manuales (ej. `10110010`) a formas de onda físicas utilizando códigos de línea:
  * NRZ Unipolar ($0$ / $+A$).
  * NRZ Polar ($-A$ / $+A$).
  * Manchester (transición en mitad de bit).

### 2. Transformaciones de la Variable Independiente
Módulo específico para evaluar operaciones sobre el argumento temporal:
$$y(t) = x(s \cdot a \cdot (t + t_0))$$
* **Desplazamiento ($t_0$):** Convenio estricto de adelanto ($t_0 > 0$, desplazamiento hacia la izquierda) y retardo ($t_0 < 0$, desplazamiento hacia la derecha).
* **Escalado temporal ($a$):** Compresión ($a > 1$) o dilatación ($a < 1$).
* **Inversión temporal ($s = -1$):** Reflexión de la señal respecto al eje de ordenadas ($x(-t)$).

### 3. Operaciones Lineales y Sistemas LTI
* **Sumador Ponderado:** Combinación lineal de dos entradas ($y = k_1 x_1 + k_2 x_2$) con factores de escala independientes.
* **Mezclador / Multiplicador:** Producto punto a punto ($y = k \cdot x_1 \cdot x_2$).
* **Convolución:**
  * **Modo Discreto:** Sumatoria exacta $y[n] = \sum_k x[k] h[n-k]$, calculando automáticamente la longitud ($N_x + N_h - 1$) y el origen $n_0$.
  * **Modo Continuo:** Integración numérica $y(t) = \int x(\tau)h(t-\tau)d\tau$ equipada con extensión analítica (*anti-edge truncation*) para evitar caídas artificiales a cero en los bordes del rango visible con señales de soporte infinito como escalones o exponenciales.
* **Separación Estricta de Dominios:** Los operadores validan los tipos de entrada y bloquean la conexión si se intentan mezclar señales continuas con discretas.

### 4. Muestreo y Filtrado
* **Muestreo Ideal:** Simulación del muestreo por tren de deltas a frecuencia $f_s$ programable. Calcula tanto las muestras instantáneas en $n T_s$ como el espectro analítico replicado:
  $$X_s(f) = \frac{1}{T_s} \sum_{k=-\infty}^{\infty} X(f - k f_s)$$
* **Filtros LTI:** Filtros configurables en configuraciones Paso Bajo (LPF), Paso Alto (HPF) y Paso Banda (BPF):
  * Respuestas: *Ideal* (corte abrupto) y *Butterworth* (orden $N$ configurable).
  * Reconstrucción: Interpolación analítica sinc ($\text{Whittaker-Shannon}$) sobre señales muestreadas para eliminar artefactos y residuos espectrales fuera de banda.
  * Superposición de la curva $|H(f)|$ del filtro sobre el espectro de la señal.

### 5. Modulación y Demodulación
* **Modulación AM:** Modulación en amplitud convencional $s(t) = A_c [1 + \mu \cdot m(t)] \cos(2\pi f_c t)$ con ajuste continuo del índice $\mu$ y portadora $f_c$.
* **Modulación FM:** Modulación en frecuencia mediante integración de fase instantánea $s(t) = A_c \cos(2\pi f_c t + 2\pi \beta k \int m(\tau)d\tau)$ con control del índice de modulación $\beta$.
* **Detector de Envolvente:** Demodulación asíncrona de AM mediante rectificador (onda completa o media onda) seguido de circuito de descarga con constante de tiempo $\tau = RC$ y filtro de bloqueo de continua (DC).

### 6. Subsistemas Jerárquicos
* Bloque **Subsistema** que permite encapsular diagramas complejos para mantener limpio el área de trabajo.
* Navegación por doble clic para editar el circuito interno.
* Bloques dedicados **Inport** y **Outport** que aseguran persistencia total de las conexiones externas e internas al entrar y salir del subsistema.

### 7. Visualización y Análisis en Tiempo Real
* **Osciloscopios Múltiples:** Inserción de tantos visualizadores como se requiera. La selección de la señal activa se realiza directamente haciendo clic sobre cualquier osciloscopio en el lienzo.
* **Dominio del Tiempo:** Graficado continuo de alta resolución o trazado tipo *stem* para secuencias discretas.
* **Espectro de Frecuencia:** Evaluación de la Transformada de Fourier normalizada (para mantener invariable la amplitud de señales periódicas frente al tamaño de ventana temporal).
* **Control de Interfaz:** Panel de gráficas con separador arrastrable para ajustar dimensiones, botón para ocultar/mostrar el visor y botón para alternar la visualización del espectro frecuencial.

---

## 🛠️ Arquitectura Técnica

El proyecto sigue una arquitectura ligera sin compilación previa (*zero-build step*):

* **[LiteGraph.js](https://github.com/jagenjo/litegraph.js):** Motor gráfico para la gestión de nodos, conexiones, serialización y flujo de datos visual.
* **[Plotly.js](https://plotly.com/javascript/):** Renderizado científico interactivo de curvas y espectros.
* **Motor Matemático en Vanilla JS:** Algoritmos numéricos optimizados desacoplados del bucle de renderizado mediante `requestAnimationFrame` para mantener tasas estables de 60 FPS.

---

## 💻 Puesta en Marcha

### Uso Inmediato en Local
1. Clona el repositorio o descarga el archivo `index.html`:
   ```bash
   git clone https://github.com/<tu-usuario>/<tu-repositorio>.git
   cd <tu-repositorio>
   ```
2. Abre `index.html` en cualquier navegador web moderno:
   * **Windows:** Doble clic sobre el archivo o `start index.html`
   * **macOS:** `open index.html`
   * **Linux:** `xdg-open index.html`

### Despliegue en la Web (GitHub Pages)
1. Sube el archivo `index.html` a la rama principal (`main` o `master`) de tu repositorio en GitHub.
2. Ve a **Settings** > **Pages**.
3. En **Branch**, selecciona `main` (carpeta `/root`) y pulsa **Save**.
4. La aplicación estará accesible públicamente en `https://<tu-usuario>.github.io/<tu-repositorio>/`.

---

## 📖 Modo de Uso Básico

1. **Añadir Bloques:** Haz clic en los módulos de la barra lateral izquierda (Fuentes, Sistemas, Modulaciones o Visores).
2. **Cablear Señales:** Haz clic sobre el conector circular de salida (derecha) de un bloque y arrastra el cable hasta el conector de entrada (izquierda) del siguiente bloque.
3. **Modificar Parámetros:** Haz clic directamente sobre los valores numéricos, selectores o cajas de texto de cada nodo para ajustar sus magnitudes en tiempo real.
4. **Ver Formas de Onda:** Conecta un bloque a un **Osciloscopio** y selecciónalo haciendo clic sobre él en el lienzo.
5. **Ajustar Ventanas:** Modifica los rangos de tiempo ($t_{min}$ a $t_{max}$) y frecuencia máxima en la barra de controles superior del panel de gráficas.

---

## 📄 Licencia

Este proyecto está distribuido bajo la licencia **MIT**. Consulta el archivo `LICENSE` para más detalles.
