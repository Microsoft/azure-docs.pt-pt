---
title: Autenticação de utilizador final - Python com Data Lake Storage Gen1 - Azure
description: Saiba como obter a autenticação do utilizador final com a Azure Data Lake Storage Gen1 utilizando o Azure Ative Directory com Python
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref, devx-track-python
ms.openlocfilehash: c3262a2802da8dbd50fc05f7aa2fea71623c9b42
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87872196"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Autenticação de utilizador final com Azure Data Lake Storage Gen1 usando Python
> [!div class="op_single_selector"]
> * [Utilizar o Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Com o .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Utilizar o Python](data-lake-store-end-user-authenticate-python.md)
> * [Utilização de REST API](data-lake-store-end-user-authenticate-rest-api.md)
>
>

Neste artigo, você aprende sobre como usar o Python SDK para fazer a autenticação do utilizador final com Azure Data Lake Storage Gen1. A autenticação do utilizador final pode ser dividida em duas categorias:

* Autenticação do utilizador final sem autenticação de vários fatores
* Autenticação do utilizador final com autenticação de vários fatores

Ambas as opções são discutidas neste artigo. Para autenticação de serviço-a-serviço com data lake storage gen1 usando Python, consulte [a autenticação de serviço-a-serviço com data lake storage gen1 usando Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Python.** Pode transferir o Python [aqui](https://www.python.org/downloads/). Este artigo utiliza a versão Python 3.6.2.

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar uma aplicação "Native" do Diretório Ativo Azure**. Deve ter completado os passos na [autenticação do utilizador final com a Data Lake Storage Gen1 utilizando o Azure Ative Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar com a Data Lake Storage Gen1 usando python, você precisa instalar três módulos.

* O módulo `azure-mgmt-resource`, que inclui módulos do Azure para o Active Directory, etc.
* O `azure-mgmt-datalake-store` módulo, que inclui as operações de gestão da conta Azure Data Lake Storage Gen1. Para obter mais informações sobre este módulo, consulte [a referência do módulo de gestão de armazenamento de dados Azure Data Lake Gen1.](/python/api/azure-mgmt-datalake-store/)
* O `azure-datalake-store` módulo, que inclui as operações do sistema de ficheiros Azure Data Lake Storage Gen1. Para obter mais informações sobre este módulo, consulte [a referência do módulo filesystem azure-datalake-store](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/).

Utilize os comandos seguintes para instalar os módulos.

```console
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

Utilize o seguinte corte para autenticar com a Azure AD para operações de gestão de conta numa conta da Data Lake Storage Gen1. O fragmento seguinte pode ser utilizado para autenticar a sua aplicação com a autenticação multifator. Forneça os valores abaixo para uma aplicação **nativa** AZure AD existente.

```python
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
```

### <a name="for-filesystem-operations"></a>Para operações de sistema de ficheiros

Utilize isto para autenticar com a Azure AD para operações de sistema de ficheiros numa conta da Data Lake Storage Gen1. O fragmento seguinte pode ser utilizado para autenticar a sua aplicação com a autenticação multifator. Forneça os valores abaixo para uma aplicação **nativa** AZure AD existente.

```console
adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')
```

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Autenticação do utilizador final sem autenticação de vários fatores

Isto é precotado. Para obter mais informações, consulte [a autenticação Azure utilizando a Python SDK](/azure/python/python-sdk-azure-authenticate).

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a usar a autenticação do utilizador final para autenticar com a Azure Data Lake Storage Gen1 usando Python. Agora pode olhar para os seguintes artigos que falam sobre como usar Python para trabalhar com a Azure Data Lake Storage Gen1.

* [Operações de gestão de conta na Data Lake Storage Gen1 usando Python](data-lake-store-get-started-python.md)
* [Operações de dados em Data Lake Storage Gen1 usando Python](data-lake-store-data-operations-python.md)
