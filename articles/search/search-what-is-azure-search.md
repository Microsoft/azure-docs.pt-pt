---
title: Introdução ao Azure Cognitive Search
titleSuffix: Azure Cognitive Search
description: A azure Cognitive Search é um serviço de pesquisa em nuvem totalmente gerido da Microsoft. Saiba mais sobre casos de utilização, o fluxo de trabalho de desenvolvimento, comparações com outros produtos de pesquisa da Microsoft e como começar.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 03/09/2021
ms.custom: contperf-fy21q1
ms.openlocfilehash: e17d08d09814c135af3e0b4fc299b6e6f42326d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549894"
---
# <a name="what-is-azure-cognitive-search"></a>O que é o Azure Cognitive Search?

Azure Cognitive Search[é](whats-new.md#new-service-name)um serviço de pesquisa em nuvem que fornece apis e ferramentas para construir uma rica experiência de pesquisa sobre conteúdo privado, heterogéneo em aplicações web, móveis e empresariais. 

A pesquisa é fundamental para qualquer app que suria conteúdo para os utilizadores, com cenários comuns, incluindo pesquisa de catálogo ou documento, pesquisa de sites de e-commerce ou mineração de conhecimento para a ciência dos dados. As APIs e a arquitetura da Pesquisa Cognitiva simplificam a tarefa de adicionar uma sofisticada recuperação de informação a qualquer solução.

Um serviço de pesquisa tem os seguintes componentes:

+ Pesquisar o motor para pesquisa completa de texto
+ Armazenamento persistente de conteúdo indexado de propriedade do utilizador
+ APIs para indexação e consulta de conteúdos
+ [Enriquecimentos opcionais baseados em IA,](cognitive-search-concept-intro.md)criando conteúdo pesmável a partir de imagens, texto cru e não estruturado, ficheiros de aplicações
+ Integração opcional com outros serviços Azure para dados, machine learning/IA, monitorização e segurança
+ Implementação opcional de [pesquisa semântica (pré-visualização)](semantic-search-overview.md) para uma maior relevância

Em termos arquitetónicos, um serviço de pesquisa situa-se entre as lojas de dados externos que contêm os seus dados não indexados, e a sua aplicação de cliente que envia pedidos de consulta para um índice de pesquisa e trata a resposta.

![Arquitetura de Pesquisa Cognitiva Azure](media/search-what-is-azure-search/azure-search-diagram.svg "Arquitetura de Pesquisa Cognitiva Azure")

Externamente, a pesquisa pode integrar-se com outros serviços Azure sob a forma de *indexantes* que automatizam a ingestão/recuperação de dados a partir de fontes de dados Azure, e *skillsets* que incorporam IA consumível dos Serviços Cognitivos, tais como análise de imagem e texto, ou IA personalizada que você cria em Azure Machine Learning ou embrulhe dentro de Funções Azure.

## <a name="inside-a-search-service"></a>Dentro de um serviço de pesquisa

No próprio serviço de pesquisa, as duas cargas de trabalho primárias estão *a indexar* e *a consultar.* 

+ [Indexar](search-what-is-an-index.md) é um processo de admissão que carrega o conteúdo para o seu serviço de pesquisa e o torna pes pespável. Internamente, o texto de entrada é processado em fichas e armazenado em índices invertidos para digitalizações rápidas. Pode fazer o upload de qualquer texto que esteja na forma de documentos JSON.

  Além disso, se o seu conteúdo inclui ficheiros mistos, tem a opção de adicionar *enriquecimento* de IA através de [habilidades cognitivas.](cognitive-search-working-with-skillsets.md) O enriquecimento de IA pode extrair texto incorporado em ficheiros de aplicações, e também inferir texto e estrutura a partir de ficheiros não-texto, analisando o conteúdo. 

  As competências que fornecem a análise são predefinidas da Microsoft, ou habilidades personalizadas que cria. As análises e transformações subsequentes podem resultar em novas informações e estruturas que anteriormente não existiam, proporcionando alta utilidade para muitos cenários de pesquisa e mineração de conhecimento.

+ [A consulta](search-query-overview.md) pode ocorrer uma vez que um índice é preenchido com texto pesmável, quando a sua aplicação do cliente envia pedidos de consulta para um serviço de pesquisa e lida com respostas. Toda a execução de consultas é sobre um índice de pesquisa que você cria, possui e armazena no seu serviço. Na sua aplicação cliente, a experiência de pesquisa é definida usando APIs da Azure Cognitive Search, e pode incluir afinação de relevância, afinação automática, correspondência de sinónimo, correspondência fuzzy, correspondência de padrões, filtro e classificação.

A funcionalidade é exposta através de uma simples [REST API](/rest/api/searchservice/) ou [.NET SDK](search-howto-dotnet-sdk.md) que dissimula a complexidade inerente da obtenção de informações. Também pode utilizar o portal Azure para a administração de serviços e gestão de conteúdos, com ferramentas para prototipagem e consulta dos seus índices e skillsets. Por o serviço executar na cloud, as infraestruturas e disponibilidade são geridas pela Microsoft.

## <a name="why-use-cognitive-search"></a>Porquê usar a Pesquisa Cognitiva

A Azure Cognitive Search é adequada para os seguintes cenários de aplicação:

+ Consolidar o conteúdo heterogéneo num índice de pesquisa definido pelo utilizador.

+ Implementar facilmente funcionalidades relacionadas com a pesquisa: afinação de relevância, navegação frontal, filtros (incluindo pesquisa geo-espacial), mapeamento de sinónimo e preconização automática.

+ Transforme grandes ficheiros de texto ou imagem indiferenciados, ou ficheiros de aplicação armazenados no armazenamento de Azure Blob ou Na Cosmos DB, em documentos JSON pesjáveis. Isto é conseguido durante o índice através de [habilidades cognitivas](cognitive-search-concept-intro.md) que adicionam processamento externo.

+ Adicione análise de texto linguística ou personalizada. Se tiver conteúdo não inglês, o Azure Cognitive Search suporta tanto os analisadores de Lucene como os processadores de linguagem natural da Microsoft. Também pode configurar analisadores para obter o processamento especializado de conteúdo bruto, como filtrar diacríticos, ou reconhecer e preservar padrões em cordas.

Para obter mais informações sobre funcionalidades específicas, consulte [funcionalidades de Pesquisa Cognitiva Azure](search-features-list.md)

## <a name="how-to-get-started"></a>Como começar

Uma exploração de ponta a ponta das funcionalidades de pesquisa do núcleo pode ser alcançada em quatro etapas:

1. [**Crie um serviço de pesquisa**](search-create-service-portal.md) no nível gratuito partilhado ou num [nível faturado](https://azure.microsoft.com/pricing/details/search/) para recursos dedicados utilizados apenas pelo seu serviço. Todos os quickstarts e tutoriais podem ser concluídos num serviço partilhado.

1. [**Crie um índice de pesquisa**](search-what-is-an-index.md) utilizando o portal REST [API](/rest/api/searchservice/create-index), [.NET SDK,](search-howto-dotnet-sdk.md)ou outro SDK. O esquema de índice define a estrutura do conteúdo pesmável.

1. [**Faça upload de conteúdo**](search-what-is-data-import.md) usando o [modelo "push"](tutorial-optimize-indexing-push-api.md) para empurrar documentos JSON de qualquer fonte, ou use o [modelo "pull" (indexantes)](search-indexer-overview.md) se os seus dados de origem estiverem no Azure.

1. [**Consultar um índice**](search-query-overview.md) utilizando o [Explorador de Pesquisa](search-explorer.md) no portal, REST [API](search-get-started-rest.md), [.NET SDK,](/dotnet/api/azure.search.documents.searchclient.search)ou outro SDK.

Para a exploração inicial, comece com o [**assistente de dados de Importação**](search-get-started-portal.md) e uma fonte de dados Azure incorporada para criar, carregar e consultar um índice em minutos.

Para obter ajuda com soluções complexas ou personalizadas, [**contacte um parceiro**](resource-partners-knowledge-mining.md) com uma vasta experiência em tecnologia de Pesquisa Cognitiva.

## <a name="compare-search-options"></a>Comparar opções de pesquisa

Os clientes perguntam frequentemente como a Azure Cognitive Search se compara com outras soluções relacionadas com a pesquisa. A tabela seguinte resume as diferenças principais.

| Em comparação com | Diferenças principais |
|-------------|-----------------|
| Microsoft Search | [O Microsoft Search](/microsoftsearch/overview-microsoft-search) é para utilizadores autenticados microsoft 365 que precisam de consultar os conteúdos no SharePoint. É oferecido como uma experiência de pesquisa pronta a usar, ativada e configurada por administradores, com a capacidade de aceitar conteúdo externo através de conectores da Microsoft e outras fontes. Se isto descreve o seu cenário, então o Microsoft Search com o Microsoft 365 é uma opção atraente para explorar.<br/><br/>Em contraste, a Azure Cognitive Search executa consultas sobre um índice que define, povoado com dados e documentos que possui, muitas vezes de diversas fontes. A Azure Cognitive Search tem capacidades de rastreio para algumas fontes de dados Azure através de [indexadores](search-indexer-overview.md), mas você pode empurrar qualquer documento JSON que esteja em conformidade com o seu esquema de índice em um único recurso pesquisalável consolidado. Também pode personalizar o pipeline de indexação para incluir machine learning e analisadores lexical. Como a Cognitive Search é construída para ser um componente plug-in em soluções maiores, pode integrar a pesquisa em quase todas as aplicações, em qualquer plataforma.|
|Bing | A [API de Pesquisa na Web do Bing](../cognitive-services/bing-web-search/index.yml) procura os índices em Bing.com para os termos correspondentes ao que enviar. Os índices são criados a partir de HTML, XML e outros conteúdos web em sites públicos. Criada com base nos mesmos fundamentos, a [Pesquisa Personalizada do Bing](/azure/cognitive-services/bing-custom-search/) oferece a mesma tecnologia crawler para tipos de conteúdo Web, no âmbito de sites individuais.<br/><br/>Na Pesquisa Cognitiva, pode definir e povoar o índice. Pode utilizar [indexadores](search-indexer-overview.md) para rastrear dados em fontes de dados do Azure ou empurrar qualquer documento JSON em conformidade com o índice para o seu serviço de pesquisa. |
|Pesquisa de base de dados | Muitas plataformas da base de dados incluem uma experiência de pesquisa incorporada. O SQL Server tem [pesquisa em texto completo](/sql/relational-databases/search/full-text-search). O Cosmos DB e as tecnologias semelhantes têm índices consultáveis. Quando avaliar os produtos que combinam a pesquisa e o armazenamento, pode ser difícil determinar qual escolher. Muitas soluções utilizam ambas: DBMS para armazenamento, e Azure Cognitive Search para funcionalidades de pesquisa especializadas.<br/><br/>Em comparação com a pesquisa DBMS, a Azure Cognitive Search armazena conteúdos de fontes heterogéneas e oferece funcionalidades especializadas de processamento de texto, tais como processamento de textos conscientes da linguística (stemming, lematização, formas de [palavras) em 56 idiomas.](/rest/api/searchservice/language-support) Também suporta a correção automática de palavras com erros ortográficos, [sinónimos](/rest/api/searchservice/synonym-map-operations), [sugestões](/rest/api/searchservice/suggestions), [controlos de classificação](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [facetas](./search-filters-facets.md)e [atomização personalizada](/rest/api/searchservice/custom-analyzers-in-azure-search). O [motor de pesquisa de texto completo](search-lucene-query-architecture.md) em Azure Cognitive Search é construído em Apache Lucene, um padrão da indústria na recuperação de informação. No entanto, embora a Azure Cognitive Search persista em dados sob a forma de um índice invertido, não é um substituto para o verdadeiro armazenamento de dados e não recomendamos a sua utilização nessa capacidade. Para obter mais informações, veja esta [mensagem no fórum](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>A utilização de recursos é outro ponto de flexão nesta categoria. A indexação e algumas operações de consulta, muitas vezes, são intensivas em termos de computação. O descarregamento da pesquisa do DBMS para uma solução dedicada na cloud preserva os recursos do sistema para o processamento de transações. Além disso, ao externalizar a pesquisa, pode facilmente ajustar o dimensionamento para corresponder ao volume de consultas.|
|Solução de pesquisa dedicada | Partindo do princípio de que escolheu a pesquisa dedicada com funcionalidade de espetro completo, a comparação categórica final será entre as soluções no local ou um serviço cloud. Muitas tecnologias de pesquisa oferecem controlo sobre os pipelines de indexação e consulta, acesso a uma sintaxe de consulta e filtragem mais rica, controlo sobre a classificação e relevância e funcionalidades para uma pesquisa inteligente e auto-direcionada. <br/><br/>Um serviço cloud é a escolha certa se quiser uma solução imediata com o mínimo de custos gerais e de manutenção e com um dimensionamento ajustável. <br/><br/>Dentro do paradigma da cloud, vários fornecedores oferecem funcionalidades de linha de base comparáveis, com pesquisa de texto inteiro, pesquisa geográfica e a capacidade de processar um determinado nível de ambiguidade nas entradas de pesquisa. Normalmente, é uma [funcionalidade especializada](search-features-list.md) ou a facilidade e simplicidade geral das APIs, das ferramentas e a gestão que determinam qual a melhor opção. |

Entre os fornecedores de nuvem, o Azure Cognitive Search é o mais forte para a pesquisa completa de textos sobre lojas de conteúdos e bases de dados no Azure, para apps que dependem principalmente da pesquisa tanto para a recuperação de informações como para a navegação de conteúdos. 

As principais vantagens incluem:

+ Integração de dados do Azure (crawlers) na camada de indexação
+ Integração de Azure Private Link para apoiar requisitos de segurança off-internet
+ Integração com o processamento de IA para tornar os tipos de conteúdo insuclesiáveis por texto.
+ A análise linguística e personalizada, com analisadores de pesquisa de texto completo sólidos em 56 idiomas
+ [Características críticas](search-features-list.md): linguagem de consulta rica, afinação de relevância, frontalização, autocompleto, sinónimos, geo-pesquisa e composição de resultados.
+ Dimensionamento, fiabilidade e disponibilidade de classe mundial do Azure

Entre os nossos clientes, aqueles que são capazes de aproveitar a mais ampla gama de funcionalidades na Azure Cognitive Search incluem catálogos online, programas de linha de negócios e aplicações de descoberta de documentos.

## <a name="watch-this-video"></a>Assistir a este vídeo

Neste vídeo de 15 minutos, o gestor de programas Luis Cabrera apresenta a Azure Cognitive Search.

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]