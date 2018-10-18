---
title: 'Exemplo: chamar a API de Imagem Digitalizada'
titlesuffix: Azure Cognitive Services
description: Saiba como chamar a API de Imagem Digitalizada ao utilizar o REST nos Serviços Cognitivos do Azure.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: sample
ms.date: 01/20/2017
ms.author: kefre
ms.openlocfilehash: e8297fbe59ebe2dea9caf112ebea4517447cf9e0
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2018
ms.locfileid: "45981750"
---
# <a name="example-how-to-call-the-computer-vision-api"></a>Exemplo: como chamar a API de Imagem Digitalizada

Este guia mostra como chamar a API de Imagem Digitalizada com o REST. Os exemplos são escritos em C# ao utilizar a biblioteca de cliente da API de Imagem Digitalizada e como chamadas HTTP POST/GET. Iremos centrar-nos no seguinte:

-   Como obter as "Etiquetas", a "Descrição" e as "Categorias".
-   Como obter informações "Específicas de domínio" (celebridades).

### <a name="Prerequisites">Pré-requisitos</a> 
URL ou caminho da imagem armazenada localmente.
  * Métodos de entrada suportados: imagem binária não processada com o formato application/octet-stream ou URL da imagem
  * Formatos de imagem suportados: JPEG, PNG, GIF, BMP
  * Tamanho do ficheiro de imagem: inferior a 4 MB
  * Dimensão da imagem: superior a 50x50 pixéis
  
Nos exemplos abaixo, são apresentadas as seguintes funcionalidades:

1. Analisar uma imagem e obter a devolução de uma matriz de etiquetas e de uma descrição.
2. Analisar uma imagem com um modelo específico de domínio (especificamente, o modelo de "celebridades") e obter a devolução do resultado correspondente em JSON.

As funcionalidades são divididas da seguinte forma:

  * **Opção Um:** Análise com Âmbito – analisar apenas um determinado modelo
  * **Opção Dois:** Análise Avançada – analisar para fornecer detalhes adicionais com a [taxonomia de 86 categorias](../Category-Taxonomy.md)
  
### <a name="Step1">Passo 1: autorizar a chamada da API</a> 
Todas as chamadas para a API de Imagem Digitalizada necessitam de uma chave de subscrição. Esta chave tem de ser transmitida através de um parâmetro de cadeia de consulta ou pode ser especificada no cabeçalho do pedido. 

Para obter uma chave de subscrição, veja [Como Obter Chaves de Subscrição](../Vision-API-How-to-Topics/HowToSubscribe.md
).

**1.** Transmitir a chave de subscrição através de uma cadeia de consulta (veja abaixo com um exemplo da API de Imagem Digitalizada):

```https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>```

**2.** A transmissão da chave de subscrição também pode ser especificada no cabeçalho do pedido HTTP:

```ocp-apim-subscription-key: <Your subscription key>```

**3.** Ao utilizar a biblioteca de cliente, a chave de subscrição é transmitida através do construtor do VisionServiceClient:

```var visionClient = new VisionServiceClient(“Your subscriptionKey”);```

### <a name="Step2">Passo 2: carregar uma imagem para o serviço da API de Imagem Digitalizada e obter a devolução de etiquetas, descrições e celebridades</a>
A forma básica de efetuar a chamada da API de Imagem Digitalizada é carregar uma imagem diretamente. Isto é feito ao enviar um pedido "POST" com o tipo de conteúdo application/octet-stream, juntamente com os dados lidos na imagem. Para as "Etiquetas" e a "Descrição", este método de carregamento será idêntico ao de todas as chamadas da API de Imagem Digitalizada. A única diferença serão os parâmetros de consulta especificados pelo utilizador. 

Eis como obter as "Etiquetas" e a "Descrição" de uma determinada imagem:

**Opção Um:** obter a lista de "Etiquetas" e uma "Descrição"
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```
```
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

###### <a name="tags-only"></a>Apenas etiquetas:
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/tag&subscription-key=<Your subscription key>
var analysisResult = await visionClient.GetTagsAsync("http://contoso.com/example.jpg");
```

###### <a name="description-only"></a>Apenas descrições:
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/describe&subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.DescribeAsync(fs);
}
```
### <a name="here-is-how-to-get-domain-specific-analysis-in-our-case-for-celebrities"></a>Eis como obter a análise específica de um domínio (neste caso, para celebridades).

**Opção Um:** Análise com Âmbito – analisar apenas um determinado modelo
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```
Para esta opção, são inválidos todos os outros parâmetros de consulta {visualFeatures, details}. Se quiser ver todos os modelos suportados, utilize: 
```
GET https://westus.api.cognitive.microsoft.com/vision/v2.0/models 
var models = await visionClient.ListModelsAsync();
```
**Opção Dois:** Análise Avançada – analisar para fornecer detalhes adicionais com a [taxonomia de 86 categorias](../Category-Taxonomy.md)

Para aplicações em que se pretende obter uma análise de imagem genérica, além dos detalhes de um ou mais modelos específicos de domínio, podemos alargar a API de v1 com o parâmetro de consulta de modelos.
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?details=celebrities
```
Ao invocar este método, iremos chamar primeiro o classificador da taxonomia de 86 categorias. Se alguma das categorias corresponder à de um modelo de correspondência/conhecido, ocorrerá uma segunda transmissão das invocações do classificador. Por exemplo, se "details=all" ou "details" incluir "celebrities", iremos chamar o modelo de celebridades depois de o classificador da taxonomia de 86 categorias ser chamado e o resultado inclui a pessoa da categoria. Esta opção irá aumentar a latência para utilizadores interessados em celebridades, em comparação com a Opção Um.

Todos os parâmetros da consulta de v1 irão comportar-se da mesma forma neste caso.  Se visualFeatures=categories não for especificado, será implicitamente ativado.

### <a name="Step3">Passo 3: obter e compreender o resultado JSON para analyze&visualFeatures=Tags, Descrição</a>

Segue-se um exemplo:
```
  {
    “tags”: [
    {
    "name": "outdoor",
        "score": 0.976
    },
    {
    "name": "bird",
        "score": 0.95
    }
            ],
    “description”: 
    {
    "tags": [
    "outdoor",
    "bird"
            ],
    "captions": [
    {
    "text”: “partridge in a pear tree”,
            “confidence”: 0.96
    }
                ]
    }
  }
```
Campo   | Tipo  | Conteúdo
------|------|------|
Etiquetas    | objeto    | Objeto de nível superior para a matriz de etiquetas.
tags[].Name | string    | Palavra-chave do classificador de etiquetas.
tags[].Score    | número    | Pontuação de confiança, entre 0 e 1.
descrição  | objeto   | Objeto de nível superior para uma descrição.
description.tags[] |    string  | Lista de etiquetas.  Se não existir confiança suficiente na capacidade de produzir uma legenda, as etiquetas poderão ser as únicas informações disponíveis para o chamador.
description.captions[].text | string    | Uma frase que descreve a imagem.
description.captions[].confidence   | número    | O nível de confiança da frase.

### <a name="Step4">Passo 4: obter e compreender o resultado JSON de modelos específicos de domínio</a>

**Opção Um:** Análise com Âmbito – analisar apenas um determinado modelo

O resultado será uma matriz de etiquetas como a do seguinte exemplo:
```
  { 
    "result": [ 
      { 
    "name": "golden retriever", 
    "score": 0.98
    },
    { 
    "name": "Labrador retriever", 
    "score": 0.78
    }
              ]
  }
```

**Opção Dois:** Análise Avançada – analisar para fornecer detalhes adicionais com a taxonomia de 86 categorias

Para modelos específicos de domínio ao utilizar a Opção Dois (Análise Avançada), o tipo de devolução de categorias é expandido. Eis um exemplo:
```
  {
    "requestId": "87e44580-925a-49c8-b661-d1c54d1b83b5",
    "metadata":     {
      "width": 640,
      "height": 430,
      "format": "Jpeg"
                    },
    "result": {
      "celebrities": 
      [
        {
        "name": "Richard Nixon",
        "faceRectangle": {
          "left": 107,
          "top": 98,
          "width": 165,
          "height": 165
                         },
        "confidence": 0.9999827
        }
      ]
  }
```

O campo de categorias é uma lista de uma ou mais das [86 categorias](../Category-Taxonomy.md) na taxonomia original. Tenha também em atenção que as categorias que terminam com um caráter de sublinhado irão corresponder a essa categoria e às respetivas subordinadas (por exemplo, people_ e people_group para o modelo de celebridades).

Campo   | Tipo  | Conteúdo
------|------|------|
categories | objeto | Objeto de nível superior.
categories[].name    | string   | Nome da taxonomia de 86 categorias.
categories[].score  | número    | Pontuação de confiança, entre 0 e 1.
categories[].detail  | objeto?      | Objeto de detalhe opcional.

Tenha em atenção que, se houver correspondência de múltiplas categorias (por exemplo, o classificador da taxonomia de 86 categorias devolve uma pontuação para people_ e people_young quando model=celebrities), os detalhes serão associados à correspondência de nível mais geral (people_ neste exemplo.)

### <a name="Errors">Respostas de Erros</a>
Estas respostas são idênticas ao vision.analyze, com o erro adicional NotSupportedModel (HTTP 400), que pode ser devolvido nos cenários da Opção Um e da Opção Dois. Para a Opção Dois (Análise Avançada), se qualquer um dos modelos especificados nos detalhes não for reconhecido, a API irá devolver um erro NotSupportedModel, mesmo que um ou mais dos modelos sejam válidos.  Os utilizadores podem chamar a função listModels para saber que modelos são suportados.

### <a name="Summary">Resumo</a>

As funcionalidades básicas da API de Imagem Digitalizada são o carregamento de imagens e a obtenção de metadados importantes.

Para utilizar a API REST, aceda à [Referência à API de Imagem Digitalizada](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
 
