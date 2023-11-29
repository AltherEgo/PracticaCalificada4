# PracticaCalificada4
###P1
```
#!/usr/bin/env node

const fs = require('fs').promises;
const path = require('path');
const { promisify } = require('util');
const readFile = promisify(fs.readFile);
const { ArgumentParser } = require('argparse');

async function grep(searchTerm, options) {
  const result = [];

  for (const file of options.files) {
    try {
      const content = (await readFile(file, 'utf8')).split('\n');

      for (let i = 0; i < content.length; i++) {
        const line = content[i];

        if (
          (options.ignoreCase && line.toLowerCase().includes(searchTerm.toLowerCase())) ||
          (!options.ignoreCase && line.includes(searchTerm))
        ) {
          if (options.onlyFileNames) {
            result.push(file);
            break;
          } else {
            const lineNumber = options.showLineNumbers ? `${i + 1}:` : '';
            const matchedLine = options.showOnlyMatching ? line : `${lineNumber}${line}`;
            result.push(`${file}:${matchedLine}`);
          }
        } else if (options.invertMatch) {
          result.push(`${file}:${i + 1}:${line}`);
        }
      }
    } catch (error) {
      result.push(`Error reading ${file}: ${error.message}`);
    }
  }

  return result.join('\n');
}

function parseCommandLineArgs() {
  const parser = new ArgumentParser({
    description: 'A simple implementation of the grep command in Node.js.',
  });

  parser.add_argument('searchTerm', { help: 'The term to search for.' });
  parser.add_argument('files', { nargs: '+', help: 'The files to search in.' });

  parser.add_argument('-i', '--ignore-case', { action: 'store_true', help: 'Ignore case distinctions.' });
  parser.add_argument('-l', '--only-file-names', { action: 'store_true', help: 'Print only names of files with matching lines.' });
  parser.add_argument('-n', '--show-line-numbers', { action: 'store_true', help: 'Show line numbers with output lines.' });
  parser.add_argument('-x', '--show-only-matching', { action: 'store_true', help: 'Show only the part of a line matching the search term.' });
  parser.add_argument('-v', '--invert-match', { action: 'store_true', help: 'Invert the sense of matching, to select non-matching lines.' });

  return parser.parse_args();
}

async function main() {
  const options = parseCommandLineArgs();
  const result = await grep(options.searchTerm, options);
  console.log(result);
}

main();
```
###P2
```
const tiposPokemon = ["Agua", "Fuego", "Planta", "Eléctrico", "Psíquico"];

const movimientosPokemon = ["Placaje", "Lanzallamas", "Rayo Solar", "Rayo", "Psíquico"];


function getRandomElement(array) {
  const randomIndex = Math.floor(Math.random() * array.length);
  return array[randomIndex];
}



class Pokemon {
    constructor(HP, attack, defense) {
      this.HP = HP;
      this.attack = attack;
      this.defense = defense;
      this.move = getRandomElement(movimientosPokemon);
      this.level = 1;
      this.type = getRandomElement(tiposPokemon);
    }
  
    flight() {
        if (!this.move) {
      //console.log("no tiene volar")
      throw new Error("No tiene movimientos");
        }
    }
  
    canFly() {
      if (!this.type) {
        throw new Error("Tipo no especificado");
      }
  
      return "Puede volar?"+this.type.includes("flying");
    }
  }
  
  // Class Charizard, inheriting from Pokemon
  class Charizard extends Pokemon {
    constructor(HP, attack, defense, move) {
      super(HP, attack, defense);
      this.move = move;
      this.type = "fire/flying";
    }
  
    fight() {
      if (this.move) {
        console.log(`Using move: ${this.move}`);
        return this.attack;
      } else {
        throw new Error("No move specified for fight");
      }
    }
  }
  console.log("Charizard");
  const charizard = new Charizard(100, 40, 50, "Puño Fuego");
  console.log(charizard.fight());
  console.log(charizard.canFly());
  const bulbasaur = new Pokemon(100, 40, 50);
  console.log("bulbasaur")
  console.log(bulbasaur.flight());
  console.log(bulbasaur.canFly());
```

```
Charizard
Using move: Puño Fuego
40
Puede volar?true
bulbasaur
undefined
Puede volar?false


```

###P3
El principio de inversión de dependencia establece que los módulos de alto nivel no deberían
depender de los módulos de bajo nivel, y tanto los módulos de alto nivel como los de bajo nivel
deberían depender de abstracciones. También establece que las abstracciones no deberían
depender de implementaciones concretas, sino que las implementaciones concretas deberían
depender de abstracciones.
Una implementación concreta del principio de inversión de dependencia es la inyección de
dependencia, o la idea de que todo de lo que depende un objeto debe pasarse al objeto, para
permitir la máxima flexibilidad. Ruby no requiere inyección de dependencia tanto como otros
lenguajes de programación debido a su flexibilidad al permitir métodos singleton en casi todos
los objetos. Sin embargo, la inyección de dependencia todavía se puede utilizar en Ruby y
Digamos que tienes una clase CurrentDay para representar el día actual y deseas tener un
método work_hours que devuelva las horas de trabajo para el día actual y método workday?
que devuelve si el día actual es un día laborable o no laborable. En tu aplicación, ya tienes una
clase MonthlySchedule que conoce el horario de trabajo para un mes determinado, que
inicializa con el año y el mes. Aquí hay un enfoque simple para implementar esta clase:
class CurrentDay
def initialize
@date = Date.today
@schedule = MonthlySchedule.new(@date.year,
@date.month)
end
def work_hours
@schedule.work_hours_for(@date)
end
def workday?
!@schedule.holidays.include?(@date)
end
end
¿Cuál es el problema con este enfoque dado, cuando quieres probar el metodo workday?.

**Respuesta**
El problema está en crear diferentes instancias de workday, esto se puede abordar pasando por parametro un objeto o función

Utiliza la inyección de dependencia aplicado al siguiente código.
before do
Date.singleton_class.class_eval do
alias_method :_today, :today
define_method(:today){Date.new(2020, 12, 16)}
end
end
after do
Date.singleton_class.class_eval do
alias_method :today, :_today
remove_method :_today
end
end
¿Qué sucede en JavaScript con el DIP en este ejemplo?
Javascript no tiene las restricciones que tiene ruby por esa razón el uso de DIP varía. Sin embargo, tambien se puede usar como parametros funciones 
u objetos con tal de preservar el Principio de Inversión de Dependencia


##P4
a. Modifica la vista Index para incluir el número de fila de cada fila en la tabla de películas

La vista por defecto 
![imagen](https://github.com/AltherEgo/PracticaCalificada4/assets/119552157/224cf274-4d68-440c-818d-4bb3c2a8cffa)

se requiere darle una enumeración para eso se puede utilizar el método with_index y usarlo en el bucle de listado

```
<%- @movies.each.with_index(1) do |movie, index| %>

```

con lo que el código final nos queda 
```
<h1>All Movies</h1>

<%= link_to 'Add Movie', new_movie_path, :class => 'btn btn-primary' %>

<div id="movies">
  <div class="row">
    <div class="col-1">n</div> // numero de fila
    <div class="col-6">Movie Title</div>
    <div class="col-2">Rating</div>
    <div class="col-2">Release Date</div>
  </div>
  <%- @movies.each.with_index(1) do |movie, index| %>
    <div class="row">
      <div class="col-1"><%= index %></div> // numero de fila
      <div class="col-6"> <%= link_to movie.title, movie_path(movie), data: { method: 'get' } %> </div>
      <div class="col-2"> <%= movie.rating %></div>
      <div class="col-2"> <%= movie.release_date.strftime('%F') %> </div>
    </div>
  <% end %>
</div>
```
Quedando de la siguiente manera:

![imagen](https://github.com/AltherEgo/PracticaCalificada4/assets/119552157/0f26f438-6953-4768-875a-94916e400386)

b. Modifica la vista Index para que cuando se sitúe el ratón sobre una fila de la tabla, dicha
fila cambie temporalmente su color de fondo a amarillo u otro color.

Lo que nos da la siguiente vista:

Para aplicar esta función usaremos nuestro archivo css con el método hover en nuestra fila en la que coloquemos nuestro cursor

Modificamos y aplicamos el estilo en application.css

```css
.row:hover {
  background-color: yellow;
}
```

c.	Modifica la acción Index del controlador para que devuelva las películas ordenadas alfabéticamente por título, en vez de por fecha de lanzamiento. No intentes ordenar el resultado de la llamada que hace el controlador a la base de datos. Los gestores de bases de datos ofrecen formas para especificar el orden en que se quiere una lista de resultados y, gracias al fuerte acoplamiento entre ActiveRecord y el sistema gestor de bases de datos (RDBMS) que hay debajo, los métodos find y all de la biblioteca de ActiveRecord en Rails ofrece una manera de pedirle al RDBMS que haga esto.

Podemos usar en el método index en nuestro modelo Movie el método order para ordenarlo según algún atributo en este caso tittle, este utiliza la base de datos para poder hacer la busqueda según 
```
  def index
    @movies = Movie.order(:title)
  end

```


###P4
```javascript
class User {
  #password;

  constructor(username, password) {
    this.#password = password;
  }

  checkPassword(inputPassword) {
    return inputPassword === this.#password;
  }
}

const user = new User("EGO", "abc");

console.log(user.checkPassword("abc")); 
console.log(user.checkPassword("321")); 
```
la clausara que se creó localmente #password nos permite realizar esta encapsulación permitiendonos verificar la contraseña 
![imagen](https://github.com/AltherEgo/PracticaCalificada4/assets/119552157/1fa37344-d03e-4758-9532-25c2f161e3c3)

