# Tproot

bien este es otro reto super sencillo y de hecho es identico a uno que ya resolvimos&#x20;

deplegamos el laboratorio.

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

luego pasamos a la fase reconocimiento , con nuestro comando de siempre

nmap -sVC -vvv -Pn 172.17.0.2

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

vemos 2 servicios expuestos el puerto 80 y 21 , revisamos el puerto 80 directamente desde la web

<figure><img src="../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

vemos que es una pagina defaul y por lo tanto quisas no valla por ahi el reto . pasamos a investigar el puerto 21 ftp , como vemos tiene la version vsftpd 2.3.4 por lo que si resolvimos un reto anterior que tenia la misma vulnerabilidad sabremos que esa version tiene un backdoor en su codigo lo que hace que si te conectas con "usuario:)" con la carita feliz pegada al estring se abria un puerto 6200 al que podremos conectarnos por nc al servidor

<figure><img src="../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

entonces ya estando dentro del servidor vemos que estamos con permisos root , pero este reto es ligeramente diferente hay un archivo dentro del directorio root que nos da una flag.

<figure><img src="../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

le damos nuestro toque y pondemos nuestra marca&#x20;

<figure><img src="../../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>

