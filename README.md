# 📃 Un landingpage en OpenWRT que presenta accesos QR para las redes de invitados.
Recientemente leí una guía en internet que mostraba cómo crear dos archivos en el router. 🎲 Un rotador de contraseñas para las interfaces wi-fi de un router OpenWRT, que aprovechaba `qrencode` para generar archivos 'QR*.svg' que eran volcados sobre el directorio `/www/` en el router. Y otro, 📋 un landingpage en `/www/cgi-bin/`, que utilizaba esos archivos 'QR*.svg' generados para exhibir los accesos a cada red de invitados. Todo apoyado sobre las posibilidades que brindan `uhttpd` y `qrencode` instalados en el router.

🛠️ Basado sobre esta lógica, adapté el script y el landingpage para que sus funcionalidades sean más limpias. 
- Primero, el script original establecía shell variables donde manualmente se debían inputar los datos como SSID, el tipo de red, etc. Esto no es óptimo y puede conducir a confusiones. Ajusté al script, para que todo sea obtenido automáticamente mediante llamadas UCI.
- En mí opinión, ambos debían ser independientes entre ellos. Si yo quiero rotar de contraseñas con un script atado a cron eso está bien. Pero la exhibición de QRs en un landingpage para invitados no puede depender de la existencia o no de ese script, que los esté generando por fuera de él (y nuevamente "☝🏻 tener todo separado puede generar confusiones"). La generación de QR entonces ahora va a ir embebida dentro del CGI script, y no va a depender de ningún rotador. Tener un rotador ahora es opcional.
- 💾 En líneas generales, usamos llamadas `uci` para obtener todos los datos y pasarlos a shell variables > que luego van a ser empleadas para asociarse con las interfaces wi-fi > cuya sintáxis para que `qrencode` genere los códigos de acceso va a estar mucho más prolijamente establecida.
## 📓 ¿Cómo se usa?
Es necesario tener `qrencode` instalado en el router. 
- via GUI / LuCi    System → Software → Update lists  →  Busca el dispositivo/paquete 'qrencode'  →  Instálalo.
- via commandline  `opkg update && opkg install qrencode`.

Copia y pega [el CGI-script en este hipervínculo.](https://raw.githubusercontent.com/nm-cash/guest_QR/refs/heads/main/guest) Necesitas tener el archivo volcado sobre tu directorio `/www/cgi-bin/` en el router.
También necesitarás darle permisos de ejecución, y reiniciar el servicio `uhttpd` para que el landingpage sea asimilado por tu sistema (ver comando a continuación).
```ash
chmod +x /www/cgi-bin/guest    ## Otorgamos al router permiso de ejecutar el archivo "guest"
/etc/init.d/uhttpd restart     ## Reiniciamos el servicio "uhttpd", que tiene a cargo todo lo relacionado con "/www/."
```
Una vez creado el archivo → volcado en el directorio → con los permisos de ejecución y con el servicio uhttpd restarteado, en un navegador web debes ir a la IP donde resida la interfaz administrativa de tu router, la misma donde estaría LuCI. Pongamos por ejemplo "192.168.1.1", y agregarle la dirección "/cgi-bin/guest". https://192.168.1.1/cgi-bin/guest. 👏🏻 Eso es todo. Verás información sobre los accesos y los respectivos códigos QR para cada uno de ellos.
## 🗣️ Comentario final - ¡Te invito a la acción!
Si te encontrás con cualquier detalle que quieras comentar sobre este script, ¡no dudes en hacerlo! Sería excelente seguirlo desarrollando. 
