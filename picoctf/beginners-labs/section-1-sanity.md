---
description: >-
  Primera sección de la playlist para principiantes de picoCTF.   Retos de
  General Skills para familiarizarte con el entorno y la terminal.
---

# 🧪 section 1 (sanity)

> En esta primera tanda nos encontramos con retos de **General Skills**, básicamente para comprobar que sabes moverte por una terminal Linux sin morir en el intento. Si ya tienes algo de experiencia con bash, esto va a ser pan comido. 🍞

## 🐱 Obedient Cat

<details>

<summary><strong>Dificultad:</strong> 🟢 Facil</summary>

Arrancamos con algo bien sencillo: el reto nos dice que la flag está **a simple vista** dentro de un archivo. Nada de magia aquí.

Lo primero que se nos viene a la cabeza es usar el comando `cat`.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
**¿Qué es `cat`?**\
Es la abreviatura de _catenate_ (concatenar). Es una herramienta básica de Linux/Unix que sirve para **leer, visualizar y combinar archivos de texto**. Básicamente, le dices "muéstrame lo que hay dentro de este archivo" y te lo escupe en pantalla.
{% endhint %}

### Resolución

Descargamos el archivo que nos da el reto a nuestra máquina local _(esto también se puede hacer desde la plataforma virtual, pero así estamos más cómodos)_ y ejecutamos:

```bash
cat flag
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Y listo, ahí está la flag esperándonos en texto plano. 🎯

{% hint style="info" %}
💡 Son comandos básicos de Linux, si no los conoces te recomiendo practicarlos porque los vas a usar **todo el tiempo**.
{% endhint %}



</details>

## 🔐 Súper SSH

<details>

<summary><strong>Dificultad:</strong> 🟢 Facil</summary>

Seguimos avanzando. Este reto nos pide conectarnos a un servidor remoto mediante **SSH** para encontrar la flag.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
**¿Qué es SSH?**\
**S**ecure **Sh**ell — es un protocolo que te permite conectarte de forma segura a otra máquina a través de la red y operar su terminal como si estuvieras sentado frente a ella.
{% endhint %}

### Resolución

Al iniciar la instancia del reto _(botón celeste)_, nos dan la siguiente info:

* **Host:** `titan.picoctf.net`
* **Puerto:** `61368`
* **Usuario:** `ctf-player`
* **Contraseña:** `1db87a14`

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nos conectamos con el siguiente comando:

```bash
ssh ctf-player@titan.picoctf.net -p 61368
```

**Desglose rápido:**

| Parte               | ¿Qué hace?                               |
| ------------------- | ---------------------------------------- |
| `ctf-player`        | El usuario con el que nos autenticamos   |
| `@`                 | Separa el usuario del host               |
| `titan.picoctf.net` | El host (servidor) al que nos conectamos |
| `-p 61368`          | Especifica el puerto de conexión         |

Al ejecutarlo nos pedirá la contraseña que ya nos dieron → la escribimos (no se va a ver en pantalla, es normal) y **boom**, nos muestra la flag directamente. ✅

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



</details>

## 🌐 What's a Net Cat?

<details>

<summary><strong>Dificultad:</strong> 🟢 Facil</summary>

Más de lo mismo por aquí. Iniciamos la instancia y vemos que nos pide usar **Netcat**.

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
**¿Qué es Netcat?**\
El comando `nc` (netcat) es conocido como la **"navaja suiza" de las redes**. En pocas palabras: te permite **enviar y recibir datos** entre computadoras a través de la red.

Piénsalo como un `cat`, pero en remoto. 😄
{% endhint %}

### Resolución

Con los datos que nos da la instancia, ejecutamos:

```bash
nc fickle-tempest.picoctf.net 60695
```

Nos conectamos al host en el puerto indicado y automáticamente nos devuelve la flag. Así de directo. 🎯

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



</details>

## ✅ Sección completada

Con esto cerramos la **primera sección** de la playlist para principiantes de picoCTF. Fueron retos de calentamiento para agarrar confianza con la terminal y herramientas básicas como `cat`, `ssh` y `nc`.

{% hint style="success" %}
🔥 Si llegaste hasta aquí sin problemas, vas por buen camino. ¡Vamos a por la siguiente sección!
{% endhint %}
