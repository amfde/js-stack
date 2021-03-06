# Criando Rotas para um item

No último capítulo, gerenciamos um conjunto de rotas para o _endpoint_ ```/users```. Agora, vamos criar rotas com parâmetros como o ```id``` do usuário.

O que precisarémos para essa é: quando a requisição for para ```/users/:id```;

- Returnar o usuário
- Atualizar o usuário
- Apagar o usuário

O ```:id``` vindo na requisição pode ser acessado graças ao pacote ```body-parser``` que configuramos anteriormente.

## Retornando o usuário: GET ```api/users/:id```

Vamos adicionar essa rota, no ```index.js```, para lidar com requisições com o parâmetro ```:id```.

```js
// rotas terminadas em /users/:id
// ----------------------------------------------------
apiRouter.route('/users/:id')
  // retorna o usuário com o id (GET http://localhost:8000/api/users/:id)
  .get(function (req, res) {
    User.findById(req.params.id, function (err, user) {
      if (err) res.send(err)
      // retorna o usuário
      res.json(user)
    })
  })
```

Em nosso _endpoint_ que lista os usuários, todos têm um id único. Vamos pegar um desses ids e testar nosso novo _endpoint_ fazendo uma requisição como essa:

```bash
 > http GET localhost:8000/api/users/58f10688a2382ab2f06e5030
HTTP/1.1 200 OK
{
  "__v": 0,
  "_id": "58f10688a2382ab2f06e5030",
  "name": "Antonio Milesi",
  "username": "milesibastos"
}
```

Vamos receber o usuário como resposta e no outro terminal que está rodando o serviço ```node index.js``` vamos perceber o _log_ da requisição parecido com ```GET /api/users/58f10688a2382ab2f06e5030 200 36.884 ms - 85```.

## Atualizando o usuário: PUT ```api/users/:id```

Agora, vamos adicionar uma nova rota ao ```router.route()``` para lidar com requisições PUT.

```js
// rotas terminadas em /users/:id
// ----------------------------------------------------
apiRouter.route('/users/:id')
  // retorna o usuário com o id (GET http://localhost:8000/api/users/:id)
  .get(function (req, res) { ... })
  // atualiza o usuário com o id (PUT http://localhost:8080/api/users/:id)
  .put(function (req, res) {
    User.findById(req.params.id, function (err, user) {
      if (err) res.send(err)
      // atualiza as informações do usuário
      if (req.body.name) user.name = req.body.name
      if (req.body.username) user.username = req.body.username
      if (req.body.password) user.password = req.body.password

      // salva o usuário
      user.save(function (err) {
        if (err) res.send(err)
        // retorna uma menssagem de sucesso
        res.json({ message: 'Usuário atalizado!' })
      })
    })
```

Então, com o ```id``` que veio na requisição, pegamos o usuário, atualizamos as informações e salvamos ele devolta no banco MongoDB.

Vamos, então, fazer um requisição PUT como essa para atualizar os dados do usuário que acabamos de consultar.

```bash
 > http --form PUT localhost:8000/api/users/58f10688a2382ab2f06e5030 name='Antonio Milesi Bastos'
HTTP/1.1 200 OK
{
    "message": "Usuário atalizado!"
}
```

Vamos receber uma mensagem de confirmação como resposta e no outro terminal que está rodando o serviço ```node index.js``` vamos perceber o _log_ da requisição parecido com ```PUT /api/users/58f10688a2382ab2f06e5030 200 70.245 ms - 33```.

Podemos utilizar o ```http GET localhost:8000/api/users``` como antes para verificar os dados atualizados do usuário.

## Apagando o usuário: DELETE ```api/users/:id```

Para excluir um usuário, tudo que precisamos é de uma requisição DELETE para ```api/users/:id```. Então, vamos adicionar esse código para apagamos um usuário.

```js
// rotas terminadas em /users/:id
// ----------------------------------------------------
apiRouter.route('/users/:id')
  // retorna o usuário com o id (GET http://localhost:8000/api/users/:id)
  .get(function (req, res) { ... })
  // atualiza o usuário com o id (PUT http://localhost:8080/api/users/:id)
  .put(function (req, res) { ... })
  // apaga o usuário com o id (DELETE http://localhost:8080/api/users/:id)
  .delete(function (req, res) {
    User.remove({ _id: req.params.id }, function (err, user) {
      if (err) return res.send(err)
      res.json({ message: 'Apagado com sucesso!' })
    })
  })
```

Agora, enviando uma requisição como essa, vamos apagar o usuário do banco MongoDB.

```bash
 > http DELETE localhost:8000/api/users/58f10688a2382ab2f06e5030
HTTP/1.1 200 OK
{
    "message": "Apagado com sucesso!"
}
```

Então, vamos receber uma mensagem de confirmação como resposta e no outro terminal que está rodando o serviço ```node index.js``` vamos perceber o _log_ da requisição parecido com ```DELETE /api/users/58f10688a2382ab2f06e5030 200 20.552 ms - 34```.

Também podemos utilizar o ```http GET localhost:8000/api/users``` como antes para verificar que o usuário foi removido do banco.
