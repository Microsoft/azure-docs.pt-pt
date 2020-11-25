---
title: Configurar a recuperação de desastres para o SQL Server com a recuperação do site Azure
description: Este artigo descreve como configurar a recuperação de desastres para o SQL Server utilizando o SQL Server e a Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: 1b02b089fea7e883bdc6c58c7a2845af12b50a37
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011953"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Configurar a recuperação de desastres para o SQL Server

Este artigo descreve como ajudar a proteger o sql Server no final de uma aplicação. Fá-lo utilizando uma combinação de tecnologias de continuidade de negócios e recuperação de desastres (BCDR) do SQL Server e [recuperação do local de azure.](site-recovery-overview.md)

Antes de começar, certifique-se de que compreende as capacidades de recuperação de desastres do SQL Server. Estas funcionalidades incluem:

* Clustering de ativação pós-falha
* Sempre em grupos de disponibilidade
* Espelhamento da base de dados
* Envio de registos
* Georreplicação ativa
* Grupos de ativação pós-falha automática

## <a name="combining-bcdr-technologies-with-site-recovery"></a>Combinando tecnologias BCDR com recuperação de local

A sua escolha de uma tecnologia BCDR para recuperar instâncias do SQL Server deve basear-se no seu objetivo de tempo de recuperação (RTO) e no objetivo do ponto de recuperação (RPO), conforme descrito na tabela seguinte. Combine a Recuperação do Site com o funcionamento de failover da sua tecnologia escolhida para orquestrar a recuperação de toda a sua aplicação.

Tipo de implantação | Tecnologia BCDR | RTO esperado para o SqL Server | RPO esperado para o SQL Server |
--- | --- | --- | ---
SQL Server em uma infraestrutura Azure como uma máquina virtual de serviço (IaaS) ou no local.| [Grupo de disponibilidade Always On](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) | O tempo que demorou a fazer a réplica secundária como primária. | Como a replicação na réplica secundária é assíncronea, há alguma perda de dados.
SQL Server em um Azure IaaS VM ou no local.| [Agrupamento de failover (Sempre na FCI)](/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017) | O tempo que demorou a falhar entre os nós. | Porque Always On FCI utiliza armazenamento partilhado, a mesma visão da instância de armazenamento está disponível no failover.
SQL Server em um Azure IaaS VM ou no local.| [Espelhamento da base de dados (modo de alto desempenho)](/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017) | O tempo necessário para forçar o serviço, que usa o servidor de espelhos como um servidor de espera quente. | A replicação é assíncronea. A base de dados de espelhos pode ficar um pouco atrás da base de dados principal. O lag é tipicamente pequeno. Mas pode tornar-se grande se o sistema do servidor principal ou de espelho estiver sob uma carga pesada.<br/><br/>O envio de registos pode ser um suplemento para o espelhamento da base de dados. É uma alternativa favorável ao espelho da base de dados assíncronos.
SQL como plataforma como um serviço (PaaS) em Azure.<br/><br/>Este tipo de implantação inclui bases de dados individuais e piscinas elásticas. | Georreplicação ativa | 30 segundos após o disparo do failover.<br/><br/>Quando o failover é ativado para uma das bases de dados secundárias, todos os outros secundários estão automaticamente ligados à nova primária. | RPO de cinco segundos.<br/><br/>A geo-replicação ativa utiliza a tecnologia Always On do SQL Server. Reproduz assincroticamente transações comprometidas na base de dados primária para uma base de dados secundária usando o isolamento instantâneo.<br/><br/>Os dados secundários garantem nunca ter transações parciais.
SQL como PaaS configurado com geo-replicação ativa em Azure.<br/><br/>Este tipo de implantação inclui casos geridos, piscinas elásticas e bases de dados individuais. | Grupos de ativação pós-falha automática | RTO de uma hora. | RPO de cinco segundos.<br/><br/>Os grupos de falha automática fornecem a semântica do grupo em cima da geo-replicação ativa. Mas o mesmo mecanismo de replicação assíncronos é usado.
SQL Server em um Azure IaaS VM ou no local.| Replicação com recuperação do local de Azure | O RTO é tipicamente inferior a 15 minutos. Para saber mais, leia o [RTO SLA fornecido pela Recuperação do Local.](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) | Uma hora para a consistência da aplicação e cinco minutos para a consistência do acidente. Se procura rpo mais baixo, use outras tecnologias BCDR.

> [!NOTE]
> Algumas considerações importantes quando está a ajudar a proteger as cargas de trabalho do SQL com a Recuperação do Local:
> * Recuperação do site é agnóstico de aplicação. A Recuperação do Site pode ajudar a proteger qualquer versão do SQL Server que seja implantada num sistema operativo suportado. Para saber mais, consulte a matriz de [suporte para a recuperação](vmware-physical-azure-support-matrix.md#replicated-machines) de máquinas replicadas.
> * Pode optar por utilizar a Recuperação do Site para qualquer implantação em Azure, Hyper-V, VMware ou infraestrutura física. Siga as orientações no final deste artigo sobre [como ajudar a proteger um cluster sql Server](#how-to-help-protect-a-sql-server-cluster) com recuperação do site.
> * Certifique-se de que a taxa de alteração de dados observada na máquina está dentro [dos limites de Recuperação do Local](vmware-physical-azure-support-matrix.md#churn-limits). A taxa de variação é medida em bytes de escrita por segundo. Para as máquinas que executam o Windows, pode ver esta taxa de alteração selecionando o separador **Desempenho** no Gestor de Tarefas. Observe a velocidade de escrita de cada disco.
> * A Recuperação do Site suporta a replicação de Instâncias de Cluster Failover em Espaços de Armazenamento Direto. Para saber mais, consulte [como ativar a replicação direta dos Espaços de Armazenamento.](azure-to-azure-how-to-enable-replication-s2d-vms.md)
> 
> Quando migrar a sua carga de trabalho SQL para Azure, é aconselhável aplicar as diretrizes de desempenho do [SQL Server em Máquinas Virtuais Azure](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md).

## <a name="disaster-recovery-of-an-application"></a>Recuperação de desastres de uma aplicação

A Recuperação do Site orquestra o failover do teste e o failover de toda a sua aplicação com a ajuda de planos de recuperação.

Existem alguns pré-requisitos para garantir que o seu plano de recuperação é totalmente personalizado de acordo com a sua necessidade. Qualquer implementação do Servidor SQL normalmente necessita de uma implementação de Ative Directory. Também precisa de conectividade para o seu nível de aplicação.

### <a name="step-1-set-up-active-directory"></a>Passo 1: Criar Diretório Ativo

Instale o Ative Directory no local de recuperação secundária para que o SQL Server possa funcionar corretamente.

* **Pequena empresa**: Tem um pequeno número de aplicações e um único controlador de domínio para o local. Se quiser falhar em todo o site, utilize a replicação da Recuperação do Local. Este serviço replica o controlador de domínio para o centro de dados secundário ou para o Azure.
* **Média a grande empresa**: Pode ser necessário configurar controladores de domínio adicionais.
  - Se tiver um grande número de aplicações, ter uma floresta de Diretório Ativo, e quiser falhar por aplicação ou carga de trabalho, crie outro controlador de domínio no centro de dados secundário ou no Azure.
  -  Se estiver a utilizar grupos de disponibilidade Always On para recuperar para um site remoto, crie outro controlador de domínio no site secundário ou no Azure. Este controlador de domínio é utilizado para a placa do SqL Server recuperada.

As instruções deste artigo pressupõem que um controlador de domínio está disponível no local secundário. Para saber mais, consulte os procedimentos para [ajudar a proteger o Ative Directory com a Recuperação do Local.](site-recovery-active-directory.md)

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>Passo 2: Garantir a conectividade com outros níveis

Depois de o nível de base de dados estar em execução na região de Azure alvo, certifique-se de que tem conectividade com a aplicação e os níveis web. Tome previamente os passos necessários para validar a conectividade com o teste de failover.

Para entender como pode projetar aplicações para considerações de conectividade, consulte estes exemplos:

* [Desenhe uma aplicação para recuperação de desastres em nuvem](../azure-sql/database/designing-cloud-solutions-for-disaster-recovery.md)
* [Estratégias elásticas de recuperação de desastres de piscina](../azure-sql/database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>Passo 3: Interoperatório com Always On, geo-replicação ativa e grupos de auto-failover

As tecnologias BCDR Always On, a geo-replicação ativa e os grupos de falha automática têm réplicas secundárias do SQL Server em execução na região de Azure alvo. O primeiro passo para o failover da sua aplicação é especificar esta réplica como primária. Este passo pressupõe que já tem um controlador de domínio no secundário. O passo pode não ser necessário se optar por fazer uma falha automática. Falha nos níveis web e de aplicação apenas depois de concluída a falha da base de dados.

> [!NOTE]
> Se ajudou a proteger as máquinas SQL com recuperação do local, basta criar um grupo de recuperação destas máquinas e adicionar o seu failover no plano de recuperação.

[Crie um plano de recuperação](site-recovery-create-recovery-plans.md) com máquinas virtuais de nível web e aplicação. Os seguintes passos mostram como adicionar o failover do nível de base de dados:

1. Importe os scripts para falhar sobre o SQL Availability Group tanto numa [máquina virtual do Gestor de Recursos](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) como numa máquina virtual [clássica.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1) Importe os scripts na sua conta Azure Automation.

    [![Imagem de um logótipo "Implementar para Azul"](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Adicione o script ASR-SQL-FailoverAG como uma pré-ação do primeiro grupo do plano de recuperação.

1. Siga as instruções disponíveis no script para criar uma variável de automação. Esta variável fornece o nome dos grupos de disponibilidade.

### <a name="step-4-conduct-a-test-failover"></a>Passo 4: Realizar um teste de failover

Algumas tecnologias BCDR, como SQL Always On, não suportam de forma nativa o failover de teste. Recomendamos a seguinte abordagem *apenas quando utilizar estas tecnologias.*

1. Configurar [o Azure Backup](../backup/backup-azure-vms-first-look-arm.md) no VM que acolhe a réplica do grupo de disponibilidade em Azure.

1. Antes de desencadear o teste de falha do plano de recuperação, recupere o VM do backup dado no passo anterior.

    ![Screenshot mostrando janela para restaurar uma configuração de Azure Backup](./media/site-recovery-sql/restore-from-backup.png)

1. [Forçar um quórum](/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) no VM que foi restaurado de reforços.

1. Atualize o endereço IP do ouvinte para ser um endereço disponível na rede de falha de teste.

    ![Screenshot da janela de regras e diálogo de propriedades de endereço IP](./media/site-recovery-sql/update-listener-ip.png)

1. Traga o ouvinte on-line.

    ![Screenshot da janela marcada Content_AG mostrando nomes e status do servidor](./media/site-recovery-sql/bring-listener-online.png)

1. Certifique-se de que o balanceador de carga na rede de failover tem um endereço IP, a partir do conjunto de endereços IP frontal que corresponde a cada ouvinte de grupo de disponibilidade, e com o SQL Server VM no pool back-end.

     ![Screenshot da janela intitulada "SQL-AlwaysOn-LB - Frontend IP Pool](./media/site-recovery-sql/create-load-balancer1.png)

    ![Screenshot da janela intitulada "SQL-AlwaysOn-LB - Backend IP Pool](./media/site-recovery-sql/create-load-balancer2.png)

1. Em grupos de recuperação posteriores, adicione o failover do seu nível de aplicação seguido pelo seu nível web para este plano de recuperação.

1. Faça um teste de falha do plano de recuperação para testar o failover de ponta a ponta da sua aplicação.

## <a name="steps-to-do-a-failover"></a>Passos para fazer um failover

Depois de adicionar o script no Passo 3 e validá-lo no Passo 4, pode fazer uma falha do plano de recuperação criado no Passo 3.

Os passos de failover para aplicações e níveis web devem ser os mesmos tanto nos planos de failover de teste como em planos de recuperação de failover.

## <a name="how-to-help-protect-a-sql-server-cluster"></a>Como ajudar a proteger um cluster de servidor SQL

Para um cluster que executa a edição padrão do SQL Server ou o SQL Server 2008 R2, recomendamos que utilize a replicação da Recuperação do Site para ajudar a proteger o SQL Server.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure para Azure e no local para Azure

A Recuperação do Site não fornece suporte de cluster de hóspedes ao replicar-se numa região do Azure. A edição SQL Server Standard também não fornece uma solução de recuperação de desastres de baixo custo. Neste cenário, recomendamos que proteja o cluster SQL Server a uma instância autónoma do SQL Server na localização primária e recupere-o no secundário.

1. Configure uma instância adicional autónoma do SQL Server na região primária de Azure ou no local.

1. Configure o caso para servir de espelho para as bases de dados que pretende ajudar a proteger. Configure o espelho em modo de alta segurança.

1. Configurar a recuperação do local no local primário para [Azure](azure-to-azure-tutorial-enable-replication.md), [Hiper-V,](./hyper-v-azure-tutorial.md)ou [VMware VMware vMs e servidores físicos](./vmware-azure-tutorial.md).

1. Utilize a replicação da recuperação do local para replicar a nova instância do SQL Server para o site secundário. Como é uma cópia espelhada de alta segurança, será sincronizada com o cluster primário, mas replicada usando a replicação da Recuperação do Local.

   ![Imagem de um cluster padrão que mostra a relação e o fluxo entre um local primário, Recuperação de Sítios e Azure](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Considerações de recuo

Para os clusters SQL Server Standard, o failback após uma falha não planeada requer uma cópia de segurança do SQL Server e restauro. Esta operação é feita desde a instância do espelho até ao aglomerado original com o restaumento do espelho.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>Como é que o SQL Server é licenciado quando usado na Recuperação do Site?

A replicação da recuperação do site para o SQL Server está coberta sob o benefício de recuperação de desastres da Garantia de Software. Esta cobertura aplica-se a todos os cenários de Recuperação de Sítios: no local para a recuperação de desastres de Azure e para a recuperação de desastres transcontinuais Azure IaaS. Consulte [os preços de recuperação do local de Azure](https://azure.microsoft.com/pricing/details/site-recovery/) para mais informações.

### <a name="will-site-recovery-support-my-sql-server-version"></a>A Recuperação do Site irá suportar a minha versão SQL Server?

Recuperação do site é agnóstico de aplicação. A Recuperação do Site pode ajudar a proteger qualquer versão do SQL Server que seja implantada num sistema operativo suportado. Para mais informações, consulte a matriz de [suporte para a recuperação](vmware-physical-azure-support-matrix.md#replicated-machines) de máquinas replicadas.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a arquitetura de Recuperação de Locais.](./azure-to-azure-architecture.md)
* Para o SQL Server em Azure, saiba mais sobre [soluções de alta disponibilidade](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions) para recuperação numa região secundária de Azure.
* Para a SQL Database, saiba mais sobre a continuidade do [negócio](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md) e [opções de elevada disponibilidade](../azure-sql/database/high-availability-sla.md) para recuperação numa região secundária de Azure.
* Para máquinas SQL Server no local, saiba mais sobre as [opções](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions) de alta disponibilidade para recuperação em Azure Virtual Machines.