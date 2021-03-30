---
title: Função cognitiva de fusão de texto
titleSuffix: Azure Cognitive Search
description: Fundir texto de uma coleção de campos num campo consolidado. Use esta habilidade cognitiva num oleoduto de enriquecimento de IA na Pesquisa Cognitiva Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: b364655f26c6ac29c14d387d69d7b4277d6aeb86
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88924644"
---
#   <a name="text-merge-cognitive-skill"></a>Função cognitiva de fusão de texto

A habilidade **text Merge** consolida o texto de uma coleção de campos num único campo. 

> [!NOTE]
> Esta habilidade não está ligada a uma API de Serviços Cognitivos e você não é cobrado por usá-lo. No entanto, deve ainda [anexar um recurso de Serviços Cognitivos](cognitive-search-attach-cognitive-services.md)para anular a opção de recursos **Gratuitos** que o limita a um pequeno número de enriquecimentos diários por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.text.mergeSkill

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis às maiúsculas e minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| `insertPreTag`    | Corda a incluir antes de cada inserção. O valor predefinido é `" "`. Para omitir o espaço, desa um valor para `""` .  |
| `insertPostTag`   | Corda a incluir após cada inserção. O valor predefinido é `" "`. Para omitir o espaço, desa um valor para `""` .  |


##  <a name="sample-input"></a>Entrada de exemplo
Um documento JSON que forneça informações utilizáveis para esta habilidade pode ser:

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "text": "The brown fox jumps over the dog",
        "itemsToInsert": ["quick", "lazy"],
        "offsets": [3, 28]
      }
    }
  ]
}
```

##  <a name="sample-output"></a>Saída de exemplo
Este exemplo mostra a saída da entrada anterior, assumindo que o *Encaixe de inserção* está definido para `" "` , e *inserirPostTag* está definido para `""` . 

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "mergedText": "The quick brown fox jumps over the lazy dog"
      }
    }
  ]
}
```

## <a name="extended-sample-skillset-definition"></a>Definição de skillset de amostra alargada

Um cenário comum para a utilização da Fusão de Texto é fundir a representação textual de imagens (texto a partir de uma habilidade de OCR, ou a legenda de uma imagem) no campo de conteúdo de um documento. 

O exemplo seguinte usa a habilidade OCR para extrair texto de imagens incorporadas no documento. Em seguida, cria um campo *merged_text* para conter texto original e OCRed de cada imagem. Pode saber mais sobre a habilidade de OCR [aqui.](./cognitive-search-skill-ocr.md)

```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
      "description": "Extract text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": "en",
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", 
          "source": "/document/content"
        },
        {
          "name": "itemsToInsert", 
          "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", 
          "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", 
          "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
O exemplo acima pressupõe que existe um campo de imagens normalizadas. Para obter o campo de imagens normalizadas, desaprote a *configuração de imageAction* na definição de indexante para *gerar ImagensNormalizadas* como mostrado abaixo:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":{
    "configuration":{
        "dataToExtract":"contentAndMetadata",
        "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Ver também

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um skillset](cognitive-search-defining-skillset.md)
+ [Criar Indexador (REST)](/rest/api/searchservice/create-indexer)