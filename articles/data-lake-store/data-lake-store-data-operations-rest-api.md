---
title: 'REST API: Operações do sistema de ficheiros no Azure Data Lake Storage Gen1 Microsoft Docs'
description: Utilize APIs DE REPOUSO WebHDFS para realizar operações de sistema de ficheiros em Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 351c92f1e1a698893f61004d523ba79ebca253e8
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "60878788"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Operações do sistema de ficheiros no Azure Data Lake Storage Gen1 utilizando a REST API
> [!div class="op_single_selector"]
> * [SDK .NET](data-lake-store-data-operations-net-sdk.md)
> * [SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Neste artigo, aprende-se a utilizar as APIs do WebHDFS REST APIs e data Lake Storage Gen1 REST APIs para realizar operações de sistema de ficheiros no Azure Data Lake Storage Gen1. Para obter instruções sobre como realizar operações de gestão de conta em Data Lake Storage Gen1 utilizando a REST API, consulte as operações de gestão da [conta no Data Lake Storage Gen1 utilizando](data-lake-store-get-started-rest-api.md)a REST API .

## <a name="prerequisites"></a>Pré-requisitos
* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta Azure Data Lake Storage Gen1.** Siga as instruções no [Get started com Azure Data Lake Storage Gen1 utilizando o portal Azure](data-lake-store-get-started-portal.md).

* **[cURL](https://curl.haxx.se/)**. Este artigo usa cURL para demonstrar como fazer chamadas REST API contra uma conta Gen1 de Armazenamento de Data Lake.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Como posso autenticar com o Azure Active Directory?
Pode utilizar duas abordagens para autenticar com o Azure Active Directory.

* Para autenticação de utilizador final para a sua aplicação (interativa), consulte [a autenticação do utilizador final com data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Para autenticação de serviço a serviço para a sua aplicação (não interativa), consulte [a autenticação serviço-a-serviço com data Lake Storage Gen1 utilizando .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-folders"></a>Criar pastas
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Utilize o seguinte comando cURL. Substitua o ** \<seu nome de loja>** pelo nome da conta Data Lake Storage Gen1.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'

No comando anterior, substitua \<`REDACTED`\> pelo token de autorização obtido anteriormente. Este comando cria um diretório chamado **mytempdir** sob a pasta raiz da sua conta Data Lake Storage Gen1.

Se a operação for concluída com êxito, deverá ver uma resposta como o seguinte fragmento:

    {"boolean":true}

## <a name="list-folders"></a>Listar pastas
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Utilize o seguinte comando cURL. Substitua o ** \<seu nome de loja>** pelo nome da conta Data Lake Storage Gen1.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS'

No comando anterior, substitua \<`REDACTED`\> pelo token de autorização obtido anteriormente.

Se a operação for concluída com êxito, deverá ver uma resposta como o seguinte fragmento:

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

## <a name="upload-data"></a>Carregar dados
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Utilize o seguinte comando cURL. Substitua o ** \<seu nome de loja>** pelo nome da conta Data Lake Storage Gen1.

    curl -i -X PUT -L -T 'C:\temp\list.txt' -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE'

No parâmetro da sintaxe anterior **-T** encontra-se a localização do ficheiro que está a carregar.

O resultado é semelhante ao seguinte fragmento:
   
    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE&write=true
    ...
    Content-Length: 0

    HTTP/1.1 100 Continue

    HTTP/1.1 201 Created
    ...

## <a name="read-data"></a>Ler dados
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

Ler dados de uma conta gen1 de armazenamento de data lake é um processo em duas etapas.

* Primeiro, submeta um pedido GET relativamente ao ponto final `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Esta chamada devolve uma localização para submeter o pedido GET seguinte.
* Em seguida, submeta o pedido GET relativamente ao ponto final `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Esta chamada apresenta o conteúdo do ficheiro.

No entanto, uma vez que não existe nenhuma diferença nos parâmetros de entrada entre o primeiro e o segundo passo, pode utilizar o parâmetro `-L` para submeter o primeiro pedido. A opção `-L` combina essencialmente dois pedidos num único e faz com que o cURL refaça o pedido na nova localização. Por fim, é apresentado o resultado de todas as chamadas do pedido, como mostrado no fragmento seguinte. Substitua o ** \<seu nome de loja>** pelo nome da conta Data Lake Storage Gen1.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN'

Deverá ver um resultado semelhante ao seguinte fragmento:

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...

    HTTP/1.1 200 OK
    ...

    Hello, Data Lake Store user!

## <a name="rename-a-file"></a>Mudar o nome de um ficheiro
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Utilize o seguinte comando cURL para mudar o nome de um ficheiro. Substitua o ** \<seu nome de loja>** pelo nome da conta Data Lake Storage Gen1.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'

Deverá ver um resultado semelhante ao seguinte fragmento:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-file"></a>Eliminar um ficheiro
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Utilize o seguinte comando cURL para eliminar um ficheiro. Substitua o ** \<seu nome de loja>** pelo nome da conta Data Lake Storage Gen1.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'

Deve ver um resultado como o seguinte:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="next-steps"></a>Passos seguintes
* [Operações de gestão de conta em Data Lake Storage Gen1 utilizando rest API](data-lake-store-get-started-rest-api.md).

## <a name="see-also"></a>Consulte também
* [Referência a API de armazenamento de lagos de dados azure](https://docs.microsoft.com/rest/api/datalakestore/)
* [Aplicações open Source Big Data compatíveis com Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)

