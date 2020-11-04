---
title: Treine modelos keras de aprendizagem profunda
titleSuffix: Azure Machine Learning
description: Saiba como treinar e registar um modelo de classificação de rede neural profunda keras em execução no TensorFlow usando Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: c6b65c97fd87d4e3ed84c837d2702395091097fa
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93308056"
---
# <a name="train-keras-models-at-scale-with-azure-machine-learning"></a>Treinar modelos Keras em escala com Azure Machine Learning

Neste artigo, aprenda a executar os seus scripts de treinamento Keras com Azure Machine Learning.

O código de exemplo neste artigo mostra-lhe como treinar e registar um modelo de classificação Keras construído com o backend TensorFlow com Azure Machine Learning. Utiliza o popular [conjunto de dados MNIST](http://yann.lecun.com/exdb/mnist/) para classificar dígitos manuscritos utilizando uma rede neural profunda (DNN) construída utilizando a [biblioteca Keras Python](https://keras.io) em execução em cima do [TensorFlow.](https://www.tensorflow.org/overview)

Keras é uma API de rede neural de alto nível capaz de executar o topo de outros quadros populares do DNN para simplificar o desenvolvimento. Com o Azure Machine Learning, você pode rapidamente escalar os trabalhos de formação usando recursos de computação em nuvem elástica. Também pode acompanhar as suas corridas de treino, modelos de versão, implementar modelos e muito mais.

Quer esteja a desenvolver um modelo Keras a partir do solo ou se está a trazer um modelo existente para a nuvem, o Azure Machine Learning pode ajudá-lo a construir modelos prontos para a produção.

> [!NOTE]
> Se estiver a utilizar o keras API **tf.keras** incorporado no TensorFlow e não o pacote Keras autónomo, consulte os [modelos Train TensorFlow](how-to-train-tensorflow.md).

## <a name="prerequisites"></a>Pré-requisitos

Executar este código em qualquer um destes ambientes:

- Exemplo de computação Azure Machine Learning - sem transferências ou instalação necessárias

     - Complete o [Tutorial: Ambiente de configuração e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de caderno dedicado pré-carregado com o SDK e o repositório de amostras.
    - Na pasta de amostras do servidor de cadernos, encontre um caderno completo e expandido navegando para este diretório: **como usar-azureml > estruturas ml > queras > pasta de ajuste de forma-hiperparametér-de-formação-com-keras.**

 - O seu próprio servidor de cadernos Jupyter

    - [Instale o Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.15.0).
    - [Crie um ficheiro de configuração do espaço de trabalho.](how-to-configure-environment.md#workspace)
    - [Descarregue os ficheiros de script de](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras) `keras_mnist.py` amostra e `utils.py`

    Também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) deste guia na página de amostras do GitHub. O caderno inclui secções expandidas que cobrem afinação inteligente de hiperparímetros, implantação de modelos e widgets de portátil.

## <a name="set-up-the-experiment"></a>Configurar a experiência

Esta secção define a experiência de formação carregando os pacotes Python necessários, inicializando um espaço de trabalho, criando o Dataset de Arquivo para os dados de formação de entrada, criando o alvo do cálculo e definindo o ambiente de treino.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importe as bibliotecas python necessárias.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Inicializar um espaço de trabalho

O espaço de [trabalho Azure Machine Learning](concept-workspace.md) é o recurso de alto nível para o serviço. Proporciona-lhe um lugar centralizado para trabalhar com todos os artefactos que cria. No Python SDK, você pode aceder aos artefactos do espaço de trabalho criando um [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) objeto.

Crie um objeto de espaço de trabalho a partir do `config.json` ficheiro criado na secção [pré-requisitos](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-file-dataset"></a>Criar um conjunto de dados de ficheiros

Um `FileDataset` objeto refere um ou vários ficheiros na sua loja de dados do espaço de trabalho ou urls públicos. Os ficheiros podem ser de qualquer formato, e a classe fornece-lhe a capacidade de descarregar ou montar os ficheiros no seu cálculo. Ao criar uma `FileDataset` , cria uma referência à localização da fonte de dados. Se aplicar alguma transformação no conjunto de dados, eles também serão armazenados no conjunto de dados. Os dados permanecem na sua localização existente, pelo que não é incorrido qualquer custo extra de armazenamento. Consulte o guia [de como fazer](./how-to-create-register-datasets.md) o pacote para obter mais `Dataset` informações.

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

Pode utilizar o `register()` método para registar o conjunto de dados no seu espaço de trabalho para que possam ser partilhados com outros, reutilizados em várias experiências e referidos pelo nome no seu script de treino.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

### <a name="create-a-compute-target"></a>Criar um alvo de computação

Crie um alvo de computação para o seu trabalho de treino. Neste exemplo, crie um cluster de cálculo de aprendizagem automática Azure ativado pela GPU.

```Python
cluster_name = "gpu-cluster"

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

### <a name="define-your-environment"></a>Defina o seu ambiente

Defina o Ambiente Azure [ML](concept-environments.md) que engloba as dependências do seu script de treino.

Primeiro, defina as suas dependências conda num ficheiro YAML; neste exemplo, o ficheiro é nomeado `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.0.0
  - keras<=2.3.1
  - matplotlib
```

Crie um ambiente Azure ML a partir desta especificação ambiente conda. O ambiente será embalado num contentor Docker em tempo de execução.

Por predefinição, se não for especificada nenhuma imagem de base, o Azure ML utilizará uma imagem cpU `azureml.core.environment.DEFAULT_CPU_IMAGE` como imagem base. Uma vez que este exemplo executa o treino num cluster de GPU, você precisará especificar uma imagem base da GPU que tenha os condutores e dependências de GPU necessários. Azure ML mantém um conjunto de imagens de base publicadas no Microsoft Container Registry (MCR) que pode utilizar, consulte o [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) GitHub repo para obter mais informações.

```python
from azureml.core import Environment

keras_env = Environment.from_conda_specification(name='keras-env', file_path='conda_dependencies.yml')

# Specify a GPU base image
keras_env.docker.enabled = True
keras_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.0-cudnn7-ubuntu18.04'
```

Para obter mais informações sobre a criação e utilização de ambientes, consulte [Criar e utilizar ambientes de software em Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Configure e submeta a sua formação

### <a name="create-a-scriptrunconfig"></a>Criar um ScriptRunConfig
Primeiro obtenha os dados da loja de dados do espaço de trabalho usando a `Dataset` classe.

```python
dataset = Dataset.get_by_name(ws, 'mnist-dataset')

# list the files referenced by mnist-dataset
dataset.to_path()
```

Crie um objeto ScriptRunConfig para especificar os detalhes de configuração do seu trabalho de treino, incluindo o seu script de treino, ambiente a utilizar e o alvo de computação para executar.

Quaisquer argumentos para o seu script de treino serão passados através da linha de comando, se especificado no `arguments` parâmetro. O DatasetConsumptionConfig para o nosso Dataset de Ficheiros é passado como um argumento para o script de formação, para o `--data-folder` argumento. A Azure ML resolverá este DatasetConsumptionConfig para o ponto de montagem da loja de dados de suporte, que pode então ser acedido a partir do script de treino.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 50,
        '--first-layer-neurons', 300,
        '--second-layer-neurons', 100,
        '--learning-rate', 0.001]

src = ScriptRunConfig(source_directory=script_folder,
                      script='keras_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=keras_env)
```

Para obter mais informações sobre a configuração de empregos com scriptRunConfig, consulte [Configure e submeta cursos de formação.](how-to-set-up-training-targets.md)

> [!WARNING]
> Se já usou o estimador tensorFlow para configurar os seus trabalhos de formação keras, por favor, note que os Estimadores serão depreciados numa futura versão do Azure ML SDK. Com Azure ML SDK >= 1.15.0, o ScriptRunConfig é a forma recomendada de configurar trabalhos de formação, incluindo aqueles que usam quadros DL.

### <a name="submit-your-run"></a>Submeta a sua corrida

O [objeto Run](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) fornece a interface para o histórico de execução enquanto o trabalho está em execução e depois de concluído.

```Python
run = Experiment(workspace=ws, name='keras-mnist').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>O que acontece durante a execução da execução
À medida que a corrida é executada, passa pelas seguintes fases:

- **Preparação:** Uma imagem de estivador é criada de acordo com o ambiente definido. A imagem é enviada para o registo de contentores do espaço de trabalho e em cache para posteriores execuções. Os registos também são transmitidos para o histórico de execução e podem ser vistos para monitorizar o progresso. Se um ambiente curado for especificado, em vez disso, a imagem em cache que o ambiente curado será usado.

- **Dimensionamento** : O cluster tenta aumentar se o cluster de AI do lote necessitar de mais nós para executar a execução do que estão atualmente disponíveis.

- **Execução** : Todos os scripts na pasta do script são carregados para o alvo do cálculo, as lojas de dados são montadas ou copiadas, e a `script` é executada. As saídas da sestada e da pasta **./logs** são transmitidas para o histórico de execução e podem ser utilizadas para monitorizar a execução.

- **Pós-Processamento** : A pasta **./outputs** da execução é copiada para o histórico de execução.

## <a name="register-the-model"></a>Registar o modelo

Depois de ter treinado o modelo, pode registá-lo no seu espaço de trabalho. O registo de modelos permite-lhe armazenar e versar os seus modelos no seu espaço de trabalho para simplificar a [gestão e implementação de modelos.](concept-model-management-and-deployment.md)

```Python
model = run.register_model(model_name='keras-mnist', model_path='outputs/model')
```

> [!TIP]
> A implementação como-fazer contém uma secção sobre os modelos de registo, mas pode saltar diretamente para [criar um alvo de computação](how-to-deploy-and-where.md#choose-a-compute-target) para implementação, uma vez que já tem um modelo registado.

Também pode baixar uma cópia local do modelo. Isto pode ser útil para fazer trabalhos adicionais de validação de modelos localmente. No script de treino, `keras_mnist.py` um objeto de poupança TensorFlow persiste o modelo para uma pasta local (local ao alvo do cálculo). Pode utilizar o objeto Executar para descarregar uma cópia do histórico de execução.

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

* [Como e onde implementar modelos](how-to-deploy-and-where.md)
* [Métricas de corrida de pista durante o treino](how-to-track-experiments.md)
* [Otimizar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implementar um modelo treinado](how-to-deploy-and-where.md)
* [Arquitetura de referência para formação de aprendizagem profunda distribuída em Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)