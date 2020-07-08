---
title: Ligue-se ao Azure Media Services v3 API - Python
description: Este artigo demonstra como ligar aos Media Services v3 API com Python.
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
ms.custom: tracking-python
ms.openlocfilehash: 3e6e421d346925f751595ce2fb2d0891e31dc108
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84559625"
---
# <a name="connect-to-media-services-v3-api---python"></a>Ligue-se aos Serviços de Mídia v3 API - Python

Este artigo mostra-lhe como ligar-se ao Azure Media Services v3 Python SDK usando o sinal principal de serviço no método.

## <a name="prerequisites"></a>Pré-requisitos

- Baixar Python de [python.org](https://www.python.org/downloads/)
- Certifique-se de definir a `PATH` variável ambiental
- [Criar uma conta de Serviços de Comunicação](create-account-cli-how-to.md)Social. Lembre-se do nome do grupo de recursos e do nome da conta dos Serviços de Comunicação Social.
- Siga os passos no tópico [das APIs de acesso.](access-api-cli-how-to.md) Grave o ID de assinatura, iD de aplicação (ID do cliente), a chave de autenticação (segredo) e a identificação do inquilino que você precisa no passo posterior.

> [!IMPORTANT]
> Rever [convenções de nomeação](media-services-apis-overview.md#naming-conventions).

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar com a Azure Media Services utilizando o Python, é necessário instalar estes módulos.

* O `azure-mgmt-resource` módulo, que inclui módulos Azure para Diretório Ativo.
* O `azure-mgmt-media` módulo, que inclui as entidades dos Serviços de Comunicação Social.

Abra uma ferramenta de linha de comando e utilize os seguintes comandos para instalar os módulos.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Ligue-se ao cliente Python

1. Criar um ficheiro com uma `.py` extensão
1. Abra o arquivo no seu editor favorito
1. Adicione o código que se segue ao ficheiro. O código importa os módulos necessários e cria o objeto de credenciais ative directory que precisa para ligar aos Serviços de Media.

      Desaperte os valores das variáveis para os valores que obteve das [APIs](access-api-cli-how-to.md) de acesso

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

1. Executar o arquivo

## <a name="next-steps"></a>Próximos passos

- Use [o Python SDK.](https://aka.ms/ams-v3-python-sdk)
- Reveja a documentação do árbitro media [python.](https://aka.ms/ams-v3-python-ref)
