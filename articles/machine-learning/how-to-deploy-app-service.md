---
title: Implementar modelos ml para O Serviço de Aplicações Azure (pré-visualização)
titleSuffix: Azure Machine Learning
description: Aprenda a usar o Azure Machine Learning para implementar um modelo para uma Aplicação Web no Azure App Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: fd7ba2eeddb3eddd98f97bda1a26c69c2cfa77c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91315308"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Implementar um modelo de machine learning para o Azure App Service (pré-visualização)


Saiba como implementar um modelo da Azure Machine Learning como uma aplicação web no Azure App Service.

> [!IMPORTANT]
> Embora tanto o Azure Machine Learning como o Azure App Service estejam geralmente disponíveis, a capacidade de implementar um modelo do serviço machine learning para o Serviço de Aplicações está em pré-visualização.

Com a Azure Machine Learning, pode criar imagens Docker a partir de modelos de aprendizagem automática treinados. Esta imagem contém um serviço web que recebe dados, submete-os ao modelo e, em seguida, devolve a resposta. O Azure App Service pode ser usado para implementar a imagem e fornece as seguintes funcionalidades:

* [Autenticação](/azure/app-service/configure-authentication-provider-aad) avançada para segurança reforçada. Os métodos de autenticação incluem o Azure Ative Directory e o multi-factor auth.
* [Autoescala](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) sem ter que recolocar.
* [Suporte TLS](/azure/app-service/configure-ssl-certificate-in-code) para comunicações seguras entre clientes e o serviço.

Para obter mais informações sobre as funcionalidades fornecidas pelo Azure App Service, consulte a visão geral do [Serviço de Aplicações.](/azure/app-service/overview)

> [!IMPORTANT]
> Se precisar da capacidade de registar os dados de pontuação utilizados com o seu modelo implantado ou os resultados da pontuação, deverá, em vez disso, ser implantado no Serviço Azure Kubernetes. Para obter mais informações, consulte [recolher dados sobre os seus modelos de produção.](how-to-enable-data-collection.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Azure Machine Learning. Para mais informações, consulte o [Artigo Criar um espaço de trabalho.](how-to-manage-workspace.md)
* O [Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)
* Um modelo de aprendizagem automática treinado registado no seu espaço de trabalho. Se não tiver um modelo, use o tutorial de [classificação de imagem: modelo de comboio](tutorial-train-models-with-aml.md) para treinar e registar um.

    > [!IMPORTANT]
    > Os códigos de corte neste artigo assumem que definiu as seguintes variáveis:
    >
    > * `ws` - O seu espaço de trabalho para aprendizagem de máquinas Azure.
    > * `model` - O modelo registado que será implantado.
    > * `inference_config` - A configuração da inferência para o modelo.
    >
    > Para obter mais informações sobre a definição destas variáveis, consulte [implementar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Preparar para a implementação

Antes de implementar, deve definir o que é necessário para executar o modelo como um serviço web. A lista a seguir descreve os principais itens necessários para uma implantação:

* Um __roteiro de entrada.__ Este script aceita pedidos, marca o pedido usando o modelo e devolve os resultados.

    > [!IMPORTANT]
    > O script de entrada é específico do seu modelo; deve compreender o formato dos dados de pedido de entrada, o formato dos dados esperados pelo seu modelo e o formato dos dados devolvidos aos clientes.
    >
    > Se os dados do pedido estiverem num formato que não seja utilizável pelo seu modelo, o script pode transformá-lo num formato aceitável. Também pode transformar a resposta antes de voltar ao cliente.

    > [!IMPORTANT]
    > O Azure Machine Learning SDK não fornece uma forma de o serviço web aceder à sua datastore ou conjuntos de dados. Se necessitar do modelo implantado para aceder aos dados armazenados fora da implementação, como numa conta de Armazenamento Azure, deve desenvolver uma solução de código personalizada utilizando o SDK relevante. Por exemplo, o [Azure Storage SDK para Python](https://github.com/Azure/azure-storage-python).
    >
    > Outra alternativa que pode funcionar para o seu cenário são [as previsões do lote](how-to-use-parallel-run-step.md), que fornece acesso às datas-tores na hora de pontuar.

    Para obter mais informações sobre scripts de entrada, consulte [implementar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

* **Dependências**, tais como scripts de ajuda ou pacotes Python/Conda necessários para executar o script de entrada ou modelo

Estas entidades são encapsuladas numa __configuração de inferência.__ A configuração de inferência referencia o script de entrada e outras dependências.

> [!IMPORTANT]
> Ao criar uma configuração de inferência para utilização com o Serviço de Aplicações Azure, deve utilizar um objeto [Ambiente.](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py&preserve-view=true) Por favor, note que se estiver a definir um ambiente personalizado, deve adicionar azureml-padrão com >versão = 1.0.45 como dependência de pip. Este pacote contém a funcionalidade necessária para hospedar o modelo como um serviço web. O exemplo a seguir demonstra a criação de um objeto ambiental e a sua utilização com uma configuração de inferência:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
> from azureml.core.model import InferenceConfig
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

Para obter mais informações sobre ambientes, consulte [Criar e gerir ambientes de formação e implantação.](how-to-use-environments.md)

Para obter mais informações sobre a configuração de inferência, consulte [implementar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

> [!IMPORTANT]
> Ao ser implantado no Azure App Service, não precisa de criar uma __configuração de implementação__.

## <a name="create-the-image"></a>Criar a imagem

Para criar a imagem Docker que é implantada no Azure App Service, utilize [o Model.package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truepackage-workspace--models--inference-config-none--generate-dockerfile-false-). O seguinte corte de código demonstra como construir uma nova imagem a partir do modelo e configuração de inferência:

> [!NOTE]
> O código de corte assume que `model` contém um modelo registado, e que contém `inference_config` a configuração para o ambiente de inferência. Para obter mais informações, consulte [implementar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

Quando `show_output=True` , a saída do processo de construção do Docker é mostrada. Uma vez terminado o processo, a imagem foi criada no Registo do Contentor Azure para o seu espaço de trabalho. Uma vez construída a imagem, é visualizada a localização do registo do seu contentor Azure. A localização devolvida está no `<acrinstance>.azurecr.io/package@sha256:<imagename>` formato. Por exemplo, `myml08024f78fd10.azurecr.io/package@sha256:20190827151241`.

> [!IMPORTANT]
> Guarde as informações de localização, tal como é utilizada ao implementar a imagem.

## <a name="deploy-image-as-a-web-app"></a>Implementar a imagem como uma aplicação web

1. Utilize o seguinte comando para obter as credenciais de login do Registo do Contentor Azure que contém a imagem. `<acrinstance>`Substitua-o pelo valor devolvido anteriormente a partir `package.location` de:

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

    Guarde o valor para __o nome de utilizador__ e uma das __palavras-passe.__

1. Se ainda não tiver um grupo de recursos ou um plano de serviço de aplicações para implementar o serviço, os seguintes comandos demonstram como criar ambos:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    Neste exemplo, é utilizado um nível __básico__ de preços `--sku B1` ()

    > [!IMPORTANT]
    > As imagens criadas por Azure Machine Learning usam o Linux, pelo que deve utilizar o `--is-linux` parâmetro.

1. Para criar a aplicação web, utilize o seguinte comando. `<app-name>`Substitua-o pelo nome que pretende utilizar. Substitua `<acrinstance>` e `<imagename>` pelos valores devolvidos `package.location` anteriormente:

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package@sha256:<imagename>
    ```

    Este comando devolve informações semelhantes ao seguinte documento JSON:

    ```json
    {
    "adminSiteName": null,
    "appServicePlanName": "myplanname",
    "geoRegion": "West Europe",
    "hostingEnvironmentProfile": null,
    "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myplanname",
    "kind": "linux",
    "location": "West Europe",
    "maximumNumberOfWorkers": 1,
    "name": "myplanname",
    < JSON data removed for brevity. >
    "targetWorkerSizeId": 0,
    "type": "Microsoft.Web/serverfarms",
    "workerTierName": null
    }
    ```

    > [!IMPORTANT]
    > Neste momento, a aplicação web foi criada. No entanto, uma vez que não forneceu as credenciais ao Registo de Contentores Azure que contém a imagem, a aplicação web não está ativa. No passo seguinte, forneça as informações de autenticação para o registo do contentor.

1. Para fornecer à aplicação web as credenciais necessárias para aceder ao registo do contentor, utilize o seguinte comando. `<app-name>`Substitua-o pelo nome que pretende utilizar. Substitua `<acrinstance>` e `<imagename>` pelos valores devolvidos `package.location` anteriormente. Substitua `<username>` e `<password>` pela informação de login da ACR recuperada anteriormente:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package@sha256:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
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
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package@sha256:20190827195524"
    }
    ]
    ```

Neste momento, a aplicação web começa a carregar a imagem.

> [!IMPORTANT]
> Pode levar alguns minutos até que a imagem esteja carregada. Para monitorizar o progresso, utilize o seguinte comando:
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> Uma vez que a imagem tenha sido carregada e o site esteja ativo, o registo apresenta uma mensagem que diz `Container <container name> for site <app-name> initialized successfully and is ready to serve requests` .

Uma vez implantada a imagem, pode encontrar o nome de anfitrião utilizando o seguinte comando:

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

Este comando devolve informações semelhantes ao nome de anfitrião seguinte - `<app-name>.azurewebsites.net` . Utilize este valor como parte do __url base__ para o serviço.

## <a name="use-the-web-app"></a>Utilize a Aplicação Web

O serviço web que transmite pedidos ao modelo está localizado em `{baseurl}/score` . Por exemplo, `https://<app-name>.azurewebsites.net/score`. O seguinte código Python demonstra como enviar dados para o URL e mostrar a resposta:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>Passos seguintes

* Aprenda a configurar a sua Web App no Serviço de Aplicações na documentação [linux.](/azure/app-service/containers/)
* Saiba mais sobre escalar em [Começar com a Autoscale em Azure.](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json)
* [Utilize um certificado TLS/SSL no seu Serviço de Aplicações Azure](/azure/app-service/configure-ssl-certificate-in-code).
* [Configure a sua aplicação de Serviço de Aplicações para utilizar o Azure Ative Directory.](/azure/app-service/configure-authentication-provider-aad)
* [Consumir um Modelo ML implantado como um serviço web](how-to-consume-web-service.md)
