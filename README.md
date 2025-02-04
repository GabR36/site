Site estático feito com o [Hugo framework](https://gohugo.io/)

<https://gabch.top>

## Instruções para postar no site

- Clone o repositório
- Instale o Hugo
- Modifique o arquivo hugo.toml para incluir o seu nome, como:
```
  [author.teste]
    name = "teste jr"
    email = "test@gmail.com"
```
- Faça um novo post com o comando:
```
hugo new content posts/meupost.md
```
- Acesse o arquivo markdown criado em contents/posts/, modifique o
  cabeçalho como quiser, coloque o seu indentificador em authors, como
  authors = ['teste']
- escreva seu post, com formatação markdown
- escreva sua página de autor com:
```
hugo new content authors_page/<indentificador>.md
```
- Faça um commit e um pull request, se estiver tudo certo irei aceitar e
  o site vai atualizar com o seu post.