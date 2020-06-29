---
title: 'Quickstart: Extrair informações de texto e layout usando Python - Reconhecimento de formulários'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, você usará o Layout de Identificação de FormulárioS REST API com Python para ler texto e dados de tabela a partir dos seus formulários.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.custom: tracking-python
ms.openlocfilehash: 61402a851ddac1cc279c51a59e48bed7949da273
ms.sourcegitcommit: fdaad48994bdb9e35cdd445c31b4bac0dd006294
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85412860"
---
# <a name="quickstart-extract-text-and-layout-information-using-the-form-recognizer-rest-api-with-python"></a>Quickstart: Extrair informações de texto e layout usando a API do Reconhecimento de Formulários REST com Python

Neste arranque rápido, você usará o API do Reconhecimento de FormulárioS Azure com Python para extrair informações de layout de texto e dados de tabelas a partir de documentos de formulário.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, você deve ter:
- [Python](https://www.python.org/downloads/) instalado (se quiser executar a amostra localmente).
- Um documento de formulário. Pode descarregar uma imagem a partir do conjunto de [dados](https://go.microsoft.com/fwlink/?linkid=2090451) da amostra para este arranque rápido.

> [!NOTE]
> Este quickstart utiliza um documento armazenado localmente. Para utilizar a utilização de ficheiros remotos acedidos por URL, consulte a [documentação de referência](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync).


## <a name="create-a-form-recognizer-resource"></a>Criar um recurso de reconhecimento de formulários

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-the-form-layout"></a>Analise o layout do formulário

Para começar a analisar o layout, você chama a **[API de Layout de Análise](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-previewoperations/AnalyzeLayoutAsync)** usando o script Python abaixo. Antes de executar o script, faça estas alterações:

1. `<Endpoint>`Substitua-o pelo ponto final que obteve com a subscrição do Form Recogniser.
1. `<path to your form>`Substitua-o pelo caminho do documento de formulário local.
1. `<subscription key>`Substitua-a pela chave de subscrição que copiou do passo anterior.

    ```python
    ########### Python Form Recognizer Async Layout #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<Subscription Key>"
    post_url = endpoint + "/formrecognizer/v2.0/Layout/analyze"
    source = r"<path to your form>"
    
    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. Guarde o código num ficheiro com uma extensão .py. Por exemplo, *form-recognizer-layout.py.*
1. Abra uma janela da linha de comandos.
1. Na linha de comandos, utilize o comando `python` para executar o exemplo. Por exemplo, `python form-recognizer-layout.py`.

Receberá uma `202 (Success)` resposta que inclui um **cabeçalho operação-localização,** que o script irá imprimir na consola. Este cabeçalho contém um ID de funcionamento que pode utilizar para consultar o estado da operação assíncronea e obter os resultados. No seguinte valor de exemplo, a cadeia seguinte `operations/` é o ID de funcionamento.

```console
https://cognitiveservice/formrecognizer/v2.0/layout/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-layout-results"></a>Obtenha os resultados do layout

Depois de ter chamado a API do **Layout de Análise,** ligue para a API **[do Resultado do Layout de Análise](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeLayoutResult)** para obter o estado da operação e os dados extraídos. Adicione o seguinte código na parte inferior do seu script Python. Este código utiliza o valor de ID de operação numa nova chamada API. Este script chama a API em intervalos regulares até que os resultados estejam disponíveis. Recomendamos um intervalo de um segundo ou mais.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Layout results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Layout Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Layout Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. Guarde o guião.
1. Volte a usar o `python` comando para executar a amostra. Por exemplo, `python form-recognizer-layout.py`.

### <a name="examine-the-response"></a>Examinar a resposta

O script imprimirá respostas à consola até que a operação **'Layout' de análise** esteja concluída. Em seguida, imprimirá os dados extraídos no formato JSON. O `"readResults"` nó contém todas as linhas de texto com a respetiva colocação da caixa de limitação na página. O `"pageResults"` campo mostra cada pedaço de texto dentro das tabelas, cada um com a sua coordenada de coluna de linha.

Consulte a seguinte imagem de fatura e a respetiva saída JSON. A saída foi encurtada para a simplicidade.

> [!div class="mx-imgBorder"]
> ![Documento de fatura contoso com uma tabela](../media/contoso-invoice.png)

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-11-12T19:55:36Z",
  "lastUpdatedDateTime":"2019-11-12T19:55:43Z",
  "analyzeResult":{ 
    "version":"2.0.0",
    "readResults":[ 
      { 
        "page":1,
        "language":"en",
        "angle":0,
        "width":8.5,
        "height":11,
        "unit":"inch",
        "lines":[ 
          { 
            "language":"en",
            "boundingBox":[ 
              0.5384,
              1.1583,
              1.4466,
              1.1583,
              1.4466,
              1.3534,
              0.5384,
              1.3534
            ],
            "text":"Contoso",
            "words":[ 
              { 
                "boundingBox":[ 
                  0.5384,
                  1.1583,
                  1.4466,
                  1.1583,
                  1.4466,
                  1.3534,
                  0.5384,
                  1.3534
                ],
                "text":"Contoso",
                "confidence":1
              }
            ]
          },
          { 
            "language":"en",
            "boundingBox":[ 
              0.7994,
              1.5143,
              1.3836,
              1.5143,
              1.3836,
              1.6154,
              0.7994,
              1.6154
            ],
            "text":"Address:",
            "words":[ 
              { 
                "boundingBox":[ 
                  0.7994,
                  1.5143,
                  1.3836,
                  1.5143,
                  1.3836,
                  1.6154,
                  0.7994,
                  1.6154
                ],
                "text":"Address:",
                "confidence":1
              }
            ]
          },
          ...
          { 
            "language":"en",
            "boundingBox":[ 
              6.2285,
              3.4114,
              6.3919,
              3.4114,
              6.3919,
              3.5119,
              6.2285,
              3.5119
            ],
            "text":"PT",
            "words":[ 
              { 
                "boundingBox":[ 
                  6.2285,
                  3.4114,
                  6.3919,
                  3.4114,
                  6.3919,
                  3.5119,
                  6.2285,
                  3.5119
                ],
                "text":"PT",
                "confidence":1
              }
            ]
          }
        ]
      }
    ],
    "pageResults":[ 
      { 
        "page":1,
        "tables":[ 
          { 
            "rows":2,
            "columns":6,
            "cells":[ 
              { 
                "rowIndex":0,
                "columnIndex":0,
                "text":"Invoice Number",
                "boundingBox":[ 
                  0.5075,
                  2.8088,
                  1.9061,
                  2.8088,
                  1.9061,
                  3.3219,
                  0.5075,
                  3.3219
                ],
                "elements":[ 
                  "#/readResults/0/lines/8/words/0",
                  "#/readResults/0/lines/8/words/1"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  }
}
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, você usou o Formulário Reconhecedor REST API com Python para extrair o layout de texto de uma fatura. Em seguida, consulte a documentação de referência para explorar mais aprofundadamente a API do Reconhecimento de Formulários.

> [!div class="nextstepaction"]
> [Documentação de referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-previewoperations/AnalyzeLayoutAsync)
