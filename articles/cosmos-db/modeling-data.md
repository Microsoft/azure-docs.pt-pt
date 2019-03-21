---
title: Modelação de dados de documento numa base de dados NoSQL
titleSuffix: Azure Cosmos DB
description: Saiba mais sobre a modelação de dados em bases de dados NoSQL, as diferenças entre a modelação de dados na base de dados relacional e uma base de dados do documento.
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: andrl
ms.custom: seodec18
ms.openlocfilehash: f122d60a4f4df011a0adbe7806e70ae173222641
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295101"
---
# <a name="modeling-document-data-for-nosql-databases"></a>Modelação de dados de documento para bases de dados NoSQL

Embora sem esquema bases de dados, como o Azure Cosmos DB, torná-lo muito mais fáceis de adotar as alterações ao seu modelo de dados deve ainda gastar algum tempo pensando sobre os seus dados.

Como os dados serão armazenados? Como será a aplicação para obter e consultar dados? É a sua aplicação pesado de leitura ou escrita pesadas?

Depois de ler este artigo, será capaz de responder às seguintes perguntas:

* Como deve pensar sobre um documento num banco de dados de documentos?
* Qual é a Modelagem de dados e por que devo me preocupar?
* Dados de modelagem num banco de dados do documento é diferente para uma base de dados relacional?
* Como posso express relações de dados numa base de dados não relacionais?
* Quando incorporar dados e quando posso ligar aos dados?

## <a name="embedding-data"></a>A incorporar dados

Quando começa a modelação de dados num arquivo de documento, como o Azure Cosmos DB, tente tratar suas entidades como **documentos autossuficiente** representado em JSON.

Antes de mergulhar demasiado muito além disso, informe-nos voltar alguns passos e ver como podemos pode modelar algo na base de dados relacional, um assunto que muitos de nós já estão familiarizados. O exemplo seguinte mostra como uma pessoa pode estar armazenada na base de dados relacional.

![Modelo de base de dados relacional](./media/sql-api-modeling-data/relational-data-model.png)

Ao trabalhar com bancos de dados relacionais, nós já foi ensinados há anos normalizar, normalizar, normalizar.

Normalizar os dados normalmente envolve pegar uma entidade, como uma pessoa e a segmentar a Modelagem em partes discretas de dados. No exemplo acima, uma pessoa pode ter vários registos de detalhes de contacto, bem como vários registos de endereço. Podemos até mesmo ir além e dividir detalhes de contacto extraindo mais comuns campos, como um tipo. Mesmo para o endereço, cada registo aqui tem um tipo como *home page* ou *Business*.

A servir de orientação no local quando normalizar dados é **Evite armazenar dados redundantes** em cada gravar e, em vez disso, consulte a dados. Neste exemplo, para ler uma pessoa, com todos os respetivos detalhes de contacto e os endereços, terá de utilizar associações para efetivamente Agregue os seus dados em tempo de execução.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType on cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

A atualizar uma única pessoa com seus detalhes de contacto e os endereços necessita de operações de escrita em muitas tabelas individuais.

Agora vamos dar uma olhada em como podemos deve modelar os mesmos dados como uma entidade independente num banco de dados do documento.

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

Usando a abordagem acima, temos agora **desnormalizada** a pessoa de registos onde podemos **embedded** todas as informações relacionadas com esta pessoa, tais como os detalhes de contacto e os endereços, num único JSON documento.
Além disso, uma vez que não está se limita a um esquema fixo temos a flexibilidade para fazer coisas como ter totalmente os detalhes de contacto de formas diferentes.

A recuperação de um registo de pessoa completa da base de dados é agora uma única operação em relação a uma única coleção e de um único documento de leitura. Atualizar um registo de pessoa, com seus detalhes de contacto e os endereços, também é uma operação de escrita única em relação a um único documento.

Desnormalizando dados, a sua aplicação poderá ter de emitir menos consultas e atualizações para concluir as operações comuns.

### <a name="when-to-embed"></a>Quando incorporar

Em geral, utiliza os dados incorporados modela quando:

* Existem **contidos** relações entre entidades.
* Existem **um-para-algumas** relações entre entidades.
* Não há dados incorporados que **mudar com pouca frequência**.
* É incorporado dados não crescer **sem limite**.
* Não há dados incorporados, que é **integral** aos dados num documento.

> [!NOTE]
> Normalmente desnormalizado dados os modelos oferecem melhor **ler** desempenho.

### <a name="when-not-to-embed"></a>Quando não incorporar

Embora a regra geral num banco de dados de documento seja desnormalizar tudo e incorporar todos os dados num único documento, isso pode levar a algumas situações que devem ser evitadas.

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

Isso pode ser o que uma entidade de post com comentários embedded seria como se podemos foram modelagem um blog típico ou CMS, system. O problema com este exemplo é que a matriz de comentários **imensos**, que significa que não existe nenhum limite (prático) para o número de comentários, pode ter qualquer único post. Isso se tornará um problema de como o tamanho do documento pode aumentar significativamente.

Como o tamanho do documento que aumenta a capacidade de transmitir os dados sobre a conexão, bem como ler e atualizar o documento, à escala, será afetada.

Neste caso, seria melhor considerar o seguinte modelo.

    Post document:
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

    Comment documents:
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

Esse modelo tem três mais recente comentários incorporados na postagem em si, que é uma matriz com fixa vinculado neste momento. Os outros comentários são agrupados em lotes de 100 comentários e armazenados em documentos separados. O tamanho do lote foi escolhido como 100, uma vez que nosso aplicativo fictício permite que o usuário carregue 100 comentários ao mesmo tempo.  

Outro caso em que a incorporação de dados não são uma boa idéia é quando os dados incorporados são usados com freqüência em documentos e serão alterado frequentemente.

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

Isso pode representar o portfólio de ações de uma pessoa. Escolhemos incorporar as informações das ações em cada documento de Portfólio. Num ambiente onde os dados relacionados são alterados muitas vezes, como uma ação de aplicação de comércio, incorporar dados alterados com frequência será significa que está constantemente a atualizar cada documento de Portfólio sempre que uma ação é cedência das.

Stock *zaza* podem ser cedência das muitas centenas de vezes num único dia e milhares de usuários podem ter *zaza* em seu portfólio. Com um modelo de dados como o anterior que teríamos para atualizar vários milhares de documentos de Portfólio, muitas vezes todos os dias que leva a um sistema que não dimensiona bem.

## <a name="referencing-data"></a>Dados de referência

Então, incorporar dados funciona muito bem para muitos casos, mas é claro que existem cenários quando desnormalizar os dados causará problemas que não vale a pena. Então, o que fazemos agora?

Bases de dados relacionais não são o único local onde pode criar relações entre entidades. Num banco de dados do documento, pode ter informações num documento, na verdade, se relaciona com dados em outros documentos. Agora, estou não defendendo durante um minuto, mesmo que criamos sistemas que poderiam ser mais apropriados para uma base de dados relacional no Azure Cosmos DB ou outro banco de dados de documento, mas as relações simples podem ser utilizadas e podem ser útil.

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

Agora, se eu tivesse um autor, eu sei que imediatamente os livros que escreveram e por outro lado se eu tivesse um documento de livro carregado eu saberia os IDs do author(s). Isso poupa essa consulta intermediária em relação a tabela de associação reduzindo o número de servidor de seu aplicativo deve tomar de ida e volta.

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

Se examinar o documento do livro, podemos ver alguns campos de interessante quando olhamos para a matriz de autores. Há uma `id` campo, ou seja, o campo que usamos para consultar um documento de autor, a prática padrão num modelo normalizado, mas, em seguida, também temos `name` e `thumbnailUrl`. Podemos poderia ter preso a `id` e deixado o aplicativo para obter informações adicionais-lo do documento de autor respectivos usando "link", mas porque o nosso aplicativo exibe o nome do autor e uma imagem de miniatura com cada livro Vamos salvar uma ida e volta para o servidor consoante o livro numa lista desnormalizando apresentadas **alguns** dados a partir de autor.

É claro que, se alterar o nome do autor ou eles queriam atualizar suas fotos que teríamos que aceder e atualizar cada livro que alguma vez publicados, mas em nosso aplicativo, com base no pressuposto de que os autores de não alterar os respetivos nomes, muitas vezes, esta é uma decisão de design aceitável.  

No exemplo, existem **previamente calculado agregações** valores para guardar o processamento dispendioso numa operação de leitura. No exemplo, alguns dos dados incorporados ao documento de autor são os dados que são calculados em tempo de execução. Sempre que for publicado um novo livro, um documento de livro é criado **e** o campo de countOfBooks é definido como um valor calculado com base no número de documentos do livro que existem para um determinado autor. Essa otimização seria bom em sistemas pesados leitura onde podemos puder fazer cálculos em escritas para otimizar as leituras.

A capacidade de ter um modelo com campos calculados previamente se tornou possível porque o Azure Cosmos DB suporta **transações de vários documentos**. Muitos arquivos de NoSQL não é possível fazer transações entre documentos e, portanto, as decisões de design, como "sempre incorporar tudo", devido a essa limitação de desenvolvimento. Com o Azure Cosmos DB, pode utilizar o servidor acionadores ou procedimentos armazenados, o que inserir livros e atualizar os autores tudo dentro de uma transação ACID. Agora não **ter** para incorporar tudo num documento apenas para ter certeza de que os seus dados permanecem consistentes.

## <a name="distinguishing-between-different-document-types"></a>Distinguir entre diferentes tipos de documentos

Em alguns cenários, pode querer combinar diferentes tipos de documentos na mesma coleção; Isso é normalmente o caso quando desejar que vários, relacionadas com documentos sentar-se na mesma [partição](partitioning-overview.md). Por exemplo, poderia colocar ambos os livros e revisões da mesma coleção de livros e particione o código por `bookId`. Em tal situação, geralmente deseja adicionar aos seus documentos com um campo que identifica o seu tipo para diferenciá-los.

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
