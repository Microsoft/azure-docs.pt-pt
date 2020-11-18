---
title: Introdução ao Azure Cognitive Search
titleSuffix: Azure Cognitive Search
description: A azure Cognitive Search é um serviço de pesquisa em nuvem totalmente gerido da Microsoft. Saiba mais sobre casos de utilização, o fluxo de trabalho de desenvolvimento, comparações com outros produtos de pesquisa da Microsoft e como começar.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 10/22/2020
ms.custom: contperfq1
ms.openlocfilehash: 0062caff7d8d25b263a9b1202f61691c056469af
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701087"
---
# <a name="what-is-azure-cognitive-search"></a>O que é o Azure Cognitive Search?

Azure Cognitive Search[é](whats-new.md#new-service-name)um serviço de pesquisa em nuvem que fornece apis e ferramentas para construir uma rica experiência de pesquisa sobre conteúdo privado, heterogéneo em aplicações web, móveis e empresariais.

Quando cria um serviço de Pesquisa Cognitiva, obtém-se um motor de busca que executa a execução de indexação e consulta, armazenamento persistente de índices que cria e gere, e uma linguagem de consulta para compor consultas simples a complexas. Opcionalmente, um serviço de pesquisa integra-se com outros serviços Azure sob a forma de *indexantes* que automatizam a ingestão/recuperação de dados a partir de fontes de dados Azure, e *skillsets* que incorporam IA consumível dos Serviços Cognitivos, tais como análise de imagem e texto, ou IA personalizada que cria em Azure Machine Learning ou embrulhe dentro de Funções Azure.

![Arquitetura de Pesquisa Cognitiva Azure](media/search-what-is-azure-search/azure-search-diagram.svg "Arquitetura de Pesquisa Cognitiva Azure")

Em termos arquitetónicos, um serviço de pesquisa situa-se entre as lojas de dados externos que contêm os seus dados não indexados, e uma aplicação de cliente que envia pedidos de consulta para um índice de pesquisa e trata a resposta.  Um esquema de índice determina a estrutura do conteúdo pesmável. 

As duas cargas de trabalho primárias de um serviço de pesquisa estão *a indexar* e *a consultar.*

+ A indexação traz texto para o seu serviço de pesquisa e torna-o pesmável. Internamente, o texto de entrada é processado em fichas e armazenado em índices invertidos para digitalizações rápidas. Durante a indexação, você tem a opção de adicionar *habilidades cognitivas*, ou pré-indefinidas da Microsoft ou habilidades personalizadas que você cria. As análises e transformações subsequentes podem resultar em novas informações e estruturas que anteriormente não existiam, proporcionando alta utilidade para muitos cenários de pesquisa e mineração de conhecimento.

+ Uma vez que um índice é preenchido com dados pes pesjáveis, a sua aplicação de cliente envia pedidos de consulta para um serviço de pesquisa e lida com respostas. Toda a execução de consultas é sobre um índice de pesquisa que você cria, possui e armazena no seu serviço. Na sua aplicação cliente, a experiência de pesquisa é definida usando APIs da Azure Cognitive Search, e pode incluir afinação de relevância, afinação automática, correspondência de sinónimo, correspondência fuzzy, correspondência de padrões, filtro e classificação.

A funcionalidade é exposta através de uma simples [REST API](/rest/api/searchservice/) ou [.NET SDK](search-howto-dotnet-sdk.md) que dissimula a complexidade inerente da obtenção de informações. Também pode utilizar o portal Azure para a administração de serviços e gestão de conteúdos, com ferramentas para prototipagem e consulta dos seus índices e skillsets. Por o serviço executar na cloud, as infraestruturas e disponibilidade são geridas pela Microsoft.

## <a name="when-to-use-cognitive-search"></a>Quando utilizar a Pesquisa Cognitiva

A Azure Cognitive Search é adequada para os seguintes cenários de aplicação:

+ Consolidação de tipos de conteúdo heterogéneo num índice de pesquisa privado e definido pelo utilizador. Pode preencher um índice de pesquisa com fluxos de documentos JSON de qualquer fonte. Para obter fontes apoiadas no Azure, utilize um *indexante* para automatizar a indexação. O controlo sobre o esquema de índice e o calendário de atualização é uma razão fundamental para a utilização da Pesquisa Cognitiva.

+ Fácil implementação de funcionalidades relacionadas com a pesquisa. As APIs de pesquisa simplificam a construção de consultas, a navegação frontal, os filtros (incluindo a pesquisa geo-espacial), o mapeamento de sinónimos, o preâmlo e a sintonização de relevância. Utilizando funcionalidades incorporadas, pode satisfazer as expectativas dos utilizadores finais para uma experiência de pesquisa semelhante a motores de pesquisa web comerciais.

+ O conteúdo bruto é um grande ficheiro de texto ou imagem indiferenciado ou ficheiros de aplicações armazenados no armazenamento de Azure Blob ou na Cosmos DB. Pode aplicar [competências cognitivas](cognitive-search-concept-intro.md) durante a indexação para identificar e extrair texto, criar estrutura ou criar novas informações, como texto traduzido ou entidades.

+ O conteúdo necessita de análise de texto linguística ou personalizada. Se tiver conteúdo não inglês, o Azure Cognitive Search suporta tanto os analisadores de Lucene como os processadores de linguagem natural da Microsoft. Também pode configurar analisadores para obter o processamento especializado de conteúdo bruto, como filtrar diacríticos, ou reconhecer e preservar padrões em cordas.

Para obter mais informações sobre funcionalidades específicas, consulte [funcionalidades de Pesquisa Cognitiva Azure](search-features-list.md)

## <a name="how-to-use-cognitive-search"></a>Como utilizar a Pesquisa Cognitiva

### <a name="step-1-provision-service"></a>Passo 1: Aprovisionar o serviço

Pode [criar um serviço gratuito](search-create-service-portal.md) partilhado com outros subscritores, ou um [nível pago](https://azure.microsoft.com/pricing/details/search/) que dedica recursos utilizados apenas pelo seu serviço. Todos os inícios rápidos e tutoriais podem ser concluídos no serviço gratuito.

Para os níveis pagos, pode escalar um serviço em duas dimensões para calibrar o reabastecimento com base nos requisitos de produção:

+ Adicione réplicas para aumentar a sua capacidade de lidar com cargas de consulta pesadas
+ Adicione divisórias para aumentar o armazenamento para mais documentos

### <a name="step-2-create-an-index"></a>Passo 2: criar um índice

Defina um esquema de índice para mapear para refletir a estrutura dos documentos que pretende pesquisar, semelhantes aos campos numa base de dados. Um índice de pesquisa é uma estrutura de dados especializada que é otimizada para a execução rápida de consultas.

É comum [criar o esquema de índice no portal Azure,](search-what-is-an-index.md)ou utilizar programáticamente a [API](/rest/api/searchservice/) [.NET SDK](search-howto-dotnet-sdk.md) ou REST .

> [!TIP]
> Comece com o [Quickstart: Importe o assistente de dados](search-get-started-portal.md) para criar, carregar e consultar um índice em minutos.

### <a name="step-3-load-data"></a>Passo 3: Carregar dados

Depois de definir um índice, está pronto para carregar conteúdo. Pode utilizar um modelo push ou pull.

O modelo push "empurra" os documentos JSON para um índice utilizando APIs de um [SDK](search-howto-dotnet-sdk.md) ou [REST](/rest/api/searchservice/addupdate-or-delete-documents). O conjunto de dados externo pode ser praticamente qualquer fonte de dados, desde que os documentos sejam JSON.

O modelo de puxar "retira" dados de fontes em Azure e envia-os para um índice de pesquisa. O modelo de puxar é implementado através [*de indexadores*](/rest/api/searchservice/Indexer-operations) que simplificam e automatizam aspetos da ingestão de dados, tais como a ligação, leitura e serialização de dados. As fontes de dados suportadas incluem Azure Cosmos DB, Azure SQL e Azure Storage.

### <a name="step-4-send-queries-and-handle-responses"></a>Passo 4: Enviar consultas e lidar com respostas

Depois de povoar um índice, pode [emitir consultas de pesquisa](search-query-overview.md) no seu ponto final de serviço utilizando pedidos HTTP simples com [API REST](/rest/api/searchservice/Search-Documents) ou o [.NET SDK](/dotnet/api/azure.search.documents.searchclient.search).

Passe por [Criar a sua primeira aplicação de pesquisa](tutorial-csharp-create-first-app.md) para construir e, em seguida, estender uma página web que recolhe a entrada do utilizador e lida com os resultados. Também pode utilizar [o Código do Carteiro ou do Estúdio Visual](search-get-started-rest.md)  para chamadas interativas REST ou o Explorador de [Pesquisa](search-explorer.md) incorporado no portal Azure para consultar um índice existente.

## <a name="how-it-compares"></a>Como se compara

Os clientes perguntam frequentemente como a Azure Cognitive Search se compara com outras soluções relacionadas com a pesquisa. A tabela seguinte resume as diferenças principais.

| Em comparação com | Diferenças principais |
|-------------|-----------------|
| Microsoft Search | [O Microsoft Search](https://docs.microsoft.com/microsoftsearch/overview-microsoft-search) é para utilizadores autenticados microsoft 365 que precisam de consultar os conteúdos no SharePoint. É oferecido como uma experiência de pesquisa pronta a usar, ativada e configurada por administradores, com a capacidade de aceitar conteúdo externo através de conectores da Microsoft e outras fontes. Se isto descreve o seu cenário, então o Microsoft Search com o Microsoft 365 é uma opção atraente para explorar.<br/><br/>Em contraste, a Azure Cognitive Search executa consultas sobre um índice que define, povoado com dados e documentos que possui, muitas vezes de diversas fontes. A Azure Cognitive Search tem capacidades de rastreio para algumas fontes de dados Azure através de [indexadores](search-indexer-overview.md), mas você pode empurrar qualquer documento JSON que esteja em conformidade com o seu esquema de índice em um único recurso pesquisalável consolidado. Também pode personalizar o pipeline de indexação para incluir machine learning e analisadores lexical. Como a Cognitive Search é construída para ser um componente plug-in em soluções maiores, pode integrar a pesquisa em quase todas as aplicações, em qualquer plataforma.|
|Bing | A [API de Pesquisa na Web do Bing](../cognitive-services/bing-web-search/index.yml) procura os índices em Bing.com para os termos correspondentes ao que enviar. Os índices são criados a partir de HTML, XML e outros conteúdos web em sites públicos. Criada com base nos mesmos fundamentos, a [Pesquisa Personalizada do Bing](/azure/cognitive-services/bing-custom-search/) oferece a mesma tecnologia crawler para tipos de conteúdo Web, no âmbito de sites individuais.<br/><br/>Na Pesquisa Cognitiva, pode definir e povoar o índice. Pode utilizar [indexadores](search-indexer-overview.md) para rastrear dados em fontes de dados do Azure ou empurrar qualquer documento JSON em conformidade com o índice para o seu serviço de pesquisa. |
|Pesquisa de base de dados | Muitas plataformas da base de dados incluem uma experiência de pesquisa incorporada. O SQL Server tem [pesquisa em texto completo](/sql/relational-databases/search/full-text-search). O Cosmos DB e as tecnologias semelhantes têm índices consultáveis. Quando avaliar os produtos que combinam a pesquisa e o armazenamento, pode ser difícil determinar qual escolher. Muitas soluções utilizam ambas: DBMS para armazenamento, e Azure Cognitive Search para funcionalidades de pesquisa especializadas.<br/><br/>Em comparação com a pesquisa DBMS, a Azure Cognitive Search armazena conteúdos de fontes heterogéneas e oferece funcionalidades especializadas de processamento de texto, tais como processamento de textos conscientes da linguística (stemming, lematização, formas de [palavras) em 56 idiomas.](/rest/api/searchservice/language-support) Também suporta a correção automática de palavras com erros ortográficos, [sinónimos](/rest/api/searchservice/synonym-map-operations), [sugestões](/rest/api/searchservice/suggestions), [controlos de classificação](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [facetas](./search-filters-facets.md)e [atomização personalizada](/rest/api/searchservice/custom-analyzers-in-azure-search). O [motor de pesquisa de texto completo](search-lucene-query-architecture.md) em Azure Cognitive Search é construído em Apache Lucene, um padrão da indústria na recuperação de informação. No entanto, embora a Azure Cognitive Search persista em dados sob a forma de um índice invertido, não é um substituto para o verdadeiro armazenamento de dados e não recomendamos a sua utilização nessa capacidade. Para obter mais informações, veja esta [mensagem no fórum](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>A utilização de recursos é outro ponto de flexão nesta categoria. A indexação e algumas operações de consulta, muitas vezes, são intensivas em termos de computação. O descarregamento da pesquisa do DBMS para uma solução dedicada na cloud preserva os recursos do sistema para o processamento de transações. Além disso, ao externalizar a pesquisa, pode facilmente ajustar o dimensionamento para corresponder ao volume de consultas.|
|Solução de pesquisa dedicada | Partindo do princípio de que escolheu a pesquisa dedicada com funcionalidade de espetro completo, a comparação categórica final será entre as soluções no local ou um serviço cloud. Muitas tecnologias de pesquisa oferecem controlo sobre os pipelines de indexação e consulta, acesso a uma sintaxe de consulta e filtragem mais rica, controlo sobre a classificação e relevância e funcionalidades para uma pesquisa inteligente e auto-direcionada. <br/><br/>Um serviço cloud é a escolha certa se quiser uma solução imediata com o mínimo de custos gerais e de manutenção e com um dimensionamento ajustável. <br/><br/>Dentro do paradigma da cloud, vários fornecedores oferecem funcionalidades de linha de base comparáveis, com pesquisa de texto inteiro, pesquisa geográfica e a capacidade de processar um determinado nível de ambiguidade nas entradas de pesquisa. Normalmente, é uma [funcionalidade especializada](search-features-list.md) ou a facilidade e simplicidade geral das APIs, das ferramentas e a gestão que determinam qual a melhor opção. |

Entre os fornecedores de nuvem, o Azure Cognitive Search é o mais forte para a pesquisa completa de textos sobre lojas de conteúdos e bases de dados no Azure, para apps que dependem principalmente da pesquisa tanto para a recuperação de informações como para a navegação de conteúdos. 

As principais vantagens incluem:

+ Integração de dados do Azure (crawlers) na camada de indexação
+ Portal do Azure para gestão central
+ Dimensionamento, fiabilidade e disponibilidade de classe mundial do Azure
+ Processamento de IA de dados brutos para torná-lo mais pes pes pes pes pes pes pestado, incluindo texto a partir de imagens, ou encontrar padrões em conteúdo não estruturado.
+ A análise linguística e personalizada, com analisadores de pesquisa de texto completo sólidos em 56 idiomas
+ [Principais funcionalidades das aplicações focadas na pesquisa](search-features-list.md): classificação, facetamento, sugestões, sinónimos, pesquisa geográfica e muito mais.

Entre os nossos clientes, aqueles que são capazes de aproveitar a mais ampla gama de funcionalidades na Azure Cognitive Search incluem catálogos online, programas de linha de negócios e aplicações de descoberta de documentos.

## <a name="watch-this-video"></a>Assistir a este vídeo

Neste vídeo de 15 minutos, o gestor de programas Luis Cabrera apresenta a Azure Cognitive Search.

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]