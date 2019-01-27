Bloqueo de sitios web
======================


Puede bloquear fácilmente uno o varios sitios web de los usuarios. Usar un archivo separado para la lista de sitios web a bloquear es una buena manera de administrar los sitios web bloqueados. Cree un nuevo archivo para almacenar la lista de sitios web a bloquear.::

	# vi /etc/squid/blocked_sites

Ahora agregue los sites que quiere bloquear::

	.cantv.com.ve
	.movilnet.com.ve


Ahora editar el archivo de configuración de Squid.::

	# vi /etc/squid/squid.conf
	#
	# Sites Block
	#
	acl blocked_sites dstdomain "/etc/squid/blocked_sites"
	http_access deny blocked_sites

Reiniciamos el servicio de Squid::

	# systemctl restart squid

Verificamos en un cliente y veremos como es bloqueado.

	.. figure:: ../images/02.png
