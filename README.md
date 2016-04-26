Facebook Live Streaming
===================

Requerimientos
-------------
1. Instalar ffmpeg
	```brew install ffmpeg --with-fdk-aac --with-ffplay --with-freetype --with-libass --with-libquvi --with-libvorbis --with-libvpx --with-opus --with-x265```

2. OBSProject [descargar](https://obsproject.com/)

Facebook Live para páginas verificadas
-------------
1. Para páginas verificadas es mas simple que , sólo deben ir al Administrador comercial ➜ Herramientas de publicación ➜ en la lateral Videos en vivo ➜ clickear el botón **Crear** y aparece el siguiente modal
![Crear Video en facebook](https://github.com/EstebanFuentealba/facebook-live-streaming/blob/master/assets/img/crear-video.png)

Con ésto obtenemos la **URL del servidor o de transmisión** , con ésta dirección podremos enviar a facebook un streaming

y para eso podemos hacerlo de 2 formas
- **ffmpeg** por linea de comandos
- **OBSProject** una forma mas elaborada y de codigo abierto


Transmitir por ffmpeg
-------------
Ya teniendo la **URL del servidor o de transmisión** que tiene el siguiente formato:
```
rtmp://rtmp-api.facebook.com:80/rtmp/111111111111111?ds=1&a=XXXXXXXXXXXXXXXXX
```
donde la **URL del servidor** es
```
rtmp://rtmp-api.facebook.com:80/rtmp/
```
y la **Clave de transmisión** es

```
111111111111111?ds=1&a=XXXXXXXXXXXXXXXXX
```

 Podemos ejecutar los siguientes comandos pasando la completa **URL del servidor o de transmisión**:
Transmitir un mp4
```
ffmpeg -re -i ./video.mp4 -acodec libmp3lame  -ar 44100 -b:a 128k -pix_fmt yuv420p -profile:v baseline -s 426x240 -bufsize 6000k -vb 400k -maxrate 1500k -deinterlace -vcodec libx264 -preset veryfast -g 30 -r 30 -f flv "rtmp://rtmp-api.facebook.com:80/rtmp/111111111111111?ds=1&a=XXXXXXXXXXXXXXXXX"
```

![Facebook Live con video local y ffmpeg](https://github.com/EstebanFuentealba/facebook-live-streaming/blob/master/assets/img/streaminglocal.png)

Transmitir un streaming HLS
> **Ejemplo:** transmitiendo 13.cl
```
ffmpeg -re -i "http://13.live.cdn.cl/13hddesktop/13hd-1250.m3u8" -acodec libmp3lame  -ar 44100 -b:a 128k -pix_fmt yuv420p -profile:v baseline -s 426x240 -bufsize 6000k -vb 400k -maxrate 1500k -deinterlace -vcodec libx264 -preset veryfast -g 30 -r 30 -f flv "rtmp://rtmp-api.facebook.com:80/rtmp/111111111111111?ds=1&a=XXXXXXXXXXXXXXXXX"
```
![Facebook Live con HLS Streaming y ffmpeg](https://github.com/EstebanFuentealba/facebook-live-streaming/blob/master/assets/img/streaminghls.png)

Con ffmpeg hay que saber como configurar las flags para sacar el mayor probecho


Transmitir por OBSProject
-------------
A la escena hay que agregar una fuente y seleccionar **Fuente multimedia** ➜  se agrega un nombre a la fuente y se selecciona si es un archivo local (mp4) o una entrada remota (HLS)
![Facebook Live con HLS Streaming y OBSProject](https://github.com/EstebanFuentealba/facebook-live-streaming/blob/master/assets/img/obsmedia.png)

Ya teniendo el streaming hay que configurar para enviarlo a los servidores de facebook

Ir al botón **Configuración** ➜ **Flujo** ➜ en el Tipo de flujo se selecciona **Personalizar el servidor de retransmisión** configurando la **URL del servidor** y **Clave de transmisión**  ➜ Aplicar ➜ Aceptar ➜ y luego presionar el botón **Iniciar Retransmisión**

![Facebook Live con HLS Streaming y OBSProject](https://github.com/EstebanFuentealba/facebook-live-streaming/blob/master/assets/img/megastreaming.png)
