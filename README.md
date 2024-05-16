# Creacion de un proyecto en Django localmente y su repositorio en GitHub

## Antes de comenzar, asegurarse que:

### Esta instalado Python?
Comprobar Instalacion
`$ python3 --version`

Instalar: [Enlace a Python ORG](https://www.python.org/downloads/macos/)
> Instalar la ultima version estable

### Esta instalado Django?
Comprobar Instalacion
`$ django-admin --version`

Instalar
`$ python3 -m pip install django`

### Esta instalado Git?
Comprobar Instalacion
`$ git --version`

Instalar: [Enlace a Git](https://git-scm.com)

Configuracion Inicial 
`$ git config --global user.name "Tu Nombre"`
`$ git config --global user.email "tu-email@ejemplo.com"`

Verificar Configuracion
`$ git config --global --list`

### Esta instalado VSC y sus extensiones?
Instalar: [Visual Studio Code](https://code.visualstudio.com/download)
Extensiones:
* Python [Microsoft]
* Django [Baptiste Darthenay]
* Autopep8 [Microsoft]
* Markdown Preview Enhanced [Yiyi Wnag]

### Tienes cuenta en GitHub?
Crear Cuenta: [Enlace a GitHub](https://github.com)

## Ahora si podemos comenzar

### 1. Crear el proyecto de Django
`$ django-admin startproject my_project`
> Algunas veces solo permite crear el proyecto con usuario root
> El comando crea el directorio del proyecto

### 2. Crear el repositorio
[Enlace a GitHub](https://github.com/)
> No crear el README.md para eviat conflictos con el primer push

### 3. Unir el proyecto local con el repositorio
`$ git init`
> Ejecutar el anterior comando dentro del diretorio que se creo al crear el proyecto de django

`$ git add.`
`$ git commit -m "My first commit"`
`$ git remote add origin https://github.com/user/my_proyect.git`
`$ git push -u origin main`
> Solo si hay un error con el primer push se puede ejecutar `$ git push --force origin main`