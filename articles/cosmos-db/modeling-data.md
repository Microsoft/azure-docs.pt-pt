---
title: Dados de modelação em Azure Cosmos DB
titleSuffix: Azure Cosmos DB
description: Saiba mais sobre a modelação de dados nas bases de dados NoSQL, diferenças entre dados de modelação numa base de dados relacional e uma base de dados de documentos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a141177846def9c94216684c1083d0d336eeda1e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93333261"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Modelação de dados em Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Enquanto as bases de dados isentas de esquemas, como a Azure Cosmos DB, facilitam a armazenagem e consulta de dados não estruturados e semi-estruturados, deve passar algum tempo a pensar no seu modelo de dados para tirar o máximo partido do serviço em termos de desempenho e escalabilidade e menor custo.

Como é que os dados vão ser armazenados? Como é que a sua aplicação vai recuperar e consultar dados? A sua candidatura é pesada ou pesada?

Depois de ler este artigo, poderá responder às seguintes perguntas:

* O que é modelação de dados e por que devo me importar?
* Como é que os dados de modelação em Azure Cosmos DB são diferentes de uma base de dados relacional?
* Como expresso as relações de dados numa base de dados não relacional?
* Quando ensoo dados e quando faço a ligação aos dados?

## <a name="embedding-data"></a>Incorporação de dados

Quando começar a modelar dados na Azure Cosmos, tente tratar as suas entidades como **itens independentes representados** como documentos JSON.

Para comparação, vamos primeiro ver como podemos modelar dados numa base de dados relacional. O exemplo a seguir mostra como uma pessoa pode ser armazenada numa base de dados relacional.

:::image type="content" source="./media/sql-api-modeling-data/relational-data-model.png" alt-text="Modelo de base de dados relacional" border="false":::

Ao trabalhar com bases de dados relacionais, a estratégia é normalizar todos os seus dados. Normalizar os seus dados normalmente envolve pegar numa entidade, como uma pessoa, e decompô-lo em componentes discretos. No exemplo acima, uma pessoa pode ter vários registos de detalhes de contacto, bem como vários registos de endereços. Os dados de contacto podem ser ainda mais desafinados através da extração de campos comuns como um tipo. O mesmo se aplica ao endereço, cada registo pode ser do tipo *Home* ou *Business*.

A premissa orientadora para a normalização dos dados é **evitar armazenar dados redundantes** em cada registo e, em vez disso, consultar os dados. Neste exemplo, para ler uma pessoa, com todos os seus dados de contacto e endereços, é necessário utilizar o JOINS para compor (ou desnormalizar) os seus dados no tempo de execução.

```sql
SELECT p.FirstName, p.LastName, a.City, cd.Detail
FROM Person p
JOIN ContactDetail cd ON cd.PersonId = p.Id
JOIN ContactDetailType cdt ON cdt.Id = cd.TypeId
JOIN Address a ON a.PersonId = p.Id
```

Atualizar uma única pessoa com os seus dados de contacto e endereços requer operações de escrita em muitas tabelas individuais.

Agora vamos ver como modelaríamos os mesmos dados que uma entidade independente em Azure Cosmos DB.

```json
{
    "id": "1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "addresses": [
        {
            "line1": "100 Some Street",
            "line2": "Unit 1",
            "city": "Seattle",
            "state": "WA",
            "zip": 98012
        }
    ],
    "contactDetails": [
        {"email": "thomas@andersen.com"},
        {"phone": "+1 555 555-5555", "extension": 5555}
    ]
}
```

Utilizando a abordagem acima, **desnormalizámos** o registo da pessoa, **incorporando** todas as informações relacionadas com esta pessoa, tais como os seus dados de contacto e endereços, num único documento *JSON.*
Além disso, como não estamos confinados a um esquema fixo, temos a flexibilidade para fazer coisas como ter detalhes de contacto de diferentes formas inteiramente.

Recuperar um registo completo da base de dados é agora uma **única operação de leitura** contra um único recipiente e para um único item. Atualizar um registo de pessoa, com os seus dados de contacto e endereços, é também uma **única operação de escrita** contra um único item.

Ao desnormalizar os dados, a sua aplicação poderá necessitar de emitir menos consultas e atualizações para completar as operações comuns.

### <a name="when-to-embed"></a>Quando incorporar

Em geral, utilize modelos de dados incorporados quando:

* Existem relações **contidas** entre entidades.
* Há **relações entre entidades.**
* Há dados incorporados que **mudam raramente.**
* Há dados incorporados que não crescerão **sem limites.**
* Há dados incorporados que **são consultados frequentemente em conjunto.**

> [!NOTE]
> Os modelos de dados tipicamente desnormalizados proporcionam um melhor desempenho **de leitura.**

### <a name="when-not-to-embed"></a>Quando não incorporar

Enquanto a regra do polegar em Azure Cosmos DB é desnormalizar tudo e incorporar todos os dados num único item, isso pode levar a algumas situações que devem ser evitadas.

Pegue este corte JSON.

```json
{
    "id": "1",
    "name": "What's new in the coolest Cloud",
    "summary": "A blog post by someone real famous",
    "comments": [
        {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
        {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
        …
        {"id": 100001, "author": "jane", "comment": "and on we go ..."},
        …
        {"id": 1000000001, "author": "angry", "comment": "blah angry blah angry"},
        …
        {"id": ∞ + 1, "author": "bored", "comment": "oh man, will this ever end?"},
    ]
}
```

Isto pode ser o que uma entidade de correio com comentários embutidos seria se estivéssemos modelando um blog típico, ou CMS, sistema. O problema com este exemplo é que o conjunto de comentários não é **limitado,** o que significa que não existe um limite (prático) para o número de comentários que qualquer publicação pode ter. Isto pode tornar-se um problema, uma vez que o tamanho do item pode crescer infinitamente grande.

À medida que o tamanho do item aumenta, a capacidade de transmitir os dados através do fio, bem como a leitura e a atualização do item, em escala, serão impactados.

Neste caso, seria melhor considerar o seguinte modelo de dados.

```json
Post item:
{
    "id": "1",
    "name": "What's new in the coolest Cloud",
    "summary": "A blog post by someone real famous",
    "recentComments": [
        {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
        {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
        {"id": 3, "author": "jane", "comment": "....."}
    ]
}

Comment items:
{
    "postId": "1"
    "comments": [
        {"id": 4, "author": "anon", "comment": "more goodness"},
        {"id": 5, "author": "bob", "comment": "tails from the field"},
        ...
        {"id": 99, "author": "angry", "comment": "blah angry blah angry"}
    ]
},
{
    "postId": "1"
    "comments": [
        {"id": 100, "author": "anon", "comment": "yet more"},
        ...
        {"id": 199, "author": "bored", "comment": "will this ever end?"}
    ]
}
```

Este modelo tem os três comentários mais recentes incorporados no contentor do poste, que é um conjunto com um conjunto fixo de atributos. Os outros comentários são agrupados em lotes de 100 comentários e armazenados como itens separados. O tamanho do lote foi escolhido como 100 porque a nossa aplicação fictícia permite ao utilizador carregar 100 comentários de cada vez.  

Outro caso em que a incorporação de dados não é uma boa ideia é quando os dados incorporados são frequentemente utilizados em itens e mudam frequentemente.

Pegue este corte JSON.

```json
{
    "id": "1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "holdings": [
        {
            "numberHeld": 100,
            "stock": { "symbol": "zaza", "open": 1, "high": 2, "low": 0.5 }
        },
        {
            "numberHeld": 50,
            "stock": { "symbol": "xcxc", "open": 89, "high": 93.24, "low": 88.87 }
        }
    ]
}
```

Isto pode representar a carteira de ações de uma pessoa. Optámos por incorporar a informação de stock em cada documento de portfólio. Num ambiente em que os dados relacionados estão a mudar frequentemente, como uma aplicação de negociação de ações, incorporar dados que mudam frequentemente vai significar que está constantemente a atualizar cada documento de carteira sempre que uma ação é negociada.

As *ações podem* ser negociadas muitas centenas de vezes num só dia e milhares de utilizadores podem ter *zaza* no seu portfólio. Com um modelo de dados como o acima, teríamos de atualizar muitos milhares de documentos de portfólio muitas vezes por dia, levando a um sistema que não vai escalar bem.

## <a name="referencing-data"></a>Referenciação de dados

A incorporação de dados funciona bem para muitos casos, mas existem cenários quando a desnormalização dos seus dados irá causar mais problemas do que vale. Então, o que fazemos agora?

As bases de dados relacionais não são o único local onde se podem criar relações entre entidades. Numa base de dados de documentos, pode ter informações num documento que se relacione com dados de outros documentos. Não recomendamos sistemas de construção que sejam mais adequados a uma base de dados relacional em Azure Cosmos DB, ou qualquer outra base de dados de documentos, mas relações simples são boas e podem ser úteis.

No JSON abaixo escolhemos usar o exemplo de uma carteira de ações de antes, mas desta vez referimo-nos à rubrica de ações da carteira em vez de a incorporar. Desta forma, quando o item de stock muda frequentemente ao longo do dia, o único documento que precisa de ser atualizado é o documento único de stock.

```json
Person document:
{
    "id": "1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "holdings": [
        { "numberHeld":  100, "stockId": 1},
        { "numberHeld":  50, "stockId": 2}
    ]
}

Stock documents:
{
    "id": "1",
    "symbol": "zaza",
    "open": 1,
    "high": 2,
    "low": 0.5,
    "vol": 11970000,
    "mkt-cap": 42000000,
    "pe": 5.89
},
{
    "id": "2",
    "symbol": "xcxc",
    "open": 89,
    "high": 93.24,
    "low": 88.87,
    "vol": 2970200,
    "mkt-cap": 1005000,
    "pe": 75.82
}
```

Uma desvantagem imediata para esta abordagem é, no entanto, se a sua aplicação é necessária para mostrar informações sobre cada ação que é detida ao exibir o portfólio de uma pessoa; neste caso, você precisaria fazer várias viagens à base de dados para carregar a informação para cada documento de stock. Aqui tomámos a decisão de melhorar a eficiência das operações de escrita, que acontecem frequentemente ao longo do dia, mas por sua vez comprometidas nas operações de leitura que potencialmente têm menos impacto no desempenho deste sistema em particular.

> [!NOTE]
> Os modelos de dados normalizados **podem exigir mais viagens de ida e volta** ao servidor.

### <a name="what-about-foreign-keys"></a>E as chaves estrangeiras?

Uma vez que não existe atualmente um conceito de restrição, chave estrangeira ou outra, quaisquer relações inter-documentadas que tenha nos documentos são efetivamente "ligações fracas" e não serão verificadas pela própria base de dados. Se pretender garantir que os dados a que um documento se refere realmente existem, então tem de o fazer na sua aplicação, ou através da utilização de gatilhos do lado do servidor ou de procedimentos armazenados no Azure Cosmos DB.

### <a name="when-to-reference"></a>Quando referência

Em geral, utilize modelos de dados normalizados quando:

* Representando **relacionamentos de um a muitos.**
* Representando **muitas** relações.
* Os dados relacionados **mudam frequentemente.**
* Os dados referenciados podem não **ser limitados.**

> [!NOTE]
> Normalmente, a normalização proporciona um melhor desempenho **de escrita.**

### <a name="where-do-i-put-the-relationship"></a>Onde coloco a relação?

O crescimento da relação ajudará a determinar em que documento armazenar a referência.

Se olharmos para o JSON abaixo que modelos editores e livros.

```json
Publisher document:
{
    "id": "mspress",
    "name": "Microsoft Press",
    "books": [ 1, 2, 3, ..., 100, ..., 1000]
}

Book documents:
{"id": "1", "name": "Azure Cosmos DB 101" }
{"id": "2", "name": "Azure Cosmos DB for RDBMS Users" }
{"id": "3", "name": "Taking over the world one JSON doc at a time" }
...
{"id": "100", "name": "Learn about Azure Cosmos DB" }
...
{"id": "1000", "name": "Deep Dive into Azure Cosmos DB" }
```

Se o número de livros por editora for pequeno e com um crescimento limitado, então armazenar a referência do livro dentro do documento da editora pode ser útil. No entanto, se o número de livros por editora não for limitado, então este modelo de dados levaria a matrizes mutáveis e crescentes, como no documento de editor de exemplo acima.

Mudar um pouco as coisas resultaria num modelo que ainda representa os mesmos dados, mas que agora evita estas grandes coleções mutáveis.

```json
Publisher document:
{
    "id": "mspress",
    "name": "Microsoft Press"
}

Book documents:
{"id": "1","name": "Azure Cosmos DB 101", "pub-id": "mspress"}
{"id": "2","name": "Azure Cosmos DB for RDBMS Users", "pub-id": "mspress"}
{"id": "3","name": "Taking over the world one JSON doc at a time"}
...
{"id": "100","name": "Learn about Azure Cosmos DB", "pub-id": "mspress"}
...
{"id": "1000","name": "Deep Dive into Azure Cosmos DB", "pub-id": "mspress"}
```

No exemplo acima, deixámos cair a coleção ilimitada no documento da editora. Em vez disso, temos apenas uma referência ao editor em cada documento do livro.

### <a name="how-do-i-model-manymany-relationships"></a>Como faço para modelar muitas: muitas relações?

Numa base de *dados relacional, muitas: muitas* relações são muitas vezes modeladas com mesas de junção, que apenas juntam registos de outras mesas.


:::image type="content" source="./media/sql-api-modeling-data/join-table.png" alt-text="Junte-se às mesas" border="false":::

Pode sentir-se tentado a replicar a mesma coisa usando documentos e produzir um modelo de dados semelhante ao seguinte.

```json
Author documents:
{"id": "a1", "name": "Thomas Andersen" }
{"id": "a2", "name": "William Wakefield" }

Book documents:
{"id": "b1", "name": "Azure Cosmos DB 101" }
{"id": "b2", "name": "Azure Cosmos DB for RDBMS Users" }
{"id": "b3", "name": "Taking over the world one JSON doc at a time" }
{"id": "b4", "name": "Learn about Azure Cosmos DB" }
{"id": "b5", "name": "Deep Dive into Azure Cosmos DB" }

Joining documents:
{"authorId": "a1", "bookId": "b1" }
{"authorId": "a2", "bookId": "b1" }
{"authorId": "a1", "bookId": "b2" }
{"authorId": "a1", "bookId": "b3" }
```

Isto ia funcionar. No entanto, carregar um autor com os seus livros, ou carregar um livro com o seu autor, exigiria sempre pelo menos duas consultas adicionais contra a base de dados. Uma consulta ao documento de adesão e, em seguida, outra consulta para obter o documento real sendo aderido.

Se tudo o que esta mesa de aderimento está a fazer é colar dois dados, então porque não deixá-lo cair completamente?
Considere o seguinte.

```json
Author documents:
{"id": "a1", "name": "Thomas Andersen", "books": ["b1", "b2", "b3"]}
{"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

Book documents:
{"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
{"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
{"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
{"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}
```

Agora, se eu tivesse um autor, eu imediatamente sei que livros eles escreveram, e inversamente se eu tivesse um documento de livro carregado eu saberia as iDs do(s) autor(s). Isto evita que a consulta intermediária contra a tabela de adesão reduza o número de viagens de ida e volta do servidor que a sua aplicação tem de fazer.

## <a name="hybrid-data-models"></a>Modelos de dados híbridos

Agora, olhamos para a incorporação (ou desnormalização) e referenciando (ou normalizando) dados, cada um tem as suas vantagens e cada um tem compromissos como vimos.

Nem sempre tem que ser ou.não tenha medo de misturar um pouco as coisas.

Com base nos padrões de utilização e cargas de trabalho específicos da sua aplicação, pode haver casos em que misturar dados incorporados e referenciados faz sentido e pode levar a uma lógica de aplicação mais simples com menos viagens de ida e volta do servidor, mantendo ainda um bom nível de desempenho.

Considere o seguinte JSON.

```json
Author documents:
{
    "id": "a1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "countOfBooks": 3,
    "books": ["b1", "b2", "b3"],
    "images": [
        {"thumbnail": "https://....png"}
        {"profile": "https://....png"}
        {"large": "https://....png"}
    ]
},
{
    "id": "a2",
    "firstName": "William",
    "lastName": "Wakefield",
    "countOfBooks": 1,
    "books": ["b1"],
    "images": [
        {"thumbnail": "https://....png"}
    ]
}

Book documents:
{
    "id": "b1",
    "name": "Azure Cosmos DB 101",
    "authors": [
        {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
        {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "https://....png"}
    ]
},
{
    "id": "b2",
    "name": "Azure Cosmos DB for RDBMS Users",
    "authors": [
        {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
    ]
}
```

Aqui seguimos (principalmente) o modelo incorporado, onde os dados de outras entidades estão incorporados no documento de alto nível, mas outros dados são referenciados.

Se olharmos para o documento do livro, podemos ver alguns campos interessantes quando olhamos para a variedade de autores. Há um `id` campo que é o campo que usamos para remeter para um documento de autor, prática padrão num modelo normalizado, mas então nós também temos e `name` `thumbnailUrl` . Poderíamos ter ficado `id` e deixado a aplicação para obter qualquer informação adicional que precisasse do respetivo documento de autor usando o "link", mas como a nossa aplicação exibe o nome do autor e uma foto de miniatura com cada livro exibido podemos salvar uma ida e volta ao servidor por livro numa lista, desnormalizando **alguns** dados do autor.

Claro, se o nome do autor mudasse ou quisesse atualizar a sua foto, teríamos de ir atualizar todos os livros que já publicaram, mas para a nossa aplicação, com base no pressuposto de que os autores não mudam frequentemente os seus nomes, esta é uma decisão de design aceitável.  

No exemplo, **existem valores agregados pré-calculados** para poupar um processamento dispendioso numa operação de leitura. No exemplo, alguns dos dados incorporados no documento de autor são dados que são calculados em tempo de execução. Sempre que um novo livro é publicado, um documento de livro é criado **e** o campo countOfBooks é definido para um valor calculado com base no número de documentos de livro que existem para um determinado autor. Esta otimização seria boa na leitura de sistemas pesados onde podemos dar-nos ao luxo de fazer cálculos em escritas de forma a otimizar as leituras.

A capacidade de ter um modelo com campos pré-calculados é possível porque a Azure Cosmos DB suporta **transações multi-documentos.** Muitas lojas NoSQL não podem fazer transações através de documentos e, portanto, defendem decisões de design, como "incorporar sempre tudo", devido a esta limitação. Com o Azure Cosmos DB, pode utilizar gatilhos do lado do servidor, ou procedimentos armazenados, que inserem livros e atualizam autores numa transação ACID. Agora não **precisa** incorporar tudo num só documento apenas para ter certeza de que os seus dados permanecem consistentes.

## <a name="distinguishing-between-different-document-types"></a>Distinção entre diferentes tipos de documentos

Em alguns cenários, poderá querer misturar diferentes tipos de documentos na mesma coleção; este é geralmente o caso quando você quer vários documentos relacionados para sentar-se na mesma [divisória](partitioning-overview.md). Por exemplo, pode colocar livros e críticas de livros na mesma coleção e dividi-lo `bookId` por. Nesta situação, normalmente pretende adicionar aos seus documentos um campo que identifique o seu tipo de forma a diferenciá-los.

```json
Book documents:
{
    "id": "b1",
    "name": "Azure Cosmos DB 101",
    "bookId": "b1",
    "type": "book"
}

Review documents:
{
    "id": "r1",
    "content": "This book is awesome",
    "bookId": "b1",
    "type": "review"
},
{
    "id": "r2",
    "content": "Best book ever!",
    "bookId": "b1",
    "type": "review"
}
```

## <a name="next-steps"></a>Passos seguintes

Os maiores takeaways deste artigo são entender que a modelação de dados num mundo livre de esquemas é tão importante como sempre.

Da mesma forma que não existe uma única forma de representar um pedaço de dados num ecrã, não existe uma única forma de modelar os seus dados. Precisa entender a sua aplicação e como irá produzir, consumir e processar os dados. Em seguida, aplicando algumas das diretrizes aqui apresentadas, pode definir-se a criação de um modelo que responda às necessidades imediatas da sua aplicação. Quando as suas aplicações precisam de ser alteradas, pode aproveitar a flexibilidade de uma base de dados sem esquemas para abraçar essa mudança e evoluir facilmente o seu modelo de dados.

Para saber mais sobre a Azure Cosmos DB, consulte a página de [documentação](https://azure.microsoft.com/documentation/services/cosmos-db/) do serviço.

Para entender como fragmentos os seus dados através de múltiplas divisórias, consulte [dados de partição em Azure Cosmos DB](partitioning-overview.md).

Para aprender a modelar e dividir dados sobre Azure Cosmos DB usando um exemplo do mundo real, consulte a [ Modelação de Dados e Partição - um exemplo Real-World](how-to-model-partition-example.md).
