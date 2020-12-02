---
title: Implementar modelos ML para FPGAs
titleSuffix: Azure Machine Learning
description: Saiba mais sobre os conjuntos de portas programáveis em campo. Você pode implementar um serviço web em um FPGA com Azure Machine Learning para inferência de latência ultra-baixa.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 09/24/2020
ms.topic: conceptual
ms.custom: how-to, contperfq2, devx-track-python, deploy
ms.openlocfilehash: e2bb133997ec7b7d5ee3b8b82ec3179460596eeb
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511105"
---
# <a name="deploy-ml-models-to-field-programmable-gate-arrays-fpgas-with-azure-machine-learning"></a>Implementar modelos ML para arrays de portão programáveis em campo (FPGAs) com Azure Machine Learning 

Neste artigo, você aprende sobre FPGAs e como implementar os seus modelos ML para um Azure FPGA usando os [modelos acelerados por hardware Pacote Python](/python/api/azureml-accel-models/azureml.accel?preserve-view=true&view=azure-ml-py) da [Azure Machine Learning](overview-what-is-azure-ml.md).

## <a name="what-are-fpgas"></a>O que são FPGAs?
As FPGAs contêm uma matriz de blocos de lógica programáveis e uma hierarquia de interligações reconfiguráveis. As interligações permitem configurar estes blocos de várias formas após a fabricação. Em comparação com outros chips, as FPGAs proporcionam uma combinação de programabilidade e desempenho. 

As FPGAs tornam possível obter baixa latência para pedidos de inferência em tempo real (ou pontuação de modelos). Não são necessários pedidos assíncronos (loteamento). O lote pode causar latência, porque mais dados precisam de ser processados. As implementações das unidades de processamento neural não requerem lotes; portanto, a latência pode ser muitas vezes menor, em comparação com os processadores CPU e GPU.

Pode reconfigurar FPGAs para diferentes tipos de modelos de aprendizagem automática. Esta flexibilidade facilita a aceleração das aplicações com base na precisão numérica mais ideal e no modelo de memória que está a ser utilizado. Como as FPGAs são reconfiguráveis, pode manter-se atual com os requisitos de algoritmos de IA em rápida mudança.

![Diagrama da comparação FPGA de Aprendizagem de Máquinas Azure](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|Processador| Abreviatura |Descrição|
|---|:-------:|------|
|Circuitos integrados específicos de aplicação|ASICs|Circuitos personalizados, como as Unidades de Processador tensor da Google (TPU), proporcionam a maior eficiência. Não podem ser reconfigurados à medida que as suas necessidades mudam.|
|Matrizes de portão programáveis em campo|FPGAs|As FPGAs, como as disponíveis no Azure, proporcionam um desempenho próximo dos ASICs. São também flexíveis e reconfiguráveis ao longo do tempo, para implementar uma nova lógica.|
|Unidades de processamento de gráficos|GPUs|Uma escolha popular para computações de IA. As GPUs oferecem capacidades de processamento paralelos, tornando-a mais rápida na renderização de imagem do que nos CPUs.|
|Unidades centrais de processamento|CPUs|Processadores de uso geral, o desempenho que não é ideal para processamento de gráficos e vídeo.|

## <a name="fpga-support-in-azure"></a>Apoio da FPGA em Azure

O Microsoft Azure é o maior investimento em nuvem do mundo em FPGAs. A Microsoft utiliza FPGAs para avaliação de redes neuronais profundas (DNN), ranking de pesquisa de Bing e aceleração de networking definido por software (SDN) para reduzir a latência, enquanto liberta CPUs para outras tarefas.

As FPGAs em Azure são baseadas em dispositivos FPGA da Intel, que cientistas de dados e desenvolvedores usam para acelerar os cálculos de IA em tempo real. Esta arquitetura ativada pela FPGA oferece desempenho, flexibilidade e escala, e está disponível no Azure.

As FPGAs do Azure estão integradas com a Azure Machine Learning. O Azure pode paralelizar dNN pré-treinado em FPGAs para escalar o seu serviço. Os DNNs podem ser pré-treinados, como um aperitivo profundo para a aprendizagem de transferências, ou afinados com pesos atualizados.

|Cenários & configurações no Azure|Modelos DNN suportados|Suporte regional|
|--------------------------|--------------------|----------------|
|+ Cenários de classificação e reconhecimento de imagem<br/>+ Implementação tensorFlow (requer tensorflow 1.x)<br/>+ Hardware Intel FPGA|- ResNet 50<br/>- ResNet 152<br/>- DenseNet-121<br/>- VGG-16<br/>- SSD-VGG|- Leste dos EUA<br/>- Sudeste Asiático<br/>- Europa Ocidental<br/>- Oeste DOS EUA 2|

Para otimizar a latência e a produção, o seu cliente envia dados para o modelo FPGA deve estar numa das regiões acima (aquela para a qual implementou o modelo).

A **PBS Family of Azure VMs** contém Intel Arria 10 FPGAs. Mostrará como "Standard PBS Family vCPUs" quando verificar a sua atribuição de quota Azure. O PB6 VM tem seis vCPUs e um FPGA. O PB6 VM é automaticamente a provisionado pela Azure Machine Learning durante a implementação do modelo para uma FPGA. É utilizado apenas com Azure ML, e não pode executar bitstreams arbitrários. Por exemplo, não será capaz de piscar o FPGA com bitstreams para fazer encriptação, codificação, etc.

## <a name="deploy-models-on-fpgas"></a>Implementar modelos em FPGAs

Pode implementar um modelo como serviço web em FPGAs com [modelos acelerados de hardware de aprendizagem de máquinas Azure.](/python/api/azureml-accel-models/azureml.accel?preserve-view=true&view=azure-ml-py) A utilização de FPGAs fornece inferência de latência ultra-baixa, mesmo com um único tamanho de lote. 

Neste exemplo, cria-se um gráfico TensorFlow para pré-processar a imagem de entrada, torná-la um aperitivo utilizando o ResNet 50 numa FPGA e, em seguida, executar as funcionalidades através de um classificador treinado no conjunto de dados ImageNet. Em seguida, o modelo é implantado num cluster AKS.

### <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma, crie uma conta [pay-as-you-go](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go) (as contas Azure gratuitas não são elegíveis para quota FPGA).

- Um espaço de trabalho de aprendizagem de máquinas Azure e o Azure Machine Learning SDK para Python instalados, como descrito na [Create a workspace](how-to-manage-workspace.md).
 
- O pacote de modelos acelerados por hardware:  `pip install --upgrade azureml-accel-models[cpu]`    
    
- O [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)

- Quota da FPGA. Apresentar um [pedido de quota,](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2nac9-PZhBDnNSV2ITz0LNUN0U5S0hXRkNITk85QURTWk9ZUUFUWkkyTC4u)ou executar este comando CLI para verificar a quota: 

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

   Certifique-se de que tem pelo menos 6 vCPUs sob o __CurrentValue__ devolvido.  

### <a name="define-the-tensorflow-model"></a>Defina o modelo TensorFlow

Comece por utilizar o [Azure Machine Learning SDK para Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) para criar uma definição de serviço. Uma definição de serviço é um ficheiro que descreve um pipeline de gráficos (entrada, aperitivo e classificador) com base no TensorFlow. O comando de implantação comprime a definição e os gráficos num ficheiro ZIP e envia o ZIP para o armazenamento da Azure Blob. O DNN já está implantado para funcionar na FPGA.

1. Carregar espaço de trabalho de aprendizagem automática Azure

   ```python
   import os
   import tensorflow as tf
   
   from azureml.core import Workspace
  
   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
   ```

1. Imagem pré-processamento. A entrada para o serviço web é uma imagem JPEG.  O primeiro passo é descodificar a imagem JPEG e pré-processá-la.  As imagens JPEG são tratadas como cordas e o resultado são tensores que serão a entrada para o modelo ResNet 50.

   ```python
   # Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
   import azureml.accel.models.utils as utils
   tf.reset_default_graph()
   
   in_images = tf.placeholder(tf.string)
   image_tensors = utils.preprocess_array(in_images)
   print(image_tensors.shape)
   ```

1. Carreguante de carga. Inicialize o modelo e descarregue um ponto de verificação TensorFlow da versão quântica do ResNet50 para ser usado como um aperitivo.  Substituir "QuantizedResnet50" no corte de código para importar outras redes neuronais profundas:

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

1. Adicione um classificador. Este classificador foi treinado no conjunto de dados imageNet.

   ```python
   classifier_output = model_graph.get_default_classifier(feature_tensor)
   print(classifier_output)
   ```

1. Salve o modelo. Agora que o pré-processor, o gravador ResNet 50 e o classificador foram carregados, guarde o gráfico e as variáveis associadas como modelo.

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

1. Guarde os tensores de entrada e **saída, pois irá usá-los para pedidos de conversão de modelos e inferências**. 

   ```python
   input_tensors = in_images.name
   output_tensors = classifier_output.name

   print(input_tensors)
   print(output_tensors)
   ```

   Os modelos a seguir estão disponíveis listados com os seus tensores de saída de classificador para inferência se utilizar o classificador predefinido.

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

### <a name="convert-the-model-to-the-open-neural-network-exchange-format-onnx"></a>Converter o modelo no formato Open Neural Network Exchange (ONNX)

Antes de poder implantar nas FPGAs, converta o modelo no formato [ONNX.](https://onnx.ai/)

1. [Registe](concept-model-management-and-deployment.md) o modelo utilizando o SDK com o ficheiro ZIP no armazenamento Azure Blob. Adicionar tags e outros metadados sobre o modelo ajuda-o a acompanhar os seus modelos treinados.

   ```python
   from azureml.core.model import Model

   registered_model = Model.register(workspace=ws,
                                     model_path=model_save_path,
                                     model_name=model_name)

   print("Successfully registered: ", registered_model.name,
         registered_model.description, registered_model.version, sep='\t')
   ```

   Se já registou um modelo e quer carregá-lo, pode recuperá-lo.

   ```python
   from azureml.core.model import Model
   model_name = "resnet50"
   # By default, the latest version is retrieved. You can specify the version, i.e. version=1
   registered_model = Model(ws, name="resnet50")
   print(registered_model.name, registered_model.description,
         registered_model.version, sep='\t')
   ```

1. Converta o gráfico TensorFlow no formato ONNX.  Tem de fornecer os nomes dos tensores de entrada e saída, para que o seu cliente possa usá-los quando consumir o serviço web.

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

### <a name="containerize-and-deploy-the-model"></a>Containerize e implemente o modelo

Em seguida, crie uma imagem docker a partir do modelo convertido e de todas as dependências.  Esta imagem docker pode então ser implantada e instantânea.  Os alvos de implementação suportados incluem o Serviço Azure Kubernetes (AKS) na nuvem ou um dispositivo de borda como [o Azure Data Box Edge](../databox-online/azure-stack-edge-overview.md).  Também pode adicionar tags e descrições para a sua imagem registada do Docker.

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

   Enuse as imagens por marca e obtenha os registos detalhados para qualquer depuragem.

   ```python
   for i in Image.list(workspace=ws):
       print('{}(v.{} [{}]) stored at {} with build log {}'.format(
           i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
   ```

#### <a name="deploy-to-an-azure-kubernetes-service-cluster"></a>Implementar para um Cluster de Serviços Azure Kubernetes

1. Para implementar o seu modelo como um serviço web de produção de alta escala, utilize a AKS. Você pode criar um novo usando o Azure Machine Learning SDK, CLI ou [Azure Machine Learning studio](https://ml.azure.com).

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

    A implantação AKS pode demorar cerca de 15 minutos.  Verifique se a implantação foi bem sucedida.

    ```python
    aks_target.wait_for_completion(show_output=True)
    print(aks_target.provisioning_state)
    print(aks_target.provisioning_errors)
    ```

1. Desloque o recipiente para o cluster AKS.

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

#### <a name="deploy-to-a-local-edge-server"></a>Implementar para um servidor de borda local

Todos os [dispositivos Azure Data Box Edge](../databox-online/azure-stack-edge-overview.md
) contêm um FPGA para executar o modelo.  Apenas um modelo pode estar a funcionar na FPGA de uma só vez.  Para executar um modelo diferente, basta colocar um novo recipiente. As instruções e o código de amostra podem ser encontrados [nesta Amostra Azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

### <a name="consume-the-deployed-model"></a>Consumir o modelo implantado

Por último, utilize o cliente da amostra para ligar para a imagem do Docker para obter previsões do modelo.  O código do cliente da amostra está disponível:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

A imagem Docker suporta o gRPC e a TensorFlow Serving "predict" API.

Também pode descarregar um cliente de amostra para TensorFlow Serving.

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

Uma vez que este classificador foi treinado no conjunto de dados [imageNet,](http://www.image-net.org/) mapear as classes para etiquetas legíveis pelo homem.

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

### <a name="clean-up-resources"></a>Limpar os recursos

Para evitar custos desnecessários, limpe os seus recursos **nesta ordem**: serviço web, depois imagem e, em seguida, o modelo.

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="next-steps"></a>Passos seguintes

+ Saiba como [proteger o documento dos seus serviços web.](how-to-secure-web-service.md)

+ Saiba mais sobre os preços e custos de aprendizagem automática da FPGA [e da Azure.](https://azure.microsoft.com/pricing/details/machine-learning/)

+ [Hardware de hiperescala: ML em escala em cima de Azure + FPGA: Build 2018 (vídeo)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [Nuvem configurável baseada na Microsoft FPGA (vídeo)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Projeto Brainwave para IA em tempo real](https://www.microsoft.com/research/project/project-brainwave/)

+ [Sistema automatizado de inspeção ótica](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)
