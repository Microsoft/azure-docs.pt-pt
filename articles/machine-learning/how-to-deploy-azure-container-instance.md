---
title: Como implementar modelos para instâncias de contentores azure
titleSuffix: Azure Machine Learning
description: Aprenda a implementar os seus modelos Azure Machine Learning como um serviço web utilizando instâncias de contentores Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 12/27/2019
ms.openlocfilehash: cb5603c12f06e4f5d5675ca27e171321ecc8827a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536450"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Implementar um modelo para instâncias de contentores azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a utilizar o Azure Machine Learning para implementar um modelo como serviço web em Casos de Contentores Azure (ACI). Utilize instâncias de contentores Azure se uma das seguintes condições for verdadeira:

- Precisa de implantar e validar rapidamente o seu modelo. Não precisa de criar recipientes ACI com antecedência. São criados como parte do processo de implantação.
- Estáa testar um modelo que está em desenvolvimento. 

Para obter informações sobre a disponibilidade de quotas e regiões para a ACI, consulte quotas e disponibilidade da região para o artigo Casos de [Contentores Azure.](https://docs.microsoft.com/azure/container-instances/container-instances-quotas)

## <a name="prerequisites"></a>Pré-requisitos

- Uma área de trabalho do Azure Machine Learning. Para mais informações, consulte Criar um espaço de [trabalho azure machine learning](how-to-manage-workspace.md).

- Um modelo de aprendizagem automática registado no seu espaço de trabalho. Se não tem um modelo registado, veja [como e onde implementar modelos.](how-to-deploy-and-where.md)

- A [extensão Azure CLI para o serviço de Machine Learning,](reference-azure-machine-learning-cli.md) [Azure Machine Learning Python SDK,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou a extensão do Código visual de [aprendizagem automática Azure.](tutorial-setup-vscode-extension.md)

- Os fragmentos de código __Python__ neste artigo assumem que as seguintes variáveis são definidas:

    * `ws`- Coloque no seu espaço de trabalho.
    * `model`- Definido para o seu modelo registado.
    * `inference_config`- Definir para a configuração de inferência para o modelo.

    Para obter mais informações sobre a definição destas variáveis, consulte [Como e onde implementar modelos](how-to-deploy-and-where.md).

- Os cortes __cli__ neste artigo assumem que `inferenceconfig.json` criou um documento. Para obter mais informações sobre a criação deste documento, consulte [Como e onde implementar modelos.](how-to-deploy-and-where.md)

## <a name="deploy-to-aci"></a>Implementar para ACI

Para implementar um modelo para o Azure Container Instances, crie uma configuração de __implementação__ que descreva os recursos computacionais necessários. Por exemplo, número de núcleos e memória. Também precisa de uma configuração de __inferência,__ que descreve o ambiente necessário para acolher o modelo e o serviço web. Para obter mais informações sobre a criação da configuração de inferência, consulte [Como e onde implementar modelos](how-to-deploy-and-where.md).

### <a name="using-the-sdk"></a>Utilizar o SDK

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Para obter mais informações sobre as classes, métodos e parâmetros utilizados neste exemplo, consulte os seguintes documentos de referência:

* [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Modelo.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Serviço web.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Usando o CLI

Para ser acionado utilizando o CLI, utilize o seguinte comando. Substitua `mymodel:1` pelo nome e versão do modelo registado. Substitua `myservice` pelo nome para prestar este serviço:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

Para mais informações, consulte a referência de implementação do [modelo Az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) 

## <a name="using-vs-code"></a>Utilizar o VS Code

Consulte [a implementação dos seus modelos com o Código VS](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Não é necessário criar um recipiente ACI para testar com antecedência. Os recipientes ACI são criados conforme necessário.

## <a name="update-the-web-service"></a>Atualizar o serviço web

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Passos seguintes

* [Como implementar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Resolução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Serviços web Secure Azure Machine Learning com SSL](how-to-secure-web-service.md)
* [Consumir um Modelo ML implantado como um serviço web](how-to-consume-web-service.md)
* [Monitorize os seus modelos de Aprendizagem automática Azure com Insights de Aplicação](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)
