# 🤝  Trust

> **Dificultad:** Muy Fácil | **OS:** Linux \
> **Servicio explotado:** SSH Fuerza bruta\
> _&#x48;echo a mano, sin Metasploit, con mucho sudor y amor al hacking._ 🔥

***

### 🐳 Despliegue inicial

El setup es súper simple. La máquina es un contenedor Docker. Si nunca has desplegado una, dejaré una guía corta en la sección de Dockerlabs (o puedes leer la docu oficial 🤖).

<figure><img src="../../.gitbook/assets/image (98).png" alt=""><figcaption></figcaption></figure>

Una vez arriba, nos escupe una dirección IP. Es hora de arrancar con la fase de reconocimiento.

***

### 🚩 Fase 1: Reconocimiento a ciegas

Tiramos de nuestra vieja y confiable herramienta: nmap.

#### 💻 Comando utilizado

```bash
nmap -sVC -Pn 172.18.0.2
```

<figure><img src="../../.gitbook/assets/image (99).png" alt=""><figcaption></figcaption></figure>

#### 🧩 Desglose del comando

| Flag | ¿Qué carajos hace?                                                                                            |
| ---- | ------------------------------------------------------------------------------------------------------------- |
| -sV  | Detecta las versiones de los servicios (para ver si hay algo desactualizado).                                 |
| -sC  | Lanza scripts básicos de reconocimiento de Nmap. (Junto al anterior forman el combo -sVC).                    |
| -Pn  | Evita la resolución DNS y el ping inicial. Le decimos a Nmap: "Bro, confía, el host está vivo, solo escanea". |

**Resultado:** Vemos dos puertos abiertos, el **22 (SSH)** y el **80 (HTTP)**.\
Lo primero que uno piensa es "me meto por SSH y listo", pero no tenemos ni usuario ni contraseña. Así que toca ir al puerto 80 a ver qué hay en la web.

***

### 🚩 Fase 2: Enumeración Web y el Evento Canónico de Gobuster 🔎

Entramos a la web y... pura información por defecto del servidor. Aburridísimo. 🥱\
Pero aquí entra la malicia: ¿Y si hay directorios o archivos ocultos? Toca hacer fuerza bruta (fuzzing) de rutas.

<figure><img src="../../.gitbook/assets/image (100).png" alt=""><figcaption></figcaption></figure>

#### 🪄 El truco de las extensiones (-x)

Aquí sufrí mi primer evento canónico. La primera vez que usé Gobuster, lo lancé sin decirle qué extensiones buscar. Resultado: **no encontró nada.**

Gobuster, por defecto, busca rutas limpias tipo host/secret. Si el archivo se llama secret.php, lo va a ignorar por completo. Para evitar que Gobuster se haga el ciego, hay que usar el parámetro -x.

#### 💻 Comando para Fuzzing

```bash
gobuster dir -u http://172.18.0.2 -w /usr/share/wordlists/dirb/common.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (101).png" alt=""><figcaption></figcaption></figure>

| Parte           | Función                                                                                                    |
| --------------- | ---------------------------------------------------------------------------------------------------------- |
| dir             | Modo de ataque: fuerza bruta de directorios y archivos.                                                    |
| -u              | La URL de la víctima.                                                                                      |
| -w              | El diccionario (wordlist) que vamos a usar.                                                                |
| -x php,html,txt | **La magia:** Le dice que por cada palabra del diccionario, pruebe también agregándole .php, .html y .txt. |

{% hint style="warning" %}
**Dato:** Pasamos de no ver nada a encontrar una joyita llamada **secret.php** con un status 200 (que significa "pase adelante, jefe").
{% endhint %}

***

### 🚩 Fase 3: El Foothold (Punto de acceso) 🦶

Entramos a http://172.18.0.2/secret.php y nos encontramos un mensaje muy XD:

> "Hola Mario, este sitio web no se puede hackear"

<figure><img src="../../.gitbook/assets/image (102).png" alt=""><figcaption></figcaption></figure>

La primera vez que vi esto pensé: "que hice mal sjdkjsdsk". \
Pero en el hacking hay que ser minucioso. El ego del dev fue su perdición. Nos acaba de regalar un posible nombre de usuario: **Mario**.

#### 💥 Rockyou entra en juego (Fuerza Bruta SSH)

Tenemos el puerto 22 abierto, tenemos un usuario (mario), pero nos falta la contraseña. Es el momento perfecto para sacar a la mal educada **Hydra**.

```bash
hydra -l mario -P /usr/share/wordlists/rockyou.txt ssh://172.18.0.2
```

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

| Flag                 | Función                                                                   |
| -------------------- | ------------------------------------------------------------------------- |
| -l mario             | Especifica el usuario en minúscula (login).                               |
| -P /ruta/rockyou.txt | Diccionario gigante de contraseñas. (La "P" en mayúscula indica archivo). |
| ssh://...            | El servicio y la IP a atacar.                                             |

En cuestión de segundos, Hydra hace su magia y nos escupe una contraseña válida. ¡Nos logueamos por SSH y boom! **Estamos dentro del servidor.** 🎉

***

### 🚩 Fase 4: Escalada de Privilegios (El jefe final) 👑

Estar dentro está cheto, pero nosotros queremos ser el administrador supremo (root). Esta fase se llama **Post-Explotación / Escalada de Privilegios**.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

¿Cómo pasamos de ser el compa Mario a ser el dueño del servidor?\
Lo primero de manual es revisar qué permisos especiales tiene nuestro usuario.

#### 🕵️‍♂️ Análisis de permisos

Ejecutamos:

```bash
sudo -l
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

(Este comando lista qué cosas podemos ejecutar como superusuario).

Y vemos una línea hermosa: nos dice que podemos ejecutar el binario **vim** (el editor de texto) con permisos de root sin que nos pida contraseña. 🚩🚩🚩 Red flag de seguridad gigante.

#### 💻 El Exploit (Vim Breakout)

Si podemos abrir vim como root, y dentro de vim podemos ejecutar comandos del sistema... la matemática es simple: ejecutamos una terminal (bash) desde adentro de vim y esa terminal nacerá con permisos de root. 🤯

```bash
sudo vim -c ':!/bin/bash'
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

| Parte         | Función                                                               |
| ------------- | --------------------------------------------------------------------- |
| sudo vim      | Abrimos el editor de texto como dios (root).                          |
| -c            | Le pasamos un comando para que lo ejecute apenas se abra.             |
| ':!/bin/bash' | La sintaxis de Vim para decir: "Ejecuta la consola Bash del sistema". |

Le damos al Enter y la magia ocurre. Nuestro prompt cambia. **Somos ROOT.** 😈🔥

***

### 🖤 Cierre

> Y así es como obtenemos el control total del sistema.\
> Sí, sé que fue mucho texto, pero creo genuinamente que una resolución así, explicada paso a paso y entendiendo por qué fallan las cosas, es la mejor forma de aprender. De nada sirve tirar comandos a lo NPC si no entendemos qué hacen por debajo. 🧠💡

```
Happy H4ck1ng
spoonki .. siempre hackeando nunca webiando
```
