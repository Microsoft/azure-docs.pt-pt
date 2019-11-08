---
title: 'Padrão de design de Azure Cosmos DB: aplicativos de mídia social'
description: Saiba mais sobre um padrão de design para redes sociais aproveitando a flexibilidade de armazenamento de Azure Cosmos DB e outros serviços do Azure.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: maquaran
ms.openlocfilehash: 8428e417f5f86edca77edae6ca4b7ef84e5ff425
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827304"
---
# <a name="going-social-with-azure-cosmos-db"></a>Tornando-se social com Azure Cosmos DB

Viver em uma sociedade imensamente interconectada significa que, em algum ponto na vida, você se torna parte de uma **rede social**. Você usa redes sociais para manter contato com amigos, colegas, família ou, às vezes, para compartilhar sua paixão com pessoas com interesses comuns.

Como engenheiros ou desenvolvedores, você pode ter se perguntou como essas redes armazenam e interconectam seus dados. Ou talvez você tenha recebido uma tarefa para criar ou arquitetar uma nova rede social para um mercado de nicho específico. É assim que surge a pergunta significativa: como todos esses dados são armazenados?

Suponha que você esteja criando uma rede social nova e brilhante na qual os usuários podem postar artigos com mídia relacionada, como imagens, vídeos ou até mesmo música. Os usuários podem comentar sobre postagens e dar pontos para classificações. Haverá um feed de postagens que os usuários verão e interagem na página de aterrissagem do site principal. Esse método não parece ser complexo a princípio, mas, para simplificar, vamos parar lá. (Você pode se aprofundar em feeds de usuários personalizados afetados por relações, mas vai além do objetivo deste artigo.)

Então, como você armazena esses dados e onde?

Você pode ter experiência em bancos de dados SQL ou ter uma noção de [modelagem relacional de data](https://en.wikipedia.org/wiki/Relational_model). Você pode começar a desenhar algo da seguinte maneira:

![Diagrama ilustrando um modelo relacional relativo](./media/social-media-apps/social-media-apps-sql.png)

Uma estrutura de dados perfeitamente normalizada e bonita... Isso não dimensiona.

Não me deixe errado, trabalhei com bancos de dados SQL toda minha vida. Eles são ótimos, mas, como todos os padrões, práticas e plataformas de software, não são perfeitos para todos os cenários.

Por que o SQL não é a melhor opção nesse cenário? Vamos examinar a estrutura de uma única postagem. Se eu quisesse mostrar a postagem em um site ou aplicativo, teria que fazer uma consulta com... unindo oito tabelas (!) apenas para mostrar uma única postagem. Agora, veja um fluxo de postagens que carregam e aparecem na tela de forma dinâmica, e você pode ver onde estou indo.

Você pode usar uma enorme instância do SQL com energia suficiente para resolver milhares de consultas com muitas junções para fornecer seu conteúdo. Mas por que você faria isso, quando existe uma solução mais simples?

## <a name="the-nosql-road"></a>A estrada NoSQL

Este artigo orienta você a modelar os dados da sua plataforma social com o Azure NoSQL [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) de forma econômica. Ele também informa como usar outros recursos de Azure Cosmos DB como a [API Gremlin](../cosmos-db/graph-introduction.md). Usando uma abordagem [NoSQL](https://en.wikipedia.org/wiki/NoSQL) , armazenando dados, no formato JSON e aplicando a [desnormalização](https://en.wikipedia.org/wiki/Denormalization), a postagem anteriormente complicada pode ser transformada em um único [documento](https://en.wikipedia.org/wiki/Document-oriented_database):

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

E isso pode ser obtido com uma única consulta e sem junções. Essa consulta é muito simples e direta e, em termos de orçamento, requer menos recursos para obter um resultado melhor.

Azure Cosmos DB garante que todas as propriedades sejam indexadas com sua indexação automática. A indexação automática pode até mesmo ser [personalizada](index-policy.md). A abordagem sem esquemas nos permite armazenar documentos com estruturas diferentes e dinâmicas. Talvez amanhã você queira que as postagens tenham uma lista de categorias ou hashtags associadas a elas? Cosmos DB manipulará os novos documentos com os atributos adicionados sem que o trabalho extra seja exigido por nós.

Os comentários em uma postagem podem ser tratados como outras postagens com uma propriedade pai. (Essa prática simplifica o mapeamento de objetos.)

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

E todas as interações sociais podem ser armazenadas em um objeto separado como contadores:

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

A criação de feeds é apenas uma questão de criar documentos que podem conter uma lista de IDs de postagem com uma determinada ordem de relevância:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Você pode ter um fluxo "mais recente" com postagens ordenadas por data de criação. Ou você pode ter um fluxo "mais preciso" com essas postagens com mais curtidas nas últimas 24 horas. Você poderia até mesmo implementar um fluxo personalizado para cada usuário com base em lógica como seguidores e interesses. Ainda seria uma lista de postagens. É uma questão de como criar essas listas, mas o desempenho de leitura permanece desprejudicado. Depois de adquirir uma dessas listas, você emite uma única consulta para Cosmos DB usando a [palavra-chave in](sql-query-keywords.md#in) para obter páginas de postagens por vez.

Os fluxos de feeds podem ser criados usando os processos em segundo plano do [Azure app Services](https://azure.microsoft.com/services/app-service/) : [webjobs](../app-service/webjobs-create.md). Depois que uma postagem é criada, o processamento em segundo plano pode ser disparado usando as [filas](../storage/queues/storage-dotnet-how-to-use-queues.md) do [armazenamento do Azure](https://azure.microsoft.com/services/storage/) e os trabalhos Web disparados usando o [SDK de trabalhos Web do Azure](https://github.com/Azure/azure-webjobs-sdk/wiki), implementando a propagação posterior dentro de fluxos com base em sua própria lógica personalizada.

Pontos e curtidos em uma postagem podem ser processados de forma adiada usando essa mesma técnica para criar um ambiente eventualmente consistente.

Os seguidores são mais complicados. Cosmos DB tem um limite de tamanho de documento e a leitura/gravação de documentos grandes pode afetar a escalabilidade do seu aplicativo. Você pode pensar em armazenar seguidores como um documento com esta estrutura:

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

Essa estrutura pode funcionar para um usuário com alguns milhares de seguidores. No entanto, se algumas celebridade unirem as classificações, essa abordagem levará a um grande tamanho de documento e poderá eventualmente atingir o limite de tamanho do documento.

Para resolver esse problema, você pode usar uma abordagem mista. Como parte do documento de estatísticas de usuário, você pode armazenar o número de seguidores:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

Você pode armazenar o grafo real de seguidores usando Azure Cosmos DB [API Gremlin](../cosmos-db/graph-introduction.md) para criar [vértices](http://mathworld.wolfram.com/GraphVertex.html) para cada usuário e [bordas](http://mathworld.wolfram.com/GraphEdge.html) que mantêm as relações "A segue-B". Com a API do Gremlin, você pode obter os seguidores de um determinado usuário e criar consultas mais complexas para sugerir pessoas em comum. Se você adicionar ao grafo as categorias de conteúdo que as pessoas gostam ou gostam, poderá iniciar as experiências de combinando que incluem a descoberta de conteúdo inteligente, sugerindo o conteúdo que essas pessoas seguem ou localizando pessoas que você possa ter muito em comum com o.

O documento de estatísticas de usuário ainda pode ser usado para criar cartões na interface do usuário ou em visualizações de perfil rápido.

## <a name="the-ladder-pattern-and-data-duplication"></a>O padrão de "escada" e a duplicação de dados

Como você deve ter notado no documento JSON que faz referência a uma postagem, há muitas ocorrências de um usuário. E você teria adivinhado o direito, essas duplicatas significam que as informações que descrevem um usuário, dada essa desnormalização, podem estar presentes em mais de um lugar.

Para permitir consultas mais rápidas, você incorre em duplicação de dados. O problema com esse efeito colateral é que, se, por alguma ação, os dados de um usuário forem alterados, você precisará encontrar todas as atividades que o usuário já fez e atualizá-las. Não parece prático, certo?

Você vai solucioná-lo identificando os principais atributos de um usuário que você mostra em seu aplicativo para cada atividade. Se você mostrar visualmente uma postagem em seu aplicativo e mostrar apenas o nome e a imagem do criador, por que armazenar todos os dados do usuário no atributo "createdBy"? Se, para cada comentário, você apenas mostrar a imagem do usuário, não precisará realmente do restante das informações do usuário. É aí que algo que chamo de "padrão de escada" se torna envolvido.

Vamos pegar as informações do usuário como um exemplo:

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

Ao examinar essas informações, você pode detectar rapidamente quais são as informações críticas e qual não é, criando, portanto, uma "escada":

![Diagrama de um padrão de escada](./media/social-media-apps/social-media-apps-ladder.png)

A menor etapa é chamada de userchunk, a informação mínima que identifica um usuário e é usada para duplicação de dados. Ao reduzir o tamanho dos dados duplicados para apenas as informações que você "Mostrar", você reduz a possibilidade de atualizações maciças.

A etapa intermediária é chamada de usuário. São os dados completos que serão usados na maioria das consultas dependentes de desempenho em Cosmos DB, o mais acessado e crítico. Ele inclui as informações representadas por um userchunk.

O maior é o usuário estendido. Ele inclui as informações críticas do usuário e outros dados que não precisam ser lidos rapidamente ou que têm uso eventual, como o processo de entrada. Esses dados podem ser armazenados fora do Cosmos DB, no banco de dados SQL do Azure ou em tabelas de armazenamento do Azure.

Por que você dividiria o usuário e até mesmo armazenaria essas informações em locais diferentes? Devido a um ponto de vista de desempenho, quanto maior os documentos, mais caras as consultas. Mantenha documentos finos, com as informações corretas para fazer todas as suas consultas dependentes de desempenho para sua rede social. Armazene as outras informações extras para cenários eventuales, como edições de perfil completas, logons e Data Mining para análise de uso e iniciativas de Big Data. Na verdade, você não se importa se a coleta de dados para Data Mining é mais lenta, pois está em execução no Azure SQL Database. Você tem dúvidas para que os usuários tenham uma experiência rápida e reduzida. Um usuário armazenado em Cosmos DB seria semelhante a este código:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"\@john"
    }

E uma postagem teria a seguinte aparência:

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

Quando surge uma edição em que um atributo de bloco é afetado, você pode encontrar facilmente os documentos afetados. Basta usar consultas que apontem para os atributos indexados, como `SELECT * FROM posts p WHERE p.createdBy.id == "edited_user_id"`e, em seguida, atualizar as partes.

## <a name="the-search-box"></a>A caixa de pesquisa

Os usuários irão gerar, felizmente, muito conteúdo. E você deve ser capaz de fornecer a capacidade de Pesquisar e localizar conteúdo que pode não estar diretamente em seus fluxos de conteúdo, talvez porque você não segue os criadores, ou talvez esteja apenas tentando descobrir essa postagem antiga que você fez seis meses atrás.

Como você está usando Azure Cosmos DB, é possível implementar facilmente um mecanismo de pesquisa usando o [pesquisa cognitiva do Azure](https://azure.microsoft.com/services/search/) em alguns minutos sem digitar nenhum código, além do processo de pesquisa e da interface do usuário.

Por que esse processo é tão fácil?

O Azure Pesquisa Cognitiva implementa o que eles chamam de [indexadores](https://msdn.microsoft.com/library/azure/dn946891.aspx), processos em segundo plano que conectam seus repositórios de dados e adicionam, atualizam ou removem de forma automática os objetos nos índices. Eles dão suporte a indexadores de [banco de dados SQL do Azure](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), [indexadores de BLOBs do Azure](../search/search-howto-indexing-azure-blob-storage.md) e, felizmente, [Azure Cosmos DB indexadores](../search/search-howto-index-documentdb.md). A transição de informações de Cosmos DB para Pesquisa Cognitiva do Azure é simples. Ambas as tecnologias armazenam informações no formato JSON, portanto, você só precisa [criar o índice](../search/search-create-index-portal.md) e mapear os atributos dos documentos que deseja indexar. Já está! Dependendo do tamanho dos dados, todo o conteúdo estará disponível para ser pesquisado em minutos pela melhor solução de pesquisa como serviço na infraestrutura de nuvem.

Para obter mais informações sobre o Azure Pesquisa Cognitiva, você pode visitar o [Guia do Hitchhiker ' s para pesquisar](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/).

## <a name="the-underlying-knowledge"></a>O conhecimento subjacente

Depois de armazenar todo o conteúdo que cresce e cresce todos os dias, você pode pensar: o que posso fazer com todo esse fluxo de informações de meus usuários?

A resposta é simples: colocá-la para trabalhar e aprender com ela.

Mas o que você pode aprender? Alguns exemplos fáceis incluem [análise de sentimentos](https://en.wikipedia.org/wiki/Sentiment_analysis), recomendações de conteúdo com base nas preferências de um usuário ou até mesmo um moderador de conteúdo automatizado que garante que o conteúdo publicado pela sua rede social seja seguro para a família.

Agora que fui conectado, você provavelmente achará que precisa de um PhD em ciência matemática para extrair esses padrões e informações dos arquivos e bancos de dados simples, mas você estaria errado.

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), parte do [Cortana Intelligence Suite](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx), é um serviço de nuvem totalmente gerenciado que permite criar fluxos de trabalho usando algoritmos em uma interface simples do tipo "arrastar e soltar", codificar seus próprios algoritmos em [R](https://en.wikipedia.org/wiki/R_\(programming_language\))ou usar alguns dos Você já criou e está pronto para usar APIs como: [análise de texto](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [content moderator ou [recomendações](https://gallery.azure.ai/Solution/Recommendations-Solution).

Para obter qualquer um desses cenários de Machine Learning, você pode usar [Azure data Lake](https://azure.microsoft.com/services/data-lake-store/) para ingerir as informações de fontes diferentes. Você também pode usar o [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) para processar as informações e gerar uma saída que pode ser processada pelo Azure Machine Learning.

Outra opção disponível é usar os [Serviços cognitivas do Azure](https://www.microsoft.com/cognitive-services) para analisar o conteúdo dos usuários; Você não só pode entender melhor isso (por meio da análise do que eles escrevem com [API de análise de texto](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), mas também pode detectar conteúdo indesejado ou maduro e agir de acordo com [API da pesquisa Visual computacional](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Os serviços cognitivas incluem muitas soluções prontas para uso que não exigem qualquer tipo de conhecimento Machine Learning para usar o.

## <a name="a-planet-scale-social-experience"></a>Uma experiência social de dimensionamento de planeta

Há um artigo, mas não menos importante, que preciso abordar: **escalabilidade**. Quando você cria uma arquitetura, cada componente deve ser dimensionado por conta própria. Eventualmente, você precisará processar mais dados ou terá uma cobertura geográfica maior. Felizmente, a obtenção de ambas as tarefas é uma **experiência pronta para uso** com o cosmos DB.

O Cosmos DB dá suporte ao particionamento dinâmico pronto para uso. Ele cria partições automaticamente com base em uma determinada **chave de partição**, que é definida como um atributo em seus documentos. A definição da chave de partição correta deve ser feita em tempo de design. Para obter mais informações, consulte [particionamento no Azure Cosmos DB](partitioning-overview.md).

Para uma experiência social, você deve alinhar sua estratégia de particionamento com a maneira como você consulta e escreve. (Por exemplo, as leituras na mesma partição são desejáveis e evitam "pontos de acesso" espalhando gravações em várias partições.) Algumas opções são: partições baseadas em uma chave temporal (dia/mês/semana), por categoria de conteúdo, por região geográfica ou por usuário. Tudo isso realmente depende de como você consultará os dados e mostrará os dados em sua experiência social.

Cosmos DB executará suas consultas (incluindo [agregações](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)) em todas as partições de forma transparente, para que você não precise adicionar nenhuma lógica à medida que seus dados crescem.

Com o tempo, você acabará crescendo no tráfego e o consumo de recursos (medido em [RUs](request-units.md)ou unidades de solicitação) aumentará. Você lerá e gravará com mais frequência à medida que sua base de usuários cresce. A base de usuários começará a criar e a ler mais conteúdo. Portanto, a capacidade de **dimensionar sua taxa de transferência** é vital. É fácil aumentar seu RUs. Você pode fazer isso com alguns cliques na portal do Azure ou [emitindo comandos por meio da API](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer).

![Dimensionamento e definição de uma chave de partição](./media/social-media-apps/social-media-apps-scaling.png)

O que acontecerá se as coisas continuarem melhorando? Suponha que os usuários de outra região, país ou continente observem sua plataforma e comecem a usá-la. O que é uma grande surpresa!

Mas espere! Em breve, você percebe que sua experiência com a plataforma não é ideal. Estão longe da sua região operacional que a latência é terrível. Obviamente, você não deseja que eles sejam encerrados. Se houvesse uma maneira fácil de **estender seu alcance global**? Há!

Cosmos DB permite [replicar os dados globalmente](../cosmos-db/tutorial-global-distribution-sql-api.md) e de forma transparente com alguns cliques e selecionar automaticamente entre as regiões disponíveis do seu [código de cliente](../cosmos-db/tutorial-global-distribution-sql-api.md). Esse processo também significa que você pode ter [várias regiões de failover](high-availability.md).

Ao replicar seus dados globalmente, você precisa certificar-se de que seus clientes possam tirar proveito dele. Se você estiver usando um front-end da Web ou acessando APIs de clientes móveis, poderá implantar o [Gerenciador de tráfego do Azure](https://azure.microsoft.com/services/traffic-manager/) e clonar seu serviço de Azure app em todas as regiões desejadas, usando uma configuração de desempenho para dar suporte à cobertura global estendida. Quando os clientes acessarem seu front-end ou APIs, eles serão roteados para o serviço de aplicativo mais próximo, que, por sua vez, se conectará à réplica de Cosmos DB local.

![Adicionando cobertura global à sua plataforma social](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>Conclusão

Este artigo esclarece algumas luzes sobre as alternativas de criação completa de redes sociais no Azure com serviços de baixo custo. Ele fornece resultados, incentivando o uso de uma solução de armazenamento em várias camadas e da distribuição de dados chamada "escada".

![Diagrama de interação entre os serviços do Azure para rede social](./media/social-media-apps/social-media-apps-azure-solution.png)

A verdade é que não há nenhum marcador prata para esse tipo de cenário. É a sinergia criada pela combinação de ótimos serviços que nos permitem criar excelentes experiências: a velocidade e a liberdade de Azure Cosmos DB para fornecer um excelente aplicativo social, a inteligência por trás de uma solução de pesquisa de primeira classe como o Pesquisa Cognitiva do Azure, a flexibilidade dos serviços de Azure App para hospedar aplicativos não independentes de linguagem, mas processos em segundo plano avançados e o armazenamento expansível do Azure e o banco de dados SQL do Azure para armazenar grandes quantidades de data e a capacidade analítica de Azure Machine Learning para Crie conhecimento e inteligência que possam fornecer comentários aos seus processos e nos ajudar a fornecer o conteúdo correto aos usuários certos.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os casos de uso para Cosmos DB, consulte [casos de uso comuns de Cosmos DB](use-cases.md).
