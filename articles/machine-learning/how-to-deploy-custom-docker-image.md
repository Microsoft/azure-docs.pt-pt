---
title: Implementar modelos com imagem personalizada do Docker
titleSuffix: Azure Machine Learning
description: Aprenda a usar uma imagem base personalizada do Docker para implementar os seus modelos de Aprendizagem automática Azure. Enquanto o Azure Machine Learning fornece uma imagem base padrão para si, também pode utilizar a sua própria imagem base.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.reviewer: larryfr
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: f621bb2a7d4543620d22ab85fb8b44752c9989ac
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376261"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Implemente um modelo usando uma imagem base personalizada do Docker

Aprenda a usar uma imagem base personalizada do Docker ao implementar modelos treinados com Azure Machine Learning.

A Azure Machine Learning utilizará uma imagem de base padrão do Docker se nenhuma for especificada. Pode encontrar a imagem específica do Docker utilizada com `azureml.core.runconfig.DEFAULT_CPU_IMAGE` . Também pode usar __ambientes__ de aprendizagem automática Azure para selecionar uma imagem base específica, ou usar um personalizado que fornece.

Uma imagem base é usada como ponto de partida quando uma imagem é criada para uma implantação. Fornece o sistema operativo e os componentes subjacentes. O processo de implementação adiciona então componentes adicionais, como o seu modelo, ambiente conda e outros ativos, à imagem.

Normalmente, cria uma imagem base personalizada quando pretende utilizar o Docker para gerir as suas dependências, manter um controlo mais apertado sobre as versões dos componentes ou economizar tempo durante a implementação. Também pode querer instalar o software exigido pelo seu modelo, onde o processo de instalação demora muito tempo. Instalar o software ao criar a imagem base significa que não é preciso instalá-lo para cada implementação.

> [!IMPORTANT]
> Quando implementa um modelo, não é possível substituir componentes do núcleo, tais como o servidor web ou os componentes IoT Edge. Estes componentes fornecem um ambiente de trabalho conhecido que é testado e suportado pela Microsoft.

> [!WARNING]
> A Microsoft pode não ser capaz de ajudar a resolver problemas causados por uma imagem personalizada. Se encontrar problemas, poderá ser-lhe pedido que utilize a imagem padrão ou uma das imagens que a Microsoft fornece para ver se o problema é específico da sua imagem.

Este documento é dividido em duas secções:

* Crie uma imagem base personalizada: Fornece informações aos administradores e devOps sobre a criação de uma imagem personalizada e a autenticação configurada para um Registo de Contentores Azure utilizando o CLI Azure e o Machine Learning CLI.
* Implementar um modelo utilizando uma imagem base personalizada: Fornece informações aos Data Scientists e DevOps / ML Engineers sobre a utilização de imagens personalizadas ao implementar um modelo treinado a partir do Python SDK ou ML CLI.

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Azure Machine Learning. Para mais informações, consulte o [Artigo Criar um espaço de trabalho.](how-to-manage-workspace.md)
* [O Azure Machine Learning SDK.](/python/api/overview/azure/ml/install) 
* O [Azure CLI.](/cli/azure/install-azure-cli)
* A [extensão CLI para Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Um [registo de contentores Azure](../container-registry/index.yml) ou outro registo Docker que esteja acessível na internet.
* Os passos neste documento assumem que está familiarizado com a criação e utilização de um objeto de configuração de __inferência__ como parte da implementação do modelo. Para mais informações, consulte [onde implementar e como.](how-to-deploy-and-where.md)

## <a name="create-a-custom-base-image"></a>Crie uma imagem base personalizada

As informações nesta secção pressupõem que está a utilizar um registo de contentores Azure para armazenar imagens do Docker. Utilize a seguinte lista de verificação quando planeia criar imagens personalizadas para a Azure Machine Learning:

* Utilizará o Registo de Contentores Azure criado para o espaço de trabalho de aprendizagem de máquinas Azure ou um registo autónomo do contentor Azure?

    Ao utilizar imagens armazenadas no registo do __contentor para o espaço de trabalho,__ não é necessário autenticar no registo. A autenticação é tratada pelo espaço de trabalho.

    > [!WARNING]
    > O Registo de Contentores Azure para o seu espaço de trabalho é __criado na primeira vez que treina ou implementa um modelo__ utilizando o espaço de trabalho. Se criou um novo espaço de trabalho, mas não treinou ou criou um modelo, não existirá nenhum Registo de Contentores Azure para o espaço de trabalho.

    Ao utilizar imagens armazenadas num __registo de contentores autónomos,__ terá de configurar um principal de serviço que tenha pelo menos lido o acesso. Em seguida, fornece o ID principal do serviço (nome de utilizador) e senha para qualquer pessoa que utilize imagens do registo. A exceção é se tornar o registo de contentores acessível ao público.

    Para obter informações sobre a criação de um registo privado de contentores Azure, consulte [criar um registo privado de contentores.](../container-registry/container-registry-get-started-azure-cli.md)

    Para obter informações sobre a utilização de principais serviços com registo de contentores [Azure, consulte a autenticação do Registo do Contentor de Azure com os principais serviços.](../container-registry/container-registry-auth-service-principal.md)

* Registo de registo de contentores Azure e informações de imagem: Forneça o nome da imagem a quem precisar de o utilizar. Por exemplo, uma imagem chamada `myimage` , armazenada num registo denominado , é `myregistry` referenciada como quando se utiliza a `myregistry.azurecr.io/myimage` imagem para implantação de modelos

### <a name="image-requirements"></a>Requisitos de imagem

A Azure Machine Learning suporta apenas imagens docker que fornecem o seguinte software:
* Ubuntu 16.04 ou maior.
* Conda 4.5.# ou maior.
* Python 3.6+.

Para utilizar conjuntos de dados, instale o pacote libfuse-dev. Certifique-se também de instalar quaisquer pacotes de espaço do utilizador que possa necessitar.

AZure ML mantém um conjunto de imagens de base cpu e GPU publicadas no Microsoft Container Registry que pode opcionalmente alavancar (ou referência) em vez de criar a sua própria imagem personalizada. Para ver os Dockerfiles para essas imagens, consulte o repositório GitHub [do Azure/AzureML-Containers.](https://github.com/Azure/AzureML-Containers)

Para imagens de GPU, a Azure ML atualmente oferece imagens de base cuda9 e cuda10. As principais dependências instaladas nestas imagens base são:

| Dependências | IntelMPI CPU | OpenMPI CPU | IntelMPI GPU | OpenMPI GPU |
| --- | --- | --- | --- | --- |
| miniconda | ==4.5.11 | ==4.5.11 | ==4.5.11 | ==4.5.11 |
| mpi | intelmpi==2018.3.222 |openmpi==3.1.2 |intelmpi==2018.3.222| openmpi==3.1.2 |
| cuda | - | - | 9.0/10.0 | 9.0/10.0/10.1 |
| Cudnn | - | - | 7.4/7.5 | 7.4/7.5 |
| nccl | - | - | 2,4 | 2,4 |
| git | 2.7.4 | 2.7.4 | 2.7.4 | 2.7.4 |

As imagens do CPU são construídas a partir de ubuntu16.04. As imagens da GPU para cuda9 são construídas a partir de nvidia/cuda:9.0-cudnn7-devel-ubuntu16.04. As imagens da GPU para cuda10 são construídas a partir de nvidia/cuda:10.0-cudnn7-devel-ubuntu16.04.
<a id="getname"></a>

> [!IMPORTANT]
> Ao utilizar imagens personalizadas do Docker, recomenda-se que pine as versões do pacote de forma a garantir melhor a reprodutibilidade.

### <a name="get-container-registry-information"></a>Obtenha informações sobre registo de contentores

Nesta secção, aprenda a obter o nome do Registo de Contentores Azure para o seu espaço de trabalho de Aprendizagem de Máquinas Azure.

> [!WARNING]
> O Registo de Contentores Azure para o seu espaço de trabalho é __criado na primeira vez que treina ou implementa um modelo__ utilizando o espaço de trabalho. Se criou um novo espaço de trabalho, mas não treinou ou criou um modelo, não existirá nenhum Registo de Contentores Azure para o espaço de trabalho.

Se já treinou ou implementou modelos utilizando o Azure Machine Learning, foi criado um registo de contentores para o seu espaço de trabalho. Para encontrar o nome deste registo de contentores, utilize os seguintes passos:

1. Abra uma nova concha ou pedido de comando e use o seguinte comando para autenticar a sua subscrição Azure:

    ```azurecli-interactive
    az login
    ```

    Siga as instruções para autenticar a subscrição.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. Utilize o seguinte comando para listar o registo do contentor para o espaço de trabalho. Substitua `<myworkspace>` o nome do espaço de trabalho Azure Machine Learning. `<resourcegroup>`Substitua-se pelo grupo de recursos Azure que contém o seu espaço de trabalho:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    A informação devolvida é semelhante ao seguinte texto:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    O `<registry_name>` valor é o nome do Registo do Contentor Azure para o seu espaço de trabalho.

### <a name="build-a-custom-base-image"></a>Construa uma imagem base personalizada

Os passos nesta secção walk-through criando uma imagem personalizada do Docker no seu Registo de Contentores Azure. Para amostras de estivadores, consulte o repo GitHub [Azure/AzureML-Containers).](https://github.com/Azure/AzureML-Containers)

1. Crie um novo ficheiro de texto nomeado `Dockerfile` , e use o seguinte texto como conteúdo:

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.7.12
    ARG PYTHON_VERSION=3.7
    ARG AZUREML_SDK_VERSION=1.13.0
    ARG INFERENCE_SCHEMA_VERSION=1.1.0

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH
    ENV DEBIAN_FRONTEND=noninteractive

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get install -y fuse && \
        apt-get clean -y && \
        rm -rf /var/lib/apt/lists/*

    RUN useradd --create-home dockeruser
    WORKDIR /home/dockeruser
    USER dockeruser

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p ~/miniconda && \
        rm ~/miniconda.sh && \
        ~/miniconda/bin/conda clean -tipsy
    ENV PATH="/home/dockeruser/miniconda/bin/:${PATH}"

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        pip install azureml-defaults==${AZUREML_SDK_VERSION} inference-schema==${INFERENCE_SCHEMA_VERSION} &&\
        conda clean -aqy && \
        rm -rf ~/miniconda/pkgs && \
        find ~/miniconda/ -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. A partir de uma concha ou de um pedido de comando, utilize o seguinte para autenticar no Registo do Contentor Azure. Substitua-a `<registry_name>` pelo nome do registo do contentor que pretende armazenar a imagem em:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Para carregar o Dockerfile e construí-lo, use o seguinte comando. `<registry_name>`Substitua-a pelo nome do registo do contentor que pretende armazenar a imagem em:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > Neste exemplo, uma etiqueta de `:v1` é aplicada à imagem. Se não for fornecida uma etiqueta, é aplicada uma `:latest` etiqueta.

    Durante o processo de construção, a informação é transmitida para trás para a linha de comando. Se a construção for bem sucedida, receberá uma mensagem semelhante ao seguinte texto:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Para obter mais informações sobre a construção de imagens com um Registo de Contentores Azure, consulte [Construir e executar uma imagem de contentor usando tarefas de registo de contentores Azure](../container-registry/container-registry-quickstart-task-cli.md)

Para obter mais informações sobre o upload das imagens existentes para um Registo de Contentores Azure, consulte Empurre a sua primeira imagem para um registo privado de [contentores do Docker](../container-registry/container-registry-get-started-docker-cli.md).

## <a name="use-a-custom-base-image"></a>Use uma imagem base personalizada

Para utilizar uma imagem personalizada, precisa das seguintes informações:

* O __nome da imagem.__ Por exemplo, `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest` é o caminho para uma simples Imagem Docker fornecida pela Microsoft.

    > [!IMPORTANT]
    > Para imagens personalizadas que criou, certifique-se de incluir quaisquer tags que foram usadas com a imagem. Por exemplo, se a sua imagem foi criada com uma etiqueta específica, como `:v1` . Se não usou uma etiqueta específica ao criar a imagem, foi aplicada uma `:latest` etiqueta.

* Se a imagem estiver num __repositório privado,__ precisa das seguintes informações:

    * O __endereço__ do registo. Por exemplo, `myregistry.azureecr.io`.
    * Um nome de utilizador principal __de__ serviço e __senha__ que leu o acesso ao registo.

    Se não tiver esta informação, fale com o administrador do Registo do Contentor Azure que contenha a sua imagem.

### <a name="publicly-available-base-images"></a>Imagens base disponíveis ao público

A Microsoft fornece várias imagens de estivadores num repositório acessível ao público, que pode ser usado com os passos nesta secção:

| Imagem | Descrição |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Imagem central para Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | Contém tempo de execução ONNX para inferencing CPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | Contém o tempo de execução ONNX e CUDA para GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | Contém ONNX Runtime e TensorRT para GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm` | Contém ONNX Runtime e OpenVINO para <sup></sup> Intel Vision Accelerator Design baseado em Movidius<sup>TM</sup> MyriadX VPUs |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Contém ONNX Runtime e OpenVINO para sticks USB Intel <sup></sup> Movidius<sup>TM</sup> |

Para obter mais informações sobre as imagens base onNX Runtime consulte a [secção onNX Runtime dockerfile](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) no repo GitHub.

> [!TIP]
> Uma vez que estas imagens estão disponíveis ao público, não precisa de fornecer um endereço, nome de utilizador ou palavra-passe ao usá-las.

Para mais informações, consulte o repositório de [recipientes de aprendizagem automática Azure](https://github.com/Azure/AzureML-Containers) no GitHub.

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Use uma imagem com o Azure Machine Learning SDK

Para utilizar uma imagem armazenada no Registo do **Contentor Azure para** o seu espaço de trabalho, ou um **registo de contentores acessível ao público,** deite os seguintes atributos [ambientais:](/python/api/azureml-core/azureml.core.environment.environment)

+ `docker.enabled=True`
+ `docker.base_image`: Definir o registo e o caminho para a imagem.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest"
```

Para utilizar uma imagem de um __registo de contentores privados__ que não esteja no seu espaço de trabalho, deve utilizar `docker.base_image_registry` para especificar o endereço do repositório e um nome de utilizador e senha:

```python
# Set the container registry information
myenv.docker.base_image_registry.address = "myregistry.azurecr.io"
myenv.docker.base_image_registry.username = "username"
myenv.docker.base_image_registry.password = "password"

myenv.inferencing_stack_version = "latest"  # This will install the inference specific apt packages.

# Define the packages needed by the model and scripts
from azureml.core.conda_dependencies import CondaDependencies
conda_dep = CondaDependencies()
# you must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
myenv.python.conda_dependencies=conda_dep
```

Deve adicionar azureml-padrão com >versão = 1.0.45 como dependência de pip. Este pacote contém a funcionalidade necessária para hospedar o modelo como um serviço web. Você também deve definir inferencing_stack_version propriedade sobre o ambiente para "o mais recente", este irá instalar pacotes específicos apt necessários pelo serviço web. 

Depois de definir o ambiente, use-o com um objeto [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) para definir o ambiente de inferência em que o modelo e o serviço web irão funcionar.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Neste momento, pode continuar com a implantação. Por exemplo, o seguinte corte de código implementaria um serviço web localmente usando a configuração de inferência e a imagem personalizada:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Para obter mais informações sobre a implementação, consulte [implementar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

Para obter mais informações sobre a personalização do seu ambiente Python, consulte [Criar e gerir ambientes para treino e implantação.](how-to-use-environments.md) 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Use uma imagem com o CLI de Aprendizagem automática

> [!IMPORTANT]
> Atualmente, o CLI de Machine Learning pode utilizar imagens do Registo de Contentores Azure para o seu espaço de trabalho ou repositórios acessíveis ao público. Não pode usar imagens de registos privados autónomos.

Antes de implementar um modelo utilizando o CLI machine learning, crie um [ambiente](/python/api/azureml-core/azureml.core.environment.environment) que utilize a imagem personalizada. Em seguida, crie um ficheiro de configuração de inferência que faça referência ao ambiente. Também pode definir o ambiente diretamente no ficheiro de configuração de inferência. O seguinte documento JSON demonstra como fazer referência a uma imagem num registo público de contentores. Neste exemplo, o ambiente é definido em linha:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Este ficheiro é usado com o `az ml model deploy` comando. O `--ic` parâmetro é utilizado para especificar o ficheiro de configuração de inferência.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Para obter mais informações sobre a implementação de um modelo utilizando o ML CLI, consulte a secção "registo, perfis e implantação do modelo" da extensão do CLI para o artigo [de Aprendizagem automática Azure.](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [onde implementar e como.](how-to-deploy-and-where.md)
* Aprenda a [treinar e implementar modelos de machine learning utilizando gasodutos Azure.](/azure/devops/pipelines/targets/azure-machine-learning)