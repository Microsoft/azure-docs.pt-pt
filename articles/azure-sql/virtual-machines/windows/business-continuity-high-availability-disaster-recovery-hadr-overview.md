---
title: Alta disponibilidade e recuperação de desastres para o SQL Server Microsoft Docs
description: Uma discussão sobre os vários tipos de estratégias HADR para o SQL Server em execução em máquinas virtuais Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: mikeray
ms.openlocfilehash: 43b218e1b86dd17a8f46cc010c8bddaf92694a00
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669227"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-on-azure-virtual-machines"></a>Alta disponibilidade e recuperação de desastres para o SQL Server em Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

O Microsoft SQL Server em Azure Virtual Machines pode ajudar a reduzir o custo de uma solução de base de dados de alta disponibilidade e recuperação de desastres (HADR). A maioria das soluções HADR do Servidor SQL são suportadas em máquinas virtuais (VMs), tanto como soluções híbridas e azure. Numa solução só para aZure, todo o sistema HADR funciona em Azure. Numa configuração híbrida, parte da solução funciona em Azure e a outra parte funciona no local da sua organização. A flexibilidade do ambiente Azure permite-lhe deslocar-se parcial ou completamente para Azure para satisfazer os requisitos de orçamento e HADR dos seus sistemas de base de dados SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>Compreender a necessidade de uma solução HADR
Cabe-lhe a si assegurar que o seu sistema de base de dados possui as capacidades HADR que o acordo de nível de serviço (SLA) requer. O facto de o Azure fornecer mecanismos de elevada disponibilidade, como a cura de serviços para serviços na nuvem e a deteção de recuperação de falhas para máquinas virtuais, não garante por si só que pode cumprir o SLA desejado. Estes mecanismos protegem a elevada disponibilidade dos VMs, mas não a elevada disponibilidade do SQL Server que corre dentro dos VMs. É possível que a instância do SQL Server falhe enquanto o VM está online e saudável. Além disso, mesmo os mecanismos de alta disponibilidade fornecidos pelo Azure permitem o tempo de inatividade dos VMs devido a eventos como a recuperação de falhas de software ou hardware e atualizações do sistema operativo.

Além disso, o armazenamento geo-redundante (GRS) em Azure, que é implementado com uma funcionalidade chamada geo-replicação, pode não ser uma solução adequada de recuperação de desastres para as suas bases de dados. Como a geo-replicação envia dados assíncronosamente, as recentes atualizações podem ser perdidas em caso de desastre. Mais informações sobre limitações de geo-replicação estão cobertas na [Geo-replicação não suportada para dados e ficheiros de registo na secção de discos separados.](#geo-replication-support)

## <a name="hadr-deployment-architectures"></a>Arquiteturas de implantação HADR
As tecnologias HADR do servidor SQL que são suportadas no Azure incluem:

* [Sempre em Grupos de Disponibilidade](https://technet.microsoft.com/library/hh510230.aspx)
* [Sempre em instâncias de cluster de failover](https://technet.microsoft.com/library/ms189134.aspx)
* [Envio de Registos](https://technet.microsoft.com/library/ms187103.aspx)
* [Backup e Restauro do servidor SQL com o serviço de armazenamento Azure Blob](https://msdn.microsoft.com/library/jj919148.aspx)
* [Base de dados Mirroring](https://technet.microsoft.com/library/ms189852.aspx) - Deprecada no SQL Server 2016

É possível combinar as tecnologias em conjunto para implementar uma solução SQL Server que tenha capacidades de alta disponibilidade e recuperação de desastres. Dependendo da tecnologia que utiliza, uma implantação híbrida pode necessitar de um túnel VPN com a rede virtual Azure. As secções abaixo mostram-lhe algumas das arquiteturas de implantação de exemplo.

## <a name="azure-only-high-availability-solutions"></a>Azure-only: Soluções de alta disponibilidade

Pode ter uma solução de alta disponibilidade para o SQL Server a nível de base de dados com grupos sempre em disponibilidade - chamados grupos de disponibilidade. Também pode criar uma solução de alta disponibilidade a nível de instância com Instâncias de Cluster Always On Failover - instâncias de cluster de falha. Para redundância adicional, pode criar redundância em ambos os níveis, criando grupos de disponibilidade em casos de cluster failover. 

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de disponibilidade** |As réplicas de disponibilidade em VMs Azure na mesma região proporcionam alta disponibilidade. É necessário configurar um controlador de domínio VM, porque o clusteramento de falha do Windows requer um domínio ative directory.<br/><br/> Para maior redundância e disponibilidade, os VMs Azure podem ser implantados em [diferentes zonas de disponibilidade,](../../../availability-zones/az-overview.md) conforme documentado na visão geral do [grupo de disponibilidade.](availability-group-overview.md) Se os VMs do SqL Server num grupo de disponibilidade forem implantados em zonas de disponibilidade, utilize o [balanceador de carga padrão](../../../load-balancer/load-balancer-standard-overview.md) para o ouvinte, conforme documentado nestes artigos - [modelos de máquina virtual Azure SQL CLI](availability-group-az-cli-configure.md)  &  [Azure Quickstart](availability-group-quickstart-template-configure.md).<br/> ![Grupos de Disponibilidade](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-ha-always-on.png)<br/>Para obter mais informações, consulte os Grupos de Disponibilidade de [Configuração em Azure (GUI)](availability-group-azure-marketplace-template-configure.md). |
| **Casos de agrupamento de falhas** |As instâncias de cluster failover (FCI), que requerem armazenamento partilhado, podem ser criadas de 4 maneiras diferentes.<br/><br/>1. Um cluster de falha de dois nós que funciona em VMs Azure com armazenamento anexado usando [o Windows Server 2016 Storage Spaces Direct \( S2D \) ](failover-cluster-instance-storage-spaces-direct-manually-configure.md) para fornecer um SAN virtual baseado em software.<br/><br/> 2. Um cluster de falha de dois nós em execução em VMs Azure usando [a partilha de ficheiros premium](failover-cluster-instance-premium-file-share-manually-configure.md). As ações de ficheiros premium são ações de ficheiros apoiadas consistentemente por baixo prazo, apoiadas por SSD, que são totalmente suportadas para utilização com casos de cluster de failover.<br/><br/>3. Um cluster de failover de dois nós em execução em VMs Azure com armazenamento suportado por uma solução de agrupamento de terceiros. Para um exemplo específico que utilize o SIOS DataKeeper, consulte [Alta disponibilidade para uma partilha de ficheiros utilizando clustering de falha e software de terceiros SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>4. Um cluster de falha de dois nós em execução em VMs Azure com armazenamento remoto iSCSI Target partilhado por via ExpressRoute. Por exemplo, o NetApp Private Storage (NPS) expõe um alvo iSCSI via ExpressRoute com Equinix a Azure VMs.<br/><br/>Para soluções de armazenamento partilhado e replicação de dados de terceiros, deve contactar o fornecedor para quaisquer problemas relacionados com o acesso a dados sobre falha.<br/><br/>|

## <a name="azure-only-disaster-recovery-solutions"></a>Azure-only: Soluções de recuperação de desastres
Pode ter uma solução de recuperação de desastres para as bases de dados do SQL Server em Azure utilizando grupos de disponibilidade, espelhamento de base de dados ou cópia de segurança e restauro com bolhas de armazenamento.

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de disponibilidade** |Réplicas de disponibilidade que atravessam vários centros de dados em VMs Azure para recuperação de desastres. Esta solução transversal protege contra a interrupção total do local. <br/> ![Grupos de Disponibilidade](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-alwayson.png)<br/>Dentro de uma região, todas as réplicas devem estar dentro do mesmo serviço de nuvem e do mesmo VNet. Como cada região terá um VNet separado, estas soluções requerem conectividade VNet para VNet. Para obter mais informações, consulte [configurar uma ligação VNet-to-VNet utilizando o portal Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Para obter instruções detalhadas, consulte [configurar um Grupo de Disponibilidade de Servidor SQL em Máquinas Virtuais Azure em Diferentes Regiões](availability-group-manually-configure-multiple-regions.md).|
| **Espelhamento da base de dados** |Diretor e espelho e servidores em execução em diferentes datacenters para recuperação de desastres. Tem de ser implantado utilizando certificados de servidor. O espelhamento da base de dados do SQL Server não é suportado para SQL Server 2008 nem SQL Server 2008 R2 num Azure VM. <br/>![Espelhamento da Base de Dados](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-dbmirroring.png) |
| **Backup e restauro com armazenamento Azure Blob** |Bases de dados de produção apoiadas diretamente para o armazenamento de bolhas num centro de dados diferente para a recuperação de desastres.<br/>![Cópia de Segurança e Restauro](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-backup-restore.png)<br/>Para obter mais informações, consulte [Backup e Restore for SQL Server em Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **Replicar e falhar o SqL Server para Azure com a recuperação do site Azure** |Produção SQL Server de um datacenter Azure replicado diretamente para Azure Storage de diferentes datacenter Azure para recuperação de desastres.<br/>![Replicar usando a recuperação do local de Azure](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-standalone-sqlserver-asr.png)<br/>Para obter mais informações, consulte [Protect SQL Server utilizando a recuperação de desastres do SQL Server e a recuperação do site Azure](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hybrid IT: Soluções de recuperação de desastres
Pode ter uma solução de recuperação de desastres para as bases de dados do SQL Server num ambiente híbrido-IT utilizando grupos de disponibilidade, espelhamento de base de dados, envio de registos e backup e restauro com armazenamento de blogs Azure.

| Tecnologia | Exemplo Arquiteturas |
| --- | --- |
| **Grupos de disponibilidade** |Algumas réplicas de disponibilidade em VMs Azure e outras réplicas que correm no local para recuperação de desastres no local. O local de produção pode ser no local ou num datacenter Azure.<br/>![Grupos de Disponibilidade](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-alwayson.png)<br/>Como todas as réplicas de disponibilidade devem estar no mesmo cluster de failover, o cluster deve abranger ambas as redes (um cluster de failover multi-sub-rede). Esta configuração requer uma ligação VPN entre a Azure e a rede no local.<br/><br/>Para uma recuperação bem sucedida de desastres nas suas bases de dados, também deve instalar um controlador de domínio de réplica no local de recuperação de desastres.<br/><br/>É possível utilizar o Assistente de Réplica de Adicionar em SSMS para adicionar uma réplica Azure a um grupo sempre disponível existente. Para mais informações, consulte Tutorial: Estenda o seu Grupo De Disponibilidade Sempre ao Azul. |
| **Espelhamento da base de dados** |Um parceiro que corre num Azure VM e o outro a correr no local para recuperação de desastres no local usando certificados de servidor. Os parceiros não precisam de estar no mesmo domínio do Diretório Ativo, e não é necessária qualquer ligação VPN.<br/>![Espelhamento da Base de Dados](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-dbmirroring.png)<br/>Outro cenário de espelhamento da base de dados envolve um parceiro que funciona num VM Azure e o outro a correr no local no mesmo domínio do Ative Directory para a recuperação de desastres trans-locais. É necessária uma [ligação VPN entre a rede virtual Azure e a rede no local.](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br/><br/>Para uma recuperação bem sucedida de desastres nas suas bases de dados, também deve instalar um controlador de domínio de réplica no local de recuperação de desastres. O espelhamento da base de dados do SQL Server não é suportado para SQL Server 2008 nem SQL Server 2008 R2 num Azure VM. |
| **Envio de registo** |Um servidor a funcionar num VM Azure e o outro a correr no local para recuperação de desastres no local. O envio de registos depende da partilha de ficheiros Windows, pelo que é necessária uma ligação VPN entre a rede virtual Azure e a rede no local.<br/>![Envio de Registos](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-log-shipping.png)<br/>Para uma recuperação bem sucedida de desastres nas suas bases de dados, também deve instalar um controlador de domínio de réplica no local de recuperação de desastres. |
| **Backup e restauro com armazenamento Azure Blob** |Bases de dados de produção no local apoiadas diretamente no armazenamento de blob de Azure para a recuperação de desastres.<br/>![Cópia de Segurança e Restauro](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-backup-restore.png)<br/>Para obter mais informações, consulte [Backup e Restore for SQL Server em Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **Replicar e falhar sobre o SQL Server para Azure com a recuperação do site Azure** |Produção no local SQL Server replicado diretamente para Azure Storage para recuperação de desastres.<br/>![Replicar usando a recuperação do local de Azure](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-standalone-sqlserver-asr.png)<br/>Para obter mais informações, consulte [Protect SQL Server utilizando a recuperação de desastres do SQL Server e a recuperação do site Azure](../../../site-recovery/site-recovery-sql.md). |


## <a name="free-dr-replica-in-azure"></a>Réplica GRATUITA DR em Azure

Se tiver [Garantia de Software,](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)pode implementar planos de recuperação de desastres híbridos (DR) com o SQL Server sem incorrer em custos adicionais de licenciamento para a instância passiva de DR.

Na imagem abaixo, a configuração utiliza o SQL Server em execução numa máquina virtual Azure utilizando 12 núcleos como uma réplica de recuperação de desastres para uma implementação do SQL Server no local utilizando 12 núcleos. No passado, precisaria de licenciar 12 núcleos de SQL Server para as instalações e a implantação de Máquinas Virtuais Azure. O novo benefício oferece benefícios passivos de réplica em execução numa máquina virtual Azure. Agora bastaria apenas licenciar 12 núcleos de SQL Server que funcionassem no local, desde que os critérios de recuperação de desastres para a réplica passiva nas Máquinas Virtuais Azure fossem cumpridos.

![Réplica GRATUITA DR em Azure](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/free-dr-replica-azure.png)

Para mais informações, consulte os [Termos de Licenciamento do Produto.](https://www.microsoft.com/licensing/product-licensing/products) 

Para ativar este benefício, navegue para o [seu recurso de máquina virtual SQL Server](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource), selecione **Configure** nas definições e, em seguida, escolha a opção **de Recuperação de Desastres** sob **licença do servidor SQL**. Selecione a caixa de verificação para confirmar que este SQL Server VM será usado como uma réplica passiva e, em seguida, selecione **Aplicar** para guardar as suas definições. 

![Configure réplica DR em Azure](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Considerações importantes para o SQL Server HADR em Azure
Os VMs, armazenamento e networking azure têm características operacionais diferentes de uma infraestrutura de TI não virtualizada no local. Uma implementação bem sucedida de uma solução HADR SQL Server em Azure requer que compreenda estas diferenças e desenhe a sua solução para as acomodar.

### <a name="high-availability-nodes-in-an-availability-set"></a>Nódes de alta disponibilidade num conjunto de disponibilidade
Os conjuntos de disponibilidade em Azure permitem-lhe colocar os nós de alta disponibilidade em domínios de falha separados (FDs) e Domínios de atualização (UDs). A cada máquina virtual no seu conjunto de disponibilidade é atribuído um domínio de atualização e um domínio de falha pela plataforma Azure subjacente. Esta configuração dentro de um datacenter garante que durante um evento de manutenção planeada ou não planeada, pelo menos uma máquina virtual está disponível e cumpre o Azure SLA de 99,95%. Para configurar uma configuração de alta disponibilidade, coloque todas as máquinas virtuais SQL participantes no mesmo conjunto de disponibilidade para evitar a aplicação ou perda de dados durante um evento de manutenção. Apenas os nós no mesmo serviço de nuvem podem participar no mesmo conjunto de disponibilidade. Para obter mais informações, veja [Gerir a disponibilidade das máquinas virtuais](../../../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="high-availability-nodes-in-an-availability-zone"></a>Nódes de alta disponibilidade numa zona de disponibilidade
As Zonas de Disponibilidade são localizações físicas exclusivas numa região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. A separação física das Zonas de Disponibilidade dentro de uma região protege as aplicações e os dados contra falhas do datacenter, garantindo que pelo menos uma máquina virtual está disponível e atende a 99,99% do Azure SLA. Para configurar a alta disponibilidade, coloque as máquinas virtuais SQL participantes espalhadas pelas Zonas de Disponibilidade disponíveis na região. Haverá taxas adicionais de transferência de dados da Zona VM-vM da Zona De Disponibilidade. Para mais informações, consulte [as zonas de disponibilidade.](/azure/availability-zones/az-overview) 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Falha no comportamento do cluster em rede Azure
O serviço DHCP não-compatível com RFC em Azure pode fazer com que a criação de certas configurações de cluster de failover falhe, devido ao nome da rede de cluster ser atribuído um endereço IP duplicado, como o mesmo endereço IP que um dos nós de cluster. Este é um problema quando implementa Grupos de Disponibilidade, que dependem da funcionalidade de cluster failover do Windows.

Considere o cenário quando um cluster de dois sentidos é criado e trazido on-line:

1. O cluster entra on-line, em seguida, NODE1 solicita um endereço IP atribuído dinamicamente para o nome da rede de cluster.
2. Nenhum endereço IP que não seja o próprio endereço IP do NODE1 é dado pelo serviço DHCP, uma vez que o serviço DHCP reconhece que o pedido provém do próprio NODE1.
3. O Windows deteta que um endereço duplicado é atribuído tanto ao NODE1 como ao nome da rede de clusters de failover, e o grupo de cluster padrão não está online.
4. O grupo de cluster predefinido move-se para NODE2, que trata o endereço IP do NODE1 como o endereço IP do cluster e coloca o grupo de cluster padrão on-line.
5. Quando o NODE2 tenta estabelecer conectividade com o NODE1, os pacotes direcionados para o NODE1 nunca saem do NODE2 porque resolve o endereço IP do NODE1 para si próprio. O NODE2 não pode estabelecer conectividade com o NODE1, depois perde o quórum e desliga o cluster.
6. Entretanto, o NODE1 pode enviar pacotes para NODE2, mas o NODE2 não pode responder. NODE1 perde quórum e desliga o cluster.

Este cenário pode ser evitado atribuindo um endereço IP estático não uused, como um endereço IP local de ligação como 169.254.1.1, para o nome da rede de cluster, de forma a colocar o nome da rede de cluster on-line. Para simplificar este processo, consulte [o cluster de failover do Windows configurado no Azure para grupos de disponibilidade](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Para obter mais informações, consulte [os grupos de disponibilidade configure em Azure (GUI)](availability-group-azure-marketplace-template-configure.md).

### <a name="availability-group-listener-support"></a>Suporte de ouvinte de grupo de disponibilidade
Os ouvintes do grupo de disponibilidade são suportados em VMs Azure com windows server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016. Este suporte é possível através da utilização de pontos finais equilibrados em carga, ativados nos VMs Azure que são nós de grupo de disponibilidade. Deve seguir os passos especiais de configuração para que os ouvintes trabalhem tanto para aplicações de clientes que estão a decorrer em Azure como para as que estão a decorrer no local.

Existem duas opções principais para configurar o seu ouvinte: externo (público) ou interno. O ouvinte externo (público) usa um equilibrador de carga virado para a internet e está associado a um IP virtual público (VIP) que é acessível através da internet. Um ouvinte interno utiliza um equilibrador de carga interno e só suporta clientes dentro da mesma Rede Virtual. Para qualquer um dos tipos de balançador de carga, deve ativar a Devolução direta do servidor. 

Se o grupo de disponibilidade abranger várias sub-redes Azure (como uma implantação que cruza as regiões de Azure), a cadeia de ligação do cliente deve incluir "**MultisubnetFailover=True**". Isto resulta em tentativas de ligação paralela às réplicas nas diferentes sub-redes. Para obter instruções sobre a configuração de um ouvinte, consulte

* [Configure um ouvinte ILB para grupos de disponibilidade em Azure](availability-group-listener-powershell-configure.md).
* [Configure um ouvinte externo para grupos de disponibilidade em Azure](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md).

Ainda pode ligar-se a cada réplica de disponibilidade separadamente ligando-se diretamente à instância de serviço. Além disso, uma vez que os grupos de disponibilidade são retrosponderados com clientes espelhados de base de dados, pode ligar-se às réplicas de disponibilidade como parceiros de espelhamento de base de dados, desde que as réplicas sejam configuradas semelhantes ao espelhamento da base de dados:

* Uma réplica primária e uma réplica secundária
* A réplica secundária é configurada como não legível **(Opção Secundária legível** definida para **Não**)

Uma cadeia de ligação do cliente de exemplo que corresponde a esta configuração espelhada em forma de base de dados utilizando ADO.NET ou SQL Server Native Client está abaixo:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Para obter mais informações sobre a conectividade do cliente, consulte:

* [Utilização de palavras-chave de cadeia de ligação com cliente nativo do servidor SQL](https://msdn.microsoft.com/library/ms130822.aspx)
* [Conecte os clientes a uma sessão de espelhamento de base de dados (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
* [Conectar-se ao Ouvinte do Grupo disponibilidade em IT híbrido](https://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Disponibilidade De Ouvintes do Grupo, Conectividade do Cliente e Falha de Aplicação (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Utilização de cadeias de ligação de espelhamento de base de dados com grupos de disponibilidade](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Latência da rede em TI híbrido
Deverá implementar a sua solução HADR com o pressuposto de que pode haver períodos de tempo com alta latência de rede entre a sua rede no local e a Azure. Ao implementar réplicas para OZure, deve utilizar um compromisso assíncronos em vez de se comprometer sincronizado para o modo de sincronização. Ao implementar servidores espelhados de base de dados tanto no local como no Azure, utilize o modo de alto desempenho em vez do modo de alta segurança.

### <a name="geo-replication-support"></a>Suporte de geo-replicação
A geo-replicação nos discos Azure não suporta o ficheiro de dados e o ficheiro de registo da mesma base de dados a armazenar em discos separados. GRS replica alterações em cada disco de forma independente e assíncronea. Este mecanismo garante a ordem de escrita dentro de um único disco na cópia geo-replicada, mas não através de cópias geo-replicadas de vários discos. Se configurar uma base de dados para armazenar o seu ficheiro de dados e o seu ficheiro de registo em discos separados, os discos recuperados após uma catástrofe podem conter uma cópia mais atualizada do ficheiro de dados do que o ficheiro de registo, que quebra o registo de inscrição no SQL Server e as propriedades acid das transações. Se não tiver a opção de desativar a geo-replicação na conta de armazenamento, deve guardar todos os dados e registar ficheiros para uma determinada base de dados no mesmo disco. Se tiver de utilizar mais do que um disco devido ao tamanho da base de dados, tem de implementar uma das soluções de recuperação de desastres listadas acima para garantir a redundância de dados.

## <a name="next-steps"></a>Próximos passos
Se necessitar de criar uma máquina virtual Azure com o SQL Server, consulte [a Provisionação de uma máquina virtual SQL Server no Azure](create-sql-vm-portal.md).

Para obter o melhor desempenho do SQL Server em execução num Azure VM, consulte a orientação em [Melhores Práticas de Desempenho para SQL Server em Máquinas Virtuais Azure](performance-guidelines-best-practices.md).

Para outros tópicos relacionados com a execução do SQL Server em VMs Azure, consulte [o SQL Server em Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md).

### <a name="other-resources"></a>Outros recursos
* [Instale uma nova floresta de Diretório Ativo em Azure](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Criar Cluster failover para grupos de disponibilidade em Azure VM](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

