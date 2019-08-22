---
title: Implantar modelos de ml no serviço de Azure App (versão prévia)
titleSuffix: Azure Machine Learning service
description: Saiba como usar o serviço de Azure Machine Learning para implantar um modelo em um aplicativo Web no serviço Azure App.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/01/2019
ms.openlocfilehash: a5fd376a6da70ed68baedf44fd4c2cc47e68d3cf
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69872380"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Implantar um modelo de aprendizado de máquina no serviço Azure App (versão prévia)

Saiba como implantar um modelo do serviço de Azure Machine Learning como um aplicativo Web no serviço Azure App.

> [!IMPORTANT]
> Embora o serviço Azure Machine Learning e o serviço de Azure App estejam geralmente disponíveis, a capacidade de implantar um modelo do serviço de Machine Learning para o serviço de aplicativo está em versão prévia.

Com o serviço Azure Machine Learning, você pode criar imagens do Docker de modelos de aprendizado de máquina treinados. Essa imagem contém um serviço Web que recebe dados, envia-os para o modelo e, em seguida, retorna a resposta. Azure App serviço pode ser usado para implantar a imagem e fornece os seguintes recursos:

* [Autenticação](/azure/app-service/configure-authentication-provider-aad) avançada para segurança aprimorada. Os métodos de autenticação incluem Azure Active Directory e autenticação multifator.
* [Dimensionamento automático](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) sem precisar reimplantar.
* [Suporte a SSL](/azure/app-service/app-service-web-ssl-cert-load) para comunicações seguras entre clientes e o serviço.

Para obter mais informações sobre os recursos fornecidos pelo serviço Azure App, consulte [visão geral do serviço de aplicativo](/azure/app-service/overview).

> [!IMPORTANT]
> Se você precisar da capacidade de registrar em log os dados de Pontuação usados com seu modelo implantado ou os resultados da pontuação, você deverá implantar no serviço kubernetes do Azure. Para obter mais informações, consulte [coletar dados em seus modelos de produção](how-to-enable-data-collection.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho de serviço do Azure Machine Learning. Para obter mais informações, consulte o artigo [criar um espaço de trabalho](how-to-manage-workspace.md) .
* Um modelo de aprendizado de máquina treinado registrado em seu espaço de trabalho. Se você não tiver um modelo, use o [tutorial de classificação de imagem: treinar modelo](tutorial-train-models-with-aml.md) para treinar e registrar um.

    > [!IMPORTANT]
    > Os trechos de código neste artigo pressupõem que você definiu as seguintes variáveis:
    >
    > * `ws`-Seu espaço de trabalho do Azure Machine Learning.
    > * `model`-O modelo registrado que será implantado.
    > * `inference_config`-A configuração de inferência para o modelo.
    >
    > Para obter mais informações sobre como definir essas variáveis, consulte [implantar modelos com o serviço de Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Preparar para a implementação

Antes de implantar o, você deve definir o que é necessário para executar o modelo como um serviço Web. A lista a seguir descreve os itens básicos necessários para uma implantação:

* Um __script de entrada__. Esse script aceita solicitações, pontua a solicitação usando o modelo e retorna os resultados.

    > [!IMPORTANT]
    > O script de entrada é específico para seu modelo; Ele deve entender o formato dos dados de solicitação de entrada, o formato dos dados esperados pelo seu modelo e o formato dos dados retornados aos clientes.
    >
    > Se os dados da solicitação estiverem em um formato que não pode ser usado pelo seu modelo, o script poderá transformá-lo em um formato aceitável. Ele também pode transformar a resposta antes de retornar a ela para o cliente.

    > [!IMPORTANT]
    > O SDK do Azure Machine Learning não fornece uma maneira para o serviço Web acessar seu datastore ou conjuntos de dados. Se você precisar que o modelo implantado acesse dados armazenados fora da implantação, como em uma conta de armazenamento do Azure, você deve desenvolver uma solução de código personalizado usando o SDK relevante. Por exemplo, o [SDK do armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python).
    >
    > Outra alternativa que pode funcionar para seu cenário é [previsões de lote](how-to-run-batch-predictions.md), que fornece acesso a repositórios de armazenamento durante a pontuação.

    Para obter mais informações sobre scripts de entrada, consulte [implantar modelos com o serviço de Azure Machine Learning](how-to-deploy-and-where.md).

* **Dependências**, como scripts auxiliares ou pacotes python/Conda necessários para executar o script ou modelo de entrada

Essas entidades são encapsuladas em uma __configuração__de inferência. A configuração de inferência referencia o script de entrada e outras dependências.

> [!IMPORTANT]
> Ao criar uma configuração de inferência para uso com Azure App Service, você deve usar um objeto de [ambiente](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) . O exemplo a seguir demonstra como criar um objeto de ambiente e usá-lo com uma configuração de inferência:
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

Para obter mais informações sobre a configuração de inferência, consulte [implantar modelos com o serviço de Azure Machine Learning](how-to-deploy-and-where.md).

> [!IMPORTANT]
> Ao implantar no Azure App Service, você não precisa criar uma __configuração de implantação__.

## <a name="create-the-image"></a>Criar a imagem

Para criar a imagem do Docker que é implantada no serviço Azure App, use [Model. Package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config--generate-dockerfile-false-). O trecho de código a seguir demonstra como criar uma nova imagem a partir do modelo e da configuração de inferência:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Quando `show_output=True`, a saída do processo de Build do Docker é mostrada. Quando o processo for concluído, a imagem terá sido criada no registro de contêiner do Azure para seu espaço de trabalho.

## <a name="deploy-image-as-a-web-app"></a>Implantar imagem como um aplicativo Web

1. No [portal do Azure](https://portal.azure.com), selecione seu espaço de trabalho do Azure Machine Learning. Na seção __visão geral__ , use o link __do registro__ para acessar o registro de contêiner do Azure para o espaço de trabalho.

    ![Captura de tela da visão geral do espaço de trabalho](media/how-to-deploy-app-service/workspace-overview.png)

2. No registro de contêiner do Azure,selecione repositórios e, em seguida, selecione o __nome da imagem__ que você deseja implantar. Para a versão que você deseja implantar, selecione a entrada __...__ e, em seguida, __implante no aplicativo Web__.

    ![Captura de tela da implantação do ACR em um aplicativo Web](media/how-to-deploy-app-service/deploy-to-web-app.png)

3. Para criar o aplicativo Web, forneça um nome de site, uma assinatura, um grupo de recursos e selecione o local/plano do serviço de aplicativo. Por fim, selecione __criar__.

    ![Captura de tela da caixa de diálogo novo aplicativo Web](media/how-to-deploy-app-service/web-app-for-containers.png)

## <a name="use-the-web-app"></a>Usar o aplicativo Web

No [portal do Azure](https://portal.azure.com), selecione o aplicativo Web criado na etapa anterior. Na seção __visão geral__ , copie a __URL__. Esse valor é a __URL base__ do serviço.

![Captura de tela da visão geral do aplicativo Web](media/how-to-deploy-app-service/web-app-overview.png)

O serviço Web que passa solicitações para o modelo está localizado em `{baseurl}/score`. Por exemplo, `https://mywebapp.azurewebsites.net/score`. O código Python a seguir demonstra como enviar dados para a URL e exibir a resposta:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

print(headers)
    
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

* Para obter mais informações sobre como configurar seu aplicativo Web, consulte a documentação do [serviço de aplicativo no Linux](/azure/app-service/containers/) .
* Para obter mais informações sobre o dimensionamento, consulte Introdução [ao dimensionamento automático no Azure](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json).
* Para obter mais informações sobre o suporte a SSL, consulte [usar um certificado SSL em seu serviço de Azure app](/azure/app-service/app-service-web-ssl-cert-load).
* Para obter mais informações sobre autenticação, consulte [configurar seu aplicativo do serviço de aplicativo para usar Azure Active Directory entrar](/azure/app-service/configure-authentication-provider-aad).
* [Consumir um modelo de ML implementado como um serviço web](how-to-consume-web-service.md)