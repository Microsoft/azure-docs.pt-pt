---
title: Gerir a conta Azure Data Lake Storage Gen1 - Azure CLI
description: Utilize o CLI Azure para criar uma conta Gen1 de armazenamento de dados e realizar operações básicas.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: de98e25cf5703a43282e551a0eda20d7767c6ce8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103633"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-cli"></a>Começar com a Azure Data Lake Storage Gen1 usando o Azure CLI

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [CLI do Azure](data-lake-store-get-started-cli-2.0.md)
>
> 

Saiba como utilizar o CLI do Azure para criar uma conta Azure Data Lake Storage Gen1 e realizar operações básicas como criar pastas, carregar e descarregar ficheiros de dados, eliminar a sua conta, etc. Para obter mais informações sobre data lake storage gen1, consulte [a visão geral da Data Lake Storage Gen1](data-lake-store-overview.md).

A CLI do Azure é a experiência da linha de comandos do Azure para a gestão de recursos do Azure. Pode ser utilizada no macOS, no Linux e no Windows. Para mais informações, consulte [a visão geral do Azure CLI](/cli/azure). Também pode olhar para a [referência Azure Data Lake Storage Gen1 CLI](/cli/azure/dls) para uma lista completa de comandos e sintaxe.


## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, tem de ter o seguinte:

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure CLI** - Consulte [a Instalação CLI para](/cli/azure/install-azure-cli) obter instruções.

## <a name="authentication"></a>Autenticação

Este artigo utiliza uma abordagem de autenticação mais simples com a Data Lake Storage Gen1, onde inicia sessão como utilizador final. O nível de acesso à conta e sistema de ficheiros Data Lake Storage Gen1 é então regido pelo nível de acesso do utilizador registado. No entanto, existem outras abordagens também para autenticar com a Data Lake Storage Gen1, que são **a autenticação do utilizador final** ou a **autenticação de serviço-a-serviço.** Para obter instruções e obter mais informações sobre como autenticar, veja [End-user authentication](data-lake-store-end-user-authenticate-using-active-directory.md) (Autenticação de utilizador final) ou [Service-to-service authentication](./data-lake-store-service-to-service-authenticate-using-active-directory.md) (Autenticação de serviço a serviço).


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

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Criar uma conta Azure Data Lake Storage Gen1

1. Criar um novo grupo de recursos. No seguinte comando, forneça os valores de parâmetros que pretende utilizar. Se o nome da localização contiver espaços, coloque-o entre aspas. Por exemplo, "E.U.A. Leste 2". 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Crie a conta De armazenamento de dados Da Gen1.
   
    ```azurecli
    az dls account create --account mydatalakestoragegen1 --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-storage-gen1-account"></a>Criar pastas numa conta gen1 de armazenamento de data lake

Pode criar pastas sob a sua conta Azure Data Lake Storage Gen1 para gerir e armazenar dados. Utilize o seguinte comando para criar uma pasta chamada **mynewfolder** na raiz da conta Deseguizão de Armazenamento de Data Lake.

```azurecli
az dls fs create --account mydatalakestoragegen1 --path /mynewfolder --folder
```

> [!NOTE]
> O parâmetro `--folder` assegura que o comando cria uma pasta. Se este parâmetro não estiver presente, o comando cria um ficheiro vazio chamado mynewfolder na raiz da conta Desepoimento gen1 de armazenamento de dados.
> 
>

## <a name="upload-data-to-a-data-lake-storage-gen1-account"></a>Faça upload de dados para uma conta Gen1 de armazenamento de dados

Pode fazer o upload de dados para data lake storage Gen1 diretamente ao nível de raiz ou para uma pasta que criou dentro da conta. Os fragmentos abaixo demonstram como carregar alguns dados de exemplo para a pasta (**mynewfolder**) que criou na secção anterior.

Se estiver à procura de alguns dados de exemplo para carregar, pode obter a pasta **Ambulance Data** a partir do [Repositório de Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Transfira o ficheiro e armazene-o num diretório local no seu computador, como C:\sampledata\.

```azurecli
az dls fs upload --account mydatalakestoragegen1 --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> Para o destino, tem de especificar o caminho completo, incluindo o nome do ficheiro.
> 
>


## <a name="list-files-in-a-data-lake-storage-gen1-account"></a>Listar ficheiros numa conta gen1 de armazenamento de dados

Utilize o seguinte comando para listar os ficheiros numa conta gen1 de armazenamento de data lake.

```azurecli
az dls fs list --account mydatalakestoragegen1 --path /mynewfolder
```

O resultado deve ser semelhante ao seguinte:

```output
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
```

## <a name="rename-download-and-delete-data-from-a-data-lake-storage-gen1-account"></a>Renomear, transferir e apagar dados de uma conta Gen1 de armazenamento de dados 

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

## <a name="work-with-permissions-and-acls-for-a-data-lake-storage-gen1-account"></a>Trabalhe com permissões e ACLs para uma conta Gen1 de armazenamento de data lake

Nesta secção aprende-se a gerir ACLs e permissões utilizando o CLI Azure. Para uma discussão detalhada sobre como os ACLs são implementados na Azure Data Lake Storage Gen1, consulte [o controlo de acesso em Azure Data Lake Storage Gen1](data-lake-store-access-control.md).

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

    ```output
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
    ```

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
    
## <a name="delete-a-data-lake-storage-gen1-account"></a>Excluir uma conta Gen1 de armazenamento de data lake
Utilize o seguinte comando para eliminar uma conta Gen1 de armazenamento de data lake.

```azurecli
az dls account delete --account mydatalakestoragegen1
```

Quando lhe for pedido, introduza **S** para eliminar a conta.

## <a name="next-steps"></a>Passos seguintes
* [Use Azure Data Lake Storage Gen1 para grandes requisitos de dados](data-lake-store-data-scenarios.md) 
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Use Azure Data Lake Analytics com data lake storage gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight com data lake storage gen1](data-lake-store-hdinsight-hadoop-use-portal.md)