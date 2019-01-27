Instalar Squid en CentOS 7
==========================================

.. figure:: ../images/01.png

Un servidor proxy es una computadora que se encuentra entre dos dispositivos de punto final y actúa como un dispositivo intermedio. Cuando la computadora cliente solicita un recurso del servidor, puede ser un archivo o una página web, la solicitud se envía primero al servidor proxy. El servidor proxy luego envía la solicitud al servidor de destino y obtiene el recurso enviado por el servidor. Una vez que el servidor proxy obtiene el recurso, envía el recurso a la máquina cliente. El uso de un servidor proxy es que puede almacenar en caché el recurso, por ejemplo, si se accede a un sitio web con frecuencia desde un servidor proxy, es probable que el servidor proxy tenga el contenido del sitio en su caché, ahora puede servir a la página web. directamente al usuario. Se puede usar un servidor proxy para facilitar la seguridad, los controles administrativos y los servicios de almacenamiento en caché. Los servidores proxy también pueden usarse para el anonimato, ya que cada vez que obtienen un recurso de un servidor, el servidor proxy utiliza su propia dirección IP en lugar de la dirección IP del cliente.

Squid Proxy es un proxy de caché de código abierto para la web. Es compatible con muchos protocolos como HTTP, HTTPS, FTP y más. Mejora el tiempo de respuesta y reduce el ancho de banda al almacenar en caché y reutilizar las páginas web y los archivos a los que se accede con más frecuencia. En este tutorial aprenderemos a instalar Squid Proxy en CentOS 7. También aprenderemos acerca de algunas configuraciones básicas que se pueden hacer en el servidor de almacenamiento en caché de Squid.

Requerimientos
+++++++++++++++++++

Squid usa mucha memoria por razones de rendimiento. Se necesita mucho, mucho más tiempo para leer algo del disco que leer directamente desde la memoria.

Una pequeña cantidad de metadatos para cada objeto almacenado en caché se guarda en la memoria. Esta es la estructura de datos de StoreEntry. Esto es de 56 bytes en arquitecturas de 32 bits y 88 bytes en arquitecturas de 64 bits. Además, hay una clave de caché de 16 bytes (suma de comprobación MD5) asociada con cada StoreEntry. Esto significa que hay 72 o 104 bytes de metadatos en la memoria para cada objeto en su caché. Por lo tanto, un caché con 1,000,000 de objetos requiere 72MB de memoria para metadatos solamente. En la práctica se requiere mucho más que eso.

**¿Cómo puedo saber cuánta memoria está usando mi proceso de Squid?**
Con top::

	last pid: 20128;  load averages:  0.06,  0.12,  0.11                   14:10:58
	46 processes:  1 running, 45 sleeping
	CPU states:     % user,     % nice,     % system,     % interrupt,     % idle
	Mem: 187M Active, 1884K Inact, 45M Wired, 268M Cache, 8351K Buf, 1296K Free
	Swap: 1024M Total, 256K Used, 1024M Free

	  PID USERNAME PRI NICE SIZE    RES STATE    TIME   WCPU    CPU COMMAND
	 9631 squid     2   0   138M   135M select  78:45  3.93%  3.93% squid


Tambien se puede consultar los procesos de Squid disponible en el Cache Manager.::

	Resource usage for squid:
	Maximum Resident Size: 137892 KB
	Memory usage for squid via mstats():
	Total space in arena:  140144 KB
	Total free:              8153 KB 6%

Ver este link oficial ayuda mucho más al tema de la memoria.

https://wiki.squid-cache.org/SquidFaq/SquidMemory

Instalando Squid
+++++++++++++++++

Instalamos también el repositorio **epel**::

	# yum install -y epel-release

Antes de instalar Squid siempre es bueno asegurarnos que tengamos al día nuestros paquetes.::

	# yum -y update
	# yum clean all

Instalamos Squid proxy::

	# yum install -y squid

Una vez instalado lo iniciamos y lo habilitamos en systemd.::

	# systemctl start squid

	# systemctl enable squid
	Created symlink from /etc/systemd/system/multi-user.target.wants/squid.service to /usr/lib/systemd/system/squid.service.

	# systemctl status squid
	● squid.service - Squid caching proxy
	   Loaded: loaded (/usr/lib/systemd/system/squid.service; disabled; vendor preset: disabled)
	   Active: inactive (dead)
	[root@itop01 ~]# systemctl enable squid
	Created symlink from /etc/systemd/system/multi-user.target.wants/squid.service to /usr/lib/systemd/system/squid.service.
	[root@itop01 ~]# systemctl start squid
	[root@itop01 ~]# systemctl status squid
	● squid.service - Squid caching proxy
	   Loaded: loaded (/usr/lib/systemd/system/squid.service; enabled; vendor preset: disabled)
	   Active: active (running) since sáb 2019-01-26 20:12:01 -04; 2s ago
	  Process: 1367 ExecStart=/usr/sbin/squid $SQUID_OPTS -f $SQUID_CONF (code=exited, status=0/SUCCESS)
	  Process: 1361 ExecStartPre=/usr/libexec/squid/cache_swap.sh (code=exited, status=0/SUCCESS)
	 Main PID: 1369 (squid)
	   CGroup: /system.slice/squid.service
		   ├─1369 /usr/sbin/squid -f /etc/squid/squid.conf
		   ├─1371 (squid-1) -f /etc/squid/squid.conf
		   └─1372 (logfile-daemon) /var/log/squid/access.log

	ene 26 20:12:01 itop01.dominio.local systemd[1]: Starting Squid caching proxy...
	ene 26 20:12:01 itop01.dominio.local systemd[1]: Started Squid caching proxy.
	ene 26 20:12:01 itop01.dominio.local squid[1369]: Squid Parent: will start 1 kids
	ene 26 20:12:01 itop01.dominio.local squid[1369]: Squid Parent: (squid-1) process 1371 started


Para la ayuda podemos hacer::

	# squid -h
	Usage: squid [-cdhvzCFNRVYX] [-n name] [-s | -l facility] [-f config-file] [-[au] port] [-k signal]
	       -a port   Specify HTTP port number (default: 3128).
	       -d level  Write debugging to stderr also.
	       -f file   Use given config-file instead of
		         /etc/squid/squid.conf
	       -h        Print help message.
	       -k reconfigure|rotate|shutdown|restart|interrupt|kill|debug|check|parse
		         Parse configuration file, then send signal to 
		         running copy (except -k parse) and exit.
	       -n name   Specify service name to use for service operations
		         default is: squid.
	       -s | -l facility
		         Enable logging to syslog.
	       -u port   Specify ICP port number (default: 3130), disable with 0.
	       -v        Print version.
	       -z        Create missing swap directories and then exit.
	       -C        Do not catch fatal signals.
	       -D        OBSOLETE. Scheduled for removal.
	       -F        Don't serve any requests until store is rebuilt.
	       -N        No daemon mode.
	       -R        Do not set REUSEADDR on port.
	       -S        Double-check swap during rebuild.
	       -X        Force full debugging.
	       -Y        Only return UDP_HIT or UDP_MISS_NOFETCH during fast reload.

Ver la versión de Squid::

	# squid -v
	Squid Cache: Version 3.5.20
	Service Name: squid
	configure options:  '--build=x86_64-redhat-linux-gnu' '--host=x86_64-redhat-linux-gnu' '--program-prefix=' '--prefix=/usr' '--exec-prefix=/usr' '--bindir=/usr/bin' '--sbindir=/usr/sbin' '--sysconfdir=/etc' '--datadir=/usr/share' '--includedir=/usr/include' '--libdir=/usr/lib64' '--libexecdir=/usr/libexec' '--sharedstatedir=/var/lib' '--mandir=/usr/share/man' '--infodir=/usr/share/info' '--disable-strict-error-checking' '--exec_prefix=/usr' '--libexecdir=/usr/lib64/squid' '--localstatedir=/var' '--datadir=/usr/share/squid' '--sysconfdir=/etc/squid' '--with-logdir=$(localstatedir)/log/squid' '--with-pidfile=$(localstatedir)/run/squid.pid' '--disable-dependency-tracking' '--enable-eui' '--enable-follow-x-forwarded-for' '--enable-auth' '--enable-auth-basic=DB,LDAP,MSNT-multi-domain,NCSA,NIS,PAM,POP3,RADIUS,SASL,SMB,SMB_LM,getpwnam' '--enable-auth-ntlm=smb_lm,fake' '--enable-auth-digest=file,LDAP,eDirectory' '--enable-auth-negotiate=kerberos' '--enable-external-acl-helpers=file_userip,LDAP_group,time_quota,session,unix_group,wbinfo_group,kerberos_ldap_group' '--enable-cache-digests' '--enable-cachemgr-hostname=localhost' '--enable-delay-pools' '--enable-epoll' '--enable-ident-lookups' '--enable-linux-netfilter' '--enable-removal-policies=heap,lru' '--enable-snmp' '--enable-ssl-crtd' '--enable-storeio=aufs,diskd,rock,ufs' '--enable-wccpv2' '--enable-esi' '--enable-ecap' '--with-aio' '--with-default-user=squid' '--with-dl' '--with-openssl' '--with-pthreads' '--disable-arch-native' 'build_alias=x86_64-redhat-linux-gnu' 'host_alias=x86_64-redhat-linux-gnu' 'CFLAGS=-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches   -m64 -mtune=generic -fpie' 'LDFLAGS=-Wl,-z,relro  -pie -Wl,-z,relro -Wl,-z,now' 'CXXFLAGS=-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches   -m64 -mtune=generic -fpie' 'PKG_CONFIG_PATH=:/usr/lib64/pkgconfig:/usr/share/pkgconfig'


El archivo de configuración principal para el proxy de Squid se puede encontrar en /etc/squid/squid.conf. Ahora puede configurar su navegador para usar el servidor proxy que acaba de crear con el puerto 3128. 

Podemos ver los logs::

	tail -500f /var/log/squid/access.log

	1548548510.940    279 192.168.1.3 TCP_MISS/301 704 GET http://google.com/ - HIER_DIRECT/172.217.15.206 text/html
	1548548511.223    277 192.168.1.3 TCP_MISS/302 1100 GET http://www.google.com/ - HIER_DIRECT/172.217.15.196 text/html
	1548548511.675    254 192.168.1.3 TCP_MISS/200 869 POST http://ocsp.pki.goog/GTSGIAG3 - HIER_DIRECT/216.58.192.35 application/ocsp-response
	1548548512.836    193 192.168.1.3 TCP_MISS/200 869 POST http://ocsp.pki.goog/GTSGIAG3 - HIER_DIRECT/216.58.192.35 application/ocsp-response
	1548548528.174    244 192.168.1.3 TCP_MISS/200 524 GET http://detectportal.firefox.com/success.txt - HIER_DIRECT/23.74.2.88 text/plain
	1548548620.592  61835 192.168.1.3 TCP_TUNNEL/200 5137 CONNECT versioncheck-bg.addons.mozilla.org:443 - HIER_DIRECT/52.42.221.117 - 1548548620.822  62047 192.168.1.3 TCP_TUNNEL/200 3921 CONNECT aus5.mozilla.org:443 - HIER_DIRECT/54.148.138.18 -



Squid se puede configurar fácilmente editando el archivo de configuración global /etc/squid/squid.conf, recuerde siempre sacar una copia::

	# vi /etc/squid/squid.conf






	


