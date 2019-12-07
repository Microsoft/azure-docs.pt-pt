---
title: Dissociar um endereço IP público de uma VM do Azure
titlesuffix: Azure Virtual Network
description: Saiba como dissociar um endereço IP público de uma VM
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: kumud
ms.openlocfilehash: 1c27af30f97ea967d170b2cccaefb2e95f8fedaf
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900750"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Dissociar um endereço IP público de uma VM do Azure 

Neste artigo, você aprenderá a dissociar um endereço IP público de uma VM (máquina virtual) do Azure.

Você pode usar o [portal do Azure](#azure-portal), a CLI ( [interface de linha de comando](#azure-cli) ) do Azure ou o [PowerShell](#powershell) para dissociar um endereço IP público de uma VM.

## <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue até ou pesquise pela máquina virtual da qual você deseja desassociar o endereço IP público e, em seguida, selecione-o.
3. Na página VM, selecione **visão geral**, selecione o endereço IP público, conforme mostrado na figura a seguir:

   ![Selecionar IP público](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. Na página endereço IP público, selecione **visão geral**e, em seguida, selecione **desassociar**, conforme mostrado na figura a seguir:

    ![Dissociar IP público](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. Em **dissociar endereço IP público**, selecione **Sim**.

## <a name="azure-cli"></a>CLI do Azure

Instale o [CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)ou use o Azure cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Tem a CLI do Azure pré-instalada e configurada para ser utilizada com a sua conta. Selecione o botão **experimentar** nos comandos da CLI a seguir. A seleção de **try** invoca uma Cloud shell que você pode entrar em sua conta do Azure com.

1. Se estiver usando a CLI localmente no bash, entre no Azure com `az login`.
2. Um endereço IP público é associado a uma configuração de IP de uma interface de rede anexada a uma VM. Use o comando [AZ Network NIC-IP-config Update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) para dissociar um endereço IP público de uma configuração de IP. O exemplo a seguir dissocia um endereço IP público denominado *myVMPublicIP* da configuração de IP denominada *ipconfigmyVM* de uma interface de rede existente denominada *myVMVMNic* que está anexada a uma VM denominada *myVM* em um grupo de recursos chamado *MyResource*Group.
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Se você não souber o nome de uma interface de rede anexada à sua VM, use o comando [AZ VM NIC List](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) para exibi-las. Por exemplo, o comando a seguir lista os nomes das interfaces de rede anexadas a uma VM chamada *myVM* em um grupo de recursos chamado *MyResource*Group:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     A saída inclui uma ou mais linhas que são semelhantes ao exemplo a seguir:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     No exemplo anterior, *myVMVMNic* é o nome da interface de rede.

   - Se você não souber o nome de uma configuração de IP para uma interface de rede, use o comando [AZ Network NIC IP-config List](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) para recuperá-las. Por exemplo, o comando a seguir lista os nomes das configurações de IP público para uma interface de rede chamada *myVMVMNic* em um grupo de recursos chamado *MyResource*Group:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Se você não souber o nome de uma configuração de IP público para uma interface de rede, use o comando [AZ Network NIC IP-config show](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-show) para recuperá-las. Por exemplo, o comando a seguir lista os nomes das configurações de IP público para uma interface de rede chamada *myVMVMNic* em um grupo de recursos chamado *MyResource*Group:

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

Instale o [PowerShell](/powershell/azure/install-az-ps)ou use o Azure cloud Shell. O Azure Cloud Shell é uma shell gratuita que pode executar diretamente no portal do Azure. Ele tem o PowerShell pré-instalado e configurado para usar com sua conta. Selecione o botão **experimentar** nos comandos do PowerShell a seguir. A seleção de **try** invoca uma Cloud shell que você pode entrar em sua conta do Azure com.

1. Se estiver usando o PowerShell localmente, entre no Azure com `Connect-AzAccount`.
2. Um endereço IP público é associado a uma configuração de IP de uma interface de rede anexada a uma VM. Use o comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) para obter uma interface de rede. Defina o valor do endereço IP público como nulo e, em seguida, use o comando [set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) para gravar a nova configuração de IP para a interface de rede.

   O exemplo a seguir dissocia um endereço IP público denominado *myVMPublicIP* de uma interface de rede denominada *myVMVMNic* que é anexada a uma VM chamada *myVM*. Todos os recursos estão em um grupo de recursos chamado *MyResource*Group.
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Se você não souber o nome de uma interface de rede anexada à sua VM, use o comando [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) para exibi-las. Por exemplo, o comando a seguir lista os nomes das interfaces de rede anexadas a uma VM chamada *myVM* em um grupo de recursos chamado *MyResource*Group:

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     A saída inclui uma ou mais linhas que são semelhantes ao exemplo a seguir. Na saída de exemplo, *myVMVMNic* é o nome da interface de rede.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Se você não souber o nome de uma configuração de IP para uma interface de rede, use o comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) para recuperá-las. Por exemplo, o comando a seguir lista os nomes das configurações de IP para uma interface de rede chamada *myVMVMNic* em um grupo de recursos chamado *MyResource*Group:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     A saída inclui uma ou mais linhas que são semelhantes ao exemplo a seguir. Na saída de exemplo, *ipconfigmyVM* é o nome de uma configuração de IP.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>Passos seguintes

- Saiba como [associar um endereço IP público a uma VM](associate-public-ip-address-vm.md).
