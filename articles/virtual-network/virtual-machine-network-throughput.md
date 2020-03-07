---
title: A entrada da rede de máquinas virtuais Azure  Microsoft Docs
description: Saiba mais sobre a entrada da rede de máquinas virtuais Azure.
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
ms.openlocfilehash: 47f58b25b082784177910d14ab95d8d242fda71a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357303"
---
# <a name="virtual-machine-network-bandwidth"></a>Largura de banda de rede de máquinas virtuais

O Azure oferece uma variedade de tamanhos e tipos vm, cada um com uma mistura diferente de capacidades de desempenho. Uma das capacidades é a entrada de rede (ou largura de banda), medida em megabits por segundo (Mbps). Como as máquinas virtuais são hospedadas em hardware partilhado, a capacidade da rede deve ser partilhada de forma justa entre as máquinas virtuais que partilham o mesmo hardware. Máquinas virtuais maiores são atribuídas relativamente mais largura de banda do que máquinas virtuais mais pequenas.
 
A largura de banda da rede atribuída a cada máquina virtual é memedíada no tráfego de saída da máquina virtual. Todo o tráfego de rede que sai da máquina virtual é contado para o limite atribuído, independentemente do destino. Por exemplo, se uma máquina virtual tem um limite de 1.000 Mbps, esse limite aplica-se se o tráfego de saída está destinado a outra máquina virtual na mesma rede virtual, ou fora de Azure.
 
A entrada não é medido ou limitada diretamente. No entanto, existem outros fatores, como cpu e limites de armazenamento, que podem afetar a capacidade de uma máquina virtual de processar dados de entrada.

O networking acelerado é uma funcionalidade concebida para melhorar o desempenho da rede, incluindo latência, entrada e utilização de CPU. Embora a rede acelerada possa melhorar a entrada de uma máquina virtual, só pode fazê-lo até à largura de banda atribuída da máquina virtual. Para saber mais sobre a rede Acelerada, consulte a rede Acelerada para [máquinas](create-vm-accelerated-networking-powershell.md) virtuais Windows ou [Linux.](create-vm-accelerated-networking-cli.md)
 
As máquinas virtuais Azure devem ter uma, mas podem ter várias interfaces de rede ligadas a elas. Largura de banda atribuída a uma máquina virtual é a soma de todo o tráfego de saída em todas as interfaces de rede ligadas a uma máquina virtual. Por outras palavras, a largura de banda atribuída é por máquina virtual, independentemente de quantas interfaces de rede estão ligadas à máquina virtual. Para saber quantas interfaces de rede diferentes suportes [](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de tamanhos Azure VM, consulte os tamanhos De VM Do Windows E [Linux.](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 

## <a name="expected-network-throughput"></a>A entrada esperada da rede

A saída esperada e o número de interfaces de rede suportadas por cada tamanho VM é detalhado nos tamanhos de VM Do [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [do Linux.](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Selecione um tipo, como o propósito geral, e, em seguida, selecione uma série de tamanhos na página resultante, como a série Dv2. Cada série tem uma tabela com especificações de rede na última coluna intitulada Max **NICs / Desempenho de rede esperado (Mbps)** . 

O limite de entrada aplica-se à máquina virtual. A produção não é afetada pelos seguintes fatores:
- **Número de interfaces de rede**: O limite de largura de banda é cumulativo de todo o tráfego de saída da máquina virtual.
- **Networking acelerado**: Embora a funcionalidade possa ser útil para atingir o limite publicado, não altera o limite.
- **Destino**de tráfego : Todos os destinos contam para o limite de saída.
- **Protocolo**: Todo o tráfego de saída sobre todos os protocolos conta para o limite.

## <a name="network-flow-limits"></a>Limites de fluxo de rede

Além da largura de banda, o número de ligações de rede presentes num VM a qualquer momento pode afetar o seu desempenho de rede. A pilha de rede Azure mantém o estado para cada direção de uma ligação TCP/UDP em estruturas de dados chamadas "fluxos". Uma ligação TCP/UDP típica terá 2 fluxos criados, um para a entrada e outro para a direção de saída. 

A transferência de dados entre pontos finais requer a criação de vários fluxos, para além dos que realizam a transferência de dados. Alguns exemplos são fluxos criados para resolução dNS e fluxos criados para sondas de saúde de equilibradores de carga. Note também que os aparelhos virtuais de rede (NVAs) tais como gateways, proxies, firewalls, verão fluxos sendo criados para ligações terminadas no aparelho e originados pelo aparelho. 

![Contagem de fluxos para conversação de TCP através de um aparelho de encaminhamento](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>Limites e recomendações de fluxo

Hoje em dia, a pilha de rede Azure suporta fluxos totais de rede de 250K com bom desempenho para VMs com mais de 8 núcleos CPU e fluxos totais de 100k com bom desempenho para VMs com menos de 8 núcleos CPU. Passado este limite, o desempenho da rede degrada-se graciosamente para fluxos adicionais até um limite rígido de fluxos totais de 500K, 250K de entrada e saída de 250K, após o qual fluxos adicionais são reduzidos.

||VMs com <8 CPU Cores|VMs com 8+ CPU Cores|
|---|---|---|
|<b>Bom Desempenho</b>|Fluxos 100K |Fluxos de 250K|
|<b>Desempenho degradado</b>|Acima de 100k Fluxos|Fluxos acima de 250k|
|<b>Limite de fluxo</b>|Fluxos de 500K|Fluxos de 500K|

As métricas estão disponíveis no [Monitor Azure](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) para monitorizar o número de fluxos de rede e o caudal de criação nas instâncias vM ou VMSS.

![azure-monitor-flow-metrics.png](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

As taxas de estabelecimento de ligação e de terminação também podem afetar o desempenho da rede, uma vez que o estabelecimento de ligação e as ações de rescisão cpU com rotinas de processamento de pacotes. Recomendamos que você benchmark cargas de trabalho contra padrões de tráfego esperados e esforce as cargas de trabalho adequadamente para corresponder às suas necessidades de desempenho. 

## <a name="next-steps"></a>Passos seguintes

- [Otimizar o débito de rede para o sistema operativo de uma máquina virtual](virtual-network-optimize-network-bandwidth.md)
- [A entrada da rede](virtual-network-bandwidth-testing.md) de teste para uma máquina virtual.
