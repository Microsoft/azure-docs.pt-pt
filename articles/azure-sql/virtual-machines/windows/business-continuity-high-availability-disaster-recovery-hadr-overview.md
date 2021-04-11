---
title: Alta disponibilidade, recuperação de desastres, continuidade do negócio
description: Conheça a elevada disponibilidade, recuperação de desastres (HADR) e opções de continuidade de negócios disponíveis para O SQL Server em VMs Azure, tais como grupos de disponibilidade Always On, instância de cluster de failover, espelhamento de base de dados, envio de registos e cópia de segurança & restaurar para O Azure Storage.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2020
ms.author: mathoma
ms.openlocfilehash: 31d22be5ee5480878633b9742837e3f5d6119043
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078949"
---
# <a name="business-continuity-and-hadr-for-sql-server-on-azure-virtual-machines"></a>Continuidade de negócios e HADR para SQL Server em Máquinas Virtuais Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Continuidade do negócio significa continuar o seu negócio em caso de desastre, planejando para a recuperação, e garantindo que os seus dados estão altamente disponíveis. O SQL Server em Azure Virtual Machines pode ajudar a reduzir o custo de uma solução de base de dados de alta disponibilidade e recuperação de desastres (HADR). 

A maioria das soluções HADR do Servidor SQL são suportadas em máquinas virtuais (VMs), como soluções apenas azure e híbridas. Numa solução só para aZure, todo o sistema HADR funciona em Azure. Numa configuração híbrida, parte da solução funciona em Azure e a outra parte funciona no local da sua organização. A flexibilidade do ambiente Azure permite-lhe deslocar-se parcial ou completamente para Azure para satisfazer os requisitos de orçamento e HADR dos seus sistemas de base de dados SQL Server.

Este artigo compara e contrasta as soluções de continuidade de negócio disponíveis para o SQL Server em VMs Azure. 

## <a name="overview"></a>Descrição Geral

Cabe-lhe a si assegurar que o seu sistema de base de dados tem as capacidades HADR que o acordo de nível de serviço (SLA) requer. O facto de o Azure fornecer mecanismos de elevada disponibilidade, como a cura de serviços para serviços na nuvem e a deteção de recuperação de falhas para máquinas virtuais, não garante, por si só, que possa cumprir o SLA. Embora estes mecanismos ajudem a proteger a alta disponibilidade da máquina virtual, eles não protegem a disponibilidade do SQL Server que funciona dentro do VM. 

É possível que a instância do SQL Server falhe enquanto o VM está online e saudável. Mesmo os mecanismos de alta disponibilidade fornecidos pelo Azure permitem o tempo de inatividade dos VMs devido a eventos como a recuperação de falhas de software ou hardware e atualizações do sistema operativo.

O armazenamento geo-redundante (GRS) em Azure é implementado com uma característica chamada geo-replicação. GRS pode não ser uma solução adequada de recuperação de desastres para as suas bases de dados. Como a geo-replicação envia dados assíncronosamente, atualizações recentes podem ser perdidas num desastre. Mais informações sobre limitações de geo-replicação são abrangidas pela secção [de suporte à geo-replicação.](#geo-replication-support)

## <a name="deployment-architectures"></a>Arquiteturas de implantação
O Azure suporta estas tecnologias sql server para a continuidade do negócio:

* [Sempre em grupos de disponibilidade](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
* [Sempre em casos de cluster failover (FCIs)](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
* [Envio de registo](/sql/database-engine/log-shipping/about-log-shipping-sql-server)
* [Backup do SQL Server e restauro com armazenamento Azure Blob](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
* [Espelhamento da base de dados](/sql/database-engine/database-mirroring/database-mirroring-sql-server) - Deprecado no SQL Server 2016

Pode combinar as tecnologias para implementar uma solução SQL Server que tenha capacidades de alta disponibilidade e recuperação de desastres. Dependendo da tecnologia que utiliza, uma implantação híbrida pode requerer um túnel VPN com a rede virtual Azure. As seguintes secções mostram-lhe alguns exemplos de arquiteturas de implantação.

## <a name="azure-only-high-availability-solutions"></a>Apenas: Soluções de alta disponibilidade

Pode ter uma solução de alta disponibilidade para o SQL Server a nível de base de dados com grupos de disponibilidade Always On. Também pode criar uma solução de alta disponibilidade a nível de instância com instâncias de cluster de sempre em falha. Para uma proteção adicional, pode criar redundância em ambos os níveis, criando grupos de disponibilidade em casos de cluster de failover. 

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de disponibilidade** |As réplicas de disponibilidade em VMs Azure na mesma região proporcionam alta disponibilidade. É necessário configurar um controlador de domínio VM, porque o clusteramento de falha do Windows requer um domínio ative directory.<br/><br/> Para maior redundância e disponibilidade, os VMs Azure podem ser implantados em [diferentes zonas de disponibilidade,](../../../availability-zones/az-overview.md) conforme documentado na visão geral do [grupo de disponibilidade.](availability-group-overview.md) Se os VMs do SqL Server num grupo de disponibilidade forem implantados em zonas de disponibilidade, utilize o [Azure Standard Load Balancer](../../../load-balancer/load-balancer-overview.md) para o ouvinte, conforme documentado nos artigos de modeloS [Azure SQL VM CLI](./availability-group-az-commandline-configure.md) e [Azure Quickstart.](availability-group-quickstart-template-configure.md)<br/> ![Diagrama que mostra o "Controlador de Domínio" acima do "Cluster WSFC" feito da "Réplica Primária", "Réplica Secundária" e "Testemunha de Partilha de Ficheiros".](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-ha-always-on.png)<br/>Para obter mais informações, consulte [os grupos de disponibilidade configure em Azure (GUI)](./availability-group-quickstart-template-configure.md). |
| **Casos de agrupamento de falhas** |As instâncias de cluster de failover são suportadas em VMs do servidor SQL. Como a funcionalidade FCI requer armazenamento partilhado, cinco soluções funcionarão com o SQL Server em VMs Azure: <br/><br/> - Utilização de [discos partilhados Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) para Windows Server 2019. Os discos geridos partilhados são um produto Azure que permite anexar um disco gerido a várias máquinas virtuais simultaneamente. Os VMs no cluster podem ler ou escrever no seu disco anexo com base na reserva escolhida pela aplicação agrupada através de Reservas Persistentes SCSI (SCSI PR). O SCSI PR é uma solução de armazenamento padrão da indústria que é usada por aplicações que são executando em uma rede de área de armazenamento (SAN) no local. Ativar o SCSI PR num disco gerido permite-lhe migrar estas aplicações para Azure como está. <br/><br/>- Utilizar [espaços de armazenamento Direct \( S2D \) ](failover-cluster-instance-storage-spaces-direct-manually-configure.md) para fornecer um SAN virtual baseado em software para o Windows Server 2016 e mais tarde.<br/><br/>- Utilizar uma [partilha de ficheiros Premium](failover-cluster-instance-premium-file-share-manually-configure.md) para o Windows Server 2012 e posteriormente. As ações de ficheiros premium são apoiadas por SSD, têm uma latência consistentemente baixa, e são totalmente suportadas para uso com a FCI.<br/><br/>- Utilização de armazenamento suportado por uma solução parceira para o agrupamento. Para um exemplo específico que utiliza o SIOS DataKeeper, consulte o [clustering de failover de](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/)entrada de blog e sios dataKeeper .<br/><br/>- Utilização de armazenamento partilhado para um alvo remoto iSCSI via Azure ExpressRoute. Por exemplo, o NetApp Private Storage (NPS) expõe um alvo iSCSI via ExpressRoute com Equinix a Azure VMs.<br/><br/>Para soluções de armazenamento partilhado e replicação de dados dos parceiros da Microsoft, contacte o fornecedor para quaisquer problemas relacionados com o acesso a dados sobre falha.<br/><br/>|

## <a name="azure-only-disaster-recovery-solutions"></a>Azure apenas: Soluções de recuperação de desastres
Pode ter uma solução de recuperação de desastres para as bases de dados do SQL Server em Azure, utilizando grupos de disponibilidade, espelhamento de base de dados ou cópia de segurança e restauro com bolhas de armazenamento.

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de disponibilidade** |Réplicas de disponibilidade que atravessam vários centros de dados em VMs Azure para recuperação de desastres. Esta solução transversal ajuda a proteger contra uma paragem total do local. <br/> ![Diagrama que mostra duas regiões com uma "Réplica Primária" e "Réplica Secundária" ligadas por um "Compromisso Assíncrona".](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-alwayson.png)<br/>Dentro de uma região, todas as réplicas devem estar dentro do mesmo serviço em nuvem e da mesma rede virtual. Como cada região terá uma rede virtual separada, estas soluções requerem conectividade rede-a-rede. Para obter mais informações, consulte [configurar uma ligação rede-a-rede utilizando o portal Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Para obter instruções detalhadas, consulte [configurar um sql servidor sempre em grupo de disponibilidade em diferentes regiões do Azure](availability-group-manually-configure-multiple-regions.md).|
| **Espelhamento da base de dados** |Diretor e espelho e servidores em execução em diferentes datacenters para recuperação de desastres. Deve implantá-los utilizando certificados de servidor. O espelhamento da base de dados do SQL Server não é suportado para SQL Server 2008 ou SQL Server 2008 R2 num Azure VM. <br/>![Diagrama que mostra o "Principal" numa região ligada ao "Espelho de outra região com "Alto Desempenho".](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-dbmirroring.png) |
| **Backup e restauro com armazenamento Azure Blob** |Bases de dados de produção apoiadas diretamente no armazenamento blob num centro de dados diferente para a recuperação de desastres.<br/>![Diagrama que mostra uma "Base de Dados" numa região que apoia o "Blob Storage" noutra região.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-backup-restore.png)<br/>Para obter mais informações, consulte [backup e restaure para O SQL Server em VMs Azure](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **Replicar e falhar sobre o SQL Server para Azure com a recuperação do site Azure** |Produção SQL Server exemplo em um datacenter Azure replicado diretamente para Azure Storage em um centro de dados Azure diferente para recuperação de desastres.<br/>![Diagrama que mostra uma "Base de dados" num datacenter Azure usando "Replicação ASR" para recuperação de desastres noutro datacenter. ](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-standalone-sqlserver-asr.png)<br/>Para obter mais informações, consulte [Protect SQL Server utilizando a recuperação de desastres do SQL Server e a recuperação do site Azure](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hybrid IT: Soluções de recuperação de desastres
Pode ter uma solução de recuperação de desastres para as bases de dados do SQL Server num ambiente de TI híbrido, utilizando grupos de disponibilidade, espelhamento de base de dados, envio de registos e backup e restauro com armazenamento Azure Blob.

| Tecnologia | Exemplo Arquiteturas |
| --- | --- |
| **Grupos de disponibilidade** |Algumas réplicas de disponibilidade em VMs Azure e outras réplicas que correm no local para recuperação de desastres no local. O local de produção pode ser no local ou num datacenter Azure.<br/>![Grupos de disponibilidade](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-alwayson.png)<br/>Como todas as réplicas de disponibilidade devem estar no mesmo cluster de failover, o cluster deve abranger ambas as redes (um cluster de failover multi-sub-rede). Esta configuração requer uma ligação VPN entre a Azure e a rede no local.<br/><br/>Para uma recuperação bem sucedida de desastres nas suas bases de dados, também deve instalar um controlador de domínio de réplica no local de recuperação de desastres.|
| **Espelhamento da base de dados** |Um parceiro que corre num Azure VM e o outro a correr no local para recuperação de desastres no local através da utilização de certificados de servidor. Os parceiros não precisam de estar no mesmo domínio do Ative Directory, e não é necessária nenhuma ligação VPN.<br/>![Espelhamento da base de dados](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-dbmirroring.png)<br/>Outro cenário de espelhamento da base de dados envolve um parceiro que funciona num VM Azure e o outro a correr no local no mesmo domínio do Ative Directory para a recuperação de desastres trans-locais. É necessária uma [ligação VPN entre a rede virtual Azure e a rede no local.](../../../vpn-gateway/tutorial-site-to-site-portal.md)<br/><br/>Para uma recuperação bem sucedida de desastres nas suas bases de dados, também deve instalar um controlador de domínio de réplica no local de recuperação de desastres. O espelhamento da base de dados do SQL Server não é suportado para SQL Server 2008 ou SQL Server 2008 R2 num Azure VM. |
| **Envio de registo** |Um servidor a funcionar num VM Azure e o outro a correr no local para recuperação de desastres no local. O envio de registos depende da partilha de ficheiros Windows, pelo que é necessária uma ligação VPN entre a rede virtual Azure e a rede no local.<br/>![Envio de registos](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-log-shipping.png)<br/>Para uma recuperação bem sucedida de desastres nas suas bases de dados, também deve instalar um controlador de domínio de réplica no local de recuperação de desastres. |
| **Backup e restauro com armazenamento Azure Blob** |Bases de dados de produção no local apoiadas diretamente no armazenamento da Azure Blob para recuperação de desastres.<br/>![Backup e restauro](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-backup-restore.png)<br/>Para obter mais informações, consulte [backup e restaure para o SQL Server em Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **Replicar e falhar sobre o SQL Server para Azure com a recuperação do site Azure** |No local produção sql server instância replicada diretamente para Azure Storage para recuperação de desastres.<br/>![Replicar usando a recuperação do local de Azure](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-standalone-sqlserver-asr.png)<br/>Para obter mais informações, consulte [Protect SQL Server utilizando a recuperação de desastres do SQL Server e a recuperação do site Azure](../../../site-recovery/site-recovery-sql.md). |


## <a name="free-dr-replica-in-azure"></a>Réplica GRATUITA DR em Azure

Se tiver [Garantia de Software,](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)pode implementar planos de recuperação de desastres híbridos (DR) com o SQL Server sem incorrer em custos adicionais de licenciamento para a instância passiva de recuperação de desastres.

Por exemplo, pode ter dois secundários passivos gratuitos quando as três réplicas estão hospedadas em Azure: 

![Duas passivas livres quando tudo em Azure](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/failover-with-primary-in-azure.png)

Ou pode configurar um ambiente híbrido de failover, com um primário licenciado no local, um passivo gratuito para HA, um passivo gratuito para DR no local, e um passivo gratuito para DR em Azure:

![Três passivas livres quando o ambiente é híbrido com uma réplica primária no local](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-with-primary-on-prem.png)

Para mais informações, consulte as [condições de licenciamento](https://www.microsoft.com/licensing/product-licensing/products)do produto. 

Para ativar este benefício, aceda ao [recurso de máquina virtual SQL Server](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource). Selecione **Configurar** em **Definições** e, em seguida, escolha a opção **de recuperação de desastres** sob **licença de servidor SQL**. Selecione a caixa de verificação para confirmar que este SQL Server VM será usado como uma réplica passiva e, em seguida, selecione **Aplicar** para guardar as suas definições. 

![Configurar uma réplica de recuperação de desastres em Azure](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Considerações importantes para o SQL Server HADR em Azure
Os VMs, armazenamento e networking azure têm características operacionais diferentes de uma infraestrutura de TI não virtualizada no local. Uma implementação bem sucedida de uma solução HADR SQL Server em Azure requer que compreenda estas diferenças e desenhe a sua solução para as acomodar.

### <a name="high-availability-nodes-in-an-availability-set"></a>Nódes de alta disponibilidade num conjunto de disponibilidade
Os conjuntos de disponibilidade em Azure permitem-lhe colocar os nós de alta disponibilidade em domínios de avaria separados e a atualizar domínios. A plataforma Azure atribui um domínio de atualização e um domínio de avaria a cada máquina virtual no seu conjunto de disponibilidade. Esta configuração dentro de um datacenter garante que durante um evento de manutenção planeada ou não planeada, pelo menos uma máquina virtual está disponível e cumpre o Azure SLA de 99,95 por cento. 

Para configurar uma configuração de alta disponibilidade, coloque todas as máquinas virtuais do SQL Server participantes no mesmo conjunto de disponibilidade para evitar a aplicação ou perda de dados durante um evento de manutenção. Apenas os nós no mesmo serviço de nuvem podem participar no mesmo conjunto de disponibilidade. Para obter mais informações, veja [Gerir a disponibilidade das máquinas virtuais](../../../virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="high-availability-nodes-in-an-availability-zone"></a>Nódes de alta disponibilidade numa zona de disponibilidade
As zonas de disponibilidade são localizações físicas únicas dentro de uma região de Azure. Cada zona é constituída por um ou mais datacenters equipados com potência, arrefecimento e networking independentes. A separação física das zonas de disponibilidade dentro de uma região ajuda a proteger aplicações e dados de falhas do datacenter, garantindo que pelo menos uma máquina virtual está disponível e cumpre o Azure SLA de 99,99 por cento. 

Para configurar a alta disponibilidade, coloque as máquinas virtuais do SQL Server participantes espalhadas por zonas de disponibilidade na região. Haverá taxas adicionais para transferências rede-rede entre zonas de disponibilidade. Para mais informações, consulte [as zonas de disponibilidade.](../../../availability-zones/az-overview.md) 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Falha no comportamento do cluster em rede Azure
O serviço DHCP não-compatível com RFC em Azure pode fazer com que a criação de certas configurações de cluster de failover falhe. Esta falha ocorre porque o nome da rede de cluster é atribuído um endereço IP duplicado, tal como o mesmo endereço IP que um dos nós de cluster. Este é um problema quando utiliza grupos de disponibilidade, que dependem da funcionalidade de cluster failover do Windows.

Considere o cenário quando um cluster de dois sentidos é criado e trazido on-line:

1. O cluster entra on-line e, em seguida, NODE1 solicita um endereço IP atribuído dinamicamente para o nome da rede de cluster.
2. O serviço DHCP não fornece nenhum endereço IP que não seja o próprio endereço IP do NODE1, porque o serviço DHCP reconhece que o pedido vem do próprio NODE1.
3. O Windows deteta que um endereço duplicado é atribuído tanto ao NODE1 como ao nome de rede do cluster de failover, e o grupo de cluster padrão não está online.
4. O grupo de agrupamento predefinido move-se para NODE2. O NODE2 trata o endereço IP do NODE1 como o endereço IP do cluster e coloca o grupo de cluster padrão online.
5. Quando o NODE2 tenta estabelecer conectividade com o NODE1, os pacotes direcionados para o NODE1 nunca saem do NODE2 porque resolve o endereço IP do NODE1 para si próprio. O NODE2 não consegue estabelecer conectividade com o NODE1, e depois perde o quórum e desliga o cluster.
6. O NODE1 pode enviar pacotes para NODE2, mas o NODE2 não pode responder. NODE1 perde quórum e desliga o cluster.

Pode evitar este cenário atribuindo um endereço IP estático não uusado ao nome da rede de cluster, de forma a colocar o nome da rede de cluster online. Por exemplo, pode utilizar um endereço IP local de ligação como 169.254.1.1. Para simplificar este processo, consulte [o cluster de failover do Windows configurado no Azure para grupos de disponibilidade](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Para obter mais informações, consulte [os grupos de disponibilidade configure em Azure (GUI)](./availability-group-quickstart-template-configure.md).

### <a name="support-for-availability-group-listeners"></a>Apoio aos ouvintes do grupo de disponibilidade
Os ouvintes do grupo de disponibilidade são suportados em VMs Azure executando Windows Server 2012 e mais tarde. Este suporte é possível através da utilização de pontos finais equilibrados em carga, ativados nos VMs Azure que são nós de grupo de disponibilidade. Deve seguir os passos de configuração especiais para que os ouvintes trabalhem tanto para aplicações de clientes em execução em Azure como para as que estão a decorrer no local.

Existem duas opções principais para configurar o seu ouvinte: externo (público) ou interno. O ouvinte externo (público) usa um equilibrador de carga virado para a Internet e está associado a um IP virtual público que é acessível através da internet. Um ouvinte interno utiliza um equilibrador de carga interno e suporta apenas clientes dentro da mesma rede virtual. Para qualquer um dos tipos de balançador de carga, deve ativar a Devolução direta do servidor. 

Se o grupo de disponibilidade abranger várias sub-redes Azure (como uma implantação que cruza as regiões de Azure), a cadeia de ligação ao cliente deve incluir `MultisubnetFailover=True` . Isto resulta em tentativas de ligação paralela às réplicas nas diferentes sub-redes. Para obter instruções sobre a configuração de um ouvinte, consulte [configurar um ouvinte ILB para grupos de disponibilidade em Azure](availability-group-listener-powershell-configure.md).


Ainda pode ligar-se a cada réplica de disponibilidade separadamente ligando-se diretamente à instância de serviço. Além disso, como os grupos de disponibilidade são retro compatíveis com clientes espelhados de base de dados, pode ligar-se às réplicas de disponibilidade como parceiros de espelhamento de bases de dados, desde que as réplicas sejam configuradas de forma semelhante à espelhamento da base de dados:

* Há uma réplica primária e uma réplica secundária.
* A réplica secundária é configurada como não legível **(Opção Secundária legível** definida para **Nº).**

Aqui está um fio de ligação ao cliente exemplo que corresponde a esta configuração espelhada em base de dados usando ADO.NET ou SQL Server Native Client:

```console
Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;
```

Para obter mais informações sobre a conectividade do cliente, consulte:

* [Utilização de palavras-chave de cadeia de ligação com cliente nativo do servidor SQL](/sql/relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client)
* [Conecte os clientes a uma sessão de espelhamento de base de dados (SQL Server)](/sql/database-engine/database-mirroring/connect-clients-to-a-database-mirroring-session-sql-server)
* [Conectar-se ao Ouvinte do Grupo disponibilidade em IT híbrido](/archive/blogs/sqlalwayson/connecting-to-availability-group-listener-in-hybrid-it)
* [Disponibilidade De Ouvintes do Grupo, Conectividade do Cliente e Falha de Aplicação (SQL Server)](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)
* [Utilizando Database-Mirroring cadeias de conexão com grupos de disponibilidade](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)

### <a name="network-latency-in-hybrid-it"></a>Latência da rede em TI híbrido
Implemente a sua solução HADR com o pressuposto de que pode haver períodos de alta latência de rede entre a sua rede no local e a Azure. Quando estiver a implementar réplicas para o Azure, use um compromisso assíncronos em vez de comprometer-se sincronizado para o modo de sincronização. Quando estiver a implementar servidores espelhados de base de dados tanto no local como no Azure, utilize o modo de alto desempenho em vez do modo de alta segurança.

### <a name="geo-replication-support"></a>Suporte de geo-replicação
A geo-replicação nos discos Azure não suporta o ficheiro de dados e o ficheiro de registo da mesma base de dados a armazenar em discos separados. GRS replica alterações em cada disco de forma independente e assíncronea. Este mecanismo garante a ordem de escrita dentro de um único disco na cópia geo-replicada, mas não através de cópias geo-replicadas de vários discos. Se configurar uma base de dados para armazenar o seu ficheiro de dados e o seu ficheiro de registo em discos separados, os discos recuperados após uma catástrofe podem conter uma cópia mais atualizada do ficheiro de dados do que o ficheiro de registo, que quebra o registo de inscrição no SQL Server e as propriedades ACID (atomicidade, consistência, isolamento e durabilidade) das transações. 

Se não tiver a opção de desativar a geo-replicação na conta de armazenamento, guarde todos os dados e registos de uma base de dados no mesmo disco. Se tiver de utilizar mais do que um disco devido ao tamanho da base de dados, utilize uma das soluções de recuperação de desastres listadas anteriormente para garantir a redundância de dados.

## <a name="next-steps"></a>Passos seguintes

Decida se um [grupo de disponibilidade](availability-group-overview.md) ou uma instância de cluster de [failover](failover-cluster-instance-overview.md) é a melhor solução de continuidade de negócio para o seu negócio. Em seguida, reveja as [melhores práticas](hadr-cluster-best-practices.md) para configurar o seu ambiente para uma elevada disponibilidade e recuperação de desastres.
