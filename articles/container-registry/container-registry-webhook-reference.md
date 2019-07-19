---
title: Referência de esquema de webhook do registro de contêiner do Azure
description: Referência de carga JSON de solicitação de webhook para registro de contêiner do Azure.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 03/05/2019
ms.author: danlep
ms.openlocfilehash: fcdee2be92f2a3052e2ebbfaab3a2f9cb96e0125
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311605"
---
# <a name="azure-container-registry-webhook-reference"></a>Referência de webhook do registro de contêiner do Azure

Você pode [Configurar](container-registry-webhook.md) WebHooks para o registro de contêiner que geram eventos quando determinadas ações são executadas em relação a ele. Por exemplo, habilite WebHooks que são disparados quando uma imagem de contêiner ou gráfico Helm é enviado para um registro ou excluído. Quando um webhook é disparado, o registro de contêiner do Azure emite uma solicitação HTTP ou HTTPS que contém informações sobre o evento para um ponto de extremidade que você especificar. Seu ponto de extremidade pode processar o webhook e agir de acordo.

As seções a seguir detalham o esquema de solicitações de webhook geradas por eventos com suporte. As seções de evento contêm o esquema de carga para o tipo de evento, um exemplo de carga de solicitação e um ou mais comandos de exemplo que dispararão o webhook.

Para obter informações sobre como configurar WebHooks para o registro de contêiner do Azure, consulte [usando WebHooks do registro de contêiner do Azure](container-registry-webhook.md).

## <a name="webhook-requests"></a>Solicitações de webhook

### <a name="http-request"></a>Solicitação HTTP

Um webhook disparado faz `POST` uma solicitação HTTP para o ponto de extremidade da URL que você especificou quando configurou o webhook.

### <a name="http-headers"></a>Cabeçalhos HTTP

As solicitações de webhook `Content-Type` incluem `application/json` um de se você não especificou um `Content-Type` cabeçalho personalizado para o webhook.

Nenhum outro cabeçalho é adicionado à solicitação além dos cabeçalhos personalizados que você pode ter especificado para o webhook.

## <a name="push-event"></a>Evento de push

Webhook disparado quando uma imagem de contêiner é enviada por push para um repositório.

### <a name="push-event-payload"></a>Carga do evento de push

|Elemento|Type|Descrição|
|-------------|----------|-----------|
|`id`|Cadeia|A ID do evento de webhook.|
|`timestamp`|DateTime|A hora em que o evento de webhook foi disparado.|
|`action`|Cadeia|A ação que disparou o evento de webhook.|
|[alvo](#target)|Tipo complexo|O destino do evento que disparou o evento de webhook.|
|[Quest](#request)|Tipo complexo|A solicitação que gerou o evento de webhook.|

### <a name="target"></a>alvo

|Elemento|Type|Descrição|
|------------------|----------|-----------|
|`mediaType`|Cadeia|O tipo MIME do objeto referenciado.|
|`size`|Int32|O número de bytes do conteúdo. O mesmo que o campo de comprimento.|
|`digest`|Cadeia|O resumo do conteúdo, conforme definido pela especificação da API HTTP do registro v2.|
|`length`|Int32|O número de bytes do conteúdo. O mesmo que o campo tamanho.|
|`repository`|Cadeia|O nome do repositório.|
|`tag`|Cadeia|O nome da marca da imagem.|

### <a name="request"></a>Quest

|Elemento|Type|Descrição|
|------------------|----------|-----------|
|`id`|Cadeia|A ID da solicitação que iniciou o evento.|
|`host`|Cadeia|O nome de host acessível externamente da instância do registro, conforme especificado pelo cabeçalho de host HTTP em solicitações de entrada.|
|`method`|Cadeia|O método de solicitação que gerou o evento.|
|`useragent`|Cadeia|O cabeçalho do agente do usuário da solicitação.|

### <a name="payload-example-image-push-event"></a>Exemplo de carga: evento de push de imagem

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

Exemplo de comando da [CLI](https://docs.docker.com/engine/reference/commandline/cli/) do Docker que dispara o webhook do evento de **Push** de imagem:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>Evento de push de gráfico

Webhook disparado quando um gráfico Helm é enviado por push para um repositório.

### <a name="chart-push-event-payload"></a>Conteúdo do evento de push de gráfico

|Elemento|Type|Descrição|
|-------------|----------|-----------|
|`id`|Cadeia|A ID do evento de webhook.|
|`timestamp`|DateTime|A hora em que o evento de webhook foi disparado.|
|`action`|Cadeia|A ação que disparou o evento de webhook.|
|[alvo](#helm_target)|Tipo complexo|O destino do evento que disparou o evento de webhook.|

### <a name="helm_target"></a>alvo

|Elemento|Type|Descrição|
|------------------|----------|-----------|
|`mediaType`|Cadeia|O tipo MIME do objeto referenciado.|
|`size`|Int32|O número de bytes do conteúdo.|
|`digest`|Cadeia|O resumo do conteúdo, conforme definido pela especificação da API HTTP do registro v2.|
|`repository`|Cadeia|O nome do repositório.|
|`tag`|Cadeia|O nome da marca do gráfico.|
|`name`|Cadeia|O nome do gráfico.|
|`version`|Cadeia|A versão do gráfico.|

### <a name="payload-example-chart-push-event"></a>Exemplo de carga: evento de push de gráfico

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

Exemplo [CLI do Azure](/cli/azure/acr) comando que dispara o webhook do evento **chart_push** :

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>Excluir evento

Webhook disparado quando um repositório ou manifesto de imagem é excluído. Não disparado quando uma marca é excluída.

### <a name="delete-event-payload"></a>Excluir carga do evento

|Elemento|Type|Descrição|
|-------------|----------|-----------|
|`id`|Cadeia|A ID do evento de webhook.|
|`timestamp`|DateTime|A hora em que o evento de webhook foi disparado.|
|`action`|Cadeia|A ação que disparou o evento de webhook.|
|[alvo](#delete_target)|Tipo complexo|O destino do evento que disparou o evento de webhook.|
|[Quest](#delete_request)|Tipo complexo|A solicitação que gerou o evento de webhook.|

### <a name="delete_target"></a>alvo

|Elemento|Type|Descrição|
|------------------|----------|-----------|
|`mediaType`|Cadeia|O tipo MIME do objeto referenciado.|
|`digest`|Cadeia|O resumo do conteúdo, conforme definido pela especificação da API HTTP do registro v2.|
|`repository`|Cadeia|O nome do repositório.|

### <a name="delete_request"></a>Quest

|Elemento|Type|Descrição|
|------------------|----------|-----------|
|`id`|Cadeia|A ID da solicitação que iniciou o evento.|
|`host`|Cadeia|O nome de host acessível externamente da instância do registro, conforme especificado pelo cabeçalho de host HTTP em solicitações de entrada.|
|`method`|Cadeia|O método de solicitação que gerou o evento.|
|`useragent`|Cadeia|O cabeçalho do agente do usuário da solicitação.|

### <a name="payload-example-image-delete-event"></a>Exemplo de carga: evento de exclusão de imagem

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

Exemplo [CLI do Azure](/cli/azure/acr) comandos que disparam um webhook de evento de **exclusão** :

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>Evento de exclusão de gráfico

Webhook disparado quando um gráfico ou repositório Helm é excluído. 

### <a name="chart-delete-event-payload"></a>Carga do evento de exclusão de gráfico

|Elemento|Type|Descrição|
|-------------|----------|-----------|
|`id`|Cadeia|A ID do evento de webhook.|
|`timestamp`|DateTime|A hora em que o evento de webhook foi disparado.|
|`action`|Cadeia|A ação que disparou o evento de webhook.|
|[alvo](#chart_delete_target)|Tipo complexo|O destino do evento que disparou o evento de webhook.|

### <a name="chart_delete_target"></a>alvo

|Elemento|Type|Descrição|
|------------------|----------|-----------|
|`mediaType`|Cadeia|O tipo MIME do objeto referenciado.|
|`size`|Int32|O número de bytes do conteúdo.|
|`digest`|Cadeia|O resumo do conteúdo, conforme definido pela especificação da API HTTP do registro v2.|
|`repository`|Cadeia|O nome do repositório.|
|`tag`|Cadeia|O nome da marca do gráfico.|
|`name`|Cadeia|O nome do gráfico.|
|`version`|Cadeia|A versão do gráfico.|

### <a name="payload-example-chart-delete-event"></a>Exemplo de carga: evento de exclusão de gráfico

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

Exemplo [CLI do Azure](/cli/azure/acr) comando que dispara o webhook do evento **chart_delete** :

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>Passos Seguintes

[Usando WebHooks do registro de contêiner do Azure](container-registry-webhook.md)