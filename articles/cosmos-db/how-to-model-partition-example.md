---
title: Modelar e particionar dados em Azure Cosmos DB com um exemplo do mundo real
description: Saiba como modelar e particionar um exemplo do mundo real usando a API de núcleo de Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: 10f8ffd90215a21ca03e112aea463d444c623d06
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445383"
---
# <a name="how-to-model-and-partition-data-on-azure-cosmos-db-using-a-real-world-example"></a>Como modelar e particionar dados em Azure Cosmos DB usando um exemplo do mundo real

Este artigo se baseia em vários Azure Cosmos DB conceitos como [modelagem de dados](modeling-data.md), [particionamento](partitioning-overview.md)e [taxa de transferência provisionada](request-units.md) para demonstrar como lidar com um exercício de design de dados do mundo real.

Se você geralmente trabalha com bancos de dados relacionais, provavelmente criou hábitos e intuições sobre como criar um modelo de dado. Devido às restrições específicas, mas também às forças exclusivas de Azure Cosmos DB, a maioria dessas práticas recomendadas não se traduz bem e pode arrastá-lo para soluções de qualidade inferior. O objetivo deste artigo é orientá-lo pelo processo completo de modelagem de um caso de uso real em Azure Cosmos DB, da modelagem de item até a colocação de entidade e particionamento de contêineres.

## <a name="the-scenario"></a>O cenário

Para este exercício, vamos considerar o domínio de uma plataforma de blog em que *os usuários* podem criar *postagens*. Os usuários também podem *gostar* e adicionar *comentários* a essas postagens.

> [!TIP]
> Destacamos algumas palavras em *itálico*; essas palavras identificam o tipo de "coisas" que nosso modelo terá para manipular.

Adicionando mais requisitos à nossa especificação:

- Uma página frontal exibe um feed de postagens criadas recentemente,
- Podemos buscar todas as postagens de um usuário, todos os comentários de uma postagem e todas as curtidas de uma postagem,
- As postagens são retornadas com o nome de usuário de seus autores e uma contagem de quantos comentários e curtidos eles têm,
- Comentários e curtidos também são retornados com o nome de usuário dos usuários que os criaram,
- Quando exibidas como listas, as postagens só precisam apresentar um resumo truncado de seu conteúdo.

## <a name="identify-the-main-access-patterns"></a>Identificar os principais padrões de acesso

Para começar, daremos uma estrutura à nossa especificação inicial identificando os padrões de acesso da solução. Ao criar um modelo de dados para Azure Cosmos DB, é importante entender quais solicitações nosso modelo terá de atender para garantir que o modelo sirva essas solicitações com eficiência.

Para tornar o processo geral mais fácil de seguir, categorizamos essas solicitações diferentes como comandos ou consultas, emprestando um vocabulário do [CQRS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_query_responsibility_segregation) , em que os comandos são solicitações de gravação (isto é, tentativas de atualizar o sistema) e as consultas são solicitações somente leitura.

Aqui está a lista de solicitações que nossa plataforma terá de expor:

- **[C1]** Criar/editar um usuário
- **[Q1]** Recuperar um usuário
- **[C2]** Criar/editar uma postagem
- **[Q2]** Recuperar uma postagem
- **[Q3]** Listar as postagens de um usuário em forma abreviada
- **[C3]** Criar um comentário
- **[T4]** Listar comentários de uma postagem
- **[C4]** Como uma postagem
- **[P5]** Listar as curtidas de uma postagem
- **[P6]** Listar as postagens *x* mais recentes criadas em forma abreviada (feed)

Como esse estágio, não pensamos nos detalhes do que cada entidade (usuário, posta etc.) conterá. Essa etapa geralmente está entre os primeiros a serem resolvidos durante a criação em um relational store, pois precisamos descobrir como essas entidades serão traduzidas em termos de tabelas, colunas, chaves estrangeiras, etc. É muito menos uma preocupação com um banco de dados de documentos que não impõe nenhum esquema na gravação.

O principal motivo pelo qual é importante identificar nossos padrões de acesso desde o início, é porque essa lista de solicitações será o nosso conjunto de testes. Toda vez que iteramos em nosso modelo de dados, passaremos por cada uma das solicitações e verificaremos seu desempenho e escalabilidade.

## <a name="v1-a-first-version"></a>V1: uma primeira versão

Começamos com dois contêineres: `users` e `posts`.

### <a name="users-container"></a>Contêiner de usuários

Este contêiner armazena somente itens de usuário:

    {
      "id": "<user-id>",
      "username": "<username>"
    }

Nós particionamos esse contêiner por `id`, o que significa que cada partição lógica dentro desse contêiner conterá apenas um item.

### <a name="posts-container"></a>Contêiner de postagens

Este contêiner hospeda postagens, comentários e gosta:

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "title": "<post-title>",
      "content": "<post-content>",
      "creationDate": "<post-creation-date>"
    }

    {
      "id": "<comment-id>",
      "type": "comment",
      "postId": "<post-id>",
      "userId": "<comment-author-id>",
      "content": "<comment-content>",
      "creationDate": "<comment-creation-date>"
    }

    {
      "id": "<like-id>",
      "type": "like",
      "postId": "<post-id>",
      "userId": "<liker-id>",
      "creationDate": "<like-creation-date>"
    }

Nós particionamos esse contêiner por `postId`, o que significa que cada partição lógica dentro desse contêiner conterá uma postagem, todos os comentários dessa postagem e todos os curtidos dessa postagem.

Observe que introduzimos uma propriedade `type` nos itens armazenados nesse contêiner para distinguir entre os três tipos de entidades que esse contêiner hospeda.

Além disso, optamos por fazer referência a dados relacionados em vez de incorporá-los (consulte [esta seção](modeling-data.md) para obter detalhes sobre esses conceitos) porque:

- Não há limite superior para o número de postagens que um usuário pode criar,
- as postagens podem ser arbitrariamente longas,
- Não há nenhum limite máximo de quantos comentários e curtir uma postagem pode ter,
- Queremos poder adicionar um comentário ou um semelhante a uma postagem sem precisar atualizar a postagem em si.

## <a name="how-well-does-our-model-perform"></a>Quão bem o nosso modelo executa?

Agora é hora de avaliar o desempenho e a escalabilidade da nossa primeira versão. Para cada uma das solicitações identificadas anteriormente, medimos sua latência e quantas unidades de solicitação consomem. Essa medida é feita em um conjunto de dados fictícios contendo 100.000 usuários com 5 a 50 postagens por usuário e até 25 Comentários e 100 gostantes por postagem.

### <a name="c1-createedit-a-user"></a>C1 Criar/editar um usuário

Essa solicitação é simples de implementar à medida que acabamos de criar ou atualizar um item no contêiner de `users`. As solicitações se espalharão bem em todas as partições graças à chave de partição de `id`.

![Gravando um único item no contêiner usuários](./media/how-to-model-partition-example/V1-C1.png)

| **Latência** | **Encargo de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 7 ms | 5,71 RU | ✅ |

### <a name="q1-retrieve-a-user"></a>Trimestre Recuperar um usuário

A recuperação de um usuário é feita com a leitura do item correspondente do contêiner `users`.

![Recuperando um único item do contêiner usuários](./media/how-to-model-partition-example/V1-Q1.png)

| **Latência** | **Encargo de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 2 ms | 1 RU | ✅ |

### <a name="c2-createedit-a-post"></a>La Criar/editar uma postagem

Da mesma forma que **[C1]** , precisamos apenas gravar no contêiner de `posts`.

![Gravando um único item no contêiner de postagens](./media/how-to-model-partition-example/V1-C2.png)

| **Latência** | **Encargo de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 9 ms | 8,76 RU | ✅ |

### <a name="q2-retrieve-a-post"></a>Lançado Recuperar uma postagem

Começamos recuperando o documento correspondente do contêiner `posts`. Mas isso não é suficiente, de acordo com nossa especificação, também temos que agregar o nome de usuário do autor da postagem e as contagens de quantos comentários e o número de curtiram essa postagem, o que exige 3 consultas SQL adicionais a serem emitidas.

![Recuperando uma postagem e agregando dados adicionais](./media/how-to-model-partition-example/V1-Q2.png)

Cada uma das consultas adicionais filtra a chave de partição de seu respectivo contêiner, que é exatamente o que desejamos para maximizar o desempenho e a escalabilidade. Mas, eventualmente, precisamos executar quatro operações para retornar uma única postagem, então vamos aprimorá-la em uma próxima iteração.

| **Latência** | **Encargo de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 9 ms | 19,54 RU | ⚠ |

### <a name="q3-list-a-users-posts-in-short-form"></a>3o Listar as postagens de um usuário em forma abreviada

Primeiro, precisamos recuperar as postagens desejadas com uma consulta SQL que busque as postagens correspondentes a esse usuário específico. Mas também precisamos emitir consultas adicionais para agregar o nome de usuário do autor e as contagens de comentários e curtições.

![Recuperando todas as postagens de um usuário e agregando seus dados adicionais](./media/how-to-model-partition-example/V1-Q3.png)

Essa implementação apresenta muitas desvantagens:

- as consultas que agregam as contagens de comentários e curtidas precisam ser emitidas para cada postagem retornada pela primeira consulta,
- a consulta principal não filtra a chave de partição do contêiner de `posts`, levando a um fan-out e uma verificação de partição no contêiner.

| **Latência** | **Encargo de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 130 ms | 619,41 RU | ⚠ |

### <a name="c3-create-a-comment"></a>C3 Criar um comentário

Um comentário é criado com a gravação do item correspondente no contêiner de `posts`.

![Gravando um único item no contêiner de postagens](./media/how-to-model-partition-example/V1-C2.png)

| **Latência** | **Encargo de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 7 ms | 8,57 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>P4 Listar comentários de uma postagem

Começamos com uma consulta que busca todos os comentários para essa postagem e novamente, também precisamos agregar nomes de username separadamente para cada comentário.

![Recuperando todos os comentários de uma postagem e agregando seus dados adicionais](./media/how-to-model-partition-example/V1-Q4.png)

Embora a consulta principal filtre a chave de partição do contêiner, a agregação dos nomes de domínio penaliza separadamente o desempenho geral. Aprimoraremos isso posteriormente.

| **Latência** | **Encargo de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 23 ms | 27,72 RU | ⚠ |

### <a name="c4-like-a-post"></a>C4 Como uma postagem

Assim como **[C3]** , criamos o item correspondente no contêiner de `posts`.

![Gravando um único item no contêiner de postagens](./media/how-to-model-partition-example/V1-C2.png)

| **Latência** | **Encargo de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 6 ms | 7, 5 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>P5 Listar as curtidas de uma postagem

Assim como o **[T4]** , consultamos os curtidos por essa postagem e agregamos seus nomes de User.

![Recuperando todas as curtidas para uma postagem e agregando seus dados adicionais](./media/how-to-model-partition-example/V1-Q5.png)

| **Latência** | **Encargo de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 59 ms | 58,92 RU | ⚠ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>P6 Listar as postagens x mais recentes criadas em forma abreviada (feed)

Buscamos as postagens mais recentes consultando o contêiner de `posts` classificado por data de criação decrescente, depois agregam nomes de dados e contagens de comentários e curtidas para cada uma das postagens.

![Recuperando as postagens mais recentes e agregando seus dados adicionais](./media/how-to-model-partition-example/V1-Q6.png)

Mais uma vez, nossa consulta inicial não filtra a chave de partição do contêiner `posts`, que dispara um fan-out dispendioso. Essa é ainda pior, pois visamos um conjunto de resultados muito maior e classificamos os resultados com uma cláusula `ORDER BY`, o que o torna mais caro em termos de unidades de solicitação.

| **Latência** | **Encargo de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 306 ms | 2063,54 RU | ⚠ |

## <a name="reflecting-on-the-performance-of-v1"></a>Refletindo no desempenho de v1

Observando os problemas de desempenho que enfrentamos na seção anterior, podemos identificar duas classes principais de problemas:

- algumas solicitações exigem que várias consultas sejam emitidas para reunir todos os dados que precisamos retornar,
- algumas consultas não filtram a chave de partição dos contêineres que elas visam, levando a um fan-out que impede nossa escalabilidade.

Vamos resolver cada um desses problemas, começando pelo primeiro.

## <a name="v2-introducing-denormalization-to-optimize-read-queries"></a>V2: apresentando a desnormalização para otimizar consultas de leitura

O motivo pelo qual precisamos emitir solicitações adicionais em alguns casos é porque os resultados da solicitação inicial não contêm todos os dados que precisamos retornar. Ao trabalhar com um armazenamento de dados não relacional como Azure Cosmos DB, esse tipo de problema é normalmente resolvido pela desnormalização dos dados em nosso conjunto de dados.

Em nosso exemplo, modificamos itens de postagem para adicionar o nome de usuário do autor da postagem, a contagem de comentários e a contagem de curtidas:

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "userUsername": "<post-author-username>",
      "title": "<post-title>",
      "content": "<post-content>",
      "commentCount": <count-of-comments>,
      "likeCount": <count-of-likes>,
      "creationDate": "<post-creation-date>"
    }

Também modificamos comentários e itens semelhantes para adicionar o nome do usuário que os criou:

    {
      "id": "<comment-id>",
      "type": "comment",
      "postId": "<post-id>",
      "userId": "<comment-author-id>",
      "userUsername": "<comment-author-username>",
      "content": "<comment-content>",
      "creationDate": "<comment-creation-date>"
    }

    {
      "id": "<like-id>",
      "type": "like",
      "postId": "<post-id>",
      "userId": "<liker-id>",
      "userUsername": "<liker-username>",
      "creationDate": "<like-creation-date>"
    }

### <a name="denormalizing-comment-and-like-counts"></a>Desnormalizando o comentário e as contagens semelhantes

O que queremos conseguir é que, sempre que adicionarmos um comentário ou um como, também incrementaremos o `commentCount` ou o `likeCount` na postagem correspondente. Como nosso contêiner de `posts` é particionado por `postId`, o novo item (comentário ou semelhante) e sua postagem correspondente estão na mesma partição lógica. Como resultado, podemos usar um [procedimento armazenado](stored-procedures-triggers-udfs.md) para executar essa operação.

Agora, ao criar um comentário ( **[C3]** ), em vez de apenas adicionar um novo item no contêiner de `posts`, chamamos o seguinte procedimento armazenado nesse contêiner:

```javascript
function createComment(postId, comment) {
  var collection = getContext().getCollection();

  collection.readDocument(
    `${collection.getAltLink()}/docs/${postId}`,
    function (err, post) {
      if (err) throw err;

      post.commentCount++;
      collection.replaceDocument(
        post._self,
        post,
        function (err) {
          if (err) throw err;

          comment.postId = postId;
          collection.createDocument(
            collection.getSelfLink(),
            comment
          );
        }
      );
    })
}
```

Esse procedimento armazenado usa a ID da postagem e o corpo do novo comentário como parâmetros, em seguida:

- Recupera a postagem
- incrementa o `commentCount`
- Substitui a postagem
- Adiciona o novo comentário

Como os procedimentos armazenados são executados como transações atômicas, é garantido que o valor de `commentCount` e o número real de comentários sempre permaneçam em sincronia.

Obviamente, chamamos um procedimento armazenado semelhante ao adicionar novos curtidos para incrementar o `likeCount`.

### <a name="denormalizing-usernames"></a>Desnormalizando nomes de acessadores

Os nomes de usuário exigem uma abordagem diferente, pois os usuários não só ficam em partições diferentes, mas em um contêiner diferente. Quando precisamos desnormalizar dados entre partições e contêineres, podemos usar o [feed de alterações](change-feed.md)do contêiner de origem.

Em nosso exemplo, usamos o feed de alterações do contêiner de `users` para reagir sempre que os usuários atualizarem seus nomes de usuário. Quando isso acontece, propagamos a alteração chamando outro procedimento armazenado no contêiner de `posts`:

![Desnormalizando nomes de acessadores no contêiner Posts](./media/how-to-model-partition-example/denormalization-1.png)

```javascript
function updateUsernames(userId, username) {
  var collection = getContext().getCollection();
  
  collection.queryDocuments(
    collection.getSelfLink(),
    `SELECT * FROM p WHERE p.userId = '${userId}'`,
    function (err, results) {
      if (err) throw err;

      for (var i in results) {
        var doc = results[i];
        doc.userUsername = username;

        collection.upsertDocument(
          collection.getSelfLink(),
          doc);
      }
    });
}
```

Esse procedimento armazenado usa a ID do usuário e o novo nome do usuário como parâmetros, em seguida:

- busca todos os itens que correspondem à `userId` (que pode ser postagens, comentários ou curtidos)
- para cada um desses itens
  - Substitui o `userUsername`
  - Substitui o item

> [!IMPORTANT]
> Essa operação é dispendiosa porque requer que esse procedimento armazenado seja executado em cada partição do contêiner de `posts`. Supomos que a maioria dos usuários escolha um nome de usuário adequado durante a inscrição e não o altere, portanto, essa atualização será executada muito raramente.

## <a name="what-are-the-performance-gains-of-v2"></a>Quais são os ganhos de desempenho de v2?

### <a name="q2-retrieve-a-post"></a>Lançado Recuperar uma postagem

Agora que nossa desnormalização está em vigor, precisamos apenas buscar um único item para tratar essa solicitação.

![Recuperando um único item do contêiner postagens](./media/how-to-model-partition-example/V2-Q2.png)

| **Latência** | **Encargo de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 2 ms | 1 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>P4 Listar comentários de uma postagem

Novamente, podemos reservar as solicitações extras que buscavam os nomes de acessados e terminar com uma única consulta que filtra a chave de partição.

![Recuperando todos os comentários de uma postagem](./media/how-to-model-partition-example/V2-Q4.png)

| **Latência** | **Encargo de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 4 ms | 7,72 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>P5 Listar as curtidas de uma postagem

Exatamente a mesma situação ao listar as curtidas.

![Recuperando todas as curtidas para uma postagem](./media/how-to-model-partition-example/V2-Q5.png)

| **Latência** | **Encargo de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 4 ms | 8,92 RU | ✅ |

## <a name="v3-making-sure-all-requests-are-scalable"></a>V3: garantindo que todas as solicitações sejam escalonáveis

Observando nossas melhorias gerais de desempenho, ainda há duas solicitações que não foram totalmente otimizadas: **[Q3]** e **[P6]** . Elas são as solicitações que envolvem consultas que não filtram a chave de partição dos contêineres de destino.

### <a name="q3-list-a-users-posts-in-short-form"></a>3o Listar as postagens de um usuário em forma abreviada

Essa solicitação já se beneficia das melhorias introduzidas na v2, que reserva consultas adicionais.

![Recuperando todas as postagens de um usuário](./media/how-to-model-partition-example/V2-Q3.png)

Mas a consulta restante ainda não está filtrando a chave de partição do contêiner de `posts`.

A maneira de pensar sobre essa situação é realmente simples:

1. Esta solicitação *tem* que filtrar na `userId` porque queremos buscar todas as postagens de um usuário específico
1. Ele não tem um bom desempenho porque é executado no contêiner `posts`, que não é particionado por `userId`
1. Informando o óbvio, resolvemos nosso problema de desempenho executando essa solicitação em um contêiner que *é* particionado por `userId`
1. Acontece que já temos esse contêiner: o contêiner de `users`!

Então, apresentamos um segundo nível de desnormalização duplicando as postagens inteiras para o contêiner de `users`. Fazendo isso, nós efetivamente obtemos uma cópia de nossas postagens, apenas particionadas em diferentes dimensões, tornando-as mais eficientes para serem recuperadas por seus `userId`.

O contêiner de `users` agora contém 2 tipos de itens:

    {
      "id": "<user-id>",
      "type": "user",
      "userId": "<user-id>",
      "username": "<username>"
    }

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "userUsername": "<post-author-username>",
      "title": "<post-title>",
      "content": "<post-content>",
      "commentCount": <count-of-comments>,
      "likeCount": <count-of-likes>,
      "creationDate": "<post-creation-date>"
    }

Tenha em atenção que:

- Apresentamos um campo de `type` no item de usuário para distinguir os usuários de postagens,
- Também adicionamos um campo de `userId` no item de usuário, que é redundante com o campo `id`, mas é necessário, pois o contêiner `users` agora é particionado pelo `userId` (e não `id` como anteriormente)

Para obter essa desnormalização, usamos novamente o feed de alterações. Desta vez, reagem ao feed de alterações do contêiner de `posts` para distribuir qualquer postagem nova ou atualizada para o contêiner de `users`. E como a listagem de postagens não exige o retorno do conteúdo completo, podemos trunca-las no processo.

![Desnormalizar postagens no contêiner usuários](./media/how-to-model-partition-example/denormalization-2.png)

Agora, podemos rotear nossa consulta para o contêiner de `users`, filtrando a chave de partição do contêiner.

![Recuperando todas as postagens de um usuário](./media/how-to-model-partition-example/V3-Q3.png)

| **Latência** | **Encargo de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 4 ms | 6,46 RU | ✅ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>P6 Listar as postagens x mais recentes criadas em forma abreviada (feed)

Temos que lidar com uma situação semelhante aqui: mesmo após a reserva de consultas adicionais deixadas desnecessárias pela desnormalização introduzida na v2, a consulta restante não filtra a chave de partição do contêiner:

![Recuperando postagens mais recentes](./media/how-to-model-partition-example/V2-Q6.png)

Seguindo a mesma abordagem, maximizar o desempenho e a escalabilidade dessa solicitação exige que ela só atinja uma partição. Isso é concebível porque só precisamos retornar um número limitado de itens; para preencher o home page da nossa plataforma de Blogs, precisamos apenas obter as 100 postagens mais recentes, sem a necessidade de paginar todo o conjunto de dados.

Portanto, para otimizar essa última solicitação, apresentamos um terceiro contêiner para nosso design, totalmente dedicado a servir essa solicitação. Desnormalize nossas postagens nesse novo contêiner de `feed`:

    {
      "id": "<post-id>",
      "type": "post",
      "postId": "<post-id>",
      "userId": "<post-author-id>",
      "userUsername": "<post-author-username>",
      "title": "<post-title>",
      "content": "<post-content>",
      "commentCount": <count-of-comments>,
      "likeCount": <count-of-likes>,
      "creationDate": "<post-creation-date>"
    }

Esse contêiner é particionado por `type`, que sempre será `post` em nossos itens. Fazer isso garante que todos os itens nesse contêiner estarão na mesma partição.

Para obter a desnormalização, precisamos apenas conectar o pipeline do feed de alterações que apresentamos anteriormente para despachar as postagens para esse novo contêiner. Uma coisa importante a ser lembrada é que precisamos nos certificar de que armazenamos apenas as 100 postagens mais recentes; caso contrário, o conteúdo do contêiner pode crescer além do tamanho máximo de uma partição. Isso é feito chamando um [post-Trigger](stored-procedures-triggers-udfs.md#triggers) toda vez que um documento é adicionado no contêiner:

![Desnormalizando postagens no contêiner do feed](./media/how-to-model-partition-example/denormalization-3.png)

Este é o corpo do post-Trigger que trunca a coleção:

```javascript
function truncateFeed() {
  const maxDocs = 100;
  var context = getContext();
  var collection = context.getCollection();

  collection.queryDocuments(
    collection.getSelfLink(),
    "SELECT VALUE COUNT(1) FROM f",
    function (err, results) {
      if (err) throw err;

      processCountResults(results);
    });

  function processCountResults(results) {
    // + 1 because the query didn't count the newly inserted doc
    if ((results[0] + 1) > maxDocs) {
      var docsToRemove = results[0] + 1 - maxDocs;
      collection.queryDocuments(
        collection.getSelfLink(),
        `SELECT TOP ${docsToRemove} * FROM f ORDER BY f.creationDate`,
        function (err, results) {
          if (err) throw err;

          processDocsToRemove(results, 0);
        });
    }
  }

  function processDocsToRemove(results, index) {
    var doc = results[index];
    if (doc) {
      collection.deleteDocument(
        doc._self,
        function (err) {
          if (err) throw err;

          processDocsToRemove(results, index + 1);
        });
    }
  }
}
```

A etapa final é redirecionar nossa consulta para nosso novo contêiner `feed`:

![Recuperando postagens mais recentes](./media/how-to-model-partition-example/V3-Q6.png)

| **Latência** | **Encargo de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 9 ms | 16,97 RU | ✅ |

## <a name="conclusion"></a>Conclusão

Vamos dar uma olhada nos aprimoramentos gerais de desempenho e escalabilidade que apresentamos nas diferentes versões de nosso design.

| | V1 | V2 | V3 |
| --- | --- | --- | --- |
| **[C1]** | 7 MS/5,71 RU | 7 MS/5,71 RU | 7 MS/5,71 RU |
| **[Q1]** | 2 MS/1 RU | 2 MS/1 RU | 2 MS/1 RU |
| **[C2]** | 9 MS/8,76 RU | 9 MS/8,76 RU | 9 MS/8,76 RU |
| **[Q2]** | 9 MS/19,54 RU | 2 MS/1 RU | 2 MS/1 RU |
| **[Q3]** | 130 MS/619,41 RU | 28 MS/201,54 RU | 4 MS/6,46 RU |
| **[C3]** | 7 MS/8,57 RU | 7 ms/15,27 RU | 7 ms/15,27 RU |
| **[Q4]** | 23 MS/27,72 RU | 4 MS/7,72 RU | 4 MS/7,72 RU |
| **[C4]** | 6 ms/7, 5 RU | 7 MS/14,67 RU | 7 MS/14,67 RU |
| **[Q5]** | 59 MS/58,92 RU | 4 MS/8,92 RU | 4 MS/8,92 RU |
| **[Q6]** | 306 MS/2063,54 RU | 83 MS/532,33 RU | 9 MS/16,97 RU |

### <a name="we-have-optimized-a-read-heavy-scenario"></a>Otimizamos um cenário de leitura intensa

Talvez você tenha notado que concentramos nossos esforços em relação ao aprimoramento do desempenho de solicitações de leitura (consultas) às custas de solicitações de gravação (comandos). Em muitos casos, as operações de gravação agora disparam a desnormalização subsequente por meio de feeds de alteração, o que as torna mais dispendiosas e mais demoradas para materializar.

Isso é justificado pelo fato de que uma plataforma de blog (como a maioria dos aplicativos sociais) é de leitura pesada, o que significa que a quantidade de solicitações de leitura que ele precisa atender é geralmente uma ordem de magnitude maior do que a quantidade de solicitações de gravação. Portanto, faz sentido tornar as solicitações de gravação mais caras para serem executadas para permitir que as solicitações de leitura sejam mais baratas e de melhor desempenho.

Se observarmos a otimização mais extrema que fizemos, **[P6]** saiu de 2.000 + RUs para apenas 17 Rus; Atingimos isso desnormalizando as postagens a um custo de cerca de 10 RUs por item. Como fornecemos muito mais solicitações de feed do que a criação ou atualizações de postagens, o custo dessa desnormalização é insignificante Considerando a economia geral.

### <a name="denormalization-can-be-applied-incrementally"></a>A desnormalização pode ser aplicada incrementalmente

As melhorias de escalabilidade que exploramos neste artigo envolvem a desnormalização e a duplicação de dados no conjunto de dados. Deve-se observar que essas otimizações não precisam ser colocadas em vigor no dia 1. As consultas que filtram as chaves de partição têm um desempenho melhor em escala, mas as consultas entre partições podem ser totalmente aceitáveis se forem chamadas raramente ou contra um conjunto de dados limitado. Se você estiver apenas criando um protótipo ou iniciando um produto com uma base de usuários pequena e controlada, poderá, provavelmente, resobrar os aprimoramentos para mais tarde; o que é importante é [monitorar](use-metrics.md) o desempenho do modelo para que você possa decidir se e quando é hora de trazê-los.

O feed de alterações que usamos para distribuir atualizações a outros contêineres armazena todas essas atualizações de forma persistente. Isso possibilita solicitar todas as atualizações desde a criação do contêiner e exibições desnormalizadas de Bootstrap como uma operação de atualização única, mesmo que o sistema já tenha muitos dados.

## <a name="next-steps"></a>Passos seguintes

Após essa introdução à modelagem e particionamento de dados práticos, você pode querer verificar os seguintes artigos para examinar os conceitos que abordamos:

- [Trabalhar com bancos de dados, contêineres e itens](databases-containers-items.md)
- [Criação de partições no Azure Cosmos DB](partitioning-overview.md)
- [Alterar feed no Azure Cosmos DB](change-feed.md)
