# ModuleNotFoundError shenanigans

This repo contains a minimal setup to avoid running into the `ModuleNotFoundError` when running scripts located 
outside the root directory.

## Getting started

```shell
# Create a virtual environment
python3 -m venv .venv

# Activate the virtual environment
source .venv/bin/activate

# To do only the first time (not necessary afterwards)
# Run the setup script that creates the path configuration file
python setup.py

# You can now import the `src` module successfully!
```


## Explanations

Given a project with the following arborescence:
```
my_project/
├───src/
│   ├───__init__.py
│   └───a.py
└───scripts/
    └───run.py
```

When a script file located in a `scripts` folder imports a file from another module (the `src` folder in this repo), we 
get a `ModuleNotFoundError`.
This is due to the fact that the path to the root directory (where the module is located) has not been added to 
`sys.path`.
Therefore, Python cannot find it.

To solve the problem, there are several solutions:

- Run the script as a module by executing `python -m scripts.run`. As mentioned in
  the [docs](https://docs.python.org/3/using/cmdline.html#cmdoption-m), the current directory gets added to
  `sys.path`. That works, but is a bit cumbersome because there is no autocompletion in the terminal
  for `scripts.run` by default.
- Manually append the path to the root directory at the beginning of each script file `sys.path.append(/path/to/root/)`.
  This also works, but it is very tedious to add this every time.
- Add the current directory to `sys.path` from the CLI before executing the script 
  (`export PYTHONPATH=: ; python scripts/run.py`). This can be automated to avoid having to add it every time on
  one's particular machine, but when sharing the repo and giving instructions to execute a script, users will get into
  the same trouble.

Another (arguably better) solution to the problem is to make use of so-called "path configuration files".
As mentioned in the [docs](https://docs.python.org/3/library/site.html), the content of those files is added
to `sys.path`.

In this repo, I added a `setup.py` script that automatically adds the root directory to `sys.path` (by adding it in a 
"path configuration file", located in the `site-packages` folder of the python executable used).
To avoid getting `ModuleNotFoundError`s, one needs to be in the correct environment (virtual or not) and execute the 
`setup.py` script.
This only needs to be done once (once the path configuration file has been created, Python will be able to find modules 
in the current directory).



