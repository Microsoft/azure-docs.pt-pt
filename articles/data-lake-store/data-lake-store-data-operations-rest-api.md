---
title: 'REST API: Operações de sistema de ficheiros na Azure Data Lake Storage Gen1 Microsoft Docs'
description: Utilize As APIs de REST da WebHDFS para realizar operações de sistema de ficheiros na Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 51e0fb2ffa7b573ecfeda163d9ad99597ff735a2
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92109209"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Operações de sistema de ficheiros em Azure Data Lake Storage Gen1 usando REST API
> [!div class="op_single_selector"]
> * [SDK do .NET](data-lake-store-data-operations-net-sdk.md)
> * [SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Neste artigo, você aprende a usar WebHDFS REST APIs e Data Lake Storage Gen1 REST APIs para realizar operações de sistema de ficheiros na Azure Data Lake Storage Gen1. Para obter instruções sobre como executar operações de gestão de conta na Data Lake Storage Gen1 utilizando a API REST, consulte [as operações de gestão de conta na Data Lake Storage Gen1 utilizando a API REST](data-lake-store-get-started-rest-api.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta Azure Data Lake Storage Gen1**. Siga as instruções da [Azure Data Lake Storage Gen1 utilizando o portal Azure](data-lake-store-get-started-portal.md).

* **[cURL](https://curl.haxx.se/)**. Este artigo utiliza o cURL para demonstrar como fazer chamadas de API REST contra uma conta Gen1 de armazenamento de data lake.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Como posso autenticar com o Azure Active Directory?
Pode utilizar duas abordagens para autenticar com o Azure Active Directory.

* Para autenticação do utilizador final para a sua aplicação (interativa), consulte [a autenticação do utilizador final com a Data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Para autenticação de serviço-a-serviço para a sua aplicação (não interativa), consulte [a autenticação de serviço-a-serviço com a Data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-folders"></a>Criar pastas
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Utilize o seguinte comando cURL. Substitua **\<yourstorename>** o nome da sua conta Gen1 de armazenamento de dados.

```console
curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'
```

No comando anterior, substitua \<`REDACTED`\> pelo token de autorização obtido anteriormente. Este comando cria um diretório chamado **mytempdir** sob a pasta raiz da sua conta Desepoimento Dese de Armazenamento Gen1.

Se a operação for concluída com êxito, deverá ver uma resposta como o seguinte fragmento:

```output
{"boolean":true}
```

## <a name="list-folders"></a>Listar pastas
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Utilize o seguinte comando cURL. Substitua **\<yourstorename>** o nome da sua conta Gen1 de armazenamento de dados.

```console
curl -i -X GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS'
```

No comando anterior, substitua \<`REDACTED`\> pelo token de autorização obtido anteriormente.

Se a operação for concluída com êxito, deverá ver uma resposta como o seguinte fragmento:

```output
{
"FileStatuses": {
    "FileStatus": [{
        "length": 0,
        "pathSuffix": "mytempdir",
        "type": "DIRECTORY",
        "blockSize": 268435456,
        "accessTime": 1458324719512,
        "modificationTime": 1458324719512,
        "replication": 0,
        "permission": "777",
        "owner": "<GUID>",
        "group": "<GUID>"
    }]
}
}
```

## <a name="upload-data"></a>Carregar dados
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Utilize o seguinte comando cURL. Substitua **\<yourstorename>** o nome da sua conta Gen1 de armazenamento de dados.

```console
curl -i -X PUT -L -T 'C:\temp\list.txt' -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE'
```

No parâmetro da sintaxe anterior **-T** encontra-se a localização do ficheiro que está a carregar.

O resultado é semelhante ao seguinte fragmento:
   
```output
HTTP/1.1 307 Temporary Redirect
...
Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE&write=true
...
Content-Length: 0

HTTP/1.1 100 Continue

HTTP/1.1 201 Created
...
```

## <a name="read-data"></a>Ler dados
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

Ler dados de uma conta da Data Lake Storage Gen1 é um processo em duas etapas.

* Primeiro, submeta um pedido GET relativamente ao ponto final `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Esta chamada devolve uma localização para submeter o pedido GET seguinte.
* Em seguida, submeta o pedido GET relativamente ao ponto final `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Esta chamada apresenta o conteúdo do ficheiro.

No entanto, uma vez que não existe nenhuma diferença nos parâmetros de entrada entre o primeiro e o segundo passo, pode utilizar o parâmetro `-L` para submeter o primeiro pedido. A opção `-L` combina essencialmente dois pedidos num único e faz com que o cURL refaça o pedido na nova localização. Por fim, é apresentado o resultado de todas as chamadas do pedido, como mostrado no fragmento seguinte. Substitua **\<yourstorename>** o nome da sua conta Gen1 de armazenamento de dados.

```console
curl -i -L GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN'
```

Deverá ver um resultado semelhante ao seguinte fragmento:

```output
HTTP/1.1 307 Temporary Redirect
...
Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
...

HTTP/1.1 200 OK
...

Hello, Data Lake Store user!
```

## <a name="rename-a-file"></a>Mudar o nome de um ficheiro
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Utilize o seguinte comando cURL para mudar o nome de um ficheiro. Substitua **\<yourstorename>** o nome da sua conta Gen1 de armazenamento de dados.

```console
curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'
```

Deverá ver um resultado semelhante ao seguinte fragmento:

```output
HTTP/1.1 200 OK
...

{"boolean":true}
```

## <a name="delete-a-file"></a>Eliminar um ficheiro
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Utilize o seguinte comando cURL para eliminar um ficheiro. Substitua **\<yourstorename>** o nome da sua conta Gen1 de armazenamento de dados.

```console
curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'
```

Deve ver um resultado como o seguinte:

```output
HTTP/1.1 200 OK
...

{"boolean":true}
```

## <a name="next-steps"></a>Passos seguintes
* [Operações de gestão de conta na Data Lake Storage Gen1 utilizando a API REST](data-lake-store-get-started-rest-api.md).

## <a name="see-also"></a>Ver também
* [Referência Azure Data Lake Storage Gen1 REST API Referência](/rest/api/datalakestore/)
* [Aplicações Open Source Big Data compatíveis com Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)