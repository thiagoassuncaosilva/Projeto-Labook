
# Projeto Labook

O Labook é uma rede social com o objetivo de promover a conexão e interação entre pessoas. Quem se cadastrar no aplicativo poderá criar e curtir publicações.<br>

## Conteúdos abordados

- NodeJS
- Typescript
- Express
- SQL e SQLite
- Knex
- POO
- Arquitetura em camadas
- Geração de UUID
- Geração de hashes
- Autenticação e autorização
- Roteamento
- Postman


# Banco de dados
![projeto-labook (2)](https://user-images.githubusercontent.com/29845719/216036534-2b3dfb48-7782-411a-bffd-36245b78594e.png)


# Lista de requisitos

- Endpoints
    - [ ]  signup
    - [ ]  login
    - [ ]  create post
    - [ ]  get post
    - [ ]  edit post
    - [ ]  delete post
    - [ ]  like / dislike post

- Autenticação e autorização
    - [ ]  identificação UUID
    - [ ]  senhas hasheadas com Bcrypt
    - [ ]  tokens JWT
 
 - Código
    - [ ]  POO
    - [ ]  Arquitetura em camadas
    - [ ]  Roteadores no Express

## Métodos
Requisições para a API devem seguir os padrões:
| Método | Descrição |
|---|---|
| `GET` | Retorna informações de um ou mais registros. |
| `POST` | Utilizado para criar um novo registro ou acesso a Login. |
| `PUT` | Atualiza dados de um registro ou altera sua situação. |
| `DELETE` | Remove um registro do sistema. |


## Respostas

| Código | Descrição |
|---|---|
| `200` | Requisição executada com sucesso (success).|
| `201` | Dados creado com sucesso(sucess).|
| `400` | Erros de validação ou os campos informados não existem no sistema.|
| `404` | Registro pesquisado não encontrado (Not found).|
| `500` | Erro inesperado.|


## Uso

Uma API onde as pessoas se conectam e interagem entre si.

Para iniciar o uso da API primeiramente entre no labook.sql na pasta database, crie a tabela users e insira o conteúdo, faça o mesmo com a tabela post. A tabela likes_dislikes deve ser criada, porém não insira nenhum dado nessa tabela, se popular essa tabela acarretará em erros no momento de inserir like e dislike no post.  

# Exemplos de requisição
Não precisa cadastrar o mesmo nome, email e quaisquer outros valores vistos aqui nos exemplos de saída. Porém, lembre-se de respeitar a estrutura pedida no banco de dados (nome das tabelas e colunas) e os nomes das propriedades na resposta da API.

Colunas a mais na tabela não tem problema, por exemplo adicionar uma 'category' dentro da tabela 'products', mas a falta de uma coluna ou propriedade na resposta será considerada falha de implementação!

## Signup
Endpoint público utilizado para cadastro. Devolve um token jwt.
```typescript
// request POST /users/signup
// body JSON
{
   "name": "Andrea", 
   "email": "andrea@email.com", 
   "password": "Andre@123"
}

// response
// status 201 CREATED
{
  token: "um token jwt"
}
```

## Login
Endpoint público utilizado para login. Devolve um token jwt.
```typescript
// request POST /users/login
// body JSON
{
  "email": "andrea@email.com",
  "password": "Andre@123"
}

// response
// status 200 OK
{
  token: "um token jwt"
}
```

## Create post
Endpoint protegido, requer um token jwt para acessá-lo.
```typescript
// request POST /posts
// headers.authorization = "token jwt"
// body JSON
{
    "content": "Partiu ... estudar!"
}

// response
// status 201 CREATED
```

## Get posts
Endpoint protegido, requer um token jwt para acessá-lo.
```typescript
// request GET /posts
// headers.authorization = "token jwt"

// response
// status 200 OK
[
    {
       "id": "db704ae1-18c8-4895-9f54-165af46f66e6",
        "content": "To cansado e com o corpo doendo!!",
        "likes": 0,
        "dislikes": 0,
        "createdAt": "2023-08-20T19:35:38.841Z",
        "updatedAt": "2023-08-20T19:37:22.265Z",
        "creator": {
            "creatorId": "8bc4ea9c-b511-4c14-bedd-16df2a077485",
            "creatorName": "Anderson"
        }
    },
    {
        "id": "uma uuid v4",
        "content": "kkkkkkkkkrying",
        "likes": 0,
        "dislikes" 0,
        "createdAt": "2023-08-20T15:41:12:000Z"
        "updatedAt": "2023-08-20T15:49:55:000Z"
        "creator": {
            "id": "uma uuid v4",
            "name": "Ciclana"
        }
    }
]
```

## Edit post
Endpoint protegido, requer um token jwt para acessá-lo.<br>
Só quem criou o post pode editá-lo e somente o conteúdo pode ser editado.
```typescript
// request PUT /posts/:id
// headers.authorization = "token jwt"
// body JSON
{
    "content": "Partiu ... Labenu!"
}

// response
// status 200 OK
```

## Delete post
Endpoint protegido, requer um token jwt para acessá-lo.<br>
Só quem criou o post pode deletá-lo. Admins podem deletar o post de qualquer pessoa.

```typescript
// request DELETE /posts/:id
// headers.authorization = "token jwt"

// response
// status 200 OK
```

## Like or dislike post (mesmo endpoint faz as duas coisas)

Endpoint protegido, requer um token jwt para acessá-lo.<br>
Quem criou o post não pode dar like ou dislike no mesmo.<br><br>
Caso dê um like em um post que já tenha dado like, o like é desfeito.<br>
Caso dê um dislike em um post que já tenha dado dislike, o dislike é desfeito.<br><br>
Caso dê um like em um post que tenha dado dislike, o like sobrescreve o dislike.<br>
Caso dê um dislike em um post que tenha dado like, o dislike sobrescreve o like.
### Like (funcionalidade 1)
```typescript
// request PUT /posts/:id/like
// headers.authorization = "token jwt"
// body JSON
{
    "like": true
}

// response
// status 200 OK
```

### Dislike (funcionalidade 2)
```typescript
// request PUT /posts/:id/like
// headers.authorization = "token jwt"
// body JSON
{
    "like": false
}

// response
// status 200 OK
```

### Para entender a tabela likes_dislikes
- no SQLite, lógicas booleanas devem ser controladas via 0 e 1 (INTEGER)
- quando like valer 1 na tabela é porque a pessoa deu like no post
    - na requisição like é true
    
- quando like valor 0 na tabela é porque a pessoa deu dislike no post
    - na requisição like é false
    
- caso não exista um registro na tabela de relação, é porque a pessoa não deu like nem dislike
- caso dê like em um post que já tenha dado like, o like é removido (deleta o item da tabela)
- caso dê dislike em um post que já tenha dado dislike, o dislike é removido (deleta o item da tabela)


## Documentação Postman

![Documentação][Documentação]

[Documentação]: /Documentação/document.jpg
