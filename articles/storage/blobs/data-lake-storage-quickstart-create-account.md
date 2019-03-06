---
title: Criar uma conta de armazenamento de geração 2 de armazenamento do Azure Data Lake | Documentos da Microsoft
description: Aprenda rapidamente a criar uma nova conta de armazenamento com acesso a geração 2 de armazenamento do Data Lake com o portal do Azure, Azure PowerShell ou a CLI do Azure
services: storage
author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 12/06/2018
ms.author: jamesbak
ms.openlocfilehash: 2eb57268aece081423b6b0beaa314a244b6fdd8f
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57456011"
---
# <a name="quickstart-create-an-azure-data-lake-storage-gen2-storage-account"></a>Início rápido: Criar uma conta de armazenamento de geração 2 de armazenamento do Azure Data Lake

Geração de armazenamento 2 do Azure Data Lake [oferece suporte a um serviço de espaço de nomes hierárquico](data-lake-storage-introduction.md) que fornece um nativo baseada no Active adaptado para trabalhar com o Hadoop Distributed File System (HDFS) do sistema de ficheiros. O acesso aos dados de Armazenamento do Data Lake Ger2 a partir do HDFS está disponível através do [controlador ABFS](data-lake-storage-abfs-driver.md).

Este início rápido demonstra como criar uma conta com o [portal do Azure](https://portal.azure.com/), o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) ou através da [CLI do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

|           | Pré-requisito |
|-----------|--------------|
|Portal     | Nenhuma         |
|PowerShell | Este início rápido requer a versão de Az.Storage de módulo do PowerShell **0,7** ou posterior. Para encontrar a versão atual, execute o `Get-Module -ListAvailable Az.Storage` comando. Se depois de executar este comando, não existem resultados são apresentados ou se uma versão diferente do **0,7** for apresentada, tem de atualizar o módulo do powershell. Consulte a [atualizar o módulo do powershell](#upgrade-your-powershell-module) secção deste guia.
|CLI        | Pode iniciar sessão no Azure e executar comandos da CLI do Azure de uma das seguintes formas: <ul><li>Pode executar comandos da CLI no portal do Azure, no Azure Cloud Shell </li><li>Pode instalar a CLI e executar os respetivos comandos localmente</li></ul>|

Ao trabalhar na linha de comandos, tem a opção de executar o Azure Cloud shell ou instalar a CLI localmente.

### <a name="use-azure-cloud-shell"></a>Utilizar o Azure Cloud Shell

O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Tem a CLI do Azure pré-instalada e configurada para ser utilizada com a sua conta. Clique no botão **Cloud Shell** no menu do canto superior direito do portal do Azure:

[![Cloud Shell](./media/data-lake-storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

O botão inicia uma shell interativa que pode utilizar para executar os passos neste guia de introdução:

[![Captura de ecrã que mostra a janela do Cloud Shell no portal](./media/data-lake-storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Instalar a CLI localmente

Também pode instalar e utilizar a CLI do Azure localmente. Este início rápido requer a execução da versão 2.0.38 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-storage-account-with-azure-data-lake-storage-gen2-enabled"></a>Criar uma conta de armazenamento com o Azure Data Lake Storage Gen2 ativado

Antes de criar uma conta, primeiro tem de criar um grupo de recursos que age como um contentor lógico para contas de armazenamento ou outros recursos criados do Azure. Se pretende limpar os recursos criados por este guia de introdução, basta eliminar o grupo de recursos. Ao eliminar o grupo de recursos também elimina a conta de armazenamento associada e quaisquer outros recursos associados ao grupo de recursos. Para obter mais informações sobre os grupos de recursos, veja [Descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

> [!NOTE]
> Tem de criar novas contas de armazenamento como o tipo **StorageV2 (V2 de fins gerais)**, para tirar partido das funcionalidades de Armazenamento do Data Lake Ger2.  

Para obter mais informações sobre as contas de armazenamento, veja [Azure Storage account overview](../common/storage-account-overview.md) (Descrição geral da Conta de Armazenamento).

Para atribuir um nome à sua conta de armazenamento, mantenha estas regras em mente:

- Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas.
- O nome da sua conta do Storage tem de ser exclusivo no Azure. Duas contas de armazenamento não podem ter o mesmo nome.

## <a name="create-an-account-using-the-azure-portal"></a>Criar uma conta no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com).

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um grupo de recursos no portal do Azure, siga estes passos:

1. No portal do Azure, expanda o menu no lado esquerdo para abrir o menu de serviços e escolha **Grupos de Recursos**.
2. Clique no botão **Adicionar** para adicionar um novo grupo de recursos.
3. Introduza um nome para o novo grupo de recursos.
4. Selecione a subscrição na que vai criar o novo grupo de recursos.
5. Escolha a localização para o grupo de recursos.
6. Clique no botão **Criar**.  

   ![Captura de ecrã que mostra a criação do grupo de recursos no portal do Azure](./media/data-lake-storage-quickstart-create-account/create-resource-group.png)

### <a name="create-a-general-purpose-v2-storage-account"></a>Criar uma conta de armazenamento v2 para fins gerais

Para criar uma conta de armazenamento para fins gerais v2 no portal do Azure, siga estes passos:

> [!NOTE]
> O espaço de nomes hierárquico está atualmente disponível em todas as regiões públicas.

1. No portal do Azure, expanda o menu do lado esquerdo para abrir o menu de serviços e escolha **Todos os Serviços**. Em seguida, desloque para baixo até **Armazenamento**e escolha **Contas de armazenamento**. Na janela **Contas de Armazenamento** que é apresentada, escolha **Adicionar**.
2. Selecione seu **subscrição** e o **grupo de recursos** que criou anteriormente.
3. Introduza um nome para a conta do Storage.
4. Defina a **Localização** para **E.U.A. Oeste 2**
5. Deixe estes campos nas predefinições: **Desempenho**, **tipo de conta**, **replicação**, **camada de acesso**.
6. Escolha a subscrição na qual pretende criar a conta de armazenamento.
7. Selecione **seguinte: Advanced >**
8. Deixe os valores em **SECURITY** e **redes virtuais** campos definidos para as predefinições.
9. Na **geração 2 de armazenamento do Data Lake** secção conjunto **espaço de nomes hierárquico** para **ativado**.
10. Clique em **rever + criar** para criar a conta de armazenamento.

    ![Captura de ecrã que mostra a criação da conta de armazenamento no portal do Azure](./media/data-lake-storage-quickstart-create-account/azure-data-lake-storage-account-create-advanced.png)

A conta de armazenamento está agora criada através do portal.

### <a name="clean-up-resources"></a>Limpar recursos

Para remover um grupo de recursos através do portal do Azure:

1. No portal do Azure, expanda o menu no lado esquerdo para abrir o menu de serviços e escolha **Grupos de Recursos**, para apresentar a lista dos seus grupos de recursos.
2. Encontre o grupo de recursos a eliminar e clique com o botão direito do rato em **Mais** (**...** ) no lado direito da lista.
3. Selecione **Eliminar grupo de recursos** e confirme.

## <a name="create-an-account-using-powershell"></a>Criar uma conta com o PowerShell

Em primeiro lugar, instale a versão mais recente dos [PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget) módulo.

Em seguida, atualizar o módulo do powershell, inicie sessão na sua subscrição do Azure, crie um grupo de recursos e, em seguida, criar uma conta de armazenamento.

### <a name="upgrade-your-powershell-module"></a>Atualizar o módulo do powershell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para interagir com a geração 2 de armazenamento do Data Lake com o PowerShell, terá de instalar a versão do módulo Az.Storage **0,7** ou posterior.

Comece por abrir uma sessão do PowerShell com permissões elevadas.

Instalar o módulo de Az.Storage

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowPrerelease -AllowClobber -Force
```

### <a name="log-in-to-your-azure-subscription"></a>Inicie sessão na sua subscrição do Azure

Utilize o `Login-AzAccount` de comando e siga na tela direções para autenticar.

```powershell
Login-AzAccount
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um novo grupo de recursos com o PowerShell, utilize o [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) comando: 

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

Para criar uma conta de armazenamento para fins gerais v2 do PowerShell com armazenamento localmente redundante (LRS), utilize o [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) comando:

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

Para remover o grupo de recursos e respetivos recursos associados, incluindo a nova conta de armazenamento, utilize o [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) comando: 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-an-account-using-azure-cli"></a>Criar uma conta com a CLI do Azure

Para iniciar o Azure Cloud Shell, inicie sessão para o [portal do Azure](https://portal.azure.com).

Se quiser iniciar sessão na sua instalação local da CLI, execute o comando de início de sessão:

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-data-lake-gen-2"></a>Adicionar a extensão da CLI para o Azure Data Lake Gen 2

Para interagir com a geração 2 de armazenamento do Data Lake com a CLI, terá de adicionar uma extensão ao seu shell.

Para tal, introduza o seguinte comando ao utilizar o Cloud Shell ou um shell local: `az extension add --name storage-preview`

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

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou uma conta de armazenamento com capacidades do Data Lake Storage Gen2. Para saber como carregar e transferir blobs para e da sua conta de armazenamento, consulte o tópico seguinte.

* [AzCopy V10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
