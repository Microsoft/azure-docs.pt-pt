---
title: Habilidade cognitiva OCR
titleSuffix: Azure Cognitive Search
description: Extrair texto de ficheiros de imagem usando reconhecimento de caracteres óticos (OCR) num oleoduto de enriquecimento em Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 8b6a7c3e05b26cbda80ebf1a3fc0d4fed8255e6b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91950810"
---
# <a name="ocr-cognitive-skill"></a>Habilidade cognitiva OCR

A habilidade **de reconhecimento de caracteres óticos (OCR)** reconhece texto impresso e manuscrito em ficheiros de imagem. Esta habilidade utiliza os modelos de aprendizagem automática fornecidos pela [Computer Vision](../cognitive-services/computer-vision/overview.md) API [v3.0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) em Serviços Cognitivos. As habilidades **OCR** mapeiam para a seguinte funcionalidade:

+ Para inglês, espanhol, alemão, francês, italiano, português e holandês, é utilizada a nova API ["Read".](../cognitive-services/computer-vision/concept-recognizing-text.md#read-api)
+ Para todas as outras línguas, é utilizada a API ["OCR".](../cognitive-services/computer-vision/concept-recognizing-text.md#ocr-api)

A habilidade **OCR** extrai texto de ficheiros de imagem. Os formatos de ficheiros suportados incluem:

+ . JPEG
+ . JPG
+ . PNG
+ . BMP
+ . GIF
+ . TIFF

> [!NOTE]
> À medida que expande o âmbito, aumentando a frequência do processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, terá de [anexar um recurso de Serviços Cognitivos faturados.](cognitive-search-attach-cognitive-services.md) As taxas acumulam-se ao chamar APIs em Serviços Cognitivos, e para a extração de imagem como parte da fase de cracking de documentos em Azure Cognitive Search. Não há encargos para a extração de texto a partir de documentos.
>
> A execução de competências incorporadas é cobrada pelo preço de pagamento dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. Os preços de extração de imagem são descritos na [página de preços de Pesquisa Cognitiva Azure](https://azure.microsoft.com/pricing/details/search/).


## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis às maiúsculas e minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| `detectOrientation`   | Permite a autodestecer a orientação da imagem. <br/> Valores válidos: verdadeiros / falsos.|
| `defaultLanguageCode` | <p>   Código linguístico do texto de entrada. As linguagens suportadas incluem: <br/> zh-hans (Chinêss simplificados) <br/> zh-Hant (tradicional chinês) <br/>cs (checo) <br/>da (dinamarquês) <br/>nl (holandês) <br/>en (inglês) <br/>fi (finlandês)  <br/>fr (francês) <br/>  de (alemão) <br/>el (grego) <br/> hu (húngaro) <br/> que (italiano) <br/>  ja (japonês) <br/> ko (coreano) <br/> nb (norueguês) <br/>   pl (polaco) <br/> pt (português) <br/>  ru (russo) <br/>  es (espanhol) <br/>  sv (sueco) <br/>  tr (turco) <br/> ar (árabe) <br/> ro (romeno) <br/> sr-Cyrl (SérvioCyrillic) <br/> sr-Latn (SérvioLatino) <br/>  sk (eslovaco) <br/>  unk (Desconhecido) <br/><br/> Se o código linguístico não for especificado ou nulo, o idioma será definido para inglês. Se a língua for explicitamente definida como "unk", a língua será detetada automaticamente. </p> |
| `lineEnding` | O valor a utilizar entre cada linha detetada. Valores possíveis: "Space", "CarriageReturn", "LineFeed".  O padrão é "Espaço". |

Anteriormente, havia um parâmetro chamado "textExtractionAlgorithm" para especificar se a habilidade deveria extrair texto "impresso" ou "manuscrito".  Este parâmetro é precotado e já não é necessário, uma vez que o mais recente algoritmo de API de leitura é capaz de extrair ambos os tipos de texto ao mesmo tempo.  Se a sua definição de habilidade já inclui este parâmetro, não precisa removê-lo, mas deixará de ser utilizado e ambos os tipos de texto serão extraídos para a frente independentemente do que está definido.

## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada      | Description                                          |
|---------------|------------------------------------------------------|
| `image`         | Tipo complexo. Atualmente, apenas funciona com o campo "/document/normalized_images", produzido pelo indexante Azure Blob quando ```imageAction``` é definido para um valor diferente de ```none``` . Consulte a [amostra](#sample-output) para mais informações.|


## <a name="skill-outputs"></a>Saídas de competências
| Nome de saída     | Description                   |
|---------------|-------------------------------|
| `text`            | Texto simples extraído da imagem.   |
| `layoutText`    | Tipo complexo que descreve o texto extraído e o local onde o texto foi encontrado.|


## <a name="sample-definition"></a>Definição de amostra

```json
{
  "skills": [
    {
      "description": "Extracts text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": null,
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text",
          "targetName": "myText"
        },
        {
          "name": "layoutText",
          "targetName": "myLayoutText"
        }
      ]
    }
  ]
}
```
<a name="sample-output"></a>

## <a name="sample-text-and-layouttext-output"></a>Texto de amostra e layoutProdução detexto

```json
{
  "text": "Hello World. -John",
  "layoutText":
  {
    "language" : "en",
    "text" : "Hello World. -John",
    "lines" : [
      {
        "boundingBox":
        [ {"x":10, "y":10}, {"x":50, "y":10}, {"x":50, "y":30},{"x":10, "y":30}],
        "text":"Hello World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ],
    "words": [
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"Hello"
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ]
  }
}
```

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Amostra: Fusão de texto extraído de imagens incorporadas com o conteúdo do documento.

Um caso de uso comum para fusão de texto é a capacidade de fundir a representação textual de imagens (texto de uma habilidade OCR, ou a legenda de uma imagem) no campo de conteúdo de um documento.

O exemplo seguinte skillset cria um *campo merged_text.* Este campo contém o conteúdo textual do seu documento e o texto OCRed de cada uma das imagens incorporadas nesse documento.

#### <a name="request-body-syntax"></a>Sintaxe do Corpo do Pedido
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
O exemplo de skillset acima pressupõe que existe um campo de imagens normalizadas. Para gerar este campo, desaprote a *configuração imageAction* na definição de indexante para *gerar ImagensNormalizadas* como mostrado abaixo:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters": {
    "configuration": {
      "dataToExtract":"contentAndMetadata",
      "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Ver também
+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Habilidade TextMerger](cognitive-search-skill-textmerger.md)
+ [Como definir um skillset](cognitive-search-defining-skillset.md)
+ [Criar Indexador (REST)](/rest/api/searchservice/create-indexer)