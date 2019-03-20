---
title: Referência de esquema de webhook de registo de contentor do Azure
description: Webhook pedido JSON payload referência para o Azure Container Registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/05/2019
ms.author: danlep
ms.openlocfilehash: 4c0845b9cf5194ecbd0ab813997e17e070840f44
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58099904"
---
# <a name="azure-container-registry-webhook-reference"></a>Referência do webhook de registo de contentor do Azure

Pode [configurar webhooks](container-registry-webhook.md) para o seu registo de contentor que geram eventos quando determinadas ações são efetuadas em relação a ele. Por exemplo, ative webhooks que são acionados quando uma imagem de contentor ou um gráfico Helm é enviado para um registo ou eliminado. Quando for acionado um webhook, o Azure Container Registry emite um pedido HTTP ou HTTPS que contém informações sobre o evento para um ponto final que especificar. O ponto final, em seguida, pode processar o webhook e aja em conformidade.

As secções seguintes detalham o esquema de pedidos de webhook gerado por eventos suportados. As secções de eventos contêm o esquema do payload para o tipo de evento, um payload de pedido de exemplo e um ou mais comandos de exemplo que irá acionar o webhook.

Para obter informações sobre como configurar webhooks para o seu registo de contentor do Azure, consulte [webhooks de utilizar o Azure Container Registry](container-registry-webhook.md).

## <a name="webhook-requests"></a>Pedidos de Webhook

### <a name="http-request"></a>Pedido HTTP

Um webhook acionado faz com que um HTTP `POST` pedido para o ponto de final do URL que especificou quando configurou o webhook.

### <a name="http-headers"></a>Cabeçalhos HTTP

Pedidos de Webhook incluir uma `Content-Type` dos `application/json` se não tiver especificado um `Content-Type` cabeçalho personalizado para o webhook.

Não existem outros cabeçalhos são adicionados à solicitação além desses cabeçalhos personalizados que pode ter especificado para o webhook.

## <a name="push-event"></a>Evento de push

Acionado quando uma imagem de contentor é emitidos via push para um repositório de Webhook.

### <a name="push-event-payload"></a>Payload de evento de push

|Elemento|Type|Descrição|
|-------------|----------|-----------|
|`id`|String|O ID do evento de webhook.|
|`timestamp`|DateTime|A hora em que o evento de webhook foi acionado.|
|`action`|String|A ação que acionou o evento de webhook.|
|[target](#target)|Tipo complexo|O destino do evento que acionou o evento de webhook.|
|[request](#request)|Tipo complexo|O pedido que gerou o evento de webhook.|

### <a name="target"></a>target

|Elemento|Type|Descrição|
|------------------|----------|-----------|
|`mediaType`|String|O tipo MIME do objeto referenciado.|
|`size`|Int32|O número de bytes do conteúdo. Mesmo que o campo de comprimento.|
|`digest`|String|O resumo do conteúdo, conforme definido pela especificação de API de HTTP do registo V2.|
|`length`|Int32|O número de bytes do conteúdo. Mesmo que o campo de tamanho.|
|`repository`|String|O nome do repositório.|
|`tag`|String|O nome da tag de imagem.|

### <a name="request"></a>Pedido

|Elemento|Type|Descrição|
|------------------|----------|-----------|
|`id`|String|O ID do pedido que deu início ao evento.|
|`host`|String|O nome de anfitrião acessível externamente da instância do Registro, conforme especificado pelo cabeçalho de anfitrião HTTP em solicitações de entrada.|
|`method`|String|O método de pedido que gerou o evento.|
|`useragent`|String|O cabeçalho do agente de utilizador do pedido.|

### <a name="payload-example-image-push-event"></a>Exemplo de payload: eventos de push de imagem

```JSON
{
  "id": "cb8c3971-9adc-488b-xxxx-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-xxxx-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

Exemplo [CLI do Docker](https://docs.docker.com/engine/reference/commandline/cli/) comando que aciona a imagem **push** webhook de evento:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>Evento de push de gráfico

Acionado quando um gráfico do Helm é emitido para um repositório de Webhook.

### <a name="chart-push-event-payload"></a>Payload de evento de push de gráfico

|Elemento|Type|Descrição|
|-------------|----------|-----------|
|`id`|String|O ID do evento de webhook.|
|`timestamp`|DateTime|A hora em que o evento de webhook foi acionado.|
|`action`|String|A ação que acionou o evento de webhook.|
|[target](#helm_target)|Tipo complexo|O destino do evento que acionou o evento de webhook.|

### <a name="helm_target"></a>target

|Elemento|Type|Descrição|
|------------------|----------|-----------|
|`mediaType`|String|O tipo MIME do objeto referenciado.|
|`size`|Int32|O número de bytes do conteúdo.|
|`digest`|String|O resumo do conteúdo, conforme definido pela especificação de API de HTTP do registo V2.|
|`repository`|String|O nome do repositório.|
|`tag`|String|O nome de etiqueta de gráfico.|
|`name`|String|O nome do gráfico.|
|`version`|String|A versão de gráfico.|

### <a name="payload-example-chart-push-event"></a>Exemplo de payload: eventos de push de gráfico

```JSON
{
  "id": "6356e9e0-627f-4fed-xxxx-d9059b5143ac",
  "timestamp": "2019-03-05T23:45:31.2614267Z",
  "action": "chart_push",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Exemplo [CLI do Azure](/cli/azure/acr) comando que aciona a **chart_push** webhook de evento:

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>Eliminar evento

Acionada por um Webhook quando um repositório de imagens ou manifesto é eliminado. Não é acionado quando uma etiqueta é eliminada.

### <a name="delete-event-payload"></a>Eliminar o payload do evento

|Elemento|Type|Descrição|
|-------------|----------|-----------|
|`id`|String|O ID do evento de webhook.|
|`timestamp`|DateTime|A hora em que o evento de webhook foi acionado.|
|`action`|String|A ação que acionou o evento de webhook.|
|[target](#delete_target)|Tipo complexo|O destino do evento que acionou o evento de webhook.|
|[request](#delete_request)|Tipo complexo|O pedido que gerou o evento de webhook.|

### <a name="delete_target"></a> Destino

|Elemento|Type|Descrição|
|------------------|----------|-----------|
|`mediaType`|String|O tipo MIME do objeto referenciado.|
|`digest`|String|O resumo do conteúdo, conforme definido pela especificação de API de HTTP do registo V2.|
|`repository`|String|O nome do repositório.|

### <a name="delete_request"></a> Pedido

|Elemento|Type|Descrição|
|------------------|----------|-----------|
|`id`|String|O ID do pedido que deu início ao evento.|
|`host`|String|O nome de anfitrião acessível externamente da instância do Registro, conforme especificado pelo cabeçalho de anfitrião HTTP em solicitações de entrada.|
|`method`|String|O método de pedido que gerou o evento.|
|`useragent`|String|O cabeçalho do agente de utilizador do pedido.|

### <a name="payload-example-image-delete-event"></a>Exemplo de payload: evento de eliminação de imagem

```JSON
{
    "id": "afc359ce-df7f-4e32-xxxx-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-xxxx-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

Exemplo [CLI do Azure](/cli/azure/acr) comandos nesse disparador um **eliminar** webhook de evento:

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>Evento de eliminação de gráfico

Acionada por um Webhook quando um gráfico do Helm ou repositório é eliminado. 

### <a name="chart-delete-event-payload"></a>Payload de evento de eliminação de gráfico

|Elemento|Type|Descrição|
|-------------|----------|-----------|
|`id`|String|O ID do evento de webhook.|
|`timestamp`|DateTime|A hora em que o evento de webhook foi acionado.|
|`action`|String|A ação que acionou o evento de webhook.|
|[target](#chart_delete_target)|Tipo complexo|O destino do evento que acionou o evento de webhook.|

### <a name="chart_delete_target"></a> Destino

|Elemento|Type|Descrição|
|------------------|----------|-----------|
|`mediaType`|String|O tipo MIME do objeto referenciado.|
|`size`|Int32|O número de bytes do conteúdo.|
|`digest`|String|O resumo do conteúdo, conforme definido pela especificação de API de HTTP do registo V2.|
|`repository`|String|O nome do repositório.|
|`tag`|String|O nome de etiqueta de gráfico.|
|`name`|String|O nome do gráfico.|
|`version`|String|A versão de gráfico.|

### <a name="payload-example-chart-delete-event"></a>Exemplo de payload: evento de eliminação de gráfico

```JSON
{
  "id": "338a3ef7-ad68-4128-xxxx-fdd3af8e8f67",
  "timestamp": "2019-03-06T00:10:48.1270754Z",
  "action": "chart_delete",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Exemplo [CLI do Azure](/cli/azure/acr) comando que aciona a **chart_delete** webhook de evento:

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>Passos Seguintes

[Utilizar webhooks de registo de contentor do Azure](container-registry-webhook.md)