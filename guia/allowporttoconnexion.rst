Permitir un puerto específico para conexiones HTTP
===================================================


Por defecto, Squid solo considera muy pocos puertos como puertos seguros y permite conexiones a través de ellos. Los puertos que están permitidos por defecto son::

	acl SSL_ports port 443
	acl Safe_ports port 80          # http
	acl Safe_ports port 21          # ftp
	acl Safe_ports port 443         # https
	acl Safe_ports port 70          # gopher
	acl Safe_ports port 210         # wais
	acl Safe_ports port 1025-65535  # unregistered ports
	acl Safe_ports port 280         # http-mgmt
	acl Safe_ports port 488         # gss-http
	acl Safe_ports port 591         # filemaker
	acl Safe_ports port 777         # multiling http
	acl CONNECT method CONNECT


No se podrá acceder a los puertos que no están listados arriba a través del proxy. Puede agregar un puerto a la lista de Safe_ports modificando la lista de ACL para puertos. Por ejemplo, si desea permitir el acceso al puerto 168 a través del servidor proxy, puede agregar la siguiente entrada de ACL para esto.::

	acl Safe_ports port 168

Reiniciamos para que los cambios tengan efecto::

	systemctl restart squid
