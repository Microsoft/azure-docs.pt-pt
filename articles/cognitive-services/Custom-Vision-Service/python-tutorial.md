---
title: 'Início rápido: Criar um projeto de classificação de imagem com o SDK de visão personalizada para Python'
titlesuffix: Azure Cognitive Services
description: Crie um projeto, adicione etiquetas, carregue imagens, prepare o seu projeto e faça uma predição com o SDK para Python.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: areddish
ms.openlocfilehash: 43c9e155a16ab5d1cc907ecd849ef109f2507782
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351699"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-python-sdk"></a>Início rápido: Criar um projeto de classificação de imagem com o SDK de Python de visão personalizada

Este artigo apresenta informações e código de exemplo para o ajudar a começar a utilizar o SDK da Visão Personalizada com Python e compilar um modelo de classificação de imagens. Depois de criado, poderá adicionar etiquetas, carregar imagens, preparar o projeto, obter o URL de ponto final de predição predefinido do projeto e utilizar o ponto final para testar uma imagem de forma programática. Utilize este exemplo como um modelo para criar a sua aplicação de Python. Se quiser percorrer o processo de compilar e utilizar um modelo de classificação _sem_ recorrer a código, veja antes as [orientações baseadas no browser](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Pré-requisitos

- [Python 2.7+ ou 3.5+](https://www.python.org/downloads/)
- Ferramenta [pip](https://pip.pypa.io/en/stable/installing/)

## <a name="install-the-custom-vision-sdk"></a>Instalar o SDK da Visão Personalizada

Para instalar o SDK do serviço de Visão Personalizada para Python, utilize o seguinte comando no PowerShell:

```PowerShell
pip install azure-cognitiveservices-vision-customvision
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]


## <a name="add-the-code"></a>Adicionar o código

Crie um novo ficheiro chamado *sample.py* no seu diretório de projetos preferido.

### <a name="create-the-custom-vision-service-project"></a>Criar o projeto do serviço de Visão Personalizada

Adicione o código seguinte ao seu script para criar um novo projeto do serviço de Visão Personalizada. Insira as chaves de subscrição nas definições apropriadas.

```Python
from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageFileCreateEntry

ENDPOINT = "https://southcentralus.api.cognitive.microsoft.com"

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"

trainer = CustomVisionTrainingClient(training_key, endpoint=ENDPOINT)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My New Project")
```

### <a name="create-tags-in-the-project"></a>Criar etiquetas no projeto

Para criar etiquetas de classificação no seu projeto, adicione o seguinte código no fim de *sample.py*:

```Python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

### <a name="upload-and-tag-images"></a>Carregar e etiquetar imagens

Para adicionar as imagens de exemplo ao projeto, insira o seguinte código após a criação da etiqueta. Este código carrega cada imagem com a etiqueta correspondente. Tem de introduzir o caminho do URL da imagem base, tendo em conta para onde transferiu o projeto Exemplos do SDK dos Serviços Cognitivos para Python.

> [!NOTE]
> Tem de alterar o caminho para as imagem tendo em conta para onde transferiu o projeto Exemplos do SDK dos Serviços Cognitivos para Python anteriormente.

```Python
base_image_url = "<path to project>"

print("Adding images...")

image_entry = lambda image_path, tag_id: ImageFileCreateEntry(
    name=image_path.split("/")[-1], contents=image_path, tag_ids=[tag_id]
)

image_list = [
    image_entry(
        base_image_url + "Images/Hemlock/hemlock_{}.jpg".format(image_num),
        hemlock_tag.id,
    )
    for image_num in range(1, 10)
] + [
    image_entry(
        base_image_url
        + "Images/Japanese Cherry/japanese_cherry_{}.jpg".format(image_num),
        cherry_tag.id,
    )
    for image_num in range(1, 10)
]

trainer.create_images_from_files(project.id, images=image_list)
```

### <a name="train-the-classifier"></a>Preparar o classificador

Este código cria a primeira iteração no projeto e marca-a como a iteração predefinida. A iteração predefinida reflete a versão do modelo que irá responder aos pedidos de predição. Deve atualizá-la sempre que voltar a preparar o modelo.

```Python
import time

print ("Training...")
iteration = trainer.train_project(project.id)
while (iteration.status != "Completed"):
    iteration = trainer.get_iteration(project.id, iteration.id)
    print ("Training status: " + iteration.status)
    time.sleep(1)

# The iteration is now trained. Make it the default project endpoint
trainer.update_iteration(project.id, iteration.id, is_default=True)
print ("Done!")
```

### <a name="get-and-use-the-default-prediction-endpoint"></a>Obter e utilizar o ponto final de predição predefinido

Para enviar uma imagem para o ponto final de predição e obter a mesma, adicione o seguinte código no fim do ficheiro:

```python
from azure.cognitiveservices.vision.customvision.prediction import CustomVisionPredictionClient

# Now there is a trained endpoint that can be used to make a prediction

predictor = CustomVisionPredictionClient(prediction_key, endpoint=ENDPOINT)

test_img_url = base_image_url + "Images/Test/test_image.jpg"
results = predictor.predict_image_url(project.id, iteration.id, url=test_img_url)

# Display the results.
for prediction in results.predictions:
    print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-application"></a>Executar a aplicação

Execute *sample.py*.

```PowerShell
python sample.py
```

O resultado da aplicação deverá ser semelhante ao seguinte texto:

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Depois, pode confirmar que a imagem de teste (disponível em **<base_image_url>/Images/Test/**) é etiquetada adequadamente. Também pode regressar ao [site da Visão Personalizada](https://customvision.ai) e ver o estado atual do projeto criado recentemente.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Passos Seguintes

Viu como cada passo do processo de classificação de imagens pode ser feito no código. Este exemplo executa uma iteração de preparação individual, mas, muitas vezes, terá de preparar e testar o seu modelo várias vezes para torná-lo mais preciso.

> [!div class="nextstepaction"]
> [Test and retrain a model](test-your-model.md) (Testar e voltar a preparar um modelo)
