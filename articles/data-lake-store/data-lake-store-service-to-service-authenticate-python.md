---
title: 'Autenticação serviço a serviço: Python com Gen1 de armazenamento do Azure Data Lake com o Azure Active Directory | Documentos da Microsoft'
description: Saiba como conseguir a autenticação do serviço para serviço com Gen1 de armazenamento do Azure Data Lake com o Azure Active Directory com o Python
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 4cad09877a3fc4f586db2d92ce97a64b6efe295e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537155"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Autenticação do serviço para serviço com Gen1 de armazenamento do Azure Data Lake com o Python
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Utilizar o SDK .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Utilizar a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

Neste artigo, saiba como utilizar o Python SDK para efetuar autenticação serviço a serviço com a geração 1 de armazenamento do Azure Data Lake. Para autenticação de utilizador final com geração 1 de armazenamento do Data Lake com o Python, veja [autenticação de utilizador final com geração 1 de armazenamento do Data Lake com o Python](data-lake-store-end-user-authenticate-python.md).


## <a name="prerequisites"></a>Pré-requisitos

* **Python**. Pode transferir o Python [aqui](https://www.python.org/downloads/). Este artigo utiliza a versão Python 3.6.2.

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação de "Web" do Azure Active Directory**. Tem de ter concluído os passos em [autenticação do serviço para serviço com a geração 1 de armazenamento do Data Lake com o Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar com a geração 1 de armazenamento do Data Lake com o Python, tem de instalar três módulos.

* O módulo `azure-mgmt-resource`, que inclui módulos do Azure para o Active Directory, etc.
* O `azure-mgmt-datalake-store` módulo, que inclui as operações de gestão de conta de geração 1 de armazenamento do Data Lake. Para obter mais informações sobre este módulo, consulte [referência do módulo de gestão de geração 1 do armazenamento do Azure Data Lake](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* O `azure-datalake-store` módulo, que inclui as operações de sistema de ficheiros de geração 1 de armazenamento do Data Lake. Para obter mais informações sobre este módulo, consulte [referência de módulo do sistema de ficheiros do azure-datalake-store](https://azure-datalake-store.readthedocs.io/en/latest/).

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

Utilize este trecho de código para autenticar com o Azure AD para operações de gestão de conta no Data Lake Storage Gen1, tais como criar uma conta de geração 1 de armazenamento do Data Lake, a conta de eliminação de uma geração de 1 de armazenamento do Data Lake, etc. O fragmento seguinte pode ser utilizado para autenticar a aplicação de forma não interativa, com o segredo de cliente para uma aplicação / serviço principal de um Azure AD existente "Aplicação Web" aplicação.

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

Utilize o fragmento seguinte para se autenticar com o Azure AD para operações de sistema de ficheiros no Data Lake Storage Gen1, tais como criar pastas, carregar ficheiros, etc. O fragmento seguinte pode ser utilizado para autenticar a aplicação de forma não interativa com o segredo do cliente de uma aplicação/principal de serviço. Utilize esta opção com uma ”Aplicação Web“ do Azure AD existente.

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

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a utilizar a autenticação de serviço a serviço para autenticar com a geração 1 de armazenamento do Data Lake com o Python. Agora, pode ver os seguintes artigos que falam sobre como utilizar Python para trabalhar com a geração 1 de armazenamento do Data Lake.

* [Operações de gestão de conta na geração 1 de armazenamento do Data Lake com o Python](data-lake-store-get-started-python.md)
* [Operações de dados no Data Lake Storage Gen1 com Python](data-lake-store-data-operations-python.md)


