---
title: Como implantar modelos em VMs de notebook
titleSuffix: Azure Machine Learning
description: Saiba como implantar seus modelos de Azure Machine Learning como um serviço Web usando VMs de notebook.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 10/25/2019
ms.openlocfilehash: d4e37b02b3d7a21546a04c8948fbbfb7262bfa6a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584775"
---
# <a name="deploy-a-model-to-azure-machine-learning-notebook-vms"></a>Implantar um modelo para Azure Machine Learning VMs de notebook

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Saiba como usar Azure Machine Learning para implantar um modelo como um serviço Web em sua VM do Azure Machine Learning notebook. Use as VMs do notebook se uma das seguintes condições for verdadeira:

- Você precisa implantar e validar rapidamente seu modelo.
- Você está testando um modelo que está em desenvolvimento.

> [!TIP]
> Implantar um modelo de um Jupyter Notebook em uma VM do notebook, para um serviço Web na mesma VM é uma _implantação local_. Nesse caso, o computador "local" é a VM do notebook. Para obter mais informações sobre implantações, consulte [implantar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Pré-requisitos

- Um espaço de trabalho Azure Machine Learning com uma VM de notebook em execução. Para obter mais informações, consulte [ambiente de instalação e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-notebook-vms"></a>Implantar nas VMs do notebook

Um bloco de anotações de exemplo que demonstra implantações locais está incluído em sua VM do bloco de anotações. Use as etapas a seguir para carregar o bloco de anotações e implantar o modelo como um serviço Web na VM:

1. No [Azure Machine Learning Studio](https://ml.azure.com), selecione suas VMs de Azure Machine Learning notebook.

1. Abra o subdiretório `samples-*` e, em seguida, abra `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`. Uma vez aberto, execute o bloco de anotações.

    ![Captura de tela do serviço local em execução no bloco de anotações](media/how-to-deploy-local-container-notebookvm/deploy-local-service.png)

1. O notebook exibe a URL e a porta em que o serviço está sendo executado. Por exemplo, `https://localhost:6789`. Você também pode executar a célula que contém `print('Local service port: {}'.format(local_service.port))` para exibir a porta.

    ![Captura de tela da porta de serviço local em execução](media/how-to-deploy-local-container-notebookvm/deploy-local-service-port.png)

1. Para testar o serviço da VM do notebook, use a URL do `https://localhost:<local_service.port>`. Para testar a partir de um cliente remoto, obtenha a URL pública do serviço em execução na VM do notebook. A URL pública pode ser determinada usando a fórmula a seguir; `https://<notebookvm_name>-<local_service_port>.<azure_region_of_notebook>.notebooks.azureml.net/score`. Por exemplo, `https://mynotebookvm-6789.eastus2.notebooks.azureml.net/score`.

## <a name="test-the-service"></a>Testar o serviço

Para enviar dados de exemplo para o serviço em execução, use o código a seguir. Substitua o valor de `service_url` pela URL de da etapa anterior:

```python
import requests
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')
access_token = "your bearer token"
headers = {'Content-Type':'application/json', 'Authorization': 'Bearer ' + access_token}
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Passos seguintes

* [Como implantar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Solução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Proteger Azure Machine Learning serviços Web com SSL](how-to-secure-web-service.md)
* [Consumir um modelo de ML implantado como um serviço Web](how-to-consume-web-service.md)
* [Monitore seus modelos de Azure Machine Learning com Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)