---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/14/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6f819d9b6ba4d74612da304aafea0118f9094bde
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91451563"
---
Os discos partilhados Azure são uma nova funcionalidade para discos geridos aZure que permite anexar um disco gerido a várias máquinas virtuais (VMs) simultaneamente. A anexação de um disco gerido a vários VMs permite-lhe implementar novas ou migrar aplicações agrupadas existentes para o Azure.

## <a name="how-it-works"></a>Como funciona

Os VMs no cluster podem ler ou escrever no seu disco anexo com base na reserva escolhida pela aplicação agrupada utilizando [Reservas Persistentes SCSI](https://www.t10.org/members/w_spc3.htm) (SCSI PR). O SCSI PR é um padrão da indústria alavancado por aplicações em execução na Rede de Área de Armazenamento (SAN) no local. Permitir o SCSI PR num disco gerido permite-lhe migrar estas aplicações para a Azure como está.

Os discos geridos partilhados oferecem armazenamento de blocos partilhados que podem ser acedidos a partir de vários VMs, estes são expostos como números de unidade lógica (LUNs). Os LUNs são então apresentados a um iniciador (VM) a partir de um alvo (disco). Estes LUNs parecem armazenamento direto (DAS) ou uma unidade local para o VM.

Os discos geridos partilhados não oferecem de forma nativa um sistema de ficheiros totalmente gerido que pode ser acedido usando SMB/NFS. É necessário utilizar um gestor de cluster, como o Windows Server Failover Cluster (WSFC) ou o Pacemaker, que lida com a comunicação do nó de cluster e escreve o bloqueio.

## <a name="limitations"></a>Limitações

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

### <a name="operating-system-requirements"></a>Requisitos do sistema operativo

Os discos partilhados suportam vários sistemas operativos. Consulte as secções [Windows](#windows) ou [Linux](#linux) para os sistemas operativos suportados.

## <a name="disk-sizes"></a>Tamanhos de disco

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Cargas de trabalho de amostra

### <a name="windows"></a>Windows

Os discos partilhados Azure são suportados no Windows Server 2008 e mais recentes. A maioria dos clustering baseados no Windows baseia-se no WSFC, que trata de todas as infraestruturas fundamentais para a comunicação de nó de cluster, permitindo que as suas aplicações tirem partido de padrões de acesso paralelos. O WSFC permite opções baseadas em CSV e não CSV consoante a versão do Windows Server. Para mais detalhes, veja [Criar um cluster de ativação pós-falha](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster).

Algumas aplicações populares em execução no WSFC incluem:

- [Criar um FCI com discos partilhados Azure (SQL Server em VMs Azure)](../articles/azure-sql/virtual-machines/windows/failover-cluster-instance-azure-shared-disks-manually-configure.md)
- Servidor de ficheiros scale-out (SoFS) [modelo] (https://aka.ms/azure-shared-disk-sofs-template)
- SAP ASCS/SCS [modelo] (https://aka.ms/azure-shared-disk-sapacs-template)
- Servidor de Ficheiros para Utilização Geral (carga de trabalho IW)
- Disco de Perfil do Utilizador do Servidor de Ambiente de Trabalho Remoto (RDS UPD)

### <a name="linux"></a>Linux

Os discos partilhados Azure são suportados em:
- [SUSE SLE para SAP e SUSE SLE HA 15 SP1 e superior](https://documentation.suse.com/sle-ha/15-SP1/single-html/SLE-HA-guide/index.html)
- [Ubuntu 18.04 e acima](https://discourse.ubuntu.com/t/ubuntu-high-availability-corosync-pacemaker-shared-disk-environments/14874)
- [Pré-visualização do desenvolvedor RHEL em qualquer versão RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_and_managing_high_availability_clusters/index)
- [Oracle Enterprise Linux](https://docs.oracle.com/en/operating-systems/oracle-linux/8/availability/hacluster-1.html)

Os clusters Linux podem alavancar gestores de clusters como [o Pacemaker.](https://wiki.clusterlabs.org/wiki/Pacemaker) O Pacemaker baseia-se no [Corosync,](http://corosync.github.io/corosync/)permitindo comunicações de cluster para aplicações implantadas em ambientes altamente disponíveis. Alguns sistemas de ficheiros agrupados comuns incluem [ocfs2](https://oss.oracle.com/projects/ocfs2/) e [gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). Pode utilizar modelos de agrupamento scsi persistente (SCSI PR) e/ou dispositivo de bloqueio STONITH (SBD) para arbitrar o acesso ao disco. Ao utilizar o SCSI PR, pode manipular reservas e registos utilizando utilitários como [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) e [sg_persist](https://linux.die.net/man/8/sg_persist).

## <a name="persistent-reservation-flow"></a>Fluxo de reserva persistente

O diagrama seguinte ilustra uma aplicação de base de dados agrupada de 2 nós de amostra que aproveita o SCSI PR para permitir a falha de um nó para o outro.

![Dois aglomerados de nó. Uma aplicação em execução no cluster está a lidar com o acesso ao disco](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

O fluxo é o seguinte:

1. A aplicação agrupada em funcionamento tanto no Azure VM1 como no VM2 regista a sua intenção de ler ou escrever para o disco.
1. A aplicação em VM1 requer então reserva exclusiva para escrever no disco.
1. Esta reserva é aplicada no seu disco Azure e a base de dados pode agora escrever exclusivamente para o disco. Quaisquer escritos da aplicação em VM2 não serão bem sucedidos.
1. Se a instância de aplicação em VM1 se concretizar, a instância em VM2 pode agora iniciar uma falha na base de dados e a tomada de posse do disco.
1. Esta reserva é agora aplicada no disco Azure e o disco deixará de aceitar as escritas da VM1. Só aceitará escritos de VM2.
1. A aplicação agrupada pode completar a falha da base de dados e servir pedidos de VM2.

O diagrama seguinte ilustra outra carga de trabalho agrupada comum composta por múltiplos nós que lêem dados do disco para executar processos paralelos, tais como a formação de modelos de aprendizagem automática.

![Quatro aglomerados VM de nó, cada nó regista intenção de escrever, a aplicação requer reserva exclusiva para lidar corretamente com os resultados da escrita](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

O fluxo é o seguinte:

1. A aplicação agrupada em todos os VMs regista a intenção de ler ou escrever no disco.
1. A aplicação em VM1 requer uma reserva exclusiva para escrever no disco enquanto abre as leituras para o disco de outros VMs.
1. Esta reserva é aplicada no seu disco Azure.
1. Todos os nós do cluster podem agora ler a partir do disco. Apenas um nó responde aos resultados do disco, em nome de todos os nós do cluster.

### <a name="ultra-disks-reservation-flow"></a>Fluxo de reserva de discos ultra

Os discos ultra oferecem um acelerador adicional, para um total de dois aceleradores. Devido a isso, o fluxo de reserva de discos ultra pode funcionar como descrito na secção anterior, ou pode acelerar e distribuir o desempenho de forma mais granular.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text="Uma imagem de uma tabela que retrata o acesso 'ReadOnly' ou 'Read/Write' para Titular de Reserva, Registado e Outros.":::

## <a name="performance-throttles"></a>Aceleradores de desempenho

### <a name="premium-ssd-performance-throttles"></a>Aceleradores de desempenho Premium SSD

Com SSD premium, o disco IOPS e a produção são fixos, por exemplo, O IOPS de um P30 é 5000. Este valor permanece se o disco é partilhado em 2 VMs ou 5 VMs. Os limites do disco podem ser atingidos a partir de um único VM ou divididos em dois ou mais VMs. 

### <a name="ultra-disk-performance-throttles"></a>Aceleradores de desempenho ultra disco

Os discos ultra têm a capacidade única de permitir definir o seu desempenho expondo atributos modificáveis e permitindo-lhe modificá-los. Por padrão, existem apenas dois atributos modificáveis mas os discos ultra partilhados têm dois atributos adicionais.


|Atributo  |Descrição  |
|---------|---------|
|DiskIOPSReadWrite     |O número total de IOPS permitiu em todos os VMs montando o disco de partilha com acesso de escrita.         |
|DiskMBpsReadWrite     |A produção total (MB/s) permitiu que todos os VMs montassem o disco partilhado com acesso de escrita.         |
|DiskIOPSReadOnly*     |O número total de IOPS permitiu que todos os VMs montassem o disco partilhado como `ReadOnly` .         |
|DiskMBpsReadOnly*     |A produção total (MB/s) permitiu que todos os VMs montassem o disco partilhado como `ReadOnly` .         |

\* Aplica-se apenas a discos ultra partilhados

As seguintes fórmulas explicam como os atributos de desempenho podem ser definidos, uma vez que são modificáveis pelo utilizador:

- DiskIOPSReadWrite/DiskIOPSReadOnly: 
    - Limites IOPS de 300 IOPS/GiB, até um máximo de 160K IOPS por disco
    - Mínimo de 100 IOPS
    - DiskIOPSReadWrite + DiskIOPSReadOnly é pelo menos 2 IOPS/GiB
- DiskMBpsRead Write/DiskMBpsReadOnly:
    - O limite de produção de um único disco é de 256 KiB/s para cada IOPS provisionado, até um máximo de 2000 MBps por disco
    - A produção mínima garantida por disco é de 4KiB/s para cada IOPS provisível, com um mínimo de base global de 1 MBps

#### <a name="examples"></a>Exemplos

Os exemplos que se seguem retratam alguns cenários que mostram como o estrangulamento pode funcionar com discos ultra partilhados, especificamente.

##### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>Dois aglomerados de nó usando volumes compartilhados de cluster

Segue-se um exemplo de um WSFC de 2 nós utilizando volumes partilhados agrupados. Com esta configuração, ambos os VMs têm acesso simultâneo ao disco, o que resulta na divisão do `ReadWrite` acelerador entre os dois VMs e o acelerador não ser `ReadOnly` utilizado.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="Uma imagem de uma tabela que retrata o acesso 'ReadOnly' ou 'Read/Write' para Titular de Reserva, Registado e Outros.":::

##### <a name="two-node-cluster-without-cluster-share-volumes"></a>Dois aglomerados de nó sem volumes de partilha de cluster

Segue-se um exemplo de um WSFC de 2 nós que não está a utilizar volumes partilhados agrupados. Com esta configuração, apenas um VM tem acesso por escrito ao disco. Isto resulta em que o `ReadWrite` acelerador seja utilizado exclusivamente para o VM primário e o `ReadOnly` acelerador só seja utilizado pelo secundário.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="Uma imagem de uma tabela que retrata o acesso 'ReadOnly' ou 'Read/Write' para Titular de Reserva, Registado e Outros.":::

##### <a name="four-node-linux-cluster"></a>Aglomerado de linux de quatro nó

Segue-se um exemplo de um aglomerado linux de 4 nós com um único escritor e três leitores de escala. Com esta configuração, apenas um VM tem acesso por escrito ao disco. Isto resulta na utilização exclusiva do `ReadWrite` acelerador para o VM primário e no `ReadOnly` acelerador que está a ser dividido pelos VM secundários.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="Uma imagem de uma tabela que retrata o acesso 'ReadOnly' ou 'Read/Write' para Titular de Reserva, Registado e Outros.":::

#### <a name="ultra-pricing"></a>Preços ultra-preços

Os discos ultra partilhados têm preços baseados na capacidade aprovisionada, no total de IOPS aprovisionados (diskIOPSReadWrite + diskIOPSReadOnly) e no total de PDS (diskMBpsReadWrite + diskMBpsReadOnly). Não há custos adicionais para cada montagem VM adicional. Por exemplo, um disco ultra partilhado com a seguinte configuração (diskSizeGB: 1024, DiskIOPSReadWrite: 10000, DiskMBpsReadWrite: 600, DiskIOPSReadOnly: 100, DiskMBpsReadOnly: 1) é cobrado com 1024 GiB, 10100 IOPS e 601 MBps independentemente de ser montado em dois VMs ou cinco VMs.