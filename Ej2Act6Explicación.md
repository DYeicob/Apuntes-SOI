### **1. Encabezado y comprobación de argumentos**

```bash
#!/bin/bash
[ $# -lt 3 ] && echo "Error de sintaxis" && exit 1
```

* `#!/bin/bash` indica que se ejecutará con Bash.
* `[ $# -lt 3 ]` comprueba si el número de argumentos (`$#`) es menor que 3.
* Si hay menos de 3 argumentos, muestra "Error de sintaxis" y termina el script (`exit 1`).

**Propósito:** El script requiere al menos 3 argumentos para funcionar.

---

### **2. Función `sucesion`**

```bash
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
```

* `base=$1`: primer argumento → diferencia de la sucesión.
* `prev=$2`: segundo argumento → valor inicial para comparar.
* `shift 2`: elimina los dos primeros argumentos, dejando solo el resto.
* Bucle `for n in "$@"`: recorre los siguientes argumentos.
* Calcula `esperado = prev + base`.
* Si algún número no coincide con `esperado`, imprime "No cumple la sucesión" y termina.
* Si todos coinciden, imprime "Cumple la sucesión".

**Propósito:** Verificar si los números forman una sucesión aritmética con diferencia `base`.

---

### **3. Función `contar`**

```bash
contar() {
    read -p "Introduce un directorio existente: " dir
    ls "$dir" >/dev/null 2>&1 || { echo "Ese directorio no existe"; return; }
    ls "$dir" | wc -l > recuento
    echo "Guardado en 'recuento'"
}
```

* Pide al usuario un directorio existente.
* Verifica que exista (`ls "$dir" >/dev/null 2>&1`).
* Lista los archivos con `ls "$dir"` y cuenta el número de líneas con `wc -l`.
* Guarda el resultado en un archivo llamado `recuento`.
* Confirma que se guardó.

**Propósito:** Contar el número de elementos en un directorio y guardar el resultado en un fichero.

---

### **4. Función `potencia`**

```bash
potencia() {
    read -p "Introduce exponente (entero > 0): " exp
    for n in "$@"; do
        echo "$n^$exp = $(( n ** exp ))"
    done
}
```

* Pide al usuario un **exponente entero positivo**.
* Recorre todos los argumentos.
* Calcula `n ** exp` (potencia) y muestra el resultado.

**Propósito:** Elevar cada argumento numérico a la potencia indicada.

---

### **5. Menú principal**

```bash
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

* Bucle infinito `while true` que muestra un menú.
* Según la opción ingresada:

  * `S|s`: llama a `sucesion` con todos los argumentos (`"$@"`).
  * `C|c`: llama a `contar` (sin argumentos).
  * `P|p`: llama a `potencia` con todos los argumentos.
  * `F|f`: finaliza el script.
  * Cualquier otra opción muestra "Opción no válida".

**Propósito:** Permitir al usuario elegir una acción hasta que decida finalizar.
