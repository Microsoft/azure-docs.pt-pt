---
title: Implementar modelos no FPGAs
titleSuffix: Azure Machine Learning service
description: Saiba como implementar um serviço web com um modelo em execução num FPGA com o serviço Azure Machine Learning para inferência de latência Ultra baixas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 1/29/2019
ms.custom: seodec18
ms.openlocfilehash: a82c3b3aef59ef53e5054f2beb2880e8a01dc8fa
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448665"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>Implementar um modelo como um serviço web num FPGA com o serviço Azure Machine Learning

Pode implementar um modelo como um serviço web no [campo matrizes de porta programável por (FPGAs)](concept-accelerate-with-fpgas.md).  A utilização de FPGAs fornece a inferência de latência Ultra baixas, mesmo com um tamanho de lote única.  Estes modelos estão atualmente disponíveis:
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16   

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](http://aka.ms/AMLFree) hoje mesmo.

- Uma área de trabalho do serviço do Azure Machine Learning e o Azure Machine Learning SDK para Python instalada. Saiba como obter estes pré-requisitos com o [como configurar um ambiente de desenvolvimento](how-to-configure-environment.md) documento.
 
  - Deverá estar na sua área de trabalho do *E.U.A. Leste 2* região.

  - Instale os extras contrib:

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```

  - Atualmente apenas tensorflow versão < = 1.10 é suportada, por isso, instalá-lo Depois de concluir todas as outras instalações:

    ```shell
    pip install "tensorflow==1.10"
    ```

### <a name="get-the-notebook"></a>Obter o bloco de notas

Para sua comodidade, este tutorial está disponível como bloco de notas do Jupyter. Siga o código aqui ou execute o [bloco de notas do guia de introdução](https://github.com/Azure/aml-real-time-ai/blob/master/notebooks/project-brainwave-quickstart.ipynb).

## <a name="create-and-deploy-your-model"></a>Criar e implementar o seu modelo
Criar um pipeline para pré-processar a imagem de entrada, torná-lo um recurso usando 50 de utilizar o ResNet num FPGA e, em seguida, execute os recursos através de um classificador com base em com o conjunto de dados ImageNet.

Siga as instruções para:

* Definir o pipeline de modelo
* Implementar o modelo
* Consumir o modelo implementado
* Eliminar serviços implementados

> [!IMPORTANT]
> Para otimizar o débito e latência, o cliente deve ser na mesma região do Azure como o ponto final.  Atualmente as APIs são criadas na região do Azure do Leste-nos.



### <a name="preprocess-image"></a>Pré-processar a imagem
O primeiro estágio do pipeline é pré-processar as imagens.

```python
import os
import tensorflow as tf

# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.contrib.brainwave.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="add-featurizer"></a>Adicionar Featurizer
Inicializar o modelo e transfira um ponto de verificação do TensorFlow da versão quantificada do ResNet50 para ser utilizado como um featurizer.

```python
from azureml.contrib.brainwave.models import QuantizedResnet50
model_path = os.path.expanduser('~/models')
model = QuantizedResnet50(model_path, is_frozen = True)
feature_tensor = model.import_graph_def(image_tensors)
print(model.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Adicionar o classificador
Este classificador foi treinado no conjunto de dados ImageNet.

```python
classifier_output = model.get_default_classifier(feature_tensor)
```

### <a name="create-service-definition"></a>Criar definição de serviço
Agora que definiu o pré-processamento de imagem, featurizer e classificador que executa o serviço, pode criar uma definição de serviço. A definição de serviço é um conjunto de arquivos gerados a partir do modelo que é implementado para o serviço FPGA. A definição de serviço é constituído por um pipeline. O pipeline é uma série de fases que são executados na ordem.  Fases do TensorFlow, Keras fases e fases BrainWave são suportadas.  As fases são executadas na ordem no serviço, com a saída de cada estágio tornar-se a entrada para a fase subsequente.

Para criar um estágio de TensorFlow, especifique uma sessão que contém o gráfico (neste caso é usado gráfico do padrão) e a entrada e saída tensors para esta fase.  Estas informações são utilizadas para guardar o gráfico para que ele pode ser executado no serviço.

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'model_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, feature_tensor, classifier_output))
    model_def.save(model_def_path)
    print(model_def_path)
```

### <a name="deploy-model"></a>Implementar modelo
Crie um serviço da definição de serviço.  A área de trabalho tem de estar na localização E.U.A. Leste 2.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')

from azureml.core.model import Model
model_name = "resnet-50-rtai"
registered_model = Model.register(ws, model_def_path, model_name)

from azureml.core.webservice import Webservice
from azureml.exceptions import WebserviceException
from azureml.contrib.brainwave import BrainwaveWebservice, BrainwaveImage
service_name = "imagenet-infer"
service = None
try:
    service = Webservice(ws, service_name)
except WebserviceException:
    image_config = BrainwaveImage.image_configuration()
    deployment_config = BrainwaveWebservice.deploy_configuration()
    service = Webservice.deploy_from_model(ws, service_name, [registered_model], image_config, deployment_config)
    service.wait_for_deployment(True)
```

### <a name="test-the-service"></a>Testar o serviço
Para enviar uma imagem para a API e testar a resposta, adicione um mapeamento de ID de classe de saída para o nome da classe ImageNet.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()
```

Chamar o seu serviço e substitua o nome do ficheiro de "your jpg" abaixo de uma imagem a partir do seu computador. 

```python
with open('your-image.jpg') as f:
    results = service.run(f)
# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
``` 

### <a name="clean-up-service"></a>Limpar o serviço
Elimine o serviço.

```python
service.delete()
    
registered_model.delete()
```

## <a name="secure-fpga-web-services"></a>Proteger serviços da web FPGA

Para obter informações sobre como proteger serviços da web FPGA, consulte a [proteger serviços da web](how-to-secure-web-service.md) documento.


## <a name="next-steps"></a>Passos Seguintes

Saiba como [Consume implementado um modelo de ML como um serviço web](how-to-consume-web-service.md).
