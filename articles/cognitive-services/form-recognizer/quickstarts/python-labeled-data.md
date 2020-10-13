---
title: 'Quickstart: Treine com etiquetas usando a API REST e Python - Form Recogniser'
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar a funcionalidade de dados etiquetada do Form Recogniser com a API REST e a Python para treinar um modelo personalizado.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 28849620863f8593e5187dbef9fc6cc978de1824
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961800"
---
# <a name="train-a-form-recognizer-model-with-labels-using-rest-api-and-python"></a>Treine um modelo de reconhecimento de formulário com etiquetas usando REST API e Python

Neste arranque rápido, você usará o Form Recogniser REST API com Python para treinar um modelo personalizado com dados etiquetados manualmente. Consulte a secção [Train com etiquetas](../overview.md#train-with-labels) da visão geral para saber mais sobre esta funcionalidade.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, você deve ter:
- [Python](https://www.python.org/downloads/) instalado (se quiser executar a amostra localmente).
- Um conjunto de pelo menos seis formas do mesmo tipo. Usará estes dados para treinar o modelo e testar um formulário. Pode utilizar um [conjunto de dados de amostra](https://go.microsoft.com/fwlink/?linkid=2090451) para este arranque rápido. Descarregue e extraia *sample_data.zip. * Faça o upload dos ficheiros de treino para a raiz de um recipiente de armazenamento de bolhas numa conta de armazenamento Azure de nível de desempenho padrão.

> [!NOTE]
> Este quickstart utiliza documentos remotos acedidos por URL. Para utilizar ficheiros locais, consulte a [documentação de referência para v2.0](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) e [documentação de referência para v2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/TrainCustomModelAsync).

## <a name="create-a-form-recognizer-resource"></a>Criar um recurso de reconhecimento de formulários

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-training-data"></a>Configurar dados de formação

Em seguida, terá de configurar os dados de entrada necessários. A funcionalidade de dados etiquetada tem requisitos especiais de entrada para além do necessário para treinar um modelo personalizado sem etiquetas.

Certifique-se de que todos os documentos de treino são do mesmo formato. Se tiver formulários em vários formatos, organize-os em subpastas com base no formato comum. Ao preparar, precisará de direcionar a API para uma subpasta.

Para formar um modelo utilizando dados rotulados, necessitará dos seguintes ficheiros como entradas na sub-pasta. Você vai aprender a criar estes ficheiros abaixo.

* **Formulários de origem** – os formulários para extrair dados de. Os tipos suportados são JPEG, PNG, PDF ou TIFF.
* **Ficheiros de layout OCR** - estes são ficheiros JSON que descrevem os tamanhos e posições de todos os textos legíveis em cada formulário de origem. Você usará o Layout API do Layout do Reconhecimento de Formulários para gerar estes dados. 
* **Ficheiros de etiquetas** - estes são ficheiros JSON que descrevem as etiquetas de dados que um utilizador introduziu manualmente.

Todos estes ficheiros devem ocupar a mesma sub-pasta e estar no seguinte formato:

* input_file1.pdf 
* input_file1.pdf.ocr.jsem
* input_file1.pdf.labels.jsem 
* input_file2.pdf 
* input_file2.pdf.ocr.jsem
* input_file2.pdf.labels.jsem
* ...

> [!TIP]
> Quando rotula formas utilizando a [ferramenta de rotulagem](./label-tool.md)da amostra do Reconhecimento de Formulários, a ferramenta cria automaticamente estes ficheiros de design de etiquetas e de layout OCR.

### <a name="create-the-ocr-output-files"></a>Criar os ficheiros de saída OCR

Necessita de ficheiros de resultados OCR para que o serviço considere os ficheiros de entrada correspondentes para a formação rotulada. Para obter os resultados do OCR para um determinado formulário de origem, siga os passos abaixo:

1. Ligue para a **[API do Layout de Análise](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)** no recipiente de Layout lido com o ficheiro de entrada como parte do corpo de pedido. Guarde a identificação encontrada no cabeçalho de **operação-localização** da resposta.
1. Ligue para a API **[do Resultado do Layout de Análise](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeLayoutResult)** get, utilizando o ID de operação do passo anterior.
1. Obtenha a resposta e escreva o conteúdo para um ficheiro. Para cada formulário de origem, o ficheiro OCR correspondente deve ter o nome original do ficheiro anexado a `.ocr.json` . A saída OCR JSON deve ter o seguinte formato. Consulte o [ficheiro OCR](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.ocr.json) da amostra para obter um exemplo completo. 

    # <a name="v20"></a>[v2.0](#tab/v2-0)
    ```json
    {
    "status": "succeeded",
    "createdDateTime": "2019-11-12T21:18:12Z",
    "lastUpdatedDateTime": "2019-11-12T21:18:17Z",
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
                        "language": "en",
                        "boundingBox": [
                            0.5384,
                            1.1583,
                            1.4466,
                            1.1583,
                            1.4466,
                            1.3534,
                            0.5384,
                            1.3534
                        ],
                        "text": "Contoso",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5384,
                                    1.1583,
                                    1.4466,
                                    1.1583,
                                    1.4466,
                                    1.3534,
                                    0.5384,
                                    1.3534
                                ],
                                "text": "Contoso",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
    ```    
    # <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)
    ```json
    {
    "status": "succeeded",
    "createdDateTime": "2019-11-12T21:18:12Z",
    "lastUpdatedDateTime": "2019-11-12T21:18:17Z",
    "analyzeResult": {
        "version": "2.1.0",
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
                        "language": "en",
                        "boundingBox": [
                            0.5384,
                            1.1583,
                            1.4466,
                            1.1583,
                            1.4466,
                            1.3534,
                            0.5384,
                            1.3534
                        ],
                        "text": "Contoso",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5384,
                                    1.1583,
                                    1.4466,
                                    1.1583,
                                    1.4466,
                                    1.3534,
                                    0.5384,
                                    1.3534
                                ],
                                "text": "Contoso",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
    ```    


    ---



### <a name="create-the-label-files"></a>Criar os ficheiros de etiquetas

Os ficheiros de etiquetas contêm associações de valor-chave que um utilizador inseriu manualmente. São necessários para a formação de dados rotulados, mas nem todos os ficheiros de origem precisam de ter um ficheiro de etiqueta correspondente. Os ficheiros de origem sem etiquetas serão tratados como documentos de formação normais. Recomendamos cinco ou mais ficheiros rotulados para um treino fiável. Pode utilizar uma ferramenta de UI como a [ferramenta de rotulagem](./label-tool.md) da amostra para gerar estes ficheiros.

Quando criar um ficheiro de etiqueta, pode especificar opcionalmente as &mdash; posições exatas dos valores no documento. Isto dará ao treino uma precisão ainda maior. As regiões são formatadas como um conjunto de oito valores correspondentes a quatro coordenadas X,Y: topo-esquerda, superior-direita, inferior direita e inferior-esquerda. Os valores de coordenadas são entre zero e um, dimensionado para as dimensões da página.

Para cada formulário de origem, o ficheiro de etiqueta correspondente deve ter o nome original do ficheiro anexado a `.labels.json` . O ficheiro da etiqueta deve ter o seguinte formato. Consulte o ficheiro da etiqueta da [amostra](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.labels.json) para obter um exemplo completo.

```json
{
    "document": "Invoice_1.pdf",
    "labels": [
        {
            "label": "Provider",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Contoso",
                    "boundingBoxes": [
                        [
                            0.06334117647058823,
                            0.1053,
                            0.17018823529411767,
                            0.1053,
                            0.17018823529411767,
                            0.12303636363636362,
                            0.06334117647058823,
                            0.12303636363636362
                        ]
                    ]
                }
            ]
        },
        {
            "label": "For",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Microsoft",
                    "boundingBoxes": [
                        [
                            0.6122941176470589,
                            0.1374,
                            0.6841764705882353,
                            0.1374,
                            0.6841764705882353,
                            0.14682727272727272,
                            0.6122941176470589,
                            0.14682727272727272
                        ]
                    ]
                },
                {
                    "page": 1,
                    "text": "1020",
                    "boundingBoxes": [
                        [
                            0.6121882352941176,
                            0.156,
                            0.6462941176470588,
                            0.156,
                            0.6462941176470588,
                            0.1653181818181818,
                            0.6121882352941176,
                            0.1653181818181818
                        ]
                    ]
                },
                ...
```

> [!IMPORTANT]
> Só é possível aplicar uma etiqueta a cada elemento de texto e cada etiqueta só pode ser aplicada uma vez por página. Não é possível aplicar uma etiqueta em várias páginas.


## <a name="train-a-model-using-labeled-data"></a>Treine um modelo usando dados rotulados

Para treinar um modelo com dados rotulados, ligue para a API **[do Modelo Personalizado do Comboio,](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** executando o seguinte código python. Antes de executar o código, faça estas alterações:

1. `<Endpoint>`Substitua-o pelo URL do ponto final para o seu recurso 'Reconhecimento de Formulário'.
1. `<SAS URL>`Substitua-a pelo URL de acesso partilhado do recipiente de armazenamento Azure Blob (SAS). Para recuperar o URL SAS, abra o Microsoft Azure Storage Explorer, clique com o botão direito no seu recipiente e selecione **Obter assinatura de acesso partilhado**. Certifique-se de que as permissões **de Leitura** e **Lista** são verificadas e clique em **Criar**. Em seguida, copie o valor na secção **URL.** Deve ter o formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
1. `<Blob folder name>`Substitua-a pelo nome da pasta no seu recipiente blob onde estão os dados de entrada. Ou, se os seus dados estiverem na raiz, deixe este em branco e retire o `"prefix"` campo do corpo do pedido HTTP.

# <a name="v20"></a>[v2.0](#tab/v2-0)
```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.0/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '<subsription key>',
}

body =     {
    "source": source,
    "sourceFilter": {
        "prefix": prefix,
        "includeSubFolders": includeSubFolders
    },
    "useLabelFile": useLabelFile
}

try:
    resp = post(url = post_url, json = body, headers = headers)
    if resp.status_code != 201:
        print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
        quit()
    print("POST model succeeded:\n%s" % resp.headers)
    get_url = resp.headers["location"]
except Exception as e:
    print("POST model failed:\n%s" % str(e))
    quit() 
```    
# <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1)    
```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.1-preview.1/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '<subsription key>',
}

body =     {
    "source": source,
    "sourceFilter": {
        "prefix": prefix,
        "includeSubFolders": includeSubFolders
    },
    "useLabelFile": useLabelFile
}

try:
    resp = post(url = post_url, json = body, headers = headers)
    if resp.status_code != 201:
        print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
        quit()
    print("POST model succeeded:\n%s" % resp.headers)
    get_url = resp.headers["location"]
except Exception as e:
    print("POST model failed:\n%s" % str(e))
    quit() 
```   

---



## <a name="get-training-results"></a>Obtenha resultados de formação

Depois de iniciar a operação do comboio, use a identificação devolvida para obter o estado da operação. Adicione o seguinte código na parte inferior do seu script Python. Isto usa o valor de ID da chamada de formação numa nova chamada da API. A operação de treino é assíncrono, pelo que este script chama a API a intervalos regulares até que o estado do treino esteja concluído. Recomendamos um intervalo de um segundo ou mais.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = headers)
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
            quit()
        model_status = resp_json["modelInfo"]["status"]
        if model_status == "ready":
            print("Training succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if model_status == "invalid":
            print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
            quit()
        # Training still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET model failed:\n%s" % str(e)
        print(msg)
        quit()
print("Train operation did not complete within the allocated time.")
```

Quando o processo de treino estiver concluído, receberá uma `201 (Success)` resposta com conteúdo JSON como o seguinte. A resposta foi encurtada para a simplicidade.

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

Copie o `"modelId"` valor a utilizar nos seguintes passos.

[!INCLUDE [analyze forms](../includes/python-custom-analyze.md)]

Quando o processo estiver concluído, receberá uma `202 (Success)` resposta com o conteúdo JSON no seguinte formato. A resposta foi encurtada para a simplicidade. As principais associações chave/valor estão no `"documentResults"` nó. O `"selectionMarks"` nó (na pré-visualização v2.1) mostra todas as marcas de seleção (caixa de verificação, marca de rádio) e se o seu estado é "selecionado" ou "não selecionado". Os resultados da API do Layout (o conteúdo e as posições de todo o texto no documento) estão no `"readResults"` nó.

# <a name="v20"></a>[v2.0](#tab/v2-0)
```json
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T02:16:28Z",
  "lastUpdatedDateTime": "2020-08-21T02:16:35Z",
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
        ]
      }
    ],
    "documentResults": [
      {
        "docType": "custom:form",
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "Receipt No": {
            "type": "string",
            "valueString": "9876",
            "text": "9876",
            "page": 1,
            "boundingBox": [
              7.615,
              1.245,
              7.915,
              1.245,
              7.915,
              1.35,
              7.615,
              1.35
            ],
            "confidence": 1,
            "elements": [
              "#/readResults/0/lines/3/words/3"
            ]
          },
          ...
        }
      }
    ],
    "errors": []
  }
}
```
# <a name="v21-preview"></a>[pré-visualização v2.1](#tab/v2-1) 
```json   
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T02:29:42Z",
  "lastUpdatedDateTime": "2020-08-21T02:29:50Z",
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
          }
        ] 
      }
    ], 
    "documentResults": [
      {
        "docType": "custom:e1073364-4f3d-4797-8cc4-4bdbcd0dab6b",
        "modelId": "e1073364-4f3d-4797-8cc4-4bdbcd0dab6b",
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "ID #": {
            "type": "string",
            "valueString": "5554443",
            "text": "5554443",
            "page": 1,
            "boundingBox": [
              2.315,
              2.43,
              2.74,
              2.43,
              2.74,
              2.515,
              2.315,
              2.515
            ],
            "confidence": 1,
            "elements": [
              "#/readResults/0/lines/8/words/1"
            ]
          },
          ...
        },
        "docTypeConfidence": 1
      }
    ],
    "errors": []
  }
}
```

---


## <a name="improve-results"></a>Melhorar os resultados

Examine os `"confidence"` valores de cada resultado chave/valor sob o `"documentResults"` nó. Deve também olhar para as pontuações de confiança no `"readResults"` nó, que correspondem à operação Layout. A confiança dos resultados do layout não afeta a confiança dos resultados da extração chave/valor, pelo que deve verificar ambos.
* Se as pontuações de confiança para a operação Layout forem baixas, tente melhorar a qualidade dos seus documentos de entrada (ver [requisitos de entrada).](../overview.md#input-requirements)
* Se as notas de confiança para a operação de extração de chaves/valor forem baixas, certifique-se de que os documentos que estão a ser analisados são do mesmo tipo que os documentos utilizados no conjunto de formação. Se os documentos do conjunto de formação tiverem variações de aparência, considere dividi-los em diferentes pastas e treinar um modelo para cada variação.

### <a name="avoid-cluttered-labels"></a>Evite rótulos desordenados

Por vezes, quando se aplicam etiquetas diferentes dentro da mesma linha de texto, o serviço pode fundir essas etiquetas num único campo. Por exemplo, num endereço, você pode rotular a cidade, estado e código postal como diferentes campos, mas durante a previsão esses campos não são reconhecidos separadamente.

Entendemos que este cenário é essencial para os nossos clientes, e estamos a trabalhar para melhorar isso no futuro. Atualmente, recomendamos que os nossos utilizadores rotulem vários campos desordenados como um campo e, em seguida, separem os termos num pós-processamento dos resultados de extração.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a usar a API de Reconhecimento de Formulários rest com Python para treinar um modelo com dados etiquetados manualmente. Em seguida, consulte a documentação de referência da API para explorar mais aprofundadamente a API do Reconhecimento de Formulários.

> [!div class="nextstepaction"]
> [Documentação de referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
