---
title: Utilizar o DNS interno para resolução de nomes VM com a CLI do Azure | Documentos da Microsoft
description: Como criar cartões de interface de rede virtual e utilizar o DNS interno para resolução de nomes VM no Azure com a CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 02/16/2017
ms.author: v-livech
ms.openlocfilehash: 5e893d597c2193676cb350fc80d7baa694ad6fd1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60478264"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Criar cartões de interface de rede virtual e utilizar o DNS interno para resolução de nomes VM no Azure

Este artigo mostra-lhe como definir estáticos para VMs do Linux utilizar placas de interface de rede virtuais (vNics) a nomes DNS internos e nomes de etiqueta DNS com a CLI do Azure. Nomes DNS estáticos são usados para serviços de infraestrutura permanente, como um servidor de compilação do Jenkins, que é utilizado para este documento ou um servidor de Git.

Os requisitos são:

* [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/)
* [Ficheiros de chaves públicas e privadas SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Comandos rápidos
Se precisar de realizar a tarefa com rapidez, a secção seguinte fornece detalhes sobre os comandos necessários. Mais informações detalhadas e contexto para cada etapa pode ser encontrada no resto do documento, [a partir de aqui](#detailed-walkthrough). Para executar estes passos, terá da versão mais recente [CLI do Azure](/cli/azure/install-az-cli2) instalado e registado à utilização conta do Azure [início de sessão az](/cli/azure/reference-index).

Pré-requisitos: Grupo de recursos, rede virtual e sub-rede, grupo de segurança de rede com SSH de entrada.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Criar uma placa de interface de rede virtual com um nome DNS interno estático
Criar a vNic com [nic da rede de az criar](/cli/azure/network/nic). O `--internal-dns-name` sinalizador CLI é para definir a etiqueta DNS, que fornece o nome DNS estático para a placa de interface de rede virtual (vNic). O exemplo seguinte cria um vNic com o nome `myNic`, liga-o para o `myVnet` rede virtual e cria um registo de nome DNS interno chamado `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Implementar uma VM e ligue-se a vNic
Crie uma VM com [az vm create](/cli/azure/vm). O `--nics` sinalizador liga-se a vNic na VM durante a implementação para o Azure. O exemplo seguinte cria uma VM com o nome `myVM` com o Managed Disks do Azure e anexa a vNic com o nome `myNic` do passo anterior:

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

Um completa integração e implementação contínua (CI/CD) infraestrutura no Azure requer que determinados servidores para serem servidores estáticos ou de longa duração. Recomenda-se que os recursos do Azure, como as redes virtuais e os grupos de segurança de rede são estáticos e longa duração recursos que raramente são implementados. Assim que tiver sido implementada uma rede virtual, pode ser reutilizado por novas Implantações sem qualquer afeta adverso na infra-estrutura. Posteriormente, pode adicionar um servidor de repositório de Git ou um servidor de automatização do Jenkins fornece CI/CD para esta rede virtual para o seu desenvolvimento ou ambientes de teste.  

Nomes DNS internos são apenas puder ser resolvidos dentro de uma rede virtual do Azure. Uma vez que os nomes DNS são internos, não são puder ser resolvidos na Internet fora, fornecer segurança adicional para a infraestrutura.

Nos exemplos a seguir, substitua os nomes de parâmetros de exemplo pelos seus próprios valores. Os nomes de parâmetros de exemplo incluem `myResourceGroup`, `myNic`, e `myVM`.

## <a name="create-the-resource-group"></a>Criar o grupo de recursos
Primeiro, crie o grupo de recursos com [criar grupo az](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na localização `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>Criar a rede virtual

A próxima etapa é criar uma rede virtual para iniciar as VMs em. A rede virtual contém uma sub-rede para este passo a passo. Para obter mais informações sobre redes virtuais do Azure, consulte [criar uma rede virtual](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). 

Criar a rede virtual com [vnet de rede de az criar](/cli/azure/network/vnet). O exemplo seguinte cria uma rede virtual denominada `myVnet` e a sub-rede com o nome `mySubnet`:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>Criar o grupo de segurança de rede
Os grupos de segurança de rede do Azure são equivalentes a uma firewall na camada da rede. Para obter mais informações sobre os grupos de segurança de rede, consulte [como criar NSGs na CLI do Azure](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Criar o grupo de segurança de rede com [nsg de rede de az criar](/cli/azure/network/nsg). O exemplo seguinte cria um grupo de segurança de rede com o nome `myNetworkSecurityGroup`:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Adicionar uma regra de entrada para permitir que o SSH
Adicionar uma regra de entrada para o grupo de segurança de rede com [criar regra de nsg de rede de az](/cli/azure/network/nsg/rule). O exemplo seguinte cria uma regra com o nome `myRuleAllowSSH`:

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

## <a name="associate-the-subnet-with-the-network-security-group"></a>Associar a sub-rede com o grupo de segurança de rede
Para associar a sub-rede com o grupo de segurança de rede, utilize [atualização de sub-rede de vnet de rede de az](/cli/azure/network/vnet/subnet). O exemplo seguinte associa o nome da sub-rede `mySubnet` com o grupo de segurança de rede com o nome `myNetworkSecurityGroup`:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>Criar o cartão de interface de rede virtual e nomes DNS estáticos
O Azure é muito flexível, mas usar nomes DNS para resolução de nomes VM, tem de criar a rede virtual placas de interface (vNics) que incluem uma etiqueta de DNS. vNics são importantes, como pode reutilizá-los ao ligá-las para diferentes VMs durante o ciclo de vida da infraestrutura. Essa abordagem mantém a vNic como um recurso estático, enquanto as VMs podem ser temporárias. Ao utilizar a etiquetagem na vNic DNS, é possível habilitar a resolução de nome simples de outras VMs na VNet. Utilizar nomes resolvíveis permite que outras VMs aceder ao servidor de automatização com o nome DNS `Jenkins` ou o servidor de Git como `gitrepo`.  

Criar a vNic com [nic da rede de az criar](/cli/azure/network/nic). O exemplo seguinte cria um vNic com o nome `myNic`, liga-o para o `myVnet` com o nome de rede virtual `myVnet`e cria um registo de nome DNS interno chamado `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Implementar a VM numa infraestrutura de rede virtual
Agora, temos uma rede virtual e uma sub-rede, um grupo de segurança de rede que funcionam como uma firewall para proteger nossos sub-rede ao bloquear todo o tráfego de entrada, exceto a porta 22 para SSH e um vNic. Agora, pode implementar uma VM esta infraestrutura de rede existente.

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo seguinte cria uma VM com o nome `myVM` com o Managed Disks do Azure e anexa a vNic com o nome `myNic` do passo anterior:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Ao utilizar os sinalizadores CLI para destacar os recursos existentes, que eu o instruir do Azure para implementar a VM dentro da rede existente. Para reiterar, depois de uma VNet e sub-rede tem sido implementados, pode ser deixados como recursos estáticos ou permanentes na sua região do Azure.  

## <a name="next-steps"></a>Passos Seguintes
* [Criar um ambiente personalizado para uma VM com Linux diretamente através dos comandos da CLI do Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM do Linux no Azure através de modelos](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
