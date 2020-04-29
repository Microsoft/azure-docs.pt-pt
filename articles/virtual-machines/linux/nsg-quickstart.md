---
title: Abrir portas para um VM com Azure CLI
description: Aprenda a abrir uma porta /crie um ponto final para o seu VM utilizando o Azure CLI.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: networking
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: c29fb075fc2d8b512070d7a6cf3fef949def5894
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066636"
---
# <a name="open-ports-and-endpoints-to-a-vm-with-the-azure-cli"></a>Abrir portas e pontos finais para um VM com o Azure CLI

Abre uma porta, ou cria um ponto final, para uma máquina virtual (VM) em Azure, criando um filtro de rede numa interface de rede de subnet ou VM. Coloca estes filtros, que controlam o tráfego de entrada e saída, num Grupo de Segurança da Rede ligado ao recurso que recebe o tráfego. Vamos usar um exemplo comum de tráfego web na porta 80. Este artigo mostra-lhe como abrir um porto para um VM com o Azure CLI. 


Para criar um Grupo de Segurança de Rede e regras, precisa do mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e registado numa conta Azure utilizando [login az](/cli/azure/reference-index).

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo pelos seus próprios valores. Exemplo nomes de parâmetros incluem *myResourceGroup,* *myNetworkSecurityGroup*, e *myVnet*.


## <a name="quickly-open-a-port-for-a-vm"></a>Abra rapidamente um porto para um VM
Se precisar de abrir rapidamente uma porta para um VM num cenário de dev/teste, pode utilizar o comando de [porta aberta az vM.](/cli/azure/vm) Este comando cria um Grupo de Segurança de Rede, adiciona uma regra e aplica-a a um VM ou subnet. O exemplo seguinte abre a porta *80* no VM denominado *myVM* no grupo de recursos chamado *myResourceGroup*.

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

Para um maior controlo sobre as regras, como a definição de uma gama de endereços IP fonte, prossiga com os passos adicionais neste artigo.


## <a name="create-a-network-security-group-and-rules"></a>Criar um Grupo de Segurança de Rede e regras
Crie o grupo de segurança da rede com a [rede az nsg criar](/cli/azure/network/nsg). O exemplo seguinte cria um grupo de segurança de rede chamado *myNetworkSecurityGroup* na localização *oriental:*

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Adicione uma regra com a [regra nsg da rede Az criar](/cli/azure/network/nsg/rule) para permitir o tráfego HTTP ao seu webserver (ou ajustar-se para o seu próprio cenário, como acesso sSH ou conectividade de base de dados). O exemplo seguinte cria uma regra chamada *myNetworkSecurityGroupRule* para permitir o tráfego de TCP na porta 80:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>Aplicar grupo de segurança de rede à VM
Associe o Grupo de Segurança da Rede à interface de rede (NIC) do seu VM com a atualização nic da [rede Az](/cli/azure/network/nic). O exemplo seguinte associa um NIC existente chamado *myNic* com o Grupo de Segurança da Rede chamado *myNetworkSecurityGroup*:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Em alternativa, pode associar o seu Grupo de Segurança de Rede a uma subnet de rede virtual com a [az network vnet update](/cli/azure/network/vnet/subnet) em vez de apenas à interface de rede num único VM. O exemplo seguinte associa uma subnet existente chamada *mySubnet* na rede virtual *myVnet* com o Grupo de Segurança da Rede chamado *myNetworkSecurityGroup*:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Mais informações sobre grupos de segurança da rede
Os comandos rápidos aqui permitem-lhe levantar-se e correr com o tráfego fluindo para o seu VM. Os Grupos de Segurança da Rede fornecem muitas funcionalidades e granularidade para controlar o acesso aos seus recursos. Pode ler mais sobre [a criação](tutorial-virtual-network.md#secure-network-traffic)de um Grupo de Segurança de Rede e regras ACL aqui .

Para aplicações web altamente disponíveis, deve colocar os seus VMs atrás de um Equilíbrio de Carga Azure. O equilibrador de carga distribui tráfego para VMs, com um Grupo de Segurança de Rede que fornece filtragem de tráfego. Para mais informações, consulte Como carregar o equilíbrio das [máquinas virtuais Linux em Azure para criar uma aplicação altamente disponível.](tutorial-load-balancer.md)

## <a name="next-steps"></a>Passos seguintes
Neste exemplo, criou uma regra simples para permitir o tráfego HTTP. Pode encontrar informações sobre a criação de ambientes mais detalhados nos seguintes artigos:

* [Visão geral do Gestor de Recursos Azure](../../azure-resource-manager/management/overview.md)
* [O que é um Grupo de Segurança de Rede (NSG)? (What is a Network Security Group (NSG)?)](../../virtual-network/security-overview.md)
