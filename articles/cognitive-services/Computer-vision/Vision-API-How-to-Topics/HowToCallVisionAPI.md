---
title: Chamar a API de Imagem Digitalizada
titleSuffix: Azure Cognitive Services
description: Saiba como chamar o API da Pesquisa Visual Computacional usando a API REST nos serviços cognitivas do Azure.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 298228eedb73298f00654f4f72c201d9ed671090
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177051"
---
# <a name="call-the-computer-vision-api"></a>Chamar a API de Imagem Digitalizada

Este artigo demonstra como chamar o API da Pesquisa Visual Computacional usando a API REST. Os exemplos são gravados no C# usando a biblioteca de cliente API da pesquisa Visual computacional e como chamadas http post ou Get. O artigo se concentra em:

- Obtendo marcas, uma descrição e categorias
- Obter informações específicas do domínio ou "celebridades"

## <a name="prerequisites"></a>Pré-requisitos

- Uma URL de imagem ou um caminho para uma imagem armazenada localmente
- Métodos de entrada com suporte: um binário de imagem bruta na forma de um aplicativo/octeto-Stream ou uma URL de imagem
- Formatos de arquivo de imagem com suporte: JPEG, PNG, GIF e BMP
- Tamanho do arquivo de imagem: 4 MB ou menos
- Dimensões de imagem: 50 &times; 50 pixels ou mais
  
Os exemplos neste artigo demonstram os seguintes recursos:

* Analisando uma imagem para retornar uma matriz de marcas e uma descrição
* Analisar uma imagem com um modelo específico de domínio (especificamente, o modelo "celebridades") para retornar o resultado correspondente em JSON

Os recursos oferecem as seguintes opções:

- **Opção 1**: análise com escopo-analisar apenas um modelo especificado
- **Opção 2**: análise avançada – analisar para fornecer detalhes adicionais usando [a taxonomia de 86 categorias](../Category-Taxonomy.md)
  
## <a name="authorize-the-api-call"></a>Autorizar a chamada à API

Todas as chamadas para a API de Imagem Digitalizada necessitam de uma chave de subscrição. Essa chave deve ser passada por meio de um parâmetro de cadeia de caracteres de consulta ou especificado no cabeçalho da solicitação.

Para obter uma chave de avaliação gratuita, siga um destes procedimentos:
* Vá para a página [experimentar serviços cognitivas](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) . 
* Vá para a página [criar uma conta de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar pesquisa Visual computacional.

Você pode passar a chave de assinatura seguindo qualquer um destes procedimentos:

* Passe por uma cadeia de caracteres de consulta, como neste API da Pesquisa Visual Computacional exemplo:

  ```
  https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
  ```

* Especifique-o no cabeçalho de solicitação HTTP:

  ```
  ocp-apim-subscription-key: <Your subscription key>
  ```

* Quando você usa a biblioteca de cliente, passe a chave por meio do construtor de ComputerVisionClient e especifique a região em uma propriedade do cliente:

    ```
    var visionClient = new ComputerVisionClient(new ApiKeyServiceClientCredentials("Your subscriptionKey"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com"
    }
    ```

## <a name="upload-an-image-to-the-computer-vision-api-service"></a>Carregar uma imagem no serviço de API da Pesquisa Visual Computacional

A maneira básica de executar a chamada de API da Pesquisa Visual Computacional é carregar uma imagem diretamente para marcas de retorno, uma descrição e celebridades. Você faz isso enviando uma solicitação "POST" com a imagem binária no corpo HTTP junto com os dados lidos da imagem. O método upload é o mesmo para todas as chamadas de API da Pesquisa Visual Computacional. A única diferença são os parâmetros de consulta que você especifica. 

Para uma imagem especificada, obtenha marcas e uma descrição usando uma das seguintes opções:

### <a name="option-1-get-a-list-of-tags-and-a-description"></a>Opção 1: obter uma lista de marcas e uma descrição

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using System.IO;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

ImageAnalysis imageAnalysis;
var features = new VisualFeatureTypes[] { VisualFeatureTypes.Tags, VisualFeatureTypes.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageAnalysis = await visionClient.AnalyzeImageInStreamAsync(fs, features);
}
```

### <a name="option-2-get-a-list-of-tags-only-or-a-description-only"></a>Opção 2: obter uma lista apenas de marcas ou apenas uma descrição

Somente para marcas, execute:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/tag?subscription-key=<Your subscription key>
var tagResults = await visionClient.TagImageAsync("http://contoso.com/example.jpg");
```

Somente para uma descrição, execute:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/describe?subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageDescription = await visionClient.DescribeImageInStreamAsync(fs);
}
```

## <a name="get-domain-specific-analysis-celebrities"></a>Obter análise específica de domínio (celebridades)

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Opção 1: análise com escopo-analisar apenas um modelo especificado
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

Para esta opção, são inválidos todos os outros parâmetros de consulta {visualFeatures, details}. Se quiser ver todos os modelos suportados, utilize:

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.1/models 
var models = await visionClient.ListModelsAsync();
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-86-categories-taxonomy"></a>Opção 2: análise avançada – analisar para fornecer detalhes adicionais usando a taxonomia de 86 categorias

Para aplicativos em que você deseja obter uma análise de imagem genérica, além dos detalhes de um ou mais modelos específicos de domínio, estenda a API v1 usando o parâmetro de consulta Models.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?details=celebrities
```

Ao invocar esse método, primeiro você chama o classificador [86-Category](../Category-Taxonomy.md) . Se qualquer uma das categorias corresponder à de um modelo conhecido ou correspondente, ocorrerá uma segunda passagem de invocações de classificador. Por exemplo, se "detalhes = todos" ou "detalhes" incluir "celebridades", você chamará o modelo celebridades depois de chamar o classificador 86-Category. O resultado inclui a categoria Person. Em contraste com a opção 1, esse método aumenta a latência para usuários interessados em celebridades.

Nesse caso, todos os parâmetros de consulta v1 se comportam da mesma maneira. Se você não especificar visualFeatures = Categories, ele será habilitado implicitamente.

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>Recuperar e entender a saída JSON para análise

Segue-se um exemplo:

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

Campo | Tipo | Conteúdo
------|------|------|
Etiquetas  | `object` | O objeto de nível superior para uma matriz de marcas.
tags[].Name | `string`  | A palavra-chave do classificador de marcas.
tags[].Score    | `number`  | A pontuação de confiança, entre 0 e 1.
descrição  | `object` | O objeto de nível superior para uma descrição.
description.tags[] |    `string`    | A lista de marcas.  Se não houver confiança suficiente na capacidade de produzir uma legenda, as marcas poderão ser as únicas informações disponíveis para o chamador.
description.captions[].text | `string`  | Uma frase que descreve a imagem.
description.captions[].confidence   | `number`  | A pontuação de confiança para a frase.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>Recuperar e entender a saída JSON de modelos específicos de domínio

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Opção 1: análise com escopo-analisar apenas um modelo especificado

A saída é uma matriz de marcas, conforme mostrado no exemplo a seguir:

```json
{  
  "result":[  
    {  
      "name":"golden retriever",
      "score":0.98
    },
    {  
      "name":"Labrador retriever",
      "score":0.78
    }
  ]
}
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-the-86-categories-taxonomy"></a>Opção 2: análise avançada – analisar para fornecer detalhes adicionais usando a taxonomia de "categorias de 86"

Para modelos específicos de domínio usando a opção 2 (análise avançada), o tipo de retorno categorias é estendido, conforme mostrado no exemplo a seguir:

```json
{  
  "requestId":"87e44580-925a-49c8-b661-d1c54d1b83b5",
  "metadata":{  
    "width":640,
    "height":430,
    "format":"Jpeg"
  },
  "result":{  
    "celebrities":[  
      {  
        "name":"Richard Nixon",
        "faceRectangle":{  
          "left":107,
          "top":98,
          "width":165,
          "height":165
        },
        "confidence":0.9999827
      }
    ]
  }
}
```

O campo categorias é uma lista de uma ou mais das [categorias 86](../Category-Taxonomy.md) na taxonomia original. As categorias que terminam em um sublinhado correspondem a essa categoria e seus filhos (por exemplo, "People_" ou "people_group", para o modelo celebridades).

Campo   | Tipo  | Conteúdo
------|------|------|
categories | `object`   | O objeto de nível superior.
categories[].name    | `string` | O nome da lista de taxonomia de 86 categorias.
categories[].score  | `number`  | A pontuação de confiança, entre 0 e 1.
categories[].detail  | `object?`      | Adicional O objeto de detalhe.

Se várias categorias corresponderem (por exemplo, o classificador de 86 de categoria retorna uma pontuação para "People_" e "people_young", quando Model = celebridades), os detalhes são anexados à correspondência de nível mais geral ("People_", nesse exemplo).

## <a name="error-responses"></a>Respostas de erro

Esses erros são idênticos aos da visão. analise, com o erro NotSupportedModel adicional (HTTP 400), que pode ser retornado nos cenários da opção 1 e da opção 2. Para a opção 2 (análise avançada), se qualquer um dos modelos especificados nos detalhes não for reconhecido, a API retornará um NotSupportedModel, mesmo se um ou mais deles forem válidos. Para descobrir quais modelos têm suporte, você pode chamar listModels.

## <a name="next-steps"></a>Passos seguintes

Para utilizar a API REST, aceda à [Referência à API de Imagem Digitalizada](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
