---
title: Bolhas alteradas e apagadas
titleSuffix: Azure Cognitive Search
description: Após uma primeira criação de índice de pesquisa que importa a partir do armazenamento de Azure Blob, a indexação subsequente pode captar apenas as bolhas que são alteradas ou eliminadas. Este artigo explica os detalhes.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 79d5583f8c9e562a0d21a91c210aa6259472661d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383539"
---
# <a name="change-and-deletion-detection-in-blob-indexing-azure-cognitive-search"></a>Deteção de alterações e eliminação na indexação de bolhas (Pesquisa Cognitiva Azure)

Após a criação de um índice inicial de pesquisa, poderá querer que os trabalhos indexantes subsequentes apenas recolham documentos novos e alterados. Para o conteúdo de pesquisa originário do armazenamento Azure Blob, a deteção de alterações ocorre automaticamente quando utiliza um crono de programa para desencadear a indexação. Por predefinição, o serviço reindexiza apenas as bolhas alteradas, conforme determinado pela estada de tempo da `LastModified` bolha. Em contraste com outras fontes de dados suportadas por indexadores de pesquisa, as bolhas têm sempre uma meta de tempo, o que elimina a necessidade de configurar manualmente uma política de deteção de alterações.

Embora a deteção de alterações seja um dado adquirido, a deteção de eliminação não é. Se pretender detetar documentos eliminados, certifique-se de que utiliza uma abordagem de "eliminação suave". Se eliminar imediatamente as bolhas, os documentos correspondentes não serão removidos do índice de pesquisa.

Existem duas formas de implementar a abordagem soft delete:

+ Mancha nativa soft delete (pré-visualização), descrito em seguida
+ [Excluir suavemente usando metadados personalizados](#soft-delete-using-custom-metadata)

## <a name="native-blob-soft-delete-preview"></a>Exclusão suave de blob nativo (pré-visualização)

Para esta abordagem de deteção de eliminação, a Pesquisa Cognitiva depende da função [de exclusão suave de blob nativa](../storage/blobs/soft-delete-blob-overview.md) no armazenamento de Azure Blob para determinar se as bolhas transitaram para um estado suavemente eliminado. Quando as bolhas são detetadas neste estado, um indexante de pesquisa utiliza esta informação para remover o documento correspondente do índice.

> [!IMPORTANT]
> O suporte para a eliminação suave de blob nativo está em pré-visualização. A funcionalidade de pré-visualização é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [versão REST API 2020-06-30-Preview](./search-api-preview.md) fornece esta funcionalidade. Atualmente não existe porta ou suporte .NET SDK.

### <a name="prerequisites"></a>Pré-requisitos

+ [Ativar a eliminação suave para as bolhas](../storage/blobs/soft-delete-blob-enable.md).
+ As bolhas devem estar num recipiente de armazenamento Azure Blob. A política de exclusão suave de blob nativo de Pesquisa Cognitiva não é suportada para bolhas da Azure Data Lake Storage Gen2.
+ As chaves documentais para os documentos do seu índice devem ser mapeadas para serem uma propriedade blob ou metadados blob.
+ Tem de utilizar a pré-visualização REST API ( `api-version=2020-06-30-Preview` ) para configurar o suporte para eliminação suave.

### <a name="how-to-configure-deletion-detection-using-native-soft-delete"></a>Como configurar a deteção de eliminação utilizando a eliminação suave nativa

1. No armazenamento Blob, ao permitir a eliminação suave, descreva a política de retenção para um valor muito superior ao seu calendário de intervalos indexante. Desta forma, se houver um problema a executar o indexante ou se tiver um grande número de documentos para indexar, há muito tempo para o indexante processar eventualmente as bolhas apagadas suaves. Os indexantes de Pesquisa Cognitiva Azure só apagarão um documento do índice se processar a bolha enquanto estiver em estado de eliminação suave.

1. Em Pesquisa Cognitiva, detete uma política de deteção de eliminação suave de bolhas nativas na fonte de dados. Apresentamos um exemplo abaixo. Como esta funcionalidade está em pré-visualização, tem de utilizar a pré-visualização REST API.

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

1. [Executar o indexante](/rest/api/searchservice/run-indexer) ou definir o indexante para executar [em um horário](search-howto-schedule-indexers.md). Quando o indexante executa e processa uma bolha com um estado de exclusão suave, o documento de pesquisa correspondente será removido do índice.

### <a name="reindexing-undeleted-blobs-using-native-soft-delete-policies"></a>Reindexing blobs não desescolados (utilizando políticas nativas de eliminação suave)

Se restaurar uma bolha apagada suave no armazenamento Blob, o indexante nem sempre o reindexou. Isto porque o indexante usa a estada de tempo da bolha `LastModified` para determinar se a indexação é necessária. Quando uma bolha apagada suave não é desescolada, o seu `LastModified` cartão de tempo não é atualizado, por isso, se o indexante já tiver processado bolhas com os mais recentes `LastModified` picos de tempo, não voltará a reindexar a bolha não desabilitado. 

Para se certificar de que uma bolha não desadaída é reexame, terá de atualizar a estada de tempo da `LastModified` bolha. Uma maneira de fazer isto é ressaltar os metadados daquela bolha. Não é necessário alterar os metadados, mas a rescelagem dos metadados atualizará a hora do `LastModified` blob para que o indexante saiba apanhá-lo.

## <a name="soft-delete-using-custom-metadata"></a>Excluir suavemente usando metadados personalizados

Este método utiliza metadados de uma bolha para determinar se um documento de pesquisa deve ser removido do índice. Este método requer duas ações separadas, eliminando o documento de pesquisa do índice, seguido de eliminação de bolhas no Armazenamento Azure.

Existem passos a seguir tanto no armazenamento blob como na Pesquisa Cognitiva, mas não existem outras dependências de recursos. Esta capacidade é suportada em APIs geralmente disponíveis.

1. Adicione um par de metadados personalizados à bolha para indicar à Azure Cognitive Search que é logicamente eliminado.

1. Configure uma política de deteção de colunas de eliminação suave na fonte de dados. Por exemplo, a seguinte política considera que uma bolha deve ser eliminada se tiver uma propriedade de metadados `IsDeleted` com o `true` valor:

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
    ```

1. Uma vez que o indexante tenha processado a bolha e eliminado o documento do índice, pode eliminar a bolha no armazenamento Azure Blob.

### <a name="reindexing-undeleted-blobs-using-custom-metadata"></a>Reindexing blobs não desescolados (usando metadados personalizados)

Depois de um indexante processar uma bolha apagada e remover o documento de pesquisa correspondente do índice, não revisitará essa bolha se a restaurar mais tarde se o tempotando da bolha `LastModified` for mais antigo do que a última execução do indexante.

Se quiser reindexar esse documento, mude o `"softDeleteMarkerValue" : "false"` para essa bolha e volte a repetir o indexante.

## <a name="next-steps"></a>Passos seguintes

+ [Indexadores na Pesquisa Cognitiva do Azure](search-indexer-overview.md)
+ [Como configurar um indexador de bolhas](search-howto-indexing-azure-blob-storage.md)
+ [Visão geral da indexação da bolha](search-blob-storage-integration.md)