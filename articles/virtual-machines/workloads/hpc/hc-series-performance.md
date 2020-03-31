---
title: Desempenho do tamanho da série HC VM - Máquinas Virtuais Azure / Microsoft Docs
description: Conheça os resultados dos testes de desempenho para os tamanhos vm da série HC em Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: cea772f03d5e2838b44d50f3cf5e926d740be5f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707689"
---
# <a name="hc-series-virtual-machine-sizes"></a>Tamanhos de máquinas virtuais da série HC

Vários testes de desempenho foram executados em tamanhos de série HC. Seguem-se alguns dos resultados deste teste de desempenho.

| Carga de trabalho                                        | HB                    |
|-------------------------------------------------|-----------------------|
| Tríade STREAM                                    | ~190 GB/s (Intel MLC AVX-512)  |
| Linpack de alto desempenho (HPL)                  | ~3520 GigaFLOPS (Rpeak), ~2970 GigaFLOPS (Rmax) |
| Latência RDMA & largura de banda                        | 1,80 microsegundos, 96.3 Gb/s   |
| FIO em NVMe SSD local                           | ~1,3 GB/s lê, ~900 MB/s escreve |  
| IOR em 4 SSD Premium Azure (P30 Discos Geridos, RAID0)**  | ~780 MB/s lê, ~780 MB/escreve |

## <a name="infiniband-send-latency"></a>InfiniBand enviar latência

Mellanox Perftest.

```azure-cli
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

|  #bytes         | #iterations     | t_min[microsegundo]     | t_max[microsegundo]     | t_typical[microsegundo] | t_avg[microsegundo]     | t_stdev[microsegundo]   |
|-----------------|-----------------|-----------------|-----------------|-----------------|-----------------|-----------------|
| 2               | 1000            | 1.80            | 7.50            | 1.85            | 1.86            | 0,20            |
| 4               | 1000            | 1.79            | 6.06            | 1.83            | 1.84            | 0,20            |
| 8               | 1000            | 1.78            | 5.26            | 1.83            | 1.84            | 0.19            |
| 16              | 1000            | 1.79            | 6.21            | 1.83            | 1.84            | 0.22            |
| 32              | 1000            | 1.80            | 6.82            | 1.84            | 1.85            | 0.24            |
| 64              | 1000            | 1.85            | 5.47            | 1.88            | 1.86            | 0,12            |
| 128             | 1000            | 1.88            | 5.61            | 1.93            | 1.89            | 0,25            |
| 256             | 1000            | 2.24            | 6.39            | 2.28            | 2.02            | 0.18            |
| 512             | 1000            | 2.32            | 5.42            | 2.36            | 2.30            | 0.17            |
| 1024            | 1000            | 2.43            | 6.22            | 2.48            | 2.38            | 0.21            |
| 2048            | 1000            | 2.68            | 6.14            | 2.75            | 2.52            | 0,20            |
| 4096            | 1000            | 3.17            | 7.02            | 3.26            | 2.81            | 0.24            |

## <a name="osu-mpi-latency-test"></a>Teste de Latência MPI da OSU

Teste de latência MPI v5.4.3.

```azure-cli
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

| #bytes  | Latência [microsegundo] (MPICH 3.3 + CH4) | Latência [microsegundo] (OpenMPI 4.0.0) | Latência [microsegundo] (MVAPICH2 2.3) |
|------|----------|----------|----------|
| 2    | 1.84     | 1.78     | 2.08     |
| 4    | 1.84     | 1.79     | 2.08     |
| 8    | 1.85     | 1.79     | 2.05     |
| 16   | 1.85     | 1.79     | 2.1      |
| 32   | 1.87     | 1.82     | 2.12     |
| 64   | 2        | 1.95     | 2.13     |
| 128  | 2.05     | 2        | 2.18     |
| 256  | 2.48     | 2.44     | 2.75     |
| 512  | 2.57     | 2.52     | 2.81     |
| 1024 | 2.76     | 2.71     | 2.97     |
| 2048 | 3.09     | 3.11     | 3.34     |
| 4096 | 3.72     | 3.91     | 4.44     |

## <a name="mpi-bandwidth"></a>Largura de banda MPI

TESTE DE Largura de Banda MPI V5.4.3.

```azure-cli
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

| #Size   | Largura de banda (MB/s) | Largura de banda (Gb/s) |
|---------|------------------|------------------|
| 2       | 6.18             | 0.04944          |
| 4       | 13.27            | 0.10616          |
| 8       | 26.58            | 0.21264          |
| 16      | 53.51            | 0.42808          |
| 32      | 106.81           | 0.85448          |
| 64      | 211.24           | 1.68992          |
| 128     | 386.98           | 3.09584          |
| 256     | 756.32           | 6.05056          |
| 512     | 1434.2           | 11.4736          |
| 1024    | 2663.8           | 21.3104          |
| 2048    | 4396.99          | 35.17592         |
| 4096    | 6365.86          | 50.92688         |
| 8192    | 8137.9           | 65.1032          |
| 16384   | 9218.29          | 73.74632         |
| 32768   | 10564.61         | 84.51688         |
| 65536   | 11275.6          | 90.2048          |
| 131072  | 11633.7          | 93.0696          |
| 262144  | 11856.27         | 94.85016         |
| 524288  | 11962.69         | 95.70152         |
| 1048576 | 12025.43         | 96.20344         |
| 2097152 | 12038.7          | 96.3096          |
| 4194304 | 11290.92         | 90.32736         |
