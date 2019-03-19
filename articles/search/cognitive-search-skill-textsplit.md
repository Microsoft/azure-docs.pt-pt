---
title: Texto a dividir a habilidade de pesquisa cognitiva - Azure Search
description: Divida texto em segmentos ou páginas de texto com base no comprimento num pipeline de enriquecimento de Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: c860066608f1b78d9a4c45b03fb3948b3c888e25
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57894211"
---
#   <a name="text-split-cognitive-skill"></a>Texto a dividir competências cognitivas

O **divisão de texto** habilidade quebra o texto em segmentos de texto. Pode especificar se pretende dividem o texto em frases ou em páginas de um comprimento específico. Essa habilidade é especialmente útil se existirem texto máximo requisitos de comprimento de outras habilidades downstream. 

> [!NOTE]
> Essa habilidade não está vinculada a uma API dos serviços cognitivos e não lhe é cobrados usá-lo. Deve ainda [anexar um recurso dos serviços cognitivos](cognitive-search-attach-cognitive-services.md), no entanto, para substituir o **gratuito** opção de recursos que limita a um pequeno número de diário possível por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Parâmetros de habilidades

Parâmetros diferenciam maiúsculas de minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| textSplitMode      | "Páginas" ou "frases" | 
| maximumPageLength | Se textSplitMode estiver definido como "páginas", isso se refere ao comprimento máximo de página, medido pela `String.Length`. O valor mínimo é 100.  Se o textSplitMode estiver definido como "páginas", o algoritmo irá tentar dividir o texto em partes que têm mais de "maximumPageLength" de tamanho. Neste caso, o algoritmo fará o melhor para interromper a frase num limite de sentença, portanto, o tamanho do segmento pode ser um pouco menor que "maximumPageLength". | 
| defaultLanguageCode   | (opcional) Um dos seguintes códigos de idioma: `da, de, en, es, fi, fr, it, ko, pt`. A predefinição é o inglês (en). Alguns aspetos a considerar:<ul><li>Se passar um formato de languagecode-indicativo do país, é utilizada apenas a parte de languagecode no formato.</li><li>Se o idioma não estiver na lista anterior, a habilidade de divisão quebra o texto em limites dos caracteres.</li><li>Fornecer um código de idioma é útil para evitar cortando uma palavra em metade para não espaço idiomas como chinês, japonês e coreano.</li></ul>  |


## <a name="skill-inputs"></a>Entradas de habilidades

| Nome do parâmetro       | Descrição      |
|----------------------|------------------|
| texto  | O texto a dividir em subcadeia. |
| languageCode  | (Opcional) Código de idioma para o documento.  |

## <a name="skill-outputs"></a>Saídas de habilidades 

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| textItems | Uma matriz de subcadeias de carateres que foram extraídos. |


##  <a name="sample-definition"></a>Definição de exemplo

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

##  <a name="sample-input"></a>Entrada de exemplo

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "This is a the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia…",
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

##  <a name="sample-output"></a>Saída de exemplo

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
Se um idioma não é suportado, é gerado um aviso e o texto for dividido em limites dos caracteres.

## <a name="see-also"></a>Consulte também

+ [Competências predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
