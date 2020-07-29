---
title: Produção de rede de máquinas virtuais Azure Microsoft Docs
description: Saiba mais sobre a produção da rede de máquinas virtuais Azure, incluindo como a largura de banda é atribuída a uma máquina virtual.
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
ms.openlocfilehash: 4fed883c8fa92df77af432e1e4a4b3acca72a0d2
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87265119"
---
# <a name="virtual-machine-network-bandwidth"></a>Largura de banda de rede de máquinas virtuais

O Azure oferece uma variedade de tamanhos e tipos VM, cada um com uma mistura diferente de capacidades de desempenho. Uma capacidade é a produção de rede (ou largura de banda), medida em megabits por segundo (Mbps). Como as máquinas virtuais estão hospedadas em hardware partilhado, a capacidade de rede deve ser partilhada de forma justa entre as máquinas virtuais que partilham o mesmo hardware. Máquinas virtuais maiores são atribuídas relativamente mais largura de banda do que máquinas virtuais menores.
 
A largura de banda de rede atribuída a cada máquina virtual é medido no tráfego de saída da máquina virtual. Todo o tráfego de rede que sai da máquina virtual é contado para o limite atribuído, independentemente do destino. Por exemplo, se uma máquina virtual tiver um limite de 1.000 Mbps, esse limite aplica-se se o tráfego de saída está destinado a outra máquina virtual na mesma rede virtual, ou fora de Azure.
 
A entrada não é medido ou limitado diretamente. No entanto, existem outros fatores, como CPU e limites de armazenamento, que podem afetar a capacidade de uma máquina virtual de processar dados de entrada.

A rede acelerada é uma funcionalidade projetada para melhorar o desempenho da rede, incluindo a latência, a produção e a utilização do CPU. Embora a rede acelerada possa melhorar a produção de uma máquina virtual, só pode fazê-lo até à largura de banda atribuída pela máquina virtual. Para saber mais sobre rede acelerada, consulte rede acelerada para máquinas virtuais [Windows](create-vm-accelerated-networking-powershell.md) ou [Linux.](create-vm-accelerated-networking-cli.md)
 
As máquinas virtuais Azure devem ter uma, mas podem ter várias interfaces de rede ligadas a elas. A largura de banda atribuída a uma máquina virtual é a soma de todo o tráfego de saída em todas as interfaces de rede ligadas a uma máquina virtual. Por outras palavras, a largura de banda atribuída é por máquina virtual, independentemente de quantas interfaces de rede estão ligadas à máquina virtual. Para saber quantas interfaces de rede suportam diferentes tamanhos Azure VM, consulte os tamanhos Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM. 

## <a name="expected-network-throughput"></a>Produção esperada da rede

O rendimento de saída esperado e o número de interfaces de rede suportadas por cada tamanho VM é detalhado nos tamanhos Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM. Selecione um tipo, como propósito geral, e, em seguida, selecione uma série de tamanhos na página resultante, como a série Dv2. Cada série tem uma tabela com especificações de networking na última coluna intitulada, **Max NICs / Desempenho da rede esperada (Mbps)**. 

O limite de produção aplica-se à máquina virtual. A produção não é afetada pelos seguintes fatores:
- **Número de interfaces de rede**: O limite de largura de banda é cumulativo de todo o tráfego de saída da máquina virtual.
- **Rede acelerada**: Embora a funcionalidade possa ser útil para atingir o limite publicado, não altera o limite.
- **Destino de tráfego**: Todos os destinos contam para o limite de saída.
- **Protocolo**: Todo o tráfego de saída em todos os protocolos conta para o limite.

## <a name="network-flow-limits"></a>Limites de fluxo de rede

Além da largura de banda, o número de ligações de rede presentes num VM a qualquer momento pode afetar o seu desempenho na rede. A pilha de rede Azure mantém o estado para cada direção de uma ligação TCP/UDP em estruturas de dados chamadas "fluxos". Uma ligação típica TCP/UDP terá 2 fluxos criados, um para a entrada e outro para a direção de saída. 

A transferência de dados entre pontos finais requer a criação de vários fluxos para além dos que realizam a transferência de dados. Alguns exemplos são fluxos criados para a resolução de DNS e fluxos criados para sondas de saúde do balanceador de carga. Note também que os aparelhos virtuais de rede (NVAs) tais como gateways, proxies, firewalls, verão os fluxos serem criados para ligações terminadas no aparelho e originados pelo aparelho. 

![Contagem de fluxo para a conversa TCP através de um aparelho de encaminhamento](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>Limites de fluxo e recomendações

Hoje, a stack de rede Azure suporta fluxos totais de rede de 250K com bom desempenho para VMs com mais de 8 núcleos de CPU e fluxos totais de 100k com bom desempenho para VMs com menos de 8 núcleos de CPU. Para além deste limite, o desempenho da rede degrada-se graciosamente para fluxos adicionais até um limite rígido de fluxos totais de 500K, entrada de 250K e saída de 250K, após o qual os fluxos adicionais são reduzidos.

| Nível de desempenho | VMs com <8 Núcleos CPU | VMs com 8+ NÚCLEOs CPU |
| ----------------- | --------------------- | --------------------- |
|<b>Bom desempenho</b>|Fluxos de 100K |Fluxos de 250K|
|<b>Desempenho Degradado</b>|Acima de 100k Fluxos|Acima de 250K Fluxos|
|<b>Limite de fluxo</b>|Fluxos de 500K|Fluxos de 500K|

As métricas estão disponíveis no [Azure Monitor](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) para monitorizar o número de fluxos de rede e o caudal de criação nas suas instâncias VM ou VMSS.

![azure-monitor-flow-metrics.png](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

As taxas de estabelecimento de ligação e de terminação também podem afetar o desempenho da rede como estabelecimento de ligação e a rescisão de ações CPU com rotinas de processamento de pacotes. Recomendamos que compareça cargas de trabalho em comparação com os padrões de tráfego esperados e reduza as cargas de trabalho adequadamente para corresponder às suas necessidades de desempenho. 

## <a name="next-steps"></a>Passos seguintes

- [Otimizar o débito de rede para o sistema operativo de uma máquina virtual](virtual-network-optimize-network-bandwidth.md)
- [Produção de rede de teste](virtual-network-bandwidth-testing.md) para uma máquina virtual.
