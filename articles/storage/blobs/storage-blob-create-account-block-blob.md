---
title: Criar uma conta de armazenamento de blocos blob - Armazenamento Azure / Microsoft Docs
description: Mostra como criar uma conta Azure BlockBlobStorage com características de desempenho premium.
author: tamram
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/10/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 4767c0310783e7e2cc51b4caa7d6e6a052d0a05a
ms.sourcegitcommit: 801a551e047e933e5e844ea4e735d044d170d99a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2020
ms.locfileid: "83007305"
---
# <a name="create-a-blockblobstorage-account"></a>Criar uma conta BlockBlobStorage

O tipo de conta BlockBlobStorage permite criar bolhas de bloco com características de desempenho premium. Este tipo de conta de armazenamento é otimizada para cargas de trabalho com altas taxas de transações ou que requerem tempos de acesso muito rápidos. Este artigo mostra como criar uma conta BlockBlobStorage utilizando o portal Azure, o Azure CLI ou o Azure PowerShell.

> [!NOTE]
> O espaço hierárquico em uma conta de armazenamento de blocos blob está em pré-visualização pública, e está disponível nas regiões leste dos EUA, US East 2, US Central, US South Central, US West 2, UK South, Canadá Central e Austrália East. Para rever as limitações, consulte as funcionalidades de [armazenamento blob disponíveis em Questões de Armazenamento](data-lake-storage-supported-blob-storage-features.md) de Lagos De Dados Azure e [questões conhecidas.](data-lake-storage-known-issues.md) Para se inscrever na pré-visualização, consulte [este formulário](https://aka.ms/adlspremiumonboard).

Para obter mais informações sobre as contas BlockBlobStorage, consulte a visão geral da conta de [armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Nenhum.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Este artigo de como fazer requer o módulo Azure PowerShell Az versão 1.2.0 ou posterior. Execute `Get-Module -ListAvailable Az` para encontrar a versão atual. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-Az-ps)(Instalar o módulo do Azure PowerShell).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Pode iniciar sessão no Azure e executar comandos Azure CLI de uma de duas formas:

- Pode executar comandos CLI dentro do portal Azure, em Azure Cloud Shell.
- Pode instalar o CLI e executar comandos CLI localmente.

### <a name="use-azure-cloud-shell"></a>Utilizar o Azure Cloud Shell

O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. O Azure CLI está pré-instalado e configurado para utilizar com a sua conta. Clique no botão **Cloud Shell** no menu na secção superior direita do portal Azure:

[![Casca de Nuvem](../common/media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

O botão lança uma concha interativa que pode utilizar para executar os passos descritos neste artigo de como:

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

Para lançar a Azure Cloud Shell, inscreva-se no [portal Azure.](https://portal.azure.com)

Para iniciar sessão na instalação local do CLI, execute o comando [de login az:](/cli/azure/reference-index#az-login)

```azurecli
az login
```

---

## <a name="create-a-blockblobstorage-account"></a>Criar uma conta BlockBlobStorage

## <a name="portal"></a>[Portal](#tab/azure-portal)
Para criar uma conta BlockBlobStorage no portal Azure, siga estes passos:

1. No portal Azure, selecione **Todos os serviços** > a categoria **de Armazenamento** > contas de **armazenamento.**

2. Nas **contas de Armazenamento,** selecione **Adicionar**.

3. No campo **Subscrição,** selecione a subscrição para criar a conta de armazenamento.

4. No campo do **grupo Recursos,** selecione um grupo de recursos existente ou selecione **Criar novo**, e insira um nome para o novo grupo de recursos.

5. No campo de **nome da conta de armazenamento,** insira um nome para a conta. Note as seguintes orientações:

   - O nome deve ser único em Azure.
   - O nome deve ter entre 3 e 24 caracteres.
   - O nome pode incluir apenas números e letras minúsculas.

6. No campo **Localização,** selecione um local para a conta de armazenamento ou utilize a localização predefinida.

7. Para o resto das definições, configure o seguinte:

   |Campo     |Valor  |
   |---------|---------|
   |**Desempenho**    |  Selecione **Premium**.   |
   |**Tipo de conta**    | Selecione **BlockBlobStorage**.      |
   |**Replicação**    |  Deixe a definição padrão de **armazenamento redundante localmente (LRS)**.      |

   ![Mostra o portal UI para criar uma conta de armazenamento de blocos blob](media/storage-blob-create-account-block-blob/create-block-blob-storage-account.png)

8. Escolha o separador **Avançado.**

9. Se pretender otimizar a sua conta de armazenamento para análise de dados, em seguida, coloque o espaço de **nome hierárquico** para **Ativado**. Caso contrário, deixe esta opção definida para o seu valor padrão.

   Para saber mais, consulte [Introdução ao Azure Data Lake Storage Gen2](data-lake-storage-introduction.md).

   > [!NOTE]
   > O espaço hierárquico em uma conta de armazenamento de blocos blob está em pré-visualização pública, e está disponível nas regiões leste dos EUA, US East 2, US Central, US South Central, US West 2, UK South, Canadá Central e Austrália East. Para rever as limitações, consulte as funcionalidades de [armazenamento blob disponíveis em Questões de Armazenamento](data-lake-storage-supported-blob-storage-features.md) de Lagos De Dados Azure e [questões conhecidas.](data-lake-storage-known-issues.md) Para se inscrever na pré-visualização, consulte [este formulário](https://aka.ms/adlspremiumonboard).

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

4. Se necessário, crie um novo grupo de recursos. Substitua os valores em cotações e execute o seguinte comando.

   ```powershell
   $resourcegroup = "new_resource_group_name"
   $location = "region_name"
   New-AzResourceGroup -Name $resourceGroup -Location $location
   ```

5. Crie a conta BlockBlobStorage. Substitua os valores em cotações e execute o seguinte comando.

   ```powershell
   $resourcegroup = "resource_group_name"
   $storageaccount = "new_storage_account_name"
   $location = "region_name"

   New-AzStorageAccount -ResourceGroupName $resourcegroup -Name $storageaccount -Location $location -Kind "BlockBlobStorage" -SkuName "Premium_LRS"
   ```
   Se quiser otimizar a sua conta de armazenamento para análise de dados, adicione `-EnableHierarchicalNamespace $True` ao comando. Para saber mais, consulte [Introdução ao Azure Data Lake Storage Gen2](data-lake-storage-introduction.md).

   > [!NOTE]
   > O espaço hierárquico em uma conta de armazenamento de blocos blob está em pré-visualização pública, e está disponível nas regiões leste dos EUA, US East 2, US Central, US South Central, US West 2, UK South, Canadá Central e Austrália East. Para rever as limitações, consulte as funcionalidades de [armazenamento blob disponíveis em Questões de Armazenamento](data-lake-storage-supported-blob-storage-features.md) de Lagos De Dados Azure e [questões conhecidas.](data-lake-storage-known-issues.md) Para se inscrever na pré-visualização, consulte [este formulário](https://aka.ms/adlspremiumonboard).

## <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para criar uma conta de bloco sinuoso utilizando o Azure CLI, tem de instalar primeiro o Azure CLI v. 2.0.46 ou uma versão posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. Inscreva-se na sua assinatura Azure.

   ```azurecli
   az login
   ```

2. Se necessário, crie um novo grupo de recursos. Substitua os valores nos suportes (incluindo os parênteses) e execute o seguinte comando.

   ```azurecli
   az group create \
    --name "<new_resource_group_name>" \
    --location "<location>"
   ```

3. Crie a conta BlockBlobStorage. Substitua os valores nos suportes (incluindo os parênteses) e execute o seguinte comando.

   ```azurecli
   az storage account create \
    --location "<location>" \
    --name "<new_storage_account_name>" \
    --resource-group "<resource_group_name>" \
    --kind "BlockBlobStorage" \
    --sku "Premium_LRS"
   ```

   Se quiser otimizar a sua conta de armazenamento para análise de dados, adicione `--hierarchical-namespace true` ao comando. Para saber mais, consulte [Introdução ao Azure Data Lake Storage Gen2](data-lake-storage-introduction.md).

   > [!NOTE]
   > O espaço hierárquico em uma conta de armazenamento de blocos blob está em pré-visualização pública, e está disponível nas regiões leste dos EUA, US East 2, US Central, US South Central, US West 2, UK South, Canadá Central e Austrália East. Para rever as limitações, consulte as funcionalidades de [armazenamento blob disponíveis em Questões de Armazenamento](data-lake-storage-supported-blob-storage-features.md) de Lagos De Dados Azure e [questões conhecidas.](data-lake-storage-known-issues.md) Para se inscrever na pré-visualização, consulte [este formulário](https://aka.ms/adlspremiumonboard).
   
---

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre contas de armazenamento, consulte a visão geral da conta de [armazenamento do Azure.](https://docs.microsoft.com/azure/storage/common/storage-account-overview)

- Para mais informações sobre grupos de recursos, veja [Descrição Geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
