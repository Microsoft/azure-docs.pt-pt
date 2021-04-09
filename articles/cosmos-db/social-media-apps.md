---
title: 'Padrão de design de Azure Cosmos DB: aplicativos de mídia social'
description: Conheça um padrão de design para redes sociais aproveitando a flexibilidade de armazenamento da Azure Cosmos DB e de outros serviços Azure.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: maquaran
ms.openlocfilehash: 329c4b40f11b36de80581d4a1396813bc8de5c73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010321"
---
# <a name="going-social-with-azure-cosmos-db"></a>Indo social com Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Viver numa sociedade massivamente interligada significa que, em algum momento da vida, você se torna parte de uma **rede social**. Usa as redes sociais para manter contacto com amigos, colegas, familiares ou, por vezes, para partilhar a sua paixão com pessoas com interesses comuns.

Como engenheiros ou desenvolvedores, pode ter-se perguntado como é que estas redes armazenam e interligam os seus dados. Ou pode até ter sido incumbido de criar ou arquiteto uma nova rede social para um nicho de mercado específico. É aí que surge a questão significativa: como é que todos estes dados são armazenados?

Suponha que está a criar uma nova e brilhante rede social onde os seus utilizadores podem publicar artigos com meios relacionados, como imagens, vídeos ou até música. Os utilizadores podem comentar publicações e dar pontos para as classificações. Haverá um feed de posts que os utilizadores verão e interagirão na página de aterragem do site principal. Este método não parece complexo no início, mas por uma questão de simplicidade, vamos parar por aqui. (Pode aprofundar feeds personalizados de utilizadores afetados por relacionamentos, mas vai além do objetivo deste artigo.)

Então, como se armazenam estes dados e onde?

Pode ter experiência em bases de dados SQL ou ter uma noção de [modelação relacional de dados.](https://en.wikipedia.org/wiki/Relational_model) Pode começar a desenhar algo da seguinte forma:

:::image type="content" source="./media/social-media-apps/social-media-apps-sql.png" alt-text="Diagrama ilustrando um modelo relativo relacional" border="false":::

Uma estrutura de dados perfeitamente normalizada e bonita... que não escala.

Não me entenda mal, trabalhei com bases de dados SQL toda a minha vida. São ótimos, mas como todos os padrões, práticas e plataformas de software, não é perfeito para todos os cenários.

Porque é que a SQL não é a melhor escolha neste cenário? Vamos olhar para a estrutura de um único poste. Se eu quisesse mostrar o post num site ou aplicação, teria de fazer uma consulta com... juntando oito tabelas(!) apenas para mostrar um único post. Agora imagina um fluxo de posts que carregam e aparecem dinamicamente no ecrã, e podes ver para onde vou.

Você poderia usar um enorme exemplo SQL com poder suficiente para resolver milhares de consultas com muitas juntas para servir o seu conteúdo. Mas por que o faria, quando existe uma solução mais simples?

## <a name="the-nosql-road"></a>A estrada NoSQL

Este artigo guia-o a modelar os dados da sua plataforma social com a base de dados NoSQL da [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) de forma eficaz. Também lhe diz como usar outras funcionalidades da Azure Cosmos DB como a [API gremlin.](../cosmos-db/graph-introduction.md) Utilizando uma abordagem [NoSQL,](https://en.wikipedia.org/wiki/NoSQL) armazenando dados, em formato JSON e aplicando [a desnormalização,](https://en.wikipedia.org/wiki/Denormalization)o post anteriormente complicado pode ser transformado num único [documento:](https://en.wikipedia.org/wiki/Document-oriented_database)

```json
{
    "id":"ew12-res2-234e-544f",
    "title":"post title",
    "date":"2016-01-01",
    "body":"this is an awesome post stored on NoSQL",
    "createdBy":User,
    "images":["https://myfirstimage.png","https://mysecondimage.png"],
    "videos":[
        {"url":"https://myfirstvideo.mp4", "title":"The first video"},
        {"url":"https://mysecondvideo.mp4", "title":"The second video"}
    ],
    "audios":[
        {"url":"https://myfirstaudio.mp3", "title":"The first audio"},
        {"url":"https://mysecondaudio.mp3", "title":"The second audio"}
    ]
}
```

E pode ser obtido com uma única consulta, e sem junção. Esta consulta é muito simples e simples e, em termos orçamentais, requer menos recursos para obter um melhor resultado.

A Azure Cosmos DB garante que todas as propriedades estão indexadas com a sua indexação automática. A indexação automática pode mesmo ser [personalizada.](index-policy.md) A abordagem sem esquemas permite-nos armazenar documentos com estruturas diferentes e dinâmicas. Talvez amanhã queira que os posts tenham uma lista de categorias ou hashtags associadas a elas? A Cosmos DB tratará dos novos Documentos com os atributos adicionais sem trabalho extra exigido por nós.

Os comentários sobre um post podem ser tratados como outros posts com uma propriedade principal. (Esta prática simplifica o mapeamento do seu objeto.)

```json
{
    "id":"1234-asd3-54ts-199a",
    "title":"Awesome post!",
    "date":"2016-01-02",
    "createdBy":User2,
    "parent":"ew12-res2-234e-544f"
}

{
    "id":"asd2-fee4-23gc-jh67",
    "title":"Ditto!",
    "date":"2016-01-03",
    "createdBy":User3,
    "parent":"ew12-res2-234e-544f"
}
```

E todas as interações sociais podem ser armazenadas num objeto separado como contadores:

```json
{
    "id":"dfe3-thf5-232s-dse4",
    "post":"ew12-res2-234e-544f",
    "comments":2,
    "likes":10,
    "points":200
}
```

Criar feeds é apenas uma questão de criar documentos que podem conter uma lista de IDs post com uma determinada ordem de relevância:

```json
[
    {"relevance":9, "post":"ew12-res2-234e-544f"},
    {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
    {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
]
```

Pode ter um fluxo "mais recente" com posts encomendados por data de criação. Ou pode ter um fluxo "mais quente" com aqueles posts com mais gostos nas últimas 24 horas. Pode até implementar um fluxo personalizado para cada utilizador com base em lógicas como seguidores e interesses. Ainda seria uma lista de postos. É uma questão de como construir estas listas, mas o desempenho da leitura permanece sem entraves. Assim que adquirir uma destas listas, emita uma única consulta à Cosmos DB utilizando a [palavra-chave IN](sql-query-keywords.md#in) para obter páginas de publicações de cada vez.

Os fluxos de alimentação poderiam ser construídos utilizando os processos de fundo [dos Serviços de Aplicações Azure:](https://azure.microsoft.com/services/app-service/) [Webjobs](../app-service/webjobs-create.md). Uma vez criado um post, o processamento de fundo pode ser desencadeado utilizando [filas](../storage/queues/storage-dotnet-how-to-use-queues.md) [de armazenamento Azure](https://azure.microsoft.com/services/storage/) e Webjobs desencadeados usando o [Azure Webjobs SDK,](https://github.com/Azure/azure-webjobs-sdk/wiki)implementando a propagação pós-propagação dentro de fluxos com base na sua própria lógica personalizada.

Pontos e gostos sobre um post podem ser processados de forma diferida usando esta mesma técnica para criar um ambiente eventualmente consistente.

Os seguidores são mais complicados. Cosmos DB tem um limite de tamanho de documento, e ler/escrever documentos grandes pode afetar a escalabilidade da sua aplicação. Por isso, pode pensar em armazenar seguidores como um documento com esta estrutura:

```json
{
    "id":"234d-sd23-rrf2-552d",
    "followersOf": "dse4-qwe2-ert4-aad2",
    "followers":[
        "ewr5-232d-tyrg-iuo2",
        "qejh-2345-sdf1-ytg5",
        //...
        "uie0-4tyg-3456-rwjh"
    ]
}
```

Esta estrutura pode funcionar para um utilizador com alguns milhares de seguidores. No entanto, se alguma celebridade se juntar às fileiras, esta abordagem levará a um grande tamanho de documento, e pode eventualmente atingir a tampa do tamanho do documento.

Para resolver este problema, pode utilizar uma abordagem mista. Como parte do documento de Estatísticas do Utilizador, pode armazenar o número de seguidores:

```json
{
    "id":"234d-sd23-rrf2-552d",
    "user": "dse4-qwe2-ert4-aad2",
    "followers":55230,
    "totalPosts":452,
    "totalPoints":11342
}
```

Pode armazenar o gráfico real dos seguidores usando a [API API](../cosmos-db/graph-introduction.md) AZure Cosmos DB Gremlin para criar [vértices](http://mathworld.wolfram.com/GraphVertex.html) para cada utilizador e [bordas](http://mathworld.wolfram.com/GraphEdge.html) que mantêm as relações "A-follows-B". Com a API Gremlin, você pode obter os seguidores de um certo utilizador e criar consultas mais complexas para sugerir pessoas em comum. Se adicionar ao gráfico as Categorias de Conteúdo que as pessoas gostam ou gostam, pode começar a tecer experiências que incluem a descoberta de conteúdos inteligentes, sugerindo conteúdo que as pessoas que segue, ou encontrar pessoas com as quais possa ter muito em comum.

O documento de Estatísticas do Utilizador ainda pode ser usado para criar cartões na UI ou pré-visualizações de perfis rápidos.

## <a name="the-ladder-pattern-and-data-duplication"></a>O padrão "Escada" e duplicação de dados

Como deve ter notado no documento JSON que faz referência a um post, existem muitas ocorrências de um utilizador. E teria adivinhado bem, estas duplicatas significam que a informação que descreve um utilizador, dada esta desnormalização, pode estar presente em mais de um lugar.

Para permitir consultas mais rápidas, incorre na duplicação de dados. O problema com este efeito colateral é que se, por alguma ação, os dados de um utilizador forem alterando, é necessário encontrar todas as atividades que o utilizador já fez e atualizá-las todas. Não parece prático, certo?

Vai resolvê-lo identificando os principais atributos de um utilizador que mostra na sua aplicação para cada atividade. Se mostra visualmente uma publicação na sua aplicação e mostra apenas o nome e a imagem do criador, porquê armazenar todos os dados do utilizador no atributo "createdBy"? Se para cada comentário apenas mostrar a imagem do utilizador, não precisa realmente do resto da informação do utilizador. É onde algo a que chamo "padrão de escada" se envolve.

Vejamos a informação do utilizador como um exemplo:

```json
{
    "id":"dse4-qwe2-ert4-aad2",
    "name":"John",
    "surname":"Doe",
    "address":"742 Evergreen Terrace",
    "birthday":"1983-05-07",
    "email":"john@doe.com",
    "twitterHandle":"\@john",
    "username":"johndoe",
    "password":"some_encrypted_phrase",
    "totalPoints":100,
    "totalPosts":24
}
```

Ao olhar para esta informação, você pode rapidamente detetar o que é informação crítica e que não é, criando assim uma "Escada":

:::image type="content" source="./media/social-media-apps/social-media-apps-ladder.png" alt-text="Diagrama de um padrão de escada" border="false":::

O passo mais pequeno chama-se UserChunk, a informação mínima que identifica um utilizador e é usada para duplicação de dados. Ao reduzir o tamanho duplicado dos dados apenas para a informação que irá "mostrar", reduz a possibilidade de atualizações massivas.

O passo médio chama-se utilizador. São os dados completos que serão usados na maioria das consultas dependentes do desempenho na Cosmos DB, a mais acedida e crítica. Inclui a informação representada por um UserChunk.

O maior é o Utilizador Alargado. Inclui as informações críticas do utilizador e outros dados que não precisam de ser lidos rapidamente ou que têm uma eventual utilização, como o processo de inscrição. Estes dados podem ser armazenados fora da Cosmos DB, na Base de Dados Azure SQL ou nas Tabelas de Armazenamento Azure.

Por que dividiria o utilizador e armazenaria esta informação em diferentes lugares? Porque do ponto de vista da performance, quanto maiores os documentos, mais dispendiosos são as consultas. Mantenha os documentos escassos, com a informação certa para fazer todas as suas consultas dependentes do desempenho para a sua rede social. Guarde as outras informações extra para eventuais cenários como edições de perfil completo, logins e mineração de dados para análise de uso e iniciativas de Big Data. Você realmente não se importa se a recolha de dados para a mineração de dados é mais lenta, porque está em execução na Base de Dados Azure SQL. Tem preocupações, no entanto, de que os seus utilizadores tenham uma experiência rápida e escassa. Um utilizador armazenado na Cosmos DB seria parecido com este código:

```json
{
    "id":"dse4-qwe2-ert4-aad2",
    "name":"John",
    "surname":"Doe",
    "username":"johndoe"
    "email":"john@doe.com",
    "twitterHandle":"\@john"
}
```

E um Post seria como:

```json
{
    "id":"1234-asd3-54ts-199a",
    "title":"Awesome post!",
    "date":"2016-01-02",
    "createdBy":{
        "id":"dse4-qwe2-ert4-aad2",
        "username":"johndoe"
    }
}
```

Quando uma edição surge onde um atributo de pedaço é afetado, você pode facilmente encontrar os documentos afetados. Basta usar consultas que apontam para os atributos indexados, `SELECT * FROM posts p WHERE p.createdBy.id == "edited_user_id"` como, por exemplo, e depois atualizar os pedaços.

## <a name="the-search-box"></a>A caixa de pesquisa

Os utilizadores gerarão, felizmente, muito conteúdo. E deve ser capaz de fornecer a capacidade de pesquisar e encontrar conteúdo que possa não estar diretamente nos seus streams de conteúdo, talvez porque não siga os criadores, ou talvez esteja apenas a tentar encontrar o antigo post que fez há seis meses.

Como está a usar o Azure Cosmos DB, pode facilmente implementar um motor de busca usando [a Azure Cognitive Search](https://azure.microsoft.com/services/search/) em poucos minutos sem digitar qualquer código, além do processo de pesquisa e UI.

Por que este processo é tão fácil?

A Azure Cognitive Search implementa o que chamam [de Indexers,](/rest/api/searchservice/Indexer-operations)processos de fundo que se ligam aos seus repositórios de dados e adição autógicamente, atualização ou remoção dos seus objetos nos índices. Suportam [um indexante de base de dados Azure SQL,](/archive/blogs/kaevans/indexing-azure-sql-database-with-azure-search) [indexadores Azure Blobs](../search/search-howto-indexing-azure-blob-storage.md) e, felizmente, [indexadores Azure Cosmos DB](../search/search-howto-index-cosmosdb.md). A transição de informação de Cosmos DB para Azure Cognitive Search é simples. Ambas as tecnologias armazenam informações em formato JSON, pelo que basta criar o [seu Índice](../search/search-what-is-an-index.md) e mapear os atributos dos seus Documentos que pretende indexados. Já está! Dependendo do tamanho dos seus dados, todo o seu conteúdo estará disponível para ser pesquisado em poucos minutos pela melhor solução de Search-as-a-Service em infraestruturas de nuvem.

Para mais informações sobre a Pesquisa Cognitiva Azure, pode visitar o [Guia de Pesquisa da Hitchhiker.](/archive/blogs/mvpawardprogram/a-hitchhikers-guide-to-search)

## <a name="the-underlying-knowledge"></a>O conhecimento subjacente

Depois de armazenar todo este conteúdo que cresce e cresce todos os dias, poderá encontrar a pensar: O que posso fazer com todo este fluxo de informação dos meus utilizadores?

A resposta é simples: ponha-o a trabalhar e aprenda com ele.

Mas o que se pode aprender? Alguns exemplos fáceis incluem [análise de sentimentos,](https://en.wikipedia.org/wiki/Sentiment_analysis)recomendações de conteúdo baseadas nas preferências de um utilizador, ou até mesmo um moderador de conteúdo automatizado que garante que o conteúdo publicado pela sua rede social é seguro para a família.

Agora que te fiquei viciado, provavelmente vais pensar que precisas de um doutoramento em ciências matemáticas para extrair estes padrões e informações de bases de dados e ficheiros simples, mas estarias enganado.

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), parte do [Cortana Intelligence Suite,](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx)é um serviço de nuvem totalmente gerido que permite criar fluxos de trabalho usando algoritmos numa interface simples de arrastar e largar, codificar os seus próprios algoritmos em [R,](https://en.wikipedia.org/wiki/R_\(programming_language\))ou utilizar alguns dos APIs já construídos e prontos para usar APIs como: [Text Analytics,](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2)Content Moderator ou [Recommendations](https://gallery.azure.ai/Solution/Recommendations-Solution).

Para alcançar qualquer um destes cenários de Machine Learning, você pode usar [O Lago de Dados Azure](https://azure.microsoft.com/services/data-lake-store/) para ingerir a informação de diferentes fontes. Também pode utilizar [o U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) para processar a informação e gerar uma saída que pode ser processada pela Azure Machine Learning.

Outra opção disponível é utilizar os [Serviços Cognitivos Azure](https://www.microsoft.com/cognitive-services) para analisar o conteúdo dos seus utilizadores; não só pode compreendê-los melhor (através da análise do que escrevem com [a API de Análise de Texto),](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)como também pode detetar conteúdo indesejado ou maduro e agir em conformidade com [a API de Visão De Computador.](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api) Os Serviços Cognitivos incluem muitas soluções fora da caixa que não requerem qualquer tipo de conhecimento de Aprendizagem automática para usar.

## <a name="a-planet-scale-social-experience"></a>Uma experiência social à escala planetário

Há um último, mas não menos importante, artigo importante que devo abordar: **escalabilidade**. Quando se projeta uma arquitetura, cada componente deve escalar por si só. Eventualmente, terá de processar mais dados, ou terá uma maior cobertura geográfica. Felizmente, realizar ambas as tarefas é uma **experiência chave na mão** com a Cosmos DB.

Cosmos DB suporta divisórias dinâmicas fora da caixa. Cria automaticamente divisórias com base numa determinada **chave de partição,** que é definida como um atributo nos seus documentos. A definição da chave de partição correta deve ser feita no momento do desenho. Para mais informações, consulte [Partitioning in Azure Cosmos DB](partitioning-overview.md).

Para uma experiência social, deve alinhar a sua estratégia de partição com a forma como consulta e escreve. (Por exemplo, as leituras dentro da mesma partição são desejáveis e evitam "pontos quentes" espalhando escritos em várias divisórias.) Algumas opções são: divisórias baseadas numa chave temporal (dia/mês/semana), por categoria de conteúdo, por região geográfica ou pelo utilizador. Tudo depende realmente de como irá consultar os dados e mostrar os dados na sua experiência social.

Cosmos DB executará as suas consultas (incluindo [agregados)](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)em todas as suas divisórias de forma transparente, para que não precise adicionar nenhuma lógica à medida que os seus dados crescem.

Com o tempo, acabará por crescer no tráfego e o seu consumo de recursos (medido em [RUs,](request-units.md)ou Unidades de Pedido) aumentará. Você vai ler e escrever com mais frequência à medida que a sua base de utilizadores cresce. A base de utilizadores começará a criar e a ler mais conteúdo. Então a capacidade de **escalar a sua produção** é vital. Aumentar as RUs é fácil. Pode fazê-lo com alguns cliques no portal Azure ou [emitindo comandos através da API](/rest/api/cosmos-db/replace-an-offer).

:::image type="content" source="./media/social-media-apps/social-media-apps-scaling.png" alt-text="Escalonamento e definição de uma chave de partição":::

O que acontece se as coisas continuarem a melhorar? Suponha que os utilizadores de outra região, país ou continente notem a sua plataforma e comecem a usá-la. Que grande surpresa!

Mas espere! Logo percebes que a experiência deles com a tua plataforma não é ótima. Estão tão longe da sua região operacional que a latência é terrível. É óbvio que não queres que desistam. Se ao menos houvesse uma maneira fácil de **alargar o seu alcance global?** Tem sim!

Cosmos DB [permite-lhe replicar os seus dados de forma global](../cosmos-db/tutorial-global-distribution-sql-api.md) e transparente com alguns cliques e selecionar automaticamente entre as regiões disponíveis a partir do código do seu [cliente.](../cosmos-db/tutorial-global-distribution-sql-api.md) Este processo também significa que você pode ter [várias regiões de failover](high-availability.md).

Quando replica os seus dados globalmente, tem de se certificar de que os seus clientes podem tirar partido dos mesmos. Se estiver a utilizar um frontend web ou a aceder a APIs de clientes móveis, pode implementar [o Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) e clonar o seu Serviço de Aplicações Azure em todas as regiões desejadas, utilizando uma configuração de desempenho para suportar a sua cobertura global alargada. Quando os seus clientes acederem ao seu frontend ou APIs, serão encaminhados para o Serviço de Aplicações mais próximo, que por sua vez, irá ligar-se à réplica local do Cosmos DB.

:::image type="content" source="./media/social-media-apps/social-media-apps-global-replicate.png" alt-text="Adicionar cobertura global à sua plataforma social" border="false":::

## <a name="conclusion"></a>Conclusão

Este artigo lança alguma luz sobre as alternativas de criar redes sociais completamente no Azure com serviços de baixo custo. fornece resultados encorajando a utilização de uma solução de armazenamento em várias camadas e distribuição de dados chamada "Escada".

:::image type="content" source="./media/social-media-apps/social-media-apps-azure-solution.png" alt-text="Diagrama de interação entre serviços Azure para redes sociais" border="false":::

A verdade é que não há bala de prata para este tipo de cenários. É a sinergia criada pela combinação de grandes serviços que nos permitem construir grandes experiências: a velocidade e a liberdade de Azure Cosmos DB para fornecer uma grande aplicação social, a inteligência por trás de uma solução de pesquisa de primeira classe como a Azure Cognitive Search, a flexibilidade dos Serviços de Aplicações Azure para acolher nem mesmo aplicações agnósticas linguísticas, mas processos de fundo poderosos e o expandível Azure Storage e Azure SQL Database para armazenar quantidades massivas de dados e o poder analítico da Azure Machine Aprender a criar conhecimento e inteligência que possa fornecer feedback aos seus processos e ajudar-nos a entregar o conteúdo certo aos utilizadores certos.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre casos de utilização para Cosmos DB, consulte [os casos de utilização do Common Cosmos DB](use-cases.md).