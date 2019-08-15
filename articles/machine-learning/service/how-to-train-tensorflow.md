---
title: Treinar uma rede neural de aprendizado profundo com TensorFlow
titleSuffix: Azure Machine Learning service
description: Saiba como executar scripts de treinamento do TensorFlow em escala usando o serviço Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/10/2019
ms.custom: seodec18
ms.openlocfilehash: 41ebca7bd4ea299bda7e2d7a95edced583866527
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966798"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Crie um modelo de aprendizado profundo TensorFlow em escala com Azure Machine Learning

Este artigo mostra como executar seus scripts de treinamento do [TensorFlow](https://www.tensorflow.org/overview) em escala usando a classe [TensorFlow estimadora](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) do Azure Machine Learning. Este exemplo treina e registra um modelo TensorFlow para classificar dígitos manuscritos usando uma DNN (rede neural profunda).

Se você estiver desenvolvendo um modelo de TensorFlow do zero ou estiver trazendo um [modelo existente](how-to-deploy-existing-model.md) para a nuvem, você pode usar Azure Machine Learning para expandir os trabalhos de treinamento de software livre para criar, implantar, obter uma versão e monitorar modelos de nível de produção .

Saiba mais sobre o [aprendizado profundo em relação ao aprendizado de máquina](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Pré-requisitos

Execute este código em qualquer um destes ambientes:

 - VM do notebook Azure Machine Learning-não é necessário nenhum download ou instalação

     - Conclua [o tutorial: Ambiente de instalação e](tutorial-1st-experiment-sdk-setup.md) espaço de trabalho para criar um servidor de notebook dedicado pré-carregado com o SDK e o repositório de exemplo.
    - Na pasta Samples Deep Learning no servidor do notebook, localize um bloco de anotações concluído e expandido navegando até este diretório: **como usar-azureml > treinamento com detalhes > Train-hiperparameter-ajuste-deploy-com-tensorflow** pasta. 
 
 - Seu próprio servidor de Jupyter Notebook

    - [Instale o SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Crie um arquivo de configuração de espaço de trabalho](how-to-configure-environment.md#workspace).
    - [Baixar os arquivos de script de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` e`utils.py`
     
    Você também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) deste guia na página de exemplos do github. O notebook inclui seções expandidas que abrangem o ajuste de hiperparâmetro inteligente, implantação de modelo e widgets de notebook.

## <a name="set-up-the-experiment"></a>Configurar o experimento

Esta seção configura o teste de treinamento carregando os pacotes do python necessários, inicializando um espaço de trabalho, criando um experimento e carregando os dados de treinamento e os scripts de treinamento.

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

### <a name="initialize-a-workspace"></a>Inicializar um espaço de trabalho

O [espaço de trabalho de serviço do Azure Machine Learning](concept-workspace.md) é o recurso de nível superior para o serviço. Ele fornece um local centralizado para trabalhar com todos os artefatos que você criar. No SDK do Python, você pode acessar os artefatos do espaço de [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) trabalho criando um objeto.

Crie um objeto de espaço de `config.json` trabalho a partir do arquivo criado na [seção pré-requisitos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Criar um experimento de aprendizado profundo

Crie um experimento e uma pasta para manter seus scripts de treinamento. Neste exemplo, crie um experimento chamado "TF-mnist".

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Carregar conjunto de script e scripts

O [armazenamento](how-to-access-data.md) de dados é um local em que podem ser armazenados e acessados com a montagem ou a cópia dos dados no destino de computação. Cada espaço de trabalho fornece um repositório de armazenamento padrão. Carregue os dados e os scripts de treinamento para o datastore para que eles possam ser acessados facilmente durante o treinamento.

1. Baixe o conjunto de MNISTs localmente.

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. Carregue o conjunto de MNIST no repositório de armazenamento padrão.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. Carregue o script de treinamento do `tf_mnist.py`TensorFlow, o e o arquivo `utils.py`auxiliar,.

    ```Python
    shutil.copy('./tf_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>Criar um destino de computação

Crie um destino de computação para a execução do seu trabalho do TensorFlow. Neste exemplo, crie um cluster de computação Azure Machine Learning habilitado para GPU.

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

Para obter mais informações sobre destinos de computação, consulte o artigo [o que é um destino de computação](concept-compute-target.md) .

## <a name="create-a-tensorflow-estimator"></a>Criar um estimador TensorFlow

O [estimador do TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fornece uma maneira simples de iniciar um trabalho de treinamento do TensorFlow em um destino de computação.

O estimador TensorFlow é implementado por meio [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) da classe genérica, que pode ser usada para dar suporte a qualquer estrutura. Para obter mais informações sobre modelos de treinamento usando o estimador genérico, consulte [treinar modelos com Azure Machine Learning usando](how-to-train-ml-models.md) o estimador

Se o script de treinamento precisar de pacotes PIP ou Conda adicionais para execução, você poderá ter os pacotes instalados na imagem do Docker resultante passando seus nomes pelos `pip_packages` argumentos `conda_packages` e.

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

## <a name="submit-a-run"></a>Enviar uma execução

O [objeto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execução enquanto o trabalho está em execução e após sua conclusão.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

À medida que a execução é executada, ela passa pelos seguintes estágios:

- **Preparando**: Uma imagem do Docker é criada de acordo com o estimador do TensorFlow. A imagem é carregada no registro de contêiner do espaço de trabalho e armazenada em cache para execuções posteriores. Os logs também são transmitidos para o histórico de execução e podem ser exibidos para monitorar o progresso.

- **Dimensionamento**: O cluster tentará escalar verticalmente se o cluster de ia do lote exigir mais nós para executar a execução do que está disponível no momento.

- **Em execução**: Todos os scripts na pasta de script são carregados para o destino de computação, os armazenamentos de dados são montados ou copiados e o entry_script é executado. As saídas de stdout e a pasta./logs são transmitidas para o histórico de execução e podem ser usadas para monitorar a execução.

- **Pós-processamento**: A pasta./Outputs da execução é copiada para o histórico de execuções.

## <a name="register-or-download-a-model"></a>Registrar ou baixar um modelo

Depois de treinar o modelo, você poderá registrá-lo em seu espaço de trabalho. O registro de modelo permite que você armazene e versione seus modelos em seu espaço de trabalho para simplificar o [Gerenciamento e a implantação de modelos](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

Você também pode baixar uma cópia local do modelo usando o objeto Run. No script `mnist-tf.py`de treinamento, um objeto TensorFlow de proteção persiste o modelo para uma pasta local (local para o destino de computação). Você pode usar o objeto Run para baixar uma cópia.

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

O [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) estimador também dá suporte ao treinamento distribuído em clusters de CPU e GPU. Você pode executar facilmente trabalhos TensorFlow distribuídos e o serviço de Azure Machine Learning gerenciará a orquestração para você.

Azure Machine Learning serviço dá suporte a dois métodos de treinamento distribuído no TensorFlow:

- Treinamento distribuído [baseado em MPI](https://www.open-mpi.org/) usando a estrutura [Horovod](https://github.com/uber/horovod)
- [TensorFlow distribuído](https://www.tensorflow.org/deploy/distributed) nativo usando o método de servidor de parâmetros

### <a name="horovod"></a>Horovod

O [Horovod](https://github.com/uber/horovod) é uma estrutura de código-fonte aberto para treinamento distribuído desenvolvido pelo Uber. Ele oferece um caminho fácil para trabalhos de TensorFlow de GPU distribuídos.

Para usar Horovod, especifique um [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) objeto para o `distributed_training` parâmetro no Construtor TensorFlow. Esse parâmetro garante que a biblioteca Horovod esteja instalada para que você use em seu script de treinamento.

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

Para usar o método de servidor de parâmetros, [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) especifique um objeto `distributed_training` para o parâmetro no Construtor TensorFlow.

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

#### <a name="define-cluster-specifications-in-tf_config"></a>Definir especificações de cluster em ' TF_CONFIG '

Você também precisa dos endereços de rede e das portas do cluster para [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)o, portanto Azure Machine Learning define `TF_CONFIG` a variável de ambiente para você.

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

Para a API de alto [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) nível do TensorFlow, o TensorFlow `TF_CONFIG` analisa a variável e cria a especificação do cluster para você.

Para as APIs principais de nível inferior do TensorFlow para treinamento, analise `TF_CONFIG` a variável e compile `tf.train.ClusterSpec` o em seu código de treinamento.

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

Neste artigo, você treinou e registrou um modelo TensorFlow. Para saber como implantar um modelo em um cluster habilitado para GPU, prossiga para nosso artigo de implantação de modelo de GPU.

> [!div class="nextstepaction"]
> [Como e onde implantar modelos](how-to-deploy-and-where.md)
* [Executar a métrica durante o treinamento do Roteiro](how-to-track-experiments.md)
* [Otimizar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implementar um modelo preparado](how-to-deploy-and-where.md)
* [Arquitetura de referência para treinamento de aprendizado profundo distribuído no Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
