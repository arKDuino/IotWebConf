# IotWebConf [![Build Status](https://travis-ci.org/prampec/IotWebConf.svg?branch=master)](https://travis-ci.org/prampec/IotWebConf)

## Resumen
IotWebConf es una biblioteca Arduino para ESP8266 / ESP32 para proporcionar un portal de configuración web WiFi / AP autónomo sin bloqueo.
**¡Para ESP8266, IotWebConf requiere el paquete de placa esp8266 versión 2.4.2 o posterior!**

## Destacar

  - Administra la configuración de la conexión WiFi.
  - Proporciona una interfaz de usuario del portal de configuración.
  - Puede ampliar la configuración con sus propios elementos de propiedad, que se almacenan automáticamente
  - Personalización de HTML
  - Soporte de validación para los elementos de propiedad de configuración.
  - El código de usuario será notificado de los cambios de estado con métodos de devolución de llamada.
  - Configuración (incluidos sus elementos personalizados) almacenados en la EEPROM.
  - Soporte de actualización de firmware OTA.
  - El portal de configuración permanece disponible incluso después de conectar WiFi
  - Ventana emergente automática "Iniciar sesión en la red" en su navegador (portal cautivo),
  - Sin bloqueo: su código personalizado no se bloqueará en todo el proceso.
  - Archivo de encabezado bien documentado y ejemplos de niveles simples a complejos.

![Screenshot](https://sharedinventions.com/wp-content/uploads/2018/11/Screenshot_20181105-191748a.png)
![Screenshot](https://sharedinventions.com/wp-content/uploads/2019/02/Screenshot-from-2019-02-03-22-16-51b.png)
  
## Cómo funciona
La idea es que Thing proporcionará una interfaz web para permitir modificar su configuración. Por ejemplo, para conectarse a una red WiFi local, necesita el SSID y la contraseña.

Cuando no hay WiFi configurado o la red configurada no está disponible, crea su propio AP (punto de acceso) y permite que los clientes se conecten directamente a él para realizar la configuración.

Además, hay un botón (o digamos un Pin), que cuando se presiona al inicio hará que se use una contraseña predeterminada en lugar de la configurada (olvidada). Puede encontrar la contraseña predeterminada en las fuentes. :)

IotWebConf guarda la configuración en la "EEPROM". Puede ampliar el portal de configuración con sus elementos de configuración personalizados. Esos artículos también serán mantenidos por IotWebConf.

## Casos de uso
  1. **Enciende su IoT la primera vez:** - se convierte en modo AP (punto de acceso) y lo espera en la dirección 192.168.4.1 con una interfaz web para configurar su red local (y otras configuraciones). Por primera vez, se utiliza una contraseña predeterminada cuando se conecta al AP. Cuando se conecta al AP, es probable que su dispositivo muestre automáticamente la página del portal. (Llamamos a esto un portal cautivo). Cuando se realiza la configuración, debe abandonar el AP. El dispositivo detecta que no hay nadie conectado y continúa con el funcionamiento normal.
  1. **La configuración de WiFi se cambia, por ejemplo, el Thing se mueve a otra ubicación** - cuando el Thing no se puede conectar al WiFi configurado, vuelve al modo AP y espera a que cambie la configuración de la red. Cuando no se realizó ninguna configuración, sigue intentando conectarse con las configuraciones ya configuradas. The Thing no apagará el AP mientras haya alguien conectado a él, por lo que debe abandonar el AP cuando haya terminado con la configuración.
  1. **Desea conectarse al AP, pero ha olvidado la contraseña configurada de AP WiFi que configuró anteriormente** - conecte el pin apropiado en el Arduino a tierra con un botón. Si mantiene presionado el botón mientras enciende el dispositivo, la Cosa inicia el modo AP con la contraseña predeterminada. (Ver Caso 1. El pin está configurado en el código).
  1. **You want to change the configuration before the Thing connects to the Internet** - Fine! The Thing always starts up in AP mode and provides you a time frame to connect to it and make any modification to the configuration. Any time one is connected to the AP (provided by the device) the AP will stay on until the connection is closed. So take your time for the changes, the Thing will wait for you while you are connected to it.
  1. **You want to change the configuration at runtime** - No problem. IotWebConf keeps the config portal up and running even after the WiFi connection is finished. In this scenario you must enter username "admin" and password (already configured) to enter the config portal. Note, that the password provided for the authentication is not hidden from devices connected to the same WiFi network. You might want to force rebooting of the Thing to apply your changes.

## IotWebConf vs. WiFiManager
tzapu's WiFiManager is a great library. The features of IotWebConf may appear very similar to WiFiManager. However, IotWebConf tries to be different.
  - WiFiManager does not manages your custom properties. IotWebConf stores your configuration in "EEPROM".
  - WiFiManager does not do validation. IotWebConf allow you to validate your property changes made in the config portal.
  - With WiFiManager you cannot use both startup and on-demand configuration. With IotWebConf the config portal remains available via the connected local WiFi.
  - WiFiManager provides list of available networks and also an information page, while these features are cool, IotWebConf tries to keep the code simple. So these features are not (yet) provided by IotWebConf.
  - IotWebConf is fitted for more advanced users. You can keep control of the web server setup, configuration item input field behavior, and validation.

## Security aspects
  - The initial system password must be modified by the user, so there is no build-in password.
  - When connecting in AP mode, the WiFi provides an encryption layer, so all you communication here is known to be safe.
  - When connecting through a WiFi router (WiFi mode), the Thing will ask for authentication when someone requests the config portal. This is required as the Thing will be visible for all devices sharing the same network. But be warned by the following note...
  - NOTE: **When connecting through a WiFi router (WiFi mode), your communication is not hidden from devices connecting to the same network.** So either: Do not allow ambiguous devices connecting to your WiFi router, or configure your Thing only in AP mode!
  - However IotWebConf has a detailed debug output, passwords are not shown in this log by default. You have
  to enable password visibility manually in the IotWebConf.h with the IOTWEBCONF_DEBUG_PWD_TO_SERIAL
  if it is needed.

## Compatibility
IotWebConf is primary built for ESP8266. But meanwhile it was discovered, that the code can be adopted
to ESP32. There are two major problems.
  - ESP8266 uses specific naming for it's classes (e.g. ESP8266WebServer). However ESP32 uses a more generic naming (e.g. WebServer). The idea here is to use the generic naming hoping that ESP8266 will adopt these "standards" sooner or later.
  - ESP32 does not provides an HTTPUpdateServer implementation. So in this project we have implemented one. Whenever ESP32 provides an official HTTPUpdateServer, this local implementation will be removed.
  
## TODO / Feature requests
  - We might want to add a "verify password" field.
  - Possibility to organize blocks of config items to lists. (E.g. provide more SSIDs with passwords as a connection option.)
  - Option the configure multiply WiFi connections. Try next when the last used one is just not available.

## Known issues
  - It is reported, that there might be unstable working with different lwIP variants. If you experiment serious problems, try to select another lwIP variant for your board in the Tools menu! (Tested with "v2 Lower Memory" version.)
  
## Credits
Although IotWebConf started without being influenced by any other solutions, in the final code you can find some segments borrowed from the WiFiManager library.
  - https://github.com/tzapu/WiFiManager

Thanks to [all contributors](/prampec/IotWebConf/graphs/contributors) providing patches for the library!
