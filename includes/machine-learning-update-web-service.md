---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: 04e8f1f212eb4fa7cf4d8e19132107f2ddb53729
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927025"
---
Para atualizar um serviço Web, use o método `update`. Você pode atualizar o serviço Web para usar um novo modelo, um novo script de entrada ou novas dependências que podem ser especificadas em uma configuração de inferência. Para obter mais informações, consulte a documentação do [WebService. Update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-).

> [!IMPORTANT]
> Ao criar uma nova versão de um modelo, você deve atualizar manualmente cada serviço que deseja usá-lo.

**Usando o SDK**

O código a seguir mostra como usar o SDK para atualizar o modelo, o ambiente e o script de entrada para um serviço Web:

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

**Usando a CLI**

Você também pode atualizar um serviço Web usando a CLI do ML. O exemplo a seguir demonstra como registrar um novo modelo e, em seguida, atualizar um serviço Web para usar o novo modelo:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> Neste exemplo, um documento JSON é usado para passar as informações do modelo do comando de registro para o comando Update.
>
> Para atualizar o serviço para usar um novo script ou ambiente de entrada, crie um [arquivo de configuração de inferência](/azure/machine-learning/service/reference-azure-machine-learning-cli#inference-configuration-schema) e especifique-o com o parâmetro `ic`.

Para obter mais informações, consulte a documentação de [atualização do serviço AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update) .