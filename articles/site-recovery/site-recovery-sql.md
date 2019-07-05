---
title: Configurar a recuperação após desastre para SQL Server com o SQL Server e o Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve como configurar a recuperação após desastre para SQL Server usando o SQL Server e o Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: sutalasi
ms.openlocfilehash: 1c44b10b54a5f58dff1aecf36c3633cc8ffbd8f0
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491783"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Configurar a recuperação após desastre para SQL Server

Este artigo descreve como proteger o SQL Server back-end de um aplicativo usando uma combinação de continuidade de negócio do SQL Server e tecnologias de (BCDR) de recuperação após desastre, e [do Azure Site Recovery](site-recovery-overview.md).

Antes de começar, certifique-se de que compreende a capacidades de recuperação de desastres do SQL Server, incluindo o clustering de ativação pós-falha, grupos de disponibilidade Always On, espelhamento, banco de dados de registo envio, a georreplicação ativa e grupos de ativação pós-falha automática.

## <a name="dr-recommendation-for-integration-of-sql-server-bcdr-technologies-with-site-recovery"></a>Recomendação de DR para a integração de tecnologias BCDR do SQL Server com o Site Recovery

Escolha de uma tecnologia BCDR para servidores SQL de recuperação deve basear-se nas suas necessidades RTO e RPO, de acordo a tabela a seguir. Depois de estabelecida essa opção, o Site Recovery pode ser integrado com a operação de ativação pós-falha do que a tecnologia para orquestrar a recuperação de todo o seu aplicativo.

**Tipo de implementação** | **Tecnologia BCDR** | **RTO esperado para o SQL** | **RPO esperado para SQL** |
--- | --- | --- | ---
SQL Server na VM de IaaS do Azure ou no local| **[Sempre no grupo de disponibilidade](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017)** | Equivalente ao tempo levado para tornar a réplica secundária como principal | Replicação assíncrona para a réplica secundária, portanto, não há alguma perda de dados.
SQL Server na VM de IaaS do Azure ou no local| **[(Sempre em FCI) de clustering de ativação pós-falha](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017)** | Equivalente ao tempo levado para ativação pós-falha entre os nós | Ele usa o armazenamento partilhado, por conseguinte, a mesma vista de instância de armazenamento está disponível na ativação pós-falha.
SQL Server na VM de IaaS do Azure ou no local| **[Base de dados de espelhamento (modo de alto desempenho)](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017)** | Equivalente ao tempo levado para forçar o serviço, que utiliza o servidor de espelhamento como um servidor de reserva semiativo. | A replicação é assíncrona. A base de dados de espelhamento poderá lag um pouco por trás do banco de dados principal. A lacuna é normalmente pequena, howvever, pode se tornar substancial se sistema do servidor principal ou espelhamento estiver sob uma carga pesada.<br></br>O envio de log pode ser um suplemento para espelhamento da base de dados e é uma alternativa favorável para espelhamento da base de dados assíncrona
SQL como PaaS no Azure<br></br>(Conjuntos elásticos, servidores de base de dados SQL) | **Georreplicação ativa** | 30 segundos, uma vez que é acionado<br></br>Quando a ativação pós-falha é ativada para uma das bases de dados secundárias, todas as outras bases de dados secundárias são automaticamente associados para a nova principal. | RPO de cinco segundos<br></br>Replicação geográfica activa tira partido da tecnologia Always On do SQL Server para a replicação assíncrona de transações consolidadas na base de dados primária para uma base de dados secundária, usando o isolamento de instantâneo. <br></br>Os dados secundários são garantidos que nunca terá transações parciais.
SQL como PaaS configurado com a georreplicação ativa no Azure<br></br>(SQL Database Managed Instance, conjuntos elásticos, servidores de base de dados SQL) | **Grupos de ativação pós-falha automática** | RTO de 1 hora | RPO de cinco segundos<br></br>Os grupos de ativação pós-falha automática fornecem a semântica de grupo por cima de georreplicação ativa, mas o mesmo mecanismo de replicação assíncrona é utilizado.
SQL Server na VM de IaaS do Azure ou no local| **Replicação Azure Site Recovery** | Normalmente, menos de 15 minutos. [Leia mais](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) para saber mais sobre o SLA de RTO fornecido pelo Azure Site Recovery. | 1 hora para consistência de aplicação e 5 minutos para consistência de falhas. 

> [!NOTE]
> Algumas considerações importantes quando proteger cargas de trabalho do SQL com o Azure Site Recovery:
> * O Azure Site Recovery é agnóstico quanto a aplicação e, por conseguinte, qualquer versão do SQL server que é implementado num sistema operativo suportado pode ser protegido pelo Azure Site Recovery. [Saiba mais](vmware-physical-azure-support-matrix.md#replicated-machines).
> * Pode optar por utilizar o Site Recovery para qualquer implementação no Azure, Hyper-V, VMware ou infraestrutura física. Siga os [orientações](site-recovery-sql.md#how-to-protect-a-sql-server-cluster-standard-editionsql-server-2008-r2) no final do documento sobre como proteger o Cluster do SQL Server com o Azure Site Recovery.
> * Certifique-se de que os dados de taxa de alteração de (bytes de escrita por segundo) observada no computador está dentro [limites do Site Recovery](vmware-physical-azure-support-matrix.md#churn-limits). Para computadores windows, pode ver isso na guia de desempenho no Gerenciador de tarefas. Observe a escrita velocidade para cada disco.
> * O Azure Site Recovery suporta a replicação de instâncias de Cluster de ativação pós-falha nos espaços de armazenamento direto. [Saiba mais](azure-to-azure-how-to-enable-replication-s2d-vms.md).
 

## <a name="disaster-recovery-of-application"></a>Recuperação após desastre de aplicação

**O Azure Site Recovery orquestra a ativação pós-falha de teste e a ativação pós-falha da sua aplicação completa com a ajuda de planos de recuperação.** 

Há alguns pré-requisitos para garantir que o plano de recuperação é totalmente personalizado de acordo com suas necessidades. Qualquer implementação do SQL Server precisa, normalmente, um Active Directory. Ele também precisa conectividade para a camada de aplicativo.

### <a name="step-1-set-up-active-directory"></a>Passo 1: Configurar o Active Directory

Configure o Active Directory, no site secundário de recuperação, para o SQL Server a ser executado corretamente.

* **Pequenas empresas**— com um pequeno número de aplicativos e o controlador de domínio único para o site no local, se pretender efetuar a ativação pós-falha de todo o site, recomendamos que utilize replicação do Site Recovery para replicar o controlador de domínio para o secundário Centro de dados, ou para o Azure.
* **Médias a grandes empresas**— se tiver um grande número de aplicativos, uma floresta do Active Directory, e que pretende efetuar a ativação por aplicação ou carga de trabalho, recomendamos que configura um controlador de domínio adicional no Centro de dados secundário ou no Azure. Se estiver a utilizar grupos de disponibilidade Always On para recuperar para um site remoto, recomendamos que definir outro controlador de domínio adicionais no site secundário ou no Azure, para utilizar para a instância do SQL Server recuperada.

As instruções neste artigo presumem que um controlador de domínio está disponível na localização secundária. [Leia mais](site-recovery-active-directory.md) sobre como proteger o Active Directory com o Site Recovery.

### <a name="step-2-ensure-connectivity-with-other-application-tiers-and-web-tier"></a>Passo 2: Certifique-se a conectividade com outros escalões de cluster:{0}<br>tipos de aplicativo e a camada web

Certifique-se de que uma vez que a camada de base de dados está em execução na região do Azure de destino, tem conectividade com o aplicativo e a camada web. Passos necessários deverão ser executados com antecedência para validar a conectividade com a ativação pós-falha de teste.

Compreenda como é possível desenvolver aplicativos para considerações sobre a conectividade com alguns exemplos:
* [Estruturar uma aplicação para recuperação de desastre na nuvem](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [Estratégias de recuperação após desastre do conjunto elástico](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-integrate-with-always-on-active-geo-replication-or-auto-failover-groups-for-application-failover"></a>Passo 3: Integrar com o Always On, a georreplicação ativa ou grupos de ativação pós-falha automática para ativação pós-falha de aplicação

Tecnologias BCDR Always On, grupos de replicação e ativação pós-falha automática de Georreplicação ativa tem réplicas secundárias do SQL server em execução na região do Azure de destino. Portanto, o primeiro passo para o failover de aplicativos é tornar esta réplica como principal (assumindo que já tem um controlador de domínio no secundário). Este passo pode não ser necessário se optar por fazer uma ativação pós-falha automática. Apenas depois de concluída a ativação pós-falha de base de dados, tem de ativação pós-falha os escalões web ou aplicação.

> [!NOTE] 
> Se proteger as máquinas SQL com o Azure Site Recovery, basta criar um grupo de recuperação destas máquinas e adicionar sua ativação pós-falha no plano de recuperação.

[Criar um plano de recuperação](site-recovery-create-recovery-plans.md) com a aplicação e máquinas de virtuais de camada web. Siga os passos abaixo para adicionar a ativação pós-falha da camada de base de dados:

1. Importar scripts para a sua conta de automatização do Azure. Contém os scripts para ativação pós-falha o grupo de disponibilidade SQL num [Resource Manager virtual machine](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) e uma [máquinas virtuais clássicas](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1).

    [![Implementar no Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. Adicione o ASR-SQL-FailoverAG como uma ação de pré-instalação do primeiro grupo de plano de recuperação.

1. Siga as instruções disponíveis no script para criar uma variável de automatização para fornecer o nome dos grupos de disponibilidade.

### <a name="step-4-conduct-a-test-failover"></a>Passo 4: Realizar uma ativação pós-falha de teste

Algumas tecnologias BCDR, como o SQL Always On nativamente não suportam a ativação pós-falha de teste. Por conseguinte, recomendamos a seguinte abordagem **apenas quando a integração com essas tecnologias**:

1. Configurar [Azure Backup](../backup/backup-azure-arm-vms.md) na máquina virtual que aloja a réplica do grupo de disponibilidade no Azure.

1. Antes de acionar a ativação pós-falha de teste do plano de recuperação, recupere a máquina virtual a partir da cópia de segurança criada no passo anterior.

    ![Restaurar a partir de cópia de segurança do Azure](./media/site-recovery-sql/restore-from-backup.png)

1. [Forçar um quórum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) na máquina virtual restaurada a partir de cópia de segurança.

1. Atualize o IP do ouvinte para um IP disponível na rede de ativação pós-falha de teste.

    ![Atualizar o serviço de escuta de IP](./media/site-recovery-sql/update-listener-ip.png)

1. Colocar o serviço de escuta online.

    ![Colocar o serviço de escuta Online](./media/site-recovery-sql/bring-listener-online.png)

1. Crie um balanceador de carga com um que IP criado no conjunto IP de front-end correspondente a cada serviço de escuta do grupo de disponibilidade e com a máquina virtual do SQL adicionado no conjunto de back-end.

     ![Criar Balanceador de carga – conjunto IP de front-end](./media/site-recovery-sql/create-load-balancer1.png)

    ![Criar Balanceador de carga - conjunto de back-end](./media/site-recovery-sql/create-load-balancer2.png)

1. Adicione a ativação pós-falha da camada do aplicativo, seguida de camada web neste plano de recuperação em grupos de recuperação subsequentes. 
1. Fazer uma ativação pós-falha de teste do plano de recuperação para ativação pós-falha de ponta a ponta do aplicativo de teste.

## <a name="steps-to-do-a-failover"></a>Passos para fazer uma ativação pós-falha

Depois de ter adicionado o script no plano de recuperação no passo 3 e validar ao fazer uma ativação pós-falha de teste com uma abordagem especializada no passo 4, pode fazer a ativação pós-falha do plano de recuperação que criou no passo 3.

Tenha em atenção que os passos de ativação pós-falha para os escalões de web e aplicativo devem ser o mesmo na ativação pós-falha de teste e planos de recuperação de ativação pós-falha.

## <a name="how-to-protect-a-sql-server-cluster-standard-editionsql-server-2008-r2"></a>Como proteger um cluster do SQL Server (standard edition/SQL Server 2008 R2)

Para um cluster a executar o SQL Server Standard edition ou o SQL Server 2008 R2, recomendamos que usar replicação do Site Recovery para proteger o SQL Server.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure para o Azure e no local para o Azure

Recuperação de sites não fornece convidado suporte de cluster, ao replicar para uma região do Azure. SQL Server também não oferece uma solução de recuperação após desastre de baixo custo para a edição Standard. Neste cenário, recomendamos que protegerem o cluster do SQL Server para SQL Server no local primário autónomo e recuperá-la no secundário.

1. Configure uma instância do SQL Server autónomo adicionais na região principal do Azure ou no site no local.
1. Configure a instância para servir como um espelho das bases de dados que pretende proteger. Configure o espelhamento no modo de alta segurança.
1. Configurar a recuperação de Site no site primário ([Azure](azure-to-azure-tutorial-enable-replication.md), [Hyper-V](site-recovery-hyper-v-site-to-azure.md) ou [VMware VMs/servidores físicos)](site-recovery-vmware-to-azure-classic.md).
1. Utilize a replicação do Site Recovery para replicar a nova instância do SQL Server para o site secundário. Uma vez que é uma cópia de espelho de alta segurança, serão sincronizado com o cluster principal, mas vão ser replicado utilizando a replicação do Site Recovery.


![Cluster Standard](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Considerações sobre a reativação pós-falha

Para clusters do SQL Server Standard, a reativação pós-falha após uma ativação pós-falha não planeada requer uma cópia de segurança do SQL server e o restauro, a partir da instância de espelhamento para o cluster original, com reestablishment do espelho.

## <a name="frequently-asked-questions"></a>Perguntas Mais Frequentes

### <a name="how-does-sql-get-licensed-when-protected-with-azure-site-recovery"></a>Como SQL obter licenciado quando protegido com o Azure Site Recovery?
A replicação do Azure Site Recovery para o SQL Server está abrangida pelo benefício Recuperação Após Desastre do Software Assurance para todos os cenários do Azure Site Recovery (recuperação após desastre do local para o Azure ou recuperação após desastre de IaaS do Azure em várias regiões). [Ler mais](https://azure.microsoft.com/pricing/details/site-recovery/)

### <a name="will-azure-site-recovery-support-my-sql-version"></a>Azure Site Recovery irá suportar a minha versão SQL?
O Azure Site Recovery é agnóstico quanto a aplicação. Por conseguinte, qualquer versão do SQL server que é implementado num sistema operativo suportado pode ser protegido pelo Azure Site Recovery. [Saiba mais](vmware-physical-azure-support-matrix.md#replicated-machines)

## <a name="next-steps"></a>Passos Seguintes
* [Saiba mais](site-recovery-components.md) sobre a arquitetura do Site Recovery.
* Para servidores SQL no Azure, saiba mais sobre [soluções de alta disponibilidade](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions) para recuperação na região secundária do Azure.
* Para a base de dados SQL no Azure, saiba mais sobre o [continuidade de negócio](../sql-database/sql-database-business-continuity.md) e [elevada disponibilidade](../sql-database/sql-database-high-availability.md) as opções para recuperação na região secundária do Azure.
* Para máquinas no local, do SQL server [Saiba mais](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions) sobre as opções de elevada disponibilidade para a recuperação em máquinas de virtuais do Azure.
