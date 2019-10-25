---
title: Criar uma conta de armazenamento de Azure Data Lake Storage Gen2 | Microsoft Docs
description: Aprenda rapidamente a criar uma nova conta de armazenamento com acesso a Data Lake Storage Gen2 usando o portal do Azure, Azure PowerShell ou o CLI do Azure.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 1c9cdfa54494cd6d77edcd13110a79e5265e5032
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72817845"
---
# <a name="create-an-azure-data-lake-storage-gen2-storage-account"></a>Criar uma conta de armazenamento Azure Data Lake Storage Gen2

O Azure Data Lake Storage Gen2 [dá suporte a um namespace hierárquico](data-lake-storage-introduction.md) que fornece um contêiner baseado em diretório nativo personalizado para funcionar com o sistema de arquivos distribuído do HADOOP (HDFS). O acesso aos dados de Armazenamento do Data Lake Ger2 a partir do HDFS está disponível através do [controlador ABFS](data-lake-storage-abfs-driver.md).

Este artigo demonstra como criar uma conta usando o portal do Azure, Azure PowerShell ou por meio do CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

|           | Pré-requisito |
|-----------|--------------|
|Portal     | Nenhuma         |
|PowerShell | Este artigo requer o módulo do PowerShell AZ. Storage versão **0,7** ou posterior. Para localizar a versão atual, execute o comando `Get-Module -ListAvailable Az.Storage`. Se, depois de executar esse comando, nenhum resultado aparecer ou se uma versão inferior a **0,7** for exibida, você precisará atualizar o módulo do PowerShell. Consulte a seção [atualizar seu módulo do PowerShell](#upgrade-your-powershell-module) deste guia.
|CLI        | Você pode entrar no Azure e executar CLI do Azure comandos de uma das duas maneiras: <ul><li>Pode executar comandos da CLI no portal do Azure, no Azure Cloud Shell </li><li>Pode instalar a CLI e executar os respetivos comandos localmente</li></ul>|

Ao trabalhar na linha de comandos, tem a opção de executar o Azure Cloud shell ou instalar a CLI localmente.

### <a name="use-azure-cloud-shell"></a>Utilizar o Azure Cloud Shell

O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Tem a CLI do Azure pré-instalada e configurada para ser utilizada com a sua conta. Clique no botão **Cloud Shell** no menu do canto superior direito do portal do Azure:

[![Cloud Shell](./media/data-lake-storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

O botão inicia um shell interativo que você pode usar para executar as etapas neste artigo:

[![Captura de ecrã que mostra a janela do Cloud Shell no portal](./media/data-lake-storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Instalar a CLI localmente

Também pode instalar e utilizar a CLI do Azure localmente. Este artigo requer que você esteja executando o CLI do Azure versão 2.0.38 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-storage-account-with-azure-data-lake-storage-gen2-enabled"></a>Criar uma conta de armazenamento com o Azure Data Lake Storage Gen2 ativado

Uma conta de armazenamento do Azure contém todos os seus objetos de dados do armazenamento do Azure: BLOBs, arquivos, filas, tabelas e discos. A conta de armazenamento fornece um namespace exclusivo para os dados do armazenamento do Azure que podem ser acessados de qualquer lugar do mundo por HTTP ou HTTPS. Os dados em sua conta de armazenamento do Azure são duráveis e altamente disponíveis, seguros e amplamente escalonáveis.

> [!NOTE]
> Tem de criar novas contas de armazenamento como o tipo **StorageV2 (V2 de fins gerais)** , para tirar partido das funcionalidades de Armazenamento do Data Lake Ger2.  

Para obter mais informações sobre as contas de armazenamento, veja [Azure Storage account overview](../common/storage-account-overview.md) (Descrição geral da Conta de Armazenamento).

## <a name="create-an-account-using-the-azure-portal"></a>Criar uma conta no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

### <a name="create-a-storage-account"></a>Create a storage account

Cada conta de armazenamento tem de pertencer a um grupo de recursos do Azure. Um grupo de recursos é um contentor lógico para agrupar os seus serviços do Azure. Quando cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou utilizar um grupo de recursos existente. Este artigo mostra como criar um novo grupo de recursos.

Para criar uma conta de armazenamento para fins gerais v2 no portal do Azure, siga estes passos:

> [!NOTE]
> O espaço de nomes hierárquico está atualmente disponível em todas as regiões públicas.

1. Escolha a subscrição na qual pretende criar a conta de armazenamento.
2. Na portal do Azure, escolha o botão **criar um recurso** e, em seguida, escolha **conta de armazenamento**.
3. No campo **Grupo de recursos**, selecione **Criar novo**. Insira um nome para o novo grupo de recursos.
   
   Um grupo de recursos é um contentor lógico para agrupar os seus serviços do Azure. Quando cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou utilizar um grupo de recursos existente.

4. A seguir, introduza um nome para a sua conta de armazenamento. O nome que escolher tem de ser exclusivo em todo o Azure. O nome também tem de ter entre 3 e 24 carateres de comprimento e apenas pode incluir números e letras minúsculas.
5. Escolher uma localização.
6. Verifique se **StorageV2 (uso geral v2)** aparece como selecionado na lista suspensa **tipo de conta** .
7. Opcionalmente, altere os valores em cada um desses campos: **desempenho**, **replicação**, **camada de acesso**. Para saber mais sobre essas opções, consulte [introdução ao armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#introducing-the-azure-storage-services).
8. Escolha a guia **avançado** .
10. Na seção **Data Lake Storage Gen2** , defina **namespace hierárquico** como **habilitado**.
11. Clique em **examinar + criar** para criar a conta de armazenamento.

A conta de armazenamento está agora criada através do portal.

### <a name="clean-up-resources"></a>Limpar recursos

Para remover um grupo de recursos através do portal do Azure:

1. No portal do Azure, expanda o menu no lado esquerdo para abrir o menu de serviços e escolha **Grupos de Recursos**, para apresentar a lista dos seus grupos de recursos.
2. Encontre o grupo de recursos a eliminar e clique com o botão direito do rato em **Mais** ( **...** ) no lado direito da lista.
3. Selecione **Eliminar grupo de recursos** e confirme.

## <a name="create-an-account-using-powershell"></a>Criar uma conta com o PowerShell

Primeiro, instale a versão mais recente do módulo [PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget) .

Em seguida, atualize seu módulo do PowerShell, entre na sua assinatura do Azure, crie um grupo de recursos e, em seguida, crie uma conta de armazenamento.

### <a name="upgrade-your-powershell-module"></a>Atualizar o módulo do powershell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para interagir com Data Lake Storage Gen2 usando o PowerShell, você precisará instalar o módulo AZ. Storage versão **0,7** ou posterior.

Comece abrindo uma sessão do PowerShell com permissões elevadas.

Instalar o módulo AZ. Storage

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Entre na sua assinatura do Azure

Use o comando `Login-AzAccount` e siga as instruções na tela para se autenticar.

```powershell
Login-AzAccount
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um novo grupo de recursos com o PowerShell, use o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) : 

> [!NOTE]
> O espaço de nomes hierárquico está atualmente disponível em todas as regiões públicas.

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-general-purpose-v2-storage-account"></a>Criar uma conta de armazenamento v2 para fins gerais

Para criar uma conta de armazenamento de uso geral v2 do PowerShell com armazenamento com redundância local (LRS), use o comando [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) :

```powershell
$location = "westus2"

New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 `
  -EnableHierarchicalNamespace $True
```

### <a name="clean-up-resources"></a>Limpar recursos

Para remover o grupo de recursos e seus recursos associados, incluindo a nova conta de armazenamento, use o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) : 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-an-account-using-azure-cli"></a>Criar uma conta com a CLI do Azure

Para iniciar o Azure Cloud Shell, entre no [portal do Azure](https://portal.azure.com).

Se você quiser entrar em sua instalação local da CLI, execute o comando de logon:

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-data-lake-gen-2"></a>Adicionar a extensão da CLI para Azure Data Lake Gen 2

Para interagir com Data Lake Storage Gen2 usando a CLI, você precisará adicionar uma extensão ao seu shell.

Para fazer isso, digite o seguinte comando usando o Cloud Shell ou um shell local: `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um novo grupo de recursos com a CLI do Azure, utilize o comando [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name storage-quickstart-resource-group `
    --location westus2
```

> [!NOTE]
> > O espaço de nomes hierárquico está atualmente disponível em todas as regiões públicas.

### <a name="create-a-general-purpose-v2-storage-account"></a>Criar uma conta de armazenamento v2 para fins gerais

Para criar uma conta de armazenamento para fins gerais v2 a partir da CLI do Azure com armazenamento localmente redundante, utilize o comando [az storage account create](/cli/azure/storage/account).

```azurecli-interactive
az storage account create `
    --name storagequickstart `
    --resource-group storage-quickstart-resource-group `
    --location westus2 `
    --sku Standard_LRS `
    --kind StorageV2 `
    --hierarchical-namespace true
```

### <a name="clean-up-resources"></a>Limpar recursos

Para remover o grupo de recursos e os respetivos recursos associados, incluindo a nova conta de armazenamento, utilize o comando [eliminação do grupo az](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você criou uma conta de armazenamento com recursos de Data Lake Storage Gen2. Para saber como carregar e baixar blobs de e para sua conta de armazenamento, consulte o tópico a seguir.

* [AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
