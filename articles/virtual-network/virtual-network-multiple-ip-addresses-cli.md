---
title: VM com vários endereços IP usando o Azure CLI
titlesuffix: Azure Virtual Network
description: Saiba como atribuir vários endereços IP a uma máquina virtual utilizando a interface de linha de comando Azure (CLI).
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
ms.openlocfilehash: 144f30463adb3dfbce1717e06548baccc8286f8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240229"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Atribuir vários endereços IP a máquinas virtuais utilizando o Azure CLI

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Este artigo explica como criar uma máquina virtual (VM) através do modelo de implementação do Gestor de Recursos Azure utilizando o Azure CLI. Vários endereços IP não podem ser atribuídos aos recursos criados através do modelo de implementação clássico. Para saber mais sobre os modelos de implementação do Azure, leia o artigo dos [modelos de implementação Da Understand.](../resource-manager-deployment-model.md)

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Criar uma VM com vários endereços IP

Os passos que se seguem explicam como criar um exemplo de máquina virtual com vários endereços IP, como descrito no cenário. Altere os valores variáveis nos tipos de endereços "" e IP, conforme necessário, para a sua implementação. 

1. Instale o [Azure CLI](/cli/azure/install-azure-cli) se ainda não o tiver instalado.
2. Crie um par de chaves públicos e privados para VMs Linux, completando os passos no par de [chaves públicos e privados Create a SSH para VMs Linux.](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
3. A partir de uma concha `az login` de comando, inicie sessão com o comando e selecione a subscrição que está a utilizar.
4. Crie o VM executando o script que se segue num computador Linux ou Mac. O script cria um grupo de recursos, uma rede virtual (VNet), um NIC com três configurações IP, e um VM com os dois NICs ligados a ele. Os recursos NIC, IP públicos, rede virtual e VM devem existir todos no mesmo local e subscrição. Embora os recursos não tenham de existir todos no mesmo grupo de recursos, no seguinte guião eles existem.

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

Além de criar um VM com um NIC com configurações de 3 IP, o script cria:

- Um único disco gerido premium por padrão, mas tem outras opções para o tipo de disco que pode criar. Leia o [VM Create a Linux utilizando o artigo do Azure CLI](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para mais detalhes.
- Uma rede virtual com uma subnet e dois endereços IP públicos. Em alternativa, pode *utilizar* recursos de endereços de endereços ip existentes, rede, NIC ou IP públicos. Para aprender a utilizar os recursos de rede `az vm create -h`existentes em vez de criar recursos adicionais, entre.

Os endereços IP públicos têm uma taxa nominal. Para saber mais sobre os preços do endereço IP, leia a página de preços do [endereço IP.](https://azure.microsoft.com/pricing/details/ip-addresses) Existe um limite para o número de endereços IP públicos que podem ser usados numa subscrição. Para saber mais sobre os limites, leia o artigo [Azure limites](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) (Limites do artigo).

Depois de criado o VM, introduza o `az network nic show --name MyNic1 --resource-group myResourceGroup` comando para visualizar a configuração NIC. Introduza `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` a lista para visualizar uma lista das configurações IP associadas ao NIC.

Adicione os endereços IP privados ao sistema operativo VM, preenchendo as etapas do seu sistema operativo nos [endereços ADD IP a uma](#os-config) secção do sistema operativo VM deste artigo.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>Adicione endereços IP a um VM

Pode adicionar endereços IP privados e públicos adicionais a uma interface de rede Azure existente, completando os passos que se seguem. Os exemplos baseiam-se no [cenário](#scenario) descrito neste artigo.

1. Abra uma concha de comando e complete os passos restantes nesta secção numa única sessão. Se ainda não tiver o Azure CLI instalado e configurado, complete os passos no artigo de [instalação Do CLI Azure](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) e faça login na sua conta Azure com o `az-login` comando.

2. Complete os passos numa das seguintes secções, com base nos seus requisitos:

    **Adicione um endereço IP privado**
    
    Para adicionar um endereço IP privado a um NIC, deve criar uma configuração IP utilizando o comando que se segue. O endereço IP estático deve ser um endereço não utilizado para a sub-rede.

    ```azurecli
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Crie as configurações necessárias, utilizando nomes de configuração únicos e endereços IP privados (para configurações com endereços IP estáticos).

    **Adicione um endereço IP público**
    
    Um endereço IP público é adicionado associando-o a uma nova configuração IP ou a uma configuração IP existente. Complete os passos numa das secções que se seguem, conforme necessário.

    Os endereços IP públicos têm uma taxa nominal. Para saber mais sobre os preços do endereço IP, leia a página de preços do [endereço IP.](https://azure.microsoft.com/pricing/details/ip-addresses) Existe um limite para o número de endereços IP públicos que podem ser usados numa subscrição. Para saber mais sobre os limites, leia o artigo [Azure limites](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) (Limites do artigo).

    - **Associar o recurso a uma nova configuração IP**
    
        Sempre que adicionar um endereço IP público numa nova configuração IP, também deve adicionar um endereço IP privado, porque todas as configurações IP devem ter um endereço IP privado. Pode adicionar um recurso de endereço IP público existente ou criar um novo. Para criar um novo, insira o seguinte comando:
    
        ```azurecli
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        Para criar uma nova configuração IP com um endereço IP privado estático e o recurso de endereço IP público associado do *myPublicIP3,* insira o seguinte comando:

        ```azurecli
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **Associar o recurso a uma configuração IP existente** Um recurso de endereço IP público só pode ser associado a uma configuração IP que ainda não tem uma associada. Pode determinar se uma configuração IP tem um endereço IP público associado, introduzindo o seguinte comando:

        ```azurecli
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        Saída devolvida:
    
            Name        PublicIpAddressId
            
            ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
            IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
            IPConfig-3

        Uma vez que a coluna **PublicIpAddressId** para *IpConfig-3* está em branco na saída, nenhum recurso de endereço IP público está atualmente associado ao mesmo. Pode adicionar um recurso de endereço IP público existente ao IpConfig-3, ou introduzir o seguinte comando para criar um:

        ```azurecli
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        Introduza o seguinte comando para associar o recurso de endereço IP público à configuração IP existente chamada *IPConfig-3*:
    
        ```azurecli
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. Consulte os endereços IP privados e os ids de endereço IP públicos atribuídos ao NIC, inserindo o seguinte comando:

    ```azurecli
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    Saída devolvida: <br>
    
        Name        PrivateIpAddress    PrivateIpAllocationMethod   PublicIpAddressId
        
        ipconfig1   10.0.0.4            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  10.0.0.5            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3  10.0.0.6            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    

4. Adicione os endereços IP privados adicionados ao NIC ao sistema operativo VM seguindo as instruções nos [endereços IP adicionais a uma](#os-config) secção do sistema operativo VM deste artigo. Não adicione os endereços IP públicos ao sistema operativo.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
