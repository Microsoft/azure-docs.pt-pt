---
title: 'Início Rápido: Criar um projeto de classificação de imagens com o SDK de Visão Personalizada para Python'
titleSuffix: Azure Cognitive Services
description: Crie um projeto, adicione etiquetas, carregue imagens, prepare o seu projeto e faça uma predição com o SDK para Python.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: areddish
ms.openlocfilehash: 2ea49eaf6fd48207996a92a997f15e5db3d3b199
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74961085"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-python-sdk"></a>Início Rápido: Criar um projeto de classificação de imagens com o SDK de Visão Personalizada para Python

Este artigo mostra como começar a usar o SDK do Visão Personalizada com Python para criar um modelo de classificação de imagem. Depois de criado, você pode adicionar marcas, carregar imagens, treinar o projeto, obter a URL de ponto de extremidade de previsão publicada do projeto e usar o ponto de extremidade para testar programaticamente uma imagem. Utilize este exemplo como um modelo para criar a sua aplicação de Python. Se quiser percorrer o processo de compilar e utilizar um modelo de classificação _sem_ recorrer a código, veja antes as [orientações baseadas no browser](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Pré-requisitos

- [Python 2.7+ ou 3.5+](https://www.python.org/downloads/)
- ferramenta [pip](https://pip.pypa.io/en/stable/installing/)
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="install-the-custom-vision-sdk"></a>Instalar o SDK da Visão Personalizada

Para instalar o SDK do serviço de Visão Personalizada para Python, utilize o seguinte comando no PowerShell:

```powershell
pip install azure-cognitiveservices-vision-customvision
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]

## <a name="add-the-code"></a>Adicionar o código

Crie um novo ficheiro chamado *sample.py* no seu diretório de projetos preferido.

### <a name="create-the-custom-vision-service-project"></a>Criar o projeto do serviço de Visão Personalizada

Adicione o código seguinte ao seu script para criar um novo projeto do serviço de Visão Personalizada. Insira as chaves de subscrição nas definições apropriadas. Além disso, obtenha a URL do ponto de extremidade na página Configurações do site Visão Personalizada.

Consulte o método [create_project](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.custom_vision_training_client.customvisiontrainingclient?view=azure-python#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config- ) para especificar outras opções ao criar seu projeto (explicado no guia [criar um](getting-started-build-a-classifier.md) portal da Web do classificador).  

```Python
from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageFileCreateEntry

ENDPOINT = "<your API endpoint>"

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"
prediction_resource_id = "<your prediction resource id>"

publish_iteration_name = "classifyModel"

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

Para adicionar as imagens de exemplo ao projeto, insira o seguinte código após a criação da etiqueta. Este código carrega cada imagem com a etiqueta correspondente. Você pode carregar até 64 imagens em um único lote.

> [!NOTE]
> Tem de alterar o caminho para as imagem tendo em conta para onde transferiu o projeto Exemplos do SDK dos Serviços Cognitivos para Python anteriormente.

```Python
base_image_url = "<path to project>"

print("Adding images...")

image_list = []

for image_num in range(1, 11):
    file_name = "hemlock_{}.jpg".format(image_num)
    with open(base_image_url + "images/Hemlock/" + file_name, "rb") as image_contents:
        image_list.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), tag_ids=[hemlock_tag.id]))

for image_num in range(1, 11):
    file_name = "japanese_cherry_{}.jpg".format(image_num)
    with open(base_image_url + "images/Japanese Cherry/" + file_name, "rb") as image_contents:
        image_list.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), tag_ids=[cherry_tag.id]))

upload_result = trainer.create_images_from_files(project.id, images=image_list)
if not upload_result.is_batch_successful:
    print("Image batch upload failed.")
    for image in upload_result.images:
        print("Image status: ", image.status)
    exit(-1)
```

### <a name="train-the-classifier-and-publish"></a>Treinar o classificador e publicar

Esse código cria a primeira iteração no projeto e, em seguida, publica essa iteração no ponto de extremidade de previsão. O nome fornecido para a iteração publicada pode ser usado para enviar solicitações de previsão. Uma iteração não está disponível no ponto de extremidade de previsão até que seja publicada.

```Python
import time

print ("Training...")
iteration = trainer.train_project(project.id)
while (iteration.status != "Completed"):
    iteration = trainer.get_iteration(project.id, iteration.id)
    print ("Training status: " + iteration.status)
    time.sleep(1)

# The iteration is now trained. Publish it to the project endpoint
trainer.publish_iteration(project.id, iteration.id, publish_iteration_name, prediction_resource_id)
print ("Done!")
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>Obter e usar a iteração publicada no ponto de extremidade de previsão

Para enviar uma imagem para o ponto final de predição e obter a mesma, adicione o seguinte código no fim do ficheiro:

```python
from azure.cognitiveservices.vision.customvision.prediction import CustomVisionPredictionClient

# Now there is a trained endpoint that can be used to make a prediction
predictor = CustomVisionPredictionClient(prediction_key, endpoint=ENDPOINT)

with open(base_image_url + "images/Test/test_image.jpg", "rb") as image_contents:
    results = predictor.classify_image(
        project.id, publish_iteration_name, image_contents.read())

    # Display the results.
    for prediction in results.predictions:
        print("\t" + prediction.tag_name +
              ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-application"></a>Executar a aplicação

Execute *sample.py*.

```powershell
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

Depois, pode confirmar que a imagem de teste (disponível em **<base_image_url>/Images/Test/** ) é etiquetada adequadamente. Também pode regressar ao [site da Visão Personalizada](https://customvision.ai) e ver o estado atual do projeto criado recentemente.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Passos seguintes

Viu como cada passo do processo de classificação de imagens pode ser feito no código. Este exemplo executa uma iteração de preparação individual, mas, muitas vezes, terá de preparar e testar o seu modelo várias vezes para torná-lo mais preciso.

> [!div class="nextstepaction"]
> [Test and retrain a model](test-your-model.md) (Testar e voltar a preparar um modelo)
