---
title: Atualizar um webservice implantado
author: gvashishtha
ms.service: machine-learning
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 66c53c7485041ec9abaf72396efcfa3325a13732
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87799911"
---
# <a name="update-a-deployed-web-service"></a>Atualizar um serviço web implantado

Este artigo mostra para implementar um serviço web que foi implementado com Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que já implementou um serviço web com a Azure Machine Learning. Se precisar de aprender a implementar um serviço web, [siga estes passos](how-to-deploy-and-where.md).

## <a name="update-web-service"></a>Atualizar serviços Web

Para atualizar um serviço web, utilize o `update` método. Pode atualizar o serviço web para utilizar um novo modelo, um novo script de entrada ou novas dependências que podem ser especificadas numa configuração de inferência. Para mais informações, consulte a documentação para [Webservice.update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-).

Consulte [o método de atualização do serviço AKS.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#update-image-none--autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--tags-none--properties-none--description-none--models-none--inference-config-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-)

Consulte [o método de atualização do serviço ACI.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#update-image-none--tags-none--properties-none--description-none--auth-enabled-none--ssl-enabled-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--enable-app-insights-none--models-none--inference-config-none-)

> [!IMPORTANT]
> Quando criar uma nova versão de um modelo, tem de atualizar manualmente cada serviço que pretende utilizar.
>
> Não é possível utilizar o SDK para atualizar um serviço web publicado pelo designer de Machine Learning Azure.

**Utilizar o SDK**

O seguinte código mostra como usar o SDK para atualizar o modelo, o ambiente e o script de entrada para um serviço web:

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
print(service.state)
print(service.get_logs())
```

**Utilização do CLI**

Também pode atualizar um serviço web utilizando o ML CLI. O exemplo a seguir demonstra o registo de um novo modelo e, em seguida, a atualização de um serviço web para utilizar o novo modelo:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> Neste exemplo, é utilizado um documento JSON para passar a informação do modelo do comando de registo para o comando de atualização.
>
> Para atualizar o serviço para utilizar um novo script ou ambiente de entrada, crie um [ficheiro de configuração de inferência](/azure/machine-learning/reference-azure-machine-learning-cli#inference-configuration-schema) e especifique-o com o `ic` parâmetro.

Para mais informações, consulte a documentação [de atualização do serviço AZ ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update)

## <a name="next-steps"></a>Passos seguintes

* [Resolução de problemas de uma implantação falhada](how-to-troubleshoot-deployment.md)
* [Implementar no Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Criar aplicações de clientes para consumir serviços web](how-to-consume-web-service.md)
* [Como implementar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Utilize o TLS para garantir um serviço web através do Azure Machine Learning](how-to-secure-web-service.md)
* [Monitorize os seus modelos de machine learning Azure com Insights de Aplicações](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)
* [Criar alertas e gatilhos de eventos para implementações de modelos](how-to-use-event-grid.md)
