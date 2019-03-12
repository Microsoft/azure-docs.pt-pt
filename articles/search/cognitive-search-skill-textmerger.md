---
title: Habilidade de pesquisa cognitiva de intercalação de texto - Azure Search
description: Mescle o texto de uma coleção de campos num campo consolidado. Utilize este cognitiva habilidade num pipeline de enriquecimento de Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: ceda22394aab27f27740bb999b36e2cc46a6bd06
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57568902"
---
#    <a name="text-merge-cognitive-skill"></a>Habilidade de cognitiva de intercalação de texto

O **intercalar texto** habilidade consolida texto de uma coleção de campos num único campo. 

> [!NOTE]
> Essa habilidade não está vinculada a uma API dos serviços cognitivos e não lhe é cobrados usá-lo. Deve ainda [anexar um recurso dos serviços cognitivos](cognitive-search-attach-cognitive-services.md), no entanto, para substituir o **gratuito** opção de recursos que limita a um pequeno número de diário possível por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.MergeSkill

## <a name="skill-parameters"></a>Parâmetros de habilidades

Parâmetros diferenciam maiúsculas de minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| insertPreTag  | A cadeia de caracteres a serem incluídos antes de cada inserção. O valor predefinido é `" "`. Ao omitir o espaço, defina o valor como `""`.  |
| insertPostTag | A cadeia de caracteres a serem incluídos após cada inserção. O valor predefinido é `" "`. Ao omitir o espaço, defina o valor como `""`.  |


##  <a name="sample-input"></a>Entrada de exemplo
Um documento JSON fornecer a entrada de utilizável por essa habilidade poderia ser:

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
Este exemplo mostra a saída da entrada anterior, partindo do princípio de que o *insertPreTag* está definida como `" "`, e *insertPostTag* está definido como `""`. 

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

## <a name="extended-sample-skillset-definition"></a>Definição de conjunto de capacidades de exemplo expandida

É um cenário comum para a utilização de intercalação de texto intercalar a representação textual de imagens (texto de uma habilidade de OCR, ou a legenda de uma imagem) no campo de conteúdo de um documento. 

O conjunto de capacidades de exemplo seguinte utiliza a habilidade de OCR para extrair texto de imagens incorporadas no documento. Em seguida, cria um *merged_text* campo para conter original e OCRed texto de cada imagem. Pode saber mais sobre a habilidade de OCR [aqui](https://docs.microsoft.com/en-us/azure/search/cognitive-search-skill-ocr).

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
O exemplo acima parte do princípio de que existe um campo de imagens normalizados. Para obter o campo de imagens normalizados, defina o *imageAction* configuração na sua definição de indexador ao *generateNormalizedImages* conforme mostrado abaixo:

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

+ [Competências predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
+ [Criar indexador (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
