---
title: Tráfego de rede VM filter - amostra de script Azure CLI
description: Filtrar o tráfego de rede de máquinas virtuais de entrada e saída (VM) utilizando uma amostra de script Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 86d33ba411c4a0294454a8e1421c35b694feb65d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87492994"
---
# <a name="filter-inbound-and-outbound-vm-network-traffic-using-an-azure-cli-script-sample"></a>Filtrar o tráfego da rede VM de entrada e saída utilizando uma amostra de script Azure CLI

Este script de exemplo cria uma rede virtual com as sub-redes de front-end e back-end. O tráfego de rede de entrada para a sub-rede do front-end está limitado a HTTP, HTTPS e SSH, enquanto o tráfego de saída à Internet da sub-rede de back-end não é permitido. Depois de executar o script, terá uma máquina virtual com dois NICs. Cada NIC está ligado a outra sub-rede.

Pode executar o script a partir do [Azure Cloud Shell](https://shell.azure.com/bash) ou a partir de uma instalação local da CLI do Azure. Se utilizar a CLI localmente, este script requer que esteja a executar a versão 2.0.28 ou posterior. Para localizar a versão instalada, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver a executar localmente a CLI, também terá de executar o `az login` para criar uma ligação com o Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/filter-network-traffic/filter-network-traffic.sh  "Filter VM network traffic")]

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
| [az network subnet create](/cli/azure/network/vnet/subnet) | Cria uma sub-rede de back-end. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet) | Associa os NSGs a sub-redes. |
| [az network public-ip create](/cli/azure/network/public-ip) | Cria um endereço IP público para aceder à VM a partir da Internet. |
| [az network nic create](/cli/azure/network/nic) | Cria interfaces de rede virtual e anexa-as a sub-redes de front-end e back-end da rede virtual. |
| [az network nsg create](/cli/azure/network/nsg) | Cria grupos de segurança (NSG) que estão associados às sub-redes de front-end e back-end. |
| [az network nsg rule create](/cli/azure/network/nsg/rule) |Cria regras do NSG que permitem ou bloquear portas específicas para sub-redes específicas. |
| [az vm create](/cli/azure/vm) | Cria máquinas virtuais e anexa um NIC para cada VM. Este comando também especifica a imagem da máquina virtual a utilizar e as credenciais administrativas. |
| [az group delete](/cli/azure/group) | Elimina um grupo de recursos e todos os recursos contidos no mesmo. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a CLI do Azure, veja [Documentação da CLI do Azure](/cli/azure).

Pode ver exemplos adicionais de scripts da CLI de rede virtual em [Exemplos da CLI de rede virtual](../cli-samples.md).
