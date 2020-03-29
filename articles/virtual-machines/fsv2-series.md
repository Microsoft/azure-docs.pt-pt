---
title: Série Fsv2 - Máquinas Virtuais Azure
description: Especificações para os VMs da série Fsv2.
services: virtual-machines
author: brbell
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: f28c6b61aee3c8cbc078db1c2cfb48ed1fba4554
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164853"
---
# <a name="fsv2-series"></a>Série Fsv2

A série Fsv2 baseia-se no processador Intel® Xeon® Platinum 8168. Possui uma velocidade de relógio Turbo de 3,4 GHz e uma frequência turbo de núcleo único máxima de 3,7 GHz. As instruções ® INTEL AVX-512 são novas em processadores Intel Scalable. Estas instruções fornecem até um aumento de desempenho de 2X para o processamento de volumes de trabalho de processamento de vetores em operações de ponto flutuante de precisão única e dupla. Por outras palavras, são muito rápidos para qualquer carga de trabalho computacional.

Os VMs da série Fsv2 apresentam Intel® Tecnologia Hyper-Threading.

ACU: 195 - 210

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

Migração Ao Vivo: Apoiado

Atualizações de preservação da memória: Suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Entrada de armazenamento em cache e temperatura máxima: IOPS/MBps (tamanho cache em GiB) | Potência de disco max uncached: IOPS/MBps | Largura de banda de rede Max NICs/Esperado (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_F2s_v2  | 2  | 4   | 16  | 4  | 4000/31 (32)       | 3200/47    | 2/875   |
| Standard_F4s_v2  | 4  | 8   | 32  | 8  | 8000/63 (64)       | 6400/95    | 2/1750  |
| Standard_F8s_v2  | 8  | 16  | 64  | 16 | 16000/127 (128)    | 12800/190  | 4/3500  |
| Standard_F16s_v2 | 16 | 32  | 128 | 32 | 32000/255 (256)    | 25600/380  | 4/7000  |
| Standard_F32s_v2 | 32 | 64  | 256 | 32 | 64000/512 (512)    | 51200/750  | 8/14000 |
| Standard_F48s_v2 | 48 | 96  | 384 | 32 | 96000/768 (768)    | 76800/1100 | 8/21000 |
| Standard_F64s_v2 | 64 | 128 | 512 | 32 | 128000/1024 (1024) | 80000/1100 | 8/28000 |
| Standard_F72s_v2<sup>1, 2</sup> | 72 | 144 | 576 | 32 | 144000/1152 (1520) | 80000/1100 | 8/30000 |

<sup>1</sup> A utilização de mais de 64 vCPU requer um destes sistemas operativos de hóspedes suportados:

- Windows Server 2016 ou mais tarde
- Ubuntu 16.04 LTS ou posteriormente, com kernel afinado azure (4.15 kernel ou posterior)
- SLES 12 SP2 ou mais tarde
- RHEL ou CentOS versão 6.7 a 6.10, com o pacote LIS fornecido pela Microsoft 4.3.1 (ou mais tarde) instalado
- RHEL ou CentOS versão 7.3, com pacote LIS fornecido pela Microsoft 4.2.1 (ou posterior) instalado
- RHEL ou CentOS versão 7.6 ou posterior
- Oracle Linux com UEK4 ou mais tarde
- Debian 9 com o núcleo de backports, Debian 10 ou mais tarde
- CoreOS com um núcleo 4.14 ou mais tarde

<sup>2</sup> A Instância está isolada em hardware dedicado a um único cliente.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como as unidades de [computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho da computação em Azure SKUs.
