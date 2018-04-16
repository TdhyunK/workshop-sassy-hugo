# Getting Started with Hugo
Today we're going to use Hugo to generate our first static site! Open up your Terminal and follow along with these instructions based on [Hugo's Getting Started guide](https://gohugo.io/getting-started/quick-start/). Feel free to use their website for further documentation.
## Install Hugo
Then verify the new install.
```
brew install hugo
hugo version
```
## Create a new site
This will automatically create a new directory for your site called `quickstart`.
```
hugo new site quickstart
```

## Add a theme
See [themes.gohugo.io](https://themes.gohugo.io) for a list of themes to consider. We happened to like the Ananke theme that the quickstart guide uses, but feel free to browse for another.
```
cd quickstart;
git init;
git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke;

# Edit your config.toml configuration file
# and add the Ananke theme.
echo 'theme = "ananke"' >> config.toml
```

## Configure the theme
Open up `config.toml` in Atom and change the title to whatever you want. Don't worry about the URL for now.

```
baseURL = "https://example.org/"
languageCode = "en-us"
title = "CS52's First Hugo Site"
theme = "ananke"
```



# Adding Content
In Hugo, everything is a page. Typically, you'll have a `content` folder where pages are further organized under directories. In our current project, we should automatically have a `content/posts/` directory.
After your site is configured, we can add our first post.

### Our first post
*Note:* Make sure you're in your top-level directory (where your config file is located) or else Hugo will complain at you.

```
hugo new posts/my-first-post.md
```
Take a look at the tree organization of the `content` directory:

```
content
└── posts
    └── my-first-post.md
```


If you open up this newly created file, you'll see that Hugo has already pre-configured some of the settings for the page. It'll look something like this:

```
---
title: "My First Post"
date: 2018-04-13T12:01:29-04:00
draft: true
---

```

Notice that we've specified `draft: true`. This allows us to create and work on drafts before publishing them, which is very helpful for a live blog. Feel free to add some Markdown content below the header.

Now we can test out the site with the drafts toggle on:

```
hugo server -D
```

![alt text](images/firstpost.png)

Notice how Hugo automatically styles your post for you! Where did this come from? If you take a look inside the `themes/ananke` directory, you'll see our theme comes with built-in html layouts under the `post` directory, as well as a `_default` directory and a `partials` directory which contain more instructions to automatically style all new content we create.

### Adding images

What if we want to add images to our Markdown files? Hugo provides a few ways to do this. First, store your images in the `static/` directory. This is where Hugo automatically looks for static content. It's also common practice to add an additional `static/img/` directory specifically for images, but that's up to you.

Add some images to `static`:

```
├── static
│   ├── post-1.jpg
│   └── post-2.jpg
```

Then you can simply include them in any of your Markdown files:

```
![image](/post-1.jpg)
```
Great!

![image](images/addimage.png)


### Adding a nav bar
Adding additional layouts such as a top nav bar with links to pages within your site is incredibly easy.

First, we need to create pages for some of the links we want to include in our navbar.

```
hugo new content/about.md
hugo new content/getting-started.md
```

Add some Markdown content in each of these pages.

Now we need to create a menu. In the `config.toml` file, add the following code to create the navbar menu:

```
[menu]

  [[menu.main]]
    identifier = "about"
    name = "About"
    pre = "<i class='fa fa-heart'></i>"
    url = "/about/"
    weight = -110

  [[menu.main]]
    name = "Getting Started"
    pre = "<i class='fa fa-road'></i>"
    url = "/getting-started/"
    weight = -100

  [[menu.main]]
    name = "Posts"
    pre = "<i class='fa fa-road'></i>"
    url = "/posts/"
    weight = -100
```

Congratulations! This is what your final product should look like:

![Final Product](https://media.giphy.com/media/w8Cdsq4ZdepmysxEbR/giphy.gif)

*Now, before you go on......*

![Take 5](https://media.giphy.com/media/krP2NRkLqnKEg/giphy.gif)

## Hosting and Deployment
### Manual Deployment And First Run

Now that you have a beautiful site setup, let's prepare to deploy it! :rocket: :rocket:

1. Install Surge CLI (if you haven't already done so for your past short assignments and labs)

    `npm install -g surge`

2. Navigate to your project root directory.

3. Now, build your site as follows:

    `hugo`

    **Note**: Before you run `hugo` the second time, you should always delete your `/public` folder first. Running `hugo` will not delete the public folder for you.

4. Navigate to the `public` folder generated by `hugo`.

    :exclamation: Make sure you are in the `public` folder before running the command :exclamation:

    Now, run the following command:

      `surge`

5. If this is your first time using Surge, you should sign up at this point.

6. If not, feel free to accept the given domain or change your URL by modifying the Surge domain as follows: **<YOUR PREFERRED DOMAIN>.surge.sh**

7. Once you have deployed the website, you can go to the URL to see your beautiful webpage now live!

### Automating Deployment with Git Hooks

Now, we want to automate this process with **Git Hooks**. Before we go into this process, however, we have to create a remote repo to push our local repo to.

1. Go to [github](https://github.com/).
2. Log in to your account.
3. Click the [new repository button](https://github.com/new) in the top-right.

  ![new_repo](images/new_repo.png)

4. Click the “Create repository” button.
5. Follow the second set of instructions:

```
$ git remote add origin https://github.com/YOUR_USER/NEW_REPO`
$ git push -u origin master
```
6. Create `.gitignore` file with the following:

```
node_modules
public
```
7. `Commit` and `push` again to your repo to make sure everything is working.

    ![Git Merge](https://media.giphy.com/media/cFkiFMDg3iFoI/giphy.gif)

**Now, we can start automating the process with Git Hooks.**

1. Create a `package.json` in your project root folder and follow through the defaults:

    `npm init`

2. Then, save both submodules as development dependencies by running:

    `npm install --save-dev surge git-scripts`

3. Now, both `git-scripts` and `surge` should be listed as follows:

    ```
    "devDependencies": {
      "surge": "^0.2.1"",
      "git-scripts": "^0.2.1"
    }
    ```

4. Now, create a new Bash script in your CLI at your project root folder:

    `atom deploy.sh`

5. Copy the following into the script:

    ```
    #!/bin/bash
    rm -rf public
    hugo
    surge --domain <YOUR DOMAIN>.surge.sh ./public
    ```

6. Grant it elevated status as follows:

    `sudo chmod 755 ./deploy.sh`

7. The Git Scripts package you’ve installed now allows you to add Git Hooks right into your `package.json` file. After the `devDependencies` section, add the following:

    ```
    "devDependencies": {
      "surge": "latest",
      "git-scripts": "0.2.1"
    },
    "git": {
      "scripts": {
        "pre-push": "./deploy.sh"
      }
    }
    ```

8. Now, when you push to your repository…

    `git push origin master`

    …the pre-push Git Hook will delete the existing `public` folder, call `hugo` and run `surge` which publishes the public directory to your domain.

    You now no longer have to worry about deleting `public` folder each time before you run hugo. You just have to commit and push your code to publish a new post onto your static site.


# Sass Tutorial

### Installation Tutorial

Mac Installation (For other operating systems, please check `http://sass-lang.com/install` for instructions)

To install sass, run the command: `npm install -g sass` in 💻terminal.

To check that Sass is installed, check the version with the command: `sass -v`.

### Variables

With Sass variables, we can store information that we want to use multiple times. For example, if a brand color/font is used multiple times throughout your website, you may want to store those in variables and just call the variable each time.

We can assign colors and fonts to variables with the following we can do: 


	$primary-font: Times New Roman, serif
	$primary-color: #a4a4a4
	$secondary-color: #faf7f4
	
And when we reference the variable as our value:

	div
		color: $primary-color
		background-color: $secondary-color
		font: $primary-font
	
Notes: 

	Compared to CSS, Sass does not require brackets {} or semicolons ; 
	
	Sass is a preprocessor so when processed, the variables that we define are translated into normal CSS. 

### Nesting

With Sass, we can nest our CSS selectors similar to the hierarchy of HTML. 

(without Sass) Rather than writing: 

	footer ul {
		display: flex;
		flex-direction: row;
		justify-content: space-around;
		list-style: none;
	}
	
	footer li {
		display: inline-block;
	}
	
	footer p {
		font: $primary-font;
		color: $primary-color;
	}

(with Sass) We can write:
	
	footer {
	  ul {
	    display: flex;
		flex-direction: row;
		justify-content: space-around;
		list-style: none;
	  }
	
	  li {	
	  	display: inline-block;
	  }
	
	  p {
	   font: $font-stack;
	   color: $primary-color;
	  }
	}

With Sass, we establish the same visual hierarchy as HTML. The ul, li and p selectors now live under the footer selector, similar to how each tag would be structured in HTML.

### Mixins

Mixins are similar to variable but allow you store larger chunks of CSS declarations that you may want to reuse throughout a website.

To declare a mixin, we first use the `@mixin` tag, followed by the name of the mixin and the inputs we want to use.

The mixin declaration would look like

	@mixin $mixinName ($input1, $input2){
		...
	}
	
For example:

	$primary-color: blue;
	
	@mixin navItem ($color, $backgroundColor) {
		color: $color;
		background-color: $backgroundColor;
    }
	
	.rectangle { @include navItem ($primary-color, black); }

When the CSS is generated, it will compile to become:

	.rectangle{
		color: blue;
		background-color: black;
	}

Notes: 

Default Values can also be set so for the example above:

	@mixin navItem($color, $backgroundColor: green) {
		color: $color;
		background-color: $backgroundColor;
	}
	
	.rectangle { @include navItem (blue); }

which would give us: 

	.rectangle{
		color: blue;
		background-color: green;
	}

The color is set to blue with the first argument that is passed in and the background-color is set to green by default since there is no correspnding argument passed in. 

### Inheritance

	.smallShyDog{
		background-color: white;
		margin: 100000px;
		height: 1px;
		width: 1px;
		border: 1px solid black;
	}
	
	.smallShyCat{
		background-color: orange;
		margin: 100000px;
		height: 1px;
		width: 1px;
		border: 1px solid black;
	}
	
	.smallShyHippo{
		background-color: grey;
		margin: 100000px;
		height: 1px;
		width: 1px;
		border: 1px solid black;
	}

We can employ Sass inheritance by using `@extend` to shorten our code where the selectors inherit the same properties and values from the same parent.

	.smallShyAnimals{
		margin: 100000px;
		height: 1px;
		width: 1px;
		border: 1px solid black;
	}
	
	.smallShyDog{
		background-color: white;
		@extend .smallShyAnimals;
	}
	
	.smallShyCat{
		background-color: orange;
		@extend .smallShyAnimals;
	}
	
	.smallShyHippo{
		background-color: grey;
		@extend .smallShyAnimals;
	}
	
Each of the small shy animals inherit their small dimensions (because they are small) and large margins (because they are shy) from a parent selector .smallShyAnimals. The background-color for each of the animal selectors are different but that is okay since they are siblings and can branch out beyond their shared attributes.
	

### Additional Features 

Sass allows us to break down our CSS into smaller parts, which is more maintainable and easier to organize.

For example, if we want to break down our styles for each page of our website, we can!

Say we have multiple Sass files: _homePage.scss, _userPage.scss, and _loginPage.scss. We can import all these pages into a singular page called styles.sass. 

	// a small section: _homePage.scss
	
	.homePageButton{
		width: 20px;
		height: 20px;
		border-radius: 5px;
		background-color:blue;
	}
	
	// a small section: _userPage.scss
	
	.userPageButton{
		width: 100px;
		height: 100px;
		border-radius: 10px;
		background-color:green;
	}
	
	// a small section: _loginPage.scss
	
	.loginPageButton{
		width: 100px;
		height: 50px;
		border-radius: 3px;
		background-color:yellow;
	}
	
All these files can be imported into the base file: 
	
	// _styles.sass
	
	@import homePage
	@import userPage
	@import loginPage
	
	body
	  font: Times New Roman, serif
	  background-color: #faf7f4
	  
Additionally, Sass supports standard math operators (+, -, *, /, and %): 

	.rectangle
	  width: 250px / 80px * 80%
	
### Sass vs Scss

Sass is the older syntax where brackets and semicolons are not used. The new and primary syntax of Sass 3 is called Scss. Scss is more like the traditional CSS with brackets and semicolons while still supporting the features of Sass such as nesting.


### Additional Sass Resources:

	http://sass-lang.com/install
	http://sass-lang.com/guide
	https://cssauthor.com/sass-resources/
	http://thesassway.com/
	https://www.sitepoint.com/html-css/css/sass-css/
	http://sassbreak.com/
	https://scotch.io/tutorials/getting-started-with-sass
	https://www.sitepoint.com/the-benefits-of-inheritance-via-extend-in-sass/
	http://sass.news/
	http://zerosixthree.se/8-sass-mixins-you-must-have-in-your-toolbox/


