---
title: Diagnosticar um máquina virtual rede problema de encaminhamento - CLI do Azure | Documentos da Microsoft
description: Neste artigo, saiba como diagnosticar um problema encaminhamento de rede de máquina virtual com a capacidade de salto seguinte do observador de rede do Azure.
services: network-watcher
documentationcenter: network-watcher
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 968b7dd703ba40f46a068deb1d8b7d2b32e0de2b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64688207"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Diagnosticar um máquina virtual rede problema de encaminhamento - CLI do Azure

Neste artigo, implementar uma máquina virtual (VM) e, em seguida, verifique as comunicações para um endereço IP e o URL. Vai determinar a causa de uma falha de comunicação e aprender a resolvê-la.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a que está a executar a CLI do Azure versão 2.0.28 ou posterior. Para localizar a versão instalada, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Depois de verificar a versão da CLI, execute `az login` para criar uma ligação com o Azure. Os comandos da CLI neste artigo são formatados para ser executado num shell de Bash.

## <a name="create-a-vm"></a>Criar uma VM

Antes de criar uma VM, tem de criar um grupo de recursos para conter a VM. Crie um grupo de recursos com [az group create](/cli/azure/group#az-group-create). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Crie uma VM com [az vm create](/cli/azure/vm#az-vm-create). Se as chaves SSH ainda não existirem numa localização de chaves predefinida, o comando cria-as. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`. O exemplo seguinte cria uma VM com o nome *myVm*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

A criação da VM demora alguns minutos. Não continue com os restantes passos até que a VM seja criada e a CLI devolva um resultado.

## <a name="test-network-communication"></a>Testar a comunicação de rede

Para testar a comunicação de rede com o observador de rede, tem de ativar um observador de rede na região que a VM que pretende testar está no primeiro e, em seguida, utilize as capacidades de salto do observador de rede seguinte para testar a comunicação entre.

### <a name="enable-network-watcher"></a>Ativar o observador de rede

Se já tiver um observador de rede ativado na região E.U.A. leste, avance para o [próximo salto de utilização](#use-next-hop). Utilize o [configurar o observador de rede de az](/cli/azure/network/watcher#az-network-watcher-configure) comando para criar um observador de rede na região E.U. a leste:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Utilizar o próximo salto

O Azure cria automaticamente rotas para destinos predefinidos. Pode criar rotas personalizadas que substituem as rotas predefinidas. Por vezes, as rotas personalizadas podem causar falhas na comunicação. Para testar o encaminhamento de uma VM, utilize [show-next-hop do observador de rede de az](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop) para determinar o próximo salto de encaminhamento quando o tráfego é destinado para um endereço específico.

Teste a comunicação de saída da VM para um dos endereços IP para www.bing.com:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 13.107.21.200 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Após alguns segundos, a saída informa que o **nextHopType** é **Internet**e que o **routeTableId** é **rota de sistema**. Este resultado permite-lhe saber que existe uma rota válida para o destino.

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

Informa-o resultado devolvido que **None** é o **nextHopType**e que o **routeTableId** também é **rota de sistema**. Este resultado permite-lhe saber que, embora exista uma rota de sistema válida para o destino, não há um próximo salto para encaminhar o tráfego para o destino.

## <a name="view-details-of-a-route"></a>Ver detalhes de uma rota

Para analisar mais de encaminhamento, reveja as rotas efetivas para a interface de rede com o [az network nic show-em vigor route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) comando:

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

O seguinte texto está incluído no resultado retornado:

```azurecli
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

Quando utilizou o `az network watcher show-next-hop` comando para testar a comunicação de saída para 13.107.21.200 no [próximo salto de utilização](#use-next-hop), a rota com o **addressPrefix** 0.0.0.0/0** foi utilizado para encaminhar o tráfego para o endereço, uma vez que nenhuma outra rota na saída inclui o endereço. Por predefinição, todos os endereços não especificados no prefixo de endereço de outra rota são encaminhados para a Internet.

Quando utilizou o `az network watcher show-next-hop` de comandos para testar a comunicação de saída para 172.31.0.100 no entanto, o resultado o informado de que não havia nenhum tipo de próximo salto. No resultado retornado verá também o seguinte texto:

```azurecli
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

Como pode ver no resultado do `az network watcher nic show-effective-route-table` comando, embora haja uma rota predefinida para o prefixo de 172.16.0.0/12, que inclui o 172.31.0.100 endereço, o **nextHopType** é **nenhum**. O Azure cria uma rota predefinida para 172.16.0.0/12, mas não especifica um tipo de próximo salto até que haja um motivo para isso. Se, por exemplo, tiver adicionado o intervalo de endereços 172.16.0.0/12 ao espaço de endereços da rede virtual, o Azure altera a **nextHopType** ao **rede Virtual** para a rota. Uma verificação, em seguida, mostraria **rede Virtual** como o **nextHopType**.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos e todos os recursos que contém:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, criou uma VM e diagnosticados encaminhamento de rede da VM. Aprendeu que o Azure cria várias rotas predefinidas e testa o encaminhamento para dois destinos diferentes. Saiba mais sobre o [encaminhamento no Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e como [criar rotas personalizadas](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Para ligações da VM de saída, também pode determinar a latência e autorizadas e negadas tráfego de rede entre a VM e um ponto final com o observador de rede [resolver problemas de ligação](network-watcher-connectivity-cli.md) capacidade. Pode monitorizar a comunicação entre uma VM e um ponto de extremidade, como um endereço IP ou o URL, ao longo do tempo com a capacidade de monitor de ligação do observador de rede. Para saber como, veja [monitorizar uma ligação de rede](connection-monitor.md).