[-- Ejercicio 1 --](./ejercicio01.md)

------------------

## Ejercicios 2

### Crear una receta para instalar nginx, tu editor favorito y algún directorio y fichero que uses de forma habitual.

##### Introducción

La [receta a crear en chef](./chef) consta de tres elementos:

* Servidor web **nginx**.

* Editor de textos **vim** (gvim si se tratara de un entorno gráfico)

* Creación de un **directorio base para proyectos en github**, incluyendo el README.md y el "LICENCE"

La estructura del recetario es la siguiente:

![](./images/chef_tree.png "Estructura del recetario")

En el [raíz del directorio](./chef) tendremos los ficheros de configuración del recetario en sí y el directorio con las distintas recetas según "elemento":

* [**node.json**](./chef/node.json): Contendrá información de ejecución de cada elemento (versión, usuario, puerto, etc)

* [**solo.rb**](./chef/solo.rb): Aquí se indicará la ruta del resto de ficheros y directorios del recetario

* [**cookbooks**](./chef/cookbooks): Directorio en el que almacenaremos cada elemento con su respectiva receta

> Cada elemento a instalar tendrá, como mínimo, sus propias recetas y un fichero de metadatos.


##### Configuración

Proseguiremos a configurar cada uno de los ficheros necesarios, empezando por los ficheros del recetario:

* [**./solo.rb**](./chef/solo.rb)
		
        cookbook_path File.expand_path("../cookbooks", __FILE__)
        json_attribs File.expand_path("../node.json", __FILE__)


* [**./node.json**](./chef/node.json)

    ```
{
        "nginx": {
                "version"       : "1.4.4",
                "user"          : "www-data",
                "port"          : "80"
        },

        "vim": {
                "version"       : "7.3.429"
        },

        "gitproject": {
                "name"          : "Proyecto base"
        },

        "run_list": [
                "recipe[nginx]",
                "recipe[vim]",
                "recipe[gitproject]"
                ]
}
    ```

Continuaremos configurando cada uno de los metadatos de los elementos ("./cookbooks/< elemento >/metadata.rb"):


```
maintainer              "Iñaki Fernández Janssens de Varebeke"
maintainer_email        "inakitinajo@gmail.com"
description             "Crea un directorio mínimo de un proyecto para github"
version                 "0.0.1"

recipe "gitproject", "Receta de creación de un direcotrio mínimo para github"
```

> ./cookbooks/gitproject/metadata.rb


Configuramos cada una de las recetas:

* [./cookbooks/nginx/recipes/default.rb](./chef/cookbooks/nginx/recipes/default.rb)

        package 'nginx'

* [./cookbooks/vim/recipes/default.rb](./chef/cookbooks/vim/recipes/default.rb)

        package 'vim'

* [./cookbooks/gitproject/recipes/default.rb](./chef/cookbooks/gitproject/recipes/default.rb)

    ```
directory "/home/melki/gitproject" do
        owner node['user']['name']
        group node['user']['group']
        mode 00755
        action :create
end
file "/home/melki/gitproject/README.md" do
        owner node['user']['name']
        group node['user']['group']
        mode 00644
        action :create_if_missing
        content "Proyecto base de GitHub"
end
remote_file "/home/melki/gitproject/LICENCE" do
        owner node['user']['name']
        group node['user']['group']
        mode 00644
        action :create_if_missing
        source "http://www.gnu.org/licenses/gpl-3.0.txt"
end
    ```

Ejecutamos el recetario en el sistema que deseemos:

    # chef-solo -c chef/solo.rb

![](./images/chef_after_run.png "Chef run")


------------------

[-- Ejercicio 3 --](./ejercicio03.md)
