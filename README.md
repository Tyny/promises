# Promises

Para tener un entendimiento claro y preciso de que es una Promise primero debemos tener claro que es el codigo asincronico, una vez que tengamos un mejor entendimiento de que es el event loop y el callback queue en javascript nos vamos a meter a fondo en que es una Promise, nos vamos a centrar especialmente en Promises en javascript, tengamos en claro que existen implementaciones de Futures o Promises en otros lenguajes y que la forma de implementar codigo asincronico puede variar entre plataformas

> Si queres entender un poco mas sobre asincronismo y event loop mirate este => https://www.youtube.com/watch?v=8aGhZQkoFbQ

## Codigo sincronico (ruby :P)

````
user_id = get_user_id_from_token(token)
user = User.get(user_id)
posts = Post.get({ id: user.id, tags: user.interests })
render_view "posts", { posts: posts }
````

## Codigo asincronico

````
let userId = getUserIdFromToken(token);
User.get(userId, function(user) {
  Post.get({ id: user.id, tags: user.interests }, function(posts) {
    renderView("posts", { posts: posts });
  });
});
````

## Continuation passing style

Cuando se escribe un programa en notación CPS, cada función recibe un parámetro adicional, que representa la Continuación de la función. En lugar de retornar, la función invocará la continuación recibida pasando el valor de retorno. De esta forma, las funciones nunca regresan al código que las llamó, sino que la ejecución del programa transcurre “hacia adelante” sin retornar hasta que el programa finalice.
La notación CPS ocasiona que el tamaño del stack crezca con cada llamada a función, con el peligro de overflow que eso conlleva.

## Promise
Una `Promise` es un valor! En realidad es un poco mas, es un valor + una computacion asociada, el uso  de Promises nos permite ser un poco mas "funcionales" a la hora de programar, como vieron con CPS ninguna funcion retorna un "valor", al abstraer el asincronismo en una Promise recuperamos la capacidad de las funciones de devolver "valores".
El uso de Promises tambien "aplana" el stack evitando los problemas de CPS, cada "continuacion" es escrita luego de un "then", que ahora pasa a ser como un ";" que separa las sentencias.
Podemos pensar a la `Promise` como una "promesa" de un valor que estara disponible en el futuro, siempre luego de un `then`, de aca podemos derivar que siempre que tengamos una `Promise` en nuestro poder, alguna computacion asincronica esta sucediendo que sera resuelta en algun instante posterior.

````
let userId = getUserIdFromToken(token);
User.get(userId)
  .then(function(user) {
    return Post.get({ id: user.id, tags: user.interests })
  })
  .then(function(post) {
    renderView("posts", { posts: posts });
  });
````

## Then
El then es como un ; capaz de "esperar" por valores asincronicos, pero es capaz de tratar valores sincronicos tambien indistintamente
y ahi vive mucho de su poder para escribir codigo reusable a pesar de tener valores asincronicos.

````
  getUsers()
    .then(function(users) {
       return augmentUsersWithFriends(users);
    })
    .then(function(usersAndFriends) {
      renderView("users", { usersAndFriends: usersAndFriends })
    })
````

Aqui la implementacion de augmentUsersWithFriends puede ser 

### sincronica

````
function augmentUsersWithFriends(users) {
  return users.map((u) => { return { user: u, friends: someObjectInScope.friendsMap[users.id] } })
}
````

### asincronica

````
function augmentUsersWithFriends(users) {
  return new Promise(function(resolve, reject) {
    SomeFriendsDBService.buildMap(users, function(err, friendsMap) {
      if(err) {
        reject(err)
      } else {
        return users.map((u) => { return { user: u, friends: friendsMap[users.id] } })
      }
    })
  });
}
````

Y el codigo "cliente" de nuestra funcion `augmentUsersWithFriends` no difiere, ya que si un `then` devuelve una `Promise`
se esperara a su resolucion antes de pasar el resultado a la proxima continuacion, si en cambio el `then` lo que devuelve es un valor, este se pasara directamente la proxima continuacion sin espera alguna. (Algo de espera hay pero eso para el capitulo de `process.nextTick()`)

