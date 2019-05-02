---
title: Elevada disponibilidade e recuperação após desastre para o SQL Server | Documentos da Microsoft
description: Uma discussão sobre os vários tipos de estratégias HADR para SQL Server em execução em máquinas de virtuais do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: mikeray
ms.openlocfilehash: 8a9d4699ba625f575cdcba2a85af900a7c04843e
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924467"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Elevada disponibilidade e recuperação após desastre para SQL Server em Máquinas Virtuais do Azure

Máquinas de virtuais do Microsoft Azure (VMs) com o SQL Server pode ajudar a reduzir o custo de uma elevada disponibilidade e após desastre (HADR) da base de dados solução de recuperação. A maioria das soluções de HADR do SQL Server são suportadas em máquinas de virtuais do Azure, como apenas do Azure e soluções híbridas. Uma solução apenas do Azure, todo o sistema HADR é executado no Azure. Numa configuração híbrida, parte da solução é executada no Azure e a outro parte é executado no local na sua organização. A flexibilidade do ambiente do Azure permite-lhe mover parcial ou completamente para o Azure para satisfazer os requisitos de HADR dos seus sistemas de base de dados do SQL Server e o orçamento.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>Noções básicas sobre a necessidade de uma solução HADR
Cabe-lhe garantir que seu sistema de base de dados possui as capacidades HADR que requer que o contrato de nível de serviço (SLA). O fato de que o Azure oferece mecanismos de elevada disponibilidade, como o serviço de recuperação para serviços cloud e de deteção de recuperação de falhas para máquinas virtuais, não se garante que pode cumprir o SLA pretendido. Estes mecanismos de proteger a elevada disponibilidade das VMs, mas não a elevada disponibilidade do SQL Server em execução nas VMs. É possível para a instância do SQL Server efetuar a ativação, enquanto a VM está online e em bom estado. Além disso, até mesmo a elevada disponibilidade mecanismos fornecidos pelo Azure permitem um período de indisponibilidade das VMs devido a eventos, como a recuperação de software ou falhas de hardware e atualizações do sistema operativo.

Além disso, o armazenamento Georredundante (GRS) no Azure, o que é implementado com um recurso chamado georreplicação, não pode ser uma solução de recuperação de desastres adequado para as bases de dados. Porque os replicação geográfica envia dados de forma assíncrona, as atualizações mais recentes podem ser perdidas em caso de desastre. Obter mais informações sobre limitações de georreplicação são abordados a [georreplicação não suportada para ficheiros de registo e dados em discos separados](#geo-replication-support) secção.

## <a name="hadr-deployment-architectures"></a>Arquiteturas de implementação de HADR
Tecnologias de HADR do SQL Server que são suportadas no Azure incluem:

* [Grupos de disponibilidade Always On](https://technet.microsoft.com/library/hh510230.aspx)
* [Sempre em instâncias de Cluster de ativação pós-falha](https://technet.microsoft.com/library/ms189134.aspx)
* [Envio de log](https://technet.microsoft.com/library/ms187103.aspx)
* [Cópia de segurança do SQL Server e o restauro com o serviço de armazenamento de Blobs do Azure](https://msdn.microsoft.com/library/jj919148.aspx)
* [Espelhamento de banco de dados](https://technet.microsoft.com/library/ms189852.aspx) – preterido no SQL Server 2016

É possível combinar as tecnologias em conjunto para implementar uma solução do SQL Server que tenha elevada disponibilidade e capacidades de recuperação após desastre. Consoante a tecnologia usada por, uma implementação híbrida pode necessitar de um túnel VPN com a rede virtual do Azure. As secções abaixo mostram-lhe algumas das arquiteturas de implementação de exemplo.

## <a name="azure-only-high-availability-solutions"></a>Azure-only: Soluções de elevada disponibilidade

Pode ter uma solução de elevada disponibilidade para o SQL Server num nível de base de dados com grupos de Disponibilidade AlwaysOn - denominados grupos de disponibilidade. Também pode criar uma solução de elevada disponibilidade num nível de instância com sempre na ativação pós-falha instâncias de Cluster - ativação pós-falha instâncias de cluster. Para proporcionar uma redundância adicional, pode criar redundância em ambos os níveis através da criação de grupos de disponibilidade na ativação pós-falha instâncias de cluster. 

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de disponibilidade** |Réplicas de disponibilidade em execução em VMs do Azure na mesma região proporcionam elevada disponibilidade. Terá de configurar um controlador de domínio VM, porque o cluster de failover do Windows requer um domínio do Active Directory.<br/> ![Grupos de disponibilidade](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_always_on.gif)<br/>Para obter mais informações, consulte [configurar grupos de disponibilidade no Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Instâncias de cluster de ativação pós-falha** |Ativação pós-falha Cluster instâncias (FCI), que requerem armazenamento partilhado, podem ser criados em 3 formas diferentes.<br/><br/>1. Um cluster de ativação pós-falha de dois nós em execução em VMs do Azure com a utilização do armazenamento de acesso [Windows Server 2016 espaços de armazenamento direto \(S2D\) ](virtual-machines-windows-portal-sql-create-failover-cluster.md) para fornecer uma SAN virtual baseado em software.<br/><br/>2. Um cluster de ativação pós-falha de dois nós em execução em VMs do Azure com o armazenamento suportado por uma solução de clustering de terceiros. Para obter um exemplo específico que utiliza o SIOS DataKeeper, veja [elevada disponibilidade para uma partilha de ficheiros com o clustering de ativação pós-falha e o software de terceiros 3º o SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>3. Um cluster de ativação pós-falha de dois nós em execução em VMs do Azure com o armazenamento de blocos de destino partilhado iSCSI remoto através do ExpressRoute. Por exemplo, armazenamento privado NetApp (NPS) expõe um destino de iSCSI através do ExpressRoute com Equinix para VMs do Azure.<br/><br/>Para armazenamento partilhado de terceiros e soluções de replicação de dados, deve contactar o fornecedor para quaisquer problemas relacionados com o acesso a dados na ativação pós-falha.<br/><br/>Tenha em atenção que o uso FCI por cima de [armazenamento de ficheiros do Azure](https://azure.microsoft.com/services/storage/files/) ainda não é suportada, porque esta solução não utiliza o armazenamento Premium. Estamos a trabalhar para dar suporte a isso em breve. |

## <a name="azure-only-disaster-recovery-solutions"></a>Azure-only: Soluções de recuperação após desastre
Pode ter uma solução de recuperação após desastre para as bases de dados do SQL Server no Azure com grupos de disponibilidade, o espelhamento de banco de dados ou a cópia de segurança e restaurar com blobs de armazenamento.

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de disponibilidade** |Réplicas de disponibilidade em execução em vários datacenters em VMs do Azure para recuperação após desastre. Esta solução entre regiões protege contra indisponibilidade do site concluída. <br/> ![Grupos de disponibilidade](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_alwayson.png)<br/>Dentro de uma região, todas as réplicas devem estar no mesmo serviço cloud e a mesma VNet. Uma vez que cada região terá uma VNet separada, tais soluções exigiam VNet para a conectividade VNet. Para obter mais informações, consulte [configurar uma ligação de VNet a VNet com o portal do Azure](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Para obter instruções detalhadas, consulte [configurar um grupo de disponibilidade do SQL Server em máquinas virtuais do Azure em regiões diferentes](virtual-machines-windows-portal-sql-availability-group-dr.md).|
| **Espelhamento de banco de dados** |Principal e espelho e servidores em execução em diferentes centros de dados para recuperação após desastre. Tem de implementar a utilização de certificados de servidor. <br/>![Espelhamento de banco de dados](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_dbmirroring.gif) |
| **Cópia de segurança e restauro com o serviço de armazenamento de Blobs do Azure** |Bases de dados de produção uma cópia de segurança diretamente para o armazenamento de BLOBs num Data Center diferente para recuperação após desastre.<br/>![Cópia de Segurança e Restauro](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_backup_restore.gif)<br/>Para obter mais informações, consulte [cópia de segurança e restaurar para o SQL Server em máquinas de virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md). |
| **A replicação e ativação pós-falha de SQL Server para o Azure com o Azure Site Recovery** |SQL Server de produção de um datacenter do Azure replicado diretamente para o armazenamento do Azure do datacenter do Azure diferente para recuperação após desastre.<br/>![Replicar com o Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_standalone_sqlserver-asr.png)<br/>Para obter mais informações, consulte [proteger o SQL Server com a recuperação de desastres do SQL Server e o Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>TI híbrida: Soluções de recuperação após desastre
Pode ter uma solução de recuperação após desastre para as bases de dados do SQL Server num ambiente de TI híbrida usando grupos de disponibilidade, espelhamento de banco de dados, envio de log e cópia de segurança e restaurar com o armazenamento do blogue do Azure.

| Tecnologia | Arquiteturas de exemplo |
| --- | --- |
| **Grupos de disponibilidade** |Algumas réplicas de disponibilidade em execução em VMs do Azure e outras réplicas em execução no local para a recuperação de desastres entre sites. O site de produção pode estar no local ou num datacenter do Azure.<br/>![Grupos de disponibilidade](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_alwayson.gif)<br/>Como todas as réplicas de disponibilidade tem de estar no mesmo cluster de ativação pós-falha, o cluster tem de abranger ambas as redes (um cluster de failover com várias sub-redes). Esta configuração requer uma ligação VPN entre o Azure e a rede no local.<br/><br/>Para a recuperação de desastre com êxito das bases de dados, também deve instalar um controlador de domínio de réplica no site de recuperação após desastre.<br/><br/>É possível utilizar o Assistente para adicionar a réplica no SSMS para adicionar uma réplica do Azure a uma existente grupo de Disponibilidade AlwaysOn. Para obter mais informações, consulte o Tutorial: Expanda o seu grupo de disponibilidade Always On para o Azure. |
| **Espelhamento de banco de dados** |Um parceiro em execução na VM do Azure e o outro executado no local para a recuperação de desastres entre sites utilizando certificados de servidor. Parceiros não tem de estar no mesmo domínio do Active Directory e não é necessária nenhuma ligação de VPN.<br/>![Espelhamento de banco de dados](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_dbmirroring.gif)<br/>Base de dados de outro cenário de espelhamento envolve um parceiro em execução na VM do Azure e a outro em execução no local no mesmo domínio do Active Directory para a recuperação de desastres entre sites. R [ligação VPN entre a rede virtual do Azure e a rede no local](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) é necessária.<br/><br/>Para a recuperação de desastre com êxito das bases de dados, também deve instalar um controlador de domínio de réplica no site de recuperação após desastre. |
| **Envio de log** |Um servidor em execução na VM do Azure e o outro executado no local para a recuperação de desastres entre sites. O envio de log depende partilha de ficheiros Windows, pelo que é necessária uma ligação VPN entre a rede virtual do Azure e a rede no local.<br/>![Envio de log](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_log_shipping.gif)<br/>Para a recuperação de desastre com êxito das bases de dados, também deve instalar um controlador de domínio de réplica no site de recuperação após desastre. |
| **Cópia de segurança e restauro com o serviço de armazenamento de Blobs do Azure** |No local bases de dados de produção uma cópia de segurança diretamente para o armazenamento de Blobs do Azure para recuperação após desastre.<br/>![Cópia de Segurança e Restauro](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_backup_restore.gif)<br/>Para obter mais informações, consulte [cópia de segurança e restaurar para o SQL Server em máquinas de virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md). |
| **A replicação e ativação pós-falha de SQL Server para o Azure com o Azure Site Recovery** |No local do SQL Server replicados diretamente para o armazenamento do Azure para recuperação após desastre de produção.<br/>![Replicar com o Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_standalone_sqlserver-asr.png)<br/>Para obter mais informações, consulte [proteger o SQL Server com a recuperação de desastres do SQL Server e o Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Considerações importantes para o SQL Server HADR no Azure
VMs do Azure, armazenamento e rede têm diferentes características operacionais que no local, infraestrutura de TI não virtualizados. Uma implementação bem-sucedida de uma solução de HADR SQL Server no Azure requer que compreende essas diferenças e estrutura a solução para acomodá-los.

### <a name="high-availability-nodes-in-an-availability-set"></a>Nós de elevada disponibilidade num conjunto de disponibilidade
Conjuntos de disponibilidade no Azure permitem-lhe colocar os nós de elevada disponibilidade em diferentes domínios de falha (FDs) e domínios de atualização (UDs). Para VMs do Azure, ser colocados no mesmo conjunto de disponibilidade, tem de implementá-los no mesmo serviço cloud. Apenas nós no mesmo serviço cloud podem participar no mesmo conjunto de disponibilidade. Para obter mais informações, consulte o artigo [Gerir a Disponibilidade das Virtual Machines](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="failover-cluster-behavior-in-azure-networking"></a>Comportamento de cluster de ativação pós-falha no sistema de rede do Azure
O serviço DHCP não conformidade RFC no Azure pode fazer com que a criação de ativação pós-falha de determinadas configurações de cluster efetuar a ativação, devido ao nome de rede de cluster que está sendo atribuído um endereço IP duplicado, por exemplo, o mesmo endereço IP como um de nós do cluster. Este é um problema ao implementar grupos de disponibilidade, que depende o recurso de cluster de ativação pós-falha do Windows.

Quando um cluster de dois nós é criado e colocado online, considere o cenário:

1. O cluster estiver online, em seguida, NODE1 solicita um endereço IP atribuído de forma dinâmica para o nome de rede de cluster.
2. Nenhum endereço IP que não seja o endereço IP do NODE1 é fornecido pelo serviço do DHCP, uma vez que o serviço DHCP reconhece que o pedido proveniente NODE1 em si.
3. Windows Deteta que um endereço duplicado é atribuído para NODE1 e para o nome de rede do cluster de ativação pós-falha e o grupo de cluster predefinido falha fique online.
4. Move o grupo de cluster predefinido para NODE2, que trata o endereço IP do NODE1 como o endereço IP do cluster e apresenta o grupo de cluster predefinido online.
5. Quando tenta estabelecer conectividade com NODE1 NODE2, pacotes direcionados em NODE1 nunca saem NODE2 porque ele resolve o endereço IP do NODE1 a próprio. NODE2 não é possível estabelecer a conectividade com Nó1, em seguida, perder o quórum e encerra o cluster.
6. Entretanto, NODE1 pode enviar pacotes para NODE2, mas não pode responder NODE2. NODE1 perder o quórum e encerra o cluster.

Este cenário pode ser evitado ao atribuir um não utilizado endereço IP estático, por exemplo, um endereço IP de vínculo local, como 169.254.1.1, como o nome de rede de cluster para poder colocar online o nome de rede do cluster. Para simplificar este processo, consulte [cluster de ativação pós-falha de configurar o Windows no Azure para grupos de disponibilidade](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Para obter mais informações, consulte [configurar grupos de disponibilidade no Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Suporte de serviço de escuta do grupo de disponibilidade
Serviços de escuta de grupo de disponibilidade são suportados em VMs do Azure a executar o Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016. Esse suporte é possibilitado pela utilização de pontos finais com balanceamento de carga ativado nas VMs do Azure que são nós de grupo de disponibilidade. Tem de seguir os passos de configuração especial para os serviços de escuta funcionar para ambas as aplicações de cliente que estão em execução no Azure, bem como os que são executados no local.

Existem duas opções principais para configurar o seu serviço de escuta: externo (público) ou interno. O serviço de escuta externo (público) utiliza um balanceador de carga de acesso à internet e está associado com um público IP Virtual (VIP) que está acessível através da internet. Um serviço de escuta interno utiliza um balanceador de carga interno e só suporta clientes dentro da mesma rede Virtual. Para um tipo de Balanceador de carga, tem de ativar a devolução direta do servidor. 

Se o grupo de disponibilidade abranger várias sub-redes do Azure (por exemplo, uma implementação que cruza regiões do Azure), a cadeia de ligação de cliente tem de incluir "**MultisubnetFailover = True**". Isso resulta nas tentativas de ligação paralela para as réplicas em diferentes sub-redes. Para obter instruções sobre como configurar um serviço de escuta de mensagens em fila, consulte

* [Configurar um serviço de escuta ILB para grupos de disponibilidade no Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
* [Configurar um serviço de escuta externo para grupos de disponibilidade no Azure](../sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md).

Pode ligar a cada réplica de disponibilidade em separado através da ligação diretamente para a instância do serviço. Além disso, uma vez que os grupos de disponibilidade são compatíveis com versões anteriores com clientes de espelhamento da base de dados, pode ligar para as réplicas de disponibilidade como parceiros de espelhamento, desde que as réplicas estão configuradas semelhante ao espelhamento do banco de dados do banco de dados:

* Uma réplica primária e uma réplica secundária
* A réplica secundária está configurada como não legível (**secundário legível** opção definida como **não**)

Uma cadeia de ligação de cliente de exemplo que corresponde a esta configuração de como o espelhamento de banco de dados com ADO.NET ou SQL Server Native Client está ilustrado abaixo:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Para obter mais informações sobre conectividade de cliente, consulte:

* [Utilizar palavras-chave de cadeia de ligação com o SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
* [Ligar a clientes para uma base de dados (SQL Server) de sessão de espelhamento](https://technet.microsoft.com/library/ms175484.aspx)
* [Ligar ao serviço de escuta do grupo de disponibilidade de TI híbridos](https://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Serviços de escuta do grupo de disponibilidade, a conectividade de cliente e o Failover de aplicativos (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Utilizar cadeias de ligação de espelhamento de banco de dados com grupos de disponibilidade](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Latência de rede em TI híbridos
Deve implantar sua solução HADR com a suposição de que pode haver períodos de tempo com latência de rede de alta entre a rede no local e o Azure. Ao implementar réplicas para o Azure, deve utilizar a consolidação assíncrona, em vez de consolidação síncrona para o modo de sincronização. Quando implementar servidores de espelhamento da base de dados tanto no local e no Azure, utilize o modo de alto desempenho, em vez do modo de alta segurança.

### <a name="geo-replication-support"></a>Suporte de georreplicação
Georreplicação nos discos do Azure não suporta o arquivo de dados e o ficheiro de registo da mesma base de dados sejam armazenados em discos separados. O GRS replica as alterações em cada disco de forma assíncrona e de forma independente. Este mecanismo garante a ordem de escrita num único disco na cópia georreplicado, mas não em georreplicado cópias de vários discos. Se configurar uma base de dados para armazenar seu arquivo de dados e seu arquivo de log em discos separados, os discos recuperados após um desastre podem conter uma cópia mais atualizada do arquivo de dados que o arquivo de log, que quebra o registo de escrita-ahead no SQL Server e as propriedades ACID de obtenção ctions. Se não tiver a opção para desativar a replicação geográfica da conta de armazenamento, deve manter todos os dados e arquivos de log para um determinado banco de dados no mesmo disco. Se tiver de utilizar mais de um disco devido ao tamanho da base de dados, terá de implementar uma das soluções de recuperação após desastre listadas acima, para garantir a redundância de dados.

## <a name="next-steps"></a>Passos Seguintes
Se precisar de criar uma máquina virtual do Azure com o SQL Server, consulte [aprovisionar uma Máquina Virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

Para obter o melhor desempenho do SQL Server em execução numa VM do Azure, consulte a documentação de orientação [melhores práticas de desempenho para o SQL Server em máquinas de virtuais do Azure](virtual-machines-windows-sql-performance.md).

Para outros tópicos relacionados ao executar o SQL Server em VMs do Azure, consulte [SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Outros recursos
* [Instalar uma nova floresta do Active Directory no Azure](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Criar o Cluster de ativação pós-falha para grupos de disponibilidade na VM do Azure](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

