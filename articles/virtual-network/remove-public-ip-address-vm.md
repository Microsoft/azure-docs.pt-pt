---
title: Dissociar um endereço IP público de um Azure VM
titlesuffix: Azure Virtual Network
description: Saiba como dissociar um endereço IP público de um VM
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: allensu
ms.openlocfilehash: f29e29f809faeeb486e5b6b9bacc84a61380a012
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82144463"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Dissociar um endereço IP público de um Azure VM 

Neste artigo, aprende-se a dissociar um endereço IP público de uma máquina virtual Azure (VM).

Pode utilizar o [portal Azure,](#azure-portal)a interface de [linha de comando](#azure-cli) Azure (CLI) ou [powerShell](#powershell) para dissociar um endereço IP público de um VM.

## <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Procure ou procure a máquina virtual a que pretende dissociar o endereço IP público e, em seguida, selecione-o.
3. Na página VM, selecione **Visão Geral**, selecione o endereço IP público como mostrado na seguinte imagem:

   ![Selecione IP público](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. Na página de endereçoIP público, selecione **Visão Geral**, e, em seguida, selecione **Dissociate**, como mostrado na seguinte imagem:

    ![Dissociar IP Público](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. No **endereço IP público dissociado,** selecione **Sim**.

## <a name="azure-cli"></a>CLI do Azure

Instale o [Azure CLI,](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)ou utilize a Casca de Nuvem Azure. O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. Tem a CLI do Azure pré-instalada e configurada para ser utilizada com a sua conta. Selecione o botão **Experimente** os comandos CLI que se seguem. Selecionando, o **tente** invoca uma Cloud Shell com a que pode iniciar sessão na sua conta Azure.

1. Se utilizar o CLI localmente em Bash, inscreva-se no Azure com `az login`.
2. Um endereço IP público está associado a uma configuração IP de uma interface de rede anexada a um VM. Utilize o comando de [atualização nic-ip-config](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) da rede az para dissociar um endereço IP público de uma configuração IP. O exemplo seguinte dissocia um endereço IP público chamado *myVMPublicIP* da configuração IP chamada *ipconfigmyVM* de uma interface de rede existente chamada *myVMVMNic* que está anexado a um VM chamado *myVM* num grupo de recursos chamado *myResourceGroup*.
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Se não sabe o nome de uma interface de rede anexa à sua VM, utilize o comando da [lista az vm nic](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) para os visualizar. Por exemplo, o seguinte comando lista os nomes das interfaces de rede anexadas a um VM denominado *myVM* num grupo de recursos chamado *myResourceGroup:*

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     A saída inclui uma ou mais linhas semelhantes ao seguinte exemplo:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     No exemplo anterior, *myVMVMNic* é o nome da interface de rede.

   - Se não souber o nome de uma configuração IP para uma interface de rede, utilize o comando da [lista ip-config da rede az](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) para os recuperar. Por exemplo, o seguinte comando lista os nomes das configurações ip públicas para uma interface de rede chamada *myVMVMNic* num grupo de recursos chamado *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Se não sabe o nome de uma configuração IP pública para uma interface de rede, utilize o comando de [exibição ip-config da rede az](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-show) para os recuperar. Por exemplo, o seguinte comando lista os nomes das configurações ip públicas para uma interface de rede chamada *myVMVMNic* num grupo de recursos chamado *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

Instale [powerShell](/powershell/azure/install-az-ps), ou use a Casca de Nuvem Azure. O Azure Cloud Shell é uma shell gratuita que pode executar diretamente no portal do Azure. Tem o PowerShell pré-instalado e configurado para utilizar com a sua conta. Selecione o botão **Experimente** nos comandos PowerShell que se seguem. Selecionando, o **tente** invoca uma Cloud Shell com a que pode iniciar sessão na sua conta Azure.

1. Se utilizar o PowerShell localmente, `Connect-AzAccount`inscreva-se no Azure com .
2. Um endereço IP público está associado a uma configuração IP de uma interface de rede anexada a um VM. Utilize o comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) para obter uma interface de rede. Desloque o valor do endereço IP público para nulo e, em seguida, utilize o comando [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) para escrever a nova configuração IP para a interface de rede.

   O exemplo seguinte dissocia um endereço IP público chamado *myVMPublicIP* de uma interface de rede chamada *myVMVMNic* que está anexado a um VM chamado *myVM*. Todos os recursos estão num grupo de recursos chamado *myResourceGroup*.
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Se não souber o nome de uma interface de rede anexa da sua VM, utilize o comando [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) para os visualizar. Por exemplo, o seguinte comando lista os nomes das interfaces de rede anexadas a um VM denominado *myVM* num grupo de recursos chamado *myResourceGroup:*

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     A saída inclui uma ou mais linhas semelhantes ao exemplo que se segue. Na saída de exemplo, *myVMVMNic* é o nome da interface de rede.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Se não souber o nome de uma configuração IP para uma interface de rede, utilize o comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) para os recuperar. Por exemplo, o seguinte comando lista os nomes das configurações IP para uma interface de rede chamada *myVMVMNic* num grupo de recursos chamado *myResourceGroup:*

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
