---
description: >-
  Segunda sección de la playlist para principiantes de picoCTF.   Introducción a
  la criptografía básica, encodings y conversiones de bases.
---

# 🔐 section 2 (cyberchef)

> En esta segunda tanda entramos al mundo de la **criptografía** y los métodos de encriptado más comunes. Nada del otro mundo todavía, pero es fundamental entender estas bases _(literal y figurativamente)_ antes de meterse con cosas más pesadas.

### 🧰 Herramienta clave: CyberChef

Todos los retos de esta sección se pueden resolver usando [**CyberChef**](https://gchq.github.io/CyberChef/) — una herramienta web gratuita que te permite codificar, decodificar, cifrar y descifrar datos de forma visual y súper intuitiva. Es como una cocina donde tú metes los ingredientes (datos) y eliges las recetas (operaciones) que quieres aplicar.

> 💡 Dicho esto, en cada reto también te voy a mostrar cómo resolverlo **desde la terminal**, porque un buen hacker no depende de una sola herramienta. 😉

***

## 🔄 Mod26

<details>

<summary><strong>Dificultad:</strong> 🟢 Facil</summary>

Arrancamos con algo clásico: nos dan un archivo con la flag cifrada en **ROT13** y tenemos que descifrarla.

<figure><img src="../../.gitbook/assets/image (29) (1).png" alt="" width="563"><figcaption></figcaption></figure>

> **¿Qué es ROT13?** Es un cifrado súper simple que rota cada letra **13 posiciones** en el abecedario. Si llegas al final (Z), vuelves al principio (A).
>
> Lo interesante es que el abecedario tiene 26 letras, así que aplicar ROT13 **dos veces** te devuelve al texto original. Es decir, el mismo proceso que cifra, también descifra. Bastante elegante para lo simple que es. ✨

### Resolución

Descargamos el archivo que nos proporciona el reto y lo inspeccionamos con `cat`:

```bash
cat values.txt
```

Nos devuelve algo que **parece** una flag, pero con letras sin sentido — eso es porque está en ROT13. Toca descifrarlo.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Lo metes en CyberChef y aplicas la receta ROT13.Usamos tr para hacer la rotación directamente:cat values.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'¿Qué hace este comando? tr (translate) reemplaza caracteres. Le estamos diciendo: "toma las letras A-Z y cámbialas por N-Z seguido de A-M", que es exactamente la rotación de 13 posiciones.

Y ahí la tenemos, flag capturada. 🎯

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



</details>

***

## 🌡️ Warmed Up

<details>

<summary><strong>Dificultad:</strong> 🟢 Facil</summary>

En este reto nos toca hacer una **conversión de bases**: pasar de **base 16 (hexadecimal)** a **base 10 (decimal)**.

El valor que nos dan es `0x3D`.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

> **Bases numéricas en 30 segundos:**

| Base    | Sistema     | Dígitos que usa | Ejemplo  |
| ------- | ----------- | --------------- | -------- |
| Base 2  | Binario     | `0, 1`          | `101010` |
| Base 10 | Decimal     | `0-9`           | `42`     |
| Base 16 | Hexadecimal | `0-9, A-F`      | `0x3D`   |

> Son diferentes formas de representar el **mismo número**. En CTFs te vas a topar con conversiones de bases constantemente.

### Resolución

CyberChef con la receta From Hex.Tengo dos comandos favoritos para esto:printf "%d\n" 0x3Decho "ibase=16; 3D" | bc    # sin el prefijo 0xAmbos te devuelven 61 — que es la respuesta y nuestra flag. ✅

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



</details>

***

## 🔢 2Warm

<details>

<summary><strong>Dificultad:</strong> 🟢 Facil</summary>

Misma mecánica que el anterior pero con bases diferentes: ahora toca pasar de **base 10 (decimal)** a **base 2 (binario)**.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

El número que nos dan es `42`. _(La respuesta a todo, según la Guía del Autoestopista Galáctico 🌌)_

### Resolución

CyberChef con To Binary.Dos formas de hacerlo:echo "obase=2; 42" | bcdc -e "2o 42p"Desglose rápido:Resultado: 101010 — flag obtenida. 🎯

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



</details>

***

## 📦 Bases

<details>

<summary><strong>Dificultad:</strong> 🟢 Facil</summary>

Para cerrar la sección, un clásico: nos dan un texto cifrado en **Base64** y debemos decodificarlo.

<figure><img src="../../.gitbook/assets/image (18) (1) (1).png" alt="" width="563"><figcaption></figcaption></figure>

> **¿Qué es Base64?** Es un método para convertir datos binarios (imágenes, archivos, cualquier cosa) en una cadena de texto legible usando solo letras, números y un par de símbolos (`+`, `/`, `=`).
>
> Se diseñó para **transportar datos por internet sin que se corrompan**. Básicamente "traduce" archivos complejos a un formato ASCII que cualquier sistema puede manejar sin errores.
>
> Lo vas a ver **en todos lados**: headers HTTP, tokens JWT, archivos adjuntos de email, cookies... Base64 está por todas partes. 🌍

### Resolución

CyberChef con la receta From Base64.echo "texto\_cifrado\_aqui" | base64 -dNos devuelve la flag en texto plano. Si ves un % al final, no te preocupes — es solo el delimitador de la terminal, todo lo anterior es la flag. ✅

<figure><img src="../../.gitbook/assets/image (19) (1) (1).png" alt=""><figcaption></figcaption></figure>



</details>

***

## ✅ Sección completada

Con esto cerramos la **segunda sección** de la playlist. Repasemos lo que aprendimos:

| Concepto          | Reto que lo enseña |
| ----------------- | ------------------ |
| ROT13             | Mod26              |
| Hex → Decimal     | Warmed Up          |
| Decimal → Binario | 2Warm              |
| Base64            | Bases              |

> 🔑 **Tip importante:** Estas conversiones y cifrados básicos aparecen en **todos** los CTFs. Tener CyberChef a mano es genial, pero saber hacerlo desde la terminal te va a dar velocidad y flexibilidad a la hora de resolver retos más complejos.

> 🔥 ¡A por la Sección 3! Esto se va poniendo cada vez más interesante.
