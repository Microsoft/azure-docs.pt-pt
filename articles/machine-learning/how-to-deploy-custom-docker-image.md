---
title: Implantar modelos com imagem personalizada do Docker
titleSuffix: Azure Machine Learning
description: Saiba como usar uma imagem de base do Docker personalizada ao implantar seus modelos de Azure Machine Learning. Embora Azure Machine Learning forneça uma imagem base padrão para você, você também pode usar sua própria imagem base.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 08/22/2019
ms.openlocfilehash: 05a466d52d89fa021235c10e7187900c350b5e50
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086924"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Implantar um modelo usando uma imagem de base do Docker personalizada
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Saiba como usar uma imagem de base do Docker personalizada ao implantar modelos treinados com Azure Machine Learning.

Quando você implanta um modelo treinado em um serviço Web ou IoT Edge dispositivo, é criado um pacote que contém um servidor Web para lidar com solicitações de entrada.

Azure Machine Learning fornece uma imagem base padrão do Docker para que você não precise se preocupar com a criação de uma. Também pode utilizar __ambientes__ de Aprendizagem automática Azure para selecionar uma imagem de base específica, ou usar uma imagem personalizada que fornece.

Uma imagem base é usada como ponto de partida quando uma imagem é criada para uma implantação. Ele fornece o sistema operacional e os componentes subjacentes. Em seguida, o processo de implantação adiciona componentes adicionais, como seu modelo, ambiente Conda e outros ativos, à imagem antes de implantá-lo.

Normalmente, você cria uma imagem de base personalizada quando deseja usar o Docker para gerenciar suas dependências, manter um controle mais rígido sobre as versões de componentes ou economizar tempo durante a implantação. Por exemplo, talvez você queira padronizar em uma versão específica do Python, Conda ou outro componente. Talvez você também queira instalar o software exigido por seu modelo, onde o processo de instalação leva muito tempo. Instalar o software ao criar a imagem base significa que você não precisa instalá-lo para cada implantação.

> [!IMPORTANT]
> Ao implantar um modelo, não é possível substituir os componentes principais, como o servidor Web ou os componentes do IoT Edge. Esses componentes fornecem um ambiente de trabalho conhecido que é testado e suportado pela Microsoft.

> [!WARNING]
> A Microsoft pode não ser capaz de ajudar a solucionar problemas causados por uma imagem personalizada. Se você encontrar problemas, você pode ser solicitado a usar a imagem padrão ou uma das imagens que a Microsoft fornece para ver se o problema é específico da imagem.

Este documento está dividido em duas seções:

* Criar uma imagem de base personalizada: fornece informações para administradores e DevOps sobre como criar uma imagem personalizada e configurar a autenticação para um registro de contêiner do Azure usando o CLI do Azure e Machine Learning a CLI.
* Implantar um modelo usando uma imagem de base personalizada: fornece informações para cientistas de dados e engenheiros de DevOps/ML usando imagens personalizadas ao implantar um modelo treinado do SDK do Python ou da CLI do ML.

## <a name="prerequisites"></a>Pré-requisitos

* Um Azure Machine Learning grupo de trabalho. Para mais informações, consulte o artigo [Criar um espaço de trabalho.](how-to-manage-workspace.md)
* O [SDK de Aprendizagem automática Azure.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) 
* O [Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* A [extensão CLI para Aprendizagem automática Azure](reference-azure-machine-learning-cli.md).
* Um registo de [contentores Azure](/azure/container-registry) ou outro registo de Docker que esteja acessível na internet.
* Os passos deste documento assumem que está familiarizado com a criação e utilização de um objeto de configuração de __inferência__ como parte da implementação do modelo. Para mais informações, consulte a secção "prepare-se para implantar" de [Onde implantar e como](how-to-deploy-and-where.md#prepare-deployment-artifacts).

## <a name="create-a-custom-base-image"></a>Criar uma imagem de base personalizada

As informações nesta seção pressupõem que você está usando um registro de contêiner do Azure para armazenar imagens do Docker. Use a seguinte lista de verificação ao planejar criar imagens personalizadas para Azure Machine Learning:

* Você usará o registro de contêiner do Azure criado para o espaço de trabalho Azure Machine Learning ou um registro de contêiner do Azure autônomo?

    Ao utilizar imagens armazenadas no registo do contentor para o espaço de __trabalho,__ não precisa de autenticar o registo. A autenticação é tratada pelo espaço de trabalho.

    > [!WARNING]
    > O Registo de Contentores Azure para o seu espaço de trabalho é __criado na primeira vez que treina ou desdobra um modelo__ utilizando o espaço de trabalho. Se você tiver criado um novo espaço de trabalho, mas não for treinado ou criado um modelo, nenhum registro de contêiner do Azure existirá para o espaço de trabalho.

    Para obter informações sobre a recuperação do nome do Registo de Contentores Azure para o seu espaço de trabalho, consulte a secção de nome do registo de [contentores Get](#getname) deste artigo.

    Ao utilizar imagens armazenadas num __registo de contentores autónomos,__ terá de configurar um diretor de serviço que tenha pelo menos lido o acesso. Em seguida, forneça a ID da entidade de serviço (nome de usuário) e a senha para qualquer pessoa que usa imagens do registro. A exceção é se você tornar o registro de contêiner acessível publicamente.

    Para obter informações sobre a criação de um registo privado de contentores Azure, consulte [Criar um registo de contentores privados](/azure/container-registry/container-registry-get-started-azure-cli).

    Para obter informações sobre a utilização de diretores de serviço com registo de contentores Azure, consulte a autenticação do Registo de [Contentores Azure com os diretores de serviço](/azure/container-registry/container-registry-auth-service-principal).

* Informações de registro e imagem de contêiner do Azure: forneça o nome da imagem para qualquer pessoa que precise usá-la. Por exemplo, uma imagem chamada `myimage`, armazenada num registo chamado `myregistry`, é referenciada como `myregistry.azurecr.io/myimage` ao utilizar a imagem para a implementação do modelo

* Requisitos de imagem: Azure Machine Learning oferece suporte apenas a imagens do Docker que fornecem o seguinte software:

    * Ubuntu 16, 4 ou superior.
    * Conda 4.5. # ou superior.
    * Python 3.5. # ou 3.6. #.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Obter informações do registro de contêiner

Nesta seção, saiba como obter o nome do registro de contêiner do Azure para seu espaço de trabalho Azure Machine Learning.

> [!WARNING]
> O Registo de Contentores Azure para o seu espaço de trabalho é __criado na primeira vez que treina ou desdobra um modelo__ utilizando o espaço de trabalho. Se você tiver criado um novo espaço de trabalho, mas não for treinado ou criado um modelo, nenhum registro de contêiner do Azure existirá para o espaço de trabalho.

Se você já tiver treinado ou implantado modelos usando Azure Machine Learning, um registro de contêiner foi criado para seu espaço de trabalho. Para localizar o nome desse registro de contêiner, use as seguintes etapas:

1. Abra um novo shell ou prompt de comando e use o seguinte comando para se autenticar em sua assinatura do Azure:

    ```azurecli-interactive
    az login
    ```

    Siga os prompts para autenticar a assinatura.

2. Use o comando a seguir para listar o registro de contêiner para o espaço de trabalho. Substitua `<myworkspace>` pelo nome do espaço de trabalho Azure Machine Learning. Substitua `<resourcegroup>` pelo grupo de recursos Azure que contém o seu espaço de trabalho:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    As informações retornadas são semelhantes ao seguinte texto:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    O valor `<registry_name>` é o nome do Registo de Contentores Azure para o seu espaço de trabalho.

### <a name="build-a-custom-base-image"></a>Criar uma imagem de base personalizada

As etapas nesta seção orientam a criação de uma imagem personalizada do Docker no registro de contêiner do Azure.

1. Crie um novo ficheiro de texto chamado `Dockerfile`e utilize o seguinte texto como conteúdo:

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.5.12
    ARG PYTHON_VERSION=3.6

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. Em um shell ou prompt de comando, use o seguinte para se autenticar no registro de contêiner do Azure. Substitua o `<registry_name>` pelo nome do registo de contentores onde pretende armazenar a imagem:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Para carregar o Dockerfile e compilá-lo, use o comando a seguir. Substitua `<registry_name>` com o nome do registo de contentores que pretende armazenar a imagem em:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    Durante o processo de compilação, as informações são transmitidas de volta para a linha de comando. Se a compilação for bem-sucedida, você receberá uma mensagem semelhante ao seguinte texto:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Para obter mais informações sobre imagens de construção com um registo de contentores Azure, consulte Construir e executar uma imagem de contentor usando tarefas de registo de [contentores Azure](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli)

Para obter mais informações sobre o upload de imagens existentes para um Registo de Contentores Azure, consulte [Empurre a sua primeira imagem para um registo privado](/azure/container-registry/container-registry-get-started-docker-cli)de contentores Docker .

## <a name="use-a-custom-base-image"></a>Usar uma imagem de base personalizada

Para usar uma imagem personalizada, você precisará das seguintes informações:

* O nome da __imagem.__ Por exemplo, `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` é o caminho para uma Imagem De Docker básica fornecida pela Microsoft.
* Se a imagem estiver num __repositório privado,__ necessita das seguintes informações:

    * O __endereço__do registo. Por exemplo, `myregistry.azureecr.io`.
    * Um nome de __utilizador__ principal de serviço e __senha__ que tenha lido o acesso ao registo.

    Se você não tiver essas informações, fale com o administrador do registro de contêiner do Azure que contém a imagem.

### <a name="publicly-available-base-images"></a>Imagens base disponíveis publicamente

A Microsoft fornece várias imagens do Docker em um repositório publicamente acessível, que pode ser usado com as etapas nesta seção:

| Imagem | Descrição |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Imagem básica para Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | Contém tempo de execução ONNX para CPU inferência |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | Contém o tempo de execução ONNX e CUDA para GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | Contém ONNX Runtime e TensorRT para GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm ` | Contém tempo de execução ONNX<sup> </sup> e OpenVINO para design acelerador de visão intel baseado em Movidius<sup>TM</sup> MyriadX VPUs |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Contém onNX Runtime e OpenVINO para intel<sup> </sup> Movidius<sup>TM</sup> USB sticks |

Para obter mais informações sobre as imagens base onNX Runtime, consulte a [secção de estivador ONNX Runtime](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) no repo GitHub.

> [!TIP]
> Como essas imagens estão disponíveis publicamente, você não precisa fornecer um endereço, nome de usuário ou senha ao usá-las.

Para mais informações, consulte [os recipientes de Aprendizagem automática Azure](https://github.com/Azure/AzureML-Containers).

> [!TIP]
>__Se o seu modelo for treinado na Azure Machine Learning Compute__, utilizando __a versão 1.0.22 ou superior__ do Azure Machine Learning SDK, é criada uma imagem durante o treino. Para descobrir o nome desta imagem, use `run.properties["AzureML.DerivedImageName"]`. O exemplo a seguir demonstra como usar essa imagem:
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Usar uma imagem com o SDK do Azure Machine Learning

Para utilizar uma imagem armazenada no Registo de **Contentores Azure para**o seu espaço de trabalho, ou um registo de **contentores acessível ao público,** definir os seguintes atributos [ambiente:](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)

+ `docker.enabled=True`
+ `docker.base_image`: Definir para o registo e caminho para a imagem.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
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

Você deve adicionar o azureml-padrões com a versão > = 1.0.45 como uma dependência Pip. Esse pacote contém a funcionalidade necessária para hospedar o modelo como um serviço Web. Deve também definir inferencing_stack_version imóvel no ambiente para "mais recente", esta instalação de pacotes aptos específicos necessários pelo serviço web. 

Depois de definir o ambiente, use-o com um objeto [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) para definir o ambiente de inferência em que o modelo e o serviço web funcionarão.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Neste ponto, você pode continuar a implantação. Por exemplo, o trecho de código a seguir implantaria um serviço Web localmente usando a configuração de inferência e a imagem personalizada:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Para obter mais informações sobre a implementação, consulte [modelos de implantação com o Azure Machine Learning](how-to-deploy-and-where.md).

Para obter mais informações sobre a personalização do seu ambiente Python, consulte [Criar e gerir ambientes para treino e implantação.](how-to-use-environments.md) 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Usar uma imagem com a CLI do Machine Learning

> [!IMPORTANT]
> Atualmente, a CLI do Machine Learning pode usar imagens do registro de contêiner do Azure para seu espaço de trabalho ou repositórios publicamente acessíveis. Ele não pode usar imagens de registros privados autônomos.

Ao implantar um modelo usando a CLI do Machine Learning, você fornece um arquivo de configuração de inferência que faz referência à imagem personalizada. O documento JSON a seguir demonstra como fazer referência a uma imagem em um registro de contêiner público:

```json
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "infenv.yml",
   "extraDockerfileSteps": null,
   "sourceDirectory": null,
   "enableGpu": false,
   "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
   "baseImageRegistry": "mcr.microsoft.com"
}
```

Este ficheiro é utilizado com o comando `az ml model deploy`. O parâmetro `--ic` é utilizado para especificar o ficheiro de configuração de inferência.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Para obter mais informações sobre a implementação de um modelo utilizando o ML CLI, consulte a secção "registo, perfis e implementação do modelo" da extensão CLI para o artigo [de Aprendizagem automática Azure.](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [onde implantar e como.](how-to-deploy-and-where.md)
* Aprenda a treinar e a implementar modelos de [aprendizagem automática utilizando pipelines Azure](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops).
