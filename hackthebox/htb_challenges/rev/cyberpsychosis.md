# Cyberpsychosis

hola aqui sp00nki  de vuelta , este sera mi primer post de writeup aqui en el blog , y que mejor empezar con un resumen de como analize un madafakin rootkit .

honestamente me costo un monton , no soy tan bueno en reversing , pero me da un auraaa increible hjahah , es muy interesante como llegas a tocar las entrañas de un programa para entender el por que o que hace , mientras caes en la demencia .

pero como siempre digo , "Romper cosas para ver cómo funcionan es la forma más pura de curiosidad"

ahora bien , eleji este reto , por que me ayudo a comprender mucho , por que es importante enteder cada pieza del rompecabezas para hallar la respuesta.



capitulo 1&#x20;

saque este reto de la plataforma Hack the box , y es de nivel facil jajaj yo me lo quise complicar hahah .&#x20;

pero valio totalmente la pena .



<figure><img src="../../../.gitbook/assets/image (139).png" alt=""><figcaption></figcaption></figure>

bien el reto nos da un archivo con un nombre raro y que esta en formato zip , lo que hice primeramente es descomprimirlo y me da 2 archivos licence.txt y diamorphine.ko

posteriormente identificamos que tipo de archivo es , por que para este punto no conocia la extencion .ko

<figure><img src="../../../.gitbook/assets/image (141).png" alt=""><figcaption></figcaption></figure>

investigando un poco me di con la sorpresa de que era un kernel object , y cai en cuenta de que no era tan facil como parecia hahah , peeeero  al final investigando entendi que son los archivos necesarios para el funcionamiento de un modulo de kernel , y que su ves un modulo de kernel es un complemento que necesita el kernel para la comunicacion entre la capa 3 y 0 del sistema de seguridad del sistema operativos o como se le dices anillo de seguridad  , es algo confuso cuando no entiendes como funciona el sistema a bajo nivel jajaja pero ey lo aprendes en unas horas , almenos eso fue lo que hice yo hahah .

bueno continuando lo siguiente que analize fue ver su metadata del archivo binario ELF , osea que tendremos que ver si tienen sus funciones sin ofuscar y son legibles .

<figure><img src="../../../.gitbook/assets/image (142).png" alt=""><figcaption></figcaption></figure>

lo mas relevante de todo esto por que se que es confuso para los novatos jaja , vemos funciones y variables con sus funciones con su nombre sin ser ofuscados , eso es bueno por que podemos entender mejor el codigo o saber por donde buscar el rootkit .

para verlo mejor podemos decompilarlo a lenguaje c para que sea mas entedible con la poderosimia ghidra.

<figure><img src="../../../.gitbook/assets/image (143).png" alt=""><figcaption></figcaption></figure>

ahi se nos lista las funciones con mas claridad . por los nombres nos podemos guiar de cuales funciones pueros remplazadas por el atacante , tenemos .

| Símbolo                                 | Tipo         | Qué nos dice                                                             |
| --------------------------------------- | ------------ | ------------------------------------------------------------------------ |
| `hacked_kill`                           | FUNC         | Reemplaza al syscall `kill` (envío de señales)                           |
| `hacked_getdents` / `hacked_getdents64` | FUNC         | Reemplazan a los syscalls que listan directorios (`ls`, `find`)          |
| `give_root`                             | FUNC         | Escalada de privilegios                                                  |
| `module_hide` / `module_show`           | FUNC         | Ocultar / mostrar el módulo en `lsmod`                                   |
| `find_task` / `is_invisible`            | FUNC         | Gestión de procesos invisibles                                           |
| `orig_kill`, `orig_getdents*`           | OBJECT       | Punteros a los syscalls originales (guardados antes de hookear)          |
| `module_hidden`                         | OBJECT       | Flag de estado: ¿está oculto el módulo?                                  |
| `prepare_creds`, `commit_creds`         | UND (import) | Funciones del kernel para cambiar credenciales → confirman escalada real |

una cosa que olvide mencionar es que los que secuestra el rootkit (hookea) son los syscalls , que son? pues  son las **llamadas al sistema** que usan internamente programas como `ls`, `find` o `readdir` para listar un directorio.&#x20;

entonces sabiendo esto nos adentramos en el hook de hacked\_getdents , y analizando ese syscall vemos que hay un valor inmediato en litle endian

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

vemos un valor en hexadecimal 0x69736f6863797370 , si los separamos de par de numeros  y eliminamos el prefijo 0x

tenemos la siguiente secuencia y si lo pasamos a ascii tendriamos el siguiente texto:

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

y si lo invertimos tenemos el siguiente texto :

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

y como vimos antes abajo de ese valor esta "s" , entonces añadiendole esa s a la cadena de texto tendremos psychosi jajaj , el daiblo estamos locos para allar eso jajaj ahora bien&#x20;

tenemos la palabra clave a buscar por que dentro de el syscall getdents esta ocultando el directorio pyschosis .

entonces como encontramos el archivo si lo que hace el rootkit es ocultarlo cuando intentamos buscar esa carpeta?

aqui es donde analize el siguiente syscall que es kill , al analizarlo encontramos que utiliza el segundo argumento de kill llamado sig para darnos acceso root.

#### Qué es `kill` y por qué nos importa

`kill(pid, sig)` es el syscall que usan los programas para **enviar una señal** a un proceso. Normalmente se usa para matar cosas (`kill -9`), pero en realidad sirve para enviar cualquier señal.

<figure><img src="../../../.gitbook/assets/image (145).png" alt=""><figcaption></figcaption></figure>

¿Por qué nos importa? Porque en el decompilado de ghidra vemos que el módulo define `hacked_kill` y guarda un `orig_kill`. Ese patrón (versión "hacked" + original guardado) es la firma clásica de un **hookeo de syscall**: el rootkit reemplaza el `kill` real por el suyo, y cada `kill` del sistema pasa primero por aquí. OsEa el rootkit se ha montado un **canal de comunicación secreto** usando señales que parecen normales.

#### La lógica, en modo resumen

c

```
señal = pt_regs->si;             // el segundo argumento de kill(pid, sig)

if (señal == 0x2e) { ... }       // 46: alternar visibilidad del módulo
else if (señal == 0x40) { ... }  // 64: darme root, por favor y gracias
else if (señal == 0x1f) { ... }  // 31: alternar invisibilidad de un proceso
else { orig_kill(...); }         // lo normal: pasa de largo
```

El rootkit **mira la señal antes que nadie**. Si es una de sus tres mágicas, hace lo suyo y **no** reenvía nada. Si no coincide, se hace el tontito y llama al `kill` original como si aquí no hubiera pasado nada.

#### La rama de root, sin dramas

c

```
else if (señal == 0x40) {
    creds = prepare_creds();       // "dame una copia editable de mis credenciales"
    if (creds != NULL) {
        creds->uid   = creds->gid   = 0;   // los 8 campos de identidad a cero
        creds->suid  = creds->sgid  = 0;
        creds->euid  = creds->egid  = 0;
        creds->fsuid = creds->fsgid = 0;
        commit_creds(creds);             // "vale, ahora sí, aplica esto"
    }
    return 0;                             // y se pirra sin llamar a orig_kill
}
```

**Qué pasa aquí, "ay we mi mente":**

* `prepare_creds()` te da una **copia modificable** de tus credenciales. El kernel no te deja tocar las originales directamente (son de solo lectura), así que primero te da un borrador.
* **Los cuatro `= 0`** ponen a cero todos los campos que definen "quién eres" para el sistema (usuario, grupo, y sus variantes de permisos). OsEa **te convierten en root**. Es lo mismo que hace el kernel cuando un proceso es root, pero sin las comprobaciones de permisos, porque esto corre en ring 0 y aquí nadie te dice que no.
* `commit_creds(creds)` aplica el borrador. A partir de aquí, el proceso **actual** es root.
* El `return 0` sale **sin** llamar a `orig_kill`. La señal 64 nunca llega al kernel real: es un código de comando disfrazado de señal.

#### ¿Y por qué `kill -64 $$` y no otro PID?

Porque en esta rama el código **ni siquiera lee el PID destino** (`pt_regs->di`). Solo lee la señal. Lo que modifica son las credenciales de `current`, y `current` es **quien hizo la llamada**, o sea, tú. El PID destino es decorativo.

Así que `kill -64 $$` te da root en **tu** shell porque el emisor eres tú. Podrías poner `kill -64 1` (apuntando a init) y el resultado sería el mismo: tu shell se vuelve root, init ni se entera. El rootkit le da root a **quien llama**, no a quien recibe .



vale entonces despues de realizar nuestro analizis del malware de este rootkit , pasemos a la accion ,  el reto nos da una ip por lo que asumimos que es una maquina infectada , donde tendremos que buscar el rootkit que acabamos de encontrar y partile su madre descargarndolo del kernel y poder encontrar el archivo oculto&#x20;

<figure><img src="../../../.gitbook/assets/image (146).png" alt=""><figcaption></figcaption></figure>

iniciamos el la instancia y nos conectaremos por el confiable netcat

<figure><img src="../../../.gitbook/assets/image (147).png" alt=""><figcaption></figcaption></figure>

una ves dentro del servidor verificamos que usuario somos , con id vemos que valor de indentidad tenemos en el sistema como vemos es 1000 por lo que es un usuario estandar , si intentamos buscar la carpeta que el rootkit esta ocultado no veremos nada xd

<figure><img src="../../../.gitbook/assets/image (148).png" alt=""><figcaption></figcaption></figure>

incluso si intentamos ver los modulos cargados del kernel no se ejecuta nada , entonces.. recordando vimos que el rootkit tenia la funcion de darnos acceso root usando un codigo magico que manda una señal root  que era el "64" entonces le mandamos esa señal a nuestra terminal con el pid \$$.

<figure><img src="../../../.gitbook/assets/image (149).png" alt=""><figcaption></figcaption></figure>

y booom somos root  :O

<figure><img src="../../../.gitbook/assets/image (150).png" alt=""><figcaption></figcaption></figure>

volviendo a intentar buscar la carpeta que el rootkit oculta , exactamente encontramos pura vrga jajaja , pero espera y si listamos los modulos cargados de nuevo ..

<figure><img src="../../../.gitbook/assets/image (151).png" alt=""><figcaption></figcaption></figure>

fack HAHAHA

bueno tampoco aparece nada , pero como no nos redimos , recordamos nuestro analizis forense del malware, recordamos que habia otro numero magico que era el... "46" para mostrar y ocultar el modulo hookiado o secuentrado o hackeado etc..&#x20;

<figure><img src="../../../.gitbook/assets/image (152).png" alt=""><figcaption></figcaption></figure>

y como vemos bumm , ahora si se nos listo el modulo modificado que contiene el rootkit  llamado diamorphine , entonces ya caido en la locura , descargamos el modulo del kernel para dejarlo con sus funciones originales y volvemos a buscar la carpeta.

<figure><img src="../../../.gitbook/assets/image (153).png" alt=""><figcaption></figcaption></figure>

una vez habiando descargado el modulo podemos ver donde se ocultaba el directorio y si porfin habiando encontrado la flag .

<figure><img src="../../../.gitbook/assets/image (154).png" alt=""><figcaption></figcaption></figure>

bueno que puedo decir , parece confunso por que  no tienes idea de donde buscar exactamente cuando ves codigo a bajo nivel , pero hey sirve para aprender la naturaleza de estos virus y malware que tienen impacato en la vida real .
