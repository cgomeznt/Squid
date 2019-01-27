Cambio de puerto de Squid
===========================



Para cambiar el puerto en el que escucha del Squid. Edite el archivo de configuración y busque el **http_port** comentelo y coloque una linea igual pero con el nuevo puerto::

	# vi /etc/squid/squid.conf
	# Squid normally listens to port 3128
	# http_port 3128
	http_port 8080

Reiniciamos el servicio de Squid::

	# systemctl restart squid

Debemos en el cliente cambiar el puerto.

