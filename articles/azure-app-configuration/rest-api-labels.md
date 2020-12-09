---
title: Azure App Configuration REST API - Etiquetas
description: Páginas de referência para trabalhar com etiquetas usando a API de Configuração de Aplicação Azure
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 5a59f5910d44f2a2b4cd75e7a1d51c2ed5dd51a4
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932511"
---
# <a name="labels"></a>Etiquetas

versão api: 1.0

O recurso **Label** é definido da seguinte forma:

```json
{
      "name": [string]             // Name of the label
}
```

Suporta as seguintes operações:

- Lista

Para todas as operações ``name`` é um parâmetro de filtro opcional. Se omitido, implica **qualquer** etiqueta.

## <a name="prerequisites"></a>Pré-requisitos

- Todos os pedidos HTTP devem ser autenticados. Consulte a secção [de autenticação.](./rest-api-authentication-index.md)
- Todos os pedidos HTTP devem fornecer `api-version` explicito. Consulte a secção [de versão.](./rest-api-versioning.md)

## <a name="list-labels"></a>Etiquetas de lista

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**Respostas:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{label-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Paginação

O resultado é paginado se o número de itens devolvidos exceder o limite de resposta. Siga os cabeçalhos de resposta opcionais `Link` e use para `rel="next"` navegação. Em alternativa, o conteúdo fornece um próximo link na forma da `@nextLink` propriedade. O próximo link contém `api-version` parâmetro.

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**Resposta:**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8
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

## <a name="filtering"></a>Filtragem

A filtragem por `name` é suportada.

```http
GET /labels?name={label-name}&api-version={api-version}
```

### <a name="supported-filters"></a>Filtros suportados

|Filtro chave|Efeito|
|--|--|
|`name` é omitido ou `name=*`|Corresponde a **qualquer** rótulo|
|`name=abc`|Corresponde a uma etiqueta chamada  **abc**|
|`name=abc*`|Corresponde a nomes de etiquetas que começam com **abc**|
|`name=abc,xyz`|Corresponde aos nomes da etiqueta **abc** ou **xyz** (limitado a 5 CSV)|

### <a name="reserved-characters"></a>Caracteres reservados

`*`, `\`, `,`

Se um carácter reservado faz parte do valor, então deve ser escapado usando `\{Reserved Character}` . Personagens não reservados também podem ser escapados.

### <a name="filter-validation"></a>Validação do filtro

Se ocorrer um erro de validação do filtro, a resposta é HTTP `400` com detalhes de erro:

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

### <a name="examples"></a>Exemplos

- Todos

    ```http
    GET /labels?api-version={api-version}
    ```

- O nome da etiqueta começa com **abc**

    ```http
    GET  /labels?name=abc*&api-version={api-version}
    ```

- O nome da etiqueta é **abc** ou **xyz**

    ```http
    GET /labels?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Solicitar campos específicos

Utilize o parâmetro de cadeia de consulta opcional `$select` e forneça a lista separada de vírgulas dos campos solicitados. Se o `$select` parâmetro for omitido, a resposta contém o conjunto predefinido.

```http
GET /labels?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Acesso Time-Based

Obtenha uma representação do resultado como foi em tempos anteriores. Ver secção [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)

```http
GET /labels&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Resposta:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json"
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
