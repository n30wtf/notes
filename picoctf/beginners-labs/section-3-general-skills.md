---
description: >-
  Tercera sección de la playlist para principiantes de picoCTF.   Seguimos
  profundizando en General Skills y tocando algunos comandos básicos de Linux.
---

# 🛠️ section 3 (general skills)

> En esta sección seguiremos exprimiendo nuestras **General Skills**, ya que tocaremos y aprenderemos algunos comandos básicos (y muy útiles) de Linux. ¡Empecemos con el primer reto! 🚀

***

## 🚩 Wave a Flag

<details>

<summary><strong>Dificultad:</strong> 🟢 Facil</summary>

Como primera instancia, descargaremos el archivo que nos da el reto. Por lo que nos dice, parece ser un archivo binario ejecutable.

<figure><img src="../../.gitbook/assets/image (16) (1).png" alt=""><figcaption></figcaption></figure>

### Resolución

Primero, usamos el comando `file` para verificar con qué tipo de archivo estamos trabajando y corroborar que efectivamente sea un ejecutable:

```bash
file warmup
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Como vemos, efectivamente es un archivo ejecutable, por lo que procederemos a darle permisos de ejecución (`chmod +x`) y a ejecutarlo:

```bash
chmod +x warmup
./warmup
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

¡Oh! ¿Qué pasó? Lo que nos dice básicamente es que le coloquemos un _flag_ (parámetro) `-h` para ver qué puede hacer. Y _voilà_, una vez colocado el flag vemos que nos dice que "no sabe mucho" pero nos regala la flag de la plataforma xD.

```bash
./warmup -h
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



</details>

***

## ⌨️ Tab, Tab, Attack

<details>

<summary><strong>Dificultad:</strong> 🟢 Facil</summary>

Siguiendo con este reto, toca pensar un poco en cómo llegar a la flag por la cantidad de información que tenemos en pantalla.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Resolución

Descargamos el `.zip` que nos da el reto y lo descomprimimos con el comando `unzip`:

```bash
unzip Addadshashanammu.zip
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Esto nos dejará una serie de carpetas. Lo podemos visualizar mejor con el comando `tree` para ver las subcarpetas y archivos. Según la salida de `tree`, hay **7 directorios y 2 archivos**. Por lo tanto, hay 2 formas de obtener la flag:

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* **Vía código fuente:** Haciéndole un `cat` al archivo con extensión `.c`. Esto nos quiere decir que es un programa escrito en el lenguaje de programación C. Si vemos dentro del código, veremos la flag del reto quemada directamente ahí.
* **Vía ejecución:** El hecho de que haya un `.c` nos intuye que el otro archivo es un programa compilado (ejecutable). Por ende, simplemente lo ejecutamos ¡y como vemos nos devuelve la flag de igual forma! 💡

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



</details>

***

## 🕵️ Insp3ct0r

<details>

<summary><strong>Dificultad:</strong> 🟢 Facil</summary>

En este reto nos desviamos un poquito más hacia el análisis de código web, pero no es tan complejo. Empezamos desplegando el reto.

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Resolución

Una vez desplegado el reto, nos dará una URL. Al ingresar, nos muestra a primera vista una web simple, pero el reto nos dice "inspeccióname", así que abrimos las **Opciones de Desarrollador** con el atajo de teclado: `Ctrl` + `Shift` + `I` (o `F12`).

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Al abrirse las herramientas, podremos inspeccionar un poco el código fuente de la página web (`index.html`) y darnos cuenta de que hay un comentario oculto: el comentario nos dice que HTML es genial XD, y además, ¡nos da **1 de 3 partes** de la flag!

<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
**Tip:** Si nos vamos al apartado del _Source_ (Código fuente) o a la pestaña de _Network_, veremos otros dos archivos con extensiones `.js` (JavaScript) y `.css` (Hojas de estilo), que vendrían siendo los complementos de construcción de este sitio web.
{% endhint %}

Luego, al analizar estos archivos (el CSS y el JS), vemos que también tienen partes de la flag ocultas en forma de comentarios. Entonces, uniendo todas las partes, armamos la flag completa, dándonos como resultado la resolución de este reto.

<div><figure><img src="../../.gitbook/assets/image (13) (1) (1).png" alt=""><figcaption></figcaption></figure> <figure><img src="../../.gitbook/assets/image (14) (1) (1).png" alt=""><figcaption></figcaption></figure></div>

<figure><img src="../../.gitbook/assets/image (15) (1) (1).png" alt=""><figcaption></figcaption></figure>

> Este reto me gustó mucho ya que es algo que podríamos hacer con páginas reales y quizás detectar comentarios o información sensible que no debería estar a la vista de todo el mundo. :O

</details>

***

## 🧵 Strings it

<details>

<summary><strong>Dificultad:</strong> 🟢 Facil</summary>

En este reto nos dicen que veamos si podemos encontrar la flag dentro de este archivo **sin ejecutarlo**.

<figure><img src="../../.gitbook/assets/image (16) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Resolución

Bien, una vez descargado, le hacemos un `file` al archivo para ver de qué se trata. Como vemos, es un archivo binario ejecutable.

<figure><img src="../../.gitbook/assets/image (17) (1).png" alt=""><figcaption></figcaption></figure>

Si te creías listo y ejecutabas el programa como yo lo hice xD, te saldrá un mensaje diciéndote _"quizás puedas probar con 'strings'"_, lo cual nos invita a buscar en la página del manual con `man strings`. Lo sé, quizás esto sea nuevo, pero quédate con que **`strings` es un comando para mostrarte caracteres legibles dentro de un archivo**.

<figure><img src="../../.gitbook/assets/image (18) (1).png" alt=""><figcaption></figcaption></figure>

Lo que hice fue usar el comando `strings` sobre el archivo y ver cuántas líneas de texto legible mostraba por pantalla... ¡son un montón! Pasarnos buscando la flag entre más de 19,000 líneas nos demoraría muchísimo. Es aquí donde entra el comando mágico: **`grep`**.

<figure><img src="../../.gitbook/assets/image (19) (1).png" alt=""><figcaption></figcaption></figure>

Con `grep` podemos filtrar la salida por palabras clave. En este caso, buscaremos "pico" xD, ya que las flags en sí empiezan por eso, pues fue lo que se me ocurrió:

```bash
strings strings | grep "pico"
```

Como vemos, nos filtra mágicamente por esa palabra y ¡boom!, apareció la flag xD y ya está.

</details>

***

## 🔍 First Grep

<details>

<summary><strong>Dificultad:</strong> 🟢 Facil</summary>

En este reto nos dice de nuevo si podemos encontrar la flag dentro del archivo. Nos avisa que podría ser tedioso hacerlo manualmente, pero siempre hay mejores maneras de hacerlo.

<figure><img src="../../.gitbook/assets/image (20) (1).png" alt=""><figcaption></figcaption></figure>

### Resolución

Al hacerle un `strings` al archivo en cuestión, tiene muchos caracteres sin sentido. Buscar la flag manualmente sería un horror dado el volumen de datos, por eso recurrimos de nuevo a `grep` — pero esta vez explorando parámetros más específicos para hacer mejor el filtrado.

<figure><img src="../../.gitbook/assets/image (21) (1).png" alt=""><figcaption></figcaption></figure>

Si ejecutamos simplemente `grep "picoCTF" file` no obtenemos nada útil, el output sigue siendo ruido sin sentido. Esto ocurre porque `grep` trabaja línea por línea, y el contenido binario mezclado con texto rompe la detección. La solución es **combinar dos flags clave**:

* `-o` → Muestra únicamente la parte del texto que coincide con el patrón, descartando el resto de la línea.
* `-E` → Activa las _Extended Regular Expressions_ (Expresiones Regulares Extendidas), necesario para que símbolos como `{` y `}` funcionen correctamente en el patrón de búsqueda.

Y aquí entra **regex** (Expresiones Regulares) por primera vez. El patrón `.*` se compone de dos elementos:

* `.` → Cualquier carácter (letra, número, símbolo).
* `*` → El elemento anterior puede repetirse 0 o más veces.

Combinado todo, el comando final nos queda así:

```bash
strings file | grep -oE "picoCTF{.*}"
```

<figure><img src="../../.gitbook/assets/image (22) (1).png" alt=""><figcaption></figcaption></figure>

El comando `strings` extrae el texto legible del archivo binario, y luego `grep` captura exactamente la flag sin ruido. 🎯 Dándonos así la flag completa y totalmente limpia.

</details>

***

## 🤖 Where are the Robots

<details>

<summary><strong>Dificultad:</strong> 🟢 Facil</summary>

Aquí tocamos un poquito de **Web Exploitation** básico. El reto nos pregunta: _¿puedes encontrar los robots?_

<figure><img src="../../.gitbook/assets/image (23) (1).png" alt=""><figcaption></figcaption></figure>

### Resolución

Una vez desplegado el reto, nos da una URL. Al ingresar, nos muestra una página sencilla y nos dice de nuevo _"¿Dónde están los robots?"_ xD.

<figure><img src="../../.gitbook/assets/image (24) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
**¿Qué es `robots.txt`?**\
Es un documento de texto plano situado en la raíz de un sitio web que funciona como una "guía de instrucciones" para los buscadores (Google, Bing). Indica a los robots qué rutas o páginas **no deben rastrear** o indexar, ayudando a gestionar el presupuesto de rastreo y mantener zonas privadas ocultas del buscador público.
{% endhint %}

Por lo general, estos directorios ocultos no deberían contener información expuesta que comprometa la web, ya que el archivo `robots.txt` es de acceso público y puede ser un vector de información crítico si no se cuida.

<figure><img src="../../.gitbook/assets/image (25) (1).png" alt=""><figcaption></figcaption></figure>

Intentamos ingresar a ese archivo directamente desde la URL: `http://<host>:<port>/robots.txt`. Y ¡oh sorpresa!, nos encontramos con la primera pista. En el archivo, el administrador le dice a los robots que no indexen un archivo `.html` específico.

<figure><img src="../../.gitbook/assets/image (26) (1).png" alt=""><figcaption></figcaption></figure>

Entonces, ¿qué pasa si colocamos ese archivo oculto en la URL? Pues accedemos directamente al recurso protegido.

<figure><img src="../../.gitbook/assets/image (27) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (28) (1).png" alt=""><figcaption></figcaption></figure>

Y ¡boom!, encontramos la flag. Básicamente, revisando el archivo `robots.txt` (que le dice a los buscadores que no indexen sitios web que estén dentro de él), terminamos dando con el contenido oculto. ¡Lo cual es irónico porque hay desarrolladores que guardan contenido sensible ahí, haciendo que cualquier persona haciendo un reconocimiento básico (recon) lo encuentre en segundos!

</details>
