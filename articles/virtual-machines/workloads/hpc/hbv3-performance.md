---
title: Desempenho do tamanho do tamanho VM da série HBv3
description: Saiba mais sobre os resultados dos testes de desempenho para tamanhos VM da série HBv3 em Azure.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: c92c30da2b8e78715df46341dc4bc502ed5e34ce
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472879"
---
# <a name="hbv3-series-virtual-machine-performance"></a>Desempenho da máquina virtual da série HBv3

Os utilizadores de Acesso Antecipado de VMS HBv3 podem esperar os seguintes números de desempenho em microesferas hpc comuns

| Carga de trabalho                                        | HBv3                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| STREAM Triad                                    | 330-350 GB/s (~82-86 GB/s por NUMA)                                     |
| High-Performance Linpack (HPL)                  | 4 TF (Rpeak, FP64), 8 TF (Rpeak, FP32) para o tamanho de 120 núcleos de VM               |
| Latência RDMA & largura de banda                        | 1.2 microsegundos (1 byte), 192 Gb/s (ida)                                        |
| FIO em SSDs NVMe locais (RAID0)                  | 7 leituras de 7 GB/s, 3 GB/s escreve; 186k leituras IOPS, 201k IOPS escreve |

## <a name="process-pinning"></a>Fixação de processos

A fixação de processos funciona bem em VMs da série HBv3 porque expomos o silício subjacente como está ao VM convidado. Recomendamos vivamente a fixação de processos para um melhor desempenho e consistência.

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
- Leia sobre os últimos anúncios e alguns exemplos e resultados da High Performance Computing (HPC) nos [Blogs comunitários da Azure Compute Tech.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- Para uma visão arquitetónica de nível superior da execução das cargas de trabalho do HPC, consulte [a High Performance Computing (HPC) em Azure](/azure/architecture/topics/high-performance-computing/).