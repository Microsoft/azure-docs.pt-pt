---
title: Como implementar um modelo com uma imagem personalizada do Docker
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar uma imagem personalizada do Docker ao implementar os seus modelos de serviço do Azure Machine Learning. Quando implementar um modelo preparado, é criada uma imagem do Docker para o anfitrião a imagem, servidor web e outros componentes necessários para executar o serviço. Enquanto o serviço Azure Machine Learning fornece uma imagem padrão para, também pode utilizar a sua própria imagem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/05/2019
ms.openlocfilehash: 29fdb200075a5b5843944a7a890cc2f8ad61f1ee
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543859"
---
# <a name="deploy-a-model-using-a-custom-docker-image"></a>Implementar um modelo com uma imagem personalizada do Docker

Saiba como utilizar uma imagem personalizada do Docker, quando implementar modelos de formação com o serviço Azure Machine Learning.

Quando implementa um modelo preparado para um serviço web ou um dispositivo IoT Edge, é criada uma imagem do Docker. Esta imagem contém o modelo, ambiente de conda e os ativos necessários para utilizar o modelo. Também contém um servidor web para processar solicitações de entrada quando implementada como um serviço da web e os componentes necessários para trabalhar com o IoT Hub do Azure.

O serviço de Machine Learning do Azure fornece uma imagem do Docker padrão para que não precisa se preocupar sobre a criação de um. Também pode utilizar uma imagem personalizada que criou como um _imagem base_. Uma imagem de base é utilizada como ponto de partida quando uma imagem é criada para uma implementação. Ele fornece o sistema operativo e componentes subjacentes. O processo de implantação, em seguida, adiciona componentes adicionais, como o seu modelo, ambiente de conda e outros ativos, a imagem antes de o implementar.

Normalmente, criar uma imagem personalizada quando deseja controlar versões de componente ou economizar tempo durante a implementação. Por exemplo, pode querer padronizar numa versão específica do Python, Conda ou outro componente. Pode também querer instalar o software exigido pelo seu modelo, em que o processo de instalação demora muito tempo. Instalar o software, ao criar a imagem de base significa que não tem de instalá-lo para cada implementação.

> [!IMPORTANT]
> Ao implementar um modelo, não é possível substituir componentes principais, como o servidor web ou de componentes do IoT Edge. Estes componentes fornecem um ambiente de trabalho conhecidos que é testado e suportado pela Microsoft.

> [!WARNING]
> Microsoft talvez não consiga ajudar a resolver problemas causados por uma imagem personalizada. Se tiver problemas, poderá ser-lhe pedido para utilizar a imagem predefinida ou uma das imagens, a Microsoft fornece para ver se o problema é específico para a sua imagem.

Este documento é dividido em duas seções:

* Crie uma imagem personalizada: Fornece informações para administradores e DevOps sobre como criar uma imagem personalizada e configurar a autenticação para o Azure Container Registry com a CLI do Azure e da CLI do Machine Learning.
* Utilize uma imagem personalizada: Fornece informações para os cientistas de dados e DevOps/MLOps sobre como utilizar imagens personalizadas quando implementar um modelo preparado a partir do SDK de Python ou a CLI do ML.

## <a name="prerequisites"></a>Pré-requisitos

* Um grupo de trabalho de serviço do Azure Machine Learning. Para obter mais informações, consulte a [criar uma área de trabalho](setup-create-workspace.md) artigo.
* O Azure Machine Learning SDK. Para obter mais informações, consulte a secção de SDK de Python do [criar uma área de trabalho](setup-create-workspace.md#sdk) artigo.
* O [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* O [extensão da CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Uma [Azure Container Registry](/azure/container-registry) ou outro registo do Docker que está acessível na internet.
* Os passos neste documento partem do princípio de que está familiarizado com a criação e a utilizar um __configuração de inferência de tipos__ objeto como parte da implementação de modelo. Para obter mais informações, consulte a secção "Preparar a implementação" [onde pretende implementar e como](how-to-deploy-and-where.md#prepare-to-deploy).

## <a name="create-a-custom-image"></a>Criar uma imagem personalizada

As informações desta secção parte do princípio de que está a utilizar um registo de contentor do Azure para armazenar imagens do Docker. Utilize a lista de verificação seguinte ao planejar para criar imagens personalizadas para o serviço Azure Machine Learning:

* Irá utilizar o Azure Container Registry criado para a área de trabalho do serviço do Azure Machine Learning ou autónoma do Azure Container Registry?

    Quando a utilização de imagens armazenadas na __registo de contentor para a área de trabalho__, não é necessário autenticar para o registo. Autenticação é manipulada pela área de trabalho.

    > [!WARNING]
    > É o Rzegistry de contentor do Azure para a área de trabalho __criado na primeira vez, preparar ou implementar um modelo__ usando a área de trabalho. Não se tiver criado uma nova área de trabalho, mas não treinados ou criou um modelo, existem nenhum registo de contentor do Azure para a área de trabalho.

    Para obter informações sobre como obter o nome do registo de contentor do Azure para a área de trabalho, consulte a [nome do registo de contentor de Get](#getname) seção deste artigo.

    Quando a utilização de imagens armazenados num __registo de contentor autónomo__, terá de configurar um principal de serviço, pelo menos, tem acesso de leitura. , Em seguida, fornecer o ID de principal de serviço (nome de utilizador) e a palavra-passe a qualquer pessoa que utiliza imagens a partir do registo. A exceção é se torne o registo de contentor publicamente acessíveis.

    Para obter informações sobre como criar um Azure Container Registry privado, consulte [criar um registo de contentor privado](/azure/container-registry/container-registry-get-started-azure-cli).

    Para obter informações sobre como utilizar principais de serviço com o Azure Container Registry, veja [autenticação do Azure Container Registry com principais de serviço](/azure/container-registry/container-registry-auth-service-principal).

* Informações de registo de contentor e a imagem do Azure: Forneça o nome de imagem para quem precisa usá-lo. Por exemplo, uma imagem designada `myimage`, armazenados num registo denominado `myregistry`, é referenciada como `myregistry.azurecr.io/myimage` ao utilizar a imagem para a implementação do modelo

* Requisitos de imagem: O serviço do Azure Machine Learning só suporta imagens do Docker que fornecem o seguinte software:

    * Ubuntu 16.04 ou superior.
    * Conda 4.5. # ou superior.
    * Python 3.5. # ou 3.6. #.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Obtenha informações de registo de contentor

Nesta secção, saiba como obter o nome do registo de contentor do Azure para a área de trabalho do serviço do Azure Machine Learning.

> [!WARNING]
> O Azure Container Registry para sua área de trabalho é __criado na primeira vez, preparar ou implementar um modelo__ usando a área de trabalho. Não se tiver criado uma nova área de trabalho, mas não treinados ou criou um modelo, existem nenhum registo de contentor do Azure para a área de trabalho.

Se já tiver preparado ou implementar modelos com o serviço Azure Machine Learning, um registo de contentor foi criado para a área de trabalho. Para localizar o nome deste registo de contentor, utilize os seguintes passos:

1. Abra um novo shell ou a linha de comandos e utilize o seguinte comando para autenticar a sua subscrição do Azure:

    ```azurecli-interactive
    az login
    ```

    Siga as instruções para autenticar para a subscrição.

2. Utilize o seguinte comando para listar o registo de contentor para a área de trabalho. Substitua `<myworkspace>` com o nome de área de trabalho do serviço Azure Machine Learning. Substitua `<resourcegroup>` com o grupo de recursos do Azure que contém a sua área de trabalho:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    As informações devolvidas são semelhantes ao seguinte texto:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    O `<registry_name>` valor é o nome do registo de contentor do Azure para a área de trabalho.

### <a name="build-a-custom-image"></a>Criar uma imagem personalizada

Os passos nestas instruções da secção criar uma imagem personalizada do Docker no seu registo de contentor do Azure.

1. Criar um novo arquivo de texto chamado `Dockerfile`e utilize o seguinte texto como o conteúdo:

    ```text
    FROM ubuntu:16.04

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-4.5.12-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y python=3.6 && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. A partir de um shell ou a linha de comandos, utilize o seguinte para autenticar para o Azure Container Registry. Substitua o `<registry_name>` com o nome do registo de contentor que pretende armazenar a imagem:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Para carregar o Dockerfile e criá-lo, utilize o seguinte comando. Substitua `<registry_name>` com o nome do registo de contentor que pretende armazenar a imagem:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    Durante o processo de compilação, informações são transmitidas para o de volta para a linha de comandos. Se a compilação for concluída com êxito, receberá uma mensagem semelhante ao seguinte texto:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Para obter mais informações sobre a criação de imagens com um Azure Container Registry, veja [compilar e executar uma imagem de contentor com tarefas de registo de contentor do Azure](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli)

Para obter mais informações sobre o carregamento de imagens existentes para o Azure Container Registry, veja [enviar a sua primeira imagem para um registo de contentor do Docker privado](/azure/container-registry/container-registry-get-started-docker-cli).

## <a name="use-a-custom-image"></a>Utilizar uma imagem personalizada

Para utilizar uma imagem personalizada, terá das seguintes informações:

* O __nome da imagem__. Por exemplo, `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` é o caminho para uma imagem do Docker básico fornecido pela Microsoft.
* Se a imagem estiver num __repositório privado__, precisa das seguintes informações:

    * O Registro __endereço__. Por exemplo, `myregistry.azureecr.io`.
    * Um principal de serviço __nome de utilizador__ e __palavra-passe__ que tem acesso de leitura para o registo.

    Se não tiver estas informações, fale com o administrador para o Azure Container Registry, que contém a imagem.

### <a name="publicly-available-images"></a>Imagens publicamente disponíveis

A Microsoft fornece várias imagens do docker num repositório publicamente acessível, que pode ser utilizado com os passos nesta secção:

| Image | Descrição |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Imagem básica para o serviço Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0` | Contém o tempo de execução ONNX. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7` | Contém os componentes CUDA e ONNX tempo de execução. |
| `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03` | Contém ONNX tempo de execução e TensorRT. |

> [!TIP]
> Uma vez que estas imagens estão publicamente disponíveis, não é necessário fornecer um endereço, o nome de utilizador ou a palavra-passe quando usá-los.

> [!IMPORTANT]
> Imagens da Microsoft que utilizam CUDA ou TensorRT devem ser utilizadas apenas em serviços do Microsoft Azure.

> [!TIP]
>__Se é preparado o modelo na computação do Azure Machine Learning__, utilizando __versão 1.0.22 ou superior__ do SDK do Azure Machine Learning, uma imagem é criada durante o treinamento. Para descobrir o nome da imagem, use `run.properties["AzureML.DerivedImageName"]`. O exemplo seguinte demonstra como utilizar esta imagem:
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Utilizar uma imagem com o SDK do Azure Machine Learning

Para utilizar uma imagem personalizada, defina o `base_image` propriedade o [objeto de configuração de inferência de tipos](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) para o endereço da imagem:

```python
# use an image from a registry named 'myregistry'
inference_config.base_image = "myregistry.azurecr.io/myimage:v1"
```

Este formato funciona para ambas as imagens armazenadas no registo de contentor do Azure para os registos de área de trabalho e um contentor que podem ser acessados publicamente. Por exemplo, o código seguinte utiliza uma imagem padrão fornecida pela Microsoft:

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

Para utilizar uma imagem a partir de um __registo de contentor privado__ que não está na sua área de trabalho, tem de especificar o endereço do repositório e um nome de utilizador e palavra-passe:

```python
# Use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

### <a name="use-an-image-with-the-machine-learning-cli"></a>Utilizar uma imagem com a CLI do Machine Learning

> [!IMPORTANT]
> Atualmente a CLI do Machine Learning podem utilizar imagens do Azure Container Registry para a sua área de trabalho ou repositórios acessíveis publicamente. Ele não é possível usar imagens de registos privados de autónomo.

Ao implementar um modelo com a CLI do Machine Learning, é fornecer um arquivo de configuração de inferência de tipos que referencia a imagem personalizada. O documento JSON seguinte demonstra como fazer referência a uma imagem num registo de contentor público:

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

Este ficheiro é utilizado com o `az ml model deploy` comando. O `--ic` parâmetro é utilizado para especificar o ficheiro de configuração de inferência de tipos.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Para obter mais informações sobre como implementar um modelo com a CLI do ML, consulte a secção "registo do modelo, a criação de perfis e a implementação" do [extensão CLI para o serviço Azure Machine Learning](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) artigo.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [onde pretende implementar e como](how-to-deploy-and-where.md).
* Saiba como [Train e implementar modelos de machine learning Pipelines do Azure a utilizar](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops).
