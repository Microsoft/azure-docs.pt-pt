---
title: 'Início rápido: extrair informações de texto e layout usando o reconhecedor de formulário Python'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você usará a API REST de layout do reconhecedor de formulário com Python para ler dados de texto e tabela de seus formulários.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/13/2019
ms.author: pafarley
ms.openlocfilehash: ea716caf575fce1197c12f3cb3cea8348c7a04c9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473949"
---
# <a name="quickstart-extract-text-and-layout-information-using-the-form-recognizer-rest-api-with-python"></a>Início rápido: extrair informações de texto e layout usando a API REST do reconhecedor de formulário com Python

Neste guia de início rápido, você usará a API REST do reconhecedor do Azure Form com Python para extrair informações de layout de texto e dados de tabela de documentos de formulário.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido, você deve ter:
- Acesso à visualização de acesso limitado do reconhecedor de formulário. Para obter acesso à visualização, preencha e envie o formulário [solicitação de acesso do reconhecedor de formulário](https://aka.ms/FormRecognizerRequestAccess) .
- [Python](https://www.python.org/downloads/) instalado (se você quiser executar o exemplo localmente).
- Um documento de formulário. Você pode baixar uma imagem do [conjunto de dados de exemplo](https://go.microsoft.com/fwlink/?linkid=2090451) para este guia de início rápido.

## <a name="create-a-form-recognizer-resource"></a>Criar um recurso de reconhecimento de formulário

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-the-form-layout"></a>Analisar o layout do formulário

Para começar a analisar o layout, você chama a API de **[layout de análise](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)** usando o script Python abaixo. Antes de executar o script, faça estas alterações:

1. Substitua `<Endpoint>` pelo ponto de extremidade obtido com a assinatura do reconhecedor de formulário.
1. Substitua `<path to your form>` pelo caminho para o documento de formulário local.
1. Substitua `<subscription key>` pela chave de assinatura que você copiou da etapa anterior.

    ```python
    ########### Python Form Recognizer Async Layout #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<Subscription Key>"
    post_url = endpoint + "/formrecognizer/v2.0-preview/Layout/analyze"
    source = r"<path to your form>"
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
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

1. Salve o código em um arquivo com uma extensão. py. Por exemplo, *Form-Recognizer-layout.py*.
1. Abra uma janela da linha de comandos.
1. Na linha de comandos, utilize o comando `python` para executar o exemplo. Por exemplo, `python form-recognizer-layout.py`.

Você receberá uma resposta `202 (Success)` que inclui um cabeçalho **Operation-Location** , que o script imprimirá no console. Esse cabeçalho contém uma ID de operação que você pode usar para consultar o status da operação assíncrona e obter os resultados. No valor de exemplo a seguir, a cadeia de caracteres após `operations/` é a ID da operação.

```console
https://cognitiveservice/formrecognizer/v2.0-preview/layout/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-layout-results"></a>Obter os resultados do layout

Depois de ter chamado a API de **análise de layout** , você chama a API **[Get Analyze layout Result](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeLayoutResult)** para obter o status da operação e dos dados extraídos. Adicione o código a seguir à parte inferior do seu script Python. Isso usa o valor da ID da operação em uma nova chamada à API. Esse script chama a API em intervalos regulares até que os resultados estejam disponíveis. É recomendável um intervalo de um segundo ou mais.

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

1. Salve o script.
1. Novamente, use o comando `python` para executar o exemplo. Por exemplo, `python form-recognizer-layout.py`.

### <a name="examine-the-response"></a>Examinar a resposta

O script imprimirá as respostas para o console até que a operação **analisar layout** seja concluída. Em seguida, ele imprimirá os dados extraídos no formato JSON. O nó `"readResults"` contém cada linha de texto com seu respectivo posicionamento de caixa delimitadora na página. O campo `"pageResults"` mostra todas as partes do texto nas tabelas, cada uma com sua coordenada de coluna de linha.

Consulte a imagem de nota fiscal a seguir e sua saída JSON correspondente. A saída foi reduzida para simplificar a simplicidade.

> [!div class="mx-imgBorder"]
> ![documento de fatura da contoso com uma tabela](../media/contoso-invoice.png)

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

Neste guia de início rápido, você usou a API REST do reconhecedor de formulário com Python para extrair o layout de texto de uma fatura. Em seguida, consulte a documentação de referência para explorar a API do reconhecedor de formulário mais detalhadamente.

> [!div class="nextstepaction"]
> [Documentação de referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)
