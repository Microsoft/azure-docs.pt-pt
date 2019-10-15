---
title: Alta disponibilidade e recuperação de desastres para SQL Server | Microsoft Docs
description: Uma discussão sobre os vários tipos de estratégias HADR para SQL Server em execução em máquinas virtuais do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: mikeray
ms.openlocfilehash: 1d0bdfbbad7e811ac8f1eeffb1991cc5430483a6
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262898"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Elevada disponibilidade e recuperação após desastre para SQL Server em Máquinas Virtuais do Azure

Microsoft Azure VMs (máquinas virtuais) com SQL Server pode ajudar a reduzir o custo de uma solução de banco de dados HADR (alta disponibilidade e recuperação de desastre). Há suporte para a maioria SQL Server soluções HADR em máquinas virtuais do Azure, tanto como as soluções híbridas e do Azure. Em uma solução somente do Azure, todo o sistema HADR é executado no Azure. Em uma configuração híbrida, parte da solução é executada no Azure e a outra parte é executada localmente em sua organização. A flexibilidade do ambiente do Azure permite que você mova parcialmente ou completamente para o Azure para atender aos requisitos de orçamento e HADR de seus sistemas de banco de dados SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>Noções básicas sobre a necessidade de uma solução HADR
Cabe a você garantir que o seu sistema de banco de dados possua os recursos HADR que o SLA (contrato de nível de serviço) exige. O fato de que o Azure fornece mecanismos de alta disponibilidade, como a recuperação de serviço para serviços de nuvem e detecção de recuperação de falha para máquinas virtuais, não garante que você possa atender ao SLA desejado. Esses mecanismos protegem a alta disponibilidade das VMs, mas não a alta disponibilidade de SQL Server em execução nas VMs. É possível que a instância de SQL Server falhe enquanto a VM estiver online e íntegra. Além disso, até mesmo os mecanismos de alta disponibilidade fornecidos pelo Azure permitem o tempo de inatividade das VMs devido a eventos como recuperação de falhas de software ou hardware e atualizações do sistema operacional.

Além disso, o GRS (armazenamento com redundância geográfica) no Azure, que é implementado com um recurso chamado replicação geográfica, pode não ser uma solução de recuperação de desastre adequada para seus bancos de dados. Como a replicação geográfica envia dados de forma assíncrona, as atualizações recentes podem ser perdidas no caso de desastre. Mais informações sobre as limitações de replicação geográfica são abordadas na seção [replicação geográfica sem suporte para dados e arquivos de log em discos separados](#geo-replication-support) .

## <a name="hadr-deployment-architectures"></a>Arquiteturas de implantação HADR
SQL Server tecnologias HADR com suporte no Azure incluem:

* [Always On grupos de disponibilidade](https://technet.microsoft.com/library/hh510230.aspx)
* [Always On instâncias de cluster de failover](https://technet.microsoft.com/library/ms189134.aspx)
* [Envio de logs](https://technet.microsoft.com/library/ms187103.aspx)
* [SQL Server Backup e restauração com o serviço de armazenamento de BLOBs do Azure](https://msdn.microsoft.com/library/jj919148.aspx)
* [Espelhamento de banco de dados](https://technet.microsoft.com/library/ms189852.aspx) -preterido no SQL Server 2016

É possível combinar as tecnologias para implementar uma solução de SQL Server que tenha recursos de alta disponibilidade e recuperação de desastres. Dependendo da tecnologia usada, uma implantação híbrida pode exigir um túnel VPN com a rede virtual do Azure. As seções a seguir mostram algumas das arquiteturas de implantação de exemplo.

## <a name="azure-only-high-availability-solutions"></a>Somente Azure: Soluções de alta disponibilidade

Você pode ter uma solução de alta disponibilidade para SQL Server em um nível de banco de dados com Always On grupos de disponibilidade – chamados de grupos de disponibilidade. Você também pode criar uma solução de alta disponibilidade em um nível de instância com Always On instâncias de cluster de failover-instâncias de cluster de failover. Para redundância adicional, você pode criar redundância em ambos os níveis criando grupos de disponibilidade em instâncias de cluster de failover. 

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de disponibilidade** |As réplicas de disponibilidade em execução nas VMs do Azure na mesma região fornecem alta disponibilidade. Você precisa configurar uma VM do controlador de domínio, pois o clustering de failover do Windows requer um domínio Active Directory.<br/> ![Grupos de disponibilidade](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-ha-always-on.png)<br/>Para obter mais informações, consulte [configurar grupos de disponibilidade no Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Instâncias de cluster de failover** |As FCI (instâncias de cluster de failover), que exigem armazenamento compartilhado, podem ser criadas de três maneiras diferentes.<br/><br/>1. Um cluster de failover de dois nós em execução em VMs do Azure com armazenamento anexado usando o [Windows\) Server 2016 espaços de armazenamento diretos \(S2D](virtual-machines-windows-portal-sql-create-failover-cluster.md) para fornecer uma SAN virtual baseada em software.<br/><br/>2. Um cluster de failover de dois nós em execução em VMs do Azure com o armazenamento com suporte de uma solução de clustering de terceiros. Para um exemplo específico que usa o SIOS datakeeper, consulte [alta disponibilidade para um compartilhamento de arquivos usando clustering de failover e o software de terceiros sios Datakeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>3. Um cluster de failover de dois nós em execução em VMs do Azure com armazenamento de bloco compartilhado de destino iSCSI remoto via ExpressRoute. Por exemplo, o armazenamento privado da NetApp (NPS) expõe um destino iSCSI por meio do ExpressRoute com Equinix para VMs do Azure.<br/><br/>Para soluções de replicação de dados e armazenamento compartilhado de terceiros, você deve entrar em contato com o fornecedor para obter quaisquer problemas relacionados ao acesso a dados no failover.<br/><br/>Observe que o uso do FCI na parte superior do [armazenamento de arquivos do Azure](https://azure.microsoft.com/services/storage/files/) ainda não é compatível, pois essa solução não utiliza o armazenamento Premium. Estamos trabalhando para dar suporte a isso em breve. |

## <a name="azure-only-disaster-recovery-solutions"></a>Somente Azure: Soluções de recuperação após desastre
Você pode ter uma solução de recuperação de desastre para seus bancos de dados SQL Server no Azure usando grupos de disponibilidade, espelhamento de banco de dados ou backup e restauração com blobs de armazenamento.

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de disponibilidade** |Réplicas de disponibilidade em execução em vários data centers em VMs do Azure para recuperação de desastre. Essa solução entre regiões protege contra a interrupção completa do site. <br/> ![Grupos de disponibilidade](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-alwayson.png)<br/>Dentro de uma região, todas as réplicas devem estar dentro do mesmo serviço de nuvem e da mesma VNet. Como cada região terá uma VNet separada, essas soluções exigem conectividade VNet para VNet. Para obter mais informações, consulte [Configurar uma conexão vnet a vnet usando o portal do Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Para obter instruções detalhadas, consulte [configurar um grupo de disponibilidade SQL Server em máquinas virtuais do Azure em regiões diferentes](virtual-machines-windows-portal-sql-availability-group-dr.md).|
| **Espelhamento de banco de dados** |Principal e espelhamento e servidores em execução em diferentes data centers para recuperação de desastre. Você deve implantar usando certificados de servidor. Não há suporte para o espelhamento de banco de dados SQL Server para SQL Server 2008 nem SQL Server 2008 R2 em uma VM do Azure. <br/>![Espelhamento de banco de dados](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-dbmirroring.png) |
| **Backup e restauração com o serviço de armazenamento de BLOBs do Azure** |Os bancos de dados de produção com backup direto no armazenamento de BLOBs em um datacenter diferente para recuperação de desastres.<br/>![Cópia de Segurança e Restauro](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-backup-restore.png)<br/>Para obter mais informações, consulte [backup e restauração para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md). |
| **Replicar e realizar failover SQL Server no Azure com Azure Site Recovery** |SQL Server de produção de um datacenter do Azure replicados diretamente para o armazenamento do Azure de diferentes datacenters do Azure para recuperação de desastres.<br/>![Replicar usando Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-standalone-sqlserver-asr.png)<br/>Para obter mais informações, consulte [proteger SQL Server usando SQL Server recuperação de desastres e Azure site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>TI híbrida: Soluções de recuperação após desastre
Você pode ter uma solução de recuperação de desastre para seus bancos de dados SQL Server em um ambiente de ti híbrido usando grupos de disponibilidade, espelhamento de banco de dados, envio de logs e backup e restauração com o armazenamento de blog do Azure.

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de disponibilidade** |Algumas réplicas de disponibilidade em execução em VMs do Azure e outras réplicas executadas localmente para recuperação de desastres entre sites. O local de produção pode ser local ou em um datacenter do Azure.<br/>![Grupos de disponibilidade](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-alwayson.png)<br/>Como todas as réplicas de disponibilidade devem estar no mesmo cluster de failover, o cluster deve abranger ambas as redes (um cluster de failover de várias sub-redes). Essa configuração requer uma conexão VPN entre o Azure e a rede local.<br/><br/>Para a recuperação de desastres bem-sucedida de seus bancos de dados, você também deve instalar um controlador de domínio de réplica no local de recuperação de desastre.<br/><br/>É possível usar o assistente para adicionar réplica no SSMS para adicionar uma réplica do Azure a um grupo de disponibilidade Always On existente. Para obter mais informações, consulte Tutorial: Estenda seu grupo de disponibilidade de Always On para o Azure. |
| **Espelhamento de banco de dados** |Um parceiro em execução em uma VM do Azure e o outro executado localmente para recuperação de desastres entre sites usando certificados de servidor. Os parceiros não precisam estar no mesmo domínio Active Directory e nenhuma conexão VPN é necessária.<br/>![Espelhamento de banco de dados](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-dbmirroring.png)<br/>Outro cenário de espelhamento de banco de dados envolve um parceiro em execução em uma VM do Azure e o outro em execução local no mesmo domínio Active Directory para recuperação de desastres entre sites. Uma [conexão VPN entre a rede virtual do Azure e a rede local](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) é necessária.<br/><br/>Para a recuperação de desastres bem-sucedida de seus bancos de dados, você também deve instalar um controlador de domínio de réplica no local de recuperação de desastre. Não há suporte para o espelhamento de banco de dados SQL Server para SQL Server 2008 nem SQL Server 2008 R2 em uma VM do Azure. |
| **Envio de logs** |Um servidor em execução em uma VM do Azure e o outro executado localmente para recuperação de desastres entre sites. O envio de logs depende do compartilhamento de arquivos do Windows, portanto, é necessária uma conexão VPN entre a rede virtual do Azure e a rede local.<br/>![Envio de logs](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-log-shipping.png)<br/>Para a recuperação de desastres bem-sucedida de seus bancos de dados, você também deve instalar um controlador de domínio de réplica no local de recuperação de desastre. |
| **Backup e restauração com o serviço de armazenamento de BLOBs do Azure** |Bancos de dados de produção locais com backup direto no armazenamento de BLOBs do Azure para recuperação de desastre.<br/>![Cópia de Segurança e Restauro](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-backup-restore.png)<br/>Para obter mais informações, consulte [backup e restauração para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md). |
| **Replicar e realizar failover SQL Server no Azure com Azure Site Recovery** |A produção local SQL Server replicada diretamente no armazenamento do Azure para recuperação de desastre.<br/>![Replicar usando Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-standalone-sqlserver-asr.png)<br/>Para obter mais informações, consulte [proteger SQL Server usando SQL Server recuperação de desastres e Azure site Recovery](../../../site-recovery/site-recovery-sql.md). |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Considerações importantes para SQL Server HADR no Azure
As VMs do Azure, o armazenamento e a rede têm características operacionais diferentes de uma infraestrutura de ti local e não virtualizada. Uma implementação bem-sucedida de uma solução de SQL Server HADR no Azure exige que você compreenda essas diferenças e projete sua solução para acomodá-las.

### <a name="high-availability-nodes-in-an-availability-set"></a>Nós de alta disponibilidade em um conjunto de disponibilidade
Os conjuntos de disponibilidade no Azure permitem que você coloque os nós de alta disponibilidade em domínios de falha (FDs) e domínios de atualização (UDs) separados. Cada máquina virtual em seu conjunto de disponibilidade recebe um domínio de atualização e um domínio de falha pela plataforma subjacente do Azure. Essa configuração em um datacenter garante que, durante um evento de manutenção planejada ou não planejada, pelo menos uma máquina virtual esteja disponível e atenda ao SLA de 99,95% do Azure. Para configurar a configuração de alta disponibilidade, Coloque todas as máquinas virtuais do SQL participantes no mesmo conjunto de disponibilidade para evitar a perda de aplicativos ou dados durante um evento de manutenção. Somente os nós no mesmo serviço de nuvem podem participar do mesmo conjunto de disponibilidade. Para obter mais informações, consulte o artigo [Gerir a Disponibilidade das Virtual Machines](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="high-availability-nodes-in-an-availability-zone"></a>Nós de alta disponibilidade em uma zona de disponibilidade
As zonas de disponibilidade são localizações físicas únicas dentro de uma região do Azure. Cada zona é constituída por um ou mais datacenters equipados com energia, refrigeração e redes. A separação física de Zonas de Disponibilidade em uma região protege aplicativos e dados de falhas do datacenter, garantindo que pelo menos uma máquina virtual esteja disponível e atenda a 99,99% de SLA do Azure. Para configurar a alta disponibilidade, coloque as máquinas virtuais do SQL participantes espalhadas por Zonas de Disponibilidade disponíveis na região. Haverá encargos adicionais de transferência de dados da VM para a VM na zona de disponibilidade. Para obter mais informações, consulte [zonas de disponibilidade](/azure/availability-zones/az-overview). 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Comportamento do cluster de failover na rede do Azure
O serviço DHCP não compatível com RFC no Azure pode fazer com que a criação de determinadas configurações de cluster de failover falhe, devido ao nome da rede de cluster que está sendo atribuído um endereço IP duplicado, como o mesmo endereço IP que um dos nós do cluster. Esse é um problema quando você implementa grupos de disponibilidade, o que depende do recurso de cluster de failover do Windows.

Considere o cenário quando um cluster de dois nós é criado e colocado online:

1. O cluster fica online e, em seguida, o NODE1 solicita um endereço IP atribuído dinamicamente para o nome de rede do cluster.
2. Nenhum endereço IP diferente do Node1 próprio endereço IP é fornecido pelo serviço DHCP, pois o serviço DHCP reconhece que a solicitação vem do NODE1 em si.
3. O Windows detecta que um endereço duplicado é atribuído a NODE1 e ao nome de rede do cluster de failover, e o grupo de clusters padrão não é colocado online.
4. O grupo de clusters padrão é movido para NODE2, que trata o endereço IP do Node1 como o endereço IP do cluster e coloca o grupo de clusters padrão online.
5. Quando o NODE2 tenta estabelecer a conectividade com o NODE1, os pacotes direcionados a NODE1 nunca deixam o NODE2 porque resolve o endereço IP do Node1 para ele mesmo. NODE2 não pode estabelecer conectividade com NODE1, então perde quorum e desliga o cluster.
6. Enquanto isso, o NODE1 pode enviar pacotes para NODE2, mas o NODE2 não pode responder. NODE1 perde quorum e desliga o cluster.

Esse cenário pode ser evitado atribuindo um endereço IP estático não usado, como um endereço IP de link local como 169.254.1.1, ao nome de rede do cluster para colocar o nome da rede de cluster online. Para simplificar esse processo, consulte [Configurando o cluster de failover do Windows no Azure para grupos de disponibilidade](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Para obter mais informações, consulte [configurar grupos de disponibilidade no Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Suporte ao ouvinte do grupo de disponibilidade
Os ouvintes do grupo de disponibilidade têm suporte em VMs do Azure que executam o Windows Server 2008 R2, o Windows Server 2012, o Windows Server 2012 R2 e o Windows Server 2016. Esse suporte é possibilitado pelo uso de pontos de extremidade com balanceamento de carga habilitados nas VMs do Azure que são nós de grupo de disponibilidade. Você deve seguir as etapas de configuração especiais para que os ouvintes funcionem para os aplicativos cliente que estão em execução no Azure, bem como aqueles em execução no local.

Há duas opções principais para configurar seu ouvinte: externo (público) ou interno. O ouvinte externo (público) usa um balanceador de carga voltado para a Internet e está associado a um IP virtual público (VIP) que pode ser acessado pela Internet. Um ouvinte interno usa um balanceador de carga interno e só dá suporte a clientes dentro da mesma rede virtual. Para o tipo de balanceador de carga, você deve habilitar o retorno de servidor direto. 

Se o grupo de disponibilidade abranger várias sub-redes do Azure (como uma implantação que atravessa regiões do Azure), a cadeia de conexão do cliente deverá incluir "**MultisubnetFailover = true**". Isso resulta em tentativas de conexão paralelas para as réplicas em sub-redes diferentes. Para obter instruções sobre como configurar um ouvinte, consulte

* [Configurar um ouvinte de ILB para grupos de disponibilidade no Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
* [Configure um ouvinte externo para grupos de disponibilidade no Azure](../sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md).

Você ainda pode se conectar a cada réplica de disponibilidade separadamente conectando-se diretamente à instância do serviço. Além disso, como os grupos de disponibilidade são compatíveis com os clientes de espelhamento de banco de dados, você pode se conectar às réplicas de disponibilidade, como os parceiros de espelhamento de banco de dados, desde que as réplicas sejam configuradas de forma semelhante ao espelhamento

* Uma réplica primária e uma réplica secundária
* A réplica secundária está configurada como não legível (opção**secundária legível** definida como **não**)

Uma cadeia de conexão de cliente de exemplo que corresponde a essa configuração de espelhamento de banco de dados usando ADO.NET ou SQL Server Native Client está abaixo:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Para obter mais informações sobre a conectividade do cliente, consulte:

* [Usando palavras-chave de cadeia de conexão com SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
* [Conectar clientes a uma sessão de espelhamento de banco de dados (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
* [Conectando-se ao ouvinte do grupo de disponibilidade em ti híbrida](https://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Ouvintes de grupo de disponibilidade, conectividade de cliente e failover de aplicativo (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Usando cadeias de conexão de espelhamento de banco de dados com grupos de disponibilidade](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Latência de rede em ti híbrida
Você deve implantar sua solução HADR com a suposição de que pode haver períodos de tempo com alta latência de rede entre sua rede local e o Azure. Ao implantar réplicas no Azure, você deve usar a confirmação assíncrona em vez da confirmação síncrona para o modo de sincronização. Ao implantar servidores de espelhamento de banco de dados localmente e no Azure, use o modo de alto desempenho em vez do modo de alta segurança.

### <a name="geo-replication-support"></a>Suporte à replicação geográfica
A replicação geográfica nos discos do Azure não dá suporte ao arquivo de dados e ao arquivo de log do mesmo banco de dado a ser armazenado em discos separados. O GRS Replica as alterações em cada disco de forma independente e assíncrona. Esse mecanismo garante a ordem de gravação dentro de um único disco na cópia replicada geograficamente, mas não entre cópias replicadas geograficamente de vários discos. Se você configurar um banco de dados para armazenar seu arquivo e seu arquivo de log em discos separados, os discos recuperados após um desastre poderão conter uma cópia mais atualizada do arquivo de dados do que o arquivo de log, o que interrompe o log write-ahead em SQL Server e as propriedades ACID de transações. Se você não tiver a opção de desabilitar a replicação geográfica na conta de armazenamento, deverá manter todos os arquivos de log e de dados de um determinado banco de dado no mesmo disco. Se for necessário usar mais de um disco devido ao tamanho do banco de dados, você precisará implantar uma das soluções de recuperação de desastre listadas acima para garantir a redundância do dado.

## <a name="next-steps"></a>Passos seguintes
Se você precisar criar uma máquina virtual do Azure com SQL Server, consulte [Provisionando uma máquina virtual SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

Para obter o melhor desempenho de SQL Server em execução em uma VM do Azure, consulte as diretrizes em [práticas recomendadas de desempenho para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md).

Para outros tópicos relacionados à execução de SQL Server em VMs do Azure, consulte [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Outros recursos
* [Instalar uma nova floresta Active Directory no Azure](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Criar cluster de failover para grupos de disponibilidade na VM do Azure](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

