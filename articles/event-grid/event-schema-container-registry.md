---
title: Registo de contentores azure como fonte da Rede de Eventos
description: Descreve as propriedades que estão fornecidas para eventos de registo de contentores com grelha de eventos Azure
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: 7e33feb04edf42f1e2a32b9b8c8e2fd214692f31
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393352"
---
# <a name="azure-container-registry-as-an-event-grid-source"></a>Registo de contentores azure como fonte da Rede de Eventos

Este artigo fornece as propriedades e esquemas para eventos de registo de contentores.Para uma introdução aos eventos schemas, consulte [o evento Azure Event Grid schema](event-schema.md).

## <a name="event-grid-event-schema"></a>Esquema de eventos do Event Grid

### <a name="available-event-types"></a>Tipos de eventos disponíveis

O Registo de Contentores Azure emite os seguintes tipos de eventos:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.ContainerRegistry.ImagePushed | Levantada quando uma imagem é empurrada. |
| Microsoft.ContainerRegistry.ImageDeleted | Levantada quando uma imagem é apagada. |
| Microsoft.ContainerRegistry.ChartPushed | Levantado quando um gráfico helm é empurrado. |
| Microsoft.ContainerRegistry.ChartDeleted | Levantado quando um gráfico helm é apagado. |

### <a name="example-event"></a>Evento de exemplo

O exemplo que se segue mostra o esquema de um evento empurrado por imagem: 

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

O esquema para um evento apagado de imagem é semelhante:

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

O esquema para um evento empurrado por gráficos é semelhante ao esquema para um evento filmado, mas não inclui um objeto de pedido:

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

O esquema para um evento eliminado por gráfico é semelhante ao esquema para um evento apagado de imagem, mas não inclui um objeto de pedido:

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

### <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de alto nível:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| tópico | string | Caminho de recursos completos para a fonte do evento. Este campo não é repreensível. O Event Grid fornece este valor. |
| Assunto | string | Caminho definido pelo publicador para o assunto do evento. |
| eventType | string | Um dos tipos de eventos registados para esta origem de evento. |
| eventTime | string | O tempo que o evento é gerado com base no tempo UTC do fornecedor. |
| ID | string | Identificador único para o evento. |
| data | objeto | Dados do evento de armazenamento blob. |
| dataVersion | string | A versão do esquema do objeto de dados. O publicador define a versão do esquema. |
| metadataVersion | string | A versão do esquema dos metadados do evento. O Event Grid define o esquema das propriedades de nível superior. O Event Grid fornece este valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| ID | string | A identificação do evento. |
| carimbo de data/hora | string | O momento em que ocorreu o evento. |
| action | string | A ação que engloba o evento previsto. |
| alvo | objeto | O alvo do evento. |
| pedido | objeto | O pedido que gerou o evento. |

O objeto-alvo tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| mediaType | string | O tipo MIME do objeto referenciado. |
| size | número inteiro | O número de bytes do conteúdo. O mesmo que o campo length. |
| digest | string | A digestão do conteúdo, tal como definida pela especificação do Registo V2 HTTP API. |
| length | número inteiro | O número de bytes do conteúdo. O mesmo que o campo size. |
| repositório | string | O nome do repositório. |
| etiqueta | string | O nome da etiqueta. |
| nome | string | O nome da ficha. |
| versão | string | A versão do gráfico. |

O objeto de pedido tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| ID | string | A identificação do pedido que iniciou o evento. |
| addr | string | O nome IP ou anfitrião e, possivelmente, o porto da ligação ao cliente que iniciou o evento. Este valor é o RemoteAddr a partir do pedido de http padrão. |
| anfitrião | string | O nome de anfitrião acessível externamente da instância de registo, conforme especificado pelo cabeçalho do anfitrião http nos pedidos de entrada. |
| método | string | O método de pedido que gerou o evento. |
| useragent | string | O cabeçalho do agente utilizador do pedido. |

## <a name="tutorials-and-how-tos"></a>Tutorials and how-tos (Tutoriais e procedimentos)
|Título |Descrição  |
|---------|---------|
| [Quickstart: envie eventos de registo de contentores](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Mostra como usar o Azure CLI para enviar eventos de registo de contentores. |


## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à Grelha de Eventos Azure, veja [o que é a Grelha de Eventos?](overview.md)
* Para mais informações sobre a criação de uma subscrição da Rede de Eventos Do Evento, consulte o esquema de subscrição da [Rede de Eventos](subscription-creation-schema.md).
