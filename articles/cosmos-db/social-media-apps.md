---
title: 'Padrão de design do Azure Cosmos DB: Aplicações de redes sociais'
description: Saiba mais sobre um padrão de design de redes sociais ao tirar partido da flexibilidade de armazenamento do Azure Cosmos DB e outros serviços do Azure.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: maquaran
ms.openlocfilehash: 36b77ff6666c2c8b0d27cbdc8552ade15b21d005
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60446264"
---
# <a name="going-social-with-azure-cosmos-db"></a>Socializar com o Azure Cosmos DB

Vivendo numa sociedade em massa interconectada significa que, em algum momento na vida, se parte de um **rede social**. Utilizar redes sociais para manter-se em contacto com amigos, colegas, família, ou, às vezes, para partilhar a sua paixão com pessoas com interesses comuns.

Como engenheiros ou desenvolvedores, talvez tenha se perguntado como estas redes armazenar e interligar os seus dados. Ou pode ter até mesmo foi a tarefa de criar ou arquitetar uma nova rede social para um mercado de nicho específico. Que é quando surge a questão significativa: Como são armazenados todos esses dados?

Suponha que está criando uma nova e brilhante rede social, onde os utilizadores podem postar artigos com suporte de dados relacionados, como imagens, vídeos ou músicas até mesmo. Os utilizadores podem comentar postagens e entregar pontos para classificações. Haverá um feed de postagens que os utilizadores irão ver e interagir com na página de aterrissagem do site principal. Este método não parece complexo, primeiro, mas para simplificar, vamos pare por aí. (Me aprofundar em feeds do usuário personalizada afetados por relações, mas ele faz mais do que o objetivo deste artigo.)

Então, como armazenar esses dados e onde?

Poderá ter experiência em bases de dados SQL ou ter uma noção de [modelagem de dados relacional](https://en.wikipedia.org/wiki/Relational_model). Pode começar a desenhar algo da seguinte forma:

![Diagrama que ilustra um modelo relacional relativo](./media/social-media-apps/social-media-apps-sql.png)

Uma perfeitamente normalizado e boa estrutura de dados... não pode ser escalonada.

Não me entenda mal, eu já trabalhou com bases de dados SQL, de todos os minha vida. São ótimos, mas, como cada plataforma padrão, prática e software, não é perfeito para cada cenário.

Por que não é SQL neste cenário, a melhor opção? Vamos examinar a estrutura de uma única mensagem. Se eu quisesse Mostrar a postagem num site ou aplicativo, eu teria de fazer uma consulta com... ao associar tables(!) oito apenas para mostrar um único post. Agora, Imagine um fluxo de mensagens que carregar e apresentada no ecrã dinamicamente e poderá ver onde vou.

Poderia usar uma instância SQL enorme com energia suficiente para resolver a milhares de consultas com muitas associações para servir o seu conteúdo. Mas por que faria isso, quando existe uma solução mais simples?

## <a name="the-nosql-road"></a>Ao longo do processo NoSQL

Este artigo orienta-o para a modelação de dados de sua plataforma de redes sociais com a base de dados do Azure NoSQL [do Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) económica. Ele também informa como usar outros recursos do Azure Cosmos DB, como o [API do Gremlin](../cosmos-db/graph-introduction.md). Utilizar um [NoSQL](https://en.wikipedia.org/wiki/NoSQL) abordagem, armazenamento de dados, no formato JSON e aplicando [desnormalização](https://en.wikipedia.org/wiki/Denormalization), a postagem complicada anteriormente pode ser transformada num único [documento](https://en.wikipedia.org/wiki/Document-oriented_database):

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

E pode ser obtido com uma única consulta e com nenhuma junções. Esta consulta é muito simples e direto, e, budget-wise, requer menos recursos para obter um resultado melhor.

O Azure Cosmos DB torna-se de que todas as propriedades são indexadas com indexação automática. A indexação automática pode até mesmo ser [personalizada](index-policy.md). A abordagem sem esquema nos permite armazenar documentos com estruturas diferentes e dinâmicas. Talvez amanhã queira postagens ter uma lista de categorias ou hashtags associadas a eles? O cosmos DB irá processar os novos documentos com os atributos adicionados sem exigir trabalho por nós.

Comentários numa postagem podem ser tratados como outras postagens com uma propriedade principal. (Esta prática simplifica o mapeamento de objeto.)

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

E todas as interações de redes sociais podem ser armazenadas num objeto separado, como contadores:

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

A criação de feeds é apenas uma questão de criação de documentos que podem conter uma lista de IDs de mensagem com uma ordem de relevância determinado:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Poderia ter um fluxo de "mais recente" com postagens ordenadas por data de criação. Ou poderia ter um fluxo "mais interessantes" com esses posts com gostos mais nas últimas 24 horas. Ainda pode implementar um fluxo personalizado para cada utilizador com base numa lógica como seguidores e interesses. Ainda seria uma lista de publicações. É uma questão de como criar estas listas, mas o desempenho de leitura permanece unhindered. Depois de adquirir uma destas listas, emite uma consulta única para o Cosmos DB utilizando a [no operador](how-to-sql-query.md#WhereClause) para obter páginas de postagens por vez.

Os fluxos de feeds podem ser criados usando [dos serviços de aplicações do Azure](https://azure.microsoft.com/services/app-service/) processos de fundo: [Webjobs](../app-service/webjobs-create.md). Quando é criada uma publicação, o processamento em segundo plano pode ser acionado, utilizando [armazenamento do Azure](https://azure.microsoft.com/services/storage/) [filas](../storage/queues/storage-dotnet-how-to-use-queues.md) e Webjobs acionados com o [Azure Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki), implementando o após a propagação dentro de fluxos com base na sua própria lógica personalizada.

Pontos e gostos ao longo de uma postagem podem ser processados de forma diferida usando essa mesma técnica para criar um ambiente eventualmente consistente.

Seguidores são mais complicados. O cosmos DB tem um limite de tamanho do documento e documentos grandes de leitura/gravação pode afetar a escalabilidade do seu aplicativo. Por isso, pode pensar a respeito armazenar seguidores como um documento com esta estrutura:

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

Essa estrutura pode funcionar para um utilizador com milhares de alguns seguidores. Se algum celebridade associa as classificações, no entanto, essa abordagem levará para um tamanho de documentos grandes e, eventualmente, ele poderá apresentar o limite de tamanho do documento.

Para resolver este problema, pode usar uma abordagem mista. Como parte do documento de estatísticas do utilizador, pode armazenar o número de seguidores:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

Pode armazenar o gráfico real de seguidores com o Azure Cosmos DB [API do Gremlin](../cosmos-db/graph-introduction.md) criem [vértices](http://mathworld.wolfram.com/GraphVertex.html) para cada utilizador e [margens](http://mathworld.wolfram.com/GraphEdge.html) que manter o "A-forma-B" relações. Com a API do Gremlin, pode obter seguidores de um determinado usuário e criar consultas mais complexas para sugerir as pessoas em comum. Se adicionar ao gráfico as categorias de conteúdo que as pessoas como ou aproveite, pode começar combinando as experiências que incluem a deteção de conteúdos inteligente, sugerindo a conteúdo que essas pessoas siga como ou a localizar as pessoas que pode ter muito common com.

O documento de estatísticas de utilizadores ainda pode ser utilizado para criar cartões na interface do Usuário ou pré-visualizações de perfil rápida.

## <a name="the-ladder-pattern-and-data-duplication"></a>A duplicação de dados e padrão de "Escada"

Como deve ter notado no documento JSON que faz referência a uma postagem, existem várias ocorrências de um utilizador. E deve ter adivinhado certo, que estes duplicados significam que as informações que descrevem um utilizador, tendo em conta esta desnormalização, poderão ser presentes em mais de um único lugar.

Para permitir consultas mais rápidas, pode incorrer duplicação de dados. O problema com este efeito é que, se por alguma ação, as alterações de dados de um usuário, precisa localizar todas as atividades o usuário nunca fez e atualizá-los todos os. Não parece prático, certo?

Vai para resolvê-lo ao identificar os principais atributos de um utilizador que mostram em seu aplicativo para cada atividade. Se visualmente mostra uma postagem em seu aplicativo e mostra do apenas o criador nome e imagem, por que armazenar todos os dados do utilizador no atributo "createdBy"? Se, para cada comentário que apenas mostrar a imagem do usuário, não precisa realmente o restante das informações do utilizador. Que é onde se envolve algo que chamo o padrão"escada".

Vamos dar informações de utilizador como um exemplo:

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

Ao examinar essas informações, pode detetar rapidamente o que é a informações críticas e que não estiver, criando assim uma "escada":

![Diagrama de um padrão de escala de bits](./media/social-media-apps/social-media-apps-ladder.png)

O passo de menor é chamado um UserChunk, a parte de um mínimo de informações que identificam um utilizador e é utilizado para duplicação de dados. Ao reduzir o tamanho de dados duplicados para apenas as informações que "Mostrar", reduzir a possibilidade de atualizações em massa.

A etapa intermediária denomina-se o utilizador. São os dados completos, que serão usados na maioria das consultas de dependente do desempenho no Cosmos DB, a mais acedidos e críticas. Ele inclui as informações representadas por um UserChunk.

O maior é o utilizador expandido. Ele inclui as informações de utilizadores críticos e outros dados que não precisam ser lida rapidamente ou tem eventual utilização, como o processo de início de sessão. Estes dados podem ser armazenados fora do Cosmos DB, na base de dados do Azure SQL ou tabelas de armazenamento do Azure.

Por que seria é dividir o usuário e até mesmo armazenar essas informações em locais diferentes? Uma vez que um ponto de vista do desempenho, quanto maiores os documentos, o costlier das consultas. Manter os documentos reduzido, com as informações certas para fazer todas as suas consultas de dependente de desempenho para a sua rede social. Store outras informações extras para cenários eventual como as edições de perfil completo, inícios de sessão e extração de dados para análise de utilização e iniciativas de grandes volumes de dados. Realmente não importa se a recolha de dados para extração de dados são mais lentos, uma vez que está a ser executado na base de dados do Azure SQL. Tem se entretanto que os utilizadores têm uma experiência rápida e reduzida. Um utilizador armazenado no Cosmos DB teria o aspeto semelhante a este código:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"\@john"
    }

E uma postagem teria o seguinte aspeto:

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

Quando uma edição surge em que um atributo de segmentos é afetado, pode encontrar facilmente os documentos afetados. Basta usar consultas que apontam para os atributos indexados, tais como `SELECT * FROM posts p WHERE p.createdBy.id == "edited_user_id"`e, em seguida, atualize os segmentos.

## <a name="the-search-box"></a>Caixa de pesquisa

Os utilizadores irão gerar, Felizmente, há conteúdo. E deve ser capaz de fornecer a capacidade de pesquisar e localizar conteúdo que poderá não estar diretamente em seus fluxos de conteúdo, talvez porque não seguir os criadores ou, talvez está tentando encontrar que post antigo fez seis meses atrás.

Uma vez que estiver a utilizar o Azure Cosmos DB, pode implementar facilmente um mecanismo de pesquisa com [Azure Search](https://azure.microsoft.com/services/search/) em poucos minutos sem escrever qualquer código, que não seja o processo de pesquisa e a interface do Usuário.

Por que esse processo é tão fácil?

O Azure Search implementa o que eles chamam [indexadores](https://msdn.microsoft.com/library/azure/dn946891.aspx), em segundo plano processa esse gancho em seus repositórios de dados e automagicamente adicionar, atualizar ou remover os objetos nos índices. Eles oferecem suporte a uma [indexadores do Azure SQL Database](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), [indexadores de Blobs do Azure](../search/search-howto-indexing-azure-blob-storage.md) e, Felizmente, [indexadores do Azure Cosmos DB](../search/search-howto-index-documentdb.md). A transição de informações do Cosmos DB para o Azure Search é muito simples. As duas tecnologias armazenam informações no formato JSON, para que só precisa [criar o seu índice](../search/search-create-index-portal.md) e mapear os atributos dos seus documentos desejar indexada. Já está! Dependendo do tamanho dos seus dados, todos os seus conteúdos estará disponível para ser pesquisada após dentro de minutos, a melhor solução de pesquisa-como-serviço na infraestrutura de nuvem.

Para obter mais informações sobre o Azure Search, pode visitar o [guia para pesquisa](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/).

## <a name="the-underlying-knowledge"></a>O conhecimento subjacente

Depois de armazenar esse conteúdo que aumenta e que cresce todos os dias, pode achar pensamento: O que posso fazer com todos esse fluxo de informações dos meus utilizadores?

A resposta é simples: Colocá-lo a funcionar e aprender com ele.

Mas o que aprender? Alguns exemplos de fácil incluem [a análise de sentimentos](https://en.wikipedia.org/wiki/Sentiment_analysis), o conteúdo recomendações com base nas preferências de um utilizador, ou até mesmo um automatizada o content moderator que certifica-se de que o conteúdo publicado pela rede social é seguro para a família.

Agora que tenho é conectado, provavelmente o achará terá algumas doutorado em ciência matemáticas para extrair estes padrões e informações das bases de dados simples e de ficheiros, mas não seria certo.

[O Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), que faz parte do [Cortana Intelligence Suite](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx), é um serviço cloud totalmente gerido que permite-lhe criar fluxos de trabalho usando algoritmos numa interface de arrastar e largar simple, código de seus próprios algoritmos no [ R](https://en.wikipedia.org/wiki/R_\(programming_language\)), ou usar alguns dos já incorporado e pronto a utilizar APIs como: [Análise de texto](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [do Content Moderator, ou [recomendações](https://gallery.azure.ai/Solution/Recommendations-Solution).

Para obter qualquer um desses cenários de Machine Learning, pode usar [do Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) ingerir as informações de diferentes origens. Também pode utilizar [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) para processar as informações e gerar uma saída que pode ser processada por Azure Machine Learning.

Outra opção disponível é usar [serviços cognitivos do Azure](https://www.microsoft.com/cognitive-services) para analisar os seus utilizadores conteúdos; não só pode compreendê-los melhor (através de análise que escrevem com [API de análise de texto](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), mas Também pode detetar conteúdo indesejado ou maduro e aja em conformidade com [API de imagem digitalizada](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Os serviços cognitivos inclui muitas soluções de out-of-the-box que não necessitam de qualquer tipo de dados de conhecimento do Machine Learning a utilizar.

## <a name="a-planet-scale-social-experience"></a>Uma experiência de redes sociais de escala planetária

Existe um último, mas não menos importante, artigo importante devem ser resolvidos: **escalabilidade**. Ao conceber uma arquitetura, cada componente deve aumentar por conta própria. Eventualmente, terá de processar os dados mais ou, deverá ter uma maior cobertura geográfica. Felizmente, a obtenção de ambas as tarefas é uma **experiência de chave na mão** com o Cosmos DB.

O cosmos DB suporta dinâmica particionamento-de-pronta. Ele automaticamente cria partições com base num determinado **chave de partição**, que é definida como um atributo em seus documentos. Definir a chave de partição correta deve ser feita em tempo de design. Para obter mais informações, consulte [criação de partições no Azure Cosmos DB](partitioning-overview.md).

Para uma experiência de redes sociais, devem estar alinhadas sua estratégia de criação de partições com a forma de consulta e escrita. (Por exemplo, leituras dentro da mesma partição desejáveis e evitar "pontos de acesso" ao propagar escritas em várias partições.) Algumas opções são: partições com base numa chave temporal (dia/mês/semana), por categoria de conteúdo, por região geográfica ou por utilizador. Tudo isso realmente depende de como consultar os dados e mostrar os dados na sua experiência de redes sociais.

O cosmos DB irá executar as suas consultas (incluindo [agregações](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)) em todas as suas partições de forma transparente, por isso, não precisa de adicionar qualquer lógica à medida que aumenta a seus dados.

Com o tempo, eventualmente crescerá no tráfego e o consumo de recursos (medido em [RUs](request-units.md), ou unidades de pedido) irá aumentar. Irá ler e escrever mais frequentemente à medida que aumenta a sua base de usuários. A base de usuários irá começar a criar e ler mais conteúdo. Portanto, a capacidade de **dimensionar o débito** é vital. Aumentar os RUs é fácil. Pode fazer isso com alguns cliques no portal do Azure ou pelo [emissão de comandos por meio da API](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer).

![Aumentar verticalmente e definir uma chave de partição](./media/social-media-apps/social-media-apps-scaling.png)

O que acontece se as coisas estão a melhorar? Suponha que os utilizadores de outra região, país ou continente tenha em atenção a plataforma e começar a utilizar. Que surpresa excelente!

Mas espere! Logo perceberá a que sua experiência com a plataforma não é ideal. Eles são até agora para fora da sua região operacional que a latência é terrível. É claro que não pretende que sair. Se houvesse uma forma fácil de **estender o alcance global**? Há!

O cosmos DB permite-lhe [replicar globalmente os seus dados](../cosmos-db/tutorial-global-distribution-sql-api.md) e de forma transparente em apenas alguns cliques e automaticamente, selecione entre as regiões disponíveis a partir do seu [código de cliente](../cosmos-db/tutorial-global-distribution-sql-api.md). Este processo também significa que pode ter [várias regiões de ativação pós-falha](high-availability.md).

Quando replica globalmente os seus dados, terá de certificar-se de que os clientes podem se aproveitar dele. Se estiver a utilizar um front-end da web ou a aceder a APIs de clientes móveis, pode implantar [Gestor de tráfego do Azure](https://azure.microsoft.com/services/traffic-manager/) e clone o App Service do Azure em todas as regiões desejadas, através de uma configuração de desempenho para suportar o expandida global cobertura. Quando os clientes acessam o front-end ou APIs, vai ser encaminhados para o serviço de aplicações mais próximo, que por sua vez, irá ligar para a réplica local do Cosmos DB.

![Adicionar cobertura global na sua plataforma de redes sociais](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>Conclusão

Este artigo esclarece sobre as alternativas de criação de redes sociais completamente no Azure com os serviços de baixo custo. ele entrega resultados por incentivando a utilização de uma distribuição de dados e solução de armazenamento em várias camadas chamada "Escada".

![Diagrama de interação entre os serviços do Azure para redes sociais](./media/social-media-apps/social-media-apps-azure-solution.png)

A verdade é que não existe nenhuma solução mágica para este tipo de cenários. Ele está sinergia criada pela combinação de serviços excelentes que permitem-nos criar excelentes experiências: a velocidade e a liberdade do Azure Cosmos DB para fornecer um ótimo aplicativo social, a inteligência por trás de uma solução de pesquisa de primeira classe, como o Azure Search, o flexibilidade de serviços de aplicações do Azure para alojar aplicações não até mesmo idioma agnóstico mas processos em segundo plano poderosas e o expansível armazenamento do Azure e SQL Database do Azure para armazenar grandes quantidades de dados e a capacidade de análise do Azure Machine Learning para criar dados de conhecimento e inteligência que pode fornecer seus comentários para seus processos e ajude-nos fornece o conteúdo certo para os utilizadores certos.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre casos de utilização para o Cosmos DB, veja [casos de utilização comuns do Cosmos DB](use-cases.md).
