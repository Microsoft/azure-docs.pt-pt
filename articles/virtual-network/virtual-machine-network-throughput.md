---
title: Taxa de transferência de rede da máquina virtual do Azure | Microsoft Docs
description: Saiba mais sobre a taxa de transferência da rede de máquinas virtuais do Azure.
services: virtual-network
documentationcenter: na
author: steveesp
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/26/2019
ms.author: steveesp
ms.reviewer: kumud, mareat
ms.openlocfilehash: 68fe50c75fc25106a0f47af8bf6cfc0db562fbe5
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529116"
---
# <a name="virtual-machine-network-bandwidth"></a>Largura de banda da rede da máquina virtual

O Azure oferece uma variedade de tamanhos e tipos de VM, cada um com uma combinação diferente de recursos de desempenho. Uma funcionalidade é a taxa de transferência de rede (ou largura de banda), medida em megabits por segundo (Mbps). Como as máquinas virtuais são hospedadas em hardware compartilhado, a capacidade de rede deve ser compartilhada de forma bem entre as máquinas virtuais que compartilham o mesmo hardware. Máquinas virtuais maiores são alocadas relativamente mais largura de banda do que máquinas virtuais menores.
 
A largura de banda de rede alocada para cada máquina virtual é medida no tráfego de saída (saída) da máquina virtual. Todo o tráfego de rede que sai da máquina virtual é contado para o limite alocado, independentemente do destino. Por exemplo, se uma máquina virtual tiver um limite de 1.000 Mbps, esse limite se aplicará se o tráfego de saída for destinado a outra máquina virtual na mesma rede virtual ou fora do Azure.
 
A entrada não é limitada ou limitado diretamente. No entanto, há outros fatores, como limites de CPU e de armazenamento, que podem afetar a capacidade de uma máquina virtual de processar dados de entrada.

A rede acelerada é um recurso projetado para melhorar o desempenho da rede, incluindo latência, taxa de transferência e utilização da CPU. Embora a rede acelerada possa melhorar a taxa de transferência de uma máquina virtual, ela pode fazer isso apenas até a largura de banda alocada da máquina virtual. Para saber mais sobre a rede acelerada, consulte rede acelerada para máquinas virtuais [Windows](create-vm-accelerated-networking-powershell.md) ou [Linux](create-vm-accelerated-networking-cli.md) .
 
As máquinas virtuais do Azure devem ter uma, mas podem ter várias interfaces de rede anexadas a elas. A largura de banda alocada para uma máquina virtual é a soma de todo o tráfego de saída em todas as interfaces de rede anexadas a uma máquina virtual. Em outras palavras, a largura de banda alocada é por máquina virtual, independentemente de quantas interfaces de rede estão conectadas à máquina virtual. Para saber quantas interfaces de rede diferentes tamanhos de VM do Azure dão suporte, confira tamanhos de VM [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure. 

## <a name="expected-network-throughput"></a>Taxa de transferência de rede esperada

A taxa de transferência de saída esperada e o número de interfaces de rede com suporte de cada tamanho de VM são detalhados em tamanhos de VM [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure. Selecione um tipo, como finalidade geral e, em seguida, selecione uma série de tamanho na página resultante, como a série Dv2. Cada série tem uma tabela com especificações de rede na última coluna intitulada, **máximo de NICs/desempenho de rede esperado (Mbps)** . 

O limite de taxa de transferência se aplica à máquina virtual. A taxa de transferência não é afetada pelos seguintes fatores:
- **Número de interfaces de rede**: o limite de largura de banda é cumulativo de todo o tráfego de saída da máquina virtual.
- **Rede acelerada**: embora o recurso possa ser útil para atingir o limite publicado, ele não altera o limite.
- **Destino do tráfego**: todos os destinos contam para o limite de saída.
- **Protocolo**: todo o tráfego de saída em todos os protocolos conta em direção ao limite.

## <a name="network-flow-limits"></a>Limites de fluxo de rede

Além da largura de banda, o número de conexões de rede presentes em uma VM em um determinado momento pode afetar o desempenho da rede. A pilha de rede do Azure mantém o estado para cada direção de uma conexão TCP/UDP em estruturas de dados chamadas ' flows '. Uma conexão TCP/UDP típica terá 2 fluxos criados, um para a entrada e outro para a direção de saída. 

A transferência de dados entre pontos de extremidade requer a criação de vários fluxos além daqueles que executam a transferência de dados. Alguns exemplos são fluxos criados para resolução de DNS e fluxos criados para investigações de integridade do balanceador de carga. Observe também que as NVAs (soluções de virtualização de rede), como gateways, proxies, firewalls, verão os fluxos que estão sendo criados para conexões encerradas no dispositivo e originadas pelo dispositivo. 

![Contagem de fluxo para conversa TCP por meio de um dispositivo de encaminhamento](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>Limites de fluxo e recomendações

Hoje, a pilha de rede do Azure dá suporte a fluxos totais de rede 250 mil com bom desempenho para VMs com mais de 8 núcleos de CPU e 100 mil fluxos totais com bom desempenho para VMs com menos de 8 núcleos de CPU. Além desse limite, o desempenho da rede diminui normalmente para fluxos adicionais até um limite rígido de 1 milhão de fluxos, 500 mil de entrada e 500 mil de saída, após o qual os fluxos adicionais são descartados.

||VMs com núcleos de CPU < 8|VMs com mais de 8 núcleos de CPU|
|---|---|---|
|<b>Bom desempenho</b>|100 mil fluxos |Fluxos de 250 mil|
|<b>Desempenho degradado</b>|Acima de 100 mil fluxos|Fluxos de 250 mil acima|
|<b>Limite de fluxo</b>|Fluxos de 500 mil|Fluxos de 500 mil|

As métricas estão disponíveis em [Azure monitor](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) para controlar o número de fluxos de rede e a taxa de criação de fluxo em suas instâncias de VM ou VMSS.

![Azure-monitor-Flow-Metrics. png](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

As taxas de estabelecimento e término da conexão também podem afetar o desempenho da rede, pois o estabelecimento da conexão e o encerramento compartilham a CPU com rotinas de processamento de pacotes É recomendável que você compare as cargas de trabalho com os padrões de tráfego esperados e escale horizontalmente as cargas de trabalho de acordo com suas necessidades de desempenho. 

## <a name="next-steps"></a>Passos seguintes

- [Otimizar o débito de rede para o sistema operativo de uma máquina virtual](virtual-network-optimize-network-bandwidth.md)
- [Testar a taxa de transferência de rede](virtual-network-bandwidth-testing.md) para uma máquina virtual.
