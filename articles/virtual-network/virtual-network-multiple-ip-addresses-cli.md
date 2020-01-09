---
title: VM com vários endereços IP usando o CLI do Azure
titlesuffix: Azure Virtual Network
description: Saiba como atribuir vários endereços IP a uma máquina virtual usando a CLI (interface de linha de comando) do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: kumud
ms.openlocfilehash: b99e5e6809a909184d775c70b56c249c11734cb9
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646613"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Atribuir vários endereços IP a máquinas virtuais usando o CLI do Azure

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Este artigo explica como criar uma VM (máquina virtual) por meio do modelo de implantação Azure Resource Manager usando o CLI do Azure. Vários endereços IP não podem ser atribuídos a recursos criados por meio do modelo de implantação clássico. Para saber mais sobre os modelos de implantação do Azure, leia o artigo [entender os modelos de implantação](../resource-manager-deployment-model.md) .

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Criar uma VM com vários endereços IP

As etapas a seguir explicam como criar uma máquina virtual de exemplo com vários endereços IP, conforme descrito no cenário. Altere os valores de variáveis em "" e os tipos de endereço IP, conforme necessário, para sua implementação. 

1. Instale o [CLI do Azure](/cli/azure/install-azure-cli) se você ainda não o tiver instalado.
2. Crie um par de chaves SSH pública e privada para VMs Linux concluindo as etapas em [criar um par de chaves SSH pública e privada para VMs do Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. Em um shell de comando, faça logon com o comando `az login` e selecione a assinatura que você está usando.
4. Crie a VM executando o script a seguir em um computador Linux ou Mac. O script cria um grupo de recursos, uma rede virtual (VNet), uma NIC com três configurações de IP e uma VM com as duas NICs anexadas a ela. A NIC, o endereço IP público, a rede virtual e os recursos da VM devem existir no mesmo local e assinatura. Embora os recursos não precisem existir no mesmo grupo de recursos, no script a seguir eles são.

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

Além de criar uma VM com uma NIC com 3 configurações de IP, o script cria:

- Um único disco gerenciado Premium por padrão, mas você tem outras opções para o tipo de disco que pode criar. Leia o artigo [criar uma VM Linux usando o CLI do Azure](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter detalhes.
- Uma rede virtual com uma sub-rede e dois endereços IP públicos. Como alternativa, você pode usar a rede virtual *existente* , a sub-rede, a NIC ou os recursos de endereço IP público. Para saber como usar os recursos de rede existentes em vez de criar recursos adicionais, digite `az vm create -h`.

Os endereços IP públicos têm uma taxa nominal. Para saber mais sobre preços de endereço IP, leia a página de [preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Há um limite para o número de endereços IP públicos que podem ser usados em uma assinatura. Para saber mais sobre os limites, leia o artigo [Azure limites](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) (Limites do artigo).

Depois que a VM for criada, insira o comando `az network nic show --name MyNic1 --resource-group myResourceGroup` para exibir a configuração da NIC. Insira o `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` para exibir uma lista das configurações de IP associadas à NIC.

Adicione os endereços IP privados ao sistema operacional da VM concluindo as etapas para seu sistema operacional na seção [adicionar endereços IP a um sistema operacional da VM](#os-config) deste artigo.

## <a name="add"></a>Adicionar endereços IP a uma VM

Você pode adicionar endereços IP públicos e privados adicionais a uma interface de rede do Azure existente concluindo as etapas a seguir. Os exemplos se baseiam no [cenário](#scenario) descrito neste artigo.

1. Abra um shell de comando e conclua as etapas restantes nesta seção em uma única sessão. Se você ainda não tiver CLI do Azure instalado e configurado, conclua as etapas no artigo de [instalação do CLI do Azure](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) e faça logon em sua conta do Azure com o comando `az-login`.

2. Conclua as etapas em uma das seções a seguir, com base em seus requisitos:

    **Adicionar um endereço IP privado**
    
    Para adicionar um endereço IP privado a uma NIC, você deve criar uma configuração de IP usando o comando a seguir. O endereço IP estático deve ser um endereço não usado para a sub-rede.

    ```bash
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Crie quantas configurações forem necessárias, usando nomes de configuração exclusivos e endereços IP privados (para configurações com endereços IP estáticos).

    **Adicionar um endereço IP público**
    
    Um endereço IP público é adicionado associando-o a uma nova configuração de IP ou a uma configuração de IP existente. Conclua as etapas em uma das seções a seguir, conforme necessário.

    Os endereços IP públicos têm uma taxa nominal. Para saber mais sobre preços de endereço IP, leia a página de [preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Há um limite para o número de endereços IP públicos que podem ser usados em uma assinatura. Para saber mais sobre os limites, leia o artigo [Azure limites](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) (Limites do artigo).

    - **Associar o recurso a uma nova configuração de IP**
    
        Sempre que você adicionar um endereço IP público em uma nova configuração de IP, também deverá adicionar um endereço IP privado, pois todas as configurações de IP devem ter um endereço IP privado. Você pode adicionar um recurso de endereço IP público existente ou criar um novo. Para criar um novo, insira o seguinte comando:
    
        ```bash
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        Para criar uma nova configuração de IP com um endereço IP privado estático e o recurso de endereço IP público *myPublicIP3* associado, digite o seguinte comando:

        ```bash
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **Associar o recurso a uma configuração de IP existente** Um recurso de endereço IP público só pode ser associado a uma configuração de IP que ainda não tenha um associado. Você pode determinar se uma configuração de IP tem um endereço IP público associado digitando o seguinte comando:

        ```bash
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        Saída retornada:
    
            Name        PublicIpAddressId
            
            ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
            IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
            IPConfig-3

        Como a coluna **PublicIpAddressId** para *ipconfig-3* está em branco na saída, nenhum recurso de endereço IP público está associado a ele no momento. Você pode adicionar um recurso de endereço IP público existente ao IpConfig-3 ou inserir o seguinte comando para criar um:

        ```bash
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        Digite o seguinte comando para associar o recurso de endereço IP público à configuração de IP existente chamada *ipconfig-3*:
    
        ```bash
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. Exiba os endereços IP privados e as IDs de recurso de endereço IP público atribuídos à NIC digitando o seguinte comando:

    ```bash
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    Saída retornada: <br>
    
        Name        PrivateIpAddress    PrivateIpAllocationMethod   PublicIpAddressId
        
        ipconfig1   10.0.0.4            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  10.0.0.5            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3  10.0.0.6            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    

4. Adicione os endereços IP privados que você adicionou à NIC ao sistema operacional da VM seguindo as instruções na seção [adicionar endereços IP a um sistema operacional de VM](#os-config) deste artigo. Não adicione os endereços IP públicos ao sistema operacional.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
