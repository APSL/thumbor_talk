
# Thumbor
## [http://talks.apsl.net/thumbor](http://talks.apsl.net/thumbor/#slide1)
## [https://github.com/APSL/thumbor_talk](https://github.com/APSL/thumbor_talk)

.fx: titleslide

---

# Sobre mí

* Edu Herraiz
* [http://www.eduherraiz.com](http://www.eduherraiz.com)
* Administrador de sistemas en [APSL](http://www.apsl.net)
* Me gusta programar en Python, Linux y el software libre.

<img src='http://thumbor.eduherraiz.com/Yj4Fn4jLJz7XKiEqfK0Z8aZoFlQ=/200x0/www.eduherraiz.com/static/portfolio/images/avatar-180.png' />

# Presenter Notes

Antes de empezar un poco de spam sobre mí.
Me llamo Edu Herraiz, en eduherraiz.com podeis ver mis cosas, trabajo en apsl como administrador de sistemas y me gusta python, linux y el software libre. 

---

# ¿Qué es thumbor?

* Servidor de imágenes total e **inteligente**
* Procesa bajo demanda
* Opcionalmente encuentra puntos importantes de la imagen para redimensionar (opencv)
* Escrito en python y pythónico
* Libre (Licencia MIT)
* [globo.com](http://globo.com)

<img src='http://thumbor.eduherraiz.com/dpxbfVJc3HvIP8o080tnCct2Ho4=/600x200/gfif.udea.edu.co/cursos/extension/python/imgs/python-logo.png' />

# Presenter Notes

Bien, empezemos. 

¿Qué es thumbor? Es un servidor de imágenes total, porque incluye todo lo que podemos necesitar para gestionar nuestro media.

¿porqué inteligente? Porque es capaz de encontrar puntos importantes en una imagen y ofrecer recortes adaptados a esos puntos importantes.

Otra ventaja es que procesa bajo demanda, es decir, no tenemos que definir a priori que tamaños o filtros de imagen queremos.

Está escrito en python, que ya es un punto a su favor, pero además, es pythónico y cualquiera de sus partes está preparada para que la adaptemos a nuestras necesidades.

Es libre, con la licencia MIT.

El proyecto lo empezó el periodico brasileño globo, para mejorar su proceso de tratamiento de imágenes en 2011.

---

# Problemática
<img src='img/thumbor2.png' />

---

# Con Thumbor
<img src='img/thumbor1.png' />

---

# ¿Qué funcionalidad cubre?

* Recoge
    * De un URL remota (http_loader)
    * De un storage (filesystem, mongodb, redis, memcached, AWS)
    * De una aplicación (vía API)
* Procesa
    * Detecta puntos importantes
    * Recorta
    * Aplica filtros
    * Optimiza
* Entrega
    * Cachea resultados

# Presenter Notes

¿Qué funcionalidad cubre?

Es capaz de recoger la imagen, desde una URL remota o de un fichero de disco o también se la podemos pasar nosotros desde nuestra aplicación.

Procesa la imagen, reconociendo incluso que es lo importante, recortando en consecuencia, aplicando filtros y optimizando la imagen para reducir su peso.

Y también se encarga de entregarlas al cliente final, guardando las imágenes procesadas en cache para no tener que volver a repetir el proceso en la siguiente petición.

---

# Generación de la URL

* <span style="color:#0073FF">http://thumbor-server</span>/<span style="color:#136340">unsafe</span>/<span style="color:#FF3437">550x200</span>/<span style="color:#B209AF">filters:filtro(0)</span>/<span style="color:#FF8A04">dominio.com/imagen.jpg</span>
* <span style="color:#0073FF">Servidor thumbor</span>
* <span style="color:#136340">Seguridad</span>
* <span style="color:#FF3437">Tamaño</span>
* <span style="color:#B209AF">Filtros y parámetros</span>
* <span style="color:#FF8A04">Cargador</span>

# Presenter Notes

¿Cómo funciona?

El secreto está en la URL. 

Donde necesites una imagen específica, montas los parámetros de una URL thumbor definiendo como la quieres.

Por ejemplo, en la primera sección va el parámetro sobre la seguridad. En el segundo sobre el tamaño de la imagen. 

Qué filtros aplicar a la imagen y finalmente el parámetro de la imagen original a cargar.

Ahora veremos más en detalle las opciones cada parte de la URL.

Voy a obviar la parte de seguridad y carga en los ejemplos donde no sea relevante, para que quede más claro.
    
---

# <span style="color:#136340">Seguridad</span>

* Modo inseguro
    * /unsafe/
    * Cualquiera puede generar URL -> Se podrían aprovechar

* Modo seguro
    * Se genera la URL cifrando con una clave configurada en el cliente y en el server
    * Python: [Libthumbor](https://github.com/thumbor/libthumbor/wiki/Usage)
    * Java: [Pollexor](http://square.github.io/pollexor/)
    * Javascript: [ThumborUrlBuilder](https://github.com/dcaramelo/ThumborUrlBuilder)

* Se puede restringir vía ALLOWED_SOURCES

# Presenter Notes

Thumbor dispone de dos modos de seguridad. Inseguro, done generará cualquier redimensión que le pidamos.

O el modo seguro. Como es el cliente final el que hace la petición a thumbor, lo único que tenemos que proteger es que la imagen que le estamos pidiendo generar sea lícita, para que alguien no aproveche nuestro thumbor para servir sus imágenes.

Para ello en lugar de la palabra clave unsafe se incluye un hash generado en el momento de crear la URL, a partir de una clave conocida por el servidor y el generador de URLS.

Tenemos varias librerías para generar URLS protegidas desde cualquier lenguaje. Aquí os dejo algunos ejemplos.

También incluye la posibilidad de limitar desde que hosts se puede redimensionar, con el típico ALLOWED_SOURCES.


---

# Libthumbor

    !python
    from libthumbor import CryptoURL
    crypto = CryptoURL(key='my-security-key')

    encrypted_url = crypto.generate(
        width=300,
        height=200,
        image_url='www.midominio.com/image.jpg'
    )

URL generada:
/<span style="color:#B209AF">LYlJ76NKiy2HAQ9RZR8iqIOTHGE=</span>/300x200/www.midominio.com/image.jpg

# Presenter Notes

Aquí podeis ver un ejemplo de libthumbor, la librería de python.

---

# <span style="color:#FF3437">Tamaño</span>

<img src='http://thumbor.eduherraiz.com/7h2Rqx7cCaxl5T0nWE0Lq62gj3s=/400x0/www.skyhdwallpaper.com/wp-content/uploads/2014/10/Pear-902x1024.jpg' />

# Presenter Notes

Para el parámetro de tamaño os enseñaré las posibilidades a partir de esta imagen original y con ejemplos.

---

# <span style="color:#FF3437">Tamaño</span>

* /300x300/
<img src='http://thumbor.eduherraiz.com/DOERp5ZQxTAo4JI-gcAFwOkWvoQ=/300x300/thumbor.eduherraiz.com/7h2Rqx7cCaxl5T0nWE0Lq62gj3s=/400x0/www.skyhdwallpaper.com/wp-content/uploads/2014/10/Pear-902x1024.jpg' />

# Presenter notes

El parámetro de tamaño se compone de dos variables numéricas, que hacen referencia al ancho y alto, separadas por la letra equis. 
Aquí veis como redimensiona la imagen forzando el ancho y el alto, pero al ser asímetrica, la recorta.


---

# <span style="color:#FF3437">Tamaño</span>

* /300x/
<img src='http://thumbor.eduherraiz.com/vMU4m5rDcjLKS3GHRebuecsVN9g=/300x0/thumbor.eduherraiz.com/7h2Rqx7cCaxl5T0nWE0Lq62gj3s=/400x0/www.skyhdwallpaper.com/wp-content/uploads/2014/10/Pear-902x1024.jpg' />

# Presenter notes

Podemos obviar una de ellas para mantener la proporción de la imagen.

---

# <span style="color:#FF3437">Tamaño</span>

* /x700/
<img src='http://thumbor.eduherraiz.com/fYKhy3D-hln6vs2jZg4_7yx13-I=/0x700/thumbor.eduherraiz.com/7h2Rqx7cCaxl5T0nWE0Lq62gj3s=/400x0/www.skyhdwallpaper.com/wp-content/uploads/2014/10/Pear-902x1024.jpg' />

# Presenter notes 

Es posible ampliar la imagen, aunque el original sea de tamaño inferior.

---

# <span style="color:#FF3437">Tamaño</span>

* /x-400/
<img src='http://thumbor.eduherraiz.com/ySeNR2LiG3At1wRdrOt_eVf0g84=/0x-400/thumbor.eduherraiz.com/7h2Rqx7cCaxl5T0nWE0Lq62gj3s=/400x0/www.skyhdwallpaper.com/wp-content/uploads/2014/10/Pear-902x1024.jpg' />

# Presenter notes 

También podemos utilizar números negativos, para voltear un eje.

---

# <span style="color:#FF3437">Tamaño</span>

* /-x-400/
<img src='http://thumbor.eduherraiz.com/YFtCXaGhrmDnTmo7mr-kAoMCd8Q=/-x-400/thumbor.eduherraiz.com/7h2Rqx7cCaxl5T0nWE0Lq62gj3s=/400x0/www.skyhdwallpaper.com/wp-content/uploads/2014/10/Pear-902x1024.jpg' />

# Presenter Notes

O los dos ejes.

---

# <span style="color:#B209AF">Filtros y parámetros</span>

<img src='http://thumbor.eduherraiz.com/lY4BggHvlP60kByUlL74S3Dg44s=/700x0/i.huffpost.com/gen/1179678/images/o-KHALEESI-BADASS-facebook.jpg' />

# Presenter Notes

Igual que en el caso del tamaño vamos a ver una muestra de filtros con ejemplos a partir de esta imagen original.


---

# Luminosidad

* /filters:brightness(40)/

<img src='http://thumbor.eduherraiz.com/-eTF4n9MRjQoS_FeURb12LzCsjQ=/700x0/filters:brightness(40)/i.huffpost.com/gen/1179678/images/o-KHALEESI-BADASS-facebook.jpg' />

# Presenter Notes

Se añade la palabra clave "filters:" seguido del filtro a aplicar, en ese caso de luminosidad.

---

# Rotar

* /filters:rotate(90)/

<img src='http://thumbor.eduherraiz.com/FxGeOA_M8LkULUvivZFotVh_yxk=/700x0/filters:rotate(90)/i.huffpost.com/gen/1179678/images/o-KHALEESI-BADASS-facebook.jpg' />

# Presenter Notes

Podemos rotar la imagen especificando el número de grados

---

# Contraste

* /filters:contrast(120)/

<img src='http://thumbor.eduherraiz.com/VkOdYliNxujTmz-IgUTJIlUAHtU=/700x0/filters:contrast(120)/i.huffpost.com/gen/1179678/images/o-KHALEESI-BADASS-facebook.jpg' />

---

# Desenfocar

* /filters:blur(7)/

<img src='http://thumbor.eduherraiz.com/jb3lOCiWNt8c0cFEGITciy2QijQ=/700x0/filters:blur(7)/i.huffpost.com/gen/1179678/images/o-KHALEESI-BADASS-facebook.jpg' />

---

# Marca de agua

* /filters:watermark(http://$URL/pycones_logo.png,-10,-10,30)/

<img src='http://thumbor.eduherraiz.com/lhgJ7l-51w79pkucB07blZTve08=/700x0/filters:watermark(www.eduherraiz.com/media/portfolio/others/pycones_logo.png,-10,-10,30)/i.huffpost.com/gen/1179678/images/o-KHALEESI-BADASS-facebook.jpg' />

# Presenter Notes

Thumbor incorpora filtros más avanzados, como este, posibilitando añadir una marca de agua.


---

# Esquinas redondeadas

* /filters:round_corner(20,255,255,255)/

<img src='http://thumbor.eduherraiz.com/Ga2JUZaMkceWF4v-bPQsl_lXXvk=/700x0/smart/filters:round_corner(20,255,255,255)/i.huffpost.com/gen/1179678/images/o-KHALEESI-BADASS-facebook.jpg' />

---

# Múltiples filtros

* /filters:blur(7):round_corner(20,255,255,255)/

<img src='http://thumbor.eduherraiz.com/47GkAKC52Si-dL7Z47wcs-HEN44=/700x0/smart/filters:blur(7):round_corner(20,255,255,255)/i.huffpost.com/gen/1179678/images/o-KHALEESI-BADASS-facebook.jpg' />

# Presenter notes

También se pueden encadenar filtros con el símbolo 2 puntos. Los irá aplicando secuencialmente.



---

# Storages

<img src='img/storage.png' />

# Presenter notes
Thumbor guarda las imágenes procesadas para no tener que procesarlas de nuevo. Lo llaman result_storage

El proyecto solo propone guardar esta caché en disco, pero puede ser interesante para algunas arquitecturas de alta disponibilidad guardar las imágenes procesadas en s3 con el controlador de cache propio igual que en los otros casos.

El último parámetro de la URL es el que indica la imagen original a cargar.

Thumbor tiene dos cargadores empaquetados:

1 De una url remota, donde es capaz de bajar la imagen.

2 Mediante un fichero, el parámetro de la URL en este caso será la ruta al fichero.

Puedes hacer tu propio cargador siguiendo una estructura sencilla en python, por ejemplo un cargador desde el servicio de Amazon S3.

Dentro de un momento veremos como almacena thumbor las imágenes, pero aquí cabe comentar que thumbor proporciona también una API para poder cargar imágenes en él.
Se envía el fichero vía POST y lo almacena.
Nosotros utilizamos esta API para integrar thumbor con una aplicación Django. Y que suba la imagen directamente a thumbor.

El almacenamiento (o storage) es donde se guardan los archivos que ya han sido cargados, para no tener que cargarlos cada vez.
¿Puede conincidir el almacenacimiento con el cargador? sí, pero esta separación nos ofrece independencia y versatilidad.

---

# Tiempo de carga una petición

<img src='img/grafico1.png'/>

---

# Thumbor vs Nginx

<img src='img/grafico2.png' />

---

# <span style="color:#FF8A04">Custom, custom, custom</span>

* <span style="color:#FF8A04">Custom loader</span>
    * [Cargador AWS S3](https://github.com/APSL/thumbor_aws/blob/master/thumbor_aws/loaders/s3_loader.py) (custom)
* <span style="color:#FF8A04">Custom storage</span>
    * [Amazon S3](https://github.com/APSL/thumbor_aws/blob/master/thumbor_aws/result_storages/s3_storage.py) (custom)
* <span style="color:#FF8A04">Vía API</span>
    * [Petición POST con el fichero de la imagen](https://github.com/thumbor/thumbor/wiki/How-to-upload-images)

--- 

# Resumen

<img src='http://thumbor.eduherraiz.com/1zcW5fxBLOslQsOMl-Ck-IsZU8M=/800x0/www.eduherraiz.com/media/portfolio/others/resumen-storage.png'/>

# Presenter Notes

Tenemos varias opciones en cada unos de ellos. Y dependiendo del caso, escogeremos uno de cada uno.

---

# Resumen

<img src='http://thumbor.eduherraiz.com/OwusvOHVHfpiirSxCLbN0hdFQX0=/800x0/www.eduherraiz.com/media/portfolio/others/casoproduccion.png'/>

# Presenter Notes

Tenemos un caso donde cargamos las imágenes vía API en un storage_s3 y la cache de resultados la tenemos en disco.

---

# Resumen

<img src='http://thumbor.eduherraiz.com/0MzQthgxWHllpQw-d23cfhWaxEc=/800x0/www.eduherraiz.com/media/portfolio/others/caso-mename.png'/>

# Presenter Notes

Aunque hoy os vamos a enseñar otro caso de uso que nos presentará despues Álvaro, cargando las imágenes por http y guardamos el resto a disco.

---

# Procesado inteligente

* Extrae información de la imagen para mejorar los procesados
* Utiliza [OpenCV](http://opencv.org/)
* Detectores focales, faciales, ...
* "/smart/" en la URL
* Detector storage

# Presenter Notes

El procesado inteligente o "smart" extrae información relevante de la imagen.

Utiliza código python (opencv) para seleccionar puntos importantes de la imagen.

En base a esos puntos puede recortar de manera más provechosa.

Se utiliza añadiendo el parámetro  /smart/ en la URL

Thumbor cachea las detecciones para no tener que procesar cada vez.

---

# Procesado inteligente

<img src='http://thumbor.eduherraiz.com/mDYDIi-I4Fc0uXt-cCDaSB0qxxY=/900x0/ell.h-cdn.co/assets/cm/15/11/980x653/5502909853b51_-_elle-00-opener-h-game-of-thrones-premiere-step-and-repeat.jpg' />

# Presenter Notes

Tomemos como original esta imagen de la premiere de juego de tronos en nueva york.

Utilicemos thumbor para pedirle un recorte más pequeño para por ejemplo, incluir en un nuesto blog.

---

# Normal

* /700x250/

<img src='http://thumbor.eduherraiz.com/jceF5sQRVBQN982ZYjoEb5naXnQ=/700x250/ell.h-cdn.co/assets/cm/15/11/980x653/5502909853b51_-_elle-00-opener-h-game-of-thrones-premiere-step-and-repeat.jpg' />

# Presenter Notes

El único que ha quedado contento es Tyrion, que ya se relame pensando en el trono de Hierro, pues no ha quedado titere con cabeza.

---

# Smart

* /700x250/smart/

<img src='http://thumbor.eduherraiz.com/SGDiE8qNBvoTNSgUbt3MynJr4bc=/700x250/smart/ell.h-cdn.co/assets/cm/15/11/980x653/5502909853b51_-_elle-00-opener-h-game-of-thrones-premiere-step-and-repeat.jpg' />

# Presenter Notes

Si utilizamos el modo inteligente, es capaz de detectar las caras en la foto y ofrecer esta versión.

---

# Debug (/debug/smart/) 

<img src='http://thumbor.eduherraiz.com/unsafe/debug/smart/ell.h-cdn.co/assets/cm/15/11/980x653/5502909853b51_-_elle-00-opener-h-game-of-thrones-premiere-step-and-repeat.jpg' />

# Presenter Notes

Podemos ver que ha detectado en la imagen con el parámetro en la URL de debug.

---

# Meneame app

<img src='img/meneame_antes.png'/>

---

# Meneame app (smart)

<img src='img/meneame_despues.png'/>


---

# Detección encolada

* Detección inteligente lleva mucho tiempo
* Proceso en background separado
* **RemoteCV**
* Se comunica vía redis con thumbor
* Se sirve sin smart si no está la detección

# Presenter Notes

Teniendo el original ya cargado, lo que tarda más tiempo en todo el proceso thumbor es la detección inteligente.

Para resolver este problema, thumbor permite una delegación de la detección a un proceso a parte que lo hace en background, llamado remotecv.

Remotecv se comunica con thumbor vía un redis.

Cuando se pide un recorte inteligente, se encola la detección y mientras se sirve la imagen en modo normal.

---

# Optimizadores

* Reducir el peso de la imagen 
* Mejoras entre un 5% y un 15%
* JPEG
* PNG
* GIF

# Presenter Notes

Thumbor incorpora la posibilidad de pasar la imagen generada por un proceso de optimización.

Este proceso lo que hace es reducir el peso de la imagen sin perder calidad.

Tenemos optimizadores para los formatos típicos. Y añadir nuestros propios optimizadores.

---

# Instalación

* Está empaquetado en PyPI
* Opencv
* Dependencias de sistema
* Varios procesos y servicios
* 82 Variables de configuración

<img src='http://thumbor.eduherraiz.com/dXa9GajsulhPADC_vX_g97z3Jg0=/-x0/louisecardon.com/wp-content/uploads/2015/06/drama_queen1.jpg'>

# Presenter Notes

Thumbor y remotecv están empaquetado en PyPI y son sencillos de instalar vía pip install.

Lo que no es tan sencillo de explicar es la parte de OpenCV, sus dependencias y sobretodo las 82 variables con sus combinaciones que incluye el archivo de configuración de thumbor.
Y quizá tampoco os apetezca perder tiempo en aprenderlo.

---

# Docker

* [https://github.com/APSL/docker-thumbor](https://github.com/APSL/docker-thumbor)

<img src='http://thumbor.eduherraiz.com/h4AwndXk660iK3FRIrYEjKU5UuE=/800x0/fotos.subefotos.com/4e7301538895cdc19b0eb5f2a3b60730o.png'/>

# Presenter Notes

Para resolver esto, he perdido un poco de pelo y ganado un par de canas en crear una imagen docker completa que incluye todo lo explicado.

---

# Docker

* [Instalar docker](https://docs.docker.com/)
* [Instalar docker-compose](https://docs.docker.com/compose/install/)
* Crear un thumbor.yml copia de los ejemplos de [APSL/docker-thumbor](https://github.com/APSL/docker-thumbor):
    * [Detección simple](https://github.com/APSL/docker-thumbor/blob/master/docker-compose-detector.yml)
    * [Detección encolada](https://github.com/APSL/docker-thumbor/blob/master/docker-compose-lazy-detector.yml)
    * [AWS S3 storage](https://github.com/APSL/docker-thumbor/blob/master/docker-compose-aws-s3-storage.yml)
    * [Producción](https://github.com/APSL/docker-thumbor/blob/master/docker-compose-production.yml)
* docker-compose -f thumbor.yml up
* [http://localhost:8000/unsafe/400x/www.eduherraiz.com/static/portfolio/images/avatar-180.png](http://localhost:8000/unsafe/400x/www.eduherraiz.com/static/portfolio/images/avatar-180.png)

# Presenter Notes


No voy a entrar en como instalar docker o docker-compose, está muy bien documentado para cualquier SO en la documentación oficial, os dejo los links.

Solo teneis que crear un archivo yml con la definición de la arquitectura docker (teneis 4 ejemplos en nuestro github con los casos típicos)

Y ejecutais un comando "docker-compose up". Ya podreis probar en local todo el servicio y sus posibilidades.

---

# Demo time

---

# Documentación y links

* [Wiki de github](https://github.com/thumbor/thumbor/wiki/Usage)
* [Propio código](https://github.com/thumbor/thumbor)
* [thumbor.org](http://thumbor.org)
* [http://thumborize.me/](http://thumborize.me/)
* [Post de @heynemann sobre escalar thumbor](http://heynemann.logdown.com/posts/152158-scaling-thumbor)

# Presenter Notes

Os dejo aquí algunos links para profundizar.
Gracias por atender y espero poder resolver vuestras dudas.

---

<img src='http://thumbor.eduherraiz.com/p75qqh8w3xs_mxE2HGAn1zVCMSY=/1024x768/smart/www.eduherraiz.com/media/portfolio/others/salteando.jpg'/>

.fx: imageslide

---

# Escalabilidad

<img src='http://thumbor.eduherraiz.com/RuqtehFcyA436PDmfCmjwLw9_Xw=/0x650/www.eduherraiz.com/media/portfolio/others/escalabilidad.png'/>

# Presenter notes

Thumbor escala muy bien, os propongo este esquema como ejemplo. 

Podríamos tener los servicios de almacenacimiento y base de datos redis en servicios cloud que nos ya nos ofrezcan alta disponibilidad.

Con ene instancias que contengan los procesos thumbor y remotecv que realmente procesan, con un número de instancias elástico según carga.

Un balanceador al gusto e incluso con una CDN por encima para acelerar la distribución geográfica y la entrega.
