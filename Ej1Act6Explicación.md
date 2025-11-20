### **1. Encabezado y comprobación de argumentos**

```bash
#!/bin/bash
[ $# -lt 2 ] && echo "Error de sintaxis" && exit 1
```

* `#!/bin/bash` indica que el script se ejecutará con Bash.
* `[ $# -lt 2 ]` comprueba si el número de argumentos (`$#`) es menor que 2.
* Si hay menos de 2 argumentos, muestra "Error de sintaxis" y termina el script (`exit 1`).

**Propósito:** El script necesita al menos 2 argumentos para funcionar.

---

### **2. Función `ordenados`**

```bash
ordenados() {
    prev=$1
    shift
    for n in "$@"; do
        [ $n -lt $prev ] && echo "No están ordenados" && return
        prev=$n
    done
    echo "Están ordenados ascendentemente"
}
```

* `prev=$1`: guarda el primer argumento.
* `shift`: desplaza los argumentos, eliminando el primero.
* Bucle `for n in "$@"`: recorre el resto de argumentos.
* Comprueba si cada número es menor que el anterior. Si es así, imprime "No están ordenados" y sale.
* Si termina el bucle sin encontrar desorden, imprime "Están ordenados ascendentemente".

**Propósito:** Verificar si los argumentos numéricos están en orden ascendente.

---

### **3. Función `directorio`**

```bash
directorio() {
    read -p "Introduce un directorio existente: " dir
    ls "$dir" >/dev/null 2>&1 || { echo "Ese directorio no existe"; return; }
    for arg in "$@"; do
        test -f "$arg" -a -x "$arg" && ln "$arg" "$dir" || \
            echo "No es un fichero o no puedo ejecutarlo"
    done
}
```

* Pide al usuario un directorio existente.
* `ls "$dir" >/dev/null 2>&1`: verifica si el directorio existe.
* Luego recorre los argumentos y:

  * Si es un **archivo ejecutable** (`-f -a -x`), crea un **enlace duro** dentro del directorio.
  * Si no, muestra un mensaje de error.

**Propósito:** Crear enlaces a ficheros ejecutables en un directorio dado.

---

### **4. Función `listado`**

```bash
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
```

* Inicializa contadores `dirs` y `files`.
* Recorre los argumentos:

  * Si es un directorio (`-d`), aumenta `dirs`.
  * Si es un fichero **no vacío** (`-f -a -s`), aumenta `files`.
  * Si no cumple ninguna, muestra mensaje de error.
* Imprime los contadores al final.

**Propósito:** Contar directorios y ficheros no vacíos entre los argumentos.

---

### **5. Función `resto`**

```bash
resto() {
    args=("$@")
    for ((i=0; i<$#-1; i++)); do
        a=${args[i]}
        b=${args[i+1]}
        [ $b -eq 0 ] && echo "No se puede dividir por 0" && return
        echo "Resto entre $a y $b = $((a % b))"
    done
}
```

* Convierte los argumentos en un array.
* Recorre los pares consecutivos de argumentos.
* Calcula el **resto de la división** (`%`) de cada par.
* Si el divisor es 0, muestra error.

**Propósito:** Mostrar el resto de la división entre cada par consecutivo de números.

---

### **6. Función `contar`**

```bash
contar() {
    read -p "Introduce el nombre de un nuevo fichero: " fichero
    test -e "$fichero" && echo "Ese fichero ya existe" && return
    wc "$@" > "$fichero"
    echo "Datos guardados en $fichero"
}
```

* Pide un nombre de fichero.
* Si ya existe, muestra mensaje y sale.
* Si no, ejecuta `wc "$@"` (cuenta líneas, palabras y caracteres de los argumentos) y guarda el resultado en el nuevo fichero.
* Confirma que los datos se guardaron.

**Propósito:** Guardar el recuento de líneas, palabras y caracteres de archivos en un fichero nuevo.

---

### **7. Menú principal**

```bash
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

* Bucle infinito `while true` que muestra un menú.
* Según la opción ingresada:

  * Llama a la función correspondiente con todos los argumentos (`"$@"`).
  * `F|f` termina el script.
  * Si se ingresa algo no válido, muestra "Opción no válida".

**Propósito:** Permitir al usuario elegir una acción hasta que decida finalizar.
