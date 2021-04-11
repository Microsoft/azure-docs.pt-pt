---
title: Ligue para a API de Análise de Imagem
titleSuffix: Azure Cognitive Services
description: Saiba como chamar a API de Análise de Imagem e configuure o seu comportamento.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 3f9a6afe3202df40e26332c3a8c91b8c3eca8a32
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012273"
---
# <a name="call-the-image-analysis-api"></a>Ligue para a API de Análise de Imagem

Este artigo demonstra como ligar para a API de Análise de Imagem para devolver informações sobre as características visuais de uma imagem.

Este guia pressupõe que já <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" criou um recurso de Visão De Computador para criar um recurso de "  target="_blank"> Visão De Computador e obteve uma chave </a> de subscrição e URL de ponto final. Se não o fez, siga um [rápido arranque](../quickstarts-sdk/image-analysis-client-library.md) para começar.
  
## <a name="submit-data-to-the-service"></a>Enviar dados para o serviço

Submete uma imagem local ou uma imagem remota à API de Análise. Para locais, coloque os dados de imagem binários no organismo de pedido HTTP. Para o controlo remoto, especifique o URL da imagem formando o corpo de pedido como o seguinte: `{"url":"http://example.com/images/test.jpg"}` .

## <a name="determine-how-to-process-the-data"></a>Determinar como processar os dados

###  <a name="select-visual-features"></a>Selecione funcionalidades visuais

A [API de Análise](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b) dá-lhe acesso a todas as funcionalidades de análise de imagem do serviço. Tem de especificar quais as funcionalidades que pretende utilizar definindo os parâmetros de consulta URL. Um parâmetro pode ter múltiplos valores, separados por vírgulas. Cada recurso que especifique requer tempo de cálculo adicional, por isso apenas especifique o que precisa.

|Parâmetro de URL | Valor | Descrição|
|---|---|--|
|`visualFeatures`|`Adult` | deteta se a imagem é pornográfica (representa nudez ou um ato sexual), ou é sangrenta (retrata extrema violência ou sangue). O conteúdo sexualmente sugestivo (aka conteúdo picante) também é detetado.|
||`Brands` | deteta várias marcas dentro de uma imagem, incluindo a localização aproximada. O argumento da Brands só está disponível em inglês.|
||`Categories` | categoriza o conteúdo da imagem de acordo com uma taxonomia definida na documentação. Este é o valor padrão de `visualFeatures` .|
||`Color` | determina a cor do acento, a cor dominante, e se uma imagem é preto&branco.|
||`Description` | descreve o conteúdo da imagem com uma frase completa em línguas apoiadas.|
||`Faces` | deteta se os rostos estão presentes. Se estiver presente, gere coordenadas, sexo e idade.|
||`ImageType` | deteta se a imagem é clip art ou um desenho de linha.|
||`Objects` | deteta vários objetos dentro de uma imagem, incluindo a localização aproximada. O argumento Objects só está disponível em inglês.|
||`Tags` | marca a imagem com uma lista detalhada de palavras relacionadas com o conteúdo da imagem.|
|`details`| `Celebrities` | identifica celebridades se detetadas na imagem.|
||`Landmarks` |identifica marcos se detetados na imagem.|

Uma URL povoada pode parecer o seguinte:

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities`

### <a name="specify-languages"></a>Especificar línguas

Também pode especificar o idioma dos dados devolvidos. O seguinte parâmetro de consulta de URL especifica o idioma. O valor predefinido é `en`.

|Parâmetro de URL | Valor | Descrição|
|---|---|--|
|`language`|`en` | Inglês|
||`es` | Espanhol|
||`ja` | Japonês|
||`pt` | Português|
||`zh` | Chinês Simplificado|

Uma URL povoada pode parecer o seguinte:

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities&language=en`

> [!NOTE]
> **Chamadas de API âmbito**
>
> Algumas das funcionalidades na Análise de Imagem podem ser chamadas diretamente, bem como através da chamada de API de análise. Por exemplo, pode fazer uma análise de âmbito de apenas etiquetas de imagem, fazendo um pedido para `https://{endpoint}/vision/v3.2-preview.3/tag` . Consulte a [documentação de referência](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b) para outras funcionalidades que podem ser chamadas separadamente.

## <a name="get-results-from-the-service"></a>Obtenha resultados do serviço

O serviço devolve uma `200` resposta HTTP e o corpo contém os dados devolvidos sob a forma de uma cadeia JSON. Segue-se um exemplo de uma resposta JSON.

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

Consulte a tabela seguinte para explicações dos campos neste exemplo:

Campo | Tipo | Conteúdo
------|------|------|
Etiquetas  | `object` | O objeto de nível superior para uma variedade de tags.
tags[].Name | `string`    | A palavra-chave do classificador de etiquetas.
tags[].Score    | `number`    | A pontuação de confiança, entre 0 e 1.
descrição     | `object`    | O objeto de nível superior para uma descrição da imagem.
description.tags[] |    `string`    | A lista de etiquetas. Se não houver confiança suficiente na capacidade de produzir uma legenda, as etiquetas podem ser a única informação disponível para o chamador.
description.captions[].text    | `string`    | Uma frase que descreve a imagem.
description.captions[].confidence    | `number`    | A pontuação de confiança para a frase.

### <a name="error-codes"></a>Códigos de erro

Consulte a seguinte lista de possíveis erros e suas causas:

* 400
    * InvalidImageUrl - O URL de imagem está mal formatado ou não está acessível.
    * InvalidImageFormat - Os dados de entrada não são uma imagem válida.
    * InvalidImageSize - A imagem de entrada é demasiado grande.
    * NotSupportedVualFeature - O tipo de recurso especificado não é válido.
    * NotSupportedImage - Imagem não suportada, por exemplo, pornografia infantil.
    * InvalidDetails - Valor do parâmetro não `detail` suportado.
    * NotSupportedLanguage - A operação solicitada não é suportada na língua especificada.
    * BadArgument - Detalhes adicionais são fornecidos na mensagem de erro.
* 415 - Erro do tipo de mídia não suportado. O Tipo de Conteúdo não está nos tipos permitidos:
    * Para um URL de imagem: O Tipo de Conteúdo deve ser aplicação/json
    * Para um dado de imagem binário: O tipo de conteúdo deve ser aplicação/fluxo de octeto ou multiparte/dados de formulário
* 500
    * FalhadoToProcessamento
    * Tempo limite - Tempo de processamento de imagem esgotado.
    * InternalServerError

## <a name="next-steps"></a>Passos seguintes

Para experimentar a API REST, aceda à [Referência API de Análise de Imagem.](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b)
