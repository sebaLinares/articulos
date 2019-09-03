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


