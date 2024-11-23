Empezamos con la maquina Stellarkwdt

![alt text](Screenshot_2024-11-23_13-03-19.png)

Arrancamos con un escaneo de puertos con nmap

![alt text](image.png)

![alt text](image-1.png)

Lo que podemos observar es que tenemos abiertos los puertos *22* y *80* corriendo *ssh* y *http* respectivamente 

Nos dirigimos al navegador para ver que contiene este *http*

![alt text](Screenshot_2024-11-23_13-14-46.png)

El nombre *Johann Gottfried Galle* (Info de Google) y *Neptuno* podrian servirnos para mas adelante 

![alt text](Screenshot_2024-11-23_13-19-20.png)

Seguimos con  fuzzing para descubrir directorios y encontramos */universe/*
 
![alt text](Screenshot_2024-11-23_13-19-48.png)

Nada a la vista por lo que investigamos el codigo fuente

![alt text](Screenshot_2024-11-23_13-20-29.png)

Encontramos esto en el codigo fuente, investigamos descubrimos que es un json que nos dice que el usuario es *neptuno*

Vamos a probar hacer un ataque de fuerza bruta al ssh, con un archivo pass.txt con los posibles passwords:

    johann
    gottfried
    galle
    Johann
    Gottfried
    Galle
    johanngottfriedgalle
    JohannGottFriedGalle


![alt text](Screenshot_2024-11-23_13-39-15.png)

Bingo! La passwd es *Gottfried* 

Acedemos por *ssh* con el usuario *neptuno* y la passwd *Gottfried*

![alt text](stellarjwt/Screenshot_2024-11-23_13-40-53.png)

Encontarmos el archivo *.carta_a_la_NASA.txt*, por lo que procedemos a ver que contiene 

![alt text](stellarjwt/Screenshot_2024-11-23_13-40-53.png)

![alt text](stellarjwt/Screenshot_2024-11-23_13-43-22.png)

Podemos ver que es una carta a la NASA con alguna informacion, podemos tomar *Eisenhower* que quiza nos pueda servir para mas adelante como contraseña quiza de algo 

    ls /home


![alt text](stellarjwt/Screenshot_2024-11-23_13-45-49.png)

Vaya! Aqui tenemos dos usuarios a los que podemos intentar acceder

Intentamos loguearnos a *nasa* con la passwd *Eisenhower* y lo conseguimos!


![alt text](stellarjwt/Screenshot_2024-11-23_13-45-49.png)

Seguimos investigando 

    ls -la

Para listar todos los archivos y no encontramos nada de reelevancia


![alt text](Screenshot_2024-11-23_13-40-53.png)

    sudo -l

Para listar los permisos 

![alt text](stellarjwt/Screenshot_2024-11-23_13-47-23.png)

Y nos muestra algo muy interesante, el usr *elite* puede ejecutar *socat* sin passw

Por lo que buscamos en *gtfobins* para ver como podemos aprovechar este binario y nos da lo siguiente

![alt text](image-2.png)

Conseguimos este comando para crear una shell como *elite*

![alt text](stellarjwt/Screenshot_2024-11-23_13-55-00.png)

Ahora somos *elite*!

    sudo -l

Para listar permisos 

![alt text](stellarjwt/Screenshot_2024-11-23_13-55-21.png)

Y nuevaente tenemos otro dato muy interesante, root puede ejecutar chown sin la passwd, lo que nos dice que podemos cambiar de propiedad archivos con root sin contraseñas

Cambiamos la propiedad del archivo */ect/passwd* 

![alt text](stellarjwt/Screenshot_2024-11-23_14-07-11.png)

*cat* para ver que contiene

![alt text](stellarjwt/Screenshot_2024-11-23_14-07-39.png)

Aqui en este momento tenemos acceso a el archivo */etc/passwd*, y al tener los privilegios tambien podemos modificarlo

Por lo que si quitamos la *x* al usuario *root*, lo dejariamos sin contraseña y tendriamos el control completo de la maquina... A por ello!

![alt text](stellarjwt/Screenshot_2024-11-23_14-08-12.png)

    su root

![alt text](stellarjwt/Screenshot_2024-11-23_14-09-08.png)

Y lo conseguimos!! Ya somos *root*