---
title: 'Exemplo: Chamar a API de análise de imagem-Pesquisa Visual Computacional'
titleSuffix: Azure Cognitive Services
description: Saiba como chamar a API de Imagem Digitalizada ao utilizar o REST nos Serviços Cognitivos do Azure.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 03/21/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 35e6ad922ab54748165fcf8e273d93ee44bc42cc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564535"
---
# <a name="example-how-to-call-the-computer-vision-api"></a>Exemplo: Como chamar o API da Pesquisa Visual Computacional

Este guia mostra como chamar a API de Imagem Digitalizada com o REST. Os exemplos são escritos em C# ao utilizar a biblioteca de cliente da API de Imagem Digitalizada e como chamadas HTTP POST/GET. Iremos centrar-nos no seguinte:

- Como obter as "Etiquetas", a "Descrição" e as "Categorias".
- Como obter informações "Específicas de domínio" (celebridades).

## <a name="prerequisites"></a>Pré-requisitos

- URL ou caminho da imagem armazenada localmente.
- Métodos de entrada com suporte: Imagem bruta binária na forma de um fluxo de aplicativo/octeto ou URL de imagem
- Formatos de imagem com suporte: JPEG, PNG, GIF, BMP
- Tamanho do arquivo de imagem: Menos de 4 MB
- Dimensão da imagem: Maior que 50 x 50 pixels
  
Nos exemplos abaixo, são apresentadas as seguintes funcionalidades:

1. Analisar uma imagem e obter a devolução de uma matriz de etiquetas e de uma descrição.
2. Analisar uma imagem com um modelo específico de domínio (especificamente, o modelo de "celebridades") e obter a devolução do resultado correspondente em JSON.

As funcionalidades são divididas da seguinte forma:

- **Opção um:** Análise com escopo definido-analisar apenas um determinado modelo
- **Opção dois:** Análise avançada – analisar para fornecer detalhes adicionais com a [taxonomia de 86 categorias](../Category-Taxonomy.md)
  
## <a name="authorize-the-api-call"></a>Autorizar a chamada à API

Todas as chamadas para a API de Imagem Digitalizada necessitam de uma chave de subscrição. Esta chave tem de ser transmitida através de um parâmetro de cadeia de consulta ou pode ser especificada no cabeçalho do pedido.

Para obter uma chave de avaliação gratuita, consulte [experimentar serviços cognitivas](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Ou siga as instruções em [criar uma conta de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar pesquisa Visual computacional e obter sua chave.

1. Transmitir a chave de subscrição através de uma cadeia de consulta (veja abaixo com um exemplo da API de Imagem Digitalizada):

```https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>```

1. A transmissão da chave de subscrição também pode ser especificada no cabeçalho do pedido HTTP:

```ocp-apim-subscription-key: <Your subscription key>```

1. Ao utilizar a biblioteca de cliente, a chave de subscrição é transmitida através do construtor do VisionServiceClient:

```var visionClient = new VisionServiceClient("Your subscriptionKey");```

## <a name="upload-an-image-to-the-computer-vision-api-service-and-get-back-tags-descriptions-and-celebrities"></a>Carregar uma imagem no serviço de API da Pesquisa Visual Computacional e obter marcas, descrições e celebridades

A forma básica de efetuar a chamada da API de Imagem Digitalizada é carregar uma imagem diretamente. Isto é feito ao enviar um pedido "POST" com o tipo de conteúdo application/octet-stream, juntamente com os dados lidos na imagem. Para as "Etiquetas" e a "Descrição", este método de carregamento será idêntico ao de todas as chamadas da API de Imagem Digitalizada. A única diferença serão os parâmetros de consulta especificados pelo utilizador. 

Eis como obter as "Etiquetas" e a "Descrição" de uma determinada imagem:

**Opção um:** Obter lista de "marcas" e uma "Descrição"

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using Microsoft.ProjectOxford.Vision;
using Microsoft.ProjectOxford.Vision.Contract;
using System.IO;

AnalysisResult analysisResult;
var features = new VisualFeature[] { VisualFeature.Tags, VisualFeature.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.AnalyzeImageAsync(fs, features);
}
```

**Opção Dois:** obter apenas a lista de "Etiquetas" ou apenas a lista de "Descrições":

###### <a name="tags-only"></a>Somente marcas:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/tag&subscription-key=<Your subscription key>
var analysisResult = await visionClient.GetTagsAsync("http://contoso.com/example.jpg");
```

###### <a name="description-only"></a>Somente Descrição:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/describe&subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.DescribeAsync(fs);
}
```

### <a name="get-domain-specific-analysis-celebrities"></a>Obter análise específica de domínio (celebridades)

**Opção um:** Análise com escopo definido-analisar apenas um determinado modelo
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

Para esta opção, são inválidos todos os outros parâmetros de consulta {visualFeatures, details}. Se quiser ver todos os modelos suportados, utilize:

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.0/models 
var models = await visionClient.ListModelsAsync();
```

**Opção dois:** Análise avançada – analisar para fornecer detalhes adicionais com a [taxonomia de 86 categorias](../Category-Taxonomy.md)

Para aplicações em que se pretende obter uma análise de imagem genérica, além dos detalhes de um ou mais modelos específicos de domínio, podemos alargar a API de v1 com o parâmetro de consulta de modelos.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?details=celebrities
```

Ao invocar este método, iremos chamar primeiro o classificador da taxonomia de 86 categorias. Se alguma das categorias corresponder à de um modelo de correspondência/conhecido, ocorrerá uma segunda transmissão das invocações do classificador. Por exemplo, se "details=all" ou "details" incluir "celebrities", iremos chamar o modelo de celebridades depois de o classificador da taxonomia de 86 categorias ser chamado e o resultado inclui a pessoa da categoria. Esta opção irá aumentar a latência para utilizadores interessados em celebridades, em comparação com a Opção Um.

Todos os parâmetros da consulta de v1 irão comportar-se da mesma forma neste caso.  Se visualFeatures=categories não for especificado, será implicitamente ativado.

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

Campo | Type | Conteúdo
------|------|------|
Tags  | `object` | Objeto de nível superior para a matriz de etiquetas.
tags[].Name | `string`  | Palavra-chave do classificador de etiquetas.
tags[].Score    | `number`  | Pontuação de confiança, entre 0 e 1.
description  | `object` | Objeto de nível superior para uma descrição.
description.tags[] |    `string`    | Lista de etiquetas.  Se não existir confiança suficiente na capacidade de produzir uma legenda, as etiquetas poderão ser as únicas informações disponíveis para o chamador.
description.captions[].text | `string`  | Uma frase que descreve a imagem.
description.captions[].confidence   | `number`  | O nível de confiança da frase.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>Recuperar e entender a saída JSON de modelos específicos de domínio

**Opção um:** Análise com escopo definido-analisar apenas um determinado modelo

O resultado será uma matriz de etiquetas como a do seguinte exemplo:

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

**Opção dois:** Análise avançada – analisar para fornecer detalhes adicionais com a taxonomia de 86 categorias

Para modelos específicos de domínio ao utilizar a Opção Dois (Análise Avançada), o tipo de devolução de categorias é expandido. Eis um exemplo:

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

O campo de categorias é uma lista de uma ou mais das [86 categorias](../Category-Taxonomy.md) na taxonomia original. Tenha também em atenção que as categorias que terminam com um caráter de sublinhado irão corresponder a essa categoria e às respetivas subordinadas (por exemplo, people_ e people_group para o modelo de celebridades).

Campo   | Type  | Conteúdo
------|------|------|
categorias | `object`   | Objeto de nível superior.
categories[].name    | `string` | Nome da taxonomia de 86 categorias.
categories[].score  | `number`  | Pontuação de confiança, entre 0 e 1.
categories[].detail  | `object?`      | Objeto de detalhe opcional.

Tenha em atenção que, se houver correspondência de múltiplas categorias (por exemplo, o classificador da taxonomia de 86 categorias devolve uma pontuação para people_ e people_young quando model=celebrities), os detalhes serão associados à correspondência de nível mais geral (people_ neste exemplo.)

## <a name="errors-responses"></a>Respostas de erros

Estas respostas são idênticas ao vision.analyze, com o erro adicional NotSupportedModel (HTTP 400), que pode ser devolvido nos cenários da Opção Um e da Opção Dois. Para a Opção Dois (Análise Avançada), se qualquer um dos modelos especificados nos detalhes não for reconhecido, a API irá devolver um erro NotSupportedModel, mesmo que um ou mais dos modelos sejam válidos.  Os utilizadores podem chamar a função listModels para saber que modelos são suportados.

## <a name="next-steps"></a>Passos Seguintes

Para utilizar a API REST, aceda à [Referência à API de Imagem Digitalizada](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
