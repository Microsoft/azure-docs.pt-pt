---
title: 'Autenticação serviço a serviço: Python com Azure Data Lake Storage Gen1 usando Azure Active Directory | Microsoft Docs'
description: Saiba como obter a autenticação serviço a serviço com Azure Data Lake Storage Gen1 usando Azure Active Directory usando o Python
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 009aff2703829e6d30f93b3c8e3696724594f29b
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76290772"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Autenticação serviço a serviço com Azure Data Lake Storage Gen1 usando Python
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Utilizar o SDK .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Utilizar a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

Neste artigo, você aprenderá como usar o SDK do Python para fazer a autenticação serviço a serviço com o Azure Data Lake Storage Gen1. Para autenticação do usuário final com Data Lake Storage Gen1 usando o Python, confira [autenticação do usuário final com data Lake Storage Gen1 usando Python](data-lake-store-end-user-authenticate-python.md).


## <a name="prerequisites"></a>Pré-requisitos

* **Python**. Pode transferir o Python [aqui](https://www.python.org/downloads/). Este artigo utiliza a versão Python 3.6.2.

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Crie um aplicativo Azure Active Directory "Web"** . Você deve ter concluído as etapas em [Autenticação serviço a serviço com data Lake Storage Gen1 usando Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar com Data Lake Storage Gen1 usando Python, você precisa instalar três módulos.

* O módulo `azure-mgmt-resource`, que inclui módulos do Azure para o Active Directory, etc.
* O módulo `azure-mgmt-datalake-store`, que inclui as operações de gerenciamento de conta de Data Lake Storage Gen1. Para obter mais informações sobre esse módulo, consulte [Azure data Lake Storage Gen1 referência do módulo de gerenciamento](/python/api/azure-mgmt-datalake-store/).
* O módulo `azure-datalake-store`, que inclui as operações de Data Lake Storage Gen1 FileSystem. Para obter mais informações sobre esse módulo, consulte [referência do módulo do sistema de arquivos Azure-datalake-Store](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/).

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

Use este trecho para autenticar com o Azure AD para operações de gerenciamento de conta no Data Lake Storage Gen1 como criar uma conta de Data Lake Storage Gen1, excluir uma conta de Data Lake Storage Gen1, etc. O trecho a seguir pode ser usado para autenticar seu aplicativo de forma não interativa, usando o segredo do cliente para uma entidade de serviço/aplicativo de um aplicativo de "aplicativo Web" do Azure AD existente.

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    RESOURCE = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_credentials(RESOURCE, client_id, client_secret)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource=RESOURCE)

## <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Autenticação de serviço para serviço com segredo do cliente para operações de sistema de ficheiros

Use o trecho a seguir para autenticar com o Azure AD para operações de sistema de arquivos em Data Lake Storage Gen1 como criar pasta, carregar arquivo, etc. O trecho a seguir pode ser usado para autenticar seu aplicativo de forma não interativa, usando o segredo do cliente para uma entidade de serviço/aplicativo. Utilize esta opção com uma ”Aplicação Web“ do Azure AD existente.

    tenant = '<TENANT>'
    RESOURCE = 'https://datalake.azure.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    adlCreds = lib.auth(tenant_id = tenant,
                    client_secret = client_secret,
                    client_id = client_id,
                    resource = RESOURCE)

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
Neste artigo, você aprendeu a usar a autenticação serviço a serviço para autenticar com Data Lake Storage Gen1 usando o Python. Agora você pode examinar os artigos a seguir que falam sobre como usar o Python para trabalhar com Data Lake Storage Gen1.

* [Operações de gerenciamento de conta em Data Lake Storage Gen1 usando Python](data-lake-store-get-started-python.md)
* [Operações de dados em Data Lake Storage Gen1 usando Python](data-lake-store-data-operations-python.md)


