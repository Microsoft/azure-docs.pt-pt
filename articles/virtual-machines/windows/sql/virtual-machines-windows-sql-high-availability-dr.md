---
title: Alta Disponibilidade e Recuperação de Desastres para o Servidor SQL [ SQL Server] Microsoft Docs
description: Uma discussão sobre os vários tipos de estratégias HADR para o Servidor SQL em execução em Máquinas Virtuais Azure.
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
ms.openlocfilehash: f58bb534728660b85f7d16910dde7a37914fd571
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249766"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Elevada disponibilidade e recuperação após desastre para SQL Server em Máquinas Virtuais do Azure

As máquinas virtuais do Microsoft Azure (VMs) com o SQL Server podem ajudar a reduzir o custo de uma solução de base de dados de alta disponibilidade e recuperação de desastres (HADR). A maioria das soluções SQL Server HADR são suportadas em máquinas virtuais Azure, tanto como soluções azure e como híbridas. Numa solução azure- only, todo o sistema HADR funciona em Azure. Numa configuração híbrida, parte da solução corre em Azure e a outra parte corre no local na sua organização. A flexibilidade do ambiente Azure permite-lhe mover-se parcial ou completamente para o Azure para satisfazer os requisitos orçamentais e HADR dos seus sistemas de base de dados SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>Compreender a necessidade de uma solução HADR
Cabe-lhe garantir que o seu sistema de base de dados possui as capacidades HADR que o acordo de nível de serviço (SLA) necessita. O facto de o Azure fornecer mecanismos de elevada disponibilidade, como a cura de serviços para serviços na nuvem e a deteção de recuperação de falhas para Máquinas Virtuais, não garante por si só que pode cumprir o SLA desejado. Estes mecanismos protegem a elevada disponibilidade dos VMs, mas não a elevada disponibilidade do SQL Server que funciona dentro dos VMs. É possível que a instância do SQL Server falhe enquanto o VM está on-line e saudável. Além disso, mesmo os mecanismos de alta disponibilidade fornecidos pelo Azure permitem o tempo de inatividade dos VMs devido a eventos como a recuperação de falhas de software ou hardware e atualizações do sistema operativo.

Além disso, o Geo Redundant Storage (GRS) em Azure, que é implementado com uma funcionalidade chamada geo-replicação, pode não ser uma solução adequada de recuperação de desastres para as suas bases de dados. Como a geo-replicação envia dados assincronicamente, atualizações recentes podem ser perdidas em caso de desastre. Mais informações sobre limitações de geo-replicação estão cobertas na [geo-replicação não suportada para dados e ficheiros](#geo-replication-support) de registo na secção de discos separados.

## <a name="hadr-deployment-architectures"></a>Arquiteturas de implantação HADR
As tecnologias SQL Server HADR que são suportadas no Azure incluem:

* [Sempre em Grupos de Disponibilidade](https://technet.microsoft.com/library/hh510230.aspx)
* [Sempre em casos de cluster failover](https://technet.microsoft.com/library/ms189134.aspx)
* [Envio de registo](https://technet.microsoft.com/library/ms187103.aspx)
* [Backup e restauro do servidor SQL com serviço de armazenamento Azure Blob](https://msdn.microsoft.com/library/jj919148.aspx)
* [Espelho de base de dados](https://technet.microsoft.com/library/ms189852.aspx) - Depreciado no SQL Server 2016

É possível combinar as tecnologias em conjunto para implementar uma solução SQL Server que tem capacidades de alta disponibilidade e recuperação de desastres. Dependendo da tecnologia que utiliza, uma implantação híbrida pode necessitar de um túnel VPN com a rede virtual Azure. As secções abaixo mostram-lhe algumas das arquiteturas de implantação de exemplos.

## <a name="azure-only-high-availability-solutions"></a>Azure-only: Soluções de alta disponibilidade

Pode ter uma solução de alta disponibilidade para o SQL Server a nível de base de dados com grupos always on availability - chamados grupos de disponibilidade. Também pode criar uma solução de alta disponibilidade a um nível de instância com casos de cluster sempre em failover - casos de cluster failover. Para redundância adicional, pode criar redundância em ambos os níveis, criando grupos de disponibilidade em casos de cluster sinuoso. 

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de disponibilidade** |As réplicas de disponibilidade em VMs Azure na mesma região proporcionam uma elevada disponibilidade.  É necessário configurar um VM controlador de domínio, porque o clusterde failover do Windows requer um domínio de Diretório Ativo.<br/><br/> Para maior redundância e disponibilidade, os VMs Azure podem ser implantados em [diferentes zonas de disponibilidade,](../../../availability-zones/az-overview.md) conforme documentado na visão geral do grupo de [disponibilidade.](virtual-machines-windows-portal-sql-availability-group-overview.md) Se os VMs do Servidor SQL num grupo de disponibilidade forem implantados em zonas de disponibilidade, utilize então o [equilíbrio de carga padrão](../../../load-balancer/load-balancer-standard-overview.md) para o ouvinte, conforme documentado nestes artigos - [Modelos Azure SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) & [Azure Quickstart](virtual-machines-windows-sql-availability-group-quickstart-template.md).<br/> ![Grupos de Disponibilidade](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-ha-always-on.png)<br/>Para mais informações, consulte [Configure Grupos de Disponibilidade em Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Casos de cluster failover** |As Instâncias de Cluster Failover (FCI), que requerem armazenamento partilhado, podem ser criadas de formas diferentes.<br/><br/>1. Um cluster de failover de dois nós em execução em VMs Azure com armazenamento anexo utilizando [o Windows Server 2016 Espaços de Armazenamento Direct \(S2D\) ](virtual-machines-windows-portal-sql-create-failover-cluster.md) para fornecer um SAN virtual baseado em software.<br/><br/> 2. Um cluster de dois nós que funciona em VMs Azure utilizando [a Premium File Share](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md). As ações de ficheiropremium são ações de ficheiros apoiadas por SSD consistentemente com baixa latência que são totalmente suportadas para utilização com a Failover Cluster Instance.<br/><br/>3. Um cluster de dois nós que funciona em VMs Azure com armazenamento suportado por uma solução de agrupamento de terceiros. Para um exemplo específico que utiliza o SIOS DataKeeper, consulte a alta disponibilidade para uma partilha de [ficheiros utilizando o clustering failover e o software de terceiros SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>4. Um cluster de dois nós falha em funcionamento em VMs Azure com armazenamento de blocos partilhados iSCSI target remoto via ExpressRoute. Por exemplo, o NetApp Private Storage (NPS) expõe um alvo iSCSI via ExpressRoute com Equinix a VMs Azure.<br/><br/>Para soluções de armazenamento partilhado e replicação de dados de terceiros, deve contactar o fornecedor para quaisquer problemas relacionados com o acesso a dados sobre a falha.<br/><br/>|

## <a name="azure-only-disaster-recovery-solutions"></a>Azure-only: Soluções de recuperação de desastres
Pode ter uma solução de recuperação de desastres para as suas bases de dados do SQL Server em Azure utilizando grupos de disponibilidade, espelhamento de bases de dados ou backup e restauro com bolhas de armazenamento.

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de Disponibilidade** |Réplicas de disponibilidade que percorrem vários centros de dados em VMs Azure para recuperação de desastres. Esta solução transversal protege contra a interrupção total do local. <br/> ![Grupos de Disponibilidade](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-alwayson.png)<br/>Dentro de uma região, todas as réplicas devem estar dentro do mesmo serviço de nuvem e do mesmo VNet. Como cada região terá um VNet separado, estas soluções requerem conectividade VNet para VNet. Para mais informações, consulte [Configure uma ligação VNet-to-VNet utilizando o portal Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Para obter instruções detalhadas, consulte Configure um Grupo de Disponibilidade de [ServidorEs SQL em Máquinas Virtuais Azure em Diferentes Regiões](virtual-machines-windows-portal-sql-availability-group-dr.md).|
| **Espelho de base de dados** |Diretor e espelho e servidores em diferentes datacenters para recuperação de desastres. Tem de ser implementado utilizando certificados de servidor. O espelhamento da base de dados do SQL Server não é suportado para o SQL Server 2008 nem para o SQL Server 2008 R2 num VM Azure. <br/>![Espelho de base de dados](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-dbmirroring.png) |
| **Backup e Restaurar com serviço de armazenamento Azure Blob** |As bases de dados de produção foram diretamente apoiadas no armazenamento de blob num centro de dados diferente para recuperação de desastres.<br/>![Cópia de Segurança e Restauro](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-backup-restore.png)<br/>Para mais informações, consulte [Backup e Restaure para O Servidor SQL em Máquinas Virtuais Azure](virtual-machines-windows-sql-backup-recovery.md). |
| **Replicar e falhar o servidor SQL para Azure com recuperação do site Azure** |Produção SQL Server de um datacenter Azure replicado diretamente para O Armazenamento Azure de diferentes datacenter Azure para recuperação de desastres.<br/>![Replicar usando a recuperação do site azure](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-standalone-sqlserver-asr.png)<br/>Para mais informações, consulte [Protect SQL Server utilizando a recuperação de desastres do Servidor SQL e a recuperação](../../../site-recovery/site-recovery-sql.md)do site Azure . |


## <a name="hybrid-it-disaster-recovery-solutions"></a>TI híbrido: Soluções de recuperação de desastres
Você pode ter uma solução de recuperação de desastres para as suas bases de dados SQL Server em um ambiente híbrido-TI usando grupos de disponibilidade, espelho de base de dados, envio de log e backup e restaurar com armazenamento de blog Azure.

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de Disponibilidade** |Algumas réplicas de disponibilidade em VMs Azure e outras réplicas que correm no local para recuperação de desastres transversais. O local de produção pode estar no local ou num centro de dados Azure.<br/>![Grupos de Disponibilidade](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-alwayson.png)<br/>Como todas as réplicas de disponibilidade devem estar no mesmo cluster de failover, o cluster deve abranger ambas as redes (um cluster de falha multi-subnet). Esta configuração requer uma ligação VPN entre o Azure e a rede no local.<br/><br/>Para uma recuperação bem sucedida das suas bases de dados, também deve instalar um controlador de domínio de réplica no local de recuperação de desastres.<br/><br/>É possível utilizar o Assistente de Réplica adicionar em SSMS para adicionar uma réplica Azure a um grupo sempre disponível existente. Para mais informações, consulte Tutorial: Estenda o seu Grupo Sempre Disponível para o Azure. |
| **Espelho de base de dados** |Um parceiro a correr num VM Azure e o outro a correr no local para a recuperação de desastres no local utilizando certificados de servidor. Os parceiros não precisam de estar no mesmo domínio de Diretório Ativo, e não é necessária nenhuma ligação VPN.<br/>![Espelho de base de dados](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-dbmirroring.png)<br/>Outro cenário de espelhamento de bases de dados envolve um parceiro a correr num VM Azure e o outro a funcionar no local no mesmo domínio de Diretório Ativo para a recuperação de desastres transversais. É necessária uma [ligação VPN entre a rede virtual Azure e a rede no local.](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br/><br/>Para uma recuperação bem sucedida das suas bases de dados, também deve instalar um controlador de domínio de réplica no local de recuperação de desastres. O espelhamento da base de dados do SQL Server não é suportado para o SQL Server 2008 nem para o SQL Server 2008 R2 num VM Azure. |
| **Envio de registo** |Um servidor a funcionar num VM Azure e o outro a correr no local para recuperação de desastres no local. O envio de registodepende da partilha de ficheiros do Windows, pelo que é necessária uma ligação VPN entre a rede virtual Azure e a rede no local.<br/>![Envio de registo](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-log-shipping.png)<br/>Para uma recuperação bem sucedida das suas bases de dados, também deve instalar um controlador de domínio de réplica no local de recuperação de desastres. |
| **Backup e Restaurar com serviço de armazenamento Azure Blob** |As bases de dados de produção no local foram diretamente apoiadas no armazenamento de blob azure para recuperação de desastres.<br/>![Cópia de Segurança e Restauro](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-backup-restore.png)<br/>Para mais informações, consulte [Backup e Restaure para O Servidor SQL em Máquinas Virtuais Azure](virtual-machines-windows-sql-backup-recovery.md). |
| **Replicar e falhar o servidor SQL para Azure com recuperação do site Azure** |Produção no local SQL Server replicada diretamente para O Armazenamento Azure para recuperação de desastres.<br/>![Replicar usando a recuperação do site azure](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-standalone-sqlserver-asr.png)<br/>Para mais informações, consulte [Protect SQL Server utilizando a recuperação de desastres do Servidor SQL e a recuperação](../../../site-recovery/site-recovery-sql.md)do site Azure . |


## <a name="free-dr-replica-in-azure"></a>Réplica DR grátis em Azure

Se tiver garantia de [software,](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)pode implementar planos híbridos de recuperação de desastres (DR) com o SQL Server sem incorrer em custos adicionais de licenciamento para a instância de DR passiva.

Na imagem abaixo, a configuração utiliza o SQL Server a funcionar numa Máquina Virtual Azure utilizando 12 núcleos como réplica de recuperação de desastres para uma implementação no SQL Server no local utilizando 12 núcleos. No passado, precisaria de licenciar 12 núcleos de SQL Server para as instalações e a implantação da Máquina Virtual Azure. O novo benefício oferece benefícios de réplica passiva em execução numa Máquina Virtual Azure. Agora precisaria de licenciar apenas 12 núcleos do SQL Server em funcionamento no local, desde que os critérios de recuperação de desastres para a réplica passiva na Máquina Virtual Azure fossem cumpridos.

![Réplica DR grátis em Azure](media/virtual-machines-windows-sql-high-availability-dr/free-dr-replica-azure.png)

Para mais informações, consulte os [Termos de Licenciamento do Produto](https://www.microsoft.com/licensing/product-licensing/products). 

Para ativar este benefício, navegue para o seu recurso de [máquina virtual Do Servidor SQL,](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource)selecione **Configurar** em definições e, em seguida, escolha a opção **de recuperação** de desastres sob **licença de servidor SQL**. Selecione a caixa de verificação para confirmar que este VM do Servidor SQL será usado como uma réplica passiva e, em seguida, selecione **Aplicar** para guardar as suas definições. 

![Configure réplica DR em Azure](media/virtual-machines-windows-sql-high-availability-dr/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Considerações importantes para O Servidor SQL HADR em Azure
Os VMs, armazenamento e networking azure têm características operacionais diferentes de uma infraestrutura de TI não virtualizada no local. Uma implementação bem-sucedida de uma solução HADR SQL Server em Azure requer que compreenda estas diferenças e desenhe a sua solução para as acomodar.

### <a name="high-availability-nodes-in-an-availability-set"></a>Nódosos de alta disponibilidade em conjunto de disponibilidade
Os conjuntos de disponibilidade em Azure permitem colocar os nós de alta disponibilidade em domínios de falhas separados (FDs) e Domínios de Atualização (UDs). A cada máquina virtual no seu conjunto de disponibilidade é atribuído um domínio de atualização e um domínio de falha pela plataforma Azure subjacente. Esta configuração dentro de um datacenter garante que durante um evento de manutenção planeado ou não planeado, pelo menos uma máquina virtual está disponível e cumpre o Azure SLA de 99,95%. Para configurar a configuração de alta disponibilidade, coloque todas as Máquinas Virtuais SQL participantes no mesmo conjunto de disponibilidade para evitar a aplicação ou perda de dados durante um evento de manutenção. Apenas os nódosos no mesmo serviço de nuvem podem participar no mesmo conjunto de disponibilidade. Para mais informações, consulte [Gerir a Disponibilidade de Máquinas Virtuais](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="high-availability-nodes-in-an-availability-zone"></a>Nódosos de alta disponibilidade em zona de disponibilidade
As Zonas de Disponibilidade são localizações físicas exclusivas numa região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. A separação física das Zonas de Disponibilidade numa região protege aplicações e dados de falhas no datacenter, garantindo que pelo menos uma máquina virtual está disponível e cumpre 99,99% Azure SLA. Para configurar a alta disponibilidade, coloque as máquinas virtuais SQL participantes espalhadas pelas Zonas de Disponibilidade disponíveis na região. Haverá taxas adicionais de transferência de dados VM-to-VM da Zona Inter-Disponibilidade. Para mais informações, consulte [zonas de disponibilidade](/azure/availability-zones/az-overview). 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Comportamento de cluster failover em rede Azure
O serviço DHCP não conforme com RFC em Azure pode fazer com que a criação de determinadas configurações de cluster failover falhe, devido ao facto de o nome da rede cluster ter sido atribuído um endereço IP duplicado, como o mesmo endereço IP que um dos nós do cluster. Este é um problema quando implementa grupos de disponibilidade, que depende da funcionalidade de cluster failover do Windows.

Considere o cenário quando um cluster de dois nós é criado e trazido on-line:

1. O cluster vem on-line, então NODE1 solicita um endereço IP dinamicamente atribuído para o nome da rede cluster.
2. Nenhum endereço IP que não seja o próprio endereço IP da NODE1 é dado pelo serviço DHCP, uma vez que o serviço DHCP reconhece que o pedido provém do próprio NODE1.
3. O Windows deteta que um endereço duplicado é atribuído tanto ao NODE1 como ao nome da rede de cluster failover, e o grupo de cluster predefinido não fica online.
4. O grupo de cluster padrão passa para NODE2, que trata o endereço IP da NODE1 como o endereço IP do cluster e coloca o grupo de cluster padrão on-line.
5. Quando o NODE2 tenta estabelecer conectividade com o NODE1, os pacotes dirigidos ao NODE1 nunca saem do NODE2 porque resolve o endereço IP da NODE1 para si próprio. Node2 não pode estabelecer conectividade com NODE1, em seguida, perde quórum e desliga o cluster.
6. Entretanto, o NODE1 pode enviar pacotes para o NODE2, mas o NODE2 não pode responder. Node1 perde quórum e desliga o cluster.

Este cenário pode ser evitado atribuindo um endereço IP estático não utilizado, como um endereço IP local de ligação como 169.254.1.1, para o nome da rede de cluster, de forma a colocar o nome da rede de cluster online. Para simplificar este processo, consulte configurar o cluster de falhas do [Windows em Azure para grupos de disponibilidade](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Para mais informações, consulte os grupos de [disponibilidade da Configure em Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Suporte de ouvintes de grupo de disponibilidade
Os ouvintes do grupo de disponibilidade são suportados em VMs Azure que executam o Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016. Este suporte é possível através da utilização de pontos finais equilibrados em carga habilitados nos VMs Azure que são nós de grupo de disponibilidade. Deve seguir passos especiais de configuração para que os ouvintes trabalhem tanto para aplicações de clientes que estão a decorrer em Azure como para as que estão a funcionar no local.

Existem duas opções principais para a configuração do seu ouvinte: externo (público) ou interno. O ouvinte externo (público) usa um equilibrador de carga virado para a Internet e está associado a um IP virtual público (VIP) que é acessível através da internet. Um ouvinte interno utiliza um equilibrador de carga interna e apenas suporta clientes dentro da mesma Rede Virtual. Para qualquer tipo de equilíbrio de carga, deve ativar a Devolução do Servidor Direto. 

Se o Grupo de Disponibilidade abranger várias subredes Azure (como uma implantação que cruza as regiões de Azure), a cadeia de ligação ao cliente deve incluir "**MultisubnetFailover=True**". Isto resulta em tentativas de ligação paralela às réplicas nas diferentes subredes. Para obter instruções sobre a configuração de um ouvinte, consulte

* [Configure um ouvinte ILB para grupos de disponibilidade em Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
* [Configure um ouvinte externo para grupos de disponibilidade em Azure](../sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md).

Ainda pode ligar-se a cada réplica de disponibilidade separadamente, ligando-se diretamente à instância de serviço. Além disso, uma vez que os grupos de disponibilidade são retrocompatíveis com clientes espelhados na base de dados, pode ligar-se às réplicas de disponibilidade, como parceiros de espelho de base de dados, desde que as réplicas estejam configuradas semelhantes às imagens de base de dados espelhadas:

* Uma réplica primária e uma réplica secundária
* A réplica secundária é configurada como não legível (opção**secundária legível** definida para **Não)**

Um fio de ligação ao cliente de exemplo que corresponde a esta configuração de espelhamento de base de dados usando ADO.NET ou SQL Server Client nativo está abaixo:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Para obter mais informações sobre a conectividade do cliente, consulte:

* [Usando palavras-chave de corda de ligação com o cliente nativo do servidor SQL](https://msdn.microsoft.com/library/ms130822.aspx)
* [Ligar clientes a uma sessão de espelhos de base de dados (Servidor SQL)](https://technet.microsoft.com/library/ms175484.aspx)
* [Ligação ao Grupo de Disponibilidade Listener em TI híbrido](https://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Disponibilidade De Ouvintes do Grupo, Conectividade do Cliente e Falha de Aplicação (Servidor SQL)](https://technet.microsoft.com/library/hh213417.aspx)
* [Utilização de cordas de ligação espelhadas na base de dados com grupos de disponibilidade](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Latência da rede em TI híbrido
Deverá implementar a sua solução HADR com o pressuposto de que pode haver períodos de tempo com elevada latência de rede entre a sua rede no local e o Azure. Ao implantar réplicas para o Azure, deve utilizar um compromisso assíncrono em vez de comprometer-se sincronizado com o modo de sincronização. Ao implementar servidores de espelhos de base de dados tanto no local como no Azure, utilize o modo de alto desempenho em vez do modo de alta segurança.

### <a name="geo-replication-support"></a>Suporte de geo-replicação
A geo-replicação nos discos Azure não suporta o ficheiro de dados e o ficheiro de registo da mesma base de dados a armazenar em discos separados. GrS replica alterações em cada disco de forma independente e assíncrona. Este mecanismo garante a ordem de escrita dentro de um único disco na cópia geo-replicada, mas não através de cópias geo-replicadas de vários discos. Se configurar uma base de dados para armazenar o seu ficheiro de dados e o seu ficheiro de registo em discos separados, os discos recuperados após um desastre podem conter uma cópia mais atualizada do ficheiro de dados do que o ficheiro de registo, que quebra o registo de escrita no SQL Server e as propriedades acidadas de transações. Se não tiver a opção de desativar a geo-replicação na conta de armazenamento, deverá manter todos os dados e ficheiros de registo para uma determinada base de dados no mesmo disco. Se tiver de utilizar mais de um disco devido ao tamanho da base de dados, terá de implementar uma das soluções de recuperação de desastres acima listadas para garantir a redundância de dados.

## <a name="next-steps"></a>Passos seguintes
Se precisar de criar uma máquina virtual Azure com o Servidor SQL, consulte [o provisionamento de uma máquina virtual do Servidor SQL no Azure](virtual-machines-windows-portal-sql-server-provision.md).

Para obter o melhor desempenho do SQL Server em execução num Azure VM, consulte a orientação em [Performance Best Practices for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

Para outros tópicos relacionados com a execução do Servidor SQL em VMs Azure, consulte [o Servidor SQL em Máquinas Virtuais Azure](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Outros recursos
* [Instale uma nova floresta de Diretório Ativo em Azure](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Criar Cluster Failover para grupos de disponibilidade em Azure VM](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

