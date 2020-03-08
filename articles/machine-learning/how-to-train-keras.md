---
title: Treine modelos Keras de aprendizagem profunda
titleSuffix: Azure Machine Learning
description: Aprenda a treinar e registar um modelo de classificação de rede neural profunda keras em funcionamento no TensorFlow utilizando o Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: ba7976d602412037578d0a324916718b2d515aac
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355505"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning"></a>Treine e registe um modelo de classificação Keras com Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este artigo mostra-lhe como treinar e registar um modelo de classificação Keras construído no TensorFlow utilizando o Azure Machine Learning. Usa o popular conjunto de [dados MNIST](http://yann.lecun.com/exdb/mnist/) para classificar os dígitos manuscritos usando uma rede neural profunda (DNN) construída usando a [biblioteca Keras Python](https://keras.io) em cima do [TensorFlow](https://www.tensorflow.org/overview).

Keras é uma rede neural de alto nível, capaz de funcionar no topo de outros quadros populares de DNN para simplificar o desenvolvimento. Com o Azure Machine Learning, você pode rapidamente escalar trabalhos de formação usando recursos elásticos de computação em nuvem. Também pode acompanhar os seus treinos, modelos de versão, modelos de implementação e muito mais.

Quer esteja a desenvolver um modelo Keras a partir do solo ou se está a trazer um modelo existente para a nuvem, o Azure Machine Learning pode ajudá-lo a construir modelos prontos para a produção.

Consulte o [artigo conceptual](concept-deep-learning-vs-machine-learning.md) para obter informações sobre as diferenças entre a aprendizagem automática e a aprendizagem profunda.

## <a name="prerequisites"></a>Pré-requisitos

Execute este código em qualquer um destes ambientes:

- Instância computacional Azure Machine Learning - sem downloads ou instalação necessária

     - Complete o [Tutorial: Ambiente de configuração e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de portátil dedicado pré-carregado com o SDK e o repositório de amostras.
    - Na pasta de amostras no servidor de cadernos, encontre um caderno completo e expandido navegando para este diretório: **como usar-azureml > treino-com-deep-learning > pasta de metrímetro-metímetro-tune-deploy-with-keras.**

 - O seu próprio servidor jupyter Notebook

    - [Instale o SDK de Aprendizagem automática Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - Criar um ficheiro de [configuração do espaço de trabalho.](how-to-configure-environment.md#workspace)
    - [Descarregue os ficheiros](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) de script da amostra `mnist-keras.py` e `utils.py`

    Também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) deste guia na página de amostras gitHub. O caderno inclui secções expandidas que cobrem a finação inteligente do hiperparâmetro, a implementação do modelo e widgets de caderno.

## <a name="set-up-the-experiment"></a>Configurar a experiência

Esta secção configura a experiência de treino carregando os pacotes de python necessários, iniciando um espaço de trabalho, criando uma experiência, e carregando os dados de treino e treinando scripts.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importar as bibliotecas piton necessárias.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicializar um espaço de trabalho

O espaço de [trabalho Azure Machine Learning](concept-workspace.md) é o recurso de alto nível para o serviço. Proporciona-lhe um lugar centralizado para trabalhar com todos os artefactos que cria. No Python SDK, você pode aceder aos artefactos do espaço de trabalho criando um objeto [`workspace`.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)

Crie um objeto espaço de trabalho a partir do ficheiro `config.json` criado na [secção pré-requisitos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Criar uma experimentação

Crie uma experiência chamada "keras-mnist" no seu espaço de trabalho.

```Python
exp = Experiment(workspace=ws, name='keras-mnist')
```

<a name="data-upload"></a>
### <a name="create-a-file-dataset"></a>Criar um conjunto de dados de ficheiros

Um `FileDataset` objeto refere um ou vários ficheiros na sua loja de dados do espaço de trabalho ou urls públicos. Os ficheiros podem ser de qualquer formato, e a classe fornece-lhe a capacidade de descarregar ou montar os ficheiros para a sua computação. Ao criar um `FileDataset`, cria-se uma referência à localização da fonte de dados. Se aplicar quaisquer transformações no conjunto de dados, também serão armazenadas no conjunto de dados. Os dados permanecem na sua localização existente, pelo que não é incorrido nenhum custo extra de armazenamento. Consulte o guia como [orientar](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) o pacote `Dataset` para obter mais informações.

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

Utilize o método `register()` para registar o conjunto de dados no seu espaço de trabalho para que possam ser partilhados com outros, reutilizados em várias experiências, e referidos pelo nome no seu script de treino.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

## <a name="create-a-compute-target"></a>Criar um destino de computação

Crie um alvo de cálculo para o seu trabalho tensorFlow para continuar. Neste exemplo, crie um cluster de computação azure machine learning ativado por GPU.

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

Para obter mais informações sobre os alvos da computação, consulte o [que é um artigo-alvo de cálculo.](concept-compute-target.md)

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>Crie um estimador TensorFlow e importe Keras

O [estimador TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fornece uma forma simples de lançar trabalhos de formação TensorFlow no alvo da computação. Uma vez que keras corre em cima do TensorFlow, você pode usar o estimador TensorFlow e importar a biblioteca Keras usando o argumento `pip_packages`.

Primeiro obtenha os dados da loja de dados do espaço de trabalho usando a classe `Dataset`.

```python
dataset = Dataset.get_by_name(ws, 'mnist dataset')

# list the files referenced by mnist dataset
dataset.to_path()
```

O estimador TensorFlow é implementado através da classe [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) genérica, que pode ser usada para suportar qualquer enquadramento. Além disso, crie um dicionário `script_params` que contenha as definições do hiperparâmetro DNN. Para obter mais informações sobre modelos de formação utilizando o estimador genérico, consulte modelos de [comboio com Machine Learning Azure utilizando estimador](how-to-train-ml-models.md)

```python
from azureml.train.dnn import TensorFlow

script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
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

## <a name="submit-a-run"></a>Submeter uma corrida

O [objeto Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fornece a interface para o histórico de execução enquanto o trabalho está em execução e após a sua conclusão.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

À medida que a Corrida é executada, passa pelas seguintes fases:

- **Preparação**: É criada uma imagem de estivador de acordo com o estimador TensorFlow. A imagem é enviada para o registo de contentores do espaço de trabalho e cache para posteriores execuções. Os registos também são transmitidos para o histórico de execução e podem ser vistos para monitorizar o progresso.

- **Escalação**: O cluster tenta aumentar a escala se o cluster de IA do lote necessitar de mais nós para executar a execução do que atualmente disponível.

- **Execução**: Todos os scripts da pasta script são enviados para o alvo da computação, as lojas de dados são montadas ou copiadas e a entry_script é executada. As saídas de dstout e a pasta ./logs são transmitidas para o histórico de execução e podem ser usadas para monitorizar o execução.

- **Pós-Processamento**: A pasta ./saídas do executo é copiada para o histórico de execução.

## <a name="register-the-model"></a>Registe o modelo

Depois de ter treinado o modelo DNN, pode registá-lo no seu espaço de trabalho. O registo do modelo permite-lhe armazenar e verver os seus modelos no seu espaço de trabalho para simplificar a [gestão e implementação](concept-model-management-and-deployment.md)de modelos.

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

> [!TIP]
> O modelo que acabou de registar é implementado da mesma forma que qualquer outro modelo registado no Azure Machine Learning, independentemente do estimador utilizado para treinar. O modo de implementação contém uma secção sobre modelos de registo, mas pode saltar diretamente para [criar um alvo de computação](how-to-deploy-and-where.md#choose-a-compute-target) para implementação, uma vez que já tem um modelo registado.

Também pode descarregar uma cópia local do modelo. Isto pode ser útil para fazer trabalhos adicionais de validação de modelos localmente. No roteiro de treino, `mnist-keras.py`, um objeto de poupança TensorFlow persiste o modelo para uma pasta local (local para o alvo da computação). Pode utilizar o objeto Executar para descarregar uma cópia da datastore.

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

Neste artigo, treinou e registou um modelo Keras no Azure Machine Learning. Para aprender a implementar um modelo, continue para o nosso artigo de implementação de modelos.

> [!div class="nextstepaction"]
> [Como e onde implementar modelos](how-to-deploy-and-where.md)
* [Métricas de corrida de pista durante o treino](how-to-track-experiments.md)
* [Hiperparâmetros de melodia](how-to-tune-hyperparameters.md)
* [Implementar um modelo treinado](how-to-deploy-and-where.md)
* [Arquitetura de referência para formação distribuída de aprendizagem profunda em Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
