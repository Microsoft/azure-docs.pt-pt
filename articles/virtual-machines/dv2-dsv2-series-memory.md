---
title: VMs da série Dv2 e DSv2 otimizados - Azure Virtual Machines
description: Especificações para os VMs da série Dv2 e DSv2.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: fb493154f81eb5ab63ed9593479356fe89552211
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84678261"
---
# <a name="memory-optimized-dv2-and-dsv2-series"></a>Memória otimizada Dv2 e Série Dsv2

Dv2 e série Dsv2, uma continuação da série D original, apresenta um CPU mais poderoso. Os tamanhos da série DSv2 são executados no Intel® Xeon® 8171M 2.1 GHz (Skylake) ou no Intel® Xeon® E5-02673 v4 2.3 GHz (Broadwell) ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell). A série Dv2 tem as mesmas configurações de memória e disco da série D.

## <a name="dv2-series-11-15"></a>Série Dv2 11-15

Os tamanhos da série Dv2 são executados no Intel® Xeon® 8171M 2.1 GHz (Skylake) ou no Intel® Xeon® E5-2 673 v4 2.3 GHz (Broadwell) ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell).

ACU: 210 - 250

Armazenamento Premium: Não Suportado

Caching de armazenamento premium: Não suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Produção de armazenamento temporário máximo: IOPS/Read MBps/Write MBps | Discos de dados/produção de dados máximos: IOPS | Largura de banda de rede Max NICs/Expect (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D11_v2 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2/1500  |
| Standard_D12_v2 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4/3000  |
| Standard_D13_v2 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8/6000  |
| Standard_D14_v2 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8/12000 |
| Standard_D15_v2 <sup>1</sup> | 20 | 140 | 1000 | 60000/937/468 | 64/64x500 | 8/25000 <sup>2</sup> |

<sup>1</sup> A instância é isolada para hardware dedicado a um único cliente.
<sup>2</sup> 25000 Mbps com Rede Acelerada.

## <a name="dsv2-series-11-15"></a>Série DSv2 11-15

Os tamanhos da série DSv2 são executados no Intel® Xeon® 8171M 2.1 GHz (Skylake) ou no Intel® Xeon® E5-02673 v4 2.3 GHz (Broadwell) ou os processadores Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell).

ACU: 210 - 250 <sup>1</sup>

Armazenamento Premium: Suportado

Caching de armazenamento premium: Suportado

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Produção de armazenamento em cache máximo e temporário: IOPS/MBps (tamanho da cache em GiB) | Produção de disco não-abacatado por maxilar: IOPS/MBps | Largura de banda de rede Max NICs/Expect (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2 <sup>3</sup> | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/96   | 2/1500  |
| Standard_DS12_v2 <sup>3</sup> | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/192 | 4/3000  |
| Standard_DS13_v2 <sup>3</sup> | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/384 | 8/6000  |
| Standard_DS14_v2 <sup>3</sup> | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/768 | 8/12000 |
| Standard_DS15_v2 <sup>2</sup> | 20 | 140 | 280 | 64 | 80000/640 (720) | 64000/960 | 8/25000 <sup>4</sup> |

<sup>1</sup> A produção máxima de disco (IOPS ou MBps) possível com um VM série DSv2 pode ser limitada pelo número, tamanho e desmontagem do ou dos discos anexos.  Para mais detalhes, consulte [Design para obter um desempenho elevado.](./windows/premium-storage-performance.md)
<sup>2</sup> Instance é isolado para o hardware baseado em Intel Haswell e dedicado a um único cliente.  
<sup>3</sup> tamanhos de núcleo limitados disponíveis.  
<sup>4</sup> 25000 Mbps com Rede Acelerada.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Outros tamanhos

- [Fins gerais](sizes-general.md)
- [Com otimização de memória](sizes-memory.md)
- [Com otimização de armazenamento](sizes-storage.md)
- [Com otimização de GPU](sizes-gpu.md)
- [Computação de elevado desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como [as unidades de computação Azure (ACU)](acu.md) podem ajudá-lo a comparar o desempenho do cálculo em Azure SKUs.
