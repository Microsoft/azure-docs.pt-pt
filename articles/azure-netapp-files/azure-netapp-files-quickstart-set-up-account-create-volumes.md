---
title: Início rápido-configurar Azure NetApp Files e criar um volume de NFS | Microsoft Docs
description: Início rápido – descreve como configurar rapidamente Azure NetApp Files e criar um volume.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/12/2019
ms.author: b-juche
ms.openlocfilehash: 06ebe8ed22453289fa02c238d9b2e6ef13191f2a
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888538"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>Início rápido: configurar Azure NetApp Files e criar um volume do NFS 

Este artigo mostra como configurar rapidamente Azure NetApp Files e criar um volume. 

Neste guia de início rápido, você configurará os seguintes itens:

- Registro para Azure NetApp Files e provedor de recursos da NetApp
- Uma conta do NetApp
- Um pool de capacidade
- Um volume do NFS para Azure NetApp Files

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar 

> [!IMPORTANT] 
> Você precisa receber acesso ao serviço de Azure NetApp Files.  Para solicitar acesso ao serviço, consulte a [página de envio Azure NetApp files Waitlist](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u).  Você deve aguardar um email de confirmação oficial da equipe de Azure NetApp Files antes de continuar. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

---

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Registrar-se para o provedor de recursos Azure NetApp Files e NetApp

> [!NOTE]
> O processo de registro pode levar algum tempo para ser concluído.
>

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Para obter as etapas de registro usando o portal, abra uma sessão de Cloud Shell conforme indicado acima e siga estas CLI do Azure etapas:

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Este artigo de instruções requer o módulo Azure PowerShell AZ Version 2.6.0 ou posterior. Execute `Get-Module -ListAvailable Az` para encontrar a versão atual. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-Az-ps)(Instalar o módulo do Azure PowerShell). Se preferir, você pode usar Cloud Shell console em uma sessão do PowerShell.

1. Em um prompt de comando do PowerShell (ou sessão Cloud Shell do PowerShell), especifique a assinatura que está na lista de permissões para Azure NetApp Files:
    ```powershell-interactive
    Select-AzSubscription -Subscription <subscriptionId>
    ```

2. Registrar o provedor de recursos do Azure:
    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.NetApp
    ```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

---

## <a name="create-a-netapp-account"></a>Criar uma conta NetApp

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Na caixa de pesquisa do portal do Azure, digite **Azure NetApp files** e, em seguida, selecione **Azure NetApp files** na lista exibida.

      ![Selecionar Azure NetApp Files](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Clique em **+ Adicionar** para criar uma nova conta NetApp.

     ![Criar nova conta do NetApp](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. Na janela nova conta do NetApp, forneça as seguintes informações: 
   1. Insira **myaccount1** para o nome da conta. 
   2. Selecione a sua subscrição.
   3. Selecione **criar novo** para criar um novo grupo de recursos. Insira **myRG1** para o nome do grupo de recursos. Clique em **OK**. 
   4. Selecione o local da sua conta.  

      ![Janela nova conta da NetApp](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)  

      ![Janela do grupo de recursos](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Clique em **criar** para criar sua nova conta do NetApp.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Defina algumas variáveis para que possamos consultá-las em todo o restante dos exemplos:

    ```powershell-interactive
    $resourceGroup = "myRG1"
    $location = "eastus"
    $anfAccountName = "myaccount1"
    ``` 

    > [!NOTE]
    > Consulte os [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) para obter uma lista de regiões com suporte.
    > Para obter o nome da região que é compatível com nossas ferramentas de linha de comando, use `Get-AzLocation | select Location`
    >

1. Crie um novo grupo de recursos usando o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) :

    ```powershell-interactive
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

2. Crie Azure NetApp Files conta com o comando [New-AzNetAppFilesAccount](/powershell/module/az.netappfiles/New-AzNetAppFilesAccount) :
   
    ```powershell-interactive
    New-AzNetAppFilesAccount -ResourceGroupName $resourceGroup -Location $location -Name $anfAccountName
    ```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Defina algumas variáveis para que possamos consultá-las em todo o restante dos exemplos:

    ```azurecli-interactive
    RESOURCE_GROUP="myRG1"
    LOCATION="eastus"
    ANF_ACCOUNT_NAME="myaccount1"
    ``` 

    > [!NOTE]
    > Consulte os [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) para obter uma lista de regiões com suporte.
    > Para obter o nome da região que é compatível com nossas ferramentas de linha de comando, use `az account list-locations -query "[].{Region:name}" --out table`
    >

2. Crie um novo grupo de recursos usando o comando [AZ Group Create](/cli/azure/group#az-group-create) :

    ```azurecli-interactive
    az group create \
        --name $RESOURCE_GROUP \
        --location $LOCATION
    ```

3. Crie Azure NetApp Files conta com o comando [AZ netappfiles Account Create](/cli/azure/netappfiles/account#az-netappfiles-account-create) :
   
    ```azurecli-interactive
    az netappfiles account create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME
    ```
---

## <a name="set-up-a-capacity-pool"></a>Configurar um conjunto de capacidade

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Na folha gerenciamento de Azure NetApp Files, selecione sua conta do NetApp (**myaccount1**).

    ![Selecionar conta do NetApp](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)  

2. Na folha gerenciamento de Azure NetApp Files da sua conta do NetApp, clique em **pools de capacidade**.

    ![Clique em pools de capacidade](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)  

3. Clique em **+ Adicionar pools**. 

    ![Clique em Adicionar pools](../media/azure-netapp-files/azure-netapp-files-click-add-pools.png)  

4. Forneça informações para o pool de capacidade: 
    1. Insira **mypool1** como o nome do pool.
    2. Selecione **Premium** para o nível de serviço. 
    3. Especifique **4 (TIB)** como o tamanho do pool. 

5. Clique em **OK**.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Definindo algumas novas variáveis para referência futura

    ```powershell-interactive
    $poolName = "mypool1"
    $poolSizeBytes = 4398046511104 # 4TiB
    $serviceLevel = "Premium" # Valid values are Standard, Premium and Ultra
    ```

1. Criar um novo pool de capacidade usando o [New-AzNetAppFilesPool](/powershell/module/az.netappfiles/new-aznetappfilespool)

    ```powershell-interactive
    New-AzNetAppFilesPool -ResourceGroupName $resourceGroup -Location $location -AccountName $anfAccountName -Name $poolName -PoolSize $poolSizeBytes -ServiceLevel $serviceLevel
    ```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Definindo algumas novas variáveis para referência futura

    ```azurecli-interactive
    POOL_NAME="mypool1"
    POOL_SIZE_TiB=4 # Size in Azure CLI needs to be in TiB unit (minimum 4 TiB)
    SERVICE_LEVEL="Premium" # Valid values are Standard, Premium and Ultra
    ```

2. Criar um novo pool de capacidade usando o [AZ netappfiles pool Create](/cli/azure/netappfiles/pool#az-netappfiles-pool-create) 

    ```azurecli-interactive
    az netappfiles pool create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --size $POOL_SIZE_TiB \
        --service-level $SERVICE_LEVEL
    ```

---

## <a name="create-nfs-volume-for-azure-netapp-files"></a>Criar volume NFS para Azure NetApp Files

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Na folha gerenciamento de Azure NetApp Files da sua conta do NetApp, clique em **volumes**.

    ![Clique em volumes](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. Clique em **+ Adicionar volume**.

    ![Clique em Adicionar volumes](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. Na janela criar um volume, forneça informações para o volume: 
   1. Insira **myvol1** como o nome do volume. 
   2. Selecione o pool de capacidade (**mypool1**).
   3. Use o valor padrão para cota. 
   4. Em rede virtual, clique em **criar novo** para criar uma nova vnet (rede virtual) do Azure.  Em seguida, preencha as seguintes informações:
       * Insira **myvnet1** como o nome da vnet.
       * Especifique um espaço de endereço para sua configuração, por exemplo, 10.7.0.0/16
       * Insira **myANFsubnet** como o nome da sub-rede.
       * Especifique o intervalo de endereços de sub-rede, por exemplo, 10.7.0.0/24. Você não pode compartilhar a sub-rede dedicada com outros recursos.
       * Selecione **Microsoft. NetApp/volumes** para delegação de sub-rede.
       * Clique em **OK** para criar a vnet.
   5. Em sub-rede, selecione a vnet recentemente criada (**myvnet1**) como a sub-rede delegada.

      ![Criar uma janela de volume](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)  

      ![Janela criar rede virtual](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)  

4. Clique em **protocolo**e, em seguida, conclua as seguintes ações: 
    * Selecione **NFS** como o tipo de protocolo para o volume.  
    * Insira **myfilepath1** como o caminho do arquivo que será usado para criar o caminho de exportação para o volume.  
    * Selecione a versão do NFS (**NFSv3** ou **nfsv 4.1**) para o volume.  
      Consulte [considerações](azure-netapp-files-create-volumes.md#considerations) e práticas [recomendadas](azure-netapp-files-create-volumes.md#best-practice) sobre as versões do NFS. 
      
> [!IMPORTANT] 
> O acesso ao recurso NFSv 4.1 requer a lista de permissões.  Para solicitar a lista de permissões, envie uma solicitação para <anffeedback@microsoft.com>. 

  ![Especificar o protocolo NFS para início rápido](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. Clique em **Rever + criar**.

    ![Revisar e criar janela](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

6. Examine as informações do volume e clique em **criar**.  
    O volume criado aparece na folha volumes.

    ![Volume criado](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Crie uma delegação de sub-rede para "Microsoft. NetApp/volumes" com o comando [New-AzDelegation](/powershell/module/az.network/new-azdelegation) .

    ```powershell-interactive
    $anfDelegation = New-AzDelegation -Name ([guid]::NewGuid().Guid) -ServiceName "Microsoft.NetApp/volumes"
    ```

2. Crie uma configuração de sub-rede usando o comando [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) .

    ```powershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "myANFSubnet" -AddressPrefix "10.7.0.0/24" -Delegation $anfDelegation
    ```

3. Crie a rede virtual usando o comando [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) .
    
    ```powershell-interactive
    $vnet = New-AzVirtualNetwork -Name "myvnet1" -ResourceGroupName $resourceGroup -Location $location -AddressPrefix "10.7.0.0/16" -Subnet $subnet
    ```

4. Crie o volume usando o comando [New-AzNetAppFilesVolume](/powershell/module/az.netappfiles/new-aznetappfilesvolume) .
   
    ```powershell-interactive
    $volumeSizeBytes = 1099511627776 # 100GiB
    $subnetId = $vnet.Subnets[0].Id

    New-AzNetAppFilesVolume -ResourceGroupName $resourceGroup `
        -Location $location `
        -AccountName $anfAccountName `
        -PoolName $poolName `
        -Name "myvol1" `
        -UsageThreshold $volumeSizeBytes `
        -SubnetId $subnetId `
        -CreationToken "myfilepath1" `
        -ServiceLevel $serviceLevel `
        -ProtocolType NFSv3
    ```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Definição de algumas variáveis para uso posterior.
    
    ```azurecli-interactive
    VNET_NAME="myvnet1"
    SUBNET_NAME="myANFSubnet"
    ```

1. Crie uma rede virtual sem sub-rede usando o comando [AZ Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create) .
    
    ```azurecli-interactive
    az network vnet create \
        --resource-group $RESOURCE_GROUP \
        --name $VNET_NAME \
        --location $LOCATION \
        --address-prefix "10.7.0.0/16"

    ```

2. Crie uma sub-rede delegada usando o comando [AZ Network vnet subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) .

    ```azurecli-interactive
    az network vnet subnet create \
        --resource-group $RESOURCE_GROUP \
        --vnet-name $VNET_NAME \
        --name $SUBNET_NAME \
        --address-prefixes "10.7.0.0/24" \
        --delegations "Microsoft.NetApp/volumes"
    ```

3. Crie o volume usando o comando [AZ netappfiles volume Create](/cli/azure/netappfiles/volume#az-netappfiles-volume-create) .
   
    ```azurecli-interactive
    VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
    SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
    VOLUME_SIZE_GiB=100 # 100 GiB
    UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within subscription and region

    az netappfiles volume create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --name "myvol1" \
        --service-level $SERVICE_LEVEL \
        --vnet $VNET_ID \
        --subnet $SUBNET_ID \
        --usage-threshold $VOLUME_SIZE_GiB \
        --file-path $UNIQUE_FILE_PATH \
        --protocol-types "NFSv3"
    ```

---

## <a name="clean-up-resources"></a>Limpar recursos

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Quando terminar e se desejar, você poderá excluir o grupo de recursos. A ação de excluir um grupo de recursos é irreversível.  

> [!IMPORTANT]
> Todos os recursos dentro dos grupos de recursos serão excluídos permanentemente e não poderão ser desfeitos. 

1. Na caixa de pesquisa do portal do Azure, digite **Azure NetApp files** e, em seguida, selecione **Azure NetApp files** na lista exibida.

2. Na lista de assinaturas, clique no grupo de recursos (myRG1) que você deseja excluir. 

    ![Navegar até grupos de recursos](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. Na página grupo de recursos, clique em **excluir grupo de recursos**.

    ![Eliminar grupo de recursos](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png) 

    É aberta uma janela com um aviso sobre os recursos que serão eliminados com o grupo de recursos.

4. Insira o nome do grupo de recursos (myRG1) para confirmar que deseja excluir permanentemente o grupo de recursos e todos os recursos nele e clique em **excluir**.

    ![Eliminar grupo de recursos](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png ) 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Quando terminar e se desejar, você poderá excluir o grupo de recursos. A ação de excluir um grupo de recursos é irreversível.  

> [!IMPORTANT]
> Todos os recursos dentro dos grupos de recursos serão excluídos permanentemente e não poderão ser desfeitos.

1. Exclua o grupo de recursos usando o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) .
   
    ```powershell-interactive
    Remove-AzResourceGroup -Name $resourceGroup
    ```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Quando terminar e se desejar, você poderá excluir o grupo de recursos. A ação de excluir um grupo de recursos é irreversível.  

> [!IMPORTANT]
> Todos os recursos dentro dos grupos de recursos serão excluídos permanentemente e não poderão ser desfeitos.

1. Exclua o grupo de recursos usando o comando [AZ Group Delete](/cli/azure/group#az-group-delete) .
   
    ```azurecli-interactive
    az group delete \
        --name $RESOURCE_GROUP
    ```
---

## <a name="next-steps"></a>Passos seguintes  

> [!div class="nextstepaction"]
> [Gerenciar volumes usando Azure NetApp Files](azure-netapp-files-manage-volumes.md)  
