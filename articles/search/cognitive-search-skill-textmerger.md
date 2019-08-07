---
title: Habilidade de pesquisa cognitiva de mesclagem de texto – Azure Search
description: Mesclar texto de uma coleção de campos em um campo consolidado. Use essa habilidade cognitiva em um pipeline de enriquecimento de Azure Search.
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
ms.openlocfilehash: 312caf2d514d630c5bc1fb7755b7ab7a6a3d443a
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840904"
---
#    <a name="text-merge-cognitive-skill"></a>Habilidade cognitiva de mesclagem de texto

A habilidade de mesclagem de **texto** consolida o texto de uma coleção de campos em um único campo. 

> [!NOTE]
> Essa habilidade não está associada a uma API de serviços cognitivas e você não é cobrado por usá-la. No entanto, você ainda deve [anexar um recurso de serviços cognitivas](cognitive-search-attach-cognitive-services.md)para substituir a opção de recurso **gratuito** que limita você a um pequeno número de aprimoramentos diários por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.MergeSkill

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros diferenciam maiúsculas de minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| insertPreTag  | Cadeia de caracteres a ser incluída antes de cada inserção. O valor predefinido é `" "`. Para omitir o espaço, defina o valor `""`como.  |
| insertPostTag | Cadeia de caracteres a ser incluída após cada inserção. O valor predefinido é `" "`. Para omitir o espaço, defina o valor `""`como.  |


##  <a name="sample-input"></a>Entrada de exemplo
Um documento JSON que fornece entrada utilizável para essa habilidade pode ser:

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "text": "The brown fox jumps over the dog",
        "itemsToInsert": ["quick", "lazy"],
        "offsets": [3, 28],
      }
    }
  ]
}
```

##  <a name="sample-output"></a>Resultado da amostra
Este exemplo mostra a saída da entrada anterior, supondo que *insertPreTag* está definido como `" "`e *insertPostTag* é definido como `""`. 

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

## <a name="extended-sample-skillset-definition"></a>Definição de contended de amostra estendida

Um cenário comum para usar a mesclagem de texto é mesclar a representação textual das imagens (texto de uma habilidade de OCR ou a legenda de uma imagem) no campo de conteúdo de um documento. 

O seguinte meio de habilidade de exemplo usa a habilidade de OCR para extrair texto de imagens inseridas no documento. Em seguida, ele cria um campo *merged_text* para conter o texto original e o OCRed de cada imagem. Você pode saber mais sobre a habilidade de OCR [aqui](https://docs.microsoft.com/azure/search/cognitive-search-skill-ocr).

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
O exemplo acima pressupõe que exista um campo de imagens normalizadas. Para obter o campo de imagens normalizadas, defina a configuração imageaction na definição do indexador como *generateNormalizedImages* , conforme mostrado abaixo:

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

## <a name="see-also"></a>Consulte também

+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
+ [Criar indexador (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
