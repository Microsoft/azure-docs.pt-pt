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
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: 9961129805d133c4512e40e4c8be80185316a1ce
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074913"
---
# <a name="train-and-register-tensorflow-models-at-scale-with-azure-machine-learning-service"></a>Dar formação e registar o TensorFlow modelos à escala com o serviço Azure Machine Learning

Este artigo mostra-lhe como dar formação e registar um modelo de TensorFlow usando o serviço Azure Machine Learning. Ele usa a popular [conjunto de dados MNIST](http://yann.lecun.com/exdb/mnist/) classificar manuscritos dígitos a utilizar uma rede neural profunda criada com o [biblioteca de Python do TensorFlow](https://www.tensorflow.org/overview).

TensorFlow é uma estrutura de computacional de código-fonte aberto frequentemente utilizada para criar redes neurais profundas (DNN). Com o serviço do Azure Machine Learning, pode rapidamente aumentar horizontalmente trabalhos de treinamento de código-fonte aberto com recursos de computação na cloud elástica. Também pode controlar a execuções de preparação, modelos de versão, implementar modelos e muito mais.

Quer esteja a desenvolver um modelo do TensorFlow desde o início ou trazem um modelo existente para a cloud, serviço Azure Machine Learning pode ajudá-lo a criar modelos prontos para produção.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.
- [Instalar o Azure Machine Learning SDK para Python](setup-create-workspace.md#sdk)
- [Criar um ficheiro de configuração da área de trabalho](setup-create-workspace.md#write-a-configuration-file)
- [Transferir os ficheiros de script de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` e `utils.py`

Também pode encontrar um concluídos [versão do bloco de notas do Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) deste guia na página de exemplos do GitHub. O bloco de notas inclui expandidas secções que abordam a otimização de hiper-parâmetros inteligente, a implementação de modelo e widgets de bloco de notas.

## <a name="set-up-the-experiment"></a>Configurar a experimentação

Esta secção define a experimentação de preparação por carregar os pacotes python necessários, a inicializar uma área de trabalho, criação de uma experimentação e carregar os dados de treinamento e os scripts de preparação.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importe as bibliotecas Python necessárias.

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

Criar um objeto de área de trabalho a partir da `config.json` ficheiro criado no [secção pré-requisitos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Criar uma experimentação

Crie uma experimentação e uma pasta para armazenar seus scripts de treinamento. Neste exemplo, crie uma experimentação chamada "tf-mnist".

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Carregar o conjunto de dados e scripts

O [arquivo de dados](how-to-access-data.md) é um local onde os dados podem ser armazenados e acedidos por montar ou copiar os dados para o destino de computação. Cada área de trabalho fornece um arquivo de dados padrão. Carregar os dados e os scripts de preparação para o arquivo de dados para que possam ser facilmente acedidos durante o treinamento.

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

Crie um destino de computação para a sua tarefa do TensorFlow ser executado no. Neste exemplo, crie um cluster de computação do Azure Machine Learning ativadas para GPU.

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

Para obter mais informações sobre os destinos de computação, consulte a [o que é um destino de computação](concept-compute-target.md) artigo.

## <a name="create-a-tensorflow-estimator"></a>Criar uma calculadora de TensorFlow

O [estimador de TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fornece uma forma simples de iniciar uma tarefa de formação de TensorFlow num destino de computação.

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

Como a execução é executada, executa as seguintes fases:

- **A preparar**: Uma imagem do docker é criada, de acordo com o avaliador de TensorFlow. A imagem é carregada para container registry a área de trabalho e armazenada em cache para execuções posteriores. Os registos também são transmitidos para o histórico de execuções e podem ser visualizados para monitorizar o progresso.

- **Dimensionamento**: O cluster tenta aumentar verticalmente, se o cluster do Batch AI requer mais nós para executar a execução que estão atualmente disponíveis.

- **Executar**: Todos os scripts na pasta de script são carregados para o destino de computação, arquivos de dados são montados ou copiados e o entry_script é executado. Saídas de stdout e o. / pasta logs são transmitidas em fluxo para o histórico de execuções e pode ser utilizado para monitorizar a execução.

- **Pós-processamento**: A. / produz a pasta da execução é copiada para o histórico de execuções.

## <a name="register-or-download-a-model"></a>Registar ou transferir um modelo

Depois de ter preparado o modelo, pode registá-lo à área de trabalho. Registo do modelo permite-lhe armazenar e versão seus modelos na sua área de trabalho para simplificar [modelo de implementação e gestão](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

Também pode transferir uma cópia local do modelo, utilizando o objeto de execução. No script de treinamento `mnist-tf.py`, um objeto de proteção do TensorFlow persistir o modelo para uma pasta local (local para o destino de computação). Pode usar o objeto de execução para transferir uma cópia.

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

Para utilizar Horovod, especifique um [ `MpiConfiguration` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) de objeto para o `distributed_training` parâmetro no construtor do TensorFlow. Esse parâmetro garante que a biblioteca Horovod está instalada para que possa utilizar no seu script de treinamento.

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

Para utilizar o método de servidor de parâmetro, especifique um [ `TensorflowConfiguration` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) de objeto para o `distributed_training` parâmetro no construtor do TensorFlow.

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
                      distributed_training=distributed_training,
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tfconfig"></a>Definir as especificações de cluster no 'TF_CONFIG'

Também precisa de endereços de rede e portas de cluster para o [ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), por isso, o Azure Machine Learning define o `TF_CONFIG` variável de ambiente para.

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

Para o nível mais alto do TensorFlow [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) TensorFlow de API, analisa o `TF_CONFIG` variável e compilações do cluster especificar para.

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
