---
title: 'Início Rápido: criar um projeto de deteção de objeto com o SDK de Visão Personalizada para Python'
titleSuffix: Azure Cognitive Services
description: Crie um projeto, adicione etiquetas, carregue imagens, prepare o seu projeto e elimine objetos com o SDK Python.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: areddish
ms.openlocfilehash: 68d63fbc71ea2dcd07522c6ba42808f88966cd7b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166599"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-python-sdk"></a>Início Rápido: criar um projeto de deteção de objeto com o SDK de Python de Visão Personalizada

Este artigo mostra-lhe como começar a usar o Custom Vision SDK com Python para construir um modelo de deteção de objetos. Depois de criado, pode adicionar regiões marcadas, carregar imagens, treinar o projeto, obter o URL final de previsão publicado do projeto, e usar o ponto final para testar programáticamente uma imagem. Utilize este exemplo como um modelo para criar a sua aplicação de Python.

## <a name="prerequisites"></a>Pré-requisitos

- [Python 2.7+ ou 3.5+](https://www.python.org/downloads/)
- Ferramenta [pip](https://pip.pypa.io/en/stable/installing/)
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="install-the-custom-vision-sdk"></a>Instalar o SDK da Visão Personalizada

Para instalar o SDK do serviço de Visão Personalizada para Python, utilize o seguinte comando no PowerShell:

```powershell
pip install azure-cognitiveservices-vision-customvision
```

Pode transferir as imagens com os [Exemplos de Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples).

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]

## <a name="add-the-code"></a>Adicionar o código

Crie um novo ficheiro chamado *sample.py* no seu diretório de projetos preferido.

### <a name="create-the-custom-vision-service-project"></a>Criar o projeto do serviço de Visão Personalizada

Adicione o código seguinte ao seu script para criar um novo projeto do serviço de Visão Personalizada. Insira as chaves de subscrição nas definições apropriadas. Além disso, obtenha o url do Ponto Final na página de Definições do website Da Visão Personalizada.

Consulte o método [create_project](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.custom_vision_training_client.customvisiontrainingclient?view=azure-python#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config- ) para especificar outras opções quando criar o seu projeto (explicado no guia do portal web [Build a detetor).](get-started-build-detector.md)  

```Python
from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageFileCreateEntry, Region

ENDPOINT = "<your API endpoint>"

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"
prediction_resource_id = "<your prediction resource id>"

publish_iteration_name = "detectModel"

trainer = CustomVisionTrainingClient(training_key, endpoint=ENDPOINT)

# Find the object detection domain
obj_detection_domain = next(domain for domain in trainer.get_domains() if domain.type == "ObjectDetection" and domain.name == "General")

# Create a new project
print ("Creating project...")
project = trainer.create_project("My Detection Project", domain_id=obj_detection_domain.id)
```

### <a name="create-tags-in-the-project"></a>Criar etiquetas no projeto

Para criar etiquetas de objetos no seu projeto, adicione o seguinte código ao final da *sample.py:*

```Python
# Make two tags in the new project
fork_tag = trainer.create_tag(project.id, "fork")
scissors_tag = trainer.create_tag(project.id, "scissors")
```

### <a name="upload-and-tag-images"></a>Enviar e marcar imagens

Quando marca imagens em projetos de deteção de objetos, é necessário especificar a região de cada objeto marcado utilizando coordenadas normalizadas.

> [!NOTE]
> Se não tiver um utilitário de clique e arrastar para marcar as coordenadas das regiões, pode utilizar a UI web em [Customvision.ai](https://www.customvision.ai/). Neste exemplo, as coordenadas já estão fornecidas.


Para adicionar as imagens, etiquetas e regiões ao projeto, insira o seguinte código após a criação da etiqueta. Para este tutorial, as regiões estão codificadas de acordo com o código. As regiões especificam a caixa delimitadora em coordenadas normalizadas e as coordenadas são dadas pela ordem seguinte: esquerda, superior, largura, altura.

```Python
fork_image_regions = {
    "fork_1": [ 0.145833328, 0.3509314, 0.5894608, 0.238562092 ],
    "fork_2": [ 0.294117659, 0.216944471, 0.534313738, 0.5980392 ],
    "fork_3": [ 0.09191177, 0.0682516545, 0.757352948, 0.6143791 ],
    "fork_4": [ 0.254901975, 0.185898721, 0.5232843, 0.594771266 ],
    "fork_5": [ 0.2365196, 0.128709182, 0.5845588, 0.71405226 ],
    "fork_6": [ 0.115196079, 0.133611143, 0.676470637, 0.6993464 ],
    "fork_7": [ 0.164215669, 0.31008172, 0.767156839, 0.410130739 ],
    "fork_8": [ 0.118872553, 0.318251669, 0.817401946, 0.225490168 ],
    "fork_9": [ 0.18259804, 0.2136765, 0.6335784, 0.643790841 ],
    "fork_10": [ 0.05269608, 0.282303959, 0.8088235, 0.452614367 ],
    "fork_11": [ 0.05759804, 0.0894935, 0.9007353, 0.3251634 ],
    "fork_12": [ 0.3345588, 0.07315363, 0.375, 0.9150327 ],
    "fork_13": [ 0.269607842, 0.194068655, 0.4093137, 0.6732026 ],
    "fork_14": [ 0.143382356, 0.218578458, 0.7977941, 0.295751631 ],
    "fork_15": [ 0.19240196, 0.0633497, 0.5710784, 0.8398692 ],
    "fork_16": [ 0.140931368, 0.480016381, 0.6838235, 0.240196079 ],
    "fork_17": [ 0.305147052, 0.2512582, 0.4791667, 0.5408496 ],
    "fork_18": [ 0.234068632, 0.445702642, 0.6127451, 0.344771236 ],
    "fork_19": [ 0.219362751, 0.141781077, 0.5919118, 0.6683006 ],
    "fork_20": [ 0.180147052, 0.239820287, 0.6887255, 0.235294119 ]
}

scissors_image_regions = {
    "scissors_1": [ 0.4007353, 0.194068655, 0.259803921, 0.6617647 ],
    "scissors_2": [ 0.426470578, 0.185898721, 0.172794119, 0.5539216 ],
    "scissors_3": [ 0.289215684, 0.259428144, 0.403186262, 0.421568632 ],
    "scissors_4": [ 0.343137264, 0.105833367, 0.332107842, 0.8055556 ],
    "scissors_5": [ 0.3125, 0.09766343, 0.435049027, 0.71405226 ],
    "scissors_6": [ 0.379901975, 0.24308826, 0.32107842, 0.5718954 ],
    "scissors_7": [ 0.341911763, 0.20714055, 0.3137255, 0.6356209 ],
    "scissors_8": [ 0.231617644, 0.08459154, 0.504901946, 0.8480392 ],
    "scissors_9": [ 0.170343131, 0.332957536, 0.767156839, 0.403594762 ],
    "scissors_10": [ 0.204656869, 0.120539248, 0.5245098, 0.743464053 ],
    "scissors_11": [ 0.05514706, 0.159754932, 0.799019635, 0.730392158 ],
    "scissors_12": [ 0.265931368, 0.169558853, 0.5061275, 0.606209159 ],
    "scissors_13": [ 0.241421565, 0.184264734, 0.448529422, 0.6830065 ],
    "scissors_14": [ 0.05759804, 0.05027781, 0.75, 0.882352948 ],
    "scissors_15": [ 0.191176474, 0.169558853, 0.6936275, 0.6748366 ],
    "scissors_16": [ 0.1004902, 0.279036, 0.6911765, 0.477124184 ],
    "scissors_17": [ 0.2720588, 0.131977156, 0.4987745, 0.6911765 ],
    "scissors_18": [ 0.180147052, 0.112369314, 0.6262255, 0.6666667 ],
    "scissors_19": [ 0.333333343, 0.0274019931, 0.443627447, 0.852941155 ],
    "scissors_20": [ 0.158088237, 0.04047389, 0.6691176, 0.843137264 ]
}
```

Em seguida, use este mapa de associações para carregar cada imagem de amostra com as suas coordenadas de região (pode carregar até 64 imagens num único lote). Adicione o seguinte código.

> [!NOTE]
> Terá de alterar o caminho para as imagens com base no local onde descarregou o repo de Amostras SDK dos Serviços Cognitivos Python SDK mais cedo.

```Python
# Update this with the path to where you downloaded the images.
base_image_url = "<path to repo directory>/cognitive-services-python-sdk-samples/samples/vision/"

# Go through the data table above and create the images
print ("Adding images...")
tagged_images_with_regions = []

for file_name in fork_image_regions.keys():
    x,y,w,h = fork_image_regions[file_name]
    regions = [ Region(tag_id=fork_tag.id, left=x,top=y,width=w,height=h) ]

    with open(base_image_url + "images/fork/" + file_name + ".jpg", mode="rb") as image_contents:
        tagged_images_with_regions.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), regions=regions))

for file_name in scissors_image_regions.keys():
    x,y,w,h = scissors_image_regions[file_name]
    regions = [ Region(tag_id=scissors_tag.id, left=x,top=y,width=w,height=h) ]

    with open(base_image_url + "images/scissors/" + file_name + ".jpg", mode="rb") as image_contents:
        tagged_images_with_regions.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), regions=regions))

upload_result = trainer.create_images_from_files(project.id, images=tagged_images_with_regions)
if not upload_result.is_batch_successful:
    print("Image batch upload failed.")
    for image in upload_result.images:
        print("Image status: ", image.status)
    exit(-1)
```

### <a name="train-the-project-and-publish"></a>Treine o projeto e publique

Este código cria a primeira iteração do modelo de previsão e, em seguida, publica essa iteração para o ponto final da previsão. O nome dado à iteração publicada pode ser usado para enviar pedidos de previsão. Uma iteração não está disponível no ponto final da previsão até ser publicada.

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

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>Obtenha e use a iteração publicada no ponto final da previsão

Para enviar uma imagem para o ponto final de predição e obter a mesma, adicione o seguinte código no fim do ficheiro:

```Python
from azure.cognitiveservices.vision.customvision.prediction import CustomVisionPredictionClient

# Now there is a trained endpoint that can be used to make a prediction

predictor = CustomVisionPredictionClient(prediction_key, endpoint=ENDPOINT)

# Open the sample image and get back the prediction results.
with open(base_image_url + "images/Test/test_od_image.jpg", mode="rb") as test_data:
    results = predictor.detect_image(project.id, publish_iteration_name, test_data)

# Display the results.    
for prediction in results.predictions:
    print("\t" + prediction.tag_name + ": {0:.2f}% bbox.left = {1:.2f}, bbox.top = {2:.2f}, bbox.width = {3:.2f}, bbox.height = {4:.2f}".format(prediction.probability * 100, prediction.bounding_box.left, prediction.bounding_box.top, prediction.bounding_box.width, prediction.bounding_box.height))
```

## <a name="run-the-application"></a>Executar a aplicação

Execute *sample.py*.

```powershell
python sample.py
```

A saída da aplicação deverá aparecer na consola. Pode, em seguida, certificar-se de que a imagem de teste (encontrada em **amostras/visão/imagens/Teste**) está identificada de forma adequada e que a região de deteção está correta.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Passos seguintes

Agora viu como cada passo do processo de deteção de objeto pode ser feito no código. Este exemplo executa uma iteração de preparação individual, mas, muitas vezes, terá de preparar e testar o seu modelo várias vezes para torná-lo mais preciso. O guia seguinte aborda a classificação de imagens, mas os seus princípios são semelhantes à deteção de objetos.

> [!div class="nextstepaction"]
> [Test and retrain a model](test-your-model.md) (Testar e voltar a preparar um modelo)
