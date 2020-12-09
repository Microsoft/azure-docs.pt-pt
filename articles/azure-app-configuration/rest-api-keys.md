---
title: Azure App Configuration REST API - Chaves
description: Páginas de referência para trabalhar com teclas usando a API de Configuração de Aplicação Azure
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 65ca190d7fbd6d8d4df473fbe2112eafbd031fde
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932545"
---
# <a name="keys"></a>Chaves

versão api: 1.0

A seguinte sintaxe representa um recurso chave:

```http
{
    "name": [string]             // Name of the key
}
```

## <a name="operations"></a>Operações

Os recursos-chave suportam a seguinte operação:

- Lista

Para todas as operações `name` é um parâmetro de filtro opcional. Se omitido, implica *qualquer* chave.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-keys"></a>Chaves da lista

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**Respostas:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{key-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Paginação

O resultado é paginado se o número de itens devolvidos exceder o limite de resposta. Siga os cabeçalhos de resposta opcionais `Link` e use para `rel="next"` navegação. Em alternativa, o conteúdo fornece um próximo link na forma do `@nextLink` imóvel. O próximo link contém `api-version` parâmetro.

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**Resposta:**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8
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

A filtragem por ```name``` é suportada.

```http
GET /keys?name={key-name}&api-version={api-version}
```

São suportados os seguintes filtros:

|Filtro chave|Efeito|
|--|--|
|`name` é omitido ou `name=*`|Corresponde **a qualquer** chave|
|`name=abc`|Corresponde a uma chave chamada  **ABC**|
|`name=abc*`|Corresponde a nomes-chave que começam com **abc**|
|`name=abc,xyz`|Corresponde a nomes-chave **abc** ou **xyz** (limitado a 5 CSV)|

Os seguintes caracteres são reservados: `*` , `\``,`

Se um carácter reservado faz parte do valor, então deve ser escapado usando `\{Reserved Character}` . Personagens não reservados também podem ser escapados.

## <a name="filter-validation"></a>Validação do filtro

No caso de um erro de validação do filtro, a resposta é HTTP `400` com detalhes de erro:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter 'name'",
  "name": "name",
  "detail": "name(2): Invalid character",
  "status": 400
}
```

## <a name="examples"></a>Exemplos

- Todos

    ```http
    GET /keys?api-version={api-version}
    ```

- O nome chave começa com **abc**

    ```http
    GET  /keys?name=abc*&api-version={api-version}
    ```

- O nome chave é **abc** ou **xyz**

    ```http
    GET /keys?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Solicitar campos específicos

Utilize o parâmetro de cadeia de consulta opcional `$select` e forneça a lista separada de vírgulas dos campos solicitados. Se o `$select` parâmetro for omitido, a resposta contém o conjunto predefinido.

```http
GET /keys?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Acesso Time-Based

Obtenha uma representação do resultado como foi em tempos anteriores. Ver secção [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)

```http
GET /keys&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Resposta:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <relative uri>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```
