---
title: Débito de rede de máquina virtual do Azure | Documentos da Microsoft
description: Saiba mais sobre o débito de rede de máquina virtual do Azure.
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
ms.author: kumud,steveesp, mareat
ms.openlocfilehash: 9d74e53c754367ecfa63642514db93354fcadf25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65153737"
---
# <a name="virtual-machine-network-bandwidth"></a>Largura de banda de rede de máquina virtual

O Azure oferece uma variedade de tamanhos de VM e tipos, cada um com uma mistura diferentes capacidades de desempenho. Um recurso é a rede débito (ou largura de banda), medida em megabits por segundo (Mbps). Uma vez que as máquinas virtuais estão alojadas no hardware partilhado, a capacidade de rede tem de ser partilhada bastante entre as máquinas de virtuais partilhar o mesmo hardware. Máquinas de virtuais maiores são alocadas relativamente mais largura de banda que mais pequeno de máquinas virtuais.
 
A largura de banda atribuída a cada máquina virtual tem tráfego limitada sobre o tráfego de saída (saída) da máquina virtual. Todo o tráfego de rede deixar a máquina virtual é contado para o limite alocado, independentemente do destino. Por exemplo, se uma máquina virtual tiver uma 1000 Mbps, esse limite aplica-se quer o tráfego de saída é destinado a outra máquina virtual na mesma rede virtual ou fora do Azure.
 
Entrada não é limitada ou limitada diretamente. No entanto, há outros fatores, como limites de CPU e armazenamento, que podem afetar a capacidade de uma máquina virtual para processar dados de entrada.

Funcionamento em rede acelerado é um recurso criado para melhorar o desempenho da rede, incluindo latência, débito e de utilização da CPU. Embora o funcionamento em rede acelerado pode melhorar o débito de uma máquina virtual, pode fazer isso apenas até a máquina virtual está alocada da largura de banda. Para saber mais sobre redes aceleradas, consulte redes aceleradas para [Windows](create-vm-accelerated-networking-powershell.md) ou [Linux](create-vm-accelerated-networking-cli.md) máquinas virtuais.
 
Máquinas virtuais do Azure tem de ter um, mas Maio possui vários, anexados de interfaces de rede. Largura de banda atribuída a uma máquina virtual é a soma de todo o tráfego de saída em todas as interfaces de rede ligadas a uma máquina virtual. Em outras palavras, a largura de banda alocada é por máquina virtual, independentemente de quantas interfaces de rede estão ligados à máquina virtual. Para obter suporte de tamanhos de VM do Azure diferente de interfaces de rede quantos, veja o Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) tamanhos de VM. 

## <a name="expected-network-throughput"></a>Débito de rede esperado

Débito de saída esperado e o número de interfaces de rede suportadas por cada tamanho de VM é detalhada no Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) tamanhos de VM. Selecione um tipo, como para fins gerais, em seguida, selecione uma série de tamanho sobre a página resultante, como a série Dv2. Cada série tem uma tabela com as especificações na última coluna com o título de rede **NICs. Máx. / desempenho de rede (Mbps) esperado**. 

O limite de débito aplicam-se à máquina virtual. Débito não é afetado pelos seguintes fatores:
- **Número de interfaces de rede**: O limite de largura de banda é cumulativo de todo o tráfego de saída da máquina virtual.
- **Funcionamento de rede acelerado**: Embora a funcionalidade pode ser útil atingir o limite de publicados, não altera o limite.
- **Destino do tráfego**: Todos os destinos contam para o limite de saída.
- **Protocolo**: Todo o tráfego de saída ao longo de todos os protocolos de conta para o limite.

## <a name="network-flow-limits"></a>Limites de fluxo de rede

Além de largura de banda, o número de ligações de rede presentes numa VM num determinado momento pode afetar o desempenho de rede. A pilha de rede do Azure mantém o estado para cada direção de uma ligação de TCP/UDP em estruturas de dados chamado 'fluxos'. Uma ligação de TCP/UDP típica terá 2 fluxos criados, um para a entrada e outro para a direção de saída. 

Transferência de dados entre pontos de extremidade requer a criação de vários fluxos para além dos que realizar a transferência de dados. Alguns exemplos são fluxos criados para a resolução DNS e fluxos criados para as sondas de estado de funcionamento do Balanceador de carga. Também tenha em atenção que as aplicações virtuais (NVAs), como gateways, proxies, firewalls, de rede irá ver fluxos a ser criados para ligações terminada a aplicação e teve origem pela aplicação. 

![Contagem de fluxo para conversação de TCP através de uma aplicação de reencaminhamento](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>Limites de fluxo e recomendações

Hoje em dia, a pilha de rede do Azure suporta fluxos de rede total de 250 mil com bom desempenho para as VMs com mais de 8 núcleos de CPU e 100 mil total de fluxos com bom desempenho para as VMs com menos de 8 núcleos de CPU. Anteriores esta rede limite desempenho executa uma redução suave para fluxos adicionais até um limite restritivo de 1 milhão total de fluxos, 500 K entrada e de 500 K saída, após o qual fluxos adicionais são ignorados.

||As VMs com < 8 núcleos de CPU|VMs com 8 + núcleos de CPU|
|---|---|---|
|<b>Bom desempenho</b>|Fluxos de 100 mil |Fluxos de 250 mil|
|<b>Degradação do desempenho</b>|Acima de 100 mil fluxos|Superior a 250 mil fluxos|
|<b>Limite de fluxo</b>|Fluxos de 1 milhão|Fluxos de 1 milhão|

As métricas estão disponíveis na [do Azure Monitor](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) para controlar o número de fluxos de rede e a taxa de criação de fluxo em suas instâncias VM ou VMSS.

![azure-monitor-flow-metrics.png](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

As taxas de estabelecimento e a terminação de ligação também podem afetar o desempenho de rede como ligação estabelecimento e a terminação de partilhas de CPU com rotinas de processamento de pacotes. Recomendamos que benchmark cargas de trabalho em relação a cargas de trabalho de escalamento horizontal e padrões de tráfego esperado adequadamente para satisfazer as suas necessidades de desempenho. 

## <a name="next-steps"></a>Passos Seguintes

- [Otimizar o débito de rede para o sistema operativo de uma máquina virtual](virtual-network-optimize-network-bandwidth.md)
- [Débito de rede de teste](virtual-network-bandwidth-testing.md) para uma máquina virtual.
