---
title: Problemas conhecidos com série HB e VMs da série HC - Azure Virtual Machines Microsoft Docs
description: Conheça os problemas conhecidos com tamanhos VM da série HB em Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 6316bcc91bb381facb4f77b2d8dbd8b22f9ed387
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660100"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>Problemas conhecidos relacionados com as VMs da série H e série N

Este artigo fornece as questões e soluções mais comuns ao utilizar os VMs da [série H](../../sizes-hpc.md) e da [série N.](../../sizes-gpu.md)

## <a name="dram-on-hb-series"></a>DRAM na série HB

Os VMs da série HB só podem expor 228 GB de RAM a VMs convidados neste momento. Isto deve-se a uma conhecida limitação do hipervisor Azure para evitar que as páginas sejam atribuídas ao DRAM local dos domínios DAM CCX (NUMA) reservados para o VM convidado.

## <a name="accelerated-networking"></a>Redes Aceleradas

O Azure Accelerated Networking não está ativado neste momento, mas irá avançar à medida que progredimos durante o período de pré-visualização. Notificaremos os clientes quando esta funcionalidade for suportada.

## <a name="qp0-access-restriction"></a>restrição de acesso qp0

Para evitar o acesso de hardware de baixo nível que pode resultar em vulnerabilidades de segurança, o Queue Pair 0 não está acessível aos VMs dos hóspedes. Isto só deve afetar as ações tipicamente associadas à administração do ConnectX-5 NIC, e executar alguns diagnósticos InfiniBand como ibdiagnet, mas não aplicações de utilizadores finais.

## <a name="gss-proxy"></a>GSS Proxy

A GSS Proxy tem um bug conhecido no CentOS/RHEL 7.5 que pode manifestar-se como uma penalidade significativa de desempenho e capacidade de resposta quando usado com NFS. Isto pode ser atenuado com:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Limpeza cache

Nos sistemas HPC, é frequentemente útil limpar a memória depois de um trabalho ter terminado antes de o próximo utilizador ser atribuído o mesmo nó. Depois de executar aplicações no Linux, poderá descobrir que a sua memória disponível reduz enquanto a memória do tampão aumenta, apesar de não ter execução de nenhuma aplicação.

![Screenshot do pedido de comando](./media/known-issues/cache-cleaning-1.png)

A utilização `numactl -H` mostrará com que NUMAnode a memória é tamponada (possivelmente todas). No Linux, os utilizadores podem limpar as caches de três formas de devolver a memória tamponada ou em cache para 'grátis'. Tens de ser raiz ou ter permissões de sudo.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Screenshot do pedido de comando](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Avisos de kernel

Pode ver as seguintes mensagens de aviso de núcleo ao iniciar um VM da série HB em Linux.

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```

Pode ignorar este aviso. Isto deve-se a uma conhecida limitação do hipervisor Azure que será abordado ao longo do tempo.


## <a name="infiniband-driver-installation-on-infiniband-enabled-n-series-vm-sizes"></a>Instalação do controlador InfiniBand em tamanhos VM da série N ativados

NC24r_v3 e ND40r_v2 estão ativados pela SR-IOV, enquanto o NC24r e o NC24r_v2 não estão ativados pela SR-IOV. Alguns detalhes sobre a bifurcação [aqui.](../../sizes-hpc.md#rdma-capable-instances)
O InfiniBand (IB) pode ser configurado nos tamanhos VM ativados pela SR-IOV com os condutores OFED, enquanto os tamanhos VM não-SR-IOV requerem condutores de ND. Este suporte IB está disponível adequadamente em [VMIs CentOS-HPC](configure.md). Para ubuntu, consulte aqui as [instruções](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351) para a instalação dos controladores OFED e ND, conforme descrito nos [documentos](enable-infiniband.md#vm-images-with-infiniband-drivers).


## <a name="next-steps"></a>Passos seguintes

- Reveja a [visão geral](hb-series-overview.md) da série HB e [a visão geral da série HC](hc-series-overview.md) para aprender sobre a configuração ideal das cargas de trabalho para desempenho e escalabilidade.
- Leia sobre os últimos anúncios e alguns exemplos e resultados do HPC no [Azure Compute Tech Community Blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Para uma visão arquitetónica de nível mais elevado da execução das cargas de trabalho do HPC, consulte [a High Performance Computing (HPC) em Azure](/azure/architecture/topics/high-performance-computing/).
