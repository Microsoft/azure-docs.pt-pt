---
title: Criar uma conta de armazenamento de BLOBs de bloco - armazenamento do Azure | Documentos da Microsoft
description: Mostra como criar uma conta de armazenamento de BLOBs de bloco do Azure com características de desempenho premium.
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
author: twooley
ms.author: twooley
ms.subservice: blobs
ms.openlocfilehash: f4d3e3ad923b6a603902bc007107cb41dae8cf6c
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400472"
---
# <a name="create-a-block-blob-storage-account"></a>Criar uma conta de armazenamento de BLOBs de bloco

O tipo de conta de armazenamento de BLOBs de bloco permite-lhe criar blobs de blocos com características de desempenho premium. Este tipo de conta de armazenamento está otimizado para cargas de trabalho com taxas de transações elevada ou que requerem horas de acesso muito rápidas. Este artigo mostra como criar uma conta de armazenamento de BLOBs de bloco, utilizando o portal do Azure, a CLI do Azure ou o Azure PowerShell.

Para obter mais informações sobre contas de armazenamento de BLOBs de bloco, consulte [descrição geral da conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="create-account-in-the-azure-portal"></a>Criar conta no portal do Azure

Para criar uma conta de armazenamento de BLOBs de blocos no portal do Azure, siga estes passos:

1. No portal do Azure, selecione **todos os serviços** > o **armazenamento** categoria > **contas de armazenamento**.

1. Sob **contas de armazenamento**, selecione **Add**.

1. Na **subscrição** campo, selecione a subscrição na qual pretende criar a conta de armazenamento.

1. Na **grupo de recursos** campo, selecione um grupo de recursos existente ou selecione **criar nova**e introduza um nome para o novo grupo de recursos.

1. Na **nome da conta de armazenamento** , insira um nome para a conta. Tenha em atenção as seguintes diretrizes:

   - O nome tem de ser exclusivo em todo o Azure.
   - O nome tem de ter entre três e 24 carateres de comprimento.
   - O nome pode conter apenas números e letras minúsculas.

1. Na **localização** campo, selecione uma localização para a conta de armazenamento ou utilize a localização predefinida.

1. Para o restante das definições, configure o seguinte:

   |Campo     |Value  |
   |---------|---------|
   |**Performance** (Desempenho)    |  Selecione **Premium**.   |
   |**Account kind** (Tipo de conta)    | Selecione **BlockBlobStorage**.      |
   |**Replicação**    |  Deixe a predefinição de **armazenamento localmente redundante (LRS)**.      |

   ![Mostra o portal da interface do Usuário para criar uma conta de armazenamento de BLOBs de bloco](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

1. Selecione **rever + criar** para rever as definições de conta de armazenamento.

1. Selecione **Criar**.

## <a name="create-account-using-azure-powershell"></a>Criar conta com o Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Abra uma sessão elevada do Windows PowerShell (executar como administrador).

1. Execute o seguinte comando para se certificar de que a versão mais recente do `Az` módulo do PowerShell está instalado.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

1. Abra uma nova consola do PowerShell e inicie sessão com a sua conta do Azure.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

1. Se necessário, crie um novo grupo de recursos. Substitua os valores na cotações e execute o seguinte comando.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

1. Crie a conta de armazenamento de BLOBs de bloco. Substitua os valores na cotações e execute o seguinte comando.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```

## <a name="create-account-using-azure-cli"></a>Criar conta com a CLI do Azure

Para criar uma conta do blob de bloco, utilizando a CLI do Azure, primeiro tem de instalar o Azure CLI v. 2.0.46 ou uma versão posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. Inicie sessão na sua subscrição do Azure.

   ```azurecli
   az login
   ```

1. Se necessário, crie um novo grupo de recursos. Substitua os valores entre parênteses Retos (incluindo os colchetes) e execute o seguinte comando.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

1. Crie a conta de armazenamento de BLOBs de bloco. Substitua os valores entre parênteses Retos (incluindo os colchetes) e execute o seguinte comando.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre as contas de armazenamento, veja [Visão geral da conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

- Para mais informações sobre grupos de recursos, veja [Descrição Geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
