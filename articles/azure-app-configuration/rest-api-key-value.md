---
title: Azure App Configuration REST API - Key-Value
description: Páginas de referência para trabalhar com valores-chave usando a API de Configuração de Aplicação Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 50d97a330507e9361674776acf29d1007ee5bf58
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424137"
---
# <a name="key-values"></a>Key-Values

versão api: 1.0

Um valor-chave é um recurso identificado por uma combinação única de `key`  +  `label` . `label` é opcional. Para referir explicitamente um valor-chave sem uma etiqueta use "\0" (url codificado como ``%00`` ). Consulte os detalhes de cada operação.

## <a name="operations"></a>Operações

- Get
- Lista múltiplo
- Definir
- Eliminar

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="syntax"></a>Syntax

```json
{
  "etag": [string],
  "key": [string],
  "label": [string, optional],
  "content_type": [string, optional],
  "value": [string],
  "last_modified": [datetime ISO 8601],
  "locked": [boolean],
  "tags": [object with string properties, optional]
}
```

## <a name="get-key-value"></a>Obter Key-Value

**Requerido:** ``{key}````{api-version}``  
*Opcional:* ``label`` - Se omitido, implica um valor-chave sem rótulo

```http
GET /kv/{key}?label={label}&api-version={api-version}
```

**Respostas:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26+00:00",
  "locked": "false",
  "tags": {
    "t1": "value1",
    "t2": "value2"
  }
}
```

Se a chave não existir, a seguinte resposta é devolvida:

```http
HTTP/1.1 404 Not Found
```

## <a name="get-conditionally"></a>Obter (Condicionalmente)

Para melhorar o caching do cliente, use `If-Match` ou `If-None-Match` solicite cabeçalhos. O `etag` argumento faz parte da representação-chave. Ver [as secções 14.24 e 14.26](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

O seguinte pedido só recupera o valor-chave se a representação atual não corresponder à `etag` especificada:

```http
GET /kv/{key}?api-version={api-version} HTTP/1.1
Accept: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "{etag}"
```

**Respostas:**

```http
HTTP/1.1 304 NotModified
```

ou

```http
HTTP/1.1 200 OK
```

## <a name="list-key-values"></a>Key-Values de lista

Ver **Filtragem** para opções adicionais

*Opcional:* ``key`` - se não especificado, implica **qualquer** chave.
*Opcional:* ``label`` - se não for especificado, implica **qualquer** etiqueta.

```http
GET /kv?label=*&api-version={api-version} HTTP/1.1
```

**Resposta:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8
```

## <a name="pagination"></a>Paginação

O resultado é paginado se o número de itens devolvidos exceder o limite de resposta. Siga os cabeçalhos de resposta opcionais `Link` e use para `rel="next"` navegação.
Em alternativa, o conteúdo fornece um próximo link na forma da `@nextLink` propriedade. O uri ligado inclui o `api-version` argumento.

```http
GET /kv?api-version={api-version} HTTP/1.1
```

**Resposta:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
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

## <a name="filtering"></a>Filtragem

Uma combinação `key` de e `label` filtragem é suportada.
Utilize os parâmetros de cadeia opcionais `key` e `label` de consulta.

```http
GET /kv?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>Filtros suportados

|Filtro chave|Efeito|
|--|--|
|`key` é omitido ou `key=*`|Corresponde **a qualquer** chave|
|`key=abc`|Corresponde a uma chave chamada **ABC**|
|`key=abc*`|Corresponde a nomes de chaves que começam com **abc**|
|`key=abc,xyz`|Corresponde aos nomes das teclas **abc** ou **xyz** (limitado a 5 CSV)|

|Filtro de etiqueta|Efeito|
|--|--|
|`label` é omitido ou `label=*`|Corresponde a **qualquer** rótulo|
|`label=%00`|Corresponde kV sem etiqueta|
|`label=prod`|Corresponde ao **rótulo**|
|`label=prod*`|Combina rótulos que começam com **prod**|
|`label=prod,test`|Corresponde a etiquetas **ou** **teste** (limitado a 5 CSV)|

**_Caracteres reservados_* _

`_`, `\`, `,`

Se um carácter reservado faz parte do valor, então deve ser escapado usando `\{Reserved Character}` . Personagens não reservados também podem ser escapados.

***Validação do filtro** _

No caso de um erro de validação do filtro, a resposta é HTTP `400` com detalhes de erro:

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

_ *Exemplos**

- Todos

    ```http
    GET /kv?api-version={api-version}
    ```

- O nome chave começa com **abc** e inclui todos os rótulos

    ```http
    GET /kv?key=abc*&label=*&api-version={api-version}
    ```

- O nome chave começa com **abc** e rótulo é igual **a v1** ou **v2**

    ```http
    GET /kv?key=abc*&label=v1,v2&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Solicitar campos específicos

Utilize o parâmetro de cadeia de consulta opcional `$select` e forneça a lista separada de vírgulas dos campos solicitados. Se o `$select` parâmetro for omitido, a resposta contém o conjunto predefinido.

```http
GET /kv?$select=key,value&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Acesso Time-Based

Obtenha uma representação do resultado como foi em tempos anteriores. Consulte a secção [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1). A paginação ainda é apoiada como definido acima.

```http
GET /kv?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Resposta:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json"
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

## <a name="set-key"></a>Chave de conjunto

- **Requerido:**``{key}``
- *Opcional:* ``label`` - se não for especificado ou rotulado=%00, implica KV sem etiqueta.

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
```

```json
{
  "value": "example value",         // optional
  "content_type": "user defined",   // optional
  "tags": {                         // optional
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

**Respostas:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": "user defined",
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26.4874615+00:00",
  "tags": {
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

Se o artigo estiver bloqueado, receberá a seguinte resposta:

```http
HTTP/1.1 409 Conflict
Content-Type: application/problem+json; charset="utf-8"
```

```json
{
    "type": "https://azconfig.io/errors/key-locked",
    "title": "Modifing key '{key}' is not allowed",
    "name": "{key}",
    "detail": "The key is read-only. To allow modification unlock it first.",
    "status": "409"
}
```

## <a name="set-key-conditionally"></a>Chave de conjunto (condicionalmente)

Para evitar condições de corrida, utilize `If-Match` ou `If-None-Match` solicite cabeçalhos. O `etag` argumento faz parte da representação-chave.
Se `If-Match` ou `If-None-Match` forem omitidos, a operação será incondicional.

A resposta seguinte atualiza o valor apenas se a representação atual corresponder à especificada `etag`

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

A resposta seguinte atualiza o valor apenas se a representação atual *não* corresponder à especificada `etag`

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

O seguinte pedido só acrescenta o valor se já existir uma representação:

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-Match: "*"
```

O seguinte pedido só acrescenta o valor se uma representação já *não* existir:

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-None-Match: "*"
```

**Respostas**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

ou

```http
HTTP/1.1 412 PreconditionFailed
```

## <a name="delete"></a>Eliminar

- **Requerido:** `{key}``{api-version}`
- *Opcional:* `{label}` - se não for especificado ou rotulado=%00, implica KV sem etiqueta.

```http
DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**Resposta:** Devolva o valor-chave apagado, ou nenhum se o valor-chave não existisse.

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

ou

```http
HTTP/1.1 204 No Content
```

## <a name="delete-key-conditionally"></a>Eliminar chave (condicionalmente)

Semelhante à **chave de conjunto (condicionalmente)**
