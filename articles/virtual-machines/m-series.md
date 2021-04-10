---
title: Série M - Azure Virtual Machines
description: Especificações para os VMs da série M.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: jushiman
ms.openlocfilehash: 91c29864367380a303a006d4aac823a22a966a2b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563097"
---
# <a name="m-series"></a>Série M

A série M oferece uma alta contagem de VCPU (até 128 vCPUs) e uma grande quantidade de memória (até 3,8 TiB). Também é ideal para bases de dados extremamente grandes ou outras aplicações que beneficiam de altas contagens de vCPU e grandes quantidades de memória. Os tamanhos da série M são suportados tanto no Intel &reg; Xeon &reg; CPU E7-8890 v3 @ 2.50GHz como no Intel &reg; Xeon &reg; Platinum 8280M (Cascade Lake).

Recurso da série M VM, Intel &reg; Hyper-Threading Technology.

[ACU](acu.md): 160-180<br>
[Armazenamento Premium](premium-storage-performance.md): Suportado<br>
[Caching premium armazenamento](premium-storage-performance.md): Suportado<br>
[Migração ao Vivo](maintenance-and-updates.md): Não Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Não suportado<br>
[Suporte de geração VM](generation-2.md): Geração 1 e 2<br>
[Write Accelerator](./how-to-enable-write-accelerator.md): Suportado<br>
[Rede Acelerada](../virtual-network/create-vm-accelerated-networking-cli.md): Suportado<br>
[Discos EFÉMEROS](ephemeral-os-disks.md): Suportados <br>
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Produção de armazenamento em cache máximo e temporário: IOPS/MBps (tamanho da cache em GiB) | Produção de disco não-abacatado por maxilar: IOPS/MBps | NICs máximos|Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_M8ms                    | 8   | 218.75 | 256   | 8  | 10000/100 (793)     | 5000/125   | 4|2000  |
| Standard_M16ms                   | 16  | 437.5  | 512   | 16 | 20000/200 (1587)    | 10000/250  | 8|4000  |
| Standard_M32ts                   | 32  | 192    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8|8000  |
| Standard_M32ls                   | 32  | 256    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8|8000  |
| Standard_M32ms                   | 32  | 875    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8|8000  |
| Standard_M64s <sup>1</sup>       | 64  | 1024   | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8|16000 |
| Standard_M64ls <sup>1</sup>      | 64  | 512    | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8|16000 |
| Standard_M64ms <sup>1</sup>      | 64  | 1792   | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8|16000 |
| Standard_M128s <sup>1</sup>    | 128 | 2048   | 4096  | 64 | 160000/1600 (12696) | 80000/2000 | 8|30000 |
| Standard_M128ms <sup>1,2</sup>   | 128 | 3892   | 4096  | 64 | 160000/1600 (12696) | 80000/2000 | 8|30000 |
| Standard_M64 <sup>1</sup>        | 64  | 1024   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8|16000 |
| Standard_M64m <sup>1</sup>       | 64  | 1792   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8|16000 |
| Standard_M128 <sup>1</sup>     | 128 | 2048   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8|32000 |
| Standard_M128m <sup>1</sup>    | 128 | 3892   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8|32000 |

<sup>1</sup> Mais de 64 vCPU's requerem uma destas versões de hóspedes suportadas: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 e Red Hat Enterprise Linux, CentOS 7.3 ou Oracle Linux 7.3 com LIS 4.2.1.

<sup>2</sup> A instância é isolada para hardware dedicado a um único cliente.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

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
