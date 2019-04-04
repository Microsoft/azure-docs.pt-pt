---
title: Método de exemplos de dicionário de API de texto de tradutor
titlesuffix: Azure Cognitive Services
description: Use o método de exemplos de dicionário de API de texto do Translator.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 26f147fde58a7f9c836bdacd6d66321f0fc5529a
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916426"
---
# <a name="translator-text-api-30-dictionary-examples"></a>Texto do Translator API 3.0: Exemplos do dicionário

Fornece exemplos que mostram como os termos no dicionário são utilizados no contexto. Esta operação é utilizada em conjunto com [pesquisa de dicionário](./v3-0-dictionary-lookup.md).

## <a name="request-url"></a>URL do Pedido

Enviar um `POST` pedido para:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros do pedido

Parâmetros de pedido passados na seqüência de consulta são:

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>Descrição</th>
  <tr>
    <td>versão de API</td>
    <td>*Parâmetro necessário*.<br/>Versão da API do pedido pelo cliente. Valor tem de ser `3.0`.</td>
  </tr>
  <tr>
    <td>de</td>
    <td>*Parâmetro necessário*.<br/>Especifica o idioma do texto de entrada. O idioma de origem tem de ser o [idiomas suportados](./v3-0-languages.md) incluído no `dictionary` âmbito.</td>
  </tr>
  <tr>
    <td>para</td>
    <td>*Parâmetro necessário*.<br/>Especifica o idioma do texto de saída. O idioma de destino tem de ser o [idiomas suportados](./v3-0-languages.md) incluído no `dictionary` âmbito.</td>
  </tr>
</table>

Cabeçalhos de pedido incluem:

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
  <tr>
    <td>_Uma autorização_<br/>_cabeçalho_</td>
    <td>*Cabeçalho do pedido necessário*.<br/>Ver [as opções disponíveis para autenticação](./v3-0-reference.md#authentication).</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Cabeçalho do pedido necessário*.<br/>Especifica o tipo de conteúdo da carga. Os valores possíveis são: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Cabeçalho do pedido necessário*.<br/>O comprimento do corpo do pedido.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Opcional*.<br/>Um GUID gerado pelo cliente para identificar exclusivamente o pedido. Pode omitir este cabeçalho se incluir o ID de rastreio na cadeia de consulta com um parâmetro de consulta com o nome `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Corpo do pedido

O corpo do pedido é uma matriz JSON. Cada elemento da matriz é um objeto JSON com as seguintes propriedades:

  * `Text`: Uma cadeia que especifica o termo de pesquisa. Isso deve ser o valor de um `normalizedText` campo de back-traduções de anterior [pesquisa de dicionário](./v3-0-dictionary-lookup.md) pedido. Também pode ser o valor da `normalizedSource` campo.

  * `Translation`: Uma cadeia que especifica o texto traduzido devolvido anteriormente pela [pesquisa de dicionário](./v3-0-dictionary-lookup.md) operação. Isso deve ser o valor do `normalizedTarget` campo no `translations` lista da [pesquisa de dicionário](./v3-0-dictionary-lookup.md) resposta. O serviço retornará exemplos para um par de origem-destino específico.

Um exemplo é:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

As seguintes limitações aplicam-se:

* A matriz pode ter no máximo 10 elementos.
* O valor de texto de um elemento de matriz não pode exceder os 100 carateres, incluindo espaços.

## <a name="response-body"></a>Corpo da resposta

Uma resposta com êxito é uma matriz JSON com um resultado para cada cadeia de caracteres na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `normalizedSource`: Uma cadeia de caracteres dar forma normalizada do termo de origem. Geralmente, isso deve ser idêntico ao valor da `Text` campo no índice de lista correspondentes no corpo do pedido.
    
  * `normalizedTarget`: Uma cadeia de caracteres dar forma normalizada do termo de destino. Geralmente, isso deve ser idêntico ao valor da `Translation` campo no índice de lista correspondentes no corpo do pedido.
  
  * `examples`: Uma lista de exemplos para a (prazo de origem, o termo de destino) par. Cada elemento da lista é um objeto com as seguintes propriedades:

    * `sourcePrefix`: A cadeia de caracteres para concatenar _antes de_ o valor de `sourceTerm` para formar um exemplo completo. Não adicione um caractere de espaço, uma vez que ainda estiver lá quando deve ser. Este valor pode ser uma cadeia vazia.

    * `sourceTerm`: Uma cadeia de caracteres de igual ao termo real pesquisar o tempo limite. A cadeia de caracteres é adicionada com `sourcePrefix` e `sourceSuffix` para formar o exemplo completo. Seu valor é separado para que ele pode ser marcado numa interface de utilizador, por exemplo, por em negrito-lo.

    * `sourceSuffix`: A cadeia de caracteres para concatenar _após_ o valor de `sourceTerm` para formar um exemplo completo. Não adicione um caractere de espaço, uma vez que ainda estiver lá quando deve ser. Este valor pode ser uma cadeia vazia.

    * `targetPrefix`: Uma cadeia de caracteres semelhante a `sourcePrefix` , mas para o destino.

    * `targetTerm`: Uma cadeia de caracteres semelhante a `sourceTerm` , mas para o destino.

    * `targetSuffix`: Uma cadeia de caracteres semelhante a `sourceSuffix` , mas para o destino.

    > [!NOTE]
    > Se não houver nenhum exemplos no dicionário, a resposta é 200 (OK), mas a `examples` lista é uma lista vazia.

## <a name="examples"></a>Exemplos

Este exemplo mostra como procurar exemplos para o par formado do termo em inglês `fly` e a tradução do espanhol `volar`.

# [<a name="curl"></a>Curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

---

O corpo de resposta (abreviado para efeitos de clareza) é:

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
