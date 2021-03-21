---
title: Extrair texto de imagens
titleSuffix: Azure Cognitive Search
description: Processar e extrair texto e outras informações a partir de imagens nos oleodutos de Pesquisa Cognitiva Azure.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 2e77bbd6e82d0d4a48b72e13e60b60608f2d7674
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419596"
---
# <a name="how-to-process-and-extract-information-from-images-in-ai-enrichment-scenarios"></a>Como processar e extrair informação de imagens em cenários de enriquecimento de IA

A Azure Cognitive Search tem várias capacidades para trabalhar com imagens e ficheiros de imagem. Durante a fissura do documento, pode utilizar o parâmetro *imageAction* para extrair texto de fotos ou imagens que contenham texto alfanumérico, como a palavra "STOP" num sinal de stop. Outros cenários incluem gerar uma representação de texto de uma imagem, como "dente-de-leão" para uma foto de um dente-de-leão, ou a cor "amarelo". Também pode extrair metadados sobre a imagem, como o seu tamanho.

Este artigo cobre o processamento de imagem com mais detalhes e fornece orientações para trabalhar com imagens num pipeline de enriquecimento de IA.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>Obtenha imagens normalizadas

Como parte da quebra de documentos, existem um novo conjunto de parâmetros de configuração do indexante para o manuseamento de ficheiros de imagem ou imagens incorporadas em ficheiros. Estes parâmetros são usados para normalizar as imagens para um processamento mais a jusante. Normalizar as imagens torna-as mais uniformes. As grandes imagens são redimensionadas para uma altura e largura máximas para torná-las consumíveis. Para imagens que fornecem metadados na orientação, a rotação da imagem é ajustada para carregamento vertical. Os ajustes de metadados são capturados num tipo complexo criado para cada imagem. 

Não é possível desligar a normalização da imagem. As habilidades que iteram sobre as imagens esperam imagens normalizadas. Permitir a normalização da imagem num indexante requer que seja anexada uma habilidade a esse indexante.

| Parâmetro de configuração | Description |
|--------------------|-------------|
| imageAction   | Definido como "nenhum" se não for necessário tomar medidas quando forem encontradas imagens incorporadas ou ficheiros de imagem. <br/>Configurar para "gerar ImagensNormalizadas" para gerar uma série de imagens normalizadas como parte da quebra de documentos.<br/>Definido para "generateNormalizedImagePerPage" para gerar um conjunto de imagens normalizadas onde, para PDFs na sua fonte de dados, cada página é renderizada a uma imagem de saída.  A funcionalidade é a mesma que "generateNormalizedImages" para tipos de ficheiros não PDF.<br/>Para qualquer opção que não seja "nenhuma", as imagens serão expostas no campo *normalized_images.* <br/>O padrão é "nenhum". Esta configuração é apenas pertinente para obter fontes de dados blob, quando "dataToExtract" é definido como "contentAndMetadata". <br/>Um máximo de 1000 imagens será extraído de um determinado documento. Se houver mais de 1000 imagens num documento, as primeiras 1000 serão extraídas e será gerado um aviso. |
|  ImageMaxWidth normalizado | A largura máxima (em pixels) para imagens normalizadas geradas. A predefinição é 2 000. O valor máximo permitido é de 10.000. | 
|  ImageMaxHeight normalizado | A altura máxima (em pixels) para imagens normalizadas geradas. A predefinição é 2 000. O valor máximo permitido é de 10.000.|

> [!NOTE]
> Se definir a propriedade *imageAction* para outra coisa que não "nenhuma", não será capaz de definir a propriedade *parsingMode* para outra coisa que não "padrão".  Só pode definir uma destas duas propriedades para um valor não padrão na configuração do indexante.

Descreva o parâmetro **parsingMode** para `json` (para indexar cada bolha como um único documento) ou `jsonArray` (se as suas bolhas contiverem matrizes JSON e precisar de cada elemento de uma matriz para ser tratado como um documento separado).

O padrão de 2000 pixels para as imagens normalizadas a largura e altura máxima baseia-se nos tamanhos máximos suportados pela [habilidade de OCR](cognitive-search-skill-ocr.md) e pela habilidade de análise de [imagem](cognitive-search-skill-image-analysis.md). A [habilidade OCR](cognitive-search-skill-ocr.md) suporta uma largura e altura máximas de 4200 para línguas não inglesas, e 10000 para inglês.  Se aumentar os limites máximos, o processamento pode falhar em imagens maiores, dependendo da definição de skillset e da linguagem dos documentos. 

Especifica a imagemAcção na [definição do indexante](/rest/api/searchservice/create-indexer) da seguinte forma:

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

Quando a *imagemAcção* é definida como um valor diferente então "nenhuma", o novo campo *de normalized_images* conterá uma variedade de imagens. Cada imagem é um tipo complexo que tem os seguintes membros:

| Membro da imagem       | Description                             |
|--------------------|-----------------------------------------|
| dados               | BASE64 codificada cadeia da imagem normalizada em formato JPEG.   |
| largura              | Largura da imagem normalizada em pixels. |
| altura             | Altura da imagem normalizada em pixéis. |
| Largura original      | A largura original da imagem antes da normalização. |
| OriginalHeight      | A altura original da imagem antes da normalização. |
| rotaçãoFromOriginal |  Rotação no sentido contrário ao dos ponteiros do relógio em graus que ocorreram para criar a imagem normalizada. Um valor entre 0 e 360 graus. Este passo lê os metadados a partir da imagem que é gerada por uma câmara ou scanner. Normalmente um múltiplo de 90 graus. |
| conjunto de conteúdos | O personagem compensa dentro do campo de conteúdo de onde a imagem foi extraída. Este campo só é aplicável para ficheiros com imagens incorporadas. |
| pageNumber | Se a imagem foi extraída ou renderizada a partir de um PDF, este campo contém o número de página no PDF de que foi extraída ou renderizada, a partir de 1.  Se a imagem não fosse de um PDF, este campo será 0.  |

 Valor da amostra de *normalized_images:*
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

## <a name="image-related-skills"></a>Competências relacionadas com a imagem

Existem duas habilidades cognitivas incorporadas que levam as imagens como uma entrada: [OCR](cognitive-search-skill-ocr.md) e [Análise de Imagem.](cognitive-search-skill-image-analysis.md) 

Atualmente, estas competências apenas funcionam com imagens geradas a partir do passo de rachadura do documento. Como tal, a única entrada suportada é `"/document/normalized_images"` .

### <a name="image-analysis-skill"></a>Habilidade de análise de imagem

A [habilidade de Análise de Imagem](cognitive-search-skill-image-analysis.md) extrai um rico conjunto de funcionalidades visuais baseadas no conteúdo da imagem. Por exemplo, pode gerar uma legenda a partir de uma imagem, gerar tags ou identificar celebridades e marcos.

### <a name="ocr-skill"></a>Habilidade de OCR

A [habilidade OCR](cognitive-search-skill-ocr.md) extrai texto de ficheiros de imagem como JPGs, PNGs e bitmaps. Pode extrair texto, bem como informação de layout. A informação do layout fornece caixas de delimitação para cada uma das cordas identificadas.

## <a name="embedded-image-scenario"></a>Cenário de imagem incorporado

Um cenário comum consiste em criar uma única cadeia contendo todos os conteúdos de ficheiros, tanto de texto como de texto de origem de imagem, realizando os seguintes passos:  

1. [Extrato normalized_images](#get-normalized-images)
1. Executar a habilidade OCR usando `"/document/normalized_images"` como entrada
1. Misture a representação por texto dessas imagens com o texto cru extraído do ficheiro. Pode utilizar a habilidade [de fusão de texto](cognitive-search-skill-textmerger.md) para consolidar ambos os pedaços de texto numa única corda grande.

O exemplo seguinte skillset cria um campo *merged_text* contendo o conteúdo textual do seu documento. Também inclui o texto OCRed de cada uma das imagens incorporadas. 

#### <a name="request-body-syntax"></a>Solicitar sintaxe corporal
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

Agora que tem um campo merged_text, pode mapeá-lo como um campo pestilizável na sua definição de indexante. Todo o conteúdo dos seus ficheiros, incluindo o texto das imagens, será pesjável.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>Visualizar caixas de delimitação de texto extraído

Outro cenário comum é visualizar a informação do layout dos resultados de pesquisa. Por exemplo, é melhor destacar onde um pedaço de texto foi encontrado numa imagem como parte dos resultados da sua pesquisa.

Uma vez que o passo OCR é realizado nas imagens normalizadas, as coordenadas de layout estão no espaço de imagem normalizado. Ao exibir a imagem normalizada, a presença de coordenadas geralmente não é um problema, mas em algumas situações pode querer exibir a imagem original. Neste caso, converta cada um dos pontos de coordenadas no layout para o sistema de coordenadas de imagem original. 

Como ajudante, se precisar de transformar coordenadas normalizadas para o espaço de coordenadas original, pode utilizar o seguinte algoritmo:

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
## <a name="passing-images-to-custom-skills"></a>Passando imagens para habilidades personalizadas

Para cenários em que você precisa de uma habilidade personalizada para trabalhar em imagens, você pode passar imagens para a habilidade personalizada, e fazê-lo devolver texto ou imagens. O processamento de imagem da [amostra Python](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Image-Processing) demonstra o fluxo de trabalho. O seguinte skillset é da amostra.

O seguinte skillset tira a imagem normalizada (obtida durante a rachadura do documento) e produz fatias da imagem.

#### <a name="sample-skillset"></a>Skillset de amostra
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
          "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
          "name": "ImageSkill",
          "description": "Segment Images",
          "context": "/document/normalized_images/*",
          "uri": "https://your.custom.skill.url",
          "httpMethod": "POST",
          "timeout": "PT30S",
          "batchSize": 100,
          "degreeOfParallelism": 1,
          "inputs": [
            {
              "name": "image",
              "source": "/document/normalized_images/*"
            }
          ],
          "outputs": [
            {
              "name": "slices",
              "targetName": "slices"
            }
          ],
          "httpHeaders": {}
        }
  ]
}
```

#### <a name="custom-skill"></a>Habilidade personalizada

A habilidade personalizada em si é externa ao skillset. Neste caso, é o código Python que primeiro dá a volta ao lote de registos de pedidos no formato de habilidade personalizada, depois converte a cadeia codificada base64 para uma imagem.

```python
# deserialize the request, for each item in the batch
for value in values:
  data = value['data']
  base64String = data["image"]["data"]
  base64Bytes = base64String.encode('utf-8')
  inputBytes = base64.b64decode(base64Bytes)
  # Use numpy to convert the string to an image
  jpg_as_np = np.frombuffer(inputBytes, dtype=np.uint8)
  # you now have an image to work with
```
Da mesma forma para devolver uma imagem, devolva uma corda codificada base64 dentro de um objeto JSON com uma `$type` propriedade de `file` .

```python
def base64EncodeImage(image):
    is_success, im_buf_arr = cv2.imencode(".jpg", image)
    byte_im = im_buf_arr.tobytes()
    base64Bytes = base64.b64encode(byte_im)
    base64String = base64Bytes.decode('utf-8')
    return base64String

 base64String = base64EncodeImage(jpg_as_np)
 result = { 
  "$type": "file", 
  "data": base64String 
}
```

## <a name="see-also"></a>Ver também
+ [Criar indexante (REST)](/rest/api/searchservice/create-indexer)
+ [Habilidade de análise de imagem](cognitive-search-skill-image-analysis.md)
+ [Habilidade de OCR](cognitive-search-skill-ocr.md)
+ [Habilidade de fusão de texto](cognitive-search-skill-textmerger.md)
+ [Como definir um skillset](cognitive-search-defining-skillset.md)
+ [Como mapear campos enriquecidos](cognitive-search-output-field-mapping.md)
+ [Como passar imagens para habilidades personalizadas](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Image-Processing)
