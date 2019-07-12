---
title: Problemas conhecidos com a série HB e as VMs da série HC - máquinas virtuais do Azure | Documentos da Microsoft
description: Saiba mais sobre problemas conhecidos com tamanhos VM da série HB no Azure.
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
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707787"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>Problemas conhecidos relacionados com a série HB e as VMs da série HC

Este artigo fornece as soluções e os problemas mais comuns ao utilizar HB série e as VMs da série HC.

## <a name="dram-on-hb-series"></a>DRAM na série HB

As VMs da série HB podem expor somente 228 GB de RAM para máquinas virtuais convidadas neste momento. Isso é devido a uma limitação conhecida do hipervisor do Azure para impedir que páginas que está sendo atribuído para o local DRAM da AMD CCX (domínios NUMA) reservado para VM do convidado.

## <a name="accelerated-networking"></a>Redes Aceleradas

Redes aceleradas do Azure não está ativada neste momento, mas será à medida que avançamos o período de pré-visualização. Os clientes será notificado quando esta funcionalidade é suportada.

## <a name="qp0-access-restriction"></a>Restrição de acesso de qp0

Para impedir o acesso de hardware de baixo nível que pode resultar em vulnerabilidades de segurança, o par de fila 0 não está acessível para máquinas virtuais convidadas. Isso deve afetam apenas as ações normalmente associadas a administração da NIC ConnectX-5 e execução de algumas diagnósticos de InfiniBand, como ibdiagnet, mas não pelos usuários finais próprios aplicativos.

## <a name="ud-transport"></a>Transporte UD

No início, a HB e HC-série não suportam dinamicamente ligados transporte (DCT). Suporte para DCT será implementado ao longo do tempo. Transportes fiáveis de ligação (RC) e pouco fiáveis datagrama (UD) são suportados.

## <a name="gss-proxy"></a>Proxy GSS

Proxy de GSS tem um bug conhecido no CentOS/RHEL 7.5 que podem se manifestar como uma significativa do desempenho e a penalidade de capacidade de resposta quando utilizado com o NFS. Isso pode ser mitigado com:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Cache de limpeza

Em sistemas HPC, muitas vezes é útil limpar a memória depois de uma tarefa esteja concluída antes do utilizador seguinte é atribuído o mesmo nó. Depois de executar aplicações no Linux, pode constatar que a memória disponível se reduz ao seu aumentos de memória intermédia, apesar de não está em execução quaisquer aplicações.

![Captura de ecrã da linha de comandos](./media/known-issues/cache-cleaning-1.png)

Usando `numactl -H` irá mostrar quais NUMAnode(s) a memória é armazenado em buffer com (possivelmente todos). No Linux, os utilizadores podem limpar as caches em três formas de devolver colocados em memória intermédia ou colocada em cache de memória para "gratuito". Tem de ser a raiz ou ter permissões de sudo.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Captura de ecrã da linha de comandos](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Avisos de kernel

Poderá ver as seguintes mensagens de aviso de kernel quando se reinicia uma VM de série HB em Linux.

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

Pode ignorar este aviso. Isso é devido a uma limitação conhecida do hipervisor do Azure que será resolvido ao longo do tempo.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [computação de alto desempenho](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) no Azure.
