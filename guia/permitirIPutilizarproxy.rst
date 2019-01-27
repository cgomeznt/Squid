Permitir que la dirección IP use Internet a través de su servidor proxy
=========================================================================


Para permitir que un rango de direcciones IP use Internet a través de su servidor proxy. Puede agregar una nueva entrada de ACL. Squid soporta notaciones CIDR. Considere un ejemplo, si desea permitir un rango de direcciones IP de 110.220.330.1 a 110.220.330.255, puede hacer la siguiente entrada en el archivo de configuración de Squid en la lista de ACL.::

	acl localnet src 110.220.330.0/24

Esta es la lista que trae por defecto::

	# Example rule allowing access from your local networks.
	# Adapt to list your (internal) IP networks from where browsing
	# should be allowed
	acl localnet src 10.0.0.0/8     # RFC1918 possible internal network
	acl localnet src 172.16.0.0/12  # RFC1918 possible internal network
	acl localnet src 192.168.0.0/16 # RFC1918 possible internal network
	acl localnet src fc00::/7       # RFC 4193 local private network range
	acl localnet src fe80::/10      # RFC 4291 link-local (directly plugged) machines

Si el cliente no esta en las reglas de acl de IP permitidas se vera en el LOG, la dirección IP 192.168.1.3 fue rechazada::

	1548549443.918      0 192.168.1.3 TCP_DENIED/403 4054 CONNECT www.google.com:443 - HIER_NONE/- text/html
	1548549443.919      0 192.168.1.3 TCP_DENIED/403 4054 CONNECT www.google.com:443 - HIER_NONE/- text/html
	1548549445.364      1 192.168.1.3 TCP_DENIED/403 4054 CONNECT www.google.com:443 - HIER_NONE/- text/html


