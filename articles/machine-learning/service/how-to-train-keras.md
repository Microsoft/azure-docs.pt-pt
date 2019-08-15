---
title: Treinar a rede neural de aprendizado profundo com Keras
titleSuffix: Azure Machine Learning service
description: Saiba como treinar e registrar um modelo de classificação de rede neural Keras profundo em execução no TensorFlow usando o serviço Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: e7646330d9d89d5257a991b5095b7b6814aa3ba9
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966822"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning-service"></a>Treinar e registrar um modelo de classificação Keras com o serviço Azure Machine Learning

Este artigo mostra como treinar e registrar um modelo de classificação Keras criado em TensorFlow usando o serviço Azure Machine Learning. Ele usa o popular [conjunto de MNIST](http://yann.lecun.com/exdb/mnist/) para classificar dígitos manuscritos usando uma DNN (rede neural profunda) criada usando a [biblioteca Keras Python](https://keras.io) em execução na parte superior do [TensorFlow](https://www.tensorflow.org/overview).

Keras é uma API de rede neural de alto nível capaz de executar a parte superior de outras estruturas de DNN populares para simplificar o desenvolvimento. Com o serviço Azure Machine Learning, você pode expandir rapidamente os trabalhos de treinamento usando recursos de computação em nuvem elástico. Você também pode acompanhar suas execuções de treinamento, modelos de versão, implantar modelos e muito mais.

Se você estiver desenvolvendo um modelo de Keras do zero ou estiver trazendo um modelo existente para a nuvem, Azure Machine Learning serviço poderá ajudá-lo a criar modelos prontos para produção.

Consulte o [artigo conceitual](concept-deep-learning-vs-machine-learning.md) para obter informações sobre as diferenças entre o aprendizado de máquina e o aprendizado profundo.

## <a name="prerequisites"></a>Pré-requisitos

Execute este código em qualquer um destes ambientes:

 - VM do notebook Azure Machine Learning-não é necessário nenhum download ou instalação

     - Conclua [o tutorial: Ambiente de instalação e](tutorial-1st-experiment-sdk-setup.md) espaço de trabalho para criar um servidor de notebook dedicado pré-carregado com o SDK e o repositório de exemplo.
    - Na pasta Samples no servidor do notebook, encontre um notebook completo e expandido navegando até este diretório: **instruções-uso-azureml > treinamento com aprendizado profundo > Train-hiperparameter-ajuste-deploy-com-Keras** pasta.

 - Seu próprio servidor de Jupyter Notebook

    - [Instale o SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Crie um arquivo de configuração de espaço de trabalho](how-to-configure-environment.md#workspace).
    - [Baixar os arquivos de script de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` e`utils.py`

    Você também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) deste guia na página de exemplos do github. O notebook inclui seções expandidas que abrangem o ajuste de hiperparâmetro inteligente, implantação de modelo e widgets de notebook.

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

### <a name="create-an-experiment"></a>Criar uma experimentação

Crie um experimento e uma pasta para manter seus scripts de treinamento. Neste exemplo, crie um experimento chamado "Keras-mnist".

```Python
script_folder = './keras-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='keras-mnist')
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

1. Carregue o script de treinamento do `keras_mnist.py`Keras, o e o arquivo `utils.py`auxiliar,.

    ```Python
    shutil.copy('./keras_mnist.py', script_folder)
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

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>Criar um estimador TensorFlow e importar Keras

O [estimador TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fornece uma maneira simples de iniciar trabalhos de treinamento do TensorFlow no destino de computação. Como o Keras é executado na parte superior do TensorFlow, você pode usar o estimador TensorFlow e importar a `pip_packages` biblioteca Keras usando o argumento.

O estimador TensorFlow é implementado por meio [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) da classe genérica, que pode ser usada para dar suporte a qualquer estrutura. Além disso, crie um `script_params` dicionário que contenha as configurações de hiperparâmetro DNN. Para obter mais informações sobre modelos de treinamento usando o estimador genérico, consulte [treinar modelos com Azure Machine Learning usando](how-to-train-ml-models.md) o estimador

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

## <a name="register-the-model"></a>Registe o modelo

Depois de treinar o modelo DNN, você poderá registrá-lo em seu espaço de trabalho. O registro de modelo permite que você armazene e versione seus modelos em seu espaço de trabalho para simplificar o [Gerenciamento e a implantação de modelos](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

Você também pode baixar uma cópia local do modelo. Isso pode ser útil para fazer o trabalho de validação de modelo adicional localmente. No script de treinamento, `mnist-keras.py`, um objeto TensorFlow de proteção persiste o modelo para uma pasta local (local para o destino de computação). Você pode usar o objeto executar para baixar uma cópia do repositório de armazenamento.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você treinou e registrou um modelo Keras no serviço Azure Machine Learning. Para saber como implantar um modelo, continue em nosso artigo de implantação de modelo.

> [!div class="nextstepaction"]
> [Como e onde implantar modelos](how-to-deploy-and-where.md)
* [Executar a métrica durante o treinamento do Roteiro](how-to-track-experiments.md)
* [Otimizar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implementar um modelo preparado](how-to-deploy-and-where.md)
* [Arquitetura de referência para treinamento de aprendizado profundo distribuído no Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
