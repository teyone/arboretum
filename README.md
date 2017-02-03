## This is a work in progress ...

Arboretum
=========
Because it'd be awesome if we had an easy way to share all of our nerdy
research more broadly throughout the organization.

Why?
----
- Detailed analyses get trapped in email and not shared throughout the org.
- The analysis is often done in a notebook anyway.
- Let's open that up more broadly within the organization so that everyone can
use the information -- context is important for all roles.
- Confluence is kind of a pain in the but to work with, especially when all of
your work already exists in a notebook.

Setup
-----
The blog runs on [Pelican](http://docs.getpelican.com/en/stable/), its
[pelican-ipynb](https://github.com/danielfrg/pelican-ipynb) plugin, Jupyter,
and various dependencies. You can install them all with the `requirements.txt`
file.

Make sure you have [virtualenv](https://virtualenv.pypa.io/en/stable/)
installed and then:
```bash
$ virtualenv venv --python=$(which python3)
$ source venv/bin/activate
$ pip install -r requirements.txt
```
Everything should work fine with Python 2.7 (but you should switch to Python 3).

Writing a new post
------------------
### If it's just text, maybe with some images:
- Create a Markdown file in the appropriate `content/{year}/` directory.
- Name the Markdown file something similar to the post's title (maybe the same).
  - Follow the `title_with_underscores.md` naming convention.
- Make sure your Markdown file starts with the appropriate metadata:
```
Title: My super title
Date: 2010-12-03 10:20
Modified: 2010-12-05 19:30
Category: Python
Tags: pelican, publishing
Slug: my-super-post
Authors: Alexis Metaireau, Conan Doyle
Summary: Short version for index and feeds and maybe some more stuff.
```

### If everything's already in a Jupyter Notebook:
- Put your notebook file in `content/notebooks/`.
  - Name it something relevant (probably similar to whatever the post's title
  will be).
- Create a Markdown file in the appropriate `content/{year}/` directory.
- Name the Markdown file something similar to the post's title (maybe the same).
  - Follow the `title_with_underscores.md` naming convention.
- Add the appropriate metadata to your Markdown file. It should look something
like this:
```
Title: Awesome Jupyter Notebook Analysis
Date: 2016-01-01
Modified: 2016-07-01
Category: marketing
Tags: funnel, trials, landing pages, ab testing
Slug: awesome-jupyter-notebook-analysis
Authors: Greg Reda
Summary: Analysis of funnels and trials and other awesome things related to
marketing and foos and bars and baz.

{% notebook notebooks/awesome-jupyter-notebook-analysis.ipynb %}
```

Structure
---------
```
plugins/  # plugins you want to use. only pelican-ipynb and assests by default.
theme/  # theme
content/
  examples/
  pages/  # these are permanent pages -- like a /404 page or /about, etc.
  images/  # if it's a markdown post and it has images, put them here
  extra/  # extra stuff, like robots.txt (but shouldn't be needed)
```
I use a `content/{year}/post.md` structure, but it's possible to
use pretty much anything. You could even through every `post.md` in the root
`content/` directory if you wanted.

Publishing
----------
### Using the AWS CLI tool
```bash
# refresh all the notebooks
$ jupyter nbconvert --to notebook --execute content/notebooks/*.ipynb
# build the static content
$ pelican content/
# push to your S3 bucket
$ aws s3 cp output/* s3://my-bucket/ --recursive
```

### Travis
Out of the box, `arboretum` comes set up for easy deployment to S3 using
[Travis](https://travis-ci.org/). On check-in, it'll also re-execute all of
your notebooks, meaning that your analyses can be kept up to date with the most recent information by executing your notebook against your database.

FAQ
---
### Why didn't you use Airbnb's
[Knowledge Repo](https://github.com/airbnb/knowledge-repo)?
I tried to at first, but ultimately found it to be a bit heavy. We
found the idea of each post being its own repo (within the knowledge repo)
confusing and didn't see a need for something backed by a database or full
web app when static HTML would do.

### Why do I keep getting an ```ERROR: Cannot load plugin `ipynb.liquid```` when generating my content?
If you've installed Pelican's plugins with `git clone --recursive
https://github.com/getpelican/pelican-plugins` you'll need to rename the
`pelican-ipynb` directory to `ipynb`:
```bash
$ cd path/to/plugins && mv pelican-ipynb ipynb
```

### Our team works in Python2, Python3, and R. Can we still use this?
Yep. You should just need to install the necessary Jupyter kernels. I would also
recommend creating separate directories within `content/notebooks/` for each
kernel. This'll allow you to know which kernel to use when executing each
notebook before deployment.

- [Installing the Python2 kernel](https://ipython.readthedocs.io/en/latest/install/kernel_install.html).
- [Installing the R kernel](https://irkernel.github.io/installation/)


TODO
----
- [ ] Fork pelican-plugins and use only what we need
  - We don't need the entire repo and all submodules
- [ ] Execute notebooks on check-in
  - [ ] Rename executed notebooks (will be `test.nbconvert.ipynb`)
- [ ] Store DB creds in Travis ... S3?
  - This would allow us to automatically refresh all analyses on check in
  - Can we do this? Probably not possible to allow Travis to hit our DB.
  - Can we use AWS Lambda somehow?
- [ ] Toggle code cells button (default on, article-level param)
- [x] Install both Jupyter kernels
  - [ ] Check notebook python version for execution -- 2 vs 3 will be different
  - [ ] How do we figure out which notebooks are 2 and which are 3?
- [ ] Fix syntax CSS for R cells
- [ ] Pages for categories
- [ ] Search functionality

Built with
----------
- [Pelican](http://blog.getpelican.com/)
- [Jupyter Notebook](http://jupyter.org/)
- [Skeleton](http://getskeleton.com/)
- [void](https://github.com/gjreda/void) theme for Pelican, though we've
simplified it quite a bit (removed various social/sharing features).
- [pelican-ipynb](https://github.com/danielfrg/pelican-ipynb) plugin for Pelican.
- [assets](https://github.com/getpelican/pelican-plugins/tree/master/assets)
plugin for Pelican.
