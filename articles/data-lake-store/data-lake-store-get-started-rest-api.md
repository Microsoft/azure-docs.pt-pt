---
title: Gerir uma conta Azure Data Lake Storage Gen1 com REST
description: Utilize a API do Rest da WebHDFS para executar operações de gestão de conta numa conta da Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: fc3f3fb0b6bb67239d6c1952d3e128076ce45aaf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85857190"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Operações de gestão de conta na Azure Data Lake Storage Gen1 utilizando a API REST
> [!div class="op_single_selector"]
> * [SDK do .NET](data-lake-store-get-started-net-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Neste artigo, você aprende a executar operações de gestão de conta na Azure Data Lake Storage Gen1 usando a API REST. As operações de gestão de conta incluem a criação de uma conta Desatóma de Armazenamento de Dados Gen1, eliminando uma conta Gen1 de armazenamento de dados, etc. Para obter instruções sobre como executar operações de sistema de ficheiros na Data Lake Storage Gen1 utilizando a API REST, consulte [as operações do Sistema de Ficheiros na Data Lake Storage Gen1 utilizando a API REST](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **[cURL](https://curl.haxx.se/)**. Este artigo utiliza o cURL para demonstrar como fazer chamadas de API REST contra uma conta Gen1 de armazenamento de data lake.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Como posso autenticar com o Azure Active Directory?
Pode utilizar duas abordagens para autenticar com o Azure Active Directory.

* Para autenticação do utilizador final para a sua aplicação (interativa), consulte [a autenticação do utilizador final com a Data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Para autenticação de serviço-a-serviço para a sua aplicação (não interativa), consulte [a autenticação de serviço-a-serviço com a Data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-storage-gen1-account"></a>Criar uma conta do Data Lake Storage Gen1
Esta operação baseia-se na chamada da API REST definida [aqui](https://docs.microsoft.com/rest/api/datalakestore/accounts/create).

Utilize o seguinte comando cURL. Substitua **\<yourstoragegen1name>** o nome da Gen1 de armazenamento de dados.

```console
curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview -d@"C:\temp\input.json"
```

No comando acima, substitua \<`REDACTED`\> pelo token de autorização obtido anteriormente. O payload do pedido para este comando está contido no ficheiro **input.json** fornecido para o parâmetro `-d` acima. O conteúdo do ficheiro input.json assemelha-se ao seguinte fragmento:

```json
{
"location": "eastus2",
"tags": {
    "department": "finance"
    },
"properties": {}
}
```

## <a name="delete-a-data-lake-storage-gen1-account"></a>Excluir uma conta Gen1 de armazenamento de data lake
Esta operação baseia-se na chamada da API REST definida [aqui](https://docs.microsoft.com/rest/api/datalakestore/accounts/delete).

Utilize o seguinte comando cURL para eliminar uma conta Gen1 de armazenamento de data lake. Substitua **\<yourstoragegen1name>** o nome da sua conta Gen1 de armazenamento de dados.

```console
curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview
```

Deve ver um resultado como o seguinte fragmento:

```output
HTTP/1.1 200 OK
...
...
```

## <a name="next-steps"></a>Passos seguintes
* [Operações de sistema de ficheiros na Data Lake Storage Gen1 utilizando a API REST](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>Consulte também
* [Referência Azure Data Lake Storage Gen1 REST API Referência](https://docs.microsoft.com/rest/api/datalakestore/)
* [Aplicações Open Source Big Data compatíveis com Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)

