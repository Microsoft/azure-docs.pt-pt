---
title: 'REST API: Operações de gestão de conta no Azure Data Lake Storage Gen1 [ Microsoft Docs'
description: Utilize o Azure Data Lake Storage Gen1 e webHDFS REST API para realizar operações de gestão de conta na conta Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 97fe33309f36cd7545f8c9d6c2d34671641caa1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60877112"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Operações de gestão de conta no Azure Data Lake Storage Gen1 utilizando a REST API
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Pitão](data-lake-store-get-started-python.md)
>
>

Neste artigo, aprende-se a realizar operações de gestão de contas no Azure Data Lake Storage Gen1 utilizando a API REST. As operações de gestão de conta incluem a criação de uma conta Data Lake Storage Gen1, apagando uma conta Gen1 de Armazenamento de Data Lake, etc. Para obter instruções sobre como executar operações do sistema de ficheiros no Data Lake Storage Gen1 utilizando a API REST, consulte [as operações do Sistema de Ficheiros no Data Lake Storage Gen1 utilizando a REST API](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **[cURL](https://curl.haxx.se/)**. Este artigo usa cURL para demonstrar como fazer chamadas REST API contra uma conta Gen1 de Armazenamento de Data Lake.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Como posso autenticar com o Azure Active Directory?
Pode utilizar duas abordagens para autenticar com o Azure Active Directory.

* Para autenticação de utilizador final para a sua aplicação (interativa), consulte [a autenticação do utilizador final com data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Para autenticação de serviço a serviço para a sua aplicação (não interativa), consulte [a autenticação serviço-a-serviço com data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-storage-gen1-account"></a>Criar uma conta do Data Lake Storage Gen1
Esta operação baseia-se na chamada da API REST definida [aqui](https://docs.microsoft.com/rest/api/datalakestore/accounts/create).

Utilize o seguinte comando cURL. Substitua o seu>de ** \<storagegen1** com o nome Data Lake Storage Gen1.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

No comando acima, substitua \<`REDACTED`\> pelo token de autorização obtido anteriormente. O payload do pedido para este comando está contido no ficheiro **input.json** fornecido para o parâmetro `-d` acima. O conteúdo do ficheiro input.json assemelha-se ao seguinte fragmento:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-storage-gen1-account"></a>Eliminar uma conta Gen1 de Armazenamento de Data Lake
Esta operação baseia-se na chamada da API REST definida [aqui](https://docs.microsoft.com/rest/api/datalakestore/accounts/delete).

Utilize o seguinte comando cURL para eliminar uma conta Gen1 de Armazenamento de Data Lake. Substitua o ** \<seu>storagegen1** com o nome da conta Data Lake Storage Gen1.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview

Deve ver um resultado como o seguinte fragmento:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>Passos seguintes
* [Operações do sistema de ficheiros no Data Lake Storage Gen1 utilizando](data-lake-store-data-operations-rest-api.md)a REST API .

## <a name="see-also"></a>Consulte também
* [Referência a API de armazenamento de lagos de dados azure](https://docs.microsoft.com/rest/api/datalakestore/)
* [Aplicações open Source Big Data compatíveis com Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)

