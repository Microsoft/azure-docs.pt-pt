---
title: Pontuações de referência de computação para VMs do Windows do Azure | Documentos da Microsoft
description: Compare SPECint pontuações de referência de computação para VMs do Azure com o Windows Server.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 69ae72ec-e8be-4e46-a8f0-e744aebb5cc2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/09/2018
ms.author: cynthn;davberg
ms.openlocfilehash: 43d0ab6552847df7f1f2a8599dcc7cb9a8fcb57b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844079"
---
# <a name="compute-benchmark-scores-for-windows-vms"></a>Pontuações de referência de computação para VMs do Windows
As pontuações de referência SPECInt seguintes mostram o desempenho de computação de alinhamento de VM de alto desempenho do Azure com o Windows Server. Pontuações de referência de computação também estão disponíveis para [VMs do Linux](../linux/compute-benchmark-scores.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!NOTE]
> Os números de Linux foi atualizados recentemente e contêm um conjunto mais abrangente de VMs.

## <a name="a-series---compute-intensive"></a>Série a – computação intensiva
| Tamanho | vCPUs | Nós NUMA | CPU | Execuções | Taxa média de base | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 |8 |1 |Intel Xeon CPU E5-2670 0 \@ 2,6 GHz |10 |236.1 |1.1 |
| Standard_A9 |16 |2 |Intel Xeon CPU E5-2670 0 \@ 2,6 GHz |10 |450.3 |7.0 |
| Standard_A10 |8 |1 |Intel Xeon CPU E5-2670 0 \@ 2,6 GHz |5 |235.6 |0.9 |
| Standard_A11 |16 |2 |Intel Xeon CPU E5-2670 0 \@ 2,6 GHz |7 |454.7 |4.8 |

## <a name="dv2-series"></a>Série Dv2
| Tamanho | vCPUs | Nós NUMA | CPU | Execuções | Taxa média de base | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |1 |Intel Xeon E5 2673 v3 \@ 2,4 GHz |83 |36.6 |2.6 |
| Standard_D2_v2 |2 |1 |Intel Xeon E5 2673 v3 \@ 2,4 GHz |27 |70.0 |3.7 |
| Standard_D3_v2 |4 |1 |Intel Xeon E5 2673 v3 \@ 2,4 GHz |19 |130.5 |4.4 |
| Standard_D4_v2 |8 |1 |Intel Xeon E5 2673 v3 \@ 2,4 GHz |19 |238.1 |5.2 |
| Standard_D5_v2 |16 |2 |Intel Xeon E5 2673 v3 \@ 2,4 GHz |14 |460.9 |15.4 |
| Standard_D11_v2 |2 |1 |Intel Xeon E5 2673 v3 \@ 2,4 GHz |19 |70.1 |3.7 |
| Standard_D12_v2 |4 |1 |Intel Xeon E5 2673 v3 \@ 2,4 GHz |2 |132.0 |1.4 |
| Standard_D13_v2 |8 |1 |Intel Xeon E5 2673 v3 \@ 2,4 GHz |17 |235.8 |3.8 |
| Standard_D14_v2 |16 |2 |Intel Xeon E5 2673 v3 \@ 2,4 GHz |15 |460.8 |6.5 |

## <a name="g-series-gs-series"></a>Série G, série GS
| Tamanho | vCPUs | Nós NUMA | CPU | Execuções | Taxa média de base | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_G1, Standard_GS1 |2 |1 |Intel Xeon E5-2698B v3 \@ 2 GHz |31 |71.8 |6.5 |
| Standard_G2, Standard_GS2 |4 |1 |Intel Xeon E5-2698B v3 \@ 2 GHz |5 |133.4 |13.0 |
| Standard_G3, Standard_GS3 |8 |1 |Intel Xeon E5-2698B v3 \@ 2 GHz |6 |242.3 |6.0 |
| Standard_G4, Standard_GS4 |16 |1 |Intel Xeon E5-2698B v3 \@ 2 GHz |15 |398.9 |6.0 |
| Standard_G5, Standard_GS5 |32 |2 |Intel Xeon E5-2698B v3 \@ 2 GHz |22 |762.8 |3.7 |

## <a name="h-series"></a>Série H
| Tamanho | vCPUs | Nós NUMA | CPU | Execuções | Taxa média de base  | StdDev |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |1 |Intel Xeon E5 2667 v3 \@ 3,2 GHz |5 |297.4 |0.9 |
| Standard_H16 |16 |2 |Intel Xeon E5 2667 v3 \@ 3,2 GHz |5 |575.8 |6.8 |
| Standard_H8m |8 |1 |Intel Xeon E5 2667 v3 \@ 3,2 GHz |5 |297.0 |1.2 |
| Standard_H16m |16 |2 |Intel Xeon E5 2667 v3 \@ 3,2 GHz |5 |572.2 |3.9 |
| Standard_H16r |16 |2 |Intel Xeon E5 2667 v3 \@ 3,2 GHz |5 |573.2 |2.9 |
| Standard_H16mr |16 |2 |Intel Xeon E5 2667 v3 \@ 3,2 GHz |7 |569.6 |2.8 |

## <a name="about-specint"></a>Sobre SPECint
Números de Windows foram computados através da execução [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html) no Windows Server. SPECint foi executado com a opção de taxa base (SPECint_rate2006), com uma cópia por vCPU. SPECint consiste em 12 testes separados, cada executada três vezes, colocar o valor mediano de cada teste e weighting-los para formar uma pontuação composta. Esses testes foram executados, em seguida, em várias VMs para fornecer as pontuações de média mostradas.

## <a name="next-steps"></a>Passos Seguintes
* Para as capacidades de armazenamento, os detalhes do disco e considerações adicionais para a escolha entre tamanhos de VM, consulte [tamanhos de máquinas virtuais](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

