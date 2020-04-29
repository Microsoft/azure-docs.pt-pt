---
title: Apache Hadoop & armazenamento seguro de transferência - Azure HDInsight
description: Saiba como criar clusters do HDInsight com contas de armazenamento do Azure com transferência segura ativada.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: c1e5ca8b0bb828e5e8ce896bba6a5278266b118e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77560087"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Aglomerados Apache Hadoop com contas de armazenamento de transferência segura no Azure HDInsight

A funcionalidade [Transferência segura necessária](../storage/common/storage-require-secure-transfer.md) melhora a segurança da sua conta da Armazenamento do Azure ao impor todos os pedidos à sua conta através de uma ligação segura. Esta funcionalidade e o esquema wasbs só são suportados pelo clusters do HDInsight versão 3.6 ou mais recente.

> [!IMPORTANT]
> Permitir a transferência de armazenamento segura após a criação de um cluster pode resultar em erros utilizando a sua conta de armazenamento e não é recomendado. É melhor criar um novo cluster usando uma conta de armazenamento com transferência segura já ativada.

## <a name="storage-accounts"></a>Contas de armazenamento

### <a name="azure-portal"></a>Portal do Azure

Por predefinição, a propriedade necessária para transferência segura está ativada quando cria uma conta de armazenamento no portal Azure.

Para atualizar uma conta de armazenamento existente com o portal Azure, consulte [Exigir transferência segura com o portal Azure](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account).

### <a name="powershell"></a>PowerShell

Para a PowerShell cmdlet [New-AzStorageAccount,](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)certifique-se de que o parâmetro `-EnableHttpsTrafficOnly` está definido para `1`.

Para atualizar uma conta de armazenamento existente com o PowerShell, consulte [Exigir transferência segura com a PowerShell](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell).

### <a name="azure-cli"></a>CLI do Azure

Para a criação da conta de [armazenamento Az](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)de comando Azure CLI, certifique-se de que o parâmetro `--https-only` está definido para `true`.

Para atualizar uma conta de armazenamento existente com o Azure CLI, consulte [Exigir transferência segura com o Azure CLI](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli).

## <a name="add-additional-storage-accounts"></a>Adicionar mais contas de armazenamento

Existem várias opções para adicionar mais contas de armazenamento com transferência segura ativada:

* Modifique o modelo do Azure Resource Manager na última secção.
* Crie um cluster através do [portal do Azure](https://portal.azure.com) e especifique a conta de armazenamento associada.
* Utilize a ação de script para adicionar mais contas de armazenamento com transferência segura ativada a um cluster do HDInsight existente. Para obter mais informações, veja [Adicionar mais contas de armazenamento ao HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Passos seguintes

* A utilização do Armazenamento Azure (WASB) em vez de [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) como loja de dados padrão
* Para obter informações sobre como o HDInsight utiliza o Armazenamento do Azure, consulte [Utilizar o Armazenamento do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Para obter informações sobre como carregar dados para o HDInsight, veja [Upload data to HDInsight](hdinsight-upload-data.md) (Carregar dados para o HDInsight).
