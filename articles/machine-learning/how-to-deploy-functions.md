---
title: Implementar modelos ml para aplicações de funções azure (pré-visualização)
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o Azure Machine Learning para implementar um modelo numa App de Funções Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 11/22/2019
ms.openlocfilehash: 29c91cf14413a11804de82eeaf08d628b125d76a
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471946"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Implementar um modelo de aprendizagem automática para funções Azure (pré-visualização)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a implementar um modelo do Azure Machine Learning como uma aplicação de funções em Funções Azure.

> [!IMPORTANT]
> Embora as funções Azure Machine Learning e Azure estejam geralmente disponíveis, a capacidade de embalar um modelo do serviço de Machine Learning para Funções está em pré-visualização.

Com o Azure Machine Learning, pode criar imagens do Docker a partir de modelos de machine learning treinados. O Azure Machine Learning tem agora a funcionalidade de pré-visualização para construir estes modelos de machine learning em aplicações de função, que podem ser [implementadas em Funções Azure.](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container)

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Azure Machine Learning. Para mais informações, consulte o artigo [Criar um espaço de trabalho.](how-to-manage-workspace.md)
* O [Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Um modelo de aprendizagem automática treinado registado no seu espaço de trabalho. Se não tiver um modelo, utilize o tutorial de classificação de [imagem: modelo de comboio](tutorial-train-models-with-aml.md) para treinar e registar um.

    > [!IMPORTANT]
    > Os fragmentos de código neste artigo assumem que definiu as seguintes variáveis:
    >
    > * `ws` - O seu espaço de trabalho de Aprendizagem automática Azure.
    > * `model` - O modelo registado que será implantado.
    > * `inference_config` - A configuração de inferência para o modelo.
    >
    > Para obter mais informações sobre a definição destas variáveis, consulte [modelos de implantação com aprendizagem automática Azure](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Preparar para a implementação

Antes de ser implementado, deve definir o que é necessário para executar o modelo como um serviço web. A lista seguinte descreve os itens básicos necessários para uma implantação:

* Um __guião de entrada.__ Este script aceita pedidos, marca o pedido usando o modelo e devolve os resultados.

    > [!IMPORTANT]
    > O script de entrada é específico do seu modelo; deve compreender o formato dos dados de pedido de entrada, o formato dos dados esperados pelo seu modelo e o formato dos dados devolvidos aos clientes.
    >
    > Se os dados do pedido estiverem num formato que não seja utilizável pelo seu modelo, o script pode transformá-lo num formato aceitável. Também pode transformar a resposta antes de voltar ao cliente.
    >
    > Por predefinição, quando se acondicionamento para funções, a entrada é tratada como texto. Se estiver interessado em consumir os bytes crus da entrada (por exemplo, para os gatilhos Blob), deve utilizar a [AMLRequest para aceitar dados brutos](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#binary-data).


* **Dependências**, tais como scripts de ajudante ou pacotes Python/Conda necessários para executar o script ou modelo de entrada

Estas entidades são encapsuladas numa configuração de __inferência.__ A configuração da inferência refere o script de entrada e outras dependências.

> [!IMPORTANT]
> Ao criar uma configuração de inferência para utilização com funções Azure, deve utilizar um objeto [Ambiente.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) Por favor, note que se estiver a definir um ambiente personalizado, deve adicionar incumprimentos em azureml com versão >= 1.0.45 como dependência do pip. Este pacote contém a funcionalidade necessária para hospedar o modelo como um serviço web. O exemplo que se segue demonstra a criação de um objeto ambiental e a sua utilização com uma configuração de inferência:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

Para obter mais informações sobre ambientes, consulte [Criar e gerir ambientes para formação e implantação.](how-to-use-environments.md)

Para obter mais informações sobre a configuração de inferência, consulte [Modelos de implantação com aprendizagem automática Azure](how-to-deploy-and-where.md).

> [!IMPORTANT]
> Ao ser implantado nas Funções, não é necessário criar uma configuração de __implementação__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Instale o pacote de pré-visualização SDK para suporte a funções

Para construir pacotes para funções Azure, tem de instalar o pacote de pré-visualização SDK.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Criar a imagem

Para criar a imagem Do Docker que é implantada para funções Azure, utilize [funções azureml.contrib.(package)](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py) ou a função de embalagem específica para o gatilho que está interessado em utilizar. O seguinte código de corte demonstra como criar um novo pacote com um gatilho blob do modelo e configuração de inferência:

> [!NOTE]
> O código snippet pressupõe que `model` contém um modelo registado, e que `inference_config` contém a configuração para o ambiente de inferência. Para mais informações, consulte [Implementar modelos com Aprendizagem automática Azure](how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

Quando `show_output=True`, a saída do processo de construção do Docker é mostrada. Uma vez terminado o processo, a imagem foi criada no Registo de Contentores Azure para o seu espaço de trabalho. Uma vez construída a imagem, a localização no registo do contentor Azure é exibida. A localização devolvida encontra-se no formato `<acrinstance>.azurecr.io/package@sha256:<hash>`.

> [!NOTE]
> A embalagem para funções suporta atualmente os gatilhos HTTP, os gatilhos blob e os gatilhos de ônibus de serviço. Para obter mais informações sobre os gatilhos, consulte as [encadernações das Funções Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob-trigger#blob-name-patterns).

> [!IMPORTANT]
> Guarde as informações de localização, pois é utilizada ao implementar a imagem.

## <a name="deploy-image-as-a-web-app"></a>Implementar imagem como uma aplicação web

1. Utilize o seguinte comando para obter as credenciais de login para o Registo de Contentores Azure que contenha a imagem. Substitua `<myacr>` pelo valor devolvido anteriormente a partir de `package.location`: 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    A saída deste comando é semelhante ao seguinte documento JSON:

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

    Guarde o valor para o nome de __utilizador__ e uma das __palavras-passe__.

1. Se ainda não tem um grupo de recursos ou um plano de serviço de aplicações para implementar o serviço, os seguintes comandos demonstram como criar ambos:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku EP1 --is-linux
    ```

    Neste exemplo, é utilizado um nível de preços _Linux Premium_ (`--sku EP1`).

    > [!IMPORTANT]
    > As imagens criadas pelo Azure Machine Learning usam o Linux, pelo que deve utilizar o parâmetro `--is-linux`.

1. Crie a conta de armazenamento para usar para o armazenamento de trabalho web e obtenha a sua cadeia de ligação. Substitua `<webjobStorage>` pelo nome que pretende utilizar.

    ```azurecli-interactive
    az storage account create --name triggerStorage --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. Para criar a aplicação de função, utilize o seguinte comando. Substitua `<app-name>` pelo nome que pretende utilizar. Substitua `<acrinstance>` e `<imagename>` os valores de `package.location` devolvidos anteriormente. Substitua `<webjobStorage>` pelo nome da conta de armazenamento da etapa anterior:

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > Neste ponto, a aplicação de funções foi criada. No entanto, uma vez que não forneceu a cadeia de ligação para o gatilho ou credenciais do registo de contentores Azure que contém a imagem, a aplicação de função não está ativa. Nos próximos passos, fornece a corda de ligação e as informações de autenticação para o registo do recipiente. 

1. Crie a conta de armazenamento para usar para o armazenamento do gatilho blob e obtenha a sua cadeia de ligação. Substitua `<triggerStorage>` pelo nome que pretende utilizar.

    ```azurecli-interactive
    az storage account create --name <triggerStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <triggerStorage> --query connectionString --output tsv
    ```
    Grave esta cadeia de ligação para fornecer à aplicação de função. Vamos usá-lo mais tarde quando pedirmos `<triggerConnectionString>`

1. Crie os recipientes para a entrada e saída na conta de armazenamento. Substitua `<triggerConnectionString>` com a corda de ligação devolvida anteriormente:

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. Para associar a cadeia de ligação do gatilho à aplicação de função, utilize o seguinte comando. Substitua `<app-name>` pelo nome da aplicação de funções. Substitua `<triggerConnectionString>` com a corda de ligação devolvida anteriormente:

    ```azurecli-interactive
    az functionapp config appsettings set --name <app-name> --resource-group myresourcegroup --settings "TriggerConnectionString=<triggerConnectionString>"
    ```
1. Terá de recuperar a etiqueta associada ao recipiente criado utilizando o seguinte comando. Substitua `<username>` pelo nome de utilizador devolvido anteriormente do registo do contentor:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    Poupe o valor devolvido, será usado como `imagetag` no próximo passo.

1. Para fornecer a aplicação de funções com as credenciais necessárias para aceder ao registo do contentor, utilize o seguinte comando. Substitua `<app-name>` pelo nome que pretende utilizar. Substitua `<acrinstance>` e `<imagetag>` os valores da chamada AZ CLI no passo anterior. Substitua `<username>` e `<password>` com as informações de login da ACR obtidas anteriormente:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Este comando devolve informações semelhantes ao seguinte documento JSON:

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

Neste ponto, a aplicação de funções começa a carregar a imagem.

> [!IMPORTANT]
> Pode levar alguns minutos até a imagem ter carregado. Pode monitorizar o progresso através do Portal Azure.

## <a name="next-steps"></a>Passos seguintes

* Aprenda a configurar a sua App de Funções na documentação ['Funções'.](/azure/azure-functions/functions-create-function-linux-custom-image)
* Saiba mais sobre o armazenamento Blob aciona [as encadernações de armazenamento Azure Blob.](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob)
* [Implemente o seu modelo para o Serviço de Aplicações Azure.](how-to-deploy-app-service.md)
* [Consumir um Modelo ML implantado como um serviço web](how-to-consume-web-service.md)
* [Referência da API](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)
