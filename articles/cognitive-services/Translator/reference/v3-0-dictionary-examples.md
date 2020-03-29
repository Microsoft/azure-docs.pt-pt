---
title: Método de exemplos de dicionário de texto de tradutor aPI
titleSuffix: Azure Cognitive Services
description: O método de exemplos de dicionário de texto tradutor api fornece exemplos que mostram como os termos no dicionário são usados no contexto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: b3551a8df19e47178c7bacd9218cfa60b66d81f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76548072"
---
# <a name="translator-text-api-30-dictionary-examples"></a>Texto tradutor API 3.0: Exemplos de dicionário

Fornece exemplos que mostram como os termos no dicionário são usados no contexto. Esta operação é utilizada em conjunto com a procura do [Dicionário](./v3-0-dictionary-lookup.md).

## <a name="request-url"></a>URL do Pedido

Envie `POST` um pedido para:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros de pedido

Os parâmetros de pedido transmitidos na corda de consulta são:

| Parâmetro de consulta | Descrição |
| --------- | ----------- |
| api-version <img width=200/> | **Parâmetro necessário**.<br/>Versão da API solicitada pelo cliente. O valor `3.0`deve ser. |
| De | **Parâmetro necessário**.<br/>Especifica a linguagem do texto de entrada. A língua de origem deve ser uma das `dictionary` [línguas apoiadas incluídas](./v3-0-languages.md) no âmbito. |
| para | **Parâmetro necessário**.<br/>Especifica a linguagem do texto de saída. A língua-alvo deve ser uma das [línguas apoiadas incluídas](./v3-0-languages.md) no `dictionary` âmbito.  | 

Os cabeçalhos de pedido incluem:

| Cabeçalhos  | Descrição |
| ------ | ----------- |
| Cabeçalho de autenticação <img width=200/>  | **Cabeçalho de pedido exigido**.<br/>Consulte <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">as opções disponíveis para autenticação.</a> |
| Content-Type | **Cabeçalho de pedido exigido**.<br/>Especifica o tipo de conteúdo da carga útil. Os valores `application/json`possíveis são: . |
| Comprimento do conteúdo   | **Cabeçalho de pedido exigido**.<br/>O comprimento do corpo de pedido. |
| X-ClientTraceid   | **Opcional.**<br/>Um GUID gerado pelo cliente para identificar exclusivamente o pedido. Pode omitir este cabeçalho se incluir o id de rastreio na `ClientTraceId`corda de consulta usando um parâmetro de consulta chamado . |

## <a name="request-body"></a>Corpo do pedido

O corpo do pedido é uma matriz JSON. Cada elemento matriz é um objeto JSON com as seguintes propriedades:

  * `Text`: Uma cadeia que especifica o termo para procurar. Este deve ser o `normalizedText` valor de um campo a partir das traduções traseiras de um pedido de [procuração](./v3-0-dictionary-lookup.md) do Dicionário anterior. Também pode ser o `normalizedSource` valor do campo.

  * `Translation`: Uma cadeia que especifica o texto traduzido anteriormente devolvido pela operação de procuração do [Dicionário.](./v3-0-dictionary-lookup.md) Este deve ser o `normalizedTarget` valor `translations` do campo na lista da resposta de procura do [Dicionário.](./v3-0-dictionary-lookup.md) O serviço devolverá exemplos para o par de palavras específico-alvo de origem.

Um exemplo é:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Aplicam-se as seguintes limitações:

* A matriz pode ter no máximo 10 elementos.
* O valor de texto de um elemento matriz não pode exceder 100 caracteres, incluindo espaços.

## <a name="response-body"></a>Corpo da resposta

Uma resposta bem sucedida é um array JSON com um resultado para cada corda na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `normalizedSource`: Uma corda que dá a forma normalizada do termo fonte. Geralmente, este deve ser idêntico `Text` ao valor do campo no índice da lista correspondente no organismo do pedido.
    
  * `normalizedTarget`: Uma corda que dá a forma normalizada do termo-alvo. Geralmente, este deve ser idêntico `Translation` ao valor do campo no índice da lista correspondente no organismo do pedido.
  
  * `examples`: Uma lista de exemplos para o par (termo-alvo de origem). Cada elemento da lista é um objeto com as seguintes propriedades:

    * `sourcePrefix`: A corda para _before_ concatenar `sourceTerm` antes do valor de formar um exemplo completo. Não adicione um personagem espacial, uma vez que já está lá quando deveria estar. Este valor pode ser uma corda vazia.

    * `sourceTerm`: Uma corda igual ao termo real olhou para cima. A corda é `sourcePrefix` `sourceSuffix` adicionada com e para formar o exemplo completo. O seu valor é separado para que possa ser marcado numa interface de utilizador, por exemplo, ousado.

    * `sourceSuffix`: A corda para _after_ concatenar `sourceTerm` após o valor de formar um exemplo completo. Não adicione um personagem espacial, uma vez que já está lá quando deveria estar. Este valor pode ser uma corda vazia.

    * `targetPrefix`: Uma corda `sourcePrefix` semelhante, mas para o alvo.

    * `targetTerm`: Uma corda `sourceTerm` semelhante, mas para o alvo.

    * `targetSuffix`: Uma corda `sourceSuffix` semelhante, mas para o alvo.

    > [!NOTE]
    > Se não houver exemplos no dicionário, a resposta é de `examples` 200 (OK), mas a lista é uma lista vazia.

## <a name="examples"></a>Exemplos

Este exemplo mostra como procurar exemplos para o par `fly` composto pelo `volar`termo inglês e sua tradução espanhola.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

O corpo de resposta (abreviado para a clareza) é:

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
