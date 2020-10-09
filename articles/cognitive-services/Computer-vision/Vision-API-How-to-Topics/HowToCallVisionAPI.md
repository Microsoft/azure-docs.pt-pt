---
title: Chamar a API de Imagem Digitalizada
titleSuffix: Azure Cognitive Services
description: Saiba como chamar a API de Visão Computacional utilizando a API REST nos Serviços Cognitivos Azure.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 085da17a29e1d5ff1fa69a62e0029fb917d56bb1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88936338"
---
# <a name="call-the-computer-vision-api"></a>Chamar a API de Imagem Digitalizada

Este artigo demonstra como chamar a API de Visão De Computador utilizando a API REST. As amostras são escritas tanto em C# utilizando a biblioteca de clientes da API da Visão de Computador como como chamadas HTTP POST ou GET. O artigo centra-se em:

- Obtenção de etiquetas, descrição e categorias
- Obtenção de informações específicas de domínio, ou "celebridades"

Os exemplos deste artigo demonstram as seguintes características:

* Analisar uma imagem para devolver uma série de tags e uma descrição
* Analisar uma imagem com um modelo específico de domínio (especificamente, o modelo "celebridades") para devolver o resultado correspondente em JSON

As funcionalidades oferecem as seguintes opções:

- **Opção 1**: Análise scoped - Analisar apenas um modelo especificado
- **Opção 2**: Análise melhorada - Analisar para fornecer detalhes adicionais utilizando [taxonomia de 86 categorias](../Category-Taxonomy.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" crie um recurso de Visão de Computador crie um recurso de "  target="_blank"> Visão De Computador no portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação ao serviço de Visão De Computador. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.
* Um URL de imagem ou um caminho para uma imagem armazenada localmente
* Métodos de entrada suportados: um binário de imagem bruta sob a forma de uma aplicação/octeto-stream, ou um URL de imagem
* Formatos de ficheiros de imagem suportados: JPEG, PNG, GIF e BMP
* Tamanho do ficheiro de imagem: 4 MB ou menos
* Dimensões da imagem: 50 &times; 50 pixels ou superiores
  
## <a name="authorize-the-api-call"></a>Autorizar a chamada da API

Todas as chamadas para a API de Imagem Digitalizada necessitam de uma chave de subscrição. Esta chave deve ser passada através de um parâmetro de cadeia de consulta ou especificada no cabeçalho de pedido.

Pode passar a chave de subscrição fazendo qualquer uma das seguintes:

* Passe-o através de uma cadeia de consulta, como neste exemplo da API de Visão De Computador:

  ```
  https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
  ```

* Especifique-o no cabeçalho de pedido HTTP:

  ```
  ocp-apim-subscription-key: <Your subscription key>
  ```

* Quando utilizar a biblioteca do cliente, passe a chave através do construtor do ComputerVisionClient e especifique a região numa propriedade do cliente:

    ```
    var visionClient = new ComputerVisionClient(new ApiKeyServiceClientCredentials("Your subscriptionKey"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com"
    }
    ```

## <a name="upload-an-image-to-the-computer-vision-api-service"></a>Faça upload de uma imagem para o serviço API da Visão De Computador

A forma básica de realizar a chamada da API da Visão de Computador é fazendo o upload de uma imagem diretamente para retornar tags, uma descrição e celebridades. Fá-lo enviando um pedido de "POST" com a imagem binária no organismo HTTP juntamente com os dados lidos a partir da imagem. O método de upload é o mesmo para todas as chamadas API da Visão De Computador. A única diferença são os parâmetros de consulta que especifica. 

Para uma imagem especificada, obtenha etiquetas e uma descrição utilizando qualquer uma das seguintes opções:

### <a name="option-1-get-a-list-of-tags-and-a-description"></a>Opção 1: Obtenha uma lista de etiquetas e uma descrição

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

### <a name="option-2-get-a-list-of-tags-only-or-a-description-only"></a>Opção 2: Obtenha uma lista apenas de etiquetas ou apenas uma descrição

Apenas para etiquetas, corra:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/tag?subscription-key=<Your subscription key>
var tagResults = await visionClient.TagImageAsync("http://contoso.com/example.jpg");
```

Para uma descrição única, corra:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/describe?subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageDescription = await visionClient.DescribeImageInStreamAsync(fs);
}
```

## <a name="get-domain-specific-analysis-celebrities"></a>Obtenha análises específicas do domínio (celebridades)

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Opção 1: Análise scoped - Analisar apenas um modelo especificado
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

Para esta opção, são inválidos todos os outros parâmetros de consulta {visualFeatures, details}. Se quiser ver todos os modelos suportados, utilize:

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.1/models 
var models = await visionClient.ListModelsAsync();
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-86-categories-taxonomy"></a>Opção 2: Análise melhorada - Analisar para fornecer detalhes adicionais utilizando taxonomia de 86 categorias

Para aplicações onde pretende obter uma análise de imagem genérica, além de detalhes de um ou mais modelos específicos de domínio, estenda a API v1 utilizando o parâmetro de consulta dos modelos.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?details=celebrities
```

Quando invoca este método, primeiro ligue para o classificador [de 86 categorias.](../Category-Taxonomy.md) Se alguma das categorias corresponder à de um modelo conhecido ou correspondente, ocorre uma segunda passagem de invocações de classificador. Por exemplo, se "details=all" ou "details" inclui "celebridades", ligue para o modelo de celebridades depois de chamar o classificador de 86 categorias. O resultado inclui a pessoa da categoria. Em contraste com a Opção 1, este método aumenta a latência para utilizadores interessados em celebridades.

Neste caso, todos os parâmetros de consulta v1 comportam-se da mesma forma. Se não especificar visualFeatures=categorias, está implicitamente ativado.

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>Recupere e compreenda a saída JSON para análise

Eis um exemplo:

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
Etiquetas  | `object` | O objeto de nível superior para uma variedade de tags.
tags[].Name | `string`    | A palavra-chave do classificador de etiquetas.
tags[].Score    | `number`    | A pontuação de confiança, entre 0 e 1.
descrição     | `object`    | O objeto de nível superior para uma descrição.
description.tags[] |    `string`    | A lista de etiquetas.  Se não houver confiança suficiente na capacidade de produzir uma legenda, as etiquetas podem ser a única informação disponível para o chamador.
description.captions[].text    | `string`    | Uma frase que descreve a imagem.
description.captions[].confidence    | `number`    | A pontuação de confiança para a frase.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>Recupere e compreenda a saída JSON de modelos específicos de domínio

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Opção 1: Análise scoped - Analisar apenas um modelo especificado

A saída é um conjunto de tags, como mostra o seguinte exemplo:

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

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-the-86-categories-taxonomy"></a>Opção 2: Análise melhorada - Analisar para fornecer detalhes adicionais utilizando a taxonomia "86 categorias"

Para modelos específicos de domínio que utilizem a Opção 2 (análise melhorada), o tipo de retorno das categorias é alargado, como mostra o exemplo seguinte:

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

O campo de categorias é uma lista de uma ou mais das [86 categorias](../Category-Taxonomy.md) da taxonomia original. Categorias que terminam numa categoria de sublinhado correspondem a essa categoria e aos seus filhos (por exemplo, "people_" ou "people_group", para o modelo de celebridades).

Campo    | Tipo    | Conteúdo
------|------|------|
categories | `object`    | O objeto de alto nível.
categories[].name     | `string`    | O nome da lista de taxonomia de 86 categorias.
categories[].score    | `number`    | A pontuação de confiança, entre 0 e 1.
categories[].detail     | `object?`      | (Opcional) O objeto de detalhe.

Se várias categorias corresponderem (por exemplo, o classificador de 86 categorias devolve uma pontuação tanto para "people_" como para "people_young", quando model=celebridades), os detalhes são anexados ao jogo de nível mais geral ("people_", nesse exemplo).

## <a name="error-responses"></a>Respostas de erro

Estes erros são idênticos aos do vision.analyze, com o erro adicional do NãoSupportmodel (HTTP 400), que pode ser devolvido tanto nos cenários Opção 1 como na Opção 2. Para a Opção 2 (análise melhorada), se algum dos modelos especificados nos detalhes não for reconhecido, a API devolve um Modelo Não Suportado, mesmo que um ou mais deles sejam válidos. Para saber quais os modelos suportados, pode ligar para listModels.

## <a name="next-steps"></a>Passos seguintes

Para utilizar a API REST, aceda à [Referência à API de Imagem Digitalizada](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f21b).
