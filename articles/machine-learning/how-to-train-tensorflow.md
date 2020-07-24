---
title: Treine e implemente um modelo TensorFlow
titleSuffix: Azure Machine Learning
description: Aprenda a executar scripts de treino tensorFlow em escala usando Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 6604f9ca6c8525a9b60ac6ffc0d043b052a27b18
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87030878"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Construa um modelo de aprendizagem profunda tensorFlow em escala com Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este artigo mostra-lhe como executar os seus scripts de treino [TensorFlow](https://www.tensorflow.org/overview) em escala usando a classe de [estimador de TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) da Azure Machine Learning. Este exemplo treina e regista um modelo TensorFlow para classificar dígitos manuscritos utilizando uma rede neural profunda (DNN).

Quer esteja a desenvolver um modelo TensorFlow a partir do solo ou se está a trazer um [modelo existente](how-to-deploy-existing-model.md) para a nuvem, pode utilizar o Azure Machine Learning para criar trabalhos de formação de código aberto para construir, implantar, ver versão e monitorizar modelos de nível de produção.

Saiba mais sobre [aprendizagem profunda vs machine learning.](concept-deep-learning-vs-machine-learning.md)

## <a name="prerequisites"></a>Pré-requisitos

Executar este código em qualquer um destes ambientes:

 - Exemplo de computação Azure Machine Learning - sem transferências ou instalação necessárias

     - Complete o [Tutorial: Ambiente de configuração e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de caderno dedicado pré-carregado com o SDK e o repositório de amostras.
    - Nas amostras da pasta de aprendizagem profunda no servidor do portátil, encontre um caderno completo e expandido navegando para este diretório: **como usar-azureml > estruturas ml > tensorflow > implantação > pasta de sintonização de hiperparametr-implantação com tensorflow.** 
 
 - O seu próprio servidor de cadernos Jupyter

    - [Instale o Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Crie um ficheiro de configuração do espaço de trabalho.](how-to-configure-environment.md#workspace)
    - [Descarregue os ficheiros de script de](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` amostra e`utils.py`
     
    Também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) deste guia na página de amostras do GitHub. O caderno inclui secções expandidas que cobrem afinação inteligente de hiperparímetros, implantação de modelos e widgets de portátil.

## <a name="set-up-the-experiment"></a>Configurar a experiência

Esta secção define a experiência de treino carregando os pacotes Python necessários, inicializando um espaço de trabalho, criando uma experiência, e carregando os dados de treino e scripts de treino.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importe as bibliotecas python necessárias.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import TensorFlow
```

### <a name="initialize-a-workspace"></a>Inicializar um espaço de trabalho

O espaço de [trabalho Azure Machine Learning](concept-workspace.md) é o recurso de alto nível para o serviço. Proporciona-lhe um lugar centralizado para trabalhar com todos os artefactos que cria. No Python SDK, você pode aceder aos artefactos do espaço de trabalho criando um [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objeto.

Crie um objeto de espaço de trabalho a partir do `config.json` ficheiro criado na secção [pré-requisitos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Criar uma experiência de aprendizagem profunda

Crie uma experiência e uma pasta para segurar os seus scripts de treino. Neste exemplo, crie uma experiência chamada "tf-mnist".

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="create-a-file-dataset"></a>Criar um conjunto de dados de ficheiros

Um `FileDataset` objeto refere um ou vários ficheiros na sua loja de dados do espaço de trabalho ou urls públicos. Os ficheiros podem ser de qualquer formato, e a classe fornece-lhe a capacidade de descarregar ou montar os ficheiros no seu cálculo. Ao criar uma `FileDataset` , cria uma referência à localização da fonte de dados. Se aplicar alguma transformação no conjunto de dados, eles também serão armazenados no conjunto de dados. Os dados permanecem na sua localização existente, pelo que não é incorrido qualquer custo extra de armazenamento. Consulte o guia [de como fazer](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) o pacote para obter mais `Dataset` informações.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

Utilize o `register()` método para registar o conjunto de dados no seu espaço de trabalho para que possam ser partilhados com outros, reutilizados em várias experiências e referidos pelo nome no seu script de treino.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

## <a name="create-a-compute-target"></a>Criar um alvo de computação

Crie um alvo de computação para o seu trabalho TensorFlow para executar. Neste exemplo, crie um cluster de cálculo de aprendizagem automática Azure ativado pela GPU.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

Para obter mais informações sobre metas de computação, consulte o [que é um artigo-alvo de computação.](concept-compute-target.md)

## <a name="create-a-tensorflow-estimator"></a>Criar um estimador de TensorFlow

O [estimador TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fornece uma forma simples de lançar um trabalho de formação TensorFlow num alvo de computação.

O estimador TensorFlow é implementado através da [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) classe genérica, que pode ser usada para suportar qualquer enquadramento. Para obter mais informações sobre modelos de formação utilizando o estimador genérico, consulte [os modelos de comboio com Azure Machine Learning utilizando o estimador](how-to-train-ml-models.md)

Se o seu script de treino precisar de pacotes adicionais de pip ou conda para executar, pode ter os pacotes instalados na imagem do Docker resultante, passando os seus nomes através dos `pip_packages` argumentos e `conda_packages` argumentos.


> [!WARNING]
> A Azure Machine Learning executa scripts de formação copiando todo o diretório de origem. Se tiver dados sensíveis que não pretende fazer o upload, utilize um [ficheiro .ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) ou não o inclua no diretório de origem . Em vez disso, aceda aos seus dados através de uma [datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py).


```python
script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True,
                 pip_packages=['azureml-dataprep[pandas,fuse]'])
```

> [!TIP]
> O suporte para **o Tensorflow 2.0** foi adicionado à classe de estimador de tensorflow. Consulte o post do [blog](https://azure.microsoft.com/blog/tensorflow-2-0-on-azure-fine-tuning-bert-for-question-tagging/) para mais informações.

Para obter mais informações sobre a personalização do seu ambiente Python, consulte [Criar e gerir ambientes para treino e implantação.](how-to-use-environments.md) 

## <a name="submit-a-run"></a>Submeter uma corrida

O [objeto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execução enquanto o trabalho está em execução e depois de concluído.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

À medida que a Corrida é executada, passa pelas seguintes fases:

- **Preparação**: Uma imagem Docker é criada de acordo com o estimador TensorFlow. A imagem é enviada para o registo de contentores do espaço de trabalho e em cache para posteriores execuções. Os registos também são transmitidos para o histórico de execução e podem ser vistos para monitorizar o progresso.

- **Dimensionamento**: O cluster tenta aumentar se o cluster de AI do lote necessitar de mais nós para executar a execução do que estão atualmente disponíveis.

- **Execução**: Todos os scripts na pasta do script são carregados para o alvo do cálculo, as lojas de dados são montadas ou copiadas e o entry_script é executado. As saídas da sestada e da pasta ./logs são transmitidas para o histórico de execução e podem ser utilizadas para monitorizar a execução.

- **Pós-Processamento**: A pasta ./outputs da execução é copiada para o histórico de execução.

## <a name="register-or-download-a-model"></a>Registe-se ou descarregue um modelo

Depois de ter treinado o modelo, pode registá-lo no seu espaço de trabalho. O registo de modelos permite-lhe armazenar e versar os seus modelos no seu espaço de trabalho para simplificar a [gestão e implementação de modelos.](concept-model-management-and-deployment.md) Especificando os parâmetros `model_framework` `model_framework_version` , `resource_configuration` e, a implementação de nenhum modelo de código fica disponível. Isto permite-lhe implementar diretamente o seu modelo como um serviço web a partir do modelo registado, e o `ResourceConfiguration` objeto define o recurso compute para o serviço web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-dnn-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='1.13.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Também pode descarregar uma cópia local do modelo utilizando o objeto Executar. No script de `mnist-tf.py` treino, um objeto de poupança TensorFlow persiste o modelo para uma pasta local (local ao alvo do cálculo). Pode utilizar o objeto Executar para descarregar uma cópia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Preparação distribuída

O [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) estimador também apoia a formação distribuída em clusters de CPU e GPU. Você pode facilmente executar trabalhos de TensorFlow distribuídos e Azure Machine Learning irá gerir a orquestração para si.

A Azure Machine Learning suporta dois métodos de formação distribuída no TensorFlow:

- [Formação](https://www.open-mpi.org/) distribuída baseada no MPI utilizando o quadro [Horovod](https://github.com/uber/horovod)
- [TemorFlow distribuído nativo](https://www.tensorflow.org/deploy/distributed) usando o método do servidor de parâmetros

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) é um quadro de código aberto para formação distribuída desenvolvida pela Uber. Oferece um caminho fácil para trabalhos distribuídos de GPU TensorFlow.

Para utilizar o Horovod, especifique um [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) objeto para o parâmetro no construtor `distributed_training` TensorFlow. Este parâmetro garante que a biblioteca Horovod está instalada para que possa utilizar no seu script de treino.

```Python
from azureml.core.runconfig import MpiConfiguration
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])
```

### <a name="parameter-server"></a>Servidor de parâmetros

Também pode executar [o TemorFlow distribuído nativo,](https://www.tensorflow.org/deploy/distributed)que utiliza o modelo do servidor de parâmetros. Neste método, treina-se através de um conjunto de servidores de parâmetros e trabalhadores. Os trabalhadores calculam os gradientes durante o treino, enquanto os servidores de parâmetros agregam os gradientes.

Para utilizar o método do servidor de parâmetros, especifique um [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) objeto para o parâmetro no construtor `distributed_training` TensorFlow.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
tf_est= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>Definir especificações de cluster em 'TF_CONFIG'

Você também precisa dos endereços de rede e portas do cluster para o [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec) , assim Azure Machine Learning define a `TF_CONFIG` variável ambiental para você.

A `TF_CONFIG` variável ambiente é uma cadeia JSON. Aqui está um exemplo da variável para um servidor de parâmetros:

```JSON
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

Para a API de alto nível da [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) TensorFlow, o TensorFlow analisa a `TF_CONFIG` variável e constrói a especificação de cluster para si.

Para as APIs de núcleo de nível inferior da TensorFlow para treino, analise a `TF_CONFIG` variável e construa o `tf.train.ClusterSpec` seu código de treino.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="deploy-a-tensorflow-model"></a>Implementar um modelo TensorFlow

O modelo que acabou de registar pode ser implantado da mesma forma que qualquer outro modelo registado no Azure Machine Learning, independentemente do estimador que usou para treinar. A implementação como-fazer contém uma secção sobre os modelos de registo, mas pode saltar diretamente para [criar um alvo de computação](how-to-deploy-and-where.md#choose-a-compute-target) para implementação, uma vez que já tem um modelo registado.

## <a name="preview-no-code-model-deployment"></a>(Pré-visualização) Implementação de modelo sem código

Em vez da rota de implementação tradicional, também pode utilizar a funcionalidade de implementação sem código (pré-visualização) para Tensorflow. Ao registar o seu modelo como mostrado acima com o `model_framework` `model_framework_version` , e `resource_configuration` parâmetros, pode simplesmente utilizar a `deploy()` função estática para implantar o seu modelo.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

A forma completa [de como](how-to-deploy-and-where.md) fazer cobre a implantação em Azure Machine Learning em maior profundidade.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, treinou e registou um modelo TensorFlow, e aprendeu sobre opções de implementação. Veja estes outros artigos para saber mais sobre Azure Machine Learning.

* [Métricas de corrida de pista durante o treino](how-to-track-experiments.md)
* [Otimizar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Arquitetura de referência para formação de aprendizagem profunda distribuída em Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
