---
title: Associar um endereço IP público a uma máquina virtual
titlesuffix: Azure Virtual Network
description: Aprenda a associar um endereço IP público a uma máquina virtual (VM) utilizando o portal Azure ou o CLI Azure.
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: allensu
ms.openlocfilehash: 76f92b5da2331748fbbbfc68f1e456fd50dd71ee
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223028"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Associar um endereço IP público a uma máquina virtual

Neste artigo, aprende-se a associar um endereço IP público a uma máquina virtual existente (VM). Se pretender ligar-se a um VM a partir da internet, o VM deve ter um endereço IP público associado a ele. Se pretender criar um novo VM com um endereço IP público, pode fazê-lo utilizando o [portal Azure](virtual-network-deploy-static-pip-arm-portal.md), a [interface de linha de comando Azure (CLI)](virtual-network-deploy-static-pip-arm-cli.md)ou [PowerShell](virtual-network-deploy-static-pip-arm-ps.md). Os endereços IP públicos têm uma taxa nominal. Para mais detalhes, consulte [os preços.](https://azure.microsoft.com/pricing/details/ip-addresses/) Existe um limite para o número de endereços IP públicos que pode utilizar por subscrição. Para mais detalhes, consulte [os limites.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address)

Pode utilizar o [portal Azure,](#azure-portal)a [interface de linha de comando](#azure-cli) Azure (CLI) ou [PowerShell](#powershell) para associar um endereço IP público a um VM.

## <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue ou procure a máquina virtual a que pretende adicionar o endereço IP público e, em seguida, selecione-o.
3. Em **Definições**, selecione **Networking** e, em seguida, selecione a interface de rede a que pretende adicionar o endereço IP público, tal como mostrado na seguinte imagem:

   ![Selecione interface de rede](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Os endereços IP públicos estão associados a interfaces de rede anexas a um VM. Na imagem anterior, o VM tem apenas uma interface de rede. Se o VM tivesse várias interfaces de rede, todas elas apareceriam e selecionaria a interface de rede a que pretende associar o endereço IP público.

4. Selecione **as configurações IP** e, em seguida, selecione uma configuração IP, como mostrado na seguinte imagem:

   ![Selecione configuração IP](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Os endereços IP públicos estão associados a configurações IP para uma interface de rede. Na imagem anterior, a interface de rede tem uma configuração IP. Se a interface de rede tivesse várias configurações IP, todas elas apareceriam na lista e selecionaria a configuração IP a que pretende associar o endereço IP público.

5. Selecione **Ativado** e, em seguida, selecione **o endereço IP *(Configurações necessárias para configuração)***. Escolha um endereço IP público existente, que fecha automaticamente a caixa **de endereços IP público** Escolha. Se não tiver nenhum endereço IP público disponível listado, tem de criar um. Para saber como, consulte [Criar um endereço IP público.](virtual-network-public-ip-address.md#create-a-public-ip-address) **Selecione Guardar**, como mostrado na imagem que se segue e, em seguida, feche a caixa para a configuração IP.

   ![Ativar o endereço IP público](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > Os endereços IP públicos que aparecem são os que existem na mesma região que o VM. Se tiver vários endereços IP públicos criados na região, todos aparecerão aqui. Se algum estiver acinzentado, é porque o endereço já está associado a um recurso diferente.

6. Veja o endereço IP público atribuído à configuração IP, como mostra a imagem que se segue. Pode levar alguns segundos para aparecer um endereço IP.

   ![Ver endereço IP público atribuído](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > O endereço é atribuído a partir de um conjunto de endereços utilizados em cada região de Azure. Para ver uma lista de conjuntos de endereços utilizados em cada região, consulte [os intervalos IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). O endereço atribuído pode ser qualquer endereço nas piscinas utilizadas para a região. Se necessitar do endereço para ser atribuído a partir de uma piscina específica da região, utilize um [prefixo de endereço IP público.](public-ip-address-prefix.md)

7. [Permitir o tráfego de rede para o VM](#allow-network-traffic-to-the-vm) com regras de segurança num grupo de segurança de rede.

## <a name="azure-cli"></a>CLI do Azure

Instale o [Azure CLI,](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)ou utilize a Concha da Nuvem Azure. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Tem a CLI do Azure pré-instalada e configurada para ser utilizada com a sua conta. Selecione o botão **Tente-o** nos comandos CLI que se seguem. Selecionando **Experimente-o** invoca uma Cloud Shell com a quais pode iniciar sôms na sua conta Azure.

1. Se utilizar o CLI localmente em Bash, inscreva-se em Azure com `az login` .
2. Um endereço IP público está associado a uma configuração IP de uma interface de rede anexada a um VM. Utilize o comando [de atualização de rede az nic-ip-config](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) para associar um endereço IP público a uma configuração IP. O exemplo a seguir associa um endereço IP público existente chamado *myVMPublicIP* à configuração IP chamada *ipconfigmyVM* de uma interface de rede existente chamada *myVMVMNic* que existe num grupo de recursos chamado *myResourceGroup*.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Se não tiver um endereço IP público existente, utilize o comando [public-ip da rede az](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) para criar um. Por exemplo, o seguinte comando cria um endereço IP público chamado *myVMPublicIP* num grupo de recursos chamado *myResourceGroup*.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > O comando anterior cria um endereço IP público com valores predefinidos para várias definições que poderá querer personalizar. Para saber mais sobre todas as definições de endereço IP público, consulte [Criar um endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address). O endereço é atribuído a partir de um conjunto de endereços IP públicos utilizados para cada região de Azure. Para ver uma lista de conjuntos de endereços utilizados em cada região, consulte [os intervalos IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653).

   - Se não souber o nome de uma interface de rede anexada ao seu VM, utilize o comando [da lista az vm nic](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) para os visualizar. Por exemplo, o seguinte comando lista os nomes das interfaces de rede anexadas a um VM nomeado *myVM* num grupo de recursos chamado *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     A saída inclui uma ou mais linhas semelhantes ao seguinte exemplo:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     No exemplo anterior, *myVMVMNic* é o nome da interface de rede.

   - Se não souber o nome de uma configuração IP para uma interface de rede, utilize o comando [da lista ip-config da rede Az](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) para os recuperar. Por exemplo, o seguinte comando lista os nomes das configurações IP para uma interface de rede chamada *myVMVMNic* num grupo de recursos chamado *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Ver o endereço IP público atribuído à configuração IP com o comando [az vm list-ip-addresss.](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) O exemplo a seguir mostra os endereços IP atribuídos a um VM existente chamado *myVM* num grupo de recursos chamado *myResourceGroup*.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > O endereço é atribuído a partir de um conjunto de endereços utilizados em cada região de Azure. Para ver uma lista de conjuntos de endereços utilizados em cada região, consulte [os intervalos IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). O endereço atribuído pode ser qualquer endereço nas piscinas utilizadas para a região. Se necessitar do endereço para ser atribuído a partir de uma piscina específica da região, utilize um [prefixo de endereço IP público.](public-ip-address-prefix.md)

4. [Permitir o tráfego de rede para o VM](#allow-network-traffic-to-the-vm) com regras de segurança num grupo de segurança de rede.

## <a name="powershell"></a>PowerShell

Instale [PowerShell,](/powershell/azure/install-az-ps)ou utilize a Concha da Nuvem Azure. O Azure Cloud Shell é uma shell gratuita que pode executar diretamente no portal do Azure. Tem o PowerShell pré-instalado e configurado para ser utilizado com a sua conta. Selecione o botão **Tente-o** nos comandos PowerShell que se seguem. Selecionando **Experimente-o** invoca uma Cloud Shell com a quais pode iniciar sôms na sua conta Azure.

1. Se utilizar o PowerShell localmente, inscreva-se em Azure com `Connect-AzAccount` .
2. Um endereço IP público está associado a uma configuração IP de uma interface de rede anexada a um VM. Utilize os comandos [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) e [Get-AzVirtualNetWorkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) para obter a rede virtual e a sub-rede em que a interface de rede se encontra. Em seguida, utilize o comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) para obter uma interface de rede e o comando [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para obter um endereço IP público existente. Em seguida, utilize o comando [Set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) para associar o endereço IP público à configuração IP e ao comando [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) para escrever a nova configuração IP na interface de rede.

   O exemplo a seguir associa um endereço IP público existente chamado *myVMPublicIP* à configuração IP chamada *ipconfigmyVM* de uma interface de rede existente chamada *myVMVMNic* que existe numa sub-rede chamada *myVMSubnet* numa rede virtual chamada *myVMVNet*. Todos os recursos estão num grupo de recursos chamado *myResourceGroup*.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Se não tiver um endereço IP público existente, utilize o comando [New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) para criar um. Por exemplo, o seguinte comando cria um endereço IP público *dinâmico* chamado *myVMPublicIP* em um grupo de recursos chamado *myResourceGroup* na região *leste.*
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > O comando anterior cria um endereço IP público com valores predefinidos para várias definições que poderá querer personalizar. Para saber mais sobre todas as definições de endereço IP público, consulte [Criar um endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address). O endereço é atribuído a partir de um conjunto de endereços IP públicos utilizados para cada região de Azure. Para ver uma lista de conjuntos de endereços utilizados em cada região, consulte [os intervalos IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653).

   - Se não souber o nome de uma interface de rede anexada ao seu VM, utilize o comando [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) para os visualizar. Por exemplo, o seguinte comando lista os nomes das interfaces de rede anexadas a um VM nomeado *myVM* num grupo de recursos chamado *myResourceGroup*:

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     A saída inclui uma ou mais linhas semelhantes ao exemplo que se segue. Na saída do exemplo, *myVMVMNic* é o nome da interface de rede.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Se não souber o nome da rede virtual ou sub-rede em que se encontra a interface de rede, utilize o `Get-AzNetworkInterface` comando para visualizar a informação. Por exemplo, o seguinte comando obtém a rede virtual e informações de sub-redes para uma interface de rede chamada *myVMVMNic* num grupo de recursos chamado *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     A saída inclui uma ou mais linhas semelhantes ao exemplo que se segue. Na saída do exemplo, *myVMVNET* é o nome da rede virtual e *myVMSubnet* é o nome da sub-rede.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Se não souber o nome de uma configuração IP para uma interface de rede, utilize o comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) para os recuperar. Por exemplo, o seguinte comando lista os nomes das configurações IP para uma interface de rede chamada *myVMVMNic* num grupo de recursos chamado *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     A saída inclui uma ou mais linhas semelhantes ao exemplo que se segue. Na saída de exemplo, *ipconfigmyVM* é o nome de uma configuração IP.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Ver o endereço IP público atribuído à configuração IP com o comando [Get-AzPublicIpAddress.](/powershell/module/az.network/get-azpublicipaddress) O exemplo a seguir mostra o endereço atribuído a um endereço IP público chamado *myVMPublicIP* num grupo de recursos chamado *myResourceGroup*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Se não souber o nome do endereço IP público atribuído a uma configuração IP, execute os seguintes comandos para obtê-lo:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   A saída inclui uma ou mais linhas semelhantes ao exemplo que se segue. Na saída de exemplo, *myVMPublicIP* é o nome do endereço IP público atribuído à configuração IP.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > O endereço é atribuído a partir de um conjunto de endereços utilizados em cada região de Azure. Para ver uma lista de conjuntos de endereços utilizados em cada região, consulte [os intervalos IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). O endereço atribuído pode ser qualquer endereço nas piscinas utilizadas para a região. Se necessitar do endereço para ser atribuído a partir de uma piscina específica da região, utilize um [prefixo de endereço IP público.](public-ip-address-prefix.md)

4. [Permitir o tráfego de rede para o VM](#allow-network-traffic-to-the-vm) com regras de segurança num grupo de segurança de rede.

## <a name="allow-network-traffic-to-the-vm"></a>Permitir o tráfego da rede para o VM

Antes de poder ligar ao endereço IP público a partir da internet, certifique-se de que tem as portas necessárias abertas em qualquer grupo de segurança de rede que possa ter associado à interface de rede, a sub-rede em que a interface de rede está, ou ambas. Embora os grupos de segurança filtrem o tráfego para o endereço IP privado da interface de rede, uma vez que o tráfego de internet de entrada chega ao endereço IP público, Azure traduz o endereço público para o endereço IP privado, então se um grupo de segurança de rede impedir o fluxo de tráfego, a comunicação com o endereço IP público falha. Pode ver as regras de segurança eficazes para uma interface de rede e a sua sub-rede utilizando o [Portal](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)ou [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="next-steps"></a>Passos seguintes

Permitir o tráfego de acesso à internet para o seu VM com um grupo de segurança de rede. Para aprender a criar um grupo de segurança de rede, consulte [Trabalhar com grupos de segurança de rede.](manage-network-security-group.md#work-with-network-security-groups) Para saber mais sobre grupos de segurança de rede, consulte [os grupos de segurança.](./network-security-groups-overview.md)