Proxy transparante
==================


La principal ventaja de configurar un proxy transparente es que no tiene que configurar navegadores individuales para trabajar con proxies.

Editar el archivo de configuración y comentar el **http_port** y colocar este otro con la dirección IP por donde usted quiere que el Squid atienda las peticiones::

	# Squid normally listens to port 3128
	# http_port 3128
	# http_port 8080
	http_port 192.168.1.230:8080 transparent

Reiniciamos el servicio de Squid::

# systemctl restart squid

Consultamos el puerto::

	# netstat -nat | grep -i listen
	tcp        0      0 192.168.1.230:8080      0.0.0.0:*               LISTEN  


