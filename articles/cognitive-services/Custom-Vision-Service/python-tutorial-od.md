---
title: Objeto de deteção com o Python e a API de visão personalizada - serviços cognitivos do Azure | Microsoft Docs
description: Explore uma aplicação básica do Windows que utiliza a API de visão personalizado nos serviços cognitivos da Microsoft. Criar um projeto, adicione etiquetas, carregar imagens, preparar o seu projeto e efetuar uma predição utilizando o ponto final predefinido.
services: cognitive-services
author: areddish
manager: chbuehle
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: areddish
ms.openlocfilehash: b946265b431a7dcb16bf99e3bf78e09f2d0a7de3
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301017"
---
# <a name="use-custom-vision-api-to-build-an-object-detection-project-with-python"></a>Utilizar a API de visão personalizada para criar um projeto de deteção de objeto com o Python

Explore um script de Python básico que utiliza a API de visão do computador para criar um projeto de deteção de objeto. Depois de criado, pode adicionar regiões marcadas, carregar imagens, preparar o projeto, obter o URL de ponto final de predição de predefinição do projeto e utilizar o ponto final para testar através de programação de uma imagem. Utilize este exemplo de open source como um modelo para criar a sua própria aplicação, utilizando a API de visão personalizada.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar o tutorial, precisa de fazer o seguinte:

- Instale o Python 2.7 + ou Python 3.5 +.
- Instale o pip.

### <a name="platform-requirements"></a>Requisitos de plataforma
Neste exemplo tem foi desenvolvido para Python.

### <a name="get-the-custom-vision-sdk"></a>Obter a visão personalizada SDK

Para criar este exemplo, terá de instalar o SDK Python para a API de visão personalizada:

```
pip install azure-cognitiveservices-vision-customvision
```

Pode transferir as imagens com o [amostras de Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples).

## <a name="step-1-get-the-training-and-prediction-keys"></a>Passo 1: Obter as chaves de formação e previsão

Para obter as chaves utilizadas neste exemplo, visite o [site visão personalizada](https://customvision.ai) e selecione o __engrenagem ícone__ no canto superior direito. No __contas__ secção, copie os valores do __formação chave__ e __predição chave__ campos.

![Imagem das chaves da IU](./media/python-tutorial/training-prediction-keys.png)

Este exemplo utiliza as imagens de [nesta localização](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images).

## <a name="step-2-create-a-custom-vision-service-project"></a>Passo 2: Criar um projeto do serviço de visão personalizada

Para criar um novo projeto de serviço de visão personalizada, crie um ficheiro de script sample.py e adicione o seguinte conteúdo. Tenha em atenção a diferença entre a criação de uma deteção de objeto e o projeto de classificação de imagem é o domínio que está especificado para a chamada de create_project.

```Python
from azure.cognitiveservices.vision.customvision.training import training_api
from azure.cognitiveservices.vision.customvision.training.models import ImageFileCreateEntry, Region

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"

trainer = training_api.TrainingApi(training_key)

# Find the object detection domain
obj_detection_domain = next(domain for domain in trainer.get_domains() if domain.type == "ObjectDetection")

# Create a new project
print ("Creating project...")
project = trainer.create_project("My Detection Project", domain_id=obj_detection_domain.id)
```

## <a name="step-3-add-tags-to-your-project"></a>Passo 3: Adicionar etiquetas ao seu projeto

Para adicionar etiquetas ao seu projeto, insira o seguinte código para criar duas etiquetas:

```Python
# Make two tags in the new project
fork_tag = trainer.create_tag(project.id, "fork")
scissors_tag = trainer.create_tag(project.id, "scissors")
```

## <a name="step-4-upload-images-to-the-project"></a>Passo 4: Carregar imagens para o projeto

Para o projeto de deteção de objeto tem de carregar a imagem, regiões e etiquetas. A região no coordiantes normalizado e especifica a localização do objeto marcado.

Para adicionar as imagens, região e etiquetas para o projeto, insira o seguinte código após a criação da etiqueta. Tenha em atenção que, para este tutorial, as regiões são codificado inline com o código. As regiões de especificar a caixa delimitadora em coordenadas normalizadas.

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

# Go through the data table above and create the images
print ("Adding images...")
tagged_images_with_regions = []

for file_name in fork_image_regions.keys():
    x,y,w,h = fork_image_regions[file_name]
    regions = [ Region(tag_id=fork_tag.id, left=x,top=y,width=w,height=h) ]

    with open("images/fork/" + file_name + ".jpg", mode="rb") as image_contents:
        tagged_images_with_regions.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), regions=regions))

for file_name in scissors_image_regions.keys():
    x,y,w,h = scissors_image_regions[file_name]
    regions = [ Region(tag_id=scissors_tag.id, left=x,top=y,width=w,height=h) ]

    with open("images/scissors/" + file_name + ".jpg", mode="rb") as image_contents:
        tagged_images_with_regions.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), regions=regions))


trainer.create_images_from_files(project.id, images=tagged_images_with_regions)
```

## <a name="step-5-train-the-project"></a>Passo 5: Preparar o projeto

Agora que já adicionou as etiquetas e imagens para o projeto, pode prepará-lo: 

1. Insira o seguinte código. Esta ação cria a primeiro iteração no projeto. 
2. Marcar esta iteração como a iteração predefinido.

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

## <a name="step-6-get-and-use-the-default-prediction-endpoint"></a>Passo 6: Obter e utilizar o ponto final de predição de predefinido

Agora, está pronto a utilizar o modelo para a predição de: 

1. Obtenha o ponto final associado a iteração predefinido. 
2. Envie uma imagem de teste para o projeto utilizando esse ponto final.

```Python
from azure.cognitiveservices.vision.customvision.prediction import prediction_endpoint
from azure.cognitiveservices.vision.customvision.prediction.prediction_endpoint import models

# Now there is a trained endpoint that can be used to make a prediction

predictor = prediction_endpoint.PredictionEndpoint(prediction_key)

# Open the sample image and get back the prediction results.
with open("images/test/test_image.jpg", mode="rb") as test_data:
    results = predictor.predict_image(project.id, test_data, iteration.id)

# Display the results.
for prediction in results.predictions:
    print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100), prediction.bounding_box.left, prediction.bounding_box.top, prediction.bounding_box.width, prediction.bounding_box.height)
```

## <a name="step-7-run-the-example"></a>Passo 7: Executar o exemplo

Execute a solução. Os resultados de predição são apresentados na consola.

```
python sample.py
```
