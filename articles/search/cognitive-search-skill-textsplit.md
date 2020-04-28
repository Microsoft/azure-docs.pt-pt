---
title: Habilidade cognitiva dividida por texto
titleSuffix: Azure Cognitive Search
description: Parta o texto em pedaços ou páginas de texto com base no comprimento de um oleoduto de enriquecimento de IA em Pesquisa Cognitiva Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3f80169808b1e6420f04b786d2bb06bde9c96231
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73479656"
---
# <a name="text-split-cognitive-skill"></a>Habilidade cognitiva dividida por texto

A habilidade **Text Split** divide texto em pedaços de texto. Pode especificar se pretende dividir o texto em frases ou em páginas de um determinado comprimento. Esta habilidade é especialmente útil se houver requisitos máximos de comprimento de texto noutras competências a jusante. 

> [!NOTE]
> Esta habilidade não está ligada a uma API de Serviços Cognitivos e você não é cobrado por usá-lo. No entanto, deve ainda [anexar um recurso dos Serviços Cognitivos](cognitive-search-attach-cognitive-services.md)para anular a opção de recursos **gratuitos** que o limita a um pequeno número de enriquecimentos diários por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis às maiúsculas e minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| textSplitMode      | Ou "páginas" ou "frases" | 
| maximumPageLength | Se o textoSplitMode for definido para "páginas", este refere-se ao comprimento máximo da página medido por `String.Length`. O valor mínimo é 100.  Se o textoSplitMode estiver definido para "páginas", o algoritmo tentará dividir o texto em pedaços que são no máximo "maximumPageLength" em tamanho. Neste caso, o algoritmo fará o seu melhor para quebrar a frase num limite de frase, pelo que o tamanho do pedaço pode ser ligeiramente inferior ao "maximumPageLength". | 
| código de idioma padrão   | (opcional) Um dos seguintes códigos linguísticos: `da, de, en, es, fi, fr, it, ko, pt`. Padrão é inglês (pt). Poucas coisas a considerar:<ul><li>Se passar um formato de código de idioma-country, apenas é utilizada a parte do idioma do formato.</li><li>Se a linguagem não estiver na lista anterior, a habilidade dividida quebra o texto nos limites do carácter.</li><li>Fornecer um código linguístico é útil para evitar cortar uma palavra ao meio para línguas não-espaciais como o chinês, o japonês e o coreano.</li><li>Se não conhece o idioma (ou seja, tem de dividir o texto para entrada no [IdiomDetectionSkill),](cognitive-search-skill-language-detection.md)o padrão do inglês (en) deve ser suficiente. </li></ul>  |


## <a name="skill-inputs"></a>Inputs de habilidade

| Nome do parâmetro       | Descrição      |
|----------------------|------------------|
| texto  | O texto para dividir em substring. |
| languageCode  | (Opcional) Código linguístico para o documento. Se não conhece o idioma (ou seja, é necessário dividir o texto para entrada no [IdiomDetectionSkill),](cognitive-search-skill-language-detection.md)é seguro remover esta entrada.  |

## <a name="skill-outputs"></a>Saídas de Habilidades 

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| textoItems | Uma série de subcordas que foram extraídas. |


##  <a name="sample-definition"></a>Definição de amostra

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "textItems",
            "targetName": "mypages"
        }
    ]
}
```

##  <a name="sample-input"></a>Entrada de Exemplo

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "This is a the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia…",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
                "languageCode": "en"
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Saída de Exemplo

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>Casos de erro
Se uma linguagem não for suportada, gera-se um aviso e o texto é dividido nos limites do carácter.

## <a name="see-also"></a>Consulte também

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de habilidades](cognitive-search-defining-skillset.md)
