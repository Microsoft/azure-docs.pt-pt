---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a14ae76e15c1adb59917e61fbcbdaa34a7efa2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77472026"
---
Os discos partilhados azure (pré-visualização) são uma nova funcionalidade para discos geridos pelo Azure que permite ligar simultaneamente um disco gerido pelo Azure a múltiplas máquinas virtuais (VMs). A fixação de um disco gerido a vários VMs permite-lhe implementar aplicações agrupadas novas ou migratórias existentes para o Azure.

## <a name="how-it-works"></a>Como funciona

Os VMs do cluster podem ler ou escrever para o seu disco anexo com base na reserva escolhida pela aplicação agrupada utilizando [Reservas Persistentes SCSI](https://www.t10.org/members/w_spc3.htm) (SCSI PR). O SCSI PR é um padrão de indústria bem conhecido, alavancado por aplicações em execução na Rede de Áreade Armazenamento (SAN) no local. Permitir o SCSI PR num disco gerido permite-lhe migrar estas aplicações para o Azure como está.

Os discos geridos com discos partilhados oferecem armazenamento de blocopartilhado partilhado que pode ser acedido por vários VMs, isto é exposto como números lógicos de unidade (LUNs). As LUNs são então apresentadas a um iniciador (VM) a partir de um alvo (disco). Estes LUNs parecem armazenamento direto (DAS) ou uma unidade local para o VM.

Os discos geridos com discos partilhados ativados não oferecem de forma nativa um sistema de ficheiros totalmente gerido que pode ser acedido através de SMB/NFS. Terá de utilizar um gestor de cluster, como o Windows Server Failover Cluster (WSFC) ou o Pacemaker, que lida com a comunicação do nó cluster, bem como o bloqueio de escrita.

## <a name="limitations"></a>Limitações

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Tamanhos do disco

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Cargas de trabalho da amostra

### <a name="windows"></a>Windows

A maioria do clustering baseado no Windows baseia-se no WSFC, que trata de todas as infraestruturas centrais para a comunicação do nó de cluster, permitindo que as suas aplicações tirem partido de padrões de acesso paralelos. O WSFC permite opções baseadas em CSV e não-CSV dependendo da sua versão do Windows Server. Para mais detalhes, consulte criar [um cluster failover](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster).

Algumas aplicações populares em execução no WSFC incluem:

- Casos de cluster de falha do servidor SQL (FCI)
- Servidor de ficheiros scale-out (SoFS)
- Servidor de ficheiros para uso geral (carga de trabalho iW)
- Disco de perfil do servidor de servidor de ambiente de trabalho remoto (RDS UPD)
- SAP ASCS/SCS

### <a name="linux"></a>Linux

Os clusters Linux podem alavancar gestores de clusters como [o Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker). O Pacemaker baseia-se em [Corosync,](http://corosync.github.io/corosync/)permitindo comunicações de cluster para aplicações implementadas em ambientes altamente disponíveis. Alguns sistemas comuns de ficheiros agrupados incluem [ocfs2](https://oss.oracle.com/projects/ocfs2/) e [gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). Pode manipular reservas e registos utilizando utilitários como [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) e [sg_persist.](https://linux.die.net/man/8/sg_persist)

## <a name="persistent-reservation-flow"></a>Fluxo persistente de reserva

O diagrama seguinte ilustra uma aplicação de base de dados agrupada de 2 nós que aproveita o SCSI PR para permitir a falha de um nó para o outro.

![Dois aglomerados de nós. Uma aplicação em execução no cluster está a manipular o acesso ao disco](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

O fluxo é o seguinte:

1. A aplicação agrupada em execução tanto no Azure VM1 como no VM2 regista a sua intenção de ler ou escrever para o disco.
1. A instância de candidatura no VM1 leva então reserva exclusiva para escrever para o disco.
1. Esta reserva é executada no seu disco Azure e a base de dados pode agora escrever exclusivamente para o disco. Quaisquer escritos da instância de candidatura em VM2 não serão bem sucedidos.
1. Se a instância de aplicação no VM1 diminuir, a instância no VM2 pode agora iniciar uma falha na base de dados e a tomada de conta do disco.
1. Esta reserva é agora executada no disco Azure e o disco deixará de aceitar as escritas da VM1. Só aceitará escritos da VM2.
1. A aplicação agrupada pode completar a falha da base de dados e servir pedidos de VM2.

O diagrama seguinte ilustra outra carga de trabalho agrupada comum composta por múltiplos nós de leitura de dados do disco para executar processos paralelos, como a formação de modelos de aprendizagem automática.

![Quatro cluster VM do nó, cada nó regista a intenção de escrever, a aplicação requer reserva exclusiva para lidar corretamente com resultados de escrita](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

O fluxo é o seguinte:

1. A aplicação agrupada em todos os VMs regista a intenção de ler ou escrever para o disco.
1. A instância de aplicação no VM1 requer uma reserva exclusiva para escrever para o disco enquanto abre leituras para o disco de outros VMs.
1. Esta reserva é executada no seu disco Azure.
1. Todos os nós do cluster podem agora ler a partir do disco. Apenas um nó escreve os resultados para o disco, em nome de todos os nós do cluster.