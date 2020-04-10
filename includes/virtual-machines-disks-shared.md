---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c3e5beaef7fcc9d407103834e2040957ff32984c
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008566"
---
Os discos partilhados azure (pré-visualização) são uma nova funcionalidade para discos geridos pelo Azure que permitem ligar simultaneamente um disco gerido a várias máquinas virtuais (VMs). A fixação de um disco gerido a vários VMs permite-lhe implementar aplicações agrupadas novas ou migratórias existentes para o Azure.

## <a name="how-it-works"></a>Como funciona

Os VMs do cluster podem ler ou escrever para o seu disco anexo com base na reserva escolhida pela aplicação agrupada utilizando [Reservas Persistentes SCSI](https://www.t10.org/members/w_spc3.htm) (SCSI PR). O SCSI PR é uma norma da indústria alavancada por aplicações em execução na Rede de Áreas de Armazenamento (SAN) no local. Permitir o SCSI PR num disco gerido permite-lhe migrar estas aplicações para o Azure como está.

A partilha de discos geridos oferece armazenamento de blocos partilhados que podem ser acedidos a partir de vários VMs, estes são expostos como números lógicos de unidade (LUNs). As LUNs são então apresentadas a um iniciador (VM) a partir de um alvo (disco). Estes LUNs parecem armazenamento direto (DAS) ou uma unidade local para o VM.

Os discos geridos partilhados não oferecem de forma nativa um sistema de ficheiros totalmente gerido que pode ser acedido usando SMB/NFS. Você precisa usar um gestor de cluster, como Windows Server Failover Cluster (WSFC) ou Pacemaker, que lida com a comunicação do nó cluster, bem como o bloqueio de escrita.

## <a name="limitations"></a>Limitações

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Tamanhos de disco

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

### <a name="ultra-disks-reservation-flow"></a>Fluxo de reserva de discos ultra

Os discos ultra oferecem um acelerador adicional, para um total de dois aceleradores. Devido a isso, o fluxo de reservas de discos ultra pode funcionar como descrito na secção anterior, ou pode acelerar e distribuir o desempenho de forma mais granular.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text=" ":::

## <a name="ultra-disk-performance-throttles"></a>Aceleradores de desempenho ultra-disco

Os discos ultra têm a capacidade única de permitir que você desloque o seu desempenho expondo atributos modificáveis e permitindo-lhe modificá-los. Por padrão, existem apenas dois atributos modificáveis mas, os discos ultra partilhados têm dois atributos adicionais.


|Atributo  |Descrição  |
|---------|---------|
|DiskIOPSReadWrite     |O número total de IOPS permitido em todos os VMs montando o disco de partilha com acesso por escrito.         |
|DiskMBpsReadWrite     |A produção total (MB/s) permitida em todos os VMs que montam o disco partilhado com acesso por escrito.         |
|DiskIOPSReadOnly*     |O número total de IOPS permitido em todos os VMs montando o disco partilhado como ReadOnly.         |
|DiskMBpsReadOnly*     |A entrada total (MB/s) permitida em todos os VMs montando o disco partilhado como ReadOnly.         |

\*Aplica-se apenas a discos ultra partilhados

As seguintes fórmulas explicam como os atributos de desempenho podem ser definidos, uma vez que são modificáveis do utilizador:

- DiskiOPSReadWrite/DiskiOPsReadApenas: 
    - Limites iops de 300 IOPS/GiB, até um máximo de 160K IOPS por disco
    - Mínimo de 100 IOPS
    - DiskIOPSReadWrite + DiskIOPSReadSó é pelo menos 2 IOPS/GiB
- DiskMBpsRead Write/DiskMBpsReadOnly:
    - O limite de entrada de um único disco é de 256 KiB/s para cada IOPS provisionado, até um máximo de 2000 MBps por disco
    - O rendimento mínimo garantido por disco é 4KiB/s para cada IOPS provisionado, com um mínimo global de base de 1 MBps

### <a name="examples"></a>Exemplos

Os exemplos seguintes retratam alguns cenários que mostram como o estrangulamento pode funcionar com discos ultra partilhados, especificamente.

#### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>Dois nós cluster usando volumes partilhados cluster

Segue-se um exemplo de um WSFC de 2 nós utilizando volumes partilhados agrupados. Com esta configuração, ambos os VMs têm acesso por escrito simultâneo ao disco, o que resulta na divisão do acelerador ReadWrite entre os dois VMs e o acelerador ReadOnly não utilizado.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="CSV dois nó ultra exemplo":::

:::image-end:::

#### <a name="two-node-cluster-without-cluster-share-volumes"></a>Dois cluster sem volumes de partilha de cluster

Segue-se um exemplo de um WSFC de 2 nós que não está a usar volumes partilhados agrupados. Com esta configuração, apenas um VM tem acesso por escrito ao disco. Isto resulta no acelerador ReadWrite sendo utilizado exclusivamente para o VM primário e o acelerador ReadOnly apenas sendo utilizado pelo secundário.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="CSV dois nosdes sem csv exemplo de disco ultra":::

:::image-end:::

#### <a name="four-node-linux-cluster"></a>Aglomerado de quatro nós Linux

Segue-se um exemplo de um aglomerado linux de 4 nós com um único escritor e três leitores de escala. Com esta configuração, apenas um VM tem acesso por escrito ao disco. Isto resulta no acelerador ReadWrite sendo utilizado exclusivamente para o VM primário e o acelerador ReadOnly sendo dividido pelos VMs secundários.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="Quatro exemplo sarquetes de nó ultra":::

:::image-end:::