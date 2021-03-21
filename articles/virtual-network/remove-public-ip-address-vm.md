---
title: Dissociar um endereço IP público a partir de um VM Azure
titlesuffix: Azure Virtual Network
description: Saiba como dissociar um endereço IP público a partir de um VM
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: allensu
ms.openlocfilehash: 0665cbd7aa21575337999fb5c59478955c764048
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98934200"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Dissociar um endereço IP público a partir de um VM Azure 

Neste artigo, aprende-se a dissociar um endereço IP público a partir de uma máquina virtual Azure (VM).

Pode utilizar o [portal Azure,](#azure-portal)a [interface de linha de comando](#azure-cli) Azure (CLI) ou o [PowerShell](#powershell) para dissociar um endereço IP público a partir de um VM.

## <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Procure ou procure a máquina virtual que pretende desassociar o endereço IP público e, em seguida, selecione-o.
3. Na página VM, selecione **'Vista Geral',** selecione o endereço IP público como mostrado na seguinte imagem:

   ![Selecione IP público](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. Na página de endereço IP público, selecione **Overview**, e, em seguida, selecione **Dissociate**, como mostrado na seguinte imagem:

    ![Dissociar o IP público](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. No **endereço IP público dissociar,** selecione **Sim**.

## <a name="azure-cli"></a>CLI do Azure

Instale o [Azure CLI,](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)ou utilize a Concha da Nuvem Azure. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Tem a CLI do Azure pré-instalada e configurada para ser utilizada com a sua conta. Selecione o botão **Tente-o** nos comandos CLI que se seguem. Selecionando **Experimente-o** invoca uma Cloud Shell com a quais pode iniciar sôms na sua conta Azure.

1. Se utilizar o CLI localmente em Bash, inscreva-se em Azure com `az login` .
2. Um endereço IP público está associado a uma configuração IP de uma interface de rede anexada a um VM. Utilize o comando [de atualização de rede az nic-ip-config](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update) para dissociar um endereço IP público a partir de uma configuração IP. O exemplo seguinte dissocia um endereço IP público chamado *myVMPublicIP* da configuração IP denominada *ipconfigmyVM* de uma interface de rede existente chamada *myVMVMNic* que está anexada a um VM nomeado *myVM* em um grupo de recursos chamado *myResourceGroup*.
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Se não souber o nome de uma interface de rede anexada ao seu VM, utilize o comando [da lista az vm nic](/cli/azure/vm/nic#az-vm-nic-list) para os visualizar. Por exemplo, o seguinte comando lista os nomes das interfaces de rede anexadas a um VM nomeado *myVM* num grupo de recursos chamado *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     A saída inclui uma ou mais linhas semelhantes ao seguinte exemplo:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     No exemplo anterior, *myVMVMNic* é o nome da interface de rede.

   - Se não souber o nome de uma configuração IP para uma interface de rede, utilize o comando [da lista ip-config da rede Az](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-list) para os recuperar. Por exemplo, o seguinte comando lista os nomes das configurações IP públicas para uma interface de rede chamada *myVMVMNic* num grupo de recursos chamado *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Se não souber o nome de uma configuração IP pública para uma interface de rede, utilize o comando [de exibição ip-config da rede az](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-show) para os recuperar. Por exemplo, o seguinte comando lista os nomes das configurações IP públicas para uma interface de rede chamada *myVMVMNic* num grupo de recursos chamado *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

Instale [PowerShell,](/powershell/azure/install-az-ps)ou utilize a Concha da Nuvem Azure. O Azure Cloud Shell é uma shell gratuita que pode executar diretamente no portal do Azure. Tem o PowerShell pré-instalado e configurado para ser utilizado com a sua conta. Selecione o botão **Tente-o** nos comandos PowerShell que se seguem. Selecionando **Experimente-o** invoca uma Cloud Shell com a quais pode iniciar sôms na sua conta Azure.

1. Se utilizar o PowerShell localmente, inscreva-se em Azure com `Connect-AzAccount` .
2. Um endereço IP público está associado a uma configuração IP de uma interface de rede anexada a um VM. Utilize o comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) para obter uma interface de rede. Descreva o valor do endereço IP público para nulo e, em seguida, utilize o comando [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) para escrever a nova configuração IP para a interface de rede.

   O exemplo seguinte dissocia um endereço IP público chamado *myVMPublicIP* de uma interface de rede chamada *myVMVMNic* que está anexada a um VM chamado *myVM*. Todos os recursos estão num grupo de recursos chamado *myResourceGroup*.
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Se não souber o nome de uma interface de rede anexada ao seu VM, utilize o comando [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) para os visualizar. Por exemplo, o seguinte comando lista os nomes das interfaces de rede anexadas a um VM nomeado *myVM* num grupo de recursos chamado *myResourceGroup*:

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     A saída inclui uma ou mais linhas semelhantes ao exemplo que se segue. Na saída do exemplo, *myVMVMNic* é o nome da interface de rede.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Se não souber o nome de uma configuração IP para uma interface de rede, utilize o comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) para os recuperar. Por exemplo, o seguinte comando lista os nomes das configurações IP para uma interface de rede chamada *myVMVMNic* num grupo de recursos chamado *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     A saída inclui uma ou mais linhas semelhantes ao exemplo que se segue. Na saída de exemplo, *ipconfigmyVM* é o nome de uma configuração IP.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>Passos seguintes

- Saiba como [associar um endereço IP público a um VM](associate-public-ip-address-vm.md).
