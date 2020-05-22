---
title: 'Início Rápido: Gerar uma miniatura – REST, Python'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, irá gerar uma miniatura de uma imagem através da API de Imagem Digitalizada com o Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 05/20/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 38fb7f3b0f2d85762161c384f4e5564078c9a63c
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745183"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-python"></a>Quickstart: Gere uma miniatura utilizando a API e Python de Visão Computacional

Neste arranque rápido, você gerará uma miniatura a partir de uma imagem usando a API DE DESCANSO DE Visão Computacional. Com o método [Get Miniatura,](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20c) pode especificar a altura e largura desejadas, e a Computer Vision utiliza uma colheita inteligente para identificar inteligentemente a área de interesse e gerar coordenadas de cultura com base nessa região.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/try/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter uma chave de subscrição da Imagem Digitalizada. Você pode obter uma chave de teste gratuita da [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Ou, siga as instruções na [Conta Criar uma Conta de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever a Visão Computacional e obter a sua chave. Em seguida, [crie variáveis ambientais](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a chave e corda final de serviço, nomeada `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT` e, respectivamente.
- Um editor de código como [Visual Studio Code.](https://code.visualstudio.com/download)

## <a name="create-and-run-the-sample"></a>Criar e executar o exemplo

Para criar e executar a amostra, copie o seguinte código no editor de código. 

```python
import os
import sys
import requests
# If you are using a Jupyter notebook, uncomment the following lines.
# %matplotlib inline
# import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

thumbnail_url = endpoint + "vision/v3.0/generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

# Construct URL
headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data = {'url': image_url}
# Call API
response = requests.post(thumbnail_url, headers=headers, params=params, json=data)
response.raise_for_status()

# Open the image from bytes
thumbnail = Image.open(BytesIO(response.content))

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))

# Save thumbnail to file
thumbnail.save('thumbnail.png')

# Display image
thumbnail.show()

# Optional. Display the thumbnail from Jupyter.
# plt.imshow(thumbnail)
# plt.axis("off")
```

Em seguida, faça o seguinte:

1. (Opcional) Substitua o valor do `image_url` URL da sua própria imagem.
1. Guarde o código como um ficheiro com a extensão `.py`. Por exemplo, `get-thumbnail.py`.
1. Abra uma janela da linha de comandos.
1. Na linha de comandos, utilize o comando `python` para executar o exemplo. Por exemplo, `python get-thumbnail.py`.

## <a name="examine-the-response"></a>Examinar a resposta

Uma resposta bem sucedida é devolvida como dados binários que representam os dados de imagem para a miniatura. A amostra deve apresentar esta imagem. Se o pedido falhar, a resposta é apresentada na janela de solicitação de comando e deve conter um código de erro.

## <a name="run-in-jupyter-optional"></a>Correr em Jupyter (opcional)

Você pode executar opcionalmente este quickstart em uma forma passo-a-passo usando um caderno Jupyter em [MyBinder](https://mybinder.org). Para iniciar o Binder, selecione o botão seguinte:

[![Aglutinante](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>Passos seguintes

Em seguida, explore uma aplicação Python que utiliza a Visão Computacional para realizar o reconhecimento ótico de caracteres (OCR); criar miniaturas de corte inteligente; e detetar, categorizar, etiquetar e descrever características visuais em imagens.

> [!div class="nextstepaction"]
> [Tutorial do Python de API de Imagem Digitalizada](../Tutorials/PythonTutorial.md)

* Para experimentar rapidamente a API de Imagem Digitalizada, experimente a [Consola de teste de API aberta](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20c).
