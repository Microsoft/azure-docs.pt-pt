---
title: Azure App Configuration REST API - revisões de valor-chave
description: Páginas de referência para trabalhar com revisões de valor-chave utilizando a API de Configuração de Aplicação Azure
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: cfa117d1ed017170c279b7c4e0a146ae4edac108
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932477"
---
# <a name="key-value-revisions"></a>Revisões do valor-chave

Uma *revisão de valor-chave* define a representação histórica de um recurso de valor-chave. As revisões expiram após 7 dias para lojas de nível livre, ou 30 dias para lojas standard. As revisões apoiam a `List` operação.

Para todas as operações, ``key`` é um parâmetro opcional. Se omitido, implica qualquer chave.

Para todas as operações, ``label`` é um parâmetro opcional. Se omitido, implica qualquer etiqueta.

Este artigo aplica-se à versão API 1.0.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-revisions"></a>Revisões da lista

```http
GET /revisions?label=*&api-version={api-version} HTTP/1.1
```

**Respostas:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8"
Accept-Ranges: items
```

```json
{
    "items": [
        {
          "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
          "key": "{key}",
          "label": "{label}",
          "content_type": null,
          "value": "example value",
          "last_modified": "2017-12-05T02:41:26.4874615+00:00",
          "tags": []
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Paginação

O resultado é paginado se o número de itens devolvidos exceder o limite de resposta. Siga o cabeçalho de resposta opcional ``Link`` e use para ``rel="next"`` navegação. Em alternativa, o conteúdo fornece um próximo link na forma do ``@nextLink`` imóvel.

```http
GET /revisions?api-version={api-version} HTTP/1.1
```

**Resposta:**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
Accept-Ranges: items
Link: <{relative uri}>; rel="next"
```

```json
{
    "items": [
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="list-subset-of-revisions"></a>Subconjunto de listas de revisões

Use o cabeçalho do `Range` pedido. A resposta contém um cabeçalho `Content-Range`. Se o servidor não conseguir satisfazer o intervalo solicitado, responde com HTTP `416` `RangeNotSatisfiable` ().

```http
GET /revisions?api-version={api-version} HTTP/1.1
Range: items=0-2
```

**Response**

```http
HTTP/1.1 206 Partial Content
Content-Type: application/vnd.microsoft.appconfig.revs+json; charset=utf-8
Content-Range: items 0-2/80
```

## <a name="filtering"></a>Filtragem

Uma combinação `key` de e `label` filtragem é suportada.
Utilize os parâmetros de cadeia opcionais `key` e `label` de consulta.

```http
GET /revisions?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>Filtros suportados

|Filtro chave|Efeito|
|--|--|
|`key` é omitido ou `key=*`|Corresponde **a qualquer** chave|
|`key=abc`|Corresponde a uma chave chamada  **ABC**|
|`key=abc*`|Corresponde a nomes de chaves que começam com **abc**|
|`key=*abc`|Corresponde aos nomes das teclas que terminam com **o abc**|
|`key=*abc*`|Corresponde aos nomes das chaves que contêm **abc**|
|`key=abc,xyz`|Corresponde aos nomes das teclas **abc** ou **xyz** (limitado a 5 CSV)|

|Filtro de etiqueta|Efeito|
|--|--|
|`label` é omitido ou `label=`|Corresponde à entrada sem etiqueta|
|`label=*`|Corresponde a **qualquer** rótulo|
|`label=prod`|Corresponde ao **rótulo**|
|`label=prod*`|Combina rótulos que começam com **prod**|
|`label=*prod`|Corresponde aos rótulos que terminam com **o prod**|
|`label=*prod*`|Corresponde às etiquetas que contêm **prod**|
|`label=prod,test`|Corresponde a etiquetas **ou** **teste** (limitado a 5 CSV)|

### <a name="reserved-characters"></a>Caracteres reservados

Os caracteres reservados são:

`*`, `\`, `,`

Se um carácter reservado faz parte do valor, então deve ser escapado utilizando `\{Reserved Character}` . Personagens não reservados também podem ser escapados.

### <a name="filter-validation"></a>Validação do filtro

Se ocorrer um erro de validação do filtro, a resposta é HTTP `400` com detalhes de erro:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter '{filter}'",
  "name": "{filter}",
  "detail": "{filter}(2): Invalid character",
  "status": 400
}
```

### <a name="examples"></a>Exemplos

- Todos:

    ```http
    GET /revisions
    ```

- Itens onde o nome chave começa com **abc:**

    ```http
    GET /revisions?key=abc*&api-version={api-version}
    ```

- Itens em que o nome-chave é **abc** ou **xyz,** e as etiquetas contêm **prod:**

    ```http
    GET /revisions?key=abc,xyz&label=*prod*&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Solicitar campos específicos

Utilize o parâmetro de cadeia de consulta opcional `$select` e forneça uma lista separada de vírgulas dos campos solicitados. Se o `$select` parâmetro for omitido, a resposta contém o conjunto predefinido.

```http
GET /revisions?$select=value,label,last_modified&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Acesso baseado no tempo

Obtenha uma representação do resultado como foi em tempos anteriores. Para mais informações, consulte [o Quadro HTTP para Time-Based Acesso aos Estados de Recursos -- Memento,](https://tools.ietf.org/html/rfc7089#section-2.1)secção 2.1.1.

```http
GET /revisions?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Resposta:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.revs+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <{relative uri}>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```
