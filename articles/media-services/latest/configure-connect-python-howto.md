---
title: Ligar à API de v3 dos serviços de multimédia do Azure - Python
description: Saiba como ligar a serviços de multimédia v3 API com Python.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 971e36b600a2c6be516e39ce84ca5780a2f23bbd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60733101"
---
# <a name="connect-to-media-services-v3-api---python"></a>Ligar à API de v3 dos serviços de suporte de dados - Python

Este artigo mostra-lhe como ligar ao SDK dos serviços de multimédia do Azure v3 Python com o início de sessão de principal de serviço no método.

## <a name="prerequisites"></a>Pré-requisitos

- Transferir o Python [python.org](https://www.python.org/downloads/)
- Certifique-se de definir o `PATH` variável de ambiente
- [Criar uma conta de Media Services](create-account-cli-how-to.md). Certifique-se de que não se esqueça de que o nome do grupo de recursos e o nome da conta dos serviços de multimédia.
- Siga os passos a [APIs de acesso](access-api-cli-how-to.md) tópico. Registe o ID de subscrição, ID da aplicação (ID de cliente), a chave de autenticação (segredo) e o ID do inquilino que precisa no passo posterior.

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar com os serviços de multimédia do Azure com o Python, tem de instalar estes módulos.

* O `azure-mgmt-resource` módulo, que inclui módulos do Azure para o Active Directory.
* O `azure-mgmt-media` módulo, que inclui as entidades de serviços de multimédia.

Abra uma ferramenta de linha de comandos e utilize os seguintes comandos para instalar os módulos.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Ligar para o cliente Python

1. Crie um ficheiro com um `.py` extensão
1. Abra o ficheiro no seu editor favorito
1. Adicione o código que se segue para o ficheiro. O código importa os módulos necessários e cria o objeto de credenciais do Active Directory, que tem de se ligar aos Media Services.

      Definir os valores para os valores que obteve da [APIs de acesso](access-api-cli-how-to.md)

      ```
      import adal
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
      from azure.mgmt.media import AzureMediaServices
      from azure.mgmt.media.models import MediaService

      RESOURCE = 'https://management.core.windows.net/'
      # Tenant ID for your Azure Subscription
      TENANT_ID = '00000000-0000-0000-000000000000'
      # Your Service Principal App ID
      CLIENT = '00000000-0000-0000-000000000000'
      # Your Service Principal Password
      KEY = '00000000-0000-0000-000000000000'
      # Your Azure Subscription ID
      SUBSCRIPTION_ID = '00000000-0000-0000-000000000000'
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = 'amsv3account'
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = 'AMSv3ResourceGroup'

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials,
          RESOURCE,
          CLIENT,
          KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print (client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. Execute o ficheiro

## <a name="next-steps"></a>Passos Seguintes

- Uso [Python SDK](https://aka.ms/ams-v3-python-sdk).
- Reveja os serviços de multimédia [Python ref](https://aka.ms/ams-v3-python-ref) documentação.
