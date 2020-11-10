---
title: Implementar modelos para calcular instâncias
titleSuffix: Azure Machine Learning
description: Saiba como implementar os seus modelos Azure Machine Learning como um serviço web utilizando instâncias compute.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, deploy
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: 05d810928a71aec0e3314c32051e5f3565413564
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444610"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Implementar um modelo para a Azure Machine Learning computação de instâncias



Aprenda a usar a Azure Machine Learning para implementar um modelo como um serviço web no seu caso de computação Azure Machine Learning. Utilize casos de computação se uma das seguintes condições for verdadeira:

- É necessário implementar e validar rapidamente o seu modelo.
- Estás a testar um modelo que está em desenvolvimento.

> [!TIP]
> Implementar um modelo de um Bloco de Notas Jupyter em uma instância computacional, para um serviço web no mesmo VM é uma _implementação local_. Neste caso, o computador 'local' é a instância computacional. Para obter mais informações sobre as implementações, consulte [implementar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Pré-requisitos

- Um espaço de trabalho de aprendizagem de máquina azure com uma instância computacional em execução. Para obter mais informações, consulte [ambiente de configuração e espaço de trabalho.](tutorial-1st-experiment-sdk-setup.md)

## <a name="deploy-to-the-compute-instances"></a>Implementar para as instâncias de computação

Um caderno de exemplo que demonstra implementações locais está incluído na sua instância de computação. Utilize os seguintes passos para carregar o caderno e implementar o modelo como um serviço web no VM:

1. A partir do [estúdio Azure Machine Learning,](https://ml.azure.com)selecione as suas instâncias de cálculo Azure Machine Learning.

1. Abra a `samples-*` subdiretória e, em seguida, abra `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb` . Uma vez aberto, executar o caderno.

    ![Screenshot do serviço local em execução no caderno](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)

1. O portátil apresenta o URL e a porta em que o serviço está em funcionamento. Por exemplo, `https://localhost:6789`. Também pode executar a célula que contém `print('Local service port: {}'.format(local_service.port))` para exibir a porta.

    ![Screenshot da porta de serviço local em execução](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. Para testar o serviço a partir de uma instância de computação, utilize o `https://localhost:<local_service.port>` URL. Para testar a partir de um cliente remoto, obtenha o URL público do serviço em execução na instância computacional. O URL público pode ser determinado utilização da seguinte fórmula; 
    * Caderno VM: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score` . 
    * Instância de cálculo: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score` . 

    Por exemplo, 
    * Caderno VM: `https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Instância de cálculo: `https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Testar o serviço

Para submeter os dados da amostra ao serviço de execução, utilize o seguinte código. Substitua o valor do `service_url` URL do passo anterior:

> [!NOTE]
> Ao autenticar uma implementação na instância computacional, a autenticação é feita utilizando o Azure Ative Directory. A chamada para `interactive_auth.get_authentication_header()` o código de exemplo autentica-o usando a AAD e devolve um cabeçalho que pode ser usado para autenticar o serviço na instância de computação. Para obter mais informações, consulte [Configurar a autenticação para os recursos de aprendizagem automática Azure e fluxos de trabalho.](how-to-setup-authentication.md#interactive-authentication)
>
> Ao autenticar uma implantação no Serviço Azure Kubernetes ou em Instâncias de Contentores Azure, é utilizado um método de autenticação diferente. Para obter mais informações, consulte a [autenticação Configure para os modelos Azure Machine implantados como serviços web](how-to-authenticate-web-service.md).

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

* [Como implementar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Resolução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Utilizar o TLS para proteger um serviço Web através do Azure Machine Learning](how-to-secure-web-service.md)
* [Consumir um Modelo ML implantado como um serviço web](how-to-consume-web-service.md)
* [Monitorize os seus modelos de machine learning Azure com Insights de Aplicações](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)
