## Jupyter lab install

### Install jupyter

    pip install jupyter

for remote access like gitpod or codespace, generate configure file:

    jupyter notebook --generate-config


edit file `jupyter_notebook_config.py`:

```
c.ServerApp.allow_remote_access = True
```

generate login password:


    jupyter notebook password


### Install deno typescript kernel

1. install deno:

    curl -fsSL https://deno.land/install.sh | sh

2. install deno jupyter kernel:

    deno jupyter --unstable --install

3. restart jupyter lab

