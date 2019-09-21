---
title: Usar o DNS interno para a resolução de nome da VM com o CLI do Azure | Microsoft Docs
description: Como criar placas de interface de rede virtual e usar o DNS interno para a resolução de nomes de VM no Azure com o CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 02/16/2017
ms.author: cynthn
ms.openlocfilehash: b143a28216d0d8417615ad2c40a3b66c9c5d1be2
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173867"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Criar placas de interface de rede virtual e usar o DNS interno para a resolução de nomes de VM no Azure

Este artigo mostra como definir nomes DNS internos estáticos para VMs Linux usando vNics (placas de interface de rede virtual) e nomes de rótulo DNS com o CLI do Azure. Os nomes DNS estáticos são usados para serviços de infraestrutura permanentes, como um servidor de compilação Jenkins, que é usado para este documento ou um servidor git.

Os requisitos são:

* [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/)
* [Ficheiros de chaves públicas e privadas SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Comandos rápidos
Se você precisar realizar rapidamente a tarefa, a seção a seguir detalha os comandos necessários. Informações mais detalhadas e contexto para cada etapa podem ser encontrados no restante do documento, [começando aqui](#detailed-walkthrough). Para executar essas etapas, você precisará do [CLI do Azure](/cli/azure/install-az-cli2) mais recente instalado e conectado a uma conta do Azure usando [AZ login](/cli/azure/reference-index).

Pré-requisitos: Grupo de recursos, rede virtual e sub-rede, grupo de segurança de rede com SSH de entrada.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Criar uma placa de interface de rede virtual com um nome DNS interno estático
Crie o vNic com [AZ Network NIC Create](/cli/azure/network/nic). O `--internal-dns-name` sinalizador da CLI é para definir o rótulo DNS, que fornece o nome DNS estático para a placa de interface de rede virtual (vNic). O exemplo a seguir cria um vNic `myNic`chamado, conecta-o `myVnet` à rede virtual e cria um registro de nome DNS interno `jenkins`chamado:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Implantar uma VM e conectar o vNic
Crie uma VM com [az vm create](/cli/azure/vm). O `--nics` sinalizador conecta o vNic à VM durante a implantação para o Azure. O exemplo a seguir cria uma VM `myVM` chamada com o Azure Managed disks e anexa o vNic `myNic` nomeado da etapa anterior:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="detailed-walkthrough"></a>Instruções detalhadas

Uma infraestrutura completa de integração contínua e implantação contínua (CiCd) no Azure exige que determinados servidores sejam servidores estáticos ou de longa duração. É recomendável que os ativos do Azure, como as redes virtuais e os grupos de segurança de rede, sejam recursos estáticos e de longa duração que raramente são implantados. Depois que uma rede virtual é implantada, ela pode ser reutilizada por novas implantações sem nenhum efeito adverso na infraestrutura. Posteriormente, você pode adicionar um servidor de repositório git ou um servidor de automação Jenkins que fornece CiCd a essa rede virtual para seus ambientes de desenvolvimento ou teste.  

Nomes DNS internos só podem ser resolvidos dentro de uma rede virtual do Azure. Como os nomes DNS são internos, eles não podem ser resolvidos para a Internet externa, fornecendo segurança adicional à infraestrutura.

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo pelos seus próprios valores. Os nomes de parâmetro `myResourceGroup`de `myNic`exemplo incluem `myVM`, e.

## <a name="create-the-resource-group"></a>Criar o grupo de recursos
Primeiro, crie o grupo de recursos com [AZ Group Create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>Criar a rede virtual

A próxima etapa é criar uma rede virtual na qual as VMs serão iniciadas. A rede virtual contém uma sub-rede para esta explicação. Para obter mais informações sobre redes virtuais do Azure, consulte [criar uma rede virtual](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). 

Crie a rede virtual com [AZ Network vnet Create](/cli/azure/network/vnet). O exemplo a seguir cria uma rede virtual `myVnet` chamada e a `mySubnet`sub-rede denominada:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>Criar o grupo de segurança de rede
Os grupos de segurança de rede do Azure são equivalentes a um firewall na camada de rede. Para obter mais informações sobre grupos de segurança de rede, consulte [como criar NSGs no CLI do Azure](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Crie o grupo de segurança de rede com [AZ Network NSG Create](/cli/azure/network/nsg). O exemplo a seguir cria um grupo de segurança `myNetworkSecurityGroup`de rede chamado:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Adicionar uma regra de entrada para permitir o SSH
Adicione uma regra de entrada para o grupo de segurança de rede com [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule). O exemplo a seguir cria uma regra `myRuleAllowSSH`chamada:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myRuleAllowSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="associate-the-subnet-with-the-network-security-group"></a>Associar a sub-rede ao grupo de segurança de rede
Para associar a sub-rede ao grupo de segurança de rede, use [AZ Network vnet subnet Update](/cli/azure/network/vnet/subnet). O exemplo a seguir associa o nome `mySubnet` da sub-rede ao grupo de segurança `myNetworkSecurityGroup`de rede chamado:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>Criar a placa de interface de rede virtual e os nomes DNS estáticos
O Azure é muito flexível, mas para usar nomes DNS para resolução de nomes de VM, você precisa criar vNics (placas de interface de rede virtual) que incluam um rótulo DNS. vNics são importantes, pois você pode reutilizá-los conectando-os a VMs diferentes no ciclo de vida da infraestrutura. Essa abordagem mantém o vNic como um recurso estático, enquanto as VMs podem ser temporárias. Usando a rotulagem DNS no vNic, podemos habilitar a resolução de nome simples de outras VMs na VNet. O uso de nomes resolvível permite que outras VMs acessem o servidor de automação pelo nome `Jenkins` DNS ou pelo servidor git como. `gitrepo`  

Crie o vNic com [AZ Network NIC Create](/cli/azure/network/nic). O exemplo a seguir cria um vNic `myNic`chamado, conecta-o `myVnet` à rede virtual `myVnet`denominada e cria um registro de nome DNS `jenkins`interno chamado:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Implantar a VM na infraestrutura de rede virtual
Agora temos uma rede virtual e uma sub-rede, um grupo de segurança de rede atuando como um firewall para proteger nossa sub-rede bloqueando todo o tráfego de entrada, exceto a porta 22 para SSH, e um vNic. Agora você pode implantar uma VM dentro desta infraestrutura de rede existente.

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo a seguir cria uma VM `myVM` chamada com o Azure Managed disks e anexa o vNic `myNic` nomeado da etapa anterior:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Usando os sinalizadores da CLI para chamar os recursos existentes, instruímos o Azure a implantar a VM dentro da rede existente. Para reiterar, depois que uma VNet e uma sub-rede foram implantadas, elas podem ser deixadas como recursos estáticos ou permanentes dentro de sua região do Azure.  

## <a name="next-steps"></a>Passos seguintes
* [Criar um ambiente personalizado para uma VM com Linux diretamente através dos comandos da CLI do Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM do Linux no Azure usando modelos](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
