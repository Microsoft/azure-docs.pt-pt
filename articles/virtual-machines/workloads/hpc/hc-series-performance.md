---
title: Desempenho do tamanho do tamanho da série HC VM
description: Saiba mais sobre os resultados dos testes de desempenho para tamanhos VM da série HC em Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/10/2020
ms.author: amverma
ms.openlocfilehash: 0d63d9770dacf6a200e8b81e8d47d9f807a8a448
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90603447"
---
# <a name="hc-series-virtual-machine-sizes"></a>Tamanhos de máquina virtual da série HC

Vários testes de desempenho foram realizados em tamanhos da série HC. Seguem-se alguns dos resultados deste teste de desempenho.

| Carga de trabalho                                        | HB                    |
|-------------------------------------------------|-----------------------|
| STREAM Triad                                    | 190 GB/s (Intel MLC AVX-512)  |
| High-Performance Linpack (HPL)                  | 3520 GigaFLOPS (Rpeak), 2970 GigaFLOPS (Rmax) |
| Latência RDMA & largura de banda                        | 1,05 microsegundos, 96,8 Gb/s   |
| FIO no NVMe SSD local                           | 1.3 leituras GB/s, 900 MB/s escreve |  
| IOR em 4 Azure Premium SSD (P30 Discos Geridos, RAID0)**  | 780 leituras de MB/s, 780 MB/writes |

## <a name="mpi-latency"></a>Latência do MPI

É executado o teste de latência MPI da suíte de microesferas OSU. Os scripts da amostra estão no [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

:::image type="content" source="./media/latency-hc.png" alt-text="Latência mpi em Azure HC.":::

## <a name="mpi-bandwidth"></a>Largura de banda do MPI

É executado o teste de largura de banda MPI da suíte de microesferas OSU. Os scripts da amostra estão no [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hc.png" alt-text="Latência mpi em Azure HC.":::


## <a name="mellanox-perftest"></a>Mellanox Perftest

O [pacote Mellanox Perftest](https://community.mellanox.com/s/article/perftest-package) tem muitos testes InfiniBand, tais como latência (ib_send_lat) e largura de banda (ib_send_bw). Um comando de exemplo está abaixo.

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

## <a name="next-steps"></a>Passos seguintes

- Leia sobre os últimos anúncios e alguns exemplos e resultados da High Performance Computing (HPC) nos [Blogs comunitários da Azure Compute Tech.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- Para uma visão arquitetónica de nível superior da execução das cargas de trabalho do HPC, consulte [a High Performance Computing (HPC) em Azure](/azure/architecture/topics/high-performance-computing/).
