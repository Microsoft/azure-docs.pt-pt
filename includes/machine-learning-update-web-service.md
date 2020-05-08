---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: e6a7ee8ed497c87c08f13e09a0022b0835c952cc
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82975075"
---
Para atualizar um serviço `update` web, utilize o método. Pode atualizar o serviço web para utilizar um novo modelo, um novo script de entrada ou novas dependências que podem ser especificadas numa configuração de inferência. Para mais informações, consulte a documentação para [Webservice.update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-).

> [!IMPORTANT]
> Quando cria uma nova versão de um modelo, tem de atualizar manualmente cada serviço que pretende utilizar.
>
> Não é possível utilizar o SDK para atualizar um serviço web publicado pelo designer de Machine Learning Azure.

**Utilizar o SDK**

O seguinte código mostra como usar o SDK para atualizar o modelo, ambiente e script de entrada para um serviço web:

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

**Usando o CLI**

Também pode atualizar um serviço web utilizando o ML CLI. O exemplo que se segue demonstra registar um novo modelo e, em seguida, atualizar um serviço web para utilizar o novo modelo:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> Neste exemplo, um documento JSON é usado para passar a informação do modelo do comando de registo para o comando de atualização.
>
> Para atualizar o serviço para utilizar um novo script ou ambiente de entrada, crie um ficheiro de configuração de [inferência](/azure/machine-learning/reference-azure-machine-learning-cli#inference-configuration-schema) e especifique-o com o `ic` parâmetro.

Para mais informações, consulte a documentação da atualização do [serviço az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update)