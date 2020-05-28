---
title: Configurar a recuperação de desastres para o Servidor SQL com recuperação do site Azure
description: Este artigo descreve como configurar a recuperação de desastres para o SQL Server utilizando o SQL Server e a Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: 4146553d59607e1512d8f15391d143d44815cea9
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84016479"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Configurar a recuperação de desastres para o Servidor SQL

Este artigo descreve como ajudar a proteger o SQL Server na parte de trás de uma aplicação. Fá-lo utilizando uma combinação de tecnologias de continuidade de negócios do SQL Server e de recuperação de desastres (BCDR) e recuperação de [sites azure.](site-recovery-overview.md)

Antes de começar, certifique-se de compreender as capacidades de recuperação de desastres do SQL Server. Estas funcionalidades incluem:

* Clustering de ativação pós-falha
* Sempre em grupos de disponibilidade
* Espelhamento da base de dados
* Envio de registos
* Georreplicação ativa
* Grupos de ativação pós-falha automática

## <a name="combining-bcdr-technologies-with-site-recovery"></a>Combinando tecnologias BCDR com recuperação do site

A sua escolha de uma tecnologia BCDR para recuperar as instâncias do SQL Server deve basear-se nas necessidades do objetivo de tempo de recuperação (RTO) e do objetivo do ponto de recuperação (RPO), conforme descrito na tabela seguinte. Combine a Recuperação do Site com o funcionamento de failover da sua tecnologia escolhida para orquestrar a recuperação de toda a sua aplicação.

Tipo de implantação | Tecnologia BCDR | RTO esperado para Servidor SQL | RPO esperado para servidor SQL |
--- | --- | --- | ---
SQL Server em uma infraestrutura Azure como uma máquina virtual de serviço (IaaS) (VM) ou no local.| [Grupo de disponibilidade Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) | O tempo que se demorou a fazer a réplica secundária como primária. | Porque a replicação à réplica secundária é assíncrona, há alguma perda de dados.
SQL Server em um VM Azure IaaS ou no local.| [Agrupamento failover (Sempre On FCI)](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017) | O tempo que demorou a falhar entre os nós. | Como o Always On FCI utiliza armazenamento partilhado, a mesma vista da instância de armazenamento está disponível no failover.
SQL Server em um VM Azure IaaS ou no local.| [Espelho de base de dados (modo de alto desempenho)](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017) | O tempo que se demorou a forçar o serviço, que utiliza o servidor de espelhos como um servidor de espera quente. | A replicação é assíncrona. A base de dados dos espelhos pode ficar um pouco atrás da base de dados principal. O lag é tipicamente pequeno. Mas pode tornar-se grande se o sistema do servidor principal ou do servidor de espelhos estiver sob uma carga pesada.<br/><br/>O envio de registos pode ser um suplemento ao espelhamento da base de dados. É uma alternativa favorável ao espelhamento de bases de dados assíncronas.
SQL como plataforma como serviço (PaaS) no Azure.<br/><br/>Este tipo de implantação inclui bases de dados únicas e piscinas elásticas. | Georreplicação ativa | 30 segundos após a falha ser desencadeada.<br/><br/>Quando a falha é ativada para uma das bases de dados secundárias, todos os outros secundários estão automaticamente ligados às novas primárias. | RPO de cinco segundos.<br/><br/>A geo-replicação ativa utiliza a tecnologia Always On do SQL Server. Replica assincronicamente transações cometidas na base de dados primária para uma base de dados secundária utilizando o isolamento instantâneo.<br/><br/>Os dados secundários são garantidos para nunca terem transações parciais.
SQL como PaaS configurado com geo-replicação ativa em Azure.<br/><br/>Este tipo de implementação inclui uma Base de Dados SQL gerida por casos, piscinas elásticas e bases de dados individuais. | Grupos de ativação pós-falha automática | RTO de uma hora. | RPO de cinco segundos.<br/><br/>Os grupos de falha automática fornecem a semântica do grupo em cima da geo-replicação ativa. Mas o mesmo mecanismo de replicação assíncrona é usado.
SQL Server em um VM Azure IaaS ou no local.| Replicação com recuperação do site Azure | RTO é tipicamente inferior a 15 minutos. Para saber mais, leia o [RTO SLA fornecido pela Recovery Site](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). | Uma hora para a consistência da aplicação e cinco minutos para a consistência do acidente. Se procura um RPO mais baixo, use outras tecnologias BCDR.

> [!NOTE]
> Algumas considerações importantes quando está a ajudar a proteger as cargas de trabalho da SQL com a Recuperação do Site:
> * A Recuperação do Site é agnóstica da aplicação. A Recuperação do Site pode ajudar a proteger qualquer versão do Servidor SQL que seja implementada num sistema operativo suportado. Para saber mais, consulte a matriz de [suporte para a recuperação](vmware-physical-azure-support-matrix.md#replicated-machines) de máquinas replicadas.
> * Pode optar por utilizar a Recuperação do Site para qualquer implantação em Azure, Hyper-V, VMware ou infraestrutura física. Por favor, siga a orientação no final deste artigo sobre [como ajudar a proteger um cluster de Servidor SQL](#how-to-help-protect-a-sql-server-cluster) com a Recuperação do Site.
> * Certifique-se de que a taxa de alteração de dados observada na máquina está dentro dos [limites de Recuperação](vmware-physical-azure-support-matrix.md#churn-limits)do Site . A taxa de variação é medida em bytes por escrito por segundo. Para máquinas que executam o Windows, pode visualizar esta taxa de alteração selecionando o separador **Performance** no Task Manager. Observe a velocidade de escrita para cada disco.
> * A Recuperação do Site suporta a replicação de instâncias de cluster failover em espaços de armazenamento direto. Para saber mais, veja [como ativar a replicação direta](azure-to-azure-how-to-enable-replication-s2d-vms.md)dos espaços de armazenamento.

## <a name="disaster-recovery-of-an-application"></a>Recuperação de desastres de uma aplicação

A Recuperação do Site orquestra a falha do teste e a falha de toda a sua aplicação com a ajuda de planos de recuperação.

Existem alguns pré-requisitos para garantir que o seu plano de recuperação seja totalmente personalizado de acordo com a sua necessidade. Qualquer implementação do Servidor SQL normalmente necessita de uma implementação de Diretório Ativo. Também precisa de conectividade para o seu nível de aplicação.

### <a name="step-1-set-up-active-directory"></a>Passo 1: Configurar diretório ativo

Configurar o Ative Directory no local de recuperação secundária para o SQL Server funcionar corretamente.

* **Pequena empresa:** Tem um pequeno número de aplicações e um único controlador de domínio para o local no local. Se quiser falhar em todo o site, utilize a replicação da Recuperação do Site. Este serviço replica o controlador de domínio para o centro de dados secundário ou para o Azure.
* **Empresa média a grande:** Pode ser necessário criar controladores de domínio adicionais.
  - Se tiver um grande número de aplicações, tenha uma floresta de Diretório Ativo, e pretenda falhar por aplicação ou carga de trabalho, instale outro controlador de domínio no centro de dados secundário ou no Azure.
  -  Se estiver a utilizar grupos de disponibilidade Always On para recuperar para um site remoto, instale outro controlador de domínio no site secundário ou no Azure. Este controlador de domínio é utilizado para a instância de Servidor SQL recuperado.

As instruções deste artigo pressupõem que um controlador de domínio está disponível no local secundário. Para saber mais, consulte os procedimentos para ajudar a proteger o [Diretório Ativo com a Recuperação](site-recovery-active-directory.md)do Site .

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>Passo 2: Garantir a conectividade com outros níveis

Depois de o nível de base de dados estar a funcionar na região-alvo do Azure, certifique-se de que tem conectividade com a aplicação e os níveis web. Tome as medidas necessárias com antecedência para validar a conectividade com a falha do teste.

Para entender como pode conceber aplicações para considerações de conectividade, consulte estes exemplos:

* [Desenhe uma aplicação para recuperação de desastres em nuvem](../azure-sql/database/designing-cloud-solutions-for-disaster-recovery.md)
* [Estratégias de recuperação de desastres de piscina elástica](../azure-sql/database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>Passo 3: Interopera com sempre on, geo-replicação ativa e grupos de auto-failover

As tecnologias BCDR Always On, geo-replicação ativa e grupos de auto-failover têm réplicas secundárias do SQL Server em execução na região-alvo do Azure. O primeiro passo para a falha da sua aplicação é especificar esta réplica como primária. Este passo pressupõe que já tem um controlador de domínio no secundário. O passo pode não ser necessário se optar por fazer uma falha automática. Falhar nos níveis de web e aplicação apenas depois de concluída a falha na base de dados.

> [!NOTE]
> Se ajudou a proteger as máquinas SQL com recuperação do site, apenas precisa criar um grupo de recuperação destas máquinas e adicionar a sua falha no plano de recuperação.

[Crie um plano de recuperação](site-recovery-create-recovery-plans.md) com aplicação e máquinas virtuais de nível web. Os seguintes passos mostram como adicionar falha do nível de base de dados:

1. Importe os scripts para falhar em SQL Availability Group tanto numa [máquina virtual do Gestor](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) de Recursos como numa máquina virtual [clássica.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1) Importe os scripts na sua conta Azure Automation.

    [![Imagem de um logótipo "Deploy to Azure"](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Adicione o script ASR-SQL-FailoverAG como uma pré-ação do primeiro grupo do plano de recuperação.

1. Siga as instruções disponíveis no script para criar uma variável de automação. Esta variável fornece o nome dos grupos de disponibilidade.

### <a name="step-4-conduct-a-test-failover"></a>Passo 4: Realizar uma falha de teste

Algumas tecnologias BCDR, como o SQL Always On, não suportam de forma nativa o failover do teste. Recomendamos a seguinte abordagem *apenas quando utilizar tais tecnologias.*

1. Instale [o Azure Backup](../backup/backup-azure-arm-vms.md) no VM que acolhe a réplica do grupo de disponibilidade em Azure.

1. Antes de desencadear a falha do teste do plano de recuperação, recupere o VM do backup feito no passo anterior.

    ![Screenshot mostrando janela para restaurar uma configuração de Backup Azure](./media/site-recovery-sql/restore-from-backup.png)

1. [Force um quórum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) no VM que foi restaurado de reserva.

1. Atualize o endereço IP do ouvinte para ser um endereço disponível na rede de failover do teste.

    ![Screenshot de regras janela e endereço IP propriedades diálogo](./media/site-recovery-sql/update-listener-ip.png)

1. Traga o ouvinte on-line.

    ![Screenshot da janela rotulado Content_AG mostrando nomes e status do servidor](./media/site-recovery-sql/bring-listener-online.png)

1. Certifique-se de que o balanceador de carga na rede failover tem um endereço IP, a partir do conjunto de endereços IP frontal que corresponde a cada ouvinte do grupo de disponibilidade, e com o VM do Servidor SQL na piscina traseira.

     ![Screenshot da janela intitulada "SQL-AlwaysOn-LB - Frontend IP Pool](./media/site-recovery-sql/create-load-balancer1.png)

    ![Screenshot da janela intitulada "SQL-AlwaysOn-LB - Backend IP Pool](./media/site-recovery-sql/create-load-balancer2.png)

1. Em grupos de recuperação posteriores, adicione falha do seu nível de aplicação seguido pelo seu nível web para este plano de recuperação.

1. Faça uma falha no teste do plano de recuperação para testar a falha de ponta a ponta da sua aplicação.

## <a name="steps-to-do-a-failover"></a>Passos para fazer uma falha

Depois de adicionar o script no Passo 3 e validá-lo no Passo 4, pode fazer uma falha do plano de recuperação criado no Passo 3.

As etapas de failover para aplicação e níveis web devem ser as mesmas tanto nos planos de failover de teste como nos planos de recuperação de failover.

## <a name="how-to-help-protect-a-sql-server-cluster"></a>Como ajudar a proteger um cluster de servidor SQL

Para um cluster que executa a edição Padrão do Servidor SQL ou o SQL Server 2008 R2, recomendamos que utilize a replicação de Recuperação do Site para ajudar a proteger o Servidor SQL.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure para Azure e No-Local para Azure

A Recuperação do Site não fornece apoio ao cluster de hóspedes quando se replica para uma região de Azure. A edição SQL Server Standard também não fornece uma solução de recuperação de desastres de baixo custo. Neste cenário, recomendamos que proteja o cluster Do Servidor SQL para uma instância autónoma do SQL Server na localização primária e recupere-o no secundário.

1. Configure uma instância adicional autónoma do SQL Server na região primária de Azure ou no local no local.

1. Configure a instância para servir de espelho para as bases de dados que pretende ajudar a proteger. Configure o espelhamento em modo de alta segurança.

1. Configure a recuperação do site no local principal para [VMs Azure,](azure-to-azure-tutorial-enable-replication.md) [Hyper-V](site-recovery-hyper-v-site-to-azure.md)ou [VMware e servidores físicos](site-recovery-vmware-to-azure-classic.md).

1. Utilize a replicação de recuperação do site para replicar a nova instância do Servidor SQL para o site secundário. Como é uma cópia espelhada de alta segurança, será sincronizada com o cluster primário, mas replicada usando a replicação da recuperação do local.

   ![Imagem de um cluster padrão que mostra a relação e fluxo entre um local primário, Recuperação do Site, e Azure](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Considerações de retrocesso

Para os clusters SQL Server Standard, o failback após uma falha não planeada requer uma cópia de segurança do SQL Server e restaure. Esta operação é feita desde a instância do espelho até ao cluster original com o restabelecimento do espelho.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>Como é que o SQL Server é licenciado quando usado com a Recuperação do Site?

A replicação de recuperação do site para o SQL Server está coberta sob o benefício de recuperação de desastres de Garantia de Software. Esta cobertura aplica-se a todos os cenários de Recuperação do Site: no local para a recuperação de desastres de Azure e recuperação de desastres azure IaaS. Consulte os preços de recuperação do [site Azure](https://azure.microsoft.com/pricing/details/site-recovery/) para mais informações.

### <a name="will-site-recovery-support-my-sql-server-version"></a>Será que a Recuperação do Site irá suportar a minha versão SQL Server?

A Recuperação do Site é agnóstica da aplicação. A Recuperação do Site pode ajudar a proteger qualquer versão do Servidor SQL que seja implementada num sistema operativo suportado. Para mais informações, consulte a matriz de [suporte para a recuperação](vmware-physical-azure-support-matrix.md#replicated-machines) de máquinas replicadas.

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre a arquitetura de [Recuperação do Site.](site-recovery-components.md)
* Para o SQL Server em Azure, saiba mais sobre [soluções de alta disponibilidade](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions) para recuperação numa região secundária do Azure.
* Para a Base de Dados SQL, saiba mais sobre a continuidade do [negócio](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md) e opções [de alta disponibilidade](../azure-sql/database/high-availability-sla.md) para recuperação numa região secundária do Azure.
* Para máquinas SQL Server no local, saiba mais sobre as [opções](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions) de alta disponibilidade para recuperação em Máquinas Virtuais Azure.
