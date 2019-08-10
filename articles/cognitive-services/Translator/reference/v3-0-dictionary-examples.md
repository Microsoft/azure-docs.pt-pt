---
title: Método de exemplos de dicionário de API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Use o método de exemplos de dicionário API de Tradução de Texto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: 419e11862b2c584686922cfc8d1db72ee4751a03
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934025"
---
# <a name="translator-text-api-30-dictionary-examples"></a>API de Tradução de Texto 3,0: Exemplos do dicionário

Fornece exemplos que mostram como os termos no dicionário são usados no contexto. Esta operação é usada em conjunto com a [pesquisa de dicionário](./v3-0-dictionary-lookup.md).

## <a name="request-url"></a>URL do Pedido

Enviar uma `POST` solicitação para:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros do pedido

Os parâmetros de solicitação passados na cadeia de caracteres de consulta são:

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>Descrição</th>
  <tr>
    <td>versão de API</td>
    <td>*Parâmetro obrigatório*.<br/>Versão da API solicitada pelo cliente. O valor deve `3.0`ser.</td>
  </tr>
  <tr>
    <td>from</td>
    <td>*Parâmetro obrigatório*.<br/>Especifica o idioma do texto de entrada. O idioma de origem deve ser um dos [idiomas com suporte](./v3-0-languages.md) incluídos no `dictionary` escopo.</td>
  </tr>
  <tr>
    <td>para</td>
    <td>*Parâmetro obrigatório*.<br/>Especifica o idioma do texto de saída. O idioma de destino deve ser um dos [idiomas com suporte](./v3-0-languages.md) incluídos no `dictionary` escopo.</td>
  </tr>
</table>

Os cabeçalhos de solicitação incluem:

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
  <tr>
    <td>Cabeçalho (s) de autenticação</td>
    <td><em>Cabeçalho de solicitação necessário</em>.<br/>Consulte <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">as opções disponíveis para autenticação</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Cabeçalho de solicitação necessário*.<br/>Especifica o tipo de conteúdo da carga. Os valores possíveis são `application/json`:.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Cabeçalho de solicitação necessário*.<br/>O comprimento do corpo da solicitação.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Opcional*.<br/>Um GUID gerado pelo cliente para identificar exclusivamente a solicitação. Você pode omitir esse cabeçalho se incluir a ID de rastreamento na cadeia de caracteres de consulta usando um `ClientTraceId`parâmetro de consulta denominado.</td>
  </tr>
</table> 

## <a name="request-body"></a>Corpo do pedido

O corpo da solicitação é uma matriz JSON. Cada elemento da matriz é um objeto JSON com as seguintes propriedades:

  * `Text`: Uma cadeia de caracteres que especifica o termo a ser pesquisado. Esse deve ser o valor de um `normalizedText` campo das traduções posteriores de uma solicitação de [pesquisa de dicionário](./v3-0-dictionary-lookup.md) anterior. Ele também pode ser o valor do `normalizedSource` campo.

  * `Translation`: Uma cadeia de caracteres que especifica o texto traduzido retornado anteriormente pela operação de [pesquisa de dicionário](./v3-0-dictionary-lookup.md) . Esse deve ser o valor do `normalizedTarget` campo `translations` na lista da resposta de pesquisa de [dicionário](./v3-0-dictionary-lookup.md) . O serviço retornará exemplos para o par de palavras de destino de origem específico.

Um exemplo é:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

As seguintes limitações se aplicam:

* A matriz pode ter no máximo 10 elementos.
* O valor de texto de um elemento de matriz não pode exceder 100 caracteres, incluindo espaços.

## <a name="response-body"></a>Corpo da resposta

Uma resposta bem-sucedida é uma matriz JSON com um resultado para cada cadeia de caracteres na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `normalizedSource`: Uma cadeia de caracteres que oferece a forma normalizada do termo de origem. Em geral, isso deve ser idêntico ao valor do `Text` campo no índice da lista de correspondência no corpo da solicitação.
    
  * `normalizedTarget`: Uma cadeia de caracteres que oferece a forma normalizada do termo de destino. Em geral, isso deve ser idêntico ao valor do `Translation` campo no índice da lista de correspondência no corpo da solicitação.
  
  * `examples`: Uma lista de exemplos para o par (termo de origem, termo de destino). Cada elemento da lista é um objeto com as seguintes propriedades:

    * `sourcePrefix`: A cadeia de caracteres a ser concatenada antes `sourceTerm` do valor de para formar um exemplo completo. Não adicione um caractere de espaço, pois ele já está lá, quando deveria estar. Esse valor pode ser uma cadeia de caracteres vazia.

    * `sourceTerm`: Uma cadeia de caracteres igual ao termo real procurado. A cadeia de caracteres é `sourcePrefix` adicionada `sourceSuffix` com e para formar o exemplo completo. Seu valor é separado para que possa ser marcado em uma interface do usuário, por exemplo, colocando-o em negrito.

    * `sourceSuffix`: A cadeia de caracteres a ser concatenada após `sourceTerm` o valor de para formar um exemplo completo. Não adicione um caractere de espaço, pois ele já está lá, quando deveria estar. Esse valor pode ser uma cadeia de caracteres vazia.

    * `targetPrefix`: Uma cadeia de caracteres `sourcePrefix` semelhante a, mas para o destino.

    * `targetTerm`: Uma cadeia de caracteres `sourceTerm` semelhante a, mas para o destino.

    * `targetSuffix`: Uma cadeia de caracteres `sourceSuffix` semelhante a, mas para o destino.

    > [!NOTE]
    > Se não houver nenhum exemplo no dicionário, a resposta será 200 (OK), mas a `examples` lista será uma lista vazia.

## <a name="examples"></a>Exemplos

Este exemplo mostra como pesquisar exemplos para o par composto pelo termo `fly` em inglês e sua tradução `volar`de espanhol.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

O corpo da resposta (abreviado para maior clareza) é:

```
[
    {
        "normalizedSource":"fly",
        "normalizedTarget":"volar",
        "examples":[
            {
                "sourcePrefix":"They need machines to ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Necesitan máquinas para ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },      
            {
                "sourcePrefix":"That should really ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Eso realmente debe ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```
