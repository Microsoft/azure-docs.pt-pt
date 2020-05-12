---
title: Referência de dados de monitorização de armazenamento azure [ Microsoft Docs
description: Referência de registo e métricas para monitorização de dados do Armazenamento Azure.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/01/2020
ms.author: normesta
ms.subservice: logs
ms.openlocfilehash: 7ba66441a87e3e02483ae27400f9900d2d052af4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118163"
---
# <a name="azure-storage-monitoring-data-reference"></a>Referência de dados de monitorização de armazenamento azure

Consulte [o Monitoramento do Armazenamento Azure](monitor-storage.md) para obter detalhes sobre a recolha e análise de dados de monitorização para o Armazenamento Do Azure.

## <a name="metrics"></a>Métricas

As tabelas seguintes listam as métricas da plataforma recolhidas para o Armazenamento Azure. 

### <a name="capacity-metrics"></a>Métricas de capacidade

Os valores das métricas de capacidade são enviados para o Monitor Azure a cada hora. Os valores são refrescados diariamente. O grão de tempo define o intervalo de tempo para o qual os valores das métricas são apresentados. O grão de tempo suportado para todas as métricas de capacidade é de uma hora (PT1H).

O Armazenamento Azure fornece as seguintes métricas de capacidade no Monitor Azure.

#### <a name="account-level"></a>Nível de conta

| Métrica | Descrição |
| ------------------- | ----------------- |
| Capacidade Utilizada | A quantidade de armazenamento utilizada pela conta de armazenamento. Para as contas de armazenamento standard, é a soma da capacidade utilizada pelos blobs, tabelas, ficheiros e filas. Para as contas de armazenamento premium e contas de armazenamento de blobs, é o mesmo que BlobCapacity. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

#### <a name="blob-storage"></a>Armazenamento de blobs

| Métrica | Descrição |
| ------------------- | ----------------- |
| BlobCapacity | O total do armazenamento blob usado na conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 <br/> Dimensões: **BlobType**e **BlobTier** [(Definição)](#metrics-dimensions) |
| BlobCount    | O número de objetos blob armazenados na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 <br/> Dimensões: **BlobType**e **BlobTier** [(Definição)](#metrics-dimensions) |
| Contagem de contentores    | O número de contentores na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| IndexCapacity     | A quantidade de armazenamento utilizado pelo Índice Hierárquico ADLS Gen2 <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

#### <a name="table-storage"></a>Table Storage

| Métrica | Descrição |
| ------------------- | ----------------- |
| Capacidade de Mesa | A quantidade de armazenamento de mesa utilizada pela conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| Contagem de tabelas   | O número de tabelas na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| Contagem de tabelas | O número de entidades de mesa na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

#### <a name="queue-storage"></a>Armazenamento de filas

| Métrica | Descrição |
| ------------------- | ----------------- |
| Capacidade de fila | A quantidade de armazenamento de fila utilizada pela conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| Contagem de filas   | O número de filas na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| Contagem de mensagens de fila | O número de mensagens de fila não expiradas na conta de armazenamento. <br/><br/>Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

#### <a name="file-storage"></a>Armazenamento de ficheiros

| Métrica | Descrição |
| ------------------- | ----------------- |
| Capacidade de Arquivo | A quantidade de armazenamento de ficheiros utilizada pela conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| Contagem de Ficheiros   | O número de ficheiros na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| FileShareCount | O número de ações de ficheiros na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

### <a name="transaction-metrics"></a>Métricas de transação

As métricas de transação são emitidas em todos os pedidos de uma conta de armazenamento do Azure Storage para o Monitor Azure. No caso de nenhuma atividade na sua conta de armazenamento, não haverá dados sobre métricas de transação no período. Todas as métricas de transação estão disponíveis tanto a nível de conta como de serviço (armazenamento blob, armazenamento de mesa, Ficheiros Azure e armazenamento de fila). O grão de tempo define o intervalo de tempo que os valores métricos são apresentados. Os grãos de tempo suportados para todas as métricas de transação são PT1H e PT1M.

O Azure Storage fornece as seguintes métricas de transação no Monitor Azure.

| Métrica | Descrição |
| ------------------- | ----------------- |
| Transações | O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: ResponseType, GeoType, ApiName e Autenticação[(Definição)](#metrics-dimensions)<br/> Exemplo de valor: 1024 |
| Entrada | A quantidade de dados de entrada. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: GeoType, ApiName e Autenticação[(Definição)](#metrics-dimensions) <br/> Exemplo de valor: 1024 |
| Saída | A quantidade de dados de saída. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: GeoType, ApiName e Autenticação[(Definição)](#metrics-dimensions) <br/> Exemplo de valor: 1024 |
| SuccessServerLatency | O tempo médio utilizado para processar um pedido com êxito pelo Armazenamento do Azure. Este valor não inclui a latência de rede especificada em SuccessE2ELatency. <br/><br/> Unidade: Milliseconds <br/> Tipo de agregação: Média <br/> Dimensões aplicáveis: GeoType, ApiName e Autenticação[(Definição)](#metrics-dimensions) <br/> Exemplo de valor: 1024 |
| SuccessE2ELatency | A latência de ponto a ponto média de pedidos com êxito feitos a um serviço de armazenamento ou a uma operação de API especificada. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta. <br/><br/> Unidade: Milliseconds <br/> Tipo de agregação: Média <br/> Dimensões aplicáveis: GeoType, ApiName e Autenticação[(Definição)](#metrics-dimensions) <br/> Exemplo de valor: 1024 |
| Disponibilidade | A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao dividir o valor do total dos pedidos faturáveis pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada. <br/><br/> Unidade: Por cento <br/> Tipo de agregação: Média <br/> Dimensões aplicáveis: GeoType, ApiName e Autenticação[(Definição)](#metrics-dimensions) <br/> Exemplo de valor: 99.99 |

<a id="metrics-dimensions" />

## <a name="metrics-dimensions"></a>Dimensões métricas

O Armazenamento Azure suporta as dimensões seguintes para métricas no Monitor Azure.

| Nome da dimensão | Descrição |
| ------------------- | ----------------- |
| **BlobType** | O tipo de bolha apenas para as métricas blob. Os valores suportados são **BlockBlob,** **PageBlob**e **Azure Data Lake Storage.** Append Blob está incluído no BlockBlob. |
| **BlobTier** | O armazenamento Azure oferece diferentes níveis de acesso, que lhe permitem armazenar dados de objetos blob da forma mais rentável. Veja mais na [categoria de blob de armazenamento Azure](../blobs/storage-blob-storage-tiers.md). Os valores suportados incluem: <br/> <li>**Hot**: Hot tier</li> <li>**Cool**: Cool tier</li> <li>**Arquivo**: Nível de arquivo</li> <li>**Premium**: Nível premium para bloco blob</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: Tipos de nível para a página premium blob</li> <li>**Standard**: Tipo de nível para página padrão Blob</li> <li>**Untiered**: Tier type para uso geral v1 conta de armazenamento</li> |
| **GeoTipo** | Transação do cluster primário ou secundário. Os valores disponíveis incluem **Primário** e **Secundário.** Aplica-se ao Read Access Geo Redundant Storage (RA-GRS) ao ler objetos de inquilino secundário. |
| **Tipo de resposta** | Tipo de resposta a transações. Os valores disponíveis incluem: <br/><br/> <li>**ServerOtherError**: Todos os outros erros do lado do servidor, exceto os descritos </li> <li>**ServerBusyError**: Pedido autenticado que devolveu um código de estado HTTP 503. </li> <li>**ServerTimeoutError**: Pedido autenticado cronometrado que devolveu um código de estado HTTP 500. O tempo limite excedido ocorreu devido a um erro de servidor. </li> <li>**AutorizaçãoErro**: Pedido autenticado que falhou devido ao acesso não autorizado de dados ou a uma falha de autorização. </li> <li>**Error de rede**: Pedido autenticado que falhou devido a erros de rede. Ocorre normalmente quando um cliente fecha prematuramente uma ligação antes da expiração do tempo limite. </li><li>**ClientAccountBandwidthThrottlingError**: O pedido é estrangulado na largura de banda para exceder os limites de [escalabilidade da conta](scalability-targets-standard-account.md)de armazenamento .</li><li>**ClientAccountRequestThrottlingError**: O pedido é acelerado na taxa de pedido por exceder [os limites](scalability-targets-standard-account.md)de escalabilidade da conta de armazenamento .<li>**ClientThrottlingError**: Outro erro de estrangulamento do lado do cliente. ClientAccountBandwidthThrottlingError e ClientAccountRequestThrottlingError estão excluídos.</li> <li>**ClientTimeoutError**: Pedido autenticado cronometrado que devolveu um código de estado HTTP 500. Se o tempo limite da rede do cliente ou do pedido estiver definido como um valor inferior ao esperado pelo serviço de armazenamento, trata-se de um tempo limite esperado. Caso contrário, é reportado como um ServerTimeoutError. </li> <li>**ClientOtherError**: Todos os outros erros do lado do cliente, exceto os descritos. </li> <li>**Sucesso**: Pedido bem sucedido</li> <li> **SucessoWithThrottling**: Pedido bem sucedido quando um cliente SMB é estrangulado na primeira tentativa(s) mas consegue após tentativas.</li> |
| **Nome Api** | O nome da operação. Por exemplo: <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> Para todos os nomes da operação, consulte [o documento](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| **Autenticação** | Tipo de autenticação utilizado em transações. Os valores disponíveis incluem: <br/> <li>**Chave de conta**: A transação é autenticada com a chave da conta de armazenamento.</li> <li>**SAS**: A transação é autenticada com assinaturas de acesso partilhado.</li> <li>**OAuth**: A transação é autenticada com fichas de acesso OAuth.</li> <li>**Anónimo**: A transação é solicitada anonimamente. Não inclui pedidos de pré-voo.</li> <li>**AnonymousPreflight**: A transação é pedido de pré-voo.</li> |

Para as dimensões de suporte das métricas, é necessário especificar o valor de dimensão para ver os valores métricos correspondentes. Por exemplo, se olhar para o valor **das Transações** para respostas bem sucedidas, precisa de filtrar a dimensão **ResponseType** com **sucesso**. Ou se olhar para o valor **BlobCount** para Block Blob, precisa de filtrar a dimensão **BlobType** com **BlockBlob**.

## <a name="resource-logs-preview"></a>Registos de recursos (pré-visualização)

> [!NOTE]
> Os registos de armazenamento azure no Monitor Azure estão em pré-visualização pública, e estão disponíveis para testes de pré-visualização em todas as regiões públicas de nuvem. Para se inscrever na pré-visualização, consulte [esta página](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).  Esta pré-visualização permite registos de bolhas (incluindo O Armazenamento de Lagos Azure Gen2), ficheiros, filas, tabelas, contas de armazenamento premium em contas de armazenamento v1 de propósito geral e v2 de uso geral. As contas de armazenamento clássicas não são suportadas.

A tabela seguinte lista as propriedades dos registos de recursos de armazenamento Azure quando são recolhidas em Registos do Monitor Azure ou no Armazenamento Azure. As propriedades descrevem a operação, o serviço e o tipo de autorização que foi usado para realizar a operação.

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

| Propriedade | Descrição |
|:--- |:---|
|**tempo** | O tempo universal coordenado (UTC) quando o pedido foi recebido por armazenamento. Por exemplo: `2018/11/08 21:09:36.6900118`.|
|**recursosId** | A identificação do recurso da conta de armazenamento. Por exemplo: `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**categoria** | A categoria da operação solicitada. Por exemplo: `StorageRead` , `StorageWrite` ou `StorageDelete` .|
|**operaçãoNome** | O tipo de operação REST que foi realizada. <br> Para obter uma lista completa de operações, consulte o tópico de [Operações e Mensagens](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)de Estado do Storage Analytics. |
|**operationVersion** | A versão do serviço de armazenamento que foi especificada quando o pedido foi feito. Isto equivale ao valor do cabeçalho da **versão x-ms.** Por exemplo: `2017-04-17`.|
|**schemaVersion** | A versão do esquema do registo. Por exemplo: `1.0`.|
|**estadoCódigo** | O código de estado HTTP para o pedido. Se o pedido for interrompido, este valor pode ser definido para `Unknown` . <br> Por exemplo: `206` |
|**estadoTexto** | O estado da operação solicitada.  Para obter uma lista completa de mensagens de estado, consulte o tópico de [Operações registadas em Análise de Armazenamento e Mensagens de Estado](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). Na versão 2017-04-17 e, mais tarde, a mensagem de estado `ClientOtherError` não é usada. Em vez disso, este campo contém um código de erro. Por exemplo: `SASSuccess`  |
|**durationMs** | O tempo total, expresso em milissegundos, para realizar a operação solicitada. Isto inclui o tempo para ler o pedido de entrada e para enviar a resposta ao solicitador. Por exemplo: `12`.|
|**callerIpAddress** | O endereço IP do solicitador, incluindo o número da porta. Por exemplo: `192.100.0.102:4362`. |
|**coralaid** | O ID que é usado para correlacionar troncos entre recursos. Por exemplo: `b99ba45e-a01e-0042-4ea6-772bbb000000`. |
|**localização** | A localização da conta de armazenamento. Por exemplo: `North Europe`. |
|**protocolo**|O protocolo que é usado na operação. Por exemplo: `HTTP` `HTTPS` , , `SMB` ou`NFS`|
| **uri** | Identificador de recursos uniformes que é solicitado. Por exemplo: `http://myaccountname.blob.core.windows.net/cont1/blobname?timeout=10`. |

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

| Propriedade | Descrição |
|:--- |:---|
|**identidade / tipo** | O tipo de autenticação que foi usado para fazer o pedido. Por exemplo: `OAuth` `SAS Key` , , `Account Key` ou`Anonymous` |
|**identidade / tokenHash**|Este campo é reservado apenas para uso interno. |
|**autorização / ação** | A ação que está atribuída ao pedido. Por exemplo: `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` |
|**autorização / roleAssignmentId** | A identificação da atribuição de papéis. Por exemplo: `4e2521b7-13be-4363-aeda-111111111111`.|
|**autorização / roleDefinitionId** | A identificação da definição de papel. Por exemplo: `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**principais / id** | A identificação do diretor de segurança. Por exemplo: `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**principais / tipo** | O tipo de diretor de segurança. Por exemplo: `ServicePrincipal`. |
|**requester / appID** | O ID de aplicação de Autorização Aberta (OAuth) que é usado como o solicitador. <br> Por exemplo: `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**requester / público** | A audiência da OAuth do pedido. Por exemplo: `https://storage.azure.com`. |
|**requester / objectId** | A identificação do objeto OAuth do solicitador. No caso de aautenticação kerberos, representa o identificador de objetos de Kerberos autenticado utilizador. Por exemplo: `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**requester / inquilinoId** | A identidade do inquilino da OAuth. Por exemplo: `72f988bf-86f1-41af-91ab-222222222222`.|
|**requester / tokenIssuer** | O emitente simbólico da OAuth. Por exemplo: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**requester / upn** | O Nome Principal do Utilizador (UPN) do solicitor. Por exemplo: `someone@contoso.com`. |
|**requester / userName** | Este campo é reservado apenas para uso interno.|

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

| Propriedade | Descrição |
|:--- |:---|
|**nome da conta** | O nome da conta de armazenamento. Por exemplo: `mystorageaccount`.  |
|**solicitarUrl** | O URL que é solicitado. Por exemplo: `http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10`.|
|**userAgentHeader** | O valor do **cabeçalho user-agent,** em aspas. Por exemplo: `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`.|
|**referrerHeader** | O valor do cabeçalho **do Referrer.** Por exemplo: `http://contoso.com/about.html`.|
|**clientRequestId** | O valor do cabeçalho **x-ms-cliente-request-id** do pedido. Por exemplo: `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6`. |
|**etag** | O identificador ETag para o objeto devolvido, em aspas. Por exemplo: `0x8D101F7E4B662C4`.  |
|**servidorLatencyMs** | O tempo total expresso em milissegundos para realizar a operação solicitada. Este valor não inclui latência da rede (o tempo para ler o pedido de entrada e enviar a resposta ao solicitador). Por exemplo: `22`. |
|**serviçoType** | O serviço associado a este pedido. Por exemplo: `blob` `table` , , ou `files` `queue` . |
|**operaçãoCount** | O número de cada operação registada que está envolvida no pedido. Esta contagem começa com um índice de `0` . Alguns pedidos requerem mais do que uma operação, como um pedido para copiar uma bolha. A maioria dos pedidos realiza apenas uma operação. Por exemplo: `1`. |
|**solicitarHeaderSize** | A dimensão do cabeçalho de pedido expresso em bytes. Por exemplo: `578`. <br>Se um pedido não for bem sucedido, este valor pode estar vazio. |
|**requestBodySize** | A dimensão dos pacotes de pedidos, expressos em bytes, que são lidos pelo serviço de armazenamento. <br> Por exemplo: `0`. <br>Se um pedido não for bem sucedido, este valor pode estar vazio.  |
|**respostaHeaderSize** | O tamanho do cabeçalho de resposta expresso em bytes. Por exemplo: `216`. <br>Se um pedido não for bem sucedido, este valor pode estar vazio.  |
|**respostaBodySize** | O tamanho dos pacotes de resposta escritos pelo serviço de armazenamento, em bytes. Se um pedido não for bem sucedido, este valor pode estar vazio. Por exemplo: `216`.  |
|**solicitarMd5** | O valor do cabeçalho **Content-MD5** ou do cabeçalho **x-ms-content-md5** no pedido. O valor hash MD5 especificado neste campo representa o conteúdo no pedido. Por exemplo: `788815fd0198be0d275ad329cafd1830`. <br>Este campo pode estar vazio.  |
|**servidorMd5** | O valor do hash MD5 calculado pelo serviço de armazenamento. Por exemplo: `3228b3cf1069a5489b298446321f8521`. <br>Este campo pode estar vazio.  |
|**últimoTempo modificado** | O Último Tempo Modificado (LMT) para o objeto devolvido.  Por exemplo: `Tuesday, 09-Aug-11 21:13:26 GMT`. <br>Este campo está vazio para operações que podem devolver vários objetos. |
|**condiçõesUtilizado** | Uma lista separada do ponto evípico de pares de valor-chave que representam uma condição. As condições podem ser qualquer uma das seguintes condições: <li> If-Modificado-Since <li> If-Unmodificad-Since <li> Se-Match <li> Se-Nenhum-Jogo  <br> Por exemplo: `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT`. |
|**conteúdoLengthHeader** | O valor do cabeçalho content-length para o pedido enviado ao serviço de armazenamento. Se o pedido foi bem sucedido, este valor é igual ao pedidoBodySize. Se um pedido não for bem sucedido, este valor pode não ser igual ao pedidoBodySize, ou pode estar vazio. |
|**tlsVersão** | A versão TLS utilizada no âmbito do pedido. Por exemplo: `TLS 1.2`. |
|**smbTreeConnectID** | A árvore do Bloco de Mensagens do Servidor (SMB) **ConnectId** estabelecida no tempo de ligação da árvore. Por exemplo: `0x3` |
|**smbPersistentHandleID** | Id de manuseamento persistente de um pedido SMB2 CREATE que sobrevive a rede reconecta-se.  Referenciado em [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 como **SMB2_FILEID. Persistente.** Por exemplo: `0x6003f` |
|**smbVolatileHandleID** | Id de manuseamento volátil a partir de um pedido SMB2 CREATE que é reciclado em rede reconecta.  Referenciado em [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 como **SMB2_FILEID. Volátil.** Por exemplo: `0xFFFFFFFF00000065` |
|**smbMessageID** | A ligação relativa **MessageId**. Por exemplo: `0x3b165` |
|**smbCreditsConsumido** | A entrada ou saída consumida pelo pedido, em unidades de 64k. Por exemplo: `0x3` |
|**smbCommandDetail** | Mais informações sobre este pedido específico e não sobre o tipo geral de pedido. Por exemplo: `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | O **FileId** associado ao ficheiro ou diretório.  Aproximadamente análogo a um Ficheiro NTFS. Por exemplo: `0x9223442405598953` |
|**smbSessionID** | O **Smb2 SessionId** estabelecido no horário de configuração da sessão. Por exemplo: `0x8530280128000049` |
|**smbCommandMajor uint32** | Valor no **comando SMB2_HEADER.Comando**. Atualmente, este é um número entre 0 e 18 inclusive. Por exemplo: `0x6` |
|**smbCommandMinor** | A subclasse de **SmbCommandMajor,** se for caso disso. Por exemplo: `DirectoryCloseAndDelete` |

## <a name="see-also"></a>Consulte também

- Consulte [o Armazenamento Azure de monitorização](monitor-storage.md) para obter uma descrição do armazenamento azure de monitorização.
- Consulte [a Monitorização dos recursos do Azure com o Monitor Azure](../../azure-monitor/insights/monitor-azure-resource.md) para obter mais informações sobre a monitorização dos recursos do Azure.