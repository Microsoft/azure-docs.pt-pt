---
title: Exemplo de script da CLI do Azure - encaminhar o tráfego através de uma aplicação virtual de rede | Microsoft Docs
description: Exemplo de script da CLI do Azure - encaminhar o tráfego através de uma aplicação virtual de rede de firewall.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: timlt
ms.openlocfilehash: f2ba4d919cc17d1c454a39490377477e99aa17e2
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875518"
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>Rotear o tráfego por meio de uma solução de virtualização de rede

Este script de exemplo cria uma rede virtual com as sub-redes de front-end e back-end. Também cria uma VM com o reencaminhamento de IP ativado para encaminhar o tráfego entre as duas sub-redes. Depois de executar o script pode implementar o software de rede, como uma aplicação de firewall, na VM.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Script de exemplo


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma rede virtual e grupos de segurança de rede. Cada comando na tabela liga à documentação específica do comando.

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](/cli/azure/network/vnet) | Cria uma rede e sub-rede virtual de front-end do Azure. |
| [az network subnet create](/cli/azure/network/vnet/subnet) | Crias as sub-redes de back-end e DMZ. |
| [az network public-ip create](/cli/azure/network/public-ip) | Cria um endereço IP público para acessar a VM da Internet. |
| [az network nic create](/cli/azure/network/nic) | Cria uma interface de rede virtual e ativa o reencaminhamento de IP para a mesma. |
| [az network nsg create](/cli/azure/network/nsg) | Cria um grupo de segurança de rede (NSG). |
| [az network nsg rule create](/cli/azure/network/nsg/rule) | Cria regras de NSG que permitem as portas HTTP e HTTPS de entrada para a VM. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet)| Associa os NSGs e as tabelas de rotas às sub-redes. |
| [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create)| Cria uma tabela de rotas para todas as rotas. |
| [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create)| Cria rotas para rotear o tráfego entre sub-redes e a Internet por meio da VM. |
| [az vm create](/cli/azure/vm) | Cria uma máquina virtual e anexa o NIC à mesma. Este comando também especifica a imagem da máquina virtual a utilizar e as credenciais administrativas. |
| [az group delete](/cli/azure/group) | Elimina um grupo de recursos e todos os recursos contidos no mesmo. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Exemplos de script da CLI de rede adicional podem ser encontrados na [documentação de visão geral da rede do Azure](../cli-samples.md)
