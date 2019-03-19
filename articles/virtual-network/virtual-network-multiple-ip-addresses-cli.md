---
title: VM com vários endereços IP com a CLI do Azure
titlesuffix: Azure Virtual Network
description: Saiba como atribuir vários endereços IP a uma máquina virtual utilizando a interface de linha de comandos (CLI) do Azure.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: jimdial
ms.openlocfilehash: 5606fb2f01a79a517279809d6da21ec865ee8b0d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58110142"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Atribuir vários endereços IP para máquinas virtuais com a CLI do Azure

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Este artigo explica como criar uma máquina virtual (VM) com o modelo de implementação Azure Resource Manager com a CLI do Azure. Não não possível atribuir vários endereços IP para os recursos criados por meio do modelo de implementação clássica. Para saber mais sobre os modelos de implementação do Azure, leia os [compreender os modelos de implementação](../resource-manager-deployment-model.md) artigo.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Criar uma VM com vários endereços IP

Os passos que se seguem explicam como criar uma máquina virtual de exemplo com vários endereços IP, conforme descrito no cenário. Alterar valores de variáveis em "" e tipos de endereços IP, conforme necessário, para a sua implementação. 

1. Instalar o [CLI do Azure](/cli/azure/install-azure-cli) se ainda não tiver instalado.
2. Criar um par de chaves público e privado SSH para VMs do Linux, concluindo os passos a [criar um par de chaves público e privado SSH para VMs do Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. A partir de um shell de comando, o início de sessão com o comando `az login` e selecione a subscrição que está a utilizar.
4. Crie a VM ao executar o script que se segue num computador Linux ou Mac. O script cria um grupo de recursos, uma rede virtual (VNet), uma NIC com três configurações de IP e uma VM com dois NICs ligados ao mesmo. A NIC, o endereço IP público, a rede virtual e a recursos da VM têm de existir na mesma localização e subscrição. Embora os recursos não têm de existir no mesmo grupo de recursos, o seguinte script fazem.

```bash
    
#!/bin/sh
    
RgName="myResourceGroup"
Location="westcentralus"
az group create --name $RgName --location $Location
    
# Create a public IP address resource with a static IP address using the `--allocation-method Static` option. If you
# do not specify this option, the address is allocated dynamically. The address is assigned to the resource from a pool
# of IP addresses unique to each Azure region. Download and view the file from
# https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists the ranges for each region.

PipName="myPublicIP"

# This name must be unique within an Azure location.
DnsName="myDNSName"

az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--dns-name $DnsName\
--allocation-method Static

# Create a virtual network with one subnet

VnetName="myVnet"
VnetPrefix="10.0.0.0/16"
VnetSubnetName="mySubnet"
VnetSubnetPrefix="10.0.0.0/24"

az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnetName \
--subnet-prefix $VnetSubnetPrefix

# Create a network interface connected to the subnet and associate the public IP address to it. Azure will create the
# first IP configuration with a static private IP address and will associate the public IP address resource to it.

NicName="MyNic1"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--private-ip-address 10.0.0.4
--vnet-name $VnetName \
--public-ip-address $PipName
    
# Create a second public IP address, a second IP configuration, and associate it to the NIC. This configuration has a
# static public IP address and a static private IP address.

az network public-ip create \
--resource-group $RgName \
--location $Location \
--name myPublicIP2 \
--dns-name mypublicdns2 \
--allocation-method Static

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--name IPConfig-2 \
--private-ip-address 10.0.0.5 \
--public-ip-name myPublicIP2

# Create a third IP configuration, and associate it to the NIC. This configuration has  static private IP address and   # no public IP address.

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--private-ip-address 10.0.0.6 \
--name IPConfig-3

# Note: Though this article assigns all IP configurations to a single NIC, you can also assign multiple IP configurations
# to any NIC in a VM. To learn how to create a VM with multiple NICs, read the Create a VM with multiple NICs 
# article: https://docs.microsoft.com/azure/virtual-network/virtual-network-deploy-multinic-arm-cli.

# Create a VM and attach the NIC.

VmName="myVm"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article. The script fails if the VM size
# is not supported in the location you select. Run the `azure vm sizes --location eastcentralus` command to get a full list
# of VMs in US West Central, for example.

VmSize="Standard_DS1"

# Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
# `az vm image list` command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file. If you're creating a Windows VM, remove the following
# line and you'll be prompted for the password you want to configure for the VM.

SshKeyValue="~/.ssh/id_rsa.pub"

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $NicName \
--admin-username $Username \
--ssh-key-value $SshKeyValue
```

Além de criar uma VM com um NIC com 3 configurações de IP, o script cria:

- Um único premium disco gerido por predefinição, mas tiver outras opções para o tipo de disco, que pode criar. Leitura a [criar uma VM do Linux com a CLI do Azure](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artigo para obter detalhes.
- Uma rede virtual com uma sub-rede e dois endereços IP públicos. Em alternativa, pode utilizar *existente* rede virtual, sub-rede, NIC ou recursos de endereço IP público. Para saber como usar recursos de rede existentes em vez de criar recursos adicionais, introduza `az vm create -h`.

Endereços IP públicos têm uma taxa nominal. Para saber mais sobre os preços de endereços IP, leia os [preços de endereços IP](https://azure.microsoft.com/pricing/details/ip-addresses) página. Existe um limite ao número de endereços IP públicos, que pode ser utilizado numa subscrição. Para saber mais sobre os limites, leia o artigo [Azure limites](../azure-subscription-service-limits.md#networking-limits) (Limites do artigo).

Depois da VM é criada, introduza o `az network nic show --name MyNic1 --resource-group myResourceGroup` comando para ver a configuração de NIC. Introduza o `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` para ver uma lista das configurações de IP associadas para o NIC.

Adicionar os endereços IP privados para o sistema operacional VM, concluindo os passos para o seu sistema operativo no [endereços IP de adicionar a um sistema de operativo VM](#os-config) seção deste artigo.

## <a name="add"></a>Adicionar endereços IP a uma VM

Pode adicionar endereços IP públicos e privados adicionais a uma interface de rede do Azure existente, concluindo os passos que se seguem. Os exemplos são criados após a [cenário](#Scenario) descrito neste artigo.

1. Abra uma shell de comando e conclua os restantes passos nesta secção dentro de uma única sessão. Se ainda não tiver a CLI do Azure instalada e configurada, conclua os passos a [instalação da CLI do Azure](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) artigo e início de sessão para o seu Azure conta com o `az-login` comando.

2. Conclua os passos de uma das seções a seguir, com base nos seus requisitos:

    **Adicionar um endereço IP privado**
    
    Para adicionar um endereço IP privado a uma NIC, tem de criar uma configuração de IP utilizando o comando seguinte. O endereço IP estático tem de ser um endereço não utilizado para a sub-rede.

    ```bash
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Crie configurações de tantos que forem necessárias, com nomes de configuração exclusiva e endereços IP privados (para configurações com endereços IP estáticos).

    **Adicionar um endereço IP público**
    
    Um endereço IP público é adicionado, associando-o para uma nova configuração de IP ou uma configuração de IP existente. Conclua os passos de uma das seções a seguir, que forem necessárias.

    Endereços IP públicos têm uma taxa nominal. Para saber mais sobre os preços de endereços IP, leia os [preços de endereços IP](https://azure.microsoft.com/pricing/details/ip-addresses) página. Existe um limite ao número de endereços IP públicos, que pode ser utilizado numa subscrição. Para saber mais sobre os limites, leia o artigo [Azure limites](../azure-subscription-service-limits.md#networking-limits) (Limites do artigo).

    - **Associar o recurso para uma nova configuração de IP**
    
        Sempre que adicionar um endereço IP público numa nova configuração de IP, também tem de adicionar um endereço IP privado, uma vez que todas as configurações de IP tem de ter um endereço IP privado. Pode adicionar um recurso de endereço IP público existente ou crie um novo. Para criar uma nova, introduza o seguinte comando:
    
        ```bash
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        Para criar uma nova configuração de IP com um endereço IP privado estático e associada *myPublicIP3* IP público de recurso de endereços, introduza o seguinte comando:

        ```bash
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **Associar o recurso a uma configuração de IP existente** um recurso de endereço IP público só pode ser associado a uma configuração de IP que ainda não tiver uma associados. Pode determinar se uma configuração de IP tem um endereço IP público associado ao introduzir o seguinte comando:

        ```bash
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        Devolveu saída:
    
            Name        PublicIpAddressId
            
            ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
            IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
            IPConfig-3

        Uma vez que o **PublicIpAddressId** coluna para *IpConfig 3* está em branco na saída, nenhum recurso de endereço IP público do está atualmente associado a ele. Pode adicionar um recurso de endereço IP público existente para IpConfig 3, ou introduza o seguinte comando para criar um:

        ```bash
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        Introduza o seguinte comando para associar o recurso de endereço IP público à configuração de IP existente com o nome *IPConfig 3*:
    
        ```bash
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. Ver os endereços IP privados e o recurso de endereço IP público Ids atribuídas ao NIC, introduzindo o seguinte comando:

    ```bash
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    Devolveu saída: <br>
    
        Name        PrivateIpAddress    PrivateIpAllocationMethod   PublicIpAddressId
        
        ipconfig1   10.0.0.4            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  10.0.0.5            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3  10.0.0.6            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    

4. Adicionar os endereços IP privados que adicionou ao NIC para o sistema operacional VM ao seguir as instruções no [endereços IP de adicionar a um sistema de operativo VM](#os-config) seção deste artigo. Não adicione os endereços IP públicos para o sistema operativo.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
