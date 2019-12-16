# git use cases explained w/ images

I don't have much working experience, but one thing I realized is that internet community is kind of a bubble. What you think is a well known subject because it talked every single day on social media, it's not on daily basis at work. One of the things I thought, before I entered the programming working force, was that everyone was a git expert and that it wasn't even worth talking about it, but how mistaken was I 🤷🏽‍♂️. So here we go, 4 situations explained with images to fill those little cracks in your git knowledge.

One git command I'll use within this post is `git log --oneline --branches --remotes --graph` I will reffer to it as `git log awesome`. This will show you the short version of the commit, of every branch and remote branches + those lines and commit asterisks for visual queue. Remember it, and use it -for those projects with a large number of commits I add `--max-count 10` so I get only the last 10 commits 🏆.

For sake of the explanations, the characters of this story are: Developer #1: makes commits in the `learning` directory **- LD = Learning Developer**. Developer #2: commits in the `projects` directory **- PD = Projects Developer**. 

1. Two developers trying to push changes to the same line of the same file.

![learning-1](https://raw.githubusercontent.com/sebaLinares/screenshots/master/blog-posts/git/learning-1.png)![learning-1](https://raw.githubusercontent.com/sebaLinares/screenshots/master/blog-posts/git/projects-1.png)

The problem here goes for the second dev that tries to push. In this case it is PD. He will find this when he tries `git push`

![merge-conflict](https://raw.githubusercontent.com/sebaLinares/screenshots/master/blog-posts/git/merge-conflict.png)

The problem: PD wanted to change line 3 which was already changed and on origin/master. Thus, merge conflict. 

So what do we do?

First, you always need to read warnings git provides you. This one in particular tells you to do a git pull. **Personally** I never use pull. As I'm a typing fanatic I like to type as much as I can, lol, so I always fetch and then merge - `git pull === git fetch && git merge`. After that I use the `git log awesome` I told you at the beggining.

![merge-conflict](https://raw.githubusercontent.com/sebaLinares/screenshots/master/blog-posts/git/fetch-msg.png)

This shows us that beautiful graph that when you start understanding your like will git much easier - see what I did there? GIT much easier 🤦🏽‍♂️.

I'll translate. Each `*` is a commit and each commit has a `SHA-1` - e.g `eb2f013`. This tells you that you - **HEAD** - are standing in `master` branch and you have the commit with the `65d57d6` sha-1, BUT that the **history** of this branch, diverges from the remote upstream branch of your local `master` branch - that is `origin/master`. Imagine that each one of those `|` is a path, can you see two paths? One points to `origin/master` and the other to `master`. 

# Tip number

