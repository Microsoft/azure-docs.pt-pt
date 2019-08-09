---
title: Implantar modelos de ml no serviço de Azure App (versão prévia)
titleSuffix: Azure Machine Learning service
description: Saiba como usar o serviço de Azure Machine Learning para implantar um modelo em um aplicativo Web no serviço Azure App.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/01/2019
ms.openlocfilehash: 84de9d53b19f5aa9b73570aa0d115d204e8b6596
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848207"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Implantar um modelo de aprendizado de máquina no serviço Azure App (versão prévia)

Saiba como implantar um modelo do serviço de Azure Machine Learning como um aplicativo Web no serviço Azure App.

> [!IMPORTANT]
> Embora o serviço Azure Machine Learning e o serviço de Azure App estejam geralmente disponíveis, a capacidade de implantar um modelo do serviço de Machine Learning para o serviço de aplicativo está em versão prévia.

Com o serviço Azure Machine Learning, você pode criar imagens do Docker de modelos de aprendizado de máquina treinados. Essa imagem contém um serviço Web que recebe dados, envia-os para o modelo e, em seguida, retorna a resposta. Azure App serviço pode ser usado para implantar a imagem e fornece os seguintes recursos:

* [Suporte a SSL](/azure/app-service/app-service-web-ssl-cert-load) para comunicações seguras entre clientes e o serviço.
* [Escale horizontalmente](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) para várias instâncias sem precisar reimplantar.
* [Autenticação avançada](/azure/app-service/configure-authentication-provider-aad) para segurança aprimorada.

Para obter mais informações sobre os recursos fornecidos pelo serviço Azure App, consulte [visão geral do serviço de aplicativo](/azure/app-service/overview).

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho de serviço do Azure Machine Learning. Para obter mais informações, consulte o artigo [criar um espaço de trabalho](how-to-manage-workspace.md) .
* Um modelo de aprendizado de máquina treinado registrado em seu espaço de trabalho. Se você não tiver um modelo, use o [tutorial de classificação de imagem: treinar modelo](tutorial-train-models-with-aml.md) para treinar e registrar um.
* Uma imagem do Docker criada a partir do modelo. Se você não tiver uma imagem, use a [imagem classificação: implantar modelo](tutorial-deploy-models-with-aml.md) para criar uma.

## <a name="deploy-image-as-a-web-app"></a>Implantar imagem como um aplicativo Web

1. No [portal do Azure](https://portal.azure.com), selecione seu espaço de trabalho do Azure Machine Learning. Na seção __visão geral__ , use o link __do registro__ para acessar o registro de contêiner do Azure para o espaço de trabalho.

    ![Captura de tela da visão geral do espaço de trabalho](media/how-to-deploy-app-service/workspace-overview.png)

2. No registro de contêiner do Azure,selecione repositórios e, em seguida, selecione o __nome da imagem__ que você deseja implantar. Para a versão que você deseja implantar, selecione a entrada __...__ e, em seguida, __implante no aplicativo Web__.

    ![Captura de tela da implantação do ACR em um aplicativo Web](media/how-to-deploy-app-service/deploy-to-web-app.png)

3. Para criar o aplicativo Web, forneça um nome de site, uma assinatura, um grupo de recursos e selecione o local/plano do serviço de aplicativo. Por fim, selecione __criar__.

    ![Captura de tela da caixa de diálogo novo aplicativo Web](media/how-to-deploy-app-service/web-app-for-containers.png)

## <a name="use-the-web-app"></a>Usar o aplicativo Web

No [portal do Azure](https://portal.azure.com), selecione o aplicativo Web criado na etapa anterior. Na seção __visão geral__ , copie a __URL__. Esse valor é a __URL base__ do serviço.

![Captura de tela da visão geral do aplicativo Web](media/how-to-deploy-app-service/web-app-overview.png)

O serviço Web que passa solicitações para o modelo está localizado em `{baseurl}/score`. Por exemplo, `https://mywebapp.azurewebsites.net/score`. O código Python a seguir demonstra como enviar dados para a URL e exibir a resposta:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre como configurar seu aplicativo Web, consulte a documentação do [serviço de aplicativo no Linux](/azure/app-service/containers/) .
* Para obter mais informações sobre o dimensionamento, consulte Introdução [ao dimensionamento automático no Azure](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json).
* Para obter mais informações sobre o suporte a SSL, consulte [usar um certificado SSL em seu serviço de Azure app](/azure/app-service/app-service-web-ssl-cert-load).
* Para obter mais informações sobre autenticação, consulte [configurar seu aplicativo do serviço de aplicativo para usar Azure Active Directory entrar](/azure/app-service/configure-authentication-provider-aad).