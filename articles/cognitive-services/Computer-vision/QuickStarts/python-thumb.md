---
title: 'Início rápido: Gere uma miniatura - REST, Python'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, irá gerar uma miniatura de uma imagem através da API de Imagem Digitalizada com o Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e0f131c7363a608962910f2cf0e5794972bf6208
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56648936"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-python-in-computer-vision"></a>Início rápido: Gere uma miniatura usando a REST API e Python em de imagem digitalizada

Neste início rápido, irá gerar uma miniatura de uma imagem usando a API de REST da visão do computador. Com o [obter miniatura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) método, pode especificar a altura desejada e a largura e a utiliza de imagem digitalizada corte inteligente para inteligentemente identificar a área de interesse e gerar as coordenadas de recorte com base na região.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/try/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter uma chave de subscrição da Imagem Digitalizada. Para obter uma chave de subscrição, veja [Obter Chaves de Subscrição](../Vision-API-How-to-Topics/HowToSubscribe.md).
- Um editor de código, tais como [Visual Studio Code](https://code.visualstudio.com/download)

## <a name="create-and-run-the-sample"></a>Criar e executar o exemplo

Para criar e executar o exemplo, copie o seguinte código no editor de códigos. 

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

thumbnail_url = vision_base_url + "generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params  = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data    = {'url': image_url}
response = requests.post(thumbnail_url, headers=headers, params=params, json=data)
response.raise_for_status()

thumbnail = Image.open(BytesIO(response.content))

# Display the thumbnail.
plt.imshow(thumbnail)
plt.axis("off")

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))
```

Em seguida, faça o seguinte:
1. Substitua o valor de `subscription_key` pela chave de subscrição.
1. Substitua o valor de `vision_base_url` pelo URL de ponto final do recurso Imagem Digitalizada na região do Azure onde obteve as chaves de subscrição, se necessário.
1. Opcionalmente, substitua o valor de `image_url` pelo URL de uma imagem diferente para a qual pretende gerar uma miniatura.
1. Guarde o código como um ficheiro com uma extensão `.py`. Por exemplo, `get-thumbnail.py`.
1. Abra uma janela da linha de comandos.
1. Na linha de comandos, utilize o comando `python` para executar o exemplo. Por exemplo, `python get-thumbnail.py`.

## <a name="examine-the-response"></a>Examinar a resposta

Uma resposta com êxito, é devolvida como dados binários, que representa os dados de imagem para a miniatura. O exemplo deve apresentar esta imagem. Se o pedido falhar, a resposta é apresentada na janela da linha de comando e deve conter um código de erro.

## <a name="run-in-jupyter-optional"></a>Executar no Jupyter (opcional)

Opcionalmente, pode executar este guia de introdução passo a passo modo com um bloco de notas do Jupyter no [MyBinder](https://mybinder.org). Para iniciar o Binder, selecione o botão seguinte:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>Passos Seguintes

Em seguida, obter informações mais detalhadas sobre a funcionalidade de geração de miniaturas.

> [!div class="nextstepaction"]
> [Geração de miniaturas](../concept-generating-thumbnails.md)
