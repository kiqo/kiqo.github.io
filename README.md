# Kiqo-Blog

## Get Started 

```
conda create -n kiqo.github.io "python=3.12"
python -m pip install "Nikola[extras]"
```

## Nikola

This folder contains the source used to generate a static site using Nikola.

Installation and documentation at https://getnikola.com/
Configuration file for the site is ``conf.py``.

To check all available commands:
```
nikola help
```

### Generate Site
```
cd kiqo/
nikola build
```
Your site has now been generated inside the `output/` directory. 

### Create New Post
```
nikola new_post -e -f markdown
```

### Start Development Server
```
nikola serve -b [--browser]
```
You can also use a server with automatic rebuilds by running `nikola auto --browser`. Note that both servers should be used for development only; use other deployment solutions instead.