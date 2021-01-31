Docker / PyCharm Odoo Development Environment
==============================================

# Configuring the docker environment

This repository provides some tools for building a simple custom Odoo docker image and
running it for development based on any Odoo source base, which you provide.

## tldr;

1. Get Odoo into `.docker/odoo`
```bash
git clone https://github.com/odoo/odoo -b 14.0 --depth=1 .docker/odoo
```
2. Build docker image.
```bash
cd .docker
docker build -t local/odoo:14.0 .
cd ..
```
3. Drop some addons in the `addons` directory
4. Launch the composition from the project root

**First** make sure your docker-compose.yaml uses the image you just created by altering this line
`image: local/odoo:14.0` to match the tag used in `docker build`

```bash
./odoo-bin -d dev -i base
```


# Configuring PyCharm for Odoo development
