---
title: Habilidade cognitiva dividida de texto
titleSuffix: Azure Cognitive Search
description: Parta o texto em pedaços ou páginas de texto com base no comprimento de um oleoduto de enriquecimento de IA na Pesquisa Cognitiva de Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 52aaeb01fef551eee350c6db662c2690ef7b3e78
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84981953"
---
# <a name="text-split-cognitive-skill"></a>Habilidade cognitiva dividida de texto

A habilidade **text split** divide texto em pedaços de texto. Pode especificar se pretende dividir o texto em frases ou em páginas de um determinado comprimento. Esta habilidade é especialmente útil se houver requisitos máximos de comprimento de texto noutras competências a jusante. 

> [!NOTE]
> Esta habilidade não está ligada a uma API de Serviços Cognitivos e você não é cobrado por usá-lo. No entanto, deve ainda [anexar um recurso de Serviços Cognitivos](cognitive-search-attach-cognitive-services.md)para anular a opção de recursos **Gratuitos** que o limita a um pequeno número de enriquecimentos diários por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.text.splitSkill 

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis às maiúsculas e minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| `textSplitMode`    | Ou "páginas" ou "frases" | 
| `maximumPageLength` | Se o textoSplitMode estiver definido como "páginas", este refere-se ao comprimento máximo da página medido por `String.Length` . O valor mínimo é 300.  Se o textoSplitMode estiver definido como "páginas", o algoritmo tentará dividir o texto em pedaços que são no máximo "Comprimento De PáginaL" em tamanho. Neste caso, o algoritmo fará o seu melhor para quebrar a frase num limite de frase, pelo que o tamanho do pedaço pode ser ligeiramente inferior ao "comprimento máximo de PageL". | 
| `defaultLanguageCode` | (opcional) Um dos seguintes códigos linguísticos: `da, de, en, es, fi, fr, it, ko, pt` . O padrão é inglês (pt). Poucas coisas a considerar:<ul><li>Se passar num formato de código de identificação de idiomas, apenas é utilizada a parte do código de idiomas do formato.</li><li>Se a língua não estiver na lista anterior, a habilidade dividida quebra o texto nos limites do personagem.</li><li>Fornecer um código linguístico é útil para evitar cortar uma palavra ao meio para línguas não-brancas como o chinês, o japonês e o coreano.</li><li>Se não conhece o idioma (ou seja, tem de dividir o texto para entrada no [LanguageDetectionSkill),](cognitive-search-skill-language-detection.md)o padrão de inglês (pt) deve ser suficiente. </li></ul>  |


## <a name="skill-inputs"></a>Entradas de habilidades

| Nome do parâmetro       | Descrição      |
|----------------------|------------------|
| `text`    | O texto para dividir em sub-corda. |
| `languageCode`    | (Opcional) Código linguístico para o documento. Se não conhece o idioma (ou seja, tem de dividir o texto para inserir no [LanguageDetectionSkill),](cognitive-search-skill-language-detection.md)é seguro remover esta entrada.  |

## <a name="skill-outputs"></a>Saídas de competências 

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| `textItems`   | Uma série de sublmotores que foram extraídos. |


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
Se uma língua não for suportada, é gerada uma advertência e o texto é dividido nos limites do personagem.

## <a name="see-also"></a>Ver também

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um skillset](cognitive-search-defining-skillset.md)
