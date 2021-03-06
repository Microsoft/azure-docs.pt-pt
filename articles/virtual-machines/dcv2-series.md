---
title: Série DC - Azure Virtual Machines
description: Especificações para os VMs da série DC.
author: susaxen
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: jushiman
ms.openlocfilehash: 677f4df0873f8b72d40dd373035111e2e0002491
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549235"
---
# <a name="dcsv2-series"></a>Série DCsv2


A série DCsv2 pode ajudar a proteger a confidencialidade e integridade dos seus dados e código enquanto é processado na nuvem pública. Estas máquinas são apoiadas pela última geração do Processador Intel XEON E-2288G com tecnologia SGX. Com a Tecnologia Intel Turbo Boost estas máquinas podem ir até 5.0GHz. As instâncias da série DCsv2 permitem que os clientes construam aplicações seguras baseadas em enclaves para proteger o seu código e dados enquanto estão a ser utilizados.

Os casos de uso de exemplo incluem: partilha de dados multipartidários confidenciais, deteção de fraudes, branqueamento de capitais, blockchain, análise de uso confidencial, análise de inteligência e aprendizagem de máquinas confidenciais.

[Armazenamento Premium](premium-storage-performance.md): Armazenamento Premium suportado *<br> 
 [:](premium-storage-performance.md)Migração ao vivo suportada : Atualizações de <br> 
 [](maintenance-and-updates.md)preservação da memória não suportadas <br> 
 [](maintenance-and-updates.md): Suporte de geração VM não suportado <br> 
 [](generation-2.md): Geração 2 <br> 
 [Rede Acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): Suportada (* Requer um mínimo de 4 vCPU*) <br>
[Discos EFÉMEROS](ephemeral-os-disks.md): Suportados <br>

*Exceto por Standard_DC8_v2 <br>

| Tamanho             | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo do armazenamento temporário e em cache: IOPS/MBps (tamanho da cache em GiB) | Max NICs / Largura de banda de rede esperada (MBps) | Memória EPC (MiB) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|----------------------------------------------|---------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16                                                                                               | 2   | 28                                      |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32                                                                                               | 2  | 56                                          |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64                                                                                               | 2  | 112                                          |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128                                                                                         | 2   | 168                                         |

- Os VMs da série DCsv2 são [de geração 2 VMs e apenas imagens](./generation-2.md#creating-a-generation-2-vm) de `Gen2` suporte.
- Atualmente disponível nas regiões listadas [aqui.](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)
- Geração anterior de VMs de Computação Confidencial: [séries DC](sizes-previous-gen.md#preview-dc-series)
- Criar DCsv2 VMs utilizando o [portal Azure](./linux/quick-create-portal.md) ou [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)



## <a name="other-sizes-and-information"></a>Outros tamanhos e informações

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Otimizada para GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

Calculadora de preços: [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/)

Mais informações sobre tipos de discos : [tipos de discos](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
