---
title: Modelando dados no Azure Cosmos DB
titleSuffix: Azure Cosmos DB
description: Saiba mais sobre a modelação de dados em bases de dados NoSQL, as diferenças entre a modelação de dados na base de dados relacional e uma base de dados do documento.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.custom: rimman
ms.openlocfilehash: da119b2858c6b6c7bbc99b40d340f79964e0fae3
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467884"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Modelagem de dados no Azure Cosmos DB

Embora os bancos de dados sem esquemas, como Azure Cosmos DB, facilitem o armazenamento e a consulta de um dado não estruturado e semiestruturado, você deve dedicar algum tempo pensando em seu modelo de dados para obter o máximo do serviço em termos de desempenho e escalabilidade e o mais baixo custo.

Como os dados serão armazenados? Como será a aplicação para obter e consultar dados? Seu aplicativo é de leitura intensa ou de gravação pesada?

Depois de ler este artigo, será capaz de responder às seguintes perguntas:

* Qual é a Modelagem de dados e por que devo me preocupar?
* Como é possível modelar dados em Azure Cosmos DB diferentes para um banco de dado relacional?
* Como posso express relações de dados numa base de dados não relacionais?
* Quando incorporar dados e quando posso ligar aos dados?

## <a name="embedding-data"></a>A incorporar dados

Quando você começa a modelar dados em Azure Cosmos DB tente tratar suas entidades como **itens** independentes representados como documentos JSON.

Para comparação, vamos ver primeiro como podemos modelar dados em um banco de dado relacional. O exemplo seguinte mostra como uma pessoa pode estar armazenada na base de dados relacional.

![Modelo de base de dados relacional](./media/sql-api-modeling-data/relational-data-model.png)

Quando se trabalha com bancos de dados relacionais, a estratégia é normalizar todos os seus próprios. Normalizar seus dados normalmente envolve pegar uma entidade, como uma pessoa, e dividi-la em componentes discretos. No exemplo acima, uma pessoa pode ter vários registros de detalhes de contato, bem como vários registros de endereço. Os detalhes de contato podem ser divididos mais detalhadamente com a extração de campos comuns como um tipo. O mesmo se aplica ao endereço, cada registro pode ser do tipo *Home* ou *Business*.

A servir de orientação no local quando normalizar dados é **Evite armazenar dados redundantes** em cada gravar e, em vez disso, consulte a dados. Neste exemplo, para ler uma pessoa, com todos os seus detalhes e endereços de contato, você precisa usar junções para compor de forma efetiva (ou desnormalizar) seus dados em tempo de execução.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

A atualizar uma única pessoa com seus detalhes de contacto e os endereços necessita de operações de escrita em muitas tabelas individuais.

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

Usando a abordagem acima, desnormalizamos o registro Person, inserindo **todas as** informações relacionadas a essa pessoa, como seus detalhes de contato e endereços, em um único documento *JSON* .
Além disso, uma vez que não está se limita a um esquema fixo temos a flexibilidade para fazer coisas como ter totalmente os detalhes de contacto de formas diferentes.

A recuperação de um registro de pessoa completo do banco de dados agora é uma **única operação de leitura** em um único contêiner e para um único item. A atualização de um registro de pessoa, com seus detalhes e endereços de contato, também é uma **única operação de gravação** em um único item.

Desnormalizando dados, a sua aplicação poderá ter de emitir menos consultas e atualizações para concluir as operações comuns.

### <a name="when-to-embed"></a>Quando incorporar

Em geral, utiliza os dados incorporados modela quando:

* Há relações **contidas** entre entidades.
* Existem **um-para-algumas** relações entre entidades.
* Não há dados incorporados que **mudar com pouca frequência**.
* Há dados inseridos que não serão aumentados **sem limite**.
* Há dados inseridos que são **consultados frequentemente em conjunto**.

> [!NOTE]
> Normalmente desnormalizado dados os modelos oferecem melhor **ler** desempenho.

### <a name="when-not-to-embed"></a>Quando não incorporar

Embora a regra geral na Azure Cosmos DB seja desnormalizar tudo e inserir todos os dados em um único item, isso pode levar a algumas situações que devem ser evitadas.

Tome este fragmento JSON.

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

Isso pode ser o que uma entidade de post com comentários embedded seria como se podemos foram modelagem um blog típico ou CMS, system. O problema com este exemplo é que a matriz de comentários **imensos**, que significa que não existe nenhum limite (prático) para o número de comentários, pode ter qualquer único post. Isso pode se tornar um problema, pois o tamanho do item pode aumentar infinitamente grande.

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

Esse modelo tem os três comentários mais recentes inseridos no contêiner post, que é uma matriz com um conjunto fixo de atributos. Os outros comentários são agrupados em lotes de 100 Comentários e armazenados como itens separados. O tamanho do lote foi escolhido como 100, uma vez que nosso aplicativo fictício permite que o usuário carregue 100 comentários ao mesmo tempo.  

Outro caso em que incorporar dados não é uma boa ideia é quando os dados inseridos costumam ser usados em todos os itens e serão alterados com frequência.

Tome este fragmento JSON.

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

Isso pode representar o portfólio de ações de uma pessoa. Optamos por inserir as informações de ações em cada documento de portfólio. Num ambiente onde os dados relacionados são alterados muitas vezes, como uma ação de aplicação de comércio, incorporar dados alterados com frequência será significa que está constantemente a atualizar cada documento de Portfólio sempre que uma ação é cedência das.

Stock *zaza* podem ser cedência das muitas centenas de vezes num único dia e milhares de usuários podem ter *zaza* em seu portfólio. Com um modelo de dados como o anterior que teríamos para atualizar vários milhares de documentos de Portfólio, muitas vezes todos os dias que leva a um sistema que não dimensiona bem.

## <a name="referencing-data"></a>Fazendo referência a dados

A inserção de dados funciona bem para muitos casos, mas há cenários em que a desnormalização de seus dados causará mais problemas do que vale a pena. Então, o que fazemos agora?

Bases de dados relacionais não são o único local onde pode criar relações entre entidades. Em um banco de dados de documentos, você pode ter informações em um documento relacionado a dados em outros documentos. Não recomendamos a criação de sistemas que seriam mais adequados para um banco de dados relacional em Azure Cosmos DB ou qualquer outro banco de dados de documentos, mas relações simples são bem e podem ser úteis.

No JSON abaixo, optamos por usar o exemplo de um portfólio de ações de anteriormente, mas desta vez, nos Referimos ao item das ações portefólio em vez de incorporá-lo. Dessa forma, quando o item de estoque são alterados frequentemente ao longo do dia o documento único que tem de ser atualizada é o único documento de estoque.

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

No entanto uma desvantagem imediata dessa abordagem é se a sua aplicação é necessária para mostrar informações sobre cada estoque que é mantido quando se apresenta o portfólio de uma pessoa; Nesse caso seria necessário tornar vários viagens ao banco de dados para carregar as informações para cada documento das ações. Aqui, fizemos uma decisão para melhorar a eficiência das operações de escrita, o que acontecer frequentemente ao longo do dia, mas, por sua vez comprometido nas operações de leitura que potencialmente tenham menos impacto no desempenho desse sistema específico.

> [!NOTE]
> Modelos de dados de normalizados **pode exigir mais ida e volta** para o servidor.

### <a name="what-about-foreign-keys"></a>E as chaves estrangeiras?

Porque não existe atualmente nenhum conceito de uma restrição, chave externa ou de outra forma, as relações entre documentos que tenham em documentos são, efetivamente, "vínculos fracos" e não serão verificadas pela base de dados em si. Se pretender certificar-se de que os dados de que um documento refere-se para realmente existe, em seguida, terá de fazê-lo em seu aplicativo, ou por meio do uso no lado do servidor acionadores ou procedimentos armazenados no Azure Cosmos DB.

### <a name="when-to-reference"></a>Quando referenciar

Em geral, utilize dados normalizados modela quando:

* Que representa **um-para-muitos** relações.
* Que representa **muitos-para-muitos** relações.
* Dados relacionados **é alterado frequentemente**.
* Podem ser dados referenciados **imensos**.

> [!NOTE]
> Normalmente, normalizar fornece melhor **escrever** desempenho.

### <a name="where-do-i-put-the-relationship"></a>Onde posso colocar a relação?

O crescimento da relação ajudarão a determinar qual documento para armazenar a referência.

Se observarmos o JSON abaixo que modela os publicadores e livros.

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

Se o número de livros por publicador é pequeno com crescimento limitado, em seguida, armazenar a referência do livro dentro do documento de fabricante poderá ser útil. No entanto, se o número de livros por publicador for imensos, em seguida, esse modelo de dados levaria a matrizes mutáveis, cada vez mais, tal como o documento de publicador do exemplo acima.

Mudar as coisas um pouco resultaria num modelo que ainda representa os mesmos dados, mas agora evita essas grandes coleções mutáveis.

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

No exemplo acima, podemos ter removido a coleção não vinculada no documento de publicador. Em vez disso, temos apenas uma referência para o publicador em cada documento do livro.

### <a name="how-do-i-model-manymany-relationships"></a>Como modelo de relações de muitos: muitos?

Numa base de dados relacional *muitos: muitos* relações, muitas vezes, são modeladas com tabelas de associação, que simplesmente UNI registos a partir de outras tabelas.

![Junte-se a tabelas](./media/sql-api-modeling-data/join-table.png)

Pode ser tentado a replicar a mesma coisa usando os documentos e produzir um modelo de dados que tem um aspeto semelhante ao seguinte.

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

Isso funcionaria. No entanto, ao carregar o autor com seus livros, ou ao carregar um livro com seu autor, sempre exigiria, pelo menos, duas consultas adicionais na base de dados. Uma consulta para o documento de junção e, em seguida, outra consulta para obter o documento real que está a ser associado a um.

Se tudo está fazendo esta tabela de associação é gluing, em conjunto, duas partes de dados, em seguida, por que não soltá-la completamente?
Considere o seguinte.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}

Agora, se eu tivesse um autor, sei imediatamente quais livros eles escreveram e, por outro lado, se eu tivesse um documento de livro carregado, saberia as identificações do (s) autor (es). Isso poupa essa consulta intermediária em relação a tabela de associação reduzindo o número de servidor de seu aplicativo deve tomar de ida e volta.

## <a name="hybrid-data-models"></a>Modelos de dados híbridos

Agora que vimos por que incorporar (ou desnormalizando) e dados de referência (ou normalização), cada uma tem suas upsides e cada uma tem comprometimentos como temos visto.

Ele sempre não tem de ser um ou, não se apavorado misturar as coisas um pouco.

Com base nos padrões de utilização específicos e cargas de trabalho pode haver casos em que mistura incorporado da sua aplicação e dados referenciados faz sentido e poderia levar à lógica de aplicativo mais simples com menos de servidor de ida e volta, mantendo ainda um bom nível de desempenho.

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

Aqui estamos nos acompanhou (principalmente) o modelo incorporado, onde os dados de outras entidades estão incorporados no documento de nível superior, mas outros dados são referenciados.

Se examinar o documento do livro, podemos ver alguns campos de interessante quando olhamos para a matriz de autores. Há um `id` campo que é o campo que usamos para fazer referência a um documento de autor, uma prática padrão em um modelo normalizado, mas `name` também temos e `thumbnailUrl`. Poderíamos ter paralisado `id` e deixado o aplicativo para obter informações adicionais necessárias do respectivo documento autor usando o "link", mas como nosso aplicativo exibe o nome do autor e uma imagem em miniatura com cada livro exibida, podemos salvar uma viagem de ida e volta ao servidor por livro em uma lista desnormalizando **alguns** dados do autor.

Com certeza, se o nome do autor for alterado ou quisessem atualizar sua foto, precisaremos atualizar todos os livros que já publicaram, mas para nosso aplicativo, com base na suposição de que os autores não alteram seus nomes com frequência, essa é uma decisão de design aceitável.  

No exemplo, existem **previamente calculado agregações** valores para guardar o processamento dispendioso numa operação de leitura. No exemplo, alguns dos dados incorporados ao documento de autor são os dados que são calculados em tempo de execução. Sempre que for publicado um novo livro, um documento de livro é criado **e** o campo de countOfBooks é definido como um valor calculado com base no número de documentos do livro que existem para um determinado autor. Essa otimização seria bom em sistemas pesados leitura onde podemos puder fazer cálculos em escritas para otimizar as leituras.

A capacidade de ter um modelo com campos calculados previamente se tornou possível porque o Azure Cosmos DB suporta **transações de vários documentos**. Muitos arquivos de NoSQL não é possível fazer transações entre documentos e, portanto, as decisões de design, como "sempre incorporar tudo", devido a essa limitação de desenvolvimento. Com o Azure Cosmos DB, pode utilizar o servidor acionadores ou procedimentos armazenados, o que inserir livros e atualizar os autores tudo dentro de uma transação ACID. Agora você não **precisa** inserir tudo em um documento apenas para ter certeza de que seus dados permanecem consistentes.

## <a name="distinguishing-between-different-document-types"></a>Diferenciando entre diferentes tipos de documento

Em alguns cenários, talvez você queira misturar tipos de documentos diferentes na mesma coleção; Normalmente, esse é o caso em que você deseja que vários documentos relacionados sejam posicionados na mesma [partição](partitioning-overview.md). Por exemplo, você pode colocar livros e revisões de livro na mesma coleção e particioná-los `bookId`. Nessa situação, você geralmente deseja adicionar aos seus documentos com um campo que identifica seu tipo para diferenciá-los.

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

## <a name="next-steps"></a>Passos Seguintes

Maiores das novidades deste artigo estão compreender que dados de modelagem num mundo sem esquema são tão importantes quanto cada vez.

Tal como não é possível único para representar um conjunto de dados numa tela, não há nenhuma forma única para modelar os seus dados. Precisa para compreender a sua aplicação e como irá produzir, consumir e processar os dados. Em seguida, ao aplicar alguns das diretrizes apresentadas aqui pode definir sobre a criação de um modelo que atende às necessidades imediatas de seu aplicativo. Quando precisam de alterar seus aplicativos, pode aproveitar a flexibilidade de um sem esquema da base de dados para adotar o que são alterados e evoluir o seu modelo de dados facilmente.

Para saber mais sobre o Azure Cosmos DB, consulte o serviço [documentação](https://azure.microsoft.com/documentation/services/cosmos-db/) página.

Para compreender como de dividir os dados em várias partições, consulte [criação de partições de dados no Azure Cosmos DB](sql-api-partition-data.md).

Para saber como modelar e particionar dados em Azure Cosmos DB usando um exemplo do mundo real, consulte [modelagem e particionamento de dados – um exemplo do mundo real](how-to-model-partition-example.md).
