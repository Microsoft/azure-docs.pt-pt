---
title: Devolva uma resposta semântica
titleSuffix: Azure Cognitive Search
description: Descreve a composição de uma resposta semântica e como obter respostas a partir de um conjunto de resultados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 9bb62544887e0bc0269b98cd98fbf97fc477352f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722434"
---
# <a name="return-a-semantic-answer-in-azure-cognitive-search"></a>Devolva uma resposta semântica na Pesquisa Cognitiva Azure

> [!IMPORTANT]
> A pesquisa semântica está em pré-visualização pública, disponível apenas através da pré-visualização REST API. As funcionalidades de pré-visualização são oferecidas como está, nos [Termos Complementares de Utilização,](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)e não estão garantidas a mesma implementação em disponibilidade geral. Estas características são faturas. Para mais informações, consulte [Disponibilidade e preços.](semantic-search-overview.md#availability-and-pricing)

Ao formular uma [consulta semântica,](semantic-how-to-query-request.md)pode opcionalmente extrair conteúdo dos documentos de topo que "respondem" diretamente à consulta. Uma ou mais respostas podem ser incluídas na resposta, que pode então render numa página de pesquisa para melhorar a experiência do utilizador da sua aplicação.

Neste artigo, aprenda a pedir uma resposta semântica, desembale a resposta e saiba quais as características de conteúdo mais propícias à produção de respostas de alta qualidade.

## <a name="prerequisites"></a>Pré-requisitos

Todos os pré-requisitos aplicáveis às [consultas semânticas](semantic-how-to-query-request.md) também se aplicam a respostas, incluindo nível de serviço e região.

+ A lógica de consulta deve incluir os parâmetros de consulta semântica, além do parâmetro "respostas". Os parâmetros necessários são discutidos neste artigo.

+ As cadeias de consulta inseridas pelo utilizador devem ser formuladas na linguagem com as características de uma pergunta (o quê, onde, quando, como).

+ Os documentos de pesquisa devem conter texto com as características de uma resposta, e esse texto deve existir num dos campos listados em "searchFields". Por exemplo, dada a consulta "o que é uma mesa de haxixe", se nenhum dos campos de pesquisa contiver passagens que incluam "Uma mesa de haxixe é..." então é improvável que uma resposta seja devolvida.

## <a name="what-is-a-semantic-answer"></a>O que é uma resposta semântica?

Uma resposta semântica é uma subestrutura de uma [resposta semântica.](semantic-how-to-query-request.md) Consiste numa ou mais passagens verbais de um documento de pesquisa, formulado como resposta a uma consulta que parece uma pergunta. Para que uma resposta seja devolvida, devem existir frases ou frases num documento de pesquisa que tenha as características linguísticas de uma resposta, e a consulta em si deve ser colocada como uma questão.

A Cognitive Search usa um modelo de compreensão de leitura de máquina para escolher a melhor resposta. O modelo produz um conjunto de respostas potenciais a partir do conteúdo disponível, e quando atinge um nível de confiança suficientemente elevado, irá propor uma resposta.

As respostas são devolvidas como um objeto independente e de alto nível na carga útil de resposta de consulta que pode optar por render nas páginas de pesquisa, juntamente com os resultados da pesquisa. Estruturalmente, é um elemento de matriz dentro da resposta consistindo de texto, uma chave de documento, e uma pontuação de confiança.

<a name="query-params"></a>

## <a name="how-to-request-semantic-answers-in-a-query"></a>Como solicitar respostas semânticas numa consulta

Para responder semântica, a consulta deve ter o semântico "consultaType", "queryLanguage", "searchFields" e o parâmetro "respostas". Especificar o parâmetro "respostas" não garante que obtenha uma resposta, mas o pedido deve incluir este parâmetro para que o processamento de resposta seja invocado.

O parâmetro "searchFields" é crucial para devolver uma resposta de alta qualidade, tanto em termos de conteúdo como de ordem (ver abaixo). 

```json
{
    "search": "how do clouds form",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "title,locations,content",
    "answers": "extractive|count-3",
    "count": "true"
}
```

+ Uma cadeia de consulta não deve ser nula e deve ser formulada como pergunta. Nesta pré-visualização, o "consultaType" e "queryLanguage" devem ser definidos exatamente como mostrado no exemplo.

+ O parâmetro "searchFields" determina quais os campos de cordas que fornecem fichas ao modelo de extração. Os mesmos campos que produzem legendas também produzem respostas. Para obter orientações precisas sobre como definir este campo de modo a que funcione tanto para legendas como para respostas, consulte [set searchFields](semantic-how-to-query-request.md#searchfields). 

+ Para "respostas", a construção `"answers": "extractive"` de parâmetros é, onde o número predefinido de respostas devolvidas é um. Pode aumentar o número de respostas adicionando uma contagem como mostrado no exemplo acima, até um máximo de cinco.  Se precisa de mais do que uma resposta depende da experiência do utilizador da sua aplicação e da forma como pretende obter resultados.

## <a name="deconstruct-an-answer-from-the-response"></a>Desconstruir uma resposta da resposta

As respostas são fornecidas na @search.answers matriz, que aparece primeiro na resposta. Se uma resposta for indeterminada, a resposta aparecerá como `"@search.answers": []` . Ao desenhar uma página de resultados de pesquisa que inclua respostas, certifique-se de lidar com casos em que as respostas não são encontradas.

Dentro @search.answers , a "chave" é a chave de documento ou ID da partida. Tendo em conta uma chave de documento, pode utilizar a [API do Documento de Procura](/rest/api/searchservice/lookup-document) para recuperar qualquer ou todas as partes do documento de pesquisa para incluir na página de pesquisa ou numa página de detalhe.

Tanto o "texto" como os "destaques" fornecem conteúdo idêntico, tanto em texto simples como em destaques. Por predefinição, os destaques são modelados como `<em>` , que pode ser sobreposto usando os parâmetros De DestaquePreTag e destaque. Como observou em outros lugares, a substância de uma resposta é o conteúdo verbatim de um documento de pesquisa. O modelo de extração procura características de uma resposta para encontrar o conteúdo adequado, mas não compõe nova linguagem na resposta.

O "score" é uma pontuação de confiança que reflete a força da resposta. Se houver várias respostas na resposta, esta pontuação é usada para determinar a ordem. As principais respostas e legendas de topo podem ser derivadas de diferentes documentos de pesquisa, onde a resposta superior é originária de um documento, e a legenda superior de outro, mas em geral você verá os mesmos documentos nas posições superiores dentro de cada matriz.

As respostas são seguidas pela matriz de "valor", que inclui sempre pontuações, legendas e quaisquer campos que sejam recuperáveis por padrão. Se especificou o parâmetro selecionado, a matriz de "valor" limita-se aos campos especificados. Para obter mais informações sobre itens na resposta, consulte [Criar uma consulta semântica.](semantic-how-to-query-request.md)

Dada a consulta "como se formam as nuvens", a seguinte resposta é devolvida na resposta:

```json
{
    "@search.answers": [
        {
            "key": "4123",
            "text": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form where air is ascending (over land in this case),   but not where it is descending (over the river).",
            "highlights": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form<em> where air is ascending</em> (over land in this case),   but not where it is<em> descending</em> (over the river).",
            "score": 0.94639826
        }
    ],
    "value": [
        {
            "@search.score": 0.5479723,
            "@search.rerankerScore": 1.0321671911515296,
            "@search.captions": [
                {
                    "text": "Like all clouds, it forms when the air reaches its dew point—the temperature at which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley fog, which is common in the Pacific Northwest of North America.",
                    "highlights": "Like all<em> clouds</em>, it<em> forms</em> when the air reaches its dew point—the temperature at    which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley<em> fog</em>, which is common in the Pacific Northwest of North America."
                }
            ],
            "title": "Earth Atmosphere",
            "content": "Fog is essentially a cloud lying on the ground. Like all clouds, it forms when the air reaches its dew point—the temperature at  \n\nwhich an air mass is cool enough for its water vapor to condense into liquid droplets.\n\nThis false-color image shows valley fog, which is common in the Pacific Northwest of North America. On clear winter nights, the \n\nground and overlying air cool off rapidly, especially at high elevations. Cold air is denser than warm air, and it sinks down into the \n\nvalleys. The moist air in the valleys gets chilled to its dew point, and fog forms. If undisturbed by winds, such fog may persist for \n\ndays. The Terra satellite captured this image of foggy valleys northeast of Vancouver in February 2010.\n\n\n",
            "locations": [
                "Pacific Northwest",
                "North America",
                "Vancouver"
            ]
        }
```

## <a name="tips-for-producing-high-quality-answers"></a>Dicas para produzir respostas de alta qualidade

Para obter melhores resultados, devolva respostas semânticas num corpus de documento com as seguintes características:

+ "searchFields" deve fornecer campos que ofereçam texto suficiente no qual uma resposta é suscetível de ser encontrada. Apenas texto verbatim de um documento pode aparecer como uma resposta.

+ as cordas de consulta não devem ser nulas (pesquisa=) `*` e a cadeia deve ter as características de uma pergunta, em oposição a uma pesquisa de palavras-chave (uma lista sequencial de termos ou frases arbitrárias). Se a cadeia de consulta não parecer ser uma resposta, o processamento de resposta é ignorado, mesmo que o pedido especifique "respostas" como parâmetro de consulta.

+ A extração semântica e a resumo têm limites sobre quantos tokens por documento podem ser analisados em tempo útil. Em termos práticos, se tiver documentos grandes que se esbarram em centenas de páginas, deve tentar dividir o conteúdo em documentos menores primeiro.

## <a name="next-steps"></a>Passos seguintes

+ [Visão geral da pesquisa semântica](semantic-search-overview.md)
+ [Algoritmo de classificação semântica](semantic-ranking.md)
+ [Algoritmo de classificação de semelhança](index-ranking-similarity.md)
+ [Criar uma consulta semântica](semantic-how-to-query-request.md)