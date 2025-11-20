# 🟦 ACTIVIDAD 6 – Programación del Shell

Guía completa para crear, ejecutar y probar los 3 ejercicios

---

# 🔵 PASOS COMUNES PARA LOS 3 EJERCICIOS

### 1️⃣ Crear el fichero del script

En la terminal:

```
vi ejercicio1.sh
```

o

```
vi ejercicio2.sh
```

o

```
vi ejercicio3.sh
```

Entra en modo edición pulsando:

```
i
```

Pega el contenido del script.
Cuando acabes, para guardar y salir:

```
ESC
:wq
ENTER
```

---

### 2️⃣ Dar permisos de ejecución al script

```
chmod +x ejercicio1.sh
chmod +x ejercicio2.sh
chmod +x ejercicio3.sh
```

Solo se hace una vez.

---

### 3️⃣ Ejecutar el script

Recuerda:

* Ejercicio 1 → requiere **2 o más argumentos**
* Ejercicio 2 → requiere **3 o más argumentos**
* Ejercicio 3 → requiere **3 o más argumentos**

Ejemplos:

```
./ejercicio1.sh 3 7 10
./ejercicio2.sh 4 1 5 9
./ejercicio3.sh script.sh a script.sh
```

Cada uno abrirá su propio menú.

---

---

# 🟢 EJERCICIO 1 — Cómo probar cada opción

Ejemplo de ejecución:

```
./ejercicio1.sh 3 8 10
```

Verás:

```
Menú [O] Ordenados
[D] Directorio
[L] Listado
[R] Resto
[C] Contar
[F] FIN
Opción:
```

Tú escribes la letra correspondiente.

---

## ✔ O → Ordenados

Comprueba si los números están en orden ascendente.

Ejemplo:

```
./ejercicio1.sh 1 2 5 10
```

Resultado:

```
Están ordenados ascendentemente
```

---

## ✔ R → Resto

Calcula el resto entre los números consecutivos.

```
./ejercicio1.sh 10 3 5
```

Salida:

```
Resto entre 10 y 3 = 1
Resto entre 3 y 5 = 3
```

---

## ✔ D → Directorio

Pide un directorio existente.
Por cada argumento del script:

* si es ejecutable → crea un enlace en ese directorio
* si no → muestra mensaje de error

Ejemplo:

```
./ejercicio1.sh script1.sh script2.sh
```

---

## ✔ L → Listado

Cuenta:

* directorios
* ficheros no vacíos

Ejemplo:

```
./ejercicio1.sh carpeta archivo.txt
```

---

## ✔ C → Contar

Pide un **fichero nuevo**.

Si existe → da error
Si NO existe → guarda:

* número de líneas
* número de palabras
* número de bytes

de todos los argumentos.

---

## ✔ F → Fin

Termina y muestra:

```
Fin del programa
```

---

---

# 🟣 EJERCICIO 2 — Cómo probar cada opción

Ejemplo de ejecución:

```
./ejercicio2.sh 4 1 5 9 13
```

Menú:

```
Menú [S] Sucesión
[C] Contar
[P] Potencia
[F] FIN
Opción:
```

---

## ✔ S → Sucesión

Los argumentos deben cumplir:
cada término = término anterior + primer argumento.

Ejemplos que cumplen:

```
./ejercicio2.sh 4 1 5 9 13
./ejercicio2.sh 3 2 5 8 11
```

---

## ✔ C → Contar

Pide un directorio existente.
Cuenta los ficheros dentro y guarda el número en un fichero llamado:

```
recuento
```

---

## ✔ P → Potencia

Pide un número (exponente).
Eleva cada argumento del script a ese exponente.

Ejemplo:

```
./ejercicio2.sh 2 3 4
```

Si introduces exponente 3:

Salida:

```
2^3 = 8
3^3 = 27
4^3 = 64
```

---

## ✔ F → Fin

Mismo comportamiento que en el ejercicio 1.

---

---

# 🟠 EJERCICIO 3 — Cómo probar cada opción

Ejemplo de ejecución:

```
./ejercicio3.sh script.sh a script.sh b c
```

Menú:

```
Menú [P] Predecir
[I] Inodo
[T] Triangular
[F] FIN
Opción:
```

---

## ✔ P → Predecir

Cuenta cuántas veces aparece **el nombre del propio script** entre los argumentos.

Ejemplo:

```
./ejercicio3.sh ejercicio3.sh x ejercicio3.sh y
```

El programa te pedirá un número.
Luego comprobará si acertaste o no.

---

## ✔ I → Inodo

Pide:

1️⃣ un número de inodo
2️⃣ un directorio existente

Muestra cuántos ficheros dentro del directorio tienen ese inodo.

Para ver inodos puedes usar:

```
ls -i
```

---

## ✔ T → Triangular

Comprueba si los números siguen la sucesión:

1, 3, 6, 10, 15, 21 …

Ejemplo correcto:

```
./ejercicio3.sh 1 3 6 10 15
```

---

## ✔ F → Fin

Cierra el programa.

---

---

## Códigos de cada script:

# ✅ **EJERCICIO 1 — SCRIPT COMPLETO (`ejercicio1.sh`)**

```bash
#!/bin/bash

###############################################
# EJERCICIO 1 — Requiere 2 o más argumentos
###############################################

[ $# -lt 2 ] && echo "Error de sintaxis" && exit 1


###############################################
# FUNCIONES
###############################################

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


###############################################
# PROGRAMA PRINCIPAL (MENÚ)
###############################################

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

# ✅ **EJERCICIO 2 — SCRIPT COMPLETO (`ejercicio2.sh`)**

```bash
#!/bin/bash

###############################################
# EJERCICIO 2 — Requiere 3 o más argumentos
###############################################
[ $# -lt 3 ] && echo "Error de sintaxis" && exit 1


###############################################
# FUNCIONES
###############################################

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


###############################################
# MENÚ
###############################################

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

# ✅ **EJERCICIO 3 — SCRIPT COMPLETO (`ejercicio3.sh`)**

```bash
#!/bin/bash

###############################################
# EJERCICIO 3 — Requiere 3 o más argumentos
###############################################
[ $# -lt 3 ] && echo "Error de sintaxis" && exit 1


###############################################
# FUNCIONES
###############################################

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


###############################################
# MENÚ
###############################################

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
