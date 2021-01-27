---
title: Método de Exemplos do Dicionário tradutor
titleSuffix: Azure Cognitive Services
description: O método Exemplos do Dicionário de Tradutores fornece exemplos que mostram como os termos no dicionário são usados em contexto.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: lajanuar
ms.openlocfilehash: e7f0e106c1ca154dcd54990395430b3e0f6c536f
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895514"
---
# <a name="translator-30-dictionary-examples"></a>Tradutor 3.0: Exemplos do Dicionário

Fornece exemplos que mostram como os termos no dicionário são usados em contexto. Esta operação é utilizada em conjunto com [a procura do Dicionário.](./v3-0-dictionary-lookup.md)

## <a name="request-url"></a>URL do Pedido

Envie um `POST` pedido para:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros de pedido

Os parâmetros de pedido passados na cadeia de consulta são:

| Parâmetro de consulta | Descrição |
| --------- | ----------- |
| api-version <img width=200/> | **Parâmetro requerido**.<br/>Versão da API solicitada pelo cliente. O valor deve `3.0` ser. |
| De | **Parâmetro requerido**.<br/>Especifica a linguagem do texto de entrada. A língua de origem deve ser uma das [línguas apoiadas incluídas](./v3-0-languages.md) no `dictionary` âmbito. |
| para | **Parâmetro requerido**.<br/>Especifica a linguagem do texto de saída. A língua-alvo deve ser uma das [línguas apoiadas incluídas](./v3-0-languages.md) no `dictionary` âmbito.  | 

Os cabeçalhos de pedido incluem:

| Cabeçalhos  | Descrição |
| ------ | ----------- |
| Cabeçalho de autenticação <img width=200/>  | **Cabeçalho de pedido necessário**.<br/>Consulte <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">as opções disponíveis para autenticação.</a> |
| Content-Type | **Cabeçalho de pedido necessário**.<br/>Especifica o tipo de conteúdo da carga útil. Os valores possíveis são: `application/json` . |
| Comprimento do conteúdo   | **Cabeçalho de pedido necessário**.<br/>O comprimento do corpo do pedido. |
| X-ClientTraceId   | **Opcional.**<br/>Um GUID gerado pelo cliente para identificar exclusivamente o pedido. Pode omitir este cabeçalho se incluir o ID de traço na cadeia de consulta utilizando um parâmetro de consulta denominado `ClientTraceId` . |

## <a name="request-body"></a>Corpo do pedido

O corpo do pedido é uma matriz JSON. Cada elemento de matriz é um objeto JSON com as seguintes propriedades:

  * `Text`: Uma corda que especifica o termo para procurar. Este deve ser o valor de um `normalizedText` campo a partir das traduções traseiras de um pedido de procura do [Dicionário](./v3-0-dictionary-lookup.md) anterior. Também pode ser o valor do `normalizedSource` campo.

  * `Translation`: Uma cadeia que especifica o texto traduzido anteriormente devolvido pela operação [de procura do Dicionário.](./v3-0-dictionary-lookup.md) Este deve ser o valor do `normalizedTarget` campo na lista da resposta de procura do `translations` [Dicionário.](./v3-0-dictionary-lookup.md) O serviço devolverá exemplos para o par de palavras-alvo específico.

Um exemplo é:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Aplicam-se as seguintes limitações:

* A matriz pode ter no máximo 10 elementos.
* O valor de texto de um elemento de matriz não pode exceder 100 caracteres, incluindo espaços.

## <a name="response-body"></a>Corpo da resposta

Uma resposta bem sucedida é uma matriz JSON com um resultado para cada cadeia na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `normalizedSource`: Uma corda que dá a forma normalizada do termo de origem. Geralmente, este deve ser idêntico ao valor do `Text` campo no índice de lista correspondente no corpo do pedido.
    
  * `normalizedTarget`: Uma corda que dá a forma normalizada do termo-alvo. Geralmente, este deve ser idêntico ao valor do `Translation` campo no índice de lista correspondente no corpo do pedido.
  
  * `examples`: Uma lista de exemplos para o par (termo de origem, termo-alvo). Cada elemento da lista é um objeto com as seguintes propriedades:

    * `sourcePrefix`: A corda para concatenar _antes_ do valor de `sourceTerm` formar um exemplo completo. Não adicione um caráter espacial, uma vez que já está lá quando deveria estar. Este valor pode ser uma corda vazia.

    * `sourceTerm`: Uma corda igual ao termo real olhou para cima. A corda é adicionada com `sourcePrefix` e para formar o exemplo `sourceSuffix` completo. O seu valor é separado para que possa ser marcado numa interface de utilizador, por exemplo, a arromando-o.

    * `sourceSuffix`: A corda para concatenar _após_ o valor de `sourceTerm` formar um exemplo completo. Não adicione um caráter espacial, uma vez que já está lá quando deveria estar. Este valor pode ser uma corda vazia.

    * `targetPrefix`: Uma corda semelhante a `sourcePrefix` mas para o alvo.

    * `targetTerm`: Uma corda semelhante a `sourceTerm` mas para o alvo.

    * `targetSuffix`: Uma corda semelhante a `sourceSuffix` mas para o alvo.

    > [!NOTE]
    > Se não houver exemplos no dicionário, a resposta é de 200 (OK), mas a `examples` lista é uma lista vazia.

## <a name="examples"></a>Exemplos

Este exemplo mostra como procurar exemplos para o par composto pelo termo inglês `fly` e sua tradução `volar` espanhola.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

O organismo de resposta (abreviado para clareza) é:

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