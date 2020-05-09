---
title: Autenticação de utilizador final - Python com Data Lake Storage Gen1 - Azure
description: Saiba como conseguir a autenticação de utilizador final com o Azure Data Lake Storage Gen1 utilizando o Diretório Ativo Azure com python
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref
ms.openlocfilehash: 6d95e8bae428741c82de270507e41b49d23a3793
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691799"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Autenticação de utilizador final com Azure Data Lake Storage Gen1 usando Python
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Com o .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-end-user-authenticate-python.md)
> * [Com a API REST](data-lake-store-end-user-authenticate-rest-api.md)
>
>

Neste artigo, aprende-se a usar o Python SDK para fazer a autenticação de utilizador final com o Azure Data Lake Storage Gen1. A autenticação do utilizador final pode ainda ser dividida em duas categorias:

* Autenticação do utilizador final sem autenticação de vários fatores
* Autenticação do utilizador final com autenticação de vários fatores

Ambas as opções são discutidas neste artigo. Para autenticação de serviço a serviço com Data Lake Storage Gen1 utilizando Python, consulte [a autenticação service-to-service com data Lake Storage Gen1 utilizando Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Python.** Pode transferir o Python [aqui](https://www.python.org/downloads/). Este artigo utiliza a versão Python 3.6.2.

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação de Diretório Ativo Azure "Nativa".** Deve ter concluído os passos de [autenticação do utilizador final com data Lake Storage Gen1 utilizando o Diretório Ativo Azure](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar com data Lake Storage Gen1 usando Python, você precisa instalar três módulos.

* O módulo `azure-mgmt-resource`, que inclui módulos do Azure para o Active Directory, etc.
* O `azure-mgmt-datalake-store` módulo, que inclui as operações de gestão de conta azure Data Lake Gen1. Para obter mais informações sobre este módulo, consulte o módulo de gestão do módulo de armazenamento de dados [do Lago Azur1.](/python/api/azure-mgmt-datalake-store/)
* O `azure-datalake-store` módulo, que inclui as operações do sistema de ficheiros Azure Data Lake Gen1. Para obter mais informações sobre este módulo, consulte a referência do módulo de ficheiros da [loja azure-datalake](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/).

Utilize os comandos seguintes para instalar os módulos.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Criar uma aplicação Python nova

1. No IDE à sua escolha, crie uma nova aplicação Python, por exemplo, **mysample.py**.

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

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Autenticação do utilizador final com autenticação de vários fatores

### <a name="for-account-management"></a>Para gestão de conta

Utilize o seguinte snippet para autenticar com a AD Azure para operações de gestão de conta numa conta Gen1 de Armazenamento de Data Lake. O fragmento seguinte pode ser utilizado para autenticar a sua aplicação com a autenticação multifator. Forneça os valores abaixo para uma aplicação **nativa** azure existente.

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

Utilize isto para autenticar com a AD Azure para operações de sistema de ficheiros numa conta Gen1 de Armazenamento de Data Lake. O fragmento seguinte pode ser utilizado para autenticar a sua aplicação com a autenticação multifator. Forneça os valores abaixo para uma aplicação **nativa** azure existente.

    adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Autenticação do utilizador final sem autenticação de vários fatores

Isto está deprecida. Para mais informações, consulte a [Autenticação Azure utilizando o Python SDK](/azure/python/python-sdk-azure-authenticate).

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a usar a autenticação de utilizador final para autenticar com o Azure Data Lake Storage Gen1 usando python. Pode agora ver os seguintes artigos que falam sobre como usar python para trabalhar com o Azure Data Lake Storage Gen1.

* [Operações de gestão de conta em Data Lake Storage Gen1 usando Python](data-lake-store-get-started-python.md)
* [Operações de dados em Data Lake Storage Gen1 usando Python](data-lake-store-data-operations-python.md)
