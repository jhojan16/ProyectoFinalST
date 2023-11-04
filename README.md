# ProyectoFinalST
## Vagrant file

	Vagrant File:
	Vagrant.configure("2") do |config|
	config.vm.define :haproxy do |haproxy|
	haproxy.vm.box = "jhojanespinosa/box"
	haproxy.vm.network :private_network, ip: "192.168.50.5"
	haproxy.vm.hostname = "haproxy"
	end
	config.vm.define :cliente do |cliente|
	cliente.vm.box = "jhojanespinosa/box"
	cliente.vm.network :private_network, ip: "192.168.50.6"
	cliente.vm.hostname = "cliente"
	end
	config.vm.define :maquina do |maquina|
	maquina.vm.box = "jhojanespinosa/box"
	maquina.vm.network :private_network, ip: "192.168.50.7"
	maquina.vm.hostname = "maquina"
	end
	end

## Configuración maquina haproxy

Instalar haproxy --> Sudo yum install haproxy

Archivo de configuración del haproxy --> Sudo vim /etc/haproxy/haproxy.cfg

Agregar las siguientes líneas en la parte de abajo del archivo haproxy.cfg:

listen stats # Define a listen section called "stats"

	bind :9000 # Listen on port 9000
	mode http
 	stats enable  # Enable stats page
	stats hide-version  # Hide HAProxy version
 	stats realm Haproxy\ Statistics  # Title text for popup window
	stats uri /haproxy_stats  # Stats URI
 	stats auth Jhojan:34264116jhojan  # Authentication credentials

Además, realizar la siguiente configuración en ese mismo archivo:

	main frontend which proxys to the backends
 	frontend main
    bind *:80 #Puerto que recibe las peticiones http
    acl url_static       path_beg       -i /static /images /javascript /stylesheets
    acl url_static       path_end       -i .jpg .gif .png .css .js

    default_backend             app

	static backend for serving up images, stylesheets and such
	backend static
    balance     roundrobin
    server      static 127.0.0.1:4331 check

	round robin balancing between the various backends
	backend app
    balance     roundrobin
    server  cliente 192.168.50.6:80 check #Cambiar la ip por la ip que está en uso
    server  maquina 192.168.50.7:80 check ##Cambiar la ip por la ip que está en uso

Posteriormente en la maquina haproxy instalar la integración de haproxy que ofrece datadog, como data dog usa una API key única por usuario, se debe crear una cuenta, siguiendo el paso a paso de datadog, escoger CentOS/Red Hat como agent a instalar, después aparecerá un comando como el siguiente con su respectiva API KEY.

	DD_API_KEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX DD_SITE="us5.datadoghq.com" DD_APM_INSTRUMENTATION_ENABLED=host bash -c "$(curl -L https://s3.amazonaws.com/dd-agent/scripts/install_script_agent7.sh)"

 ![image](https://github.com/jhojan16/ProyectoFinalST/assets/147114192/958e9e6c-f492-416e-8b78-aff408737a4f)

Para confirmar que los servicios estén funcionando, realizar los siguientes comandos:

Datadog

	sudo systemctl restart datadog-agent
 
Haproxy

	sudo service haproxy restart

## Configuración las maquinas "cliente" y "maquina":

Instalar el servicio de apache/http
	
	Sudo yum install httpd

Directorio de configuración

	cd /var/www/html

Configuración de la página que visualiza el usuario

	sudo vim main.html

En main.html se realiza la pagina web al gusto del desarrollador, de preferencia que cada maquina tenga un objeto diferenciador para que sea visible el cambio de página y el balaceo, por ejemplo:

      <!DOCTYPE html>
      <html>
      <head>
          <title>Cliente Balanceo</title>
          <style>
              body {
                  font-family: Arial, sans-serif;
                  text-align: center;
                  background-color: #f2f2f2;
                  margin: 0;
                  padding: 0;
              }
      
              h1 {
                  color: #333;
              }
      
              p {
                  font-style: italic;
                  color: #555;
              }
      
              .container {
                  display: flex;
                  justify-content: center;
                  align-items: center;
                  height: 100vh;
              }
      
              .content {
                  background-color: #fff;
                  border-radius: 8px;
                  padding: 20px;
                  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
                  max-width: 400px;
                  width: 100%;
              }
      
              .section {
                  margin-bottom: 20px;
                  padding: 10px;
                  border-radius: 5px;
              }
      
              .section:nth-child(odd) {
                  background-color: #e0e0e0;
              }
      
              .section:nth-child(even) {
                  background-color: #d3d3d3;
              }
          </style>
      </head>
      <body>
          <div class="container">
              <div class="content">
                  <h1>Cliente Balanceo</h1>
                  <div class="section">
                      <p>Nombres: Jhojan Espinosa</p>
                  </div>
                  <div class="section">
                      <p>Nombres: Brayan Maca</p>
                  </div>
              </div>
          </div>
      </body>
      </html>

Asegurarse de que el servicio httpd está activo

	sudo service httpd restart

## Comprobar funcionamiento Datadog

En el apartado de infraestructura debe aparecer el nombre de la maquina en la que se instaló el agent de haproxy (ver imagen).

![image](https://github.com/jhojan16/ProyectoFinalST/assets/147114192/16cf7ebd-e80d-4d24-9285-3cd9aedc0f41)

En el apartado de dashboard, aparecen varios por default o tambien hay la opción de crear uno nuevo.

![image](https://github.com/jhojan16/ProyectoFinalST/assets/147114192/6ac0fb54-0dad-440d-8f38-5b22c7efc190)

Las graficas de este dashboard nos enseñan el trafico de la red.

![image](https://github.com/jhojan16/ProyectoFinalST/assets/147114192/76c1f735-d64e-42bb-a1cc-a170f4bb3ab0)

## Comprobar funcionamiento haproxy

Ingresar atraves del buscador a la ip de la maquina "haproxy" (192.168.50.5)

![image](https://github.com/jhojan16/ProyectoFinalST/assets/147114192/760a6249-237a-4efb-a418-248ee30ff65a)

De esta manera debe cargar alguna de las dos paginas web que realizaste en pasos anteriores:

![image](https://github.com/jhojan16/ProyectoFinalST/assets/147114192/35cf4c06-41a9-4bd2-84d1-dcd1b51e4645)

![image](https://github.com/jhojan16/ProyectoFinalST/assets/147114192/253d54be-115f-497c-be11-a6e639373e9a)




