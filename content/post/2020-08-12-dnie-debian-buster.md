---
title:  "Instalación de DNI electrónico (DNIe) en Debian 10 Buster"
# subtitle: "Resolviendo problemas"
slug:   "instalacion-de-dnie-en-Debian-10-Buster"
date:   "2020-08-12T05:59:39+02:00"
draft:  false
bigimg: [{src: "/img/post/dnie3.jpg", desc: ""}]
#categorias: ["Desarrollo Web"]
temas: ["Debian", "DNIe"]
proyectos: ["Trámites Administración"]
image: "/img/post/dnie3.jpg"
---
Para facilitar algunos de mis trámites con la administración pública, tanto local como estatal, voy a instalar un lector de DNIe en mi máquina con Debian Buster y los certificados de dicho documento en el navegador web Firefox 68.12.0esr (64-bit). 
<!--more-->
&nbsp;
### Preparando la instalación ###
1. Instalo los paquetes necesarios para el lector y la gestión de la entrada del PIN 

    ```console
    usuario@usuariopc:~$ sudo apt-get install pcscd pcsc-tools opensc
    ```

    Dependiendo del entorno de ventanas que esté utilizando, deberé instalar el paquete de pinentry (pinentry-curses para diálogos de consola, pinetry-gtk2 para Gnome o pinentry-qt4 para KDE) que solicita el pin de nuestro DNIe: En mi caso, al usar Gnome, instalaré pinentry-gtk2:

    ```console
    usuario@usuariopc:~$ sudo apt-get install pinentry-gtk2
    ```

2. Compruebo con el comando pcsc_scan que el sistema reconoce el  lector.
    Conecto el lector USB con el DNI metido, y escribo

    ```console
    usuario@usuariopc:~$ pcsc_scan
    ```

    Esta es la salida por consola:

    ```console
    usuario@usuariopc:~$ pcsc_scan

    Using reader plug'n play mechanism
    Scanning present readers...
    0: C3PO LTC31 v2 00 00
    
    Mon Jun 24 12:35:44 2019
    Reader 0: C3PO LTC31 v2 00 00
    Event number: 0
    Card state: Card inserted, Shared Mode, 
    ATR: 3B 7F 96 00 00 00 6A 44 4E 49 65 10 01 01 55 04 21 03 90 00

    ATR: 3B 7F 96 00 00 00 6A 44 4E 49 65 10 01 01 55 04 21 03 90 00
    + TS = 3B --> Direct Convention
    + T0 = 7F, Y(1): 0111, K: 15 (historical bytes)
    TA(1) = 96 --> Fi=512, Di=32, 16 cycles/ETU
        250000 bits/s at 4 MHz, fMax for Fi = 5 MHz => 312500 bits/s
    TB(1) = 00 --> VPP is not electrically connected
    TC(1) = 00 --> Extra guard time: 0
    + Historical bytes: 00 6A 44 4E 49 65 10 01 01 55 04 21 03 90 00
    Category indicator byte: 00 (compact TLV data object)
        Tag: 6, len: A (pre-issuing data)
        Data: 44 4E 49 65 10 01 01 55 04 21
        Mandatory status indicator (3 last bytes)
        LCS (life card cycle): 03 (Initialisation state)
        SW: 9000 (Normal processing.)

    Possibly identified card (using /usr/share/pcsc/smartcard_list.txt):
    3B 7F 96 00 00 00 6A 44 4E 49 65 10 01 01 55 04 21 03 90 00
    3B 7F 96 00 00 00 6A 44 4E 49 65 10 01 01 55 04 .. 03 90 00
            DNIE Spain (eID)
            http://www.dnielectronico.es/PortalDNIe/
    ```
    Salgo de  pcsc_scan con `Ctrl+C` y desconecto el lector de DNI.

3. Descargo el paquete de DNI electrónico, para Debian Buster desde la página de la Policía Nacional

    ```console
    usuario@usuariopc:~$ wget https://www.dnielectronico.es/descargas/distribuciones_linux/Debian_10_libpkcs11-dnie_1.5.3_amd64.deb
    ```

    Compruebo que la suma de comprobación es correcta:

    ```console
    usuario@usuariopc:~$ md5sum Debian_10_libpkcs11-dnie_1.5.3_amd64.deb 
    c9a15dd9ae4327a468f850af93b8ef45   Debian_10_libpkcs11-dnie_1.5.3_amd64.deb
    ```

4. Lo instalo
    
    ```console
    usuario@usuariopc:~$ sudo dpkg -i Debian_10_libpkcs11-dnie_1.5.3_amd64.deb
    ```

    Aquí se instala el paquete pero luego da un error porque intenta abrir Firefox como root, cosa que el sistema no deja.
    Lo que hace es llamar a un script en Perl que es el que lanza Firefox, así que lo llamo como usuario normal: `usuario@usuariopc:~$ /usr/share/libpkcs11-dnie/launch.pl`.
    Se abre Firefox y hay que seguir las instrucciones de la página web que aparece `(file:///usr/share/libpkcs11-dnie/launch.html)` para cargar el módulo de seguridad de DNIe, y la autoridad de certificación FNMT. 


&nbsp;
#### Para usar el DNI electrónico se requiere: ####  
 * **Instalar el Módulo de Seguridad PKCS#11**  
     Para instalar el módulo PCKS#11 debo ir en Firefox a `Preferencias/Privacidad & Seguridad/Seguridad/ certificados/Dispositivos de seguridad` y selecciono`Cargar`.  
     Le doy un nombre al módulo. (Por ejemplo "Modulo DNIe").  
     Indico manualmente la ruta del módulo: `/usr/lib/libpkcs11-dnie.so`.  
     Pulso el botón`Aceptar`.  
     &nbsp;
    {{< figure src="/img/post/dni_pkcs.jpg" >}}
&nbsp;
    {{< figure src="/img/post/dni_pkcs2.jpg" >}}
    Con la sesión iniciada.  
    {{< figure src="/img/post/dni_pkcs3.jpg" >}}

&nbsp;
 * **Instalar el Certificado Raíz de la Autoridad de Certificación del DNIe**        
      Para instalar el certificado raíz debo ir  en Firefox a `Preferencias/Privacidad & Seguridad/Seguridad/ certificados/Ver certificados` y en la pestaña `Autoridades` selecciono`Importa`.  
      &nbsp;
      {{< figure src="/img/post/dni_cert_raiz.jpg" >}}  
      Indico manualmente la ruta del certificado raíz: `/usr/share/libpkcs11-dnie/ac_raiz_dnie.crt`.  
      El asistente me pide que establezca la confianza para el certificado, por lo que marco las tres casillas de confianza y pulso el botón `Aceptar`.  
      &nbsp;
      {{< figure src="/img/post/dni_cert_raiz1.jpg" >}}

Una vez hecho esto, cierro Firefox, lo vuelvo a abrir, entro en una web que me pida el DNI electrónico y compruebo que funciona.


&nbsp;