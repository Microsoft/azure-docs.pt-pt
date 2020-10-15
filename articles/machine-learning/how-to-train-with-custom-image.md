---
title: Treine um modelo usando uma imagem personalizada do Docker
titleSuffix: Azure Machine Learning
description: Aprenda a treinar modelos com imagens personalizadas do Docker em Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 13a88b327b5ba56b52cd4f08d9c7fae5d653ed38
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92095947"
---
# <a name="train-a-model-using-a-custom-docker-image"></a>Treine um modelo usando uma imagem personalizada do Docker

Neste artigo, aprenda a usar uma imagem personalizada do Docker ao treinar modelos com Azure Machine Learning. 

Os scripts de exemplo neste artigo são usados para classificar imagens de animais de estimação criando uma rede neural convolucional. 

Enquanto a Azure Machine Learning fornece uma imagem base padrão do Docker, também pode utilizar ambientes de aprendizagem automática Azure para especificar uma imagem de base específica, como um dos conjuntos de [imagens de base Azure ML mantidas](https://github.com/Azure/AzureML-Containers) ou a sua [própria imagem personalizada.](how-to-deploy-custom-docker-image.md#create-a-custom-base-image) As imagens de base personalizadas permitem-lhe gerir de perto as suas dependências e manter um controlo mais apertado sobre as versões dos componentes ao executar trabalhos de formação. 

## <a name="prerequisites"></a>Pré-requisitos 
Executar este código em qualquer um destes ambientes:
* Exemplo de computação Azure Machine Learning - sem transferências ou instalação necessárias
    * Complete o [Tutorial: Ambiente de configuração e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md) para criar um servidor de caderno dedicado pré-carregado com o SDK e o repositório de amostras.
    * No [repositório](https://github.com/Azure/azureml-examples)Azure Machine Learning, encontre um caderno completo navegando para este diretório: **cadernos > fastai > comboio-pets-resnet34.ipynb** 

* O seu próprio servidor de cadernos Jupyter
    * Crie um [ficheiro de configuração do espaço de trabalho.](how-to-configure-environment.md#workspace)
    * [O Azure Machine Learning SDK.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) 
    * Um [registo de contentores Azure](/azure/container-registry) ou outro registo Docker que esteja acessível na internet.

## <a name="set-up-the-experiment"></a>Configurar a experiência 
Esta secção configura a experiência de treino inicializando um espaço de trabalho, criando uma experiência, e carregando os dados de treino e scripts de treino.

### <a name="initialize-a-workspace"></a>Inicializar um espaço de trabalho
O espaço de [trabalho Azure Machine Learning](concept-workspace.md) é o recurso de alto nível para o serviço. Proporciona-lhe um lugar centralizado para trabalhar com todos os artefactos que cria. No Python SDK, você pode aceder aos artefactos do espaço de trabalho criando um [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) objeto.

Crie um objeto de espaço de trabalho a partir do `config.json` ficheiro criado na secção [pré-requisitos](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>Preparar scripts
Para este tutorial, o roteiro de treino **train.py** é fornecido [aqui.](https://github.com/Azure/azureml-examples/blob/main/code/models/fastai/pets-resnet34/train.py) Na prática, você pode pegar qualquer script de treino personalizado, como está, e executá-lo com Azure Machine Learning.

### <a name="define-your-environment"></a>Defina o seu ambiente
Crie um objeto ambiental e ative o Docker. 

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

A imagem base especificada abaixo suporta a biblioteca fast.ai que permite capacidades de aprendizagem profunda distribuídas. Para mais informações, consulte o [fast.ai DockerHub.](https://hub.docker.com/u/fastdotai) 

Quando estiver a usar a sua imagem personalizada do Docker, pode já ter o seu ambiente Python devidamente configurado. Nesse caso, coloque a `user_managed_dependencies` bandeira à True para aproveitar o ambiente de pitão incorporado da sua imagem personalizada. Por padrão, o Azure ML construirá um ambiente Conda com dependências especificadas, e executará a execução nesse ambiente em vez de usar quaisquer bibliotecas Python que instalou na imagem base.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

Para utilizar uma imagem de um registo de contentores privados que não esteja no seu espaço de trabalho, deve utilizar `docker.base_image_registry` para especificar o endereço do repositório e um nome de utilizador e senha:

```python
# Set the container registry information
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

Também é possível usar um Dockerfile personalizado. Utilize esta abordagem se precisar de instalar pacotes não Python como dependências e lembre-se de definir a imagem base para Nenhum.

```python 
# Specify docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

Para obter mais informações sobre a criação e gestão de ambientes Azure ML, consulte [criar & utilizar ambientes de software](how-to-use-environments.md). 

### <a name="create-or-attach-existing-amlcompute"></a>Criar ou anexar a AmlCompute existente
Terá de criar um [alvo de computação](concept-azure-machine-learning-architecture.md#compute-targets) para treinar o seu modelo. Neste tutorial, cria a AmlCompute como recurso de computação de formação.

A criação da AmlCompute demora aproximadamente 5 minutos. Se o AmlCompute com esse nome já estiver no seu espaço de trabalho, este código irá ignorar o processo de criação.

Tal como acontece com outros serviços Azure, existem limites em determinados recursos (por exemplo, AmlCompute) associados ao serviço Azure Machine Learning. Leia [este artigo](how-to-manage-quotas.md) sobre os limites de incumprimento e como solicitar mais quota. 

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current AmlCompute
print(compute_target.get_status().serialize())
```

### <a name="create-a-scriptrunconfig"></a>Criar um ScriptRunConfig
Este ScriptRunConfig configurará o seu trabalho para a execução no [alvo de computação](how-to-set-up-training-targets.md)pretendido .

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

### <a name="submit-your-run"></a>Submeta a sua corrida
Quando uma corrida de treino é submetida usando um objeto ScriptRunConfig, o método de submissão devolve um objeto do tipo ScriptRun. O objeto ScriptRun devolvido dá-lhe acesso programático a informações sobre a execução de treino. 

```python
from azureml.core import Experiment

run = Experiment(ws,'fastai-custom-image').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> A Azure Machine Learning executa scripts de formação copiando todo o diretório de origem. Se tiver dados sensíveis que não pretende fazer o upload, utilize um [ficheiro .ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) ou não o inclua no diretório de origem . Em vez disso, aceda aos seus dados através de uma [datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py&preserve-view=true).

## <a name="next-steps"></a>Passos seguintes
Neste artigo, treinaste um modelo usando uma imagem personalizada do Docker. Veja estes outros artigos para saber mais sobre Azure Machine Learning.
* [Métricas de corrida de pista](how-to-track-experiments.md) durante o treino
* [Implemente um modelo](how-to-deploy-custom-docker-image.md) usando uma imagem personalizada do Docker.
