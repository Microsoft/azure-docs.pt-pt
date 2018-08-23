---
title: Reagir a eventos de armazenamento de Blobs do Azure | Documentos da Microsoft
description: Utilize a Azure Event Grid para subscrever a eventos de armazenamento de Blobs.
services: storage,event-grid
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.component: blobs
ms.openlocfilehash: d38ab71ed2d2ebff04004f02589cfccca4199318
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2018
ms.locfileid: "42060960"
---
# <a name="reacting-to-blob-storage-events"></a>Reagir a eventos de armazenamento de BLOBs

Eventos de armazenamento do Azure permitem que os aplicativos reagir para a criação e eliminação de blobs com modernas arquiteturas sem servidor. Ele faz isso sem a necessidade de código complicado ou serviços de consulta dispendiosa e ineficiente.  Em vez disso, os eventos são enviados por meio [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) para os assinantes, tal como [as funções do Azure](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), ou até mesmo para o seu próprio serviço de escuta de http personalizado e apenas pague apenas aquilo que utiliza. 

Cenários de evento de armazenamento de BLOBs comuns incluem o processamento de vídeo ou imagem, a indexação da pesquisa ou qualquer fluxo de trabalho orientados por arquivos.  Carregamentos de ficheiros assíncronos são uma excelente escolha para eventos.  Quando as alterações são pouco frequentes, mas o seu cenário requer a capacidade de resposta imediata, com base em eventos arquitetura pode ser especialmente eficiente.

Dê uma olhada [eventos de armazenamento de BLOBs de rota para um personalizado da web endpoint - CLI](storage-blob-event-quickstart.md) ou [eventos de armazenamento de BLOBs de rota para um personalizado da web endpoint - PowerShell](storage-blob-event-quickstart-powershell.md) um exemplo rápido. 

![Modelo de grade do evento](./media/storage-blob-event-overview/event-grid-functional-model.png)

## <a name="blob-storage-accounts"></a>Contas do Blob Storage
Eventos de armazenamento de BLOBs estão disponíveis na [contas de armazenamento de BLOBs](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) e, na [contas de armazenamento para fins gerais v2](../common/storage-account-options.md#general-purpose-v2-accounts). As contas de **Fins Gerais v2 (GPv2)** são contas de armazenamento que suportam todas as funcionalidades de todos os serviços de armazenamento, incluindo Blobs, Ficheiros, Filas e Tabelas. Uma **conta de armazenamento de Blobs** é uma conta de armazenamento especializada para armazenar os seus dados não estruturados como blobs (objetos) no Armazenamento do Azure. As contas de armazenamento de Blobs são semelhantes às contas de armazenamento para fins gerais e partilham todas as excelentes características de durabilidade, disponibilidade, escalabilidade e desempenho que utiliza atualmente, incluindo 100% de consistência com a API dos blobs de blocos e dos blobs de acréscimo. Para aplicações que requerem apenas armazenamento de blobs de blocos ou de blobs de acréscimo, recomendamos a utilização das contas de armazenamento de Blobs. 

## <a name="available-blob-storage-events"></a>Eventos de armazenamento de BLOBs disponíveis
Grelha de eventos usa [subscrições de eventos](../../event-grid/concepts.md#event-subscriptions) encaminhar mensagens de eventos para os assinantes.  Subscrições de eventos de armazenamento de BLOBs podem incluir dois tipos de eventos:  

> |Nome do Evento|Descrição|
> |----------|-----------|
> |`Microsoft.Storage.BlobCreated`|Acionado quando um blob é criado ou substituído por meio da `PutBlob`, `PutBlockList`, ou `CopyBlob` operações|
> |`Microsoft.Storage.BlobDeleted`|Acionado quando um blob é eliminado por meio de um `DeleteBlob` operação|

## <a name="event-schema"></a>Esquema de Eventos
Eventos de armazenamento de BLOBs contenham todas as informações que necessárias para responder a alterações nos seus dados.  É possível identificar um evento de armazenamento de BLOBs, porque a propriedade eventType começa com "Microsoft". Informações adicionais sobre a utilização das propriedades de eventos do Event Grid estão documentadas no [esquema de eventos do Event Grid](../../event-grid/event-schema.md).  

> |Propriedade|Tipo|Descrição|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |tópico|cadeia|Id do Azure Resource Manager completo da conta de armazenamento que emite o evento.|
> |Assunto|cadeia|O caminho relativo do recurso para o objeto que é o assunto do evento, usando o mesmo do Azure Resource Manager formato expandida que utilizamos para descrever as contas de armazenamento, serviços e contentores do RBAC do Azure.  Este formato inclui um nome de blob de preservação de caso.|
> |eventTime|cadeia|Data/hora que o evento foi gerado, no formato ISO 8601|
> |eventType|cadeia|"Microsoft.Storage.BlobCreated" ou "Microsoft.Storage.BlobDeleted"|
> |Id|cadeia|Identificador exclusivo, se este evento|
> |dataVersion|cadeia|A versão do esquema do objeto de dados.|
> |metadataVersion|cadeia|A versão do esquema das propriedades de nível superior.|
> |dados|objeto|Recolha de dados de eventos específico do armazenamento de BLOBs|
> |data.contentType|cadeia|O tipo de conteúdo do blob, como seria retornado no cabeçalho de tipo de conteúdo do blob|
> |data.contentLength|número|O tamanho do blob como no número inteiro que representa um número de bytes, como seria retornado no cabeçalho Content-Length de blob.  Enviado com o evento de BlobCreated, mas não com BlobDeleted.|
> |data.url|cadeia|O url do objeto que é o assunto do evento|
> |data.eTag|cadeia|A etag do objeto quando este evento disparado.  Não está disponível para o evento de BlobDeleted.|
> |data.api|cadeia|O nome da operação de api que disparou o evento. Para eventos de BlobCreated, este valor é "PutBlob", "PutBlockList" ou "CopyBlob". Para eventos de BlobDeleted, este valor é "DeleteBlob". Estes valores são os mesmos nomes de api que estão presentes nos registos de diagnóstico do armazenamento do Azure. Ver [registados operações e mensagens de estado](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).|
> |data.sequencer|cadeia|Um valor de cadeia opaca que representa a sequência lógica de eventos para qualquer nome de determinado blob.  Os utilizadores podem utilizar a comparação de cadeias de caracteres padrão para compreender a sequência relativa de dois eventos sobre o mesmo nome de blob.|
> |data.requestId|cadeia|Id do pedido geradas pelo serviço para a operação de API de armazenamento. Pode ser utilizado para correlacionar ao armazenamento do Azure registos com o campo de "cabeçalho de id de pedido" nos registos de diagnóstico e é devolvido de iniciar a chamada de API no cabeçalho "x-ms-pedido-id". Ver [formato de registo](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format).|
> |data.clientRequestId|cadeia|Id do pedido fornecido pelo cliente para o armazenamento de operação de API. Pode ser utilizado para correlacionar para registos de diagnóstico de armazenamento do Azure com o campo de "client-request-id" nos registos e pode ser fornecida nos pedidos de cliente com o cabeçalho "x-ms-client-request-id". Ver [formato de registo](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
> |data.storageDiagnostics|objeto|Dados de diagnóstico, ocasionalmente, incluídos pelo serviço de armazenamento do Azure. Quando estiver presente, deve ser ignorado pelos consumidores de eventos.|
|data.blobType|cadeia|O tipo de blob. Valores válidos são "BlockBlob" ou "PageBlob".| 

Eis um exemplo de um evento de BlobCreated:
```json
[{
  "topic": "/subscriptions/319a9601-1ec0-0000-aebc-8fe82724c81e/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file1.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T01:57:26.005121Z",
  "id": "602a88ef-0001-00e6-1233-1646070610ea",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "799304a4-bbc5-45b6-9849-ec2c66be800a",
    "requestId": "602a88ef-0001-00e6-1233-164607000000",
    "eTag": "0x8D4E44A24ABE7F1",
    "contentType": "text/plain",
    "contentLength": 447,
    "blobType": "BlockBlob",
    "url": "https://myaccount.blob.core.windows.net/testcontainer/file1.txt",
    "sequencer": "00000000000000EB000000000000C65A",
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

Para obter mais informações, consulte [esquema de eventos de armazenamento de BLOBs](../../event-grid/event-schema-blob-storage.md).

## <a name="filtering-events"></a>Filtragem de eventos
Subscrições de eventos de blob podem ser filtradas com base no tipo de evento e, pelo nome do contentor e o nome de blob do objeto que foi criado ou eliminado.  Pode aplicar os filtros para subscrições de eventos ou durante a [criação](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create) a subscrição de evento ou [num momento posterior](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update). Filtros de requerente em projetos do Event Grid com base no "começa com" e "termina com" correspondências, para que os eventos com um assunto correspondente são entregues ao subscritor. 

O assunto de eventos de armazenamento de BLOBs utiliza o formato:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Para fazer corresponder a todos os eventos para uma conta de armazenamento, pode deixar os filtros de requerente vazio.

Para fazer corresponder os eventos a partir de blobs criados num conjunto de contentores partilha um prefixo, utilize um `subjectBeginsWith` filtrar, como:

```
/blobServices/default/containers/containerprefix
```

Para fazer corresponder os eventos a partir de blobs criados no contentor específico, utilize um `subjectBeginsWith` filtrar, como:

```
/blobServices/default/containers/containername/
```

Para fazer corresponder os eventos a partir de blobs criados no contentor específico partilha um prefixo de nome de blob, utilize um `subjectBeginsWith` filtrar, como:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Para fazer corresponder os eventos a partir de blobs criados no contentor específico partilha um sufixo de blob, utilize um `subjectEndsWith` filtro como ". log" ou ". jpg". Para obter mais informações, consulte [conceitos do Event Grid](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Práticas recomendadas para o consumo de eventos
Aplicações que processam os eventos de armazenamento de BLOBs devem seguir algumas práticas recomendadas:
> [!div class="checklist"]
> * Como várias subscrições podem ser configuradas para encaminhar eventos para o mesmo manipulador de eventos, é importante não partem do princípio de eventos são de uma origem específica, mas para verificar o tópico da mensagem para se certificar de que trata da conta de armazenamento que está esperando.
> * Da mesma forma, verifique se o eventType é um estão preparados para o processo e não partem do princípio de que todos os eventos recebidos serão os tipos esperados.
> * Como as mensagens podem chegar fora de ordem e, depois de algum atraso, utilize os campos de etag para saber se as informações sobre objetos são ainda atualizadas.  Além disso, utilize os campos do sequenciador para compreender a ordem dos eventos em qualquer objeto específico.
> * Utilize o campo de blobType para compreender o tipo das operações são permitidos no blob e que biblioteca de cliente tipos que devem utilizar para aceder ao blob. Os valores válidos são `BlockBlob` ou `PageBlob`. 
> * Utilize o campo de url com o `CloudBlockBlob` e `CloudAppendBlob` construtores para aceder ao blob.
> * Ignore campos que não compreende. Essa prática ajuda a manter-se resiliente aos novos recursos que podem ser adicionados no futuro.


## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o Event Grid e dê uma chance de eventos de armazenamento de BLOBs:

- [Sobre o Event Grid](../../event-grid/overview.md)
- [Encaminhar eventos de armazenamento de BLOBs para um ponto final web personalizado](storage-blob-event-quickstart.md)
