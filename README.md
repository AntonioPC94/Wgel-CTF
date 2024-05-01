# Máquina: Wgel CTF

**Tryhackme: Wgel CTF

# NMAP

Lanzamos el comando de reconocimiento y sacamos la siguiente información:

![WGCTF1](img/WGCTF1.png)

Como se observa en las imágenes anteriores, existen 3 servicios que nos pueden interesar:

- Puerto 22 (SSH)
- Puerto 80 (HTTP)

# Enumeración: Puerto 80

Accedemos a la página web que nos ofrece la máquina objetivo. Una vez dentro, veremos algo que no nos encaja del todo:

![WGCTF2](img/WGCTF2.png)

Parece ser que la página no está completa del todo, ya que faltaría un trozo de texto. Por lo tanto, vamos a acceder al código fuente de la página para ver si encontramos información adicional.

![WGCTF3](img/WGCTF3.png)

Como se observa en la imagen anterior, alguien le dejó un comentario a Jessie (Este posiblemente sea un usuario del sistema), al cual parece ser que se le olvidó actualizar la página web.

# Fuzzing con Ffuf

A continuación, realizaremos fuzzing con ffuf. Para ello, utilizaremos el siguiente comando:

![WGCTF4](img/WGCTF4.png)

Observando la salida del comando, habrá un directorio que nos llamará especialmente la atención:

![WGCTF5](img/WGCTF5.png)

Ahora lo que vamos a hacer, es acceder a ese directorio y nos vamos a descargar esa clave que hemos encontrado.

![WGCTF6](img/WGCTF6.png)

# Explotación: Puerto 22

A continuación, vamos a utilizar "ssh2jhon" para comprobar si existe alguna contraseña adherida a la clave que nos hemos encontrado.

![WGCTF7](img/WGCTF7.png)

Genial, como no hay ninguna contraseña adherida a la clave, vamos a utilizarla para acceder por SSH a la máquina objetivo:

![WGCTF8](img/WGCTF8.png)

# Flag usuario.txt

Para encontrar la flag del usuario, tendremos que acceder a la carpeta "Documents" y listar su contenido. Una vez lo hayamos hecho, veremos que la flag se encuentra en dicho directorio. Por último, realizaremos un "cat" y visualizaremos el contenido de la flag.

![WGCTF9](img/WGCTF9.png)

# Flag root.txt

Para obtener la flag del usuario root, tendremos que elevar nuestros privilegios en la máquina objetivo. Para ello, realizaremos primeramente un "sudo -l" para ver qué comandos puede ejecutar el usuario jessie como administrador.

![WGCTF10](img/WGCTF10.png)

Como podemos observar en la imagen anterior, jessie tiene permisos para ejecutar el comando "wget" como administrador.

Ahora usaremos "wget" para transferir el contenido de "/etc/passwd" a nuestra máquina local, luego crearemos un nuevo fichero "passwd" y lo transferiremos de nuevo a la máquina objetivo.

## Nos traemos el fichero "/etc/passwd"

Ponemos a escuchar a nuestra máquina local con "nc":

![WGCTF13](img/WGCTF13.png)

Nos traemos el "/etc/passwd" utilizando el método POST:

![WGCTF11](img/WGCTF11.png)

Miramos que nuestra Kali haya recibido el fichero "passwd":

![WGCTF12](img/WGCTF12.png)

Ahora lo que haremos, será copiar el contenido en un nuevo archivo y vamos a modificar la parte de la contraseña del usuario root.

Es decir, cambiaremos la "x" por una contraseña cualquiera. Una vez establezcamos la nueva contraseña, el "/etc/passwd" no buscará el archivo "/etc/shadow" para la contraseña del usuario root.

![WGCTF14](img/WGCTF14.png)

Ahora crearemos un "Simple HTTP Server" con Python3 para poder mandar el fichero modificado a la máquina objetivo.

![WGCTF15](img/WGCTF15.png)

Nos vamos a la máquina objetivo y nos descargamos el fichero.

![WGCTF16](img/WGCTF16.png)

Visualizamos el fichero "/etc/passwd" para ver si se ha realizado el cambio.

![WGCTF17](img/WGCTF17.png)

A continuación, vamos a tratar de acceder como usuario root, en principio no deberíamos de introducir ningún tipo de contraseña.

![WGCTF18](img/WGCTF18.png)

Como vemos en la imagen anterior, ya seríamos usuario root.

Ahora para encontrar la flag, nos iríamos a la ruta "/root" y listaríamos el contenido de la carpeta. Una vez listada, veremos que la flag está dentro de dicho directorio. Realizamos un "cat" y veremos la flag de root.
