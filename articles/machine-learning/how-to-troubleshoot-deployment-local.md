---
title: Implementação de serviço web de resolução de problemas localmente
titleSuffix: Azure Machine Learning
description: Aprenda a trabalhar, resolver e resolver problemas com os erros comuns de funcionamento do Azure Machine Learning Docker localmente.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.reviewer: luquinta
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: 01a5f600acf9542c5adabcdf2523a42ca44233af
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680496"
---
# <a name="troubleshoot-model-deployment-locally"></a>Implementação de modelo de resolução de problemas localmente

Aprenda a resolver problemas e a resolver, ou a trabalhar em torno de erros comuns de implementação do serviço web Azure Machine Learning Docker localmente.

## <a name="prerequisites"></a>Pré-requisitos

* Uma **subscrição do Azure**. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
* [O Azure Machine Learning SDK.](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)
* O [Azure CLI.](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)
* A [extensão CLI para Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Para depurar localmente, pode implementar o modelo para [Azure Machine Learning Compute Instance](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-local-container-notebook-vm) ou ter uma instalação de Docker em funcionamento no seu sistema local.

    Para verificar a instalação do Docker, utilize o comando `docker run hello-world` a partir de um terminal ou de um pedido de comando. Para obter informações sobre a instalação do Docker, ou sobre os erros do Docker, consulte a [Documentação](https://docs.docker.com/)do Docker .

## <a name="debug-locally"></a>Debug localmente

Se tiver problemas ao implementar um modelo para ACI ou AKS, implemente-o como um serviço web local. A utilização de um serviço Web local facilita a resolução de problemas.

Pode encontrar uma amostra [de um caderno de implementação local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb) no repo  [MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) para explorar um exemplo runnable.

> [!WARNING]
> As implementações de serviços web locais não são suportadas para cenários de produção.

Para implementar localmente, modifique o seu código para utilizar `LocalWebservice.deploy_configuration()` para criar uma configuração de implementação. Em seguida, utilize `Model.deploy()` para implantar o serviço. O exemplo a seguir implementa um modelo (contido na variável modelo) como um serviço web local:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Se estiver a definir a sua própria especificação conda YAML, liste a versão azureml-defaults >= 1.0.45 como uma dependência de pip. Este pacote é necessário para hospedar o modelo como um serviço web.

Neste momento, pode trabalhar com o serviço normalmente. O seguinte código demonstra o envio de dados para o serviço:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

Para obter mais informações sobre a personalização do seu ambiente Python, consulte [Criar e gerir ambientes para treino e implantação.](how-to-use-environments.md) 

### <a name="update-the-service"></a>Atualizar o serviço

Durante os testes locais, poderá ter de atualizar o `score.py` ficheiro para adicionar registo ou tentar resolver quaisquer problemas que tenha descoberto. Para recarregar as alterações no `score.py` ficheiro, utilize `reload()` . Por exemplo, o seguinte código recarrega o script para o serviço e, em seguida, envia dados para o mesmo. Os dados são pontuados utilizando o `score.py` ficheiro atualizado:

> [!IMPORTANT]
> O `reload` método só está disponível para implantações locais. Para obter informações sobre a atualização de uma implementação para outro alvo de computação, consulte [como atualizar o seu serviço web](how-to-deploy-update-web-service.md).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> O script é recarregado a partir do local especificado pelo `InferenceConfig` objeto utilizado pelo serviço.

Para alterar o modelo, as dependências da Conda ou a configuração de implantação, utilize [a atualização()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueupdate--args-). O exemplo a seguir atualiza o modelo utilizado pelo serviço:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Apagar o serviço

Para eliminar o serviço, utilize [eliminar()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Inspecione o registo do Docker

Pode imprimir mensagens detalhadas de registo do motor Do Docker a partir do objeto de serviço. Pode visualizar o registo de implementações ACI, AKS e Local. O exemplo a seguir demonstra como imprimir os registos.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

Se vires a linha `Booting worker with pid: <pid>` a ocorrer várias vezes nos registos, significa que não há memória suficiente para ligar o trabalhador.
Pode resolver o erro aumentando o valor de `memory_gb` in `deployment_config`

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a implementação:

* [Como resolver implementações remotas](how-to-troubleshoot-deployment.md)
* [Como implantar e onde](how-to-deploy-and-where.md)
* [Tutorial: Modelos de implantação de & de comboios](tutorial-train-models-with-aml.md)
* [Como executar e depurar experiências localmente](./how-to-debug-visual-studio-code.md)
