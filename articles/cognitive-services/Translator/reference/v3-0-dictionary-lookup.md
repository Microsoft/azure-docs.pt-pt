---
title: Método de lookup dicionário de texto tradutor API
titleSuffix: Azure Cognitive Services
description: O método de lookup do Dicionário fornece traduções alternativas para uma palavra e um pequeno número de frases idiomáticas.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: bd27827441082698bb4e0b43e7dd22d5b7e66539
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76548956"
---
# <a name="translator-text-api-30-dictionary-lookup"></a>Texto tradutor API 3.0: Lookup dicionário

Fornece traduções alternativas para uma palavra e um pequeno número de frases idiomáticas. Cada tradução tem uma parte da fala e uma lista de traduções traseiras. As traduções traseiras permitem que um utilizador compreenda a tradução em contexto. A operação [Dictionary Example](./v3-0-dictionary-examples.md) permite continuar a perfurar para ver as utilizações de cada par de tradução.

## <a name="request-url"></a>URL do Pedido

Envie `POST` um pedido para:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros de pedido

Os parâmetros de pedido transmitidos na corda de consulta são:

| Parâmetro de consulta  | Descrição |
| ------ | ----------- |
| api-version <img width=200/>   | **Parâmetro necessário**.<br/>Versão da API solicitada pelo cliente. Valor deve ser`3.0` |
| De | **Parâmetro necessário**.<br/>Especifica a linguagem do texto de entrada. A língua de origem deve ser uma das `dictionary` [línguas apoiadas incluídas](./v3-0-languages.md) no âmbito. |
| para   | **Parâmetro necessário**.<br/>Especifica a linguagem do texto de saída. A língua-alvo deve ser uma das [línguas apoiadas incluídas](v3-0-languages.md) no `dictionary` âmbito. |


Os cabeçalhos de pedido incluem:

| Cabeçalhos  | Descrição |
| ------ | ----------- |
| Cabeçalho de autenticação <img width=200/>  | **Cabeçalho de pedido exigido**.<br/>Consulte <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">as opções disponíveis para autenticação.</a> |
| Content-Type | **Cabeçalho de pedido exigido**.<br/>Especifica o tipo de conteúdo da carga útil. Os valores `application/json`possíveis são: . |
| Comprimento do conteúdo   | **Cabeçalho de pedido exigido**.<br/>O comprimento do corpo de pedido. |
| X-ClientTraceid   | **Opcional.**<br/>Um GUID gerado pelo cliente para identificar exclusivamente o pedido. Pode omitir este cabeçalho se incluir o id de rastreio na `ClientTraceId`corda de consulta usando um parâmetro de consulta chamado . |

## <a name="request-body"></a>Corpo do pedido

O corpo do pedido é uma matriz JSON. Cada elemento matriz é um objeto JSON com uma propriedade de cordas chamada, `Text`que representa o termo para pesquisa.

```json
[
    {"Text":"fly"}
]
```

Aplicam-se as seguintes limitações:

* A matriz pode ter no máximo 10 elementos.
* O valor de texto de um elemento matriz não pode exceder 100 caracteres, incluindo espaços.

## <a name="response-body"></a>Corpo da resposta

Uma resposta bem sucedida é um array JSON com um resultado para cada corda na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `normalizedSource`: Uma corda que dá a forma normalizada do termo fonte. Por exemplo, se o pedido for "JOHN", o formulário normalizado será "joão". O conteúdo deste campo torna-se a entrada para [procurar exemplos](./v3-0-dictionary-examples.md).
    
  * `displaySource`: Uma corda que dá o termo fonte de forma mais adequada para o ecrã do utilizador final. Por exemplo, se a entrada for "JOHN", o formulário de exibição refletirá a ortografia habitual do nome: "João". 

  * `translations`: Uma lista de traduções para o termo-fonte. Cada elemento da lista é um objeto com as seguintes propriedades:

    * `normalizedTarget`: Uma corda que dá a forma normalizada deste termo na linguagem-alvo. Este valor deve ser utilizado como entrada para [procurar exemplos](./v3-0-dictionary-examples.md).

    * `displayTarget`: Uma corda que dá o termo no idioma-alvo e de uma forma mais adequada para o ecrã do utilizador final. Em geral, isso só `normalizedTarget` será diferente do em termos de capitalização. Por exemplo, um substantivo como "Juan" terá `normalizedTarget = "juan"` e. `displayTarget = "Juan"`

    * `posTag`: Uma corda que associa este termo a uma etiqueta de parte da fala.

        | Nome da etiqueta | Descrição  |
        |----------|--------------|
        | ADJ      | Adjetivos   |
        | ADV      | Advérbios      |
        | CONJ     | Conjunções |
        | DET      | Determinantes  |
        | MODAL    | Verbos        |
        | SUBSTANTIVO     | Substantivos        |
        | PREPARAÇÃO     | Preposições |
        | PRON     | Pronomes     |
        | VERBo     | Verbos        |
        | OUTROS    | Outros        |

        Como nota de implementação, estas etiquetas foram determinadas por parte da fala marcando o lado inglês, e, em seguida, tomando a etiqueta mais frequente para cada par de fonte/alvo. Assim, se as pessoas frequentemente traduzem uma palavra espanhola para uma etiqueta diferente de parte da fala em inglês, as etiquetas podem acabar por estar erradas (no que diz respeito à palavra espanhola).

    * `confidence`: Um valor entre 0,0 e 1.0 que represente a "confiança" (ou talvez mais precisamente, "probabilidade nos dados de formação") desse par de tradução. A soma da confiança para uma palavra fonte pode ou não resumir-se a 1,0. 

    * `prefixWord`: Uma corda que dá a palavra para exibir como prefixo da tradução. Atualmente, este é o determinante de género dos substantivos, em línguas que têm determinantes de género. Por exemplo, o prefixo da palavra espanhola "mosca" é "la", uma vez que "mosca" é um substantivo feminino em espanhol. Isto depende apenas da tradução, e não da fonte. Se não houver prefixo, será a corda vazia.
    
    * `backTranslations`: Uma lista de "traduções traseiras" do alvo. Por exemplo, palavras-chave para as as que o alvo pode traduzir. A lista é garantida para conter a palavra-fonte que foi solicitada (por exemplo, se a palavra-fonte que está `backTranslations` a ser analisada é "fly", então é garantido que "fly" estará na lista). No entanto, não é garantido que esteja na primeira posição, e muitas vezes não estará. Cada elemento `backTranslations` da lista é um objeto descrito pelas seguintes propriedades:

        * `normalizedText`: Uma corda que dá a forma normalizada do termo fonte que é uma tradução traseira do alvo. Este valor deve ser utilizado como entrada para [procurar exemplos](./v3-0-dictionary-examples.md).        

        * `displayText`: Uma cadeia que dá o termo fonte que é uma tradução traseira do alvo de uma forma mais adequada para o ecrã do utilizador final.

        * `numExamples`: Um inteiro que representa o número de exemplos disponíveis para este par de tradução. Os exemplos reais devem ser recuperados com uma chamada separada para [procurar exemplos](./v3-0-dictionary-examples.md). O número destina-se principalmente a facilitar o visualização num UX. Por exemplo, uma interface de utilizador pode adicionar uma hiperligação à tradução traseira se o número de exemplos for superior a zero e mostrar a tradução traseira como texto simples se não houver exemplos. Note que o número real de exemplos devolvidos por uma `numExamples`chamada para [procurar exemplos](./v3-0-dictionary-examples.md) pode ser inferior , porque pode ser aplicada filtragem adicional no voo para remover exemplos "maus".
        
        * `frequencyCount`: Um inteiro que represente a frequência deste par de tradução nos dados. O principal objetivo deste campo é fornecer uma interface de utilizador com um meio de ordenar traduções traseiras para que os termos mais frequentes sejam os primeiros.

    > [!NOTE]
    > Se o termo que está a ser procurado não existir no dicionário, a `translations` resposta é de 200 (OK), mas a lista é uma lista vazia.

## <a name="examples"></a>Exemplos

Este exemplo mostra como procurar traduções alternativas `fly` em espanhol do termo inglês.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

O corpo de resposta (abreviado para a clareza) é:

```
[
    {
        "normalizedSource":"fly",
        "displaySource":"fly",
        "translations":[
            {
                "normalizedTarget":"volar",
                "displayTarget":"volar",
                "posTag":"VERB",
                "confidence":0.4081,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":4637},
                    {"normalizedText":"flying","displayText":"flying","numExamples":15,"frequencyCount":1365},
                    {"normalizedText":"blow","displayText":"blow","numExamples":15,"frequencyCount":503},
                    {"normalizedText":"flight","displayText":"flight","numExamples":15,"frequencyCount":135}
                ]
            },
            {
                "normalizedTarget":"mosca",
                "displayTarget":"mosca",
                "posTag":"NOUN",
                "confidence":0.2668,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":1697},
                    {"normalizedText":"flyweight","displayText":"flyweight","numExamples":0,"frequencyCount":48},
                    {"normalizedText":"flies","displayText":"flies","numExamples":9,"frequencyCount":34}
                ]
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```

Este exemplo mostra o que acontece quando o termo que está a ser procurado não existe para o par de dicionário válido.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

Uma vez que o termo não é encontrado no `translations` dicionário, o corpo de resposta inclui uma lista vazia.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
