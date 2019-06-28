---
title: Método de pesquisa de dicionário de API de texto do tradutor
titlesuffix: Azure Cognitive Services
description: Use o método de pesquisa de dicionário de API de texto do Translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: 8cc37c9edd5679158f8454e00bd27a53e4491004
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357700"
---
# <a name="translator-text-api-30-dictionary-lookup"></a>Texto do Translator API 3.0: Pesquisa no Dicionário

Fornece traduções alternativas para uma palavra e um pequeno número de frases idiomático. Cada tradução tem uma parte da voz e uma lista de traduções back. As back-traduções permitem que um utilizador compreender a tradução em contexto. O [exemplo de dicionário](./v3-0-dictionary-examples.md) operação permite desagregações ainda mais a ver exemplo utiliza de cada par de tradução.

## <a name="request-url"></a>URL do Pedido

Enviar um `POST` pedido para:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
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
    <td>from</td>
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
    <td>Excederam de autenticação</td>
    <td><em>Cabeçalho do pedido necessário</em>.<br/>Ver <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">as opções disponíveis para autenticação</a>.</td>
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

O corpo do pedido é uma matriz JSON. Cada elemento da matriz é um objeto JSON com uma propriedade de cadeia de caracteres chamada `Text`, que representa o termo de pesquisa.

```json
[
    {"Text":"fly"}
]
```

As seguintes limitações aplicam-se:

* A matriz pode ter no máximo 10 elementos.
* O valor de texto de um elemento de matriz não pode exceder os 100 carateres, incluindo espaços.

## <a name="response-body"></a>Corpo da resposta

Uma resposta com êxito é uma matriz JSON com um resultado para cada cadeia de caracteres na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `normalizedSource`: Uma cadeia de caracteres dar forma normalizada do termo de origem. Por exemplo, se o pedido é "JOÃO", o formato normalizado será "João". O conteúdo deste campo torna-se a entrada para [exemplos de pesquisa](./v3-0-dictionary-examples.md).
    
  * `displaySource`: Uma cadeia de caracteres dando o termo de origem num formato mais adequado para apresentar do utilizador final. Por exemplo, se a entrada é "JOÃO", o formulário de apresentação refletirá a ortografia habitual do nome: "João". 

  * `translations`: Uma lista de traduções para o termo de origem. Cada elemento da lista é um objeto com as seguintes propriedades:

    * `normalizedTarget`: Uma cadeia de caracteres dar forma normalizada desse termo no idioma de destino. Este valor deve ser utilizado como entrada para [exemplos de pesquisa](./v3-0-dictionary-examples.md).

    * `displayTarget`: Uma cadeia de caracteres dando o termo no idioma de destino e de forma mais adequada para apresentar do utilizador final. Geralmente, isso só será diferente do `normalizedTarget` em termos de uso. Por exemplo, um nome próprio, como "Juan" terão `normalizedTarget = "juan"` e `displayTarget = "Juan"`.

    * `posTag`: Uma cadeia de caracteres associar esse termo com uma etiqueta de parte da voz.

        | Nome da etiqueta | Descrição  |
        |----------|--------------|
        | ADJ      | Adjetivos   |
        | ADV      | Adverbs      |
        | CONJ     | Conjunções |
        | DET      | Determiners  |
        | MODAL    | Verbos        |
        | NOME PRÓPRIO     | Substantivos        |
        | PREPARAÇÃO     | Preposições |
        | PRON     | Pronomes     |
        | VERBO     | Verbos        |
        | OUTROS    | Outros        |

        Como uma observação de implementação, essas marcas foram determinadas por parte da voz marcação no lado em inglês e, em seguida, levando a marca mais frequente para cada par de origem/destino. Por isso, se as pessoas freqüentemente traduzir uma palavra espanhola para uma etiqueta de parte da voz diferente em inglês, as etiquetas podem acabar por estar errado (com respeito à palavra espanhola).

    * `confidence`: Um valor entre 0,0 e 1,0 que representa a "confiança" (ou talvez mais precisamente, "probabilidade nos dados de treinamento") desse par de tradução. A soma das pontuações de confiança para uma palavra de origem pode ou não pode soma seja 1,0. 

    * `prefixWord`: Uma cadeia de caracteres dando o word para exibir como um prefixo da tradução. Atualmente, este é o determiner gendered de substantivos, em idiomas que têm gendered determiners. Por exemplo, o prefixo da palavra "mosca" Espanhol é "la", uma vez que "mosca" é um substantivo feminino em espanhol. Isto só é dependente a tradução e não na origem. Se não houver nenhum prefixo, é a cadeia vazia.
    
    * `backTranslations`: Uma lista de "back-traduções" de destino. Por exemplo, a origem de palavras que o destino pode traduzir-se. A lista é garantida que contenham a palavra de origem que foi pedida (por exemplo, se a palavra de origem que está sendo pesquisado "voar", então é garantido que "voar" estarão no `backTranslations` lista). No entanto, ele não é garantido que estará na primeira posição e, muitas vezes, não será possível. Cada elemento do `backTranslations` lista é um objeto descrito pelas seguintes propriedades:

        * `normalizedText`: Uma cadeia de caracteres dar forma normalizada do termo de origem que é uma tradução back do destino. Este valor deve ser utilizado como entrada para [exemplos de pesquisa](./v3-0-dictionary-examples.md).        

        * `displayText`: Uma cadeia de caracteres dando o termo de origem que é uma tradução back do destino de uma forma melhor adequado para apresentar do utilizador final.

        * `numExamples`: Um número inteiro que representa o número de exemplos que estão disponíveis para esse par de tradução. Exemplos reais tem de ser obtidos com uma chamada separada para [exemplos de pesquisa](./v3-0-dictionary-examples.md). O número destina-se principalmente para facilitar a exibição numa experiência do usuário. Por exemplo, uma interface do usuário pode adicionar uma hiperligação para a back-tradução se o número de exemplos é superior a zero e mostrar a tradução de back como texto simples, se não houver nenhum exemplos. Tenha em atenção que o número real de exemplos devolvido por uma chamada para [exemplos de pesquisa](./v3-0-dictionary-examples.md) pode ser inferior a `numExamples`, porque a filtragem adicionais pode ser aplicado em tempo real para remover exemplos "bad".
        
        * `frequencyCount`: Um número inteiro que representa a frequência desse par de tradução nos dados. A principal finalidade deste campo é fornecer uma interface do usuário com um meio para ordenar back traduções para que os termos mais frequentes são primeiro.

    > [!NOTE]
    > Se o termo que está a ser pesquisado-up não existe no dictionary, a resposta é 200 (OK), mas a `translations` lista é uma lista vazia.

## <a name="examples"></a>Exemplos

Este exemplo mostra como procurar traduções alternativas em espanhol do termo em inglês `fly` .

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

---

O corpo de resposta (abreviado para efeitos de clareza) é:

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

Este exemplo mostra o que acontece quando o termo que está a ser pesquisado não existe para o par de dicionário válido.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

---

Uma vez que o termo não foi encontrado no dicionário, o corpo da resposta inclui vazio `translations` lista.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
