---
title: API de Tradução de Texto método de pesquisa de dicionário
titleSuffix: Azure Cognitive Services
description: O método de pesquisa de dicionário fornece traduções alternativas para uma palavra e um pequeno número de frases idiomáticas.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: bd725d41f75bdfb1048b5bee7e8224679dbece4c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837257"
---
# <a name="translator-text-api-30-dictionary-lookup"></a>API de Tradução de Texto 3,0: pesquisa de dicionário

Fornece traduções alternativas para uma palavra e um pequeno número de frases idiomáticas. Cada tradução tem uma parte da fala e uma lista de traduções posteriores. As conversões traseiras permitem que um usuário entenda a tradução no contexto. A operação de [exemplo de dicionário](./v3-0-dictionary-examples.md) permite um detalhamento adicional para ver os usos de exemplo de cada par de tradução.

## <a name="request-url"></a>URL do Pedido

Enviar uma solicitação de `POST` para:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros de solicitação

Os parâmetros de solicitação passados na cadeia de caracteres de consulta são:

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>Descrição</th>
  <tr>
    <td>versão da API</td>
    <td>*Parâmetro obrigatório*.<br/>Versão da API solicitada pelo cliente. O valor deve ser `3.0`.</td>
  </tr>
  <tr>
    <td>De</td>
    <td>*Parâmetro obrigatório*.<br/>Especifica o idioma do texto de entrada. O idioma de origem deve ser um dos [idiomas com suporte](./v3-0-languages.md) incluídos no escopo de `dictionary`.</td>
  </tr>
  <tr>
    <td>para</td>
    <td>*Parâmetro obrigatório*.<br/>Especifica o idioma do texto de saída. O idioma de destino deve ser um dos [idiomas com suporte](./v3-0-languages.md) incluídos no escopo de `dictionary`.</td>
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
    <td>Tipo de conteúdo</td>
    <td>*Cabeçalho de solicitação necessário*.<br/>Especifica o tipo de conteúdo da carga. Os valores possíveis são: `application/json`.</td>
  </tr>
  <tr>
    <td>Comprimento do conteúdo</td>
    <td>*Cabeçalho de solicitação necessário*.<br/>O comprimento do corpo da solicitação.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Opcional*.<br/>Um GUID gerado pelo cliente para identificar exclusivamente a solicitação. Você pode omitir esse cabeçalho se incluir a ID de rastreamento na cadeia de caracteres de consulta usando um parâmetro de consulta chamado `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Corpo do pedido

O corpo da solicitação é uma matriz JSON. Cada elemento da matriz é um objeto JSON com uma propriedade de cadeia de caracteres chamada `Text`, que representa o termo a ser pesquisado.

```json
[
    {"Text":"fly"}
]
```

As seguintes limitações se aplicam:

* A matriz pode ter no máximo 10 elementos.
* O valor de texto de um elemento de matriz não pode exceder 100 caracteres, incluindo espaços.

## <a name="response-body"></a>Corpo da resposta

Uma resposta bem-sucedida é uma matriz JSON com um resultado para cada cadeia de caracteres na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `normalizedSource`: uma cadeia de caracteres fornecendo a forma normalizada do termo de origem. Por exemplo, se a solicitação for "JOHN", a forma normalizada será "João". O conteúdo desse campo torna-se a entrada para [exemplos de pesquisa](./v3-0-dictionary-examples.md).
    
  * `displaySource`: uma cadeia de caracteres fornecendo o termo de origem em um formulário mais adequado para exibição do usuário final. Por exemplo, se a entrada for "João", o formulário de exibição refletirá a grafia usual do nome: "João". 

  * `translations`: uma lista de traduções para o termo de origem. Cada elemento da lista é um objeto com as seguintes propriedades:

    * `normalizedTarget`: uma cadeia de caracteres fornecendo a forma normalizada desse termo no idioma de destino. Esse valor deve ser usado como entrada para [exemplos de pesquisa](./v3-0-dictionary-examples.md).

    * `displayTarget`: uma cadeia de caracteres que dá o termo no idioma de destino e em um formulário mais adequado para exibição do usuário final. Em geral, isso só será diferente da `normalizedTarget` em termos de capitalização. Por exemplo, um substantivo apropriado, como "Juan", terá `normalizedTarget = "juan"` e `displayTarget = "Juan"`.

    * `posTag`: uma cadeia de caracteres que associa esse termo a uma marca de parte de fala.

        | Nome da marca | Descrição  |
        |----------|--------------|
        | AJUSTE      | Adjetivos   |
        | AVÇD      | Advérbios      |
        | CONJ     | Associações |
        | DET      | Determinantes  |
        | JANELAS    | Verbos        |
        | Substantivo     | Substantivos        |
        | PREP     | Preposições |
        | PRON     | Pronomes     |
        | VERBO     | Verbos        |
        | OUTROS    | Outros        |

        Como uma observação de implementação, essas marcas foram determinadas pela marcação de parte da fala do lado Inglês e, em seguida, por meio da marca mais frequente para cada par de origem/destino. Portanto, se as pessoas traduzirem com frequência uma palavra em espanhol para uma marca de parte de fala diferente em inglês, as marcas podem acabar tendo errado (em relação à palavra espanhol).

    * `confidence`: um valor entre 0,0 e 1,0 que representa a "confiança" (ou talvez mais precisamente, "probabilidade nos dados de treinamento") desse par de tradução. A soma das pontuações de confiança para uma palavra de origem pode ou não ser somada a 1,0. 

    * `prefixWord`: uma cadeia de caracteres que permite que a palavra seja exibida como um prefixo da tradução. Atualmente, esse é o determinante sexo dos substantivos, em idiomas que têm dedeterminadores de gêneros. Por exemplo, o prefixo da palavra espanhola "mosca" é "la", já que "mosca" é um substantivo feminino em espanhol. Isso depende apenas da tradução, e não da origem. Se não houver nenhum prefixo, será a cadeia de caracteres vazia.
    
    * `backTranslations`: uma lista de "traduções posteriores" do destino. Por exemplo, palavras-fonte nas quais o destino pode ser convertido. A lista tem a garantia de conter a palavra de origem que foi solicitada (por exemplo, se a palavra de origem que está sendo pesquisada for "voar", é garantido que "voar" estará na lista de `backTranslations`). No entanto, não há garantia de que ela esteja na primeira posição e, muitas vezes, não será. Cada elemento da lista de `backTranslations` é um objeto descrito pelas seguintes propriedades:

        * `normalizedText`: uma cadeia de caracteres que fornece a forma normalizada do termo de origem que é uma tradução traseira do destino. Esse valor deve ser usado como entrada para [exemplos de pesquisa](./v3-0-dictionary-examples.md).        

        * `displayText`: uma cadeia de caracteres que fornece o termo de origem que é uma tradução posterior do destino em um formulário mais adequado para exibição do usuário final.

        * `numExamples`: um inteiro que representa o número de exemplos disponíveis para esse par de tradução. Exemplos reais devem ser recuperados com uma chamada separada para [exemplos de pesquisa](./v3-0-dictionary-examples.md). O número é principalmente destinado a facilitar a exibição em uma UX. Por exemplo, uma interface do usuário pode adicionar um hiperlink à tradução regressiva se o número de exemplos for maior que zero e mostrar a tradução de fundo como texto sem formatação se não houver nenhum exemplo. Observe que o número real de exemplos retornados por uma chamada para [exemplos de pesquisa](./v3-0-dictionary-examples.md) pode ser menor que `numExamples`, pois a filtragem adicional pode ser aplicada em tempo real para remover exemplos "inválidos".
        
        * `frequencyCount`: um inteiro que representa a frequência desse par de tradução nos dados. A principal finalidade deste campo é fornecer uma interface do usuário com meios para classificar as traduções de volta para que os termos mais frequentes sejam primeiro.

    > [!NOTE]
    > Se o termo que está sendo procurado não existir no dicionário, a resposta será 200 (OK), mas a lista de `translations` será uma lista vazia.

## <a name="examples"></a>Exemplos

Este exemplo mostra como Pesquisar traduções alternativas em espanhol do termo em inglês `fly`.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

O corpo da resposta (abreviado para maior clareza) é:

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

Este exemplo mostra o que acontece quando o termo que está sendo procurado não existe para o par de dicionários válido.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

Como o termo não é encontrado no dicionário, o corpo da resposta inclui uma lista de `translations` vazia.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
