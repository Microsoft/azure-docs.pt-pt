---
title: Apache Hadoop & armazenamento de transferência seguro - Azure HDInsight
description: Saiba como criar clusters do HDInsight com contas de armazenamento do Azure com transferência segura ativada.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: a02da7237252811d89e2c19a29f49f0bf9bb3804
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945738"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Aglomerados Apache Hadoop com contas de armazenamento de transferência seguras em Azure HDInsight

A funcionalidade [Transferência segura necessária](../storage/common/storage-require-secure-transfer.md) melhora a segurança da sua conta da Armazenamento do Azure ao impor todos os pedidos à sua conta através de uma ligação segura. Esta funcionalidade e o esquema wasbs só são suportados pelo clusters do HDInsight versão 3.6 ou mais recente.

> [!IMPORTANT]
> Permitir uma transferência segura de armazenamento após a criação de um cluster pode resultar em erros usando a sua conta de armazenamento e não é recomendado. É melhor criar um novo cluster usando uma conta de armazenamento com transferência segura já ativada.

## <a name="storage-accounts"></a>Contas de armazenamento

### <a name="azure-portal"></a>Portal do Azure

Por predefinição, a propriedade necessária de transferência segura é ativada quando cria uma conta de armazenamento no portal Azure.

Para atualizar uma conta de armazenamento existente com o portal Azure, consulte [Exigir transferência segura com o portal Azure](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account).

### <a name="powershell"></a>PowerShell

Para o cmdlet PowerShell [New-AzStorageAccount, certifique-se](/powershell/module/az.storage/new-azstorageaccount)de que o parâmetro `-EnableHttpsTrafficOnly` está definido para `1` .

Para atualizar uma conta de armazenamento existente com o PowerShell, consulte [Exigir transferência segura com o PowerShell](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell).

### <a name="azure-cli"></a>CLI do Azure

Para a conta de armazenamento Azure CLI [az criar,](/cli/azure/storage/account#az-storage-account-create)certifique-se de que o parâmetro `--https-only` está definido para `true` .

Para atualizar uma conta de armazenamento existente com o Azure CLI, consulte [Exigir transferência segura com o Azure CLI](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli).

## <a name="add-additional-storage-accounts"></a>Adicionar mais contas de armazenamento

Existem várias opções para adicionar mais contas de armazenamento com transferência segura ativada:

* Modifique o modelo do Azure Resource Manager na última secção.
* Crie um cluster através do [portal do Azure](https://portal.azure.com) e especifique a conta de armazenamento associada.
* Utilize a ação de script para adicionar mais contas de armazenamento com transferência segura ativada a um cluster do HDInsight existente. Para obter mais informações, veja [Adicionar mais contas de armazenamento ao HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Próximos passos

* A utilização do Azure Storage (WASB) em vez de [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) como a loja de dados padrão
* Para obter informações sobre como o HDInsight utiliza o Armazenamento do Azure, consulte [Utilizar o Armazenamento do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Para obter informações sobre como carregar dados para o HDInsight, veja [Upload data to HDInsight](hdinsight-upload-data.md) (Carregar dados para o HDInsight).