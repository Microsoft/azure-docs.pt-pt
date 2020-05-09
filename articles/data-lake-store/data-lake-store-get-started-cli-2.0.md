---
title: Gerir a conta De armazenamento de lagos Azure Data Gen1 - Azure CLI
description: Utilize o Azure CLI para criar uma conta Gen1 de Armazenamento de Data Lake e realizar operações básicas.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 4e278981ce7647a53d2e80c5b835c8ed666db541
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82688178"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-cli"></a>Inicie-se com o Azure Data Lake Storage Gen1 usando o Azure CLI

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [CLI do Azure](data-lake-store-get-started-cli-2.0.md)
>
> 

Aprenda a utilizar o Azure CLI para criar uma conta Azure Data Lake Storage Gen1 e efetuar operações básicas como criar pastas, carregar e descarregar ficheiros de dados, eliminar a sua conta, etc. Para mais informações sobre data Lake Storage Gen1, consulte [a visão geral do Data Lake Storage Gen1](data-lake-store-overview.md).

A CLI do Azure é a experiência da linha de comandos do Azure para a gestão de recursos do Azure. Pode ser utilizada no macOS, no Linux e no Windows. Para mais informações, consulte a [visão geral do Azure CLI](https://docs.microsoft.com/cli/azure). Também pode ver a referência do [Azure Data Lake Storage Gen1 CLI](https://docs.microsoft.com/cli/azure/dls) para uma lista completa de comandos e sintaxe.


## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, tem de ter o seguinte:

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure CLI** - Consulte [a instalação do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) para obter instruções.

## <a name="authentication"></a>Autenticação

Este artigo utiliza uma abordagem de autenticação mais simples com data Lake Storage Gen1 onde faz login como utilizador final. O nível de acesso à conta e sistema de ficheiros Data Lake Storage Gen1 é então regido pelo nível de acesso do registado no utilizador. No entanto, existem outras abordagens também para autenticar com data Lake Storage Gen1, que são **autenticação de utilizador final** ou **autenticação serviço-a-serviço**. Para obter instruções e obter mais informações sobre como autenticar, veja [End-user authentication](data-lake-store-end-user-authenticate-using-active-directory.md) (Autenticação de utilizador final) ou [Service-to-service authentication](data-lake-store-authenticate-using-active-directory.md) (Autenticação de serviço a serviço).


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

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Criar uma conta de Armazenamento de Lago de Dados Azure Gen1

1. Crie um novo grupo de recursos. No seguinte comando, forneça os valores de parâmetros que pretende utilizar. Se o nome da localização contiver espaços, coloque-o entre aspas. Por exemplo, "E.U.A. Leste 2". 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Crie a conta Data Lake Storage Gen1.
   
    ```azurecli
    az dls account create --account mydatalakestoragegen1 --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-storage-gen1-account"></a>Criar pastas numa conta Gen1 de Armazenamento de Data Lake

Pode criar pastas sob a sua conta Azure Data Lake Storage Gen1 para gerir e armazenar dados. Utilize o seguinte comando para criar uma pasta chamada **mynewfolder** na raiz da conta Data Lake Storage Gen1.

```azurecli
az dls fs create --account mydatalakestoragegen1 --path /mynewfolder --folder
```

> [!NOTE]
> O parâmetro `--folder` assegura que o comando cria uma pasta. Se este parâmetro não estiver presente, o comando cria um ficheiro vazio chamado mynewfolder na raiz da conta Data Lake Storage Gen1.
> 
>

## <a name="upload-data-to-a-data-lake-storage-gen1-account"></a>Faça upload de dados para uma conta Gen1 de Armazenamento de Data Lake

Pode fazer o upload de dados para data Lake Storage Gen1 diretamente ao nível raiz ou a uma pasta que criou dentro da conta. Os fragmentos abaixo demonstram como carregar alguns dados de exemplo para a pasta (**mynewfolder**) que criou na secção anterior.

Se estiver à procura de alguns dados de exemplo para carregar, pode obter a pasta **Ambulance Data** a partir do [Repositório de Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Transfira o ficheiro e armazene-o num diretório local no seu computador, como C:\sampledata\.

```azurecli
az dls fs upload --account mydatalakestoragegen1 --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> Para o destino, tem de especificar o caminho completo, incluindo o nome do ficheiro.
> 
>


## <a name="list-files-in-a-data-lake-storage-gen1-account"></a>Lista rumine ficheiros numa conta Gen1 de Armazenamento de Data Lake

Utilize o seguinte comando para listar os ficheiros numa conta Gen1 de Armazenamento de Data Lake.

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

## <a name="rename-download-and-delete-data-from-a-data-lake-storage-gen1-account"></a>Renomear, transferir e eliminar dados de uma conta Gen1 de Armazenamento de Data Lake 

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

## <a name="work-with-permissions-and-acls-for-a-data-lake-storage-gen1-account"></a>Trabalhe com permissões e ACLs para uma conta Gen1 de Armazenamento de Data Lake

Nesta secção aprende-se a gerir ACLs e permissões utilizando o Azure CLI. Para uma discussão detalhada sobre como os ACLs são implementados em Azure Data Lake Storage Gen1, consulte o controlo de [acesso em Azure Data Lake Storage Gen1](data-lake-store-access-control.md).

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
    
## <a name="delete-a-data-lake-storage-gen1-account"></a>Eliminar uma conta Gen1 de Armazenamento de Data Lake
Utilize o seguinte comando para eliminar uma conta Gen1 de Armazenamento de Data Lake.

```azurecli
az dls account delete --account mydatalakestoragegen1
```

Quando lhe for pedido, introduza **S** para eliminar a conta.

## <a name="next-steps"></a>Passos seguintes
* [Use o Azure Data Lake Storage Gen1 para grandes necessidades de dados](data-lake-store-data-scenarios.md) 
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Use Azure Data Lake Analytics com Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
