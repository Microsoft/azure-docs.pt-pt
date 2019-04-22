---
title: Utilizar a CLI do Azure para começar a utilizar com a geração 1 de armazenamento do Azure Data Lake | Documentos da Microsoft
description: Utilizar a CLI do Azure para criar uma conta de geração 1 de armazenamento do Data Lake e executar operações básicas
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 9431cc7fa12b86371ce6b2325aca8e13d264442e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58880581"
---
# <a name="get-started-with-azure-data-lake-store-using-azure-cli"></a>Introdução ao Azure Data Lake Store com a CLI do Azure

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [CLI do Azure](data-lake-store-get-started-cli-2.0.md)
>
> 

Saiba como utilizar a CLI do Azure para criar uma conta de geração 1 de armazenamento do Azure Data Lake e executar operações básicas, tais como criar pastas, carregar e transferem ficheiros de dados, eliminar a conta, entre outras. Para obter mais informações sobre a geração 1 de armazenamento do Data Lake, veja [descrição geral do Data Lake Storage Gen1](data-lake-store-overview.md).

A CLI do Azure é a experiência da linha de comandos do Azure para a gestão de recursos do Azure. Pode ser utilizada no macOS, no Linux e no Windows. Para obter mais informações, consulte [descrição geral da CLI do Azure](https://docs.microsoft.com/cli/azure). Também pode ver o [referência da CLI do Azure Data Lake Storage Gen1](https://docs.microsoft.com/cli/azure/dls) para obter uma lista completa de comandos e sintaxe.


## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, tem de ter o seguinte:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **CLI do Azure** -veja [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) para obter instruções.

## <a name="authentication"></a>Authentication

Este artigo utiliza uma abordagem de autenticação mais simples com o Data Lake Storage Gen1 em que inicia sessão como utilizador final. O nível de acesso para a Gen1 de armazenamento do Data Lake conta e sistema de ficheiros é, então, governado pelo nível de acesso do utilizador com sessão iniciada. No entanto, existem outras abordagens para autenticar com o Data Lake Storage Gen1, que são **autenticação de utilizador final** ou **autenticação serviço a serviço**. Para obter instruções e mais informações sobre como autenticar, consulte [End-user authentication](data-lake-store-end-user-authenticate-using-active-directory.md) (Autenticação de utilizador final) ou [Service-to-service authentication](data-lake-store-authenticate-using-active-directory.md) (Autenticação de serviço a serviço).


## <a name="log-in-to-your-azure-subscription"></a>Inicie sessão na subscrição do Azure

1. Inicie sessão na subscrição do Azure.

    ```azurecli
    az login
    ```

    Obtenha um código para utilizar no passo seguinte. Utilize um browser para abrir a página https://aka.ms/devicelogin e introduza o código para autenticar. É-lhe pedido para iniciar sessão com as suas credenciais.

2. Depois de iniciar sessão, a janela lista todas as subscrições do Azure que estão associadas à sua conta. Utilize o seguinte comando para utilizar uma subscrição específica.
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Criar uma conta de geração 1 de armazenamento do Azure Data Lake

1. Crie um novo grupo de recursos. No seguinte comando, forneça os valores de parâmetros que pretende utilizar. Se o nome da localização contiver espaços, coloque-o entre aspas. Por exemplo, "EUA Leste 2". 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Crie a conta de geração 1 de armazenamento do Data Lake.
   
    ```azurecli
    az dls account create --account mydatalakestoragegen1 --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-storage-gen1-account"></a>Criar pastas numa conta do Data Lake Storage Gen1

Pode criar pastas na sua conta de geração 1 de armazenamento do Azure Data Lake para gerir e armazenar dados. Utilize o seguinte comando para criar uma pasta denominada **mynewfolder** na raiz da conta do Data Lake Storage Gen1.

```azurecli
az dls fs create --account mydatalakestoragegen1 --path /mynewfolder --folder
```

> [!NOTE]
> O parâmetro `--folder` assegura que o comando cria uma pasta. Se este parâmetro não estiver presente, o comando cria um ficheiro vazio designado mynewfolder na raiz da conta do Data Lake Storage Gen1.
> 
>

## <a name="upload-data-to-a-data-lake-storage-gen1-account"></a>Carregar dados para uma conta de geração 1 de armazenamento do Data Lake

Pode carregar dados para a geração 1 de armazenamento do Data Lake diretamente no nível de raiz ou para uma pasta que criou na conta. Os fragmentos abaixo demonstram como carregar alguns dados de exemplo para a pasta (**mynewfolder**) que criou na secção anterior.

Se estiver à procura de alguns dados de exemplo para carregar, pode obter a pasta **Ambulance Data** a partir do [Repositório de Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Transfira o ficheiro e armazene-o num diretório local no seu computador, como C:\sampledata\.

```azurecli
az dls fs upload --account mydatalakestoragegen1 --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> Para o destino, tem de especificar o caminho completo, incluindo o nome do ficheiro.
> 
>


## <a name="list-files-in-a-data-lake-storage-gen1-account"></a>Listar ficheiros numa conta de geração 1 de armazenamento do Data Lake

Utilize o seguinte comando para listar os ficheiros numa conta de geração 1 de armazenamento do Data Lake.

```azurecli
az dls fs list --account mydatalakestoragegen1 --path /mynewfolder
```

O resultado deve ser semelhante ao seguinte:

    [
        {
            "accessTime": 1491323529542,
            "aclBit": false,
            "blockSize": 268435456,
            "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "length": 1589881,
            "modificationTime": 1491323531638,
            "msExpirationTime": 0,
            "name": "mynewfolder/vehicle1_09142014.csv",
            "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "pathSuffix": "vehicle1_09142014.csv",
            "permission": "770",
            "replication": 1,
            "type": "FILE"
        }
    ]

## <a name="rename-download-and-delete-data-from-a-data-lake-storage-gen1-account"></a>Mudar o nome, transferir e eliminar dados de uma conta de geração 1 de armazenamento do Data Lake 

* **Para mudar o nome de um ficheiro**, utilize o seguinte comando:
  
    ```azurecli
    az dls fs move --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* **Para transferir um ficheiro**, utilize o seguinte comando: Certifique-se de que o caminho de destino especificado já existe.
  
    ```azurecli     
    az dls fs download --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > O comando cria a pasta de destino se não existir.
    > 
    >

* **Para eliminar um ficheiro**, utilize o seguinte comando:
  
    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    Se pretender eliminar a pasta **mynewfolder** e o ficheiro **vehicle1_09142014_copy.csv** em conjunto num comando, utilize o --parâmetro de recurso

    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-storage-gen1-account"></a>Trabalhar com as permissões e ACLs de uma conta de geração 1 de armazenamento do Data Lake

Nesta secção, saiba mais sobre como gerir ACLs e permissões com a CLI do Azure. Para uma discussão detalhada sobre como as ACLs estão implementadas na geração 1 de armazenamento do Azure Data Lake, veja [controlo de acesso no Azure Data Lake Storage Gen1](data-lake-store-access-control.md).

* **Para atualizar o proprietário de um ficheiro/pasta**, utilize o seguinte comando:

    ```azurecli
    az dls fs access set-owner --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* **Para atualizar as permissões de um ficheiro/pasta**, utilize o seguinte comando:

    ```azurecli
    az dls fs access set-permission --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* **Para obter as ACLs de um determinado caminho**, utilize o seguinte comando:

    ```azurecli
    az dls fs access show --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv
    ```

    O resultado deve ser semelhante ao seguinte:

        {
            "entries": [
            "user::rwx",
            "group::rwx",
            "other::---"
          ],
          "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "permission": "770",
          "stickyBit": false
        }

* **Para definir uma entrada de uma ACL**, utilize o seguinte comando:

    ```azurecli
    az dls fs access set-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* **Para remover uma entrada de uma ACL**, utilize o seguinte comando:

    ```azurecli
    az dls fs access remove-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* **Para remover uma ACL predefinida completa**, utilize o seguinte comando:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder --default-acl
    ```

* **Para remover uma ACL não-predefinida completa**, utilize o seguinte comando:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-storage-gen1-account"></a>Eliminar uma conta de geração 1 de armazenamento do Data Lake
Utilize o seguinte comando para eliminar uma conta de geração 1 de armazenamento do Data Lake.

```azurecli
az dls account delete --account mydatalakestoragegen1
```

Quando lhe for pedido, introduza **S** para eliminar a conta.

## <a name="next-steps"></a>Passos Seguintes
* [Utilize a geração 1 de armazenamento do Azure Data Lake para requisitos de grandes volumes de dados](data-lake-store-data-scenarios.md) 
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Utilizar o Azure Data Lake Analytics com o Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Utilizar o Azure HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
