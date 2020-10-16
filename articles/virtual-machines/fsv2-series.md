---
title: Série Fsv2
description: Especificações para os VMs da série Fsv2.
author: brbell
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 039045b613e57ee396201ee93c3962c5970cf02b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91651926"
---
# <a name="fsv2-series"></a>Série Fsv2

A série Fsv2 funciona nos processadores Intel® Xeon® Platinum 8272CL (Cascade Lake) e nos processadores Intel® Xeon® Platinum 8168 (Skylake). Apresenta uma velocidade do relógio Turbo de 3,4 GHz e uma frequência máxima de turbo mono-núcleo de 3,7 GHz. As instruções ® AVX-512 da Intel são novas nos processadores Intel Scalable. Estas instruções fornecem até um aumento de desempenho de 2X para cargas de trabalho de processamento de vetores em operações de ponto flutuante de precisão única e dupla. Por outras palavras, são muito rápidos para qualquer carga de trabalho computacional.

Os VMs da série Fsv2 apresentam Tecnologia intel® Hyper-Threading.

[ACU](acu.md): 195 - 210<br>
[Armazenamento Premium](premium-storage-performance.md): Suportado<br>
[Caching premium armazenamento](premium-storage-performance.md): Suportado<br>
[Migração Ao Vivo](maintenance-and-updates.md): Suportado<br>
[Atualizações de preservação da memória](maintenance-and-updates.md): Suportado<br>
[Suporte de geração VM](generation-2.md): Geração 1 e 2<br>
<br>

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Produção de armazenamento em cache máximo e temporário: IOPS/MBps (tamanho da cache em GiB) | Produção de disco não-abacatado por maxilar: IOPS/MBps | NICs máximos|Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_F2s_v2  | 2  | 4   | 16  | 4  | 4000/31 (32)       | 3200/47    | 2|875   |
| Standard_F4s_v2  | 4  | 8   | 32  | 8  | 8000/63 (64)       | 6400/95    | 2|1750  |
| Standard_F8s_v2  | 8  | 16  | 64  | 16 | 16000/127 (128)    | 12800/190  | 4|3500  |
| Standard_F16s_v2 | 16 | 32  | 128 | 32 | 32000/255 (256)    | 25600/380  | 4|7000  |
| Standard_F32s_v2 | 32 | 64  | 256 | 32 | 64000/512 (512)    | 51200/750  | 8|14000 |
| Standard_F48s_v2 | 48 | 96  | 384 | 32 | 96000/768 (768)    | 76800/1100 | 8|21000 |
| Standard_F64s_v2 | 64 | 128 | 512 | 32 | 128000/1024 (1024) | 80000/1100 | 8|28000 |
| Standard_F72s_v2<sup>1, 2</sup> | 72 | 144 | 576 | 32 | 144000/1152 (1520) | 80000/1100 | 8|30000 |

<sup>1</sup> A utilização de mais de 64 vCPU requer um destes sistemas operativos de hóspedes suportados:

- Windows Server 2016 ou mais tarde
- Ubuntu 16.04 LTS ou mais tarde, com núcleo afinado Azure (4.15 núcleo ou posterior)
- SLES 12 SP2 ou mais tarde
- Versão RHEL ou CentOS 6.7 a 6.10, com pacote LIS fornecido pela Microsoft 4.3.1 (ou posteriormente) instalado
- Versão RHEL ou CentOS 7.3, com pacote LIS fornecido pela Microsoft 4.2.1 (ou posteriormente) instalado
- VERSÃO RHEL ou CentOS 7.6 ou posterior
- Oracle Linux com UEK4 ou mais tarde
- Debian 9 com o kernel backports, Debian 10 ou mais tarde
- CoreOS com um núcleo 4.14 ou mais tarde

<sup>2</sup> A instância é isolada para hardware dedicado a um único cliente.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Outros tamanhos e informações

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

Calculadora de preços: [Calculadora de Preços](https://azure.microsoft.com/pricing/calculator/)

Mais informações sobre tipos de discos : [tipos de discos](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.