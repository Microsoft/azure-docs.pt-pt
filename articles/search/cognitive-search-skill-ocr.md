---
title: Habilidade cognitiva ocr
titleSuffix: Azure Cognitive Search
description: Extrair texto de ficheiros de imagem utilizando o reconhecimento ótico de caracteres (OCR) num pipeline de enriquecimento em Pesquisa Cognitiva Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bdb510113a8d65ac04b54e77158f46d03cccd9de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72791933"
---
# <a name="ocr-cognitive-skill"></a>Habilidade cognitiva ocr

A habilidade ótica de reconhecimento de **caracteres (OCR)** reconhece texto impresso e manuscrito em ficheiros de imagem. Esta habilidade utiliza os modelos de machine learning fornecidos pela [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) in Cognitive Services. Os mapas de habilidades **oCR** para a seguinte funcionalidade:

+ A API ["OCR"](../cognitive-services/computer-vision/concept-recognizing-text.md#ocr-optical-character-recognition-api) é utilizada para outras línguas que não o inglês. 
+ Para inglês, é utilizada a nova API ["Ler".](../cognitive-services/computer-vision/concept-recognizing-text.md#read-api)

A habilidade **OCR** extrai texto de ficheiros de imagem. Os formatos de ficheirosuportados incluem:

+ . JPEG
+ . JPG
+ . PNG
+ . BMP
+ . GIF
+ . TIFF

> [!NOTE]
> À medida que expande o âmbito aumentando a frequência do processamento, adicionando mais documentos, ou adicionando mais algoritmos de IA, terá de [anexar um recurso de Serviços Cognitivos faturados.](cognitive-search-attach-cognitive-services.md) As acusações acumulam-se quando se ligam para apis em Serviços Cognitivos, e para extração de imagem como parte da fase de quebra de documentos na Pesquisa Cognitiva Azure. Não há encargos para a extração de texto de documentos.
>
> A execução de competências incorporadas é cobrada pelo preço de pagamento dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. Os preços de extração de imagem são descritos na página de preços da [Pesquisa Cognitiva Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis às maiúsculas e minúsculas.

| Nome do parâmetro     | Descrição |
|--------------------|-------------|
| detectarOrientação | Permite a deteção automática da orientação da imagem. <br/> Valores válidos: verdadeiros / falsos.|
|código de idioma padrão | <p>  Código linguístico do texto de entrada. As linguagens suportadas incluem: <br/> zh-Hans (ChineseSimplified) <br/> zh-Hant (ChinêsTradicional) <br/>cs (checo) <br/>da (dinamarquês) <br/>nl (holandês) <br/>en (inglês) <br/>fi (finlandês)  <br/>fr (francês) <br/>  de (alemão) <br/>el (grego) <br/> hu (húngaro) <br/> lo (italiano) <br/>  ja (japonês) <br/> ko (coreano) <br/> nb (norueguês) <br/>   pl (polaco) <br/> pt (Português) <br/>  ru (russo) <br/>  es (espanhol) <br/>  sv (sueco) <br/>  tr (turco) <br/> ar (árabe) <br/> ro (romeno) <br/> sr-Cyrl (sérviocyrillic) <br/> sr-Latn (sérvio latino) <br/>  sk (eslovaco). <br/>  unk (Desconhecido) <br/><br/> Se o código de idioma não for especificado ou nulo, a língua será definida para inglês. Se a linguagem estiver explicitamente definida para "unk", a linguagem será detetada automaticamente. </p> |
|linhaEnding | O valor a utilizar entre cada linha detetada. Valores possíveis: 'Espaço','CarriageReturn','LineFeed'.  O padrão é 'Space' |

Anteriormente, existia um parâmetro chamado "TextExtractionAlgorithm" para especificar se a habilidade deveria extrair texto "impresso" ou "manuscrito".  Este parâmetro é depreciado e já não é necessário, uma vez que o mais recente algoritmo de Read API é capaz de extrair ambos os tipos de texto ao mesmo tempo.  Se a sua definição de habilidade já incluir este parâmetro, não precisa de o remover, mas deixará de ser utilizado e ambos os tipos de texto serão extraídos, independentemente do que esteja definido.

## <a name="skill-inputs"></a>Inputs de habilidade

| Nome de entrada      | Descrição                                          |
|---------------|------------------------------------------------------|
| image         | Tipo complexo. Atualmente, apenas funciona com o campo "/documento/normalized_images", produzido ```imageAction``` pelo indexante Azure ```none```Blob quando é fixado para um valor diferente de . Consulte a [amostra](#sample-output) para mais informações.|


## <a name="skill-outputs"></a>Saídas de habilidades
| Nome de saída     | Descrição                   |
|---------------|-------------------------------|
| texto          | Texto simples extraído da imagem.   |
| layoutTexto    | Tipo complexo que descreve o texto extraído e o local onde o texto foi encontrado.|


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

## <a name="sample-text-and-layouttext-output"></a>Saída de texto e layout da amostra

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

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Amostra: Texto de fusão extraído de imagens embutidas com o conteúdo do documento.

Um caso comum de utilização para a Fusão de Textos é a capacidade de fundir a representação textual de imagens (texto de uma habilidade OCR, ou a legenda de uma imagem) no campo de conteúdo de um documento.

O exemplo seguinte cria um campo *merged_text.* Este campo contém o conteúdo textual do seu documento e o texto OCRed de cada uma das imagens incorporadas nesse documento.

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
O exemplo de habilidade acima assume que existe um campo de imagens normalizadas. Para gerar este campo, detete a configuração *imageAction* na definição do indexador para *gerarImagens Normalizadas* como mostrado abaixo:

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
+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Habilidade de fusão de texto](cognitive-search-skill-textmerger.md)
+ [Como definir um conjunto de habilidades](cognitive-search-defining-skillset.md)
+ [Criar Indexador (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
