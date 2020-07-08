# Promises

How javascript uses Continuation passing style to support async code?

## Sync code (ruby :P)

````
user_id = get_user_id_from_token(token)
puts "1"

user = User.get(user_id) # esta llamada es bloqueante
puts "2"

render_view "user", { user: user }
puts "3"
````

## Async code

````
let userId = getUserIdFromToken(token);

console.log("1")

User.get(userId, function(user) {
  renderView("user", { user: user });
  console.log("3")
});

console.log("2")
````

## Continuation passing style

When writing a program using CPS each function receives an additional parameter which represents the continuation of the function - none will return a "value" instead the function will invoke the received continuation passing the "return" value as an argument.


## Async in javascript

Won't try to explain this just watch this awesome video => https://www.youtube.com/watch?v=8aGhZQkoFbQ

## Promise

A `Promise` is a value! Actually it is a little more, it is a value + an associated computation - the use of Promises allows us to be a little more "functional" when programming by abstracting the asynchronism in a "value" we recover the capacity of the functions to return "values".

The use of Promises also "flattens" the stack avoiding CPS problems, each "continuation" is written after a "then", which now becomes a ";" that separates the sentences.

It is important to note that whenever we have a 'Promise' in our possession, some asynchronous computing is happening that will be resolved at some later time.

Here you can read a standard of Promises in Javascript => https://promisesaplus.com/


## Then

The then is like a `map` capable of transforming one Promise into another Promise that returns a new value

`const newArray = [1].map((v) => v + 1)`
`const newPromise = somePromise.then((v) => v + 1)`

### Challenge

The idea is to implement your own version of a Promise:

> All tests are run against the native node Promise as well so you can be sure that the tests work

1. `git clone git@github.com:Tyny/promises.git`
2. `npm install`
3. `npm test`
4. Edit `src / promise.js` and make all tests pass! Good luck!
