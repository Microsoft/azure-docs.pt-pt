---
title: Treine e implemente um modelo TensorFlow
titleSuffix: Azure Machine Learning
description: Saiba como o Azure Machine Learning permite-lhe escalar um trabalho de treino tensorFlow usando recursos de computação em nuvem elástica.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 41231e19960edfe1a4f0521b8738fa62a463c927
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2020
ms.locfileid: "97796470"
---
# <a name="train-tensorflow-models-at-scale-with-azure-machine-learning"></a>Trem tensorFlow modelos em escala com Azure Machine Learning

Neste artigo, aprenda a executar os seus scripts de treino [TensorFlow](https://www.tensorflow.org/overview) em escala utilizando a Azure Machine Learning.

Este exemplo treina e regista um modelo TensorFlow para classificar dígitos manuscritos utilizando uma rede neural profunda (DNN).

Quer esteja a desenvolver um modelo TensorFlow a partir do solo ou se está a trazer um [modelo existente](how-to-deploy-existing-model.md) para a nuvem, pode utilizar o Azure Machine Learning para criar trabalhos de formação de código aberto para construir, implantar, ver versão e monitorizar modelos de nível de produção.

## <a name="prerequisites"></a>Pré-requisitos

Executar este código em qualquer um destes ambientes:

 - Exemplo de computação Azure Machine Learning - sem transferências ou instalação necessárias

     - Complete o [Tutorial: Ambiente de configuração e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de caderno dedicado pré-carregado com o SDK e o repositório de amostras.
    - Nas amostras da pasta de aprendizagem profunda no servidor do portátil, encontre um caderno completo e expandido navegando para este diretório: **como usar-azureml > estruturas ml > tensorflow > pasta de ajuste de linha-hiperparametr-implement-com-tensorflow.** 
 
 - O seu próprio servidor de cadernos Jupyter

    - [Instale o Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.15.0).
    - [Crie um ficheiro de configuração do espaço de trabalho.](how-to-configure-environment.md#workspace)
    - [Descarregue os ficheiros de script de](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow) `tf_mnist.py` amostra e `utils.py`
     
    Também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) deste guia na página de amostras do GitHub. O caderno inclui secções expandidas que cobrem afinação inteligente de hiperparímetros, implantação de modelos e widgets de portátil.

## <a name="set-up-the-experiment"></a>Configurar a experiência

Esta secção estabelece a experiência de treino carregando os pacotes Python necessários, inicializando um espaço de trabalho, criando o alvo de computação, e definindo o ambiente de treino.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importe as bibliotecas python necessárias.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core import Environment

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

Utilize o `register()` método para registar o conjunto de dados no seu espaço de trabalho para que possam ser partilhados com outros, reutilizados em várias experiências e referidos pelo nome no seu script de treino.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

### <a name="create-a-compute-target"></a>Criar um alvo de computação

Crie um alvo de computação para o seu trabalho TensorFlow para executar. Neste exemplo, crie um cluster de cálculo de aprendizagem automática Azure ativado pela GPU.

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

Para definir o [Ambiente](concept-environments.md) Azure ML que engloba as dependências do seu script de treino, você pode definir um ambiente personalizado ou usar um ambiente curado Azure ML.

#### <a name="use-a-curated-environment"></a>Use um ambiente curado
A azure ML fornece ambientes pré-construídos e curados se você não quiser definir o seu próprio ambiente. AZure ML tem vários ambientes com curadoria de CPU e GPU para TensorFlow correspondentes a diferentes versões do TensorFlow. Para mais informações, consulte [aqui.](resource-curated-environments.md)

Se quiser utilizar um ambiente curado, pode executar o seguinte comando:

```python
curated_env_name = 'AzureML-TensorFlow-2.2-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
```

Para ver os pacotes incluídos no ambiente curado, você pode escrever as dependências conda para o disco:
```python
tf_env.save_to_directory(path=curated_env_name)
```

Certifique-se de que o ambiente curado inclui todas as dependências exigidas pelo seu roteiro de treino. Caso contrário, terá de modificar o ambiente para incluir as dependências em falta. Note que, se o ambiente for modificado, terá de lhe dar um novo nome, uma vez que o prefixo 'AzureML' está reservado para ambientes com curadoria. Se modificar o ficheiro IML das dependências conda, pode criar um novo ambiente a partir dele com um novo nome, por exemplo:
```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')
```

Se em vez disso modificou diretamente o objeto ambiental curado, pode clonar esse ambiente com um novo nome:
```python
tf_env = tf_env.clone(new_name='tensorflow-2.2-gpu')
```

#### <a name="create-a-custom-environment"></a>Criar um ambiente personalizado

Também pode criar o seu próprio ambiente Azure ML que encapsula as dependências do seu script de treino.

Primeiro, defina as suas dependências conda num ficheiro YAML; neste exemplo, o ficheiro é nomeado `conda_dependencies.yml` .

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
```

Crie um ambiente Azure ML a partir desta especificação ambiente conda. O ambiente será embalado num contentor Docker em tempo de execução.

Por predefinição, se não for especificada nenhuma imagem de base, o Azure ML utilizará uma imagem cpU `azureml.core.environment.DEFAULT_CPU_IMAGE` como imagem base. Uma vez que este exemplo executa o treino num cluster de GPU, você precisará especificar uma imagem base da GPU que tenha os condutores e dependências de GPU necessários. Azure ML mantém um conjunto de imagens de base publicadas no Microsoft Container Registry (MCR) que pode utilizar, consulte o [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) GitHub repo para obter mais informações.

```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
tf_env.docker.enabled = True
tf_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Opcionalmente, pode capturar todas as suas dependências diretamente numa imagem personalizada do Docker ou Dockerfile, e criar o seu ambiente a partir daí. Para mais informações, consulte [Train com imagem personalizada.](how-to-train-with-custom-image.md)

Para obter mais informações sobre a criação e utilização de ambientes, consulte [Criar e utilizar ambientes de software em Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Configure e submeta a sua formação

### <a name="create-a-scriptrunconfig"></a>Criar um ScriptRunConfig

Crie um objeto [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) para especificar os detalhes de configuração do seu trabalho de treino, incluindo o seu script de treino, ambiente a utilizar e o alvo de computação para executar. Quaisquer argumentos para o seu script de treino serão passados através da linha de comando, se especificado no `arguments` parâmetro.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 64,
        '--first-layer-neurons', 256,
        '--second-layer-neurons', 128,
        '--learning-rate', 0.01]

src = ScriptRunConfig(source_directory=script_folder,
                      script='tf_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
```

> [!WARNING]
> A Azure Machine Learning executa scripts de formação copiando todo o diretório de origem. Se tiver dados sensíveis que não pretende fazer o upload, utilize um [ficheiro .ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) ou não o inclua no diretório de origem . Em vez disso, aceda aos seus dados utilizando um [conjunto de dados](how-to-train-with-datasets.md)Azure ML .

Para obter mais informações sobre a configuração de empregos com scriptRunConfig, consulte [Configure e submeta cursos de formação.](how-to-set-up-training-targets.md)

> [!WARNING]
> Se já usou o estimador TensorFlow para configurar os seus trabalhos de formação tensorFlow, por favor, note que os Estimadores foram depreciados a partir da versão 1.19.0 SDK. Com Azure ML SDK >= 1.15.0, o ScriptRunConfig é a forma recomendada de configurar trabalhos de formação, incluindo aqueles que usam quadros de aprendizagem profunda. Para questões de migração comuns, consulte o [guia de migração ScriptRunConfig](how-to-migrate-from-estimators-to-scriptrunconfig.md).

### <a name="submit-a-run"></a>Submeter uma corrida

O [objeto Run](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) fornece a interface para o histórico de execução enquanto o trabalho está em execução e depois de concluído.

```Python
run = Experiment(workspace=ws, name='Tutorial-TF-Mnist').submit(src)
run.wait_for_completion(show_output=True)
```
### <a name="what-happens-during-run-execution"></a>O que acontece durante a execução da execução
À medida que a corrida é executada, passa pelas seguintes fases:

- **Preparação:** Uma imagem de estivador é criada de acordo com o ambiente definido. A imagem é enviada para o registo de contentores do espaço de trabalho e em cache para posteriores execuções. Os registos também são transmitidos para o histórico de execução e podem ser vistos para monitorizar o progresso. Se um ambiente curado for especificado, em vez disso, a imagem em cache que o ambiente curado será usado.

- **Dimensionamento**: O cluster tenta aumentar se o cluster de AI do lote necessitar de mais nós para executar a execução do que estão atualmente disponíveis.

- **Execução**: Todos os scripts na pasta do script são carregados para o alvo do cálculo, as lojas de dados são montadas ou copiadas, e a `script` é executada. As saídas da sestada e da pasta **./logs** são transmitidas para o histórico de execução e podem ser utilizadas para monitorizar a execução.

- **Pós-Processamento**: A pasta **./outputs** da execução é copiada para o histórico de execução.

## <a name="register-or-download-a-model"></a>Registe-se ou descarregue um modelo

Depois de ter treinado o modelo, pode registá-lo no seu espaço de trabalho. O registo de modelos permite-lhe armazenar e versar os seus modelos no seu espaço de trabalho para simplificar a [gestão e implementação de modelos.](concept-model-management-and-deployment.md) Opcional: especificando os parâmetros `model_framework` `model_framework_version` , `resource_configuration` e, a implementação do modelo sem código fica disponível. Isto permite-lhe implementar diretamente o seu modelo como um serviço web a partir do modelo registado, e o `ResourceConfiguration` objeto define o recurso compute para o serviço web.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='2.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Também pode descarregar uma cópia local do modelo utilizando o objeto Executar. No script de `tf_mnist.py` treino, um objeto de poupança TensorFlow persiste o modelo para uma pasta local (local ao alvo do cálculo). Pode utilizar o objeto Executar para descarregar uma cópia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)
run.download_files(prefix='outputs/model', output_directory='./model', append_prefix=False)
```

## <a name="distributed-training"></a>Preparação distribuída

O Azure Machine Learning também suporta trabalhos de tensorFlow distribuídos em vários nós para que possa escalar as suas cargas de trabalho de formação. Pode executar facilmente trabalhos de TensorFlow distribuídos e a Azure ML gerirá a orquestração para si.

A Azure ML suporta a execução de postos de trabalho distribuídos no TensorFlow com a API de formação distribuída incorporada da Horovod e da TensorFlow.

### <a name="horovod"></a>Horovod
[A Horovod](https://github.com/uber/horovod) é uma fonte aberta, todas reduzem o enquadramento para a formação distribuída desenvolvida pela Uber. Oferece um caminho fácil para escrever o código TensorFlow distribuído para o treino.

O seu código de treino terá de ser instrumentalizado com o Horovod para treino distribuído. Para obter mais informações utilizando o Horovod com o TensorFlow, consulte a documentação horovod:

Para obter mais informações sobre a utilização de Horovod com TensorFlow, consulte a documentação horovod:

* [Horovod com TensorFlow](https://github.com/horovod/horovod/blob/master/docs/tensorflow.rst)
* [Horovod com API keras da TensorFlow](https://github.com/horovod/horovod/blob/master/docs/keras.rst)

Além disso, certifique-se de que o seu ambiente de treino inclui o pacote **horovod.** Se estiver a utilizar um ambiente com curadoria do TensorFlow, o horovod já está incluído como uma das dependências. Se estiver a utilizar o seu próprio ambiente, certifique-se de que a dependência horovod está incluída, por exemplo:

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
  - horovod==0.19.5
```

Para executar um trabalho distribuído utilizando MPI/Horovod em Azure ML, deve especificar uma [Configuração de Mpi](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py) para o `distributed_job_config` parâmetro do construtor ScriptRunConfig. O código abaixo configurará um trabalho distribuído de 2 nós executando um processo por nó. Se também quiser executar vários processos por nó (isto é, se o seu cluster SKU tiver várias GPUs), especificar ainda o `process_count_per_node` parâmetro em MpiConfiguration (o padrão `1` é).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='tf_horovod_word2vec.py',
                      arguments=['--input_data', dataset.as_mount()],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Para um tutorial completo sobre o tensorFlow distribuído com Horovod em Azure ML, consulte [O TensorFlow Distribuído com Horovod](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/distributed-tensorflow-with-horovod).

### <a name="tfdistribute"></a>tf.distribuir

Se estiver a utilizar [o TemorFlow distribuído nativo](https://www.tensorflow.org/guide/distributed_training) no seu código de formação, por exemplo, a API do TensorFlow 2.x, também pode lançar o trabalho distribuído através do `tf.distribute.Strategy` Azure ML. 

Para tal, especifique uma [Configuração tensorflow](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?preserve-view=true&view=azure-ml-py) para o `distributed_job_config` parâmetro do construtor ScriptRunConfig. Se estiver a `tf.distribute.experimental.MultiWorkerMirroredStrategy` utilizar, especifique `worker_count` a configuração no TensorflowConfiguration correspondente ao número de nós para o seu trabalho de formação.

```python
import os
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import TensorflowConfiguration

distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=0)

model_path = os.path.join("./outputs", "keras-model")

src = ScriptRunConfig(source_directory=source_dir,
                      script='train.py',
                      arguments=["--epochs", 30, "--model-dir", model_path],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=distr_config)
```

No TensorFlow, a `TF_CONFIG` variável ambiente é necessária para o treino em várias máquinas. A Azure ML configurará e definirá a `TF_CONFIG` variável adequadamente para cada trabalhador antes de executar o seu script de treino. Pode aceder `TF_CONFIG` a partir do seu roteiro de treino se precisar através de `os.environ['TF_CONFIG']` .

Estrutura de exemplo do `TF_CONFIG` conjunto num nó de trabalhador chefe:
```JSON
TF_CONFIG='{
    "cluster": {
        "worker": ["host0:2222", "host1:2222"]
    },
    "task": {"type": "worker", "index": 0},
    "environment": "cloud"
}'
```

Se o seu script de treino utilizar a estratégia do servidor de parâmetros para o treino distribuído, isto é, para o legado TensorFlow 1.x, também terá de especificar o número de servidores de parâmetros a utilizar no trabalho, por `distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=1)` exemplo.

## <a name="deploy-a-tensorflow-model"></a>Implementar um modelo TensorFlow

A implementação como-fazer contém uma secção sobre os modelos de registo, mas pode saltar diretamente para [criar um alvo de computação](how-to-deploy-and-where.md#choose-a-compute-target) para implementação, uma vez que já tem um modelo registado.

### <a name="preview-no-code-model-deployment"></a>(Pré-visualização) Implementação de modelo sem código

Em vez da rota de implementação tradicional, também pode utilizar a funcionalidade de implementação sem código (pré-visualização) para TensorFlow. Ao registar o seu modelo como mostrado acima com o `model_framework` `model_framework_version` , e `resource_configuration` parâmetros, pode simplesmente utilizar a `deploy()` função estática para implantar o seu modelo.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

A forma completa [de como](how-to-deploy-and-where.md) fazer cobre a implantação em Azure Machine Learning em maior profundidade.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, treinou e registou um modelo TensorFlow, e aprendeu sobre opções de implementação. Veja estes outros artigos para saber mais sobre Azure Machine Learning.

* [Métricas de corrida de pista durante o treino](how-to-track-experiments.md)
* [Otimizar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Arquitetura de referência para formação de aprendizagem profunda distribuída em Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
