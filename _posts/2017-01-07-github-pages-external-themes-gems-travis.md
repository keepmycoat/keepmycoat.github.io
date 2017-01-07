---
title: Using External Jekyll Themes and Gems on Github Pages with Travis CI
---

I recently decided to start blogging (for real, this time). As someone from the developer community, I know that [Github Pages][1] is the [one-true way][2] to host your website.

Github Pages provides free hosting, git based deployment, support for custom domains and a lot of other nifty features. It uses [Jekyll][3], a static site generator which underwent a [massive overhaul][4] recently and now includes gem based theming and plugins support. Previously, you had to include all the assets of the theme and there were only few plugins supported by default at the time.

I was excited when I found out the [perfect theme][5] for my blog, but turns out Github Pages only supports a select few [themes][6] and [plugins][7]. I could still build the site on my machine and push it to Github, but that would defeat the purpose of using Jekyll in the first place.

I Googled (more like [Duck Duck Go'd][8]) around figured out that using a continuous integration system like [Travis CI][9] (which is free for Open Source) was the proper way to do it. I also found a [nice example][10] on how to proceed forward. I've outline what I did below.

### Create a new branch
Github Pages [builds from][11] the `master` branch for User or Organization Pages sites or the `gh-pages` branch for Project Pages sites. I created a new `jekyll` branch on [my personal repository][12] for the source. It also makes things easier if you change the default branch in the repository settings.
![Repository default branch]({{ site.url }}/assets/images/github-repository-default-branch.png)

### Get a Github API token
You will be using Travis CI to commit to the `default` or `gh-pages` branch. Using passwords is insecure to use for third party deployment, so Github provides [Personal Access Tokens][13] to authenticate. Create a token using [this link][14] and select the `public_repo` scope (or the `repo` scope if you are using a private repository). Make sure you keep the token safely or someone else might use it to authenticate as you on Github.

### Configure Travis
Go to [https://travis-ci.org/profile]() and enable your repository by toggling the switch. Add your Personal Access Token as an environment variable. The token is [stored encrypted][15] on their servers and only decrypted during builds.  You can choose any name for the environment variable which you will be using later (I used `GH_TOKEN`). I also selected the "Build only if .travis.yml is present" and "Build pushes" options to simply things. The former options prevents the deployed repository from being built (Jekyll does not copy that file to the `_site` directory) while the later runs the build process as soon as you push to Github.

![Travis repository settings]({{ site.url }}/assets/images/travis-repository-settings.png)

Travis is configured using a `.travis.yml` file on the repository. You can see my configuration [here][16].

{% highlight yaml %}
language: ruby
rvm:
  - 2.4.0
script: bundle exec jekyll build
branches:
  only:
    - jekyll
sudo: false
after_success:
  - cd _site
  - git init
  - git config user.name "Shritesh Bhattarai"
  - git config user.email "shritesh@shritesh.com"
  - git remote add upstream "https://$GH_TOKEN@github.com/shritesh/shritesh.github.io.git"
  - git fetch upstream
  - git reset upstream/master
  - git add -A .
  - git commit -m "[Build] ${TRAVIS_COMMIT}"
  - git push -q upstream HEAD:master
{% endhighlight %}

The configuration is easy to understand. I set the language as Ruby and used the version `2.4.0` for Jekyll. Travis will then execute the script `bundle exec jekyll build` which compiles the site to `_site` directory. I have also configured it to build only on the `jekyll` branch of the repository.

After it is successfully built, it changes into the `_site` directory, initializes an empty git repository, sets the git credentials to be me and adds an upstream repository using the token stored previously for authentication. It fetches the repository and stages the changes. The `TRAVIS_COMMIT` environment variable denotes the latest commit hash, which is used in the commit message. Then, it pushes it to the `master` [branch][17] on Github.

You could also test your site using `html-proofer` gem as outlined in Jekyll's [documentation on continuous integration][18].

Similarly, you can also use Travis to [deploy to another service][19], which I intend to do soon as Github Pages does not yet support HTTPS on custom domains.

### Push your Jekyll Site
Now push your site to Github and Travis should build and deploy your site.

![Travis build status]({{ site.url }}/assets/images/travis-build-status.png)

Congratulations, you can use any theme or gem on Github Pages now.

---
*Thank you for reading my first blog post. You can follow [@shritesh][20] on Twitter if you'd like.*

[1]: https://pages.github.com/
[2]: https://github.com/search?q=github.io
[3]: http://jekyllrb.com/
[4]: http://jekyllrb.com/news/2016/10/06/jekyll-3-3-is-here/
[5]: https://github.com/bertrandkeller/overkyll-jekyll-theme
[6]: https://pages.github.com/themes/
[7]: https://pages.github.com/versions/
[8]: https://duckduckgo.com
[9]: https://travis-ci.org/
[10]: https://github.com/steveklabnik/automatically_update_github_pages_with_travis_example
[11]: https://help.github.com/articles/user-organization-and-project-pages/
[12]: https://github.com/shritesh/shritesh.github.io/tree/jekyll
[13]: https://github.com/blog/1509-personal-api-tokens
[14]: https://github.com/settings/tokens
[15]: https://docs.travis-ci.com/user/environment-variables/#Defining-Variables-in-Repository-Settings
[16]: https://github.com/shritesh/shritesh.github.io/blob/jekyll/.travis.yml
[17]: https://github.com/shritesh/shritesh.github.io/tree/master
[18]: http://jekyllrb.com/docs/continuous-integration/
[19]: https://docs.travis-ci.com/user/deployment
[20]: https://twitter.com/shritesh
