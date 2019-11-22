---
title: Diagnosticar um problema de roteamento de rede VM-CLI do Azure
titleSuffix: Azure Network Watcher
description: Neste artigo, você aprenderá a diagnosticar um problema de roteamento de rede de máquina virtual usando o recurso de próximo salto do observador de rede do Azure.
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
ms.openlocfilehash: 23ffc16948c250a6999c33b8812769ba889f4900
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276096"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Diagnosticar um problema de roteamento de rede de máquina virtual-CLI do Azure

Neste artigo, você implanta uma VM (máquina virtual) e, em seguida, verifica as comunicações para um endereço IP e uma URL. Determine a causa de uma falha de comunicação e como pode resolvê-la.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que você esteja executando o CLI do Azure versão 2.0.28 ou posterior. Para localizar a versão instalada, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Depois de verificar a versão da CLI, execute `az login` para criar uma ligação com o Azure. Os comandos da CLI neste artigo são formatados para serem executados em um shell bash.

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

Para testar a comunicação de rede com o observador de rede, você deve primeiro habilitar um observador de rede na região em que a VM que você deseja testar está e, em seguida, usar o recurso de próximo salto do observador de rede para testar a comunicação.

### <a name="enable-network-watcher"></a>Ativar o observador de rede

Se você já tiver um observador de rede habilitado na região leste dos EUA, pule para [usar o próximo salto](#use-next-hop). Use o comando [AZ Network vigia configure](/cli/azure/network/watcher#az-network-watcher-configure) para criar um observador de rede na região leste dos EUA:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Utilizar o próximo salto

O Azure cria automaticamente rotas para destinos predefinidos. Pode criar rotas personalizadas que substituem as rotas predefinidas. Por vezes, as rotas personalizadas podem causar falhas na comunicação. Para testar o roteamento de uma VM, use [AZ Network Watcher mostrar-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop) para determinar o próximo salto de roteamento quando o tráfego for destinado a um endereço específico.

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

Depois de alguns segundos, a saída informa que o **nextHopType** é a **Internet**e que o **RouteTableId** é a rota do **sistema**. Esse resultado permite que você saiba que há uma rota válida para o destino.

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

A saída retornada informa que **nenhum** é o **nextHopType**, e que o **routeTableId** também é a rota do **sistema**. Este resultado permite-lhe saber que, embora exista uma rota de sistema válida para o destino, não há um próximo salto para encaminhar o tráfego para o destino.

## <a name="view-details-of-a-route"></a>Ver detalhes de uma rota

Para analisar ainda mais o roteamento, examine as rotas efetivas para a interface de rede com o comando [AZ Network NIC show-efetivo-Route-Table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) :

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

O texto a seguir está incluído na saída retornada:

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

Quando você usou o comando `az network watcher show-next-hop` para testar a comunicação de saída para 13.107.21.200 em [usar o próximo salto](#use-next-hop), a rota com o **addressPrefix** 0.0.0.0/0 * * foi usada para rotear o tráfego para o endereço, pois nenhuma outra rota na saída inclui o endereço. Por predefinição, todos os endereços não especificados no prefixo de endereço de outra rota são encaminhados para a Internet.

Quando você usou o comando `az network watcher show-next-hop` para testar a comunicação de saída para 172.31.0.100 no entanto, o resultado informava que não havia nenhum tipo do próximo salto. Na saída retornada, você também verá o seguinte texto:

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

Como você pode ver na saída do comando `az network watcher nic show-effective-route-table`, embora haja uma rota padrão para o prefixo 172.16.0.0/12, que inclui o endereço 172.31.0.100, **nextHopType** é **nenhum**. O Azure cria uma rota predefinida para 172.16.0.0/12, mas não especifica um tipo de próximo salto até que haja um motivo para isso. Se, por exemplo, você adicionou o intervalo de endereços 172.16.0.0/12 ao espaço de endereço da rede virtual, o Azure altera o **nextHopType** para a **rede virtual** para a rota. Uma verificação, em seguida, mostraria **rede virtual** como o **nextHopType**.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos e todos os recursos que contém:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você criou uma VM e um roteamento de rede diagnosticado da VM. Aprendeu que o Azure cria várias rotas predefinidas e testa o encaminhamento para dois destinos diferentes. Saiba mais sobre o [encaminhamento no Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e como [criar rotas personalizadas](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Para conexões de VM de saída, você também pode determinar a latência e o tráfego de rede permitido e negado entre a VM e um ponto de extremidade usando a funcionalidade de [solução de problemas de conexão](network-watcher-connectivity-cli.md) do observador de rede. Você pode monitorar a comunicação entre uma VM e um ponto de extremidade, como um endereço IP ou URL, ao longo do tempo usando o recurso de monitor de conexão do observador de rede. Para saber como, consulte [monitorar uma conexão de rede](connection-monitor.md).