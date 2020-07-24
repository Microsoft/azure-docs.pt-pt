---
title: Azure tamanhos VM nomear convenções
description: Explica as convenções de nomeação utilizadas para tamanhos Azure VM
ms.service: virtual-machines
subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.date: 7/22/2020
ms.author: mimckitt
ms.custom: sttsinar
ms.openlocfilehash: 2059c6f374e4cd5c2518e2fc0ac0da5858b99825
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131723"
---
# <a name="azure-virtual-machine-sizes-naming-conventions"></a>Tamanhos de máquina virtual Azure nomeando convenções

Esta página descreve as convenções de nomeação utilizadas para os VMs Azure. Os VMs utilizam estas convenções de nomeação para denotar diferentes características e especificações.

## <a name="naming-convention-explanation"></a>Explicação da convenção de nomeação

**[Família]**  +  **[Sub-família*]**  +  **[# de vCPUs]**  +  **[Características aditivas]**  +  **[Tipo de acelerador*]**  +  **[Versão]**

|Valor | Explicação|
|---|---|
| Padrão, Básico ou Experimental | "Standard" é o valor padrão atribuído para todos os tamanhos GA VM | 
| Família | Indica a Série Familiar VM| 
| *Sub-família | Usado apenas para diferenciações de VM especializadas|
| # de vCPUs| Denota o número de VCPUs do VM |
| Características adírias | Uma ou mais letras minúsculas denotam características aditivas, tais como: <br> a = processador baseado em AMD <br> d = disco (disco temporário local está presente); isto é para novos VMs Azure, ver [Ddv4 e Série Ddsv4](./ddv4-ddsv4-series.md) <br> h = hibernação capaz <br> i = isolado <br> l = memória baixa <br> m = memória intensiva <br> t = memória minúscula <br> r = RDMA <br> s = Armazenamento Premium capaz, incluindo a possível utilização de [Ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk) (Nota: alguns tamanhos mais recentes sem o atributo de s ainda podem suportar Armazenamento Premium, por exemplo, M128, M64, etc.)<br> |
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

Saiba mais sobre [os tamanhos VM](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) disponíveis em Azure. 
