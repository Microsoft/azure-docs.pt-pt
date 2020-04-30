---
title: DC-series - Máquinas Virtuais Azure
description: Especificações para os VMs da série DC.
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: d35e37e53b84d317446a93a2301fc3b703b426b7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82085727"
---
# <a name="dcsv2-series"></a>Série DCsv2


A série DCsv2 pode ajudar a proteger a confidencialidade e integridade dos seus dados e código enquanto é processado na nuvem pública. Estas máquinas são apoiadas pela mais recente geração do processador Intel XEON E-2288G com tecnologia SGX. Com a Tecnologia Intel Turbo Boost estas máquinas podem ir até 5.0GHz. As instâncias da série DCsv2 permitem que os clientes construam aplicações seguras baseadas no enclave para proteger o seu código e dados enquanto estão em uso.

Os casos de utilização por exemplo incluem: partilha de dados multipartidários confidenciais, deteção de fraudes, anti-branqueamento de capitais, blockchain, análise de uso confidencial, análise de inteligência e aprendizagem automática confidencial.

Armazenamento Premium: Suportado*

Caching de armazenamento premium: Suportado*

Migração Ao Vivo: Não Suportado

Atualizações de preservação da memória: não suportadas

*Exceto por Standard_DC8_v2



| Tamanho             | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo do armazenamento temporário e em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | Max NICs / Largura de banda de rede esperada (MBps) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16 (21)                                                            | 1600/24                                   | 2                                            |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                            | 3200/48                                   | 2                                            |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                            | 6400/96                                   | 2                                            |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128 (172)                                                         | 12800/192                                 | 2                                            |

- Os VMs da série DCsv2 são [VMs de geração 2](./linux/generation-2.md#creating-a-generation-2-vm) e apenas suportam `Gen2` imagens.
- Atualmente disponível apenas no Reino Unido Sul, Canadá Central e EUA East.
- Geração anterior de VMs de Computação Confidencial: [série seleção de DC](sizes-previous-gen.md#preview-dc-series)
- Criar VMs DCsv2 utilizando o [portal Azure](./linux/quick-create-portal.md) ou [o Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)



## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como as unidades de [computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho da computação em Azure SKUs.
