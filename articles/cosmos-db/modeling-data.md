---
title: Dados de modelação em Azure Cosmos DB
titleSuffix: Azure Cosmos DB
description: Conheça a modelação de dados nas bases de dados noSQL, diferenças entre modelar dados numa base de dados relacional e uma base de dados de documentos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 523049ea3286445117f41147f3dd12a2c911d1ae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72755020"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Modelação de dados em Azure Cosmos DB

Enquanto as bases de dados sem esquemas, como o Azure Cosmos DB, tornam super fácil armazenar e consultar dados não estruturados e semi-estruturados, deve passar algum tempo a pensar no seu modelo de dados para tirar o máximo partido do serviço em termos de desempenho e escalabilidade e custo mais baixo.

Como é que os dados vão ser armazenados? Como é que a sua aplicação vai recuperar e consultar dados? A sua aplicação é pesada ou pesada?

Depois de ler este artigo, poderá responder às seguintes perguntas:

* O que é modelação de dados e por que devo me importar?
* Como é que a modelação de dados no Azure Cosmos DB é diferente de uma base de dados relacional?
* Como expresso relações de dados numa base de dados não relacional?
* Quando é que insinto dados e quando é que me ligarei aos dados?

## <a name="embedding-data"></a>Dados de incorporação

Quando começar a modelar dados em Azure Cosmos DB tente tratar as suas entidades como **itens independentes representados** como documentos JSON.

Para comparação, vamos primeiro ver como podemos modelar dados numa base de dados relacional. O exemplo que se segue mostra como uma pessoa pode ser armazenada numa base de dados relacional.

![Modelo de base de dados relacional](./media/sql-api-modeling-data/relational-data-model.png)

Ao trabalhar com bases de dados relacionais, a estratégia é normalizar todos os seus dados. Normalizar os seus dados normalmente envolve tomar uma entidade, como uma pessoa, e decompartá-la em componentes discretos. No exemplo acima, uma pessoa pode ter múltiplos registos de detalhes de contacto, bem como vários registos de endereços. Os dados de contacto podem ser ainda mais discriminados através da extração de campos comuns como um tipo. O mesmo se aplica ao endereço, cada registo pode ser do tipo *Casa* ou *Negócios.*

A premissa orientadora ao normalizar os dados é **evitar armazenar dados redundantes** em cada registo e antes consultar os dados. Neste exemplo, para ler uma pessoa, com todos os seus dados de contacto e endereços, é necessário utilizar o JOINS para compor eficazmente (ou desnormalizar) os seus dados no momento de funcionamento.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Atualizar uma única pessoa com os seus dados de contacto e endereços requer a escrita de operações em muitas tabelas individuais.

Agora vamos ver como modelaríamos os mesmos dados que uma entidade independente no Azure Cosmos DB.

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

Utilizando a abordagem acima, **desnormalizámos** o registo da pessoa, **incorporando** todas as informações relacionadas com esta pessoa, tais como os seus dados de contacto e endereços, num único documento *da JSON.*
Além disso, como não estamos confinados a um esquema fixo, temos a flexibilidade para fazer coisas como ter detalhes de contacto de diferentes formas inteiramente.

Recuperar um registo completo de pessoas da base de dados é agora uma **operação de leitura única** contra um único recipiente e para um único item. Atualizar um registo de uma pessoa, com os seus dados de contacto e endereços, é também uma **única operação de escrita** contra um único item.

Ao desnormalizar os dados, a sua aplicação poderá ter de emitir menos consultas e atualizações para completar operações comuns.

### <a name="when-to-embed"></a>Quando incorporar

Em geral, utilize modelos de dados incorporados quando:

* Existem relações **contidas** entre entidades.
* **Há** relações entre entidades.
* Há dados incorporados que **mudam com pouca frequência.**
* Há dados incorporados que não crescerão **sem limites.**
* Há dados incorporados que são **consultados frequentemente em conjunto.**

> [!NOTE]
> Os modelos de dados tipicamente desnormalizados proporcionam um melhor desempenho **de leitura.**

### <a name="when-not-to-embed"></a>Quando não incorporar

Embora a regra do polegar em Azure Cosmos DB seja desnormalizar tudo e incorporar todos os dados num único item, isso pode levar a algumas situações que devem ser evitadas.

Pegue este corte JSON.

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

Isto pode ser o que uma entidade postcom comentários incorporados seria se estivéssemos modelando um blog típico, ou CMS, sistema. O problema deste exemplo é que o conjunto de comentários não está **limitado,** o que significa que não existe um limite (prático) para o número de comentários que qualquer publicação pode ter. Isto pode tornar-se um problema, uma vez que o tamanho do item pode crescer infinitamente grande.

À medida que o tamanho do item aumenta a capacidade de transmitir os dados sobre o fio, bem como a leitura e a atualização do item, à escala, será impactado.

Neste caso, seria prefere considerar o seguinte modelo de dados.

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

Este modelo tem os três comentários mais recentes incorporados no contentor do correio, que é um conjunto com um conjunto fixo de atributos. Os outros comentários são agrupados em lotes de 100 comentários e armazenados como itens separados. O tamanho do lote foi escolhido como 100 porque a nossa aplicação fictícia permite ao utilizador carregar 100 comentários de cada vez.  

Outro caso em que incorporar dados não é uma boa ideia é quando os dados incorporados são frequentemente utilizados em itens e mudam frequentemente.

Pegue este corte JSON.

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

Isto pode representar o portfólio de ações de uma pessoa. Optámos por incorporar a informação sobre as ações em cada documento de carteira. Num ambiente em que os dados relacionados estão a mudar frequentemente, como uma aplicação de negociação de ações, incorporar dados que mudam frequentemente significa que está constantemente a atualizar cada documento de carteira sempre que uma ação é negociada.

O *stock zaza* pode ser negociado muitas centenas de vezes num só dia e milhares de utilizadores podem ter *zaza* no seu portfólio. Com um modelo de dados como o acima referido teríamos de atualizar muitos milhares de documentos de portfólio muitas vezes todos os dias, levando a um sistema que não escalaria bem.

## <a name="referencing-data"></a>Dados de referência

Incorporar dados funciona bem para muitos casos, mas existem cenários para desnormalizar os seus dados causará mais problemas do que vale. Então, o que fazemos agora?

As bases de dados relacionais não são o único local onde se pode criar relações entre entidades. Numa base de dados de documentos, pode ter informações num documento relacionado com dados de outros documentos. Não recomendamos sistemas de construção que sejam mais adequados a uma base de dados relacional em Azure Cosmos DB, ou qualquer outra base de dados documental, mas relacionamentos simples são bons e podem ser úteis.

No JSON abaixo escolhemos usar o exemplo de uma carteira de ações de antes, mas desta vez referimo-nos ao item de stock da carteira em vez de o incorporarmos. Desta forma, quando o item de stock muda frequentemente ao longo do dia, o único documento que precisa de ser atualizado é o documento de stock único.

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

Uma desvantagem imediata para esta abordagem é se a sua aplicação for necessária para mostrar informações sobre cada stock que é detido ao exibir o portfólio de uma pessoa; neste caso, teria de fazer múltiplas viagens à base de dados para carregar a informação para cada documento de stock. Aqui tomámos a decisão de melhorar a eficiência das operações de escrita, que acontecem frequentemente ao longo do dia, mas por sua vez comprometidas nas operações de leitura que potencialmente têm menos impacto no desempenho deste sistema em particular.

> [!NOTE]
> Os modelos de dados normalizados **podem exigir mais viagens de ida e volta** ao servidor.

### <a name="what-about-foreign-keys"></a>E as chaves estrangeiras?

Uma vez que não existe atualmente um conceito de restrição, chave estrangeira ou não, quaisquer relações inter-documentais que tenha em documentos são efetivamente "ligações fracas" e não serão verificadas pela própria base de dados. Se pretender garantir que os dados a que um documento se refere realmente existe, então tem de o fazer na sua aplicação, ou através da utilização de gatilhos do lado do servidor ou procedimentos armazenados no Azure Cosmos DB.

### <a name="when-to-reference"></a>Quando fazer referência

Em geral, utilize modelos de dados normalizados quando:

* Representando **relacionamentos** de um a muitos.
* Representando **muitas a muitas** relações.
* Alterações de dados **relacionadas frequentemente**.
* Os dados referenciados podem ser **ilimitados.**

> [!NOTE]
> Normalmente, a normalização proporciona um melhor desempenho **de escrita.**

### <a name="where-do-i-put-the-relationship"></a>Onde coloco a relação?

O crescimento da relação ajudará a determinar em que documento armazenar a referência.

Se olharmos para o JSON abaixo que modela editores e livros.

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

Se o número de livros por editor for pequeno e com um crescimento limitado, então armazenar a referência do livro dentro do documento da editora pode ser útil. No entanto, se o número de livros por editora não estiver limitado, então este modelo de dados conduziria a matrizes mutáveis e em crescimento, como no documento de editor de exemplo acima.

Mudar um pouco as coisas resultaria num modelo que ainda representa os mesmos dados, mas agora evita estas grandes coleções mutáveis.

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

No exemplo acima, deixámos cair a coleção não limitada no documento da editora. Em vez disso, temos apenas uma referência à editora em cada documento do livro.

### <a name="how-do-i-model-manymany-relationships"></a>Como modelo muitas: muitas relações?

Numa base de dados relacional *muitas: muitas* relações são muitas vezes modeladas com mesas de adesão, que se juntam apenas a registos de outras tabelas.

![Junte mesas](./media/sql-api-modeling-data/join-table.png)

Pode sentir-se tentado a replicar a mesma coisa usando documentos e produzir um modelo de dados semelhante ao seguinte.

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

Isto funcionaria. No entanto, carregar um autor com os seus livros, ou carregar um livro com o seu autor, exigiria sempre pelo menos duas consultas adicionais contra a base de dados. Uma consulta ao documento de adesão e, em seguida, outra consulta para obter o documento real sendo aderido.

Se tudo o que esta mesa de adesão está a fazer é colar dois pedaços de dados, então porque não o largas completamente?
Considere o seguinte.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}

Agora, se eu tivesse um autor, eu imediatamente sei que livros escreveram, e inversamente se eu tivesse um documento de livro carregado eu saberia as iDs do(s autor). Isto economiza essa consulta intermediária contra a mesa de adesão reduzindo o número de viagens redondas do servidor que a sua aplicação tem de fazer.

## <a name="hybrid-data-models"></a>Modelos de dados híbridos

Agora, parecemos incorporar (ou desnormalizar) e referenciar dados (ou normalizar), cada um tem as suas vantagens e cada um tem compromissos como vimos.

Nem sempre tem de ser ou.não tenhas medo de misturar um pouco as coisas.

Com base nos padrões de utilização e cargas de trabalho específicos da sua aplicação, pode haver casos em que misturar dados incorporados e referenciados faz sentido e pode levar a uma lógica de aplicação mais simples com menos viagens redondas do servidor, mantendo um bom nível de desempenho.

Considere o seguinte JSON.

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

Aqui seguimos (principalmente) o modelo incorporado, onde os dados de outras entidades estão incorporados no documento de alto nível, mas outros dados são referenciados.

Se olharmos para o documento do livro, podemos ver alguns campos interessantes quando olhamos para o conjunto de autores. Há um `id` campo que é o campo que usamos para remeter de volta a um `name` documento `thumbnailUrl`de autor, prática padrão em um modelo normalizado, mas depois também temos e . Poderíamos ter `id` ficado com e deixado a aplicação para obter qualquer informação adicional necessária do respetivo documento de autor usando o "link", mas como a nossa aplicação exibe o nome do autor e uma imagem de miniatura com cada livro exibido podemos guardar uma viagem de ida e volta ao servidor por livro numa lista desnormalizando **alguns** dados do autor.

Claro, se o nome do autor mudasse ou quisesse minhá-lo, teríamos de atualizar todos os livros que já publicaram, mas para a nossa aplicação, com base no pressuposto de que os autores não mudam os seus nomes com frequência, esta é uma decisão de design aceitável.  

No exemplo, existem **valores agregados pré-calculados** para economizar processamento dispendioso numa operação de leitura. No exemplo, alguns dos dados incorporados no documento do autor são dados que são calculados no prazo de execução. Sempre que um novo livro é publicado, é criado um documento de livro **e** o campo countOfBooks é definido para um valor calculado com base no número de documentos de livros que existem para um determinado autor. Esta otimização seria boa na leitura de sistemas pesados onde podemos dar-nos ao luxo de fazer cálculos em escritos de forma a otimizar as leituras.

A capacidade de ter um modelo com campos pré-calculados é possível porque a Azure Cosmos DB suporta **transações multi-documentos.** Muitas lojas NoSQL não podem fazer transações através de documentos e, por isso, defendem decisões de design, como "sempre incorporar tudo", devido a esta limitação. Com o Azure Cosmos DB, pode utilizar gatilhos do lado do servidor, ou procedimentos armazenados, que insiram livros e atualizar autores todos dentro de uma transação ACID. Agora não **precisa** incorporar tudo num único documento apenas para ter certeza de que os seus dados permanecem consistentes.

## <a name="distinguishing-between-different-document-types"></a>Distinguindo diferentes tipos de documentos

Em alguns cenários, é possível misturar diferentes tipos de documentos na mesma coleção; este é geralmente o caso quando se quer que vários documentos relacionados se sente na mesma [divisão](partitioning-overview.md). Por exemplo, você poderia colocar ambos livros e críticas `bookId`de livros na mesma coleção e dividi-lo por . Nesta situação, normalmente pretende adicionar aos seus documentos um campo que identifique o seu tipo de forma a diferenciá-los.

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

## <a name="next-steps"></a>Passos seguintes

Os maiores takeaways deste artigo são compreender que a modelação de dados num mundo livre de esquemas é tão importante como sempre.

Assim como não há uma única maneira de representar um pedaço de dados num ecrã, não há uma única maneira de modelar os seus dados. Precisa entender a sua aplicação e como irá produzir, consumir e processar os dados. Em seguida, aplicando algumas das diretrizes aqui apresentadas, pode definir a criação de um modelo que responda às necessidades imediatas da sua aplicação. Quando as suas aplicações precisam de mudar, pode aproveitar a flexibilidade de uma base de dados sem esquemas para abraçar essa mudança e evoluir facilmente o seu modelo de dados.

Para saber mais sobre o Azure Cosmos DB, consulte a página de [documentação](https://azure.microsoft.com/documentation/services/cosmos-db/) do serviço.

Para entender como fragmentos dos seus dados em várias divisórias, consulte dados [de partição em Azure Cosmos DB](sql-api-partition-data.md).

Para aprender a modelar e dividir dados sobre o Azure Cosmos DB usando um exemplo real, consulte [modelação de dados e partição - um exemplo do Mundo Real](how-to-model-partition-example.md).
