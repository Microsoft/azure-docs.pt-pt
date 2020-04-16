---
title: Use a Grelha de Eventos Azure com eventos em esquema CloudEvents
description: Descreve como usar o esquema CloudEvents para eventos em Azure Event Grid. O serviço suporta eventos na implementação da JSON de Cloud Events.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 127095bef2c67a93097bf90bea54ca1b44b16c58
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394387"
---
# <a name="cloudevents-v10-schema-with-event-grid"></a>CloudEvents v1.0 schema com grelha de eventos

Além do seu [esquema de evento padrão,](event-schema.md)a Azure Event Grid apoia de forma nativa eventos na [implementação jSON de CloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) e [http protocol binding](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) é uma [especificação aberta](https://github.com/cloudevents/spec/blob/v1.0/spec.md) para descrever dados do evento.

A CloudEvents simplifica a interoperabilidade fornecendo um esquema comum de eventos para a publicação e consumindo eventos baseados em nuvem. Este esquema permite uma ferramenta uniforme, formas padrão de encaminhamento & lidar com eventos, e formas universais de desserialização do esquema de eventos exteriores. Com um esquema comum, pode integrar mais facilmente o trabalho em todas as plataformas.

A CloudEvents está a ser construída por [vários colaboradores](https://github.com/cloudevents/spec/blob/master/community/contributors.md), incluindo a Microsoft, através da [Cloud Native Computing Foundation](https://www.cncf.io/). Atualmente está disponível como versão 1.0.

Este artigo descreve o esquema CloudEvents com a Grelha de Eventos.

## <a name="sample-event-using-cloudevents-schema"></a>Evento de amostra usando esquema CloudEvents

Aqui está um exemplo de um evento de armazenamento De Blob Azure no formato CloudEvents:

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

Uma descrição detalhada dos campos disponíveis, seus tipos e definições em CloudEvents v1.0 está [disponível aqui](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Os valores dos cabeçalhos para eventos entregues no esquema CloudEvents e `content-type`no esquema da Grelha de Eventos são os mesmos exceto para . Para o esquema CloudEvents, o `"content-type":"application/cloudevents+json; charset=utf-8"`valor do cabeçalho é . Para o esquema da Grelha de `"content-type":"application/json; charset=utf-8"`Eventos, o valor do cabeçalho é .

## <a name="event-grid-for-cloudevents"></a>Grelha de eventos para CloudEvents

Pode utilizar a Grelha de Eventos tanto para a entrada como para a saída de eventos em esquema CloudEvents. Você pode usar CloudEvents para eventos de sistema, como eventos de armazenamento blob e eventos IoT Hub, e eventos personalizados. Também pode transformar esses eventos no fio de um lado para o outro.


| Input schema       | Esquema de saída
|--------------------|---------------------
| Formato CloudEvents | Formato CloudEvents
| Formato Da Grelha de Eventos  | Formato CloudEvents
| Formato Da Grelha de Eventos  | Formato Da Grelha de Eventos

Para todos os eventos, a Grelha de Eventos requer validação ao publicar para um tópico de grelha de eventos e ao criar uma subscrição de eventos. Para mais informações, consulte [a segurança e a autenticação da Rede de Eventos.](security-authentication.md)

## <a name="next-steps"></a>Passos seguintes
Ver [Como utilizar cloudEvents v1.0 schema com Grelha](cloudevents-schema.md)de Eventos .  
