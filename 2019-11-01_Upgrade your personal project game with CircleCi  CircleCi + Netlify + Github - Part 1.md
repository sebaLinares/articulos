# Upgrading Your Personal Project Game With CircleCi  CircleCi + Netlify + Github - Part 1

![pipeline](https://images.unsplash.com/photo-1559510981-10719ce4266a?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1350&q=80)

## Intro 
During the time I've been  learning to program - because I don't know if I can say I've studied *computer science* as a whole-, I've done many little projects and other more ambitious - all just for learning purposes-, and as the time passes, I started building some criteria 🧠 about what's else, what spice can I add to those projects to enrich them in technologies. There is always the term "overkill" lurking around, but when it's just for learning purposes and you can set terms and goals ... just do it.
![do-it](https://media.giphy.com/media/CpgNjk2E54p7W/giphy.gif)

For those who haven't hopped in the CI/CD train, here I bring you a comprehensive set of steps you can take to connect your beautiful, dusty and forgotten, `Todo App`, that's been wandering for many months in your `/projects` directory. 

I'll try to make this as short as possible, but it will be divided into 2 posts. This first post will be about *continuous delivery* and the second, I'm still not sure if it is **continuous delivery** or **continuous deployment**, BUT the technical terminology is the homework, I'm just here to show you the door, you're the one who crosses it and determines what is made of. Off we go ‼️.

![go](https://media.giphy.com/media/LmrlNycquCmOPFM6WR/giphy.gif)

## End result 🔚
* Basic app 👶🏽
* Github repository with two branches: `master` and `circleci`
* A CircleCI account
* A `.yml` file that serves as configuration for CircleCI. It will trigger your test suite every you push content to the `origin/circleci` you were working on.

## Continuous Integration (CI) 🤝
As Microsoft describes it, *continuous integration* is:
> ... the process of automating the build and testing of code every time a team member commits changes to version control. - [microsoft docs](https://docs.microsoft.com/en-us/azure/devops/learn/what-is-continuous-integration)

In simple words, for me, CI means that every time we make `git push`, it triggers a set of tasks, previously configured, that culminate with our app to go through multiple tests and/or builds. All this to validate the integrity of the code that you `push` to your remote repositories, either to Github, Gitlab, Bitbucket or other remote version control system of your preference, so at this point we are able to say: *Ok, the build that originated with the changes on this last commit went through the testing suite without breaking, maybe this is ready for **production**

### First Step 1️⃣
Actually, I'm not starting from the first stem, but you do it on your computers. I'm creating a new app with `yarn create react-app` and connecting it to a Github repository. From this point, I'll assume you've all done that.

### Second step: Protect master branch in Github 2️⃣
On the main page of our Github repository, let's go `settings` and then `branches`.
![setting-branch-1](https://media.giphy.com/media/gLd9ihsbKl2aZbISB8/giphy.gif)

Then we click in `Add Rule`, in `Branch name pattern` we write *master*. Then click in `Require status checks to pass before merging`, `Require branch to be up to date before merging`, finally let's click in `Include administrators`. The last click ensures that **everyone** has to respect the *checks* you set to the branch you are protecting, which includes administrators. If we do not check it, we will still be able to merge our *feature branch* with *master*. 

As you can see, the options panel displays is a message that says `Sorry, we couldn't find any status checks ...`. This message appears because our repository doesn't know about the possible *checks* that we will create in the **CircleCI** config file. 
![setting-branch-2](https://media.giphy.com/media/eJdJ61YE0k3sim2uuB/giphy.gif)

Let add this file immediately 🏃🏽‍♂️.

### Third step: CircleCI 3️⃣
To be able to have continuous integration in our project we'll use the services that CircleCI's free tier delivers me - which by the way is more than enough for our small projects. We can find the website in [this link](https://circleci.com). We register with Github and we'll have access yo all our repositories in the **dashboard**.

The file we are creating has a `.yml` extension -YAML Ain't Markup Language-, which gives us the possibility of setting the steps, for a good configuration file, in a human legible way 👇🏽.

1. Go to `ADD PROJECTS`
2. To the right of your project, it will say `Set Up Project`. Click here
3. Choose `Node`
4. Scroll down the page and we will see a configuration file template. Copy it
5. In our local repository, create and checkout to a new branch with the name of the feature you are adding to it, in this case, we will create one named `circleci` --> `git checkout -b circleci`
6. Create a directory named `.circleci`.
7. Inside that directory create a file named `config.yml`
8. Paste the configuration file template copied before👇🏽
![config-file-template](https://media.giphy.com/media/Z8k5OZgo8WSjiBIuly/giphy.gif)

Our configuration file should look like this.

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

### Forth Step: Do The First Push 4️⃣
1. Let's stage our changes to the *circleci* directory with `git add .circleci/`
2. Commit the changes
3. We Push them to our remote with `git push -u origin circleci`. This will generate a new remote branch with the same name as the local one - origin/circleci
4. Go to our Github repository
5. We will find a green button that says `Create pull request`. If it doesn't appear, we create the pull request ourself👇🏽
![crear-pull-request](https://media.giphy.com/media/H1A305gMub8pBfeeNm/giphy.gif)
6. Notice that the `Merge pull request button` is disabled. This is because we **protected** master with the `circleci: build` check. Thus, meanwhile the check is not approved, we won't be able to merge 😡.
![come-on](https://media.giphy.com/media/giQyAaSEkPsM25htum/giphy.gif)

7. Let's debug! The build failed and we need to check **why** 🤷🏽‍♂️. 
![build-fails](https://media.giphy.com/media/JQG1Q2awvX3vUxsYdz/giphy.gif)
8. In the same Github page, next to the build that fails, the one with the ❌, we see a hyperlink `Details`, click it. That takes us to our CircleCI account, specifically to the 'Job' that failed. There it clearly says that we need a Node version that is `>=8.10`. There it is! Let's fix it 👷🏽‍♂️.
![circleci-fails-node-version](https://raw.githubusercontent.com/sebaLinares/screenshots/master/blog-posts/ci-cd-blog/circleci-fails-node-version.jpg)
9. Go to the `config.yml` file in our local repository and on line 5 change the version of your node image.

```yml
# image: circleci/node:7.10 <-- Change this
- image: circleci/node:lts # For this.
```

10. We save the file, stage our changes and push them to Github with `git add. && git commit -m 'fix(ci): change to node:lts' && git push`
11. Head to our Github repository and in the *Pull request* tab we can see there is a "(1)", that's our old pull request, it's still alive 👻. For it to disappear we have to explicitly close it or merge it, if not, it will be there, receiving every other commit you push to the branch we are trying to merge with, in this case `origin/circleci`
12. Now the build check passes 👏🏽
![build-success](https://media.giphy.com/media/MCivExf510LGhqLOzH/giphy.gif)
13. Finally, click `Merge` and then we can delete the branch from your Github - then we can do the same in our local repository. 
![merge-pull-request](https://media.giphy.com/media/S667pS1XsodQjzpD05/giphy.gif)

And ***voilà*** we just made our first step in the looong way of continuous integration 🏆. In the next post we'll use Netlify as a hosting for this app and I'll implement **Continuous Delivery** with a new configuration file and some Netlify spices, so the deploy process is automatized, plus a way to make the `build` process faster using the `cache` commands that CircleCI has available for us.

👾 [Here is my Twitter account, sometimes I post about web development](https://twitter.com/SLinaresL)

I hope it's useful, Chao!

![exito](https://statics.memondo.com/p/99/gifs/2012/05/GIF_113206_3bb8f57d5ca2453f8551a243a06a3faf_exito_origen_del_meme.mp4)

## Bonus: Github Flow
The way I manage my version control system is known as *Github Flow*, and it goes like this.

- Everything in `master` is deployable
- To work on something new, create a descriptively named branch off of `master` (ie: `new-circleci`)
- Commit to that branch locally and regularly push your work to the same-named branch on the server
- When you need feedback or help, or you think the branch is ready for merging, open a **pull request**
- After someone else has reviewed and signed off on the feature, you can merge it into master
- Once it is merged and pushed to `master`, you can and should deploy immediately

## Keywords
* Continuous Development
* Continuous Delivery
* Github Flow





