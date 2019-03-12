---
title: 'Python: Conta de gestão de operações em Gen1 de armazenamento do Azure Data Lake | Documentos da Microsoft'
description: Saiba como utilizar o Python SDK para trabalhar com operações de gestão de conta de geração 1 de armazenamento do Azure Data Lake.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: cc384e9f392aa88342c5b8bd97963bed6d7ee814
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57533329"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>Operações de gestão de conta na geração 1 de armazenamento do Azure Data Lake com o Python
> [!div class="op_single_selector"]
> * [SDK do .NET](data-lake-store-get-started-net-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Saiba como utilizar o SDK de Python para a geração 1 de armazenamento do Azure Data Lake para efetuar conta básica, operações de gestão, tais como criar uma conta do Data Lake Storage Gen1, contas de lista a geração de 1 de armazenamento do Data Lake, etc. Para obter instruções sobre como efetuar operações do sistema de ficheiros na geração 1 de armazenamento do Data Lake com o Python, veja [operações do sistema de ficheiros no Data Lake Storage Gen1 com o Python](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Python**. Pode transferir o Python [aqui](https://www.python.org/downloads/). Este artigo utiliza a versão Python 3.6.2.

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Um grupo de recursos do Azure**. Para obter instruções, veja [Criar um grupo de recursos do Azure](../azure-resource-manager/manage-resource-groups-portal.md).

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

1. No IDE à sua escolha, crie uma aplicação Python nova, como, por exemplo, **mysample.py**.

2. Adicione o fragmento de código seguinte para importar os módulos necessários

    ```
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

## <a name="authentication"></a>Authentication

Nesta secção, vamos falar sobre as diferentes formas de autenticar com o Azure AD. As opções disponíveis são:

* Para a autenticação de utilizador final para a sua aplicação, veja [autenticação de utilizador final com geração 1 de armazenamento do Data Lake com o Python](data-lake-store-end-user-authenticate-python.md).
* Para a autenticação de serviço a serviço para a sua aplicação, veja [autenticação de serviço para serviço com geração 1 de armazenamento do Data Lake com o Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-client-and-data-lake-storage-gen1-account"></a>Criar cliente e uma conta de geração 1 de armazenamento do Data Lake

O fragmento seguinte cria primeiro o cliente de conta de geração 1 de armazenamento do Data Lake. Ele usa o objeto de cliente para criar uma conta de geração 1 de armazenamento do Data Lake. Por fim, o fragmento cria um objeto de cliente do sistema de ficheiros.

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

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>Listar as contas de geração 1 de armazenamento do Data Lake

    ## List the existing Data Lake Storage Gen1 accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-storage-gen1-account"></a>Eliminar a conta de geração 1 de armazenamento do Data Lake

    ## Delete an existing Data Lake Storage Gen1 account
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>Passos Seguintes
* [Operações do sistema de ficheiros no Data Lake Storage Gen1 com o Python](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>Consulte também

* [referência de Python (sistema de ficheiros) do Azure-datalake-store](https://azure-datalake-store.readthedocs.io/en/latest)
* [Abrir aplicações de macrodados de código compatíveis com a geração 1 de armazenamento do Azure Data Lake](data-lake-store-compatible-oss-other-applications.md)
