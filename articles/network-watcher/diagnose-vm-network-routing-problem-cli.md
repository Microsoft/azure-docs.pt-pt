---
title: Diagnosticar um problema de encaminhamento de rede VM - Azure CLI
titleSuffix: Azure Network Watcher
description: Neste artigo, aprende-se a usar o Azure CLI para diagnosticar um problema de encaminhamento de rede de máquinas virtuais utilizando a próxima capacidade de lúpulo do Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 415fcc72116cc36644b58b619404d96ff63b024d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065927"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Diagnosticar um problema de encaminhamento de rede de máquinas virtuais - Azure CLI

Neste artigo, você implanta uma máquina virtual (VM) e, em seguida, verifique as comunicações para um endereço IP e URL. Vai determinar a causa de uma falha de comunicação e aprender a resolvê-la.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada. 

- Os comandos Azure CLI neste artigo são formatados para correr numa concha bash.

## <a name="create-a-vm"></a>Criar uma VM

Antes de criar uma VM, tem de criar um grupo de recursos para conter a VM. Crie um grupo de recursos com [az group create](/cli/azure/group#az-group-create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *este:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Crie uma VM com [az vm create](/cli/azure/vm#az-vm-create). Se as chaves SSH ainda não existirem numa localização de chaves predefinida, o comando cria-as. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`. O exemplo a seguir cria um VM chamado *myVm:*

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

A criação da VM demora alguns minutos. Não continue com os passos restantes até que o VM seja criado e o Azure CLI devolva a saída.

## <a name="test-network-communication"></a>Testar a comunicação de rede

Para testar a comunicação da rede com o Network Watcher, tem primeiro de ativar um observador de rede na região onde o VM que pretende testar e, em seguida, utilizar a próxima capacidade de lúpulo do Network Watcher para testar a comunicação.

### <a name="enable-network-watcher"></a>Ativar o observador de rede

Se já tiver um observador de rede ativado na região leste dos EUA, salte para use o [próximo salto](#use-next-hop). Use o comando [de configuração de rede az](/cli/azure/network/watcher#az-network-watcher-configure) para criar um observador de rede na região leste dos EUA:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Utilizar o próximo salto

O Azure cria automaticamente rotas para destinos predefinidos. Pode criar rotas personalizadas que substituem as rotas predefinidas. Por vezes, as rotas personalizadas podem causar falhas na comunicação. Para testar o encaminhamento a partir de um VM, utilize [o show-next-hop do observador de rede Az](/cli/azure/network/watcher#az-network-watcher-show-next-hop) para determinar o próximo salto de encaminhamento quando o tráfego está destinado a um endereço específico.

Teste a comunicação de saída a partir da VM para um dos endereços IP para www.bing.com:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 13.107.21.200 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Após alguns segundos, a saída informa-o de que o **próximo 00000 é** **a Internet,** e que o **routeTableId** é **a Rota do Sistema**. Este resultado permite-lhe saber que existe uma rota válida para o destino.

Teste a comunicação de saída da VM para 172.31.0.100:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 172.31.0.100 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

A saída devolvida informa-o de que **nenhum** é o **próximo 00000,** e que o **routeTableId** também é **a Rota do Sistema.** Este resultado permite-lhe saber que, embora exista uma rota de sistema válida para o destino, não há um próximo salto para encaminhar o tráfego para o destino.

## <a name="view-details-of-a-route"></a>Ver detalhes de uma rota

Para analisar o encaminhamento, reveja as rotas eficazes para a interface de rede com o comando [de tabela de séries de séries de rede az nic:](/cli/azure/network/nic#az-network-nic-show-effective-route-table)

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

O texto a seguir está incluído na saída devolvida:

```console
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
  },
  "addressPrefix": [
    "0.0.0.0/0"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "Internet",
  "source": "Default",
  "state": "Active"
},
```

Quando utilizou o `az network watcher show-next-hop` comando para testar a comunicação de saída para 13.107.21.200 em [Utilização do próximo salto,](#use-next-hop)a rota com o **endereçoPrefix** 0.0.0.0/0** foi utilizada para encaminhar o tráfego para o endereço, uma vez que nenhuma outra rota na saída inclui o endereço. Por predefinição, todos os endereços não especificados no prefixo de endereço de outra rota são encaminhados para a Internet.

Quando usou o `az network watcher show-next-hop` comando para testar a comunicação de saída para 172.31.0.100, no entanto, o resultado informou-o de que não havia próximo tipo de lúpulo. Na saída devolvida vê-se também o seguinte texto:

```console
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
      },
  "addressPrefix": [
    "172.16.0.0/12"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "None",
  "source": "Default",
  "state": "Active"
},
```

Como pode ver na saída a partir do `az network watcher nic show-effective-route-table` comando, embora exista uma rota padrão para o prefixo 172.16.0.0/12, que inclui o endereço 172.31.0.100, o **próximoHopType** é **Nenhum**. O Azure cria uma rota predefinida para 172.16.0.0/12, mas não especifica um tipo de próximo salto até que haja um motivo para isso. Se, por exemplo, adicionou a gama de endereços 172.16.0.0/12 ao espaço de endereço da rede virtual, o Azure altera o **próximo Dispositivo de Segurança** para a rede **Virtual** para a rota. Uma verificação mostraria então a **rede Virtual** como o **próximo Ópsia.**

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, pode utilizar [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos e todos os recursos que contém:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou um VM e diagnosticou o encaminhamento de rede a partir do VM. Aprendeu que o Azure cria várias rotas predefinidas e testa o encaminhamento para dois destinos diferentes. Saiba mais sobre o [encaminhamento no Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e como [criar rotas personalizadas](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Para ligações VM de saída, também pode determinar a latência e o tráfego de rede permitido e negado entre o VM e um ponto final utilizando a capacidade de resolução de problemas de ligação do Observador de [Rede.](network-watcher-connectivity-cli.md) Pode monitorizar a comunicação entre um VM e um ponto final, como um endereço IP ou URL, utilizando ao longo do tempo a capacidade do monitor de ligação do Observador de Rede. Para saber como, consulte [monitorar uma ligação de rede](connection-monitor.md).
