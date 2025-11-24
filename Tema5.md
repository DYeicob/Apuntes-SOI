# 🌟 **TEMA 5 – Gestión de Entrada/Salida (E/S)**

**Resumen completo y simplificado**

---

# 🎯 **Competencias del tema**

* Entender **problemas** derivados de la gran **diversidad de dispositivos de E/S**.
* Comprender la **estructura del software de E/S** y cómo se comunican sus niveles.
* Desarrollar **capacidad de abstracción**.
* Aplicar estrategias ya aprendidas a **problemas nuevos**.

---

# 1️⃣ **Principios de la gestión de E/S**

## 1.1. 🧩 **Problemática de los dispositivos de E/S**

Los dispositivos de entrada/salida son muy distintos entre sí. Las principales diferencias:

1. **Velocidad**

   * Los periféricos van MUCHO más lentos que la CPU.
   * También difieren entre sí (disco vs teclado, etc.).

2. **Unidad de transferencia**

   * Pueden trabajar con **caracteres**, **bytes**, **bloques**, **palabras**, **registros**, etc.

3. **Representación de datos**

   * Codificaciones distintas (ASCII, Unicode, códigos del dispositivo…).

4. **Operaciones permitidas**

   * Algunos solo leen (entrada), otros solo escriben (salida), otros ambos.

5. **Condiciones de error**

   * Cada dispositivo puede fallar por motivos diferentes.

👉 **Conclusión:** la heterogeneidad obliga al sistema a “homogeneizar” todo.

---

## 1.2. 🎯 **Objetivos del software de E/S**

### 1️⃣ **Independencia del periférico**

* Los programas deben funcionar **sin importar**:

  * el **modelo** del dispositivo;
  * e incluso, **el tipo** del dispositivo usado.
* El SO se encarga de abstraerlos.

### 2️⃣ **Eficiencia**

* Minimizar tiempos muertos.
* Aprovechar interrupciones, buffers, etc.

### 3️⃣ **Tratamiento uniforme**

A) **Independencia del código de caracteres**

* Códigos externos → convertidos a un **código interno uniforme**.
* Conversión:

  * Tras la entrada
  * Antes de la salida

B) **Streams (periféricos virtuales)**

* Los programas NO trabajan con dispositivos físicos, sino con **streams**.
* El SO mantiene una **lista de descriptores** que asocia stream ↔ dispositivo físico.

C) **Tablas de descriptores de periférico**
Cada dispositivo tiene un descriptor con:

* Identificación
* Instrucciones válidas
* Tablas para traducciones
* Estado (libre/ocupado/estropeado)
* Proceso que lo usa

---

## 1.3. ⚙️ **Principios hardware de E/S**

### 🔹 A. Tipos de dispositivos

* **Dispositivos de bloques**

  * Trabajan con bloques fijos (discos, SSD…).
* **Dispositivos de caracteres**

  * Flujo continuo sin estructura (teclado, ratón…).

### 🔹 B. Controladores de dispositivo

* Cada dispositivo tiene un **controlador**, que se comunica con la CPU mediante **registros (puertos)**.
* A través de ellos se envían:

  * **Comandos**
  * **Parámetros**
  * **Datos**

---

### 1.3.1. 📝 **E/S controlada por programa (polling)**

* La **CPU espera activamente** a que la operación termine.
* Tiene que leer continuamente el registro de estado → **ineficiente**.

**Pasos:**

1. Inicializar controlador
2. Iniciar lectura
3. Consultar estado hasta que termine
4. Leer el dato

👉 La CPU está **ocupada todo el tiempo**.

---

### 1.3.2. 🔔 **E/S controlada por interrupciones**

* La CPU **no espera**: hace otras tareas y solo atiende la E/S cuando llega una **interrupción**.
* Permite **solapar** muchas operaciones de E/S.

👉 Mucho más eficiente.

---

# 2️⃣ **Estructura del software de E/S**

Organizado en **niveles**, donde los inferiores ocultan el hardware a los superiores.

### 🎯 Objetivos generales

1. **Independencia del dispositivo**
2. **Manejo cercano de errores**
3. **Simular transferencias controladas por programa**
4. **Gestionar dispositivos no compartibles**

---

## 2.1. 🧱 **Niveles del software de E/S** (según Tanenbaum)

### 🔼 **1. Software de E/S a nivel de usuario**

(No forma parte del SO → son librerías)

Funciones:

* Preparar parámetros para llamadas al SO
* Informar de errores
* Opcional: formatear o interpretar datos

---

### 🔼 **2. Software de E/S independiente del dispositivo**

(Sí forma parte del SO)

Funciones:

1. Ofrecer una interfaz de E/S común
2. Gestionar nombres simbólicos → dispositivo real
3. Controlar permisos
4. Proporcionar **bloques uniformes** a pesar del hardware
5. Gestionar *buffers*
6. Gestionar espacio en dispositivos de bloques
7. Controlar el acceso a dispositivos no compartibles
8. Tratamiento de errores independiente del dispositivo

---

### 🔼 **3. Software dependiente del dispositivo (Device Driver)**

* Parte más cercana al hardware.
* Recibe peticiones abstractas y las convierte en operaciones concretas.

Funciones:

1. Traducir peticiones abstractas
2. Escribir comandos en los puertos del controlador
3. Esperar finalización

   * Si hay interrupciones → se bloquea
   * Si es controlado por programa → no
4. Comprobar errores
5. Gestionar cola de peticiones

---

### 🔼 **4. Manejadores de interrupciones**

* Rutinas que se ejecutan cuando llega una interrupción.
* Desbloquean al driver y permiten continuar.

---

## 2.2. 🧠 **Ejemplo del funcionamiento entre niveles**

1. **Usuario** llama a una rutina de E/S
2. Se identifica el periférico y se construye un **IORB**
3. Se mete el IORB en la cola del dispositivo
4. El *driver* extrae el IORB, inicia la operación
5. Cuando termina (interrupción) → se avisa a niveles superiores
6. Se informa al proceso de que la petición está servida

---

# 2.3. 📦 **Técnica del *buffering***

Evita que el proceso se bloquee al esperar el dispositivo.

### ¿Cómo funciona?

* El SO hace cargas/descargas hacia un **búfer intermedio**:

  * **Entrada**: el SO llena el búfer y el proceso lee de ahí
  * **Salida**: el proceso escribe en el búfer y el SO lo vacía al dispositivo

### Útil si:

* La velocidad del proceso no supera la del periférico en promedio.

---

# 2.4. 🖨️ **Técnica del *spooling***

Para **dispositivos no compartibles** (como impresoras).

### Funcionamiento:

1. El proceso abre un stream → se crea un **fichero temporal** en disco
2. Todas las salidas van a ese fichero
3. Al cerrar el stream, se añade a la cola de “trabajos pendientes”
4. El **spooler** (proceso independiente) envía uno por uno los trabajos al dispositivo real

👉 Evita que los procesos queden bloqueados esperando un dispositivo exclusivo.
👉 Permite que varios trabajos “convivan” sin usar la impresora simultáneamente.

---

# 📌 **Resumen visual final**

* **Problemas de E/S** → velocidad, codificación, unidad, errores…
* **Objetivos del software** → independencia + eficiencia + uniformidad
* **Hardware** → controladores, puertos, interrupciones
* **Niveles del software**

  1. Usuario (librerías)
  2. Independiente del dispositivo
  3. Driver
  4. Manejadores de interrupciones
* **Técnicas**

  * *Buffering* → búfer intermedio para evitar bloqueos
  * *Spooling* → cola en disco para dispositivos no compartibles

---
