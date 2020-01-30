---
title: Como implantar modelos para computar instâncias
titleSuffix: Azure Machine Learning
description: Saiba como implantar seus modelos de Azure Machine Learning como um serviço Web usando instâncias de computação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 10/25/2019
ms.openlocfilehash: 45f9f9d1c9c8414f7d3c97aab05b012a16079f40
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843536"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Implantar um modelo para Azure Machine Learning instâncias de computação

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

> [!NOTE]
> Os casos computacionais (pré-visualização) estão disponíveis apenas para espaços de trabalho com um **Norte Central US**, ** East US 2**, Norte da **Europa** ou Reino **Unido Sul**.
>Se o seu espaço de trabalho estiver em qualquer outra região, você poderá continuar a criar e usar uma [VM do bloco de anotações](concept-compute-instance.md#notebookvm) .  Você pode implantar um modelo em uma instância de computação ou em uma VM do notebook usando as etapas neste artigo.

Saiba como usar Azure Machine Learning para implantar um modelo como um serviço Web em sua instância de computação do Azure Machine Learning. Use instâncias de computação se uma das seguintes condições for verdadeira:

- Precisa implementar e validar o seu modelo rapidamente.
- Está a testar um modelo que está em desenvolvimento.

> [!TIP]
> Implantar um modelo de um Jupyter Notebook em uma instância de computação para um serviço Web na mesma VM é uma _implantação local_. Nesse caso, o computador "local" é a instância de computação. Para obter mais informações sobre implantações, consulte [implantar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Pré-requisitos

- Um espaço de trabalho Azure Machine Learning com uma instância de computação em execução. Para obter mais informações, consulte [ambiente de instalação e espaço de trabalho](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-compute-instances"></a>Implantar nas instâncias de computação

Um bloco de anotações de exemplo que demonstra implantações locais está incluído em sua instância de computação. Use as etapas a seguir para carregar o bloco de anotações e implantar o modelo como um serviço Web na VM:

1. No [Azure Machine Learning Studio](https://ml.azure.com), selecione suas instâncias de computação Azure Machine Learning.

1. Abra o subdiretório `samples-*` e, em seguida, abra `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`. Uma vez aberto, execute o bloco de anotações.

    ![Captura de tela do serviço local em execução no bloco de anotações](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)

1. O notebook exibe a URL e a porta em que o serviço está sendo executado. Por exemplo, `https://localhost:6789`. Também pode executar a célula que contém `print('Local service port: {}'.format(local_service.port))` para exibir a porta.

    ![Captura de tela da porta de serviço local em execução](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. Para testar o serviço a partir de uma instância de cálculo, utilize o URL `https://localhost:<local_service.port>`. Para testar a partir de um cliente remoto, obtenha a URL pública do serviço em execução na instância de computação. A URL pública pode ser determinada usando a fórmula a seguir; 
    * Notebook VM: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score`. 
    * Instância computacional: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score`. 

    Por exemplo, 
    * VM do notebook: `https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Instância de computação: `https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Testar o serviço

Para enviar dados de exemplo para o serviço em execução, use o código a seguir. Substitua o valor da `service_url` pelo URL do passo anterior:

> [!NOTE]
> Ao autenticar para uma implantação na instância de computação, a autenticação é feita usando Azure Active Directory. A chamada para `interactive_auth.get_authentication_header()` no código de exemplo autentica-o usando AAD, e devolve um cabeçalho que pode ser usado para autenticar o serviço na instância computacional. Para obter mais informações, consulte [Configurar a autenticação para Azure Machine Learning recursos e fluxos de trabalho](how-to-setup-authentication.md#interactive-authentication).
>
> Ao autenticar para uma implantação no serviço kubernetes do Azure ou em instâncias de contêiner do Azure, um método de autenticação diferente é usado. Para obter mais informações sobre o, consulte [Configurar a autenticação para Azure Machine Learning recursos e fluxos de trabalho](how-to-setup-authentication.md#web-service-authentication).

```python
import requests
import json
from azureml.core.authentication import InteractiveLoginAuthentication

# Get a token to authenticate to the compute instance from remote
interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()

# Create and submit a request using the auth header
headers = auth_header
# Add content type header
headers.update({'Content-Type':'application/json'})

# Sample data to send to the service
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

# Replace with the URL for your compute instance, as determined from the previous section
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Passos seguintes

* [Como implantar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Solução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Proteger serviços da web do Azure Machine Learning com SSL](how-to-secure-web-service.md)
* [Consumir um modelo de ML implementado como um serviço web](how-to-consume-web-service.md)
* [Monitore seus modelos de Azure Machine Learning com Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)