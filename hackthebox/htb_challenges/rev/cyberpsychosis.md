# Cyberpsychosis

hola aqui sp00nki  de vuelta , este sera mi primer post de writeup aqui en el blog , y que mejor empezar con un resumen de como analize un madafakin rootkit .

honestamente me costo un monton , no soy tan bueno en reversing , pero me da un auraaa increible hjahah , es muy interesante como llegas a tocar las entrañas de un programa para entender el por que o que hace , mientras caes en la demencia .

pero como siempre digo , "Romper cosas para ver cómo funcionan es la forma más pura de curiosidad"

ahora bien , eleji este reto , por que me ayudo a comprender mucho , como es que logran identificar y averiguar su funcionalidad con estos tipos de malware.



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

hacked\_kill

hacker\_getdents64

give\_root

module\_hide

module\_show

