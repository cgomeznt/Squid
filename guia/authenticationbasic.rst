Usando la autenticación básica con Squid
======================================


Si desea autenticar al usuario antes de que pueda usar su servidor proxy, puede hacerlo utilizando la función de autenticación básica disponible en el proxy de Squid. Aunque Squid admite muchos tipos de autenticación, la autenticación básica es muy fácil de configurar.

Hay seis (6) tipos de autenticación disponible en HTTP en estos momentos:

* Basic - been around since the very beginning
* NTLM - Microsoft's first attempt at single-sign-on for LAN environments
* Digest - w3c's attempt at having a secure authentication system
* Negotiate (aka SPNEGO) - Microsoft's second attempt at single-sign-on.
* OAuth - IETF attempt at single-sign-on
* OAuth 2.0 (aka Bearer) - IETF second attempt at single-sign-on
Squid-2.6 and later support Basic, NTLM (SMB LM, v1 and v2), Digest, and Negotiate (Kerberos and/or NTLM flavours).

Para mayor información oficial ver:  https://wiki.squid-cache.org/Features/Authentication

En primer lugar, deberá instalar httpd-tools, que viene con una herramienta htpasswd que utilizaremos para crear un archivo de contraseña cifrado. Ejecute el siguiente comando para instalar httpd-tools.::

	# yum -y install httpd-tools


Ahora cree un nuevo archivo y proporcione la propiedad al demonio de Squid para que pueda acceder a él.::

	# touch /etc/squid/passwd && chown squid. /etc/squid/passwd


Ahora agregar un nuevo usuario al archivo de contraseña con la herramienta htpasswd. Vamos a crear un usuario de ejemplo cgomez.::

	# htpasswd /etc/squid/passwd cgomez
	New password: 
	Re-type new password: 
	Adding password for user cgomez

Consultamos el archivo::

	# cat /etc/squid/passwd 
	cgomez:$apr1$GImKntKg$z754I/MTNEkOPB4m3T67r/


Por defecto, htpasswd usa el cifrado MD5 para la contraseña, por lo tanto, su contraseña se almacenará en el hash MD5.

Ya que tenemos nuestro archivo de contraseña listo, ahora puede editar el archivo de configuración de squid y agregamos la siguiente configuración.::

	#
	#Authentication Basic
	#
	auth_param basic program /usr/lib64/squid/basic_ncsa_auth /etc/squid/passwd
	auth_param basic children 5
	auth_param basic realm Squid Basic Authentication
	auth_param basic credentialsttl 2 hours
	acl auth_users proxy_auth REQUIRED
	http_access allow auth_users

Reiniciamos el servicio de Squid.::

	# systemctl restart squid


Y ya el cliente debera colocar el usuario y la clave.



