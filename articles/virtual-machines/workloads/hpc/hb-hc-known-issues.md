---
title: Questões conhecidas com vMs da série HB e HC - Máquinas Virtuais Azure [ Máquinas Virtuais Azure ] Microsoft Docs
description: Saiba mais sobre questões conhecidas com tamanhos VM da série HB em Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 8d4b57fb2fee3849e102868c86fe3cab465fc70d
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67707787"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>Problemas conhecidos relacionados com a série HB e as VMs da série HC

Este artigo fornece as questões e soluções mais comuns ao utilizar vMs de sérieHB e séries HC.

## <a name="dram-on-hb-series"></a>DRAM em série HB

VMs da série HB só podem expor 228 GB de RAM a VMs convidados neste momento. Isto deve-se a uma conhecida limitação do hipervisor Azure para impedir que as páginas sejam atribuídas ao DRAM local de DOMÍNIOS DA (NUM) da AMD CCX reservado para o VM convidado.

## <a name="accelerated-networking"></a>Redes Aceleradas

O Azure Accelerated Networking não está ativado neste momento, mas irá progredir durante o período de Pré-visualização. Notificaremos os clientes quando esta funcionalidade for suportada.

## <a name="qp0-access-restriction"></a>qp0 Restrição de Acesso

Para evitar o acesso a hardware de baixo nível que possa resultar em vulnerabilidades de segurança, o Queue Pair 0 não está acessível aos VMs dos hóspedes. Isto só deve afetar as ações tipicamente associadas à administração do NIC ConnectX-5, e executar alguns diagnósticos InfiniBand como ibdiagnet, mas não aplicações de utilizador final.

## <a name="ud-transport"></a>Transporte ud

No lançamento, as séries HB e HC não suportam o Transporte Dinâmico Conectado (DCT). O apoio ao DCT será implementado ao longo do tempo. São apoiados os transportes de Ligação Fiável (RC) e Datagram (UD) fiáveis.

## <a name="gss-proxy"></a>Procuração GSS

A GSS Proxy tem um bug conhecido em CentOS/RHEL 7.5 que pode manifestar-se como uma penalidade significativa de desempenho e resposta quando usado com NFS. Isto pode ser atenuado com:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Limpeza de cache

Nos sistemas HPC, é muitas vezes útil limpar a memória depois de um trabalho ter terminado antes de o próximo utilizador ser atribuído o mesmo nó. Depois de executar aplicações no Linux, pode descobrir que a sua memória disponível reduz enquanto a memória do tampão aumenta, apesar de não executar nenhuma aplicação.

![Screenshot do pedido de comando](./media/known-issues/cache-cleaning-1.png)

A `numactl -H` utilização mostrará qual a(s) NUMAnode(s) a memória é tamponada com (possivelmente todos). Em Linux, os utilizadores podem limpar os caches de três formas de devolver a memória tamponada ou em cache a 'grátis'. Tens de ser raiz ou ter permissões sudo.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Screenshot do pedido de comando](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Avisos kernel

Pode ver as seguintes mensagens de aviso de kernel ao iniciar um VM da série HB sob o Linux.

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

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [computação de alto desempenho](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) em Azure.
