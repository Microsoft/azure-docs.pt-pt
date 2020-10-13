---
title: 'Quickstart: Treine um modelo e extraia dados de formulário usando cURL - Reconhecimento de Formulários'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, você usará o Formulário Reconhecendo API rest com cURL para treinar um modelo e extrair dados de formulários.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: pafarley
ms.openlocfilehash: 82b44c00b0ee4160a4baf6ad6cca7a97350d6239
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91970950"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Quickstart: Treine um modelo de reconhecimento de formulário e extraia dados de formulário utilizando a API REST com cURL

Neste arranque rápido, você usará o API API do Reconhecimento de FormulárioS Azure com cURL para treinar e marcar formulários para extrair pares e tabelas de valor-chave.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, você deve ter:
- [cURL](https://curl.haxx.se/windows/) instalado.
- Um conjunto de pelo menos seis formas do mesmo tipo. Vais usar cinco destes para treinar o modelo, e depois vais testá-lo com a sexta forma. Os seus formulários podem ser de diferentes tipos de ficheiros, mas devem ser o mesmo tipo de documento. Pode utilizar um [conjunto de dados de amostra](https://go.microsoft.com/fwlink/?linkid=2090451) (descarregar e extrair *sample_data.zip) *para este arranque rápido. Faça o upload dos ficheiros de treino para a raiz de um recipiente de armazenamento de bolhas numa conta de armazenamento Azure de nível de desempenho padrão. Pode colocar os ficheiros de teste numa pasta separada.

## <a name="create-a-form-recognizer-resource"></a>Criar um recurso de reconhecimento de formulários

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Treine um modelo de reconhecimento de formulário

Primeiro, vai precisar de um conjunto de dados de treino numa bolha de armazenamento Azure. Deverá ter um mínimo de cinco formulários preenchidos (documentos PDF e/ou imagens) do mesmo tipo/estrutura que os seus principais dados de entrada. Ou pode usar um único formulário vazio com dois formulários preenchidos. O nome do ficheiro vazio precisa incluir a palavra "vazio". Consulte [Construir um conjunto de dados de treino para um modelo personalizado](../build-training-data-set.md) para dicas e opções para reunir os seus dados de treino.

> [!NOTE]
> Pode utilizar a função de dados etiquetada para rotular manualmente alguns ou todos os seus dados de treino previamente. Este é um processo mais complexo, mas resulta num modelo mais bem treinado. Consulte a secção [Train com etiquetas](../overview.md#train-with-labels) da visão geral para saber mais sobre esta funcionalidade.



Para treinar um modelo de Reconhecimento de Formulários com os documentos no seu recipiente de bolhas Azure, ligue para o **[Modelo Personalizado](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** de Comboio API executando o seguinte comando cURL. Antes de executar o comando, faça estas alterações:

1. `<Endpoint>`Substitua-o pelo ponto final que obteve com a subscrição do Form Recogniser.
1. `<subscription key>`Substitua-a pela chave de subscrição que copiou do passo anterior.
1. `<SAS URL>`Substitua-a pelo URL de acesso partilhado do recipiente de armazenamento Azure Blob (SAS). Para recuperar o URL SAS, abra o Microsoft Azure Storage Explorer, clique com o botão direito no seu recipiente e selecione **Obter assinatura de acesso partilhado**. Certifique-se de que as permissões **de Leitura** e **Lista** são verificadas e clique em **Criar**. Em seguida, copie o valor na secção **URL.** Deve ter o formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

    # <a name="v20"></a>[v2.0](#tab/v2-0)    
    ```bash
    curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
    ```
    # <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)    
    ```bash
    curl -i -X POST "https://<Endpoint>/formrecognizer/v2.1-preview.1/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
    ```
    
    ---



Receberá uma `201 (Success)` resposta com um **cabeçalho de localização.** O valor deste cabeçalho é a identificação do novo modelo que está a ser treinado.

## <a name="get-training-results"></a>Obtenha resultados de formação

Depois de iniciar a operação do comboio, use uma nova operação, **[Obtenha o Modelo Personalizado](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** para verificar o estado do treino. Passe o ID do modelo para esta chamada da API para verificar o estado da formação:

1. `<Endpoint>`Substitua-o pelo ponto final que obteve pela tecla de subscrição do Form Recogniser.
1. `<subscription key>`Substitua-a pela chave de subscrição
1. `<model ID>`Substitua-se pelo ID do modelo que recebeu no passo anterior



# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
# <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.1-preview.1/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```ce\": \""<SAS URL>"\"}"
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

## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Analisar formulários para pares e tabelas de valor-chave

Em seguida, você usará o seu modelo recém-treinado para analisar um documento e extrair pares e tabelas de valor chave a partir dele. Ligue para a **[API do formulário de análise](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** executando o seguinte comando cURL. Antes de executar o comando, faça estas alterações:

1. `<Endpoint>`Substitua-o pelo ponto final que obteve da tecla de subscrição do Form Recogniser. Pode encontrá-lo no separador **'Visão geral'** do recurso 'Reconhecimento de Formulário'.
1. `<model ID>`Substitua-o pelo ID do modelo que recebeu na secção anterior.
1. Substitua `<SAS URL>` por um URL SAS no seu ficheiro no armazenamento Azure. Siga os passos na secção De Formação, mas em vez de obter um URL SAS para todo o recipiente blob, obtenha um para o ficheiro específico que pretende analisar.
1. Substitua `<subscription key>` pela sua chave de subscrição.

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```
# <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)    
```bash
```bash
curl -v "https://<Endpoint>/formrecognizer/v2.1-preview.1/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```
    
---



Receberá uma `202 (Success)` resposta com um **cabeçalho de operação-localização.** O valor deste cabeçalho inclui um ID de resultados que utiliza para acompanhar os resultados da operação Análise. Guarde este ID de resultados para o próximo passo.

## <a name="get-the-analyze-results"></a>Obtenha os resultados da Análise

Utilize a seguinte API para consultar os resultados da operação Análise.

1. `<Endpoint>`Substitua-o pelo ponto final que obteve da tecla de subscrição do Form Recogniser. Pode encontrá-lo no separador **'Visão geral'** do recurso 'Reconhecimento de Formulário'.
1. `<result ID>`Substitua-o pelo ID que recebeu na secção anterior.
1. Substitua `<subscription key>` pela sua chave de subscrição.


# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
# <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.1-preview/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
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


## <a name="improve-results"></a>Melhorar os resultados

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, você usou o Form Recogniser REST API com cURL para treinar um modelo e executá-lo em um cenário de amostra. Em seguida, consulte a documentação de referência para explorar mais aprofundadamente a API do Reconhecimento de Formulários.

> [!div class="nextstepaction"]
> [Documentação de referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
