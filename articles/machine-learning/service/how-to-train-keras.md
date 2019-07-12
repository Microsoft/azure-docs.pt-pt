---
title: Dar formação e registe-se os modelos de Keras em execução no TensorFlow
titleSuffix: Azure Machine Learning service
description: Este artigo mostra como treinar e registar um modelo do Keras em execução no TensorFlow com o serviço Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 06/07/2019
ms.custom: seodec18
ms.openlocfilehash: 9d405b454d755e0c848e9422c8d4cf6e7c505b68
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840057"
---
# <a name="train-and-register-keras-models-at-scale-with-azure-machine-learning-service"></a>Dar formação e registar Keras modelos à escala com o serviço Azure Machine Learning

Este artigo mostra como treinar e registar um modelo do Keras criado no TensorFlow com o serviço Azure Machine Learning. Ele usa a popular [conjunto de dados MNIST](http://yann.lecun.com/exdb/mnist/) classificar manuscritos dígitos a utilizar uma rede neural profunda (DNN) criada com o [biblioteca de Python do Keras](https://keras.io) em execução na parte superior do [TensorFlow](https://www.tensorflow.org/overview) .

Keras é uma API com capacidade para executar a parte superior de outras estruturas DNN populares para simplificar o desenvolvimento de rede neural alto nível. Com o serviço do Azure Machine Learning, pode rapidamente aumentar horizontalmente trabalhos de treinamento com recursos de computação na cloud elástica. Também pode controlar a execuções de preparação, modelos de versão, implementar modelos e muito mais.

Quer esteja a desenvolver um modelo do Keras desde o início ou trazem um modelo existente para a cloud, serviço Azure Machine Learning pode ajudá-lo a criar modelos prontos para produção.

## <a name="prerequisites"></a>Pré-requisitos

Execute esse código em qualquer um destes ambientes:

 - Azure Machine Learning bloco de notas do VM - nenhuma downloads ou de uma instalação necessária

     - Concluir o [guia de introdução do bloco de notas com base na cloud](quickstart-run-cloud-notebook.md) para criar um servidor dedicado de bloco de notas pré-carregados com o SDK e o repositório de exemplo.
    - Na pasta de exemplos no servidor do bloco de notas, encontrar um bloco de notas concluído e expandido ao navegar para este diretório: **procedimentos-to-use-azureml > treinamento-com-aprendizagem profunda > train-hyperparameter-tune-deploy-with-keras** pasta. 
 
 - O seu servidor de bloco de notas do Jupyter

     - [Instalar o Azure Machine Learning SDK para Python](setup-create-workspace.md#sdk)
    - [Criar um ficheiro de configuração da área de trabalho](setup-create-workspace.md#write-a-configuration-file)
    - [Transferir os ficheiros de script de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` e `utils.py`
     
    Também pode encontrar um concluídos [versão do bloco de notas do Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) deste guia sobre a página de exemplos do GitHub. O bloco de notas inclui expandidas secções que abordam a otimização de hiper-parâmetros inteligente, a implementação de modelo e widgets de bloco de notas.

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

Crie uma experimentação e uma pasta para armazenar seus scripts de treinamento. Neste exemplo, crie uma experimentação chamada "keras mnist".

```Python
script_folder = './keras-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='keras-mnist')
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

1. Carregar o script de treinamento de Keras `keras_mnist.py`e o arquivo do auxiliar, `utils.py`.

    ```Python
    shutil.copy('./keras_mnist.py', script_folder)
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

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>Criar uma calculadora de TensorFlow e importar Keras

O [estimador de TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fornece uma forma simples de iniciar as tarefas de formação de TensorFlow no destino de computação. Uma vez que Keras é executado sobre o TensorFlow, pode utilizar o avaliador de TensorFlow e importar a biblioteca de Keras com o `pip_packages` argumento.

O avaliador de TensorFlow é implementado por meio de genérica [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) classe, que pode ser utilizado para suportar qualquer estrutura. Para obter mais informações sobre modelos com o avaliador de genérico de treinamento, consulte [formar modelos com o Azure Machine Learning utilizando estimator](how-to-train-ml-models.md)

```Python
script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.001
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='keras_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 pip_packages=['keras', 'matplotlib'],
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

## <a name="register-the-model"></a>Registe o modelo

Depois de ter preparado o modelo, pode registá-lo à área de trabalho. Registo do modelo permite-lhe armazenar e versão seus modelos na sua área de trabalho para simplificar [modelo de implementação e gestão](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

Também pode transferir uma cópia local do modelo. Isso pode ser útil para fazer o trabalho de validação de modelo adicional localmente. No script de treinamento, `mnist-keras.py`, um objeto de proteção do TensorFlow persistir o modelo para uma pasta local (local para o destino de computação). Pode utilizar o objeto de execução para transferir uma cópia do arquivo de dados.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, treinados e registado um modelo do Keras no serviço Azure Machine Learning. Para saber como implementar um modelo, continue para o nosso artigo de implementação do modelo.

> [!div class="nextstepaction"]
> [Como e onde implementar modelos](how-to-deploy-and-where.md)
