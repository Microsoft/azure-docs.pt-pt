---
title: Criar e implementar um modelo de deteção de objeto utilizando o pacote do Azure Machine Learning para problemas de visão do computador.
description: Saiba como criar, dar formação, testar e implementar um modelo de deteção do objeto do computador visão utilizando o pacote do Azure Machine Learning para problemas de visão do computador.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 06/01/2018
ms.openlocfilehash: 62cc37d8c462d0fc1831de7b50a85738d6e63a17
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34728022"
---
# <a name="build-and-deploy-object-detection-models-with-azure-machine-learning"></a>Criar e implementar modelos de deteção de objeto com o Azure Machine Learning

Neste artigo, saiba como utilizar **Azure Machine Learning pacote para computador visão** para preparar, testar e implementar uma [mais rápida de R CNN](https://arxiv.org/abs/1506.01497) modelo de deteção de objeto. 

Um grande número de problemas no domínio de visão computador possam ser resolvido através da deteção de objeto. Estes problemas incluem a criação de modelos que encontrar um número de variável de objetos de uma imagem. 

Quando criar e implementar este modelo com este pacote, leia os seguintes passos:
1.  Criação de conjunto de dados
2.  Definição de modelo de rede neuronal profundo (DNN)
3.  Modelo de formação
4.  Avaliação e visualização
5.  Implementação de serviço Web
6.  Serviço Web do teste de carga

Neste exemplo, TensorFlow é utilizado como o framework de aprendizagem profunda, formação é executada localmente num computador GPU ligada, tal como o [profunda de VM de ciência de dados de aprendizagem](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview), e a implementação utilizar a CLI do Azure ML Operationalization.

Consulte o [documentação de referência do pacote](https://aka.ms/aml-packages/vision) para a referência de detalhado para cada módulo e uma classe.

## <a name="prerequisites"></a>Pré-requisitos

1. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

1. As seguintes contas e aplicação tem de ser configurados e instalados:
   - Uma conta de Experimentação do Azure Machine Learning 
   - Uma conta de gestão de modelo do Azure Machine Learning
   - O Azure Machine Learning Workbench instalado

   Se estes três são ainda não foi criadas ou instaladas, siga o [instalação de início rápido do Azure Machine Learning e Workbench](../service/quickstart-installation.md) artigo. 

1. O pacote de aprendizagem máquina do Azure para problemas de visão do computador tem de estar instalado. Saiba como [instalar este pacote aqui](https://aka.ms/aml-packages/vision).

## <a name="sample-data-and-notebook"></a>Bloco de notas e dados de exemplo

### <a name="get-the-jupyter-notebook"></a>Obter o bloco de notas do Jupyter

Transferir o bloco de notas para executar o exemplo descrito aqui por si.

> [!div class="nextstepaction"]
> [Obter o bloco de notas do Jupyter](https://aka.ms/aml-packages/vision/notebooks/object_detection)

### <a name="load-the-sample-data"></a>Carregar os dados de exemplo

Para esta demonstração, é fornecido um conjunto de dados de itens de grocery dentro refrigerators, constituída por 30 imagens e 8 classes (eggBox, joghurt, ketchup, mushroom, monteiro, laranja, squash e máximo). Para cada imagem jpg, há um ficheiro de xml anotação com um nome semelhante. 

A figura seguinte mostra a estrutura da pasta recomendada. 

![Estrutura de pastas](media/how-to-build-deploy-object-detection-models/data_directory.JPG)

## <a name="image-annotation"></a>Anotação de imagem

Anotado localizações são necessários para dar formação e avaliar detector um objeto de objeto. [LabelImg](https://tzutalin.github.io/labelImg) é uma ferramenta de anotação de código aberto que pode ser utilizada para anotar imagens. LabelImg escreve um ficheiro xml por imagem no formato de Pascal VOC, que pode ser lidos por este pacote. 

## <a name="storage-context"></a>Contexto de armazenamento
O contexto de armazenamento é utilizado para determinar onde estão armazenados vários ficheiros de saída, tais como ficheiros de modelo DNN. Para obter mais informações, consulte o [StorageContext documentação](https://docs.microsoft.com/en-us/python/api/cvtk.core.context.storagecontext?view=azure-ml-py-latest). Normalmente, os conteúdos de armazenamento não necessitam de ser definida explicitamente. No entanto, para evitar o limite de tamanho de projeto do Workbench de 25 MB, definir o diretório de saídas para apontar para uma localização fora do projecto AML (".. /.. /.. /.. / cvtk_output "). Certifique-se de que remove o diretório de "cvtk_output" uma vez que já não é necessária.


```python
import warnings
warnings.filterwarnings("ignore")
import os, time
from cvtk.core import Context, ObjectDetectionDataset, TFFasterRCNN
from cvtk.utils import detection_utils
from matplotlib import pyplot as plt

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)

# Initialize the context object
out_root_path = "../../../cvtk_output"
Context.create(outputs_path=out_root_path, persistent_path=out_root_path, temp_path=out_root_path)

# Display the images
%matplotlib inline
```

## <a name="create-a-dataset"></a>Criar um conjunto de dados

Crie um conjunto de dados CVTK consiste num conjunto de imagens, com os seus respetivos anotações de caixa delimitador. Neste exemplo, as imagens de refrigerator que são fornecidos na ".. pasta/foods/sample_data/formação"são utilizados. Imagens JPEG só são suportadas.


```python
image_folder = "../sample_data/foods/train"
data_train = ObjectDetectionDataset.create_from_dir(dataset_name='training_dataset', data_dir=image_folder,
                                                    annotations_dir="Annotations", image_subdirectory='JPEGImages')

# Show some statistics of the training image, and also give one example of the ground truth rectangle annotations
data_train.print_info()
_ = data_train.images[2].visualize_bounding_boxes(image_size = (10,10))
```

    F1 2018-05-25 23:12:21,727 INFO azureml.vision:machine info {"is_dsvm": true, "os_type": "Windows"} 
    F1 2018-05-25 23:12:21,733 INFO azureml.vision:dataset creating dataset for scenario=detection 
    Dataset name: training_dataset
    Total classes: 8, total images: 25
    Label-wise object counts:
        Label eggBox: 20 objects
        Label joghurt: 20 objects
        Label ketchup: 20 objects
        Label mushroom: 20 objects
        Label mustard: 20 objects
        Label orange: 20 objects
        Label squash: 40 objects
        Label water: 20 objects
    Bounding box width and height distribution:
        Bounding box widths  0/5/25/50/75/95/100-percentile: 54/61/79/117/133/165/311 pixels
        Bounding box heights 0/5/25/50/75/95/100-percentile: 48/58/75/124/142/170/212 pixels
    


![PNG](media/how-to-build-deploy-object-detection-models/output_6_1.JPG)


## <a name="define-a-model"></a>Definir um modelo

Neste exemplo, é utilizado o modelo de R-CNN mais rapidamente. Vários parâmetros podem ser fornecidos quando definir este modelo. O significado destes parâmetros, bem como os parâmetros utilizados para formação (consulte a secção seguinte) pode ser encontrado em documentos de API de qualquer um dos CVTK ou no [Web site de deteção de objeto Tensorflow](https://github.com/tensorflow/models/tree/master/research/object_detection). Obter mais informações sobre o modelo mais rápida de R CNN podem ser encontradas em [esta ligação](https://docs.microsoft.com/en-us/cognitive-toolkit/Object-Detection-using-Faster-R-CNN#technical-details). Este modelo baseia-se no rápido R-CNN e podem encontrar mais informações sobre o assunto [aqui](https://docs.microsoft.com/en-us/cognitive-toolkit/Object-Detection-using-Fast-R-CNN#algorithm-details).


```python
score_threshold = 0.0       # Threshold on the detection score, use to discard lower-confidence detections.
max_total_detections = 300  # Maximum number of detections. A high value slows down training but might increase accuracy.
my_detector = TFFasterRCNN(labels=data_train.labels, 
                           score_threshold=score_threshold, 
                           max_total_detections=max_total_detections)
```

## <a name="train-the-model"></a>Dar formação sobre o modelo

O modelo de preparação de COCO mais rapidamente do R-CNN com ResNet50 é utilizado como ponto de partida para formação. 

Para preparar o detector, definir o número de passos de preparação no código é 350, para que formação seja executado mais rapidamente (~ 5 minutos com GPU). Na prática, tem de defini-lo para, pelo menos, 10 vezes o número de imagens no conjunto de preparação.

Neste exemplo, o número de passos de preparação de detector está definido para 350 para formação speedy. No entanto, na prática, uma boa regra prática consiste em definir os passos para 10 ou mais vezes o número de imagens no conjunto de preparação.

Dois parâmetros de chaves para formação são:
- Número de passos para preparar o modelo representado pelo argumento de num_seps. Cada passo trains o modelo com um minibatch do tamanho do lote um.
- Learning rate(s), que pode ser definido pelo initial_learning_rate

```python
print("tensorboard --logdir={}".format(my_detector.train_dir))

# to get good results, use a larger value for num_steps, e.g., 5000.
num_steps = 350
learning_rate = 0.001 # learning rate

start_train = time.time()
my_detector.train(dataset=data_train, num_steps=num_steps, 
                  initial_learning_rate=learning_rate)
end_train = time.time()
print(end_train-start_train)
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\train
    F1 2018-05-25 23:12:22,764 INFO azureml.vision:Fit starting in experiment  1125722225 
    F1 2018-05-25 23:12:22,767 INFO azureml.vision:model starting trainging for scenario=detection 
    Using existing checkpoint file that's saved at 'C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\models\detection\faster_rcnn_resnet50_coco_2018_01_28\model.ckpt.index'.
    TFRecords creation started.
    F1 2018-05-25 23:12:22,773 INFO On image 0 of 25
    TFRecords creation completed.
    Training started.
    Training progressing: step 0 ...
    Training progressing: step 100 ...
    Training progressing: step 200 ...
    Training progressing: step 300 ...
    F1 2018-05-25 23:18:02,730 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:18:10,722 INFO 2953 ops in the final graph.
    F1 2018-05-25 23:18:24,244 INFO azureml.vision:Fit finished in experiment  1125722225 
    Training completed.
    361.604615688324
    

TensorBoard pode ser utilizado para visualizar o progresso de formação. Eventos de TensorBoard estão localizados na pasta especificada pelo atributo de train_dir o objeto de modelo. Para ver TensorBoard, siga estes passos:
1. Copie o printout que começa com 'tensorboard – logdir' para uma linha de comandos e executá-la. 
2. Copie o URL devolvido na linha de comandos para um web browser para ver o TensorBoard. 

O TensorBoard deverá ser semelhante à seguinte captura de ecrã. Demora alguns instantes para a pasta de formação ser preenchido. Por isso, se não for apresentada TensorBoard segurança corretamente o primeiro tempo tente repetir os passos 1-2.  

![tensorboard](media/how-to-build-deploy-object-detection-models/tensorboard.JPG)

## <a name="evaluate-the-model"></a>Avaliar o modelo

O método 'avaliar' é utilizado para avaliar o modelo. Esta função requer um objeto de ObjectDetectionDataset como entrada. O conjunto de dados de avaliação pode ser criado utilizando a mesma função que utilizou para o conjunto de dados de formação. A métrica suportada é precisão médio, tal como definido para o [PASCAL VOC desafio](http://host.robots.ox.ac.uk/pascal/VOC/pubs/everingham10.pdf).  


```python
image_folder = "../sample_data/foods/test"
data_val = ObjectDetectionDataset.create_from_dir(dataset_name='val_dataset', data_dir=image_folder)
eval_result = my_detector.evaluate(dataset=data_val)
```

    F1 2018-05-25 23:18:24,253 INFO azureml.vision:dataset creating dataset for scenario=detection 
    F1 2018-05-25 23:18:24,286 INFO On image 0 of 5
    F1 2018-05-25 23:18:29,300 INFO Starting evaluation at 2018-05-26-03:18:29
    F1 2018-05-25 23:18:32,403 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:33,158 INFO Detection visualizations written to summary with tag image-0.
    F1 2018-05-25 23:18:33,518 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:34,342 INFO Detection visualizations written to summary with tag image-1.
    F1 2018-05-25 23:18:34,714 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:35,470 INFO Detection visualizations written to summary with tag image-2.
    F1 2018-05-25 23:18:35,835 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:36,654 INFO Detection visualizations written to summary with tag image-3.
    F1 2018-05-25 23:18:37,010 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:37,798 INFO Detection visualizations written to summary with tag image-4.
    F1 2018-05-25 23:18:37,804 INFO Running eval batches done.
    F1 2018-05-25 23:18:37,805 INFO # success: 5
    F1 2018-05-25 23:18:37,806 INFO # skipped: 0
    F1 2018-05-25 23:18:38,119 INFO Writing metrics to tf summary.
    F1 2018-05-25 23:18:38,121 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/eggBox: 1.000000
    F1 2018-05-25 23:18:38,205 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/joghurt: 0.942857
    F1 2018-05-25 23:18:38,206 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/ketchup: 1.000000
    F1 2018-05-25 23:18:38,207 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/mushroom: 1.000000
    F1 2018-05-25 23:18:38,208 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/mustard: 1.000000
    F1 2018-05-25 23:18:38,209 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/orange: 1.000000
    F1 2018-05-25 23:18:38,210 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/squash: 1.000000
    F1 2018-05-25 23:18:38,211 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/water: 1.000000
    F1 2018-05-25 23:18:38,211 INFO PASCAL/Precision/mAP@0.5IOU: 0.992857
    F1 2018-05-25 23:18:38,253 INFO Metrics written to tf summary.
    F1 2018-05-25 23:18:38,254 INFO Finished evaluation!
    

Os resultados da avaliação, podem ser impresso num formato correto.


```python
# print out the performance metric values
for label_obj in data_train.labels:
    label = label_obj.name
    key = 'PASCAL/PerformanceByCategory/AP@0.5IOU/' + label
    print('{0: <15}: {1: <3}'.format(label, round(eval_result[key], 2)))
print('{0: <15}: {1: <3}'.format("overall:", round(eval_result['PASCAL/Precision/mAP@0.5IOU'], 2))) 
```

    joghurt        : 0.94
    squash         : 1.0
    mushroom       : 1.0
    eggBox         : 1.0
    ketchup        : 1.0
    mustard        : 1.0
    water          : 1.0
    orange         : 1.0
    overall:       : 0.99
    

Da mesma forma, pode calcular a precisão do modelo no conjunto de preparação. Isto ajuda a garantir de formação convergida para uma boa solução de fazê-lo. A precisão no conjunto de preparação depois de formação bem sucedida, muitas vezes, está próximo de 100%.

Também podem ser visualizados resultados da avaliação TensorBoard, incluindo as imagens com caixas delimitador previstas e valores de mapa. Copie o printout do seguinte código para uma janela de linha de comandos para iniciar o cliente TensorBoard. Aqui, um valor de porta 8008 é utilizado para evitar conflitos com o valor predefinido 6006, que estava a utilizar para visualizar o estado de preparação.


```python
print("tensorboard --logdir={} --port=8008".format(my_detector.eval_dir))
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\eval --port=8008
    

## <a name="score-an-image"></a>Uma imagem de pontuação

Quando estiver satisfeito com o desempenho do modelo treinado, a função de 'Pontuar' do objeto de modelo pode ser utilizada para pontuar novas imagens. As pontuações devolvidas podem ser visualizadas com a função 'visualizar'. 


```python
image_path = data_val.images[1].storage_path
detections_dict = my_detector.score(image_path)
path_save = out_root_path + "/scored_images/scored_image_preloaded.jpg"
ax = detection_utils.visualize(image_path, detections_dict, image_size=(8, 12))
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

![PNG](media/how-to-build-deploy-object-detection-models/output_20_0.JPG)

##  <a name="save-the-model"></a>Guarde o modelo

O modelo treinado pode ser guardado no disco e carregado novamente na memória, conforme ilustrado nos exemplos de código seguinte.


```python
save_model_path = out_root_path + "/frozen_model/faster_rcnn.model" # Please save your model to outside of your AML workbench project folder because of the size limit of AML project
my_detector.save(save_model_path)
```

    F1 2018-05-25 23:18:55,166 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:19:03,867 INFO 2953 ops in the final graph.
    

## <a name="load-the-saved-model-for-scoring"></a>Carregar o modelo guardado para classificação

Para utilizar o modelo guardado, carregar o modelo na memória com a função 'carga'. Só tem de carregar uma vez. 

```python
my_detector_loaded = TFFasterRCNN.load(save_model_path)
```

Depois do modelo é carregado, pode ser utilizado para pontuar uma imagem ou uma lista de imagens. Para uma única imagem, é devolvido um dicionário com chaves como 'detection_boxes', 'detection_scores' e 'num_detections'. Se a entrada é uma lista de imagens, uma lista de dicionário é devolvida, com um dicionário correspondente a uma imagem. 


```python
detections_dict = my_detector_loaded.score(image_path)
```

Os objetos detetados com pontuações acima 0,5, incluindo etiquetas, pontuações e coordenadas pode ser impresso.


```python
look_up = dict((v,k) for k,v in my_detector.class_map.items())
n_obj = 0
for i in range(detections_dict['num_detections']):
    if detections_dict['detection_scores'][i] > 0.5:
        n_obj += 1
        print("Object {}: label={:11}, score={:.2f}, location=(top: {:.2f}, left: {:.2f}, bottom: {:.2f}, right: {:.2f})".format(
            i, look_up[detections_dict['detection_classes'][i]], 
            detections_dict['detection_scores'][i], 
            detections_dict['detection_boxes'][i][0],
            detections_dict['detection_boxes'][i][1], 
            detections_dict['detection_boxes'][i][2],
            detections_dict['detection_boxes'][i][3]))    
        
print("\nFound {} objects in image {}.".format(n_obj, image_path))           
```

    Object 0: label=squash     , score=0.99, location=(top: 0.74, left: 0.30, bottom: 0.84, right: 0.42)
    Object 1: label=squash     , score=0.98, location=(top: 0.27, left: 0.21, bottom: 0.37, right: 0.33)
    Object 2: label=orange     , score=0.98, location=(top: 0.31, left: 0.39, bottom: 0.37, right: 0.48)
    Object 3: label=joghurt    , score=0.98, location=(top: 0.57, left: 0.29, bottom: 0.67, right: 0.39)
    Object 4: label=eggBox     , score=0.97, location=(top: 0.41, left: 0.53, bottom: 0.49, right: 0.69)
    Object 5: label=water      , score=0.95, location=(top: 0.23, left: 0.51, bottom: 0.37, right: 0.57)
    Object 6: label=mustard    , score=0.88, location=(top: 0.61, left: 0.47, bottom: 0.66, right: 0.57)
    Object 7: label=ketchup    , score=0.80, location=(top: 0.62, left: 0.62, bottom: 0.68, right: 0.72)
    
    Found 8 objects in image ../sample_data/foods/test\JPEGImages\10.jpg.
    

Visualize tal como as pontuações antes.


```python
path_save = out_root_path + "/scored_images/scored_image_frozen_graph.jpg"
ax = detection_utils.visualize(image_path, detections_dict, path_save=path_save, image_size=(8, 12))
# ax.get_figure() # use this code extract the returned image
```

![PNG](media/how-to-build-deploy-object-detection-models/output_30_0.JPG)

## <a name="operationalization-deploy-and-consume"></a>Operationalization: implementar e consumir

Operationalization é o processo de publicação modelos e código como serviços web e o consumo destes serviços para produzir resultados de negócios. 

Depois do seu modelo está preparado, pode implementar este modelo como um serviço web para utilizar o consumo [CLI do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning). Os modelos podem ser implementados para o computador local ou o cluster do serviço de contentor do Azure (ACS). Utilizar ACS, pode dimensionar o seu serviço web manualmente ou utilizar a funcionalidade de dimensionamento automático.

**Iniciar sessão com a CLI do Azure**

Utilizar um [Azure](https://azure.microsoft.com/) conta com uma subscrição válida, inicie sessão com o seguinte comando da CLI:
<br>`az login`

+ Para mudar para outra subscrição do Azure, utilize o comando:
<br>`az account set --subscription [your subscription name]`

+ Para ver a conta de gestão do modelo atual, utilize o comando:
  <br>`az ml account modelmanagement show`

**Criar e definir o seu ambiente de implementação de cluster**

Só tem de definir o seu ambiente de implementação de uma vez. Se ainda não tem um, configure o ambiente de implementação utiliza agora [estas instruções](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup). 

Para ver o seu ambiente de implementação do Active Directory, utilize o seguinte comando da CLI:
<br>`az ml env show`
   
Comando da CLI do Azure para criar e definir o ambiente de implementação de exemplo

```CLI
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. westcentralus] [-g [resource group]]
az ml env set -n [environment name] -g [resource group]
az ml env cluster
```
    
### <a name="manage-web-services-and-deployments"></a>Gerir serviços web e implementações

As APIs seguintes pode ser utilizadas para implementar modelos como serviços web, gerir esses serviços web e gerir implementações.

|Tarefa|API|
|----|----|
|Criar o objeto de implementação|`deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model, aml_env="cluster")`
|Implementar o serviço web|`deploy_obj.deploy()`|
|Imagem de modelo de pontuação|`deploy_obj.score_image(local_image_path_or_image_url)`|
|Eliminar o serviço web|`deploy_obj.delete()`|
|Compilar a imagem do docker sem o serviço web|`deploy_obj.build_docker_image()`|
|Lista de implementação existente|`AMLDeployment.list_deployment()`|
|Eliminar se o serviço existe com o nome da implementação|`AMLDeployment.delete_if_service_exist(deployment_name)`|

**Documentação da API:** consulte o [documentação de referência do pacote](https://aka.ms/aml-packages/vision) para a referência de detalhado para cada módulo e uma classe.

**Referência da CLI:** para mais avançadas operações relacionadas com a implementação, consulte o [modelo gestão referência CLI](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/model-management-cli-reference).

**Gestão de implementação no portal do Azure**: pode controlar e gerir as implementações no [portal do Azure](https://ms.portal.azure.com/). No portal do Azure, localize a sua página de conta de gestão de modelo do Machine Learning utilizando o respetivo nome. Em seguida, avance para a página de conta de gestão do modelo > Gestão de modelo > serviços.

```python
# ##### OPTIONAL - Interactive CLI setup helper ###### 
# # Interactive CLI setup helper, including model management account and deployment environment.
# # If you haven't setup you CLI before or if you want to change you CLI settings, you can use this block to help you interactively.
# # UNCOMMENT THE FOLLOWING LINES IF YOU HAVE NOT CREATED OR SET THE MODEL MANAGEMENT ACCOUNT AND DEPLOYMENT ENVIRONMENT

# from azuremltkbase.deployment import CliSetup
# CliSetup().run()
```


```python
from cvtk.operationalization import AMLDeployment

# set deployment name
deployment_name = "wsdeployment"

# Create deployment object
# It will use the current deployment environment (you can check it with CLI command "az ml env show").
deploy_obj = AMLDeployment(deployment_name=deployment_name, aml_env="cluster", associated_DNNModel=my_detector, replicas=1)

# Alternatively, you can provide azure machine learning deployment cluster name (environment name) and resource group name
# to deploy your model. It will use the provided cluster to deploy. To do that, please uncomment the following lines to create 
# the deployment object.

# azureml_rscgroup = "<resource group>"
# cluster_name = "<cluster name>"
# deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=my_detector,
#                            aml_env="cluster", cluster_name=cluster_name, resource_group=azureml_rscgroup, replicas=1)

# Check if the deployment name exists, if yes remove it first.
if deploy_obj.is_existing_service():
    AMLDeployment.delete_if_service_exist(deployment_name)
    
# create the webservice
print("Deploying to Azure cluster...")
deploy_obj.deploy()
print("Deployment DONE")
```

### <a name="consume-the-web-service"></a>Consumir o serviço web

Assim que tiver criado o serviço Web, pode Pontuar imagens com o serviço Web implementado. Tem várias opções:

   - Diretamente pode Pontuar o serviço Web com o objeto de implementação com: deploy_obj.score_image(image_path_or_url) 
   - Em alternativa, pode utilizar o url de ponto final de serviço e a chave de serviço (nenhum para a implementação local) com: AMLDeployment.score_existing_service_with_image (image_path_or_url service_endpoint_url, service_key = None)
   - Forma os pedidos de http diretamente para pontuar o ponto final de serviço Web (para utilizadores avançados).

### <a name="score-with-existing-deployment-object"></a>Com o objeto de implementação existente de pontuação
```
deploy_obj.score_image(image_path_or_url)
```


```python
# Score with existing deployment object

# Score local image with file path
print("Score local image with file path")
image_path_or_url = data_train.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json[:50])

# Score image url and remove image resizing
print("Score image url")
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json[:50])

```


```python
# Time image scoring
import timeit

num_images = 3
for img_index, img_obj in enumerate(data_train.images[:num_images]):
    print("Calling API for image {} of {}: {}...".format(img_index, num_images, img_obj.name))
    tic = timeit.default_timer()
    return_json = deploy_obj.score_image(img_obj.storage_path, image_resize_dims=[224,224])
    print("   Time for API call: {:.2f} seconds".format(timeit.default_timer() - tic))
```

### <a name="score-with-service-endpoint-url-and-service-key"></a>Pontuar com o url de ponto final de serviço e a chave de serviço
```
    AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)
```


```python
# Import related classes and functions
from cvtk.operationalization import AMLDeployment

service_endpoint_url = "http://xxx" # please replace with your own service url
service_key = "xxx" # please replace with your own service key

# score image url
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json[:50])
```

### <a name="score-endpoint-with-http-request-directly"></a>Pontuar diretamente o ponto final com o pedido de http
Segue-se algum código de exemplo para formar o pedido http diretamente no Python. Pode fazê-lo noutras linguagens de programação.


```python
def score_image_with_http(image, service_endpoint_url, service_key=None, parameters={}):
    """Score local image with http request

    Args:
        image (str): Image file path
        service_endpoint_url(str): web service endpoint url
        service_key(str): Service key. None for local deployment.
        parameters (dict): Additional request paramters in dictionary. Default is {}.


    Returns:
        str: serialized result 
    """
    import requests
    from io import BytesIO
    import base64
    import json

    if service_key is None:
        headers = {'Content-Type': 'application/json'}
    else:
        headers = {'Content-Type': 'application/json',
                   "Authorization": ('Bearer ' + service_key)}
    payload = []
    encoded = None
    
    # Read image
    with open(image,'rb') as f:
        image_buffer = BytesIO(f.read()) ## Getting an image file represented as a BytesIO object
        
    # Convert your image to base64 string
    # image_in_base64 : "b'{base64}'"
    encoded = base64.b64encode(image_buffer.getvalue())
    image_request = {"image_in_base64": "{0}".format(encoded), "parameters": parameters}
    payload.append(image_request)
    body = json.dumps(payload)
    r = requests.post(service_endpoint_url, data=body, headers=headers)
    try:
        result = json.loads(r.text)
        json.loads(result[0])
    except:
        raise ValueError("Incorrect output format. Result cant not be parsed: " + r.text)
    return result[0]

```

### <a name="parse-serialized-result-from-webservice"></a>Analisar o resultado serializado do webservice
O resultado do serviço web é na cadeia json que possa ser analisada.


```python
image_path_or_url = image_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json[:50])
```


```python
# Parse result from json string
import numpy as np
parsed_result = TFFasterRCNN.parse_serialized_result(serialized_result_in_json)
print("Parsed result:", parsed_result)
```


```python
ax = detection_utils.visualize(image_path, parsed_result)
path_save = "../../../cvtk_output/scored_images/scored_image_web.jpg"
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o pacote do Azure Machine Learning para visão de computador nestes artigos:

+ Leia o [descrição geral do pacote e aprender a instalá-lo](https://aka.ms/aml-packages/vision).

+ Explorar o [documentação de referência](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) para este pacote.

+ Saiba mais sobre [outros pacotes do Python para o Azure Machine Learning](reference-python-package-overview.md).
