---
title: 'Início rápido: Extrair texto impresso-REST, ondulação'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, irá extrair texto impresso de uma imagem através da API de Imagem Digitalizada com o cURL.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 2f4b201a425075d5be55d831fd23bb1133f70256
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176869"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-computer-vision-rest-api-and-curl"></a>Início rápido: Extrair texto impresso (OCR) usando a API REST Pesquisa Visual Computacional e a ondulação

Neste guia de início rápido, irá extrair texto impresso de uma imagem com o reconhecimento ótico de carateres (OCR) através da API REST de Imagem Digitalizada. Com o método [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc), pode detetar texto impresso numa imagem e extrair os carateres reconhecidos para um fluxo de carateres que pode ser utilizado por um computador.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter o [cURL](https://curl.haxx.se/windows).
- Tem de ter uma chave de subscrição da Imagem Digitalizada. Você pode obter uma chave de avaliação gratuita de [experimentar serviços cognitivas](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Ou siga as instruções em [criar uma conta de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar pesquisa Visual computacional e obter sua chave.

## <a name="create-and-run-the-sample-command"></a>Criar e executar o comando de exemplo

Para criar e executar o exemplo, siga os seguintes passos:

1. Copie o comando seguinte para um editor de texto.
1. Faça as alterações seguintes ao comando, se for necessário:
    1. Substitua o valor de `<subscriptionKey>` pela chave de subscrição.
    1. Substitua a primeira parte da URL da solicitação (`westcentralus`) pelo texto em sua própria URL de ponto de extremidade.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Opcionalmente, altere o URL da imagem no corpo do pedido (`https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\`) pelo URL de uma imagem diferente a ser analisada.
1. Abra uma janela da linha de comandos.
1. Cole o comando a partir do editor de texto na janela da linha de comandos e, em seguida, execute o comando.

```bash
curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.1/ocr?language=unk&detectOrientation=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\"}"
```

## <a name="examine-the-response"></a>Examinar a resposta

O JSON devolve uma resposta de êxito. A aplicação de exemplo analisa e apresenta uma resposta de êxito na janela da linha de comandos, semelhante ao seguinte exemplo:

```json
{
  "language": "en",
  "orientation": "Up",
  "textAngle": 0,
  "regions": [
    {
      "boundingBox": "21,16,304,451",
      "lines": [
        {
          "boundingBox": "28,16,288,41",
          "words": [
            {
              "boundingBox": "28,16,288,41",
              "text": "NOTHING"
            }
          ]
        },
        {
          "boundingBox": "27,66,283,52",
          "words": [
            {
              "boundingBox": "27,66,283,52",
              "text": "EXISTS"
            }
          ]
        },
        {
          "boundingBox": "27,128,292,49",
          "words": [
            {
              "boundingBox": "27,128,292,49",
              "text": "EXCEPT"
            }
          ]
        },
        {
          "boundingBox": "24,188,292,54",
          "words": [
            {
              "boundingBox": "24,188,292,54",
              "text": "ATOMS"
            }
          ]
        },
        {
          "boundingBox": "22,253,297,32",
          "words": [
            {
              "boundingBox": "22,253,105,32",
              "text": "AND"
            },
            {
              "boundingBox": "144,253,175,32",
              "text": "EMPTY"
            }
          ]
        },
        {
          "boundingBox": "21,298,304,60",
          "words": [
            {
              "boundingBox": "21,298,304,60",
              "text": "SPACE."
            }
          ]
        },
        {
          "boundingBox": "26,387,294,37",
          "words": [
            {
              "boundingBox": "26,387,210,37",
              "text": "Everything"
            },
            {
              "boundingBox": "249,389,71,27",
              "text": "else"
            }
          ]
        },
        {
          "boundingBox": "127,431,198,36",
          "words": [
            {
              "boundingBox": "127,431,31,29",
              "text": "is"
            },
            {
              "boundingBox": "172,431,153,36",
              "text": "opinion."
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Passos seguintes

Explore a API de Imagem Digitalizada utilizada para analisar uma imagem, detetar celebridades e marcos, criar uma miniatura e extrair texto impresso e manuscrito. Para experimentar rapidamente a API de Imagem Digitalizada, experimente a [Consola de teste de API aberta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Explorar a API de Imagem Digitalizada](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
