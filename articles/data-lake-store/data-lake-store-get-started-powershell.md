---
title: Introdução ao Azure Data Lake Storage Gen1-PowerShell | Microsoft Docs
description: Use Azure PowerShell para criar uma conta de Azure Data Lake Storage Gen1 e executar operações básicas.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 42ddab6991b418af3e41da9966cdab69ded87461
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837897"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>Introdução ao Azure Data Lake Storage Gen1 usando Azure PowerShell

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [CLI do Azure](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Saiba como usar Azure PowerShell para criar uma conta de Azure Data Lake Storage Gen1 e executar operações básicas, como criar pastas, carregar e baixar arquivos de dados, excluir sua conta, etc. Para obter mais informações sobre Data Lake Storage Gen1, consulte [visão geral do data Lake Storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou superior**. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a name="authentication"></a>Autenticação

Este artigo usa uma abordagem de autenticação mais simples com Data Lake Storage Gen1 em que você será solicitado a inserir suas credenciais de conta do Azure. O nível de acesso para Data Lake Storage Gen1 conta e sistema de arquivos é governado pelo nível de acesso do usuário conectado. No entanto, há outras abordagens para autenticar com Data Lake Storage Gen1, que são a autenticação do usuário final ou a autenticação serviço a serviço. Para obter instruções e obter mais informações sobre como autenticar, veja [End-user authentication](data-lake-store-end-user-authenticate-using-active-directory.md) (Autenticação de utilizador final) ou [Service-to-service authentication](data-lake-store-authenticate-using-active-directory.md) (Autenticação de serviço a serviço).

## <a name="create-a-data-lake-storage-gen1-account"></a>Criar uma conta de Data Lake Storage Gen1

1. No ambiente de trabalho, abra uma nova janela do Windows PowerShell. Insira o trecho a seguir para fazer logon em sua conta do Azure, defina a assinatura e registre o provedor de Data Lake Storage Gen1. Quando for solicitado a fazer logon, certifique-se de fazer logon como um dos administradores/proprietário da assinatura:

    ```PowerShell
    # Log in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Azure Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

1. Uma conta de Data Lake Storage Gen1 está associada a um grupo de recursos do Azure. Comece criando um grupo de recursos.

    ```PowerShell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    ![Criar um grupo de recursos do Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Criar um Grupo de Recursos do Azure")

1. Crie uma conta de Data Lake Storage Gen1. O nome que especificar só pode conter minúsculas e números.

    ```PowerShell
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    ![Criar uma conta de Data Lake Storage Gen1](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Criar uma conta de Data Lake Storage Gen1")

1. Certifique-se de que a conta foi criada com êxito.

    ```PowerShell
    Test-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
    ```

    O resultado do cmdlet deve ser **Verdadeiro**.

## <a name="create-directory-structures"></a>Criar estruturas de diretório

Você pode criar diretórios em sua conta do Data Lake Storage Gen1 para gerenciar e armazenar dados.

1. Especifique um diretório de raiz.

    ```PowerShell
    $myrootdir = "/"
    ```

1. Crie um novo diretório denominado **mynewdirectory** na raiz especificada.

    ```PowerShell
    New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/mynewdirectory
    ```

1. Certifique-se de que o novo diretório foi criado com êxito.

    ```PowerShell
    Get-AzDataLakeStoreChildItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir
    ```

    Deve ser apresentado como uma saída, conforme mostrado na captura de ecrã seguinte:

    ![Verificar diretório](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Verificar Diretório")

## <a name="upload-data"></a>Carregar dados

Você pode carregar seus dados para Data Lake Storage Gen1 diretamente no nível raiz ou em um diretório que você criou na conta. Os fragmentos nesta secção demonstram como carregar alguns dados de exemplo para o diretório (**mynewdirectory**) que criou na secção anterior.

Se estiver à procura de alguns dados de exemplo para carregar, pode obter a pasta **Ambulance Data** a partir do [Repositório de Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Transfira o ficheiro e armazene-o num diretório local no seu computador, como C:\sampledata\.

```PowerShell
Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
   -Path "C:\sampledata\vehicle1_09142014.csv" `
   -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv
```

## <a name="rename-download-and-delete-data"></a>Renomear, baixar e excluir dados

Para mudar o nome de um ficheiro, utilize o seguinte comando:

```PowerShell
Move-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv `
    -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

Para transferir um ficheiro, utilize o seguinte comando:

```PowerShell
Export-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv `
    -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"
```

Para eliminar um ficheiro, utilize o seguinte comando:

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

Quando lhe for pedido, introduza **S** para eliminar o item. Se tiver mais de um ficheiro a eliminar, pode fornecer todos os caminhos separados por vírgula.

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv
```

## <a name="delete-your-account"></a>Excluir sua conta

Use o comando a seguir para excluir sua conta de Data Lake Storage Gen1.

```PowerShell
Remove-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
```

Quando lhe for pedido, introduza **S** para eliminar a conta.

## <a name="next-steps"></a>Passos seguintes

* [Diretrizes de ajuste de desempenho para usar o PowerShell com Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-powershell.md)
* [Usar Azure Data Lake Storage Gen1 para requisitos de Big Data](data-lake-store-data-scenarios.md)
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Usar Azure Data Lake Analytics com Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
