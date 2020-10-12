---
title: Configure endereços IP privados para VMs - Azure CLI
description: Saiba como configurar endereços IP privados para máquinas virtuais utilizando a interface de linha de comando Azure (CLI).
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: allensu
ms.openlocfilehash: c34ab73422d8dd41feb9da542ed63fdba060fe3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84708166"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>Configure endereços IP privados para uma máquina virtual usando o Azure CLI


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> A amostra a seguir os comandos Azure CLI esperam um ambiente simples existente. Se pretender executar os comandos tal como são apresentados neste documento, primeiro construa o ambiente de teste descrito na [criação de um vnet](quick-create-cli.md).

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Especifique um endereço IP privado estático ao criar um VM

Para criar um VM chamado *DNS01* na sub-rede *FrontEnd* de um VNet chamado *TestVNet* com um IP estático privado de *192.168.1.1.101,* complete os seguintes passos:

1. Se ainda não o fez, instale e configuure o mais recente [Azure CLI](/cli/azure/install-azure-cli) e faça login numa conta Azure utilizando [o login az](/cli/azure/reference-index).

2. Executar o [nic de rede az criar](/cli/azure/network/nic) comando para criar um NIC com um IP estático privado. A lista apresentada depois do resultado explica os parâmetros utilizados. 
   
    ```azurecli
    az network nic create \
    --resource-group TestRG \
    --name TestNIC \
    --location centralus \
    --subnet FrontEnd \
    --private-ip-address 192.168.1.101 \
    --vnet-name TestVNet
    ```

    Resultado esperado:
   
    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.101",
                "privateIPAllocationMethod": "Static",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>"
        }
    }
    ```
    
    Parâmetros:

    * `--private-ip-address`: Endereço IP privado estático para o NIC.
    * `--vnet-name`: Nome do VNet em que criar o NIC.
    * `--subnet`: Nome da sub-rede na qual criar o NIC.

3. Executar o [comando azure vm criar](/cli/azure/vm/nic) para criar o VM usando o IP público e NIC criados anteriormente. A lista apresentada depois do resultado explica os parâmetros utilizados.
   
    ```azurecli
    az vm create \
    --resource-group TestRG \
    --name DNS01 \
    --location centralus \
    --image Debian \
    --admin-username adminuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics TestNIC
    ```

    Resultado esperado:
   
    ```json
    {
        "fqdns": "",
        "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01",
        "location": "centralus",
        "macAddress": "00-0D-3A-92-C1-66",
        "powerState": "VM running",
        "privateIpAddress": "192.168.1.101",
        "publicIpAddress": "",
        "resourceGroup": "TestRG"
    }
    ```
   
   Parâmetros que não os [az vm básicos criam](/cli/azure/vm) parâmetros.

   * `--nics`: Nome do NIC ao qual o VM está ligado.
   
Recomenda-se que não atribua estáticamente o IP privado atribuído à máquina virtual Azure dentro do sistema operativo de um VM, a menos que seja necessário, como quando [atribuir vários endereços IP a um VM do Windows](virtual-network-multiple-ip-addresses-cli.md). Se definir manualmente o endereço IP privado dentro do sistema operativo, certifique-se de que é o mesmo endereço que o endereço IP privado atribuído à [interface de rede](virtual-network-network-interface-addresses.md#change-ip-address-settings)Azure , ou pode perder conectividade com a máquina virtual. Saiba mais sobre as definições [privadas de endereço IP.](virtual-network-network-interface-addresses.md#private)

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Recuperar informações estáticas de endereço IP privadas para um VM

Executar o seguinte comando Azure CLI para observar os valores para o *método alococ-método IP privado* e endereço IP *privado*:

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Resultado esperado:

```json
"192.168.1.101"
```

Para apresentar as informações IP específicas do NIC para esse VM, consultar o NIC especificamente:

```azurecli
az network nic show \
-g testrg \
-n testnic \
--query 'ipConfigurations[0].{PrivateAddress:privateIpAddress,IPVer:privateIpAddressVersion,IpAllocMethod:p
rivateIpAllocationMethod,PublicAddress:publicIpAddress}'
```

O resultado é algo como:

```json
{
    "IPVer": "IPv4",
    "IpAllocMethod": "Static",
    "PrivateAddress": "192.168.1.101",
    "PublicAddress": null
}
```

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Remova um endereço IP estático privado de um VM

Não é possível remover um endereço IP privado estático de um NIC em Azure CLI para implementações do Azure Resource Manager. Tens de o fazer:
- Criar um novo NIC que usa um IP dinâmico
- Coloque o NIC no VM faça o NIC recém-criado. 

Para alterar o NIC para o VM utilizado nos comandos anteriores, complete os seguintes passos:

1. Executar o **nic de rede azul criar** comando para criar um novo NIC usando alocação ip dinâmica com um novo endereço IP. Como não é especificado nenhum endereço IP, o método de atribuição é **Dinâmico.**

    ```azurecli
    az network nic create     \
    --resource-group TestRG     \
    --name TestNIC2     \
    --location centralus     \
    --subnet FrontEnd    \
    --vnet-name TestVNet
    ```        
   
    Resultado esperado:

    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.4",
                "privateIPAllocationMethod": "Dynamic",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "0808a61c-476f-4d08-98ee-0fa83671b010"
        }
    }
    ```

2. Executar o comando **conjunto azure vm** para alterar o NIC utilizado pelo VM.
   
    ```azurecli
   az vm nic set --resource-group TestRG --vm-name DNS01 --nics TestNIC2
    ```

    Resultado esperado:
   
    ```json
    [
        {
            "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC3",
            "primary": true,
            "resourceGroup": "TestRG"
        }
    ]
    ```

    > [!NOTE]
    > Se o VM for suficientemente grande para ter mais do que um NIC, executar o comando **de eliminação do nic de rede** azul para eliminar o antigo NIC.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a gestão das [definições de endereço IP](virtual-network-network-interface-addresses.md).
