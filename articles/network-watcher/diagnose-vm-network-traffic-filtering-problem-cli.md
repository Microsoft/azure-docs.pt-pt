---
title: 'Quickstart: Diagnosticar um problema de filtro de tráfego de rede VM - Azure CLI'
titleSuffix: Azure Network Watcher
description: Aprenda a usar o Azure CLI para diagnosticar um problema de filtro de tráfego de rede de máquinas virtuais utilizando a capacidade de verificação do fluxo IP do Observador de Redes Azure.
services: network-watcher
documentationcenter: network-watcher
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose a virtual machine (VM) network traffic filter problem that prevents communication to and from a VM.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: kumud
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 536a9e304d111b16e39c3187560a7a529d05c937
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013157"
---
# <a name="quickstart-diagnose-a-virtual-machine-network-traffic-filter-problem---azure-cli"></a>Início Rápido: Diagnosticar um problema de filtro de tráfego de rede na máquina virtual - CLI do Azure

Neste início rápido, vai implementar uma máquina virtual (VM) e, em seguida, verificar as comunicações para um endereço IP e URL e de um endereço IP. Vai determinar a causa de uma falha de comunicação e aprender a resolvê-la.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este arranque rápido requer a versão 2.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada. 

- Os comandos Azure CLI neste quickstart são formatados para correr numa concha bash.

## <a name="create-a-vm"></a>Criar uma VM

Antes de criar uma VM, tem de criar um grupo de recursos para conter a VM. Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *este:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Crie uma VM com [az vm create](/cli/azure/vm). Se as chaves SSH ainda não existirem numa localização de chaves predefinida, o comando cria-as. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`. O exemplo a seguir cria um VM chamado *myVm:*

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

A criação da VM demora alguns minutos. Não continue com os passos restantes até que o VM seja criado e o Azure CLI devolva a saída.

## <a name="test-network-communication"></a>Testar a comunicação de rede

Para testar a comunicação de rede com o Observador de Rede, tem primeiro ativar um observador de rede na região onde se encontra a VM que pretende testar e, em seguida, utilizar a função de verificação do fluxo IP do Observador de Rede para testar a comunicação.

### <a name="enable-network-watcher"></a>Ativar o observador de rede

Se já tiver um observador de rede ativado na região E.U.A. Leste, avance para [Utilizar a verificação do fluxo IP](#use-ip-flow-verify). Utilize o comando [az network watcher configure](/cli/azure/network/watcher#az-network-watcher-configure) para criar um observador de rede na região E.U.A. Leste:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-ip-flow-verify"></a>Utilizar a verificação do fluxo IP

Quando cria uma VM, o Azure permite e recusa o tráfego de rede de e para a VM, por predefinição. Mais tarde, poderá substituir as predefinições do Azure, ao permitir ou recusar tipos adicionais de tráfego. Para testar se o tráfego é permitido ou recusado para destinos diferentes e a partir de um endereço IP de origem, utilize o comando [az network watcher test-ip-flow](/cli/azure/network/watcher#az-network-watcher-test-ip-flow).

Teste a comunicação de saída a partir da VM para um dos endereços IP para www.bing.com:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction outbound \
  --local 10.0.0.4:60000 \
  --protocol TCP \
  --remote 13.107.21.200:80 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

Após alguns segundos, o resultado devolvido informa-o de que é permitido o acesso devido a uma regra de segurança denominada **AllowInternetOutbound**.

Teste a comunicação de saída da VM para 172.31.0.100:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction outbound \
  --local 10.0.0.4:60000 \
  --protocol TCP \
  --remote 172.31.0.100:80 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

O resultado devolvido informa-o de que o acesso foi recusado devido a uma regra de segurança denominada **DefaultOutboundDenyAll**.

Teste a comunicação de entrada para a VM a partir de 172.31.0.100:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction inbound \
  --local 10.0.0.4:80 \
  --protocol TCP \
  --remote 172.31.0.100:60000 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

O resultado devolvido informa-o de que o acesso foi recusado devido a uma regra de segurança denominada **DefaultInboundDenyAll**. Agora que sabe quais as regras de segurança que estão a permitir ou a recusar tráfego de ou para uma VM, pode determinar como resolver os problemas.

## <a name="view-details-of-a-security-rule"></a>Ver detalhes de uma regra de segurança

Para determinar o motivo pelo qual as regras em [Utilizar verificação de fluxo IP](#use-ip-flow-verify) estão a permitir ou a impedir a comunicação, reveja as regras de segurança efetivas para a interface de rede com o comando [az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg):

```azurecli-interactive
az network nic list-effective-nsg \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

O resultado devolvido inclui o seguinte texto para a regra **AllowInternetOutbound** que permitiu o acesso de saída para www.bing.com num passo anterior em [Utilizar a verificação do fluxo IP](#use-ip-flow-verify):

```console
{
 "access": "Allow",
 "additionalProperties": {},
 "destinationAddressPrefix": "Internet",
 "destinationAddressPrefixes": [
  "Internet"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Outbound",
 "expandedDestinationAddressPrefix": [
  "1.0.0.0/8",
  "2.0.0.0/7",
  "4.0.0.0/6",
  "8.0.0.0/7",
  "11.0.0.0/8",
  "12.0.0.0/6",
  ...
 ],
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/AllowInternetOutBound",
 "priority": 65001,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
},
```

No resultado anterior, pode ver que **destinationAddressPrefix** é **Internet**. No entanto, não é claro de que forma 13.107.21.200 se relaciona com **Internet**. Você vê vários prefixos de endereço listados no **âmbito do ExpandedDestinationAddressPrefix**. Um dos prefixos na lista é **12.0.0.0/6**, que abrange o intervalo 12.0.0.1-15.255.255.254 de endereços IP. Uma vez que 13.107.21.200 está dentro desse intervalo de endereços, a regra **AllowInternetOutBound** permite o tráfego de saída. Além disso, não existem regras com prioridade superior (número inferior) apresentadas no resultado anterior que substituam esta regra. Para recusar comunicações de saída para um endereço IP, pode adicionar uma regra de segurança com uma prioridade mais elevada, que recusa a saída da porta 80 para o endereço IP.

Quando executou o comando `az network watcher test-ip-flow` para testar a comunicação de saída para 172.131.0.100 em [Utilizar a verificação do fluxo IP](#use-ip-flow-verify), o resultado informou-o de que a regra **DefaultOutboundDenyAll** recusou a comunicação. A regra **DefaultOutboundDenyAll** equivale à regra **DenyAllOutBound** apresentada no resultado seguinte do comando `az network nic list-effective-nsg`:

```console
{
 "access": "Deny",
 "additionalProperties": {},
 "destinationAddressPrefix": "0.0.0.0/0",
 "destinationAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Outbound",
 "expandedDestinationAddressPrefix": null,
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/DenyAllOutBound",
 "priority": 65500,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
}
```

A regra lista **0.0.0.0/0** como **destinoAddressPrefix**. A regra nega a comunicação de saída para 172.131.0.100, porque o endereço não está dentro do **destinoPrefixo** de qualquer outra regra de saída na saída a partir do `az network nic list-effective-nsg` comando. Para permitir a comunicação de saída, pode adicionar uma regra de segurança com uma prioridade mais elevada, que permite o tráfego de saída para a porta 80 em 172.131.0.100.

Quando executou o comando `az network watcher test-ip-flow` em [Utilizar a verificação do fluxo IP](#use-ip-flow-verify) para testar a comunicação de entrada de 172.131.0.100, o resultado informou-o de que a regra **DefaultInboundDenyAll** recusou a comunicação. A regra **DefaultInboundDenyAll** equivale à regra **DenyAllInBound** apresentada no resultado seguinte do comando `az network nic list-effective-nsg`:

```console
{
 "access": "Deny",
 "additionalProperties": {},
 "destinationAddressPrefix": "0.0.0.0/0",
 "destinationAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Inbound",
 "expandedDestinationAddressPrefix": null,
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/DenyAllInBound",
 "priority": 65500,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
},
```

A regra **DenyAllInBound** é aplicada porque, conforme apresentado no resultado, não existe outra regra de prioridade superior no resultado do comando `az network nic list-effective-nsg` que permita a entrada da porta 80 para a VM de 172.131.0.100. Para permitir a comunicação de entrada, pode adicionar uma regra de segurança com uma prioridade superior que permita a entrada da porta 80 de 172.131.0.100.

As verificações neste guia de início rápido testaram a configuração do Azure. Se as verificações devolverem os resultados esperados e continuar a ter problemas de rede, certifique-se de que não tem uma firewall entre a VM e o ponto final com o qual está a comunicar e que o sistema operativo na VM não tem uma firewall que esteja a permitir ou a recusar a comunicação.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, pode utilizar [az group delete](/cli/azure/group) para remover o grupo de recursos e todos os recursos que contém:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Próximos passos

Neste guia de início rápido, criou uma VM e diagnosticou filtros de tráfego de rede de entrada e saída. Aprendeu que as regras do grupo de segurança de rede permitem ou recusam tráfego de e para uma VM. Saiba mais sobre [regras de segurança](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e como [criar regras de segurança](../virtual-network/manage-network-security-group.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-security-rule).

Mesmo com os filtros de tráfego de rede adequados ativados, a comunicação para uma VM ainda pode falhar, devido à configuração de encaminhamento. Para saber como diagnosticar problemas de encaminhamento de rede de VM, veja [Diagnosticar problemas de encaminhamento de VM](diagnose-vm-network-routing-problem-cli.md) ou, para diagnosticar problemas de encaminhamento de saída, latência e filtro de tráfego, com uma ferramenta, veja [Resolver problemas de ligação](network-watcher-connectivity-cli.md).