---
title: Processar e extrair texto de imagens na pesquisa cognitiva - Azure Search
description: Processar e extrair texto e outras informações a partir de imagens no cognitivos pesquisa pipelines na Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: ca9b3607041f75b1c866aa2813308312ad5d1017
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023754"
---
#  <a name="how-to-process-and-extract-information-from-images-in-cognitive-search-scenarios"></a>Como processar e extrair informações de imagens em cenários de pesquisa cognitiva

Pesquisa cognitiva tem várias capacidades para trabalhar com imagens e arquivos de imagem. Durante a decodificação de documento, pode utilizar o *imageAction* parâmetro para extrair texto de fotos ou imagens que contém o texto de alfanumérico, como a palavra "STOP" num sinal de paragem. Outros cenários incluem a gerar uma representação de texto de uma imagem, por exemplo, "dandelion" para uma fotografia de um dandelion ou a cor "amarela". Também é possível extrair metadados sobre a imagem, como o seu tamanho.

Este artigo aborda em mais detalhes de processamento de imagens e fornece orientações para trabalhar com imagens num pipeline de pesquisa cognitiva.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Obter imagens normalizadas

Como parte de aberturas de documentos, há um novo conjunto de parâmetros de configuração do indexador para lidar com arquivos de imagem ou imagens incorporadas nos arquivos. Esses parâmetros são utilizados para normalizar as imagens para ainda mais processamento a jusante. Normalizar imagens torna mais uniforme. Imagens grandes são redimensionadas para uma altura e largura máximas para torná-los consumível. Para fornecer metadados na orientação de imagens, a rotação de imagem é ajustada para carregamento vertical. Ajustes de metadados são registrados num tipo complexo, criado para cada imagem. 

Não é possível desativar a normalização de imagem. As competências que iterar sobre imagens esperam imagens normalizadas.

| Parâmetro de configuração | Descrição |
|--------------------|-------------|
| imageAction   | Definido como "none" se nenhuma ação deve ser realizada quando são encontrados imagens incorporadas ou arquivos de imagem. <br/>Definido como "generateNormalizedImages" para gerar uma matriz de imagens normalizadas como parte da abertura do documento.<br/>Definido como "generateNormalizedImagePerPage" para gerar uma matriz de imagens normalizadas onde para PDFs na sua origem de dados, cada página é processada para uma imagem de saída.  A funcionalidade é igual a "generateNormalizedImages" para tipos de ficheiro não PDF.<br/>Para qualquer opção que não é "none", as imagens vão ser expostas no *normalized_images* campo. <br/>A predefinição é "none". Esta configuração apenas é pertinente para o blob de origens de dados, quando "dataToExtract" está definida como "contentAndMetadata." <br/>Um máximo de 1000 imagens será extraído de um determinado documento. Se existirem mais de 1000 imagens num documento, a primeira 1000 serão extraídos e será gerado um aviso. |
|  normalizedImageMaxWidth | A largura máxima (em pixéis) de normalizado imagens geradas. A predefinição é 2000.|
|  normalizedImageMaxHeight | A altura máxima (em pixéis) para imagens normalizadas geradas. A predefinição é 2000.|

> [!NOTE]
> Se definir o *imageAction* propriedade com algo diferente de "none", não será capaz de definir o *parsingMode* propriedade com algo diferente de "predefinição".  Apenas pode definir uma destas duas propriedades para um valor não predefinido na configuração do indexador.

Definir o **parsingMode** parâmetro para `json` (para cada blob como um único documento de índice) ou `jsonArray` (se os blobs contêm matrizes JSON, e terá de cada elemento de uma matriz deve ser tratada como um documento separado).

A predefinição de 2000 pixels para a largura máxima de imagens normalizado e a altura baseia-se os tamanhos máximos suportados pelos [habilidade de OCR](cognitive-search-skill-ocr.md) e o [habilidade de análise de imagem](cognitive-search-skill-image-analysis.md). Se aumentar os limites máximos, processamento poderá falhar nas imagens de maior.


Especificar o imageAction no seu [definição de indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer) da seguinte forma:

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

Quando o *imageAction* está definido como um valor de outros, em seguida, "none", a nova *normalized_images* campo irá conter uma matriz de imagens. As imagens são um tipo complexo que tem os seguintes membros:

| Membro de imagem       | Descrição                             |
|--------------------|-----------------------------------------|
| dados               | Cadeia de caracteres da imagem em formato JPEG normalizada de codificado em Base64.   |
| Largura              | Largura da imagem normalizada em pixéis. |
| Altura             | Altura da imagem normalizada em pixéis. |
| originalWidth      | A largura original da imagem antes de normalização. |
| originalHeight      | A altura original da imagem antes de normalização. |
| rotationFromOriginal |  No sentido rotação em graus que ocorreram criar a imagem normalizada. Um valor entre 0 graus e de 360 graus. Este passo lê os metadados a partir da imagem que é gerada por uma câmara ou o scanner. Normalmente, um múltiplo de 90 graus. |
| contentOffset |O deslocamento de carateres dentro do campo conteúdo extraída onde a imagem da. Este campo só é aplicável para os ficheiros com imagens incorporadas. |

 Valor de exemplo *normalized_images*:
```json
[
  {
    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
    "width": 500,
    "height": 300,
    "originalWidth": 5000,  
    "originalHeight": 3000,
    "rotationFromOriginal": 90,
    "contentOffset": 500  
  }
]
```

## <a name="image-related-skills"></a>Habilidades relacionadas à imagem

Existem duas capacidades cognitivas incorporadas que necessitam de imagens como uma entrada: [OCR](cognitive-search-skill-ocr.md) e [análise de imagem](cognitive-search-skill-image-analysis.md). 

Atualmente, essas habilidades só funcionam com imagens geradas a partir do passo de quebra de código do documento. Como tal, é a única entrada suportada `"/document/normalized_images"`.

### <a name="image-analysis-skill"></a>Habilidade de análise de imagem

O [habilidade de análise de imagem](cognitive-search-skill-image-analysis.md) extrai um conjunto avançado de recursos visuais com base no conteúdo de imagem. Por exemplo, pode gerar uma legenda a partir de uma imagem, gerar etiquetas ou identificar celebridades e marcos.

### <a name="ocr-skill"></a>Habilidade de OCR

O [habilidade de OCR](cognitive-search-skill-ocr.md) extrai o texto de arquivos de imagem como JPGs, PNGs e bitmaps. Ele pode extrair texto, bem como informações de layout. As informações de esquema fornecem caixas delimitadora para cada uma das cadeias de caracteres identificadas.

A habilidade de OCR permite-lhe selecionar o algoritmo a utilizar para detectar o texto em suas imagens. Atualmente suporta dois algoritmos, uma para texto impresso e outro para texto manuscrito.

## <a name="embedded-image-scenario"></a>Cenário de imagem incorporada

Um cenário comum envolve a criação de uma única cadeia de caracteres que contém todos os conteúdos do ficheiro, texto e o texto de origem da imagem, efetuando os seguintes passos:  

1. [Extrair normalized_images](#get-normalized-images)
1. Executar a habilidade de OCR utilizando `"/document/normalized_images"` como entrada
1. Intercale a representação de texto dessas imagens com o texto não processado extraído a partir do ficheiro. Pode utilizar o [intercalar texto](cognitive-search-skill-textmerger.md) habilidades a consolidação de ambos os segmentos de texto numa única cadeia de caracteres de grandes dimensões.

O conjunto de capacidades de exemplo seguinte cria um *merged_text* campo que contém o conteúdo textual do seu documento. Ele também inclui o texto de OCRed de cada uma das imagens incorporadas. 

#### <a name="request-body-syntax"></a>Sintaxe de corpo de pedido
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

Agora que tem um campo de merged_text, pode mapeá-lo como um campo pesquisável na sua definição de indexador. Todo o conteúdo dos ficheiros, incluindo o texto das imagens, será pesquisável.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Visualizar delimitação de caixas de texto extraído

Outro cenário comum é visualizar informações de layout de resultados de pesquisa. Por exemplo, talvez queira destacar em que foi encontrado um pedaço de texto numa imagem como parte de seus resultados de pesquisa.

Uma vez que o passo de OCR é realizado nas imagens de normalizado, as coordenadas de layout são o espaço de imagem normalizado. Ao exibir a imagem normalizada, a presença de coordenadas em geral, não é um problema, mas em algumas situações pode querer exibir a imagem original. Converta cada um dos pontos de coordenadas no layout neste caso, o sistema de coordenadas de imagem original. 

Como um auxiliar, se tiver de transformar as coordenadas normalizadas para o espaço coordenado original, poderia usar o seguinte algoritmo:

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
+ [Analisar a habilidade de imagem](cognitive-search-skill-image-analysis.md)
+ [OCR skill](cognitive-search-skill-ocr.md)
+ [Habilidade de intercalação de texto](cognitive-search-skill-textmerger.md)
+ [Como definir um conjunto de capacidades](cognitive-search-defining-skillset.md)
+ [Como mapear campos plena](cognitive-search-output-field-mapping.md)
