---
title: Habilidades de pesquisa cognitiva de OCR-Azure Search
description: Extraia texto de arquivos de imagem usando OCR (reconhecimento óptico de caracteres) em um pipeline de enriquecimento Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 903673e2c953328e90029938a9b7446271411646
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422999"
---
# <a name="ocr-cognitive-skill"></a>Habilidade cognitiva de OCR

A habilidade de reconhecimento óptico de caracteres (OCR) reconhece texto impresso e manuscrito em arquivos de imagem. Essa habilidade usa os modelos de aprendizado de máquina fornecidos pelo [Pesquisa Visual computacional](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) em serviços cognitivas. A habilidade do **OCR** é mapeada para a seguinte funcionalidade:

+ A API ["OCR"](../cognitive-services/computer-vision/concept-recognizing-text.md#ocr-optical-character-recognition-api) é usada para idiomas diferentes do inglês. 
+ Para o inglês, a nova API de ["leitura"](../cognitive-services/computer-vision/concept-recognizing-text.md#read-api) é usada.

A habilidade de **OCR** extrai texto de arquivos de imagem. Os formatos de arquivo com suporte incluem:

+ .JPEG
+ .JPG
+ .PNG
+ .BMP
+ .GIF
+ .TIFF

> [!NOTE]
> Ao expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de ia, você precisará [anexar um recurso de serviços cognitivas cobráveis](cognitive-search-attach-cognitive-services.md). As cobranças são acumuladas ao chamar APIs em serviços cognitivas e para extração de imagem como parte do estágio de quebra de documento no Azure Search. Não há encargos para a extração de texto de documentos.
>
> A execução de habilidades internas é cobrada pelo [preço pago pelo uso dos serviços cognitivas](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. O preço de extração de imagem é descrito na [página de preços de Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros diferenciam maiúsculas de minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| detectOrientation | Habilita a detecção automática da orientação da imagem. <br/> Valores válidos: true/false.|
|defaultLanguageCode | <p>  Código de idioma do texto de entrada. As linguagens suportadas incluem: <br/> zh-Hans (ChineseSimplified) <br/> zh-Hant (ChineseTraditional) <br/>CS (tcheco) <br/>da (dinamarquês) <br/>NL (Holandês) <br/>EN (inglês) <br/>Fi (finlandês)  <br/>fr (francês) <br/>  de (alemão) <br/>El (grego) <br/> Hu (húngaro) <br/> It (italiano) <br/>  Ja (japonês) <br/> KO (coreano) <br/> NB (norueguês) <br/>   pl (polonês) <br/> pt (Português) <br/>  ru (russo) <br/>  es (espanhol) <br/>  VA (Sueco) <br/>  TR (Turco) <br/> ar (árabe) <br/> ro (romeno) <br/> Sr-Cyrl (SerbianCyrillic) <br/> Sr-Latn (SerbianLatin) <br/>  SK (eslovaco). <br/>  UNK (desconhecido) <br/><br/> Se o código de idioma não for especificado ou for nulo, o idioma será definido como inglês. Se o idioma estiver explicitamente definido como "UNK", o idioma será detectado automaticamente. </p> |
|lineEnding | O valor a ser usado entre cada linha detectada. Valores possíveis: ' Space ', ' CarriageReturn ', ' alimentação de espaço '.  O padrão é ' Space ' |

Anteriormente, havia um parâmetro chamado "textExtractionAlgorithm" para especificar se a habilidade deve extrair texto "impresso" ou "manuscrito".  Esse parâmetro é preterido e não é mais necessário, pois o algoritmo mais recente da API de leitura é capaz de extrair os dois tipos de texto de uma só vez.  Se sua definição de habilidade já incluir esse parâmetro, você não precisará removê-lo, mas ele não será mais usado e os dois tipos de texto serão extraídos no futuro, independentemente do que está definido como.

## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada      | Descrição                                          |
|---------------|------------------------------------------------------|
| image         | Tipo complexo. No momento, só funciona com o campo "/Document/normalized_images", produzido pelo indexador de ```imageAction``` blob do Azure quando é definido com ```none```um valor diferente de. Consulte o [exemplo](#sample-output) para obter mais informações.|


## <a name="skill-outputs"></a>Saídas de habilidades
| Nome da saída     | Descrição                   |
|---------------|-------------------------------|
| text          | Texto sem formatação extraído da imagem.   |
| layoutText    | Tipo complexo que descreve o texto extraído e o local onde o texto foi encontrado.|


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

## <a name="sample-text-and-layouttext-output"></a>Texto de exemplo e saída de layoutText

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

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Exemplo: Mesclagem de texto extraído de imagens inseridas com o conteúdo do documento.

Um caso de uso comum para a fusão de texto é a capacidade de mesclar a representação textual das imagens (texto de uma habilidade de OCR ou a legenda de uma imagem) no campo de conteúdo de um documento.

O seguinte o skillset de exemplo cria um campo *merged_text* . Este campo contém o conteúdo textual do seu documento e o texto OCRed de cada uma das imagens inseridas nesse documento.

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
O exemplo do skillble acima supõe que exista um campo de imagens normalizadas. Para gerar esse campo, defina a  configuração imageaction na definição do indexador como *generateNormalizedImages* , conforme mostrado abaixo:

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
+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
+ [Habilidade do textmerger](cognitive-search-skill-textmerger.md)
+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
+ [Criar indexador (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
