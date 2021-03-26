---
title: Desempenho e escalabilidade dos tamanhos VM da série HBv3
description: Saiba mais sobre o desempenho e escalabilidade dos tamanhos VM da série HBv3 em Azure.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: bf64cfc8ad00fc7f761019ed2fa66089434a96ba
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604775"
---
# <a name="hbv3-series-virtual-machine-performance"></a>Desempenho da máquina virtual da série HBv3

As expectativas de desempenho utilizando as marcas comuns de micróbios HPC são as seguintes:

| Carga de trabalho                                        | HBv3                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| STREAM Triad                                    | 330-350 GB/s (~82-86 GB/s por NUMA)                                     |
| High-Performance Linpack (HPL)                  | 4 TF (Rpeak, FP64), 8 TF (Rpeak, FP32) para o tamanho de 120 núcleos de VM               |
| Latência RDMA & largura de banda                        | 1.2 microsegundos (1 byte), 192 Gb/s (ida)                                        |
| FIO em SSDs NVMe locais (RAID0)                  | 7 leituras de 7 GB/s, 3 GB/s escreve; 186k leituras IOPS, 201k IOPS escreve |

## <a name="process-pinning"></a>Fixação de processos

[A fixação de](compiling-scaling-applications.md#process-pinning) processos funciona bem em VMs da série HBv3 porque expomos o silício subjacente como está ao VM convidado. Recomendamos vivamente a fixação de processos para um melhor desempenho e consistência.

## <a name="mpi-latency"></a>Latência do MPI

O teste de latência MPI da suíte de microesferas OSU pode ser executado por baixo. Os scripts da amostra estão no [GitHub.](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
## <a name="mpi-bandwidth"></a>Largura de banda do MPI
O teste de largura de banda MPI da suíte de microesferas OSU pode ser executado por baixo. Os scripts da amostra estão no [GitHub.](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)
```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```
## <a name="mellanox-perftest"></a>Mellanox Perftest
O [pacote Mellanox Perftest](https://community.mellanox.com/s/article/perftest-package) tem muitos testes InfiniBand, tais como latência (ib_send_lat) e largura de banda (ib_send_bw). Um comando de exemplo está abaixo.
```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```
## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [o escalonamento de aplicações mpi](compiling-scaling-applications.md).
- Reveja os resultados de desempenho e escalabilidade das aplicações HPC nos VMs HBv3 no artigo da [TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/hpc-performance-and-scalability-results-with-azure-hbv3-vms/bc-p/2235843).
- Leia sobre os últimos anúncios, exemplos de carga de trabalho do HPC e resultados de desempenho nos [Blogs comunitários Azure Compute Tech.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- Para uma visão arquitetónica de nível superior da execução das cargas de trabalho do HPC, consulte [a High Performance Computing (HPC) em Azure](/azure/architecture/topics/high-performance-computing/).
