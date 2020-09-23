---
title:  "Instalación de Autofirma en Debian 10 Buster"
# subtitle: "Resolviendo problemas"
slug:   "instalacion-de-autofirma-en-Debian-10-Buster"
date:   "2020-08-14T05:59:39+02:00"
draft:  false
bigimg: [{src: "/img/post/autofirma.jpg", desc: ""}]
#categorias: ["Desarrollo Web"]
temas: ["Debian", "Autofirma"]
proyectos: ["Trámites Administración"]
image: "/img/post/autofirma.jpg"
---
Otra herramienta útil  en la tramitación de documentación de manera telemática ante la administración es Autofirma. 
Según nos indican en la sede electrónicoa del gobierno de España.
>Autofirma es una aplicación de firma realizada por el Ministerio de Hacienda y Administraciones Públicas. Su principal objetivo es ofrecer al usuario un sistema de firma en el que éste pueda firmar cualquier tipo de documento de manera sencilla. El usuario indica qué fichero quiere firmar y la aplicación escoge automáticamente el formato de firma qué debe aplicar, liberando así, al usuario de cualquier duda técnica.

<!--more-->
&nbsp;
### Requisitos técnicos previos ###

En Linux para poder realizar el envío telemático usando un certificado o firma electrónica es necesario que se cumplan los siguientes requisitos:

* Tener instalado en el ordenador un certificado digital válido y aprobado por el Ministerio (por ejemplo: certificado del DNIe o un certificado en tarjeta de la FNMT).

* Tener instalado en el ordenador desde el que se quiere realizar el proceso de firma una versión válida de la máquina virtual de Java. En Linux se necesita un entorno de ejecución de Java 8 de Oracle u OpenJDK 8 mínimo (marcado como dependencia en el instalador integrado de AutoFirma). 

* Mozilla Firefox 41.0.1 o superior.

   >Nota: Se ha detectado una incompatibilidad entre la aplicación "Autofirma" y la última versión de navegador web "Mozilla Firefox 71" que impide la realización de firmas electrónicas.
   El problema afecta a los sistemas operativos Windows y Linux. Se recomienda no actualizar a esta versión del navegador."
      
* Biblioteca “libnss3-tools”.

&nbsp;
### Instalando Autofirma ###
Antes de instalar el paquete deb de autofirma debo cumplir los requerimientos mínimos anteriormente mencionados, para que en el momento en que la aplicación haga contacto con el navegador Firefox no ocurra ningún tipo de error que entorpezca luego la correcta instalación.
Como requisito ineludible, la instalación de AutoFirma en Linux debe ser realizada por un usuario con permisos de administrador, así que me logeo como tal y aprovecho para instalar las dependencias (OpenJDK y biblioteca NSS Tools) que requiere para su correcto funcionamiento.

```console  
root@asuspc:~#  apt-get install libnss3-tools
root@asuspc:~#  apt install default-jre
```

Compruebo la versión de java

```console
root@asuspc:~# java -version
openjdk version "11.0.8" 2020-07-14
OpenJDK Runtime Environment (build 11.0.8+10-post-Debian-1deb10u1)
OpenJDK 64-Bit Server VM (build 11.0.8+10-post-Debian-1deb10u1, mixed mode, sharing)
root@asuspc:~#
```

Creo una carpeta donde descargo temporalmente el zip de Autofirma y lo descomprimo.

```console
root@asuspc:~# mkdir tmp_autofirma
root@asuspc:~# cd tmp_autofirma
root@asuspc:~#  wget https://estaticos.redsara.es/comunes/autofirma/currentversion/AutoFirma_Linux.zip
root@asuspc:~# unzip AutoFirma_Linux.zip
Archive:  AutoFirma_Linux.zip
  inflating: AF_manual_instalacion_usuarios_ES.pdf  
 extracting: autofirma-1.6.5-1.noarch.rpm  
 extracting: AutoFirma_1_6_5.deb     
  inflating: autofirma-1.6.5-1.noarch_SUSE.rpm
```

Procedo a instalar Autofirma.

```console
root@asuspc:~# dpkg -i AutoFirma_1_6_5.deb
```

El proceso de instalación creará la CA de Autofirma y la instalará en los navegadores Firefox, Chrome y Chromium. Los navegadores deben estar previamente instalados, en caso de instalarse posteriormente al proceso de instalación de Autofirma sería necesario instalar de forma manual el certificado de confianza, o bien ejecutar de nuevo la instalación de Autofirma.


&nbsp;
{{< figure src="/img/post/autofirma3.jpg" >}}


&nbsp;
El funcionamiento de AutoFirma está verificado en distribuciones Ubuntu, Fedora y OpenSuse. Una vez instalada, puede usarse como aplicación de escritorio e invocarse desde los navegadores web Mozilla Firefox y Google Chrome.

> Hay dos opciones de instalación: por línea de comandos y desde la interfaz de escritorio.
La instalación por ambos medios dejará la aplicación instalada por defecto en el directorio:
`/usr/lib/AutoFirma`.

Una vez finalizada la instalación y comprobado que funciona correctamente, borro el directorio temporal.
```console
root@asuspc:~# cd ..
root@asuspc:~# rm tmp_autofirma
```
Para desinstalar Autofirma se puede hacer.
```console
root@asuspc:~# apt-get remove –-purge autofirma
```

&nbsp;
### Uso de DNIe y tarjetas inteligentes de la FNMT ###
AutoFirma comprueba al abrirse si existen lectores de tarjeta inteligente instalados en el sistema. Si los detecta, muestra al usuario una pantalla para que seleccione si desea “Firmar con DNIe” o “Usar cualquier certificado”. Tanto si se selecciona “Firmar con DNIe” como si se selecciona “Usar cualquier certificado” y luego se selecciona el certificado del DNIe o un certificado en tarjeta de la FNMT, AutoFirma hará uso del certificado mediante un controlador propio integrado en la propia aplicación. Este controlador es JMulticard y permite utilizar los certificados alojados en este tipo de tarjetas aun cuando no se tiene instalado su controlador oficial (CSP o PKCS#11) en el equipo.

JMulticard pedirá el PIN de las tarjetas que lo requieran para acceder al listado de certificados que contienen. También pedirá el PIN cada vez que se desee utilizar para firmar cualquiera de estos certificados. Esto puede suponer un inconveniente si queremos firmar múltiples documentos seguidos. Este comportamiento puede no darse con los controladores oficiales de las tarjetas que  mantienen habilitado el permiso de firma después de la primera, evitando que sea necesario volver a introducir el PIN.

Si no desea utilizar JMulticard cuando seleccione el uso de cualquier certificado, puede deshabilitar su uso desde el menú de preferencias de AutoFirma. Acceda a la aplicación, selecciones el menú “Herramientas” y la opción “Preferencias”. En la pestaña “General” se muestra la opción “Habilitar JMulticard para el uso de las tarjetas de la FNMT y DNIe”. Esta opción estará habilitada por defecto y deberá desactivarla para que se use el controlador oficial de su tarjeta. Después de desactivar esta opción, deberá pulsar Aceptar en el panel. La próxima vez que abra AutoFirma, se cargará el controlador oficial de su tarjeta en lugar de Jmulticard.

&nbsp;
{{< figure src="/img/post/autofirma1.jpg" >}}

&nbsp;
### Posibles problemas

1. **Al instalar AutoFirma falla la instalación de los certificados de confianza SSL.**  
    AutoFirma requiere permisos de administrador para ser instalado y para insertar el certificado de confianza SSL para el funcionamiento de la firma en los trámites online. Si no puede instalar AutoFirma o el certificado de confianza, solicite al administrador de su sistema que realice la instalación de la aplicación.  
&nbsp;
2. **Al abrir Google Chrome después del proceso de instalación de AutoFirma se muestra un mensaje notificando que la configuración de la aplicación está corrupta.**  
    El navegador Google Chrome incluye en su configuración un listado de protocolos que considera seguros para la llamada a aplicaciones externas. Durante el proceso de instalación de AutoFirma se registra el protocolo “afirma” en este listado seguro de Chrome para que las invocaciones desde el navegador se realicen correctamente.
    En algunas situaciones en las que el instalador podría no poder completar el proceso de registro, el fichero de configuración de Chrome podría quedar en un estado inconsistente. En estos casos, al iniciarse de nuevo el navegador, detectará el problema y anunciará al usuario esta corrupción de datos mediante una ventana de advertencia que nos permitirá restaurar las propiedades por defecto.
    Seguidamente, el navegador restaurará las propiedades de configuración y volverá a funcionar normalmente. En este caso, el usuario recibirá mensajes de advertencia al usar AutoFirma desde Chrome para realizar firmas, aunque esto no impedirá que funcione normalmente.
    Durante el proceso de desinstalación de AutoFirma se realiza el proceso inverso al de instalación y se elimina el protocolo “afirma” del listado de protocolos seguros registrados en Chrome. Este proceso podría derivar en algunas circunstancias a la misma corrupción de la configuración del navegador.  
&nbsp;
3. **Cuando se instala Mozilla Firefox o se crea un nuevo perfil de usuario después de la instalación de AutoFirma, este no funciona en Firefox.**  
    Durante el proceso de instalación de AutoFirma se genera un certificado para la comunicación entre la página web y la aplicación, y lo instala en los almacenes de confianza del sistema y de Firefox. Si se crea un perfil de usuario de Firefox o se instala el propio Firefox después de la instalación de AutoFirma, este no contará con el certificado entre los que considera de confianza. Para resolver este problema deberá desinstalar AutoFirma y volverlo a instalar. Tenga en cuenta, sin embargo, que esto implicará que se pierda la configuración personalizada establecida en AutoFirma.  
&nbsp;
4. **AutoFirma indica que un documento PDF es demasiado grande cuando se intenta firmar con firma visible.**  
    Cuando AutoFirma crea la previsualización de un documento PDF, lo carga en memoria y crea miniaturas de cada una de sus páginas. Esta tarea requiere una gran cantidad de memoria y es posible que no pueda completarse porque la aplicación no pueda reservar suficiente. Este problema suele darse con documentos PDF de gran tamaño o con un gran número de páginas. La probabilidad aumenta considerablemente en equipos con instalaciones de 32bits de AutoFirma, ya que no permiten reservar toda la memoria necesaria. También puede ocurrir en equipos con 2Gb de memoria o menos.
    Se recomienda que los usuarios de sistemas operativos de 64bits utilicen AutoFirma de 64bits para reducir la probabilidad de sufrir este error.  
&nbsp;
5. **AutoFirma se cierra inmediatamente tras ser invocado desde el navegador web.**  
    Cuando se abre AutoFirma por petición de un navegador web inmediatamente se abre una conexión entre ambas aplicaciones. Si AutoFirma detecta cualquier problema que evita que se pueda establecer esa comunicación, se cierra. Esto dará lugar a que, pasado un tiempo, la página web que intentó realizar la operación de firma informe de que no se pudo conectar con AutoFirma.
    Si se encuentra en esta situación, utilice la función de “Restaurar instalación” de AutoFirma. Esta función permitirá reestablecer la configuración y los recursos necesarios para que se pueda establecer la comunicación entre el navegador y AutoFirma.
    La función de “Restauración instalación” se encuentra disponible en el menú de herramientas de AutoFirma.  


&nbsp;