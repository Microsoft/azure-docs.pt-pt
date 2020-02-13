---
title: Habilidade cognitiva de fusão de texto
titleSuffix: Azure Cognitive Search
description: Fundir texto de uma coleção de campos num campo consolidado. Use esta habilidade cognitiva num oleoduto de enriquecimento de IA em Pesquisa Cognitiva Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 98ea416305f080850d85498f74693eb2d45b0944
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162349"
---
#   <a name="text-merge-cognitive-skill"></a>Habilidade cognitiva de fusão de texto

A habilidade **Text Merge** consolida texto de uma coleção de campos num único campo. 

> [!NOTE]
> Esta habilidade não está ligada a uma API de Serviços Cognitivos e você não é cobrado por usá-lo. No entanto, deve ainda [anexar um recurso dos Serviços Cognitivos](cognitive-search-attach-cognitive-services.md)para anular a opção de recursos **gratuitos** que o limita a um pequeno número de enriquecimentos diários por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.MergeSkill

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis aos casos.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| inserirPreTag  | Corda a ser incluída antes de cada inserção. O valor predefinido é `" "`. Para omitir o espaço, detete o valor para `""`.  |
| insertPostTag | Corda a ser incluída após cada inserção. O valor predefinido é `" "`. Para omitir o espaço, detete o valor para `""`.  |


##  <a name="sample-input"></a>Entrada da amostra
Um documento da JSON que forneça uma entrada utilizável para esta habilidade pode ser:

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

##  <a name="sample-output"></a>Resultado da amostra
Este exemplo mostra a saída da entrada anterior, assumindo que o *preserção* de entrada está definido para `" "`, e *a inserção PostTag* está definida para `""`. 

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

## <a name="extended-sample-skillset-definition"></a>Definição de habilidade de amostra estendida

Um cenário comum para a utilização do Text Merge é fundir a representação textual de imagens (texto de uma habilidade OCR, ou a legenda de uma imagem) no campo de conteúdo de um documento. 

O exemplo seguinte utiliza a habilidade OCR para extrair texto de imagens incorporadas no documento. Em seguida, cria um campo *merged_text* para conter texto original e OCRed de cada imagem. Pode saber mais sobre a habilidade do OCR [aqui.](https://docs.microsoft.com/azure/search/cognitive-search-skill-ocr)

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
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
O exemplo acima pressupõe que existe um campo de imagens normalizadas. Para obter o campo de imagens normalizadas, detete a configuração *imageAction* na definição do indexador para *gerarImagens Normalizadas* como mostrado abaixo:

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

## <a name="see-also"></a>Veja também

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de habilidades](cognitive-search-defining-skillset.md)
+ [Criar Indexer (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
