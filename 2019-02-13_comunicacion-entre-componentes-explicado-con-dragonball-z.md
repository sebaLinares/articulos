# Comunicación entre componentes, explicado con Dragon Ball Z— Angular 7 — Parte 1.

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

> goku.component.ts
```ts
@Component({
  selector: 'app-goku',
  template: `
  <div class="container">
      <div class="row">
        <div class="col-sm-8 offset-sm-2">
            <h1 class="mx-4">Goku Component</h1>
            <input 
              class="mx-2 form-control"
              #gokuInput
              (keyup)="keyUp(gokuInput.value)">
            <div class="col-sm-12">
              <app-gohan [kamehamehaGohan]="kamehamehaGoku"></app-gohan>
            </div>
        </div>
          
      </div>
    </div>
  `,
  styleUrls: ['./goku.component.css']
})
export class GokuComponent {

  // Se declara e inicia en el componente padre para luego comunicarlo al componente hijo
  kamehamehaGoku: string = '';

  // con cada tecla apretada se activa esta funcion.
  keyUp(letra: string) {
    this.kamehamehaGoku = letra;
  }

  constructor() { }

}
```

> gohan.component.ts
```ts
import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-gohan',
  template: `
    <div class="gohanComp">
      <h3>Gohan Comp</h3>
      <p>Gohan ahora puede usar el <p>
      <h1>{{kamehamehaGohan}}</h1>
    </div>
  `,
  styleUrls: ['./gohan.component.css']
})
export class GohanComponent {

  // Se recibe desde el padre. Ahora se puede utilizar en este componente
  // Cualquier cambio en esta variable en 'app-goku', se reflejará acá
  @Input() kamehamehaGohan: string;

  constructor() { }

}
```

Ahora Gohan puede utilizar el `kamehameha` cuando quiera, ya que su padre se lo ha enseñado (comunicado) 👌.

![kamehameha](https://giphy.com/gifs/NPGWVCyKOwMzNBZnW0?utm_source=iframe&utm_medium=embed&utm_campaign=Embeds&utm_term=https%3A%2F%2Fcdn.embedly.com%2Fwidgets%2Fmedia.html%3Fsrc%3Dhttps%3A%2F%2Fgiphy.com%2Fembed%2FNPGWVCyKOwMzNBZnW0%2Ftwitter%2Fiframe&%3Burl=https%3A%2F%2Fgiphy.com%2Fgifs%2FNPGWVCyKOwMzNBZnW0&%3Bimage=https%3A%2F%2Fmedia.giphy.com%2Fmedia%2FNPGWVCyKOwMzNBZnW0%2Fgiphy.gif&%3Bkey=a19fcc184b9711e1b4764040d3dc5c07&%3Btype=text%2Fhtml&%3Bschema=giphy)

Ejemplo en [Stack Blitz](https://stackblitz.com/edit/goku-gohan) 👨💻

## Comunicación hijo a padre utilizando '@Output()' y '@EventEmitter()' 👈
Tal como les mencioné, un componente padre — controlador — tiene la **lógica** de una función, pero es su componente hijo — presentador — quien debe enviar un aviso, para desencadenarla. Volvamos a 5to básico y a Dragon Ball Z 📺.
Se acuerdan de la Genkidama? Bueno, esta técnica que salvo a la tierra 🌎 en incontables oportunidades solo la realizaba Gokú. Él, nuevamente, es nuestro **componente controlador**, tiene la lógica de la genkidama(){}. Como ya sabrán todos — porque los que no, ya hicieron click en el link y ahora saben lo que es una genkidama — para realiza está técnica se necesita la energía de todos los seres vivientes del universo — o todos los que quieran ayudar — . Para términos demostrativos, imaginemos que Gohan, nuevamente nuestro componente presentador, es el último en entregar su energía, con la cual se completaría la genkidama para que nuestro héroe Gokú, pueda hacer uso de ella y derrotar a <ingrese su villano favorito> 👹. Vamos con el código.

> gohan-genkidama.component.ts
```ts
import { Component, Input, Output, EventEmitter  } from '@angular/core';

@Component({
	selector: 'gohan',
	template: `
		<div class="gohanComp">
		<button 
			type="button" 
			(click)="genkidamaAlerta(true)"
			>Hacer genkidama</button>
		</div>
	`,
	styleUrls: ['./gohan.component.css']
})
export class GohanComponent {
	@Output() energiaGohan = new EventEmitter<boolean>();
	constructor() { }
        
	genkidamaAlerta(msg: boolean){
		this.energiaGohan.emit(msg)
		console.log(msg);
	}
      
}
```

> goku-genkidama.component.ts
```ts
import { Component  } from '@angular/core';

@Component({
    selector: 'goku',
    template: `
      <div class="container">
   		  <div class="row">
          <div class="col-sm-8 offset-sm-2">
        		<gohan
							(energiaGohan)="genkidamaLista("vent)">
						</gohan>
						<h1>Goku Component</h1>
						<img src="{{genkidamaImg}}" alt="">
					</div>
				</div>
			</div>
		`,
		styleUrls: ['./goku.component.css']
})
export class GokuComponent {
	genkidama: boolean = false;
	genkidamaImg: string;

	constructor() { }

	genkidamaLista(confirmation: boolean){
	this.genkidama = confirmation;
	console.log(this.genkidama);

	//hacer genkidama si es true
	if(this.genkidama){
		this.genkidamaImg = 'https://vignette.wikia.nocookie.net/dragonball/images/6/6c/Goku_lanzando_la_Genkidama.png/revision/latest?cb=20130105194140&path-prefix=es'
	}
}
```

Finalmente con la energía de `GohanComponent`, nuestro `GokuComponent` puede terminar la `genkidama` y está listo para salvar nuevamente el planeta 👏.
![comunicacion hijo a padre
gif](https://giphy.com/gifs/1zl0R62f0kqUJOqICe?utm_source=iframe&utm_medium=embed&utm_campaign=Embeds&utm_term=https%3A%2F%2Fcdn.embedly.com%2Fwidgets%2Fmedia.html%3Fsrc%3Dhttps%3A%2F%2Fgiphy.com%2Fembed%2F1zl0R62f0kqUJOqICe%2Ftwitter%2Fiframe&%3Burl=https%3A%2F%2Fgiphy.com%2Fgifs%2F1zl0R62f0kqUJOqICe&%3Bimage=https%3A%2F%2Fmedia.giphy.com%2Fmedia%2F1zl0R62f0kqUJOqICe%2Fgiphy.gif&%3Bkey=a19fcc184b9711e1b4764040d3dc5c07&%3Btype=text%2Fhtml&%3Bschema=giphy)

Ejemplo en [Stack Blitz](https://stackblitz.com/edit/gohan-goku) 👨💻
