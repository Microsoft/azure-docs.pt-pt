---
title: Adicione pesquisa completa de texto ao Armazenamento De Blob Azure
titleSuffix: Azure Cognitive Search
description: Extrair conteúdo e adicionar estrutura às bolhas Azure ao construir um índice completo de pesquisa de texto em Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fd405513f8bdef09e6d3ab996fc2c04bd397db13
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659435"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-cognitive-search"></a>Adicione pesquisa completa de texto aos dados de blob Azure usando pesquisa cognitiva Azure

Pesquisar em toda a variedade de tipos de conteúdo armazenados no armazenamento Azure Blob pode ser um problema difícil de resolver. No entanto, pode indexar e pesquisar o conteúdo das suas Blobs em apenas alguns cliques utilizando a [Pesquisa Cognitiva Azure](search-what-is-azure-search.md). A Azure Cognitive Search tem uma integração incorporada para indexar o armazenamento blob através de um [*indexador Blob*](search-howto-indexing-azure-blob-storage.md) que adiciona capacidades de conhecimento de dados à indexação.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>O que significa adicionar pesquisa completa de texto a dados blob

O Azure Cognitive Search é um serviço de pesquisa em nuvem que fornece motores de indexação e consulta que operam sobre índices definidos pelo utilizador hospedados no seu serviço de pesquisa. Co-localizar o seu conteúdo pesquisável com o motor de consulta na nuvem é necessário para o desempenho, devolvendo resultados a uma velocidade que os utilizadores esperam de consultas de pesquisa.

A Azure Cognitive Search integra-se com o armazenamento azure blob na camada de indexação, importando o seu conteúdo blob como documentos de pesquisa indexados em *índices invertidos* e outras estruturas de consulta que suportam consultas de texto de forma livre e expressões de filtro. Como o seu conteúdo blob está indexado num índice de pesquisa, o acesso ao conteúdo blob pode alavancar toda a gama de funcionalidades de consulta em Pesquisa Cognitiva Azure.

Uma vez criado e povoado o índice, existe independentemente do seu recipiente de bolhas, mas pode reexecutar as operações de indexação para refrescar o seu índice com alterações no recipiente subjacente. A informação sobre as bolhas individuais é utilizada para a deteção de alterações. Pode optar por uma execução programada ou por uma indexação a pedido como mecanismo de atualização.

As inputs são as suas bolhas, num único recipiente, no armazenamento azure Blob. Blobs pode ser quase qualquer tipo de dados de texto. Se as suas bolhas contiverem imagens, pode adicionar o enriquecimento de [IA à indexação](search-blob-ai-integration.md) de bolhas para criar e extrair texto a partir de imagens.

A saída é sempre um índice de Pesquisa Cognitiva Azure, usado para pesquisa rápida de texto, recuperação e exploração em aplicações de clientes. Pelo meio está a arquitetura do oleoduto indexante em si. O oleoduto baseia-se na característica do *indexador,* discutida ainda mais neste artigo.

## <a name="start-with-services"></a>Comece com os serviços

Precisa de pesquisa cognitiva azure e armazenamento azure blob. No armazenamento blob, você precisa de um recipiente que forneça conteúdo de origem.

Pode começar diretamente na página do portal da sua conta de Armazenamento. Na página de navegação à esquerda, sob o **serviço Blob** clique em **Adicionar Pesquisa Cognitiva Azure** para criar um novo serviço ou selecione um existente. 

Assim que adicionar a Pesquisa Cognitiva Azure à sua conta de armazenamento, pode seguir o processo padrão para indexar os dados blob. Recomendamos o assistente de **dados de importação** em Pesquisa Cognitiva Azure para uma introdução inicial fácil, ou chamamos as APIs REST usando uma ferramenta como o Carteiro. Este tutorial acompanha-o através dos passos de chamar a API REST em Carteiro: [Index e pesquisar dados semi-estruturados (bolhas JSON) em Pesquisa Cognitiva Azure](search-semi-structured-data.md). 

## <a name="use-a-blob-indexer"></a>Use um indexador Blob

Um *indexante* é um subserviço consciente de dados equipado com lógica interna para recolher dados, ler dados de metadados, recuperar dados e serializar dados de formatos nativos em documentos JSON para posterior importação. 

As bolhas no Armazenamento Azure são indexadas utilizando o indexante de [armazenamento De pesquisa cognitiva Azure Blob](search-howto-indexing-azure-blob-storage.md). Pode invocar este indexante utilizando o assistente de **dados de importação,** uma API REST ou o .NET SDK. Em código, utiliza este indexador definindo o tipo e fornecendo informações de ligação que incluam uma conta de Armazenamento Azure juntamente com um recipiente de bolhas. Pode subdefinir as suas bolhas criando um diretório virtual, que pode passar como parâmetro, ou filtrando uma extensão do tipo de ficheiro.

Um indexante faz a "rachadura de documentos", abrindo uma bolha para inspecionar o conteúdo. Depois de ligar à fonte de dados, é o primeiro passo no oleoduto. Para os dados blob, é aqui que são detetados PDF, docs de escritório e outros tipos de conteúdo. A rachadura de documentos com extração de texto não é custo. Se as suas bolhas contiverem conteúdo de imagem, as imagens são ignoradas a menos que adicione enriquecimento de [IA](search-blob-ai-integration.md). A indexação padrão aplica-se apenas ao conteúdo de texto.

O indexante Blob vem com parâmetros de configuração e suporta o rastreio de alterações se os dados subjacentes fornecerem informações suficientes. Pode saber mais sobre a funcionalidade principal no indexante de [armazenamento De Pesquisa Cognitiva Azure](search-howto-indexing-azure-blob-storage.md).

### <a name="supported-content-types"></a>Tipos de conteúdo suportado

Ao executar um indexador Blob sobre um recipiente, pode extrair texto e metadados dos seguintes tipos de conteúdo com uma única consulta:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>Indexação de metadados blob

Um cenário comum que facilita a triagem através de bolhas de qualquer tipo de conteúdo é indexar tanto os metadados personalizados como as propriedades do sistema para cada bolha. Desta forma, a informação para todas as bolhas é indexada independentemente do tipo de documento, armazenada num índice no seu serviço de pesquisa. Usando o seu novo índice, pode então proceder a classificar, filtrar e encarar todos os conteúdos de armazenamento blob.

> [!NOTE]
> As etiquetas do Índice Blob são indexadas de forma nativa pelo serviço de armazenamento Blob e expostas para consulta. Se os atributos chave/valor das suas bolhas necessitarem de capacidades de indexação e filtragem, as etiquetas do Índice Blob devem ser alavancadas em vez de metadados.
>
> Para saber mais sobre o Blob Index, consulte [Gerir e encontrar dados sobre o Armazenamento de Blob Azure com índice blob](../storage/blobs/storage-manage-find-blobs.md).

### <a name="indexing-json-blobs"></a>Bolhas JSON indexantes
Os indexadores podem ser configurados para extrair conteúdo estruturado encontrado em bolhas que contenham JSON. Um indexante pode ler bolhas JSON e analisar o conteúdo estruturado nos campos apropriados de um documento de pesquisa. Os indexadores também podem tomar bolhas que contenham uma variedade de objetos JSON e mapear cada elemento para um documento de pesquisa separado. Pode definir um modo de análise para afetar o tipo de objeto JSON criado pelo indexante.

## <a name="search-blob-content-in-a-search-index"></a>Pesquisar conteúdo blob em um índice de pesquisa 

A saída de um indexante é um índice de pesquisa, usado para exploração interativa usando texto gratuito e consultas filtradas numa aplicação de cliente. Para a exploração e verificação inicial de conteúdos, recomendamos começar pelo [Search Explorer](search-explorer.md) no portal para examinar a estrutura do documento. Você pode usar [sintaxe de consulta simples,](query-simple-syntax.md) [sintaxe](query-lucene-syntax.md)de consulta completa, e [sintaxe](query-odata-filter-orderby-syntax.md) de expressão de filtro no explorador de pesquisa.

Uma solução mais permanente é recolher inputs de consulta e apresentar a resposta como resultados de pesquisa numa aplicação de cliente. O seguinte tutorial C# explica como construir uma aplicação de pesquisa: Crie a [sua primeira aplicação em Pesquisa Cognitiva Azure](tutorial-csharp-create-first-app.md).

## <a name="next-steps"></a>Passos seguintes

+ [Upload, download e lista de bolhas com o portal Azure (armazenamento Azure Blob)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Configurar um indexador de bolhas (Pesquisa Cognitiva Azure)](search-howto-indexing-azure-blob-storage.md) 
