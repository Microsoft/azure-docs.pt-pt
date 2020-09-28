---
title: Bolhas alteradas e apagadas
titleSuffix: Azure Cognitive Search
description: Após uma primeira criação de índice de pesquisa que importa a partir do armazenamento de Azure Blob, a indexação subsequente pode captar apenas as bolhas que são alteradas ou eliminadas. Este artigo explica os detalhes.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 210a4a352f5130bc67d70f47bdf6305c5a992282
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403795"
---
# <a name="how-to-set-up-change-and-deletion-detection-for-blobs-in-azure-cognitive-search-indexing"></a>Como configurar a deteção de alterações e eliminação para bolhas no índice de pesquisa cognitiva Azure

Após a criação de um índice inicial de pesquisa, é melhor configurar os trabalhos indexantes subsequentes para recolher apenas os documentos que foram criados ou eliminados desde a execução inicial. Para o conteúdo de pesquisa originário do armazenamento Azure Blob, a deteção de alterações ocorre automaticamente quando utiliza um crono de programa para desencadear a indexação. Por predefinição, o serviço reindexiza apenas as bolhas alteradas, conforme determinado pela estada de tempo da `LastModified` bolha. Em contraste com outras fontes de dados suportadas por indexadores de pesquisa, as bolhas têm sempre uma meta de tempo, o que elimina a necessidade de configurar manualmente uma política de deteção de alterações.

Embora a deteção de alterações seja um dado adquirido, a deteção de eliminação não é. Se pretender detetar documentos eliminados, certifique-se de que utiliza uma abordagem de "eliminação suave". Se eliminar imediatamente as bolhas, os documentos correspondentes não serão removidos do índice de pesquisa.

Existem duas formas de implementar a abordagem de eliminação suave. Ambos são descritos abaixo.

## <a name="native-blob-soft-delete-preview"></a>Exclusão suave de blob nativo (pré-visualização)

> [!IMPORTANT]
> O suporte para a eliminação suave de blob nativo está em pré-visualização. A funcionalidade de pré-visualização é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [versão REST API 2020-06-30-Preview](./search-api-preview.md) fornece esta funcionalidade. Atualmente não existe porta ou suporte .NET SDK.

> [!NOTE]
> Ao utilizar a política de exclusão suave de blob nativa, as chaves do documento para os documentos no seu índice devem ser uma propriedade blob ou metadados blob.

Neste método utilizará a função [de exclusão suave de blob nativa](../storage/blobs/soft-delete-blob-overview.md) oferecida pelo armazenamento Azure Blob. Se a exclusão suave de blob nativa estiver ativada na sua conta de armazenamento, a sua fonte de dados tem um conjunto de política de exclusão suave nativa, e o indexante encontra uma bolha que foi transitada para um estado de eliminação suave, o indexante removerá esse documento do índice. A política de eliminação suave de blob nativo não é suportada ao indexar bolhas do Azure Data Lake Storage Gen2.

Utilize os passos seguintes:

1. Ativar [a eliminação suave nativa para o armazenamento do Azure Blob](../storage/blobs/soft-delete-blob-overview.md). Recomendamos definir a política de retenção para um valor muito superior ao seu calendário de intervalos indexante. Desta forma, se houver um problema a executar o indexante ou se tiver um grande número de documentos para indexar, há muito tempo para o indexante processar eventualmente as bolhas apagadas suaves. Os indexantes de Pesquisa Cognitiva Azure só apagarão um documento do índice se processar a bolha enquanto estiver em estado de eliminação suave.

1. Configure uma política de deteção de eliminação suave de bolhas nativas na fonte de dados. Apresentamos um exemplo abaixo. Uma vez que esta funcionalidade está em pré-visualização, tem de utilizar a pré-visualização REST API.

1. Executar o indexante ou definir o indexante para executar em um horário. Quando o indexante executa e processa a bolha, o documento será removido do índice.

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

### <a name="reindexing-un-deleted-blobs-using-native-soft-delete-policies"></a>Reindexing blobs não eliminados (utilizando políticas nativas de eliminação suave)

Se eliminar uma bolha do armazenamento Azure Blob com exclusão suave nativa ativada na sua conta de armazenamento, a bolha irá transitar para um estado suavemente eliminado, dando-lhe a opção de desaprovar essa bolha dentro do período de retenção. Se inverter uma eliminação após o indexante o ter processado, o indexante nem sempre indexará a bolha restaurada. Isto porque o indexante determina quais as bolhas a indexar com base na estada de tempo da `LastModified` bolha. Quando uma bolha apagada suave é desabatida, o seu `LastModified` cartão de tempo não é atualizado, por isso, se o indexante já tiver processado bolhas com os mais recentes `LastModified` picos de tempo, não voltará a reindexar a bolha não eliminada. 

Para se certificar de que uma bolha não eliminada é reexexexuada, terá de atualizar a estada de tempo da `LastModified` bolha. Uma maneira de fazer isto é ressaltar os metadados daquela bolha. Não é necessário alterar os metadados, mas a rescelagem dos metadados atualizará o tempotamos da bolha `LastModified` para que o indexante saiba que precisa de reindexar esta bolha.

## <a name="soft-delete-using-custom-metadata"></a>Excluir suavemente usando metadados personalizados

Neste método utilizará os metadados de uma bolha para indicar quando um documento deve ser removido do índice de pesquisa. Este método requer duas ações separadas, eliminando o documento de pesquisa do índice, seguido de eliminação de bolhas no Armazenamento Azure.

Utilize os passos seguintes:

1. Adicione um par de metadados personalizados à bolha para indicar à Azure Cognitive Search que é logicamente eliminado.

1. Configure uma política de deteção de colunas de eliminação suave na fonte de dados. Apresentamos um exemplo abaixo.

1. Uma vez que o indexante tenha processado a bolha e eliminado o documento do índice, pode eliminar a bolha no armazenamento Azure Blob.

Por exemplo, a seguinte política considera que uma bolha deve ser eliminada se tiver uma propriedade de metadados `IsDeleted` com o `true` valor:

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

### <a name="reindexing-un-deleted-blobs-using-custom-metadata"></a>Reindexing blobs não eliminados (usando metadados personalizados)

Depois de um indexante processar uma bolha apagada e remover o documento de pesquisa correspondente do índice, não revisitará essa bolha se a restaurar mais tarde se o tempotando da bolha `LastModified` for mais antigo do que a última execução do indexante.

Se quiser reindexar esse documento, mude o `"softDeleteMarkerValue" : "false"` para essa bolha e volte a repetir o indexante.

## <a name="see-also"></a>Ver também

* [Indexadores na Pesquisa Cognitiva do Azure](search-indexer-overview.md)
* [Como configurar um indexador de bolhas](search-howto-indexing-azure-blob-storage.md)
* [Visão geral da indexação da bolha](search-blob-storage-integration.md)