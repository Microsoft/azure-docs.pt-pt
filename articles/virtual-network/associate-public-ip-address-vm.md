---
title: Associar um endereço IP público a uma máquina virtual
titlesuffix: Azure Virtual Network
description: Saiba como associar um endereço IP público a uma máquina virtual.
services: virtual-network
documentationcenter: ''
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: jdial
ms.openlocfilehash: be3e9b8003bc6eb585177b285255658c44c7fc36
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56808655"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Associar um endereço IP público a uma máquina virtual

Neste artigo, saiba como associar um endereço IP público a uma máquina virtual existente (VM). Se pretender ligar a uma VM a partir da internet, a VM tem de ter um endereço IP público associado a si. Se quiser criar uma nova VM com um endereço IP público, pode fazê-lo utilizando o [portal do Azure](virtual-network-deploy-static-pip-arm-portal.md), o [interface de linha de comandos (CLI) do Azure](virtual-network-deploy-static-pip-arm-cli.md), ou [PowerShell](virtual-network-deploy-static-pip-arm-ps.md). Endereços IP públicos têm uma taxa nominal. Para obter detalhes, consulte [preços](https://azure.microsoft.com/pricing/details/ip-addresses/). Existe um limite ao número de endereços IP públicos, que podem ser utilizados por subscrição. Para obter detalhes, consulte [limites](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits-1).

Pode utilizar o [portal do Azure](#azure-portal), o Azure [interface de linha de comandos](#azure-cli) (CLI), ou [PowerShell](#powershell) para associar um endereço IP público a uma VM.

## <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue para ou procure pela máquina virtual que pretende adicionar o endereço IP público a e, em seguida, selecioná-lo.
3. Sob **configurações**, selecione **Networking**e, em seguida, selecione a interface de rede que pretende adicionar o endereço IP público, conforme mostrado na imagem seguinte:

   ![Selecionar interface de rede](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Endereços IP públicos são associados a interfaces de rede ligadas a uma VM. Na imagem anterior, a VM tem apenas uma interface de rede. Se a VM tiver várias interfaces de rede, todos eles seriam apresentados e, selecionaria a interface de rede que pretende associar o endereço IP público a.

4. Selecione **configurações de IP** e, em seguida, selecione uma configuração de IP, conforme mostrado na imagem seguinte:

   ![Selecione a configuração de IP](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Endereços IP públicos são associados a configurações de IP para uma interface de rede. Na imagem anterior, a interface de rede tem uma configuração de IP. Se a interface de rede tinha várias configurações de IP, todos eles seriam apresentados na lista e deverá selecionar a configuração de IP que pretende associar o endereço IP público para.

5. Selecione **Enabled**, em seguida, selecione **endereço IP (*configurar definições necessárias*)**. Escolha um endereço IP público existente, que fecha automaticamente a **escolher endereço IP público** caixa. Se não tiver quaisquer endereços IP públicos disponíveis listados, terá de criar uma. Para saber como, veja [criar um endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address). Selecione **guardar**, conforme mostrado na imagem que se segue e, em seguida, feche a caixa para a configuração de IP.

   ![Ativar o endereço IP público](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > Os endereços IP públicos que aparecem são aquelas que existem na mesma região que a VM. Se tiver vários endereços IP públicos criados na região, todos vão aparecer aqui. Se qualquer estiverem desativados, é porque o endereço já está associado a um recurso diferente.

6. Ver o endereço IP público atribuído à configuração de IP, como mostrado na imagem que se segue. Pode demorar alguns segundos para um endereço IP a aparecer.

   ![Vista atribuída o endereço IP público](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > O endereço é atribuído a partir de um conjunto de endereços utilizados em cada região do Azure. Para ver uma lista de conjuntos de endereços utilizados em cada região, consulte [intervalos de IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). O endereço atribuído pode ser qualquer endereço nos agrupamentos de utilizado para a região. Se precisar do endereço a ser atribuído a partir de um agrupamento específico na região, utilize um [prefixo de endereço IP público](public-ip-address-prefix.md).

7. [Permitir o tráfego de rede para a VM](#allow-network-traffic-to-the-vm) com regras de segurança num grupo de segurança de rede.

## <a name="azure-cli"></a>CLI do Azure

Instalar o [CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json), ou utilizar o Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Tem a CLI do Azure pré-instalada e configurada para ser utilizada com a sua conta. Selecione o **experimente** botão na CLI comandos que se seguem. Selecionando **experimente** invoca um Shell de Cloud que pode iniciar sessão sua conta do Azure com.

1. Se utilizar a CLI localmente no Bash, inicie sessão no Azure com `az login`.
2. Um endereço IP público é associado a uma configuração de IP de uma interface de rede ligada a uma VM. Utilize o [atualização de configuração do ip do nic de rede de az](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) comando para associar um endereço IP público a uma configuração de IP. O exemplo seguinte associa um endereço IP público com o nome *myVMPublicIP* à configuração de IP com o nome *ipconfigmyVM* de uma interface de rede existente com o nome *myVMVMNic* que existe num grupo de recursos com o nome *myResourceGroup*.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

  - Se não tiver um endereço IP público existente, utilize o [criar a rede de az public-ip](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) comando para criar um. Por exemplo, o comando seguinte cria um endereço IP público com o nome *myVMPublicIP* num grupo de recursos com o nome *myResourceGroup*.
  
    ```azurecli-interactive
    az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
    ```

    > [!NOTE]
    > O comando anterior cria um endereço IP público com valores predefinidos para várias definições que pretende personalizar. Para saber mais sobre todas as definições de endereço IP públicas, veja [criar um endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address). O endereço é atribuído a partir de um conjunto de endereços IP públicos utilizados para cada região do Azure. Para ver uma lista de conjuntos de endereços utilizados em cada região, consulte [intervalos de IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653).

  - Se não souber o nome de uma interface de rede ligado à sua VM, utilize o [lista do az vm nic](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) comando para visualizá-los. Por exemplo, o comando a seguir lista os nomes das interfaces de rede ligadas a uma VM com o nome *myVM* num grupo de recursos com o nome *myResourceGroup*:

    ```azurecli-interactive
    az vm nic list --vm-name myVM --resource-group myResourceGroup
    ```

     A saída inclui uma ou mais linhas que são semelhantes ao seguinte exemplo:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     No exemplo anterior, *myVMVMNic* é o nome da interface de rede.

  - Se não souber o nome de uma configuração de IP para uma interface de rede, utilize o [lista de configuração de ip de nic de redes de az](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) comando para recuperá-los. Por exemplo, o comando a seguir lista os nomes das configurações de IP para uma interface de rede com o nome *myVMVMNic* num grupo de recursos com o nome *myResourceGroup*:

    ```azurecli-interactive
    az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
    ```

3. Ver o endereço IP público atribuído à configuração de IP com o [az vm lista-ip-addresses](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) comando. O exemplo seguinte mostra os endereços IP atribuídos a uma VM existente denominada *myVM* num grupo de recursos com o nome *myResourceGroup*.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > O endereço é atribuído a partir de um conjunto de endereços utilizados em cada região do Azure. Para ver uma lista de conjuntos de endereços utilizados em cada região, consulte [intervalos de IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). O endereço atribuído pode ser qualquer endereço nos agrupamentos de utilizado para a região. Se precisar do endereço a ser atribuído a partir de um agrupamento específico na região, utilize um [prefixo de endereço IP público](public-ip-address-prefix.md).

4. [Permitir o tráfego de rede para a VM](#allow-network-traffic-to-the-vm) com regras de segurança num grupo de segurança de rede.

## <a name="powershell"></a>PowerShell

Instale [PowerShell](/powershell/azure/install-az-ps), ou utilizar o Azure Cloud Shell. O Azure Cloud Shell é uma shell gratuita que pode executar diretamente no portal do Azure. Ele tem o PowerShell pré-instaladas e configuradas para utilizar com a sua conta. Selecione o **experimente** botão no PowerShell comandos que se seguem. Selecionando **experimente** invoca um Shell de Cloud que pode iniciar sessão sua conta do Azure com.

1. Se utilizar o PowerShell localmente, inicie sessão no Azure com `Connect-AzAccount`.
2. Um endereço IP público é associado a uma configuração de IP de uma interface de rede ligada a uma VM. Utilize o [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) e [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) comandos para obter a rede virtual e a sub-rede que está a interface de rede. Em seguida, utilize o [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) comando para obter uma interface de rede e o [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) comando para obter um endereço IP público existente. Em seguida, utilizar o [conjunto AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) comando para associar o endereço IP público à configuração de IP e o [conjunto AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) comando para escrever a nova configuração de IP para o interface de rede.

   O exemplo seguinte associa um endereço IP público com o nome *myVMPublicIP* à configuração de IP com o nome *ipconfigmyVM* de uma interface de rede existente com o nome *myVMVMNic* que existe uma sub-rede denominada *myVMSubnet* numa rede virtual com o nome *myVMVNet*. Todos os recursos estão no grupo de recursos chamado *myResourceGroup*.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

  - Se não tiver um endereço IP público existente, utilize o [New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) comando para criar um. Por exemplo, o comando seguinte cria um *dinâmica* com o nome de endereço IP público *myVMPublicIP* num grupo de recursos com o nome *myResourceGroup* no  *eastus* região.
  
    ```azurepowershell-interactive
    New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
    ```

    > [!NOTE]
    > O comando anterior cria um endereço IP público com valores predefinidos para várias definições que pretende personalizar. Para saber mais sobre todas as definições de endereço IP públicas, veja [criar um endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address). O endereço é atribuído a partir de um conjunto de endereços IP públicos utilizados para cada região do Azure. Para ver uma lista de conjuntos de endereços utilizados em cada região, consulte [intervalos de IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653).

  - Se não souber o nome de uma interface de rede ligado à sua VM, utilize o [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) comando para visualizá-los. Por exemplo, o comando a seguir lista os nomes das interfaces de rede ligadas a uma VM com o nome *myVM* num grupo de recursos com o nome *myResourceGroup*:

    ```azurepowershell-interactive
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

    A saída inclui uma ou mais linhas que são semelhantes ao exemplo a seguir. Na saída de exemplo, *myVMVMNic* é o nome da interface de rede.
  
    ```
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
    ```

  - Se não souber o nome da rede virtual ou sub-rede que está a interface de rede, utilize o `Get-AzNetworkInterface` comando para ver as informações. Por exemplo, o comando seguinte obtém as informações de rede e sub-rede virtual para uma interface de rede com o nome *myVMVMNic* num grupo de recursos com o nome *myResourceGroup*:

    ```azurepowershell-interactive
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
    $ipConfigs = $nic.IpConfigurations
    $ipConfigs.Subnet | Select Id
    ```

     A saída inclui uma ou mais linhas que são semelhantes ao exemplo a seguir. Na saída de exemplo, *myVMVNET* é o nome da rede virtual e *myVMSubnet* é o nome da sub-rede.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

  - Se não souber o nome de uma configuração de IP para uma interface de rede, utilize o [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) comando para recuperá-los. Por exemplo, o comando a seguir lista os nomes das configurações de IP para uma interface de rede com o nome *myVMVMNic* num grupo de recursos com o nome *myResourceGroup*:

    ```azurepowershell-interactive
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
    $nic.IPConfigurations
    ```

    A saída inclui uma ou mais linhas que são semelhantes ao exemplo a seguir. Na saída de exemplo, *ipconfigmyVM* é o nome de uma configuração de IP.
  
    ```
    Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
    ```

3. Ver o endereço IP público atribuído à configuração de IP com o [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) comando. O exemplo seguinte mostra o endereço atribuído a um endereço IP público com o nome *myVMPublicIP* num grupo de recursos com o nome *myResourceGroup*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Se não souber o nome do endereço IP público atribuído a uma configuração de IP, execute os seguintes comandos para obtê-lo:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   A saída inclui uma ou mais linhas que são semelhantes ao exemplo a seguir. Na saída de exemplo, *myVMPublicIP* é o nome do endereço IP público atribuído à configuração de IP.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > O endereço é atribuído a partir de um conjunto de endereços utilizados em cada região do Azure. Para ver uma lista de conjuntos de endereços utilizados em cada região, consulte [intervalos de IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). O endereço atribuído pode ser qualquer endereço nos agrupamentos de utilizado para a região. Se precisar do endereço a ser atribuído a partir de um agrupamento específico na região, utilize um [prefixo de endereço IP público](public-ip-address-prefix.md).

4. [Permitir o tráfego de rede para a VM](#allow-network-traffic-to-the-vm) com regras de segurança num grupo de segurança de rede.

## <a name="allow-network-traffic-to-the-vm"></a>Permitir o tráfego de rede para a VM

Antes de poder ligar para o endereço IP público a partir da internet, certifique-se de que tem as portas necessárias abrir em qualquer grupo de segurança de rede que pode ter associado para a interface de rede, a sub-rede na qual que a interface de rede se encontra ou ambos. Embora o tráfego de filtro de grupos de segurança para o endereço IP privado da interface de rede, uma vez que o tráfego de entrada da internet chega o endereço IP público, do Azure traduz-se o endereço público para o IP privado de endereços, por isso, se um grupo de segurança de rede impede que o Falha do fluxo de tráfego, a comunicação com o endereço IP público. Pode ver as regras de segurança efetivas para uma interface de rede e a sub-rede a utilizar o [Portal](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli), ou [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="next-steps"></a>Passos Seguintes

Permitir o tráfego de internet de entrada para a VM com um grupo de segurança de rede. Para saber como criar um grupo de segurança de rede, veja [trabalhar com grupos de segurança de rede](manage-network-security-group.md#work-with-network-security-groups). Para saber mais sobre os grupos de segurança de rede, veja [grupos de segurança](security-overview.md).