---
title: 'Início rápido: Extrair dados de recebimento usando o reconhecedor de forma de ondulação'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você usará a API REST do reconhecedor de formulário com ondulação para extrair dados de imagens de recibos de vendas.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: badd674030ec014f2e70050c3c45599a26b17882
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71073818"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-curl"></a>Início rápido: Extrair dados de recebimento usando a API REST do reconhecedor de formulário com ondulação

Neste guia de início rápido, você usará a API REST do reconhecedor do Azure Form com a rotação para extrair e identificar informações relevantes em recibos de vendas.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de início rápido, você deve ter:
- Acesso à visualização de acesso limitado do reconhecedor de formulário. Para obter acesso à visualização, preencha e envie o formulário [solicitação de acesso do reconhecedor de formulário](https://aka.ms/FormRecognizerRequestAccess) .
- [rotação](https://curl.haxx.se/windows/) instalada.
- Uma URL para uma imagem de um recibo. Você pode usar uma [imagem de exemplo](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true) para este guia de início rápido.

## <a name="create-a-form-recognizer-resource"></a>Criar um recurso de reconhecimento de formulário

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Analisar um recibo

Para começar a analisar um recibo, chame a API de **confirmação de análise** usando o comando de ondulação abaixo. Antes de executar o comando, faça estas alterações:

1. Substitua `<Endpoint>` pelo ponto de extremidade obtido com a chave de assinatura do reconhecedor de formulário. Você pode encontrá-lo na guia **visão geral** de recursos do reconhecedor de formulário.
1. Substituir `<your receipt URL>` pelo endereço URL de uma imagem de recebimento.
1. Substitua `<subscription key>` pela chave de assinatura que você copiou da etapa anterior.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/asyncBatchAnalyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"url\": \"<your receipt URL>\"}"
```

Você receberá uma `202 (Success)` resposta que inclui um cabeçalho **Operation-Location** . O valor desse cabeçalho contém uma ID de operação que você pode usar para consultar o status da operação e obter os resultados. No exemplo a seguir, a cadeia de `operations/` caracteres após é a ID da operação.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>Obter os resultados da confirmação

Depois de ter chamado a API de **análise de recebimento** , você chama a API **obter resultado de recebimento** para obter o status da operação e dos dados extraídos.

1. Substitua `<operationId>` pela ID da operação da etapa anterior.
1. Substitua `<subscription key>` pela sua chave de subscrição.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/operations/<operationId>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Examinar a resposta

Você receberá uma `200 (Success)` resposta com a saída JSON. O primeiro campo, `"status"`, indica o status da operação. Se a operação for concluída, o `"recognitionResults"` campo conterá cada linha de texto que foi extraída do recibo e o `"understandingResults"` campo conterá informações de chave/valor para as partes mais relevantes do recibo. Se a operação não for concluída, o valor de `"status"` `"Running"` será ou `"NotStarted"`, e você deverá chamar a API novamente, manualmente ou por meio de um script. É recomendável um intervalo de um segundo ou mais entre as chamadas.

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

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você usou a API REST do reconhecedor de formulário com ondulação para extrair o conteúdo de um recibo de vendas. Em seguida, consulte a documentação de referência para explorar a API do reconhecedor de formulário mais detalhadamente.

> [!div class="nextstepaction"]
> [Documentação de referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)
