---
title: 'REST API: Operações de sistema de ficheiros em Gen1 de armazenamento do Azure Data Lake | Documentos da Microsoft'
description: Utilizar WebHDFS REST APIs para executar operações de sistema de ficheiros no Gen1 de armazenamento do Azure Data Lake
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 4c61d68a948cbc5638dab30713871d1aaf822f03
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57529259"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Operações do sistema de ficheiros em Gen1 de armazenamento do Azure Data Lake com a REST API
> [!div class="op_single_selector"]
> * [SDK do .NET](data-lake-store-data-operations-net-sdk.md)
> * [SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Neste artigo, irá aprender a utilizar WebHDFS REST APIs e APIs de REST do Data Lake Storage Gen1 para executar operações de sistema de ficheiros na geração 1 de armazenamento do Azure Data Lake. Para obter instruções sobre como efetuar operações de gestão contas no Data Lake Storage Gen1 com a REST API, consulte [operações de gestão na geração 1 de armazenamento do Data Lake com a REST API de contas](data-lake-store-get-started-rest-api.md).

## <a name="prerequisites"></a>Pré-requisitos
* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta do Azure de geração 1 de armazenamento do Data Lake**. Siga as instruções em [introdução à geração 1 de armazenamento do Azure Data Lake com o portal do Azure](data-lake-store-get-started-portal.md).

* **[cURL](https://curl.haxx.se/)**. Este artigo utiliza o cURL para demonstrar como fazer chamadas de REST API em relação a uma conta de geração 1 de armazenamento do Data Lake.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Como posso autenticar com o Azure Active Directory?
Pode utilizar duas abordagens para autenticar com o Azure Active Directory.

* Para a autenticação de utilizador final para a sua aplicação (interativa), veja [autenticação de utilizador final com geração 1 de armazenamento do Data Lake com o .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Para a autenticação de serviço a serviço para a sua aplicação (não interativa), veja [autenticação de serviço para serviço com geração 1 de armazenamento do Data Lake com o .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-folders"></a>Criar pastas
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Utilize o seguinte comando cURL. Substitua  **\<nomedoarquivo >** com o nome da sua conta de geração 1 de armazenamento do Data Lake.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'

No comando anterior, substitua \<`REDACTED`\> pelo token de autorização obtido anteriormente. Este comando cria um diretório chamado **mytempdir** na pasta raiz da sua conta de geração 1 de armazenamento do Data Lake.

Se a operação for concluída com êxito, deverá ver uma resposta como o seguinte fragmento:

    {"boolean":true}

## <a name="list-folders"></a>Listar pastas
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Utilize o seguinte comando cURL. Substitua  **\<nomedoarquivo >** com o nome da sua conta de geração 1 de armazenamento do Data Lake.

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

Utilize o seguinte comando cURL. Substitua  **\<nomedoarquivo >** com o nome da sua conta de geração 1 de armazenamento do Data Lake.

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

Leitura de dados de uma geração de 1 de armazenamento do Data Lake conta é um processo de dois passos.

* Primeiro, submeta um pedido GET relativamente ao ponto final `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Esta chamada devolve uma localização para submeter o pedido GET seguinte.
* Em seguida, submeta o pedido GET relativamente ao ponto final `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Esta chamada apresenta o conteúdo do ficheiro.

No entanto, uma vez que não existe nenhuma diferença nos parâmetros de entrada entre o primeiro e o segundo passo, pode utilizar o parâmetro `-L` para submeter o primeiro pedido. A opção `-L` combina essencialmente dois pedidos num único e faz com que o cURL refaça o pedido na nova localização. Por fim, é apresentado o resultado de todas as chamadas do pedido, como mostrado no fragmento seguinte. Substitua  **\<nomedoarquivo >** com o nome da sua conta de geração 1 de armazenamento do Data Lake.

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

Utilize o seguinte comando cURL para mudar o nome de um ficheiro. Substitua  **\<nomedoarquivo >** com o nome da sua conta de geração 1 de armazenamento do Data Lake.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'

Deverá ver um resultado semelhante ao seguinte fragmento:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-file"></a>Eliminar um ficheiro
Esta operação baseia-se na chamada da API REST WebHDFS definida [aqui](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Utilize o seguinte comando cURL para eliminar um ficheiro. Substitua  **\<nomedoarquivo >** com o nome da sua conta de geração 1 de armazenamento do Data Lake.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'

Deve ver um resultado como o seguinte:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="next-steps"></a>Passos Seguintes
* [Operações de gestão na geração 1 de armazenamento do Data Lake com a REST API de contas](data-lake-store-get-started-rest-api.md).

## <a name="see-also"></a>Consulte também
* [Referência da API REST do Azure Data Lake Storage Gen1](https://docs.microsoft.com/rest/api/datalakestore/)
* [Abrir aplicações de macrodados de código compatíveis com a geração 1 de armazenamento do Azure Data Lake](data-lake-store-compatible-oss-other-applications.md)

