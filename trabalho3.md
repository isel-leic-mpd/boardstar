# Enunciado do Trabalho 3

**Objectivos**: Prática com API assíncrona, `CompletableFuture<T>` e _reactive streams_.
<br>
**Data limite de entrega**: ~~1 de Junho~~ **15 de Junho de 2019**

Implemente os testes unitários necessários para validar o
funcionamento das funcionalidades pedidas.

**NOTA 1**: não poderá criar ou usar explicitamente fios de execução (i.e.
`Thread`), nem por diferimento de tarefas (i.e.
`CompletableFuture.supplyAsync(...)`) nem através de qualquer outro meio.

**NOTA 2**: não poderá bloquear sobre o resultado das computações assíncronas
(i.e. `.join()` ou `.get()`) com excepção aos testes unitários.

## Parte 0

No âmbito do  projecto **boardstar** pretende-se tornar a sua API assíncrona. Deverá
criar um novo módulo `boardstar-reactive` com base no anterior e adaptá-lo de
acordo com os requisitos deste enunciado.

## Parte 1

De modo a que o módulo `boardstar-reactive` passe a usar IO não-bloqueante, crie uma
nova interface `AsyncRequest` com um método `getBody()` que tenha API
assíncrona. 
A implementação desta interface para pedidos HTTP GET deve recorrer a uma
biblioteca para realização de pedidos HTTP não bloqueantes, como por exemplo
[AsyncHttpClient](https://github.com/AsyncHttpClient/async-http-client) ou
[java.net.http](https://docs.oracle.com/en/java/javase/11/docs/api/java.net.http/java/net/http/HttpClient.html)
do JDK 11.

Os métodos de `BgaWebApi` devem passar a retornar resultados na forma de 
`CompletableFuture<List<...>>`.

Os tipos do modelo de domínio e serviço devem ser actualizados para que passem
a oferecer uma API assíncrona baseada no tipo
[Observable](http://reactivex.io/RxJava/javadoc/io/reactivex/Observable.html)
sempre que faça sentido.

Os métodos `searchBy...()` de `BoardstarService` e `getGames()` do modelo de
domínio, devem passar a receber um parâmetro inteiro que determina o número
total de elementos da sequência retornada, permitindo assim que possam ser
desencadeados  pedidos concorrentes à `BgaWebApi`.
Ou seja, se for chamado o método `searchBy...(..., 270 )` de `BoardstarService`
e se por sua vez `BgaWebApi` retornar resultados em grupos de 30 elementos,
então serão feitos pedidos a `BgaWebApi` para as páginas de 1 a 9. 

## Parte 2

Implemente uma aplicação Web usando a tecnologia
com suporte para handlers assíncronos, como por exemplo 
[javalin](https://javalin.io/),
[VertX](https://vertx.io/docs/vertx-web/java/), 
[Spring Web Flux](https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html), 
ou outro.

A aplicação deve disponibilizar as seguintes páginas:

1.  Listagem de todas as categorias de jogos.
    Cada categoria tem um link para a listagem de jogos dessa categoria
    (página 2).
2.  Listagem de jogos de uma categoria ou de um artista.
    Cada jogo tem 2 links: um para a listagem dos seus artistas (página 3) e outro para a
    listagem das suas categorias (página 1 com query-string das categorias a apresentar).
    O número de items apresentados nesta página pode ser limitado por um
    parâmetro de _query-string_.
3.  Listagem de artistas de um jogo. Cada artista tem um link para a listagem
    dos seus jogos (página 2).

As páginas anteriores são acessíveis através dos seguintes caminhos (paths): 

1.	`/categories`
2.	`/categories/:id/games` ou `/artists/:id/games`
3.	`/games/:id/artists`

A aplicação web **nunca poderá bloquear** (não fazer `join()` e nem `get()`) na
obtenção de um resultado. 

As listagens devem ser retornadas no corpo da reposta HTTP em modo chunked
(`response.setChunked(true)`) sendo a user-interface construída de forma
progressiva à medida que a resposta é recebida no browser.

Por exemplo, o pedido a `/categories/eX8uuNlQkQ/games/` retorna cerca de 300 jogos que 
devem ser apresentados progressivamente à medida que são obtidos os resultados da 
Web API do Board Game Atlas.