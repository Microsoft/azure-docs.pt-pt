---
title: Habilidade de pesquisa cognitiva de análise de imagem - Azure Search
description: Extrair texto semântico por meio da análise de imagem usando a habilidade de cognitiva ImageAnalysis num pipeline de enriquecimento de Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 4fb14b49e573770aaef13d6b5d47c265337c408c
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57568928"
---
#   <a name="image-analysis-cognitive-skill"></a>Habilidade de cognitiva de análise de imagem

O **análise de imagem** habilidade extrai um conjunto avançado de recursos visuais com base no conteúdo de imagem. Por exemplo, pode gerar uma legenda a partir de uma imagem, gerar etiquetas ou identificar celebridades e marcos. Essa habilidade usa os modelos de machine learning fornecidos pela [de imagem digitalizada](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) nos serviços cognitivos. 

> [!NOTE]
> A partir de 21 de Dezembro de 2018, pode [anexar um recurso dos serviços cognitivos](cognitive-search-attach-cognitive-services.md) com um conjunto de capacidades do Azure Search. Isso permite que iniciemos a cobrança de consumo para a execução do conjunto de capacidades. Esta data, também Começámos a cobrar para extração de imagem como parte da fase de aberturas de documentos. Extração de texto de documentos continua a ser disponibilizado sem custos adicionais.
>
> [Competências cognitivas incorporada](cognitive-search-predefined-skills.md) execução é cobrada a [dos serviços cognitivos pay as you go preço](https://azure.microsoft.com/pricing/details/cognitive-services), em para classificar o mesmo como se realizados diretamente a tarefa. Extração de imagem é um valor de Azure Search, atualmente oferecido a preços de pré-visualização. Para obter detalhes, consulte a [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400) ou [como funciona a faturação](search-sku-tier.md#how-billing-works).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Vision.ImageAnalysisSkill 

## <a name="skill-parameters"></a>Parâmetros de habilidades

Parâmetros diferenciam maiúsculas de minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| defaultLanguageCode   |  Uma cadeia que indica o idioma para retornar. O serviço retorna resultados de reconhecimento num idioma especificado. Se este parâmetro não for especificado, o valor predefinido é "en". <br/><br/>Idiomas suportados são: <br/>*EN* -inglês (predefinição) <br/> *zh* -chinês simplificado|
|visualFeatures |   Uma matriz de cadeias que indica os tipos de recurso visual para retornar. Os tipos de recurso visual válidos incluem:  <ul><li> *categorias* -categoriza o conteúdo da imagem de acordo com uma taxonomia definido nos serviços cognitivos [documentação](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy).</li><li> *etiquetas* -marca a imagem com uma lista detalhada das palavras relacionadas com o conteúdo de imagem.</li><li>*Descrição* -descreve a imagem de conteúdo com uma sentença em inglês completa.</li><li>*Rostos* -Deteta se os rostos estão presentes. Se estiver presente, gera as coordenadas, sexo e idade.</li><li> *ImageType* -Deteta se a imagem é clip-art ou um desenho de linha.</li><li>  *Cor* -determina a cor de destaque, a cor dominante, e se uma imagem é preta e branco.</li><li>*Para adultos* -Deteta se a imagem é pornográfico por natureza (ilustra nudez ou um ato de sexo). Conteúdo sexualmente suggestive também é detetado.</li></ul> Nomes dos recursos do visual diferenciam maiúsculas de minúsculas.|
| detalhes   | Uma matriz de cadeias que indica quais específicas do domínio fornece detalhes sobre a devolver. Os tipos de recurso visual válidos incluem: <ul><li>*Celebridades* -identifica celebridades detetada na imagem.</li><li>*Pontos de referência* -identifica pontos de referência detetada na imagem.</li></ul>
 |

## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada      | Descrição                                          |
|---------------|------------------------------------------------------|
| image         | Tipo complexo. Atualmente, só funciona com o campo de "/ documento/normalized_images", produzido pelo indexador Blob do Azure quando ```imageAction``` está definido como um valor diferente de ```none```. Consulte a [exemplo](#sample-output) para obter mais informações.|



##  <a name="sample-definition"></a>Definição de exemplo
```json
{
    "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
    "context": "/document/normalized_images/*",
    "visualFeatures": [
        "Tags",
        "Faces",
        "Categories",
        "Adult",
        "Description",
        "ImageType",
        "Color"
    ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "image",
            "source": "/document/normalized_images/*"
        }
    ],
    "outputs": [
        {
            "name": "categories",
            "targetName": "myCategories"
        },
        {
            "name": "tags",
            "targetName": "myTags"
        },
        {
            "name": "description",
            "targetName": "myDescription"
        },
        {
            "name": "faces",
            "targetName": "myFaces"
        },
        {
            "name": "imageType",
            "targetName": "myImageType"
        },
        {
            "name": "color",
            "targetName": "myColor"
        },
        {
            "name": "adult",
            "targetName": "myAdultCategory"
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
                "image": {
                    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
                    "width": 500,
                    "height": 300,
                    "originalWidth": 5000,
                    "originalHeight": 3000,
                    "rotationFromOriginal": 90,
                    "contentOffset": 500
                }
            }
        }
    ]
}
```


##  <a name="sample-output"></a>Resultado da amostra

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "categories": [
                    {
                        "name": "abstract_",
                        "score": 0.00390625
                    },
                    {
                        "name": "people_",
                        "score": 0.83984375,
                        "detail": {
                            "celebrities": [
                                {
                                    "name": "Satya Nadella",
                                    "faceBoundingBox": {
                                        "left": 597,
                                        "top": 162,
                                        "width": 248,
                                        "height": 248
                                    },
                                    "confidence": 0.999028444
                                }
                            ],
                            "landmarks": [
                                {
                                    "name": "Forbidden City",
                                    "confidence": 0.9978346
                                }
                            ]
                        }
                    }
                ],
                "adult": {
                    "isAdultContent": false,
                    "isRacyContent": false,
                    "adultScore": 0.0934349000453949,
                    "racyScore": 0.068613491952419281
                },
                "tags": [
                    {
                        "name": "person",
                        "confidence": 0.98979085683822632
                    },
                    {
                        "name": "man",
                        "confidence": 0.94493889808654785
                    },
                    {
                        "name": "outdoor",
                        "confidence": 0.938492476940155
                    },
                    {
                        "name": "window",
                        "confidence": 0.89513939619064331
                    }
                ],
                "description": {
                    "tags": [
                        "person",
                        "man",
                        "outdoor",
                        "window",
                        "glasses"
                    ],
                    "captions": [
                        {
                            "text": "Satya Nadella sitting on a bench",
                            "confidence": 0.48293603002174407
                        }
                    ]
                },
                "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
                "metadata": {
                    "width": 1500,
                    "height": 1000,
                    "format": "Jpeg"
                },
                "faces": [
                    {
                        "age": 44,
                        "gender": "Male",
                        "faceBoundingBox": {
                            "left": 593,
                            "top": 160,
                            "width": 250,
                            "height": 250
                        }
                    }
                ],
                "color": {
                    "dominantColorForeground": "Brown",
                    "dominantColorBackground": "Brown",
                    "dominantColors": [
                        "Brown",
                        "Black"
                    ],
                    "accentColor": "873B59",
                    "isBwImg": false
                    },
                "imageType": {
                    "clipArtType": 0,
                    "lineDrawingType": 0
                }
            }
        }
    ]
}
```


## <a name="error-cases"></a>Casos de erro
Nos seguintes casos de erro, não existem elementos são extraídos.

| Código de Erro | Descrição |
|------------|-------------|
| NotSupportedLanguage | O idioma fornecido não é suportado. |
| InvalidImageUrl | URL da imagem é formatado incorretamente ou não está acessível.|
| InvalidImageFormat | Dados de entrada não são uma imagem válida. |
| InvalidImageSize | Imagem de entrada é demasiado grande. |
| NotSupportedVisualFeature  | Tipo de recurso especificado não é válido. |
| NotSupportedImage | Imagem não suportada, por exemplo, pornografia infantil. |
| InvalidDetails | Modelo de domínio específico não suportado. |

## <a name="see-also"></a>Consulte também

+ [Competências predefinidas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
+ [Criar indexador (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
