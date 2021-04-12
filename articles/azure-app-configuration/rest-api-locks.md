---
title: Azure App Configuration REST API - fechaduras
description: Páginas de referência para trabalhar com fechaduras de valor-chave utilizando a API de Configuração de Aplicação Azure
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: e99ce5595bae8ed64285317d9249da60e0fc1b83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932528"
---
# <a name="locks"></a>Bloqueios

Esta API (versão 1.0) fornece semântica de bloqueio e desbloqueio para o recurso de valor-chave. Apoia as seguintes operações:

- Bloqueio de lugar
- Remover fechadura

Se estiver presente, `label` deve ser um valor de etiqueta explícito (não um wildcard). Para todas as operações, é um parâmetro opcional. Se omitido, não implica qualquer etiqueta.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="lock-key-value"></a>Valor-chave de bloqueio

- Requerido: ``{key}````{api-version}``  
- Opcional: ``label``

```http
PUT /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**Respostas:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

Se o valor-chave não existir, a seguinte resposta é devolvida:

```http
HTTP/1.1 404 Not Found
```

## <a name="unlock-key-value"></a>Desbloquear valor-chave

- Requerido: ``{key}````{api-version}``  
- Opcional: ``label``

```http
DELETE /locks/{key}?label={label}?api-version={api-version} HTTP/1.1
```

**Respostas:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

Se o valor-chave não existir, a seguinte resposta é devolvida:

```http
HTTP/1.1 404 Not Found
```

## <a name="conditional-lock-and-unlock"></a>Bloqueio condicional e desbloqueio

Para evitar condições de corrida, utilize `If-Match` ou `If-None-Match` solicite cabeçalhos. O `etag` argumento faz parte da representação-chave. Se `If-Match` ou `If-None-Match` forem omitidos, a operação é incondicional.

O seguinte pedido só aplica a operação se a representação do valor-chave atual corresponder à `etag` especificada:

```http
PUT|DELETE /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

O seguinte pedido só aplica a operação se a representação atual do valor-chave existir, mas não corresponder à `etag` especificada:

```http
PUT|DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```
