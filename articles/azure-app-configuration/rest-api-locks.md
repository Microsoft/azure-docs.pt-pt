---
title: Configuração de aplicativo Azure REST API - Fechaduras
description: Páginas de referência para trabalhar com fechaduras de valor-chave usando a API de Configuração de Aplicação Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 4949db646c54d75f60d29d3c631d0f4ee8d7c26e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424515"
---
# <a name="locks"></a>Bloqueios

versão api: 1.0

Esta API fornece semântica de bloqueio/desbloqueio para o recurso de valor chave. Apoia as seguintes operações:

- Bloqueio de lugar
- Remover fechadura

Se estiver presente, `label` deve ser um valor de etiqueta explícito **(não** um wildcard). Para todas as operações é um parâmetro opcional. Se omitido, não implica qualquer etiqueta.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="lock-key-value"></a>Key-Value de bloqueio

- **Requerido:** ``{key}````{api-version}``  
- *Opcional:*``label``

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

## <a name="unlock-key-value"></a>Desbloqueie Key-Value

- **Requerido:** ``{key}````{api-version}``  
- *Opcional:*``label``

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

## <a name="conditional-lockunlock"></a>Bloqueio condicional/desbloqueio

Para evitar condições de corrida, utilize `If-Match` ou `If-None-Match` solicite cabeçalhos. O `etag` argumento faz parte da representação-chave. Se `If-Match` ou `If-None-Match` forem omitidos, a operação será incondicional.

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
