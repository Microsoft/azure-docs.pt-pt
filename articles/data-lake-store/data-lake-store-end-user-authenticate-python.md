---
title: 'Autenticação de utilizador final: Python com Gen1 de armazenamento do Azure Data Lake com o Azure Active Directory | Documentos da Microsoft'
description: Saiba como conseguir a autenticação de utilizador final com Gen1 de armazenamento do Azure Data Lake com o Azure Active Directory com o Python
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
ms.openlocfilehash: 2e8859667e3a69f51ae23bab0487bf15d9eeeab6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537189"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Autenticação de utilizador final com Gen1 de armazenamento do Azure Data Lake com o Python
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Utilizar o SDK .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-end-user-authenticate-python.md)
> * [Utilizar a API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
> 

Neste artigo, saiba como utilizar o SDK de Python para fazer a autenticação de utilizador final com Gen1 de armazenamento do Azure Data Lake. Autenticação de utilizador final ainda mais pode ser dividida em duas categorias:

* Autenticação de utilizador final sem multi-factor authentication
* Autenticação de utilizador final com multi-factor authentication

Ambas estas opções são abordadas neste artigo. Para a autenticação serviço a serviço com a geração 1 de armazenamento do Data Lake com o Python, veja [autenticação de serviço para serviço com geração 1 de armazenamento do Data Lake com o Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Python**. Pode transferir o Python [aqui](https://www.python.org/downloads/). Este artigo utiliza a versão Python 3.6.2.

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação de "Nativa" do Azure Active Directory**. Tem de ter concluído os passos em [autenticação de utilizador final com geração 1 de armazenamento do Data Lake com o Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar com a geração 1 de armazenamento do Data Lake com o Python, tem de instalar três módulos.

* O módulo `azure-mgmt-resource`, que inclui módulos do Azure para o Active Directory, etc.
* O `azure-mgmt-datalake-store` módulo, que inclui as operações de gestão de conta de geração 1 de armazenamento do Azure Data Lake. Para obter mais informações sobre este módulo, consulte [referência do módulo de gestão de geração 1 do armazenamento do Azure Data Lake](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* O `azure-datalake-store` módulo, que inclui as operações de sistema de ficheiros de geração 1 de armazenamento do Azure Data Lake. Para obter mais informações sobre este módulo, consulte [referência de módulo do sistema de ficheiros do azure-datalake-store](https://azure-datalake-store.readthedocs.io/en/latest/).

Utilize os comandos seguintes para instalar os módulos.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Criar uma aplicação Python nova

1. No IDE à sua escolha, crie uma aplicação Python nova, por exemplo, **mysample.py**.

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

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Autenticação de utilizador final com multi-factor authentication

### <a name="for-account-management"></a>Para a gestão de conta

Utilize o seguinte fragmento para autenticar com o Azure AD para operações de gestão de conta numa conta de geração 1 de armazenamento do Data Lake. O fragmento seguinte pode ser utilizado para autenticar a sua aplicação com a autenticação multifator. Forneça os valores abaixo para um Azure AD existente **nativo** aplicação.

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

### <a name="for-filesystem-operations"></a>Para operações de sistema de ficheiros

Utilize esta opção para autenticar com o Azure AD para operações de sistema de ficheiros numa conta do Data Lake Storage Gen1. O fragmento seguinte pode ser utilizado para autenticar a sua aplicação com a autenticação multifator. Forneça os valores abaixo para um Azure AD existente **nativo** aplicação.

    adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Autenticação de utilizador final sem multi-factor authentication

Este é preterido. Para obter mais informações, consulte [Authentication do Azure com o SDK de Python](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python#mgmt-auth-token).
   
## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a utilizar a autenticação de utilizador final para se autenticar com a geração 1 de armazenamento do Azure Data Lake com o Python. Agora, pode ver os seguintes artigos que falam sobre como utilizar Python para trabalhar com a geração 1 de armazenamento do Azure Data Lake.

* [Operações de gestão de conta na geração 1 de armazenamento do Data Lake com o Python](data-lake-store-get-started-python.md)
* [Operações de dados no Data Lake Storage Gen1 com Python](data-lake-store-data-operations-python.md)

