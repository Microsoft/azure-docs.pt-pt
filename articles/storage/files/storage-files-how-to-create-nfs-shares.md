---
title: Criar uma partilha NFS - Azure Files (pré-visualização)
description: Saiba como criar uma partilha de ficheiros Azure que pode ser montada usando o protocolo sistema de ficheiros de rede.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/22/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: dc23dec8a8d59a7762e93cdfaa2a39d824506e7b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382128"
---
# <a name="how-to-create-an-nfs-share"></a>Como criar uma quota NFS
As ações de ficheiros Azure são totalmente geridas por ações de ficheiros que vivem na nuvem. Este artigo abrange a criação de uma partilha de ficheiros que utiliza o protocolo NFS. Para obter mais informações sobre ambos os protocolos, consulte os [protocolos de partilha de ficheiros Azure](storage-files-compare-protocols.md).

## <a name="limitations"></a>Limitações
[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Disponibilidade regional
[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Pré-requisitos
- As ações da NFS só podem ser acedidas a partir de redes fidedignas. As ligações à sua quota NFS devem ter origem numa das seguintes fontes:
    - Ou [cria um ponto final privado](storage-files-networking-endpoints.md#create-a-private-endpoint) (recomendado) ou restringe o acesso ao seu ponto final [público.](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - [Configure uma VPN ponto-a-local (P2S) no Linux para utilização com ficheiros Azure](storage-files-configure-p2s-vpn-linux.md).
    - [Configure uma VPN site-to-site para utilização com ficheiros Azure](storage-files-configure-s2s-vpn.md).
    - Configurar [ExpressRoute](../../expressroute/expressroute-introduction.md).

- Se pretender utilizar o Azure CLI, [instale a versão mais recente](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).

## <a name="register-the-nfs-41-protocol"></a>Registar o protocolo NFS 4.1
Se estiver a utilizar o módulo Azure PowerShell ou o Azure CLI, registe a sua função utilizando os seguintes comandos:

# <a name="portal"></a>[Portal](#tab/azure-portal)
Utilize o Azure PowerShell ou o Azure CLI para registar a função NFS 4.1 para ficheiros Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
# Connect your PowerShell session to your Azure account, if you have not already done so.
Connect-AzAccount

# Set the actively selected subscription, if you have not already done so.
$subscriptionId = "<yourSubscriptionIDHere>"
$context = Get-AzSubscription -SubscriptionId $subscriptionId
Set-AzContext $context

# Register the NFS 4.1 feature with Azure Files to enable the preview.
Register-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares 
    
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
# Connect your Azure CLI to your Azure account, if you have not already done so.
az login

# Provide the subscription ID for the subscription where you would like to 
# register the feature
subscriptionId="<yourSubscriptionIDHere>"

az feature register \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId

az provider register \
    --namespace Microsoft.Storage
```

---

A aprovação do registo pode demorar até uma hora. Para verificar se o registo está completo, utilize os seguintes comandos:

# <a name="portal"></a>[Portal](#tab/azure-portal)
Utilize a Azure PowerShell ou a Azure CLI para verificar o registo da funcionalidade NFS 4.1 para ficheiros Azure. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Get-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
az feature show \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId
```

---

## <a name="create-a-filestorage-storage-account"></a>Criar uma conta de armazenamento de fileStorage
Atualmente, as ações NFS 4.1 só estão disponíveis como ações de ficheiros premium. Para implementar uma partilha de ficheiros premium com suporte ao protocolo NFS 4.1, tem primeiro de criar uma conta de armazenamento de FileStorage. Uma conta de armazenamento é um objeto de alto nível em Azure que representa um conjunto partilhado de armazenamento que pode ser usado para implementar várias partilhas de ficheiros Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para criar uma conta de armazenamento fileStorage, navegue para o portal Azure.

1. No portal Azure, selecione **Contas de Armazenamento** no menu esquerdo.

    ![Página principal do portal Azure seleciona conta de armazenamento](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

2. Na janela **Contas de Armazenamento** que é apresentada, escolha **Adicionar**.
3. Selecione a subscrição na qua pretende criar a conta de armazenamento.
4. Selecione o grupo de recursos para criar a conta de armazenamento

5. A seguir, introduza um nome para a sua conta de armazenamento. O nome que escolher tem de ser exclusivo em todo o Azure. O nome também tem de ter entre 3 e 24 carateres de comprimento e apenas pode incluir números e letras minúsculas.
6. Selecione uma localização para a sua conta de armazenamento ou utilize a localização predefinida.
7. Para o **desempenho** selecione **Premium**.

    Tem de selecionar **Premium** for **FileStorage** para ser uma opção disponível no **dropdown do tipo Conta.**

8. Selecione **tipo de Conta** e escolha O Armazenamento de **Ficheiros.**
9. Deixe **a replicação** definida para o seu valor padrão de **armazenamento localmente redundante (LRS)**.

    ![Como criar uma conta de armazenamento para uma parte de ficheiro premium](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

10. Selecione **Rever + Criar** para rever as definições de conta de armazenamento e criar a conta.
11. Selecione **Criar**.

Uma vez criado o seu recurso de conta de armazenamento, navegue até ele.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para criar uma conta de armazenamento FileStorage, abra um pedido powerShell e execute os seguintes comandos, lembrando-se de substituir `<resource-group>` e `<storage-account>` pelos valores apropriados para o seu ambiente.

```powershell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$location = "westus2"

$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $location `
    -Kind FileStorage
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
Para criar uma conta de armazenamento FileStorage, abra o seu terminal e execute os seguintes comandos, lembrando-se de substituir `<resource-group>` e `<storage-account>` pelos valores adequados para o seu ambiente.

```azurecli-interactive
resourceGroup="<resource-group>"
storageAccount="<storage-account>"
location="westus2"

az storage account create \
    --resource-group $resourceGroup \
    --name $storageAccount \
    --location $location \
    --sku Premium_LRS \
    --kind FileStorage
```
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
    
   Para atualizar a sua versão do PowerShell, consulte [a atualização do Windows PowerShell existente](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true#upgrading-existing-windows-powershell)
    
1. Instale a versão mais recente do módulo PowershellGet.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. Feche e, em seguida, reabra a consola PowerShell.

1. Instale a versão do módulo de pré-visualização **Az.Storage** **2.5.2-preview**.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Para obter mais informações sobre como instalar módulos PowerShell, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps?view=azps-3.0.0&preserve-view=true)
   
1. Para criar uma partilha de ficheiros premium com o módulo Azure PowerShell, utilize o cmdlet [New-AzRmStorageShare.](/powershell/module/az.storage/new-azrmstorageshare)

    > [!NOTE]
    > As ações de ficheiros premium são faturadas utilizando um modelo provisionado. A parte especifica a parte abaixo o tamanho `QuotaGiB` previsto. Para obter mais informações, consulte [compreender o modelo provisionado](understanding-billing.md#provisioned-model) e a página de preços dos [Ficheiros Azure](https://azure.microsoft.com/pricing/details/storage/files/).

    ```powershell
    New-AzRmStorageShare `
        -StorageAccount $storageAccount `
        -Name myshare `
        -EnabledProtocol NFS `
        -RootSquash RootSquash `
        -QuotaGiB 1024
    ```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
Para criar uma partilha de ficheiros premium com o Azure CLI, utilize o comando [de partilha de armazenamento az.](/cli/azure/storage/share-rm)

> [!NOTE]
> As ações de ficheiros premium são faturadas utilizando um modelo provisionado. A parte especifica a parte abaixo o tamanho `quota` previsto. Para obter mais informações, consulte [compreender o modelo provisionado](understanding-billing.md#provisioned-model) e a página de preços dos [Ficheiros Azure](https://azure.microsoft.com/pricing/details/storage/files/).

```azurecli-interactive
az storage share-rm create \
    --resource-group $resourceGroup \
    --storage-account $storageAccount \
    --name "myshare" \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --quota 1024
```
---

## <a name="next-steps"></a>Passos seguintes
Agora que criou uma parte da NFS, para usá-la, tem que montá-la no seu cliente Linux. Para mais detalhes, consulte [como montar uma partilha NFS](storage-files-how-to-mount-nfs-shares.md).

Se tiver algum problema, consulte [as ações de ficheiros Troubleshoot Azure NFS](storage-troubleshooting-files-nfs.md).