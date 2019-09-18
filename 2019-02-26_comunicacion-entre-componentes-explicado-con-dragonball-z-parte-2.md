# Comunicación entre componentes explicado con Dragon Ball Z — Angular 7 — Parte 2

![dbz](https://miro.medium.com/max/5120/1*5xyrwgnr1S_ftNsgTUUsbQ.jpeg)

¿Qué tal amigos? Esta es la segunda parte de la entrada anterior, con relación a la comunicación entre componentes en Angular 📞. Este post será probablemente un poco más corto y ahondaremos en otras formas de comunicación entre componentes. Así que sin más preámbulos … go❗️

>“Hay tres cosas que no puedo tolerar: Cobardía, malos peinados e insurrección militar. Es muy desafortunado que nuestro amigo Vegeta tenga esas tres cosas.” — Freezer

### Recapitulemos

En la entrada anterior tratamos 3 formas de comunicación diferentes para 3 tipos de relación entre componentes:

1. Comunicación 👨padre → hijo 👦 mediante `Input()` — y el Kame Hame Ha.
<iframe src="https://giphy.com/embed/NPGWVCyKOwMzNBZnW0" width="480" height="250" frameBorder="0" class="giphy-embed" allowFullScreen></iframe>

2. Comunicación 👦 hijo → padre👨 mediante `Output()` y `EventEmitter()` — y la Genkidama.
<iframe src="https://giphy.com/embed/1zl0R62f0kqUJOqICe" width="480" height="352" frameBorder="0" class="giphy-embed" allowFullScreen></iframe>

3. Comunicación entre componentes hermanos 👦 ←→👦 mediante `Input()` `Output()` y `EventEmitter()`
<iframe src="https://giphy.com/embed/dJMK0wsFfIwngBDsDp" width="480" height="274" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/dJMK0wsFfIwngBDsDp">via GIPHY</a></p>

Esas formas de comunicación pueden **resultar muy útiles** y seguir todas las recomendaciones y **buenas prácticas de Angular**, pero **no siempre son** suficientes para la idea que tenemos en mente o el problema del que necesitamos salir.
Para eso les traigo, en mi opinión, la forma más fácil y rápida, y la que uso en mis desarrollos.

## Comunicación entre dos componentes sin parentesco 👲 ← →👳

![goku gritando a vegeta](https://miro.medium.com/max/1000/1*gZUZ14o03phn8K-YCLMIUA.jpeg)

No era raro ver a Vegeta discutiendo con Goku, molesto 😠 por algo o evitándolo a toda costa. Si fuera por él, quizás ni dirigirle la palabra directamente. Imaginemos que a su vez, Goku también estaba molesto con Vegeta y ambos no querían hablarse directamente. ¿Cómo se comunicarían entonces estos dos grandes personajes de la serie? A través de Bulma. Este es el ejemplo que utilizaremos para explicar el concepto de la **comunicación a través de un servicio y el uso del BehaviorSubject**, los cuales explicaré brevemente a continuación.

“Un **servicio** es una categoría amplia que abarca cualquier valor, función o característica que necesita una aplicación. Un **servicio** es típicamente una clase con un propósito estrecho y bien definido.” — Definición del [sitio oficial de Angular](https://angular.io). El otro concepto que deben entender antes de continuar es el de un **BehaviorSubject**, que está dentro de otro concepto, más grande, llamado RxJS. No ahondaremos en lo que es **RxJS**, pero quédense con lo siguiente, es una librería que facilita el trabajo de manejar funciones asincrónicas ⏳, con uno o muchos eventos (algo como **promises** o **async/await**, en esteroides). Dentro de esta librería tenemos un tipo especial de [observable](https://angular.io/guide/observables) llamado **BehaviorSubject**, el cual, en simples palabras, permite emitir múltiples valores a uno o muchos [Observers](https://angular.io/guide/observables#defining-observers) (el que “observa” los valores emitidos por el “observable”). Vamos al ejemplo.

Cuando Goku quiera comunicarse con Vegeta — y viceversa, tendrán que ir a la casa de Bulma y comunicarle un mensaje ✉️ para Vegeta. Como a Bulma no le gusta ser entrometida, ella solo entregará el mensaje ✉️ cuando Vegeta se lo pida.

En este ejemplo tenemos:

1. Goku → Observer
2. Vegeta → Observer
3. CasaBulmaService → servicio
4. Bulma → BehaviorSubject (tipo de observable)

## Al código 👨‍💻

<script src="https://gist.github.com/sebaLinares/293e8ba95042066ea03391787a4e5e43.js"></script>

<script src="https://gist.github.com/sebaLinares/1725de1148acf95eb7bdcbb395da296f.js"></script>

<script src="https://gist.github.com/sebaLinares/40b0aca0bb02fec1de823f61ce11822f.js"></script>

## Representación visual 👀

## Para los que necesitan el paso a paso

Quiero explicar esto en detalle para los que no lo entienden bien con el código solamente, alguna vez también estuve en esa situación, así que, quiero que lean lo que a mí me hubiese gustado leer en su momento, por muy largo que sea. Los que ya entendieron, pueden saltarse este párrafo:

### Casa Bulma Service

Primero creamos el servicio, que este caso sería **CasaBulmaService**. Dentro de él tenemos los siguientes bloques de código.

En este bloque de código creamos de manera **privada** el BehaviorSubject que les mencioné antes. Este es un tipo de **observable** que irá almacenando el último mensaje que envíe Goku o Vegeta, **cualquiera**. Porque crearlo privado: Aquí hay una explicación, que yo aún no entiendo bien, pero es una buena práctica, mejor acostumbrase a hacerlo y en el camino aprender por qué.

`private bulma = new BehaviorSubject<string>('');`

En el siguiente bloque de código hacemos lo que recomienda el post y exponemos nuestro BehaviorSubject al mundo en la forma de un común y silvestre Observable. El signo $ es convención para declarar un **observable**, lo verán en muchos lugares. Ahora cuando queramos utilizar el **BehaviorSubject**, no lo haremos llamándolo directamente, si no que, a través de este **observable**.

`bulma$ = this.bulma.asObservable();`

El último bloque de código de nuestro CasaBulmaService, es la función enviar(), la cual llamaremos, **desde un componente**, cuando queramos enviar un mensaje. Básicamente, con el método next(), del BehaviorSubject, le decimos a este, que guarde el mensaje que le entregaremos, nada más.

```
enviar(mensaje){
  this.bulma.next(mensaje);
}
```

### GokuComponent y VegetaComponent

Ambos son esencialmente iguales, así que los explicaré como uno.
Primero que todo en el constructor es ambos componentes, debemos declarar el servicio **CasaBulmaService**, para poder utilizar sus propiedades y funciones.

```
constructor(){
  private casaBulmaService: CasaBulmaService
}
```

Luego podría llamarles la atención el `<input>` en el que hay un `#msg`. Esto es una [variable de referencia del template](https://angular.io/guide/template-syntax#template-reference-variables--var-), una forma de hacer una referencia a un elemento del DOM. ¿Para qué la usaremos? Para poder enviar el atributo.value de ese input en la función asociada al `<button></button>`. Finalmente el resultado de `msg.value` será lo que se haya escrito dentro del `<input>`.

```
<div class="form-group">
  <label>Mensaje para Vegeta:</label>
  <input class="form-control" #msg type="text">
</div>
<button
  class="btn btn-primary" type="button"
  (click)="enviarMensaje(msg.value)" // Aquí usamos el #msg
  >Enviar Mensaje
</button>
```

Continuando, **para enviar un mensaje** ✉️, tenemos una función `enviarMensaje(mensajeGoku)` o `enviarMensaje(mensajeVegeta)`, estas funciones se desencadenan en el `<button (click)="enviarMensaje(msg.value)"></button>` luego del `<input>`. El parámetro `mensajeGoku` o `mensajeVegeta` va a ser el `msg.value` que les enseñe en el párrafo anterior. Para eso sirven las **variable de referencia del template** — junto con otras cosas. ¿Qué hacemos con este mensaje? Enviarlo a nuestro **BehaviorSubject** que esta en **CasaBulmaService**. En nuestro servicio teníamos una función que almacenaba los mensajes llamada `enviar()`. Esta función espera un argumento, que será el mensaje ✉️ a almacenar.

```
enviarMensaje(mensajeGoku){
  this.casaBulmaService.enviar(mensajeGoku);
}
```
Finalmente, para ver el último mensaje que se envío, está la función `verMensaje()`. Aquí hay que poner mucha atención, porque es aquí donde aplica aquello que les mencioné, acerca de que no se debía llamar a un BehaviorSubject directamente, sino que, se debía hacer a través de un Observable. Si se fijan, llamo al servicio y al observable `bulma$` de dicho servicio. No basta con eso, para que un observable emita (muestre) que tiene almacenado, debemos suscribirnos a su respuesta. En este caso yo use esa respuesta y la asigne a una variable que luego la mostré en el template a través de una [interpolación](https://angular.io/guide/template-syntax#interpolation-).

Y para ahondar un poco más en la función, si se fijan, antes de suscribirme al observable `bulma$`, hay algo llamado `pipe`. ¿Qué es un pipe? Utilizamos `.pipe` cuando queremos utilizar diferentes operadores que nos facilita RxJS. Estos operadores son varios y tienen diferentes funciones, los invito a leer la [documentación](https://www.learnrxjs.io/operators/filtering/take.html) para ahondar más en ellos — son muy útiles. Bueno, pero este operator `take`, nos permite solo tomar el último valor que se le entrego al **Observable**, si no estuviera, cada vez que hacemos click en el botón de ver mensaje — de cualquiera de los componentes — veríamos que se muestra el mensaje en ambos componentes, no solo en el que se hizo click. Esto sucede porque, recuerdan el concepto que les mencione de “suscribirse”, bueno una vez que se suscriben, valga la redundancia, quedan suscritos. Es algo parecido a la listas de mail, si no nos desuscribimos, siempre seguiremos recibiendo los mails, aunque no queramos — aunque a veces ni con desuscribirnos basta. Con el `take` nos “desuscribimos” del bservable, luego de leer el mensaje que nos interesa. Pueden hacer la prueba, en este proyecto [StackBlitz](https://stackblitz.com/edit/behaviorsubject-medium) que les hice y borren el pipe dejando solo el subscribe, a ver qué sucede cuando hacen click en el botón “Ver mensaje de ..”, después del segundo mensaje enviado — el primero siempre va a funcionar, porque no hay nada antes de él.

```
verMensaje() {
  this.casaBulmaService.bulma$.pipe(take(1))
    .subscribe(mensaje => this.mensajeVegeta = mensaje);
}
// Y si quieren probar lo que les dije cambien la funcion por esto
// en el proyecto de StackBlitz
verMensaje() {
  this.casaBulmaService.bulma$.subscribe(mensaje => this.mensajeVegeta = mensaje);
}
```

### Conceptos que deberían ahondar

1. [Servicios de Angular](https://angular.io/guide/architecture-services<Paste>).
2. [Observables](https://angular.io/guide/observables).
3. [BehaviorSubject](https://www.learnrxjs.io/subjects/behaviorsubject.html).
4. [Operadores en RxJS](https://www.learnrxjs.io/operators/).

### Para concluir ...
Cuando desarrollemos una aplicación en **Angular**, tendremos muchos obstáculos y uno de los primeros que se nos presenta, es el de cómo hacer que X componente se comunique con Y componente, teniendo cada uno de estos muchos tipos de relaciones. El objetivo de esta serie de entradas (2), fue que, de manera simple, para desarrolladores de todos los niveles, aprendieran o refrescaran sus conocimientos en esta tarea. Creo que las analogías con series animadas o reales y sus personajes son una
tremenda herramienta, al momento de explicar un concepto o un tema. Así que me valí de eso para explicar estos conceptos tan utilizados en el quehacer del desarrollo con **Angular**, y pretendo seguir haciéndolo.

Espero sirva || haya servido,

Saludos!

![Goku-nube](https://miro.medium.com/max/1000/1*Bpud6kka2ZFNXuKfe1Icyw.gif)
