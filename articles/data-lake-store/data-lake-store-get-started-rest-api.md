---
title: 'REST API: Conta de gestão de operações em Gen1 de armazenamento do Azure Data Lake | Documentos da Microsoft'
description: Utilize Gen1 de armazenamento do Azure Data Lake e WebHDFS REST API para efetuar operações de gestão de contas na conta de geração 1 de armazenamento do Data Lake
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: b2c4d38462cf617d8417b269d47a8c1bb0c266c3
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530131"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Operações de gestão de conta na geração 1 de armazenamento do Azure Data Lake com a REST API
> [!div class="op_single_selector"]
> * [SDK do .NET](data-lake-store-get-started-net-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Neste artigo, aprenderá a efetuar operações de gestão de conta na geração 1 de armazenamento do Azure Data Lake com a API REST. Operações de gestão de contas incluem criar uma conta de geração 1 de armazenamento do Data Lake, eliminar uma conta do Data Lake Storage Gen1, etc. Para obter instruções sobre como efetuar operações do sistema de ficheiros no Data Lake Storage Gen1 com a REST API, consulte [operações do sistema de ficheiros no Data Lake Storage Gen1 com a REST API](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **[cURL](https://curl.haxx.se/)**. Este artigo utiliza o cURL para demonstrar como fazer chamadas de REST API em relação a uma conta de geração 1 de armazenamento do Data Lake.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Como posso autenticar com o Azure Active Directory?
Pode utilizar duas abordagens para autenticar com o Azure Active Directory.

* Para a autenticação de utilizador final para a sua aplicação (interativa), veja [autenticação de utilizador final com geração 1 de armazenamento do Data Lake com o .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Para a autenticação de serviço a serviço para a sua aplicação (não interativa), veja [autenticação de serviço para serviço com geração 1 de armazenamento do Data Lake com o .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-storage-gen1-account"></a>Criar uma conta de geração 1 de armazenamento do Data Lake
Esta operação baseia-se na chamada da API REST definida [aqui](https://docs.microsoft.com/rest/api/datalakestore/accounts/create).

Utilize o seguinte comando cURL. Substitua  **\<yourstoragegen1name >** com o seu nome de geração 1 de armazenamento do Data Lake.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

No comando acima, substitua \<`REDACTED`\> pelo token de autorização obtido anteriormente. O payload do pedido para este comando está contido no ficheiro **input.json** fornecido para o parâmetro `-d` acima. O conteúdo do ficheiro input.json assemelha-se ao seguinte fragmento:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-storage-gen1-account"></a>Eliminar uma conta de geração 1 de armazenamento do Data Lake
Esta operação baseia-se na chamada da API REST definida [aqui](https://docs.microsoft.com/rest/api/datalakestore/accounts/delete).

Utilize o seguinte comando cURL para eliminar uma conta de geração 1 de armazenamento do Data Lake. Substitua  **\<yourstoragegen1name >** com o nome da sua conta de geração 1 de armazenamento do Data Lake.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview

Deve ver um resultado como o seguinte fragmento:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>Passos Seguintes
* [Operações do sistema de ficheiros no Data Lake Storage Gen1 com a REST API](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>Consulte também
* [Referência da API REST do Azure Data Lake Storage Gen1](https://docs.microsoft.com/rest/api/datalakestore/)
* [Abrir aplicações de macrodados de código compatíveis com a geração 1 de armazenamento do Azure Data Lake](data-lake-store-compatible-oss-other-applications.md)

