---
title: Como a partição e o modelo de dados no Azure Cosmos DB com um exemplo real
description: Aprenda a modelar e faça a partição de um exemplo do mundo real usando a API de núcleos do Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 3/27/2019
ms.author: thweiss
ms.openlocfilehash: ac1b94de4b439aab202d53b23b0d0da616a9f851
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58919617"
---
# <a name="how-to-model-and-partition-data-on-azure-cosmos-db-using-a-real-world-example"></a>Como a partição e o modelo de dados no Azure Cosmos DB com um exemplo real

Este artigo baseia-se em diversos conceitos do Azure Cosmos DB, como [modelação de dados](modeling-data.md), [criação de partições](partitioning-overview.md), e [débito aprovisionado](request-units.md) para demonstrar como lidar com um mundo real exercício de design de dados.

Se, normalmente, trabalha com bancos de dados relacionais, que provavelmente criou hábitos e intuitions sobre como criar um modelo de dados. Devido as restrições específicas, mas também os pontos fortes exclusivos do Azure Cosmos DB, a maioria dessas melhores práticas não traduzir bem e pode arrastar-lhe para soluções não ideais. O objetivo deste artigo é que os orientam durante o processo completo de modelar um caso de utilização do mundo real no Azure Cosmos DB, a partir do item de modelagem a colocalização de entidade e a criação de partições de contentor.

## <a name="the-scenario"></a>O cenário

Para este exercício, vamos considerar o domínio de uma plataforma de blogues onde *usuários* pode criar *publica*. Os utilizadores também podem *, como* e adicione *comentários* para essas postagens.

> [!TIP]
> Destacamos algumas palavras *itálico*; essas palavras identificar o tipo de "coisas", nosso modelo irá ter de manipular.

Adicionar mais requisitos ao nosso especificação:

- Uma página apresenta um feed de publicações recentemente criadas,
- Podemos pode obter todas as mensagens para um utilizador, todos os comentários para um post e todos os gostos para um post,
- Postagens são devolvidas com o nome de utilizador dos respetivos autores e uma contagem do número de comentários e gostos têm,
- Comentários e gostos também são devolvidos com o nome de utilizador dos utilizadores que criaram-los,
- Quando apresentado como listas, postagens só tem de apresentar um resumo truncado do seu conteúdo.

## <a name="identify-the-main-access-patterns"></a>Identificar os padrões de acesso de principal

Para começar, podemos dar alguma estrutura nosso especificação inicial ao identificar padrões de acesso de nossa solução. Ao conceber um modelo de dados para o Azure Cosmos DB, é importante compreender quais solicitações nosso modelo terá para servir para se certificar de que o modelo irá servir esses pedidos com eficiência.

Para facilitar o processo geral a seguir, vamos categorizar automaticamente esses esses pedidos diferentes como comandos ou consultas, tomando alguma vocabulário da [CQRS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation#Command_query_responsibility_segregation) onde comandos são pedidos (ou seja, intenções para atualizar o sistema) de escrita e as consultas são pedidos só de leitura.

Eis a lista de pedidos que a nossa plataforma terá de expor:

- **[C1]**  Criar/editar um utilizador
- **[Q1]**  Recupera um usuário
- **[C2]**  Criar/editar uma postagem
- **[Q2]**  Obter uma postagem
- **[P3]**  Lista postagens de um utilizador no formato curto
- **[C3]**  Criar um comentário
- **[P4]**  Listar comentários de uma postagem
- **[C4]**  Como uma postagem
- **[P5]**  Lista gostos de uma postagem
- **[P6]**  Lista os *x* postagens mais recentes em Resumo criado formulário (feed)

Como nesta fase, ainda não pensamos sobre o que cada entidade (utilizador, post etc.) que irá conter os detalhes. Este passo é normalmente entre as primeiras que a ser realizado ao projetar em relação a um arquivo relacional, uma vez que temos que descobrir como essas entidades se traduzirá em termos de tabelas, colunas, chaves estrangeiras, etc. É muito menos de um problema relacionado com uma base de dados de documento não impõe qualquer esquema na escrita.

A principal razão por que é importante identificar os nossos padrões de acesso desde o início, é porque esta lista de pedidos vai ser o nosso conjunto de testes. Sempre que iteramos nosso modelo de dados, iremos cada uma das solicitações e verificar o desempenho e escalabilidade.

## <a name="v1-a-first-version"></a>V1: Uma primeira versão

Vamos começar com dois contentores: `users` e `posts`.

### <a name="users-container"></a>Contentor de utilizadores

Este contentor só armazena os itens de utilizador:

    {
      "id": "<user-id>",
      "username": "<username>"
    }

Estamos neste contentor, de partição `id`, que significa que cada partição lógica no interior desse contêiner irá conter apenas um item.

### <a name="posts-container"></a>Contentor de mensagens

Este contentor aloja postagens, comentários e gosta de:

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

Estamos neste contentor, de partição `postId`, que significa que cada partição lógica no interior desse contêiner conterá uma postagem, todos os comentários para essa publicação e todos os gostos para essa publicação.

Tenha em atenção que introduzimos um `type` propriedade nos itens armazenados neste contentor para distinguir entre os três tipos de entidades que este anfitriões de contentor.

Além disso, escolhemos mencionam dados relacionados em vez de incorporá-lo (verifique [esta secção](modeling-data.md) para obter detalhes sobre esses conceitos) porque:

- não existe nenhum limite superior para um utilizador pode criar, de quantas postagens
- postagens podem ser a forma arbitrária, longas,
- não existe nenhum limite superior ao número de comentários e gostos pode ter uma postagem,
- Queremos poder adicionar um comentário ou uma semelhante a uma postagem sem ter de atualizar a postagem em si.

## <a name="how-well-does-our-model-perform"></a>A eficiência com que realiza o nosso modelo?

Agora é hora de avaliar o desempenho e escalabilidade de nossa primeira versão. Para cada um dos pedidos identificados anteriormente, vamos medir a latência e o número de pedidos unidades que consome. Esta medida é feita em relação a um conjunto de dados fictício que contém 100 000 utilizadores com postagens de 5 a 50 por utilizador e um máximo de 25 comentários e 100 gostos por mensagem.

### <a name="c1-createedit-a-user"></a>[C1] Criar/editar um utilizador

Este pedido é simples de implementar, podemos simplesmente criar ou atualizar um item para o `users` contentor. Os pedidos serão propagam-se muito bem todas as partições graças ao `id` chave de partição.

![Escrever um único item para o contentor de utilizadores](./media/how-to-model-partition-example/V1-C1.png)

| **Latência** | **Taxa de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 7 ms | 5.71 RU | ✅ |

### <a name="q1-retrieve-a-user"></a>[Q1] Obter um utilizador

Obter um utilizador é feito, lendo o item correspondente do `users` contentor.

![Obter um único item do contentor de utilizadores](./media/how-to-model-partition-example/V1-Q1.png)

| **Latência** | **Taxa de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 2 ms | 1 RU | ✅ |

### <a name="c2-createedit-a-post"></a>[C2] Criar/editar uma postagem

Tal **[C1]**, basta que escrever o `posts` contentor.

![Escrever um único item para o contentor de mensagens](./media/how-to-model-partition-example/V1-C2.png)

| **Latência** | **Taxa de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 9 ms | 8.76 RU | ✅ |

### <a name="q2-retrieve-a-post"></a>[Q2] Obter uma postagem

Vamos começar por obter o documento correspondente a partir do `posts` contentor. Mas isso não é suficiente, de acordo com nossa especificação também temos que agregar o nome de utilizador do autor da postagem e as contagens de quantos comentários e quantos gosta esta mensagem tiver, que requerem 3 emissão de consultas SQL adicionais.

![Recuperar uma postagem e agregação de dados adicionais](./media/how-to-model-partition-example/V1-Q2.png)

Cada um dos filtros de consultas adicionais na chave de partição do respetivo contentor respectivo, que é exatamente o que queremos maximizar o desempenho e escalabilidade. Mas, por fim, temos de executar operações de quatro para retornar uma única postagem, portanto, podemos irá melhorar que numa iteração seguinte.

| **Latência** | **Taxa de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 9 ms | 19.54 RU | ⚠ |

### <a name="q3-list-a-users-posts-in-short-form"></a>[P3] Lista de mensagens de um utilizador no formato curto

Em primeiro lugar, temos de obter as postagens pretendidas com uma consulta SQL que obtém as postagens correspondente a esse utilizador específico. Mas temos também emitir consultas adicionais para agregar o nome de utilizador do autor e as contagens de comentários e gostos.

![Todas as mensagens para um utilizador a obter e agregar os dados adicionais](./media/how-to-model-partition-example/V1-Q3.png)

Essa implementação apresenta várias desvantagens:

- as consultas ao agregar as contagens de comentários e gostos tem de ser emitidos para cada mensagem devolvida pela primeira consulta,
- a consulta principal não filtra a chave de partição do `posts` contentor, que leva a um fan-out e uma análise de partição entre o contentor.

| **Latência** | **Taxa de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 130 ms | 619.41 RU | ⚠ |

### <a name="c3-create-a-comment"></a>[C3] Criar um comentário

Um comentário é criado ao escrever o item correspondente `posts` contentor.

![Escrever um único item para o contentor de mensagens](./media/how-to-model-partition-example/V1-C2.png)

| **Latência** | **Taxa de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 7 ms | 8.57 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>[P4] Listar comentários de uma postagem

Vamos começar com uma consulta que obtém todos os comentários para essa publicação e mais uma vez, também precisamos agregado nomes de utilizador em separado para cada comentário.

![Obter todos os comentários para um post e agregar os dados adicionais](./media/how-to-model-partition-example/V1-Q4.png)

Embora a consulta principal filtrar numa chave de partição do contentor, agregar os nomes de utilizador em separado penalizes o desempenho geral. Podemos irá melhorar que mais tarde.

| **Latência** | **Taxa de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 23 ms | 27.72 RU | ⚠ |

### <a name="c4-like-a-post"></a>[C4] Como uma postagem

Tal como **[C3]**, podemos criar o item correspondente no `posts` contentor.

![Escrever um único item para o contentor de mensagens](./media/how-to-model-partition-example/V1-C2.png)

| **Latência** | **Taxa de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 6 ms | 7.05 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[P5] Listar gostos de uma postagem

Tal como **[P4]**, podemos consultar o "gostos" para essa mensagem, em seguida, agregar os nomes de utilizador.

![Recuperar todos gosta para um post e agregar os dados adicionais](./media/how-to-model-partition-example/V1-Q5.png)

| **Latência** | **Taxa de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 59 ms | 58.92 RU | ⚠ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[P6] Listar as postagens mais recentes de x criadas, em Resumo, o formulário (feed)

Podemos obter as postagens mais recentes, consultando o `posts` contentor ordenado por descendentes data de criação, em seguida, nomes de utilizador de agregação e contagens de comentários e gostos para cada uma das postagens.

![Postagens mais recentes de obtenção e agregar os dados adicionais](./media/how-to-model-partition-example/V1-Q6.png)

Mais uma vez, nossa consulta inicial não filtra a chave de partição do `posts` contentor, o que aciona um fan-out dispendiosa. Esse é ainda pior, como um conjunto de resultados muito maior de destino e ordenar os resultados com um `ORDER BY` cláusula, o que torna mais caro em termos de unidades de pedido.

| **Latência** | **Taxa de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 306 ms | 2063.54 RU | ⚠ |

## <a name="reflecting-on-the-performance-of-v1"></a>Refletindo sobre o desempenho do V1

Examinando os problemas de desempenho, nós nos deparamos com na secção anterior, podemos identificar duas classes principais de problemas:

- alguns pedidos exigem várias consultas a ser emitido para recolher todos os dados, precisamos voltar,
- a chave de partição dos contentores destinam-se, que leva a um fan-out que impede nosso escalabilidade não filtrar algumas consultas.

Vamos resolver cada um desses problemas, a partir do primeiro.

## <a name="v2-introducing-denormalization-to-optimize-read-queries"></a>V2: Introdução ao desnormalização para otimizar as consultas de leitura

O motivo por que temos para emitir pedidos adicionais em alguns casos é porque os resultados da solicitação inicial não contém todos os dados de retornar. Ao trabalhar com um arquivo de dados não relacionais, como o Azure Cosmos DB, esse tipo de problema é normalmente resolvido desnormalizando dados em nosso conjunto de dados.

No nosso exemplo, podemos modificar itens de post para adicionar o nome de utilizador do autor da postagem, a contagem de comentários e a contagem de gosta:

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

Também podemos modificar o comentário e, como itens para adicionar o nome de utilizador do utilizador que criou-los:

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

### <a name="denormalizing-comment-and-like-counts"></a>Desnormalização de comentário e, como contagens

O que queremos obter é que sempre que podemos adicionar um comentário ou um tipo, também incrementamos a `commentCount` ou o `likeCount` na publicação correspondente. Como nosso `posts` contentor particionado `postId`, o novo item (comentar ou, como) e seu post correspondente, estejam na mesma partição lógica. Como resultado, podemos usar um [procedimento armazenado](stored-procedures-triggers-udfs.md) para executar essa operação.

Agora durante a criação de um comentário (**[C3]**), em vez de apenas adicionar um novo item no `posts` contentor, chamamos o seguinte procedimento armazenado nesse contentor:

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

Este procedimento armazenado pega a ID da mensagem e o corpo do comentário novo como parâmetros, em seguida:

- obtém a mensagem
- incrementos a `commentCount`
- substitui a postagem
- Adiciona o novo comentário

Como procedimentos armazenados são executados como transações atômicas, é garantido que o valor de `commentCount` e o número real de comentários permanecerão sempre em sincronia.

Podemos, obviamente, chamar um procedimento armazenado semelhante quando adicionar novos gosta de incrementar a `likeCount`.

### <a name="denormalizing-usernames"></a>Desnormalizando os nomes de utilizador

Nomes de utilizador exigem uma abordagem diferente, pois os usuários não só manter-se em partições diferentes, mas num contêiner diferente. Quando temos de desnormalizar os dados em partições e contentores, podemos usar o contentor de origem [feed de alterações](change-feed.md).

No nosso exemplo, utilizamos o feed de alterações da `users` contentor reagir sempre que os usuários atualizam seus nomes de utilizador. Quando isso acontece, podemos propagar a alteração ao chamar outro procedimento armazenado no `posts` contentor:

![Desnormalizando os nomes de utilizador para o contentor de mensagens](./media/how-to-model-partition-example/denormalization-1.png)

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

Este procedimento armazenado pega a ID do usuário e o novo nome de utilizador do utilizador como parâmetros, em seguida:

- obtém todos os itens que correspondem a `userId` (que pode ser posta, comentários, ou gosta)
- para cada um desses itens
  - substitui o `userUsername`
  - substitui o item

> [!IMPORTANT]
> Esta operação é dispendiosa, porque requer este procedimento armazenado a ser executado em cada partição do `posts` contentor. Partimos do princípio de que a maioria dos usuários escolha um nome de utilizador adequada durante a inscrição e não nunca alterá-la, para que esta atualização será executado muito raramente.

## <a name="what-are-the-performance-gains-of-v2"></a>Quais são os ganhos de desempenho do V2?

### <a name="q2-retrieve-a-post"></a>[Q2] Obter uma postagem

Agora que nossos desnormalização está no lugar, temos apenas para obtenção de um único item para lidar com esse pedido.

![A obter um único item do contêiner postagens](./media/how-to-model-partition-example/V2-Q2.png)

| **Latência** | **Taxa de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 2 ms | 1 RU | ✅ |

### <a name="q4-list-a-posts-comments"></a>[P4] Listar comentários de uma postagem

Aqui, novamente, podemos pode poupá-os pedidos adicionais que obtido os nomes de utilizador e de fim de cópia de segurança com uma única consulta, que filtra a chave de partição.

![Obter todos os comentários para um post](./media/how-to-model-partition-example/V2-Q4.png)

| **Latência** | **Taxa de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 4 ms | 7.72 RU | ✅ |

### <a name="q5-list-a-posts-likes"></a>[P5] Listar gostos de uma postagem

Situação mesmo exata quando lista os gostos.

![Recuperar todos gosta para um post](./media/how-to-model-partition-example/V2-Q5.png)

| **Latência** | **Taxa de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 4 ms | 8.92 RU | ✅ |

## <a name="v3-making-sure-all-requests-are-scalable"></a>V3: Certificar-se a todos os pedidos são dimensionáveis

Ver a nossa aprimoramentos gerais do desempenho, ainda existem duas solicitações que ainda não totalmente otimizamos: **[P3]** e **[P6]**. Eles são os pedidos que envolvem a consultas que não filtrar na chave de partição dos contentores destinam-se.

### <a name="q3-list-a-users-posts-in-short-form"></a>[P3] Lista de mensagens de um utilizador no formato curto

Este pedido já se beneficia dos aprimoramentos introduzidos no V2, que spares consultas adicionais.

![Obter todas as mensagens para um utilizador](./media/how-to-model-partition-example/V2-Q3.png)

Mas a consulta restante ainda não está a filtrar na chave de partição do `posts` contentor.

A forma de pensar sobre esta situação é realmente simples:

1. Este pedido *tem* para filtrar o `userId` uma vez que queremos obter todas as mensagens para um utilizador específico
1. Não executa bem porque ele é executado em relação a `posts` contentor, o que não particionado `userId`
1. Declarando o óbvio, nós seria resolver o nosso problema de desempenho ao executar este pedido em relação a um contentor que *é* particionada por `userId`
1. Acontece que já temos um contentor de tal: o `users` contentor!

Portanto, apresentamos um segundo nível de desnormalização ao duplicar postagens completas para o `users` contentor. Com isso, podemos efetivamente obtenha uma cópia do nosso postagens, apenas particionados ao longo de um dimensões diferentes, tornando-os forma mais eficiente para obter seus `userId`.

O `users` contentor agora contém 2 tipos de itens:

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

- Introduzimos um `type` campo no item de utilizador para distinguir os usuários de postagens,
- também adicionámos um `userId` campo no item de utilizador, o que é redundante com o `id` campo, mas é necessário como o `users` contentor agora particionado `userId` (e não `id` conforme anteriormente)

Para atingir esse desnormalização, utilizamos mais uma vez o feed de alterações. Neste momento, estamos reagir no feed de alterações do `posts` contentor para expedir qualquer postagem de nova ou atualizada para o `users` contentor. E porque listagem postagens não requer para retornar o conteúdo completo, pode truncá-los no processo.

![Desnormalização de mensagens para o contentor de utilizadores](./media/how-to-model-partition-example/denormalization-2.png)

Agora podemos encaminhar nossa consulta para o `users` contentor, filtragem na chave de partição do contentor.

![Obter todas as mensagens para um utilizador](./media/how-to-model-partition-example/V3-Q3.png)

| **Latência** | **Taxa de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 4 ms | 6.46 RU | ✅ |

### <a name="q6-list-the-x-most-recent-posts-created-in-short-form-feed"></a>[P6] Listar as postagens mais recentes de x criadas, em Resumo, o formulário (feed)

Temos de lidar com uma situação semelhante aqui: mesmo depois de poupando as consultas adicionais deixado desnecessário pela desnormalização introduzida no V2, a consulta restante não filtra numa chave de partição do contentor:

![Postagens mais recentes a obter](./media/how-to-model-partition-example/V2-Q6.png)

Seguindo a mesma abordagem, maximizar o desempenho e escalabilidade este pedido requer que apenas chegar a uma partição. Isso é concebível porque temos apenas devolver um número limitado de itens; para preencher a página inicial de nossa plataforma de blogues, precisamos apenas obter as postagens mais recentes 100, sem a necessidade de paginar através de todo o conjunto de dados.

Portanto, para otimizar este último pedido, apresentamos um contentor de terceiro ao nosso design, inteiramente dedicado que serve este pedido. Podemos desnormalizar nosso postagens para que os novos `feed` contentor:

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

Este contentor particionado `type`, que será sempre `post` no nosso itens. Isso garante que todos os itens neste contentor ficará sem ser na mesma partição.

Para alcançar a desnormalização, temos apenas conectar-se sobre o pipeline anteriormente apresentamos para expedir as mensagens para esse novo contentor do feed de alterações. Uma coisa importante para bare em mente é que temos de certificar-se de que apenas armazenamos as postagens mais recentes 100; caso contrário, o conteúdo do contentor pode aumentar o tamanho máximo de uma partição. Isso é feito chamando uma [pós-acionador](stored-procedures-triggers-udfs.md#triggers) sempre que um documento for adicionado no contentor:

![Desnormalização de mensagens para o contentor de feed](./media/how-to-model-partition-example/denormalization-3.png)

Este é o corpo do pós-acionador que trunca a coleção:

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

A etapa final é redirecionar a nossa consulta para nossa nova `feed` contentor:

![Postagens mais recentes a obter](./media/how-to-model-partition-example/V3-Q6.png)

| **Latência** | **Taxa de RU** | **Performance** (Desempenho) |
| --- | --- | --- |
| 9 ms | 16.97 RU | ✅ |

## <a name="conclusion"></a>Conclusão

Vamos dar uma olhada no aprimoramentos gerais do desempenho e escalabilidade que introduzimos sobre as diferentes versões do nosso projeto.

| | V1 | V2 | V3 |
| --- | --- | --- | --- |
| **[C1]** | 7 ms / 5.71 RU | 7 ms / 5.71 RU | 7 ms / 5.71 RU |
| **[Q1]** | 2 ms / 1 RU | 2 ms / 1 RU | 2 ms / 1 RU |
| **[C2]** | 9 ms / 8.76 RU | 9 ms / 8.76 RU | 9 ms / 8.76 RU |
| **[Q2]** | 9 ms / 19.54 RU | 2 ms / 1 RU | 2 ms / 1 RU |
| **[Q3]** | 130 ms / 619.41 RU | 28 de ms / 201.54 RU | 4 ms / 6.46 RU |
| **[C3]** | 7 ms / 8.57 RU | 7 ms / 15.27 RU | 7 ms / 15.27 RU |
| **[Q4]** | 23 ms / 27.72 RU | 4 ms / 7.72 RU | 4 ms / 7.72 RU |
| **[C4]** | 6 ms / 7.05 RU | 7 ms / 14.67 RU | 7 ms / 14.67 RU |
| **[Q5]** | 59 ms / 58.92 RU | 4 ms / 8.92 RU | 4 ms / 8.92 RU |
| **[Q6]** | 306 ms / 2063.54 RU | 83 ms / 532.33 RU | 9 ms / 16.97 RU |

### <a name="we-have-optimized-a-read-heavy-scenario"></a>Nós otimizamos um cenário de leitura intensiva

Talvez tenha notado que podemos ter concentrated nossos esforços em direção à melhoria do desempenho das solicitações de leitura (consultas) às custas de pedidos de escrita (comandos). Em muitos casos, operações de escrita acionam agora desnormalização subsequente através de feeds de mudança, que as torna mais computacionalmente caras e mais tempo para materializar.

Isso é justificado pelo fato de que uma plataforma de blogues (como as aplicações mais sociais) é de leitura intensiva, que significa que a quantidade de solicitações de leitura, que ele deve servir normalmente é mais do que a quantidade de pedidos de escrita de ordens de magnitude. Por isso faz sentido fazer mais dispendiosa para deixar de pedidos de escrita de solicitações de leitura ser mais barato e melhor desempenho.

Se verificarmos até extreme optimization que fizemos **[P6]** passou de 2000 + RUs para apenas 17 RUs; alcançámos que o desnormalizando postagens a um custo de cerca de 10 RU por item. Como podemos serviria solicitações de feeds muito mais do que a criação ou atualização de postagens, o custo deste desnormalização é irrisório Considerando as poupanças geral.

### <a name="denormalization-can-be-applied-incrementally"></a>Desnormalização pode ser aplicada de forma incremental

Os aprimoramentos de escalabilidade que exploramos neste artigo envolvem desnormalização e eliminação de duplicados de dados entre o conjunto de dados. É importante observar que essas otimizações não tem de ser colocadas em vigor no dia 1. Consultas que filtrarão nas chaves de partição têm um desempenho melhor em escala, mas as consultas entre partições podem ser totalmente aceitáveis se forem chamados raramente ou em relação a um conjunto de dados limitado. Se estiver apenas criando um protótipo ou iniciar um produto com uma pequena e controlado base de usuários, provavelmente pode sobra essas melhorias para utilizar mais tarde; o que é importante, em seguida, vai [monitor](use-metrics.md) desempenho de seu modelo para que possa decidir se e quando é hora de colocá-los.

A alteração do feed que utilizamos para distribuir atualizações para outro arquivo de contentores todas as atualizações de forma permanente. Isto torna possível solicitar todas as atualizações desde a criação do contentor e modos de exibição desnormalizados bootstrap como uma única operação catch-up, mesmo que o sistema já tem uma grande quantidade de dados.

## <a name="next-steps"></a>Passos Seguintes

Depois desta introdução a práticas dados de modelagem e criação de partições, talvez queira verificar os seguintes artigos para rever os conceitos abrangemos:

- [Trabalhar com bancos de dados, contentores e itens](databases-containers-items.md)
- [Criação de partições no Azure Cosmos DB](partitioning-overview.md)
- [Alterar feed no Azure Cosmos DB](change-feed.md)