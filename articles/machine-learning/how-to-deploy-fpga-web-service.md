---
title: O que são FPGA - como implementar
titleSuffix: Azure Machine Learning
description: Aprenda a implementar um serviço web com um modelo em execução num FPGA com Azure Machine Learning para inferência de latência ultra-baixa.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 06/03/2020
ms.custom: seodec18, contperfq4, tracking-python
ms.openlocfilehash: ace657efaac323dcf48cfba95629abc59ca0cac3
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231765"
---
# <a name="what-are-field-programmable-gate-arrays-fpga-and-how-to-deploy"></a>O que são matrizes de portão programáveis em campo (FPGA) e como implementar

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este artigo fornece uma introdução aos conjuntos de portas programáveis em campo (FPGA), e mostra-lhe como implementar os seus modelos usando [Azure Machine Learning](overview-what-is-azure-ml.md) para um Azure FPGA.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma, terá de criar uma conta [pay-as-you-go](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go) (as contas Azure gratuitas não são elegíveis para quota FPGA).
- [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Quota da FPGA. Utilize o CLI Azure para verificar se tem quota:

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > As outras localizações possíveis ``southeastasia`` ``westeurope`` são, e ``westus2`` .

    O texto de comando devolve texto semelhante ao seguinte:

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    Certifique-se de que tem pelo menos 6 vCPUs sob __o CurrentValue__.

    Se não tiver quota, então envie um pedido em [https://aka.ms/accelerateAI](https://aka.ms/accelerateAI) .

- Um espaço de trabalho de aprendizagem de máquinas Azure e o Azure Machine Learning SDK para Python instalados. Para mais informações, consulte [Criar um espaço de trabalho.](how-to-manage-workspace.md)
 
- O Python SDK para modelos acelerados por hardware:

    ```bash
    pip install --upgrade azureml-accel-models[cpu]
    ```

## <a name="what-are-fpgas"></a>O que são FPGAs

As FPGAs contêm uma matriz de blocos de lógica programáveis e uma hierarquia de interligações reconfiguráveis. As interligações permitem configurar estes blocos de várias formas após a fabricação. Em comparação com outros chips, as FPGAs proporcionam uma combinação de programabilidade e desempenho. 

O diagrama e a tabela seguintes mostram como as FPGAs se comparam a outros processadores.

![Diagrama da comparação FPGA de Aprendizagem de Máquinas Azure](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|Processador| Abreviatura |Descrição|
|---|:-------:|------|
|Circuitos integrados específicos de aplicação|ASICs|Circuitos personalizados, como as Unidades de Processadores TensorFlow da Google (TPU), proporcionam a maior eficiência. Não podem ser reconfigurados à medida que as suas necessidades mudam.|
|Matrizes de portão programáveis em campo|FPGAs|As FPGAs, como as disponíveis no Azure, proporcionam um desempenho próximo dos ASICs. São também flexíveis e reconfiguráveis ao longo do tempo, para implementar uma nova lógica.|
|Unidades de processamento de gráficos|GPUs|Uma escolha popular para computações de IA. As GPUs oferecem capacidades de processamento paralelos, tornando-a mais rápida na renderização de imagem do que nos CPUs.|
|Unidades centrais de processamento|CPUs|Processadores de uso geral, o desempenho que não é ideal para processamento de gráficos e vídeo.|

As FPGAs em Azure são baseadas em dispositivos FPGA da Intel, que cientistas de dados e desenvolvedores usam para acelerar os cálculos de IA em tempo real. Esta arquitetura ativada pela FPGA oferece desempenho, flexibilidade e escala, e está disponível no Azure.

As FPGAs tornam possível obter baixa latência para pedidos de inferência em tempo real (ou pontuação de modelos). Não são necessários pedidos assíncronos (loteamento). O lote pode causar latência, porque mais dados precisam de ser processados. As implementações das unidades de processamento neural não requerem lotes; portanto, a latência pode ser muitas vezes menor, em comparação com os processadores CPU e GPU.

### <a name="reconfigurable-power"></a>Potência reconfigurável

Pode reconfigurar FPGAs para diferentes tipos de modelos de aprendizagem automática. Esta flexibilidade facilita a aceleração das aplicações com base na precisão numérica mais ideal e no modelo de memória que está a ser utilizado. Como as FPGAs são reconfiguráveis, pode manter-se atual com os requisitos de algoritmos de IA em rápida mudança.

### <a name="fpga-support-in-azure"></a>Apoio da FPGA em Azure

O Microsoft Azure é o maior investimento em nuvem do mundo em FPGAs. A Microsoft utiliza FPGAs para avaliação de DNN, ranking de pesquisa Bing e aceleração de networking definido por software (SDN) para reduzir a latência, enquanto liberta CPUs para outras tarefas.

As FPGAs do Azure estão integradas com a Azure Machine Learning. O Azure pode paralelizar redes neuronais profundas pré-treinadas (DNN) através das FPGAs para escalar o seu serviço. Os DNNs podem ser pré-treinados, como um aperitivo profundo para a aprendizagem de transferências, ou afinados com pesos atualizados.

FPGAs on Azure suporta:

+ Cenários de classificação e reconhecimento de imagem
+ Implementação tensorFlow (requer tensorflow 1.x)
+ Hardware Intel FPGA

Estes modelos DNN estão atualmente disponíveis:

  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

As FPGAs estão disponíveis nestas regiões de Azure:

  - E.U.A. Leste
  - Sudeste Asiático
  - Europa Ocidental
  - E.U.A. Oeste 2

> [!IMPORTANT]
> Para otimizar a latência e a produção, o seu cliente envia dados para o modelo FPGA deve estar numa das regiões acima (aquela para a qual implementou o modelo).

A **PBS Family of Azure VMs** contém Intel Arria 10 FPGAs. Mostrará como "Standard PBS Family vCPUs" quando verificar a sua atribuição de quota Azure. O PB6 VM tem seis vCPUs e um FPGA, e será automaticamente abastado pela Azure ML como parte da implementação de um modelo para uma FPGA. É utilizado apenas com Azure ML, e não pode executar bitstreams arbitrários. Por exemplo, não será capaz de piscar o FPGA com bitstreams para fazer encriptação, codificação, etc.

## <a name="deploy-models-on-fpgas"></a>Implementar modelos em FPGAs

Pode implementar um modelo como serviço web em FPGAs com [modelos acelerados de hardware de aprendizagem de máquinas Azure.](https://docs.microsoft.com/python/api/azureml-accel-models/azureml.accel?view=azure-ml-py) A utilização de FPGAs fornece inferência de latência ultra-baixa, mesmo com um único tamanho de lote. Inferência, ou pontuação de modelos, é a fase em que o modelo implantado é usado para previsão, mais frequentemente em dados de produção.

A implantação de um modelo para uma FPGA envolve os seguintes passos:

* Defina o modelo TensorFlow
* Converter o modelo para ONNX
* Implemente o modelo para a nuvem ou um dispositivo de borda
* Consumir o modelo implantado

Nesta amostra, cria-se um gráfico TensorFlow para pré-processar a imagem de entrada, torná-la um aperitivo utilizando o ResNet 50 numa FPGA e, em seguida, executar as funcionalidades através de um classificador treinado no conjunto de dados ImageNet. Em seguida, o modelo é implantado num cluster AKS.

## <a name="1-define-the-tensorflow-model"></a>1. Definir o modelo TensorFlow

Utilize o [Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) para criar uma definição de serviço. Uma definição de serviço é um ficheiro que descreve um pipeline de gráficos (entrada, aperitivo e classificador) com base no TensorFlow. O comando de implementação comprime automaticamente a definição e os gráficos num ficheiro ZIP e envia o ZIP para o armazenamento de Azure Blob. O DNN já está implantado para funcionar na FPGA.

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

1. Carreguante de carga. Inicialize o modelo e descarregue um ponto de verificação TensorFlow da versão quântica do ResNet50 para ser usado como um aperitivo.  Pode substituir "QuantizedResnet50" no corte de código abaixo com a importação de outras redes neuronais profundas:

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

1. Adicione um classificador. Este classificador foi treinado no conjunto de dados ImageNet.  Exemplos para a aprendizagem de transferência e formação os seus pesos personalizados estão disponíveis no conjunto de cadernos de [amostras.](https://aka.ms/aml-notebooks)

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

1. Guarde os tensores de entrada e de saída. Os tensores de entrada e saída que foram criados durante os passos de pré-processamento e de classificação serão necessários para a conversão e inferência do modelo.

   ```python
   input_tensors = in_images.name
   output_tensors = classifier_output.name

   print(input_tensors)
   print(output_tensors)
   ```

   > [!IMPORTANT]
   > Guarde os tensores de entrada e saída porque vai precisar deles para pedidos de conversão de modelos e inferências.

   Os modelos disponíveis e os tensores de saída do classificador predefinido correspondente estão abaixo, que é o que usaria para inferência se usasse o classificador predefinido.

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

## <a name="2-convert-the-model"></a>2. Converter o modelo

Antes de implementar o modelo para FPGAs, tem de convertê-lo no formato ONNX.

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

1. Converta o gráfico TensorFlow no formato Open Neural Network Exchange[(ONNX](https://onnx.ai/)).  Terá de fornecer os nomes dos tensores de entrada e saída, e estes nomes serão utilizados pelo seu cliente quando consumir o serviço web.

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

## <a name="3-containerize-and-deploy-the-model"></a>3. Containerize e implemente o modelo

Crie a imagem do Docker a partir do modelo convertido e de todas as dependências.  Esta imagem docker pode então ser implantada e instantânea.  Os alvos de implementação suportados incluem AKS na nuvem ou um dispositivo de borda, como [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  Também pode adicionar tags e descrições para a sua imagem registada do Docker.

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

### <a name="deploy-to-aks-cluster"></a>Implementar para o Cluster AKS

1. Para implementar o seu modelo como um serviço web de produção de alta escala, utilize o Serviço Azure Kubernetes (AKS). Você pode criar um novo usando o Azure Machine Learning SDK, CLI ou [Azure Machine Learning studio](https://ml.azure.com).

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

### <a name="deploy-to-a-local-edge-server"></a>Implementar para um servidor de borda local

Todos os [dispositivos Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) contêm um FPGA para executar o modelo.  Apenas um modelo pode estar a funcionar na FPGA de uma só vez.  Para executar um modelo diferente, basta colocar um novo recipiente. As instruções e o código de amostra podem ser encontrados [nesta Amostra Azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

## <a name="4-consume-the-deployed-model"></a>4. Consumir o modelo implantado

A imagem Docker suporta o gRPC e a TensorFlow Serving "predict" API.  Use o cliente da amostra para ligar para a imagem do Docker para obter previsões do modelo.  O código do cliente da amostra está disponível:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Se quiser utilizar a Serviço TensorFlow, pode [descarregar um cliente de amostras](https://www.tensorflow.org/serving/setup).

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

## <a name="clean-up-resources"></a>Limpar os recursos

Elimine o seu serviço web, imagem e modelo (deve ser feito por esta ordem uma vez que existem dependências).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="secure-fpga-web-services"></a>Serviços web FPGA seguros

Para garantir os seus serviços web FPGA, consulte o documento [secure web services.](how-to-secure-web-service.md)

## <a name="next-steps"></a>Passos seguintes

Confira estes cadernos, vídeos e blogs:

+ Vários [cadernos de amostras](https://aka.ms/aml-accel-models-notebooks)
+ [Hardware de hiperescala: ML em escala em cima de Azure + FPGA: Build 2018 (vídeo)](https://channel9.msdn.com/events/Build/2018/BRK3202)
+ [Dentro da nuvem configurável baseada na Microsoft FPGA (vídeo)](https://channel9.msdn.com/Events/Build/2017/B8063)
+ [Projeto Brainwave para IA em tempo real: página inicial do projeto](https://www.microsoft.com/research/project/project-brainwave/)
+ [Sistema automatizado de inspeção ótica](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)
+ [Mapeamento de cobertura de terra](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)
