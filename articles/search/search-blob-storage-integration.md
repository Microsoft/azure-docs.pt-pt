---
title: Adicionar pesquisa de texto completo ao armazenamento de BLOBs do Azure
titleSuffix: Azure Cognitive Search
description: Extraia o conteúdo e adicione a estrutura aos BLOBs do Azure ao criar um índice de pesquisa de texto completo no Azure cognitiva esquisa.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 6bd96df1a2ed4ff7cde637a646ab514e89340b38
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792493"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-cognitive-search"></a>Adicionar pesquisa de texto completo aos dados de blob do Azure usando o Azure Pesquisa Cognitiva

Pesquisar na variedade de tipos de conteúdo armazenados no armazenamento de BLOBs do Azure pode ser um problema difícil de resolver. No entanto, você pode indexar e pesquisar o conteúdo de seus BLOBs em apenas alguns cliques usando o [Azure pesquisa cognitiva](search-what-is-azure-search.md). O Azure Pesquisa Cognitiva tem integração interna para indexação fora do armazenamento de BLOBs por meio de um [*indexador de blob*](search-howto-indexing-azure-blob-storage.md) que adiciona recursos de reconhecimento de fonte de dados à indexação.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>O que significa adicionar a pesquisa de texto completo aos dados de BLOB

O Azure Pesquisa Cognitiva é um serviço de pesquisa de nuvem que fornece mecanismos de indexação e consulta que operam em índices definidos pelo usuário hospedados em seu serviço de pesquisa. A colocalização de seu conteúdo pesquisável com o mecanismo de consulta na nuvem é necessária para o desempenho, retornando resultados em uma velocidade que os usuários esperam das consultas de pesquisa.

O Azure Pesquisa Cognitiva integra-se com o armazenamento de BLOBs do Azure na camada de indexação, importando o conteúdo do blob como documentos de pesquisa que são indexados em *índices invertidos* e outras estruturas de consulta que dão suporte a consultas de texto de forma livre e expressões . Como o conteúdo do blob é indexado em um índice de pesquisa, o acesso ao conteúdo do blob pode aproveitar a gama completa de recursos de consulta no Azure Pesquisa Cognitiva.

Depois que o índice é criado e preenchido, ele existe independentemente do seu contêiner de BLOB, mas você pode executar novamente as operações de indexação para atualizar o índice com as alterações no contêiner subjacente. As informações de carimbo de data/hora em BLOBs individuais são usadas para detecção de alteração. Você pode optar por execução agendada ou indexação sob demanda como o mecanismo de atualização.

As entradas são seus BLOBs, em um único contêiner, no armazenamento de BLOBs do Azure. Os BLOBs podem ser praticamente qualquer tipo de dados de texto. Se seus BLOBs contiverem imagens, você poderá adicionar o [enriquecimento de ia à indexação de blob](search-blob-ai-integration.md) para criar e extrair texto de imagens.

A saída é sempre um índice de Pesquisa Cognitiva do Azure, usado para pesquisa rápida de texto, recuperação e exploração em aplicativos cliente. No, between é a própria arquitetura de pipeline de indexação. O pipeline é baseado no recurso de *indexador* , abordado mais adiante neste artigo.

## <a name="start-with-services"></a>Iniciar com serviços

Você precisa do Azure Pesquisa Cognitiva e do armazenamento de BLOBs do Azure. No armazenamento de BLOBs, você precisa de um contêiner que forneça o conteúdo de origem.

Você pode iniciar diretamente em sua página do portal da conta de armazenamento. Na página de navegação à esquerda, em **serviço blob** , clique em **Adicionar Azure pesquisa cognitiva** para criar um novo serviço ou selecione um existente. 

Depois de adicionar o Azure Pesquisa Cognitiva à sua conta de armazenamento, você pode seguir o processo padrão para indexar dados de BLOB. Recomendamos o assistente de **importação de dados** no Azure pesquisa cognitiva para uma introdução inicial fácil ou chamar as APIs REST usando uma ferramenta como o postmaster. Este tutorial orienta você pelas etapas de chamada da API REST no postmaster: [indexe e pesquise dados semiestruturados (BLOBs JSON) no Azure pesquisa cognitiva](search-semi-structured-data.md). 

## <a name="use-a-blob-indexer"></a>Usar um indexador de BLOB

Um *indexador* é um subserviço com reconhecimento de fonte de dados equipado com lógica interna para dados de amostragem, leitura de dados de metadados, recuperação de dados e serialização de dados de formatos nativos em documentos JSON para importação subsequente. 

Os BLOBs no armazenamento do Azure são indexados usando o [indexador de armazenamento de Blob pesquisa cognitiva do Azure](search-howto-indexing-azure-blob-storage.md). Você pode invocar esse indexador usando o assistente de **importação de dados** , uma API REST ou o SDK do .net. No código, você usa esse indexador definindo o tipo e fornecendo informações de conexão que incluem uma conta de armazenamento do Azure junto com um contêiner de BLOB. Você pode subagrupar seus BLOBs criando um diretório virtual, que você pode então passar como um parâmetro ou filtrando uma extensão de tipo de arquivo.

Um indexador faz a "quebra de documento", abrindo um blob para inspecionar o conteúdo. Depois de se conectar à fonte de dados, é a primeira etapa no pipeline. Para dados de BLOB, é aqui que são detectados PDF, documentos do Office e outros tipos de conteúdo. A quebra de documentos com a extração de texto não é cobrada. Se seus BLOBs contiverem conteúdo de imagem, as imagens serão ignoradas, a menos que você [adicione o enriquecimento de ia](search-blob-ai-integration.md). A indexação padrão aplica-se somente ao conteúdo de texto.

O indexador de blob vem com parâmetros de configuração e oferece suporte ao controle de alterações se os dados subjacentes fornecerem informações suficientes. Você pode saber mais sobre a funcionalidade básica no [indexador de armazenamento de blobs pesquisa cognitiva do Azure](search-howto-indexing-azure-blob-storage.md).

### <a name="supported-content-types"></a>Tipos de conteúdo com suporte

Ao executar um indexador de BLOB em um contêiner, você pode extrair texto e metadados dos seguintes tipos de conteúdo com uma única consulta:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>Indexando metadados de BLOB

Um cenário comum que torna mais fácil a classificação por meio de blobs de qualquer tipo de conteúdo é indexar metadados personalizados e propriedades do sistema para cada blob. Dessa forma, as informações de todos os BLOBs são indexadas independentemente do tipo de documento, armazenados em um índice em seu serviço de pesquisa. Usando seu novo índice, você pode continuar a classificar, filtrar e facetar em todo o conteúdo do armazenamento de BLOBs.

### <a name="indexing-json-blobs"></a>Indexando BLOBs JSON
Os indexadores podem ser configurados para extrair o conteúdo estruturado encontrado em BLOBs que contêm JSON. Um indexador pode ler BLOBs JSON e analisar o conteúdo estruturado nos campos apropriados de um documento de pesquisa. Os indexadores também podem pegar BLOBs que contêm uma matriz de objetos JSON e mapear cada elemento para um documento de pesquisa separado. Você pode definir um modo de análise para afetar o tipo de objeto JSON criado pelo indexador.

## <a name="search-blob-content-in-a-search-index"></a>Pesquisar conteúdo de BLOB em um índice de pesquisa 

A saída de uma indexação é um índice de pesquisa, usada para exploração interativa usando texto livre e consultas filtradas em um aplicativo cliente. Para a exploração e a verificação iniciais do conteúdo, é recomendável iniciar com o [Search Explorer](search-explorer.md) no portal para examinar a estrutura do documento. Você pode usar a [sintaxe de consulta simples](query-simple-syntax.md), a [sintaxe de consulta completa](query-lucene-syntax.md)e a [sintaxe de expressão de filtro](query-odata-filter-orderby-syntax.md) no Search Explorer.

Uma solução mais permanente é coletar entradas de consulta e apresentar a resposta como resultado da pesquisa em um aplicativo cliente. O tutorial C# a seguir explica como criar um aplicativo de pesquisa: [criar seu primeiro aplicativo no Azure pesquisa cognitiva](tutorial-csharp-create-first-app.md).

## <a name="next-steps"></a>Passos seguintes

+ [Carregar, baixar e listar BLOBs com o portal do Azure (armazenamento de BLOBs do Azure)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Configurar um indexador de BLOB (Azure Search)](search-howto-indexing-azure-blob-storage.md) 
