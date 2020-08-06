---
title: Convenções de nomenclatura de tamanhos de VM do Azure
description: Explica as convenções de nomeação utilizadas para tamanhos Azure VM
ms.service: virtual-machines
subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.date: 7/22/2020
ms.author: mimckitt
ms.custom: sttsinar
ms.openlocfilehash: a49d1cc47515e698737bf833af09d30da16712a6
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87832330"
---
# <a name="azure-virtual-machine-sizes-naming-conventions"></a>Tamanhos de máquina virtual Azure nomeando convenções

Esta página descreve as convenções de nomeação utilizadas para os VMs Azure. Os VMs utilizam estas convenções de nomeação para denotar diferentes características e especificações.

## <a name="naming-convention-explanation"></a>Explicação da convenção de nomeação

**[Família]**  +  **[Sub-família*]**  +  **[# de vCPUs]**  +  **[Características aditivas]**  +  **[Tipo de acelerador*]**  +  **[Versão]**

|Valor | Explicação|
|---|---|
| Família | Indica a Série Familiar VM| 
| *Sub-família | Usado apenas para diferenciações de VM especializadas|
| # de vCPUs| Denota o número de VCPUs do VM |
| Características adírias | Uma ou mais letras minúsculas denotam características aditivas, tais como: <br> a = processador baseado em AMD <br> d = disco (disco temporário local está presente); isto é para novos VMs Azure, ver [Ddv4 e Série Ddsv4](./ddv4-ddsv4-series.md) <br> h = hibernação capaz <br> i = tamanho isolado <br> l = memória baixa; uma menor quantidade de memória do que o tamanho intensivo da memória <br> m = memória intensiva; a maior quantidade de memória em um tamanho particular <br> t = memória minúscula; a menor quantidade de memória em um tamanho particular <br> r = RDMA capaz <br> s = Armazenamento Premium capaz, incluindo a possível utilização de [Ultra SSD](./windows/disks-types.md#ultra-disk) (Nota: alguns tamanhos mais recentes sem o atributo de s ainda podem suportar Armazenamento Premium, por exemplo, M128, M64, etc.)<br> |
| *Tipo de acelerador | Denota o tipo de acelerador de hardware nos SKUs especializados/GPU. Apenas os novos SKUs especializados/GPU lançados a partir do 3º trimestre de 2020 terão o acelerador de hardware no nome. |
| Versão | Denota a versão da VM Family Series |

## <a name="example-breakdown"></a>Avaria de exemplo

**[Família]**  +  **[Sub-família*]**  +  **[# de vCPUs]**  +  **[Características aditivas]**  +  **[Tipo de acelerador*]**  +  **[Versão]**

### <a name="example-1-m416ms_v2"></a>Exemplo 1: M416ms_v2

|Valor | Explicação|
|---|---|
| Família | M | 
| # de vCPUs | 416 |
| Características adírias | m = memória intensiva <br> s = Armazenamento Premium capaz |
| Versão | v2 |

### <a name="example-2-nv16as_v4"></a>Exemplo 2: NV16as_v4

|Valor | Explicação|
|---|---|
| Família | N | 
| Sub-família | V |
| # de vCPUs | 16 |
| Características adírias | a = processador baseado em AMD <br> s = Armazenamento Premium capaz |
| Versão | v4 |

### <a name="example-3-nc4as_t4_v3"></a>Exemplo 3: NC4as_T4_v3

|Valor | Explicação|
|---|---|
| Família | N | 
| Sub-família | C |
| # de vCPUs | 4 |
| Características adírias | a = processador baseado em AMD <br> s = Armazenamento Premium capaz |
| Tipo de Acelerador | T4 |
| Versão | v3 |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [os tamanhos VM](./sizes.md) disponíveis em Azure. 