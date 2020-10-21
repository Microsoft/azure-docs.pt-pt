---
title: Dados de modelo e partição sobre Azure Cosmos DB com um exemplo no mundo real
description: Saiba como modelar e dividir um exemplo do mundo real usando a API do Núcleo de Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/23/2019
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 8e9d11ed39d6e4dc7ad432659534e7dd14fcf1ec
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92277993"
---
# <a name="how-to-model-and-partition-data-on-azure-cosmos-db-using-a-real-world-example"></a>Como modelar e criar partições de dados no Azure Cosmos DB com um exemplo do mundo real

Este artigo baseia-se em vários conceitos de DB da Azure Cosmos, como [modelação de dados,](modeling-data.md) [partição,](partitioning-overview.md)e [produção antejada](request-units.md) para demonstrar como lidar com um exercício de design de dados no mundo real.

Se normalmente trabalha com bases de dados relacionais, provavelmente já construiu hábitos e intuições sobre como desenhar um modelo de dados. Devido aos constrangimentos específicos, mas também aos pontos fortes únicos da Azure Cosmos DB, a maioria destas melhores práticas não se traduz bem e pode arrastá-lo para soluções sub-ideais. O objetivo deste artigo é guiá-lo através do processo completo de modelação de um caso de uso no mundo real em Azure Cosmos DB, desde a modelação de artigos até à colocação de entidades e partição de contentores.

## <a name="the-scenario"></a>Cenário

Para este exercício, vamos considerar o domínio de uma plataforma de blogs onde *os utilizadores* podem criar *publicações.* Os utilizadores também podem *gostar* e adicionar *comentários* a essas publicações.

> [!TIP]
> Realçámos algumas palavras em *itálico;* estas palavras identificam o tipo de "coisas" que o nosso modelo terá de manipular.

Adicionar mais requisitos à nossa especificação:

- Uma primeira página exibe um feed de publicações recentemente criadas,
- Podemos ir buscar todas as publicações para um utilizador, todos os comentários para uma publicação e todos os gostos para uma publicação,
- As publicações são devolvidas com o nome de utilizador dos seus autores e uma contagem de quantos comentários e gostos têm,
- Comentários e gostos também são devolvidos com o nome de utilizador dos utilizadores que os criaram,
- Quando apresentados como listas, as publicações apenas têm de apresentar um resumo truncado do seu conteúdo.

## <a name="identify-the-main-access-patterns"></a>Identificar os principais padrões de acesso

Para começar, damos alguma estrutura à nossa especificação inicial identificando os padrões de acesso da nossa solução. Ao desenhar um modelo de dados para a Azure Cosmos DB, é importante entender quais os pedidos que o nosso modelo terá de servir para garantir que o modelo irá servir esses pedidos de forma eficiente.

Para facilitar o processo global, categorizamos esses diferentes pedidos como comandos ou consultas, emprestando algum vocabulário do [CQRS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_query_responsibility_segregation) onde os comandos são pedidos de escrita (isto é, intenções de atualizar o sistema) e consultas são apenas pedidos de leitura.

Aqui está a lista de pedidos que a nossa plataforma terá de expor:

- **[C1]** Criar/editar um utilizador
- **[Q1]** Recuperar um utilizador
- **[C2]** Criar/editar um post
- **[Q2]** Recuperar um poste
- **[Q3]** Listar os posts de um utilizador em formato curto
- **[C3]** Criar um comentário
- **[Q4]** Listar os comentários de um post
- **[C4]** Como um poste
- **[Q5]** Listar gostos de um post
- **[Q6]** Listar os *posts* mais recentes criados em formato curto (feed)

Nesta fase, ainda não pensámos nos detalhes do que cada entidade (utilizador, post, etc.) irá conter. Este passo é geralmente um dos primeiros a ser abordado quando se projeta contra uma loja relacional, porque temos que descobrir como essas entidades se vão traduzir em termos de tabelas, colunas, chaves estrangeiras, etc. É muito menos uma preocupação com uma base de dados de documentos que não impõe qualquer esquema na escrita.

A principal razão pela qual é importante identificar os nossos padrões de acesso desde o início, é porque esta lista de pedidos vai ser a nossa suíte de teste. Sempre que iteraçãomos sobre o nosso modelo de dados, vamos analisar cada um dos pedidos e verificar o seu desempenho e escalabilidade.

## <a name="v1-a-first-version"></a>V1: Uma primeira versão

Começamos com dois contentores: `users` `posts` e.

### <a name="users-container"></a>Recipiente de utilizadores

Este recipiente armazena apenas itens de utilizador:

```json
{
    "id": "<user-id>",
    "username": "<username>"
}
```

Dividimos este contentor `id` por, o que significa que cada divisória lógica dentro desse contentor conterá apenas um item.

### <a name="posts-container"></a>Contentor de postes

Este recipiente acolhe posts, comentários e gostos:

```json
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
```

Dividimos este contentor `postId` por, o que significa que cada divisória lógica dentro desse contentor conterá um post, todos os comentários para esse post e todos os gostos para esse post.

Note que introduzimos um `type` imóvel nos itens armazenados neste contentor para distinguir entre os três tipos de entidades que este contentor acolhe.

Além disso, optámos por fazer referência a dados relacionados em vez de os incorporar (consulte [esta secção](modeling-data.md) para obter detalhes sobre estes conceitos) porque:

- não há limite máximo para quantas publicações um utilizador pode criar,
- os postos podem ser arbitrariamente longos,
- não há limite máximo para quantos comentários e gostos um post pode ter,
- queremos ser capazes de adicionar um comentário ou um like a um post sem ter que atualizar o próprio post.

## <a name="how-well-does-our-model-perform"></a>Qual é o bom desempenho do nosso modelo?

Está na hora de avaliar o desempenho e escalabilidade da nossa primeira versão. Para cada um dos pedidos previamente identificados, medimos a sua latência e quantas unidades de pedido consome. Esta medição é feita contra um conjunto de dados falsos contendo 100.000 utilizadores com 5 a 50 posts por utilizador, e até 25 comentários e 100 gostos por post.

### <a name="c1-createedit-a-user"></a>[C1] Criar/editar um utilizador

Este pedido é simples de implementar à medida que apenas criamos ou atualizamos um item no `users` recipiente. Os pedidos espalhar-se-ão por todas as divisórias graças à `id` chave de partição.

:::image type="content" source="./media/how-to-model-partition-example/V1-C1.png" alt-text="Escrever um único item no recipiente dos utilizadores" border="false":::

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 7 ms | 5.71 RU | ✅ |

### <a name="q1-retrieve-a-user"></a>[Q1] Recuperar um utilizador

A recuperação de um utilizador é feita lendo o item correspondente do `users` recipiente.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q1.png" alt-text="Escrever um único item no recipiente dos utilizadores" border="false":::

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 2 ms | 1 RU | ✅ |

### <a name="c2-createedit-a-post"></a>[C2] Criar/editar um post

Da mesma forma **que [C1],** só temos que escrever para o `posts` recipiente.

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="Escrever um único item no recipiente dos utilizadores" border="false":::

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 9 ms | 8.76 RU | ✅ |

### <a name="q2-retrieve-a-post"></a>[Q2] Recuperar um poste

Começamos por recuperar o documento correspondente do `posts` contentor. Mas isso não é suficiente, como de acordo com a nossa especificação também temos que agregar o nome de utilizador do autor do post e as contagens de quantos comentários e quantos gostos este post tem, o que requer 3 consultas adicionais de SQL para ser emitida.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q2.png" alt-text="Escrever um único item no recipiente dos utilizadores" border="false":::

Cada uma das consultas adicionais filtra na chave de partição do respetivo recipiente, que é exatamente o que queremos maximizar o desempenho e a escalabilidade. Mas eventualmente temos que fazer quatro operações para devolver um único poste, então vamos melhorar isso em uma próxima iteração.

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 9 ms | 19.54 RU | ⚠ |

### <a name="q3-list-a-users-posts-in-short-form"></a>[Q3] Listar os posts de um utilizador em formato curto

Em primeiro lugar, temos de recuperar os posts desejados com uma consulta SQL que vai buscar os posts correspondentes a esse utilizador em particular. Mas também temos de emitir perguntas adicionais para agregar o nome de utilizador do autor e as contagens de comentários e gostos.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q3.png" alt-text="Escrever um único item no recipiente dos utilizadores" border="false":::

Esta implementação apresenta muitos inconvenientes:

- as consultas que agregam as contagens de comentários e gostos têm de ser emitidas para cada posto devolvido pela primeira consulta,
- a consulta principal não filtra a chave de partição do `posts` recipiente, conduzindo a uma saída de ventilação e a uma digitalização de divisórias através do recipiente.

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 130 ms | 619.41 RU | ⚠ |

### <a name="c3-create-a-comment"></a>[C3] Criar um comentário

Um comentário é criado escrevendo o item correspondente no `posts` recipiente.

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="Escrever um único item no recipiente dos utilizadores" border="false":::

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 7 ms | 8.57 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>[Q4] Listar os comentários de um post

Começamos com uma consulta que vai buscar todos os comentários para esse post e, mais uma vez, precisamos também de agregar nomes de utilizadores separadamente para cada comentário.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q4.png" alt-text="Escrever um único item no recipiente dos utilizadores" border="false":::

Embora a consulta principal filtre na chave de partição do recipiente, agregar os nomes de utilizador penaliza separadamente o desempenho geral. Vamos melhorar mais tarde.

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 23 ms | 27.72 RU | ⚠ |

### <a name="c4-like-a-post"></a>[C4] Como um poste

Tal como **[C3],** criamos o item correspondente no `posts` recipiente.

:::image type="content" source="./media/how-to-model-partition-example/V1-C2.png" alt-text="Escrever um único item no recipiente dos utilizadores" border="false":::

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 6 ms | 7.05 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] Listar gostos de um post

Assim como **[Q4],** consultamos os gostos para esse post e, em seguida, agregamos os seus nomes de utilizador.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q5.png" alt-text="Escrever um único item no recipiente dos utilizadores" border="false":::

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 59 ms | 58.92 RU | ⚠ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] Listar os posts mais recentes criados em formato curto (feed)

Buscamos as publicações mais recentes consultando o `posts` contentor classificado por data de criação descendente, em seguida, agregamos nomes de utilizadores e contagens de comentários e gostos para cada um dos posts.

:::image type="content" source="./media/how-to-model-partition-example/V1-Q6.png" alt-text="Escrever um único item no recipiente dos utilizadores" border="false":::

Mais uma vez, a nossa consulta inicial não filtra a chave de partição do recipiente, o `posts` que despoleta uma dispendiosa saída de ventilação. Este é ainda pior, uma vez que visamos um conjunto de resultados muito maior e classificamos os resultados com uma cláusula, o `ORDER BY` que o torna mais caro em termos de unidades de pedido.

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 306 ms | 2063.54 RU | ⚠ |

## <a name="reflecting-on-the-performance-of-v1"></a>Refletindo sobre o desempenho da V1

Olhando para os problemas de desempenho que enfrentamos na secção anterior, podemos identificar duas classes principais de problemas:

- alguns pedidos requerem múltiplas consultas a serem emitidas para recolher todos os dados que precisamos para devolver,
- algumas consultas não filtram a chave de partição dos recipientes que visam, levando a um fan-out que impede a nossa escalabilidade.

Vamos resolver cada um desses problemas, a começar pelo primeiro.

## <a name="v2-introducing-denormalization-to-optimize-read-queries"></a>V2: Introdução da denormalização para otimizar consultas de leitura

A razão pela qual temos de emitir pedidos adicionais em alguns casos é porque os resultados do pedido inicial não contêm todos os dados que precisamos para devolver. Ao trabalhar com uma loja de dados não relacional como a Azure Cosmos DB, este tipo de problema é geralmente resolvido desnormalizando dados em todo o nosso conjunto de dados.

No nosso exemplo, modificamos itens postais para adicionar o nome de utilizador do autor do post, a contagem de comentários e a contagem de gostos:

```json
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
```

Também modificamos comentários e itens para adicionar o nome de utilizador do utilizador que os criou:

```json
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
```

### <a name="denormalizing-comment-and-like-counts"></a>Comentário desnormalizado e como conta

O que queremos alcançar é que cada vez que adicionamos um comentário ou um like, também incrementamos o `commentCount` ou o no post `likeCount` correspondente. Como o nosso `posts` recipiente é dividido `postId` por, o novo item (comentário ou similar) e o seu posto correspondente sentam-se na mesma partição lógica. Como resultado, podemos usar um [procedimento armazenado](stored-procedures-triggers-udfs.md) para executar esta operação.

Agora, ao criar um comentário **([C3]**), em vez de apenas adicionar um novo item no `posts` recipiente chamamos o seguinte procedimento armazenado nesse recipiente:

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

Este procedimento armazenado toma a ID do post e o corpo do novo comentário como parâmetros, em seguida:

- recupera o poste
- incrementa o `commentCount`
- substitui o poste
- adiciona o novo comentário

Como os procedimentos armazenados são executados como transações atómicas, o valor `commentCount` e o número real de comentários permanecerão sempre sincronizados.

Obviamente chamamos um procedimento armazenado semelhante ao adicionar novos gostos para incrementar o `likeCount` .

### <a name="denormalizing-usernames"></a>Nomes de utilizador desnormalizantes

Os nomes de utilizador requerem uma abordagem diferente, uma vez que os utilizadores não só se sentam em diferentes divisórias, mas num recipiente diferente. Quando temos de desnormalizar os dados através de divisórias e contentores, podemos utilizar a rede de [alteração](change-feed.md)do recipiente de origem.

No nosso exemplo, utilizamos o feed de alteração do `users` recipiente para reagir sempre que os utilizadores atualizarem os seus nomes de utilizador. Quando isso acontece, propagamos a mudança chamando outro procedimento armazenado no `posts` recipiente:

:::image type="content" source="./media/how-to-model-partition-example/denormalization-1.png" alt-text="Escrever um único item no recipiente dos utilizadores" border="false":::

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

Este procedimento armazenado toma o ID do utilizador e o novo nome de utilizador do utilizador como parâmetros, em seguida:

- adquire todos os itens correspondentes ao `userId` (que podem ser posts, comentários ou gostos)
- para cada um desses itens
  - substitui o `userUsername`
  - substitui o item

> [!IMPORTANT]
> Esta operação é dispendiosa porque requer que este procedimento armazenado seja executado em todas as divisões do `posts` recipiente. Assumimos que a maioria dos utilizadores escolhe um nome de utilizador adequado durante a inscrição e nunca o alterará, pelo que esta atualização será executada muito raramente.

## <a name="what-are-the-performance-gains-of-v2"></a>Quais são os ganhos de desempenho da V2?

### <a name="q2-retrieve-a-post"></a>[Q2] Recuperar um poste

Agora que a nossa desnormalização está em vigor, só temos de ir buscar um único item para tratar desse pedido.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q2.png" alt-text="Escrever um único item no recipiente dos utilizadores" border="false":::

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 2 ms | 1 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>[Q4] Listar os comentários de um post

Aqui novamente, podemos poupar os pedidos extra que buscaram os nomes de utilizador e acabar com uma única consulta que filtra na chave de partição.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q4.png" alt-text="Escrever um único item no recipiente dos utilizadores" border="false":::

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 4 ms | 7.72 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[Q5] Listar gostos de um post

Exatamente a mesma situação ao listar os gostos.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q5.png" alt-text="Escrever um único item no recipiente dos utilizadores" border="false":::

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 4 ms | 8.92 RU | ✅ |

## <a name="v3-making-sure-all-requests-are-scalable"></a>V3: Certificar-se de que todos os pedidos são escaláveis

Olhando para as nossas melhorias globais de desempenho, ainda existem dois pedidos que ainda não otimizamos totalmente: **[Q3]** e **[Q6]**. São os pedidos que envolvem consultas que não filtram a chave de partição dos contentores que visam.

### <a name="q3-list-a-users-posts-in-short-form"></a>[Q3] Listar os posts de um utilizador em formato curto

Este pedido já beneficia das melhorias introduzidas na V2, que poupam consultas adicionais.

:::image type="content" source="./media/how-to-model-partition-example/V2-Q3.png" alt-text="Escrever um único item no recipiente dos utilizadores" border="false":::

Mas a restante consulta ainda não está a filtrar a chave de partição do `posts` recipiente.

A forma de pensar sobre esta situação é, na verdade, simples:

1. Este pedido *tem que* filtrar no `userId` porque queremos buscar todos os posts para um determinado utilizador
1. Não tem um bom desempenho porque é executado contra o `posts` recipiente, que não é dividido por `userId`
1. Afirmando o óbvio, resolveríamos o nosso problema de desempenho executando este pedido contra um contentor que *é* dividido por `userId`
1. Acontece que já temos tal contentor: o `users` contentor!

Assim, introduzimos um segundo nível de desnormalização duplicando postos inteiros no `users` contentor. Ao fazê-lo, efetivamente obtemos uma cópia dos nossos posts, apenas divididos ao longo de diferentes dimensões, tornando-os muito mais eficientes para recuperar pelos seus `userId` .

O `users` recipiente contém agora 2 tipos de itens:

```json
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
```

Tenha em atenção que:

- introduzimos um `type` campo no item do utilizador para distinguir os utilizadores das publicações,
- também adicionámos um `userId` campo no item do utilizador, que é redundante com o `id` campo, mas que é necessário, uma vez que o recipiente é agora dividido por `users` `userId` (e não `id` como anteriormente)

Para conseguir essa desnormalização, mais uma vez usamos o feed de mudança. Desta vez, reagimos à alteração do feed do `posts` contentor para enviar qualquer posto novo ou atualizado para o `users` contentor. E como os posts de listagem não requerem devolver todo o seu conteúdo, podemos truncá-los no processo.

:::image type="content" source="./media/how-to-model-partition-example/denormalization-2.png" alt-text="Escrever um único item no recipiente dos utilizadores" border="false":::

Agora podemos encaminhar a nossa consulta para o `users` recipiente, filtrando a chave de partição do recipiente.

:::image type="content" source="./media/how-to-model-partition-example/V3-Q3.png" alt-text="Escrever um único item no recipiente dos utilizadores" border="false":::

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 4 ms | 6.46 RU | ✅ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[Q6] Listar os posts mais recentes criados em formato curto (feed)

Temos de lidar com uma situação semelhante: mesmo depois de pouparmos as consultas adicionais deixadas desnecessárias pela desnormalização introduzida em V2, a restante consulta não filtra a chave de partição do recipiente:

:::image type="content" source="./media/how-to-model-partition-example/V2-Q6.png" alt-text="Escrever um único item no recipiente dos utilizadores" border="false":::

Seguindo a mesma abordagem, maximizar o desempenho e escalabilidade deste pedido requer que atinja apenas uma partição. Isto é concebível porque só temos de devolver um número limitado de itens; para preencher a página inicial da nossa plataforma de blogs, só precisamos de obter as 100 publicações mais recentes, sem necessidade de paginar através de todo o conjunto de dados.

Assim, para otimizar este último pedido, introduzimos um terceiro recipiente ao nosso design, inteiramente dedicado a servir este pedido. Desnormalizamos os nossos postos para aquele novo `feed` contentor:

```json
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
```

Este recipiente é dividido `type` por, que estará sempre `post` nos nossos itens. Ao fazê-lo, todos os itens deste recipiente ficarão na mesma divisória.

Para conseguir a desnormalização, só temos de nos ligar ao gasoduto de alimentação de mudanças que introduzimos anteriormente para despachar os postes para aquele novo contentor. Uma coisa importante a ter em conta é que temos de nos certificar de que só armazenamos os 100 postos mais recentes; caso contrário, o conteúdo do recipiente pode crescer para além do tamanho máximo de uma divisória. Isto é feito chamando um [pós-gatilho](stored-procedures-triggers-udfs.md#triggers) cada vez que um documento é adicionado no recipiente:

:::image type="content" source="./media/how-to-model-partition-example/denormalization-3.png" alt-text="Escrever um único item no recipiente dos utilizadores" border="false":::

Aqui está o corpo do pós-gatilho que trunta a coleção:

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

O passo final é redirecionar a nossa consulta para o nosso novo `feed` contentor:

:::image type="content" source="./media/how-to-model-partition-example/V3-Q6.png" alt-text="Escrever um único item no recipiente dos utilizadores" border="false":::

| **Latência** | **Carga RU** | **Desempenho** |
| --- | --- | --- |
| 9 ms | 16.97 RU | ✅ |

## <a name="conclusion"></a>Conclusão

Vamos ver o desempenho geral e melhorias de escalabilidade que introduzimos ao longo das diferentes versões do nosso design.

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

### <a name="we-have-optimized-a-read-heavy-scenario"></a>Otimizamos um cenário de leitura pesada

Devem ter reparado que concentrámos os nossos esforços para melhorar o desempenho dos pedidos de leitura (consultas) em detrimento de pedidos de escrita (comandos). Em muitos casos, as operações de escrita agora desencadeiam a subsequente desnormalização através de feeds de mudança, o que os torna mais caros computacionalmente e mais longos para se materializarem.

Isto é justificado pelo facto de uma plataforma de blogging (como a maioria das aplicações sociais) ser muito pesada, o que significa que a quantidade de pedidos de leitura que tem de servir é geralmente encomendas de magnitude superior à quantidade de pedidos de escrita. Por isso, faz sentido tornar os pedidos de escrita mais caros para serem executados, a fim de deixar que os pedidos de leitura sejam mais baratos e mais bem executados.

Se olharmos para a otimização mais extrema que fizemos, **[Q6]** passou de 2000+ RUs para apenas 17 RUs; conseguimos isso desnormalizando postos a um custo de cerca de 10 RUs por item. Como serviríamos muito mais pedidos de alimentação do que a criação ou atualizações de publicações, o custo desta desnormalização é insignificante tendo em conta a poupança global.

### <a name="denormalization-can-be-applied-incrementally"></a>A desnormalização pode ser aplicada incrementalmente

As melhorias de escalabilidade que exploramos neste artigo envolvem desnormalização e duplicação de dados em todo o conjunto de dados. Note-se que estas otimizações não têm de ser postas em prática no primeiro dia. As consultas que filtram as teclas de partição têm um melhor desempenho à escala, mas as consultas de partição cruzada podem ser totalmente aceitáveis se forem chamadas raramente ou contra um conjunto de dados limitado. Se estiver apenas a construir um protótipo, ou a lançar um produto com uma base de utilizadores pequena e controlada, provavelmente pode poupar essas melhorias para mais tarde; o que é importante, então, é [monitorizar](use-metrics.md) o desempenho do seu modelo para que possa decidir se e quando é a hora de trazê-los.

O feed de alteração que usamos para distribuir atualizações para outros contentores armazena todas essas atualizações de forma persistente. Isto permite solicitar todas as atualizações desde a criação do contentor e vistas desnormalizadas como uma operação única de recuperação, mesmo que o seu sistema já tenha muitos dados.

## <a name="next-steps"></a>Passos seguintes

Após esta introdução à modelação prática de dados e partição, pode querer consultar os seguintes artigos para rever os conceitos que cobrimos:

- [Trabalhar com bases de dados, contentores e itens](account-databases-containers-items.md)
- [Criação de partições no Azure Cosmos DB](partitioning-overview.md)
- [Mude o feed em Azure Cosmos DB](change-feed.md)
