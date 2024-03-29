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

Rails is an MVC framework. It uses models, views and controllers, and also routes. https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller

In terms of implementation: Routes are rules written in a Ruby DSL (Domain-Specific Language). Controllers are Ruby classes, and their public methods are actions. And views are templates, usually written in a mixture of HTML and Ruby.

## Make Rails say "Hello"

https://guides.rubyonrails.org/getting_started.html#say-hello-rails

Añadir una ruta. Luego un controlador.

Para añadir un controlador, se puede usar el generador de Rails.

En este caso usamos `--skip-routes` porque ya la hemos creado.

```
$ bin/rails generate controller Articles index --skip-routes
```

![Pantallazo Terminal Resultado](image.png)

Parece que también te crear los archivos para los tests.

Ahora ya podemos ir a `app/controllers/articles_controller.rb`.

![Pantallazo ArticlesController acabo de crear](image-1.png)

Vemos que la acción `index` está vacía. Cuando no incluya una vista para renderizar específica, usará una vista que coincida el nombre con la acción. Convention Over Configuration!

Vista creada en `app/views/articles/index.html.erb`.

![Pantallazo vista Index Articles acabada de crear](image-2.png)

Substituimos contenido por `<h1>Hello, Rails!</h1>` y vamos a http://localhost:3000/articles

![Pantallazo resultado](image-3.png)

### Home Page

https://guides.rubyonrails.org/getting_started.html#setting-the-application-home-page

La ruta principal, por ahora, sigue siendo la página de prueba. Hay que definir una ruta a la página principal.

![Pantallazo resultado](image-4.png)

## Autoloading

https://guides.rubyonrails.org/getting_started.html#autoloading  
more info: https://guides.rubyonrails.org/autoloading_and_reloading_constants.html

Rails tiene una "autocarga" de los objetos y librerías imprescindibles. Por lo tanto, no hay que usar `requiere "application_controller"` (por ejemplo), ya que con heredar es suficiente.

**You only need `require` calls for two use cases:**

- To load files under the `lib` directory.
- To load gem dependencies that have `require: false` in the `Gemfile`.

## Models

https://guides.rubyonrails.org/getting_started.html#mvc-and-you-generating-a-model

Para generar modelos con el generador:

```
$ bin/rails generate model Article title:string body:text
```

![Pantallazo terminal resultado](image-5.png)

![Pantallazo migracion creada](image-6.png)

![Pantallazo modelo](image-7.png)


### Migraciones

Por defecto `create_table` añade una columna `id`

```
$ bin/rails db:migrate
```

![Pantallazo terminal resultado](image-8.png)

Curiosament no demana fer un create.

### Modelo (clase)

Empezaremos trabajando por consola:

```
$ bin/rails console
```

![Pantallazo consola](image-9.png)

```
irb> article = Article.new(title: "Hello Rails", body: "I am on Rails!")
```

![consola resultado](image-10.png)

```
irb> article.save
```

![consola resultado guardado](image-12.png)

```
irb> Article.find(1)
irb> Article.all
```

Prueba tu

## Usando MVC para mostrar una lista de Articles

https://guides.rubyonrails.org/getting_started.html#showing-a-list-of-articles

Ahora que ya tenemos el modelo, solo falta poner al día el controlador y la vista:

**Controlador**

`app/controllers/articles_controller.rb`

```
class ArticlesController < ApplicationController
  def index
    @articles = Article.all
  end
end
```

**Vista**

`app/views/articles/index.html.erb`

```
<h1>Articles</h1>

<ul>
  <% @articles.each do |article| %>
    <li>
      <%= article.title %>
    </li>
  <% end %>
</ul>
```


Now: http://localhost:3000/

## CRUD

### Read

Ya hemos hecho el Read del índice, ahora el "single":

`config/routes.rb`

```
Rails.application.routes.draw do
  root "articles#index"

  get "/articles", to: "articles#index"
  get "/articles/:id", to: "articles#show"
end
```

`app/controllers/articles_controller.rb`

```
class ArticlesController < ApplicationController
  def index
    @articles = Article.all
  end

  def show
    @article = Article.find(params[:id])
  end
end
```

Y ahora falta la nueva vista: `app/views/articles/show.html.erb`

```
<h1><%= @article.title %></h1>

<p><%= @article.body %></p>
```

Por último, en la vista del `index` podemos añadir un enlace que lleve a la vista de detalles de cada Article.

```
<h1>Articles</h1>

<ul>
  <% @articles.each do |article| %>
    <li>
      <a href="/articles/<%= article.id %>">
        <%= article.title %>
      </a>
    </li>
  <% end %>
</ul>
```

### Resources

`resources` nos sirve para cubrir todo el CRUD en el `config/routes.rb`

```
Rails.application.routes.draw do
  root "articles#index"

  resources :articles
end
```

![bin/rails routes](image-13.png)

### Creating

https://guides.rubyonrails.org/getting_started.html#creating-a-new-article

`app/controllers/articles_controller.rb`

```
class ArticlesController < ApplicationController
  def index
    @articles = Article.all
  end

  def show
    @article = Article.find(params[:id])
  end

  def new
    @article = Article.new
  end

  def create
    @article = Article.new(title: "...", body: "...")

    if @article.save
      redirect_to @article
    else
      render :new, status: :unprocessable_entity
    end
  end
end
```

Y ahora las vistas:

`app/views/articles/new.html.erb`

```
<h1>New Article</h1>

<%= form_with model: @article do |form| %>
  <div>
    <%= form.label :title %><br>
    <%= form.text_field :title %>
  </div>

  <div>
    <%= form.label :body %><br>
    <%= form.text_area :body %>
  </div>

  <div>
    <%= form.submit %>
  </div>
<% end %>
```

#### Parámetros fuertes

Para evitar introducir los valores del nuevo objeto Article uno a uno (usando los parámetros recibidos por el formulario), Rails permite el uso de un concepto llamado "Strong Parameters", inspirado en el concepto del tipado fuerte https://en.wikipedia.org/wiki/Strong_and_weak_typing

Para ello, crearemos un método privado con el nombre `article_params` que filtrará los parámetros.

`app/controllers/articles_controller.rb`

```
class ArticlesController < ApplicationController
  def index
    @articles = Article.all
  end

  def show
    @article = Article.find(params[:id])
  end

  def new
    @article = Article.new
  end

  def create
    @article = Article.new(article_params)

    if @article.save
      redirect_to @article
    else
      render :new, status: :unprocessable_entity
    end
  end

  private
    def article_params
      params.require(:article).permit(:title, :body)
    end
end
```

#### Validaciones

Usar validaciones en el modelo para evitar errores:

`app/models/article.rb`

```
class Article < ApplicationRecord
  validates :title, presence: true
  validates :body, presence: true, length: { minimum: 10 }
end
```

Esto nos permite añadir algunas validaciones en el formulario:

`app/views/articles/new.html.erb`

```
<h1>New Article</h1>

<%= form_with model: @article do |form| %>
  <div>
    <%= form.label :title %><br>
    <%= form.text_field :title %>
    <% @article.errors.full_messages_for(:title).each do |message| %>
      <div><%= message %></div>
    <% end %>
  </div>

  <div>
    <%= form.label :body %><br>
    <%= form.text_area :body %><br>
    <% @article.errors.full_messages_for(:body).each do |message| %>
      <div><%= message %></div>
    <% end %>
  </div>

  <div>
    <%= form.submit %>
  </div>
<% end %>
```

### Update

`app/controllers/articles_controller.rb`

```
class ArticlesController < ApplicationController
  def index
    @articles = Article.all
  end

  def show
    @article = Article.find(params[:id])
  end

  def new
    @article = Article.new
  end

  def create
    @article = Article.new(article_params)

    if @article.save
      redirect_to @article
    else
      render :new, status: :unprocessable_entity
    end
  end

  def edit
    @article = Article.find(params[:id])
  end

  def update
    @article = Article.find(params[:id])

    if @article.update(article_params)
      redirect_to @article
    else
      render :edit, status: :unprocessable_entity
    end
  end

  private
    def article_params
      params.require(:article).permit(:title, :body)
    end
end
```

#### Using partials between New and Edit

https://guides.rubyonrails.org/getting_started.html#using-partials-to-share-view-code  
more info: https://guides.rubyonrails.org/layouts_and_rendering.html#using-partials

Como el formulario de Edit y New son el mismo, usaremos el mismo partial para las dos vistas.

`app/views/articles/_form.html.erb`

```
<%= form_with model: article do |form| %>
  <div>
    <%= form.label :title %><br>
    <%= form.text_field :title %>
    <% article.errors.full_messages_for(:title).each do |message| %>
      <div><%= message %></div>
    <% end %>
  </div>

  <div>
    <%= form.label :body %><br>
    <%= form.text_area :body %><br>
    <% article.errors.full_messages_for(:body).each do |message| %>
      <div><%= message %></div>
    <% end %>
  </div>

  <div>
    <%= form.submit %>
  </div>
<% end %>
```

### Delete

https://guides.rubyonrails.org/getting_started.html#deleting-an-article

`app/controllers/articles_controller.rb`

```
class ArticlesController < ApplicationController
  def index
    @articles = Article.all
  end

  def show
    @article = Article.find(params[:id])
  end

  def new
    @article = Article.new
  end

  def create
    @article = Article.new(article_params)

    if @article.save
      redirect_to @article
    else
      render :new, status: :unprocessable_entity
    end
  end

  def edit
    @article = Article.find(params[:id])
  end

  def update
    @article = Article.find(params[:id])

    if @article.update(article_params)
      redirect_to @article
    else
      render :edit, status: :unprocessable_entity
    end
  end

  def destroy
    @article = Article.find(params[:id])
    @article.destroy

    redirect_to root_path, status: :see_other
  end

  private
    def article_params
      params.require(:article).permit(:title, :body)
    end
end
```

a la vista del show `app/views/articles/show.html.erb` se añade:

```
<h1><%= @article.title %></h1>

<p><%= @article.body %></p>

<ul>
  <li><%= link_to "Edit", edit_article_path(@article) %></li>
  <li><%= link_to "Destroy", article_path(@article), data: {
                    turbo_method: :delete,
                    turbo_confirm: "Are you sure?"
                  } %></li>
</ul>
```

**Interesante** el uso de `data-turbo-method="delete"` y `data-turbo-confirm="Are you sure?"`

## Ampliando con segundo modelo

https://guides.rubyonrails.org/getting_started.html#adding-a-second-model

```
$ bin/rails generate model Comment commenter:string body:text article:references
```

`app/models/comment.rb`

```
class Comment < ApplicationRecord
  belongs_to :article
end
```

Se han generado relaciones entre modelos

```
$ bin/rails db:migrate
```

Con las asociaciones, puedes relacionar modelos y desde un objeto en concreto puedes llegar a todos los objetos relacionados. Ejemplo: `@article.comments`

More info: https://guides.rubyonrails.org/association_basics.html

### Añadiendo a la ruta con `resources`

`config/routes.rb`

```
Rails.application.routes.draw do
  root "articles#index"

  resources :articles do
    resources :comments
  end
end
```

### Controlador

`app/controllers/comments_controller.rb`

```
class CommentsController < ApplicationController
  def create
    @article = Article.find(params[:article_id])
    @comment = @article.comments.create(comment_params)
    redirect_to article_path(@article)
  end

  private
    def comment_params
      params.require(:comment).permit(:commenter, :body)
    end
end
```

**Interesante** la manera de crear con la asociacion directamente: `@comment = @article.comments.create(comment_params)`

### REFACTORING

https://guides.rubyonrails.org/getting_started.html#refactoring

Partials para los comments, en vez de ponerlos a pelo en el show de los article

**INTERESANTISIMO** NO HACE FALTA HACER UN BUCLE automáticamente se genera solo con hacer un render:

![Alt text](image-14.png)

![Alt text](image-15.png)

Parece ser que al usar `render @articles.comments` en vez de buscar el show, busca un `_comments`

![Alt text](image-16.png)

### Using concerns

https://guides.rubyonrails.org/getting_started.html#using-concerns

```
$ bin/rails generate migration AddStatusToArticles status:string
$ bin/rails generate migration AddStatusToComments status:string
$ bin/rails db:migrate
```

![Alt text](image-17.png)

Los Concerns son comportamientos compartidos entre diferentes modelos (no entiendo porque no heredan)

![Alt text](image-18.png)

Ara puc importar aquest comportament als altres models:

![Alt text](image-19.png)

![Alt text](image-20.png)

Además, se le pueden añadir métodos de clase:

```
class_methods do
  def public_count
    where(status: 'public').count
  end
end
```

![Alt text](image-21.png)

Y ahora podemos hacer uso del método .public_count() con cualquier clase:

![Alt text](image-22.png)

**IMPORTANT** Podem cridar atributs de classe amb els doble colons: `Visible::VALID_STATUSES`. He fet proves i també es pot fer desde les classes que ho hereden (permetent el polimorfisme)

### DELETING comments

https://guides.rubyonrails.org/getting_started.html#deleting-comments

## Security

https://guides.rubyonrails.org/getting_started.html#security

Seguirdad básica: en un controlador añadir...

```
http_basic_authenticate_with name: "dhh", password: "secret", only: :destroy
```

![Alt text](image-23.png)

Es un sistema de autenticacion HTTP básico que hace uso del propio navegador

"Other authentication methods are available for Rails applications. Two popular authentication add-ons for Rails are the **Devise rails engine** (https://github.com/plataformatec/devise) and the **Authlogic gem** (https://github.com/binarylogic/authlogic), along with a number of others."

More info: https://guides.rubyonrails.org/security.html

