---
title: Treine modelos PyTorch de aprendizagem profunda
titleSuffix: Azure Machine Learning
description: Aprenda a executar os seus scripts de treinamento PyTorch à escala empresarial usando Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 01/14/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 962054943a68aa61ac681de97eeebc10fe3f2b0a
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216636"
---
# <a name="train-pytorch-models-at-scale-with-azure-machine-learning"></a>Treinar modelos PyTorch em escala com Azure Machine Learning

Neste artigo, aprenda a executar os seus scripts de treinamento [PyTorch](https://pytorch.org/) à escala empresarial usando Azure Machine Learning.

Os scripts de exemplo neste artigo são usados para classificar imagens de frango e peru para construir uma rede neural de aprendizagem profunda (DNN) baseada no [tutorial](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html)de aprendizagem de transferência de PyTorch. A aprendizagem de transferência é uma técnica que aplica conhecimentos adquiridos desde a resolução de um problema para um problema diferente, mas relacionado. Isto atalho o processo de formação, requerendo menos dados, tempo e recursos de computação do que a formação de raiz. Veja o artigo [de aprendizagem profunda vs machine learning](./concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning) para saber mais sobre a aprendizagem de transferências.

Quer esteja a treinar um modelo PyTorch de aprendizagem profunda a partir do solo ou se está a trazer um modelo existente para a nuvem, pode usar o Azure Machine Learning para escalar trabalhos de formação de código aberto usando recursos de computação em nuvem elástica. Pode construir, implementar, verrativa e monitorizar modelos de nível de produção com Azure Machine Learning. 

## <a name="prerequisites"></a>Pré-requisitos

Executar este código em qualquer um destes ambientes:

- Exemplo de computação Azure Machine Learning - sem transferências ou instalação necessárias

    - Complete o [Tutorial: Ambiente de configuração e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de caderno dedicado pré-carregado com o SDK e o repositório de amostras.
    - Nas amostras da pasta de aprendizagem profunda no servidor do portátil, encontre um caderno completo e expandido navegando para este diretório: **como usar-azureml > estruturas ml > pytorch > pasta de ajuste de afinação de comboio-hiperparametr-implement-with-pytorch.** 
 
 - O seu próprio servidor de cadernos Jupyter
    - [Instale o Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.15.0).
    - [Crie um ficheiro de configuração do espaço de trabalho.](how-to-configure-environment.md#workspace)
    - [Descarregue os ficheiros de script de amostra](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    Também pode encontrar uma versão completa do [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) deste guia na página de amostras do GitHub. O caderno inclui secções expandidas que cobrem afinação inteligente de hiperparímetros, implantação de modelos e widgets de portátil.

## <a name="set-up-the-experiment"></a>Configurar a experiência

Esta secção estabelece a experiência de treino carregando os pacotes Python necessários, inicializando um espaço de trabalho, criando o alvo de computação, e definindo o ambiente de treino.

### <a name="import-packages"></a>Importar pacotes

Primeiro, importe as bibliotecas python necessárias.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment
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

### <a name="get-the-data"></a>Obter os dados

O conjunto de dados consiste em cerca de 120 imagens de treino cada para perus e galinhas, com 100 imagens de validação para cada turma. Vamos descarregar e extrair o conjunto de dados como parte do nosso script de `pytorch_train.py` treino. As imagens são um subconjunto do Conjunto de [Dados V5 Imagens Abertas](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-script"></a>Preparar roteiro de treino

Neste tutorial, o roteiro de `pytorch_train.py` formação, já está fornecido. Na prática, você pode pegar qualquer script de treino personalizado, como está, e executá-lo com Azure Machine Learning.

Crie uma pasta para o seu script(s) de treino.

```python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)
shutil.copy('pytorch_train.py', project_folder)
```

### <a name="create-a-compute-target"></a>Criar um alvo de computação

Crie um alvo de computação para o seu trabalho PyTorch funcionar. Neste exemplo, crie um cluster de cálculo de aprendizagem automática Azure ativado pela GPU.

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
A azure ML fornece ambientes pré-construídos e curados se você não quiser definir o seu próprio ambiente. Azure ML tem vários ambientes com curadoria de CPU e GPU para PyTorch correspondentes a diferentes versões de PyTorch. Para mais informações, consulte [aqui.](resource-curated-environments.md)

Se quiser utilizar um ambiente curado, pode executar o seguinte comando:

```python
curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)
```

Para ver os pacotes incluídos no ambiente curado, você pode escrever as dependências conda para o disco:
```python
pytorch_env.save_to_directory(path=curated_env_name)
```

Certifique-se de que o ambiente curado inclui todas as dependências exigidas pelo seu roteiro de treino. Caso contrário, terá de modificar o ambiente para incluir as dependências em falta. Note que, se o ambiente for modificado, terá de lhe dar um novo nome, uma vez que o prefixo 'AzureML' está reservado para ambientes com curadoria. Se modificar o ficheiro IML das dependências conda, pode criar um novo ambiente a partir dele com um novo nome, por exemplo:
```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')
```

Se em vez disso modificou diretamente o objeto ambiental curado, pode clonar esse ambiente com um novo nome:
```python
pytorch_env = pytorch_env.clone(new_name='pytorch-1.6-gpu')
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
  - torch==1.6.0
  - torchvision==0.7.0
  - future==0.17.1
  - pillow
```

Crie um ambiente Azure ML a partir desta especificação ambiente conda. O ambiente será embalado num contentor Docker em tempo de execução.

Por predefinição, se não for especificada nenhuma imagem de base, o Azure ML utilizará uma imagem cpU `azureml.core.environment.DEFAULT_CPU_IMAGE` como imagem base. Uma vez que este exemplo executa o treino num cluster de GPU, você precisará especificar uma imagem base da GPU que tenha os condutores e dependências de GPU necessários. Azure ML mantém um conjunto de imagens de base publicadas no Microsoft Container Registry (MCR) que pode utilizar, consulte o [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers) GitHub repo para obter mais informações.

```python
pytorch_env = Environment.from_conda_specification(name='pytorch-1.6-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
pytorch_env.docker.enabled = True
pytorch_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Opcionalmente, pode capturar todas as suas dependências diretamente numa imagem personalizada do Docker ou Dockerfile, e criar o seu ambiente a partir daí. Para mais informações, consulte [Train com imagem personalizada.](how-to-train-with-custom-image.md)

Para obter mais informações sobre a criação e utilização de ambientes, consulte [Criar e utilizar ambientes de software em Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Configure e submeta a sua formação

### <a name="create-a-scriptrunconfig"></a>Criar um ScriptRunConfig

Crie um objeto [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) para especificar os detalhes de configuração do seu trabalho de treino, incluindo o seu script de treino, ambiente a utilizar e o alvo de computação para executar. Quaisquer argumentos para o seu script de treino serão passados através da linha de comando, se especificado no `arguments` parâmetro. 

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_train.py',
                      arguments=['--num_epochs', 30, '--output_dir', './outputs'],
                      compute_target=compute_target,
                      environment=pytorch_env)
```

> [!WARNING]
> A Azure Machine Learning executa scripts de formação copiando todo o diretório de origem. Se tiver dados sensíveis que não pretende fazer o upload, utilize um [ficheiro .ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) ou não o inclua no diretório de origem . Em vez disso, aceda aos seus dados utilizando um [conjunto de dados](how-to-train-with-datasets.md)Azure ML .

Para obter mais informações sobre a configuração de empregos com scriptRunConfig, consulte [Configure e submeta cursos de formação.](how-to-set-up-training-targets.md)

> [!WARNING]
> Se já usou o estimador PyTorch para configurar os seus trabalhos de formação PyTorch, por favor, note que os estimadores foram depreciados a partir da versão 1.19.0 SDK. Com Azure ML SDK >= 1.15.0, o ScriptRunConfig é a forma recomendada de configurar trabalhos de formação, incluindo aqueles que usam quadros de aprendizagem profunda. Para questões de migração comuns, consulte o [guia de migração ScriptRunConfig](how-to-migrate-from-estimators-to-scriptrunconfig.md).

## <a name="submit-your-run"></a>Submeta a sua corrida

O [objeto Run](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) fornece a interface para o histórico de execução enquanto o trabalho está em execução e depois de concluído.

```Python
run = Experiment(ws, name='Tutorial-pytorch-birds').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>O que acontece durante a execução da execução
À medida que a corrida é executada, passa pelas seguintes fases:

- **Preparação:** Uma imagem de estivador é criada de acordo com o ambiente definido. A imagem é enviada para o registo de contentores do espaço de trabalho e em cache para posteriores execuções. Os registos também são transmitidos para o histórico de execução e podem ser vistos para monitorizar o progresso. Se um ambiente curado for especificado, em vez disso, a imagem em cache que o ambiente curado será usado.

- **Dimensionamento**: O cluster tenta aumentar se o cluster de AI do lote necessitar de mais nós para executar a execução do que estão atualmente disponíveis.

- **Execução**: Todos os scripts na pasta do script são carregados para o alvo do cálculo, as lojas de dados são montadas ou copiadas, e a `script` é executada. As saídas da sestada e da pasta **./logs** são transmitidas para o histórico de execução e podem ser utilizadas para monitorizar a execução.

- **Pós-Processamento**: A pasta **./outputs** da execução é copiada para o histórico de execução.

## <a name="register-or-download-a-model"></a>Registe-se ou descarregue um modelo

Depois de ter treinado o modelo, pode registá-lo no seu espaço de trabalho. O registo de modelos permite-lhe armazenar e versar os seus modelos no seu espaço de trabalho para simplificar a [gestão e implementação de modelos.](concept-model-management-and-deployment.md)

```Python
model = run.register_model(model_name='pytorch-birds', model_path='outputs/model.pt')
```

> [!TIP]
> A implementação como-fazer contém uma secção sobre os modelos de registo, mas pode saltar diretamente para [criar um alvo de computação](how-to-deploy-and-where.md#choose-a-compute-target) para implementação, uma vez que já tem um modelo registado.

Também pode descarregar uma cópia local do modelo utilizando o objeto Executar. No roteiro de `pytorch_train.py` treino, um objeto de salvamento PyTorch persiste o modelo para uma pasta local (local para o alvo do cálculo). Pode utilizar o objeto Executar para descarregar uma cópia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

# Download the model from run history
run.download_file(name='outputs/model.pt', output_file_path='./model/model.pt'), 
```

## <a name="distributed-training"></a>Preparação distribuída

O Azure Machine Learning também suporta trabalhos PyTorch distribuídos com vários nós para que possa escalar as suas cargas de trabalho de formação. Você pode facilmente executar trabalhos PyTorch distribuídos e Azure ML vai gerir a orquestração para você.

A Azure ML suporta a execução de trabalhos PyTorch distribuídos com o módulo DistributedDataParallel incorporado da Horovod e da PyTorch.

### <a name="horovod"></a>Horovod
[A Horovod](https://github.com/uber/horovod) é uma fonte aberta, todas reduzem o enquadramento para a formação distribuída desenvolvida pela Uber. Oferece um caminho fácil para escrever código PyTorch distribuído para o treino.

O seu código de treino terá de ser instrumentalizado com o Horovod para treino distribuído. Para obter mais informações utilizando Horovod com PyTorch, consulte a [documentação horovod](https://horovod.readthedocs.io/en/stable/pytorch.html).

Além disso, certifique-se de que o seu ambiente de treino inclui o pacote **horovod.** Se você está usando um ambiente curado PyTorch, horovod já está incluído como uma das dependências. Se estiver a utilizar o seu próprio ambiente, certifique-se de que a dependência horovod está incluída, por exemplo:

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - torch==1.6.0
  - torchvision==0.7.0
  - horovod==0.19.5
```

Para executar um trabalho distribuído utilizando MPI/Horovod em Azure ML, deve especificar uma [Configuração de Mpi](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py) para o `distributed_job_config` parâmetro do construtor ScriptRunConfig. O código abaixo configurará um trabalho distribuído de 2 nós executando um processo por nó. Se também quiser executar vários processos por nó (isto é, se o seu cluster SKU tiver várias GPUs), especificar ainda o `process_count_per_node` parâmetro em MpiConfiguration (o padrão `1` é).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_horovod_mnist.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Para um tutorial completo sobre a execução de PyTorch distribuído com Horovod em Azure ML, consulte [PyTorch Distribuído com Horovod](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-horovod).

### <a name="distributeddataparallel"></a>DistribuídoDataParallel
Se estiver a utilizar o módulo [DistributedDataParallel](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html) incorporado da PyTorch que é construído utilizando o pacote **distribuído pela tocha** no seu código de treino, também pode lançar o trabalho distribuído através do Azure ML.

Para executar um trabalho PyTorch distribuído com DistributedDataParallel, especifique uma [Configuração PyTorch](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?preserve-view=true&view=azure-ml-py) para o `distributed_job_config` parâmetro do construtor ScriptRunConfig. Para utilizar o backend NCCL para tocha.distribuído, especifique `communication_backend='Nccl'` na Configuração PyTorch. O código abaixo configurará um trabalho distribuído de 2 nós. O backend NCCL é o backend recomendado para o treino de GPU distribuído por PyTorch.

Para os trabalhos de PyTorch distribuídos configurados através da Configuração PyTorch, a Azure ML definirá as seguintes variáveis ambientais nos nódos do alvo do cálculo:

* `AZ_BATCHAI_PYTORCH_INIT_METHOD`: URL para a inicialização do sistema de ficheiros partilhados do grupo de processos
* `AZ_BATCHAI_TASK_INDEX`: classificação global do processo de trabalhador

Pode especificar estas variáveis ambientais para os argumentos correspondentes do script de treino através `arguments` do parâmetro do ScriptRunConfig.

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import PyTorchConfiguration

args = ['--dist-backend', 'nccl',
        '--dist-url', '$AZ_BATCHAI_PYTORCH_INIT_METHOD',
        '--rank', '$AZ_BATCHAI_TASK_INDEX',
        '--world-size', 2]

src = ScriptRunConfig(source_directory=project_folder,
                      script='pytorch_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=PyTorchConfiguration(communication_backend='Nccl', node_count=2))
```

Se em vez disso, gostaria de usar o backend Gloo para treino distribuído, especifique. `communication_backend='Gloo'` O backend Gloo é recomendado para o treino de CPU distribuído.

Para obter um tutorial completo sobre a execução do PyTorch distribuído no Azure ML, consulte [PyTorch Distribuído com DadoParallel Distribuído](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/distributed-pytorch-with-nccl-gloo).

### <a name="troubleshooting"></a>Resolução de problemas

* **Horovod foi encerrado**: Na maioria dos casos, se encontrar "AbortedError: Horovod foi encerrado", houve uma exceção subjacente num dos processos que fez Com que Horovod fosse encerrado. Cada classificação no trabalho MPI tem o seu próprio ficheiro de registo dedicado no Azure ML. Estes registos são designados `70_driver_logs`. No caso da preparação distribuída, os nomes dos registos têm o sufixo `_rank` para facilitar a diferenciação dos mesmos. Para encontrar o erro exato que fez a Horovod desligar, procure todos os ficheiros de registo e procure `Traceback` no final dos ficheiros driver_log. Um destes ficheiros vai dar-lhe a verdadeira exceção subjacente. 

## <a name="export-to-onnx"></a>Exportação para ONNX

Para otimizar a inferência com o [Tempo de Execução ONNX,](concept-onnx.md)converta o seu modelo PyTorch treinado para o formato ONNX. Inferência, ou pontuação de modelos, é a fase em que o modelo implantado é usado para previsão, mais frequentemente em dados de produção. Veja o [tutorial](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) como exemplo.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você treinou e registou uma rede neural de aprendizagem profunda usando PyTorch em Azure Machine Learning. Para aprender a implementar um modelo, continue para o nosso artigo de implementação de modelos.

* [Como e onde implementar modelos](how-to-deploy-and-where.md)
* [Métricas de corrida de pista durante o treino](how-to-track-experiments.md)
* [Otimizar hiperparâmetros](how-to-tune-hyperparameters.md)
* [Implementar um modelo treinado](how-to-deploy-and-where.md)
* [Arquitetura de referência para formação de aprendizagem profunda distribuída em Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
