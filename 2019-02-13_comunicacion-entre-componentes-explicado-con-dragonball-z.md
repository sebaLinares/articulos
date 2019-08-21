# Comunicación entre componentes, explicado con Dragon Ball Z— Angular 7 — Parte 1.

![goku-y-gohan](https://miro.medium.com/max/3840/1*mVzKRZkN3Sv2tXfYNknmEw.jpeg)

Por ahí escuché que si se lo puedes explicar a un niño de 5to básico, es porque lo entiendes. Y qué hace un niño de 5to básico, pues ver **Dragon Ball Z** — bueno, al menos yo lo hacía — . Así que me valdré de Goku y sus amigos para explicar este pequeño fragmento del quehacer de nuestro querido framework **Angular**.

> “Si no lo puedes explicar con simplicidad, es que no lo entiendes bien”. Albert Einstein.

## Contextualizando
¿Cómo hago para que **este** componente le envíe información a este **otro** componente? **Todos** (sí, todos) nos hacemos esa pregunta cuando estamos comenzando a usar **Angular**, algunos hasta mucho después del haber comenzado y, si bien utilizar un framework tan robusto como este trae muchas ventajas, también supone ciertos desafíos 💪. Este será un post en colaboración con **Angular Chile** en el que intentaré explicar, de una forma sencilla, el funcionamiento de la comunicación e interacción entre componentes de distintos niveles. Espero les quede más claro una vez leído, comencemos!

## Comunicación Padre 👨 ← → Hijo 👦
![goku-gohan-nube](https://miro.medium.com/max/1000/1*KnatQV0IaboioH2EZjj2Rg.jpeg)


No, no hablaremos de psicología y de cómo un padre debería comunicarse con su hijo, hablaremos de la comunicación entre un componente padre 👨hacia un componente hijo 👦, y viceversa. Primero que todo: ¿Qué hace que un componente sea padre/hijo de otro?
Al igual que la relación de Gohan con Goku — hijo y padre — , un componente que se instancia adentro de otro componente, podemos llamarlo componente hijo. Esta relación de “parentesco” les dará la habilidad de comunicarse y compartir propiedades o eventos siguiendo las buenas prácticas de Angular en relación a la separación de intereses.
