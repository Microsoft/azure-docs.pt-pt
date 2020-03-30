---
title: 'Padrão de design de Azure Cosmos DB: Aplicativos de mídia social'
description: Conheça um padrão de design para redes sociais aproveitando a flexibilidade de armazenamento da Azure Cosmos DB e de outros serviços Azure.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: maquaran
ms.openlocfilehash: 8428e417f5f86edca77edae6ca4b7ef84e5ff425
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73827304"
---
# <a name="going-social-with-azure-cosmos-db"></a>Indo social com Azure Cosmos DB

Viver numa sociedade massivamente interligada significa que, em algum momento da vida, se torna parte de uma **rede social.** Usa as redes sociais para manter contacto com amigos, colegas, familiares ou, por vezes, para partilhar a sua paixão com pessoas com interesses comuns.

Como engenheiros ou desenvolvedores, pode ter-se perguntado como é que estas redes armazenam e interligam os seus dados. Ou até pode ter sido incumbido de criar ou arquiteto uma nova rede social para um nicho de mercado específico. É aí que surge a questão significativa: como é que todos estes dados são armazenados?

Suponha que está a criar uma nova e brilhante rede social onde os seus utilizadores podem publicar artigos com meios relacionados, como imagens, vídeos ou até música. Os utilizadores podem comentar publicações e dar pontos para as classificações. Haverá um feed de publicações que os utilizadores irão ver e interagir na página de aterragem do site principal. Este método não parece complexo no início, mas por uma questão de simplicidade, vamos parar por aí. (Você pode mergulhar em feeds personalizados de utilizadores afetados por relacionamentos, mas vai além do objetivo deste artigo.)

Então, como é que guardas estes dados e onde?

Pode ter experiência em bases de dados SQL ou ter uma noção de [modelação relacional de dados](https://en.wikipedia.org/wiki/Relational_model). Pode começar a desenhar algo da seguinte forma:

![Diagrama ilustrando um modelo relacional relativo](./media/social-media-apps/social-media-apps-sql.png)

Uma estrutura de dados perfeitamente normalizada e bonita... que não escala.

Não me entenda mal, trabalhei com bases de dados DaQL toda a minha vida. São ótimos, mas como todos os padrões, práticas e plataformas de software, não é perfeito para todos os cenários.

Porque é que a SQL não é a melhor escolha neste cenário? Vamos olhar para a estrutura de um único poste. Se eu quisesse mostrar o post num site ou aplicação, teria de fazer uma consulta com... juntando oito mesas(!) apenas para mostrar um único poste. Agora imagine um fluxo de publicações que carregam e aparecem no ecrã, e talvez vejam para onde vou.

Você poderia usar um enorme caso SQL com poder suficiente para resolver milhares de consultas com muitas juntas para servir o seu conteúdo. Mas por que o faria, quando existe uma solução mais simples?

## <a name="the-nosql-road"></a>A estrada NoSQL

Este artigo guia-o a modelar os dados da sua plataforma social com a base de dados NoSQL do [Azure Cosmos DB,](https://azure.microsoft.com/services/cosmos-db/) de forma rentável. Também lhe diz como usar outras funcionalidades do Azure Cosmos DB como a [API Gremlin.](../cosmos-db/graph-introduction.md) Utilizando uma abordagem [NoSQL,](https://en.wikipedia.org/wiki/NoSQL) armazenando dados, em formato JSON e aplicando a [desnormalização,](https://en.wikipedia.org/wiki/Denormalization)o post anteriormente complicado pode ser transformado num único [Documento:](https://en.wikipedia.org/wiki/Document-oriented_database)

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

E pode ser obtido com uma única consulta, e sem adesão. Esta consulta é muito simples e simples e, em termos orçamentais, requer menos recursos para obter um resultado melhor.

A Azure Cosmos DB garante que todas as propriedades estão indexadas com a sua indexação automática. O indexante automático pode até ser [personalizado.](index-policy.md) A abordagem sem esquemas permite-nos armazenar documentos com estruturas diferentes e dinâmicas. Talvez amanhã queira que os posts tenham uma lista de categorias ou hashtags associadas a eles? Cosmos DB tratará dos novos Documentos com os atributos adicionais sem trabalho extra exigido por nós.

Comentários sobre uma publicação podem ser tratados como outros posts com uma propriedade dos pais. (Esta prática simplifica o mapeamento do objeto.)

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

E todas as interações sociais podem ser armazenadas num objeto separado como contadores:

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

Criar feeds é apenas uma questão de criar documentos que possam conter uma lista de iDs post com uma determinada ordem de relevância:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Pode ter um fluxo "mais recente" com posts encomendados até à data da criação. Ou podes ter um fluxo "mais quente" com aqueles posts com mais likes nas últimas 24 horas. Pode até implementar um fluxo personalizado para cada utilizador com base em lógicas como seguidores e interesses. Ainda seria uma lista de posts. É uma questão de como construir estas listas, mas o desempenho da leitura permanece sem entraves. Assim que adquire uma destas listas, emite uma única consulta à Cosmos DB usando a [palavra-chave IN](sql-query-keywords.md#in) para obter páginas de publicações de cada vez.

Os fluxos de alimentação poderiam ser construídos utilizando os processos de fundo [da Azure App Services:](https://azure.microsoft.com/services/app-service/) [Webjobs](../app-service/webjobs-create.md). Uma vez criado um post, o processamento de fundo pode ser desencadeado usando filas de [armazenamento azure](https://azure.microsoft.com/services/storage/) e Webjobs [desencadeados](../storage/queues/storage-dotnet-how-to-use-queues.md) usando o [Azure Webjobs SDK,](https://github.com/Azure/azure-webjobs-sdk/wiki)implementando a propagação de postdentro de streams com base na sua própria lógica personalizada.

Pontos e gostos sobre um post podem ser processados de forma diferida usando esta mesma técnica para criar um ambiente eventualmente consistente.

Os seguidores são mais complicados. Cosmos DB tem um limite de tamanho de documento, e ler/escrever grandes documentos pode afetar a escalabilidade da sua aplicação. Por isso, pode pensar em armazenar seguidores como um documento com esta estrutura:

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

Esta estrutura pode funcionar para um utilizador com alguns milhares de seguidores. No entanto, se alguma celebridade se juntar às fileiras, esta abordagem levará a um grande tamanho de documento, e pode eventualmente atingir a tampa do tamanho do documento.

Para resolver este problema, pode usar uma abordagem mista. Como parte do documento de estatísticas do utilizador pode armazenar o número de seguidores:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

Pode armazenar o gráfico real de seguidores usando a API Azure Cosmos DB [Gremlin](../cosmos-db/graph-introduction.md) para criar [vértices](http://mathworld.wolfram.com/GraphVertex.html) para cada utilizador e [bordas](http://mathworld.wolfram.com/GraphEdge.html) que mantêm as relações "A-follows-B". Com a API Gremlin, você pode obter os seguidores de um determinado utilizador e criar consultas mais complexas para sugerir pessoas em comum. Se adicionar ao gráfico as Categorias de Conteúdo que as pessoas gostam ou gostam, pode começar a tecer experiências que incluem a descoberta de conteúdo inteligente, sugerindo conteúdo que as pessoas que segue, ou encontrar pessoas com as quais possa ter muito em comum.

O documento de estatísticas de utilizadores ainda pode ser usado para criar cartões nas pré-visualizações de ui ou de perfil rápido.

## <a name="the-ladder-pattern-and-data-duplication"></a>O padrão "Escada" e duplicação de dados

Como deve ter reparado no documento JSON que faz referência a uma publicação, existem muitas ocorrências de um utilizador. E teria adivinhado bem, estes duplicados significam que a informação que descreve um utilizador, dada esta desnormalização, pode estar presente em mais de um lugar.

Para permitir consultas mais rápidas, incorre na duplicação de dados. O problema com este efeito colateral é que se por alguma ação, os dados de um utilizador mudar, você precisa encontrar todas as atividades que o utilizador já fez e atualizá-las todas. Não parece prático, certo?

Vai resolvê-lo identificando os principais atributos de um utilizador que mostra na sua aplicação para cada atividade. Se mostra visualmente uma publicação na sua aplicação e mostra apenas o nome e a imagem do criador, por que armazenar todos os dados do utilizador no atributo "createdBy"? Se para cada comentário apenas mostrar a imagem do utilizador, não precisa realmente do resto das informações do utilizador. É aí que algo a que chamo "padrão de escada" se envolve.

Vamos tomar a informação do utilizador como um exemplo:

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

Ao olhar para esta informação, pode detetar rapidamente o que é informação crítica e que não é, criando assim uma "Escada":

![Diagrama de um padrão de escada](./media/social-media-apps/social-media-apps-ladder.png)

O menor passo chama-se UserChunk, a informação mínima que identifica um utilizador e é usada para duplicação de dados. Ao reduzir o tamanho dos dados duplicados apenas à informação que irá "mostrar", reduz a possibilidade de atualizações massivas.

O passo do meio chama-se utilizador. São os dados completos que serão usados na maioria das consultas dependentes do desempenho no Cosmos DB, os mais acedidos e críticos. Inclui as informações representadas por um UserChunk.

O maior é o Utilizador Estendido. Inclui a informação crítica do utilizador e outros dados que não precisam de ser lidos rapidamente ou que têm eventual utilização, como o processo de iniciar sessão. Estes dados podem ser armazenados fora da Cosmos DB, em Base de Dados Azure SQL ou Em Mesas de Armazenamento Azure.

Por que dividir ia dividir o utilizador e até armazenar esta informação em diferentes lugares? Porque do ponto de vista do desempenho, quanto maior forem os documentos, mais caros são as consultas. Mantenha os documentos escassos, com a informação certa para fazer todas as suas consultas dependentes do desempenho para a sua rede social. Guarde as outras informações adicionais para eventuais cenários como edificações de perfil completo, logins e mineração de dados para análise de uso e iniciativas big data. Você realmente não se importa se a recolha de dados para a mineração de dados é mais lenta, porque está a funcionar na Base de Dados Azure SQL. No entanto, tem a preocupação de que os seus utilizadores tenham uma experiência rápida e escassa. Um utilizador armazenado no Cosmos DB seria parecido com este código:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"\@john"
    }

E um post seria como:

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

Quando uma edição surge onde um atributo é afetado, você pode facilmente encontrar os documentos afetados. Basta utilizar consultas que apontem para os `SELECT * FROM posts p WHERE p.createdBy.id == "edited_user_id"`atributos indexados, como , e depois atualizar os pedaços.

## <a name="the-search-box"></a>A caixa de pesquisa

Os utilizadores gerarão, felizmente, muito conteúdo. E deves ser capaz de fornecer a capacidade de pesquisar e encontrar conteúdos que possam não estar diretamente nos seus fluxos de conteúdo, talvez porque não sigas os criadores, ou talvez estejas apenas a tentar encontrar aquela publicação antiga que fizeste há seis meses.

Como está a usar o Azure Cosmos DB, pode facilmente implementar um motor de busca utilizando a [Pesquisa Cognitiva Azure](https://azure.microsoft.com/services/search/) em poucos minutos sem digitar qualquer código, além do processo de pesquisa e uI.

Por que este processo é tão fácil?

A Pesquisa Cognitiva Azure implementa o que chamam de [Indexers,](https://msdn.microsoft.com/library/azure/dn946891.aspx)processos de fundo que se ligam aos seus repositórios de dados e adicionam, atualizam ou removem automaticamente os seus objetos nos índices. Suportam um Indexers de Base de [Dados Azure SQL,](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/) [indexadores Azure Blobs](../search/search-howto-indexing-azure-blob-storage.md) e, felizmente, [indexadores De DB Azure Cosmos.](../search/search-howto-index-documentdb.md) A transição de informação da Cosmos DB para a Pesquisa Cognitiva Azure é simples. Ambas as tecnologias armazenam informação em formato JSON, por isso basta [criar o seu Índice](../search/search-create-index-portal.md) e mapear os atributos dos seus Documentos que deseja indexados. Já está! Dependendo do tamanho dos seus dados, todo o seu conteúdo estará disponível para ser pesquisado dentro de minutos pela melhor solução de Pesquisa como um Serviço em infraestrutura em nuvem.

Para mais informações sobre a Pesquisa Cognitiva azure, pode visitar o Guia de [Pesquisa da Boleia.](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/)

## <a name="the-underlying-knowledge"></a>O conhecimento subjacente

Depois de armazenar todo este conteúdo que cresce e cresce todos os dias, pode achar que penso: O que posso fazer com todo este fluxo de informação dos meus utilizadores?

A resposta é simples: colocá-lo a trabalhar e aprender com ele.

Mas o que pode aprender? Alguns exemplos fáceis incluem análise de [sentimentos,](https://en.wikipedia.org/wiki/Sentiment_analysis)recomendações de conteúdo baseadas nas preferências de um utilizador, ou mesmo um moderador de conteúdo automatizado que garante que o conteúdo publicado pela sua rede social é seguro para a família.

Agora que te deixei viciado, provavelmente vais pensar que precisas de um doutoramento em matemática para extrair estes padrões e informações de bases de dados e ficheiros simples, mas estarias enganado.

[O Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), parte da [Suite de Inteligência cortana,](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx)é um serviço de nuvem totalmente gerido que permite criar fluxos de trabalho usando algoritmos numa interface simples de arrastar e largar, codificar os seus próprios algoritmos em [R,](https://en.wikipedia.org/wiki/R_\(programming_language\))ou usar alguns dos APIs já construídos e prontos a usar APIs como: [Text Analytics](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [Moderador de Conteúdo, ou [Recomendações.](https://gallery.azure.ai/Solution/Recommendations-Solution)

Para alcançar qualquer um destes cenários de Machine Learning, pode utilizar o [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) para ingerir a informação de diferentes fontes. Também pode usar [o U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) para processar a informação e gerar uma saída que pode ser processada pelo Azure Machine Learning.

Outra opção disponível é utilizar [os Serviços Cognitivos Azure](https://www.microsoft.com/cognitive-services) para analisar o conteúdo dos seus utilizadores; não só pode compreendê-los melhor (através da análise do que escrevem com [API](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)de Análise de Texto), como também pode detetar conteúdo indesejado ou maduro e agir em conformidade com a API da [Visão Computacional](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Os Serviços Cognitivos incluem muitas soluções fora da caixa que não requerem qualquer tipo de conhecimento de Aprendizagem automática para usar.

## <a name="a-planet-scale-social-experience"></a>Uma experiência social à escala planetária

Há um último, mas não menos importante, artigo que devo abordar: **escalabilidade.** Quando se projeta uma arquitetura, cada componente deve escalar por si só. Eventualmente terá de processar mais dados, ou terá uma maior cobertura geográfica. Felizmente, alcançar ambas as tarefas é uma **experiência chave na mão** com cosmos DB.

Cosmos DB suporta divisória dinâmica fora da caixa. Cria automaticamente divisórias com base numa determinada chave de **partição,** que é definida como um atributo nos seus documentos. A definição da chave de partição correta deve ser feita no momento do desenho. Para mais informações, consulte [A Partilha em Azure Cosmos DB](partitioning-overview.md).

Para uma experiência social, deve alinhar a sua estratégia de partilha com a forma como consulta e escreve. (Por exemplo, as leituras dentro da mesma divisória são desejáveis e evitam "pontos quentes" espalhando escritos em várias divisórias.) Algumas opções são: divisórias baseadas numa chave temporal (dia/mês/semana), por categoria de conteúdo, por região geográfica ou por utilizador. Tudo depende realmente de como irá consultar os dados e mostrar os dados na sua experiência social.

Cosmos DB irá executar suas consultas (incluindo [agregados)](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)em todas as suas divisórias de forma transparente, por isso não precisa adicionar nenhuma lógica à medida que os seus dados crescem.

Com o tempo, acabará por crescer no tráfego e o seu consumo de recursos (medido em [RUs](request-units.md), ou Unidades de Pedido) aumentará. Você vai ler e escrever com mais frequência à medida que a sua base de utilizador cresce. A base de utilizadores começará a criar e a ler mais conteúdo. Portanto, a capacidade de **escalar a sua entrada** é vital. Aumentar as suas RUs é fácil. Pode fazê-lo com alguns cliques no portal Azure ou através da emissão de [comandos através da API](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer).

![Dimensionar e definir uma chave de partição](./media/social-media-apps/social-media-apps-scaling.png)

O que acontece se as coisas continuarem a melhorar? Suponha que os utilizadores de outra região, país ou continente reparem na sua plataforma e comecem a usá-la. Que grande surpresa!

Mas espere! Logo percebe que a experiência deles com a sua plataforma não é a ideal. Estão tão longe da sua região operacional que a latência é terrível. É óbvio que não queres que se demitam. Se ao menos houvesse uma maneira fácil de **estender o seu alcance global?** Tem!

Cosmos DB [permite-lhe replicar os seus dados global](../cosmos-db/tutorial-global-distribution-sql-api.md) e transparentemente com um par de cliques e selecionar automaticamente entre as regiões disponíveis a partir do seu código de [cliente](../cosmos-db/tutorial-global-distribution-sql-api.md). Este processo também significa que você pode ter [várias regiões de failover](high-availability.md).

Quando replica os seus dados globalmente, tem de se certificar de que os seus clientes podem aproveitar-se dos mesmos. Se estiver a utilizar um frontend web ou a aceder a APIs de clientes móveis, pode implementar o [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) e clonar o seu Serviço de Aplicações Azure em todas as regiões desejadas, utilizando uma configuração de desempenho para suportar a sua cobertura global alargada. Quando os seus clientes acederem à sua frontend ou APIs, serão encaminhados para o Serviço de Aplicações mais próximo, que por sua vez, irá ligar-se à réplica local do Cosmos DB.

![Adicionar cobertura global à sua plataforma social](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>Conclusão

Este artigo lança alguma luz sobre as alternativas de criar redes sociais completamente no Azure com serviços de baixo custo. apresenta resultados incentivando a utilização de uma solução de armazenamento multicamadas e distribuição de dados chamada "Escada".

![Diagrama de interação entre serviços Azure para redes sociais](./media/social-media-apps/social-media-apps-azure-solution.png)

A verdade é que não há bala de prata para este tipo de cenários. É a sinergia criada pela combinação de grandes serviços que nos permitem construir grandes experiências: a velocidade e liberdade do Azure Cosmos DB para fornecer uma grande aplicação social, a inteligência por trás de uma solução de pesquisa de primeira classe como a Pesquisa Cognitiva Azure, a flexibilidade dos Serviços de Aplicações Azure para alojar nem mesmo aplicações agnósticas linguísticas, mas processos de fundo poderosos e a expansível Azure Storage e Azure SQL Database para armazenar quantidades massivas de dados e o poder analítico da Aprendizagem automática Azure criar conhecimento e inteligência que possam fornecer feedback aos seus processos e ajudar-nos a entregar o conteúdo certo aos utilizadores certos.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os casos de utilização para cosmos DB, consulte casos de utilização de DB da [Common Cosmos.](use-cases.md)
