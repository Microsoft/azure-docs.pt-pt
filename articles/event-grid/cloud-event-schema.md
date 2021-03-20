---
title: CloudEvents v1.0 schema com Azure Event Grid
description: Descreve como usar o esquema CloudEvents v1.0 para eventos em Azure Event Grid. O serviço suporta eventos na implementação json de Eventos cloud.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: d17e92c28784ca31f3c9809c93e885b22c6a38d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91324183"
---
# <a name="cloudevents-v10-schema-with-azure-event-grid"></a>CloudEvents v1.0 schema com Azure Event Grid

Além do seu [esquema de eventos predefinidos,](event-schema.md)a Azure Event Grid suporta de forma nativa eventos na [implementação JSON de cloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) e [protocolo HTTP.](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md) [CloudEvents](https://cloudevents.io/) é uma [especificação aberta](https://github.com/cloudevents/spec/blob/v1.0/spec.md) para descrever dados de eventos.

O CloudEvents simplifica a interoperabilidade, fornecendo um esquema comum de eventos para a publicação e consumindo eventos baseados na nuvem. Este esquema permite uma ferramenta uniforme, formas padrão de encaminhamento & eventos de manuseamento, e formas universais de desseeciar o esquema do evento exterior. Com um esquema comum, pode integrar mais facilmente o trabalho através das plataformas.

O CloudEvents está a ser construído por [vários colaboradores](https://github.com/cloudevents/spec/blob/master/community/contributors.md), incluindo a Microsoft, através da [Cloud Native Computing Foundation.](https://www.cncf.io/) Atualmente está disponível como versão 1.0.

Este artigo descreve o esquema do CloudEvents com a Grade de Eventos.

## <a name="sample-event-using-cloudevents-schema"></a>Evento de amostra usando o esquema do CloudEvents

Aqui está um exemplo de um evento de Armazenamento Azure Blob no formato CloudEvents:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

Uma descrição detalhada dos campos disponíveis, seus tipos e definições em CloudEvents v1.0 está [disponível aqui.](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)

Os valores dos cabeçalhos para os eventos entregues no esquema cloudEvents e no esquema da Grelha de Eventos são os mesmos, exceto `content-type` . Para o esquema cloudEvents, o valor do cabeçalho é `"content-type":"application/cloudevents+json; charset=utf-8"` . Para o esquema da Grelha de Eventos, o valor do cabeçalho é `"content-type":"application/json; charset=utf-8"` .

## <a name="event-grid-for-cloudevents"></a>Grelha de eventos para Eventos CloudEvents

Você pode usar a Grade de Eventos tanto para a entrada como para a saída de eventos no esquema do CloudEvents. Você pode usar Eventos CloudEvents para eventos do sistema, como eventos blob storage e eventos IoT Hub, e eventos personalizados. Também pode transformar esses eventos no fio para trás e para a frente.


| Esquema de entrada       | Esquema de saída
|--------------------|---------------------
| Formato CloudEvents | Formato CloudEvents
| Formato de grelha de eventos  | Formato CloudEvents
| Formato de grelha de eventos  | Formato de grelha de eventos

Para todos os esquemas de eventos, o Event Grid requer validação ao publicar um tópico de grelha de eventos e ao criar uma subscrição de eventos. Para mais informações, consulte [a segurança e a autenticação da Grade de Eventos.](security-authentication.md)

## <a name="next-steps"></a>Passos seguintes
Ver [como utilizar o esquema cloudEvents v1.0 com grade de eventos](cloudevents-schema.md).  
