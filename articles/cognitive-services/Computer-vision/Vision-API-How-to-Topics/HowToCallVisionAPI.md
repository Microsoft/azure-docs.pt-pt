---
title: Chamar a API de Imagem Digitalizada
titleSuffix: Azure Cognitive Services
description: Aprenda a chamar a API da Visão Computacional utilizando a API REST em Serviços Cognitivos Azure.
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72177051"
---
# <a name="call-the-computer-vision-api"></a>Chamar a API de Imagem Digitalizada

Este artigo demonstra como chamar a API da Visão Computacional utilizando a API REST. As amostras são escritas tanto em C# utilizando a biblioteca de clientes DaPI computer Vision e como http post ou get chamadas. O artigo centra-se em:

- Obtenção de etiquetas, uma descrição e categorias
- Obtenção de informações específicas do domínio, ou "celebridades"

## <a name="prerequisites"></a>Pré-requisitos

- Um URL de imagem ou um caminho para uma imagem armazenada localmente
- Métodos de entrada suportados: um binário de imagem crua sob a forma de uma aplicação/fluxo de octeto, ou um URL de imagem
- Formatos de ficheiros de imagem suportados: JPEG, PNG, GIF e BMP
- Tamanho do ficheiro de imagem: 4 MB ou menos
- Dimensões de imagem: 50 &times; 50 pixels ou mais
  
Os exemplos deste artigo demonstram as seguintes características:

* Analisar uma imagem para devolver uma série de tags e uma descrição
* Analisar uma imagem com um modelo específico de domínio (especificamente, o modelo "celebridades") para devolver o resultado correspondente em JSON

As funcionalidades oferecem as seguintes opções:

- **Opção 1**: Análise scoped - Analisar apenas um modelo especificado
- **Opção 2**: Análise Melhorada - Analisar para fornecer detalhes adicionais utilizando [taxonomia de 86 categorias](../Category-Taxonomy.md)
  
## <a name="authorize-the-api-call"></a>Autorizar a chamada da API

Todas as chamadas para a API de Imagem Digitalizada necessitam de uma chave de subscrição. Esta chave deve ser passada através de um parâmetro de corda de consulta ou especificada no cabeçalho de pedido.

Para obter uma chave de teste gratuita, faça qualquer um dos seguintes:
* Vá à página [do Serviço Cognitivo experimente.](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) 
* Vá à página de conta Criar um Serviço si tome conta de [Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever a Visão Computacional.

Pode passar a chave de subscrição fazendo qualquer uma das seguintes:

* Passá-lo através de uma corda de consulta, como neste exemplo da API da Visão Computacional:

  ```
  https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
  ```

* Especifique-o no cabeçalho de pedido http:

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

## <a name="upload-an-image-to-the-computer-vision-api-service"></a>Faça upload de uma imagem para o serviço API da Visão Computacional

A forma básica de realizar a chamada da Computer Vision API é enviando uma imagem diretamente para etiquetas de devolução, uma descrição e celebridades. Fá-lo enviando um pedido "POST" com a imagem binária no corpo HTTP juntamente com os dados lidos a partir da imagem. O método de upload é o mesmo para todas as chamadas API da Visão Computacional. A única diferença são os parâmetros de consulta que especifica. 

Para uma imagem especificada, obtenha etiquetas e uma descrição utilizando qualquer uma das seguintes opções:

### <a name="option-1-get-a-list-of-tags-and-a-description"></a>Opção 1: Obtenha uma lista de tags e uma descrição

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

### <a name="option-2-get-a-list-of-tags-only-or-a-description-only"></a>Opção 2: Obtenha apenas uma lista de tags ou uma descrição

Apenas para etiquetas, corra:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/tag?subscription-key=<Your subscription key>
var tagResults = await visionClient.TagImageAsync("http://contoso.com/example.jpg");
```

Para uma descrição apenas, corra:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/describe?subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageDescription = await visionClient.DescribeImageInStreamAsync(fs);
}
```

## <a name="get-domain-specific-analysis-celebrities"></a>Obtenha análises específicas do domínio (celebridades)

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Opção 1: Análise de âmbito - Analisar apenas um modelo especificado
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

Para aplicações onde pretende obter uma análise genérica de imagem para além de detalhes de um ou mais modelos específicos de domínio, estenda a API v1 utilizando o parâmetro de consulta dos modelos.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?details=celebrities
```

Quando invoca este método, primeiro liga para o classificador [de 86 categorias.](../Category-Taxonomy.md) Se alguma das categorias corresponder à de um modelo conhecido ou correspondente, ocorre uma segunda passagem de invocações de classificação. Por exemplo, se "details=all" ou "details" inclui "celebridades", você chama o modelo de celebridades depois de chamar o classificador de 86 categorias. O resultado inclui a pessoa da categoria. Ao contrário da Opção 1, este método aumenta a latência para os utilizadores interessados em celebridades.

Neste caso, todos os parâmetros de consulta v1 comportam-se da mesma forma. Se não especificar as categorias visualFeatures=categorias, está implicitamente ativado.

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>Recuperar e compreender a saída jSON para análise

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
Etiquetas  | `object` | O objeto de alto nível para uma variedade de etiquetas.
tags[].Name | `string`  | A palavra-chave do classificador de etiquetas.
tags[].Score    | `number`  | A pontuação de confiança, entre 0 e 1.
descrição  | `object` | O objeto de alto nível para uma descrição.
description.tags[] |    `string`    | A lista de etiquetas.  Se não houver confiança suficiente na capacidade de produzir uma legenda, as etiquetas podem ser a única informação disponível para o chamador.
description.captions[].text | `string`  | Uma frase que descreve a imagem.
description.captions[].confidence   | `number`  | A pontuação de confiança para a frase.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>Recuperar e compreender a saída jSON de modelos específicos de domínio

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Opção 1: Análise de âmbito - Analisar apenas um modelo especificado

A saída é uma variedade de tags, como mostra o seguinte exemplo:

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

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-the-86-categories-taxonomy"></a>Opção 2: Análise melhorada - Analisar para fornecer detalhes adicionais utilizando a taxonomia das "86 categorias"

Para modelos específicos de domínio utilizando a Opção 2 (análise melhorada), o tipo de retorno das categorias é alargado, como se pode ver no seguinte exemplo:

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

O campo das categorias é uma lista de uma ou mais das [86 categorias](../Category-Taxonomy.md) na taxonomia original. As categorias que terminam numa categoria de sublinhado correspondem a essa categoria e aos seus filhos (por exemplo, "people_" ou "people_group", para o modelo de celebridades).

Campo   | Tipo  | Conteúdo
------|------|------|
categories | `object`   | O objeto de alto nível.
categories[].name    | `string` | O nome da lista de taxonomia de 86 categorias.
categories[].score  | `number`  | A pontuação de confiança, entre 0 e 1.
categories[].detail  | `object?`      | (Opcional) O objeto de detalhe.

Se várias categorias corresponderem (por exemplo, o classificador de 86 categorias devolve uma pontuação tanto para "people_" como para "people_young", quando model=celebridades), os detalhes são anexados ao nível mais geral ("people_", nesse exemplo).

## <a name="error-responses"></a>Respostas de erro

Estes erros são idênticos aos da vision.analyze, com o erro adicional do NotSupportedModel (HTTP 400), que poderá ser devolvido tanto nos cenários da Opção 1 como da Opção 2. Para a Opção 2 (análise melhorada), se algum dos modelos especificados nos detalhes não for reconhecido, a API devolve um NotSupportedModel, mesmo que um ou mais deles sejam válidos. Para saber quais os modelos suportados, pode ligar para a listaModels.

## <a name="next-steps"></a>Passos seguintes

Para utilizar a API REST, aceda à [Referência à API de Imagem Digitalizada](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
