---
title: 'Python: operações de gerenciamento de conta no Azure Data Lake Storage Gen1 | Microsoft Docs'
description: Saiba como usar o SDK do Python para trabalhar com Azure Data Lake Storage Gen1 operações de gerenciamento de conta.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: fad832ba7b16e8dbb37043d971368062fcbd842f
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75966048"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>Operações de gerenciamento de conta em Azure Data Lake Storage Gen1 usando Python
> [!div class="op_single_selector"]
> * [SDK do .NET](data-lake-store-get-started-net-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Saiba como usar o SDK do Python para Azure Data Lake Storage Gen1 executar operações básicas de gerenciamento de conta, como criar uma conta de Data Lake Storage Gen1, listar as contas de Data Lake Storage Gen1, etc. Para obter instruções sobre como executar operações de sistema de arquivos em Data Lake Storage Gen1 usando o Python, consulte [operações de sistema de arquivos em data Lake Storage Gen1 usando Python](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Python**. Pode transferir o Python [aqui](https://www.python.org/downloads/). Este artigo utiliza a versão Python 3.6.2.

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Um grupo de recursos do Azure**. Para obter instruções, veja [Criar um grupo de recursos do Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar com Data Lake Storage Gen1 usando Python, você precisa instalar três módulos.

* O módulo `azure-mgmt-resource`, que inclui módulos do Azure para o Active Directory, etc.
* O módulo `azure-mgmt-datalake-store`, que inclui as operações de gerenciamento de conta de Azure Data Lake Storage Gen1. Para obter mais informações sobre esse módulo, consulte [Azure data Lake Storage Gen1 referência do módulo de gerenciamento](/python/api/azure-mgmt-datalake-store/).
* O módulo `azure-datalake-store`, que inclui as operações de Azure Data Lake Storage Gen1 FileSystem. Para obter mais informações sobre esse módulo, consulte [referência do módulo do sistema de arquivos Azure-datalake-Store](https://azure-datalake-store.readthedocs.io/en/latest/).

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

## <a name="authentication"></a>Autenticação

Nesta secção, vamos falar sobre as diferentes formas de autenticar com o Azure AD. As opções disponíveis são:

* Para autenticação do usuário final para seu aplicativo, consulte [autenticação do usuário final com data Lake Storage Gen1 usando Python](data-lake-store-end-user-authenticate-python.md).
* Para autenticação serviço a serviço para seu aplicativo, consulte [Autenticação serviço a serviço com data Lake Storage Gen1 usando Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-client-and-data-lake-storage-gen1-account"></a>Criar conta de cliente e Data Lake Storage Gen1

O trecho a seguir cria primeiro o cliente da conta Data Lake Storage Gen1. Ele usa o objeto de cliente para criar uma conta de Data Lake Storage Gen1. Por fim, o fragmento cria um objeto de cliente do sistema de ficheiros.

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

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>Listar as contas de Data Lake Storage Gen1

    ## List the existing Data Lake Storage Gen1 accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-storage-gen1-account"></a>Excluir a conta de Data Lake Storage Gen1

    ## Delete an existing Data Lake Storage Gen1 account
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>Passos seguintes
* [Operações de sistema de arquivos em data Lake Storage Gen1 usando Python](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>Ver também

* [referência do Python (Filesystem) do Azure-datalake-Store](https://azure-datalake-store.readthedocs.io/en/latest)
* [Aplicativos de Big data de software livre compatíveis com o Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)
