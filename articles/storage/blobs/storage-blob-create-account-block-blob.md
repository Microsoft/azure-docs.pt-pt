---
title: Criar uma conta de armazenamento de blob de bloco - Azure Storage | Microsoft Docs
description: Mostra como criar uma conta Azure BlockBlobStorage com características de desempenho premium.
author: tamram
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 10/30/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 9350f9aeff90b75a4e1362f6fa2fa1b0d07f20cf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997091"
---
# <a name="create-a-blockblobstorage-account"></a>Criar uma conta BlockBlobStorage

O tipo de conta BlockBlobStorage permite criar blobs de bloco com características de desempenho premium. Este tipo de conta de armazenamento é otimizado para cargas de trabalho com altas taxas de transações ou que requerem tempos de acesso muito rápidos. Este artigo mostra como criar uma conta BlockBlobStorage utilizando o portal Azure, o Azure CLI ou Azure PowerShell.

Para obter mais informações sobre as contas blockBlobStorage, consulte [a visão geral da conta de armazenamento Azure](../common/storage-account-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Nenhum.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Este artigo requer o módulo Azure PowerShell versão 1.2.0 ou posterior. Execute `Get-Module -ListAvailable Az` para encontrar a versão atual. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-Az-ps)(Instalar o módulo do Azure PowerShell).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Pode entrar no Azure e executar os comandos Azure CLI de uma de duas maneiras:

- Pode executar comandos CLI a partir do portal Azure, em Azure Cloud Shell.
- Pode instalar o CLI e executar os comandos CLI localmente.

### <a name="use-azure-cloud-shell"></a>Utilizar o Azure Cloud Shell

O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. O Azure CLI está pré-instalado e configurado para ser utilizado com a sua conta. Clique no botão **Cloud Shell** no menu na secção superior direita do portal Azure:

[![Cloud Shell](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

O botão lança uma concha interativa que pode usar para executar os passos descritos neste artigo de como fazer:

[![Screenshot mostrando a janela Cloud Shell no portal](../common/media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Instalar a CLI localmente

Também pode instalar e utilizar a CLI do Azure localmente. Este artigo de como fazer requer que esteja a executar a versão Azure CLI 2.0.46 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

---

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

# <a name="portal"></a>[Portal](#tab/azure-portal)

Inicie sessão no [portal do Azure](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Inscreva-se na sua subscrição Azure com o `Connect-AzAccount` comando e siga as instruções no ecrã para autenticar.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para lançar a Azure Cloud Shell, inscreva-se no [portal Azure](https://portal.azure.com).

Para iniciar sessão na instalação local do CLI, execute o comando [de login az:](/cli/azure/reference-index#az-login)

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>Criar uma conta BlockBlobStorage

## <a name="portal"></a>[Portal](#tab/azure-portal)
Para criar uma conta BlockBlobStorage no portal Azure, siga estes passos:

1. No portal Azure, selecione **Todos os serviços** > a categoria **de Armazenamento** > Contas **de Armazenamento**.

2. Nas **contas de Armazenamento**, selecione **Add**.

3. No campo **Subscrição,** selecione a subscrição na qual criar a conta de armazenamento.

4. No campo **grupo De recursos,** selecione um grupo de recursos existente ou selecione **Criar novo**, e insira um nome para o novo grupo de recursos.

5. No campo **de nome da conta De armazenamento,** insira um nome para a conta. Note as seguintes orientações:

   - O nome deve ser único em Azure.
   - O nome deve ter entre três e 24 caracteres de comprimento.
   - O nome pode incluir apenas números e letras minúsculas.

6. No campo **Localização,** selecione uma localização para a conta de armazenamento ou utilize a localização predefinitiva.

7. Para o resto das definições, configufique o seguinte:

   |Campo     |Valor  |
   |---------|---------|
   |**Desempenho**    |  Selecione **Premium**.   |
   |**Tipo de conta**    | Selecione **BlockBlobStorage**.      |
   |**Replicação**    |  Deixe a definição padrão de **armazenamento localmente redundante (LRS)**.      |

   ![Mostra portal UI para criar uma conta de armazenamento de blob de bloco](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

8. Escolha o separador **Avançado.**

9. Se pretender otimizar a sua conta de armazenamento para análise de dados, em seguida, desapasça o **espaço de nome hierárquico** para **Ativado**. Caso contrário, deixe esta opção definida para o seu valor predefinido. Ativar esta definição com a sua conta BlockBlobStorage dá-lhe o nível premium para o [armazenamento de data lake.](premium-tier-for-data-lake-storage.md)  Para saber mais sobre o armazenamento do data lake, consulte [Introdução ao Azure Data Lake Storage Gen2](data-lake-storage-introduction.md).

8. Selecione **Review + criar** para rever as definições da conta de armazenamento.

9. Selecione **Criar**.

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Abra uma sessão elevada do Windows PowerShell (Executar como administrador).

2. Executar o seguinte comando para se certificar de que a versão mais recente do `Az` módulo PowerShell está instalada.

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

3. Abra uma nova consola PowerShell e inscreva-se na sua conta Azure.

   ```powershell
   Connect-AzAccount -SubscriptionId <SubscriptionID>
   ```

4. Se necessário, crie um novo grupo de recursos. Substitua os valores nas citações e execute o seguinte comando.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

5. Crie a conta BlockBlobStorage. Substitua os valores nas citações e execute o seguinte comando.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```
   Se pretender otimizar a sua conta de armazenamento para análise de dados, adicione `-EnableHierarchicalNamespace $True` ao comando. Ativar esta definição com a sua conta BlockBlobStorage dá-lhe o nível premium para o [armazenamento de data lake.](premium-tier-for-data-lake-storage.md)  Para saber mais sobre o armazenamento do data lake, consulte [Introdução ao Azure Data Lake Storage Gen2](data-lake-storage-introduction.md).

## <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para criar uma conta blob de bloco utilizando o CLI Azure, tem primeiro de instalar o Azure CLI v. 2.0.46 ou uma versão posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. Inscreva-se na sua assinatura Azure.

   ```azurecli
   az login
   ```

2. Se necessário, crie um novo grupo de recursos. Substitua os valores nos suportes (incluindo os suportes) e executar o seguinte comando.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

3. Crie a conta BlockBlobStorage. Substitua os valores nos suportes (incluindo os suportes) e executar o seguinte comando.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

   Se pretender otimizar a sua conta de armazenamento para análise de dados, adicione `--hierarchical-namespace true` ao comando. Ativar esta definição com a sua conta BlockBlobStorage dá-lhe o nível premium para o [armazenamento de data lake.](premium-tier-for-data-lake-storage.md)  Para saber mais sobre o armazenamento do data lake, consulte [Introdução ao Azure Data Lake Storage Gen2](data-lake-storage-introduction.md).

---

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre contas de armazenamento, consulte [a visão geral da conta de armazenamento Azure](../common/storage-account-overview.md).

- Para mais informações sobre grupos de recursos, veja [Descrição Geral do Azure Resource Manager](../../azure-resource-manager/management/overview.md).