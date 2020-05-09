---
title: Implementar modelos ml para o Serviço de Aplicações Azure (pré-visualização)
titleSuffix: Azure Machine Learning
description: Aprenda a usar o Azure Machine Learning para implementar um modelo para uma Aplicação Web no Serviço de Aplicações Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/27/2019
ms.openlocfilehash: 646254238f83166c53fe94a1821c68ff4dac8f04
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82651931"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Implementar um modelo de aprendizagem automática para o Azure App Service (pré-visualização)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a implementar um modelo do Azure Machine Learning como uma aplicação web no Azure App Service.

> [!IMPORTANT]
> Embora tanto o Azure Machine Learning como o Azure App Service estejam geralmente disponíveis, a capacidade de implementar um modelo do serviço de Machine Learning para o Serviço de Aplicações está em pré-visualização.

Com o Azure Machine Learning, pode criar imagens do Docker a partir de modelos de machine learning treinados. Esta imagem contém um serviço web que recebe dados, os submete ao modelo e devolve a resposta. O Serviço de Aplicações Azure pode ser utilizado para implementar a imagem e fornece as seguintes funcionalidades:

* [Autenticação](/azure/app-service/configure-authentication-provider-aad) avançada para maior segurança. Os métodos de autenticação incluem o Diretório Ativo Azure e o auth multifactor.
* [Escala automática](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) sem ter que recolocar.
* [Suporte tLS](/azure/app-service/configure-ssl-certificate-in-code) para comunicações seguras entre clientes e o serviço.

Para obter mais informações sobre as funcionalidades fornecidas pelo Azure App Service, consulte a visão geral do Serviço de [Aplicações](/azure/app-service/overview).

> [!IMPORTANT]
> Se necessitar da capacidade de registar os dados de pontuação utilizados com o seu modelo implantado, ou os resultados da pontuação, deve, em vez disso, ser utilizado para o Serviço Azure Kubernetes. Para mais informações, consulte [Recolher dados sobre os seus modelos](how-to-enable-data-collection.md)de produção .

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Azure Machine Learning. Para mais informações, consulte o artigo [Criar um espaço de trabalho.](how-to-manage-workspace.md)
* O [Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Um modelo de aprendizagem automática treinado registado no seu espaço de trabalho. Se não tiver um modelo, utilize o tutorial de classificação de [imagem: modelo de comboio](tutorial-train-models-with-aml.md) para treinar e registar um.

    > [!IMPORTANT]
    > Os fragmentos de código neste artigo assumem que definiu as seguintes variáveis:
    >
    > * `ws`- O seu espaço de trabalho azure machine learning.
    > * `model`- O modelo registado que será implantado.
    > * `inference_config`- A configuração de inferência para o modelo.
    >
    > Para obter mais informações sobre a definição destas variáveis, consulte [modelos de implantação com aprendizagem automática Azure](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Preparar para a implementação

Antes de ser implementado, deve definir o que é necessário para executar o modelo como um serviço web. A lista seguinte descreve os itens básicos necessários para uma implantação:

* Um __guião de entrada.__ Este script aceita pedidos, marca o pedido usando o modelo e devolve os resultados.

    > [!IMPORTANT]
    > O script de entrada é específico do seu modelo; deve compreender o formato dos dados de pedido de entrada, o formato dos dados esperados pelo seu modelo e o formato dos dados devolvidos aos clientes.
    >
    > Se os dados do pedido estiverem num formato que não seja utilizável pelo seu modelo, o script pode transformá-lo num formato aceitável. Também pode transformar a resposta antes de voltar ao cliente.

    > [!IMPORTANT]
    > O Azure Machine Learning SDK não fornece uma forma de o serviço web aceder à sua loja de dados ou conjuntos de dados. Se necessitar do modelo implementado para aceder aos dados armazenados fora da implementação, como numa conta de Armazenamento Azure, deve desenvolver uma solução de código personalizada utilizando o SDK relevante. Por exemplo, o [SDK](https://github.com/Azure/azure-storage-python)de Armazenamento Azure para Python .
    >
    > Outra alternativa que pode funcionar para o seu cenário são [as previsões](how-to-use-parallel-run-step.md)do lote , que fornece acesso a lojas de dados ao marcar.

    Para obter mais informações sobre scripts de entrada, consulte [Modelos de implantação com Aprendizagem automática Azure](how-to-deploy-and-where.md).

* **Dependências**, tais como scripts de ajudante ou pacotes Python/Conda necessários para executar o script ou modelo de entrada

Estas entidades são encapsuladas numa configuração de __inferência.__ A configuração de inferência referencia o script de entrada e outras dependências.

> [!IMPORTANT]
> Ao criar uma configuração de inferência para utilização com o Serviço de Aplicações Azure, deve utilizar um objeto [Ambiente.](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) Por favor, note que se estiver a definir um ambiente personalizado, deve adicionar incumprimentos em azul com versão >= 1.0,45 como dependência do pip. Este pacote contém a funcionalidade necessária para hospedar o modelo como um serviço web. O exemplo que se segue demonstra a criação de um objeto ambiental e a sua utilização com uma configuração de inferência:
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

Para obter mais informações sobre ambientes, consulte [Criar e gerir ambientes para formação e implantação.](how-to-use-environments.md)

Para obter mais informações sobre a configuração de inferência, consulte [Modelos de implantação com aprendizagem automática Azure](how-to-deploy-and-where.md).

> [!IMPORTANT]
> Ao ser implantado no Serviço de Aplicações Azure, não precisa de criar uma configuração de __implementação__.

## <a name="create-the-image"></a>Criar a imagem

Para criar a imagem Do Docker que é implantada para o Serviço de Aplicações Azure, utilize [o Model.package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-). O seguinte código de corte demonstra como construir uma nova imagem a partir do modelo e configuração de inferência:

> [!NOTE]
> O código snippet `model` assume que contém um `inference_config` modelo registado, e que contém a configuração para o ambiente de inferência. Para mais informações, consulte [Implementar modelos com Aprendizagem automática Azure](how-to-deploy-and-where.md).

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

Quando, `show_output=True`a saída do processo de construção do Docker é mostrada. Uma vez terminado o processo, a imagem foi criada no Registo de Contentores Azure para o seu espaço de trabalho. Uma vez construída a imagem, a localização no registo do contentor Azure é exibida. A localização devolvida encontra-se no formato `<acrinstance>.azurecr.io/package@sha256:<imagename>`. Por exemplo, `myml08024f78fd10.azurecr.io/package@sha256:20190827151241`.

> [!IMPORTANT]
> Guarde as informações de localização, pois é utilizada ao implementar a imagem.

## <a name="deploy-image-as-a-web-app"></a>Implementar imagem como uma aplicação web

1. Utilize o seguinte comando para obter as credenciais de login para o Registo de Contentores Azure que contenha a imagem. Substitua `<acrinstance>` pelo valor devolvido `package.location`anteriormente a partir de:

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
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    Neste exemplo, é utilizado um`--sku B1`nível de preços __básicos.__

    > [!IMPORTANT]
    > As imagens criadas pelo Azure Machine Learning `--is-linux` usam o Linux, pelo que deve utilizar o parâmetro.

1. Para criar a aplicação web, utilize o seguinte comando. Substitua `<app-name>` pelo nome que pretende utilizar. `<acrinstance>` Substitua `<imagename>` e com `package.location` os valores de volta anterior:

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
    > Neste momento, a aplicação web foi criada. No entanto, uma vez que não forneceu as credenciais ao Registo de Contentores Azure que contém a imagem, a aplicação web não está ativa. No passo seguinte, fornece as informações de autenticação para o registo do contentor.

1. Para fornecer à aplicação web as credenciais necessárias para aceder ao registo do contentor, utilize o seguinte comando. Substitua `<app-name>` pelo nome que pretende utilizar. `<acrinstance>` Substitua `<imagename>` e com `package.location` os valores de volta anterior. `<username>` Substitua `<password>` e com as informações de login da ACR recuperadas anteriormente:

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

Neste ponto, a aplicação da web começa a carregar a imagem.

> [!IMPORTANT]
> Pode levar alguns minutos até a imagem ter carregado. Para monitorizar o progresso, utilize o seguinte comando:
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> Uma vez que a imagem é carregada e o site `Container <container name> for site <app-name> initialized successfully and is ready to serve requests`está ativo, o registo exibe uma mensagem que indica .

Uma vez que a imagem é implantada, pode encontrar o nome de anfitrião utilizando o seguinte comando:

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

Este comando devolve informações semelhantes `<app-name>.azurewebsites.net`ao seguinte nome de anfitrião - . Utilize este valor como parte da __url base__ para o serviço.

## <a name="use-the-web-app"></a>Use a Aplicação Web

O serviço web que passa pedidos para `{baseurl}/score`o modelo está localizado em . Por exemplo, `https://<app-name>.azurewebsites.net/score`. O seguinte código Python demonstra como submeter dados ao URL e mostrar a resposta:

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

* Aprenda a configurar a sua Aplicação Web no Serviço de Aplicações na documentação [do Linux.](/azure/app-service/containers/)
* Saiba mais sobre a escala em [Get started com Autoscale in Azure](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json).
* [Utilize um certificado TLS/SSL no seu Serviço de Aplicações Azure](/azure/app-service/configure-ssl-certificate-in-code).
* [Configure a sua aplicação App Service para utilizar o sign-in do Diretório Ativo Do Azure](/azure/app-service/configure-authentication-provider-aad).
* [Consumir um Modelo ML implantado como um serviço web](how-to-consume-web-service.md)
