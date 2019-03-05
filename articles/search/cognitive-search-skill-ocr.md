---
title: Habilidade de pesquisa cognitiva OCR - Azure Search
description: Extrair texto de arquivos de imagem usando o reconhecimento ótico de carateres (OCR) num pipeline de enriquecimento de Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 01/17/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: a978e1054367620f25043ab810c3fb78c45d9f72
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57310680"
---
# <a name="ocr-cognitive-skill"></a>Habilidade de cognitiva OCR

Habilidade de reconhecimento ótico de carateres (OCR) reconhece texto impresso e manuscrito nos arquivos de imagem. Essa habilidade usa os modelos de machine learning fornecidos pela [de imagem digitalizada](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) nos serviços cognitivos. O **OCR** habilidade é mapeado para a seguinte funcionalidade:

+ Quando textExtractionAlgorithm está definida para "manuscritas", o ["RecognizeText"](../cognitive-services/computer-vision/quickstarts-sdk/csharp-hand-text-sdk.md) funcionalidade é utilizada.
+ Quando textExtractionAlgorithm está definida para "impressa", o ["OCR"](../cognitive-services/computer-vision/concept-extracting-text-ocr.md) funcionalidade é utilizada para idiomas diferentes do inglês. Para inglês, a nova ["Reconhecer Text"](../cognitive-services/computer-vision/concept-recognizing-text.md) é utilizada a funcionalidade para texto impresso.

O **OCR** habilidade extrai o texto de arquivos de imagem. Formatos de ficheiro suportados incluem:

+ .JPEG
+ .JPG
+ .PNG
+ .BMP
+ .GIF

> [!NOTE]
> A partir de 21 de Dezembro de 2018, pode [anexar um recurso dos serviços cognitivos](cognitive-search-attach-cognitive-services.md) com um conjunto de capacidades do Azure Search. Isso permite que iniciemos a cobrança de consumo para a execução do conjunto de capacidades. Esta data, também Começámos a cobrar para extração de imagem como parte da fase de aberturas de documentos. Extração de texto de documentos continua a ser disponibilizado sem custos adicionais.
>
> [Competências cognitivas incorporada](cognitive-search-predefined-skills.md) execução é cobrada a [dos serviços cognitivos pay as you go preço](https://azure.microsoft.com/pricing/details/cognitive-services), em para classificar o mesmo como se realizados diretamente a tarefa. Extração de imagem é um valor de Azure Search, atualmente oferecido a preços de pré-visualização. Para obter detalhes, consulte a [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400) ou [como funciona a faturação](search-sku-tier.md#how-billing-works).

## <a name="skill-parameters"></a>Parâmetros de habilidades

Parâmetros diferenciam maiúsculas de minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| detectOrientation | Permite autodetection de orientação da imagem. <br/> Valores válidos: Verdadeiro / Falso.|
|defaultLanguageCode | <p>  Código de idioma do texto de entrada. As linguagens suportadas incluem: <br/> zh-Hans (ChineseSimplified) <br/> zh-Hant (ChineseTraditional) <br/>CS (Checa) <br/>NaN (dinamarquês) <br/>NL (Holandês) <br/>en (em inglês) <br/>Fi (Finlandês)  <br/>FR (francês) <br/>  Alemanha (alemão) <br/>EL (Grego) <br/> hu (Húngaro) <br/> ele (italiano) <br/>  ja (japonês) <br/> ko (coreano) <br/> nb (Norueguês) <br/>   PL (Polaco) <br/> Hora do Pacífico (Português) <br/>  RU (Russo) <br/>  es (espanhol) <br/>  SV (Sueco) <br/>  TR (Turco) <br/> ar (Árabe) <br/> ro (Romeno) <br/> SR-Cyrl (SerbianCyrillic) <br/> SR-Latn (SerbianLatin) <br/>  SK (Eslovaco). <br/>  UNK (desconhecido) <br/><br/> Se o código de idioma não especificado ou for nulo, o idioma será definido como inglês. Se o idioma é explicitamente definido para "unk", o idioma será detetado automaticamente. </p> |
| textExtractionAlgorithm | "impresso" ou "manuscritas". O algoritmo de OCR de reconhecimento de texto "manuscritas" está atualmente em pré-visualização e só é suportada em inglês. |

## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada      | Descrição                                          |
|---------------|------------------------------------------------------|
| image         | Tipo complexo. Atualmente, só funciona com o campo de "/ documento/normalized_images", produzido pelo indexador Blob do Azure quando ```imageAction``` está definido como um valor diferente de ```none```. Consulte a [exemplo](#sample-output) para obter mais informações.|


## <a name="skill-outputs"></a>Saídas de habilidades
| Nome de saída     | Descrição                   |
|---------------|-------------------------------|
| texto          | Texto sem formatação extraído a partir da imagem.   |
| layoutText    | Tipo complexo que descreve o texto extraído e a localização onde o texto foi encontrado.|


## <a name="sample-definition"></a>Definição de exemplo

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

## <a name="sample-text-and-layouttext-output"></a>Saída de texto e layoutText de exemplo

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

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Exemplo: Intercalar os textos extraídos dos imagens incorporadas com o conteúdo do documento.

Um caso de utilização comuns para fusão de texto é a capacidade de mesclar a representação textual de imagens (texto de uma habilidade de OCR, ou a legenda de uma imagem) no campo de conteúdo de um documento.

O conjunto de capacidades de exemplo seguinte cria um *merged_text* campo. Este campo contém o conteúdo textual do seu documento e o texto de OCRed de cada uma das imagens incorporadas contidas nesse documento.

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
O exemplo de conjunto de capacidades acima parte do princípio de que existe um campo de imagens normalizados. Para gerar este campo, defina o *imageAction* configuração na sua definição de indexador ao *generateNormalizedImages* conforme mostrado abaixo:

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

## <a name="see-also"></a>Consulte também
+ [Competências predefinidas](cognitive-search-predefined-skills.md)
+ [Habilidade de TextMerger](cognitive-search-skill-textmerger.md)
+ [Como definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
+ [Criar indexador (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
