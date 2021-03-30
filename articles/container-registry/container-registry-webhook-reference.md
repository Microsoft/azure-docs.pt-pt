---
title: Referência de esquema webhook de registo
description: Referência para a carga útil JSON para pedidos de webhook num registo de contentores Azure, que são gerados quando os webhooks são ativados para empurrar ou apagar eventos
ms.topic: article
ms.date: 03/05/2019
ms.openlocfilehash: 8354ef9db24d5825238155ac567d5d829f9b0d7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "74455971"
---
# <a name="azure-container-registry-webhook-reference"></a>Referência webhook do registo de contentores Azure

Pode [configurar webhooks](container-registry-webhook.md) para o seu registo de contentores que geram eventos quando certas ações são realizadas contra ele. Por exemplo, ative os webhooks que são acionados quando uma imagem de contentor ou gráfico helm é empurrado para um registo, ou eliminado. Quando um webhook é acionado, o Registo do Contentor Azure emite um pedido HTTP ou HTTPS contendo informações sobre o evento para um ponto final que especifique. O seu ponto final pode então processar o webhook e agir em conformidade.

As secções seguintes detalham o esquema dos pedidos do webhook gerados por eventos suportados. As secções do evento contêm o esquema de carga útil para o tipo de evento, um exemplo de pedido de carga útil, e um ou mais comandos de exemplo que desencadeariam o webhook.

Para obter informações sobre a configuração de webhooks para o seu registo de contentores Azure, consulte [webhooks do Registo de Contentores Azure](container-registry-webhook.md).

## <a name="webhook-requests"></a>Pedidos webhook

### <a name="http-request"></a>Pedido HTTP

Um webhook desencadeado faz um pedido HTTP `POST` para o ponto final url especificado quando configura o webhook.

### <a name="http-headers"></a>Cabeçalhos HTTP

Os pedidos webhook incluem um `Content-Type` de se não tiver especificado um `application/json` `Content-Type` cabeçalho personalizado para o seu webhook.

Nenhum outro cabeçalho é adicionado ao pedido para além dos cabeçalhos personalizados que pode ter especificado para o webhook.

## <a name="push-event"></a>Evento push

Webhook desencadeado quando uma imagem de recipiente é empurrada para um repositório.

### <a name="push-event-payload"></a>Empurrar carga útil do evento

|Elemento|Tipo|Description|
|-------------|----------|-----------|
|`id`|String|A identificação do evento webhook.|
|`timestamp`|DateTime|O momento em que o evento webhook foi desencadeado.|
|`action`|String|A ação que desencadeou o evento webhook.|
|[alvo](#target)|Tipo Complexo|O alvo do evento que desencadeou o evento webhook.|
|[pedido](#request)|Tipo Complexo|O pedido que gerou o evento webhook.|

### <a name="target"></a><a name="target"></a>alvo

|Elemento|Tipo|Description|
|------------------|----------|-----------|
|`mediaType`|String|O tipo MIME do objeto referenciado.|
|`size`|Int32|O número de bytes do conteúdo. O mesmo que o campo De Comprimento.|
|`digest`|String|A digestão do conteúdo, tal como definido pela Especificação API HTTP do Registo V2.|
|`length`|Int32|O número de bytes do conteúdo. O mesmo que o campo Size.|
|`repository`|String|O nome do repositório.|
|`tag`|String|O nome da etiqueta de imagem.|

### <a name="request"></a><a name="request"></a>pedido

|Elemento|Tipo|Description|
|------------------|----------|-----------|
|`id`|String|A identificação do pedido que iniciou o evento.|
|`host`|String|O nome de anfitrião acessível externamente da instância de registo, conforme especificado pelo cabeçalho do anfitrião HTTP sobre os pedidos de entrada.|
|`method`|String|O método de pedido que gerou o evento.|
|`useragent`|String|O cabeçalho do agente de utilizador do pedido.|

### <a name="payload-example-image-push-event"></a>Exemplo de carga útil: evento de impulso de imagem

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

Exemplo Do comando [Do Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) que desencadeia o webhook do evento **de impulso** de imagem:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>Evento de push de gráficos

Webhook desencadeado quando um gráfico helm é empurrado para um repositório.

### <a name="chart-push-event-payload"></a>Carga útil do evento push de gráfico

|Elemento|Tipo|Description|
|-------------|----------|-----------|
|`id`|String|A identificação do evento webhook.|
|`timestamp`|DateTime|O momento em que o evento webhook foi desencadeado.|
|`action`|String|A ação que desencadeou o evento webhook.|
|[alvo](#helm_target)|Tipo Complexo|O alvo do evento que desencadeou o evento webhook.|

### <a name="target"></a><a name="helm_target"></a>alvo

|Elemento|Tipo|Description|
|------------------|----------|-----------|
|`mediaType`|String|O tipo MIME do objeto referenciado.|
|`size`|Int32|O número de bytes do conteúdo.|
|`digest`|String|A digestão do conteúdo, tal como definido pela Especificação API HTTP do Registo V2.|
|`repository`|String|O nome do repositório.|
|`tag`|String|O nome da etiqueta de gráfico.|
|`name`|String|O nome da carta.|
|`version`|String|A versão do gráfico.|

### <a name="payload-example-chart-push-event"></a>Exemplo de carga útil: evento de push de gráficos

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

Exemplo O comando [CLI do Azure](/cli/azure/acr) que desencadeia o webhook do evento **chart_push:**

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>Excluir evento

Webhook desencadeado quando um repositório de imagem ou manifesto é eliminado. Não é acionado quando uma etiqueta é apagada.

### <a name="delete-event-payload"></a>Eliminar carga útil do evento

|Elemento|Tipo|Description|
|-------------|----------|-----------|
|`id`|String|A identificação do evento webhook.|
|`timestamp`|DateTime|O momento em que o evento webhook foi desencadeado.|
|`action`|String|A ação que desencadeou o evento webhook.|
|[alvo](#delete_target)|Tipo Complexo|O alvo do evento que desencadeou o evento webhook.|
|[pedido](#delete_request)|Tipo Complexo|O pedido que gerou o evento webhook.|

### <a name="target"></a><a name="delete_target"></a> alvo

|Elemento|Tipo|Description|
|------------------|----------|-----------|
|`mediaType`|String|O tipo MIME do objeto referenciado.|
|`digest`|String|A digestão do conteúdo, tal como definido pela Especificação API HTTP do Registo V2.|
|`repository`|String|O nome do repositório.|

### <a name="request"></a><a name="delete_request"></a> pedido

|Elemento|Tipo|Description|
|------------------|----------|-----------|
|`id`|String|A identificação do pedido que iniciou o evento.|
|`host`|String|O nome de anfitrião acessível externamente da instância de registo, conforme especificado pelo cabeçalho do anfitrião HTTP sobre os pedidos de entrada.|
|`method`|String|O método de pedido que gerou o evento.|
|`useragent`|String|O cabeçalho do agente de utilizador do pedido.|

### <a name="payload-example-image-delete-event"></a>Exemplo de carga útil: evento de eliminação de imagem

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

Exemplo [Azure CLI](/cli/azure/acr) comandos que desencadeiam um webhook de eventos **de exclusão:**

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>Evento de eliminação de gráficos

Webhook desencadeado quando um gráfico de Helm ou repositório é eliminado. 

### <a name="chart-delete-event-payload"></a>Gráfico eliminar carga útil do evento

|Elemento|Tipo|Description|
|-------------|----------|-----------|
|`id`|String|A identificação do evento webhook.|
|`timestamp`|DateTime|O momento em que o evento webhook foi desencadeado.|
|`action`|String|A ação que desencadeou o evento webhook.|
|[alvo](#chart_delete_target)|Tipo Complexo|O alvo do evento que desencadeou o evento webhook.|

### <a name="target"></a><a name="chart_delete_target"></a> alvo

|Elemento|Tipo|Description|
|------------------|----------|-----------|
|`mediaType`|String|O tipo MIME do objeto referenciado.|
|`size`|Int32|O número de bytes do conteúdo.|
|`digest`|String|A digestão do conteúdo, tal como definido pela Especificação API HTTP do Registo V2.|
|`repository`|String|O nome do repositório.|
|`tag`|String|O nome da etiqueta de gráfico.|
|`name`|String|O nome da carta.|
|`version`|String|A versão do gráfico.|

### <a name="payload-example-chart-delete-event"></a>Exemplo de carga útil: evento de eliminação de gráficos

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

Exemplo O comando [CLI do Azure](/cli/azure/acr) que desencadeia o webhook do evento **chart_delete:**

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>Passos seguintes

[Usando webhooks de registo de contentores Azure](container-registry-webhook.md)