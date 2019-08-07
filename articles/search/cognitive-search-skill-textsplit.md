---
title: Habilidade de pesquisa cognitiva de divisão de texto-Azure Search
description: Quebra o texto em partes ou páginas de texto com base no comprimento em um pipeline de enriquecimento de Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 8fb7ff51507212dfb162c09fcee469d6f154f3c3
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840894"
---
#   <a name="text-split-cognitive-skill"></a>Habilidade cognitiva de divisão de texto

A habilidade de **divisão de texto** quebra o texto em partes de texto. Você pode especificar se deseja dividir o texto em frases ou em páginas de um comprimento específico. Essa habilidade é especialmente útil se houver requisitos de comprimento de texto máximo em outras habilidades downstream. 

> [!NOTE]
> Essa habilidade não está associada a uma API de serviços cognitivas e você não é cobrado por usá-la. No entanto, você ainda deve [anexar um recurso de serviços cognitivas](cognitive-search-attach-cognitive-services.md)para substituir a opção de recurso **gratuito** que limita você a um pequeno número de aprimoramentos diários por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros diferenciam maiúsculas de minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| textSplitMode      | "Pages" ou "Sentences" | 
| maximumPageLength | Se textsplitmode for definido como "Pages", isso se refere ao comprimento máximo da página, conforme `String.Length`medido por. O valor mínimo é 100.  Se textsplitmode for definido como "Pages", o algoritmo tentará dividir o texto em partes que estão no máximo "maximumPageLength" em tamanho. Nesse caso, o algoritmo fará o melhor para quebrar a frase em um limite de frase, de modo que o tamanho da parte pode ser um pouco menor do que "maximumPageLength". | 
| defaultLanguageCode   | adicional Um dos seguintes códigos de idioma: `da, de, en, es, fi, fr, it, ko, pt`. O padrão é inglês (EN). Algumas coisas a serem consideradas:<ul><li>Se você passar um formato languagecode-CountryCode, somente a parte languagecode do formato será usada.</li><li>Se o idioma não estiver na lista anterior, a habilidade de divisão quebrará o texto em limites de caracteres.</li><li>Fornecer um código de idioma é útil para evitar cortar uma palavra na metade para idiomas que não sejam de espaço, como chinês, japonês e coreano.</li></ul>  |


## <a name="skill-inputs"></a>Entradas de habilidades

| Nome do parâmetro       | Descrição      |
|----------------------|------------------|
| text  | O texto a ser dividido em subcadeia de caracteres. |
| languageCode  | Adicional Código de idioma do documento.  |

## <a name="skill-outputs"></a>Saídas de habilidades 

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| textItems | Uma matriz de subcadeias de caracteres que foram extraídas. |


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
Se não houver suporte para um idioma, um aviso será gerado e o texto será dividido em limites de caracteres.

## <a name="see-also"></a>Consulte também

+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
