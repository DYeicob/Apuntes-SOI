# 🟦 ACTIVIDAD 6 – Programación del Shell
Guía completa para crear, ejecutar y probar los 3 ejercicios de shell.
---

# 🔵 PASOS COMUNES PARA LOS 3 EJERCICIOS

### 1️⃣ Crear el script

```bash
vi ejercicio1.sh
vi ejercicio2.sh
vi ejercicio3.sh
```

* Pulsa `i` para entrar en modo edición.
* Pega el contenido del script.
* Para guardar y salir: `ESC` → `:wq` → `ENTER`.

### 2️⃣ Dar permisos de ejecución

```bash
chmod +x ejercicio1.sh ejercicio2.sh ejercicio3.sh
```

*(Solo una vez.)*

### 3️⃣ Ejecutar el script

* **Ejercicio 1:** requiere 2 o más argumentos
* **Ejercicio 2:** requiere 3 o más argumentos
* **Ejercicio 3:** requiere 3 o más argumentos

Ejemplos:

```bash
./ejercicio1.sh 3 7 10
./ejercicio2.sh 4 1 5 9
./ejercicio3.sh script.sh a script.sh
```

Cada script mostrará su menú correspondiente.

---

# 🟢 EJERCICIO 1 — `ejercicio1.sh`

**Requiere 2 o más argumentos.**

### Menú y opciones

```
Menú [O] Ordenados
[D] Directorio
[L] Listado
[R] Resto
[C] Contar
[F] FIN
Opción:
```

#### ✔ O → Ordenados

Comprueba si los números están en orden ascendente.

```bash
./ejercicio1.sh 1 2 5 10
# Menú → O
# Salida: Están ordenados ascendentemente
```

#### ✔ R → Resto

Calcula el resto entre números consecutivos.

```bash
./ejercicio1.sh 10 3 5
# Menú → R
# Salida:
# Resto entre 10 y 3 = 1
# Resto entre 3 y 5 = 3
```

#### ✔ D → Directorio

Crea enlaces en un directorio a los archivos ejecutables.

```bash
./ejercicio1.sh scripts/script1.sh scripts/script2.sh
# Menú → D
# Directorio: directorios/dir1
```

#### ✔ L → Listado

Cuenta:

* Directorios
* Ficheros no vacíos

```bash
./ejercicio1.sh directorios/dir1 archivos/archivo1.txt
# Menú → L
# Salida:
# Número de directorios: 1
# Número de ficheros no vacíos: 1
```

#### ✔ C → Contar

Guarda estadísticas (líneas, palabras, bytes) en un fichero nuevo.

```bash
./ejercicio1.sh archivos/archivo1.txt archivos/archivo2.txt archivos/archivo3.txt
# Menú → C
# Nombre del nuevo fichero: resumen.txt
```

#### ✔ F → Fin

Termina el programa:

```
Fin del programa
```

---

# 🟣 EJERCICIO 2 — `ejercicio2.sh`

**Requiere 3 o más argumentos numéricos.**

### Menú y opciones

```
Menú [S] Sucesión
[C] Contar
[P] Potencia
[F] FIN
Opción:
```

#### ✔ S → Sucesión

Comprueba si cada término = anterior + primer argumento.

```bash
./ejercicio2.sh 4 1 5 9
# Menú → S
# Salida: Cumple la sucesión
```

#### ✔ C → Contar

Cuenta ficheros en un directorio y guarda el número en `recuento`.

```bash
# Menú → C
# Directorio: directorios/dir1
```

#### ✔ P → Potencia

Eleva cada argumento a un exponente indicado por el usuario.

```bash
# Menú → P
# Exponente: 3
# Salida:
# 4^3 = 64
# 1^3 = 1
# 5^3 = 125
# 9^3 = 729
```

#### ✔ F → Fin

Termina el programa:

```
Fin del programa
```

---

# 🟠 EJERCICIO 3 — `ejercicio3.sh`

**Requiere 3 o más argumentos.**

### Menú y opciones

```
Menú [P] Predecir
[I] Inodo
[T] Triangular
[F] FIN
Opción:
```

#### ✔ P → Predecir

Cuenta cuántas veces aparece el nombre del propio script.

```bash
./ejercicio3.sh ejercicio3.sh x ejercicio3.sh y
# Menú → P
# Número mínimo de repeticiones: 2
# Salida: Has acertado o superado el mínimo
```

#### ✔ I → Inodo

Cuenta ficheros con un inodo determinado en un directorio.

```bash
# Menú → I
# Número de inodo: <obtenido con ls -i>
# Directorio: directorios/dir2
```

#### ✔ T → Triangular

Comprueba la sucesión triangular: 1, 3, 6, 10, 15, …

```bash
./ejercicio3.sh 1 3 6 10 15
# Menú → T
# Salida: Cumple la sucesión triangular
```

#### ✔ F → Fin

Termina el programa:

```
Fin del programa
```

---

# ✅ Códigos de los scripts

### **ejercicio1.sh**

```bash
#!/bin/bash
[ $# -lt 2 ] && echo "Error de sintaxis" && exit 1

ordenados() {
    prev=$1
    shift
    for n in "$@"; do
        [ $n -lt $prev ] && echo "No están ordenados" && return
        prev=$n
    done
    echo "Están ordenados ascendentemente"
}

directorio() {
    read -p "Introduce un directorio existente: " dir
    ls "$dir" >/dev/null 2>&1 || { echo "Ese directorio no existe"; return; }
    for arg in "$@"; do
        test -f "$arg" -a -x "$arg" && ln "$arg" "$dir" || \
            echo "No es un fichero o no puedo ejecutarlo"
    done
}

listado() {
    dirs=0
    files=0
    for arg in "$@"; do
        ( test -d "$arg" && dirs=$((dirs+1)) ) || \
        ( test -f "$arg" -a -s "$arg" && files=$((files+1)) ) || \
        echo "No es un fichero no vacío o directorio"
    done
    echo "Número de directorios: $dirs"
    echo "Número de ficheros no vacíos: $files"
}

resto() {
    args=("$@")
    for ((i=0; i<$#-1; i++)); do
        a=${args[i]}
        b=${args[i+1]}
        [ $b -eq 0 ] && echo "No se puede dividir por 0" && return
        echo "Resto entre $a y $b = $((a % b))"
    done
}

contar() {
    read -p "Introduce el nombre de un nuevo fichero: " fichero
    test -e "$fichero" && echo "Ese fichero ya existe" && return
    wc "$@" > "$fichero"
    echo "Datos guardados en $fichero"
}

while true; do
    echo "Menú [O] Ordenados"
    echo "[D] Directorio"
    echo "[L] Listado"
    echo "[R] Resto"
    echo "[C] Contar"
    echo "[F] FIN"
    read -p "Opción: " op
    case "$op" in
        O|o) ordenados "$@" ;;
        D|d) directorio "$@" ;;
        L|l) listado "$@" ;;
        R|r) resto "$@" ;;
        C|c) contar "$@" ;;
        F|f) echo "Fin del programa"; exit ;;
        *) echo "Opción no válida" ;;
    esac
done
```

---

### **ejercicio2.sh**

```bash
#!/bin/bash
[ $# -lt 3 ] && echo "Error de sintaxis" && exit 1

sucesion() {
    base=$1
    prev=$2
    shift 2
    for n in "$@"; do
        esperado=$((prev + base))
        [ $n -ne $esperado ] && echo "No cumple la sucesión" && return
        prev=$n
    done
    echo "Cumple la sucesión"
}

contar() {
    read -p "Introduce un directorio existente: " dir
    ls "$dir" >/dev/null 2>&1 || { echo "Ese directorio no existe"; return; }
    ls "$dir" | wc -l > recuento
    echo "Guardado en 'recuento'"
}

potencia() {
    read -p "Introduce exponente (entero > 0): " exp
    for n in "$@"; do
        echo "$n^$exp = $(( n ** exp ))"
    done
}

while true; do
    echo "Menú [S] Sucesión"
    echo "[C] Contar"
    echo "[P] Potencia"
    echo "[F] FIN"
    read -p "Opción: " op
    case "$op" in
        S|s) sucesion "$@" ;;
        C|c) contar ;;
        P|p) potencia "$@" ;;
        F|f) echo "Fin del programa"; exit ;;
        *) echo "Opción no válida" ;;
    esac
done
```

---

### **ejercicio3.sh**

```bash
#!/bin/bash
[ $# -lt 3 ] && echo "Error de sintaxis" && exit 1

predecir() {
    read -p "Número mínimo de repeticiones del nombre del script: " minimo
    count=0
    nombre=$(basename "$0")
    for arg in "$@"; do
        test "$arg" = "$nombre" && count=$((count+1))
        [ $count -gt $minimo ] && break
    done
    ( [ $count -ge $minimo ] && echo "Has acertado o superado el mínimo" ) || \
    echo "Te has quedado corto"
}

inodo() {
    read -p "Introduce inodo: " ino
    read -p "Introduce posible directorio: " dir
    ls "$dir" >/dev/null 2>&1 || { echo "Ese directorio no existe"; return; }
    find "$dir" -maxdepth 1 -inum "$ino" | wc -l
}

triangular() {
    pos=1
    for x in "$@"; do
        t=$(( pos*(pos+1)/2 ))
        [ $t -ne $x ] && echo "No cumple la sucesión triangular" && return
        pos=$((pos+1))
    done
    echo "Cumple la sucesión triangular"
}

while true; do
    echo "Menú [P] Predecir"
    echo "[I] Inodo"
    echo "[T] Triangular"
    echo "[F] FIN"
    read -p "Opción: " op
    case "$op" in
        P|p) predecir "$@" ;;
        I|i) inodo ;;
        T|t) triangular "$@" ;;
        F|f) echo "Fin del programa"; exit ;;
        *) echo "Opción no válida" ;;
    esac
done
```

---

# 🌳 Estructura de directorios y archivos

```
Actividad6/
├─ ejercicio1.sh
├─ ejercicio2.sh
├─ ejercicio3.sh
├─ archivos/
│   ├─ archivo1.txt
│   ├─ archivo2.txt
│   └─ archivo3.txt
├─ scripts/
│   ├─ script1.sh
│   └─ script2.sh
└─ directorios/
    ├─ dir1/
    │   ├─ a.txt
    │   ├─ b.txt
    │   └─ c.txt
    └─ dir2/
        ├─ fichero1.txt
        └─ fichero2.txt
```

---

## Contenido de los archivos

### **archivos/archivo1.txt**

```
Hola, este es el archivo 1.
Contiene varias líneas de ejemplo.
Fin del archivo.
```

### **archivos/archivo2.txt**

```
Archivo 2.
Otra línea de prueba.
```

### **archivos/archivo3.txt**

```
Archivo 3 con contenido diferente.
Sólo dos líneas.
```

---

### **scripts/script1.sh**

```bash
#!/bin/bash
echo "Este es script1"
```

### **scripts/script2.sh**

```bash
#!/bin/bash
echo "Este es script2"
```

Dar permisos de ejecución:

```bash
chmod +x scripts/script1.sh scripts/script2.sh
```

---

### **directorios/dir1/**

Archivos para contar y listar:

* **a.txt**

```
Uno
Dos
Tres
```

* **b.txt**

```
Prueba
De
Archivos
```

* **c.txt**

```
Archivo final
```

---

### **directorios/dir2/**
Archivos para probar inodos:
* **fichero1.txt**
```
Contenido para inodo
```
* **fichero2.txt**
```
Otro contenido
```
Ver inodos con:
```bash
ls -i directorios/dir2/
```
---
# 💡 Consejos
1. Para la opción **I → Inodo** en `ejercicio3.sh`:
```bash
ls -i directorios/dir2/fichero1.txt
# Usar el número de inodo mostrado
```
2. Ejecutar todos los scripts desde el directorio `Actividad6/`.
---
