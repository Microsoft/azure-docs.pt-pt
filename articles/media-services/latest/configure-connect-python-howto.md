---
title: Ligue-se ao Azure Media Services v3 API - Python
description: Este artigo demonstra como se conectar aos Media Services v3 API com python.
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
ms.openlocfilehash: 98a8cdf4120cf56184eb5735249640e3423acdf4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74888466"
---
# <a name="connect-to-media-services-v3-api---python"></a>Ligação aos Media Services v3 API - Python

Este artigo mostra-lhe como se conectar ao Azure Media Services v3 Python SDK utilizando o sinal principal de serviço no método.

## <a name="prerequisites"></a>Pré-requisitos

- Baixar Python de [python.org](https://www.python.org/downloads/)
- Certifique-se de `PATH` definir a variável ambiental
- [Criar uma conta de Media Services.](create-account-cli-how-to.md) Lembre-se do nome do grupo de recursos e do nome da conta Media Services.
- Siga os passos no tópico [access APIs.](access-api-cli-how-to.md) Grave o ID de subscrição, o ID da aplicação (ID do cliente), a chave de autenticação (segredo) e o ID do inquilino de que necessita no passo posterior.

> [!IMPORTANT]
> Rever convenções de [nomeação.](media-services-apis-overview.md#naming-conventions)

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar com a Azure Media Services utilizando a Python, é necessário instalar estes módulos.

* O `azure-mgmt-resource` módulo, que inclui módulos Azure para Diretório Ativo.
* O `azure-mgmt-media` módulo, que inclui as entidades dos Serviços de Media.

Abra uma ferramenta de linha de comando e utilize os seguintes comandos para instalar os módulos.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Ligue-se ao cliente Python

1. Criar um ficheiro `.py` com uma extensão
1. Abra o ficheiro no seu editor favorito
1. Adicione o código que se segue ao ficheiro. O código importa os módulos necessários e cria o objeto de credenciais de Diretório Ativo que precisa para ligar aos Serviços de Media.

      Detete os valores das variáveis para os valores que obteve do [Access APIs](access-api-cli-how-to.md)

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

1. Executar o ficheiro

## <a name="next-steps"></a>Passos seguintes

- Use [Python SDK](https://aka.ms/ams-v3-python-sdk).
- Reveja a documentação ref dos Media Services [Python.](https://aka.ms/ams-v3-python-ref)
