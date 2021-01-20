---
title: Criar uma partilha NFS - Azure Files (pré-visualização)
description: Saiba como criar uma partilha de ficheiros Azure que pode ser montada usando o protocolo sistema de ficheiros de rede.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 323eed77d6f7a6ccfcdd0a7c7aecff3a125300dc
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602674"
---
# <a name="how-to-create-an-nfs-share"></a>Como criar uma quota NFS

As ações de ficheiros Azure são totalmente geridas por ações de ficheiros que vivem na nuvem. Podem ser acedidos utilizando o protocolo Bloco de Mensagens do Servidor ou o protocolo Sistema de Ficheiros de Rede (NFS). Este artigo abrange a criação de uma partilha de ficheiros que utiliza o protocolo NFS. Para obter mais informações sobre ambos os protocolos, consulte os [protocolos de partilha de ficheiros Azure](storage-files-compare-protocols.md).

## <a name="limitations"></a>Limitações

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Disponibilidade regional

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Criar uma [conta de arquitetorage](storage-how-to-create-premium-fileshare.md).

    > [!IMPORTANT]
    > As ações da NFS só podem ser acedidas a partir de redes fidedignas. As ligações à sua quota NFS devem ter origem numa das seguintes fontes:

    - Ou [cria um ponto final privado](storage-files-networking-endpoints.md#create-a-private-endpoint) (recomendado) ou restringe o acesso ao seu ponto final [público.](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - [Configure uma VPN ponto-a-local (P2S) no Linux para utilização com ficheiros Azure](storage-files-configure-p2s-vpn-linux.md).
    - [Configure uma VPN site-to-site para utilização com ficheiros Azure](storage-files-configure-s2s-vpn.md).
    - Configurar [ExpressRoute](../../expressroute/expressroute-introduction.md).
- Se pretender utilizar o Azure CLI, [instale a versão mais recente](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="register-the-nfs-41-protocol"></a>Registar o protocolo NFS 4.1

Se estiver a utilizar o módulo Azure PowerShell ou o Azure CLI, registe a sua função utilizando os seguintes comandos:

### <a name="powershell"></a>PowerShell

```azurepowershell
Connect-AzAccount
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context
Register-AzProviderFeature -FeatureName AllowNfsFileShares -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli
az login
az feature register --name AllowNfsFileShares \
                    --namespace Microsoft.Storage \
                    --subscription <yourSubscriptionIDHere>
az provider register --namespace Microsoft.Storage
```

## <a name="verify-feature-registration"></a>Verificar registo de funcionalidades

A aprovação do registo pode demorar até uma hora. Para verificar se o registo está completo, utilize os seguintes comandos:

### <a name="powershell"></a>PowerShell

```azurepowershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNfsFileShares
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli
az feature show --name AllowNfsFileShares --namespace Microsoft.Storage --subscription <yourSubscriptionIDHere>
```

## <a name="verify-storage-account-kind"></a>Verificar o tipo de conta de armazenamento

Atualmente, apenas as contas FileStorage podem criar ações NFS. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para verificar que tipo de conta de armazenamento tem, navegue para ele no portal Azure. Em seguida, a partir da sua conta de armazenamento, selecione **Propriedades.** A partir da lâmina de propriedades, examine o valor em **tipo Conta,** o valor deve ser **FileStorage**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para verificar se tem uma conta Detorage de Ficheiros, pode utilizar o seguinte comando:

```azurepowershell
$accountKind=Get-AzStorageAccount -ResourceGroupName "yourResourceGroup" -Name "yourStorageAccountName"
$accountKind.Kind
```

A saída deve ser **fileStorage**, se não for, então a sua conta de armazenamento é o tipo incorreto. Para criar uma conta **FileStorage,** consulte [Como criar uma partilha de ficheiros premium Azure](storage-how-to-create-premium-fileshare.md).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
Para verificar se tem uma conta Detorage de Ficheiros, pode utilizar o seguinte comando:

```azurecli
az storage account show -g yourResourceGroup -n yourStorageAccountName
```

A saída deve conter **"tipo": "FileStorage",** se não o fizer, então a sua conta de armazenamento é o tipo incorreto. Para criar uma conta **FileStorage,** consulte [Como criar uma partilha de ficheiros premium Azure](storage-how-to-create-premium-fileshare.md).

---
## <a name="create-an-nfs-share"></a>Criar uma partilha NFS

# <a name="portal"></a>[Portal](#tab/azure-portal)

Agora que criou uma conta FileStorage e configurar a rede, pode criar uma partilha de ficheiros NFS. O processo é semelhante à criação de uma quota SMB, seleciona **NFS** em vez de **SMB** ao criar a partilha.

1. Navegue para a sua conta de armazenamento e selecione **ações de ficheiros.**
1. Selecione **+ Partilha de ficheiros** para criar uma nova partilha de ficheiros.
1. Nomeie a sua parte do ficheiro, selecione uma capacidade a provisionada.
1. Para **o protocolo** selecione **NFS (pré-visualização)**.
1. Para **Root Squash** fazer uma seleção.

    - Squash raiz (padrão) - O acesso ao superuser remoto (raiz) é mapeado para UID (65534) e GID (65534).
    - Sem abóbora de raiz - O superuser remoto (raiz) recebe acesso como raiz.
    - Todas as abóboras - Todo o acesso ao utilizador está mapeado para UID (65534) e GID (65534).
    
1. Selecione **Criar**.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/create-nfs-file-share.png" alt-text="Screenshot da lâmina de criação de partilha de ficheiros":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Certifique-se de que a estrutura .NET está instalada. Consulte [o Download .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
 
1. Verifique se a versão do PowerShell que foi instalada é `5.1` ou superior utilizando o seguinte comando.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Para atualizar a sua versão do PowerShell, consulte [a atualização do Windows PowerShell existente](/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
1. Instale a versão mais recente do módulo PowershellGet.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. Feche e, em seguida, reabra a consola PowerShell.

1. Instale a versão do módulo de pré-visualização **Az.Storage** **2.5.2-preview**.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Para obter mais informações sobre como instalar módulos PowerShell, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps?view=azps-3.0.0)
   
1. Para criar uma partilha de ficheiros premium com o módulo Azure PowerShell, utilize o cmdlet [New-AzRmStorageShare.](/powershell/module/az.storage/new-azrmstorageshare)

> [!NOTE]
> As dimensões das ações provisidas são especificadas pela quota de ações, as ações de ficheiro são faturadas na dimensão prevista. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage/files/).

  ```powershell
  New-AzRmStorageShare `
   -ResourceGroupName $resourceGroupName `
   -StorageAccountName $storageAccountName `
   -Name myshare `
   -EnabledProtocol NFS `
   -RootSquash RootSquash `
   -Context $storageAcct.Context
  ```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para criar uma partilha de ficheiros premium com o Azure CLI, utilize o comando [de partilha de armazenamento az.](/cli/azure/storage/share-rm)

> [!NOTE]
> As dimensões das ações provisidas são especificadas pela quota de ações, as ações de ficheiro são faturadas na dimensão prevista. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage/files/).

```azurecli-interactive
az storage share-rm create \
    --storage-account $STORAGEACCT \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --name "myshare" 
```
---

## <a name="next-steps"></a>Passos seguintes

Agora que criou uma parte da NFS, para usá-la, tem que montá-la no seu cliente Linux. Para mais detalhes, consulte [como montar uma partilha NFS](storage-files-how-to-mount-nfs-shares.md).

Se tiver algum problema, consulte [as ações de ficheiros Troubleshoot Azure NFS](storage-troubleshooting-files-nfs.md).
