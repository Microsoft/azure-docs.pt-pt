---
title: Utilize DNS interno para resolução de nome VM com o Azure CLI
description: Como criar cartões de interface de rede virtual e utilizar DNS internos para resolução de nome VM no Azure com o ClI Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/16/2017
ms.author: cynthn
ms.openlocfilehash: 07a78e4987a844627824ac5034046cf6a393ad8d
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757841"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Crie cartões de interface de rede virtual e utilize DNS internos para resolução de nome VM no Azure

Este artigo mostra-lhe como definir nomes dNS internos estáticos para VMs Linux usando cartões de interface de rede virtuais (vNics) e nomes de etiquetas DNS com o ClI Azure. Os nomes dNS estáticos são usados para serviços de infraestrutura permanente, como um servidor de construção Jenkins, que é usado para este documento, ou um servidor Git.

Os requisitos são:

* [uma conta Azure](https://azure.microsoft.com/pricing/free-trial/)
* [Ficheiros de chaves públicas e privadas SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Comandos rápidos
Se precisar de realizar rapidamente a tarefa, a secção seguinte detalha os comandos necessários. Informações mais detalhadas e contexto para cada passo podem ser encontrados no resto do documento, [a partir](#detailed-walkthrough)daqui . Para realizar estes passos, necessita do mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e registado numa conta Azure utilizando [login az](/cli/azure/reference-index).

Pré-Requisitos: Grupo de Recursos, rede virtual e subnet, Network Security Group com entrada em SSH.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Crie um cartão de interface de rede virtual com um nome dNS interno estático
Crie o vNic com a [z network nic criar](/cli/azure/network/nic). A `--internal-dns-name` bandeira CLI destina-se a configurar a etiqueta DNS, que fornece o nome DNS estático para o cartão de interface de rede virtual (vNic). O exemplo seguinte cria `myNic`um vNic chamado `myVnet` , conecta-o à rede virtual, e cria um registo de nome interno do DNS chamado: `jenkins`

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Implementar um VM e ligar o vNic
Crie uma VM com [az vm create](/cli/azure/vm). A `--nics` bandeira liga o vNic ao VM durante a implantação a Azure. O exemplo seguinte cria `myVM` um VM nomeado com Discos Geridos `myNic` Azure e anexa o vNic nomeado a partir do passo anterior:

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

Uma infraestrutura de integração contínua completa e de implantação contínua (CiCd) no Azure requer que certos servidores sejam servidores estáticos ou de longa duração. Recomenda-se que os ativos do Azure, como as redes virtuais e os Grupos de Segurança da Rede, sejam recursos estáticos e de longa duração que raramente são implantados. Uma vez implantada uma rede virtual, pode ser reutilizada por novas implantações sem quaisquer efeitos adversos na infraestrutura. Mais tarde, pode adicionar um servidor de repositório Git ou um servidor de automação Jenkins entrega ciCd a esta rede virtual para o seu desenvolvimento ou ambientes de teste.  

Os nomes internos do DNS só são resolúveis dentro de uma rede virtual Azure. Como os nomes dNS são internos, não são resolúveis para a internet externa, proporcionando segurança adicional à infraestrutura.

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo pelos seus próprios valores. Exemplo nomes `myResourceGroup`de `myNic`parâmetros incluem, e `myVM`.

## <a name="create-the-resource-group"></a>Criar o grupo de recursos
Primeiro, crie o grupo de recursos com o [grupo Az criar](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>Criar a rede virtual

O próximo passo é construir uma rede virtual para lançar os VMs. A rede virtual contém uma subnet para esta passagem. Para obter mais informações sobre redes virtuais Azure, consulte [Criar uma rede virtual.](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network) 

Criar a rede virtual com a [rede az vnet criar](/cli/azure/network/vnet). O exemplo seguinte cria `myVnet` uma rede `mySubnet`virtual chamada:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>Criar o Grupo de Segurança da Rede
Os Grupos de Segurança da Rede Azure são equivalentes a uma firewall na camada de rede. Para obter mais informações sobre grupos de segurança de rede, consulte [como criar NSGs no Azure CLI](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Crie o grupo de segurança da rede com a [rede az nsg criar](/cli/azure/network/nsg). O exemplo seguinte cria um `myNetworkSecurityGroup`grupo de segurança de rede chamado:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Adicione uma regra de entrada para permitir o SSH
Adicione uma regra de entrada para o grupo de segurança da rede com a [regra nsg de rede az criar](/cli/azure/network/nsg/rule). O exemplo seguinte cria `myRuleAllowSSH`uma regra chamada:

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

## <a name="associate-the-subnet-with-the-network-security-group"></a>Associar a subnet ao Grupo de Segurança da Rede
Para associar a subnet ao Grupo de Segurança da Rede, utilize a atualização de subnet da [rede az](/cli/azure/network/vnet/subnet). O exemplo seguinte associa o `mySubnet` nome da sub-rede ao Grupo de Segurança da Rede denominado: `myNetworkSecurityGroup`

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>Crie o cartão de interface de rede virtual e os nomes dNS estáticos
O Azure é muito flexível, mas para utilizar nomes DNS para resolução de nomes VM, precisa de criar cartões de interface de rede virtuais (vNics) que incluam uma etiqueta DNS. vNics são importantes, pois pode reutilizá-los ligando-os a diferentes VMs ao longo do ciclo de vida da infraestrutura. Esta abordagem mantém o vNic como um recurso estático enquanto os VMs podem ser temporários. Ao utilizar a rotulagem DNS no vNic, somos capazes de permitir a resolução de nome simples de outros VMs no VNet. A utilização de nomes solucionáveis permite que outros `Jenkins` VMs acedam ao servidor de automatização pelo nome DNS ou pelo servidor Git como `gitrepo`.  

Crie o vNic com a [z network nic criar](/cli/azure/network/nic). O exemplo seguinte cria `myNic`um vNic chamado `myVnet` , `myVnet`conecta-o à rede virtual `jenkins`chamada, e cria um registo de nome interno do DNS chamado:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Implementar o VM na infraestrutura de rede virtual
Temos agora uma rede virtual e uma subnet, um Grupo de Segurança de Rede que atua como uma firewall para proteger a nossa subnet bloqueando todo o tráfego de entrada, exceto a porta 22 para SSH, e um vNic. Agora pode implantar um VM dentro desta infraestrutura de rede existente.

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo seguinte cria `myVM` um VM nomeado com Discos Geridos `myNic` Azure e anexa o vNic nomeado a partir do passo anterior:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Ao usar as bandeiras cli para chamar os recursos existentes, instruímos o Azure a implantar o VM dentro da rede existente. Para reiterar, uma vez implantado um VNet e uma subnet, podem ser deixados como recursos estáticos ou permanentes dentro da sua região azure.  

## <a name="next-steps"></a>Passos seguintes
* [Criar um ambiente personalizado para uma VM com Linux diretamente através dos comandos da CLI do Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Crie um VM Linux em Azure usando modelos](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
