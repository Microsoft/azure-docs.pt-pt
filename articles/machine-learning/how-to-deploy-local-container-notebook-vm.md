---
title: Como implementar modelos para calcular instâncias
titleSuffix: Azure Machine Learning
description: Aprenda a implementar os seus modelos Azure Machine Learning como um serviço web utilizando instâncias de cálculo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: 09164580b8bdb249fc12d14e827ad799d51cab34
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756586"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Implementar um modelo para os casos de computação de machine learning azure

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a utilizar o Azure Machine Learning para implementar um modelo como serviço web na sua instância de computação Azure Machine Learning. Utilize instâncias computadas se uma das seguintes condições for verdadeira:

- Precisa de implantar e validar rapidamente o seu modelo.
- Estáa testar um modelo que está em desenvolvimento.

> [!TIP]
> A implementação de um modelo de um Caderno Jupyter numa instância de computação, para um serviço web no mesmo VM é uma _implantação local._ Neste caso, o computador 'local' é o exemplo do cálculo. Para obter mais informações sobre as implementações, consulte [modelos de implantação com aprendizagem automática Azure](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Pré-requisitos

- Um espaço de trabalho azure machine learning com uma instância de computação em execução. Para mais informações, consulte [ambiente de configuração e espaço de trabalho.](tutorial-1st-experiment-sdk-setup.md)

## <a name="deploy-to-the-compute-instances"></a>Implementar para as instâncias de computação

Um caderno de exemplo que demonstra implementações locais está incluído na sua instância de cálculo. Utilize os seguintes passos para carregar o caderno e implementar o modelo como um serviço web no VM:

1. Do [estúdio Azure Machine Learning,](https://ml.azure.com)selecione os seus casos de computação Azure Machine Learning.

1. Abra `samples-*` o subdiretório e `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`abra. Uma vez aberto, execute o caderno.

    ![Screenshot do serviço local em execução no caderno](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)

1. O caderno mostra o URL e a porta em que o serviço está a funcionar. Por exemplo, `https://localhost:6789`. Também pode executar a `print('Local service port: {}'.format(local_service.port))` célula contendo para exibir a porta.

    ![Screenshot do porto de serviço local em execução](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. Para testar o serviço a partir `https://localhost:<local_service.port>` de uma instância de cálculo, utilize o URL. Para testar a partir de um cliente remoto, obtenha o URL público do serviço em execução na instância de computação. O URL público pode ser determinado usar a seguinte fórmula; 
    * Notebook VM: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score`. 
    * Exemplo de `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score`computação: . 

    Por exemplo, 
    * VM do caderno:`https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Instância computacional:`https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Testar o serviço

Para submeter os dados da amostra ao serviço de execução, utilize o seguinte código. Substitua o `service_url` valor do URL do passo anterior:

> [!NOTE]
> Ao autenticar uma implantação na instância computacional, a autenticação é feita utilizando o Diretório Ativo Azure. A chamada `interactive_auth.get_authentication_header()` para o código de exemplo autentica-o usando AAD e devolve um cabeçalho que pode ser usado para autenticar o serviço na instância computacional. Para mais informações, consulte [A autenticação configurar os recursos e fluxos de trabalho do Azure Machine Learning.](how-to-setup-authentication.md#interactive-authentication)
>
> Ao autenticar uma implantação no Serviço Azure Kubernetes ou nas instâncias de contentores Azure, é utilizado um método de autenticação diferente. Para mais informações, consulte [A autenticação configurar os recursos e fluxos de trabalho do Azure Machine Learning.](how-to-setup-authentication.md#web-service-authentication)

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
* [Use tLS para garantir um serviço web através de Azure Machine Learning](how-to-secure-web-service.md)
* [Consumir um Modelo ML implantado como um serviço web](how-to-consume-web-service.md)
* [Monitorize os seus modelos de Aprendizagem automática Azure com Insights de Aplicação](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)