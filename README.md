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



Facebook Live para usuarios normales
-------------
Para transmitir con ffmpeg , OBSProject o cualquier otro Broadcaster Software se debe dar usar el sdk o api de facebook.


### Requerimientos
1. Ser un facebook developer y tener una [app en facebook](https://developers.facebook.com/) (tener una app id)
2. Un servidor para subir un archivo html


Se debe subir el siguiente archivo al servidor y cambiar el valor del appId
```html
<meta charset="utf-8">
<button id="liveButton">Create Live Stream To Facebook</button>
<div id="log"></div>
<script>
window.fbAsyncInit = function() {
	FB.init({
	  appId      : 'XXXXXXXXXXXXXXXXX',
	  xfbml      : true,
	  version    : 'v2.4'
	});
	document.getElementById('liveButton').onclick = function() {
	FB.ui({
	    display: 'popup',
	    method: 'live_broadcast',
	    phase: 'create',
	}, function(response) {
	    if (!response.id) {
	      alert('dialog canceled');
	      return;
	    }
	    var matches = response.stream_url.match(/rtmp\:\/\/rtmp-api.facebook.com:80\/rtmp\/(.*)/);
	    if(matches.length == 2) {
	        var serverKey = matches[1];
	        var serverURL = matches[0].replace(serverKey, '')
	      }
		  console.log("KEY " , serverKey)
		  console.log("SERVER " , serverURL)

		  document.getElementById('log').innerHTML = '<div>URL del servidor o de transmisión:<br /> <textarea style="width:100%">'+response.stream_url+'</textarea></div><div>URL del servidor: <br /><textarea style="width:100%">'+serverURL+'</textarea></div><div>Clave de transmisión: <br /><textarea style="width:100%">'+serverKey+'</textarea></div><div><button id="livebroadcast">Ya copié los datos, configure mi Broadcaster y está transmitiendo</button></div>';

		  document.getElementById('livebroadcast').onclick = function() {
			  FB.ui({
			      display: 'popup',
			      method: 'live_broadcast',
			      phase: 'publish',
			      broadcast_data: response,
			    }, function(response) {
			    alert("video status: \n" + response.status);
			    });
		  };
	  });
	};

};
(function(d, s, id) {
  var js, fjs = d.getElementsByTagName(s)[0];
  if (d.getElementById(id)) return;
  js = d.createElement(s); js.id = id;
  js.src = "//connect.facebook.net/es_ES/sdk.js#xfbml=1&version=v2.4";
  fjs.parentNode.insertBefore(js, fjs);
}(document, 'script', 'facebook-jssdk'));
</script>
```


Presionar el botón **Crear Live Stream en Facebook** , se abre un popup y seleccionar donde se quiere compartir ➜ Siguiente

![Facebook Live con HLS Streaming Usuario Normal](https://github.com/EstebanFuentealba/facebook-live-streaming/blob/master/assets/img/transmitenormal.png)

Copiar los datos necesarios según el software Broadcaster.

Clickear el botón **Ya copié los datos, configure mi Broadcaster y está transmitiendo**

y ya estás transmitiendo a facebook como usuario normal
![Facebook Live con HLS Streaming Usuario Normal](https://github.com/EstebanFuentealba/facebook-live-streaming/blob/master/assets/img/usuarionormalstreaming.png)
