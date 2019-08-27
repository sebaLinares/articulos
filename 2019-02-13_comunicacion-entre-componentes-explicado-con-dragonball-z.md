omunicación entre componentes, explicado con Dragon Ball Z— Angular 7 — Parte 1.
  
![goku-y-gohan](https://miro.medium.com/max/3840/1*mVzKRZkN3Sv2tXfYNknmEw.jpeg)

Por ahí escuché que si se lo puedes explicar a un niño de 5to básico, es porque lo entiendes. Y qué hace un niño de 5to básico, pues ver **Dragon Ball Z** — bueno, al menos yo lo hacía — . Así que me valdré de Goku y sus amigos para explicar este pequeño fragmento del quehacer de nuestro querido framework **Angular**.

> “Si no lo puedes explicar con simplicidad, es que no lo entiendes bien”. Albert Einstein.

## Contextualizando
¿Cómo hago para que **este** componente le envíe información a este **otro** componente? **Todos** (sí, todos) nos hacemos esa pregunta cuando estamos comenzando a usar **Angular**, algunos hasta mucho después del haber comenzado y, si bien utilizar un framework tan robusto como este trae muchas ventajas, también supone ciertos desafíos 💪. Este será un post en colaboración con **Angular Chile** en el que intentaré explicar, de una forma sencilla, el funcionamiento de la comunicación e interacción entre componentes de distintos niveles. Espero les quede más claro una vez leído, comencemos!

## Comunicación Padre 👨 ← → Hijo 👦
![goku-gohan-nube](https://miro.medium.com/max/1000/1*KnatQV0IaboioH2EZjj2Rg.jpeg)


No, no hablaremos de psicología y de cómo un padre debería comunicarse con su hijo, hablaremos de la comunicación entre un componente padre 👨hacia un **componente hijo** 👦, y viceversa. Primero que todo: ¿Qué hace que un componente sea padre/hijo de otro?
Al igual que la relación de Gohan con Goku — hijo y padre — , un componente que se instancia adentro de otro componente, podemos llamarlo componente hijo. Esta relación de “parentesco” les dará la habilidad de comunicarse y compartir propiedades o eventos siguiendo las buenas prácticas de Angular en relación a la **separación de intereses**.

## ¿Para qué quiero enviarle información a un componente hijo y/o desencadenar eventos hacia un componente padre?
Las buenas prácticas de angular nos recomiendan modularizar nuestras aplicaciones. Básicamente separar un gran componente, con cientos de líneas de código, en pequeñas sub unidades, que individualmente no son funcionales, pero que en connjunto, generan la lógica que en un comienzo se pensó. Para esto tendremos que generar **componentes controladores** y **componentes presentadores** - smart y dumb components. Esencialmente, un **componente controlador** es quien se debería comunicar con la
base de datos, servicios u otros y debería ser a través de ellos que se reparta la información hacia sus componentes hijo. Se preocupa de **cómo funcionan las cosas**. A diferencia de nuestro **componente presentador**, que se preocupa de **cómo se ven las cosas**. Este tipo de componentes obtiene la información desde su componente padre a través de ‘@Input( )’s y le avisa de eventos al mismo a través de ‘@Output( )’s. No ahondaré en esto, porque de buenas prácticas hay mucho que decir, quizás para otra entrada.

## Comunicación padre a hijo utilizando '@Input()' 👈
Imaginemos que el componente **GohanComponent** quiere hacer un Kamehameha, pero no sabe cómo 🤔 ¿Quién le enseñará? Su padre, el componente **GokuComponent** a través de un decorador@Input() que permite traspasar datos desde el template — o desde el archivo .html — .

<script src="https://gist.github.com/sebaLinares/40f424d2c649a0a2277173cbbe139d9c.js"></script>

<script src="https://gist.github.com/sebaLinares/e3f2253524111c5d681b99d33980b8b0.js"></script>

Ahora Gohan puede utilizar el `kamehameha` cuando quiera, ya que su padre se lo ha enseñado (comunicado) 👌.

<iframe src="https://giphy.com/embed/NPGWVCyKOwMzNBZnW0" width="480" height="250" frameBorder="0" class="giphy-embed" allowFullScreen></iframe>

Ejemplo en [Stack Blitz](https://stackblitz.com/edit/goku-gohan) 👨💻
  
  
## Comunicación hijo a padre utilizando '@Output()' y '@EventEmitter()' 👈
Tal como les mencioné, un componente padre — controlador — tiene la **lógica** de una función, pero es su componente hijo — presentador — quien debe enviar un aviso, para desencadenarla. Volvamos a 5to básico y a Dragon Ball Z 📺.
Se acuerdan de la Genkidama? Bueno, esta técnica que salvo a la tierra 🌎 en incontables oportunidades solo la realizaba Gokú. Él, nuevamente, es nuestro **componente controlador**, tiene la lógica de la genkidama(){}. Como ya sabrán todos — porque los que no, ya hicieron click en el link y ahora saben lo que es una genkidama — para realiza está técnica se necesita la energía de todos los seres vivientes del universo — o todos los que quieran ayudar — . Para términos demostrativos, imaginemos que Gohan, nuevamente nuestro componente presentador, es el último en entregar su energía, con la cual se completaría la genkidama para que nuestro héroe Gokú, pueda hacer uso de ella y derrotar a <ingrese su villano favorito> 👹. Vamos con el código.

<script src="https://gist.github.com/sebaLinares/f0990554bae744ed193c93c4c6a9fcca.js"></script>

<script src="https://gist.github.com/sebaLinares/88a258d0f7b15039ca53f5f8ee685e1a.js"></script>

Finalmente con la energía de `GohanComponent`, nuestro `GokuComponent` puede terminar la `genkidama` y está listo para salvar nuevamente el planeta 👏.

<iframe src="https://giphy.com/embed/1zl0R62f0kqUJOqICe" width="480" height="352" frameBorder="0" class="giphy-embed" allowFullScreen></iframe>

Ejemplo en [Stack Blitz](https://stackblitz.com/edit/gohan-goku) 👨💻

## Comunicación entre componentes hermanos 👦 ← → 👦  
  
Para terminar y a modo de integración de lo aprendido hasta el momento. ¿Cómo solucionaríamos el problema de comunicación entre dos componentes del mismo nivel jerárquico?

![comunicacion hijo - hijo](https://miro.medium.com/max/1224/1*PglXhaTxXwuZWbiGcTYOLA.jpeg)

Vamos con Dragon Ball. Hubo un momento en la serie, en que Goku y Gohan (padre e hijo) estaban en el Planeta Sagrado y el único que de comunicaba con el planeta tierra, era Goku a través del Kaio Supremo. Imaginemos que Gohan quería comunicarle un mensaje a su hermano Goten. Gohan no puede hablarle directamente, debe hacerlo a través de Goku… ¿Cómo lo haría entonces? Vamos al código.

<script src="https://gist.github.com/sebaLinares/c4661c162cb7029fae5af8194f3aa944.js"></script>

<script src="https://gist.github.com/sebaLinares/293e8ba95042066ea03391787a4e5e43.js"></script>

<script src="https://gist.github.com/sebaLinares/fd53bb8ef822a2d8ce79e8c6387dcd09.js"></script>

<iframe src="https://giphy.com/embed/dJMK0wsFfIwngBDsDp" width="480" height="274" frameBorder="0" class="giphy-embed" allowFullScreen></iframe>

Ejemplo en [Stack Blitz](https://stackblitz.com/edit/angular-3xugow) 👨💻

## Resumen
La comunicación entre componentes es un contenido recurrente y me quise dar **el lujo** de explicar un tema que me apasiona con otro que me trae muy buenos recuerdos, **Angular** con **Dragon Ball Z**.
Hemos puesto sobre la mesa las formas más comunes y básicas de la comunicación entre componentes de distinto nivel jerárquico. En una siguiente entrada ahondaremos en mecanismos más complejos de comunicación para casos un tanto más específicos. Espero les sirva, saludos!

![goku](https://miro.medium.com/max/800/1*866pGDLtHjF-mZFjdr2AwA.jpeg)
