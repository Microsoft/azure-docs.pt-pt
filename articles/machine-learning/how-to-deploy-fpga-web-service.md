---
title: O que são FPGA - como implementar
titleSuffix: Azure Machine Learning
description: Aprenda a implementar um serviço web com um modelo em execução numa FPGA com Azure Machine Learning para inferência de latência ultra-baixa.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 8569f4751c54d7b37aa15737a9b3f7f394c7e26e
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983589"
---
# <a name="what-are-field-programmable-gate-arrays-fpga-and-how-to-deploy"></a>O que são matrizes de porta programáveis de campo (FPGA) e como implementar
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este artigo fornece uma introdução a matrizes de portaprogramáveis de campo (FPGA), e mostra-lhe como implementar os seus modelos usando [o Azure Machine Learning](overview-what-is-azure-ml.md) para um Azure FPGA.

As FPGAs contêm uma matriz de blocos de lógica programáveis e uma hierarquia de interligações reconfiguráveis. As interligações permitem que estes blocos sejam configurados de várias formas após a fabricação. Em comparação com outras fichas, as FPGAs proporcionam uma combinação de programabilidade e desempenho.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGAs vs CPU, GPU e ASIC

O diagrama e a tabela que se aseguir mostram como as FPGAs se comparam a outros processadores.

![Diagrama da comparação FPGA de aprendizagem automática azure](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|Processador||Descrição|
|---|:-------:|------|
|Circuitos integrados específicos da aplicação|ASICs|Circuitos personalizados, como as Unidades de Processador TensorFlow da Google (TPU), proporcionam a maior eficiência. Não podem ser reconfigurados à medida que as suas necessidades mudam.|
|Matrizes de portão programáveis de campo|FPGAs|As FPGAs, como as disponíveis no Azure, proporcionam um desempenho próximo dos ASICs. São também flexíveis e reconfiguráveis ao longo do tempo, para implementar uma nova lógica.|
|Unidades de processamento de gráficos|GPUs|Uma escolha popular para computações de IA. As GPUs oferecem capacidades de processamento paralelas, tornando-as mais rápidas na renderização de imagem do que cpus.|
|Unidades centrais de processamento|CPUs|Processadores de uso geral, o desempenho dos quais não é ideal para gráficos e processamento de vídeo.|

As FPGAs no Azure baseiam-se nos dispositivos FPGA da Intel, que os cientistas de dados e desenvolvedores usam para acelerar os cálculos de IA em tempo real. Esta arquitetura ativada pela FPGA oferece desempenho, flexibilidade e escala, e está disponível no Azure.

As FPGAs possibilitam obter baixa latência para pedidos de inferência em tempo real (ou pontuação de modelos). Não são necessários pedidos assíncronos (loting). O loteamento pode causar latência, porque mais dados precisam de ser processados. Implementações de unidades de processamento neural não requerem lotação; portanto, a latência pode ser muitas vezes menor, em comparação com os processadores CPU e GPU.

### <a name="reconfigurable-power"></a>Potência reconfigurável
Pode reconfigurar as FPGAs para diferentes tipos de modelos de aprendizagem automática. Esta flexibilidade facilita a aceleração das aplicações com base no modelo de precisão numérica e memória mais ideal que está a ser utilizado. Como os FPGAs são reconfiguráveis, pode manter-se atual com os requisitos de algoritmos de IA em rápida mudança.

## <a name="whats-supported-on-azure"></a>O que é apoiado em Azure
O Microsoft Azure é o maior investimento em nuvem do mundo em FPGAs. Utilizando esta arquitetura de hardware ativada pela FPGA, as redes neurais treinadas funcionam rapidamente e com menor latência. O Azure pode paralelamente a redes neuronais profundas pré-treinadas (DNN) através de FPGAs para ampliar o seu serviço. Os DNNs podem ser pré-treinados, como um profundo característica para a aprendizagem de transferências, ou afinados com pesos atualizados.

FPGAs em Suportes Azure:

+ Cenários de classificação e reconhecimento de imagem
+ TensorFlow (requer Tensorflow 1.x)
+ Hardware Intel FPGA

Estes modelos DNN estão atualmente disponíveis:
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

As FPGAs estão disponíveis nestas regiões de Azure:
  - E.U.A. Leste
  - Ásia Sudeste
  - Europa ocidental
  - E.U.A.Oeste 2

> [!IMPORTANT]
> Para otimizar a latência e a entrada, o seu cliente que envia dados para o modelo FPGA deve estar numa das regiões acima (aquela para a qual implementou o modelo).

A **Família PBS de VMs Azure** contém Intel Arria 10 FPGAs. Mostrará como "Standard PBS Family vCPUs" quando verificar a sua atribuição de quotas Azure. O PB6 VM tem seis vCPUs e uma FPGA, e será automaticamente provisionado pela Azure ML como parte da implementação de um modelo para uma FPGA. É utilizado apenas com O Mio-Azul, e não pode funcionar em bitstreams arbitrários. Por exemplo, não será capaz de mostrar a FPGA com bitstreams para fazer encriptação, codificação, etc.

### <a name="scenarios-and-applications"></a>Cenários e aplicações

As FPGAs Azure estão integradas com o Azure Machine Learning. A Microsoft utiliza FPGAs para avaliação de DNN, ranking de pesquisa bing e aceleração de rede definida por software (SDN) para reduzir a latência, ao mesmo tempo que liberta CPUs para outras tarefas.

Os seguintes cenários utilizam FPGAs:
+ [Sistema automatizado de inspeção ótica](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Mapeamento de cobertura de terra](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="deploy-models-on-fpgas"></a>Implementar modelos em FPGAs

Pode implementar um modelo como serviço web em FPGAs com modelos acelerados de hardware de [aprendizagem automática Azure](https://docs.microsoft.com/python/api/azureml-accel-models/azureml.accel?view=azure-ml-py). A utilização de FPGAs proporciona inferência de latência ultra-baixa, mesmo com um único tamanho de lote. Inferência, ou pontuação de modelos, é a fase em que o modelo implantado é usado para previsão, mais frequentemente em dados de produção.

### <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure.  Se não tiver uma, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

- Quota FPGA. Utilize o Azure CLI para verificar se tem quota:

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > Os outros locais ``southeastasia`` ``westeurope``possíveis ``westus2``são, e .

    O comando devolve texto semelhante ao seguinte:

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    Certifique-se de que tem pelo menos 6 vCPUs no âmbito do __CurrentValue__.

    Se não tiver quota, em seguida, submeta um pedido em [https://aka.ms/accelerateAI](https://aka.ms/accelerateAI).

- Um espaço de trabalho azure machine learning e o Azure Machine Learning SDK para Python instalado. Para mais informações, consulte [Criar um espaço de trabalho.](how-to-manage-workspace.md)
 
- O Python SDK para modelos acelerados por hardware:

    ```bash
    pip install --upgrade azureml-accel-models[cpu]
    ```

### <a name="1-create-and-containerize-models"></a>1. Criar e containerizar modelos

Este documento descreverá como criar um gráfico TensorFlow para pré-processar a imagem de entrada, torná-la um featurizer usando resNet 50 numa FPGA, e, em seguida, executar as funcionalidades através de um classificador treinado no conjunto de dados ImageNet.

Siga as instruções para:

* Defina o modelo TensorFlow
* Converter o modelo
* Implementar o modelo
* Consumir o modelo implantado
* Eliminar serviços implantados

Utilize o [SDK de Aprendizagem automática Azure para python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) para criar uma definição de serviço. Uma definição de serviço é um ficheiro que descreve um pipeline de gráficos (entrada, característica e classificação) com base no TensorFlow. O comando de implantação comprime automaticamente a definição e os gráficos num ficheiro ZIP e envia o ZIP para o armazenamento da Blob Azure. O DNN já está implantado para funcionar na FPGA.

1. Espaço de trabalho de aprendizagem automática De carga Azure

   ```python
   import os
   import tensorflow as tf
   
   from azureml.core import Workspace
  
   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
   ```

2. Pré-processar imagem. A entrada para o serviço web é uma imagem JPEG.  O primeiro passo é descodificar a imagem JPEG e processá-la.  As imagens JPEG são tratadas como cordas e o resultado são tensores que serão a entrada para o modelo ResNet 50.

   ```python
   # Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
   import azureml.accel.models.utils as utils
   tf.reset_default_graph()
   
   in_images = tf.placeholder(tf.string)
   image_tensors = utils.preprocess_array(in_images)
   print(image_tensors.shape)
   ```

1. Carregador de características. Inicialize o modelo e baixe um ponto de verificação TensorFlow da versão quantificada do ResNet50 para ser usado como um featurizer.  Pode substituir "QuantizedResnet50" no código abaixo por importar outras redes neuronais profundas:

   - QuantizedResnet152
   - QuantizedVgg16
   - Densenet121

   ```python
   from azureml.accel.models import QuantizedResnet50
   save_path = os.path.expanduser('~/models')
   model_graph = QuantizedResnet50(save_path, is_frozen=True)
   feature_tensor = model_graph.import_graph_def(image_tensors)
   print(model_graph.version)
   print(feature_tensor.name)
   print(feature_tensor.shape)
   ```

1. Adicione um classificador. Este classificador foi treinado no conjunto de dados ImageNet.  Exemplos para a aprendizagem de transferências e formação dos seus pesos personalizados estão disponíveis no conjunto de cadernos de [amostras.](https://aka.ms/aml-notebooks)

   ```python
   classifier_output = model_graph.get_default_classifier(feature_tensor)
   print(classifier_output)
   ```

1. Salve o modelo. Agora que o pré-processador, o resNet 50, e o classificador foram carregados, guarde o gráfico e as variáveis associadas como modelo.

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

1. Guarde os tensores de entrada e de saída. Os tensores de entrada e saída que foram criados durante as etapas de pré-processamento e classificação serão necessários para a conversão e inferência do modelo.

   ```python
   input_tensors = in_images.name
   output_tensors = classifier_output.name

   print(input_tensors)
   print(output_tensors)
   ```

   > [!IMPORTANT]
   > Guarde os tensores de entrada e saída porque vai precisar deles para pedidos de conversão e inferência do modelo.

   Os modelos disponíveis e os tensores de saída de classificadores padrão correspondentes estão abaixo, que é o que você usaria para inferência se usasse o classificador predefinido.

   + Resnet50, QuantizedResnet50
     ```python
     output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
     ```
   + Resnet152, QuantizedResnet152
     ```python
     output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
     ```
   + Densenet121, QuantizedDensenet121
     ```python
     output_tensors = "classifier/densenet121/predictions/Softmax:0"
     ```
   + Vgg16, QuantizedVgg16
     ```python
     output_tensors = "classifier/vgg_16/fc8/squeezed:0"
     ```
   + SsdVgg
     ```python
     output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
     ```

1. [Registe](concept-model-management-and-deployment.md) o modelo utilizando o SDK com o ficheiro ZIP no armazenamento Da Blob Azure. Adicionar tags e outros metadados sobre o modelo ajuda-o a acompanhar os seus modelos treinados.

   ```python
   from azureml.core.model import Model

   registered_model = Model.register(workspace=ws,
                                     model_path=model_save_path,
                                     model_name=model_name)

   print("Successfully registered: ", registered_model.name,
         registered_model.description, registered_model.version, sep='\t')
   ```

   Se já registou um modelo e quiser carregá-lo, poderá recuperá-lo.

   ```python
   from azureml.core.model import Model
   model_name = "resnet50"
   # By default, the latest version is retrieved. You can specify the version, i.e. version=1
   registered_model = Model(ws, name="resnet50")
   print(registered_model.name, registered_model.description,
         registered_model.version, sep='\t')
   ```

1. Converter o gráfico TensorFlow para o formato de troca de rede neural aberta[(ONNX).](https://onnx.ai/)  Terá de fornecer os nomes dos tensores de entrada e saída, e estes nomes serão utilizados pelo seu cliente quando consumir o serviço web.

   ```python
   from azureml.accel import AccelOnnxConverter

   convert_request = AccelOnnxConverter.convert_tf_model(
       ws, registered_model, input_tensors, output_tensors)

   # If it fails, you can run wait_for_completion again with show_output=True.
   convert_request.wait_for_completion(show_output=False)

   # If the above call succeeded, get the converted model
   converted_model = convert_request.result
   print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
         converted_model.id, converted_model.created_time, '\n')
   ```

1. Crie a imagem do Docker a partir do modelo convertido e de todas as dependências.  Esta imagem do Docker pode então ser implantada e instantânea.  Os alvos de implementação suportados incluem AKS na nuvem ou um dispositivo de borda como [O Edge De Data Box](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  Também pode adicionar etiquetas e descrições para a sua imagem registada do Docker.

   ```python
   from azureml.core.image import Image
   from azureml.accel import AccelContainerImage

   image_config = AccelContainerImage.image_configuration()
   # Image name must be lowercase
   image_name = "{}-image".format(model_name)

   image = Image.create(name=image_name,
                        models=[converted_model],
                        image_config=image_config,
                        workspace=ws)
   image.wait_for_creation(show_output=False)
   ```

   Enumera as imagens por etiqueta e obtém os registos detalhados para qualquer depuração.

   ```python
   for i in Image.list(workspace=ws):
       print('{}(v.{} [{}]) stored at {} with build log {}'.format(
           i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
   ```

### <a name="2-deploy-to-cloud-or-edge"></a>2. Desloque-se para a nuvem ou borda

Para implementar o seu modelo como um serviço web de produção de alta escala, utilize o Serviço Azure Kubernetes (AKS). Pode criar um novo utilizando o estúdio Azure Machine Learning SDK, CLI ou [Azure Machine Learning.](https://ml.azure.com)

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2". If no value is specified, the default is "eastus".
prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                    agent_count = 1,
                                                    location = "eastus")

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)
```

A implantação da AKS pode demorar cerca de 15 minutos.  Verifique se o destacamento foi bem sucedido.

```python
aks_target.wait_for_completion(show_output=True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

Desloque o recipiente para o aglomerado AKS.
```python
from azureml.core.webservice import Webservice, AksWebservice

# For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                num_replicas=1,
                                                auth_enabled=False)

aks_service_name = 'my-aks-service'

aks_service = Webservice.deploy_from_image(workspace=ws,
                                           name=aks_service_name,
                                           image=image,
                                           deployment_config=aks_config,
                                           deployment_target=aks_target)
aks_service.wait_for_deployment(show_output=True)
```

#### <a name="test-the-cloud-service"></a>Teste o serviço de nuvem
A imagem do Docker suporta o gRPC e o TensorFlow servindo "previsão" da API.  Use o cliente da amostra para ligar para a imagem do Docker para obter previsões do modelo.  O código do cliente da amostra está disponível:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C #](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Se quiser utilizar o TensorFlow Serving, pode [descarregar um cliente](https://www.tensorflow.org/serving/setup)de amostra .

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize Azure ML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Uma vez que este classificador foi treinado no conjunto de dados [ImageNet,](http://www.image-net.org/) mapeie as aulas para rótulos legíveis pelo homem.

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

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

### <a name="clean-up-the-service"></a>Limpeza do serviço
Elimine o seu serviço web, imagem e modelo (deve ser feito por esta ordem, uma vez que existem dependências).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

### <a name="deploy-to-a-local-edge-server"></a>Desdobrar para um servidor de borda local

Todos os [dispositivos Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) contêm uma FPGA para executar o modelo.  Só um modelo pode estar a funcionar na FPGA de uma só vez.  Para executar um modelo diferente, basta colocar um novo recipiente. As instruções e o código da amostra podem ser encontrados [nesta amostra Azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

## <a name="secure-fpga-web-services"></a>Serviços web Seguros FPGA

Para garantir os seus serviços web da FPGA, consulte o documento [de serviços web Secure.](how-to-secure-web-service.md)

## <a name="next-steps"></a>Passos seguintes

Confira estes cadernos, vídeos e blogs:

+ Vários [cadernos de amostra](https://aka.ms/aml-accel-models-notebooks)

+ [Hardware de hiperescala: ML em escala em cima de Azure + FPGA: Build 2018 (vídeo)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [Dentro da nuvem configurável baseada na Microsoft FPGA (vídeo)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Project Brainwave para IA em tempo real: página inicial do projeto](https://www.microsoft.com/research/project/project-brainwave/)
