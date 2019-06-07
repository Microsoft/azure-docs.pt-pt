---
title: Dar formação e registe-se de modelos do TensorFlow
titleSuffix: Azure Machine Learning service
description: Este artigo mostra-lhe como dar formação e registar um modelo de TensorFlow usando o serviço Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.date: 05/28/2019
ms.custom: seodec18
ms.openlocfilehash: 4a6f9734a7b2b59035efcbb0f4e2d75f47e053be
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515600"
---
# <a name="train-and-register-tensorflow-models-at-scale-with-azure-machine-learning-service"></a>Dar formação e registar o TensorFlow modelos à escala com o serviço Azure Machine Learning

Este artigo mostra-lhe como dar formação e registar um modelo de TensorFlow usando o serviço Azure Machine Learning. Vamos utilizar o popular [conjunto de dados MNIST](http://yann.lecun.com/exdb/mnist/) classificar manuscritos dígitos a utilizar uma rede neural profunda criada com o [biblioteca de Python do TensorFlow](https://www.tensorflow.org/overview).

Com o serviço Azure Machine Learning, poderá rapidamente aumentar horizontalmente as tarefas de formação do código-fonte aberto com recursos de computação na cloud elástica. Também será capaz de controlar suas execuções de preparação, modelos de versão, implementar modelos e muito mais.

Quer esteja a desenvolver um modelo do TensorFlow desde o início ou trazem um modelo existente para a cloud, pode criar modelos prontos para produção com o serviço Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

- Instalar o [do Azure Machine Learning SDK para Python](setup-create-workspace.md#sdk). Opcional: criar um `config.json` ficheiro de configuração.
- Transfira o [arquivos de script de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` e `utils.py`

Também pode encontrar um concluídos [versão do bloco de notas do Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) deste guia na nossa página de exemplos do Github. O bloco de notas inclui expandidas secções que abordam a otimização de hiper-parâmetros inteligente e a implementação do modelo.

## <a name="set-up-the-experiment"></a>Configurar a experimentação

Esta secção define a experimentação de preparação por carregar os pacotes python necessários, a inicializar uma área de trabalho, criação de uma experimentação e carregar os dados de treinamento e os scripts de treinamento com o SDK de Python.

### <a name="import-packages"></a>Importar pacotes

Em primeiro lugar, é necessário importar as bibliotecas Python necessárias.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicializar uma área de trabalho

O [área de trabalho do Azure Machine Learning serviço](concept-workspace.md) é o recurso de nível superior para o serviço. Ele fornece um local centralizado para trabalhar com todos os artefactos de que criar. O SDK de Python, pode acessar os artefactos de área de trabalho através da criação de um [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objeto.

Se tiver concluído o passo opcional a [secção pré-requisitos](#prerequisites), pode utilizar `Workspace.from_config()` para criar rapidamente um objeto de área de trabalho dos detalhes armazenados no ficheiro de configuração.

```Python
ws = Workspace.from_config()
```

Também pode criar uma área de trabalho explicitamente:

```Python
ws = Workspace.create(name='<workspace-name>',
                      subscription_id='<azure-subscription-id>',
                      resource_group='<choose-a-resource-group>',
                      create_resource_group=True,
                      location='<select-location>' # For example: 'eastus2'
                      )
```

### <a name="create-an-experiment"></a>Criar uma experimentação

Crie uma experimentação e uma pasta para armazenar seus scripts de treinamento. Neste exemplo, crie uma experimentação chamada "tf-mnist".

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Carregar o conjunto de dados e scripts

O [arquivo de dados](how-to-access-data.md) é um local onde os dados podem ser armazenados e acedidos por montar ou copiar os dados para o destino de computação. Cada área de trabalho fornece um arquivo de dados padrão. Podemos carregá nossos dados e scripts de preparação para que possam ser facilmente acedidos durante o treinamento.

1. Transferir o conjunto de dados do MNIST localmente.

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. Carregar o conjunto de dados MNIST para o arquivo de dados padrão.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. Carregar o script de formação de TensorFlow `tf_mnist.py`e o arquivo do auxiliar, `utils.py`.

    ```Python
    shutil.copy('./tf_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>Criar um destino de computação

Crie um destino de computação para a sua tarefa do TensorFlow ser executado no. Neste exemplo, vamos criar um cluster de cálculo ativadas para GPU do Azure Machine Learning. Para obter uma lista de treinamento disponíveis destinos de computação, consulte [neste artigo](how-to-set-up-training-targets.md#compute-targets-for-training)

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

## <a name="create-a-tensorflow-estimator"></a>Criar uma calculadora de TensorFlow

O [estimador de TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fornece uma forma simples de iniciar uma tarefa de formação de TensorFlow num destino de computação. Ele criará uma imagem de docker com o TensorFlow instalado.

O avaliador de TensorFlow é implementado por meio de genérica [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) classe, que pode ser utilizado para suportar qualquer estrutura. Para obter mais informações sobre modelos com o avaliador de genérico de treinamento, consulte [formar modelos com o Azure Machine Learning utilizando estimator](how-to-train-ml-models.md)

Se o script de treinamento precisar pip adicional ou pacotes de conda para ser executado, pode ter os pacotes instalados na imagem de docker resultante ao transmitir os respetivos nomes através da `pip_packages` e `conda_packages` argumentos.

```Python
script_params = {
    '--data-folder': ws.get_default_datastore().as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Submeter uma execução

O [executar o objeto](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execuções, enquanto a tarefa está em execução e depois de concluída.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Como a execução é executada, ele avançará as seguintes fases:

- **A preparar**: Uma imagem do docker é criada, de acordo com o avaliador de TensorFlow. A imagem é carregada para container registry a área de trabalho e armazenada em cache para execuções posteriores. Os registos também são transmitidos para o histórico de execuções e podem ser visualizados para monitorizar o progresso.

- **Dimensionamento**: O cluster irá tentar aumentar verticalmente, se o cluster do Batch AI requer mais nós para executar a execução que estão atualmente disponíveis.

- **Executar**: Todos os scripts na pasta de script são carregados para o destino de computação, arquivos de dados são montados ou copiados e o entry_script é executado. Saídas de stdout e o. / pasta logs são transmitidas em fluxo para o histórico de execuções e pode ser utilizado para monitorizar a execução.

- **Pós-processamento**: A. / produz a pasta da execução é copiada para o histórico de execuções.

## <a name="register-or-download-a-model"></a>Registar ou transferir um modelo

Depois de ter preparado o modelo, pode registá-lo à área de trabalho. Registo do modelo permite-lhe armazenar e versão seus modelos na sua área de trabalho para simplificar [modelo de implementação e gestão](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

Também pode transferir uma cópia local do modelo, utilizando o objeto de execução. No script de treinamento `mnist-tf.py`, um objeto de proteção do TensorFlow persistir o modelo para uma pasta local (local para o destino de computação). Podemos usar o objeto de execução para transferir uma cópia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Formação distribuída

O [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) estimator também suporta a formação distribuída em clusters GPU e CPU. Pode facilmente executar tarefas de TensorFlow distribuídas e o serviço Azure Machine Learning irão gerir a orquestração para.

O serviço de Machine Learning do Azure suporta dois métodos de formação distribuída no TensorFlow:

- [Com base em MPI](https://www.open-mpi.org/) distribuído treinamento usando o [Horovod](https://github.com/uber/horovod) framework
- Nativo [distribuídas do TensorFlow](https://www.tensorflow.org/deploy/distributed) usando o método de servidor de parâmetro

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) é uma arquitetura de código-fonte aberto para treinamento distribuído desenvolvidos por Uber. Ele oferece uma forma fácil de tarefas de GPU TensorFlow distribuídas.

Para utilizar Horovod, especifique `mpi` para o `distributed_training` parâmetro no construtor de Calculadora TensorFlow. Horovod será instalada para que possa utilizar no seu script de treinamento.

```Python
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
                      use_gpu=True)
```

### <a name="parameter-server"></a>Servidor de parâmetros

Também pode executar [nativo distribuído TensorFlow](https://www.tensorflow.org/deploy/distributed), que utiliza o modelo de parâmetro de servidor. Nesse método, Treine num cluster de servidores de parâmetro e funções de trabalho. Os operadores de calculam os gradientes durante o treinamento, enquanto os servidores de parâmetro agregam os gradientes.

Para utilizar o método de servidor de parâmetro, especifique `ps` para o `distributed_training` parâmetro no construtor de Calculadora TensorFlow.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend=distributed_training,
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tfconfig"></a>Definir as especificações de cluster no 'TF_CONFIG'

Também precisará os endereços de rede e portas do cluster para o [ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), por isso, o Azure Machine Learning define o `TF_CONFIG` variável de ambiente para.

O `TF_CONFIG` variável de ambiente é uma cadeia de caracteres do JSON. Eis um exemplo da variável para um servidor de parâmetro:

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

Para o nível mais alto do TensorFlow [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) API, TensorFlow analisar isso `TF_CONFIG` variável e o cluster de compilação especificar para.

Para nível inferior APIs centrais do TensorFlow para treinamento, analisar o `TF_CONFIG` variável e compilação o `tf.train.ClusterSpec` em seu código de treinamento.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, treinados e registado um modelo do TensorFlow no serviço Azure Machine Learning. Para saber como implementar um modelo, continue para o nosso artigo de implementação do modelo.

> [!div class="nextstepaction"]
> [Como e onde implementar modelos](how-to-deploy-and-where.md)
