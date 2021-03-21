---
title: Treine um modelo usando uma imagem personalizada do Docker
titleSuffix: Azure Machine Learning
description: Aprenda a usar as suas próprias imagens Docker, ou com curadoria da Microsoft, para treinar modelos em Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 10/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 03400173dd35660d6e7a98500b831a7c6aa4ebd7
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102521175"
---
# <a name="train-a-model-by-using-a-custom-docker-image"></a>Treine um modelo usando uma imagem personalizada do Docker

Neste artigo, aprenda a usar uma imagem personalizada do Docker quando estiver a treinar modelos com Azure Machine Learning. Você usará os scripts de exemplo neste artigo para classificar imagens de animais de estimação criando uma rede neural convolucional. 

A Azure Machine Learning fornece uma imagem base padrão do Docker. Também pode utilizar ambientes de aprendizagem automática Azure para especificar uma imagem base diferente, como uma das imagens base de aprendizagem automática [Azure](https://github.com/Azure/AzureML-Containers) ou a sua [própria imagem personalizada.](how-to-deploy-custom-docker-image.md#create-a-custom-base-image) As imagens de base personalizadas permitem-lhe gerir de perto as suas dependências e manter um controlo mais apertado sobre as versões dos componentes ao executar trabalhos de formação.

## <a name="prerequisites"></a>Pré-requisitos

Executar o código em qualquer um destes ambientes:

* Exemplo de computação Azure Machine Learning (sem transferências ou instalações necessárias):
  * Complete o [ambiente configurado e](tutorial-1st-experiment-sdk-setup.md) tutorial de espaço de trabalho para criar um servidor de caderno dedicado pré-carregado com o SDK e o repositório de amostras.
  * No [repositório](https://github.com/Azure/azureml-examples)Azure Machine Learning, encontre um caderno completo indo aos **cadernos**  >  **fastai**  >  **train-pets-resnet34.ipynb.** 
* O seu próprio servidor de cadernos Jupyter:
  * Crie um [ficheiro de configuração do espaço de trabalho.](how-to-configure-environment.md#workspace)
  * Instale o [Azure Machine Learning SDK](/python/api/overview/azure/ml/install). 
  * Crie um [registo de contentores Azure](../container-registry/index.yml) ou outro registo Docker que esteja disponível na internet.

## <a name="set-up-a-training-experiment"></a>Crie uma experiência de treino

Nesta secção, você configura a sua experiência de treino inicializando um espaço de trabalho, definindo o seu ambiente, e configurando um alvo de computação.

### <a name="initialize-a-workspace"></a>Inicializar um espaço de trabalho

O espaço de [trabalho Azure Machine Learning](concept-workspace.md) é o recurso de alto nível para o serviço. Dá-te um lugar centralizado para trabalhar com todos os artefactos que crias. No Python SDK, você pode aceder aos artefactos do espaço de trabalho criando um [`Workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) objeto.

Crie um `Workspace` objeto a partir do config.jsno ficheiro que criou como [pré-requisito](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="define-your-environment"></a>Defina o seu ambiente

Cria um `Environment` objeto e ativa o Docker.

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

A imagem base especificada no código seguinte suporta a biblioteca fast.ai, que permite capacidades de aprendizagem profunda distribuídas. Para mais informações, consulte o [repositório fast.ai Docker Hub.](https://hub.docker.com/u/fastdotai) 

Quando estiver a usar a sua imagem personalizada do Docker, pode já ter o seu ambiente Python devidamente configurado. Nesse caso, coloque a `user_managed_dependencies` bandeira para usar o ambiente python incorporado da sua imagem `True` personalizada. Por padrão, a Azure Machine Learning constrói um ambiente Conda com dependências que especificou. O serviço executa o script nesse ambiente em vez de usar quaisquer bibliotecas Python que instalou na imagem base.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

#### <a name="use-a-private-container-registry-optional"></a>Utilize um registo privado de contentores (opcional)

Para utilizar uma imagem de um registo de contentores privados que não esteja no seu espaço de trabalho, utilize `docker.base_image_registry` para especificar o endereço do repositório e um nome de utilizador e senha:

```python
# Set the container registry information.
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

#### <a name="use-a-custom-dockerfile-optional"></a>Use um Dockerfile personalizado (opcional)

Também é possível usar um Dockerfile personalizado. Utilize esta abordagem se precisar de instalar pacotes não Python como dependências. Lembre-se de definir a imagem base para `None` .

```python 
# Specify Docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set the base image to None, because the image is defined by Dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

>[!IMPORTANT]
> A Azure Machine Learning suporta apenas imagens docker que fornecem o seguinte software:
> * Ubuntu 16.04 ou maior.
> * Conda 4.5.# ou maior.
> * Python 3.5+.

Para obter mais informações sobre a criação e gestão de ambientes de aprendizagem automática Azure, consulte [Criar e utilizar ambientes de software.](how-to-use-environments.md) 

### <a name="create-or-attach-a-compute-target"></a>Criar ou anexar um alvo de computação

Tens de criar um [alvo de computação](concept-azure-machine-learning-architecture.md#compute-targets) para treinar o teu modelo. Neste tutorial, cria-se `AmlCompute` como recurso de computação de formação.

A criação `AmlCompute` demora alguns minutos. Se o `AmlCompute` recurso já estiver no seu espaço de trabalho, este código ignora o processo de criação.

Tal como acontece com outros serviços Azure, existem limites em determinados recursos (por exemplo, `AmlCompute` ) associados ao serviço Azure Machine Learning. Para obter mais informações, consulte [os limites de incumprimento e como solicitar uma quota mais elevada.](how-to-manage-quotas.md)

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your cluster.
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # Create the cluster.
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# Use get_status() to get a detailed status for the current AmlCompute.
print(compute_target.get_status().serialize())
```

## <a name="configure-your-training-job"></a>Configure o seu trabalho de formação

Para este tutorial, use o script de treino *train.py* no [GitHub](https://github.com/Azure/azureml-examples/blob/main/workflows/train/fastai/pets/src/train.py). Na prática, você pode pegar qualquer script de treino personalizado e executá-lo, como está, com Azure Machine Learning.

Crie um `ScriptRunConfig` recurso para configurar o seu trabalho para executar o [alvo de computação](how-to-set-up-training-targets.md)pretendido .

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

## <a name="submit-your-training-job"></a>Submeta o seu trabalho de formação

Quando submete um treino executado utilizando um `ScriptRunConfig` objeto, o `submit` método devolve um objeto do tipo `ScriptRun` . O objeto devolvido `ScriptRun` dá-lhe acesso programático à informação sobre o treino. 

```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-fastai').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> A Azure Machine Learning executa scripts de formação copiando todo o diretório de origem. Se tiver dados sensíveis que não pretende fazer o upload, utilize um [ficheiro .ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) ou não o inclua no diretório de origem. Em vez disso, aceda aos seus dados utilizando uma [loja de dados.](/python/api/azureml-core/azureml.data)

## <a name="next-steps"></a>Passos seguintes
Neste artigo, treinaste um modelo usando uma imagem personalizada do Docker. Veja estes outros artigos para saber mais sobre Azure Machine Learning:
* [Métricas de corrida de pista](how-to-track-experiments.md) durante o treino.
* [Implemente um modelo](how-to-deploy-custom-docker-image.md) utilizando uma imagem personalizada do Docker.
