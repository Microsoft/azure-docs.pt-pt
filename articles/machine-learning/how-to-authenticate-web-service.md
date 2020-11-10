---
title: Configure a autenticação para modelos implementados como serviços web
titleSuffix: Azure Machine Learning
description: Saiba como configurar a autenticação para modelos de machine learning implantados em serviços web em Azure Machine Learning.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 11/06/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5bd938fce347d439c2acb4e3fcace04d5b27d770
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94447778"
---
# <a name="configure-authentication-for-models-deployed-as-web-services"></a>Configure a autenticação para modelos implementados como serviços web

O Azure Machine Learning permite-lhe implementar os seus modelos de aprendizagem automática treinados como serviços web. Neste artigo, saiba como configurar a autenticação para estas implementações.

As implementações do modelo criadas pela Azure Machine Learning podem ser configuradas para utilizar um de dois métodos de autenticação:

* **baseado em teclas** : Uma tecla estática é usada para autenticar o serviço web.
* **Baseado em símbolos** : Um símbolo temporário deve ser obtido a partir do espaço de trabalho Azure Machine Learning (utilizando o Azure Ative Directory) e utilizado para autenticar o serviço web. Este token expira após um período de tempo, e deve ser atualizado para continuar a trabalhar com o serviço web.

    > [!NOTE]
    > A autenticação baseada em token só está disponível quando é implantada no Serviço Azure Kubernetes.

## <a name="key-based-authentication"></a>Autenticação baseada em chaves

Os serviços web implantados no Serviço Azure Kubernetes (AKS) têm auth baseado em chaves *ativada* por padrão.

Os serviços implantados em Azure Container Instances (ACI) têm auth baseado em chaves *desativados* por padrão, mas pode capacitá-lo definindo `auth_enabled=True` ao criar o serviço web ACI. O código a seguir é um exemplo de criação de uma configuração de implementação de ACI com auth baseado em chave ativada.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Em seguida, pode utilizar a configuração ACI personalizada na implementação utilizando a `Model` classe.

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

Para ir buscar as teclas auth, `aci_service.get_keys()` use. Para regenerar uma chave, utilize a `regen_key()` função e passe tanto **no Primário** como **no Secundário**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

## <a name="token-based-authentication"></a>Autenticação baseada em tokens

Quando ativa a autenticação simbólica de um serviço web, os utilizadores devem apresentar um Azure Machine Learning JSON Web Token ao serviço web para aceder ao mesmo. O token expira após um prazo especificado e precisa de ser atualizado para continuar a fazer chamadas.

* A autenticação token é **desativada por padrão** quando implementa para o Serviço Azure Kubernetes.
* A autenticação **token não é suportada** quando se implanta em Instâncias de Contentores Azure.
* A autenticação token **não pode ser utilizada ao mesmo tempo que a autenticação baseada em chaves.**

Para controlar a autenticação simbólica, utilize o `token_auth_enabled` parâmetro quando criar ou atualizar uma implementação:

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

Se a autenticação simbólica estiver ativada, pode utilizar o `get_token` método para recuperar um Token Web JSON (JWT) e o tempo de validade do token:

> [!TIP]
> Se utilizar um principal de serviço para obter o token, e quiser que tenha o acesso mínimo necessário para recuperar um token, atribua-o ao papel de **leitor** para o espaço de trabalho.

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> Precisará de pedir um novo token depois do prazo `refresh_by` do token. Se precisar de atualizar fichas fora do Python SDK, uma opção é utilizar a API REST com autenticação principal de serviço para fazer a `service.get_token()` chamada periodicamente, como discutido anteriormente.
>
> Recomendamos vivamente que crie o seu espaço de trabalho Azure Machine Learning na mesma região que o seu cluster de Serviço Azure Kubernetes.
>
> Para autenticar com um token, o serviço web fará uma chamada para a região em que o seu espaço de trabalho Azure Machine Learning é criado. Se a sua região de espaço de trabalho não estiver disponível, não poderá obter um símbolo para o seu serviço web, mesmo que o seu cluster esteja numa região diferente do seu espaço de trabalho. O resultado é que a autenticação Ad AD Azure não está disponível até que a região do espaço de trabalho esteja novamente disponível.
>
> Além disso, quanto maior for a distância entre a região do seu cluster e a região do seu espaço de trabalho, mais tempo demorará a obter um símbolo.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a autenticação de um modelo implantado, consulte [Criar um cliente para um modelo implantado como serviço web](how-to-consume-web-service.md).