---
title: Gerir uma conta Azure Data Lake Storage Gen1 com Python
description: Saiba como usar o Python SDK para operações de gestão de conta de Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-python
ms.openlocfilehash: 35e71b80c6f47bb13f7a2b490b493b0cb42acf04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103514"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>Operações de gestão de conta na Azure Data Lake Storage Gen1 usando Python
> [!div class="op_single_selector"]
> * [SDK do .NET](data-lake-store-get-started-net-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Saiba como usar o Python SDK para o Azure Data Lake Storage Gen1 para executar operações básicas de gestão de conta, tais como criar uma conta Gen1 de armazenamento de data lake, listar as contas de Data Lake Storage Gen1, etc. Para obter instruções sobre como executar operações de sistema de ficheiros na Data Lake Storage Gen1 utilizando python, consulte [as operações do Sistema de Ficheiros na Data Lake Storage Gen1 utilizando python](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Python.** Pode transferir o Python [aqui](https://www.python.org/downloads/). Este artigo utiliza a versão Python 3.6.2.

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Um grupo de recursos do Azure**. Para obter instruções, veja [Criar um grupo de recursos do Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar com a Data Lake Storage Gen1 usando python, você precisa instalar três módulos.

* O módulo `azure-mgmt-resource`, que inclui módulos do Azure para o Active Directory, etc.
* O `azure-mgmt-datalake-store` módulo, que inclui as operações de gestão da conta Azure Data Lake Storage Gen1. Para obter mais informações sobre este módulo, consulte [a referência do módulo de gestão de armazenamento de dados Azure Data Lake Gen1.](/python/api/azure-mgmt-datalake-store/)
* O `azure-datalake-store` módulo, que inclui as operações do sistema de ficheiros Azure Data Lake Storage Gen1. Para obter mais informações sobre este módulo, consulte [a referência do módulo de ficheiros azure-datalake-store](/python/api/azure-datalake-store/azure.datalake.store.core/).

Utilize os comandos seguintes para instalar os módulos.

```console
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Criar uma aplicação Python nova

1. No IDE à sua escolha, crie uma aplicação Python nova, como, por exemplo, **mysample.py**.

2. Adicione o fragmento de código seguinte para importar os módulos necessários

    ```python
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Guarde as alterações a mysample.py.

## <a name="authentication"></a>Autenticação

Nesta secção, vamos falar sobre as diferentes formas de autenticar com o Azure AD. As opções disponíveis são:

* Para autenticação do utilizador final para a sua aplicação, consulte [a autenticação do utilizador final com a Data Lake Storage Gen1 utilizando python](data-lake-store-end-user-authenticate-python.md).
* Para autenticação de serviço-a-serviço para a sua aplicação, consulte [a autenticação de serviço-a-serviço com a Data Lake Storage Gen1 utilizando python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-client-and-data-lake-storage-gen1-account"></a>Criar conta Gen1 de armazenamento de clientes e data lake

O seguinte corte cria primeiro o cliente da conta Desaguisamento de Data Lake Gen1. Usa o objeto do cliente para criar uma conta Gen1 de armazenamento de dados. Por fim, o fragmento cria um objeto de cliente do sistema de ficheiros.

```python
## Declare variables
subscriptionId = 'FILL-IN-HERE'
adlsAccountName = 'FILL-IN-HERE'
resourceGroup = 'FILL-IN-HERE'
location = 'eastus2'

## Create Data Lake Storage Gen1 account management client object
adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

## Create a Data Lake Storage Gen1 account
adlsAcctResult = adlsAcctClient.account.create(
    resourceGroup,
    adlsAccountName,
    DataLakeStoreAccount(
        location=location
    )
).wait()
```

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>Listar as contas gen1 de armazenamento de data lake

```python
## List the existing Data Lake Storage Gen1 accounts
result_list_response = adlsAcctClient.account.list()
result_list = list(result_list_response)
for items in result_list:
    print(items)
```

## <a name="delete-the-data-lake-storage-gen1-account"></a>Apagar a conta Gen1 de Armazenamento de Data Lake

```python
## Delete an existing Data Lake Storage Gen1 account
adlsAcctClient.account.delete(adlsAccountName)
```
    

## <a name="next-steps"></a>Passos seguintes
* [Operações de sistema de ficheiros na Data Lake Storage Gen1 usando Python](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>Ver também

* [referência python (filesystem) azure-datalake -store](/python/api/azure-datalake-store/azure.datalake.store.core)
* [Aplicações Open Source Big Data compatíveis com Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)