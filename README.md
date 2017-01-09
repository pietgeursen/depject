# depject

minimal dependency injection - a second opinion

## module motivation
See Dominic Tarr's [depject](github.com/dominictarr/depject) original for the inspiration for the module.

Seeing as depject is about managing opinions, I'm gonna go ahead and have an opinion about how to do depject. ;)

The existing depject module puts the responsibility for how the dependencies are combined on the original module author. 
My [reckon](https://www.youtube.com/watch?v=OQnd5ilKx2Y) is that responsibility lies with future authors. 

It also untangles the actual dependency injection from the decisions about HOW to combine them.  

## patterns

## api

Each module is an object which exposes `{needs, gives, create}` properties.
`needs` and `gives` describe the module features that this module requires,
and exports.

`needs` Is a string name of it's export, or if there are multiple exports an object where each key is a name `{<name>: true,...}`. Only ever wants one value for a dependency (an array is still a value).

`gives` Is a string name of it's export, or if there are multiple exports an object where each key is a name `{<name>: true,...}`. If there are multiple modules that `gives` opinions about the same thing then they must be combined down into one opinion before being passed to `combine`.

`create` is a function that is called with an object connected to modules which provide
the `needs` and must return a value which provides the `gives` or an object with keys that match what the module gives. 

`combine` Takes an array of objects that have the keys `create` (mandatory), `gives` (optional) and `needs` (optional).


## Example - Decorating an opinion

```js
var combine = require('depject')
var pipe = require('depject/pipe')

var hi = {
  gives: 'hello', 
  create: function () {
    return function (name) {
      return (name)
    }
  }
}

var capitalize = {
  gives: 'hello',
  create: function () {
    return function (name) {
      return name.toUpperCase()
    }
  }
}

var greet = {
  gives: 'hello',
  create: function () {
    return function (name) {
      return 'Hello, '+name
    }
  }
}

//reduce all the `hello` modules down to one `hello` module. 
var hello = reduce('hello', [hi, capitalize, greet]) //order would matter

combine([hello]).hello[0]('dominic')
```
## Example - Combining multiple opinions 

```js
var combine = require('depject')
var pipe = require('depject/flatten')

var english = {
  gives: 'greetings', 
  create: function (sockets) {
    return (function (name) {
      return `hello ${name}`
    }
  }
}

var es = {
  gives: 'greetings', 
  create: function (sockets) {
    return (function (name) {
      return `hola ${name}`
    }
  }
}

// flatten all the greetings into one array, the equivialent of map 
var greetings = flatten('greetings', [english, es]) //order would matter

combine([greetings])[0]('dominic')
```
## License

MIT
