---
title: Modelando dados no Azure Cosmos DB
titleSuffix: Azure Cosmos DB
description: Saiba mais sobre a modelagem de dados em bancos de dados NoSQL, as diferenças entre modelar dados em um banco de dados relacional e um banco de dados de documentos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 523049ea3286445117f41147f3dd12a2c911d1ae
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755020"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Modelagem de dados no Azure Cosmos DB

Embora os bancos de dados sem esquemas, como Azure Cosmos DB, facilitem o armazenamento e a consulta de um dado não estruturado e semiestruturado, você deve dedicar algum tempo pensando em seu modelo de dados para obter o máximo do serviço em termos de desempenho e escalabilidade e o mais baixo custo.

Como os dados serão armazenados? Como seu aplicativo vai recuperar e consultar dados? Seu aplicativo é de leitura intensa ou de gravação pesada?

Depois de ler este artigo, você poderá responder às seguintes perguntas:

* O que é modelagem de dados e por que devo me preocupar?
* Como é possível modelar dados em Azure Cosmos DB diferentes para um banco de dado relacional?
* Como fazer as relações de dados expressas em um banco não relacional?
* Quando faço para inserir dados e quando vinculo a dados?

## <a name="embedding-data"></a>Inserindo dados

Quando você começa a modelar dados em Azure Cosmos DB tente tratar suas entidades como **itens** independentes representados como documentos JSON.

Para comparação, vamos ver primeiro como podemos modelar dados em um banco de dado relacional. O exemplo a seguir mostra como uma pessoa pode ser armazenada em um banco de dados relacional.

![Modelo de banco de dados relacional](./media/sql-api-modeling-data/relational-data-model.png)

Quando se trabalha com bancos de dados relacionais, a estratégia é normalizar todos os seus próprios. Normalizar seus dados normalmente envolve pegar uma entidade, como uma pessoa, e dividi-la em componentes discretos. No exemplo acima, uma pessoa pode ter vários registros de detalhes de contato, bem como vários registros de endereço. Os detalhes de contato podem ser divididos mais detalhadamente com a extração de campos comuns como um tipo. O mesmo se aplica ao endereço, cada registro pode ser do tipo *Home* ou *Business*.

A premissa de orientação ao normalizar os dados é **evitar o armazenamento de dados redundantes** em cada registro e, em vez disso, consultar os dados. Neste exemplo, para ler uma pessoa, com todos os seus detalhes e endereços de contato, você precisa usar junções para compor de forma efetiva (ou desnormalizar) seus dados em tempo de execução.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Atualizar uma única pessoa com seus endereços e detalhes de contato requer operações de gravação em várias tabelas individuais.

Agora vamos dar uma olhada em como modelar os mesmos dados como uma entidade autônoma em Azure Cosmos DB.

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

Usando a abordagem acima, **desnormalizamos** o registro Person **, inserindo todas as** informações relacionadas a essa pessoa, como seus detalhes de contato e endereços, em um único documento *JSON* .
Além disso, como não estamos confinados a um esquema fixo, temos a flexibilidade de fazer coisas como ter detalhes de contato de formas diferentes inteiramente.

A recuperação de um registro de pessoa completo do banco de dados agora é uma **única operação de leitura** em um único contêiner e para um único item. A atualização de um registro de pessoa, com seus detalhes e endereços de contato, também é uma **única operação de gravação** em um único item.

Ao desnormalizar os dados, seu aplicativo pode precisar emitir menos consultas e atualizações para concluir operações comuns.

### <a name="when-to-embed"></a>Quando inserir

Em geral, use modelos de dados inseridos quando:

* Há relações **contidas** entre entidades.
* Há relações de **um para poucos** entre entidades.
* Há dados inseridos que são **alterados com pouca frequência**.
* Há dados inseridos que não serão aumentados **sem limite**.
* Há dados inseridos que são **consultados frequentemente em conjunto**.

> [!NOTE]
> Normalmente, os modelos de dados desnormalizados fornecem melhor desempenho de **leitura** .

### <a name="when-not-to-embed"></a>Quando não inserir

Embora a regra geral na Azure Cosmos DB seja desnormalizar tudo e inserir todos os dados em um único item, isso pode levar a algumas situações que devem ser evitadas.

Faça este trecho de JSON.

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

Isso pode ser a aparência de uma entidade post com comentários inseridos se tivéssemos modelando um sistema típico de blog, ou CMS,. O problema com este exemplo é que a matriz de comentários não está **associada**, o que significa que não há nenhum limite (prático) para o número de comentários que qualquer postagem pode ter. Isso pode se tornar um problema, pois o tamanho do item pode aumentar infinitamente grande.

Como o tamanho do item aumenta a capacidade de transmitir os dados pela conexão, bem como ler e atualizar o item, em escala, será afetado.

Nesse caso, seria melhor considerar o seguinte modelo de dados.

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

Esse modelo tem os três comentários mais recentes inseridos no contêiner post, que é uma matriz com um conjunto fixo de atributos. Os outros comentários são agrupados em lotes de 100 Comentários e armazenados como itens separados. O tamanho do lote foi escolhido como 100 porque nosso aplicativo fictício permite que o usuário carregue 100 Comentários por vez.  

Outro caso em que incorporar dados não é uma boa ideia é quando os dados inseridos costumam ser usados em todos os itens e serão alterados com frequência.

Faça este trecho de JSON.

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

Isso pode representar o portfólio de ações de uma pessoa. Optamos por inserir as informações de ações em cada documento de portfólio. Em um ambiente em que os dados relacionados são alterados com frequência, como um aplicativo de negociação de estoque, a inserção de dados que mudam com frequência significa que você atualiza constantemente cada documento do portfólio sempre que um estoque é negociado.

O *Zaza* de ações pode ser comercializado muitas centenas de vezes em um único dia e milhares de usuários podem ter *Zaza* em seu portfólio. Com um modelo de dados como o acima, teríamos que atualizar muitos milhares de documentos de portfólio muitas vezes todos os dias que levam a um sistema que não será bem dimensionado.

## <a name="referencing-data"></a>Fazendo referência a dados

A inserção de dados funciona bem para muitos casos, mas há cenários em que a desnormalização de seus dados causará mais problemas do que vale a pena. Então, o que fazemos agora?

Os bancos de dados relacionais não são o único lugar onde você pode criar relações entre entidades. Em um banco de dados de documentos, você pode ter informações em um documento relacionado a dados em outros documentos. Não recomendamos a criação de sistemas que seriam mais adequados para um banco de dados relacional em Azure Cosmos DB ou qualquer outro banco de dados de documentos, mas relações simples são bem e podem ser úteis.

No JSON abaixo, optamos por usar o exemplo de um portfólio de ações de antes, mas desta vez vamos nos referir ao item de estoque no portfólio em vez de incorporá-lo. Dessa forma, quando o item de estoque é alterado com frequência durante todo o dia, o único documento que precisa ser atualizado é o documento de ações únicas.

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

No entanto, uma desvantagem imediata dessa abordagem é se o aplicativo for necessário para mostrar informações sobre cada estoque que é mantido ao exibir o portfólio de uma pessoa; Nesse caso, você precisaria fazer várias viagens ao banco de dados para carregar as informações de cada documento de ações. Aqui, tomamos uma decisão de melhorar a eficiência das operações de gravação, que ocorrem frequentemente durante todo o dia, mas, por sua vez, são comprometidas nas operações de leitura que potencialmente têm menos impacto sobre o desempenho desse sistema específico.

> [!NOTE]
> Modelos de dados normalizados **podem exigir mais viagens de ida** e volta ao servidor.

### <a name="what-about-foreign-keys"></a>E quanto às chaves estrangeiras?

Como atualmente não há nenhum conceito de restrição, chave estrangeira ou de outra forma, qualquer relação entre documentos que você tenha em documentos será efetivamente "links fracos" e não será verificada pelo próprio banco de dados. Se você quiser garantir que os dados aos quais um documento está se referindo realmente existem, você precisará fazer isso em seu aplicativo ou por meio do uso de gatilhos do lado do servidor ou de procedimentos armazenados no Azure Cosmos DB.

### <a name="when-to-reference"></a>Quando fazer referência

Em geral, use modelos de dados normalizados quando:

* Representando relações **um-para-muitos** .
* Representando relações **muitos para muitos** .
* Os dados relacionados **são alterados com frequência**.
* Os dados referenciados podem ser **desvinculados**.

> [!NOTE]
> Normalmente, a normalização proporciona um melhor desempenho de **gravação** .

### <a name="where-do-i-put-the-relationship"></a>Onde eu coloco a relação?

O crescimento da relação ajudará a determinar em qual documento armazenar a referência.

Se observarmos o JSON abaixo, que modela os editores e os livros.

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

Se o número de livros por Publicador for pequeno com crescimento limitado, o armazenamento da referência do livro dentro do documento do Publicador poderá ser útil. No entanto, se o número de livros por Publicador não estiver associado, esse modelo de dados levaria a matrizes mutáveis e crescentes, como no documento de exemplo Publicador acima.

Mudar as coisas em um pouco resultaria em um modelo que ainda representa os mesmos dados, mas agora evita essas grandes coleções mutáveis.

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

No exemplo acima, descartamos a coleção não associada no documento do Publicador. Em vez disso, temos apenas uma referência ao Publicador em cada documento de livro.

### <a name="how-do-i-model-manymany-relationships"></a>Como fazer modelo muitos: muitas relações?

Em um banco de dados relacional *muitos: muitas* relações geralmente são modeladas com tabelas de junção, que apenas ingressam em registros de outras tabelas.

![Unir tabelas](./media/sql-api-modeling-data/join-table.png)

Você pode estar tentado a replicar a mesma coisa usando documentos e produzir um modelo de dados parecido com o seguinte.

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

Isso funcionaria. No entanto, carregar um autor com seus livros ou carregar um livro com seu autor, sempre exigiria pelo menos duas consultas adicionais no banco de dados. Uma consulta para o documento de junção e outra consulta para buscar o documento real que está sendo Unido.

Se toda essa tabela de junção estiver colando duas partes de dados, por que não soltá-la completamente?
Considere o seguinte.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}

Agora, se eu tivesse um autor, sei imediatamente quais livros eles escreveram e, por outro lado, se eu tivesse um documento de livro carregado, saberia as identificações do (s) autor (es). Isso salva a consulta intermediária em relação à tabela de junção, reduzindo o número de viagens de ida e volta do servidor que seu aplicativo precisa fazer.

## <a name="hybrid-data-models"></a>Modelos de dados híbridos

Agora, examinamos a inserção (ou desnormalização) e a referência (ou a normalização) de dados, cada um deles com seus lados e cada um tem comprometeções como vimos.

Nem sempre precisa ser ou não fazer medo para misturar coisas por um pouco.

Com base nos padrões de uso e cargas de trabalho específicos de seu aplicativo, pode haver casos em que misturar dados incorporados e referenciados faz sentido e pode levar à lógica do aplicativo mais simples com menos viagens de ida e volta do servidor e, ao mesmo tempo, manter um bom nível de desempenho.

Considere o JSON a seguir.

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

Aqui, (principalmente), seguimos o modelo incorporado, em que os dados de outras entidades são inseridos no documento de nível superior, mas outros dados são referenciados.

Se você examinar o documento de livro, poderemos ver alguns campos interessantes ao examinarmos a matriz de autores. Há um campo `id` que é o campo que usamos para fazer referência a um documento de autor, uma prática padrão em um modelo normalizado, mas também temos `name` e `thumbnailUrl`. Poderíamos ter preso `id` e deixar o aplicativo para obter informações adicionais necessárias do respectivo documento autor usando o "link", mas como nosso aplicativo exibe o nome do autor e uma imagem em miniatura com cada livro exibido pode salvar uma viagem de ida e volta ao servidor por livro em uma lista desnormalizando **alguns** dados do autor.

Com certeza, se o nome do autor for alterado ou quisessem atualizar sua foto, precisaremos atualizar todos os livros que já publicaram, mas para nosso aplicativo, com base na suposição de que os autores não alteram seus nomes com frequência, essa é uma decisão de design aceitável.  

No exemplo, há valores de **agregações previamente calculados** para salvar o processamento caro em uma operação de leitura. No exemplo, alguns dos dados inseridos no documento do autor são dados que são calculados em tempo de execução. Sempre que um novo livro é publicado, um documento de livro é criado **e** o campo countOfBooks é definido como um valor calculado com base no número de documentos de livro que existem para um determinado autor. Essa otimização seria boa em sistemas de leitura intensa, nos quais podemos arcar com cálculos em gravações a fim de otimizar as leituras.

A capacidade de ter um modelo com campos pré-calculados é possibilitada porque Azure Cosmos DB dá suporte a **Transações de vários documentos**. Muitos repositórios NoSQL não podem realizar transações em documentos e, portanto, defendem decisões de design, como "sempre inserir tudo", devido a essa limitação. Com Azure Cosmos DB, você pode usar gatilhos do lado do servidor ou procedimentos armazenados, que inserem livros e atualizam os autores em uma transação ACID. Agora você não **precisa** inserir tudo em um documento apenas para ter certeza de que seus dados permanecem consistentes.

## <a name="distinguishing-between-different-document-types"></a>Diferenciando entre diferentes tipos de documento

Em alguns cenários, talvez você queira misturar tipos de documentos diferentes na mesma coleção; Normalmente, esse é o caso em que você deseja que vários documentos relacionados sejam posicionados na mesma [partição](partitioning-overview.md). Por exemplo, você pode colocar livros e revisões de livro na mesma coleção e particioná-los por `bookId`. Nessa situação, você geralmente deseja adicionar aos seus documentos com um campo que identifica seu tipo para diferenciá-los.

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

As maiores informações deste artigo são entender que a modelagem de dados em um mundo livre de esquemas é tão importante quanto nunca.

Assim como não há uma maneira única de representar dados em uma tela, não há uma maneira única de modelar seus dados. Você precisa entender seu aplicativo e como ele vai produzir, consumir e processar os dados. Em seguida, aplicando algumas das diretrizes apresentadas aqui, você pode definir sobre a criação de um modelo que atenda às necessidades imediatas do seu aplicativo. Quando seus aplicativos precisam ser alterados, você pode aproveitar a flexibilidade de um banco de dados sem esquemas para adotar essa alteração e desenvolver facilmente seu modelo de dados.

Para saber mais sobre Azure Cosmos DB, consulte a página de [documentação](https://azure.microsoft.com/documentation/services/cosmos-db/) do serviço.

Para entender como fragmentar seus dados em várias partições, consulte [Particionando dados em Azure Cosmos DB](sql-api-partition-data.md).

Para saber como modelar e particionar dados em Azure Cosmos DB usando um exemplo do mundo real, consulte [modelagem e particionamento de dados – um exemplo do mundo real](how-to-model-partition-example.md).
