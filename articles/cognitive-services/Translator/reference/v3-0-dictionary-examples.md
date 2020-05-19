---
title: Método de Exemplos do Dicionário de Tradutor
titleSuffix: Azure Cognitive Services
description: O método tradutor Dicionário Exemplos fornece exemplos que mostram como os termos no dicionário são usados no contexto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: a1d86ac354524cb4d7bf9f9776b8605f244d92f7
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592513"
---
# <a name="translator-30-dictionary-examples"></a>Tradutor 3.0: Exemplos de dicionário

Fornece exemplos que mostram como os termos no dicionário são usados no contexto. Esta operação é utilizada em conjunto com a procura do [Dicionário](./v3-0-dictionary-lookup.md).

## <a name="request-url"></a>URL do Pedido

Envie um `POST` pedido para:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros de pedido

Os parâmetros de pedido transmitidos na corda de consulta são:

| Parâmetro de consulta | Descrição |
| --------- | ----------- |
| api-version <img width=200/> | **Parâmetro necessário**.<br/>Versão da API solicitada pelo cliente. O valor deve `3.0` ser. |
| De | **Parâmetro necessário**.<br/>Especifica a linguagem do texto de entrada. A língua de origem deve ser uma das [línguas apoiadas incluídas](./v3-0-languages.md) no `dictionary` âmbito. |
| para | **Parâmetro necessário**.<br/>Especifica a linguagem do texto de saída. A língua-alvo deve ser uma das [línguas apoiadas incluídas](./v3-0-languages.md) no `dictionary` âmbito.  | 

Os cabeçalhos de pedido incluem:

| Cabeçalhos  | Descrição |
| ------ | ----------- |
| Cabeçalho de autenticação <img width=200/>  | **Cabeçalho de pedido exigido**.<br/>Consulte <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">as opções disponíveis para autenticação.</a> |
| Content-Type | **Cabeçalho de pedido exigido**.<br/>Especifica o tipo de conteúdo da carga útil. Os valores possíveis são: `application/json` . |
| Comprimento do conteúdo   | **Cabeçalho de pedido exigido**.<br/>O comprimento do corpo de pedido. |
| X-ClientTraceid   | **Opcional.**<br/>Um GUID gerado pelo cliente para identificar exclusivamente o pedido. Pode omitir este cabeçalho se incluir o id de rastreio na corda de consulta usando um parâmetro de consulta chamado `ClientTraceId` . |

## <a name="request-body"></a>Corpo do pedido

O corpo do pedido é uma matriz JSON. Cada elemento matriz é um objeto JSON com as seguintes propriedades:

  * `Text`: Uma cadeia que especifica o termo para procurar. Este deve ser o valor de um `normalizedText` campo a partir das traduções traseiras de um pedido de [procuração](./v3-0-dictionary-lookup.md) do Dicionário anterior. Também pode ser o valor do `normalizedSource` campo.

  * `Translation`: Uma cadeia que especifica o texto traduzido anteriormente devolvido pela operação de procuração do [Dicionário.](./v3-0-dictionary-lookup.md) Este deve ser o valor do campo na lista da resposta de procura do `normalizedTarget` `translations` [Dicionário.](./v3-0-dictionary-lookup.md) O serviço devolverá exemplos para o par de palavras específico-alvo de origem.

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

  * `normalizedSource`: Uma corda que dá a forma normalizada do termo fonte. Geralmente, este deve ser idêntico ao valor do campo no índice da `Text` lista correspondente no organismo do pedido.
    
  * `normalizedTarget`: Uma corda que dá a forma normalizada do termo-alvo. Geralmente, este deve ser idêntico ao valor do campo no índice da `Translation` lista correspondente no organismo do pedido.
  
  * `examples`: Uma lista de exemplos para o par (termo-alvo de origem). Cada elemento da lista é um objeto com as seguintes propriedades:

    * `sourcePrefix`: A corda para concatenar _antes_ do valor de `sourceTerm` formar um exemplo completo. Não adicione um personagem espacial, uma vez que já está lá quando deveria estar. Este valor pode ser uma corda vazia.

    * `sourceTerm`: Uma corda igual ao termo real olhou para cima. A corda é adicionada com `sourcePrefix` e para formar o exemplo `sourceSuffix` completo. O seu valor é separado para que possa ser marcado numa interface de utilizador, por exemplo, ousado.

    * `sourceSuffix`: A corda para concatenar _após_ o valor de `sourceTerm` formar um exemplo completo. Não adicione um personagem espacial, uma vez que já está lá quando deveria estar. Este valor pode ser uma corda vazia.

    * `targetPrefix`: Uma corda semelhante, `sourcePrefix` mas para o alvo.

    * `targetTerm`: Uma corda semelhante, `sourceTerm` mas para o alvo.

    * `targetSuffix`: Uma corda semelhante, `sourceSuffix` mas para o alvo.

    > [!NOTE]
    > Se não houver exemplos no dicionário, a resposta é de 200 (OK), mas a `examples` lista é uma lista vazia.

## <a name="examples"></a>Exemplos

Este exemplo mostra como procurar exemplos para o par composto pelo termo inglês `fly` e sua tradução `volar` espanhola.

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
