---
title: Pesquisa rumo ao conteúdo de armazenamento da Azure Blob
titleSuffix: Azure Cognitive Search
description: Saiba como extrair texto das bolhas Azure e torná-lo pescativo num índice de Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: f61bf635cc61a2153a7bb016ef4b4711d7ba7391
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91355300"
---
# <a name="search-over-azure-blob-storage-content"></a>Pesquisa rumo ao conteúdo de armazenamento da Azure Blob

Pesquisar através da variedade de tipos de conteúdo armazenados no armazenamento Azure Blob pode ser um problema difícil de resolver. Neste artigo, reveja o fluxo básico de trabalho para extrair conteúdo e metadados de bolhas e enviá-lo para um índice de pesquisa em Azure Cognitive Search. O índice resultante pode ser consultado através da pesquisa completa por texto.

> [!NOTE]
> Já familiarizado com o fluxo de trabalho e composição? [Como configurar um indexante blob](search-howto-indexing-azure-blob-storage.md) é o seu próximo passo.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>O que significa adicionar a pesquisa completa de texto aos dados blob

Azure Cognitive Search é um serviço de pesquisa que suporta a indexação e consulta de cargas de trabalho sobre índices definidos pelo utilizador que contém o seu conteúdo pesmável remoto alojado na nuvem. Co-localizar o seu conteúdo pesmável com o motor de consulta é necessário para o desempenho, retornando os resultados a uma velocidade que os utilizadores têm vindo a esperar das consultas de pesquisa.

A Cognitive Search integra-se com o armazenamento Azure Blob na camada de indexação, importando o seu conteúdo blob como documentos de pesquisa que são indexados em *índices invertidos* e outras estruturas de consulta que suportam consultas de texto de forma livre e expressões de filtro. Como o seu conteúdo blob está indexado a um índice de pesquisa, pode utilizar toda a gama de funcionalidades de consulta na Azure Cognitive Search para encontrar informações no seu conteúdo blob.

As entradas são as suas bolhas, num único recipiente, no armazenamento de Azure Blob. As bolhas podem ser quase qualquer tipo de dados de texto. Se as suas bolhas contiverem imagens, pode adicionar [enriquecimento de IA à indexação de bolhas](search-blob-ai-integration.md) para criar e extrair texto das imagens.

A saída é sempre um índice de Pesquisa Cognitiva Azure, utilizado para pesquisa rápida de texto, recuperação e exploração em aplicações de clientes. Pelo meio está a própria arquitetura do gasoduto de indexação. O pipeline baseia-se na característica *indexante,* discutida mais aprofundadamente neste artigo.

Uma vez criado e povoado o índice, existe independentemente do seu recipiente blob, mas pode re-re-repetir as operações de indexação para atualizar o seu índice com base em documentos alterados. As informações sobre o tempo em bolhas individuais são utilizadas para deteção de alterações. Pode optar pela execução programada ou pela indexação a pedido como mecanismo de atualização.

## <a name="required-resources"></a>Recursos necessários

Precisa tanto de Pesquisa Cognitiva Azure como de armazenamento de Azure Blob. Dentro do armazenamento Blob, você precisa de um recipiente que forneça conteúdo de origem.

Pode começar diretamente na página do portal da conta de Armazenamento. Na página de navegação esquerda, em **serviço Blob** clique em **Adicionar Azure Cognitive Search** para criar um novo serviço ou selecionar um existente. 

Assim que adicionar a Azure Cognitive Search à sua conta de armazenamento, pode seguir o processo padrão para indexar dados de blob. Recomendamos o assistente **de dados de importação** em Azure Cognitive Search para uma introdução inicial fácil, ou chamar as APIs REST usando uma ferramenta como o Carteiro. Este tutorial acompanha-o através dos passos de chamar a API REST em Carteiro: [Índice e pesquisa de dados semi-estruturados (JSON blobs) em Azure Cognitive Search](search-semi-structured-data.md). 

## <a name="use-a-blob-indexer"></a>Use um indexador Blob

Um *indexante* é um subserviço de dados-consciente na Pesquisa Cognitiva, equipado com lógica interna para amostragem de dados, leitura de dados de metadados, recuperação de dados e serialização de dados de formatos nativos em documentos JSON para posterior importação. 

As bolhas no Azure Storage são indexadas utilizando o [indexante de armazenamento Azure Cognitive Search Blob](search-howto-indexing-azure-blob-storage.md). Pode invocar este indexer utilizando o assistente **de dados De importação,** uma API REST ou o .NET SDK. Em código, utilize este indexante definindo o tipo e fornecendo informações de ligação que incluam uma conta de Armazenamento Azure juntamente com um recipiente blob. Pode subconscrever as suas bolhas criando um diretório virtual, que pode passar como parâmetro, ou filtrando uma extensão do tipo de ficheiro.

Um indexante faz o "documento rachar", abrindo uma bolha para inspecionar o conteúdo. Depois de ligar à fonte de dados, é o primeiro passo no oleoduto. Para os dados blob, é aqui que são detetados PDF, docs de escritório e outros tipos de conteúdo. A rachadura de documento com a extração de texto não é cobrado. Se as suas bolhas contiverem conteúdo de imagem, as imagens são ignoradas a menos que [adicione enriquecimento de IA](search-blob-ai-integration.md). A indexação padrão aplica-se apenas ao conteúdo de texto.

O indexante Blob vem com parâmetros de configuração e suporta o rastreio de alterações se os dados subjacentes fornecerem informações suficientes. Pode saber mais sobre a funcionalidade principal no [indexer de armazenamento Azure Cognitive Search Blob](search-howto-indexing-azure-blob-storage.md).

### <a name="supported-content-types"></a>Tipos de conteúdo suportado

Ao executar um indexante Blob sobre um recipiente, pode extrair texto e metadados dos seguintes tipos de conteúdo com uma única consulta:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>Indexação de metadados blob

Um cenário comum que facilita a classificação através de bolhas de qualquer tipo de conteúdo é indexar tanto metadados personalizados como propriedades do sistema para cada bolha. Desta forma, a informação para todas as bolhas é indexada independentemente do tipo de documento, armazenada num índice no seu serviço de pesquisa. Utilizando o seu novo índice, pode proceder à triagem, filtro e faceta em todos os conteúdos de armazenamento Blob.

> [!NOTE]
> As etiquetas blob Index são indexadas de forma nativa pelo serviço de armazenamento Blob e expostas para consulta. Se os atributos chave/valor das suas bolhas requerem capacidades de indexação e filtragem, as tags Blob Index devem ser alavancadas em vez de metadados.
>
> Para saber mais sobre o Blob Index, consulte [Gerir e encontrar dados sobre o armazenamento de blob Azure blob com índice blob](../storage/blobs/storage-manage-find-blobs.md).

### <a name="indexing-json-blobs"></a>Indexação de bolhas JSON

Os indexantes podem ser configurados para extrair conteúdo estruturado encontrado em bolhas que contenham JSON. Um indexante pode ler bolhas JSON e analisar o conteúdo estruturado nos campos apropriados de um documento de pesquisa. Os indexantes também podem tomar bolhas que contenham uma matriz de objetos JSON e mapear cada elemento para um documento de pesquisa separado. Pode definir um modo de análise para afetar o tipo de objeto JSON criado pelo indexante.

## <a name="search-blob-content-in-a-search-index"></a>Pesquisar conteúdo de bolhas num índice de pesquisa 

A saída de um indexante é um índice de pesquisa, usado para exploração interativa usando texto gratuito e consultas filtradas numa aplicação do cliente. Para a exploração inicial e verificação de conteúdos, recomendamos começar com [o Search Explorer](search-explorer.md) no portal para examinar a estrutura do documento. Pode utilizar [a sintaxe de consulta simples,](query-simple-syntax.md) [a sintaxe de consulta completa](query-lucene-syntax.md)e a [sintaxe de expressão de filtro](query-odata-filter-orderby-syntax.md) no explorador de pesquisa.

Uma solução mais permanente é recolher entradas de consulta e apresentar a resposta como resultados de pesquisa numa aplicação do cliente. O seguinte tutorial C# explica como construir uma aplicação de pesquisa: [Crie a sua primeira aplicação em Azure Cognitive Search](tutorial-csharp-create-first-app.md).

## <a name="next-steps"></a>Passos seguintes

+ [Upload, download e liste blobs com o portal Azure (armazenamento Azure Blob)](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [Configurar um indexante blob (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md)