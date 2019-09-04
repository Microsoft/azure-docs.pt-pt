---
title: Como implantar modelos em instâncias de contêiner do Azure
titleSuffix: Azure Machine Learning service
description: Saiba como implantar seus modelos de serviço de Azure Machine Learning como um serviço Web usando instâncias de contêiner do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2019
ms.openlocfilehash: cb80cb92ef93132aae3c57e74c108293da3a7834
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279144"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Implantar um modelo em instâncias de contêiner do Azure

Saiba como usar o serviço de Azure Machine Learning para implantar um modelo como um serviço Web em ACI (instâncias de contêiner do Azure). Use as instâncias de contêiner do Azure se uma das seguintes condições for verdadeira:

- Precisa implementar e validar o seu modelo rapidamente. Você não precisa criar contêineres ACI antes do tempo. Eles são criados como parte do processo de implantação.
- Está a testar um modelo que está em desenvolvimento. 

Para obter informações sobre a disponibilidade de cota e região para ACI, consulte o artigo [cotas e disponibilidade de região para instâncias de contêiner do Azure](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) .

## <a name="prerequisites"></a>Pré-requisitos

- Uma área de trabalho de serviço do Azure Machine Learning. Para obter mais informações, consulte [criar um Azure Machine Learning espaço de trabalho de serviço](how-to-manage-workspace.md).

- Um modelo de aprendizado de máquina registrado em seu espaço de trabalho. Se você não tiver um modelo registrado, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

- A [extensão CLI do Azure para o serviço Machine Learning](reference-azure-machine-learning-cli.md), o [SDK do Azure Machine Learning Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou a [extensão Azure Machine Learning Visual Studio Code](how-to-vscode-tools.md).

- Os trechos de código __Python__ neste artigo pressupõem que as seguintes variáveis sejam definidas:

    * `ws`-Defina para seu espaço de trabalho.
    * `model`-Defina para o modelo registrado.
    * `inference_config`– Defina para a configuração de inferência para o modelo.

    Para obter mais informações sobre como definir essas variáveis, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

- Os trechos de código da __CLI__ neste artigo pressupõem que você `inferenceconfig.json` criou um documento. Para obter mais informações sobre como criar este documento, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

## <a name="deploy-to-aci"></a>Implementar para ACI

Para implantar um modelo em instâncias de contêiner do Azure, crie uma __configuração de implantação__ que descreva os recursos de computação necessários. Por exemplo, número de núcleos e memória. Você também precisa de uma __configuração__de inferência, que descreve o ambiente necessário para hospedar o modelo e o serviço Web. Para obter mais informações sobre como criar a configuração de inferência, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

### <a name="using-the-sdk"></a>Utilizar o SDK

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Para obter mais informações sobre as classes, os métodos e os parâmetros usados neste exemplo, consulte os seguintes documentos de referência:

* [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-)
* [Modelo. implantar](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)
* [WebService. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Usando a CLI

Para implantar usando a CLI, use o comando a seguir. Substituir `mymodel:1` pelo nome e versão do modelo registrado. Substitua `myservice` pelo nome para fornecer este serviço:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aci-deploy-config.md)]

Para obter mais informações, consulte a referência de [implantação do modelo AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) . 

## <a name="using-vs-code"></a>Usando VS Code

Consulte [implantar seus modelos com vs Code](how-to-vscode-tools.md#deploy-and-manage-models).

> [!IMPORTANT]
> Você não precisa criar um contêiner ACI para testar com antecedência. Os contêineres ACI são criados conforme necessário.

## <a name="update-the-web-service"></a>Atualizar o serviço web

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Passos Seguintes

* [Como implantar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Solução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Proteger serviços da web do Azure Machine Learning com SSL](how-to-secure-web-service.md)
* [Consumir um modelo de ML implementado como um serviço web](how-to-consume-web-service.md)
* [Monitore seus modelos de Azure Machine Learning com Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)
