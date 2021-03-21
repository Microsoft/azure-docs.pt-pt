---
title: Desempenho do tamanho vm da série HB
description: Saiba mais sobre os resultados dos testes de desempenho para tamanhos VM da série HB em Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 09/09/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 1f2d24279d3e74774da05eba42c0e916370d4f1c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101672167"
---
# <a name="hb-series-virtual-machine-sizes"></a>Tamanhos de máquina virtual da série HB

Vários testes de desempenho foram executados em tamanhos da série HB. Seguem-se alguns dos resultados deste teste de desempenho.

| Carga de trabalho                                        | HB                    |
|-------------------------------------------------|-----------------------|
| STREAM Triad                                    | 260 GB/s (32-33 GB/s por CCX)  |
| High-Performance Linpack (HPL)                  | 1.000 GigaFLOPS (Rpeak), 860 GigaFLOPS (Rmax) |
| Latência RDMA & largura de banda                        | 1.27 microsegundos, 99,1 Gb/s   |
| FIO no NVMe SSD local                           | 1.7 leituras DE 1,0 GB/s      |  
| IOR em 4 * Azure Premium SSD (P30 Discos Geridos, RAID0)**  | 725 leituras de MB/s, 780 MB/writes   |


## <a name="mpi-latency"></a>Latência do MPI

É executado o teste de latência MPI da suíte de microesferas OSU. Os scripts da amostra estão no [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

:::image type="content" source="./media/latency-hb.png" alt-text="Latência mpi em Azure HB.":::

## <a name="mpi-bandwidth"></a>Largura de banda do MPI

É executado o teste de largura de banda MPI da suíte de microesferas OSU. Os scripts da amostra estão no [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hb.png" alt-text="Largura de banda mpi em Azure HB.":::


## <a name="mellanox-perftest"></a>Mellanox Perftest

O [pacote Mellanox Perftest](https://community.mellanox.com/s/article/perftest-package) tem muitos testes InfiniBand, tais como latência (ib_send_lat) e largura de banda (ib_send_bw). Um comando de exemplo está abaixo.

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

## <a name="next-steps"></a>Passos seguintes

- Leia sobre os últimos anúncios e alguns exemplos e resultados da High Performance Computing (HPC) nos [Blogs comunitários da Azure Compute Tech.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- Para uma visão arquitetónica de nível superior da execução das cargas de trabalho do HPC, consulte [a High Performance Computing (HPC) em Azure](/azure/architecture/topics/high-performance-computing/).
