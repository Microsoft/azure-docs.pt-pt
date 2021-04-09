---
title: Ligue-se ao Azure Media Services v3 API - Python
description: Este artigo demonstra como ligar aos Media Services v3 API com Python.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-python
ms.openlocfilehash: 24e2ba4027dc818256dc9572f697fe7ec5a5a56b
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960712"
---
# <a name="connect-to-media-services-v3-api---python"></a>Ligue-se aos Serviços de Mídia v3 API - Python

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo mostra-lhe como ligar-se ao Azure Media Services v3 Python SDK usando o sinal principal de serviço no método.

## <a name="prerequisites"></a>Pré-requisitos

- Baixar Python de [python.org](https://www.python.org/downloads/)
- Certifique-se de definir a `PATH` variável ambiental
- [Criar uma conta de Serviços de Comunicação](./account-create-how-to.md)Social. Lembre-se do nome do grupo de recursos e do nome da conta dos Serviços de Comunicação Social.
- Siga os passos no tópico [das APIs de acesso,](./access-api-howto.md) selecionando o método de autenticação principal do Serviço. Registe o ID de subscrição `SubscriptionId` , ID do cliente de aplicação , `AadClientId` a chave de autenticação ( ) e `AadSecret` o ID do inquilino que você precisa `AadTenantId` nos passos posteriores.

> [!IMPORTANT]
> Rever [convenções de nomeação](media-services-apis-overview.md#naming-conventions).

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar com a Azure Media Services utilizando o Python, é necessário instalar estes módulos.

* O `azure-mgmt-resource` módulo, que inclui módulos Azure para Diretório Ativo.
* O `azure-mgmt-media` módulo, que inclui as entidades dos Serviços de Comunicação Social.

    Certifique-se de obter [a versão mais recente do Media Services SDK para Python](https://pypi.org/project/azure-mgmt-media/).

Abra uma ferramenta de linha de comando e utilize os seguintes comandos para instalar os módulos.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==3.0.0
```

## <a name="connect-to-the-python-client"></a>Ligue-se ao cliente Python

1. Criar um ficheiro com uma `.py` extensão
1. Abra o arquivo no seu editor favorito
1. Adicione o código que se segue ao ficheiro. O código importa os módulos necessários e cria o objeto de credenciais ative directory que precisa para ligar aos Serviços de Media.

      Desaperte os valores das variáveis para os valores que obteve das APIs de [acesso.](./access-api-howto.md) Atualize as `ACCOUNT_NAME` `RESOUCE_GROUP_NAME` variáveis e variáveis para o nome da conta dos Serviços de Comunicação e nomes do Grupo de Recursos utilizados na criação desses recursos.

      ```
      import adal
      from azure.mgmt.media import AzureMediaServices
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD

      RESOURCE = "https://management.core.windows.net/"
      # Tenant ID for your Azure Subscription (AadTenantId)
      TENANT_ID = "00000000-0000-0000-000000000000"
      # Your Service Principal App ID (AadClientId)
      CLIENT = "00000000-0000-0000-000000000000"
      # Your Service Principal Password (AadSecret)
      KEY = "00000000-0000-0000-000000000000"
      # Your Azure Subscription ID (SubscriptionId)
      SUBSCRIPTION_ID = "00000000-0000-0000-000000000000"
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = "amsaccount"
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = "amsResourceGroup"

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + "/" + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials, RESOURCE, CLIENT, KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print(client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. Executar o arquivo

## <a name="next-steps"></a>Passos seguintes

- Use [o Python SDK.](https://aka.ms/ams-v3-python-sdk)
- Reveja a documentação do árbitro media [python.](/python/api/overview/azure/mediaservices/management)
