---
title: 'Início rápido: Detetar e quadro rostos numa imagem com o SDK de Python'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, irá criar um script de Python que utiliza a API de rostos para detetar e quadro rostos numa imagem remoto.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 07/03/2018
ms.author: sbowles
ms.openlocfilehash: 741dd18a3b8da5e44d77c24d46adb8d550322281
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603287"
---
# <a name="quickstart-create-a-python-script-to-detect-and-frame-faces-in-an-image"></a>Início rápido: Criar um script de Python para detetar e rostos numa imagem de fotograma

Neste início rápido, irá criar um script de Python que utiliza a API Face do Azure, através do SDK de Python, para detetar rostos humanos numa imagem remota. O aplicativo exibe uma imagem selecionada e desenha um quadro em torno de cada rosto detetado.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de assinatura da Face API. Pode obter uma chave de subscrição de avaliação gratuita de [experimentar os serviços cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Em alternativa, siga as instruções em [criar uma conta dos serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para subscrever o serviço de API de rostos e obtenha a chave.
- [Python 2.7+ ou 3.5+](https://www.python.org/downloads/)
- Ferramenta [pip](https://pip.pypa.io/en/stable/installing/)

## <a name="get-the-face-sdk"></a>Obtenha o SDK de rostos

Instale o SDK de Python de Face ao abrir a linha de comandos e executar o seguinte comando:

```shell
pip install cognitive_face
```

## <a name="detect-faces-in-an-image"></a>Detetar rostos numa imagem

Criar um novo script de Python com o nome _FaceQuickstart.py_ e adicione o seguinte código. Esse código lida com a funcionalidade principal de deteção de rostos. Terá de substituir `<Subscription Key>` com o valor da chave. Também poderá ter de alterar o valor de `BASE_URL` para utilizar o identificador de região corretos para a sua chave (consulte a [documentos da API de rostos](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) para obter uma lista de todos os pontos finais de região). Chaves de subscrição de avaliação gratuita são geradas na **westus** região. Opcionalmente, defina `img_url` para o URL de qualquer imagem que pretende utilizar.

O script irá detetar rostos ao chamar o **cognitive_face.face.detect** método, que encapsula o [detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) REST API e retorna uma lista de faces.

```python
import cognitive_face as CF

# Replace with a valid subscription key (keeping the quotes in place).
KEY = '<Subscription Key>'
CF.Key.set(KEY)

# Replace with your regional Base URL
BASE_URL = 'https://westus.api.cognitive.microsoft.com/face/v1.0/'
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)
```

### <a name="try-the-app"></a>Experimente a aplicação

Execute a aplicação com o comando `python FaceQuickstart.py`. Obterá uma resposta de texto na janela da consola, semelhante ao seguinte:

```console
[{'faceId': '26d8face-9714-4f3e-bfa1-f19a7a7aa240', 'faceRectangle': {'top': 124, 'left': 459, 'width': 227, 'height': 227}}]
```

A saída representa uma lista de rostos detetados. Cada item na lista é uma **dict** instância onde `faceId` é um ID exclusivo para o rosto detetado e `faceRectangle` descreve a posição do mostrador da detetado. 

> [!NOTE]
> IDs de face expirem após 24 horas; precisará armazenar dados de face explicitamente se quiser manter a longo prazo.

## <a name="draw-face-rectangles"></a>Desenhar retângulos faciais nos pontos

Utilizar as coordenadas que recebeu do comando anterior, pode desenhar retângulos na imagem para cada rosto de representar visualmente. Precisará instalar Pillow (`pip install pillow`) para utilizar o módulo de imagem Pillow. Na parte superior *FaceQuickstart.py*, adicione o seguinte código:

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

Em seguida, na parte inferior do seu script, adicione o seguinte código. Este código cria uma função simples para analisar as coordenadas do retângulo e usa Pillow para desenhar retângulos na imagem original. Em seguida, apresenta a imagem no seu Visualizador de imagem padrão.

```python
# Convert width height to a point in a rectangle


def getRectangle(faceDictionary):
    rect = faceDictionary['faceRectangle']
    left = rect['left']
    top = rect['top']
    bottom = left + rect['height']
    right = top + rect['width']
    return ((left, top), (bottom, right))


# Download the image from the url
response = requests.get(img_url)
img = Image.open(BytesIO(response.content))

# For each face returned use the face rectangle and draw a red box.
draw = ImageDraw.Draw(img)
for face in faces:
    draw.rectangle(getRectangle(face), outline='red')

# Display the image in the users default image browser.
img.show()
```

## <a name="run-the-app"></a>Executar a aplicação

Poderá ser-lhe pedido para selecionar um Visualizador de imagem padrão em primeiro lugar. Em seguida, deverá ver uma imagem semelhante ao seguinte. Também verá os dados de retângulo impressos na janela da consola.

![Uma moça com um retângulo vermelho desenhada em todo o Mostrador](../images/face-rectangle-result.png)

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu o processo básico para utilizar o SDK de Python de API de rostos e criou um script para detetar e quadro rostos numa imagem. Em seguida, explore o uso do SDK do Python num exemplo mais complexo. Vá para o exemplo de Python de Face cognitivos no GitHub, clone-o para a pasta do projeto e siga as instruções no _README.md_ ficheiro.

> [!div class="nextstepaction"]
> [Exemplo de Python de Face cognitivo](https://github.com/Microsoft/Cognitive-Face-Python)
