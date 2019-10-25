---
title: Criar uma conta de armazenamento de blobs de blocos-armazenamento do Azure | Microsoft Docs
description: Mostra como criar uma conta do Azure BlockBlobStorage com características de desempenho premium.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 1df1d5180d951e7a720ec82c548438892a47a426
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881869"
---
# <a name="create-a-blockblobstorage-account"></a>Criar uma conta do BlockBlobStorage

O tipo de conta BlockBlobStorage permite criar blobs de blocos com características de desempenho premium. Esse tipo de conta de armazenamento é otimizado para cargas de trabalho com altas taxas de transações ou que exigem tempos de acesso muito rápidos. Este artigo mostra como criar uma conta do BlockBlobStorage usando o portal do Azure, o CLI do Azure ou o Azure PowerShell.

Para obter mais informações sobre contas do BlockBlobStorage, consulte [visão geral da conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Para criar uma conta do BlockBlobStorage no portal do Azure, siga estas etapas:

1. Na portal do Azure, selecione **todos os serviços** > Categoria de **armazenamento** > **contas de armazenamento**.

1. Em **contas de armazenamento**, selecione **Adicionar**.

1. No campo **assinatura** , selecione a assinatura na qual criar a conta de armazenamento.

1. No campo **grupo de recursos** , selecione um grupo de recursos existente ou selecione **criar novo**e insira um nome para o novo grupo de recursos.

1. No campo **nome da conta de armazenamento** , insira um nome para a conta. Observe as seguintes diretrizes:

   - O nome deve ser exclusivo no Azure.
   - O nome deve ter entre três e 24 caracteres.
   - O nome pode incluir apenas números e letras minúsculas.

1. No campo **local** , selecione um local para a conta de armazenamento ou use o local padrão.

1. Para o restante das configurações, configure o seguinte:

   |Campo     |Valor  |
   |---------|---------|
   |**Performance** (Desempenho)    |  Selecione **Premium**.   |
   |**Account kind** (Tipo de conta)    | Selecione **BlockBlobStorage**.      |
   |**Replicação**    |  Deixe a configuração padrão de **armazenamento com redundância local (LRS)** .      |

   ![Mostra a interface do usuário do portal para criar uma conta de armazenamento de blob de blocos](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Selecione **revisão + criar** para revisar as configurações da conta de armazenamento.

1. Selecione **Criar**.

## <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Abra uma sessão do Windows PowerShell com privilégios elevados (executar como administrador).

1. Execute o comando a seguir para certificar-se de que a versão mais recente do módulo `Az` PowerShell esteja instalada.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Abra um novo console do PowerShell e entre com sua conta do Azure.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. Se necessário, crie um novo grupo de recursos. Substitua os valores entre aspas e execute o comando a seguir.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Crie a conta BlockBlobStorage. Substitua os valores entre aspas e execute o comando a seguir.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para criar uma conta de blob de blocos usando o CLI do Azure, você deve primeiro instalar o CLI do Azure v. 2.0.46 ou uma versão posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. Entre em sua assinatura do Azure.

   ```azurecli
   az login
   ```

1. Se necessário, crie um novo grupo de recursos. Substitua os valores entre colchetes (incluindo os colchetes) e execute o comando a seguir.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. Crie a conta BlockBlobStorage. Substitua os valores entre colchetes (incluindo os colchetes) e execute o comando a seguir.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre as contas de armazenamento, veja [Visão geral da conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- Para mais informações sobre grupos de recursos, veja [Descrição Geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
