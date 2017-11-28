---
title: "How I created this website using Hugo and used a custom domain with github pages"
date: 2017-11-27T18:06:51+05:30
draft: false
---
* * *

Started up with blogging on Machine learning. Thought about using the Hugo static file generator. Here is code I used and the changes I made. Thought might be helpful to someone.

## Deploy the Website:

Go to the [quickstart](https://gohugo.io/getting-started/quick-start/) tutorial and follow through with the steps. The main steps are:

```bash
brew install hugo
# replace with your site name
hugo new site mlrumble
cd mlrumble;\
git init;\
git submodule add https://github.com/avianto/hugo-kiera.git themes/kiera
# create a new post
hugo new posts/my-first-post.md
# run the server with draft mode enabled
hugo server -D
# remove archetypes folder
rm -rf archetypes
```

Once done you can see your website up and running at `localhost:1313`

* * *

## Adding Social Buttons, Disqus IDs, or Google Analytics:

Next step is to change the *config.toml* file to this if you want to use the kiera theme.
This step is mainly a little theme specific:

```python
baseURL = "http://mlrumble.github.io"
languageCode = "en-us"
title = "MLRumble"
theme = "kiera"

pygmentsCodeFences = true

disqusShortname = "mlrumble" #Disqus shortname
googleAnalytics = "" #Google Analytics ID

[author]
    name = "MLRumble"       #Author name
    github = "mlrumble"     #Github username
    gitlab = ""     #Gitlab username
    linkedin = ""   #LinkedIn username
    facebook = ""   #Facebook username
    twitter = "mlrumble"    #Twitter username
    instagram = ""  #Instagram username

[params]
    tagline = "My ML Perambulations"
```

* * *

## Adding Pages:

if you want to add a page about for your website add *about.md* in the <kbd>content</kbd>kbd> directory

```md
+++

title = "About Hugo"
date = "2014-04-09"
menu = "main"
weight = "20"
meta = "false"
+++

Hugo is a static site engine written in Go.

It makes use of a variety of open source projects including:

* [Cobra](https://github.com/spf13/cobra)
* [Viper](https://github.com/spf13/viper)
* [J Walter Weatherman](https://github.com/spf13/jWalterWeatherman)
* [Cast](https://github.com/spf13/cast)

Learn more and contribute on [GitHub](https://github.com/gohugoio).

```
* * *

## Code change for Disqus:

I didn't like the show/hide comments functionality of this theme. So I embedded disqus as default. To do this :

Go to <kbd>themes/kiera/layouts/partials/disqus.html</kbd> and change it to:

```html
<section class="comments-block"></section>
<section id="disqus_thread"></section>
<script>

(function() { // DON'T EDIT BELOW THIS LINE
      if (window.location.hostname == "localhost")
          return;

      var disqus_loaded = false;
      var disqus_shortname = '{{ .Site.DisqusShortname }}';

      var d = document, s = d.createElement('script');
      s.src = "//" + disqus_shortname + ".disqus.com/embed.js";
      s.setAttribute('data-timestamp', +new Date());
      (d.head || d.body).appendChild(s);
})();

</script>

```
* * *

## Change for Copyright and bottom footer. Also enable the atom feed.

Go to <kbd>themes/kiera/layouts/partials/footer.html</kbd> and change it to:

```html

</main>
    <footer>
        <h6>{{ .Site.Copyright | markdownify }}
            Copyright © 2014-2017 - MLRumble | Rendered by <a href="https://gohugo.io" title="Hugo">Hugo</a> |
            <a href="{{.Site.BaseURL}}/index.xml">Subscribe RSS</a></h6>
    </footer>
</div>
<script src="{{ "js/scripts.js" | relURL }}"></script>
</body>

</html>

```
* * *

## Deployment to github pages:

Create two repositories in github:

* mlrumble/mlrumble
* mlrumble/mlrumble.github.io

go to the root folder <kbd>mlrumble</kbd> and execute the following commands just once:

``` bash
git init
git remote add origin git@github.com:mlrumble/mlrumble.git
git remote -v
git fetch origin
git branch -avv
git submodule add -b master git@github.com:mlrumble/mlrumble.github.io.git public
git submodule init
git submodule update --remote
git status
git add .
git commit -m "First commit to blog"
git push origin master  --force


cd public
git remote add origin git@github.com:mlrumble/mlrumble.github.io.git
git add .
git commit -m "First commit to blog posts"
git push origin master  --force

```

After this your whole codebase should be up on github repositories. Open up **mlrumble.github.io** and you should see your website up and running.

You might not want to write commit messages and everything while working with the blog. just create a file with all the add/commit/push commands and name it deploy.sh

Go back to the root folder <kbd>mlrumble</kbd> and create a file named deploy.sh with executable permissions(chmod). Put the following in deploy.sh:

``` bash
#!/bin/bash

echo -e "\033[0;32mDeploying updates to GitHub...\033[0m"

# Build the project.
#hugo # if using a theme, replace by `
hugo -t kiera

# Go To Public folder
cd public
# Add changes to git.
git add -A

# Commit changes.
msg="rebuilding site `date`"
if [ $# -eq 1 ]
  then msg="$1"
fi
git commit -m "$msg"

# Push source and build repos.
git push origin master

# Come Back
cd ..

```

Now whnever you want to push changes to your website on github pages just run `sh deploy.sh`

* * *

## Add a Custom Domain:

You will need to buy a domain name. Preferrably by [NameCheap](https://namecheap.com/) - Good provider. Go to Dashboard > Manage domain > Advanced DNS page:

{{< figure src="../images/dns.png" class="mid" caption="DNS Setup from https://charleskerr.com/2017/05/11/hosting-a-hugo-site-on-githubs-user-pages/" >}}

You will need three DNS records:

* A record:@:192.30.252.153
* A record:@:192.30.252.154
* CNAME record:www:mlrumble.github.io

After that run this on command line once in the root directory <kbd>mlrumble</kbd>: `echo "$mlrumble.com" > static/CNAME`

And finally deploy again using: `sh deploy.sh`.

* * *
## Add a Favicon:

Add `favicon.ico` file to <kbd>static</kbd> folder.

Go to <kbd>/themes/kiera/layouts/partials/header.html</kbd> and add the following line in the `<head>` tag:

`<link rel="shortcut icon" href="/favicon.ico?v=kPP08pKEXB">`

* * *
## Adding Google Analytics:

Get a tracking Id for the new site from Google Analytics Account and add this to the <kbd>/themes/kiera/layouts/partials/header.html</kbd> file in between the `<head>` tags:

```html
    <script async src="https://www.googletagmanager.com/gtag/js?id=YOUR_TRACKING_ID"></script>
    <script>
      window.dataLayer = window.dataLayer || [];
      function gtag(){dataLayer.push(arguments);}
      gtag('js', new Date());
      gtag('config', 'YOUR_TRACKING_ID');
    </script>
```

* * *

## Formatting Posts:
Check out the markdown that has been used to create this post [here](https://raw.githubusercontent.com/mlrumble/mlrumble/master/content/posts/Blog%20created.md)

Also check out the [Style Guide](https://avianto.github.io/hugo-kiera/posts/style-guide/) and [Image Style Guide](https://avianto.github.io/hugo-kiera/posts/image-content/)

To see the .md files for these above style guides go to folder: <kbd>/themes/kiera/exampleSite/content/posts</kbd>

* * *

Hope you liked this post. You are up and running now. Do comment if you get stuck or like this post. Enjoy!!!
