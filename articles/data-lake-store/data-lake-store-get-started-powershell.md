---
title: Utilizar o PowerShell para obter uma introdução Gen1 de armazenamento do Azure Data Lake | Documentos da Microsoft
description: Utilizar o Azure PowerShell para criar uma conta de geração 1 de armazenamento do Azure Data Lake e executar operações básicas
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 6635b572d11339307083b8aa2aaaba69dc731101
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885864"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>Introdução à geração 1 de armazenamento do Azure Data Lake com o Azure PowerShell
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [CLI do Azure](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Saiba como utilizar o Azure PowerShell para criar uma conta de geração 1 de armazenamento do Azure Data Lake e executar operações básicas, tais como criar pastas, carregar e transferem ficheiros de dados, eliminar a conta, etc. Para obter mais informações sobre a geração 1 de armazenamento do Data Lake, veja [descrição geral do Data Lake Storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou superior**. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a name="authentication"></a>Authentication
Este artigo utiliza uma abordagem de autenticação mais simples com o Data Lake Storage Gen1 qual lhe é pedido que introduza as credenciais de conta do Azure. O nível de acesso ao Data Lake Storage Gen1 conta e sistema de ficheiros é, então, governado pelo nível de acesso do utilizador com sessão iniciada. No entanto, existem outras abordagens para autenticar com o Data Lake Storage Gen1, que são **autenticação de utilizador final** ou **autenticação serviço a serviço**. Para obter instruções e mais informações sobre como autenticar, consulte [End-user authentication](data-lake-store-end-user-authenticate-using-active-directory.md) (Autenticação de utilizador final) ou [Service-to-service authentication](data-lake-store-authenticate-using-active-directory.md) (Autenticação de serviço a serviço).

## <a name="create-a-data-lake-storage-gen1-account"></a>Criar uma conta de geração 1 de armazenamento do Data Lake
1. No ambiente de trabalho, abra uma nova janela do Windows PowerShell. Introduza o fragmento seguinte para iniciar sessão na sua conta do Azure, definir a subscrição e registar o fornecedor de geração 1 de armazenamento do Data Lake. Quando lhe for pedido para iniciar sessão, certifique-se de que inicia sessão como um do proprietário/administradores da subscrição:

        # Log in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
2. Uma conta de geração 1 de armazenamento do Data Lake está associada um grupo de recursos do Azure. Comece por criar um Grupo de Recursos do Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Criar um Grupo de Recursos do Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Criar um Grupo de Recursos do Azure")
3. Crie uma conta de geração 1 de armazenamento do Data Lake. O nome que especificar só pode conter minúsculas e números.

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
        New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"

    ![Criar uma conta de geração 1 de armazenamento do Data Lake](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "criar uma conta de geração 1 de armazenamento do Data Lake")
4. Certifique-se de que a conta foi criada com êxito.

        Test-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name

    O resultado do cmdlet deve ser **Verdadeiro**.

## <a name="create-directory-structures-in-your-data-lake-storage-gen1-account"></a>Criar estruturas de diretório na sua conta de geração 1 de armazenamento do Data Lake
Pode criar diretórios na sua conta de geração 1 de armazenamento do Data Lake para gerir e armazenar dados.

1. Especifique um diretório de raiz.

        $myrootdir = "/"
2. Crie um novo diretório denominado **mynewdirectory** na raiz especificada.

        New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/mynewdirectory
3. Certifique-se de que o novo diretório foi criado com êxito.

        Get-AzDataLakeStoreChildItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir

    Deve ser apresentado como uma saída, conforme mostrado na captura de ecrã seguinte:

    ![Verificar Diretório](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Verificar Diretório")

## <a name="upload-data-to-your-data-lake-storage-gen1-account"></a>Carregar dados para a conta de geração 1 de armazenamento do Data Lake
Pode carregar os dados para a geração 1 de armazenamento do Data Lake diretamente no nível de raiz ou para um diretório que criou na conta. Os fragmentos nesta secção demonstram como carregar alguns dados de exemplo para o diretório (**mynewdirectory**) que criou na secção anterior.

Se estiver à procura de alguns dados de exemplo para carregar, pode obter a pasta **Ambulance Data** a partir do [Repositório de Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Transfira o ficheiro e armazene-o num diretório local no seu computador, como C:\sampledata\.

    Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-storage-gen1-account"></a>Mudar o nome, transferir e eliminar dados da sua conta de geração 1 de armazenamento do Data Lake
Para mudar o nome de um ficheiro, utilize o seguinte comando:

    Move-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Para transferir um ficheiro, utilize o seguinte comando:

    Export-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Para eliminar um ficheiro, utilize o seguinte comando:

    Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Quando lhe for pedido, introduza **S** para eliminar o item. Se tiver mais de um ficheiro a eliminar, pode fornecer todos os caminhos separados por vírgula.

    Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-data-lake-storage-gen1-account"></a>Eliminar a sua conta de geração 1 de armazenamento do Data Lake
Utilize o seguinte comando para eliminar a sua conta de geração 1 de armazenamento do Data Lake.

    Remove-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name

Quando lhe for pedido, introduza **S** para eliminar a conta.

## <a name="next-steps"></a>Passos Seguintes
* [Guia para utilizar o PowerShell com a geração 1 de armazenamento do Azure Data Lake de sintonização de desempenho](data-lake-store-performance-tuning-powershell.md)
* [Utilize a geração 1 de armazenamento do Azure Data Lake para requisitos de grandes volumes de dados](data-lake-store-data-scenarios.md) 
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Utilizar o Azure Data Lake Analytics com o Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Utilizar o Azure HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
