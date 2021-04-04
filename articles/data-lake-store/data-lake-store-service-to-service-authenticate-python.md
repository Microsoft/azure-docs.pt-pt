---
title: Python - Autenticação de serviço-a-serviço - Data Lake Storage Gen1
description: Saiba como obter a autenticação de serviço-a-serviço com a Azure Data Lake Storage Gen1 utilizando o Azure Ative Directory usando Python
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref, devx-track-python
ms.openlocfilehash: fe987070438c689e899dee18edaf4f708555ab11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92106761"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Autenticação de serviço-a-serviço com Azure Data Lake Storage Gen1 usando Python
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Com o .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Utilizar a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

Neste artigo, você aprende sobre como usar o Python SDK para fazer autenticação de serviço-a-serviço com Azure Data Lake Storage Gen1. Para autenticação do utilizador final com data lake storage gen1 usando Python, consulte [a autenticação do utilizador final com data lake storage gen1 usando Python](data-lake-store-end-user-authenticate-python.md).


## <a name="prerequisites"></a>Pré-requisitos

* **Python.** Pode transferir o Python [aqui](https://www.python.org/downloads/). Este artigo utiliza a versão Python 3.6.2.

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação "Web" do Diretório Ativo Azure**. Deve ter completado os passos na [autenticação de serviço-a-serviço com a Data Lake Storage Gen1 utilizando o Azure Ative Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar com a Data Lake Storage Gen1 usando python, você precisa instalar três módulos.

* O módulo `azure-mgmt-resource`, que inclui módulos do Azure para o Active Directory, etc.
* O `azure-mgmt-datalake-store` módulo, que inclui as operações de gestão da conta de armazenamento de dados Da Gen1. Para obter mais informações sobre este módulo, consulte [a referência do módulo de gestão de armazenamento de dados Azure Data Lake Gen1.](/python/api/azure-mgmt-datalake-store/)
* O `azure-datalake-store` módulo, que inclui as operações do sistema de ficheiros Data Lake Storage Gen1. Para obter mais informações sobre este módulo, consulte [a referência do módulo filesystem azure-datalake-store](/python/api/azure-datalake-store/azure.datalake.store.core/).

Utilize os comandos seguintes para instalar os módulos.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Criar uma aplicação Python nova

1. No IDE à sua escolha, crie uma aplicação Python nova, como, por exemplo, **mysample.py**.

2. Adicione o fragmento de código seguinte para importar os módulos necessários

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Guarde as alterações a mysample.py.

## <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>Autenticação serviço para serviço com segredo do cliente para gestão da conta

Utilize este corte para autenticar com a Azure AD para operações de gestão de conta na Data Lake Storage Gen1, como criar uma conta Gen1 de armazenamento de data lake, eliminar uma conta Gen1 de armazenamento de dados, etc. O seguinte corte pode ser usado para autenticar a sua aplicação não interativamente, utilizando o segredo do cliente para uma aplicação/diretor de serviço de uma aplicação "Web App" Azure Ad existente.

```python
authority_host_uri = 'https://login.microsoftonline.com'
tenant = '<TENANT>'
authority_uri = authority_host_uri + '/' + tenant
RESOURCE = 'https://management.core.windows.net/'
client_id = '<CLIENT_ID>'
client_secret = '<CLIENT_SECRET>'

context = adal.AuthenticationContext(authority_uri, api_version=None)
mgmt_token = context.acquire_token_with_client_credentials(RESOURCE, client_id, client_secret)
armCreds = AADTokenCredentials(mgmt_token, client_id, resource=RESOURCE)
```

## <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Autenticação de serviço para serviço com segredo do cliente para operações de sistema de ficheiros

Utilize o seguinte corte para autenticar com a Azure AD para operações de sistema de ficheiros na Data Lake Storage Gen1, como criar pasta, ficheiro de upload, etc. O seguinte corte pode ser usado para autenticar a sua aplicação não interativamente, utilizando o segredo do cliente para uma aplicação/diretor de serviço. Utilize esta opção com uma ”Aplicação Web“ do Azure AD existente.

```python
tenant = '<TENANT>'
RESOURCE = 'https://datalake.azure.net/'
client_id = '<CLIENT_ID>'
client_secret = '<CLIENT_SECRET>'

adlCreds = lib.auth(tenant_id = tenant,
                client_secret = client_secret,
                client_id = client_id,
                resource = RESOURCE)
```

<!-- ## Service-to-service authentication with certificate for account management

Use this snippet to authenticate with Azure AD for account management operations on Data Lake Storage Gen1 such as create a Data Lake Storage Gen1 account, delete a Data Lake Storage Gen1 account, etc. The following snippet can be used to authenticate your application non-interactively, using the certificate of an existing Azure AD "Web App" application. For instructions on how to create an Azure AD application, see [Create service principal with certificates](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    resource_uri = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_cert = '<CLIENT_CERT>'
    client_cert_thumbprint = '<CLIENT_CERT_THUMBPRINT>'

    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_certificate(resource_uri, client_id, client_cert, client_cert_thumbprint)
    credentials = AADTokenCredentials(mgmt_token, client_id) -->

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a usar a autenticação de serviço-a-serviço para autenticar com data lake storage Gen1 usando Python. Agora pode olhar para os seguintes artigos que falam sobre como usar Python para trabalhar com data lake storage gen1.

* [Operações de gestão de conta na Data Lake Storage Gen1 usando Python](data-lake-store-get-started-python.md)
* [Operações de dados em Data Lake Storage Gen1 usando Python](data-lake-store-data-operations-python.md)