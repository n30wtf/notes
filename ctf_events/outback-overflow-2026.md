# Outback Overflow 2026

hey como estan de nuevo aqui spoonki , esta vez vengo con un post que me alegra haber sacado&#x20;

como veran , yo soy muy fan de los retos de hacking ctfs , de distintas categorias ,  recien hasta este punto punt

y bueno ya habiendo finalizado el evento , cabe recalcar que este evento estaba hecho para todas las personas, por lo cual habia retos desde nivel facil hsta insano ,  para ser mi primer ctf en vivo , resolvi la categoria web , donde considero que me enfoco mas es por eso que resolvi algunos retos de esta categoria .



el primer reto que resolvi fue uno muy senscillo&#x20;

<figure><img src="../.gitbook/assets/image (155).png" alt=""><figcaption></figcaption></figure>

nos da la bienbenida a su primer sitio web , nos dice que nos demos un tiempo para mirar a los ardededores para ver que descubrimos ,al principo pense que habria un comentario que dejo el desarrolloador ya que es muy comun que estos dejen sutiles datos en el codigo fuente de la pagina

<figure><img src="../.gitbook/assets/image (156).png" alt=""><figcaption></figcaption></figure>

pero no tiene nada hjskdskd , asi que  por ese lado no era , luego pense en que quisas tenga directorios ocultos , por lo que probe /panel , /admin, /login , /robots.txt pero tampoco iba por ese lado jaslkdjalk , me andaba complicando  , pero no esta de mas pensar en todas las posibilidades .

luego me dio por revisar sus certificado ssl , por que a primera vista se vio que no estaba firmado y bueno..&#x20;

<figure><img src="../.gitbook/assets/image (157).png" alt=""><figcaption></figcaption></figure>

digo , tampoco era tan complicado darse cuenta . pero fue lo ultimo que revise , ya que quise ver las cabezeras con "curl"

<figure><img src="../.gitbook/assets/image (158).png" alt=""><figcaption></figcaption></figure>

y como ves me salia eror de que no esta verificado el ceritificado .  por eso acudi a verlo desde el sitio webv asjdk xd



el siguiente reto fue mas facil de resolver que el anterior&#x20;

<figure><img src="../.gitbook/assets/image (159).png" alt=""><figcaption></figcaption></figure>

literal nos dice "no necesitas irte del sitio web" osea del sitio web del evento , solo pensar como "robot" , por lo que a bueno "robot?.. = robots.txt , es un archivo que usan lo rastreadores o bots de busqueda a que sitios no deben de visitar o mostrar directamente.&#x20;

<figure><img src="../.gitbook/assets/image (160).png" alt=""><figcaption></figcaption></figure>

vemos que hay un parametro "disallow" es como no permitido  y vemos que tiene 2 directorios /admin y /betteradmin.html  , este ultimo es la del reto , ya que lo otro no tenemos acceso de login con credenciales del administrador xd asi que ese descartado .

<figure><img src="../.gitbook/assets/image (161).png" alt=""><figcaption></figcaption></figure>

vistamos /betteradmin.html y nos da la flag del reto , fue muy facil de asociar .



ahora si se vienen lo bueno padrino.

empezamos con este reto&#x20;

<figure><img src="../.gitbook/assets/image (162).png" alt=""><figcaption></figcaption></figure>

bueno basicamente es un verificador de estado para saber si un sitio web esta online y no esta caido , osea seguiendo el lore , el personal de TI del evento lo creo para uso "interno" osea que solo ellos podian usarlo en su red local .... peeeero , no pensaron en la seguridad que tenia este checker y como alguien con suficiente curiosidad podria hacer.. , entonces ..

<figure><img src="../.gitbook/assets/image (163).png" alt=""><figcaption></figcaption></figure>

este es la herramienta en cuestion , si  colocamos un url para verificar su estado por ejemplo la url del evento.

<figure><img src="../.gitbook/assets/image (164).png" alt=""><figcaption></figcaption></figure>

vemos que nos muesta el codigo fuente de la pagina que visito , entonces .. para enter la vurlnerabilidad lo que hizo esta herramienta fue lo siguiente:

... básicamente, el checker recibe la URL que ingresamos y realiza una petición HTTP desde su propio servidor para comprobar si el sitio está disponible.

Hasta aquí todo parece normal, ¿verdad? El problema viene cuando descubrimos que podemos controlar la dirección a la que realiza esa petición.

Por ejemplo, en lugar de introducir la URL pública del evento, podemos probar con:

`http://localhost:8080/admin`

¿Qué sucede aquí?

El término `localhost` hace referencia al la propia maquina donde se ejecuta la aplicación. Es decir, no estamos intentando visitar un sitio externo, sino que estamos haciendo que el servidor del checker intente conectarse a uno de sus propios servicios internos.

Si existe una aplicación escuchando en el puerto `8080` y una ruta `/admin`, el checker podría acceder a ella y recibir su contenido.

<figure><img src="../.gitbook/assets/image (170).png" alt=""><figcaption></figcaption></figure>

Y aquí viene lo interesante: como la herramienta nos devuelve el código fuente de la página que visita, también podría mostrarnos el contenido de ese servicio interno.

**¿Dónde está la vulnerabilidad?**

En que el checker confía demasiado en la URL proporcionada por el usuario y permite que su servidor realice peticiones a destinos internos sin validar correctamente a dónde se está conectando.

Esto es lo que conocemos como **SSRF (Server-Side Request Forgery)**.

En pocas palabras: conseguimos que un servidor haga peticiones HTTP por nosotros y, dependiendo de sus permisos y controles de seguridad, podemos acceder a recursos que normalmente no estarían disponibles desde el exterior.



para este segundo reto , es lo mismo que el reto anterior , solo que ahora segun el lore del reto , el equipo de TI , agrego una lista de blockeos , donde "localhost" y "127.0.0.1" estan filtrados&#x20;

peeeero claramente hay muchas mas formas de escribir una direccion local ..

<figure><img src="../.gitbook/assets/image (165).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (166).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (167).png" alt=""><figcaption></figcaption></figure>

como vemos no nos deja ver el contenido del localhost por que tiene los filtros activados , es por aquello que existen formas de bypassear este tipo de vulnerabilidad que ya estuvimos biendo .

entonces elegi la opcion mas sencilla o facil de entender para mi que fue pasar el 127.0.0.1 localhost , a hexadecimal , el primer dijito 127 = 0x7f y lo demas de la direccion ip queda igual por que no varia en hexadecimal . por lo que con exito vemos la respuesta del servidor a una direccion ip interna , solo queda colocar la direccion de la flag y boom.

<figure><img src="../.gitbook/assets/image (176).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (169).png" alt=""><figcaption></figcaption></figure>



pasando con el ultimo reto que pude resolver de este evento , tenemos la misma vulnerabilidad conocida como SSRF , pero un poco mas robusta xd&#x20;

<figure><img src="../.gitbook/assets/image (171).png" alt=""><figcaption></figcaption></figure>

segun el lore de esta tercera version de reto , dice que llamaron a los pros de la seguridad para que ahora el sitio web resulva las direcciones url proporcionadas y vean si apuntan a una ip privada interna y asi poder evitar este tipo de intruciones&#x20;

<figure><img src="../.gitbook/assets/image (177).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (173).png" alt=""><figcaption></figcaption></figure>

vemos que si consultados una direccion hardcodeada como la del reto pasado para bypasear los filtros , nos arroja que la ip no esta permitida .

ahora bien , investigando que tipo de fugas puede tener este tipo de vulnerabilidad me encontre con la siguiente&#x20;

<figure><img src="../.gitbook/assets/image (174).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (175).png" alt=""><figcaption></figcaption></figure>

#### DNS Rebinding con `1u.ms`

```
make-8.8.8.8-rebind-127.0.0.1-rr.1u.ms:8080
```

Este hostname utiliza un servicio de DNS Rebinding que permite alternar entre dos direcciones IP:

* `make-...-rebind-...` → Define las dos IPs que utilizará el servicio.
* `8.8.8.8` → Primera IP pública, utilizada para superar el filtro anti-SSRF.
* `127.0.0.1` → Segunda IP, correspondiente a localhost.
* `rr` → _Round-Robin_: alterna las respuestas DNS entre ambas direcciones.
* `1u.ms` → Dominio que proporciona el servicio DNS de rebinding.
* `:8080` → Puerto del servicio interno al que se intenta acceder.

**¿Cómo consigue evadir el filtro?**

1. El checker consulta el dominio para validar su IP y recibe `8.8.8.8`, por lo que lo considera un destino público.
2. Al realizar la petición HTTP, una nueva resolución DNS devuelve `127.0.0.1`.
3. El servidor termina intentando conectarse a su propio localhost por el puerto `8080`.

**En resumen:** se aprovecha que el servidor valida una IP pública, pero posteriormente puede conectarse a una IP local utilizando el mismo hostname. Esto permite intentar evadir filtros anti-SSRF sin necesidad de configurar un servidor DNS propio.
