---
title: Esquema de eventos de registo de contentor de grelha de eventos do Azure
description: Descreve as propriedades que são fornecidas para eventos de registo de contentor com o Azure Event Grid
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 03/12/2019
ms.author: spelluru
ms.openlocfilehash: c5998ff428c4b6f4c1f7a4087c6ccb27d93773eb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58084332"
---
# <a name="azure-event-grid-event-schema-for-container-registry"></a>Esquema de eventos do Azure Event Grid para o registo de contentor

Este artigo fornece as propriedades e o esquema para eventos de registo de contentor. Para obter uma introdução aos esquemas de eventos, consulte [esquema de eventos do Azure Event Grid](event-schema.md).

## <a name="available-event-types"></a>Tipos de eventos disponíveis

O Azure Container Registry emite os seguintes tipos de evento:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.ContainerRegistry.ImagePushed | Gerado quando é enviada por push uma imagem. |
| Microsoft.ContainerRegistry.ImageDeleted | Desencadeado quando uma imagem é eliminada. |
| Microsoft.ContainerRegistry.ChartPushed | Desencadeado quando um gráfico do Helm é emitido. |
| Microsoft.ContainerRegistry.ChartDeleted | Desencadeado quando um gráfico do Helm é eliminado. |

## <a name="example-event"></a>Evento de exemplo

O exemplo seguinte mostra o esquema de uma imagem que enviou o evento: 

```json
[{
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld:v1",
  "eventType": "ImagePushed",
  "eventTime": "2018-04-25T21:39:47.6549614Z",
  "data": {
    "id": "31c51664-e5bd-416a-a5df-e5206bc47ed0",
    "timestamp": "2018-04-25T21:39:47.276585742Z",
    "action": "push",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 3023,
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "length": 3023,
      "repository": "aci-helloworld",
      "tag": "v1"
    },
    "request": {
      "id": "7c66f28b-de19-40a4-821c-6f5f6c0003a4",
      "host": "demo.azurecr.io",
      "method": "PUT",
      "useragent": "docker/18.03.0-ce go/go1.9.4 git-commit/0520e24 os/windows arch/amd64 UpstreamClient(Docker-Client/18.03.0-ce \\\\(windows\\\\))"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

O esquema para um evento de imagem eliminado é semelhante:

```json
[{
  "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld",
  "eventType": "ImageDeleted",
  "eventTime": "2018-04-26T17:56:01.8211268Z",
  "data": {
    "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
    "timestamp": "2018-04-26T17:56:00.996603117Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "repository": "aci-helloworld"
    },
    "request": {
      "id": "aeda5b99-4197-409f-b8a8-ff539edb7de2",
      "host": "demo.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

O esquema para um gráfico que enviou o evento é semelhante para o esquema para um evento enviada por push com imagem duplicada, mas ele não inclui um objeto de solicitação:

```json
[{
  "id": "ea3a9c28-5b17-40f6-a500-3f02b6829277",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartPushed",
  "eventTime": "2019-03-12T22:16:31.5164086Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:16:31.0087496+00:00",
    "action":"chart_push",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

O esquema para um evento de gráfico eliminado é semelhante para o esquema para um evento eliminado imagem, mas ele não inclui um objeto de solicitação:

```json
[{
  "id": "39136b3a-1a7e-416f-a09e-5c85d5402fca",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartDeleted",
  "eventTime": "019-03-12T22:42:08.7034064Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:42:08.3783775+00:00",
    "action":"chart_delete",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| tópico | string | Caminho de recurso completo para a origem do evento. Este campo não é gravável. Event Grid fornece este valor. |
| assunto | string | Caminho definidos pelo publicador para o assunto de evento. |
| eventType | string | Um dos tipos de eventos registrados para esta origem de evento. |
| eventTime | string | O tempo que o evento é gerado com base no fuso horário UTC do fornecedor. |
| ID | string | Identificador exclusivo para o evento. |
| dados | objeto | Dados de eventos de armazenamento de Blobs. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. Grelha de eventos define o esquema das propriedades de nível superior. Event Grid fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| ID | string | O ID de evento. |
| carimbo de data/hora | string | A hora em que ocorreu o evento. |
| action | string | A ação que abrange o evento fornecido. |
| destino | objeto | O destino do evento. |
| pedido | objeto | O pedido que gerou o evento. |

O objeto de destino tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| mediaType | string | O tipo MIME do objeto referenciado. |
| tamanho | inteiro | O número de bytes do conteúdo. Mesmo que o campo de comprimento. |
| Resumo | string | O resumo do conteúdo, conforme definido pela especificação de API de HTTP do registo V2. |
| Comprimento | inteiro | O número de bytes do conteúdo. Mesmo que o campo de tamanho. |
| Repositório | string | O nome do repositório. |
| etiqueta | string | O nome da etiqueta. |
| nome | string | O nome do gráfico. |
| versão | string | A versão de gráfico. |

O objeto de solicitação tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| ID | string | O ID do pedido que deu início ao evento. |
| addr | string | O IP ou nome de anfitrião e, possivelmente, porta da ligação de cliente que deu início ao evento. Este valor é o RemoteAddr no pedido de http padrão. |
| anfitrião | string | O nome de anfitrião acessível externamente da instância do Registro, conforme especificado pelo cabeçalho de anfitrião http em solicitações de entrada. |
| método | string | O método de pedido que gerou o evento. |
| useragent | string | O cabeçalho do agente de utilizador do pedido. |

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma introdução ao Azure Event Grid, consulte [o que é o Event Grid?](overview.md)
* Para obter mais informações sobre a criação de uma subscrição do Azure Event Grid, veja [esquema de subscrições do Event Grid](subscription-creation-schema.md).
