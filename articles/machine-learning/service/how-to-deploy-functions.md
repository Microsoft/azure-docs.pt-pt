---
title: Implantar modelos de ml em aplicativos Azure Functions (versão prévia)
titleSuffix: Azure Machine Learning
description: Saiba como usar Azure Machine Learning para implantar um modelo em um aplicativo Azure Functions.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 11/22/2019
ms.openlocfilehash: 9fba3221656405f2bf2b1654b43d687f1915cca6
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74542397"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Implantar um modelo de aprendizado de máquina para Azure Functions (versão prévia)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Saiba como implantar um modelo de Azure Machine Learning como um aplicativo de funções no Azure Functions.

> [!IMPORTANT]
> Embora os Azure Machine Learning e Azure Functions estejam geralmente disponíveis, a capacidade de empacotar um modelo do serviço de Machine Learning para o Functions está em versão prévia.

Com Azure Machine Learning, você pode criar imagens do Docker de modelos de aprendizado de máquina treinados. O Azure Machine Learning agora tem a funcionalidade de visualização para criar esses modelos de aprendizado de máquina em aplicativos de funções, que podem ser [implantados em Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container).

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Azure Machine Learning. Para obter mais informações, consulte o artigo [criar um espaço de trabalho](how-to-manage-workspace.md) .
* O [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Um modelo de aprendizado de máquina treinado registrado em seu espaço de trabalho. Se você não tiver um modelo, use o [tutorial de classificação de imagem: treinar modelo](tutorial-train-models-with-aml.md) para treinar e registrar um.

    > [!IMPORTANT]
    > Os trechos de código neste artigo pressupõem que você definiu as seguintes variáveis:
    >
    > * `ws`-seu espaço de trabalho do Azure Machine Learning.
    > * `model`-o modelo registrado que será implantado.
    > * `inference_config`-a configuração de inferência para o modelo.
    >
    > Para obter mais informações sobre como definir essas variáveis, consulte [implantar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Preparar para implementar

Antes de implantar o, você deve definir o que é necessário para executar o modelo como um serviço Web. A lista a seguir descreve os itens básicos necessários para uma implantação:

* Um __script de entrada__. Esse script aceita solicitações, pontua a solicitação usando o modelo e retorna os resultados.

    > [!IMPORTANT]
    > O script de entrada é específico para seu modelo; Ele deve entender o formato dos dados de solicitação de entrada, o formato dos dados esperados pelo seu modelo e o formato dos dados retornados aos clientes.
    >
    > Se os dados da solicitação estiverem em um formato que não pode ser usado pelo seu modelo, o script poderá transformá-lo em um formato aceitável. Ele também pode transformar a resposta antes de retornar a ela para o cliente.
    >
    > Por padrão, ao empacotar para funções, a entrada é tratada como texto. Se você estiver interessado em consumir os bytes brutos da entrada (por exemplo, para gatilhos de BLOB), deverá usar [AMLRequest para aceitar dados brutos](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#binary-data).


* **Dependências**, como scripts auxiliares ou pacotes python/Conda necessários para executar o script ou modelo de entrada

Essas entidades são encapsuladas em uma __configuração de inferência__. A configuração de inferência referencia o script de entrada e outras dependências.

> [!IMPORTANT]
> Ao criar uma configuração de inferência para uso com Azure Functions, você deve usar um objeto de [ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) . O exemplo a seguir demonstra como criar um objeto de ambiente e usá-lo com uma configuração de inferência:
>
> ```python
> from azureml.core import Environment
> from azureml.core.environment import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
> ```

Para obter mais informações sobre ambientes, consulte [criar e gerenciar ambientes para treinamento e implantação](how-to-use-environments.md).

Para obter mais informações sobre a configuração de inferência, consulte [implantar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

> [!IMPORTANT]
> Ao implantar o para o functions, você não precisa criar uma __configuração de implantação__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Instalar o pacote de visualização do SDK para o suporte do Functions

Para criar pacotes para Azure Functions, você deve instalar o pacote de visualização do SDK.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Criar a imagem

Para criar a imagem do Docker que é implantada no Azure Functions, use [azureml. contrib. Functions. Package](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py) ou a função de pacote específica para o gatilho que você está interessado em usar. O trecho de código a seguir demonstra como criar um novo pacote com um gatilho de blob do modelo e a configuração de inferência:

> [!NOTE]
> O trecho de código pressupõe que `model` contém um modelo registrado e que `inference_config` contém a configuração para o ambiente de inferência. Para obter mais informações, consulte [implantar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

Quando `show_output=True`, a saída do processo de Build do Docker é mostrada. Quando o processo for concluído, a imagem terá sido criada no registro de contêiner do Azure para seu espaço de trabalho. Depois que a imagem tiver sido criada, o local no registro de contêiner do Azure será exibido. O local retornado está no formato `<acrinstance>.azurecr.io/package@sha256:<hash>`.

> [!NOTE]
> O empacotamento para funções atualmente dá suporte a gatilhos HTTP, gatilhos de BLOB e gatilhos do barramento de serviço Para obter mais informações sobre gatilhos, consulte [associações de Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob?tabs=csharp#trigger---blob-name-patterns).

> [!IMPORTANT]
> Salve as informações de local, pois elas são usadas durante a implantação da imagem.

## <a name="deploy-image-as-a-web-app"></a>Implantar imagem como um aplicativo Web

1. Use o comando a seguir para obter as credenciais de logon para o registro de contêiner do Azure que contém a imagem. Substitua `<acrinstance>` pelo valor retornado anteriormente da `package.location`: 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    A saída desse comando é semelhante ao seguinte documento JSON:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    Salve o valor de __nome de usuário__ e uma das __senhas__.

1. Se você ainda não tiver um grupo de recursos ou um plano do serviço de aplicativo para implantar o serviço, os comandos a seguir demonstrarão como criar ambos:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku EP1 --is-linux
    ```

    Neste exemplo, um tipo de preço do _Linux Premium_ (`--sku EP1`) é usado.

    > [!IMPORTANT]
    > As imagens criadas por Azure Machine Learning usam o Linux, portanto, você deve usar o parâmetro `--is-linux`.

1. Para criar o aplicativo de funções, use o comando a seguir. Substitua `<app-name>` pelo nome que você deseja usar. Substitua `<acrinstance>` e `<imagename>` pelos valores de `package.location` retornados anteriormente:

    ```azurecli-interactive
    az storage account create --name 
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename>
    ```

    > [!IMPORTANT]
    > Neste ponto, o aplicativo de funções foi criado. No entanto, como você não forneceu a cadeia de conexão para o gatilho de BLOB ou credenciais para o registro de contêiner do Azure que contém a imagem, o aplicativo de funções não está ativo. Nas próximas etapas, você fornecerá a cadeia de conexão e as informações de autenticação para o registro de contêiner. 

1. Crie a conta de armazenamento para usar como o gatilho e obtenha a cadeia de conexão.

    ```azurecli-interactive
    az storage account create --name triggerStorage --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name triggerStorage --query connectionString --output tsv
    ```
    Registre essa cadeia de conexão para fornecer ao aplicativo de funções. Usaremos isso mais tarde quando solicitarmos `<triggerConnectionString>`

1. Crie os contêineres para a entrada e a saída na conta de armazenamento. 

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. Você precisará recuperar a marca associada ao contêiner criado usando o seguinte comando:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    A marca mais recente mostrada será `imagetag`da abaixo.

1. Para fornecer ao aplicativo de funções as credenciais necessárias para acessar o registro de contêiner, use o comando a seguir. Substitua `<app-name>` pelo nome que você deseja usar. Substitua `<acrinstance>` e `<imagetag>` pelos valores da chamada AZ CLI na etapa anterior. Substitua `<username>` e `<password>` com as informações de logon do ACR recuperadas anteriormente:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Esse comando retorna informações semelhantes ao seguinte documento JSON:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

Neste ponto, o aplicativo de funções começa a carregar a imagem.

> [!IMPORTANT]
> Pode levar vários minutos para que a imagem seja carregada. Você pode monitorar o progresso usando o portal do Azure.

## <a name="next-steps"></a>Passos seguintes

* Saiba como configurar seu aplicativo de funções na documentação do [Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-linux-custom-imag) .
* Saiba mais sobre o armazenamento de BLOBs que dispara [associações de armazenamento de BLOBs do Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob).
* [Implante seu modelo no serviço Azure app](how-to-deploy-app-service.md).
* [Consumir um modelo de ML implantado como um serviço Web](how-to-consume-web-service.md)
* [Referência da API](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)