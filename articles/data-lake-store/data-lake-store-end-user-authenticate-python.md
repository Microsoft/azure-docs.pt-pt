---
title: 'Autenticação do usuário final: Python com Azure Data Lake Storage Gen1 usando Azure Active Directory | Microsoft Docs'
description: Saiba como obter a autenticação do usuário final com Azure Data Lake Storage Gen1 usando Azure Active Directory com Python
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
ms.openlocfilehash: 6a7e1b36e01094ea8ce65a785de0aad9494f4dbb
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088885"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Autenticação do usuário final com Azure Data Lake Storage Gen1 usando Python
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Utilizar o SDK .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-end-user-authenticate-python.md)
> * [Utilizar a API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
> 

Neste artigo, você aprenderá como usar o SDK do Python para fazer a autenticação do usuário final com o Azure Data Lake Storage Gen1. A autenticação do usuário final pode ser dividida em duas categorias:

* Autenticação do usuário final sem autenticação multifator
* Autenticação do usuário final com autenticação multifator

Essas duas opções são discutidas neste artigo. Para autenticação serviço a serviço com Data Lake Storage Gen1 usando Python, consulte [Autenticação serviço a serviço com data Lake Storage Gen1 usando Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Python**. Pode transferir o Python [aqui](https://www.python.org/downloads/). Este artigo utiliza a versão Python 3.6.2.

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Crie um aplicativo Azure Active Directory "nativo"** . Você deve ter concluído as etapas em [autenticação do usuário final com data Lake Storage Gen1 usando Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar com Data Lake Storage Gen1 usando Python, você precisa instalar três módulos.

* O módulo `azure-mgmt-resource`, que inclui módulos do Azure para o Active Directory, etc.
* O `azure-mgmt-datalake-store` módulo, que inclui as operações de gerenciamento de conta Azure data Lake Storage Gen1. Para obter mais informações sobre esse módulo, consulte [Azure data Lake Storage Gen1 referência do módulo de gerenciamento](/python/api/azure-mgmt-datalake-store/).
* O `azure-datalake-store` módulo, que inclui as operações de sistema de arquivos Azure data Lake Storage Gen1. Para obter mais informações sobre esse módulo, consulte [referência do módulo do sistema de arquivos Azure-datalake-Store](https://azure-datalake-store.readthedocs.io/en/latest/).

Utilize os comandos seguintes para instalar os módulos.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Criar uma aplicação Python nova

1. No IDE de sua escolha, crie um novo aplicativo Python, por exemplo, **MySample.py**.

2. Adicione o fragmento de código seguinte para importar os módulos necessários

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, pprint, uuid, time
    ```

3. Guarde as alterações a mysample.py.

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Autenticação do usuário final com autenticação multifator

### <a name="for-account-management"></a>Para gerenciamento de conta

Use o trecho a seguir para autenticar com o Azure AD para operações de gerenciamento de conta em uma conta de Data Lake Storage Gen1. O fragmento seguinte pode ser utilizado para autenticar a sua aplicação com a autenticação multifator. Forneça os valores abaixo para um aplicativo **nativo** existente do Azure AD.

    authority_host_url = "https://login.microsoftonline.com"
    tenant = "FILL-IN-HERE"
    authority_url = authority_host_url + '/' + tenant
    client_id = 'FILL-IN-HERE'
    redirect = 'urn:ietf:wg:oauth:2.0:oob'
    RESOURCE = 'https://management.core.windows.net/'
    
    context = adal.AuthenticationContext(authority_url)
    code = context.acquire_user_code(RESOURCE, client_id)
    print(code['message'])
    mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource = RESOURCE)

### <a name="for-filesystem-operations"></a>Para operações de sistema de arquivos

Use isso para autenticar com o Azure AD para operações de sistema de arquivos em uma conta de Data Lake Storage Gen1. O fragmento seguinte pode ser utilizado para autenticar a sua aplicação com a autenticação multifator. Forneça os valores abaixo para um aplicativo **nativo** existente do Azure AD.

    adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Autenticação do usuário final sem autenticação multifator

Isso é preterido. Para obter mais informações, consulte [autenticação do Azure usando o SDK do Python](/azure/python/python-sdk-azure-authenticate).
   
## <a name="next-steps"></a>Passos seguintes
Neste artigo, você aprendeu a usar a autenticação do usuário final para se autenticar com Azure Data Lake Storage Gen1 usando o Python. Agora você pode examinar os artigos a seguir que falam sobre como usar o Python para trabalhar com Azure Data Lake Storage Gen1.

* [Operações de gerenciamento de conta em Data Lake Storage Gen1 usando Python](data-lake-store-get-started-python.md)
* [Operações de dados em Data Lake Storage Gen1 usando Python](data-lake-store-data-operations-python.md)

