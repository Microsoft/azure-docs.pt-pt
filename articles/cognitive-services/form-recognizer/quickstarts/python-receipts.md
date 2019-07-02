---
title: 'Início rápido: Extrair dados de recebimento com Python - reconhecedor de formulário'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, vai utilizar a API de REST do reconhecedor de formulário com o Python para extrair dados de imagens de recibos de vendas.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: da699c9e5ca933c182c83a33dd0c09b49a0726c3
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503442"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-python"></a>Início rápido: Extrair dados de recebimento com a API de REST do reconhecedor de formulário com Python

Neste início rápido, vai utilizar a API de REST do reconhecedor de formulário do Azure com o Python para extrair e identificar informações relevantes no recibos de vendas.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este início rápido, tem de ter:
- Aceder à pré-visualização de acesso limitado do reconhecedor de formulário. Para obter acesso à pré-visualização, preencha e envie os [pedido de acesso do reconhecedor de formulário](https://aka.ms/FormRecognizerRequestAccess) formulário.
- [Python](https://www.python.org/downloads/) instalado (se quiser executar o exemplo localmente).
- Um URL para uma imagem de recibo. Pode utilizar um [imagem de exemplo](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true) para este início rápido.

## <a name="create-a-form-recognizer-resource"></a>Criar um recurso do reconhecedor de formulário

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Analisar um recibo de

Para começar a analisar um recibo, chame o **analisar recibo** API utilizando o script de Python abaixo. Antes de executar o script, efetue estas alterações:

1. Substitua `<Endpoint>` com o ponto final que obteve na sua chave de assinatura do reconhecedor de formulário. Pode encontrá-lo no seu recurso do reconhecedor de formulário **descrição geral** separador.
1. Substitua `<your receipt URL>` com o endereço de URL de uma imagem de receção.
1. Substitua `<subscription key>` com a chave de subscrição que copiou no passo anterior.

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

1. Guarde o código num arquivo com uma extensão. PY. Por exemplo, *formulário-reconhecedor-receipts.py*.
1. Abra uma janela da linha de comandos.
1. Na linha de comandos, utilize o comando `python` para executar o exemplo. Por exemplo, `python form-recognizer-receipts.py`.

Receberá um `202 (Success)` resposta que inclui um **operação-Location** cabeçalho, que o script será impresso no Console. Este cabeçalho contém um ID de operação que pode utilizar para consultar o estado da operação e obter os resultados da análise. O valor de exemplo seguinte, a cadeia de caracteres depois de `operations/` é o ID de operação.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>Obter os resultados de receção

Após ter chamado a **analisar recibo** API, chama o **obter Resultado da receção** API para obter o estado da operação e os dados extraídos. Adicione o seguinte código para a parte inferior do seu script de Python. Isso extrai o valor de ID de operação e passa-o para uma nova chamada de API. A operação é assíncrona, para que este script chama a API em intervalos regulares, até que os resultados estejam disponíveis. Recomendamos um intervalo de um segundo ou mais.

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
1. Utilizar novamente o `python` comando para executar o exemplo. Por exemplo, `python form-recognize-analyze.py`.

### <a name="examine-the-response"></a>Examinar a resposta

O script imprimirá respostas na consola até que a operação de análise seja concluída. Em seguida, ele imprimirá os dados de texto extraído no formato JSON. O `"recognitionResults"` campo contém todas as linhas de texto que foram extraída do recebimento, e o `"understandingResults"` campo contém informações de chave/valor para as partes mais relevantes de receção.

Veja que a imagem seguinte de receção e o JSON correspondente de saída. A saída tem foram abreviada para facilitar a leitura.

![Um recibo da loja de Contoso](../media/contoso-receipt.png)

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

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, utilizou a API de REST do reconhecedor de formulário com o Python para preparar um modelo e executá-lo num cenário de exemplo. Em seguida, consulte a documentação de referência para explorar a API do reconhecedor de forma mais detalhadamente.

> [!div class="nextstepaction"]
> [Documentação de referência da REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)
