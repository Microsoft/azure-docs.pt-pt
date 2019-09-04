---
title: Processar e extrair texto de imagens na pesquisa cognitiva – Azure Search
description: Processe e extraia texto e outras informações de imagens em pipelines de pesquisa cognitiva no Azure Search.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.subservice: cognitive-search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: 84109cf04588a5de6fb3fd946a89b5dfee4baa1b
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259158"
---
#  <a name="how-to-process-and-extract-information-from-images-in-cognitive-search-scenarios"></a>Como processar e extrair informações de imagens em cenários de pesquisa cognitiva

A pesquisa cognitiva tem vários recursos para trabalhar com imagens e arquivos de imagem. Durante a quebra de documento, você pode usar o parâmetro imageaction para extrair texto de fotos ou imagens que contêm texto alfanumérico, como a palavra "Stop" em um sinal de parada. Outros cenários incluem a geração de uma representação de texto de uma imagem, como "Dandelion" para uma foto de um dandelion ou a cor "amarelo". Você também pode extrair metadados sobre a imagem, como seu tamanho.

Este artigo aborda o processamento de imagem em mais detalhes e fornece diretrizes para trabalhar com imagens em um pipeline de pesquisa cognitiva.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Obter imagens normalizadas

Como parte da quebra de documento, há um novo conjunto de parâmetros de configuração do indexador para manipular arquivos de imagem ou imagens inseridas em arquivos. Esses parâmetros são usados para normalizar imagens para processamento mais longo do downstream. Normalizar imagens torna-as mais uniformes. Imagens grandes são redimensionadas para uma altura e largura máximas para torná-las consumíveis. Para imagens que fornecem metadados na orientação, a rotação da imagem é ajustada para o carregamento vertical. Os ajustes de metadados são capturados em um tipo complexo criado para cada imagem. 

Não é possível desativar a normalização de imagem. As habilidades que iteram em imagens esperam imagens normalizadas. Habilitar a normalização de imagem em um indexador requer que um conconhecimento seja anexado a esse indexador.

| Parâmetro de configuração | Descrição |
|--------------------|-------------|
| imageAction   | Defina como "nenhum" se nenhuma ação for executada quando imagens inseridas ou arquivos de imagem forem encontrados. <br/>Defina como "generateNormalizedImages" para gerar uma matriz de imagens normalizadas como parte da quebra de documento.<br/>Defina como "generateNormalizedImagePerPage" para gerar uma matriz de imagens normalizadas em que os PDFs em sua fonte de dados, cada página é renderizada para uma imagem de saída.  A funcionalidade é a mesma que "generateNormalizedImages" para tipos de arquivo não PDF.<br/>Para qualquer opção que não seja "None", as imagens serão expostas no campo *normalized_images* . <br/>O padrão é "nenhum". Essa configuração só é pertinente a fontes de dados de BLOB, quando "dataToExtract" é definido como "contentAndMetadata". <br/>Um máximo de 1000 imagens será extraído de um determinado documento. Se houver mais de 1000 imagens em um documento, o primeiro 1000 será extraído e um aviso será gerado. |
|  normalizedImageMaxWidth | A largura máxima (em pixels) para as imagens normalizadas geradas. O padrão é 2000. O valor máximo permitido é 10000. | 
|  normalizedImageMaxHeight | A altura máxima (em pixels) para as imagens normalizadas geradas. O padrão é 2000. O valor máximo permitido é 10000.|

> [!NOTE]
> Se você definir a Propriedade imageaction como algo diferente de "None", não será possível definir a propriedade *parsingMode* como algo diferente de "default".  Você só pode definir uma dessas duas propriedades como um valor não padrão na configuração do indexador.

Defina o parâmetro **parsingMode** como `json` (para indexar cada blob como um único documento) `jsonArray` ou (se seus BLOBs contiverem matrizes JSON e se você precisar que cada elemento de uma matriz seja tratado como um documento separado).

O padrão de 2000 pixels para a largura e a altura máximas das imagens normalizadas baseia-se nos tamanhos máximos com suporte da [habilidade de OCR](cognitive-search-skill-ocr.md) e da [habilidade de análise de imagem](cognitive-search-skill-image-analysis.md). A [habilidade de OCR](cognitive-search-skill-ocr.md) dá suporte a uma largura e altura máxima de 4200 para idiomas que não estão em inglês e 10000 para inglês.  Se você aumentar os limites máximos, o processamento poderá falhar em imagens maiores, dependendo da sua definição de Skills e do idioma dos documentos. 

Você especifica a imageaction na [definição](https://docs.microsoft.com/rest/api/searchservice/create-indexer) do indexador da seguinte maneira:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":
  {
    "configuration": 
    {
        "dataToExtract": "contentAndMetadata",
        "imageAction": "generateNormalizedImages"
    }
  }
}
```

Quando *imageaction* é definido com um valor diferente de "None", o novo campo *normalized_images* conterá uma matriz de imagens. Cada imagem é um tipo complexo que tem os seguintes membros:

| Membro da imagem       | Descrição                             |
|--------------------|-----------------------------------------|
| data               | Cadeia de caracteres codificada em BASE64 da imagem normalizada no formato JPEG.   |
| Largura              | Largura da imagem normalizada em pixels. |
| tamanho             | Altura da imagem normalizada em pixels. |
| originalWidth      | A largura original da imagem antes da normalização. |
| originalHeight      | A altura original da imagem antes da normalização. |
| rotationFromOriginal |  Rotação no sentido anti-horário em graus que ocorreu para criar a imagem normalizada. Um valor entre 0 graus e 360 graus. Esta etapa lê os metadados da imagem que é gerada por uma câmera ou um scanner. Geralmente, um múltiplo de 90 graus. |
| contentOffset | O deslocamento de caractere dentro do campo de conteúdo do qual a imagem foi extraída. Este campo só é aplicável a arquivos com imagens inseridas. |
| pageNumber | Se a imagem tiver sido extraída ou renderizada de um PDF, esse campo conterá o número da página no PDF em que foi extraído ou renderizado, começando em 1.  Se a imagem não fosse de um PDF, esse campo será 0.  |

 Valor de exemplo de *normalized_images*:
```json
[
  {
    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
    "width": 500,
    "height": 300,
    "originalWidth": 5000,  
    "originalHeight": 3000,
    "rotationFromOriginal": 90,
    "contentOffset": 500,
    "pageNumber": 2
  }
]
```

## <a name="image-related-skills"></a>Habilidades relacionadas a imagens

Há duas habilidades cognitivas internas que usam imagens como uma entrada: [OCR](cognitive-search-skill-ocr.md) e [análise de imagem](cognitive-search-skill-image-analysis.md). 

Atualmente, essas habilidades só funcionam com imagens geradas na etapa de quebra de documento. Como tal, a única entrada com suporte `"/document/normalized_images"`é.

### <a name="image-analysis-skill"></a>Habilidade de análise de imagem

A [habilidade de análise de imagem](cognitive-search-skill-image-analysis.md) extrai um conjunto avançado de recursos visuais com base no conteúdo da imagem. Por exemplo, você pode gerar uma legenda de uma imagem, gerar marcas ou identificar celebridades e pontos de referência.

### <a name="ocr-skill"></a>Habilidade de OCR

A [habilidade de OCR](cognitive-search-skill-ocr.md) extrai texto de arquivos de imagem, como JPGs, PNGs e bitmaps. Ele pode extrair texto, bem como informações de layout. As informações de layout fornecem caixas delimitadoras para cada uma das cadeias de caracteres identificadas.

## <a name="embedded-image-scenario"></a>Cenário de imagem inserida

Um cenário comum envolve a criação de uma única cadeia de caracteres contendo todo o conteúdo do arquivo, texto e texto de origem da imagem, executando as seguintes etapas:  

1. [Extrair normalized_images](#get-normalized-images)
1. Executar a habilidade do OCR `"/document/normalized_images"` usando como entrada
1. Mescle a representação de texto dessas imagens com o texto bruto extraído do arquivo. Você pode usar a habilidade de mesclagem de [texto](cognitive-search-skill-textmerger.md) para consolidar as partes de texto em uma única cadeia de caracteres grande.

O seguinte contenção de exemplo cria um campo *merged_text* contendo o conteúdo textual do documento. Ele também inclui o texto OCRed de cada uma das imagens inseridas. 

#### <a name="request-body-syntax"></a>Sintaxe do corpo da solicitação
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

Agora que você tem um campo merged_text, pode mapeá-lo como um campo pesquisável na definição do indexador. Todo o conteúdo de seus arquivos, incluindo o texto das imagens, será pesquisável.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Visualizar caixas delimitadoras de texto extraído

Outro cenário comum é Visualizar as informações de layout dos resultados da pesquisa. Por exemplo, talvez você queira realçar onde um pedaço de texto foi encontrado em uma imagem como parte dos resultados da pesquisa.

Como a etapa de OCR é executada nas imagens normalizadas, as coordenadas de layout estão no espaço de imagem normalizado. Ao exibir a imagem normalizada, a presença de coordenadas geralmente não é um problema, mas em algumas situações talvez você queira exibir a imagem original. Nesse caso, converta cada um dos pontos de coordenadas no layout para o sistema de coordenadas da imagem original. 

Como um auxiliar, se você precisar transformar as coordenadas normalizadas no espaço de coordenadas original, poderá usar o seguinte algoritmo:

```csharp
        /// <summary>
        ///  Converts a point in the normalized coordinate space to the original coordinate space.
        ///  This method assumes the rotation angles are multiples of 90 degrees.
        /// </summary>
        public static Point GetOriginalCoordinates(Point normalized,
                                    int originalWidth,
                                    int originalHeight,
                                    int width,
                                    int height,
                                    double rotationFromOriginal)
        {
            Point original = new Point();
            double angle = rotationFromOriginal % 360;

            if (angle == 0 )
            {
                original.X = normalized.X;
                original.Y = normalized.Y;
            } else if (angle == 90)
            {
                original.X = normalized.Y;
                original.Y = (width - normalized.X);
            } else if (angle == 180)
            {
                original.X = (width -  normalized.X);
                original.Y = (height - normalized.Y);
            } else if (angle == 270)
            {
                original.X = height - normalized.Y;
                original.Y = normalized.X;
            }

            double scalingFactor = (angle % 180 == 0) ? originalHeight / height : originalHeight / width;
            original.X = (int) (original.X * scalingFactor);
            original.Y = (int)(original.Y * scalingFactor);

            return original;
        }
```

## <a name="see-also"></a>Consulte também
+ [Criar indexador (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
+ [Analisar habilidade da imagem](cognitive-search-skill-image-analysis.md)
+ [Habilidade de OCR](cognitive-search-skill-ocr.md)
+ [Habilidade de mesclagem de texto](cognitive-search-skill-textmerger.md)
+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
+ [Como mapear campos aprimorados](cognitive-search-output-field-mapping.md)
