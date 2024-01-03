# Studying Ruby on Rails

Tutorial to learn and master Ruby on Rails.

https://guides.rubyonrails.org/

Ruby 3.2.1
Ruby on Rails 7.2.1

## Notes

https://guides.rubyonrails.org/getting_started.html

In this tutorial we'll gonna create a weblog.

## Generator

The generator application create a new Rails application and install the gem dependencies

```
$ rails new blog
```

You can see all of the command line options that the Rails application generator accepts by running `rails new --help`.

---

Me acaba de crear una aplicación rails con su propio git y una versión de rails que no quiero... Investigar...

**Respuesta:** resulta que cuando lancé el comando `rails new blog` estaba usando una version de rails 7.0.8. Para poder usar la versión que a mí me interesa, he tenido que crear un `Gemfile` con la gema Rails y lanzar un `bundle install`.

Al hacer una aplicación con este comando, te crea un git con la rama 'main' como principal (en vez de 'master')

Al haber doble git (el de esta carpeta y el de la carpeta `blog`) no puedo guardar todos los cambios a la vez en este mismo, así que he borrado la carpeta `.git` de la aplicación rails.

---

## Open/Run rails

When de new Rails application is created, you can run de Rails Server (running on Puma https://github.com/puma/puma).

```
$ bin/rails server
```

## Basic concepts

Rails is an MVC framework. It uses models, views and controllers, and also routes.

In terms of implementation: Routes are rules written in a Ruby DSL (Domain-Specific Language). Controllers are Ruby classes, and their public methods are actions. And views are templates, usually written in a mixture of HTML and Ruby.

## Make Rails say "Hello"