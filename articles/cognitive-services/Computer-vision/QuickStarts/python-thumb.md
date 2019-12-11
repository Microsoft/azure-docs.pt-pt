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
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 9a76d4f083c791bb1e3c29b4068c2f82f3cd0bc5
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973701"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-python"></a>Início rápido: gerar uma miniatura usando a API REST do Pesquisa Visual Computacional e o Python

Neste guia de início rápido, você gerará uma miniatura de uma imagem usando a API REST do Pesquisa Visual Computacional. Com o método [obter miniatura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) , você pode especificar a altura e a largura desejadas e pesquisa Visual computacional usa o corte inteligente para identificar de forma inteligente a área de interesse e gerar coordenadas de corte com base nessa região.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/try/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter uma chave de subscrição da Imagem Digitalizada. Você pode obter uma chave de avaliação gratuita de [experimentar serviços cognitivas](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Ou siga as instruções em [criar uma conta de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar pesquisa Visual computacional e obter sua chave. Em seguida, [crie variáveis de ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para a cadeia de caracteres de ponto de extremidade de serviço e chave, denominada `COMPUTER_VISION_SUBSCRIPTION_KEY` e `COMPUTER_VISION_ENDPOINT`, respectivamente.
- Um editor de códigos como o [Visual Studio Code](https://code.visualstudio.com/download).

## <a name="create-and-run-the-sample"></a>Criar e executar o exemplo

Para criar e executar o exemplo, copie o código a seguir no editor de código. 

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
if 'COMPUTER_VISION_SUBSCRIPTION_KEY' in os.environ:
    subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
else:
    print("\nSet the COMPUTER_VISION_SUBSCRIPTION_KEY environment variable.\n**Restart your shell or IDE for changes to take effect.**")
    sys.exit()

if 'COMPUTER_VISION_ENDPOINT' in os.environ:
    endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

thumbnail_url = endpoint + "vision/v2.1/generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data = {'url': image_url}
response = requests.post(thumbnail_url, headers=headers,
                         params=params, json=data)
response.raise_for_status()

thumbnail = Image.open(BytesIO(response.content))

# Display the thumbnail.
plt.imshow(thumbnail)
plt.axis("off")

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))
```

Em seguida, faça o seguinte:
1. Opcionalmente, substitua o valor de `image_url` pelo URL de uma imagem diferente para a qual pretende gerar uma miniatura.
1. Guarde o código como um ficheiro com uma extensão `.py`. Por exemplo, `get-thumbnail.py`.
1. Abra uma janela da linha de comandos.
1. Na linha de comandos, utilize o comando `python` para executar o exemplo. Por exemplo, `python get-thumbnail.py`.

## <a name="examine-the-response"></a>Examinar a resposta

Uma resposta bem-sucedida é retornada como dados binários que representam os dados da imagem para a miniatura. O exemplo deve exibir essa imagem. Se a solicitação falhar, a resposta será exibida na janela de prompt de comando e deverá conter um código de erro.

## <a name="run-in-jupyter-optional"></a>Executar em Jupyter (opcional)

Opcionalmente, você pode executar este guia de início rápido em uma maneira passo a lado usando um notebook Jupyter em [Mybinder](https://mybinder.org). Para iniciar o Binder, selecione o botão seguinte:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>Passos seguintes

Em seguida, saiba mais informações detalhadas sobre o recurso de geração de miniatura.

> [!div class="nextstepaction"]
> [Gerando miniaturas](../concept-generating-thumbnails.md)
