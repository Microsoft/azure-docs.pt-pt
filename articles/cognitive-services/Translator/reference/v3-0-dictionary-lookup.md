---
title: Método de procura do dicionário de tradutor
titleSuffix: Azure Cognitive Services
description: O método DeEscureamento do Dicionário fornece traduções alternativas para uma palavra e um pequeno número de frases idiomáticas.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: lajanuar
ms.openlocfilehash: 88a76a16de43853a001f5db895d6ad418940de0f
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895497"
---
# <a name="translator-30-dictionary-lookup"></a>Tradutor 3.0: Observatório do Dicionário

Fornece traduções alternativas para uma palavra e um pequeno número de frases idiomáticas. Cada tradução tem uma parte do discurso e uma lista de back-translations. As traduções traseiras permitem ao utilizador compreender a tradução em contexto. A operação [Exemplo do Dicionário](./v3-0-dictionary-examples.md) permite perfurar ainda mais para ver o exemplo de utilização de cada par de tradução.

## <a name="request-url"></a>URL do Pedido

Envie um `POST` pedido para:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros de pedido

Os parâmetros de pedido passados na cadeia de consulta são:

| Parâmetro de consulta  | Descrição |
| ------ | ----------- |
| api-version <img width=200/>   | **Parâmetro requerido**.<br/>Versão da API solicitada pelo cliente. Valor deve ser `3.0` |
| De | **Parâmetro requerido**.<br/>Especifica a linguagem do texto de entrada. A língua de origem deve ser uma das [línguas apoiadas incluídas](./v3-0-languages.md) no `dictionary` âmbito. |
| para   | **Parâmetro requerido**.<br/>Especifica a linguagem do texto de saída. A língua-alvo deve ser uma das [línguas apoiadas incluídas](v3-0-languages.md) no `dictionary` âmbito. |


Os cabeçalhos de pedido incluem:

| Cabeçalhos  | Descrição |
| ------ | ----------- |
| Cabeçalho de autenticação <img width=200/>  | **Cabeçalho de pedido necessário**.<br/>Consulte <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">as opções disponíveis para autenticação.</a> |
| Content-Type | **Cabeçalho de pedido necessário**.<br/>Especifica o tipo de conteúdo da carga útil. Os valores possíveis são: `application/json` . |
| Comprimento do conteúdo   | **Cabeçalho de pedido necessário**.<br/>O comprimento do corpo do pedido. |
| X-ClientTraceId   | **Opcional.**<br/>Um GUID gerado pelo cliente para identificar exclusivamente o pedido. Pode omitir este cabeçalho se incluir o ID de traço na cadeia de consulta utilizando um parâmetro de consulta denominado `ClientTraceId` . |

## <a name="request-body"></a>Corpo do pedido

O corpo do pedido é uma matriz JSON. Cada elemento de matriz é um objeto JSON com uma propriedade de cordas chamada `Text` , que representa o termo para procurar.

```json
[
    {"Text":"fly"}
]
```

Aplicam-se as seguintes limitações:

* A matriz pode ter no máximo 10 elementos.
* O valor de texto de um elemento de matriz não pode exceder 100 caracteres, incluindo espaços.

## <a name="response-body"></a>Corpo da resposta

Uma resposta bem sucedida é uma matriz JSON com um resultado para cada cadeia na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `normalizedSource`: Uma corda que dá a forma normalizada do termo de origem. Por exemplo, se o pedido for "JOÃO", o formulário normalizado será "john". O conteúdo deste campo torna-se a entrada para [procurar exemplos](./v3-0-dictionary-examples.md).
    
  * `displaySource`: Uma cadeia que dá o termo de origem num formulário mais adequado para visualização do utilizador final. Por exemplo, se a entrada for "JOHN", o formulário de visualização refletirá a ortografia habitual do nome: "João". 

  * `translations`: Uma lista de traduções para o termo-fonte. Cada elemento da lista é um objeto com as seguintes propriedades:

    * `normalizedTarget`: Uma corda que dá a forma normalizada deste termo na linguagem-alvo. Este valor deve ser utilizado como entrada para [procurar exemplos](./v3-0-dictionary-examples.md).

    * `displayTarget`: Uma cadeia que dá o termo no idioma-alvo e num formulário mais adequado para visualização do utilizador final. Em geral, isto só será diferente do que se refere à `normalizedTarget` capitalização. Por exemplo, um substantivo adequado como "Juan" terá `normalizedTarget = "juan"` e `displayTarget = "Juan"` .

    * `posTag`: Uma corda que associa este termo a uma etiqueta de parte do discurso.

        | Nome da etiqueta | Descrição  |
        |----------|--------------|
        | ADJ      | Adjetivos   |
        | ADV      | Advér ovabs      |
        | CONJ     | Conjunções |
        | DET      | Determinantes  |
        | MODAL    | Verbos        |
        | SUBSTANTIVO     | Substantivos        |
        | PREP     | Preposições |
        | PRON     | Pronomes     |
        | VERBO     | Verbos        |
        | OUTROS    | Outro        |

        Como nota de implementação, estas tags foram determinadas por marcação parcial do lado inglês e, em seguida, tomando a etiqueta mais frequente para cada par de origem/alvo. Assim, se as pessoas frequentemente traduzem uma palavra espanhola para uma etiqueta de parte diferente do discurso em inglês, as etiquetas podem acabar por estar erradas (no que diz respeito à palavra espanhola).

    * `confidence`: Valor entre 0,0 e 1.0 que represente a "confiança" (ou talvez mais precisamente, "probabilidade nos dados de formação") desse par de tradução. A soma das notas de confiança para uma palavra-fonte pode ou não ser elevada a 1.0. 

    * `prefixWord`: Uma corda que dá a palavra para exibir como prefixo da tradução. Atualmente, este é o determinante de género dos substantivos, em línguas que têm determinantes de género. Por exemplo, o prefixo da palavra espanhola "mosca" é "la", uma vez que "mosca" é um substantivo feminino em espanhol. Isto só depende da tradução, e não da fonte. Se não houver prefixo, será a corda vazia.
    
    * `backTranslations`: Uma lista de "traduções traseiras" do alvo. Por exemplo, palavras-fonte para as que o alvo pode traduzir. A lista é garantida para conter a palavra-fonte que foi solicitada (por exemplo, se a palavra de origem que está a ser analisada é "fly", então é garantido que "fly" estará na `backTranslations` lista). No entanto, não é garantido estar na primeira posição, e muitas vezes não estará. Cada elemento da `backTranslations` lista é um objeto descrito pelas seguintes propriedades:

        * `normalizedText`: Uma corda que dá a forma normalizada do termo de origem que é uma tradução traseira do alvo. Este valor deve ser utilizado como entrada para [procurar exemplos](./v3-0-dictionary-examples.md).        

        * `displayText`: Uma cadeia que dá o termo de origem que é uma tradução traseira do alvo num formulário mais adequado para visualização do utilizador final.

        * `numExamples`: Um número inteiro que representa o número de exemplos disponíveis para este par de tradução. Os exemplos reais devem ser recuperados com uma chamada separada para [procurar exemplos](./v3-0-dictionary-examples.md). O número destina-se principalmente a facilitar a visualização num UX. Por exemplo, uma interface de utilizador pode adicionar uma hiperligação à tradução traseira se o número de exemplos for maior do que zero e mostrar a tradução traseira como texto simples se não houver exemplos. Note que o número real de exemplos devolvidos por uma chamada para [procurar exemplos](./v3-0-dictionary-examples.md) pode ser menor do que `numExamples` , porque a filtragem adicional pode ser aplicada na mosca para remover exemplos "maus".
        
        * `frequencyCount`: Um número inteiro que represente a frequência deste par de tradução nos dados. O principal objetivo deste campo é fornecer a uma interface de utilizador um meio de ordenar traduções traseiras para que os termos mais frequentes sejam os primeiros.

    > [!NOTE]
    > Se o termo que está a ser analisado não existir no dicionário, a resposta é de 200 (OK), mas a `translations` lista é uma lista vazia.

## <a name="examples"></a>Exemplos

Este exemplo mostra como procurar traduções alternativas em espanhol do termo inglês `fly` .

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

O organismo de resposta (abreviado para clareza) é:

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

Este exemplo mostra o que acontece quando o termo que está a ser analisado não existe para o par de dicionários válidos.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

Uma vez que o termo não é encontrado no dicionário, o corpo de resposta inclui uma `translations` lista vazia.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```