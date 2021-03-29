# multiples-cuentas-ssh-en-local
repositorio para probar en local el funcionamiento de multiples cuentas de git.

# pasos para tener multiples ssh
## generar claves
Pega el siguiente texto, que sustituye tu dirección de correo electrónico en GitHub.

```sh
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

Esto crea una nueva clave ssh usando el correo electrónico proporcionado como etiqueta.

> Generating public/private rsa key pair.
Cuando se te indique "Ingresar un archivo donde guardar la clave", presiona Intro. Al hacerlo aceptas la ubicación predeterminada del archivo.

En esta parte en lugar de darle enter, se procedera a cambiar el nombre de la clave para evitar sobreescribir las claves que se encuentran si es que ya se ha generado alguna
> Enter a file in which to save the key (/home/you/.ssh/id_rsa): /home/damian/.ssh/id_rsa_ejemplo


Donde se indica, escribe una contraseña segura. Para obtener más información, consulta "Trabajar con frases de contraseña de la clave SSH".

> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]

## Agregar tu clave SSH al ssh-agent
Antes de agregar una nueva clave SSH al ssh-agent para gestionar tus claves, debes haber comprobado las claves SSH existentes y generado una nueva clave SSH.
Inicia el agente SSH en segundo plano.

$ eval "$(ssh-agent -s)"
> Agent pid 59566
Agrega tu llave privada SSH al ssh-agent. Si creaste tu llave con un nombre distinto, o si estás agregando una llave existente que tiene un nombre distinto, reemplaza id_rsa en el comando con el nombre de tu archivo de llave privada.

$ ssh-add ~/.ssh/id_rsa

una vez que se ha generado las claves, se procede a copiar las claves para insertarlas en tu cuenta de github.

se puede ir directamente a la carpeta en este caso linux, en /home/nombredeusuario/.ssh/
dentro se abre con un editor el archivo id_rsa.pub y se copia su contenido.
o desde la terminal
```sh
xclip -sel clip < ~/.ssh/id_rsa.pub
```

## Agregar otra clave ssh
para este caso se siguen los mismos pasos anteriores practicamente.
solo teniendo cuidado en esta parte.

en lugar de darle enter, se procedera a cambiar el nombre de la clave para evitar sobreescribir las claves que se encuentran si es que ya se ha generado alguna
> Enter a file in which to save the key (/home/you/.ssh/id_rsa): /home/damian/.ssh/id_rsa_ejemplo2
teniendo cuidado de no tener el mismo nombre que una clave anterior.

## Creando archivo config dentro de .ssh
El problema ahora es cómo indicar en nuestro equipo que en un caso vamos a usar nuestra cuenta personal y, en otro caso la cuenta que tenemos para un proyecto o para una empresa.

Para esto, necesitamos crear el archivo config (si es que no existiera) dentro del directorio .ssh. En este archivo vamos a indicar qué clave usar para un caso y cuál para otro.

Una vez creado, vamos a agregar el siguiente contenido.

```sh
#repositorio 1
Host nombrecualquiera1
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa1

#repositorio 2
Host nombrecualquiera2
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa2
```
si ya tuvieras una cuenta principal y solo agregaste ota clave seria asi:

```sh
#Default GitHub
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa
```

para clonar:
Si trabajamos con nuestra cuenta personal y ya estaba generado esa clave, haríamos un clone de la siguiente forma:
```sh
git clone git@github.com:miusuario/repositorio.git
```

cuando estemos utilizando cualquier cuenta o de una empresa, vamos a cambiar el nombre del host.
```sh
git clone git@nombrecualquiera1:empresa/repositorio.git
```

## Agregando nombre y correo 
se agregan estos datos en la carpeta del repositorio clonado

El nombre el host en este caso coincide con lo que hemos indicado en el archivo config.

Como detalle final, deberíamos ajustar la configuración de los repositorios para que el usuario que se asocia al commit sea el correcto y no estemos mezclándolos.

Para esto, vamos a setear 3 variables locales del repositorio. Parados dentro del mismo ejecutamos:

```sh
git config user.name "Damián Culotta"
git config user.email "damian@ejemplo.com.ar"
git config github.user damian-ejemplo
```

De esta forma, en lugar de utilizarse los valores globales (que en mi caso sirven para mi cuenta personal), el repositorio usará las configuraciones específicas.

Si necesitáramos, por algún motivo, más cuentas, tendremos que repetir estos pasos.