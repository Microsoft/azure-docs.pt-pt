---
title: 'Início rápido: Extrair dados de recebimento com o cURL - reconhecedor de formulário'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, usará a API de REST do reconhecedor de formulário com o cURL para extrair dados de imagens de recibos de vendas.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: c232469886941a2db1ae57437c4b72d7565c123c
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503449"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-curl"></a>Início rápido: Extrair dados de recebimento com a API de REST do reconhecedor de formulário com cURL

Neste início rápido, usará a API de REST do reconhecedor de formulário do Azure com o cURL para extrair e identificar informações relevantes no recibos de vendas.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este início rápido, tem de ter:
- Aceder à pré-visualização de acesso limitado do reconhecedor de formulário. Para obter acesso à pré-visualização, preencha e envie os [pedido de acesso do reconhecedor de formulário](https://aka.ms/FormRecognizerRequestAccess) formulário.
- [cURL](https://curl.haxx.se/windows/) instalado.
- Um URL para uma imagem de recibo. Pode utilizar um [imagem de exemplo](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true) para este início rápido.

## <a name="create-a-form-recognizer-resource"></a>Criar um recurso do reconhecedor de formulário

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Analisar um recibo de

Para começar a analisar um recibo, chame o **analisar recibo** API com o comando cURL abaixo. Antes de executar o comando, efetue estas alterações:

1. Substitua `<Endpoint>` com o ponto final que obteve na sua chave de assinatura do reconhecedor de formulário. Pode encontrá-lo no seu recurso do reconhecedor de formulário **descrição geral** separador.
1. Substitua `<your receipt URL>` com o endereço de URL de uma imagem de receção.
1. Substitua `<subscription key>` com a chave de subscrição que copiou no passo anterior.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/asyncBatchAnalyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"url\": \"<your receipt URL>\"}"
```

Receberá um `202 (Success)` resposta que inclui um **operação-Location** cabeçalho. O valor deste cabeçalho contém um ID de operação que pode utilizar para consultar o estado da operação e obter os resultados. No exemplo seguinte, a cadeia de caracteres depois `operations/` é o ID de operação.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>Obter os resultados de receção

Após ter chamado a **analisar recibo** API, chama o **obter Resultado da receção** API para obter o estado da operação e os dados extraídos.

1. Substitua `<operationId>` com o ID de operação do passo anterior.
1. Substitua `<subscription key>` pela sua chave de subscrição.

```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/operations/<operationId>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Examinar a resposta

Receberá um `200 (Success)` resposta com a saída do JSON. O primeiro campo `"status"`, indica o estado da operação. Se a operação estiver concluída, o `"recognitionResults"` campo contém todas as linhas de texto que foram extraída do recebimento, e o `"understandingResults"` campo contém informações de chave/valor para as partes mais relevantes de receção. Se a operação não estiver concluída, o valor de `"status"` serão `"Running"` ou `"NotStarted"`, e deve chamar a API mais uma vez, manualmente ou através de um script. Recomendamos um intervalo de um segundo ou mais entre as chamadas.

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

Neste início rápido, utilizou a API de REST do reconhecedor de formulário com o cURL para extrair o conteúdo de um recibo de vendas. Em seguida, consulte a documentação de referência para explorar a API do reconhecedor de forma mais detalhadamente.

> [!div class="nextstepaction"]
> [Documentação de referência da REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)
