---
title: Dados de modelo e partição em Azure Cosmos DB com um exemplo real
description: Aprenda a modelar e dividir um exemplo do mundo real usando a API Core Core Azure Cosmos
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: 10f8ffd90215a21ca03e112aea463d444c623d06
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75445383"
---
# <a name="how-to-model-and-partition-data-on-azure-cosmos-db-using-a-real-world-example"></a>Como modelar e criar partições de dados no Azure Cosmos DB com um exemplo do mundo real

Este artigo baseia-se em vários conceitos de Azure Cosmos DB como modelação de [dados,](modeling-data.md) [partição,](partitioning-overview.md)e [aprovisionado para](request-units.md) demonstrar como lidar com um exercício de design de dados no mundo real.

Se normalmente trabalha com bases de dados relacionais, provavelmente construiu hábitos e intuições sobre como desenhar um modelo de dados. Devido aos constrangimentos específicos, mas também aos pontos fortes únicos do Azure Cosmos DB, a maioria destas boas práticas não se traduz bem e pode arrastá-lo para soluções sub-ideais. O objetivo deste artigo é guiá-lo através do processo completo de modelação de um caso de utilização real no Azure Cosmos DB, desde a modelação de itens até à colocalização de entidades e à partilha de contentores.

## <a name="the-scenario"></a>O cenário

Para este exercício, vamos considerar o domínio de uma plataforma de blogs onde *os utilizadores* podem criar *posts.* Os utilizadores também podem *gostar* e adicionar *comentários* a essas publicações.

> [!TIP]
> Realçámos algumas palavras em *itálico;* estas palavras identificam o tipo de "coisas" que o nosso modelo terá de manipular.

Adicionando mais requisitos à nossa especificação:

- Uma primeira página exibe um feed de publicações recentemente criadas,
- Podemos recolher todas as publicações para um utilizador, todos os comentários para uma publicação e todos os gostos para uma publicação,
- As publicações são devolvidas com o nome de utilizador dos seus autores e uma contagem de quantos comentários e gostos têm,
- Comentários e gostos também são devolvidos com o nome de utilizador dos utilizadores que os criaram,
- Quando apresentados como listas, as publicações só têm de apresentar um resumo truncado do seu conteúdo.

## <a name="identify-the-main-access-patterns"></a>Identificar os principais padrões de acesso

Para começar, damos alguma estrutura à nossa especificação inicial identificando os padrões de acesso da nossa solução. Ao conceber um modelo de dados para o Azure Cosmos DB, é importante perceber quais os pedidos que o nosso modelo terá de servir para garantir que o modelo servirá esses pedidos de forma eficiente.

Para facilitar o seguimento do processo global, categorizamos esses diferentes pedidos como comandos ou consultas, pedindo algum vocabulário do [CQRS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_query_responsibility_segregation) onde os comandos são pedidos de escrita (isto é, intenções de atualizar o sistema) e as consultas são apenas pedidos de leitura.

Aqui está a lista de pedidos que a nossa plataforma terá de expor:

- **[C1]** Criar/editar um utilizador
- **[Q1]** Recuperar um utilizador
- **[C2]** Criar/editar um post
- **[Q2]** Recuperar um poste
- **[Q3]** Enumerar as publicações de um utilizador em formato curto
- **[C3]** Criar um comentário
- **[Q4]** Enumerar os comentários de uma publicação
- **[C4]** Como um poste
- **[Q5]** Listar gostos de um post
- **[Q6]** Enumerar *x* os posts mais recentes criados em formato curto (feed)

Como esta fase, não pensamos nos detalhes do que cada entidade (utilizador, post etc.) irá conter. Este passo é geralmente um dos primeiros a ser abordado sao projetar contra uma loja relacional, porque temos que descobrir como essas entidades se traduzirão em termos de tabelas, colunas, chaves estrangeiras, etc. É muito menos preocupante com uma base de dados de documentos que não impõe qualquer esquema por escrito.

A principal razão pela qual é importante identificar os nossos padrões de acesso desde o início, é porque esta lista de pedidos vai ser a nossa suíte de teste. Sempre que iterratemos sobre o nosso modelo de dados, vamos analisar cada um dos pedidos e verificar o seu desempenho e escalabilidade.

## <a name="v1-a-first-version"></a>V1: Uma primeira versão

Começamos com dois `users` contentores: e. `posts`

### <a name="users-container"></a>Recipiente de utilizadores

Este contentor armazena apenas itens de utilizador:

    {
      "id": "<user-id>",
      "username": "<username>"
    }

Dividimos este `id`recipiente por , o que significa que cada divisória lógica dentro desse recipiente só conterá um item.

### <a name="posts-container"></a>Contentor de postes

Este contentor acolhe posts, comentários e gostos:

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

Dividimos este `postId`contentor por , o que significa que cada partição lógica dentro desse contentor conterá um post, todos os comentários para esse post e todos os gostos para esse post.

Note que introduzimos `type` um imóvel nos itens armazenados neste contentor para distinguir entre os três tipos de entidades que este contentor acolhe.

Além disso, optámos por fazer referência a dados relacionados em vez de os incorporar (verifique [esta secção](modeling-data.md) para obter detalhes sobre estes conceitos) porque:

- não há limite superior para quantas publicações um utilizador pode criar,
- os cargos podem ser arbitrariamente longos,
- não há limite superior para quantos comentários e gostos um post pode ter,
- queremos ser capazes de adicionar um comentário ou um like a um post sem ter que atualizar o post em si.

## <a name="how-well-does-our-model-perform"></a>Até que estado funciona o nosso modelo?

Está na hora de avaliar o desempenho e escalabilidade da nossa primeira versão. Para cada um dos pedidos previamente identificados, medimos a sua latência e quantas unidades de pedido consome. Esta medição é feita contra um conjunto de dados de bonecos contendo 100.000 utilizadores com 5 a 50 posts por utilizador, e até 25 comentários e 100 gostos por post.

### <a name="c1-createedit-a-user"></a>[C1] Criar/editar um utilizador

Este pedido é simples de implementar, pois acabamos `users` de criar ou atualizar um item no recipiente. Os pedidos espalhar-se-ão bem por `id` todas as divisórias graças à chave de partição.

![Escrever um único item para o recipiente dos utilizadores](./media/how-to-model-partition-example/V1-C1.png)

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 7 ms | 5.71 RU | ✅ |

### <a name="q1-retrieve-a-user"></a>[Q1] Recuperar um utilizador

A recuperação de um utilizador é feita `users` lendo o item correspondente do recipiente.

![Recuperação de um único item do recipiente dos utilizadores](./media/how-to-model-partition-example/V1-Q1.png)

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 2 ms | 1 RU | ✅ |

### <a name="c2-createedit-a-post"></a>[C2] Criar/editar um post

Da mesma forma que **[C1],** só `posts` temos que escrever para o recipiente.

![Escrever um único item para o recipiente de postes](./media/how-to-model-partition-example/V1-C2.png)

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 9 ms | 8.76 RU | ✅ |

### <a name="q2-retrieve-a-post"></a>[Q2] Recuperar um poste

Começamos por recuperar o documento `posts` correspondente do recipiente. Mas isso não é suficiente, de acordo com a nossa especificação, também temos de agregar o nome de utilizador do autor do post e as contagens de quantos comentários e quantos gostos esta publicação tem, o que requer 3 consultas SQL adicionais para serem emitidas.

![Recuperar um post e agregar dados adicionais](./media/how-to-model-partition-example/V1-Q2.png)

Cada uma das consultas adicionais filtra a chave de partição do respetivo recipiente, que é exatamente o que queremos maximizar o desempenho e a escalabilidade. Mas eventualmente temos que fazer quatro operações para devolver um único posto, então vamos melhorar isso numa próxima iteração.

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 9 ms | 19.54 RU | ⚠ |

### <a name="q3-list-a-users-posts-in-short-form"></a>[Q3] Enumerar as publicações de um utilizador em formato curto

Em primeiro lugar, temos de recuperar as publicações desejadas com uma consulta SQL que rebusca as publicações correspondentes a esse utilizador em particular. Mas também temos de emitir consultas adicionais para agregar o nome de utilizador do autor e as contagens de comentários e gostos.

![Recuperar todas as publicações para um utilizador e agregar os seus dados adicionais](./media/how-to-model-partition-example/V1-Q3.png)

Esta implementação apresenta muitos inconvenientes:

- as consultas que agregam as contagens de comentários e gostos têm de ser emitidas para cada publicação devolvida pela primeira consulta,
- a consulta principal não filtra a chave `posts` de partição do recipiente, conduzindo a uma saída de ventilação e a uma varredura de divisória através do recipiente.

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 130 ms | 619.41 RU | ⚠ |

### <a name="c3-create-a-comment"></a>[C3] Criar um comentário

Um comentário é criado escrevendo o `posts` item correspondente no recipiente.

![Escrever um único item para o recipiente de postes](./media/how-to-model-partition-example/V1-C2.png)

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 7 ms | 8.57 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>[Q4] Enumerar os comentários de uma publicação

Começamos com uma consulta que vai buscar todos os comentários para esse post e, mais uma vez, precisamos também de agregar nomes de utilizadores separadamente para cada comentário.

![Recuperando todos os comentários para uma publicação e agregando os seus dados adicionais](./media/how-to-model-partition-example/V1-Q4.png)

Embora a consulta principal seja filtrada na chave de partição do recipiente, aagregação dos nomes de utilizador penaliza separadamente o desempenho geral. Vamos melhorar isso mais tarde.

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 23 ms | 27.72 RU | ⚠ |

### <a name="c4-like-a-post"></a>[C4] Como um poste

Tal como **[C3],** criamos o `posts` item correspondente no recipiente.

![Escrever um único item para o recipiente de postes](./media/how-to-model-partition-example/V1-C2.png)

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 6 ms | 7.05 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] Listar gostos de um post

Tal como **[Q4],** consultamos os gostos por esse post e depois agregamos os seus nomes de utilizador.

![Recuperar todos os gostos para um post e agregar os seus dados adicionais](./media/how-to-model-partition-example/V1-Q5.png)

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 59 ms | 58.92 RU | ⚠ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] Enumerar os posts mais recentes criados em formato curto (feed)

Pegamos nas publicações mais recentes `posts` consultando o recipiente classificado por data de criação descendente, em seguida, agregamos nomes de utilizadores e contagens de comentários e gostos para cada um dos posts.

![Recuperar publicações mais recentes e agregar os seus dados adicionais](./media/how-to-model-partition-example/V1-Q6.png)

Mais uma vez, a nossa consulta inicial não `posts` filtra a chave de partição do recipiente, o que despoleta uma saída dispendiosa. Este é ainda pior, uma vez que visamos um `ORDER BY` conjunto de resultados muito maior e classificamos os resultados com uma cláusula, o que o torna mais caro em termos de unidades de pedido.

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 306 ms | 2063.54 RU | ⚠ |

## <a name="reflecting-on-the-performance-of-v1"></a>Refletindo sobre o desempenho da V1

Olhando para os problemas de desempenho que enfrentamos na secção anterior, podemos identificar duas classes principais de problemas:

- alguns pedidos requerem múltiplas consultas para recolher todos os dados que precisamos para devolver,
- algumas consultas não filtram a chave de partição dos contentores que visam, levando a uma saída de ventilação que impede a nossa escalabilidade.

Vamos resolver cada um desses problemas, a começar pelo primeiro.

## <a name="v2-introducing-denormalization-to-optimize-read-queries"></a>V2: Introdução da desnormalização para otimizar consultas de leitura

A razão pela qual temos que emitir pedidos adicionais em alguns casos é porque os resultados do pedido inicial não contêm todos os dados que precisamos para devolver. Ao trabalhar com uma loja de dados não relacionais como o Azure Cosmos DB, este tipo de problema é geralmente resolvido desnormalizando dados através do nosso conjunto de dados.

No nosso exemplo, modificamos itens de post para adicionar o nome de utilizador do autor da publicação, a contagem de comentários e a contagem de gostos:

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

Também modificamos comentários e similares para adicionar o nome de utilizador do utilizador que os criou:

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

### <a name="denormalizing-comment-and-like-counts"></a>Desnormalizar comentários e contagens como

O que queremos alcançar é que cada vez que adicionamos `commentCount` um `likeCount` comentário ou um like, também incrementamos o ou o post correspondente. Como `posts` o nosso recipiente `postId`é dividido por, o novo item (comentário ou gosto) e o seu post correspondente sentam-se na mesma divisória lógica. Como resultado, podemos usar um [procedimento armazenado](stored-procedures-triggers-udfs.md) para realizar esta operação.

Agora, ao criar um comentário **([C3]),** em vez `posts` de apenas adicionar um novo item no recipiente, chamamos o seguinte procedimento armazenado nesse recipiente:

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

Este procedimento armazenado leva a identificação do post e o corpo do novo comentário como parâmetros, então:

- recupera o post
- incrementa o`commentCount`
- substitui o cargo
- adiciona o novo comentário

À medida que os procedimentos armazenados são executados como `commentCount` transações atómicas, é garantido que o valor e o número real de comentários permanecerão sempre sincronizados.

Obviamente chamamos um procedimento armazenado semelhante ao adicionar `likeCount`novos likes para incrementar o .

### <a name="denormalizing-usernames"></a>Desnormalizando nomes de utilizadores

Os nomes de utilizador requerem uma abordagem diferente, uma vez que os utilizadores não só se sentam em divisórias diferentes, mas num recipiente diferente. Quando tivermos de desnormalizar os dados através de divisórias e contentores, podemos utilizar o feed de [mudança](change-feed.md)do recipiente de origem.

No nosso exemplo, utilizamos o `users` feed de alteração do recipiente para reagir sempre que os utilizadores atualizarem os seus nomes de utilizador. Quando isso acontece, propagamos a mudança chamando `posts` outro procedimento armazenado no recipiente:

![Desnormalizando os nomes de utilizador no recipiente de posts](./media/how-to-model-partition-example/denormalization-1.png)

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

Este procedimento armazenado leva a identificação do utilizador e o novo nome de utilizador como parâmetros:

- requer todos os itens `userId` correspondentes aos (que podem ser posts, comentários ou gostos)
- para cada um desses itens
  - substitui o`userUsername`
  - substitui o item

> [!IMPORTANT]
> Esta operação é dispendiosa porque requer que este procedimento armazenado seja `posts` executado em todas as divisórias do recipiente. Assumimos que a maioria dos utilizadores escolhe um nome de utilizador adequado durante o início de sessão e nunca o altera, pelo que esta atualização será executada muito raramente.

## <a name="what-are-the-performance-gains-of-v2"></a>Quais são os ganhos de desempenho do V2?

### <a name="q2-retrieve-a-post"></a>[Q2] Recuperar um poste

Agora que a nossa desnormalização está em vigor, só temos de ir buscar um único item para tratar desse pedido.

![Recuperando um único item do contentor dos postes](./media/how-to-model-partition-example/V2-Q2.png)

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 2 ms | 1 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>[Q4] Enumerar os comentários de uma publicação

Aqui novamente, podemos poupar os pedidos extra que buscaram os nomes de utilizador e acabar com uma única consulta que filtra na chave de partição.

![Recuperando todos os comentários para um post](./media/how-to-model-partition-example/V2-Q4.png)

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 4 ms | 7.72 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] Listar gostos de um post

Exatamente a mesma situação quando listam gostos.

![Recuperando todos os gostos para um post](./media/how-to-model-partition-example/V2-Q5.png)

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 4 ms | 8.92 RU | ✅ |

## <a name="v3-making-sure-all-requests-are-scalable"></a>V3: Certificar-se de que todos os pedidos são escaláveis

Olhando para as nossas melhorias globais de desempenho, ainda há dois pedidos que ainda não otimizamos totalmente: **[Q3]** e **[Q6]**. São os pedidos que envolvem consultas que não filtram a chave de partição dos contentores que visam.

### <a name="q3-list-a-users-posts-in-short-form"></a>[Q3] Enumerar as publicações de um utilizador em formato curto

Este pedido já beneficia das melhorias introduzidas na V2, que poupa maquetes adicionais.

![Recuperação de todas as publicações para um utilizador](./media/how-to-model-partition-example/V2-Q3.png)

Mas a consulta restante ainda não está a `posts` filtrar a chave de partição do recipiente.

A forma de pensar sobre esta situação é, na verdade, simples:

1. Este pedido *tem* de `userId` filtrar o porque queremos buscar todas as publicações para um determinado utilizador
1. Não tem um bom desempenho porque é `posts` executado contra o recipiente, que não é dividido por`userId`
1. Afirmando o óbvio, resolveríamos o nosso problema de desempenho executando este pedido contra um contentor que *é* dividido por`userId`
1. Acontece que já temos um recipiente `users` assim: o contentor!

Assim, introduzimos um segundo nível de desnormalização `users` duplicando postos inteiros para o contentor. Ao fazê-lo, efetivamente obtemos uma cópia dos nossos posts, apenas divididos `userId`ao longo de diferentes dimensões, tornando-os muito mais eficientes para recuperar em seus .

O `users` recipiente contém agora 2 tipos de itens:

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

- introduzimos um `type` campo no item de utilizador para distinguir os utilizadores de publicações,
- adicionámos também `userId` um campo no item do utilizador, que é redundante com `id` o campo, mas é necessário, uma vez que o `users` recipiente é agora dividido por `userId` (e não `id` como anteriormente)

Para conseguir essa desnormalização, voltamos a usar o feed de mudança. Desta vez, reagimos na mudança `posts` do recipiente para enviar qualquer `users` posto novo ou atualizado para o contentor. E como as publicações de listagem não requerem devolver todo o seu conteúdo, podemos trunca-los no processo.

![Desnormalizando posts no recipiente dos utilizadores](./media/how-to-model-partition-example/denormalization-2.png)

Podemos agora encaminhar a `users` nossa consulta para o contentor, filtrando a chave de partição do recipiente.

![Recuperação de todas as publicações para um utilizador](./media/how-to-model-partition-example/V3-Q3.png)

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 4 ms | 6.46 RU | ✅ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] Enumerar os posts mais recentes criados em formato curto (feed)

Temos de lidar com uma situação semelhante: mesmo depois de pouparmos as consultas adicionais deixadas desnecessárias pela desnormalização introduzida em V2, a consulta restante não filtra a chave de partição do contentor:

![Recuperando publicações mais recentes](./media/how-to-model-partition-example/V2-Q6.png)

Seguindo a mesma abordagem, maximizar o desempenho e escalabilidade deste pedido requer que apenas atinja uma divisória. Isto é concebível porque só temos de devolver um número limitado de itens; para povoar a página inicial da nossa plataforma de blogging, só precisamos de obter as 100 publicações mais recentes, sem a necessidade de paginar através de todo o conjunto de dados.

Assim, para otimizar este último pedido, introduzimos um terceiro recipiente ao nosso design, inteiramente dedicado a servir este pedido. Desnormalizamos os nossos `feed` postos para aquele novo contentor:

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

Este recipiente é `type`dividido por , `post` que estará sempre nos nossos itens. Ao fazê-lo, todos os itens deste recipiente ficarão na mesma divisória.

Para conseguir a desnormalização, temos apenas de nos ligar ao gasoduto de alimentação de mudanças que introduzimos anteriormente para enviar os postos para aquele novo contentor. Uma coisa importante a ter em conta é que temos de nos certificar de que apenas armazenamos os 100 posts mais recentes; caso contrário, o conteúdo do recipiente pode crescer para além do tamanho máximo de uma divisória. Isto é feito chamando um [pós-gatilho](stored-procedures-triggers-udfs.md#triggers) cada vez que um documento é adicionado no recipiente:

![Desnormalizando postos no recipiente de alimentação](./media/how-to-model-partition-example/denormalization-3.png)

Aqui está o corpo do pós-gatilho que trunca a coleção:

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

O passo final é redirecionar a `feed` nossa consulta para o nosso novo contentor:

![Recuperando publicações mais recentes](./media/how-to-model-partition-example/V3-Q6.png)

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 9 ms | 16.97 RU | ✅ |

## <a name="conclusion"></a>Conclusão

Vamos ver as melhorias globais do desempenho e da escalabilidade que introduzimos sobre as diferentes versões do nosso design.

| | V1 | V2 | V3 |
| --- | --- | --- | --- |
| **[C1]** | 7 ms / 5.71 RU | 7 ms / 5.71 RU | 7 ms / 5.71 RU |
| **[Q1]** | 2 ms / 1 RU | 2 ms / 1 RU | 2 ms / 1 RU |
| **[C2]** | 9 ms / 8.76 RU | 9 ms / 8.76 RU | 9 ms / 8.76 RU |
| **[Q2]** | 9 ms / 19.54 RU | 2 ms / 1 RU | 2 ms / 1 RU |
| **[Q3]** | 130 ms / 619.41 RU | 28 ms / 201.54 RU | 4 ms / 6.46 RU |
| **[C3]** | 7 ms / 8.57 RU | 7 ms / 15.27 RU | 7 ms / 15.27 RU |
| **[Q4]** | 23 ms / 27.72 RU | 4 ms / 7.72 RU | 4 ms / 7.72 RU |
| **[C4]** | 6 ms / 7.05 RU | 7 ms / 14.67 RU | 7 ms / 14.67 RU |
| **[Q5]** | 59 ms / 58.92 RU | 4 ms / 8.92 RU | 4 ms / 8.92 RU |
| **[Q6]** | 306 ms / 2063.54 RU | 83 ms / 532.33 RU | 9 ms / 16.97 RU |

### <a name="we-have-optimized-a-read-heavy-scenario"></a>Otimizámos um cenário pesado de leitura

Deve ter reparado que concentrámos os nossos esforços para melhorar o desempenho dos pedidos de leitura (consultas) em detrimento dos pedidos de escrita (comandos). Em muitos casos, as operações de escrita desencadeiam agora uma desnormalização subsequente através de feeds de mudança, o que as torna mais computacionalmente caras e mais longas para se materializarem.

Isto é justificado pelo facto de uma plataforma de blogues (como a maioria das aplicações sociais) ser lida, o que significa que a quantidade de pedidos de leitura que tem para servir é geralmente encomendas de magnitude superior à quantidade de pedidos de escrita. Por isso, faz sentido tornar os pedidos de escrita mais caros para executar, a fim de permitir que os pedidos de leitura sejam mais baratos e mais bem executados.

Se olharmos para a otimização mais extrema que fizemos, **[Q6]** passou de 2000+ RUs para apenas 17 RUs; conseguimos isso desnormalizando postos a um custo de cerca de 10 RUs por item. Como serviríamos muito mais pedidos de alimentação do que a criação ou atualizações de postos, o custo desta desnormalização é negligenciável tendo em conta as poupanças globais.

### <a name="denormalization-can-be-applied-incrementally"></a>A desnormalização pode ser aplicada incrementalmente

As melhorias de escalabilidade que exploramos neste artigo envolvem desnormalização e duplicação de dados em todo o conjunto de dados. Note-se que estas otimizações não têm de ser implementadas no dia 1. As consultas que filtram as teclas de partição funcionam melhor à escala, mas as consultas de divisória cruzada podem ser totalmente aceitáveis se forem chamadas raramente ou contra um conjunto de dados limitado. Se estiver apenas a construir um protótipo, ou a lançar um produto com uma base de utilizadores pequena e controlada, provavelmente poderá poupar essas melhorias para mais tarde; o que é importante então é [monitorizar](use-metrics.md) o desempenho do seu modelo para que possa decidir se e quando é a hora de trazê-los.

O feed de mudança que usamos para distribuir atualizações para outros recipientes armazena todas essas atualizações persistentemente. Isto permite solicitar todas as atualizações desde a criação do recipiente e das vistas desnormalizadas como uma operação única de recuperação, mesmo que o seu sistema já tenha muitos dados.

## <a name="next-steps"></a>Passos seguintes

Após esta introdução à modelação prática de dados e à partilha, é possível que queira verificar os seguintes artigos para rever os conceitos que abordámos:

- [Trabalhar com bases de dados, contentores e itens](databases-containers-items.md)
- [Criação de partições no Azure Cosmos DB](partitioning-overview.md)
- [Alterar feed em Azure Cosmos DB](change-feed.md)
