---
title: Utilize DNS internos para resolução de nome vm com o CLI Azure
description: Como criar cartões de interface de rede virtual e utilizar DNS internos para resolução de nome VM em Azure com o Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/16/2017
ms.author: cynthn
ms.openlocfilehash: 38f231e63ad4974a23a1201aad4d290685860b50
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87292160"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Crie cartões de interface de rede virtual e utilize DNS internos para resolução de nome VM no Azure

Este artigo mostra-lhe como definir nomes de DNS internos estáticos para Os VMs do Linux utilizando cartões de interface de rede virtual (vNics) e nomes de etiquetas DNS com o CLI Azure. Os nomes de DNS estáticos são usados para serviços de infraestrutura permanente, como um servidor de construção Jenkins, que é usado para este documento, ou um servidor Git.

Os requisitos são:

* [uma conta Azure](https://azure.microsoft.com/pricing/free-trial/)
* [Ficheiros de chaves públicas e privadas SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Comandos rápidos
Se precisar de realizar rapidamente a tarefa, a secção seguinte detalha os comandos necessários. Informações e contextos mais detalhados para cada passo podem ser encontrados no resto do documento, [a partir daqui.](#detailed-walkthrough) Para realizar estes passos, precisa do mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e iniciado numa conta Azure utilizando [o login az](/cli/azure/reference-index).

Pré-Requisitos: Grupo de Recursos, rede virtual e sub-rede, Grupo de Segurança de Rede com entrada SSH.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Crie um cartão de interface de rede virtual com um nome de DNS interno estático
Crie o vNic com [az network nic create](/cli/azure/network/nic). A `--internal-dns-name` bandeira CLI destina-se à definição da etiqueta DNS, que fornece o nome estático dns para o cartão de interface de rede virtual (vNic). O exemplo a seguir cria um vNic `myNic` nomeado, liga-o à `myVnet` rede virtual, e cria um registo interno de nome DNS chamado `jenkins` :

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Implementar um VM e ligar o vNic
Crie uma VM com [az vm create](/cli/azure/vm). A `--nics` bandeira liga o vNic ao VM durante a colocação em Azure. O exemplo a seguir cria um VM nomeado `myVM` com Discos Geridos Azure e anexa o vNic nomeado `myNic` a partir do passo anterior:

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

Uma infraestrutura de integração contínua e implementação contínua (CiCd) em Azure requer que certos servidores sejam servidores estáticos ou de longa duração. Recomenda-se que os ativos da Azure, como as redes virtuais e os Grupos de Segurança de Rede, sejam recursos estáticos e de longa duração que raramente são implantados. Uma vez implementada uma rede virtual, pode ser reutilizada por novas implementações sem quaisquer efeitos adversos na infraestrutura. Mais tarde, pode adicionar um servidor de repositório Git ou um servidor de automação Jenkins que entrega o CiCd a esta rede virtual para o seu desenvolvimento ou ambientes de teste.  

Os nomes de DNS internos só são resolúveis dentro de uma rede virtual Azure. Como os nomes dns são internos, não são resolúveis para a internet externa, proporcionando segurança adicional à infraestrutura.

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo pelos seus próprios valores. Os nomes dos parâmetros incluem `myResourceGroup` `myNic` , e `myVM` .

## <a name="create-the-resource-group"></a>Criar o grupo de recursos
Em primeiro lugar, crie o grupo de recursos com [a criação do grupo AZ](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>Criar a rede virtual

O próximo passo é construir uma rede virtual para lançar os VMs. A rede virtual contém uma sub-rede para este walkthrough. Para obter mais informações sobre redes virtuais Azure, consulte [Criar uma rede virtual.](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network) 

Crie a rede virtual com [a criação de vnet de rede Az.](/cli/azure/network/vnet) O exemplo a seguir cria uma rede virtual chamada `myVnet` e sub-rede `mySubnet` chamada:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>Criar o Grupo de Segurança da Rede
Os grupos de segurança da rede Azure são equivalentes a uma firewall na camada de rede. Para obter mais informações sobre grupos de segurança de rede, consulte [Como criar NSGs no Azure CLI](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Crie o grupo de segurança de rede com [a criação de nsg de rede az](/cli/azure/network/nsg). O exemplo a seguir cria um grupo de segurança de rede `myNetworkSecurityGroup` chamado:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Adicione uma regra de entrada para permitir sSH
Adicione uma regra de entrada para o grupo de segurança da rede com [a regra az network nsg criar](/cli/azure/network/nsg/rule). O exemplo a seguir cria uma regra chamada `myRuleAllowSSH` :

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

## <a name="associate-the-subnet-with-the-network-security-group"></a>Associar a sub-rede ao Grupo de Segurança de Rede
Para associar a sub-rede ao Grupo de Segurança da Rede, utilize [a atualização da sub-rede de rede Az.](/cli/azure/network/vnet/subnet) O exemplo a seguir associa o nome da sub-rede `mySubnet` ao Grupo de Segurança da Rede denominado `myNetworkSecurityGroup` :

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>Crie o cartão de interface de rede virtual e os nomes estáticos de DNS
O Azure é muito flexível, mas para utilizar nomes DNS para resolução de nomeS VM, é necessário criar cartões de interface de rede virtual (vNics) que incluam uma etiqueta DNS. vNics são importantes pois você pode reutilizá-los ligando-os a diferentes VMs ao longo do ciclo de vida da infraestrutura. Esta abordagem mantém o vNic como um recurso estático enquanto os VMs podem ser temporários. Ao utilizar a rotulagem DNS no vNic, somos capazes de permitir uma resolução simples de nomes de outros VMs no VNet. A utilização de nomes resolúveis permite que outros VMs acedam ao servidor de automação pelo nome DNS `Jenkins` ou pelo servidor Git como `gitrepo` .  

Crie o vNic com [az network nic create](/cli/azure/network/nic). O exemplo a seguir cria um vNic chamado `myNic` , liga-o à `myVnet` rede virtual chamada , e cria um registo interno de nome `myVnet` DNS chamado `jenkins` :

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Implementar o VM na infraestrutura de rede virtual
Dispomos agora de uma rede virtual e sub-rede, um Grupo de Segurança de Rede que atua como uma firewall para proteger a nossa sub-rede bloqueando todo o tráfego de entrada, exceto a porta 22 para sSH, e um vNic. Agora pode implantar um VM dentro desta infraestrutura de rede existente.

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo a seguir cria um VM nomeado `myVM` com Discos Geridos Azure e anexa o vNic nomeado `myNic` a partir do passo anterior:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Ao usar as bandeiras do CLI para chamar a atenção para os recursos existentes, instruímos a Azure a implantar o VM dentro da rede existente. Para reiterar, uma vez que um VNet e sub-rede tenham sido implantados, eles podem ser deixados como recursos estáticos ou permanentes dentro da sua região de Azure.  

## <a name="next-steps"></a>Passos seguintes
* [Criar um ambiente personalizado para uma VM com Linux diretamente através dos comandos da CLI do Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Crie um Linux VM em Azure usando modelos](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
