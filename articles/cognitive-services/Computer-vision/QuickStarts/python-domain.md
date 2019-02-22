---
title: 'Início rápido: Conteúdo da imagem de específicas de domínio - REST, Python'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, vai utilizar modelos de domínio para identificar celebridades e marcos numa imagem através da API de Imagem Digitalizada com o Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: bdbff3777949298fbefec80ecb6608d6af10037b
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56651656"
---
# <a name="quickstart-use-a-domain-model-using-the-rest-api-and-python-in-computer-vision"></a>Início rápido: Utilizar um modelo de domínio usando a REST API e Python em de imagem digitalizada

Neste guia de início rápido, vai utilizar um modelo de domínio para identificar marcos ou, opcionalmente, celebridades numa imagem armazenada remotamente através da API REST de Imagem Digitalizada. Com o método [Recognize Domain Specific Content](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) (Reconhecer Conteúdo Específico do Domínio), pode aplicar um modelo específico do domínio para reconhecer conteúdo dentro de uma imagem.

Pode executar este início rápido passo a passo com um bloco de notas do Jupyter no [MyBinder](https://mybinder.org). Para iniciar o Binder, selecione o botão seguinte:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/try/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter o [Python](https://www.python.org/downloads/) instalado se quiser executar o exemplo localmente.
- Tem de ter uma chave de subscrição da Imagem Digitalizada. Para obter uma chave de subscrição, veja [Obter Chaves de Subscrição](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-landmarks-sample"></a>Criar e executar o exemplo de marcos

Para criar e executar o exemplo de marcos, siga os seguintes passos:

1. Copie o código seguinte para um editor de texto.
1. Faça as alterações seguintes ao código, onde for necessário:
    1. Substitua o valor de `subscription_key` pela chave de subscrição.
    1. Substitua o valor de `vision_base_url` pelo URL de ponto final do recurso Imagem Digitalizada na região do Azure onde obteve as chaves de subscrição, se necessário.
    1. Opcionalmente, substitua o valor de `image_url` pelo URL de uma imagem diferente na qual pretende detetar marcos.
1. Guarde o código como um ficheiro com uma extensão `.py`. Por exemplo, `get-landmarks.py`.
1. Abra uma janela da linha de comandos.
1. Na linha de comandos, utilize o comando `python` para executar o exemplo. Por exemplo, `python get-landmarks.py`.

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
#%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

# You must use the same region in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from
# westus, replace "westcentralus" in the URI below with "westus".
#
# Free trial subscription keys are generated in the "westus" region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

landmark_analyze_url = vision_base_url + "models/landmarks/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/f/f6/" + \
    "Bunker_Hill_Monument_2005.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params  = {'model': 'landmarks'}
data    = {'url': image_url}
response = requests.post(
    landmark_analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The
# most relevant landmark for the image is obtained from the 'result' property.
analysis = response.json()
assert analysis["result"]["landmarks"] is not []
print(analysis)
landmark_name = analysis["result"]["landmarks"][0]["name"].capitalize()

# Display the image and overlay it with the landmark name.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(landmark_name, size="x-large", y=-0.1)
```

## <a name="examine-the-response-for-the-landmarks-sample"></a>Examinar a resposta para o exemplo de marcos

O JSON devolve uma resposta de êxito. A página Web de exemplo analisa e apresenta uma resposta de êxito na janela da linha de comandos, semelhante ao seguinte exemplo:

```json
{
  "result": {
    "landmarks": [
      {
        "name": "Bunker Hill Monument",
        "confidence": 0.9768505096435547
      }
    ]
  },
  "requestId": "659a10cd-44bb-44db-9147-a295b853b2b8",
  "metadata": {
    "height": 1600,
    "width": 1200,
    "format": "Jpeg"
  }
}
```

## <a name="create-and-run-the-celebrities-sample"></a>Criar e executar o exemplo de celebridades

Para criar e executar o exemplo de marcos, siga os seguintes passos:

1. Copie o código seguinte para um editor de texto.
1. Faça as alterações seguintes ao código, onde for necessário:
    1. Substitua o valor de `subscription_key` pela chave de subscrição.
    1. Substitua o valor de `vision_base_url` pelo URL de ponto final do recurso Imagem Digitalizada na região do Azure onde obteve as chaves de subscrição, se necessário.
    1. Opcionalmente, substitua o valor de `image_url` pelo URL de uma imagem diferente na qual pretende detetar celebridades.
1. Guarde o código como um ficheiro com uma extensão `.py`. Por exemplo, `get-celebrities.py`.
1. Abra uma janela da linha de comandos.
1. Na linha de comandos, utilize o comando `python` para executar o exemplo. Por exemplo, `python get-celebrities.py`.

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
#%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

celebrity_analyze_url = vision_base_url + "models/celebrities/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/d/d9/" + \
    "Bill_gates_portrait.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params  = {'model': 'celebrities'}
data    = {'url': image_url}
response = requests.post(
    celebrity_analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The
# most relevant celebrity for the image is obtained from the 'result' property.
analysis = response.json()
assert analysis["result"]["celebrities"] is not []
print(analysis)
celebrity_name = analysis["result"]["celebrities"][0]["name"].capitalize()

# Display the image and overlay it with the celebrity name.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(celebrity_name, size="x-large", y=-0.1)
```

## <a name="examine-the-response-for-the-celebrities-sample"></a>Examinar a resposta para o exemplo de celebridades

O JSON devolve uma resposta de êxito. A página Web de exemplo analisa e apresenta uma resposta de êxito na janela da linha de comandos, semelhante ao seguinte exemplo:


```json
{
  "result": {
    "celebrities": [
      {
        "faceRectangle": {
          "top": 123,
          "left": 156,
          "width": 187,
          "height": 187
        },
        "name": "Bill Gates",
        "confidence": 0.9993845224380493
      }
    ]
  },
  "requestId": "f14ec1d0-62d4-4296-9ceb-6b5776dc2020",
  "metadata": {
    "height": 521,
    "width": 550,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos ficheiros, elimine-os para ambos os exemplos.

## <a name="next-steps"></a>Passos Seguintes

Explore uma aplicação do Python que utilize a Imagem Digitalizada para realizar o reconhecimento ótico de carateres (OCR); criar miniaturas com recorte inteligente; além de detetar, categorizar, etiquetar e descrever funcionalidades visuais, incluindo rostos, numa imagem. Para experimentar rapidamente a API de Imagem Digitalizada, experimente a [Consola de teste de API aberta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Tutorial do Python de API de Imagem Digitalizada](../Tutorials/PythonTutorial.md)
