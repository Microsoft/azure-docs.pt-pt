---
title: Tráfego de rota através de aparelho virtual de rede - amostra de script Azure CLI
description: Encaminhe o tráfego através de um aparelho virtual de rede de firewall - amostra de script Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 964fda8168867c115502c7262dc1d41e55075866
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91317655"
---
# <a name="route-traffic-through-a-network-virtual-appliance---azure-cli-script-sample"></a>Tráfego de rotas através de um aparelho virtual de rede - amostra de script Azure CLI

Este script de exemplo cria uma rede virtual com as sub-redes de front-end e back-end. Também cria uma VM com o reencaminhamento de IP ativado para encaminhar o tráfego entre as duas sub-redes. Depois de executar o script pode implementar o software de rede, como uma aplicação de firewall, na VM.

Pode executar o script a partir do [Azure Cloud Shell](https://shell.azure.com/bash) ou a partir de uma instalação local da CLI do Azure. Se utilizar a CLI localmente, este script requer que esteja a executar a versão 2.0.28 ou posterior. Para localizar a versão instalada, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver a executar localmente a CLI, também terá de executar o `az login` para criar uma ligação com o Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados:

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar um grupo de recursos, uma rede virtual e grupos de segurança de rede. Cada comando na tabela seguinte liga à documentação específica do comando:

| Comando | Notas |
|---|---|
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](/cli/azure/network/vnet) | Cria uma rede e sub-rede virtual de front-end do Azure. |
| [az network subnet create](/cli/azure/network/vnet/subnet) | Crias as sub-redes de back-end e DMZ. |
| [az network public-ip create](/cli/azure/network/public-ip) | Cria um endereço IP público para aceder à VM a partir da Internet. |
| [az network nic create](/cli/azure/network/nic) | Cria uma interface de rede virtual e ativa o reencaminhamento de IP para a mesma. |
| [az network nsg create](/cli/azure/network/nsg) | Cria um grupo de segurança de rede (NSG). |
| [az network nsg rule create](/cli/azure/network/nsg/rule) | Cria regras de NSG que permitem as portas HTTP e HTTPS de entrada para a VM. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet)| Associa os NSGs e as tabelas de rotas às sub-redes. |
| [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create)| Cria uma tabela de rotas para todas as rotas. |
| [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create)| Cria rotas para encaminhar o tráfego entre as sub-redes e a Internet através da VM. |
| [az vm create](/cli/azure/vm) | Cria uma máquina virtual e anexa o NIC à mesma. Este comando também especifica a imagem da máquina virtual a utilizar e as credenciais administrativas. |
| [az group delete](/cli/azure/group) | Elimina um grupo de recursos e todos os recursos contidos no mesmo. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos adicionais de scripts da CLI de rede virtual em [Exemplos da CLI de rede virtual](../cli-samples.md).
