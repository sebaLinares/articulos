# Profesionaliza tus proyectos personales con CircleCi + Netlify + Github Parte 1

![pipeline](https://images.unsplash.com/photo-1559510981-10719ce4266a?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1350&q=80)

## Intro
Durante mi tiempo estudiando a programar - porque no sé si decir que he estudiado la "informática" como tal -, he hecho varios proyectos pequeños y otros un poco más ambiciosos - todos proyectos personales -, y a medida que ha pasado el tiempo, me he ido formando algo de criterio 🧠 acerca de qué cosa extra, que picante le puedo agregar a esos proyectos para enriquecerlos en tecnologías y a su vez enriquecer mis conocimientos. Siempre está el fantasma del *overkill* deambulando, pero cuando es por aprender y uno sabe sus tiempos y objetivos, solo hazlo. 
![do-it](https://media.giphy.com/media/CpgNjk2E54p7W/giphy.gif)

Para aquellos que aún no se suben al tren del CI/CD, aquí les va una lista comprensiva de pasos a seguir para conectar su preciada, empolvada y olvidada `Todo App` que ha estado en el olvido en su carpeta `/proyectos`. 

Trataré de hacer esto lo más corto posible y lo dividiré en 2 posts. El primero será acerca del **desarrollo continuo** y el segundo, todavía no estoy seguro si es **entrega continua** o **despliegue continuo**, **pero** los tecnisismos es tarea de cada uno, yo solo estoy aquí para mostrarles la puerta y ustedes tienen que ver de qué está hecha, cruzarla, etc. Vamos‼️
![go](https://media.giphy.com/media/LmrlNycquCmOPFM6WR/giphy.gif)

## Resultado Final Del Post 🔚
* Una app básica 👶🏽
* Un repositorio en Github que funcionará con una rama `master` y una `circleci`
* Una cuenta en CircleCI
* Un archivo `.yml` de configuración CircleCI que hará un test básico cada vez que hagamos `push` a `origin/circleci`.

## Integración Continua (CI) 🤝
Integración continua o Continous Integration - de ahí el CI -, según lo describe Microsoft es 
> ... el proceso de automatizar el build y el testing del código, cada vez que un miembro del equipo hace `commit` a un sistema de control de versionamiento (`git`). - [microsoft docs](https://docs.microsoft.com/en-us/azure/devops/learn/what-is-continuous-integration)

En palabras sencillas - para mi -, es que cada vez que hacemos `git push origin <rama_de_elección>` se desencadenen una serie de procesos que culminan con que nuestra app pase por un suite de testing. Todo esto para validar la integridad del código que se "sube" a un repositorio, ya sea Github, Gitlab, Bitbucket u otro, y poder decir: "Ok, el build que se originó de mi `commit` reciente paso por el testing sin romperse, quizás está listo para `producción`. 

### Primer paso 1️⃣
En realidad no voy a partir del primer paso, pero ustedes lo hacen con sus proyectos personales. Yo voy a crear una app con `yarn create react-app` y la voy a conectar con un repositorio en github. Así que supondré que todos ya hicieron eso para continuar.

### Segundo paso: Proteger la rama de github 2️⃣
Vamos a los `settings` de nuestro repositorio y ahí entramos en `branches`.
![setting-branch-1](https://media.giphy.com/media/gLd9ihsbKl2aZbISB8/giphy.gif)

Luego hacemos click en `Add rule`, en donde dice `Branch name pattern` escribimos `master`. Luego elejimos `Require status checks to pass before merging`, después hacemos click en `Require branch to be up to date before merging`, y finalmente click en `Include administrator` porque o si no de todas maneras podrán hacer merge si son administradores, lo cual rompe completamente el sentido de hacer estos `checks`.

Se darán cuenta que les dice un mensaje `Sorry, we coudn't find any status checks ...`. Este mensaje es porque todavía su repositorio no sabe que tiene disponible posibles "checks" que son los que crearemos en la configuración de `CircleCI`.
![setting-branch-2](https://media.giphy.com/media/eJdJ61YE0k3sim2uuB/giphy.gif)

Lo arreglamos inmediatamente 🏃🏽‍♂️.

### Tercer paso: CircleCI 3️⃣
Para poder tener integración continua en nuestro proyecto utilizaremos los servicios que nos presta el free tier de CircleCi - que es más que suficiente para nuestros pequeños proyectos personales, podemos encontrar la página en [este link](https://circleci.com), nos registramos con github y tendremos acceso automático a nuestros repositorios en el `dashboard` de CircleCI.

El archivo que crearemos tiene la extensión `.yml` -YAML Ain't Markup Language-, el cual nos permite, de una manera muy legible para humanos, escribir un archivo de configuración para nuestra `build` de CircleCI 👇🏽. 

1. Vamos a `ADD PROJECTS`
2. A la derecha del nombre de nuestro proyecto dirá `Set Up Project`. Hacemos click ahí, 
3. Elejimos `Node`
4. Más abajo nos aparecerá un template para el archivo de configuración básica. 
5. Copiamos esa configuración.
6. Creamos una carpeta, además una branch que se llame *circleci* y hacemos checkout a esa branch con `git checkout -b circleci`
7. Creamos un directorio con el nombre `.circleci/`
8. Adentro de esa carpeta creamos un archivo `config.yml`. 
9. Pegamos lo que copiaron antes 👇🏽.
![config-file-template](https://media.giphy.com/media/Z8k5OZgo8WSjiBIuly/giphy.gif)

Nuestro archivo de configuración debería verse así 👇🏽.

```yml
jobs:
  build:
    docker:
      # specify the version you desire here
      - image: circleci/node:7.10

      # Specify service dependencies here if necessary
      # CircleCI maintains a library of pre-built images
      # documented at https://circleci.com/docs/2.0/circleci-images/
      # - image: circleci/mongo:3.4.4

    working_directory: ~/repo

    steps:
      - checkout

      # Download and cache dependencies
      - restore_cache:
          keys:
            - v1-dependencies-{{ checksum "package.json" }}
            # fallback to using the latest cache if no exact match is found
            - v1-dependencies-

      - run: yarn install

      - save_cache:
          paths:
            - node_modules
          key: v1-dependencies-{{ checksum "package.json" }}

      # run tests!
      - run: yarn test
```

### Cuarto paso: Hacer el primer push 4️⃣
1. Agregamos los cambios al directorio *circleci* con `git add .circleci/`
2. Hacemos commit de esos cambios.
3. Hacemos `push` con `git push -u origin circleci`. Esto creará una rama remota con el mismo nombre que la rama local -origin/circleci.
4. Vamos a nuestro repositorio en Github.com
5. Vamos a encontrar una nueva pestaña que dice `Create pull request`. Si no nos aparece, creamos nosotros mismos el `pull request` 👇🏽.
![crear-pull-request](https://media.giphy.com/media/H1A305gMub8pBfeeNm/giphy.gif)
6. Si nos fijamos, el botón `Merge pull request` esta deshabilitado. Esto es porque ya protegimos `master` con `circleci:build`. Por lo tanto, mientras no pase eso, no podremos realizar el `merge` 😡.
![come-on](https://media.giphy.com/media/giQyAaSEkPsM25htum/giphy.gif)
7. ¡Hagamos debug! La build falló y hay que revisar por qué 🤷🏽‍♂️. 
![build-fails](https://media.giphy.com/media/JQG1Q2awvX3vUxsYdz/giphy.gif)

8. En la misma página de Github, a la derecha de la build que fallo, la que tiene la ❌, veremos un hyperlink `Details`, hagamos click. Esto nos lleva a nuestra cuenta de CircleCI, específicamente al `Job` que falló. Ahí dice claramento q espera que nuestra versión de node sea `>=8.10`. Aquí está el problema. Arreglemos 👷🏽‍♂️
![circleci-fails-node-version](https://raw.githubusercontent.com/sebaLinares/screenshots/master/blog-posts/ci-cd-blog/circleci-fails-node-version.jpg)
9. Vamos al archivo `config.yml` en nuestro repositorio local y cambiamos la línea 5 de código.

```yml
# image: circleci/node:7.10 <-- Change this
- image: circleci/node:lts # For this.
```

10. Guardamos el archivo, lo agregamos, hacemos `commit` y `push`  con `git add. && git commit -m 'fix(ci): change to node:lts' && git push`
11. Vayamos a nuestro repositorio Github y en la pestaña *Pull request*, debería ver que dice "(1)", ese es nuestro antiguo pull request, el que falló, aún está "vivo" 👻. Para que desaparezca tenemos que explicitamente hacer merge o cerrarlo, si no, estará ahí, recibiendo otros commits de algún push que hagamos en nuestro repositorio local
12. Ahora si la la `build` de CircleCI fue exitosa👇🏽
![build-success](https://media.giphy.com/media/MCivExf510LGhqLOzH/giphy.gif)
13. Finalmente hacemos click en `Merge` y luego podemos borrar el branch - podemos hacer lo mismo en nuestro repositorio local👇🏽.
![merge-pull-request](https://media.giphy.com/media/S667pS1XsodQjzpD05/giphy.gif)


Y ***voilá!*** dimos el primer paso en el laaargo camino de la integración continua 🥇. En el siguiente post utilizaremos Netlify como hosting para nuestra aplicación e implementaremos la **Entrega Continua** para que nuestro deploy se automatice, completando esta serie de posts 📝. Además les mostraré como hacer que nuestros `jobs` de circleci se hagan más rápido utilizando un `cache` de las dependencias, cambiando la configuración de su archivo `config.yml`.

👾 [Aquí les dejo mi Twitter, a veces posteo sobre desarrollo web](https://twitter.com/SLinaresL)

¡Espero les sirva, saludos!

![exito](https://statics.memondo.com/p/99/gifs/2012/05/GIF_113206_3bb8f57d5ca2453f8551a243a06a3faf_exito_origen_del_meme.mp4)

## Bonus: Github Flow
La forma en la que manejo mi control de versiones es más conocida como *Github Flow*, que va así:  

- Todo en `master` es deployable
- Para trabajar en algo nuevo crea una `branch`, desde master, con un nombre descriptivo - por ejemplo, *nuevo-menu* 
- Agrega todos los `commit` que quieras a tu nueva `branch` *nuevo-menu* y ve haciendo `push` esos cambios a una `branch` remota con el mismo nombre
- Cuando creas que está lista tu nueva `feature` abre un [pull request](https://help.github.com/es/github/collaborating-with-issues-and-pull-requests/about-pull-requests)
- Si trabajas en equipo, luego de que alguien revise tu `pull request`, puedes hacerle `merge` hacia master
- Una vez que se completa el `merge` debería haber un `deploy` inmediatamente

## Palabras clave
* Desarrollo continuo
* Entrega Continua
* Github Flow






