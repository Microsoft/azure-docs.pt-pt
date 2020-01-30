---
title: Diagnosticar um problema de encaminhamento de rede VM - Azure CLI
titleSuffix: Azure Network Watcher
description: Neste artigo, aprende-se a diagnosticar um problema de encaminhamento de rede de máquinas virtuais utilizando a próxima capacidade de lúpulo do Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
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
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: bf4c5e364b7f18b363f9915f54e43c7ea54c33c4
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76834686"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Diagnosticar um problema de encaminhamento de rede de máquinas virtuais - Azure CLI

Neste artigo, implementa uma máquina virtual (VM) e, em seguida, verifica as comunicações para um endereço IP e URL. Vai determinar a causa de uma falha de comunicação e aprender a resolvê-la.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este artigo requer que esteja a executar a versão Azure CLI 2.0.28 ou posterior. Para localizar a versão instalada, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Depois de verificar a versão da CLI, execute `az login` para criar uma ligação com o Azure. Os comandos cli neste artigo são formatados para correr em uma concha bash.

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

Para testar a comunicação da rede com o Observador de Rede, primeiro deve ativar um observador de rede na região em que o VM que pretende testar e, em seguida, utilizar a próxima capacidade de lúpulo do Network Watcher para testar a comunicação.

### <a name="enable-network-watcher"></a>Ativar o observador de rede

Se já tem um observador de rede ativado na região leste dos EUA, salte para use o [próximo lúpulo](#use-next-hop). Utilize o comando configurado de um observador de [rede Az](/cli/azure/network/watcher#az-network-watcher-configure) para criar um observador de rede na região leste dos EUA:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Utilizar o próximo salto

O Azure cria automaticamente rotas para destinos predefinidos. Pode criar rotas personalizadas que substituem as rotas predefinidas. Por vezes, as rotas personalizadas podem causar falhas na comunicação. Para testar o encaminhamento de um VM, use o [show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop) do observador da rede Az para determinar o próximo salto de encaminhamento quando o tráfego está destinado a um endereço específico.

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

Após alguns segundos, a saída informa-o de que o **próximo HopType** **é**internet , e que o **routeTableId** é **a Rota**do Sistema . Este resultado permite-lhe saber que existe uma rota válida para o destino.

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

A saída devolvida informa-o de que **Nenhum** é o **próximo HopType,** e que o **routeTableId** também é **A Rota**do Sistema . Este resultado permite-lhe saber que, embora exista uma rota de sistema válida para o destino, não há um próximo salto para encaminhar o tráfego para o destino.

## <a name="view-details-of-a-route"></a>Ver detalhes de uma rota

Para analisar ainda mais o encaminhamento, reveja as rotas eficazes para a interface da rede com o comando de tabela de rota sin [iveis da rede az:](/cli/azure/network/nic#az-network-nic-show-effective-route-table)

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

O seguinte texto está incluído na saída devolvida:

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

Quando utilizou o comando `az network watcher show-next-hop` para testar a comunicação de saída para 13.107.21.200 em [Utilização seguinte,](#use-next-hop)a rota com o **endereçoPrefix** 0.0.0.0/0** foi utilizada para encaminhar o tráfego para o endereço, uma vez que nenhuma outra rota na saída inclui o endereço. Por predefinição, todos os endereços não especificados no prefixo de endereço de outra rota são encaminhados para a Internet.

No entanto, quando usou o comando `az network watcher show-next-hop` para testar a comunicação de saída para 172.31.0.100, o resultado informou-o de que não havia um próximo tipo de lúpulo. Na saída devolvida, consulte também o seguinte texto:

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

Como pode ver na saída do comando `az network watcher nic show-effective-route-table`, embora exista uma rota padrão para o prefixo 172.16.0.0.0/12, que inclui o endereço 172.31.0.100, o **próximo HopType** é **Nenhum**. O Azure cria uma rota predefinida para 172.16.0.0/12, mas não especifica um tipo de próximo salto até que haja um motivo para isso. Se, por exemplo, adicionou a gama de endereços 172.16.0.0/12 ao espaço de endereço da rede virtual, o Azure muda o **próximo HopType** para **a rede Virtual** para a rota. Uma verificação mostraria então a **rede Virtual** como o **próximo HopType**.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos e todos os recursos que contém:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou um VM e diagnosticou o encaminhamento de rede a partir do VM. Aprendeu que o Azure cria várias rotas predefinidas e testa o encaminhamento para dois destinos diferentes. Saiba mais sobre o [encaminhamento no Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e como [criar rotas personalizadas](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Para ligações VM de saída, também pode determinar a latência e o tráfego de rede permitido e negado entre o VM e um ponto final utilizando a capacidade de resolução de problemas de [ligação](network-watcher-connectivity-cli.md) do Network Watcher. Pode monitorizar a comunicação entre um VM e um ponto final, como um endereço IP ou URL, ao longo do tempo utilizando a capacidade do monitor de ligação do Observador de Rede. Para saber como, consulte [monitor uma ligação de rede](connection-monitor.md).