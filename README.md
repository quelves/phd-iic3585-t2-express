Express Template Engines
===================


En express, un motor de plantillas le permite utilizar archivos de plantillas estáticas en su aplicación. En tiempo de ejecución, el motor de plantillas reemplaza variables en un archivo de plantilla con valores reales, y transforma la plantilla en un archivo HTML que se envía al cliente.

# Pug/Jade #

Pug es un motor de plantillas de alto rendimiento fuertemente influenciado por Haml e implementado con el JavaScript para Node.js y navegadores. Este proyecto era conocido anteriormente como "Jade", pero "Jade" es una marca registrada y el proyecto cambió de nombre. La próxima versión principal llevará a "pug", como el nombre del paquete.

## Language References ##

Pug posee una serie de comandos para soportar la creacion de plantillas, con opciones para extender otras plantillas asi como incluir otras plantillas para componer un layout, asi como funciones para el control de flujo y estado.

## Attributes ##

Los atributos son los mismos de html pero con la sintaxis de JS

```javascript
a(href='google.com') Google
= '\n'
a(class='button' href='google.com') Google
= '\n'
a(class='button', href='google.com') Google
```

## Template Inheritance ##


### Extends y Block ###

The extends keyword allows a template to extend a layout or parent template. It can then override certain pre-defined blocks of content.

Un **block** es simplemente un "bloque" que puede ser sustituido dentro de una plantilla hija. Este proceso es recursivo
```javascript
html
  head
    title My Site - #{title}
    link(rel='stylesheet', href='/bootstrap/css/bootstrap.min.css')
    link(rel='stylesheet', href='/bootstrap/css/bootstrap-responsive.min.css')
    link(rel='stylesheet', href='/stylesheets/style.css')
    block scripts
      script(src='/bootstrap/js/bootstrap.min.js')
      script(src='/javascripts/jquery-3.1.0.min.js')
  body
    block content
    block foot
      .container.navbar-fixed-bottom.panel-footer
        footer.footer
          p © Taller de Aplicaciones en Plataformas Moviles | IIC3380 - Sección 1 | 2' 2016
```


Para extender la plantilla, hay que crear una nueva plantilla como Pug y usar Extends
```javascript
extends layout.pug

block scripts
  script(src='/jquery.js')
  script(src='/pets.js')

block content
  .container
    .jumbotron
      h1 Pug usando variables
        p
          | Aplicacion de Ejemplo de NodeJS con Express y Jade.

    h2 Lista de Pets
    - var pets = ['cat', 'dog']
    ul.list-group
      each petName in pets
        include pet.pug
```

## Case ##
La declaración de _Case_ es similar al  switch de JavaScript.
```javascript
extends layout.pug

block content
    .container
        .jumbotron
            h1=title
                p=message+' y '+title


            h2 Case
            - var friends = 10
            case friends
                when 0
                    p you have no friends
                when 1
                    p you have a friend
                default
                    p you have #{friends} friends

            h2 Case Fall Through
            - friends = 0
            case friends
                when 0
                when 1
                    p you have very few friends
                default
                    p you have #{friends} friends

            h2 Case with break
            - var friends = 0
            case friends
                when 0
                    - break
                when 1
                    p you have very few friends
                default
                    p you have #{friends} friends
```
Las condiciones son atendidas segun el valor del when, tambien se puede utilizar el break para cuando no se desea renderizar nada para un dado valor

## Code ##

Pug hace que sea posible escribir código JavaScript en línea en sus plantillas. Hay tres tipos de código

### Unbuffered Code ###
Empeza con **-** y no anade ninguna salida
```javascript
- list = ["Uno", "Dos", "Tres", "Cuatro", "Cinco", "Seis"]
each item in list
  li= item
```

### Buffered Code ###
Empeza con **=** como salida va el resultado de la evaluación de la expresión de JavaScript en la plantilla. Para mayor seguridad, es la primera de escape HTML
```javascript
p= 'This code is' + ' <escaped>!'
```


### Unescaped Buffered Code ###
comienza con! = y envía el resultado de la evaluación de la expresión de JavaScript en la plantilla. Esto no hace ningún escape, por lo que no es seguro para la entrada del usuario
```javascript
p
  != 'This code is <strong>not</strong> escaped!'
```

## Conditionals ##
Pug incorpora sintaxis condicional de primera clase que permite omitir la carga del contenido.

### If ... Else ###
Se puede utilizar el condicional **if ... else** para controlar la renderización 

```javascript
- var user = { name:'quelves', description: 'foo bar baz' }
- var authorised = false
#user
  if user.description
    h2.green Description
    p.description= user.description
  else if authorised
    h2.blue Description
    p.description.
      User has no description,
      why not add one...
  else
    h2.red Description
    p.description User has no description
```

### unless ###

Se puedo utilizar **unless** para negar una condición

```javascript
unless user.isAnonymous
  p You're logged in as #{user.name}
```

## Filters ##

Los filtros permiten usar otros idiomas dentro de una plantilla Pug, todos los módulos JSTransformer se pueden usar como filtros de Pug. filtros populares incluyen :babel, :uglify-js, :scss, y :markdown-it

Para usar markdown
```shellscript
npm install --save jstransformer-markdown-it
```

En la plantilla PUG usar
```js
:markdown-it(linkify langPrefix='highlight-')
  # Markdown

  Markdown document with http://links.com and


```

##  Interpolation ##
Pug prove los operadores para una variedad de sus necesidades diferentes de interpolación

### String Interpolation, Escaped ###
Tenga en cuenta la ubicación de los locales **title, author, and theGreat** de la plantilla, title, author, and theGreat en la siguiente plantilla
```js
- var title = "On Dogs: Man's Best Friend";
- var author = "enlore";
- var theGreat = "<span>escape!</span>";

h1= title
p Written with love by #{author}
p This will be safe: #{theGreat}
```

**title** sogie el padron básico para evaluar una plantilla local, pero el código entre **#{ and }** es evaluada, aplicado el escape y el resultado es bufferizado en la plantilla


### String Interpolation, Unescaped ###
```js
- var riskyBusiness = "<em>Some of the girls are wearing my mother's clothing.</em>";
.quote
  p Joel: !{riskyBusiness}
```


### Tag Interpolation  ###
Se puede utilizar tag interpolation
```js
p.
  This is a very long and boring paragraph that spans multiple lines.
  Suddenly there is a #[strong strongly worded phrase] that cannot be
  #[em ignored].
```


### Whitespace Control ###
La sintaxis de la etiqueta de interpolación es especialmente útil para las etiquetas en línea, donde los espacios en blanco antes y después de la etiqueta es significativa
```js
p
  | If I don't write the paragraph with tag interpolation, tags like
  strong strong
  | and
  em em
  | might produce unexpected results.
p.
  If I do, whitespace is #[strong respected] and #[em everybody] is happy.
```

## Mixins ##
Mixins permite crear bloques reutilizables
```js
//- Declaration
mixin list
  ul
    li foo
    li bar
    li baz
//- Use
+list
+list

mixin pet(name)
  li.pet= name
ul
  +pet('cat')
  +pet('dog')
  +pet('pig')

```

### Mixin Blocks ###
Mixins también puede tener un bloque de barro para que actúe como el contenido
```js
mixin article(title)
  .article
    .article-wrapper
      h1= title
      if block
        block
      else
        p No content provided

+article('Hello world')

+article('Hello world')
  p This is my
  p Amazing article
```

### Mixin Attributes ###
Mixins también obtener un argumento implícito atributos tomados de los atributos pasados ​​al mixin
```js
mixin link(href, name)
  //- attributes == {class: "btn"}
  a(class!=attributes.class href=href)= name

+link('/foo', 'foo')(class="btn")
```

### Rest Arguments ###
Puede escribir mixins que tienen un número indeterminado de argumentos usando la sintaxis "rest arguments", p.ej.
```js
mixin list(id, ...items)
  ul(id=id)
    each item in items
      li= item

+list('my-list', 1, 2, 3, 4)
```


## Build Pug Project ##

Crear proyecto
```javascript
mkdir pug_ejemplo
cd pug_ejemplo
```

Inicializar el proyecto
```javascript
npm init
```

Adicionar dependencia de express
```javascript
npm install express --save
```

Adicionar dependencia de jade
```javascript
npm install pug --save
```

Adicionar dependencia para logger
```javascript
npm install morgan --save
```

Definir el template engine
```javascript
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'pug');
```

Definir rutas
```javascript
var routes = require('./routes/index');
app.use('/', routes);
```

Plantilla con Jade en views
```javascript
html
  head
    title!= title
  body
    h1!= message
```

Definición de rutas en routs
```javascript
var express = require('express');
var router = express.Router();

router.get('/', function (req, res) {
  res.render('index', { title: 'Pug Ejemplo', message: 'Pug Ejemplo - Fue pasado por parametro desde el router!'});
});


module.exports = router;
```

Iniciar Servidor
```javascript
npm start
```

Probar plantilla
```javascript
http://localhost:3000/
```



## Mustache ##

Mustache es un _logic-less template syntax_. Puede ser utilizado para HTML, archivos de configuración, código fuente y etc. Funciona mediante tags que son expandidas utilizando los valores proporcionados en un hash o un objeto

Mustache.js es una implementación del sistema de plantillas [Mustache] (Mustache) en JavaScript.

Lo llamamos "logic-less" porque no existen declaraciones IF ni otras estructuras lógicas. En su lugar sólo hay etiquetas. Algunas etiquetas se reemplazan con un valor, algunos nada, y otros una serie de valores.

```js
<div class="post">
  <h1>By {{fullName author}}</h1>
  <div class="body">{{body}}</div>
 
  <h1>Comments</h1>
 
  {{#each comments}}
    <h2>By {{fullName author}}</h2>
    <div class="body">{{body}}</div>
  {{/each}}
</div>
1
2
3
var mustache = require('mustache');
var template = mustache.compile('string of mustache');
var result = template(locals);
```


## Handlebars ##

Handlebars proporciona la potencia necesaria que le permitirán crear plantillas semánticas con eficacia sin la frustración, es una extension de mustache.

Install Express
```js
npm install express --save
```


Install handlebars
```js
npm install express-handlebars --save
```


Crear la aplicacion y instanciar el Template Engine
```js
var express = require('express');
var app = express();
var exphbs  = require('express-handlebars');


app.engine('handlebars', exphbs({defaultLayout: 'main'}));
app.set('view engine', 'handlebars');

app.get('/', function (req, res) {

  var luckyNumber = Math.round(Math.random() *10);

  res.render('home', {
    luckyNumber: luckyNumber
  });
});

app.get('/about', function (req, res) {
  res.render('about');
});


app.use('/public', express.static(__dirname + '/public'));

var port = Number(process.env.PORT || 5000);
app.listen(port);
```


Definir el layout
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Example App</title>
</head>
<body>

<div style="position: absolute; top: 0; height: 40px;width: 100%; background-color: #2a9fd6">
    This is header
</div>

<div style="margin-top: 60px">
    {{{body}}}
</div>

<div style="position: absolute; bottom: 0; height: 40px;width: 100%;background-color: #4cae4c ">
    This is footer
</div>

</body>
</html>

```

Crear las view para completar la informacion del layout principal 
```html
<h1>Example App: Home</h1>

Lucky Number is  {{ luckyNumber }}!
```


## EJS ##
Embedded JavaScript Templates (EJS) una solución ligera hacia la creación de HTML, 

Instalar con NPM
```
npm install ejs
```

Usando
```js
var ejs = require('ejs'),
    people = ['geddy', 'neil', 'alex'],
    html = ejs.render('<%= people.join(", "); %>', {people: people});
```

Replace 
```
<ul>
  <% users.forEach(function(user){ %>
    <%- include('user/show', {user: user}); %>
  <% }); %>
</ul>
```


## Comparación ##
Para unicar una comparación simple, se tomó en cuenta la actividad que tiene cada motor en su repositorio de desarrollo, en este resultado se puede ver que Jade es el proyecto más activo, según datos del 2016.
```
NAME	        STARS	  FORKS	  COMMITS	  CONTRIBUTORS	
Jade/Pug	    12,088	  1,624	   2,442	           176	         
Mustache.js	    9,752	  1,919	     697	            88	          
Dust.js	        2,358	    399	     882	            35	          
Nunjucks	    3,354	    296	     1,095	            96	          
EJS	            3,005	    419	     227	            23            
```

## Referencias ##
1. http://expressjs.com/es/guide/using-template-engines.html 
2. https://www.npmjs.com/package/jade
3. https://strongloop.com/strongblog/compare-javascript-templates-jade-mustache-dust/?_ga=1.249538049.782897852.1473104247
4. https://github.com/pugjs/pug
5. https://pugjs.org/api/getting-started.html
6. https://github.com/wycats/handlebars.js










