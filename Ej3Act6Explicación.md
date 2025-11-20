### **1. Encabezado y comprobación de argumentos**

```bash
#!/bin/bash
[ $# -lt 3 ] && echo "Error de sintaxis" && exit 1
```

* `#!/bin/bash` indica que se ejecutará con Bash.
* `[ $# -lt 3 ]` comprueba si se han pasado menos de 3 argumentos.
* Si hay menos de 3, muestra un error y termina (`exit 1`).

**Propósito:** Garantizar que hay suficientes argumentos para ejecutar las funciones.

---

### **2. Función `predecir`**

```bash
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
```

* Pide un **número mínimo de repeticiones**.
* `nombre=$(basename "$0")` obtiene el nombre del script actual.
* Recorre los argumentos y cuenta cuántas veces coincide con el nombre del script.
* Si la cuenta supera el mínimo, rompe el bucle.
* Luego imprime:

  * `"Has acertado o superado el mínimo"` si el conteo ≥ mínimo.
  * `"Te has quedado corto"` si no.

**Propósito:** Contar cuántos argumentos coinciden con el nombre del script y comparar con un mínimo dado.

---

### **3. Función `inodo`**

```bash
inodo() {
    read -p "Introduce inodo: " ino
    read -p "Introduce posible directorio: " dir
    ls "$dir" >/dev/null 2>&1 || { echo "Ese directorio no existe"; return; }
    find "$dir" -maxdepth 1 -inum "$ino" | wc -l
}
```

* Pide un **número de inodo** y un **directorio**.
* Comprueba que el directorio existe.
* Usa `find` para buscar archivos en el directorio (`-maxdepth 1`) con el inodo dado (`-inum "$ino"`).
* Cuenta cuántos archivos coinciden (`wc -l`) y lo muestra.

**Propósito:** Ver cuántos archivos en un directorio tienen un inodo específico.

---

### **4. Función `triangular`**

```bash
triangular() {
    pos=1
    for x in "$@"; do
        t=$(( pos*(pos+1)/2 ))
        [ $t -ne $x ] && echo "No cumple la sucesión triangular" && return
        pos=$((pos+1))
    done
    echo "Cumple la sucesión triangular"
}
```

* `pos` inicia en 1.
* Recorre los argumentos.
* Calcula el **número triangular** en la posición `pos`: `t = pos*(pos+1)/2`.
* Si el argumento no coincide con `t`, imprime `"No cumple la sucesión triangular"` y termina.
* Si todos coinciden, imprime `"Cumple la sucesión triangular"`.

**Propósito:** Verificar si los argumentos forman una sucesión de números triangulares.

---

### **5. Menú principal**

```bash
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

* Bucle infinito que muestra un menú.
* Según la opción:

  * `P|p` → llama a `predecir` con todos los argumentos.
  * `I|i` → llama a `inodo` (sin argumentos, se pide todo por teclado).
  * `T|t` → llama a `triangular` con los argumentos.
  * `F|f` → termina el script.
  * Otra opción → mensaje de error.

**Propósito:** Permitir al usuario elegir qué función ejecutar hasta salir.
