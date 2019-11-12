# Profesionaliza tus proyectos personales con CircleCI + Netlify + Github Parte 2

Está es la continuación de mi post [post anterior](https://dev.to/sebalinares/upgrading-your-personal-project-game-with-circleci-circleci-netlify-github-part-1-eng-5bp2). Esta vez, concluiremos esta serie con la implementación del CD en CI/CD - Continuous Delivery. 

## Resultado 🔚
* Un proyecto alojado en Netlify (free tier)
* Un nuevo archivo de configuración CircleCI `config.yml`
* Un flujo de trabajo en CircleCI que nos permitirá hacer *deploy* cada vez que hagamos *merge* a un pull request de nuestra `master branch`. 

## Continuous Delivery (CD) 🤝
En esta edición no recurriré a la documentación de Microsoft, si no que a la de [continuousdelivery.com](www.continuousdelivery.com), que dice: 
> Continuous Delivery es la habilidad de obtener los cambios de todo tipo, incluyendo las nuevas características, cambios de configuración, bug fixes y experimentos, a producción o a las manos de los usuarios, de una manera segura, rápida y sostenible.

Una definición bastante completa. En el último post, hablamos acerca de la integración. Una instancia para un testing completo - nos gustaría -, el último checkeo para estar seguros que los cambios que hicimos a nuestra aplicación no romperán el producto final. Ahora - en el continuous delivery -, es tiempo de asegurar que nuestro usuario final reciba la aplicación de la manera más rápida e integra🏃‍♂️.
![like-a-boss](https://media.giphy.com/media/l0IykI5OLMhjtnB60/giphy.gif)

## Primer paso 1️⃣: Crear una cuenta en Netlify
1. Entramos [este link](https://app.netlify.com/signup/email) y creamos una cuenta.
2. Una vez que terminemos de registrarnos, seremos redirigidos a nuestro panel de control, el cual estará vacío. Hacemos click en *New site from Git*
![new-site](https://raw.githubusercontent.com/sebaLinares/screenshots/master/blog-posts/ci-cd-blog/new-site.jpg)  
3. Click en Github e ingresamos nuestras credenciales 🔐  
![github-credentials](https://raw.githubusercontent.com/sebaLinares/screenshots/master/blog-posts/ci-cd-blog/github-credentials.jpg)  
 
4. Aparecerá una solicitud de instalación 🔧 que necesita hacerse en Github 
5. Tenemos dos opciones aquí: permitir que Netlify tenga acceso a todos nuestros proyectos o solamente a los que escojamos. Elegiremos la última y solo permitiremos que Netlify vea la aplicación que creamos en el último post.  
![install-netlify](https://raw.githubusercontent.com/sebaLinares/screenshots/master/blog-posts/ci-cd-blog/install-netlify.jpg)  

6. Hacemos click en `Install` e ingresamos nuestra clave 🔑 .
7. En la ventana siguiente, hacemos click nuevamente en el nombre de nuestro proyecto de Github
8. Ahora veremos un formulario donde podemos configurar nuestras *build options* para el proyecto. Lo dejaremos como está, porque así tal cual es como lo necesitamos para los siguientes pasos.
![deploy-settings.jpg](https://raw.githubusercontent.com/sebaLinares/screenshots/master/blog-posts/ci-cd-blog/deploy-settings.jpg)  
  
9. Hagamos click en *Deploy Site*

Si están siguiendo esta serie de posts, todo debería funcional y en un par de segundos su proyecto deberá haberse generado. Abajo del nombre de su proyecto, debería haber un link con una extensión `.netlify.com`. Si le hacemos click seremos redirigidos a nuestro nuevo sitio web!
![awesome](https://media.giphy.com/media/3ohzdIuqJoo8QdKlnW/giphy.gif)

Si el sitio no fue creado 🙅🏽‍♂️, sugiero estos posibles escenarios.

* Su proyecto fue creado con NPM y en el 8vo paso, lo configuramos para usarse con `yarn build`. Solo tenemos que ir a `settings > build & deploy > edit settings`. Ahí podremos modificar nuestro `build command` para usar NPM.
* Quizás a Netlify le dimos permisos para otro repositorio. Para revisar que repositorio está conectado con nuestra cuenta Netlify, vayamos a `setting` y abajo de la url, hacemos click donde dice `Github`. Ese link nos redireccionara al repositorio en conexión. 

## Agregar un build Hook a nuestra configuración de Netlify

En simples palabras, el build hook **es un endpoint**. Si enviamos un `POST` request vacío, desencadenaríamos el proceso de `build` + `deploy` de la rama master de nuestro repositorio.
![unleash-the-beast-gif](https://media.giphy.com/media/5xaOcLE3ZnWJhT2IYCY/giphy.gif)

1. Vayamos a `settings > build & deploy` y bajemos hasta `build hooks`.  
![build-hook-1](https://raw.githubusercontent.com/sebaLinares/screenshots/master/blog-posts/ci-cd-blog/build-hook-1.jpg)  
  
2. Hacemos click en `Add build Hook`  
![build-hook-2](https://raw.githubusercontent.com/sebaLinares/screenshots/master/blog-posts/ci-cd-blog/build-hook-2.jpg)  
 
3. Le asignamos un nombre descriptivo y guardamos.
4. Copíamos la url que se generó. Peguémosla en algún lugar porque la necesitaremos más tarde.
![build-hook-end](https://raw.githubusercontent.com/sebaLinares/screenshots/master/blog-posts/ci-cd-blog/build-hook-end.jpg) 


## Configurar una environment variable en CircleCI
Ya tenemos una url que sirve como un endpoint para desencadenar nuestro *build + deploy*. Ahora vamos a usar eso en nuestro archivo de configuración de CircleCI `config.yml`. Pero como buenos desarrolladores que somos, me imagino que no queremos que esa `url` quedé pública en nuestro repositorio de Github 🤔 - cualquiera podría gatillar un deploy simplemente enviando un POST a esa url. Para esto, tenemos variables de ambiente que los amables trabajadores de CircleCi nos han provisto.

1. Vayamos a nuestra app de CircleCI
2. Hacemos click en `Jobs`
3. Hagamos click en el ícono de configuración que está al lado del nombre de nuestro proyecto  
![job-settings](https://raw.githubusercontent.com/sebaLinares/screenshots/master/blog-posts/ci-cd-blog/job-settings.jpg)  

4. Nos encontramos dentro de la configuración específica para ese `Job`. Hacemos click donde dice `Environment Variables`
5. Ahora click en `Add Variable`  
![environment-variables-circleci](https://raw.githubusercontent.com/sebaLinares/screenshots/master/blog-posts/ci-cd-blog/environment-variables-circleci.jpg) 
6. Le daremos un nombre descriptivo - `awesome_endpoint` -, y en el `value` pegamos el endpoint que guardamos anteriormente - la url del build hook de netlify
7. Hagamos click en `Add Variable` nuevamente
![add-environment-variable](https://raw.githubusercontent.com/sebaLinares/screenshots/master/blog-posts/ci-cd-blog/add-environment-variable.jpg) 


Casi listos, solo un paso más!

## Cambiar la configuración de CircleCI 
En nuestro último archivo `config.yml` solo ejecutamos un set de test. Ahora el objectivo es testear solo en nuestra `feature branch` y únicamente hacer `deploy` cuando hacemos *merge* de una *pull request* hacia nuestra rama master.

PRIMERO: Asegurémonos de estar en la `feature branch`, no en la `master` 🙏🏽. Si vemos el archivo `config.yml`, veremos la palabra `jobs`. Abajo de esto, solo veremos un `job`, `build`. Ahora agregaremos un segundo job, lo llamaremos `deploy`

En este `job` especificaremos en qué máquina queremos trabajar. En este caso elijamos una `macos`. Luego recuerdan la url que nos dieron en nuestro `build hook` 🧠, la usaremos nuevamente. 

Su nuevo `job` debería verse así.

```yml
deploy:
    machine:
      macos:
        xcode: '9.0'
    steps:
      - run:
          command: curl -X POST -d {} ${awesome_endpoint}
```

Cómo pueden imaginar, detrás de `${awesome_endpoint}` se esconde nuestra variable de ambiente con el endpoint secreto que gatilla el *build + deploy* del proyecto. 

Ahora tenemos dos `jobs`, build & deploy. Como los unimos? Con `workflows`. Podemos agendar los `jobs` que queremos que se gatillen ante ciertas condiciones que previamente configuraremos. Más información [aquí en la documentación](https://circleci.com/docs/2.0/workflows/).

```yml
workflows:
  version: 2
  build:
    jobs:
      - build:
          filters:
            branches:
              ignore: master
      - deploy:
          filters:
            branches:
              only: master
```

Primero, declaramos la palabra clave `workflows`. Luego, escribimos cada `job` que queremos ejecutar y filtramos cada uno por `branches`. El `build` job ignora la `master branch`, por lo tanto se ejecuta en cualquier otra rama. Por otro lado, el job `deploy` estipula que se ejecutará **solo** en la rama `master`. Tal como lo hablamos anteriormente, estamos pensando en hacer el testing en la `feature branch` y luego solo hacer `deploy` en `master`.

Nuestro archivo final `config.yml` debería terminar viéndose así:

```yml
version: 2
jobs:
  build:
    docker:
      - image: circleci/node:lts
    working_directory: ~/repo

    steps:
     - checkout
     - restore_cache:
          keys:
            - v1-dependencies-{{ checksum "package.json" }}
            - v1-dependencies-

      - run: yarn install

      - save_cache:
          paths:
            - node_modules
          key: v1-dependencies-{{ checksum "package.json" }}

      - run: yarn test

  deploy:
    machine:
      macos:
        xcode: '9.0'
    steps:
      - run:
          command: curl -X POST -d {} ${awesome_endpoint}


workflows:
  version: 2
  build:
    jobs:
      - build:
          filters:
            branches:
              ignore: master
      - deploy:
          filters:
            branches:
              only: master

```

## Hacer commit de los cambios y push a la feature branch 
El único cambio que hicimos en el código del repositorio fue en el archivo `config.yml`. Lo agregamos y hacemos push a nuestro remoto con `git add .circleci/config.yml && git commit -m 'circleci: add deploy job and workflow' && git push origin <branch_name>`

Creamos un pull request como vimos en el post anterior.

### Ahora se ven los resultados de todo lo que hemos hecho en los últimos dos posts. 
Primero, tenemos que esperar que los *checks* se completen. Específicamente el job `build` que es el que configuramos en nuestra `master branch` como protección. Si no se completa exitosamente, no podremos hacer **merge*
![github-merging](https://raw.githubusercontent.com/sebaLinares/screenshots/master/blog-posts/ci-cd-blog/github-merging.jpg)  

Mientras esperamos, en CircleCI nuestro `build` esta corriendo   
![feature-branch-circleci-job-success](https://raw.githubusercontent.com/sebaLinares/screenshots/master/blog-posts/ci-cd-blog/feature-branch-circleci-job-success.jpg)  

Luego de haber hecho *merge* del pull request, el job `deploy` se ejecuta en nuestra `master branch`
![master-branch-circleci-build-running](https://raw.githubusercontent.com/sebaLinares/screenshots/master/blog-posts/ci-cd-blog/master-branch-circleci-build-running.jpg)  

Cuando se completa, se gatilla el `build hook` de Netlify - el que declaramos en la variable de ambiente de CircleCI. Esto gatilla un build + deploy en nuestro sitio de Netlify - el con la `.netlify.com` extension.
![netlify-building-in-process](https://raw.githubusercontent.com/sebaLinares/screenshots/master/blog-posts/ci-cd-blog/netlify-building-in-process.jpg)  

Finalmente, luego de que la *build* de Netlify es exitosa, nuestro sitio web está listo!
![netlify-deploy-success](https://raw.githubusercontent.com/sebaLinares/screenshots/master/blog-posts/ci-cd-blog/netlify-deploy-success.jpg)  

**DONE**.

![yes!](https://media.giphy.com/media/DffShiJ47fPqM/giphy.gif)


## Últimas palabras
Esto concluye nuestro viaje. Ahora son los modestos dueños de un proyecto que hace testing y deploy automático. Siéntanse orgullosos y pueden hacerle el `overkill` que quieran con lo aprendido, hasta a sus proyecto más sencillos. Crean en sus proyectos, son geniales.

Saludos!

![self-claps](https://media.giphy.com/media/gdkVL0ljDHbNcXQOZD/giphy.gif)

## Further information
La [documentación de CircleCI](https://circleci.com/docs/) puede darle sun mayor entendimiento de como usar la plataforma.
