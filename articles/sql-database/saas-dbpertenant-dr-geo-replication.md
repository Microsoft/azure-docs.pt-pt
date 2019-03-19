---
title: Recuperação após desastre para aplicações de SaaS com o Azure SQL da base de dados de Georreplicação | Documentos da Microsoft
description: Saiba como utilizar réplicas geo de base de dados do Azure SQL para recuperar de uma aplicação SaaS de multi-inquilino em caso de interrupção
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: ayolubek
ms.reviewer: sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: b6f0d25f621768f79e8262f38617152e91692a23
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57838855"
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Recuperação após desastre para uma aplicação de SaaS de multi-inquilino com georreplicação de base de dados

Neste tutorial, explorar um cenário de recuperação após desastre para uma aplicação SaaS de multi-inquilino implementado usando o modelo de base de dados por inquilino. Para proteger a aplicação a partir de uma falha, utilize [ _georreplicação_ ](sql-database-geo-replication-overview.md) para criar as réplicas das bases de dados do catálogo e de inquilino numa região de recuperação alternativo. Se ocorrer uma falha, rapidamente a ativação pós-falha a estas réplicas para retomar as operações comerciais normais. Na ativação pós-falha, as bases de dados na região original tornam-se as réplicas secundárias das bases de dados na região de recuperação. Depois destas réplicas fique novamente online forem automaticamente recuperados para o estado das bases de dados na região de recuperação. Após a falha for resolvida, reativação pós-falha para as bases de dados na região de produção original.

Este tutorial explora os fluxos de trabalho de ativação pós-falha e reativação pós-falha. Vai aprender a:
> [!div class="checklist"]
> 
> * Base de dados de sincronização e informações de configuração do conjunto elástico para o catálogo de inquilino
> * Configurar um ambiente de recuperação numa região alternativo, que consiste em aplicativos, servidores e de agrupamentos
> * Uso _georreplicação_ para replicar as bases de dados do catálogo e de inquilino para a região de recuperação
> * Efetuar a ativação pós-falha da aplicação e as bases de dados de catálogo e de inquilino para a região de recuperação 
> * Mais tarde, efetuar a ativação pós-falha do aplicativo, as bases de dados do catálogo e de inquilino de volta para a região original após a falha for resolvida
> * Atualizar o catálogo, como cada base de dados do inquilino é foi efetuada para controlar a localização primária da base de dados de cada inquilino
> * Certifique-se de que a base de dados do inquilino de aplicação e principal sempre serão colocalizados na mesma região do Azure para reduzir a latência  
 

Antes de iniciar este tutorial, certifique-se de que os seguintes pré-requisitos foram concluídos:
* A base de dados de Wingtip Tickets SaaS por inquilino aplicação é implementada. Para implementar em menos de cinco minutos, veja [implementar e explorar a base de dados de Wingtip Tickets SaaS por aplicação de inquilino](saas-dbpertenant-get-started-deploy.md)  
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>Introdução ao padrão de recuperação de replicação geográfica

![Arquitetura de recuperação](media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)
 
Recuperação após desastre (DR) é uma consideração importante para muitos aplicativos, seja em motivos de conformidade ou de continuidade do negócio. Deve haver uma interrupção de serviço prolongada, um plano de DR bem preparado pode minimizar interrupções nos negócios. Utilizar a replicação geográfica fornece a mais baixa RPO e RTO ao manter as réplicas de base de dados numa região de recuperação que pode efetuar a ativação pós-falha em curto observar.

Plano de DR da com base na replicação geográfica é composto por três partes distintas:
* Configuração-criação e manutenção do ambiente de recuperação
* Recuperação - ativação pós-falha da aplicação e bases de dados para o ambiente de recuperação se ocorrer uma falha,
* Repatriation - ativação pós-falha da aplicação e bases de dados de volta para a região original assim que o aplicativo for resolvido 

Todas as partes deve ser considerado com cuidado, principalmente se operar à escala. Em geral, o plano deve realizar várias metas:

* Configurar
    * Estabelecer e manter um ambiente de imagem espelhada na região de recuperação. Criar os conjuntos elásticos e replicar quaisquer bases de dados neste ambiente de recuperação se a reserva de capacidade na região de recuperação. Manter este ambiente inclui novas bases de dados do inquilino a replicar como terem sido aprovisionados.  
* Recuperação
    * Sempre que um ambiente de recuperação de diminuída é usado para minimizar os custos de diários, conjuntos e bases de dados devem ser aumentados para adquirir capacidade operacional completa na região de recuperação
    * Ativar aprovisionamento logo que possível na região de recuperação do novo inquilino  
    * Otimizado para restaurar os inquilinos na ordem de prioridade
    * Otimizado para obter os inquilinos online mais rápido possível efetuando os passos em paralelo onde prático
    * Ser resiliente a falhas, reinicializável e idempotentes
    * Possível cancelar o processo em trânsito médio, se a região original vem voltar a estar online.
* Repatriation 
    * Efetuar a ativação pós-falha de bases de dados de região de recuperação para réplicas na região original com impacto mínimo para os inquilinos: sem perda de dados e o período mínimo off-line por inquilino.   

Neste tutorial, esses desafios são resolvidos usando recursos do Azure SQL Database e a plataforma do Azure:

* [Modelos Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), para reservar capacidade necessária todas as mais depressa possível. Modelos Azure Resource Manager são utilizados para Aprovisionar uma imagem espelhada dos servidores de produção e conjuntos elásticos na região de recuperação.
* [Replicação geográfica](sql-database-geo-replication-overview.md), para criar replicadas de forma assíncrona secundárias só de leitura para todas as bases de dados. Durante uma falha, efetuar a ativação pós-falha para as réplicas na região de recuperação.  Após a falha for resolvida, reativação pós-falha para as bases de dados na região sem perda de dados original.
* [Assíncrona](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) enviadas por ordem de prioridade de inquilino, para minimizar o tempo de ativação pós-falha para um grande número de bases de dados de operações de ativação pós-falha.
* [Recursos de recuperação de gestão de partições horizontais](sql-database-elastic-database-recovery-manager.md), para alterar as entradas da base de dados no catálogo durante a recuperação e repatriation. Esses recursos permitem que a aplicação para ligar a bases de dados de inquilino, independentemente da localização, sem necessidade de reconfigurar a aplicação.
* [Aliases DNS do SQL server](dns-alias-overview.md), para ativar o aprovisionamento totalmente integrada de novos inquilinos, independentemente de qual a região a aplicação está a funcionar no. Aliases de DNS também são utilizadas para permitir que o processo de sincronização do catálogo para ligar ao catálogo do Active Directory, independentemente da respetiva localização.

## <a name="get-the-disaster-recovery-scripts"></a>Obter scripts de recuperação de desastre 

> [!IMPORTANT]
> Como todos os scripts de gestão de Wingtip Tickets, os scripts de DR são qualidade de exemplo e não devem ser utilizados na produção. 

A recuperação scripts utilizados neste tutorial e o código de origem de aplicação Wingtip estão disponíveis no [Wingtip Tickets SaaS base de dados por inquilino repositório do GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/). Veja a [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter os passos transferir e os scripts de gestão da Wingtip Tickets de desbloqueio.

## <a name="tutorial-overview"></a>Descrição geral do tutorial
Neste tutorial, utilize primeiro georreplicação para criar as réplicas da aplicação Wingtip Tickets e respetivas bases de dados numa região diferente. Em seguida, efetuar a ativação pós-falha para esta região para simular a recuperar a partir de um período de indisponibilidade. Quando terminar, o aplicativo é totalmente funcional na região de recuperação.

Mais tarde, um passo separado repatriation, efetuar a ativação pós-falha as bases de dados de catálogo e de inquilino na região de recuperação para a região original. A aplicação e as bases de dados mantenha-se disponíveis em todo o repatriation. Quando terminar, o aplicativo é totalmente funcional na região original.

> [!Note]
> O aplicativo é recuperado para o _região emparelhada_ da região em que a aplicação é implementada. Para obter mais informações, consulte [regiões emparelhadas do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="review-the-healthy-state-of-the-application"></a>Reveja o estado de funcionamento da aplicação

Antes de começar o processo de recuperação, reveja o estado de bom estado de funcionamento normal do aplicativo.
1. No seu navegador da web, abra o Hub de eventos do Wingtip Tickets (http://events.wingtip-dpt.&lt; usuário&gt;. trafficmanager.net - substitua &lt;utilizador&gt; com valor de utilizador da sua implementação).
    * Desloque-se para a parte inferior da página e tenha em atenção o nome do servidor de catálogo e o local no rodapé. A localização é a região em que implementou a aplicação.
    *SUGESTÃO: Paire o rato sobre a localização para aumentar a exibição. * 
     ![Estado bom estado de funcionamento do hub de eventos na região original](media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Clique no inquilino Contoso Concert Hall e abrir a página de eventos.
    * No rodapé, tenha em atenção o nome do servidor de inquilino. A localização será a mesma localização do servidor de catálogo.

3. Na [portal do Azure](https://portal.azure.com), abra o grupo de recursos no qual a aplicação é implementada
    * Tenha em atenção a região em que os servidores são implementados. 

## <a name="sync-tenant-configuration-into-catalog"></a>Configuração de inquilinos de sincronização no catálogo

Nesta tarefa, iniciar um processo que sincroniza a configuração de servidores, conjuntos elásticos e bases de dados para o catálogo de inquilino. O processo mantém estas informações atualizadas no catálogo.  O processo funciona com o catálogo do Active Directory, se na região original ou na região de recuperação. As informações de configuração são utilizadas como parte do processo de recuperação para garantir que o ambiente de recuperação é consistente com o ambiente original e, em seguida, mais tarde durante repatriation para garantir que a região original é tornada consistente com todas as alterações efetuadas a ambiente de recuperação. O catálogo também serve para controlar o estado de recuperação dos recursos de inquilino

> [!IMPORTANT]
> Para simplificar, o processo de sincronização e outros processos de recuperação e repatriation execução demorada são implementados nestes tutoriais como tarefas do PowerShell locais ou sessões que são executados em seu início de sessão de utilizador do cliente. Os tokens de autenticação emitidos quando iniciar sessão vai expirar após várias horas e, em seguida, as tarefas falhará. Num cenário de produção, os processos de longa execução devem ser implementados como serviços do Azure fiáveis de algum tipo, um principal de serviço a ser executado. Ver [utilize o Azure PowerShell para criar um principal de serviço com um certificado](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal).

1. Na _ISE do PowerShell_, abra o ficheiro de Modules\UserConfig.psm1 ...\Learning. Substitua `<resourcegroup>` e `<user>` em linhas 10 e 11 com o valor que usou quando implementou a aplicação.  Guarde o ficheiro!

2. Na *ISE do PowerShell*, abra o ...\Learning script Modules\Business continuidade e desastre Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 e defina:
    * **$DemoScenario = 1**, iniciar uma tarefa em segundo plano que se sincroniza o servidor de inquilino e agrupamento de informações de configuração para o catálogo

3. Prima **F5** para executar o script de sincronização. Uma nova sessão do PowerShell é aberta para sincronizar a configuração dos recursos de inquilino.
![Processo de sincronização](media/saas-dbpertenant-dr-geo-replication/sync-process.png)

Deixe a janela do PowerShell em execução em segundo plano e continuar com o resto do tutorial. 

> [!Note]
> O processo de sincronização liga-se para o catálogo por meio de um alias de DNS. Este alias é modificado durante o restauro e repatriation para apontar para o catálogo do Active Directory. O processo de sincronização mantém o catálogo atualizado com a base de dados ou um conjunto de alterações de configuração feitas na região de recuperação.  Durante a repatriation, estas alterações são aplicadas a recursos equivalentes na região original.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>Criar réplicas de base de dados secundária numa região de recuperação

Nesta tarefa, iniciar um processo que implementa uma instância de aplicação duplicado e replica o catálogo e todas as bases de dados de inquilinos para uma região de recuperação.

> [!Note]
> Este tutorial adiciona proteção de georreplicação para a aplicação Wingtip Tickets de exemplo. Num cenário de produção para uma aplicação que utiliza os replicação geográfica, cada inquilino teria de ser aprovisionado com uma base de dados de georreplicação desde o início. Consulte [criando serviços de elevada disponibilidade com a base de dados do Azure SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. Na *ISE do PowerShell*, abra o ...\Learning script Modules\Business continuidade e desastre Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 e defina os seguintes valores:
    * **$DemoScenario = 2**, crie o ambiente de recuperação da imagem espelhada e replicar bases de dados do catálogo e de inquilino

2. Prima **F5** para executar o script. Uma nova sessão do PowerShell é aberta para criar as réplicas.
![Processo de sincronização](media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>Reveja o estado de aplicativo normal

Neste momento, a aplicação está a funcionar normalmente na região original e agora está protegida pela georreplicação.  As réplicas secundárias só de leitura, existe na região de recuperação para todas as bases de dados. 

1. No portal do Azure, observe os grupos de recursos e tenha em atenção que um grupo de recursos foi criado com - sufixo de recuperação na região de recuperação. 

2. Explore os recursos no grupo de recursos de recuperação.  

3. Clique na base de dados Contoso Concert Hall no _tenants1-dpt -&lt;usuário&gt;-recuperação_ server.  Clique no Georreplicação no lado esquerdo. 

    ![Ligação de georreplicação de contoso Concert](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

O mapa de regiões do Azure, tenha em atenção a ligação de georreplicação entre a primária na região original e o elemento secundário na região de recuperação.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>Efetuar a ativação pós-falha a aplicação para a região de recuperação

### <a name="geo-replication-recovery-process-overview"></a>Visão geral do processo de recuperação georreplicação

O script de recuperação executa as seguintes tarefas:

1. Desativa o ponto de final do Gestor de tráfego para a aplicação web na região original. Desativar o ponto final impede os utilizadores estabeleçam uma ligação para a aplicação num estado inválido deve a região original ficar online durante a recuperação.

1. Utiliza uma ativação pós-falha forçada da base de dados de catálogo na região de recuperação para torná-lo a base de dados primária e atualiza a _activecatalog_ alias para apontar para o servidor de catálogo de recuperação.

1. Atualizações a _newtenant_ alias para apontar para o servidor de inquilino na região de recuperação. Alterar este alias garante que as bases de dados para quaisquer novos inquilinos aprovisionados na região de recuperação. 

1. Marca todos os inquilinos existentes no catálogo de recuperação como offline para impedir o acesso às bases de dados do inquilino antes de eles são a ativação pós-falha.

1. Atualiza a configuração de todos os conjuntos elásticos e bases de dados individuais replicados na região de recuperação para espelhar a configuração na região original. (Esta tarefa só é necessário se agrupamentos ou bancos de dados replicados no ambiente de recuperação são reduzidos verticalmente durante operações normais para reduzir os custos).

1. Permite que o ponto de final do Gestor de tráfego para a aplicação web na região de recuperação. Ativar este ponto final permite que o aplicativo aprovisionar novos inquilinos. Nesta fase, os inquilinos existentes estão ainda offline.

1. Envia lotes de pedidos para forçar a ativação pós-falha bases de dados por ordem de prioridade.
    * Lotes são organizadas para que as bases de dados com ativação pós-falha em paralelo em todos os agrupamentos.
    * Pedidos de ativação pós-falha são submetidos a utilizar operações assíncronas, de modo a que for enviado rapidamente e várias solicitações podem ser processadas em simultâneo.

   > [!Note]
   > Num cenário de falha, as bases de dados primárias na região original estão offline.  Força a ativação pós-falha em quebras de secundárias a ligação para o primário sem tentar aplicar todas as transações em fila residuais. Um cenário de exploração de DR, como neste tutorial, se houver qualquer atividade de atualização no momento da ativação pós-falha pode haver alguma perda de dados. Mais tarde, durante repatriation, quando falha nas bases de dados na região de recuperação para a região original, uma ativação pós-falha normal é utilizada para garantir que existe sem perda de dados.

1. Monitoriza o serviço de base de dados SQL para determinar quando as bases de dados têm falhados. Depois de uma base de dados do inquilino é a ativação pós-falha, ele atualiza o catálogo para registrar o estado de recuperação da base de dados de inquilinos e marcar o inquilino como online.
    * Bases de dados do inquilino podem ser acessados pela aplicação assim que estiver marcadas no catálogo online.
    * Uma soma dos valores de rowversion na base de dados de inquilino é armazenada no catálogo. Este valor atua como uma impressão digital que permite que o processo de repatriation determinar se a base de dados foi atualizado na região de recuperação.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>Execute o script para efetuar a ativação pós-falha para a região de recuperação

Agora imagine que houver uma falha na região em que a aplicação é implementada e execute o script de recuperação:

1. Na *ISE do PowerShell*, abra o ...\Learning script Modules\Business continuidade e desastre Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 e defina os seguintes valores:
    * **$DemoScenario = 3**, recuperar a aplicação para uma região de recuperação por realizar a ativação pós-falha para réplicas

2. Prima **F5** para executar o script.  
    * O script abre-se numa nova janela do PowerShell e, em seguida, inicia uma série de tarefas do PowerShell que são executadas em paralelo. Estas tarefas a ativação pós-falha de bases de dados de inquilinos para a região de recuperação.
    * A região de recuperação é o _região emparelhada_ associados à região do Azure que implementou a aplicação. Para obter mais informações, consulte [regiões emparelhadas do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Monitorize o estado do processo de recuperação na janela do PowerShell.
    ![Processo de ativação pós-falha](media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> Para explorar o código para as tarefas de recuperação, reveja os scripts do PowerShell na pasta ...\Learning Modules\Business continuidade e desastre Recovery\DR-FailoverToReplica\RecoveryJobs.

### <a name="review-the-application-state-during-recovery"></a>Reveja o estado da aplicação durante a recuperação

Embora o ponto final da aplicação está desabilitado no Gestor de tráfego, o aplicativo não está disponível. Depois do catálogo é a ativação pós-falha para a região de recuperação e todos os inquilinos marcada como offline, o aplicativo seja novamente colocado online. Embora o aplicativo estiver disponível, cada inquilino aparece offline no hub de eventos até que a respetiva base de dados é a ativação pós-falha. É importante projetar seu aplicativo para lidar com bases de dados do inquilino offline.

1. Imediatamente depois da base de dados do catálogo foi recuperado, atualize o Hub de eventos de bilhetes Wingtip no seu browser.
   * No rodapé, tenha em atenção que o nome do servidor de catálogo agora tem um _-recuperação_ sufixo e está localizado na região de recuperação.
   * Tenha em atenção que os inquilinos que ainda não foram restaurados, são marcados como offline e não sejam selecionável.  

     > [!Note]
     > Com apenas algumas bases de dados para recuperar, não poderá atualizar o browser antes de recuperação foi concluída, pelo que poderá não ver os inquilinos enquanto estão offline. 
 
     ![Hub de eventos offline](media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

   * Se abrir a página de eventos de um inquilino offline diretamente, ele apresenta uma notificação de "inquilino offline". Por exemplo, se a Contoso Concert Hall estiver offline, tente abrir http://events.wingtip-dpt.&lt; usuário&gt;.trafficmanager.net/contosoconcerthall ![página Contoso Offline](media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>Aprovisionar um inquilino novo na região de recuperação
Antes mesmo de todo os inquilino bases de dados existentes têm a ativação pós-falha, pode aprovisionar novos inquilinos na região de recuperação.  

1. Na *ISE do PowerShell*, abra o ...\Learning script Modules\Business continuidade e desastre Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 e defina a propriedade seguinte:
    * **$DemoScenario = 4**, aprovisionar um inquilino novo na região de recuperação

2. Prima **F5** para executar o script e aprovisionar o novo inquilino. 

3. Quando for concluída, é aberta a página de eventos Hawthorn Hall no browser. Observe, no rodapé, o que a base de dados Hawthorn Hall é aprovisionado na região de recuperação.
    ![Página de eventos de hawthorn Hall](media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. No browser, atualize a página de Hub de eventos de bilhetes Wingtip para ver que hawthorn Hall incluídas. 
    * Se aprovisionou Hawthorn Hall sem esperar por outros inquilinos restaurar, outros inquilinos poderão ainda estar offline.


## <a name="review-the-recovered-state-of-the-application"></a>Reveja o estado recuperado da aplicação

Quando tiver concluído o processo de recuperação, a aplicação e todos os inquilinos estão totalmente funcionais na região de recuperação. 

1. Assim que a exibição na janela de consola do PowerShell indica que todos os inquilinos são recuperados, atualize o Hub de eventos.  Os inquilinos irão de aparecer online, incluindo o novo inquilino, Hawthorn Hall.

    ![recuperada e novos inquilinos no hub de eventos](media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. Na [portal do Azure](https://portal.azure.com), abra a lista de grupos de recursos.  
    * Tenha em atenção o grupo de recursos que implementou, além do grupo de recursos de recuperação, com o _-recuperação_ sufixo.  O grupo de recursos de recuperação contém todos os recursos criados durante o processo de recuperação, além de recursos novos criados durante o período de inatividade.  

3. Abra o grupo de recursos de recuperação e tenha em atenção os seguintes itens:
   * As versões de recuperação dos servidores de catálogo e tenants1, com _-recuperação_ sufixo.  Os catálogo e de inquilino bancos de dados restaurados nestes servidores todos os tem os nomes utilizados na região original.

   * O _tenants2-dpt -&lt;usuário&gt;-recuperação_ do SQL server.  Este servidor é utilizado para aprovisionar novos inquilinos durante o período de inatividade.
   * O serviço de aplicações com o nome, _eventos-wingtip-dpt -&lt;recoveryregion&gt;-&lt;utilizador & gt_; que é a instância de recuperação da aplicação de eventos. 

     ![Recursos de recuperação do Azure](media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png) 
    
4. Abra o _tenants2-dpt -&lt;usuário&gt;-recuperação_ do SQL server.  Observe que contém a base de dados _hawthornhall_ e o conjunto elástico, _Pool1_.  O _hawthornhall_ base de dados é configurada como uma base de dados elástica _Pool1_ conjunto elástico.

5. Navegue de volta para o grupo de recursos e clique na base de dados Contoso Concert Hall no _tenants1-dpt -&lt;usuário&gt;-recuperação_ server. Clique no Georreplicação no lado esquerdo.
    
    ![Base de dados contoso após a ativação pós-falha](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Dados de alteração de inquilino 
Nesta tarefa, atualizar uma das bases de dados do inquilino. 

1. No seu browser, encontrar a lista de eventos para a Contoso Concert Hall e tome nota do último nome de evento.
2. Na *ISE do PowerShell*, na ...\Learning script Modules\Business continuidade e desastre Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1, defina o seguinte valor:
    * **$DemoScenario = 5** elimine um evento de um inquilino na região de recuperação
3. Prima **F5** para executar o script
4. Atualize a página de eventos de Contoso Concert Hall (http://events.wingtip-dpt.&lt; usuário&gt;.trafficmanager.net/contosoconcerthall - substitua &lt;utilizador&gt; com valor de utilizador da sua implementação) e tenha em atenção que o último evento foi eliminado.

## <a name="repatriate-the-application-to-its-original-production-region"></a>Repatriate o aplicativo para sua região de produção original

Esta tarefa repatriates o aplicativo para sua região original. Num cenário real, seria iniciar repatriation quando a falha for resolvida.

### <a name="repatriation-process-overview"></a>Visão geral do processo repatriation

![Arquitetura de repatriation](media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

O processo de repatriation:
1. Cancela quaisquer pedidos de restauro de base de dados pendentes ou em trânsito.
2. Atualizações a _newtenant_ alias para apontar para o servidor dos inquilinos na região de origem. Alterar este alias garante que as bases de dados para quaisquer novos inquilinos agora serão aprovisionados na região de origem.
3. Espalha os dados de inquilino alterado para a região original
4. Efetua a ativação pós-falha de bases de dados de inquilinos na ordem de prioridade.

Ativação pós-falha move efetivamente a base de dados para a região original. Quando a base de dados falha, todas as ligações abertas são ignoradas e a base de dados está indisponível por alguns segundos. Aplicações devem ser escritas com a lógica de repetição para garantir que eles se conectem novamente.  Embora este breve disconnect, muitas vezes, não é percebido, pode optar por repatriate bases de dados fora do horário comercial. 


### <a name="run-the-repatriation-script"></a>Execute o script de repatriation
Agora vamos imaginar a indisponibilidade for resolvida e execute o script de repatriation.

1. Na *ISE do PowerShell*, na ...\Learning Modules\Business continuidade e desastre Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 script.

2. Certifique-se de que o processo de sincronização do catálogo ainda está em execução na sua instância do PowerShell.  Se necessário, reinicie-o através da definição:
    * **$DemoScenario = 1**, iniciar a sincronização de servidor de inquilino, o conjunto e informações de configuração de base de dados para o catálogo
    * Prima **F5** para executar o script.

3.  Em seguida, para iniciar o processo de repatriation, defina:
    * **$DemoScenario = 6**, repatriate a aplicação em sua região original
    * Prima **F5** para executar o script de recuperação numa nova janela do PowerShell.  Repatriation demorará alguns minutos e pode ser monitorado na janela do PowerShell.
    ![Processo de repatriation](media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. Enquanto estiver a executar o script, atualize a página de Hub de eventos (http://events.wingtip-dpt.&lt; utilizador&gt;. trafficmanager.net)
    * Tenha em atenção que todos os inquilinos estão online e acessíveis ao longo deste processo.

5. Depois de concluída a repatriation, atualize o hub de eventos e abra a página de eventos de Hawthorn Hall. Tenha em atenção que esta base de dados tem sido repatriated para a região original.
    ![Hub de eventos repatriated](media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Projetar o aplicativo para garantir que a aplicação e a base de dados estão colocalizados 
O aplicativo foi projetado para que ele liga-se sempre de uma instância na mesma região que a base de dados do inquilino. Esta conceção reduz a latência entre a aplicação e a base de dados. Essa otimização assume que a interação de bancos de aplicação é chattier que a interação de aplicação do utilizador.  

Bases de dados do inquilino podem estar espalhadas entre regiões originais e de recuperação durante algum tempo durante repatriation. Cada base de dados, o aplicativo procura a região na qual a base de dados está localizada, fazendo uma pesquisa de DNS no nome do servidor de inquilino. Na base de dados SQL, o nome do servidor é um alias. O nome do servidor de um alias contém o nome da região. Se o aplicativo não estiver na mesma região que a base de dados, será redirecionado para a instância na mesma região que o servidor de base de dados.  Redirecionar para a instância na mesma região que a base de dados minimiza a latência entre a aplicação e a base de dados. 

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:
> [!div class="checklist"]
> 
> * Base de dados de sincronização e informações de configuração do conjunto elástico para o catálogo de inquilino
> * Configurar um ambiente de recuperação numa região alternativo, que consiste em aplicativos, servidores e de agrupamentos
> * Uso _georreplicação_ para replicar as bases de dados do catálogo e de inquilino para a região de recuperação
> * Efetuar a ativação pós-falha da aplicação e as bases de dados de catálogo e de inquilino para a região de recuperação 
> * A reativação pós-falha as bases de dados de aplicativos, catálogo e de inquilino para a região original após a falha for resolvida

Pode saber mais sobre as tecnologias de base de dados SQL do Azure fornece para permitir a continuidade do negócio no [descrição geral da continuidade de negócio](sql-database-business-continuity.md) documentação.

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que se baseiam na aplicação Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
