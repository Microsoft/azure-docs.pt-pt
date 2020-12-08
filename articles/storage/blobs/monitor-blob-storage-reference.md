---
title: Referência de dados de monitorização de armazenamento Azure Blob Microsoft Docs
description: Referência de registo e métricas para monitorização de dados do armazenamento da Azure Blob.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 16ae2f9e74202aff47e58a22dbe21a28d8280a7e
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780727"
---
# <a name="azure-blob-storage-monitoring-data-reference"></a>Referência de dados de monitorização de armazenamento Azure Blob

Consulte [o Monitor Azure Storage](monitor-blob-storage.md) para obter informações sobre a recolha e análise de dados de monitorização para o Armazenamento Azure.

## <a name="metrics"></a>Métricas

As tabelas a seguir listam as métricas da plataforma recolhidas para o Azure Storage. 

### <a name="capacity-metrics"></a>Métricas de capacidade

Os valores das métricas de capacidade são atualizados diariamente (até 24 Horas). O grão de tempo define o intervalo de tempo para o qual são apresentados os valores das métricas. O grão de tempo suportado para todas as métricas de capacidade é de uma hora (PT1H).

O Azure Storage fornece as seguintes métricas de capacidade no Monitor Azure.

#### <a name="account-level"></a>Nível de conta

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="blob-storage"></a>Armazenamento de blobs

Esta tabela mostra [métricas de armazenamento Blob](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices).

| Métrica | Descrição |
| ------------------- | ----------------- |
| Capacidade blob | O total do armazenamento blob usado na conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 <br/> Dimensões: **BlobType,** e **BlobTier** [(Definição)](#metrics-dimensions) |
| BlobCount    | O número de objetos blob armazenados na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 <br/> Dimensões: **BlobType,** e **BlobTier** [(Definição)](#metrics-dimensions) |
| BlobProvisionedSize | A quantidade de armazenamento a provisionada na conta de armazenamento. Esta métrica aplica-se apenas às contas de armazenamento premium. <br/><br/> Unidade: bytes <br/> Tipo de agregação: Média |
| Contagem de contentores    | O número de contentores na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| Capacidade de Indexação     | A quantidade de armazenamento utilizada pelo Índice Hierárquico ADLS Gen2 <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

### <a name="transaction-metrics"></a>Métricas de transação

As métricas de transação são emitidas em cada pedido para uma conta de armazenamento de Azure Storage para Azure Monitor. No caso de não haver atividade na sua conta de armazenamento, não haverá dados sobre métricas de transação no período. Todas as métricas de transação estão disponíveis tanto a nível de serviço de armazenamento de contas como blob. O grão de tempo define o intervalo de tempo que os valores métricos são apresentados. Os grãos de tempo suportados para todas as métricas de transação são PT1H e PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Dimensões das métricas

O Azure Storage suporta as seguintes dimensões para métricas no Azure Monitor.

### <a name="dimensions-available-to-all-storage-services"></a>Dimensões disponíveis para todos os serviços de armazenamento

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

### <a name="dimensions-specific-to-blob-storage"></a>Dimensões específicas do armazenamento blob

| Nome da dimensão | Descrição |
| ------------------- | ----------------- |
| **BlobType** | O tipo de bolha apenas para métricas Blob. Os valores suportados são **BlockBlob,** **PageBlob** e **Azure Data Lake Storage**. As bolhas de apêndice estão incluídas no **BlockBlob.** |
| **BlobTier** | O armazenamento Azure oferece diferentes níveis de acesso, que permitem armazenar dados de objetos blob da forma mais rentável. Veja mais na [camada de blob de armazenamento Azure](../blobs/storage-blob-storage-tiers.md). Os valores suportados incluem: <br/> <li>**Quente**: Nível quente</li> <li>**Cool**: Cool tier</li> <li>**Arquivo**: Nível de arquivo</li> <li>**Premium**: Nível premium para bolha de bloco</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: Tipos de nível para bolha de página premium</li> <li>**Standard**: Tipo de nível para página padrão Blob</li> <li>**Untiered**: Tipo de nível para fins gerais conta de armazenamento v1</li> |

Para as dimensões de suporte das métricas, é necessário especificar o valor da dimensão para ver os valores das métricas correspondentes. Por exemplo, se olharmos para o valor  **de Transações** para respostas bem sucedidas, precisa filtrar a dimensão **ResponseType** com **Sucesso**. Se olhar para o valor **BlobCount** para Block Blob, tem de filtrar a dimensão **BlobType** com **BlockBlob**.

## <a name="resource-logs-preview"></a>Registos de recursos (pré-visualização)

> [!NOTE]
> Os registos de armazenamento Azure no Azure Monitor estão em pré-visualização pública e estão disponíveis para testes de pré-visualização em todas as regiões de nuvem pública. Esta pré-visualização permite registos para blobs (incluindo Azure Data Lake Storage Gen2), ficheiros, filas, tabelas, contas de armazenamento premium em v1 de uso geral e contas de armazenamento v2 de uso geral. As contas de armazenamento clássicas não são suportadas.

A tabela que se segue lista as propriedades dos registos de recursos de armazenamento Azure quando são recolhidas em Registos monitores Azure ou Azure Storage. As propriedades descrevem a operação, o serviço e o tipo de autorização que foi usada para realizar a operação.

### <a name="fields-that-describe-the-operation"></a>Campos que descrevem a operação

```json
{
    "time": "2019-02-28T19:10:21.2123117Z",
    "resourceId": "/subscriptions/12345678-2222-3333-4444-555555555555/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/testaccount1/blobServices/default",
    "category": "StorageWrite",
    "operationName": "PutBlob",
    "operationVersion": "2017-04-17",
    "schemaVersion": "1.0",
    "statusCode": 201,
    "statusText": "Success",
    "durationMs": 5,
    "callerIpAddress": "192.168.0.1:11111",
    "correlationId": "ad881411-201e-004e-1c99-cfd67d000000",
    "location": "uswestcentral",
    "uri": "http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10"
}
```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Campos que descrevem como a operação foi autenticada

```json
{
    "identity": {
        "authorization": [
            {
                "action": "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
                "principals": [
                    {
                        "id": "fde5ba15-4355-4223-b811-cccccccccccc",
                        "type": "User"
                    }
                ],
                "roleAssignmentId": "ecf75cb8-491c-4a25-ad6e-aaaaaaaaaaaa",
                "roleDefinitionId": "b7e6dc6d-f1e8-4753-8033-ffffffffffff"
            }
        ],
        "requester": {
            "appId": "691458b9-1327-4635-9f55-bbbbbbbbbbbb",
            "audience": "https://storage.azure.com/",
            "objectId": "fde5ba15-4355-4223-b811-cccccccccccc",
            "tenantId": "72f988bf-86f1-41af-91ab-dddddddddddd",
            "tokenIssuer": "https://sts.windows.net/72f988bf-86f1-41af-91ab-eeeeeeeeeeee/"
           },
        "type": "OAuth"
    },
}

```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Campos que descrevem o serviço

```json
{
    "properties": {
        "accountName": "testaccount1",
        "requestUrl": "https://testaccount1.blob.core.windows.net:443/upload?restype=container&comp=list&prefix=&delimiter=%2F&marker=&maxresults=30&include=metadata&_=1551405598426",
        "userAgentHeader": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134",
        "referrerHeader": "blob:https://ms.portal.azure.com/6f50025f-3b88-488d-b29e-3c592a31ddc9",
        "clientRequestId": "",
        "etag": "",
        "serverLatencyMs": 63,
        "serviceType": "blob",
        "operationCount": 0,
        "requestHeaderSize": 2658,
        "requestBodySize": 0,
        "responseHeaderSize": 295,
        "responseBodySize": 2018,
        "contentLengthHeader": 0,
        "requestMd5": "",
        "serverMd5": "",
        "lastModifiedTime": "",
        "conditionsUsed": "",
        "smbTreeConnectID" : "0x3",
        "smbPersistentHandleID" : "0x6003f",
        "smbVolatileHandleID" : "0xFFFFFFFF00000065",
        "smbMessageID" : "0x3b165",
        "smbCreditsConsumed" : "0x3",
        "smbCommandDetail" : "0x2000 bytes at offset 0xf2000",
        "smbFileId" : " 0x9223442405598953",
        "smbSessionID" : "0x8530280128000049",
        "smbCommandMajor" : "0x6",
        "smbCommandMinor" : "DirectoryCloseAndDelete"
    }

}
```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Ver também

- Consulte [o Monitor Azure Storage](monitor-blob-storage.md) para obter uma descrição da monitorização do armazenamento do Azure.
- Consulte [os recursos de Monitor Azure com o Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md) para obter informações sobre a monitorização dos recursos do Azure.