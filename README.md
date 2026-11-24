# 📡 SimuTelecom — Simulador Web Modular de Señales y Sistemas

**SimuTelecom** es un entorno interactivo basado en diagramas de bloques para el diseño, simulación y análisis de señales y sistemas de telecomunicaciones en los dominios temporal y frecuencial.

La aplicación se ejecuta íntegramente en el navegador web (PC, tablet o móvil, con soporte táctil) desde un único archivo autocontenido (`index.html`), sin necesidad de instalación de software, compiladores ni dependencias de backend.

---

## 🚀 Características Principales

### 1. Fuentes de Señal
* **Generador Continuo (`x(t)`):** Interfaz adaptativa con 9 formas de onda seleccionables:
  * **Sinusoidales:** Coseno y Seno con control de amplitud $A$ y frecuencia $f$ (Hz).
  * **Pulsos y Rampas:** Pulso Rectangular (ancho configurable), Pulso Triangular (duración configurable) y Función Rampa (duración $D$ configurable sobre $[0, D]$).
  * **Sinc (Seno Cardinal):** $x(t) = A \cdot \frac{\sin(\pi t / T)}{\pi t / T}$ con periodo de cruce por cero $T$ configurable.
  * **Singularidades y Señales Causales:** Escalón Unidad $u(t)$, Impulso Delta de Dirac $\delta(t)$ (con evaluación espectral analítica) y Exponencial Decreciente causal $x(t) = A \cdot e^{-t/\tau} \cdot u(t)$ con constante de decaimiento $\tau$ ajustable.
* **Generador Discreto (`x[n]`):** Generador manual de secuencias numéricas separadas por comas sobre índices enteros $n$, con índice de inicio $n_0$ configurable y representación mediante gráficos de tallo (*stem*).
* **Secuencia Digital (`d(t)`):** Conversor de cadenas binarias manuales (ej. `10110010`) a formas de onda físicas con ajuste de tasa binaria $R_b$ (bps), instante inicial $t_0$ (s) y amplitud $A$, soportando 6 códigos de línea:
  * NRZ Unipolar ($0$ / $+A$)
  * NRZ Polar ($-A$ / $+A$)
  * RZ Unipolar ($0$ / $+A$, retorno a cero en mitad de bit)
  * RZ Polar ($-A$ / $0$ / $+A$)
  * AMI Bipolar ($-A$ / $0$ / $+A$, inversión de marcas alternada)
  * Manchester (transición en mitad de bit)

### 2. Transformación de Variable y Sistemas LTI
* **Transformación de Variable (adaptativa al dominio):**
  * **Dominio continuo:** Evalúa $y(t) = x(s \cdot a \cdot (t + t_0))$ con desplazamiento $t_0$ (adelanto $t_0 > 0$ hacia la izquierda, retardo $t_0 < 0$ hacia la derecha), escalado temporal $a$ (compresión $a > 1$ / dilatación $a < 1$) e inversión temporal ($s = -1$).
  * **Dominio discreto:** Adapta automáticamente sus puertos y controles a $x[n] \to y[n]$, con desplazamiento entero $n_0$, diezmado entero ($a \ge 1$) e inversión temporal ($-n$).
* **Sumador Ponderado:** Combinación lineal de dos entradas ($y = k_1 x_1 + k_2 x_2$) con factores independientes, tanto en tiempo continuo como discreto (con alineación automática de índices $n$).
* **Multiplicador / Mezclador:** Producto punto a punto ($y = k \cdot x_1 \cdot x_2$) con ganancia $k$ configurable.
* **Convolución (Discreta y Continua):**
  * **Modo Discreto:** Sumatoria exacta $y[n] = \sum_k x[k] h[n-k]$, calculando automáticamente la longitud ($N_x + N_h - 1$) y el índice origen.
  * **Modo Continuo:** Integración numérica $y(t) = \int x(\tau)h(t-\tau)\,d\tau$ con evaluación analítica / extensión en los extremos para evitar truncamientos artificiales en los bordes de la ventana visible.
* **Filtro LTI:** Configuraciones **Paso Bajo (LPF)**, **Paso Alto (HPF)** y **Paso Banda (BPF, con frecuencia central $f_0$ y ancho de banda $B_w$)**:
  * Respuestas: **Ideal** (corte abrupto) y **Butterworth** (orden configurable, por defecto $4$).
  * Ganancia ajustable y superposición opcional de la curva $|H(f)|$ sobre el espectro de frecuencias.
  * **Reconstrucción:** Interpolación analítica sinc (Whittaker–Shannon) sobre señales muestreadas.
* **Separación Estricta de Dominios:** Sumador, Multiplicador y Convolución validan la compatibilidad de las entradas y marcan visualmente el error (borde rojo) si se intentan mezclar señales continuas con discretas.

### 3. Conversión A/D (PCM)
* **Muestreo Ideal (`xs(t)`):** Muestreo por tren de deltas a frecuencia $f_s$ configurable ($T_s = 1/f_s$), generando las muestras instantáneas en $n T_s$ y el espectro analítico replicado:
  $$X_s(f) = \frac{1}{T_s} \sum_{k=-\infty}^{\infty} X(f - k f_s)$$
* **Cuantizador A/D (`xq(t)`):**
  * Número de niveles $L$ ($2$ a $1024$) y rango dinámico $\pm V_{\max}$ (V) configurables.
  * **6 modos de cuantización:** Uniforme (**Midrise** y **Midtread**) y No Uniforme mediante compansión logarítmica **Ley $\mu$** (parámetro $\mu$, por defecto $255$) y **Ley A** (parámetro $A$, por defecto $87.6$), ambas en variantes Midrise y Midtread.
  * **Panel integrado en el bloque:** Mini-gráficas en vivo de la característica de transferencia $y_q(x)$ y del error de cuantización $e_q(x) = x - y_q(x)$, métricas en tiempo real de error medio absoluto ($|e|$) y SQNR (dB), y vista previa de niveles.
  * **Modal interactivo (*Ver Característica y Niveles*):** Gráficas Plotly detalladas de $y_q(x)$ y $e_q(x)$ junto con la tabla completa de niveles (tensión reconstruida, intervalo de entrada y conteo de muestras por nivel).
* **Codificador PCM (`d_pcm(t)`):**
  * Asigna palabras binarias de $n$ bits por muestra ($1$ a $16$ bits) y genera la forma de onda en línea (**NRZ Unipolar**, **NRZ Polar** o **Manchester**) con amplitud configurable y tasa binaria automática $R_b = n \cdot f_s$.
  * **Validación automática de capacidad:** Comprueba que $2^n \ge L$; si los bits son insuficientes, resalta el bloque en rojo e indica el mínimo necesario ($\lceil \log_2 L \rceil$).
  * **Tabla integrada y modal completo (*Ver Tabla Completa Niveles → Código*):** Muestra la tasa $R_b$, vista previa de la trama binaria y la correspondencia Nivel $\to$ Tensión $\to$ Código binario.

### 4. Modulación y Demodulación
* **Modulador AM:** Modulación en amplitud $s_{\text{AM}}(t) = A_c [1 + k_a m(t)] \cos(2\pi f_c t)$ con portadora $f_c$, amplitud $A_c$ e índice $\mu$. Calcula automáticamente la amplitud pico $A_m = \max|m(t)|$ y ajusta $k_a = \mu / A_m$ (mostrados en el bloque) para mantener exacto el índice $\mu$.
* **Modulador FM:** Modulación en frecuencia mediante integración de fase instantánea con portadora $f_c$, amplitud $A_c$ e índice de modulación $\beta$ configurable (normalizando la moduladora a su pico y usando $R_b$ como frecuencia de referencia para señales digitales).
* **Modulador PM:** Modulación en fase $s_{\text{PM}}(t) = A_c \cos(2\pi f_c t + k_p m(t))$ con portadora $f_c$, sensibilidad $k_p$ (rad) y amplitud $A_c$.
* **Modulador QAM (4-QAM / 16-QAM):** Modulación en cuadratura $s_{\text{QAM}}(t) = A_c [I \cos(2\pi f_c t) - Q \sin(2\pi f_c t)]$ con constelaciones **4-QAM (QPSK)** y **16-QAM** usando codificación Gray (normalizada por $\sqrt{10}$ en 16-QAM), portadora $f_c$, tasa de símbolos en baudios (sym/s), secuencia binaria de entrada o *fallback* configurable, y **diagrama de constelación I/Q integrado** en el propio nodo.
* **Demodulador QAM:** Demodulación coherente por correlación / mínimos cuadrados I/Q y decisión por mínima distancia euclídea (4-QAM y 16-QAM), con modo **Auto-Sincronizar** (o ajuste manual de $f_c$, baudios, $t_0$ y n.º de símbolos), reconstrucción de la secuencia binaria $d_{\text{rec}}(t)$, **constelación recibida I/Q** y **tabla de símbolos decodificados** (bits, frecuencia estimada y fase en grados) integradas en el nodo.
* **Detector de Envolvente:** Demodulación asíncrona de AM mediante rectificador de onda completa ($|x|$), circuito de descarga RC con constante de tiempo $\tau = RC$ ajustable y opción **Quitar DC**.

### 5. Canal de Comunicaciones
* **Canal (Aten/Ruido):** Modela un medio físico con **atenuación** ($\alpha$), **ruido blanco aditivo gaussiano AWGN** (desviación típica $\sigma$ generada por Box–Muller) y **retardo temporal** (s), propagando metadatos de amplitud y retardo para simulaciones QAM de extremo a extremo.

### 6. Subsistemas Jerárquicos, Encapsulado y Protección
* **Caja Subsistema y Puertos (`Inport` / `Outport`):** Encapsula diagramas complejos, permite navegación por doble clic y sincroniza automáticamente sus puertos externos con los puertos internos.
* **Selección Múltiple y Encapsulado Automático (`📦 Encapsular Selección`):** Selecciona varios bloques en el lienzo (manteniendo `Shift` + arrastrar o activando el modo selección `⬚`) y agrúpalos en un subsistema desde la barra lateral, el botón flotante o el menú contextual, generando automáticamente sus puertos de entrada/salida y preservando enlaces y osciloscopios.
* **Protección con Contraseña (SHA-256):** Desde el menú contextual de cualquier **Caja Subsistema** (*Proteger con Contraseña...* / *Quitar Contraseña...*) se puede bloquear el acceso a su circuito interno, útil para prácticas docentes tipo «caja negra».

### 7. Visualización y Medición
* **Osciloscopios Múltiples:** Inserta tantos osciloscopios como necesites y selecciona la señal activa haciendo clic sobre el bloque o desde el selector superior (`Ver:`). Grafica el **dominio del tiempo** (continuo o *stem*) y el **espectro de frecuencia $|X(f)|$** (801 puntos adaptativos en $[-f_{\max}, f_{\max}]$, soporte analítico de deltas, DTFT discreta y superposición de $|H(f)|$), con zoom interactivo persistente en Plotly.
* **Osciloscopio Diagrama de Ojo:** Superpone trazas en ventanas de $2 T_b$ con detección automática (`Auto Tb/Ts`) o periodo $T_b$, offset $t_0$ y número de trazas configurables. Se representa tanto en el panel principal Plotly (indicando el instante de decisión en $T_b$) como en una **mini-gráfica integrada** en el nodo, junto con métricas en vivo: **Apertura Vertical (V)**, **Ancho de Ojo (s)**, **Factor Q**, **Jitter P-P (ms)** y **Jitter RMS (ms)**.
* **Medidor de BER (Tasa de Error de Bit):** Compara las señales `Tx (Original)` y `Rx (Recibida)` con **auto-alineación de retardo**, modo acumulativo o instantáneo y botón de reinicio. Muestra en su panel integrado el **BER Acumulado**, **BER Instantáneo**, total de bits y errores, retardo detectado y comparativa visual bit a bit (`Tx` vs `Rx`).

### 8. Control de Simulación, Persistencia e Interfaz
* **Control de Ejecución:** La aplicación arranca en pausa para facilitar el diseño de esquemas grandes; permite **▶ Reanudar / ⏸ Pausar Simulación** (desde la barra lateral o el botón flotante) y avanzar **paso a paso (`⏭ Paso`)**.
* **Guardar, Cargar y Combinar Esquemáticos:** Exportación e importación de archivos **JSON** (`esquematico_telecom.json`) y guardado rápido en **`localStorage`** del navegador. Si el lienzo ya contiene bloques al cargar un esquema, los nuevos módulos se **combinan** desplazados automáticamente sin borrar el circuito actual.
* **Interfaz Adaptable y Soporte Táctil:** Barra lateral con categorías en **acordeón desplegable**, controles flotantes en el lienzo (Play/Pause, modo selección `⬚`, encapsulado `📦`, zoom `➕`/`➖` y centrar `⛶`), gestos táctiles (*pinch-to-zoom* y paneo con dos dedos), panel de gráficas redimensionable/ocultable, botón para mostrar/ocultar $|X(f)|$ y actualización manual con **🔄 Refrescar Gráfica**.

---

## 🛠️ Arquitectura Técnica

El proyecto sigue una arquitectura ligera autocontenida sin paso de compilación (*zero-build step*) en un único archivo [index.html](./index.html):

* **[LiteGraph.js](https://github.com/jagenjo/litegraph.js):** Motor gráfico para la gestión de nodos, conexiones, subgrafos jerárquicos y serialización en JSON.
* **[Plotly.js](https://plotly.com/javascript/) (v2.27.0):** Renderizado científico interactivo para las curvas temporales, espectros frecuenciales, diagramas de ojo y gráficas del cuantizador.
* **Motor Matemático en Vanilla JS + HTML5 Canvas:** Evaluación numérica y analítica a intervalos de 60 ms desacoplada del bucle de renderizado (`requestAnimationFrame`), junto con paneles embebidos personalizados (`onDrawForeground`) para cuantización, codificación PCM, constelaciones QAM, diagrama de ojo y medidor de BER.
* **APIs Estándar del Navegador:** Web Crypto API (`SHA-256`) para protección de subsistemas, File API para carga/descarga de esquemáticos JSON y `localStorage` para persistencia local.

---

## 💻 Puesta en Marcha

### Uso Inmediato en Local
1. Clona el repositorio o descarga el archivo `index.html`:
   ```bash
   git clone https://github.com/miguelpoveda92-byte/signal_analyser.git
   cd signal_analyser
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

### Documentación Adicional
* Consulta la guía detallada en [Manual de Usuario - SimuTelecom (GTOMA).pdf](./Manual%20de%20Usuario%20-%20SimuTelecom%20(GTOMA).pdf).

---

## 📖 Modo de Uso Básico

1. **Añadir Bloques:** Despliega las categorías de la barra lateral izquierda y haz clic sobre los módulos deseados.
2. **Cablear y Configurar:** Conecta los terminales de salida (derecha) con los de entrada (izquierda) y ajusta los parámetros directamente sobre cada bloque.
3. **Ejecutar la Simulación:** Pulsa **▶ Reanudar Simulación** para simular en continuo o **⏭ Paso** para evaluar un paso discreto de cálculo (la aplicación inicia pausada para facilitar la edición).
4. **Visualizar y Medir:** Conecta un **Osciloscopio**, **Osciloscopio Diagrama de Ojo** o **Medidor de BER**, selecciónalo en el lienzo o en el selector `Ver:`, y ajusta los rangos de tiempo ($t_{\min}$ a $t_{\max}$), $\pm f_{\max}$ o el zoom interactivo en Plotly.
5. **Organizar y Guardar:** Selecciona varios bloques (`Shift` + arrastrar o botón `⬚`) para encapsularlos en un subsistema (protegible con contraseña desde su menú contextual) y guarda o combina tus circuitos mediante archivos JSON o el almacenamiento del navegador.

---

## 📄 Licencia

Este proyecto está distribuido bajo la licencia **MIT**.
