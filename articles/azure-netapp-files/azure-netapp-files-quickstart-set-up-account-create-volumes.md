---
title: 'Quickstart: Configurar ficheiros Azure NetApp e volume NFS'
description: Quickstart - Descreve como configurar rapidamente os Ficheiros Azure NetApp e criar um volume.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: quickstart
ms.date: 09/22/2020
ms.custom: devx-track-azurecli, subject-armqs
ms.openlocfilehash: 70441c3a1953fa1b6ebd69ef9cdb324d6cc04a5b
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336457"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>Quickstart: Configurar ficheiros Azure NetApp e criar um volume NFS

Este artigo mostra-lhe como configurar rapidamente os Ficheiros Azure NetApp e criar um volume.

Neste arranque rápido, irá configurar os seguintes itens:

- Registo para Ficheiros Azure NetApp e Provedor de Recursos NetApp
- Uma conta NetApp
- Uma piscina de capacidade
- Um volume NFS para ficheiros Azure NetApp

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

> [!IMPORTANT]
> É necessário ter acesso ao serviço Azure NetApp Files. Para solicitar o acesso ao serviço, consulte a página de submissão da [lista de espera Azure NetApp Files](https://aka.ms/azurenetappfiles).  Deve aguardar um e-mail oficial de confirmação da equipa do Azure NetApp Files antes de continuar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

---

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Registre-se para ficheiros Azure NetApp e Provedor de Recursos NetApp

> [!NOTE]
> O processo de registo pode demorar algum tempo a ser concluído.
>

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para as etapas de registo utilizando o Portal, abra uma sessão cloud Shell como indicado acima e siga estes passos Azure CLI:

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Este artigo requer o módulo Azure PowerShell versão 2.6.0 ou posterior. Execute `Get-Module -ListAvailable Az` para encontrar a versão atual. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-Az-ps)(Instalar o módulo do Azure PowerShell). Se preferir, pode utilizar a consola Cloud Shell numa sessão PowerShell.

1. Numa essão de comando PowerShell (ou sessão PowerShell Cloud Shell), especifique a subscrição que foi aprovada para ficheiros Azure NetApp:
    ```powershell-interactive
    Select-AzSubscription -Subscription <subscriptionId>
    ```

2. Registe-se o Fornecedor de Recursos Azure:
    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.NetApp
    ```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="template"></a>[Modelo](#tab/template)

Nenhum.

Utilize o portal Azure, PowerShell ou o Azure CLI para se registar para ficheiros Azure NetApp e o Fornecedor de Recursos NetApp.

Consulte [registo de ficheiros Azure NetApp](azure-netapp-files-register.md) para obter mais informações.

---

## <a name="create-a-netapp-account"></a>Criar uma conta NetApp

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Na caixa de pesquisa do portal Azure, insira **os Ficheiros Azure NetApp** e, em seguida, selecione **ficheiros Azure NetApp** da lista que aparece.

      ![Selecione ficheiros Azure NetApp](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Clique em **+ Adicionar** para criar uma nova conta NetApp.

     ![Criar nova conta NetApp](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. Na janela New NetApp Account, forneça as seguintes informações:
   1. **Insira a minha contagem1** para o nome da conta.
   2. Selecione a sua subscrição.
   3. Selecione **Criar novo** para criar um novo grupo de recursos. Insira **o myRG1** para o nome do grupo de recursos. Clique em **OK**.
   4. Selecione a localização da sua conta.

      ![Janela da nova conta NetApp](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)

      ![Janela do grupo de recursos](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Clique em **Criar** para criar a sua nova conta NetApp.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Defina algumas variáveis para que possamos referir-nos a elas ao longo dos restantes exemplos:

    ```powershell-interactive
    $resourceGroup = "myRG1"
    $location = "eastus"
    $anfAccountName = "myaccount1"
    ```

    > [!NOTE]
    > Consulte os [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) para obter uma lista de regiões apoiadas.
    > Para obter o nome da região que é suportado pelas nossas ferramentas de linha de comando, por favor use `Get-AzLocation | select Location`
    >

1. Criar um novo grupo de recursos utilizando o comando [New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

    ```powershell-interactive
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

2. Criar conta Azure NetApp Com comando [New-AzNetAppFilesAccount:](/powershell/module/az.netappfiles/New-AzNetAppFilesAccount)

    ```powershell-interactive
    New-AzNetAppFilesAccount -ResourceGroupName $resourceGroup -Location $location -Name $anfAccountName
    ```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Defina algumas variáveis para que possamos referir-nos a elas ao longo dos restantes exemplos:

    ```azurecli-interactive
    RESOURCE_GROUP="myRG1"
    LOCATION="eastus"
    ANF_ACCOUNT_NAME="myaccount1"
    ```

    > [!NOTE]
    > Consulte os [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) para obter uma lista de regiões apoiadas.
    > Para obter o nome da região que é suportado pelas nossas ferramentas de linha de comando, por favor use `az account list-locations --query "[].{Region:name}" --out table`
    >

2. Criar um novo grupo de recursos utilizando o [comando de criação do grupo AZ:](/cli/azure/group#az-group-create)

    ```azurecli-interactive
    az group create \
        --name $RESOURCE_GROUP \
        --location $LOCATION
    ```

3. Criar conta Azure NetApp Files com [conta az netappfiles criar](/cli/azure/netappfiles/account#az-netappfiles-account-create) comando:

    ```azurecli-interactive
    az netappfiles account create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME
    ```

# <a name="template"></a>[Modelo](#tab/template)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

O seguinte corte de código mostra como criar uma conta NetApp num modelo de Gestor de Recursos Azure (modelo ARM), utilizando o recurso [Microsoft.NetApp/netAppAccounts.](/azure/templates/microsoft.netapp/netappaccounts) Para executar o código, descarregue o [modelo ARM completo](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) do nosso repo GitHub.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="177-183":::

<!-- Block begins with "type": "Microsoft.NetApp/netAppAccounts", -->

---

## <a name="set-up-a-capacity-pool"></a>Configurar um conjunto de capacidade

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. A partir da lâmina de gestão de Ficheiros Azure NetApp, selecione a sua conta NetApp **(myaccount1** ).

    ![Selecione conta NetApp](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)

2. A partir da lâmina de gestão de Ficheiros Azure NetApp da sua conta NetApp, clique em **pools de capacidade**.

    ![Clique em piscinas de capacidade](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)

3. Clique **+ Adicione piscinas.**

    ![Clique em Adicionar piscinas](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Fornecer informações sobre o pool de capacidade:
    * **Insira a mypool1** como o nome da piscina.
    * Selecione **Premium** para o nível de serviço.
    * Especifique **4 (TiB)** como o tamanho da piscina.
    * Utilize o tipo **Auto** QoS.

5. Clique em **Criar**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Definição de algumas novas variáveis para referência futura

    ```powershell-interactive
    $poolName = "mypool1"
    $poolSizeBytes = 4398046511104 # 4TiB
    $serviceLevel = "Premium" # Valid values are Standard, Premium and Ultra
    ```

1. Criar um novo pool de capacidade utilizando o [New-AzNetAppFilesPool](/powershell/module/az.netappfiles/new-aznetappfilespool)

    ```powershell-interactive
    New-AzNetAppFilesPool -ResourceGroupName $resourceGroup -Location $location -AccountName $anfAccountName -Name $poolName -PoolSize $poolSizeBytes -ServiceLevel $serviceLevel
    ```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Definição de algumas novas variáveis para referência futura

    ```azurecli-interactive
    POOL_NAME="mypool1"
    POOL_SIZE_TiB=4 # Size in Azure CLI needs to be in TiB unit (minimum 4 TiB)
    SERVICE_LEVEL="Premium" # Valid values are Standard, Premium and Ultra
    ```

2. Crie um novo pool de capacidade usando a [piscina az netappfiles criar](/cli/azure/netappfiles/pool#az-netappfiles-pool-create)

    ```azurecli-interactive
    az netappfiles pool create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --size $POOL_SIZE_TiB \
        --service-level $SERVICE_LEVEL
    ```

# <a name="template"></a>[Modelo](#tab/template)

<!-- [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)] -->

O seguinte corte de código mostra como criar um pool de capacidade num modelo de Gestor de Recursos Azure (modelo ARM), utilizando o recurso [Microsoft.NetApp/netAppAccounts/capacityPools.](/azure/templates/microsoft.netapp/netappaccounts/capacitypools) Para executar o código, descarregue o [modelo ARM completo](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) do nosso repo GitHub.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="184-196":::

<!-- LN 185, block begins with  "type": "Microsoft.NetApp/netAppAccounts/capacityPools", -->

---

## <a name="create-nfs-volume-for-azure-netapp-files"></a>Criar volume NFS para ficheiros Azure NetApp

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. A partir da lâmina de gestão de Ficheiros Azure NetApp da sua conta NetApp, clique em **Volumes**.

    ![Clicar em Volumes](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)

2. Clique em **+ Adicionar volume**.

    ![Clique Em Adicionar volumes](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)

3. Na janela Criar um Volume, fornecer informações sobre o volume:
   1. Insira **o myvol1** como o nome do volume.
   2. Selecione a sua piscina de capacidade **(mypool1** ).
   3. Utilize o valor predefinido para quota.
   4. Em rede virtual, clique em **Criar novo** para criar uma nova rede virtual Azure (Vnet).  Em seguida, preencha as seguintes informações:
       * Insira **o myvnet1** como o nome Vnet.
       * Especifique um espaço de endereço para a sua definição, por exemplo, 10.7.0.0/16
       * **Insira a minha rede DENF** como o nome da sub-rede.
       * Especifique o intervalo de endereços da sub-rede, por exemplo, 10.7.0.0/24. Não pode partilhar a sub-rede dedicada com outros recursos.
       * Selecione **Microsoft.NetApp/volumes** para a delegação de sub-redes.
       * Clique **em OK** para criar o Vnet.
   5. Na sub-rede, selecione o Vnet recém-criado **(myvnet1)** como sub-rede de delegado.

      ![Criar uma janela de volume](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)

      ![Criar janela de rede virtual](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)

4. Clique **em Protocolo** e, em seguida, complete as seguintes ações:
    * Selecione **NFS** como o tipo de protocolo para o volume.
    * Insira **o myfilepath1** como o caminho do ficheiro que será usado para criar o caminho de exportação para o volume.
    * Selecione a versão NFS **(NFSv3** ou **NFSv4.1** ) para o volume.
      Consulte [considerações](azure-netapp-files-create-volumes.md#considerations) e boas [práticas](azure-netapp-files-create-volumes.md#best-practice) sobre versões NFS.

    ![Especificar o protocolo NFS para arranque rápido](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. Clique em **Rever + criar**.

    ![Rever e criar janela](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)

6. Reveja as informações para o volume e, em seguida, clique em **Criar**.
    O volume criado aparece na lâmina Volumes.

    ![Volume criado](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Crie uma delegação de sub-redes para "Microsoft.NetApp/volumes" com comando [New-AzDelegation.](/powershell/module/az.network/new-azdelegation)

    ```powershell-interactive
    $anfDelegation = New-AzDelegation -Name ([guid]::NewGuid().Guid) -ServiceName "Microsoft.NetApp/volumes"
    ```

2. Crie uma configuração de sub-rede utilizando o comando [New-AzVirtualNetWorkSubnetConfig.](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)

    ```powershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "myANFSubnet" -AddressPrefix "10.7.0.0/24" -Delegation $anfDelegation
    ```

3. Crie a rede virtual utilizando o comando [New-AzVirtualNetwork.](/powershell/module/az.network/new-azvirtualnetwork)

    ```powershell-interactive
    $vnet = New-AzVirtualNetwork -Name "myvnet1" -ResourceGroupName $resourceGroup -Location $location -AddressPrefix "10.7.0.0/16" -Subnet $subnet
    ```

4. Crie o volume utilizando o comando [New-AzNetAppFilesVolume.](/powershell/module/az.netappfiles/new-aznetappfilesvolume)

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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Definindo algumas variáveis para posterior utilização.

    ```azurecli-interactive
    VNET_NAME="myvnet1"
    SUBNET_NAME="myANFSubnet"
    ```

1. Crie rede virtual sem sub-rede utilizando o [vnet de rede az criar](/cli/azure/network/vnet#az-network-vnet-create) comando.

    ```azurecli-interactive
    az network vnet create \
        --resource-group $RESOURCE_GROUP \
        --name $VNET_NAME \
        --location $LOCATION \
        --address-prefix "10.7.0.0/16"

    ```

2. Crie uma sub-rede delegada utilizando [o comando de criação de rede vnet de rede az.](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create)

    ```azurecli-interactive
    az network vnet subnet create \
        --resource-group $RESOURCE_GROUP \
        --vnet-name $VNET_NAME \
        --name $SUBNET_NAME \
        --address-prefixes "10.7.0.0/24" \
        --delegations "Microsoft.NetApp/volumes"
    ```

3. Crie o volume utilizando o [volume de redes az criar](/cli/azure/netappfiles/volume#az-netappfiles-volume-create) comando.

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

# <a name="template"></a>[Modelo](#tab/template)

<!-- [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)] -->

Os seguintes snippets de código mostram como configurar um VNet e criar um volume de Ficheiros Azure NetApp num modelo de Gestor de Recursos Azure (modelo ARM). A configuração VNet utiliza o recurso [Microsoft.Network/virtualNetworks.](/azure/templates/Microsoft.Network/virtualNetworks) A criação de volume utiliza o recurso [Microsoft.NetApp/netAppAccounts/capacityPools/volumes.](/azure/templates/microsoft.netapp/netappaccounts/capacitypools/volumes) Para executar o código, descarregue o [modelo ARM completo](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) do nosso repo GitHub.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="148-176":::

<!-- Block begins with  "type": "Microsoft.Network/virtualNetworks", -->

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="197-229":::

<!-- Block begins with  "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes", -->

---

## <a name="clean-up-resources"></a>Limpar os recursos

# <a name="portal"></a>[Portal](#tab/azure-portal)

Quando terminar e se quiser, pode eliminar o grupo de recursos. A ação de eliminar um grupo de recursos é irreversível.

> [!IMPORTANT]
> Todos os recursos dentro dos grupos de recursos serão permanentemente eliminados e não podem ser desfeitos.

1. Na caixa de pesquisa do portal Azure, insira **os Ficheiros Azure NetApp** e, em seguida, selecione **ficheiros Azure NetApp** da lista que aparece.

2. Na lista de subscrições, clique no grupo de recursos (myRG1) que pretende eliminar.

    ![Navegue para grupos de recursos](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. Na página do grupo de recursos, clique **em Eliminar o grupo de recursos**.

    ![Screenshot que realça o botão do grupo de recursos Delete.](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png)

    É aberta uma janela com um aviso sobre os recursos que serão eliminados com o grupo de recursos.

4. Insira o nome do grupo de recursos (myRG1) para confirmar que pretende eliminar permanentemente o grupo de recursos e todos os recursos nele e, em seguida, clique em **Eliminar**.

    ![Confirmar a eliminação do grupo de recursos](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png )

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Quando terminar e se quiser, pode eliminar o grupo de recursos. A ação de eliminar um grupo de recursos é irreversível.

> [!IMPORTANT]
> Todos os recursos dentro dos grupos de recursos serão permanentemente eliminados e não podem ser desfeitos.

1. Eliminar o grupo de recursos utilizando o comando [Remove-AzResourceGroup.](/powershell/module/az.resources/remove-azresourcegroup)

    ```powershell-interactive
    Remove-AzResourceGroup -Name $resourceGroup
    ```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Quando terminar e se quiser, pode eliminar o grupo de recursos. A ação de eliminar um grupo de recursos é irreversível.

> [!IMPORTANT]
> Todos os recursos dentro dos grupos de recursos serão permanentemente eliminados e não podem ser desfeitos.

1. Elimine o grupo de recursos utilizando o comando de eliminação do [grupo az.](/cli/azure/group#az-group-delete)

    ```azurecli-interactive
    az group delete \
        --name $RESOURCE_GROUP
    ```

# <a name="template"></a>[Modelo](#tab/template)

Nenhum.

Utilize o portal Azure, PowerShell ou o Azure CLI para eliminar o grupo de recursos.

---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)

> [!div class="nextstepaction"]
> [Níveis de serviços do Azure NetApp Files](azure-netapp-files-service-levels.md)

> [!div class="nextstepaction"]
> [Criar um volume NFS](azure-netapp-files-create-volumes.md)
