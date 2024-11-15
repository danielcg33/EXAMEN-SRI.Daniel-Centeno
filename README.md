# EXAMEN-SRI.Daniel-Centeno


1. Para abrir el proceso de consola en un contenedor que esta en ejecución empleamos el siguiente comando 

 `docker exec -it nombre_contenedor /bin/bash`
 
 
 los parámetros _it_ permiten la interacción del proceso en ejecución dentro del contenedor a través de una terminal 
 
 
2. Si lo que se quiere es determinar la capacidad de interacción en el momento de arranque del contenedor deberemos utilizar el mismo parámetro _it_ dentro de la estructura _docker run_ 


`docker run -it nombre_imagen /bin/bash`


El comando _run_ ejecuta la creación y la puesta en marcha del contenedor simultáneamente.


3.  El archivo docker-compose.yml para que dos contenedores se comuniquen solo entre ellos a través de una red interna tendrá que especificar la condición en el apartado _networks_.

Este campo pertenece a a cada uno de los servicios de contenedor constituyentes de la red que se quiere conformar.
Dicho espacio se concretará con la cadena de texto _internal network_ en cada uno de los apartados de red de cada uno de los contenedores .

 A mayores , fuera de la sección de servicios del documento , epecificamente en el apartado de red ,se deberá concretar la existencia de la red.
 
 
 
 services:
 
       
       nombre_primer_servicio (primer contenedor):
       
            image: imagen_contenedor1
            container_name: identificador del contendedor1 
            ports: #mapeo de los puertos entre el huésped y el propio contenedor1 
       
               - 54:53/tcp       

            networks:
                internal network
                
            ....
            
            
            
       nombre_segundo_sevicio (segundo contenedor):
       
             image: imagen_contenedor2
             container_name: indentificador del contenedor2      
             ports: #mapeo de los puertos ....
             
             ...
             
             
             networks:
                 internal network
                 
                 
             ...    
                  
              
                    
networks:
        
       internal network:
              driver: bridge
              
              




De esta manera en el último apartado se especifica el nombre de la red interna junto con la configuración de red (en este caso modo _puente_)


Las identaciones y espacios forman parte de la sintaxis del propio documento yml. con lo que hay que tener precaución con ellas.(Los puntos suspensivos en el documento no pertenecen al propio documento)


4. Para que los contenedores del archivo de configuración docker-compose anterior estén fijados a una IP deberemos concretar estas en el campo de red perteneciente a cada uno de los  servicios.

A mayores, en la sección _network_ se podrá especificar elementos como la IP  de red, el rango de IP así como la puerta de salida.





services:
 
       
       nombre_primer_servicio (primer contenedor):
       
            image: imagen_contenedor1
            container_name: identificador del contendedor1 
            ports: #mapeo de los puertos entre el huésped y el propio contenedor1 
       
               - 54:53/tcp       

            networks:
            
                bind9_subnet:
                    ipv4_address: IP A FIJAR PARA EL PRIMER CONTENEDOR 
                
                
            ....
            
            
            
       nombre_segundo_sevicio (segundo contenedor):
       
             image: imagen_contenedor2
             container_name: indentificador del contenedor2      
             ports: #mapeo de los puertos ....
             
             ...
             
             
             networks:
                 bind9_subnet:
                     ipv4_address: IP A FIJAR PARA EL SEGUNDO  CONTENEDOR 
                 
                 
             ...    
                  
              
                    
networks:
        
       bind9_subnet:
              driver: bridge
              ipam:
                 config:
                     - subnet: IP DE RED CON MÁSCARA
                       ip_range: RANGO DE IP CON MÁSCARA
                       gateway: PUERTA DE SALIDA   
              
              

          


                
            
5. Para saber las ip asociadas a los contenedores pertenecientes a la red , podemos acceder a la información propia de la configuración de red .



        ` docker network inspect nombre_red `
        
               
              
 6. El apartado _ports_ en los archivos de configuración de docker-compose.yml permite llevar a cabo el mapeo entre los puertos pertenecientes al anfitrión y los pertenecientes al contenedor constituyente del servicio particular.
Este mapeo se realiza ya que por defecto la comunicación contenedor-host solamente se realiza en una dirección (desde el contenedor al anfitrión) , pudiendose así configurarse bidireccionalmente.

La sintaxis general dentro del propios campo _ports_ del servicio es :


          `puerto_anfitrión : puerto_contenedor 




7. El registro de recursos CNAME permite relacionar un _alias_ determinado con el nombre canónico propio de un nombre de domino característico. De esta forma se facilita el acceso al nombre de dominio .

Una restricción fundamental del registro CNAME es la de apuntar al registro de recursos de tipo A (el que vincula nombre de dominio con IP versión iPv4) del nombre de domino canónico.


8. Con el objeto de solventar el problema de permanencia asociado a la tecnología de contenedores,se genera el concepto de volúmenes.

Consiste en crear una prolongación de la capacidad de almacenamiento del propio contenedor en el anfitrión .De esta forma las contingencias asociadas al sistema de archivos del contenedor se vuelve independiente del ciclo de vida de éste.

Los volúmenes son uno de los posibles apartados que pueden estar presentes en cada uno de los servicios definidos en un archivo de configuracioń docker-compose.yml.

La sintaxis asociada a su uso es:

        `/ruta/en/el/anfitrión : /ruta/en/el/contenedor/
        
        
En términos simples se genera un mapeo entre directorios pertenecientes al contenedor y al anfitrión , soslayando de esta manera el problema de permanencia de información.        



9. Para generar una nueva zona actuamos sobre los propios archivos de configuración de BIND9 que tenemos en nuestro anfitrión.

En el documento _named.conf.local_ tendremos acceso a la definición de las nuevas zonas.
Habrá que añadir la nueva zona con las características requeridas .




zone "tendaelectrónica.int" {

      type master;
      file "/etc/bind/db.tendaelectrónica.int
      
};





El archivo db. al que se hace referencia al final de la estructura se refiere al db.local particular(regular) .
Se especifica el nombre de dominio y el rol a realizar (maestro, primario).


Posteriormente modificaremos el archivo de configuración de zona db.local (en el caso particular db.tendaelectrónica.int)
Aquí es en donde se encuentran la información de los registros de recursos de la zona en particular, pudiendo modificarlos en caso de necesidad o requerimiento.




    
BIND data file for tendaelectrónica.int

$TTL    89709
@        IN          SOA         ns.tendaelectrónica.int  root.tendalectrónica.com (
                                            ...           ; Serial                                            
                                            ...           ; Refresh 
                                            ...           ; Retry 
                                            ...           ; Expire
                                            ...           ; Negative Cache TTL
                                            
                                            

@        IN          NS          ns.tendaelectrónica.int
www      IN           A          172.16.0.1 
owncloud IN          CNAME       www                                                  
texto    IN          TXT         "1234ASDF" 








En el apartado del SOA establecemos el servidor primario de la zona a  la vez que se especifica el el correo electrónico del administrador.

Se ha añadido los registros correspondientes al requerimiento del examen.

EL registro _CNAME_  apunta al registro de tipo A  de _www_  al cual asigna el alias _owncloud_.

Se incorpora el texto pedido a través del registro _TXT_

Con el registro de tipo A  se asigna el nombnre de dominio _www.tendaelectrónica.int_ a la IP especificada 

El @ hace referencia a _tendaelectrónica.int_ asociada con registro NS al servidor primario _ns.tendalectrónica.int_





Despues de realizar la modificación de los anteriores archivos de configuración se puede lanzar el archivo docker-compose.yml con al estrucruta de red de contenedores específica.


 `docker compose up 



Para comprobar que funciona correctamente entramos dentro del contenedor  con el comando utilizado en el primer ejercicio y realizar un dig a la zona directamente (tendaelectrónica.int) teniendo que obtener una respuesta a partir de los archivos de configuración de los registros solicitados 







S






      





                 
