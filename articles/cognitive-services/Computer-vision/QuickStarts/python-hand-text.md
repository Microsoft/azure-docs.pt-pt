---
title: 'Quickstart: Visão Computacional 2.1 e 3.1 - Extrato de texto impresso e manuscrito - REST, Python'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, extrai texto impresso e manuscrito de uma imagem utilizando a API de Visão De Computador com Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: 7ab61c788a6213b092e74d596b522ac4bb0412ac
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541900"
---
# <a name="quickstart-extract-printed-and-handwritten-text-using-the-computer-vision-rest-api-and-python"></a>Quickstart: Extrair texto impresso e manuscrito utilizando a API e a Python da Visão de Computador

Neste arranque rápido, extrairá texto impresso e manuscrito de uma imagem utilizando a API de Visão de Computador. Com os métodos [Ler](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) e [Obter Resultados,](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750) pode detetar texto numa imagem e extrair caracteres reconhecidos num fluxo de caracteres legíveis por máquina. 

> [!IMPORTANT]
> O método [ler](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) funciona assíncroticamente. Este método não devolve quaisquer informações no corpo de uma resposta de êxito. Em vez disso, o método De Leitura de Lote devolve um URI no valor do campo do cabeçalho de `Operation-Location` resposta. Em seguida, pode chamar este URI, que representa a API [do Resultado de Leitura,](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750) para verificar o estado e devolver os resultados da chamada do método Ler.

---

Pode executar este início rápido passo a passo com um bloco de notas do Jupyter no [MyBinder](https://mybinder.org). Para iniciar o Binder, selecione o botão seguinte:

[![O botão de lançamento Do Encaixe](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure - [Crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* [Python](https://www.python.org/downloads/)
* Assim que tiver a subscrição do Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" crie um recurso de Visão de Computador crie um recurso de "  target="_blank"> Visão De Computador no portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Azure para obter a sua chave e ponto final. Depois de implementar, clique em **Ir para o recurso**.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação ao serviço de Visão De Computador. Colará a chave e o ponto final no código abaixo mais tarde no arranque rápido.
    * Pode utilizar o nível de preços gratuitos `F0` para experimentar o serviço e fazer upgrade mais tarde para um nível pago para produção.
* [Crie variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para o URL chave e ponto final, nomeado `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` e, respectivamente.

## <a name="create-and-run-the-sample"></a>Criar e executar o exemplo

Para criar e executar o exemplo, siga os seguintes passos:

1. Copie o código seguinte para um editor de texto.
1. Opcionalmente, substitua o valor de `image_url` uma imagem diferente da qual pretende extrair texto.
1. Guarde o código como um ficheiro com a extensão `.py`. Por exemplo, `get-text.py`.
1. Abra uma janela da linha de comandos.
1. Na linha de comandos, utilize o comando `python` para executar o exemplo. Por exemplo, `python get-text.py`.

```python
import json
import os
import sys
import requests
import time
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from matplotlib.patches import Polygon
from PIL import Image
from io import BytesIO

missing_env = False
# Add your Computer Vision subscription key and endpoint to your environment variables.
if 'COMPUTER_VISION_ENDPOINT' in os.environ:
    endpoint = os.environ['COMPUTER_VISION_ENDPOINT']
else:
    print("From Azure Cognitive Service, retrieve your endpoint and subscription key.")
    print("\nSet the COMPUTER_VISION_ENDPOINT environment variable, such as \"https://westus2.api.cognitive.microsoft.com\".\n")
    missing_env = True

if 'COMPUTER_VISION_SUBSCRIPTION_KEY' in os.environ:
    subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
else:
    print("From Azure Cognitive Service, retrieve your endpoint and subscription key.")
    print("\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable, such as \"1234567890abcdef1234567890abcdef\".\n")
    missing_env = True

if missing_env:
    print("**Restart your shell or IDE for changes to take effect.**")
    sys.exit()

text_recognition_url = endpoint + "/vision/v3.1/read/analyze"

# Set image_url to the URL of an image that you want to recognize.
image_url = "https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/cognitive-services/Computer-vision/Images/readsample.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
data = {'url': image_url}
response = requests.post(
    text_recognition_url, headers=headers, json=data)
response.raise_for_status()

# Extracting text requires two API calls: One call to submit the
# image for processing, the other to retrieve the text found in the image.

# Holds the URI used to retrieve the recognized text.
operation_url = response.headers["Operation-Location"]

# The recognized text isn't immediately available, so poll to wait for completion.
analysis = {}
poll = True
while (poll):
    response_final = requests.get(
        response.headers["Operation-Location"], headers=headers)
    analysis = response_final.json()
    
    print(json.dumps(analysis, indent=4))

    time.sleep(1)
    if ("analyzeResult" in analysis):
        poll = False
    if ("status" in analysis and analysis['status'] == 'failed'):
        poll = False

polygons = []
if ("analyzeResult" in analysis):
    # Extract the recognized text, with bounding boxes.
    polygons = [(line["boundingBox"], line["text"])
                for line in analysis["analyzeResult"]["readResults"][0]["lines"]]

# Display the image and overlay it with the extracted text.
image = Image.open(BytesIO(requests.get(image_url).content))
ax = plt.imshow(image)
for polygon in polygons:
    vertices = [(polygon[0][i], polygon[0][i+1])
                for i in range(0, len(polygon[0]), 2)]
    text = polygon[1]
    patch = Polygon(vertices, closed=True, fill=False, linewidth=2, color='y')
    ax.axes.add_patch(patch)
    plt.text(vertices[0][0], vertices[0][1], text, fontsize=20, va="top")
plt.show()
```

## <a name="examine-the-response"></a>Examinar a resposta

O JSON devolve uma resposta de êxito. A página Web de exemplo analisa e apresenta uma resposta de êxito na janela da linha de comandos, semelhante ao seguinte exemplo:


```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              },
              {
                "boundingBox": [
                  540,
                  665,
                  926,
                  679,
                  926,
                  825,
                  541,
                  823
                ],
                "text": "quick",
                "confidence": 0.57
              },
              {
                "boundingBox": [
                  1125,
                  686,
                  1569,
                  700,
                  1569,
                  838,
                  1125,
                  828
                ],
                "text": "brown",
                "confidence": 0.799
              },
              {
                "boundingBox": [
                  1674,
                  703,
                  1966,
                  711,
                  1966,
                  851,
                  1674,
                  841
                ],
                "text": "fox",
                "confidence": 0.442
              },
              {
                "boundingBox": [
                  2083,
                  714,
                  2580,
                  725,
                  2579,
                  876,
                  2083,
                  855
                ],
                "text": "jumps",
                "confidence": 0.878
              }
            ]
          },
          {
            "boundingBox": [
              187,
              1062,
              485,
              1056,
              486,
              1120,
              189,
              1126
            ],
            "text": "over",
            "words": [
              {
                "boundingBox": [
                  190,
                  1064,
                  439,
                  1059,
                  441,
                  1122,
                  192,
                  1126
                ],
                "text": "over",
                "confidence": 0.37
              }
            ]
          },
          {
            "boundingBox": [
              664,
              1008,
              1973,
              1023,
              1969,
              1178,
              664,
              1154
            ],
            "text": "the lazy dog!",
            "words": [
              {
                "boundingBox": [
                  668,
                  1008,
                  923,
                  1015,
                  923,
                  1146,
                  669,
                  1117
                ],
                "text": "the",
                "confidence": 0.909
              },
              {
                "boundingBox": [
                  1107,
                  1018,
                  1447,
                  1023,
                  1445,
                  1178,
                  1107,
                  1162
                ],
                "text": "lazy",
                "confidence": 0.853
              },
              {
                "boundingBox": [
                  1639,
                  1024,
                  1974,
                  1023,
                  1971,
                  1170,
                  1636,
                  1178
                ],
                "text": "dog!",
                "confidence": 0.41
              }
            ]
          }
        ]
      }
    ]
  }
}
```


## <a name="next-steps"></a>Passos seguintes

Em seguida, explore uma aplicação Python que usa a Visão Computacional para realizar o reconhecimento de caracteres óticos (OCR); criar miniaturas cortadas inteligentemente; e detetar, categorizar, etiquetar e descrever características visuais em imagens.

> [!div class="nextstepaction"]
> [Tutorial do Python de API de Imagem Digitalizada](../Tutorials/PythonTutorial.md)

* Para experimentar rapidamente a API de Imagem Digitalizada, experimente a [Consola de teste de API aberta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b/console).
