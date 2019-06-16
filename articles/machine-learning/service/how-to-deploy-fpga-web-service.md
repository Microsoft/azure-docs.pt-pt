---
title: Implementar modelos no FPGAs
titleSuffix: Azure Machine Learning service
description: Saiba como implementar um serviço web com um modelo em execução num FPGA com o serviço Azure Machine Learning para inferência de tipos de latência Ultra baixas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: tedway
author: tedway
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 6cb9de60fe63c936da7340e6ec540a37163216f5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074982"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>Implementar um modelo como um serviço web num FPGA com o serviço Azure Machine Learning

Pode implementar um modelo como um serviço web no [campo matrizes de porta programável por (FPGAs)](concept-accelerate-with-fpgas.md) com modelos de acelerado de Hardware do Azure Machine Learning. A utilização de FPGAs fornece a inferência de tipos de latência Ultra baixas, mesmo com um tamanho de lote única. Inferência de tipos ou modelo de classificação, é a fase em que o modelo implementado é utilizado para predição, mais comumente nos dados de produção.

Estes modelos estão atualmente disponíveis:
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

FPGAs estão disponíveis nestas regiões do Azure:
  - EUA Leste
  - Sudeste Asiático
  - Europa Ocidental
  - EUA Oeste 2

> [!IMPORTANT]
> Para otimizar o débito e latência, o cliente a enviar dados para o modelo FPGA deve ser de uma das regiões acima (aquele que implementou o modelo para).

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure.  Se não tiver uma, crie uma conta gratuita, antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

- FPGA quota.  Utilize a CLI do Azure para verificar se tem quota.
    ```shell
    az vm list-usage --location "eastus" -o table
    ```

    Os outros locais são ``southeastasia``, ``westeurope``, e ``westus2``.

    Na coluna "Name", procure "VCPUs família PBS Standard" e certifique-se de que tem, pelo menos, 6 vCPUs em "CurrentValue."

    Se não tiver quota, em seguida, enviar um formulário de pedido [aqui](https://aka.ms/accelerateAI).

- Uma área de trabalho do serviço do Azure Machine Learning e o Azure Machine Learning SDK para Python instalada. Saiba como obter estes pré-requisitos com o [como configurar um ambiente de desenvolvimento](how-to-configure-environment.md) documento.
 
- O SDK de Python para modelos de acelerada por hardware:

    ```shell
    pip install --upgrade azureml-accel-models
    ```

## <a name="sample-notebooks"></a>Blocos de notas de exemplo

Para sua comodidade [blocos de notas de exemplo](https://aka.ms/aml-accel-models-notebooks) estão disponíveis para o exemplo abaixo e outros exemplos.

## <a name="create-and-containerize-your-model"></a>Criar e contentorizar o seu modelo

Este documento descrevem como criar um gráfico de TensorFlow pré-processar a imagem de entrada, torná-lo um featurizer usando 50 de utilizar o ResNet num FPGA e, em seguida, execute os recursos através de um classificador com base em com o conjunto de dados ImageNet.

Siga as instruções para:

* Definir o modelo do TensorFlow
* Converter o modelo
* Implementar o modelo
* Consumir o modelo implementado
* Eliminar serviços implementados

### <a name="load-azure-ml-workspace"></a>Carregar a área de trabalho do Azure ML

Carregar a sua área de trabalho do Azure ML.

```python
import os
import tensorflow as tf
 
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
```

### <a name="preprocess-image"></a>Pré-processar a imagem

A entrada para o serviço web é uma imagem JPEG.  A primeira etapa é decodificar a imagem JPEG e reprocessá-lo.  As imagens JPEG são tratadas como seqüências de caracteres e o resultado são tensors que serão a entrada para o modelo de utilizar o ResNet 50.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Carregar featurizer

Inicializar o modelo e transfira um ponto de verificação do TensorFlow da versão quantificada do ResNet50 para ser utilizado como um featurizer.  Pode substituir "QuantizedResnet50" no fragmento de código abaixo com através da importação de outras redes neurais profundas:

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen = True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Adicionar o classificador

Este classificador foi treinado no conjunto de dados ImageNet.  Exemplos para a transferência de aprendizagem e treinamento seus pesos personalizados estão disponíveis no conjunto de [blocos de notas de exemplo](https://aka.ms/aml-notebooks).

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>Guarde o modelo

Agora que a pré-processador e o classificador de utilizar o ResNet 50 featurizer tenham sido carregados, salve o gráfico e variáveis associadas como um modelo.

```python
model_name = "resnet50"
model_save_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_save_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_save_path,
                                   inputs={'images': in_images},
                                   outputs={'output_alias': classifier_output})
```

### <a name="save-input-and-output-tensors"></a>Guardar tensors de entrada e saídas
Serão necessário os tensors de entrada e saídas que foram criados durante os passos de pré-processamento e classificador de conversão de modelo e inferência.

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> Guardar a entrada e saída tensors porque vai precisar deles para pedidos de conversão e inferência de tipos de modelo.

Os modelos disponíveis e o classificador de padrão correspondente de saída tensors estão abaixo, que é o que usaria para inferência de tipos se tiver utilizado o classificador de predefinição.

+ Resnet50, QuantizedResnet50
  ```
  output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
  ```
+ Resnet152, QuantizedResnet152
  ```
  output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
  ```
+ Densenet121, QuantizedDensenet121
  ```
  output_tensors = "classifier/densenet121/predictions/Softmax:0"
  ```
+ Vgg16, QuantizedVgg16
  ```
  output_tensors = "classifier/vgg_16/fc8/squeezed:0"
  ```
+ SsdVgg, QuantizedSsdVgg
  ```
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>Registar o modelo

[Registar](./concept-model-management-and-deployment.md) o modelo que criou.  Adicionar etiquetas e outros metadados sobre o modelo ajuda-o a manter o controle de seus modelos de formação.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace = ws,
                                  model_path = model_save_path,
                                  model_name = model_name)

print("Successfully registered: ", registered_model.name, registered_model.description, registered_model.version, sep = '\t')
```

Se já tenha registado um modelo e pretende carregá-lo, pode recuperá-la.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description, registered_model.version, sep = '\t')
```

### <a name="convert-model"></a>Converter o modelo

Converter o gráfico do TensorFlow para o formato de troca de rede Neural aberto ([ONNX](https://onnx.ai/)).  Terá de fornecer os nomes dos tensors a entrada e saídas, e esses nomes serão utilizados pelo seu cliente quando consome o serviço web.

```python
from azureml.accel import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(ws, registered_model, input_tensors, output_tensors)

# If it fails, you can run wait_for_completion again with show_output=True.
convert_request.wait_for_completion(show_output = False)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version, 
      converted_model.id, converted_model.created_time, '\n')
```

### <a name="create-docker-image"></a>Criar a imagem do Docker

O modelo convertido e todas as dependências são adicionadas a uma imagem do Docker.  Esta imagem de Docker, em seguida, pode ser implementada e instanciada.  Destinos de implementação suportados incluem AKS na cloud ou um dispositivo do edge como [Edge de caixa de dados do Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  Também pode adicionar etiquetas e descrições para a imagem do Docker registada.

```python
from azureml.core.image import Image
from azureml.accel import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
# Image name must be lowercase
image_name = "{}-image".format(model_name)

image = Image.create(name = image_name,
                     models = [converted_model],
                     image_config = image_config, 
                     workspace = ws)
image.wait_for_creation(show_output = False)
```

Listar as imagens por etiqueta e obter os registos detalhados para nenhuma depuração.

```python
for i in Image.list(workspace = ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="model-deployment"></a>Implementação do modelo

### <a name="deploy-to-the-cloud"></a>Implemente na cloud

Para implementar o seu modelo como um serviço da web de produção de grande escala, utilize o Azure Kubernetes Service (AKS). Pode criar uma nova com o SDK do Azure Machine Learning, CLI ou o portal do Azure.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM
prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                    agent_count = 1)

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                  name = aks_name, 
                                  provisioning_configuration = prov_config)
```

A implementação do AKS pode demorar cerca de 15 minutos.  Verifique se a implementação foi concluída com êxito.

```python
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

Implemente o contentor para o cluster do AKS.
```python
from azureml.core.webservice import Webservice, AksWebservice

# For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                num_replicas=1,
                                                auth_enabled = False)

aks_service_name ='my-aks-service'

aks_service = Webservice.deploy_from_image(workspace = ws,
                                           name = aks_service_name,
                                           image = image,
                                           deployment_config = aks_config,
                                           deployment_target = aks_target)
aks_service.wait_for_deployment(show_output = True)
```

#### <a name="test-the-cloud-service"></a>Testar o serviço em nuvem
A imagem do Docker suporta gRPC e o TensorFlow que serve "prever" API.  Utilize o cliente de exemplo para ligar para a imagem do Docker para obter as previsões do modelo.  Código de cliente de exemplo está disponível:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Se desejar usar a servir de TensorFlow, pode [transferir um cliente de exemplo](https://www.tensorflow.org/serving/setup).

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize AzureML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Uma vez que este classificador foi com base em com o [ImageNet](http://www.image-net.org/) dados definir, mapear as classes para etiquetas legível por humanos.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg", 
                             input_name=input_tensors, 
                             outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-the-service"></a>O serviço de limpeza
Elimine o serviço web, imagem e o modelo (deve ser feito por esta ordem uma vez que existem dependências).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="deploy-to-a-local-edge-server"></a>Implementar um servidor de periferia locais

Todos os [dispositivos periféricos de caixa de dados do Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) contêm um FPGA para executar o modelo.  Só um modelo pode ser executado no FPGA de uma só vez.  Para executar um modelo diferente, basta implemente um novo contentor. Instruções e código de exemplo podem ser encontrados na [este exemplo de Azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

## <a name="secure-fpga-web-services"></a>Proteger serviços da web FPGA

Para obter informações sobre como proteger serviços da web FPGA, consulte a [proteger serviços da web](how-to-secure-web-service.md) documento.

## <a name="pbs-family-vms"></a>Família PBS VMs

A família PBS de VMs do Azure contém Intel Arria 10 FPGAs.  Este será apresentado como "Família PBS Standard vCPUs" quando verificar sua alocação de quota do Azure.  A VM de PB6 tem seis vCPUs e uma FPGA e automaticamente serão aprovisionada pelo Azure ML como parte da implementação de um modelo para um FPGA.  Só é utilizado com o Azure ML e ele não é possível executar bitstreams arbitrário.  Por exemplo, não será capaz de flash FPGA com bitstreams, fazer a encriptação, codificação, etc. 
