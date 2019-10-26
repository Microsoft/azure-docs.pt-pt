---
title: 'Início rápido: extrair dados de recebimento usando o reconhecedor de formulário Python'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você usará a API REST do reconhecedor de formulário com Python para extrair dados de imagens de recibos de vendas.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: ef5c9e8d548e8acbcbdbe83f6e7c9965c798ad44
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931271"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-python"></a>Início rápido: extrair dados de recebimento usando a API REST do reconhecedor de formulário com Python

Neste guia de início rápido, você usará a API REST do reconhecedor do Azure Form com Python para extrair e identificar informações relevantes em recibos de vendas.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de início rápido, você deve ter:
- Acesso à visualização de acesso limitado do reconhecedor de formulário. Para obter acesso à visualização, preencha e envie o formulário [solicitação de acesso do reconhecedor de formulário](https://aka.ms/FormRecognizerRequestAccess) .
- [Python](https://www.python.org/downloads/) instalado (se você quiser executar o exemplo localmente).
- Uma URL para uma imagem de um recibo. Você pode usar uma [imagem de exemplo](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true) para este guia de início rápido.

## <a name="create-a-form-recognizer-resource"></a>Criar um recurso de reconhecimento de formulário

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Analisar um recibo

Para começar a analisar um recibo, você chama a API de **recebimento de análise** usando o script Python abaixo. Antes de executar o script, faça estas alterações:

1. Substitua `<Endpoint>` pelo ponto de extremidade obtido com a assinatura do reconhecedor de formulário.
1. Substitua `<your receipt URL>` pelo endereço URL de uma imagem de recebimento.
1. Substitua `<subscription key>` pela chave de assinatura que você copiou da etapa anterior.

    ```python
    import http.client, urllib.request, urllib.parse, urllib.error, base64

    source = r"<your receipt URL>"
    body = {"url":source}
    body = json.dumps(body)

    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': '<subscription key>',
    }

    try:
        conn = http.client.HTTPSConnection('<Endpoint>')
        conn.request("POST", "/formrecognizer/v1.0-preview/prebuilt/receipt/asyncBatchAnalyze", body, headers)
        response = conn.getresponse()
        data = response.read()
        operationURL = "" + response.getheader("Operation-Location")
        print ("Operation-Location header: " + operationURL)
        conn.close()
    except Exception as e:
        print(e)
        exit()
    ```

1. Salve o código em um arquivo com uma extensão. py. Por exemplo, *Form-Recognizer-Receipts.py*.
1. Abra uma janela da linha de comandos.
1. Na linha de comandos, utilize o comando `python` para executar o exemplo. Por exemplo, `python form-recognizer-receipts.py`.

Você receberá uma resposta `202 (Success)` que inclui um cabeçalho **Operation-Location** , que o script imprimirá no console. Esse cabeçalho contém uma ID de operação que você pode usar para consultar o status da operação e obter os resultados da análise. No valor de exemplo a seguir, a cadeia de caracteres após `operations/` é a ID da operação.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>Obter os resultados da confirmação

Depois de ter chamado a API de **análise de recebimento** , você chama a API **obter resultado de recebimento** para obter o status da operação e dos dados extraídos. Adicione o código a seguir à parte inferior do seu script Python. Isso extrai o valor da ID da operação e o transmite para uma nova chamada à API. A operação é assíncrona, portanto, esse script chama a API em intervalos regulares até que os resultados estejam disponíveis. É recomendável um intervalo de um segundo ou mais.

```python
operationId = operationURL.split("operations/")[1]

conn = http.client.HTTPSConnection('<Endpoint>')
while True:
    try:
        conn.request("GET", f"/formrecognizer/v1.0-preview/prebuilt/receipt/operations/{operationId}", "", headers)
        responseString = conn.getresponse().read().decode('utf-8')
        responseDict = json.loads(responseString)
        conn.close()
        print(responseString)
        if 'status' in responseDict and responseDict['status'] not in ['NotStarted','Running']:
            break
        time.sleep(1)
    except Exception as e:
        print(e)
        exit()
```

1. Salve o script.
1. Novamente, use o comando `python` para executar o exemplo. Por exemplo, `python form-recognize-analyze.py`.

### <a name="examine-the-response"></a>Examinar a resposta

O script imprimirá as respostas para o console até que a operação de análise seja concluída. Em seguida, ele imprimirá os dados de texto extraídos no formato JSON. O campo `"recognitionResults"` contém todas as linhas de texto que foram extraídas do recibo e o campo `"understandingResults"` contém informações de chave/valor para as partes mais relevantes do recibo.

Consulte a seguinte imagem de recebimento e sua saída JSON correspondente. A saída foi reduzida para facilitar a leitura.

![Um recebimento da loja contoso](../media/contoso-receipt.png)

```json
{
  "status": "Succeeded",
  "recognitionResults": [{
    "page": 1,
    "clockwiseOrientation": 0.36,
    "width": 1688,
    "height": 3000,
    "unit": "pixel",
    "lines": [{
      "boundingBox": [616, 291, 1050, 278, 1053, 384, 620, 397],
      "text": "Contoso",
      "words": [{
        "boundingBox": [619, 292, 1051, 284, 1052, 382, 620, 398],
        "text": "Contoso"
      }]
    }, {
      "boundingBox": [322, 588, 501, 600, 497, 655, 318, 643],
      "text": "Contoso",
      "words": [{
        "boundingBox": [330, 590, 501, 602, 499, 654, 326, 644],
        "text": "Contoso"
      }]
    },
    ...
    ]
  }],
  "understandingResults": [{
    "pages": [1],
    "fields": {
      "Subtotal": {
        "valueType": "numberValue",
        "value": 1098.99,
        "text": "1098.99",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/14/words/1"
        }]
      },
      "Total": {
        "valueType": "numberValue",
        "value": 1203.39,
        "text": "1203.39",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/18/words/1"
        }]
      },
      "Tax": {
        "valueType": "numberValue",
        "value": 104.4,
        "text": "$104.40",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/16/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/16/words/1"
        }]
      },
      "MerchantAddress": {
        "valueType": "stringValue",
        "value": "123 Main Street Redmond, WA 98052",
        "text": "123 Main Street Redmond, WA 98052",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/2/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/2"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/2"
        }]
      },
      "MerchantName": {
        "valueType": "stringValue",
        "value": "Contoso",
        "text": "Contoso",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/1/words/0"
        }]
      },
      "MerchantPhoneNumber": {
        "valueType": "stringValue",
        "value": null,
        "text": "123-456-7890",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/4/words/0"
        }]
      },
      "TransactionDate": {
        "valueType": "stringValue",
        "value": "2019-06-10",
        "text": "6/10/2019",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/0"
        }]
      },
      "TransactionTime": {
        "valueType": "stringValue",
        "value": "13:59:00",
        "text": "13:59",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/1"
        }]
      }
    }
  }]
}
```

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você usou a API REST do reconhecedor de formulário com Python para treinar um modelo e executá-lo em um cenário de exemplo. Em seguida, consulte a documentação de referência para explorar a API do reconhecedor de formulário mais detalhadamente.

> [!div class="nextstepaction"]
> [Documentação de referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)
