---
title: Implantar modelos com uma imagem personalizada do Docker
titleSuffix: Azure Machine Learning service
description: Saiba como usar uma imagem personalizada do Docker ao implantar seus modelos de serviço Azure Machine Learning. Ao implantar um modelo treinado, uma imagem do Docker é criada para hospedar a imagem, o servidor Web e outros componentes necessários para executar o serviço. Embora Azure Machine Learning serviço forneça uma imagem padrão para você, você também pode usar sua própria imagem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/11/2019
ms.openlocfilehash: 0025f488f6a9b0af4e05a8bdf3ddf36c089d4d9f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856125"
---
# <a name="deploy-a-model-using-a-custom-docker-image"></a>Implantar um modelo usando uma imagem personalizada do Docker

Saiba como usar uma imagem personalizada do Docker ao implantar modelos treinados com o serviço de Azure Machine Learning.

Quando você implanta um modelo treinado em um serviço Web ou IoT Edge dispositivo, uma imagem do Docker é criada. Essa imagem contém o modelo, o ambiente Conda e os ativos necessários para usar o modelo. Ele também contém um servidor Web para lidar com solicitações de entrada quando implantado como um serviço Web e os componentes necessários para trabalhar com o Hub IoT do Azure.

Azure Machine Learning serviço fornece uma imagem padrão do Docker para que você não precise se preocupar com a criação de uma. Você também pode usar uma imagem personalizada que você cria como uma _imagem de base_. Uma imagem base é usada como ponto de partida quando uma imagem é criada para uma implantação. Ele fornece o sistema operacional e os componentes subjacentes. Em seguida, o processo de implantação adiciona componentes adicionais, como seu modelo, ambiente Conda e outros ativos, à imagem antes de implantá-lo.

Normalmente, você cria uma imagem personalizada quando deseja controlar as versões de componentes ou economizar tempo durante a implantação. Por exemplo, talvez você queira padronizar em uma versão específica do Python, Conda ou outro componente. Talvez você também queira instalar o software exigido por seu modelo, onde o processo de instalação leva muito tempo. Instalar o software ao criar a imagem base significa que você não precisa instalá-lo para cada implantação.

> [!IMPORTANT]
> Ao implantar um modelo, não é possível substituir os componentes principais, como o servidor Web ou os componentes do IoT Edge. Esses componentes fornecem um ambiente de trabalho conhecido que é testado e suportado pela Microsoft.

> [!WARNING]
> A Microsoft pode não ser capaz de ajudar a solucionar problemas causados por uma imagem personalizada. Se você encontrar problemas, você pode ser solicitado a usar a imagem padrão ou uma das imagens que a Microsoft fornece para ver se o problema é específico da imagem.

Este documento está dividido em duas seções:

* Criar uma imagem personalizada: Fornece informações para administradores e DevOps sobre como criar uma imagem personalizada e configurar a autenticação para um registro de contêiner do Azure usando o CLI do Azure e Machine Learning a CLI.
* Usar uma imagem personalizada: Fornece informações para cientistas de dados e DevOps/MLOps usando imagens personalizadas ao implantar um modelo treinado do SDK do Python ou da CLI do ML.

## <a name="prerequisites"></a>Pré-requisitos

* Um grupo de trabalho do Azure Machine Learning Service. Para obter mais informações, consulte o artigo [criar um espaço de trabalho](how-to-manage-workspace.md) .
* O [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). 
* O [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* A [extensão da CLI para Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Um [registro de contêiner do Azure](/azure/container-registry) ou outro registro do Docker que está acessível na Internet.
* As etapas neste documento pressupõem que você esteja familiarizado com a criação e o uso de um objeto de __configuração__ de inferência como parte da implantação do modelo. Para obter mais informações, consulte a seção "preparar para implantar" de [onde implantar e como](how-to-deploy-and-where.md#prepare-to-deploy).

## <a name="create-a-custom-image"></a>Criar uma imagem personalizada

As informações nesta seção pressupõem que você está usando um registro de contêiner do Azure para armazenar imagens do Docker. Use a seguinte lista de verificação ao planejar criar imagens personalizadas para Azure Machine Learning serviço:

* Você usará o registro de contêiner do Azure criado para o espaço de trabalho do Azure Machine Learning Service ou um registro de contêiner do Azure autônomo?

    Ao usar imagens armazenadas no __registro de contêiner para o espaço de trabalho__, você não precisa se autenticar no registro. A autenticação é tratada pelo espaço de trabalho.

    > [!WARNING]
    > O Rzegistry de contêiner do Azure para seu espaço de trabalho é __criado na primeira vez que você treina ou implanta um modelo__ usando o espaço de trabalho. Se você tiver criado um novo espaço de trabalho, mas não for treinado ou criado um modelo, nenhum registro de contêiner do Azure existirá para o espaço de trabalho.

    Para obter informações sobre como recuperar o nome do registro de contêiner do Azure para seu espaço de trabalho, consulte a seção [obter nome do registro de contêiner](#getname) deste artigo.

    Ao usar as imagens armazenadas em um __registro de contêiner autônomo__, será necessário configurar uma entidade de serviço que tenha pelo menos acesso de leitura. Em seguida, forneça a ID da entidade de serviço (nome de usuário) e a senha para qualquer pessoa que usa imagens do registro. A exceção é se você tornar o registro de contêiner acessível publicamente.

    Para obter informações sobre como criar um registro de contêiner do Azure privado, consulte [criar um registro de contêiner privado](/azure/container-registry/container-registry-get-started-azure-cli).

    Para obter informações sobre como usar entidades de serviço com o registro de contêiner do Azure, consulte [autenticação do registro de contêiner do Azure com entidades de serviço](/azure/container-registry/container-registry-auth-service-principal).

* Informações sobre o registro de contêiner do Azure e a imagem: Forneça o nome da imagem para qualquer pessoa que precise usá-la. Por exemplo, uma imagem chamada `myimage`, armazenada em um registro `myregistry`chamado, é referenciada como `myregistry.azurecr.io/myimage` ao usar a imagem para implantação de modelo

* Requisitos de imagem: O Azure Machine Learning Service oferece suporte apenas a imagens do Docker que fornecem o seguinte software:

    * Ubuntu 16, 4 ou superior.
    * Conda 4.5. # ou superior.
    * Python 3.5. # ou 3.6. #.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Obter informações do registro de contêiner

Nesta seção, saiba como obter o nome do registro de contêiner do Azure para seu espaço de trabalho do Azure Machine Learning Service.

> [!WARNING]
> O registro de contêiner do Azure para seu espaço de trabalho é __criado na primeira vez que você treina ou implanta um modelo__ usando o espaço de trabalho. Se você tiver criado um novo espaço de trabalho, mas não for treinado ou criado um modelo, nenhum registro de contêiner do Azure existirá para o espaço de trabalho.

Se você já tiver treinado ou implantado modelos usando o serviço de Azure Machine Learning, um registro de contêiner foi criado para seu espaço de trabalho. Para localizar o nome desse registro de contêiner, use as seguintes etapas:

1. Abra um novo shell ou prompt de comando e use o seguinte comando para se autenticar em sua assinatura do Azure:

    ```azurecli-interactive
    az login
    ```

    Siga os prompts para autenticar a assinatura.

2. Use o comando a seguir para listar o registro de contêiner para o espaço de trabalho. Substitua `<myworkspace>` pelo nome do espaço de trabalho do serviço Azure Machine Learning. Substitua `<resourcegroup>` pelo grupo de recursos do Azure que contém seu espaço de trabalho:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    As informações retornadas são semelhantes ao seguinte texto:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    O `<registry_name>` valor é o nome do registro de contêiner do Azure para seu espaço de trabalho.

### <a name="build-a-custom-image"></a>Criar uma imagem personalizada

As etapas nesta seção orientam a criação de uma imagem personalizada do Docker no registro de contêiner do Azure.

1. Crie um novo arquivo de texto `Dockerfile`chamado e use o seguinte texto como o conteúdo:

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

2. Em um shell ou prompt de comando, use o seguinte para se autenticar no registro de contêiner do Azure. `<registry_name>` Substitua pelo nome do registro de contêiner no qual você deseja armazenar a imagem:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Para carregar o Dockerfile e compilá-lo, use o comando a seguir. Substitua `<registry_name>` pelo nome do registro de contêiner no qual você deseja armazenar a imagem:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    Durante o processo de compilação, as informações são transmitidas de volta para a linha de comando. Se a compilação for bem-sucedida, você receberá uma mensagem semelhante ao seguinte texto:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Para obter mais informações sobre como criar imagens com um registro de contêiner do Azure, consulte [Compilar e executar uma imagem de contêiner usando tarefas do registro de contêiner do Azure](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli)

Para obter mais informações sobre como carregar imagens existentes para um registro de contêiner do Azure, consulte [enviar por push sua primeira imagem para um registro de contêiner privado do Docker](/azure/container-registry/container-registry-get-started-docker-cli).

## <a name="use-a-custom-image"></a>Utilizar uma imagem personalizada

Para usar uma imagem personalizada, você precisará das seguintes informações:

* O __nome da imagem__. Por exemplo, `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` é o caminho para uma imagem básica do Docker fornecida pela Microsoft.
* Se a imagem estiver em um __repositório privado__, você precisará das seguintes informações:

    * O __endereço__do registro. Por exemplo, `myregistry.azureecr.io`.
    * Um nome de __usuário__ e __senha__ da entidade de serviço que tem acesso de leitura ao registro.

    Se você não tiver essas informações, fale com o administrador do registro de contêiner do Azure que contém a imagem.

### <a name="publicly-available-images"></a>Imagens disponíveis publicamente

A Microsoft fornece várias imagens do Docker em um repositório publicamente acessível, que pode ser usado com as etapas nesta seção:

| Image | Descrição |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Imagem básica para o serviço de Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0` | Contém o tempo de execução ONNX. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7` | Contém os componentes de tempo de execução ONNX e CUDA. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03` | Contém ONNX Runtime e TensorRT. |

> [!TIP]
> Como essas imagens estão disponíveis publicamente, você não precisa fornecer um endereço, nome de usuário ou senha ao usá-las.

> [!IMPORTANT]
> As imagens da Microsoft que usam CUDA ou TensorRT devem ser usadas somente em serviços Microsoft Azures.

> [!TIP]
>__Se seu modelo for treinado em Azure Machine Learning computação__, usando a __versão 1.0.22 ou superior__ do SDK do Azure Machine Learning, uma imagem será criada durante o treinamento. Para descobrir o nome desta imagem, use `run.properties["AzureML.DerivedImageName"]`. O exemplo a seguir demonstra como usar essa imagem:
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Usar uma imagem com o SDK do Azure Machine Learning

Para usar uma imagem personalizada, defina a `base_image` Propriedade do [objeto de configuração](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) de inferência como o endereço da imagem:

```python
# use an image from a registry named 'myregistry'
inference_config.base_image = "myregistry.azurecr.io/myimage:v1"
```

Esse formato funciona para as duas imagens armazenadas no registro de contêiner do Azure para seu espaço de trabalho e registros de contêiner que estão publicamente acessíveis. Por exemplo, o código a seguir usa uma imagem padrão fornecida pela Microsoft:

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

Para usar uma imagem de um __registro de contêiner privado__ que não está em seu espaço de trabalho, você deve especificar o endereço do repositório e um nome de usuário e senha:

```python
# Use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

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

Esse arquivo é usado com o `az ml model deploy` comando. O `--ic` parâmetro é usado para especificar o arquivo de configuração de inferência.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Para obter mais informações sobre como implantar um modelo usando a CLI do ML, consulte a seção "registro do modelo, criação de perfil e implantação" do artigo [extensão da CLI para Azure Machine Learning serviço](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) .

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [onde implantar e como](how-to-deploy-and-where.md).
* Saiba como [treinar e implantar modelos de aprendizado de máquina usando o Azure pipelines](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops).
