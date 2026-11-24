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

bueno basicamente es un verificador de estado para saber si un sitio web esta online y no esta caido , osea seguiendo el lore , el personal de TI del evento lo creo para uso "interno" osea que solo ellos podian usarlo en su red local .... peeeero , no pensaron en la seguridad que tenia este checker y como alguien con suficiente curiosidad podia hacer.. , entonces ..

<figure><img src="../.gitbook/assets/image (163).png" alt=""><figcaption></figcaption></figure>

este es la herramienta en cuestion , si  colocamos un url para verificar su estado por ejemplo la url del evento.

<figure><img src="../.gitbook/assets/image (164).png" alt=""><figcaption></figcaption></figure>

vemos que nos muesta el codigo fuente de la pagina que visito , entonces .. para enter la vurlnerabilidad lo que hizo esta herramienta fue lo siguiente:



<figure><img src="../.gitbook/assets/image (170).png" alt=""><figcaption></figcaption></figure>











<figure><img src="../.gitbook/assets/image (165).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (166).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (167).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (168).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (169).png" alt=""><figcaption></figcaption></figure>







<figure><img src="../.gitbook/assets/image (171).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (172).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (173).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (174).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (175).png" alt=""><figcaption></figcaption></figure>

#### DNS Rebinding

```
http://7f000001.08080808.rbndr.us:8080/admin
```

Esta URL utiliza **DNS Rebinding**, una técnica que permite que un mismo dominio resuelva a diferentes IPs, en este caso:

* `7f000001` → `127.0.0.1` (localhost)
* `08080808` → `8.8.8.8` (IP pública de Google DNS)

El dominio `rbndr.us` devuelve una de estas IPs aleatoriamente mediante DNS. Esto permite que un hostname aparentemente externo pueda resolver posteriormente a una dirección local.

* `:8080` → puerto del servicio web.
* `/admin` → ruta del panel administrativo.

**¿Por qué es interesante?** Puede utilizarse para intentar evadir restricciones de acceso a servicios internos o locales, aprovechando cómo el navegador maneja DNS y el mismo origen (Same-Origin Policy). Su éxito depende de las protecciones del navegador y del servicio objetivo.
