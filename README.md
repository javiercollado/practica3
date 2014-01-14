PRÁCTICA 3
==========

En esta práctica vamos a montar un entorno de pruebas para probar configuraciones de NGINX en lo que a Balanceo de carga se refiere. Como no sabemos que máquina necesitamos, tenemos que hacer pruebas con diferentes configuraciones.

Para esto vamos a usar máquinas virtuales con vmWare Fusion.

En este caso vamos a necesitar 3. Una máquina balanceadora (Sobre la cual realizaremos los cambios de "hardware" y las dos servidoras).
Para no malgastar recursos, vamos a usar Ubuntu Server 12.04 LTS sin interfaz gráfica.
Las máquinas servidoras tendrán un núcleo y 512MB de RAM. Y como software para servir las web apache2 + php5.
La maquina principal (balanceador) variará en la configuración y para realizar el balanceo de carga usaremos NGINX. El algoritmo de balanceo que vamos a usar en NGINX es Round-Robin.

Para comprobar las diferentes configuraciones de la máquina balanceadora usaremos apache benchmark desde la máquina anfitriona.

##Creación máquinas virtuales vmWare Fusion:

Pulsamos añadir:

![](https://github.com/javiercollado/practica3/blob/master/Imagenes/1%20Pulsamos%20a%C3%B1adir.png?raw=true)

Continuar sin disco:

![](https://github.com/javiercollado/practica3/blob/master/Imagenes/2%20Continuar%20sin%20el%20disco.png?raw=true)

Elegimos el disco: 

![](https://github.com/javiercollado/practica3/blob/master/Imagenes/3%20Seleccionamos%20disco.png?raw=true)

En este caso para simplificar usaremos instalación sencilla que nos evita la configuración inicial:

![](https://github.com/javiercollado/practica3/blob/master/Imagenes/4%20Instalacion%20sencilla.png?raw=true)

Personalizamos ajustes para poner la RAM y el numero de núcleos que queremos:

![](https://github.com/javiercollado/practica3/blob/master/Imagenes/5%20Personalizar%20Ajustes.png?raw=true)  
![](https://github.com/javiercollado/practica3/blob/master/Imagenes/6%20Procesador%20y%20memoria.png?raw=true)  
![](https://github.com/javiercollado/practica3/blob/master/Imagenes/7%20Seleccionar%20configuracion.png?raw=true)


¡Ya podemos iniciar!


##Configuración balanceador:
 
Procedemos a la instalación de NGINX:

	sudo apt-get install nginx

![](https://github.com/javiercollado/practica3/blob/master/Imagenes/Balanceador%201%20Install%20NGINX.png?raw=true)

Configuramos NGINX. Para esto vamos al directorio dónde esta la configuración (Realizamos una copia de la configuración por defecto, para poder volver a esta en caso de error). Y finalmente ponemos en el archivo default lo siguiente (información sobre el backend, es decir, las máquinas servidoras):

![](https://github.com/javiercollado/practica3/blob/master/Imagenes/Balanceador%202%20configurar%20nginx.png?raw=true)

![](https://github.com/javiercollado/practica3/blob/master/Imagenes/Balanceador%203%20configurar%20nginx.png?raw=true)

![](https://github.com/javiercollado/practica3/blob/master/Imagenes/Balanceador%204%20configurar%20nginx.png?raw=true)

##Configuración servidoras:

Estos pasos se realizan en las 2 máquinas servidoras.
Instalamos git (Para poder descargar las aplicaciones):

![](https://github.com/javiercollado/practica3/blob/master/Imagenes/Servidoras%201%20Install%20git.png?raw=true)

Instalamos apache y php:

![](https://github.com/javiercollado/practica3/blob/master/Imagenes/Servidoras%202%20install%20apache%20y%20php%20.png?raw=true)

Realizamos el clone del repositorio git y copiamos a /var/www :

![](https://github.com/javiercollado/practica3/blob/master/Imagenes/Servidoras%203%20git%20clone%20y%20cp.png?raw=true)

#Todo funcionando:

En esta captura vemos como todo funciona, pues accediendo desde dos ventanas del navegador a la misma IP obtenemos dos página diferentes. En el caso de la máquina servidora 1, tenemos la practica 1 de esta asignatura. Y en la servidora 2 la segunda practica.

![](https://github.com/javiercollado/practica3/blob/master/Imagenes/Prueba%20-%20Funciona%20balanceador.png?raw=true)

#Resultados:

Para ver los resultados ejecutamos el siguiente comando desde la máquina anfitriona cambiando la configuración de la máquina balanceadora.
	ab -n 10000 -c 200 http://192.168.174.166/index.php
Es decir, 10000 peticiones con una concurrencia de 200.

![](https://github.com/javiercollado/practica3/blob/master/Imagenes/Ejecutando%20ab.png?raw=true)  

Las configuraciones serán:
·1 core 256 RAM  
·2 core 256 RAM  
·1 core 512 RAM  
·2 core 512 RAM  
·1 core 1024 RAM  
·2 core 1024 RAM  

Los resultados son los siguientes:  

Tiempo de respuesta

![](https://github.com/javiercollado/practica3/blob/master/Imagenes/Tabla%20tRespuesta.png?raw=true)  
![](https://github.com/javiercollado/practica3/blob/master/Imagenes/Grafico%20tRespuesta.png?raw=true)  

Tiempo total

![](https://github.com/javiercollado/practica3/blob/master/Imagenes/Tabla%20tTotal.png?raw=true)  
![](https://github.com/javiercollado/practica3/blob/master/Imagenes/Grafico%20tTotal.png?raw=true)  

##Conclusiones:

Como podemos comprobar por las gráficas y las tablas, la configuración que mejor resultado ha dado ha sido 2 cores con 1024 de RAM. Pero la mejora significativa es por la RAM. Ya que un balanceador necesita al igual que un servidor web bastante memoria principal. 
Otro resultado a destacar, aunque no este reflejado en las gráficas es el numero de errores. Estos no están causados por el balanceador, si no por la poca cantidad de RAM y procesador de las máquinas servidoras (Pero teniendo en cuenta los recursos limitados de un ordenador personal. Y que estos son necesarios para el probar el balanceador. Podemos concluir que esos errores no son de importancia en esta práctica).


##EXTRA

En un inicio, toda esta configuración fue realizada sobre una máquina en Azure (Que es el balanceador), pero que también tenia dos contenedores con lxc para servir las diferentes páginas). 
Esto no era muy útil, pues los recursos estaban compartidos entre máquinas servidoras y el balanceador. 

Pero igualmente sirve para ver en funcionamiento la estructura que he montado con máquinas virtuales locales. (Funciona igual, aunque no tenga la misma estructura y use contenedores).

La dirección dónde esta alojada es la siguiente:
[practica3ivjcl.cloudapp.net](practica3ivjcl.cloudapp.net)


Hay que tener en cuenta que al realizar la petición a la misma IP (La del balanceador), el explorador se hace un lío, y mezcla al recargar los css de las dos páginas. Lo mismo sucede al enviar los formularios.
Esto tiene una explicación lógica, y es que las peticiones y envío de formularios se envían al balanceador. Así que si la primera vez que hemos entrado en la página, nos muestra la aplicación de cálculo de ecuaciones y nosotros rellenamos el formulario y enviamos. Al llegar de nuevo al balanceador nos mandará las respuestas del formulario a la otra aplicación (Ya que el algoritmo round-robin alterna entre las máquinas del backend).

Esto en un caso real no sucedería (Incluso con la misma configuración), ya que el objetivo del balanceo de carga es distribuir la carga entre servidores que sirven la misma web. 

Además en esta práctica el objetivo es ver como va de un aplicación a otra, y no que el funcionamiento de estas sea óptimo. Para ver el funcionamiento óptimo podemos acceder a ellas en heroku:
[Práctica 1: Resolución ecuaciones 2º grado](http://segundogradopractica1ivjcl.herokuapp.com/)
[Práctica 2: Calcula resistencia equivalente](http://resitenciasp2ivjcl.herokuapp.com/)