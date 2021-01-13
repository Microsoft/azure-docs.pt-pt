---
title: 'Quickstart: Biblioteca de clientes do Reconhecimento de Formulários para .NET'
description: Utilize a biblioteca cliente Do Reconhecimento de Formulários para .NET para criar uma aplicação de processamento de formulários que extrai pares de chaves/valor e dados de tabela dos seus documentos personalizados.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 31e1a0d912c6623f57d4ea256968102604ce42ff
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98132341"
---
> [!NOTE]
> Este guia utiliza cURL para executar chamadas REST API. Há também [um código de amostra no GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/FormRecognizer/rest) que ilustra como chamar as APIs rest com Python.

## <a name="prerequisites"></a>Pré-requisitos

* [cURL](https://curl.haxx.se/windows/) instalado.
* Subscrição Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Uma bolha de armazenamento Azure que contém um conjunto de dados de treino. Consulte [Construir um conjunto de dados de treino para um modelo personalizado](../../build-training-data-set.md) para dicas e opções para reunir o seu conjunto de dados de treino. Para este arranque rápido, pode utilizar os ficheiros sob a pasta **Train** do conjunto de [dados](https://go.microsoft.com/fwlink/?linkid=2090451) da amostra (descarregar e extrair *sample_data.zip*).
* Assim que tiver a sua subscrição Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" Crie um recurso De Reconhecimento de "  target="_blank"> Formulários crie um recurso De Reconhecimento de <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Formulários no portal Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API do Reconhecimento de Formulários. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.
* Uma URL para uma imagem de um recibo. Pode utilizar uma [imagem de amostra](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg) para este arranque rápido.
* Um URL para uma imagem de um cartão de visita. Pode utilizar uma [imagem de amostra](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg) para este arranque rápido.
* Um URL para uma imagem de uma fatura. Pode utilizar um [documento de amostra](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf) para este arranque rápido.


## <a name="analyze-layout"></a>Analisar layout

Pode utilizar o Form Recogniser para reconhecer e extrair tabelas, linhas e palavras em documentos, sem precisar de treinar um modelo. Antes de executar o comando, faça estas alterações:

1. `{Endpoint}`Substitua-o pelo ponto final que obteve com a subscrição do Form Recogniser.
1. `{subscription key}`Substitua-a pela chave de subscrição que copiou do passo anterior.
1. Substitua o URL no corpo de pedido por um dos URLs de exemplo.

# <a name="v20"></a>[v2.0](#tab/v2-0)    

```bash
curl -v -X POST "https://{Endpoint}/formrecognizer/v2.0/layout/analyze"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
--data-ascii "{\"source\": \"http://example.com/test.jpg\"}" 
```

# <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)    

```bash
curl -v -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/layout/analyze"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
--data-ascii "{\"source\": \"http://example.com/test.jpg\"}" 
```
---

Receberá uma `202 (Success)` resposta que inclui o cabeçalho am **Operation-Location.** O valor deste cabeçalho contém um ID de operação que pode utilizar para consultar o estado da operação assíncronea e obter os resultados. No exemplo seguinte, a corda seguinte `analyzeResults/` é o ID de funcionamento.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>Obtenha resultados de layout

Depois de ter chamado a API do **[Layout de Análise,](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync)** ligue para a API **[do Resultado do Layout de Análise](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeLayoutResult)** para obter o estado da operação e os dados extraídos. Antes de executar o comando, faça estas alterações:

1. `{Endpoint}`Substitua-o pelo ponto final que obteve com a subscrição do Form Recogniser.
1. `{subscription key}`Substitua-a pela chave de subscrição que copiou do passo anterior.
1. `{resultId}`Substitua-a pelo ID de funcionamento do passo anterior.

# <a name="v20"></a>[v2.0](#tab/v2-0)    

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/layout/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

# <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)  
```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/layout/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```
---

### <a name="examine-the-results"></a>Examinar os resultados

Receberá uma `200 (success)` resposta com o conteúdo da JSON.

Consulte a seguinte imagem de fatura e a respetiva saída JSON. A saída foi encurtada para a simplicidade. O `"readResults"` nó contém todas as linhas de texto com a respetiva colocação da caixa de limitação na página. O `"selectionMarks"` nó (na pré-visualização v2.1) mostra todas as marcas de seleção (caixa de verificação, marca de rádio) e se o seu estado é "selecionado" ou "não selecionado". O `"pageResults"` campo mostra cada pedaço de texto dentro das tabelas, cada um com a sua coordenada de coluna de linha.

:::image type="content" source="../../media/contoso-invoice.png" alt-text="Documento de declaração do projeto Contoso com uma mesa.":::

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:36:52Z",
    "lastUpdatedDateTime": "2020-08-20T20:36:58Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/14/words/0",
                                    "#/readResults/0/lines/14/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
```

# <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)   
```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:40:50Z",
    "lastUpdatedDateTime": "2020-08-20T20:40:55Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                        ]
                    }
                ],
                "selectionMarks": [
                    {
                        "boundingBox": [
                            3.9737,
                            3.7475,
                            4.1693,
                            3.7475,
                            4.1693,
                            3.9428,
                            3.9737,
                            3.9428
                        ],
                        "confidence": 0.989,
                        "state": "selected"
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/12/words/0",
                                    "#/readResults/0/lines/12/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
``` 

---

## <a name="analyze-receipts"></a>Analisar recibos

Para começar a analisar um recibo, ligue para a API **[de Receção de Análise](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeReceiptAsync)** utilizando o comando cURL abaixo. Antes de executar o comando, faça estas alterações:

1. `{Endpoint}`Substitua-o pelo ponto final que obteve com a subscrição do Form Recogniser.
1. `{your receipt URL}`Substitua-o pelo endereço URL de uma imagem de receção.
1. `{subscription key>`Substitua-a pela chave de subscrição que copiou do passo anterior.

# <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ \"source\": \"{your receipt URL}\"}"
```

# <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)    

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ \"source\": \"{your receipt URL}\"}"
```
---

Receberá uma `202 (Success)` resposta que inclui o cabeçalho am **Operation-Location.** O valor deste cabeçalho contém um ID de operação que pode utilizar para consultar o estado da operação assíncronea e obter os resultados. No exemplo seguinte, a corda seguinte `operations/` é o ID de funcionamento.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-the-receipt-results"></a>Obtenha os resultados do recibo

Depois de ter chamado a API **do Recibo de Análise,** ligue para a API **[do Resultado do Recibo de Análise](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeReceiptResult)** para obter o estado da operação e os dados extraídos. Antes de executar o comando, faça estas alterações:

1. `{Endpoint}`Substitua-o pelo ponto final que obteve pela tecla de subscrição do Form Recogniser. Pode encontrá-lo no separador **'Visão geral'** do recurso 'Reconhecimento de Formulário'.
1. `{operationId}`Substitua-a pelo ID de funcionamento do passo anterior.
1. Substitua `{subscription key}` pela sua chave de subscrição.

# <a name="v20"></a>[v2.0](#tab/v2-0)  
```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
# <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)  
```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
---

### <a name="examine-the-response"></a>Examinar a resposta

Receberá uma `200 (Success)` resposta com a saída da JSON. O primeiro `"status"` campo, indica o estado da operação. Se a operação estiver concluída, o `"readResults"` campo contém todas as linhas de texto que foram extraídas do recibo, e o campo contém `"documentResults"` informações de chave/valor para as partes mais relevantes do recibo. Se a operação não estiver concluída, o valor será `"status"` ou , e deverá voltar a ligar para a `"running"` `"notStarted"` API, manualmente ou através de um script. Recomendamos um intervalo de um segundo ou mais entre chamadas.

Consulte a seguinte imagem de recibo e a respetiva saída JSON. A saída foi encurtada para a legibilidade.

![Um recibo da loja Contoso](../../media/contoso-allinone.jpg)

O `"readResults"` nó contém todo o texto reconhecido (se definir o parâmetro opcional *incluaTextDetails* para `true` ). O texto é organizado por página, depois por linha, depois por palavras individuais. O `"documentResults"` nó contém os valores específicos do recibo que o modelo descobriu. É aqui que você encontrará pares de chaves/valor úteis como o endereço de imposto, total, comerciante, e assim por diante.

```json
{
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{
    "version":"2.1.0",
    "readResults":[
      {
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[
          {
            "text":"Contoso",
            "boundingBox":[
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[
              {
                "text":"Contoso",
                "boundingBox":[
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[
      {
        "docType":"prebuilt:receipt",
        "pageRange":[
          1,
          1
        ],
        "fields":{
          "ReceiptType":{
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{
            "type":"array",
            "valueArray":[
              {
                "type":"object",
                "valueObject":{
                  "Quantity":{
                    "type":"number",
                    "text":"1",
                    "boundingBox":[
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="analyze-business-cards"></a>Analisar cartões de visita

# <a name="v20"></a>[v2.0](#tab/v2-0)  

> [!IMPORTANT]
> Esta funcionalidade não está disponível na versão API selecionada.

# <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)  

Para começar a analisar um cartão de visita, ligue para a API do **[Cartão De Negócios analisando](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)** o comando cURL abaixo. Antes de executar o comando, faça estas alterações:

1. `{Endpoint}`Substitua-o pelo ponto final que obteve com a subscrição do Form Recogniser.
1. `{your receipt URL}`Substitua-o pelo endereço URL de uma imagem de receção.
1. `{subscription key}`Substitua-a pela chave de subscrição que copiou do passo anterior.

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ \"source\": \"{your receipt URL}\"}"
```

Receberá uma `202 (Success)` resposta que inclui o cabeçalho am **Operation-Location.** O valor deste cabeçalho contém um ID de operação que pode utilizar para consultar o estado da operação assíncronea e obter os resultados.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-business-card-results"></a>Obtenha resultados do cartão de visita

Depois de ter chamado a API do **Cartão de Visita,** ligue para a API do **[Resultado do Cartão de Visita da Análise](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeBusinessCardResult)** para obter o estado da operação e os dados extraídos. Antes de executar o comando, faça estas alterações:

1. `{Endpoint}`Substitua-o pelo ponto final que obteve pela tecla de subscrição do Form Recogniser. Pode encontrá-lo no separador **'Visão geral'** do recurso 'Reconhecimento de Formulário'.
1. `{resultId}`Substitua-a pelo ID de funcionamento do passo anterior.
1. Substitua `{subscription key}` pela sua chave de subscrição.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Examinar a resposta

Receberá uma `200 (Success)` resposta com a saída da JSON. O `"readResults"` nó contém todo o texto reconhecido. O texto é organizado por página, depois por linha, depois por palavras individuais. O `"documentResults"` nó contém os valores específicos do cartão de visita que o modelo descobriu. É aqui que encontrará informações úteis de contacto como o nome da empresa, o nome próprio, o apelido, o número de telefone, e assim por diante.

![Um cartão de visita da empresa Contoso](../../media/business-card-english.jpg)

Esta amostra ilustra a saída JSON devolvida pelo Form Recogniser. Foi truncado para a legibilidade.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-06-04T08:19:29Z",
    "lastUpdatedDateTime": "2020-06-04T08:19:35Z",
    "analyzeResult": {
        "version": "2.1.1",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel"
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.993
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

O script imprimirá respostas à consola até que a operação **do Cartão De Visita de Análise** esteja concluída.

---

## <a name="analyze-invoices"></a>Analisar faturas

# <a name="version-20"></a>[versão 2.0](#tab/v2-0)

> [!IMPORTANT]
> Esta funcionalidade não está disponível na versão API selecionada.

# <a name="version-21-preview"></a>[versão 2.1 pré-visualização](#tab/v2-1)

Para começar a analisar uma fatura, ligue para a **[API de Fatura de Análise](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9843c2794cbb1a96291)** utilizando o comando cURL abaixo. Antes de executar o comando, faça estas alterações:

1. `{Endpoint}`Substitua-o pelo ponto final que obteve com a subscrição do Form Recogniser.
1. `{your invoice URL}`Substitua-o pelo endereço URL de um documento de fatura.
1. Substitua `{subscription key}` pela sua chave de subscrição.

```bash
curl -v -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyze"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
--data-ascii "{ \"source\": \"{your invoice URL}\"}"
```

Receberá uma `202 (Success)` resposta que inclui o cabeçalho am **Operation-Location.** O valor deste cabeçalho contém um ID de operação que pode utilizar para consultar o estado da operação assíncronea e obter os resultados.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-invoice-results"></a>Obtenha resultados da fatura

Depois de ter chamado a **API de Fatura de Análise,** ligue para a API **[do Resultado da Fatura de Análise](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9acb78c40a2533aee83)** para obter o estado da operação e os dados extraídos. Antes de executar o comando, faça estas alterações:

1. `{Endpoint}`Substitua-o pelo ponto final que obteve pela tecla de subscrição do Form Recogniser. Pode encontrá-lo no separador **'Visão geral'** do recurso 'Reconhecimento de Formulário'.
1. `{resultId}`Substitua-a pelo ID de funcionamento do passo anterior.
1. Substitua `{subscription key}` pela sua chave de subscrição.

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Examinar a resposta

Receberá uma `200 (Success)` resposta com a saída da JSON. O `"readResults"` campo contém todas as linhas de texto que foram extraídas da fatura, `"pageResults"` incluindo as tabelas e marcas de seleções extraídas da fatura e o `"documentResults"` campo contém informações de chave/valor para as partes mais relevantes da fatura.

Consulte o seguinte documento de fatura e a respetiva saída JSON. O conteúdo JSON foi encurtado para a legibilidade.

* [Fatura da amostra](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf)

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-11-06T23:32:11Z",
    "lastUpdatedDateTime": "2020-11-06T23:32:20Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [{
            "page": 1,
            "angle": 0,
            "width": 8.5,
            "height": 11,
            "unit": "inch"
        }],
        "pageResults": [{
            "page": 1,
            "tables": [{
                "rows": 3,
                "columns": 4,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "QUANTITY",
                    "boundingBox": [0.4953,
                    5.7306,
                    1.8097,
                    5.7306,
                    1.7942,
                    6.0122,
                    0.4953,
                    6.0122]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "DESCRIPTION",
                    "boundingBox": [1.8097,
                    5.7306,
                    5.7529,
                    5.7306,
                    5.7452,
                    6.0122,
                    1.7942,
                    6.0122]
                },
                ...
                ],
                "boundingBox": [0.4794,
                5.7132,
                8.0158,
                5.714,
                8.0118,
                6.5627,
                0.4757,
                6.5619]
            },
            {
                "rows": 2,
                "columns": 6,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "SALESPERSON",
                    "boundingBox": [0.4979,
                    4.963,
                    1.8051,
                    4.963,
                    1.7975,
                    5.2398,
                    0.5056,
                    5.2398]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "P.O. NUMBER",
                    "boundingBox": [1.8051,
                    4.963,
                    3.3047,
                    4.963,
                    3.3124,
                    5.2398,
                    1.7975,
                    5.2398]
                },
                ...
                ],
                "boundingBox": [0.4976,
                4.961,
                7.9959,
                4.9606,
                7.9959,
                5.5204,
                0.4972,
                5.5209]
            }]
        }],
        "documentResults": [{
            "docType": "prebuilt:invoice",
            "pageRange": [1,
            1],
            "fields": {
                "AmountDue": {
                    "type": "number",
                    "valueNumber": 610,
                    "text": "$610.00",
                    "boundingBox": [7.3809,
                    7.8153,
                    7.9167,
                    7.8153,
                    7.9167,
                    7.9591,
                    7.3809,
                    7.9591],
                    "page": 1,
                    "confidence": 0.875
                },
                "BillingAddress": {
                    "type": "string",
                    "valueString": "123 Bill St, Redmond WA, 98052",
                    "text": "123 Bill St, Redmond WA, 98052",
                    "boundingBox": [0.594,
                    4.3724,
                    2.0125,
                    4.3724,
                    2.0125,
                    4.7125,
                    0.594,
                    4.7125],
                    "page": 1,
                    "confidence": 0.997
                },
                "BillingAddressRecipient": {
                    "type": "string",
                    "valueString": "Microsoft Finance",
                    "text": "Microsoft Finance",
                    "boundingBox": [0.594,
                    4.1684,
                    1.7907,
                    4.1684,
                    1.7907,
                    4.2837,
                    0.594,
                    4.2837],
                    "page": 1,
                    "confidence": 0.998
                },
                ...                
            }
        }]
    }
}
```

---

## <a name="train-a-custom-model"></a>Preparar um modelo personalizado

Para treinar um modelo personalizado, você precisará de um conjunto de dados de treino em uma bolha de armazenamento Azure. Deverá ter um mínimo de cinco formulários preenchidos (documentos PDF e/ou imagens) do mesmo tipo/estrutura que os seus principais dados de entrada. Ou pode usar um único formulário vazio com dois formulários preenchidos. O nome do ficheiro vazio precisa incluir a palavra "vazio". Consulte [Construir um conjunto de dados de treino para um modelo personalizado](../../build-training-data-set.md) para dicas e opções para reunir os seus dados de treino.

> [!NOTE]
> Pode utilizar a função de dados etiquetada para rotular manualmente alguns ou todos os seus dados de treino previamente. Este é um processo mais complexo, mas resulta num modelo mais bem treinado. Consulte a secção [Train com etiquetas](../../overview.md#train-with-labels) da visão geral para saber mais sobre esta funcionalidade.

Para treinar um modelo de Reconhecimento de Formulários com os documentos no seu recipiente de bolhas Azure, ligue para o **[Modelo Personalizado](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** de Comboio API executando o seguinte comando cURL. Antes de executar o comando, faça estas alterações:

1. `{Endpoint}`Substitua-o pelo ponto final que obteve com a subscrição do Form Recogniser.
1. `{subscription key}`Substitua-a pela chave de subscrição que copiou do passo anterior.
1. `{SAS URL}`Substitua-a pelo URL de acesso partilhado do recipiente de armazenamento Azure Blob (SAS). [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Recuperação de URL SAS":::

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ \"source\": \""{SAS URL}"\"}"
```
# <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)    
```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ \"source\": \""{SAS URL}"\"}"
```

---


Receberá uma `201 (Success)` resposta com um **cabeçalho de localização.** O valor deste cabeçalho é a identificação do novo modelo que está a ser treinado.

### <a name="get-training-results"></a>Obtenha resultados de formação

Depois de iniciar a operação do comboio, use uma nova operação, **[Obtenha o Modelo Personalizado](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** para verificar o estado do treino. Passe o ID do modelo para esta chamada da API para verificar o estado da formação:

1. `{Endpoint}`Substitua-o pelo ponto final que obteve pela tecla de subscrição do Form Recogniser.
1. `{subscription key}`Substitua-a pela chave de subscrição
1. `{model ID}`Substitua-se pelo ID do modelo que recebeu no passo anterior


# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
# <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)    
```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
    
---

Receberá uma `200 (Success)` resposta com um corpo JSON no seguinte formato. Reparem no `"status"` campo. Isto terá o valor assim que o `"ready"` treino estiver completo. Se o modelo não terminar o treino, terá de consultar novamente o serviço, repetindo o comando. Recomendamos um intervalo de um segundo ou mais entre chamadas.

O `"modelId"` campo contém a identificação do modelo que estás a treinar. Vai precisar disto para o próximo passo.

```json
{
  "modelInfo":{
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{
    "trainingDocuments":[
      {
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      }
    ],
    "errors":[

    ]
  },
  "keys":{
    "0":[
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-with-a-custom-model"></a>Analisar formas com um modelo personalizado

Em seguida, você usará o seu modelo recém-treinado para analisar um documento e extrair pares e tabelas de valor chave a partir dele. Ligue para a **[API do formulário de análise](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** executando o seguinte comando cURL. Antes de executar o comando, faça estas alterações:

1. `{Endpoint}`Substitua-o pelo ponto final que obteve da tecla de subscrição do Form Recogniser. Pode encontrá-lo no separador **'Visão geral'** do recurso 'Reconhecimento de Formulário'.
1. `{model ID}`Substitua-o pelo ID do modelo que recebeu na secção anterior.
1. Substitua `{SAS URL}` por um URL SAS no seu ficheiro no armazenamento Azure. Siga os passos na secção De Formação, mas em vez de obter um URL SAS para todo o recipiente blob, obtenha um para o ficheiro específico que pretende analisar.
1. Substitua `{subscription key}` pela sua chave de subscrição.

# <a name="v20"></a>[v2.0](#tab/v2-0)    

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ \"source\": \""{SAS URL}"\" } "
```

# <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)    
```bash
curl -v "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{model ID}/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ \"source\": \""{SAS URL}"\" } "
```
    
---



Receberá uma `202 (Success)` resposta com um **cabeçalho de operação-localização.** O valor deste cabeçalho inclui um ID de resultados que utiliza para acompanhar os resultados da operação Análise. Guarde este ID de resultados para o próximo passo.

### <a name="get-the-analyze-results"></a>Obtenha os resultados da Análise

Utilize a seguinte API para consultar os resultados da operação Análise.

1. `{Endpoint}`Substitua-o pelo ponto final que obteve da tecla de subscrição do Form Recogniser. Pode encontrá-lo no separador **'Visão geral'** do recurso 'Reconhecimento de Formulário'.
1. `{result ID}`Substitua-o pelo ID que recebeu na secção anterior.
1. Substitua `{subscription key}` pela sua chave de subscrição.

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
# <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)    
```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
---

Receberá uma `200 (Success)` resposta com um corpo JSON no seguinte formato. A saída foi encurtada para a simplicidade. Reparem no `"status"` campo perto do fundo. Isto terá o valor `"succeeded"` quando a operação 'Analisar' estiver concluída. Se a operação 'Analisar' não tiver concluído, terá de consultar novamente o serviço, reexecutando o comando. Recomendamos um intervalo de um segundo ou mais entre chamadas.

As principais associações e tabelas de pares chave/valor estão no `"pageResults"` nó. Se também especificou a extração de texto simples através do parâmetro *URL incluindoTextDetails,* então o `"readResults"` nó mostrará o conteúdo e as posições de todo o texto no documento.

Esta amostra de saída JSON foi encurtada para a simplicidade.

# <a name="v20"></a>[v2.0](#tab/v2-0)
```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T00:46:25Z",
  "lastUpdatedDateTime": "2020-08-21T00:46:32Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0153,
              0.275,
              8.0944,
              0.275,
              8.0944,
              0.7125,
              5.0153,
              0.7125
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0153,
                  0.275,
                  6.2278,
                  0.275,
                  6.2278,
                  0.7125,
                  5.0153,
                  0.7125
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3292,
                  0.275,
                  8.0944,
                  0.275,
                  8.0944,
                  0.7125,
                  6.3292,
                  0.7125
                ]
              }
            ]
          }, 
        ...
        ]
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9722,
                1.0264,
                7.3417,
                1.0264,
                7.3417,
                1.1931,
                6.9722,
                1.1931
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "confidence": 1
          },
         ...
        ],
        "tables": [
          {
            "rows": 4,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6931,
                  4.2444,
                  1.5681,
                  4.2444,
                  1.5681,
                  4.4125,
                  0.6931,
                  4.4125
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```    
# <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)    
```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T01:13:28Z",
  "lastUpdatedDateTime": "2020-08-21T01:13:42Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0444,
              0.3613,
              8.0917,
              0.3613,
              8.0917,
              0.6718,
              5.0444,
              0.6718
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0444,
                  0.3587,
                  6.2264,
                  0.3587,
                  6.2264,
                  0.708,
                  5.0444,
                  0.708
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3361,
                  0.3635,
                  8.0917,
                  0.3635,
                  8.0917,
                  0.6396,
                  6.3361,
                  0.6396
                ]
              }
            ]
          }, 
          ...
        ] 
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9833,
                1.0615,
                7.3333,
                1.0615,
                7.3333,
                1.1649,
                6.9833,
                1.1649
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "value": {
              "text": "9/10/2020",
              "boundingBox": [
                7.3833,
                1.0802,
                7.925,
                1.0802,
                7.925,
                1.174,
                7.3833,
                1.174
              ],
              "elements": [
                "#/readResults/0/lines/3/words/0"
              ]
            },
            "confidence": 1
          },
          ...
        ], 
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6944,
                  4.2779,
                  1.5625,
                  4.2779,
                  1.5625,
                  4.4005,
                  0.6944,
                  4.4005
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ], 
    "documentResults": [],
    "errors": []
  }
}
``` 
---

### <a name="improve-results"></a>Melhorar os resultados

[!INCLUDE [improve results](../improve-results-unlabeled.md)]

## <a name="manage-custom-models"></a>Gerir modelos personalizados

### <a name="get-a-list-of-custom-models"></a>Obtenha uma lista de modelos personalizados

Utilize o seguinte comando para devolver uma lista de todos os modelos personalizados que pertencem à sua subscrição.

1. `{Endpoint}`Substitua-o pelo ponto final que obteve com a subscrição do Form Recogniser.
1. `{subscription key}`Substitua-a pela chave de subscrição que copiou do passo anterior.

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

# <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)    

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```
---

Receberá uma resposta de `200` sucesso, com dados JSON como os seguintes. O `"modelList"` elemento contém todos os seus modelos criados e as suas informações.

```json
{
  "summary": {
    "count": 0,
    "limit": 0,
    "lastUpdatedDateTime": "string"
  },
  "modelList": [
    {
      "modelId": "string",
      "status": "creating",
      "createdDateTime": "string",
      "lastUpdatedDateTime": "string"
    }
  ],
  "nextLink": "string"
}
```

### <a name="get-a-specific-model"></a>Obtenha um modelo específico

Para obter informações detalhadas sobre um modelo personalizado específico, utilize o seguinte comando.

1. `{Endpoint}`Substitua-o pelo ponto final que obteve com a subscrição do Form Recogniser.
1. `{subscription key}`Substitua-a pela chave de subscrição que copiou do passo anterior.
1. `{modelId}`Substitua-o pelo ID do modelo personalizado que pretende procurar.

# <a name="v20"></a>[v2.0](#tab/v2-0)    

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
--data-ascii "{body}" 
```

# <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)    

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{modelId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
--data-ascii "{body}" 
```
---

Receberá uma resposta de `200` sucesso, com dados JSON como os seguintes.

```json
{
  "modelInfo": {
    "modelId": "string",
    "status": "creating",
    "createdDateTime": "string",
    "lastUpdatedDateTime": "string"
  },
  "keys": {
    "clusters": {}
  },
  "trainResult": {
    "trainingDocuments": [
      {
        "documentName": "string",
        "pages": 0,
        "errors": [
          "string"
        ],
        "status": "succeeded"
      }
    ],
    "fields": [
      {
        "fieldName": "string",
        "accuracy": 0.0
      }
    ],
    "averageModelAccuracy": 0.0,
    "errors": [
      {
        "message": "string"
      }
    ]
  }
}
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, usou a API do Reconhecimento de Formulários para formar modelos e analisar formas de diferentes formas. Em seguida, consulte a documentação de referência para explorar mais aprofundadamente a API do Reconhecimento de Formulários.

> [!div class="nextstepaction"]
> [Documentação de referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)

* [O que é o Reconhecedor de Formato?](../../overview.md)
