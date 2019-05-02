---
title: Débito de rede de máquina virtual do Azure | Documentos da Microsoft
description: Saiba mais sobre o débito de rede de máquina virtual do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: kumud
ms.openlocfilehash: 182b3b7dad828e67d006391e00986406729c959d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689257"
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

## <a name="next-steps"></a>Passos Seguintes

- [Otimizar o débito de rede para o sistema operativo de uma máquina virtual](virtual-network-optimize-network-bandwidth.md)
- [Débito de rede de teste](virtual-network-bandwidth-testing.md) para uma máquina virtual.
