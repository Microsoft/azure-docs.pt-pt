---
title: Configurar endereços IP privados para VMs-CLI do Azure
description: Saiba como configurar endereços IP privados para máquinas virtuais usando a CLI (interface de linha de comando) do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: kumud
ms.openlocfilehash: 5734b96466801efaa991a971bd87f60aafc9df32
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196617"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>Configurar endereços IP privados para uma máquina virtual usando o CLI do Azure

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo abrange o modelo de implementação do Resource Manager. Você também pode [gerenciar o endereço IP privado estático no modelo de implantação clássico](virtual-networks-static-private-ip-classic-cli.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> O exemplo a seguir CLI do Azure comandos esperam um ambiente simples existente. Se quiser executar os comandos à medida que são apresentadas neste documento, primeiro crie o ambiente de teste descrito em [criar uma vnet](quick-create-cli.md).

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Especifique um endereço IP privado estático ao criar uma VM

Para criar uma VM denominada *DNS01* na sub-rede *frontend* de uma VNet denominada *TestVNet* com um IP privado estático de *192.168.1.101*, conclua as seguintes etapas:

1. Se você ainda não fez isso, instale e configure as [CLI do Azure](/cli/azure/install-azure-cli) mais recentes e faça logon em uma conta do Azure usando [AZ login](/cli/azure/reference-index).

2. Crie um IP público para a VM com o comando [AZ Network Public-IP Create](/cli/azure/network/public-ip) . A lista apresentada depois do resultado explica os parâmetros utilizados.

    > [!NOTE]
    > Talvez você queira ou precise usar valores diferentes para seus argumentos nesta e em etapas subsequentes, dependendo do seu ambiente.

    ```azurecli
    az network public-ip create \
    --name TestPIP \
    --resource-group TestRG \
    --location centralus \
    --allocation-method Static
    ```

    Resultado esperado:

   ```json
   {
        "publicIp": {
            "idleTimeoutInMinutes": 4,
            "ipAddress": "52.176.43.167",
            "provisioningState": "Succeeded",
            "publicIPAllocationMethod": "Static",
            "resourceGuid": "79e8baa3-33ce-466a-846c-37af3c721ce1"
        }
    }
    ```

   * `--resource-group`: o nome do grupo de recursos no qual criar o IP público.
   * `--name`: nome do IP público.
   * `--location`: região do Azure na qual criar o IP público.

3. Execute o comando [AZ Network NIC Create](/cli/azure/network/nic) para criar uma NIC com um IP privado estático. A lista apresentada depois do resultado explica os parâmetros utilizados. 
   
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

    * `--private-ip-address`: endereço IP privado estático para a NIC.
    * `--vnet-name`: nome da VNet na qual criar a NIC.
    * `--subnet`: o nome da sub-rede na qual criar a NIC.

4. Execute o comando [Azure VM Create](/cli/azure/vm/nic) para criar a VM usando o IP público e a NIC criados anteriormente. A lista apresentada depois do resultado explica os parâmetros utilizados.
   
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
   
   Parâmetros diferentes dos parâmetros básicos [AZ VM Create](/cli/azure/vm) .

   * `--nics`: nome da NIC à qual a VM está anexada.
   
É recomendável que você não atribua estaticamente o IP privado atribuído à máquina virtual do Azure no sistema operacional de uma VM, a menos que seja necessário, como ao [atribuir vários endereços IP a uma VM do Windows](virtual-network-multiple-ip-addresses-cli.md). Se você definir manualmente o endereço IP privado no sistema operacional, verifique se ele é o mesmo endereço que o endereço IP privado atribuído ao [adaptador de rede](virtual-network-network-interface-addresses.md#change-ip-address-settings)do Azure ou se você pode perder a conectividade com a máquina virtual. Saiba mais sobre as configurações de [endereço IP privado](virtual-network-network-interface-addresses.md#private) .

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Recuperar informações de endereço IP privado estático para uma VM

Execute o seguinte comando CLI do Azure para observar os valores para o *método de alocação de IP privado* e o *endereço IP privado*:

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Resultado esperado:

```json
"192.168.1.101"
```

Para exibir as informações de IP específicas da NIC para essa VM, consulte a NIC especificamente:

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

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Remover um endereço IP privado estático de uma VM

Não é possível remover um endereço IP privado estático de uma NIC em CLI do Azure para implantações Azure Resource Manager. Você deve:
- Criar uma nova NIC que usa um IP dinâmico
- Defina a NIC na VM do NIC criado recentemente. 

Para alterar a NIC para a VM usada nos comandos anteriores, conclua as seguintes etapas:

1. Execute o comando **Azure Network NIC Create** para criar uma nova NIC usando a alocação de IP dinâmico com um novo endereço IP. Como nenhum endereço IP foi especificado, o método de alocação é **dinâmico**.

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

2. Execute o comando **Azure VM Set** para alterar a NIC usada pela VM.
   
    ```azurecli
    azure vm set -g TestRG -n DNS01 -N TestNIC2
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
    > Se a VM for grande o suficiente para ter mais de uma NIC, execute o comando de **exclusão da NIC de rede do Azure** para excluir a NIC antiga.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o gerenciamento de [configurações de endereço IP](virtual-network-network-interface-addresses.md).
