---
title: Recuperação de desastre para aplicativos SaaS com replicação geográfica
description: Saiba como usar as réplicas geográficas do banco de dados SQL do Azure para recuperar um aplicativo SaaS multilocatário no caso de uma interrupção
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: craigg
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 0668ccf5ceb972dd120e4e3f37be6d879a12d0a7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73811710"
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Recuperação de desastre para um aplicativo SaaS multilocatário usando replicação geográfica de banco de dados

Neste tutorial, você explora um cenário de recuperação de desastres completo para um aplicativo SaaS multilocatário implementado usando o modelo de banco de dados por locatário. Para proteger o aplicativo contra uma interrupção, use a [_replicação geográfica_](sql-database-geo-replication-overview.md) para criar réplicas para os bancos de dados de catálogo e locatário em uma região de recuperação alternativa. Se ocorrer uma interrupção, você rapidamente faz failover para essas réplicas para retomar as operações normais de negócios. No failover, os bancos de dados na região original se tornam réplicas secundárias dos bancos de dados na região de recuperação. Quando essas réplicas voltam a ficar online, elas são automaticamente atualizadas para o estado dos bancos de dados na região de recuperação. Após a interrupção ser resolvida, você faz failback para os bancos de dados na região de produção original.

Este tutorial explora os fluxos de trabalho de failover e failback. Vai aprender a:
> [!div class="checklist"]
> 
> * Sincronizar informações de configuração do banco de dados e do pool elástico no catálogo de locatários
> * Configurar um ambiente de recuperação em uma região alternativa, abrangendo aplicativos, servidores e pools
> * Usar a _replicação geográfica_ para replicar os bancos de dados de catálogo e locatário para a região de recuperação
> * Fazer failover dos bancos de dados do aplicativo e do catálogo e do locatário para a região de recuperação 
> * Posteriormente, faça failover do aplicativo, do catálogo e dos bancos de dados de locatário de volta para a região original após a interrupção ser resolvida
> * Atualizar o catálogo à medida que cada banco de dados de locatário faz failover para rastrear o local principal do banco de dados de cada locatário
> * Verifique se o aplicativo e o banco de dados de locatário primário estão sempre colocalizados na mesma região do Azure para reduzir a latência  
 

Antes de iniciar este tutorial, verifique se os seguintes pré-requisitos foram concluídos:
* O aplicativo de banco de dados por locatário SaaS Wingtip tickets foi implantado. Para implantar em menos de cinco minutos, consulte [implantar e explorar o aplicativo de banco de dados por locatário SaaS Wingtip tickets](saas-dbpertenant-get-started-deploy.md)  
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>Introdução ao padrão de recuperação de replicação geográfica

![Arquitetura de recuperação](media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)
 
A recuperação de desastres (DR) é uma consideração importante para muitos aplicativos, seja por motivos de conformidade ou continuidade de negócios. Caso haja uma interrupção prolongada do serviço, um plano de DR bem preparado pode minimizar a interrupção dos negócios. O uso da replicação geográfica fornece o menor RPO e RTO mantendo as réplicas de banco de dados em uma região de recuperação que pode passar por failover para a breve notificação.

Um plano de DR baseado na replicação geográfica consiste em três partes distintas:
* Configuração-criação e manutenção do ambiente de recuperação
* Recuperação-failover do aplicativo e dos bancos de dados para o ambiente de recuperação se ocorrer uma interrupção,
* Repatriação-failover do aplicativo e dos bancos de dados de volta para a região original depois que o aplicativo for resolvido 

Todas as partes precisam ser consideradas com cuidado, especialmente se estiverem operando em escala. Em geral, o plano deve atingir várias metas:

* Configurar
    * Estabeleça e mantenha um ambiente de imagem espelho na região de recuperação. Criar os pools elásticos e replicar quaisquer bancos de dados nesse ambiente de recuperação reserva a capacidade na região de recuperação. A manutenção desse ambiente inclui a replicação de novos bancos de dados de locatário à medida que eles são provisionados.  
* Recuperação
    * Onde um ambiente de recuperação reduzido para baixo é usado para minimizar os custos diários, os pools e os bancos de dados devem ser escalados verticalmente para adquirir capacidade operacional total na região de recuperação
    * Habilitar o provisionamento de novo locatário na região de recuperação assim que possível  
    * Ser otimizado para restaurar locatários em ordem de prioridade
    * Seja otimizado para obter os locatários online o mais rápido possível seguindo as etapas em paralelo, onde for prático
    * Ser resiliente a falhas, reiniciáveis e idempotentes
    * Será possível cancelar o processo em meados de vôo se a região original voltar a ficar online.
* Repatriação 
    * Faça failover de bancos de dados da região de recuperação para réplicas na região original com impacto mínimo sobre os locatários: nenhuma perda de dados e o período mínimo off-line por locatário.   

Neste tutorial, esses desafios são abordados usando os recursos do banco de dados SQL do Azure e a plataforma do Azure:

* [Azure Resource Manager modelos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), para reservar toda a capacidade necessária o mais rápido possível. Os modelos de Azure Resource Manager são usados para provisionar uma imagem espelho dos servidores de produção e pools elásticos na região de recuperação.
* [Replicação geográfica](sql-database-geo-replication-overview.md), para criar secundários somente leitura replicados assincronamente para todos os bancos de dados. Durante uma interrupção, você faz failover para as réplicas na região de recuperação.  Depois que a interrupção for resolvida, você realiza o failback para os bancos de dados na região original sem perda.
* Operações de failover [assíncrono](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) enviadas na ordem de prioridade de locatário para minimizar o tempo de failover para grandes números de bancos de dados.
* [Recursos de recuperação de gerenciamento de fragmento](sql-database-elastic-database-recovery-manager.md), para alterar as entradas do banco de dados no catálogo durante a recuperação e repatriação. Esses recursos permitem que o aplicativo se conecte a bancos de dados de locatário, independentemente do local, sem reconfigurar o aplicativo.
* [Aliases de DNS do SQL Server](dns-alias-overview.md), para habilitar o provisionamento contínuo de novos locatários, independentemente da região em que o aplicativo está operando. Os aliases DNS também são usados para permitir que o processo de sincronização de catálogo se conecte ao catálogo ativo, independentemente de sua localização.

## <a name="get-the-disaster-recovery-scripts"></a>Obter os scripts de recuperação de desastre 

> [!IMPORTANT]
> Assim como todos os scripts de gerenciamento da Wingtip tickets, os scripts de DR são de amostra de qualidade e não devem ser usados na produção. 

Os scripts de recuperação usados neste tutorial e no código-fonte do aplicativo Wingtip estão disponíveis no [repositório GitHub de banco de dados por locatário do Wingtip tickets SaaS](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/). Confira as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts de gerenciamento Wingtip tickets.

## <a name="tutorial-overview"></a>Descrição geral do tutorial
Neste tutorial, você primeiro usa a replicação geográfica para criar réplicas do aplicativo Wingtip tickets e seus bancos de dados em uma região diferente. Em seguida, você faz failover para essa região para simular a recuperação de uma interrupção. Ao concluir, o aplicativo estará totalmente funcional na região de recuperação.

Posteriormente, em uma etapa repatriação separada, você faz o failover dos bancos de dados de catálogo e locatário na região de recuperação para a região original. O aplicativo e os bancos de dados permanecem disponíveis em todo o repatriação. Ao concluir, o aplicativo estará totalmente funcional na região original.

> [!Note]
> O aplicativo é recuperado para a _região emparelhada_ da região em que o aplicativo é implantado. Para obter mais informações, consulte [regiões emparelhadas do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="review-the-healthy-state-of-the-application"></a>Examinar o estado de integridade do aplicativo

Antes de iniciar o processo de recuperação, examine o estado de integridade normal do aplicativo.
1. No navegador da Web, abra o Hub de eventos do Wingtip tickets (http://events.wingtip-dpt.&lt; usuário&gt;. trafficmanager.net-substitua &lt;&gt; de usuário pelo valor de usuário da implantação).
    * Role até a parte inferior da página e observe o nome e o local do servidor de catálogo no rodapé. O local é a região na qual você implantou o aplicativo.
    *Sugestão: Paire o rato sobre a localização para aumentar a exibição.* 
    ![Estado bom estado de funcionamento do hub de eventos na região original](media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Clique no locatário contoso Concert Hall e abra sua página de evento.
    * No rodapé, observe o nome do servidor de locatário. O local será o mesmo que o local do servidor de catálogo.

3. No [portal do Azure](https://portal.azure.com), abra o grupo de recursos no qual o aplicativo é implantado
    * Observe a região em que os servidores são implantados. 

## <a name="sync-tenant-configuration-into-catalog"></a>Sincronizar a configuração de locatário no catálogo

Nesta tarefa, você inicia um processo que sincroniza a configuração dos servidores, pools elásticos e bancos de dados no catálogo de locatários. O processo mantém essas informações atualizadas no catálogo.  O processo funciona com o catálogo ativo, seja na região original ou na região de recuperação. As informações de configuração são usadas como parte do processo de recuperação para garantir que o ambiente de recuperação seja consistente com o ambiente original e, posteriormente, durante a repatriação para garantir que a região original seja tornada consistente com as alterações feitas no ambiente de recuperação. O catálogo também é usado para controlar o estado de recuperação de recursos de locatário

> [!IMPORTANT]
> Para simplificar, o processo de sincronização e outros processos de recuperação e repatriação de longa execução são implementados nesses tutoriais como trabalhos locais do PowerShell ou sessões que são executadas no logon de usuário do cliente. Os tokens de autenticação emitidos quando você fizer logon expirarão após várias horas e os trabalhos falharão. Em um cenário de produção, processos de execução longa devem ser implementados como serviços confiáveis do Azure de algum tipo, sendo executados sob uma entidade de serviço. Consulte [usar Azure PowerShell para criar uma entidade de serviço com um certificado](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal).

1. No _ISE do PowerShell_, abra o arquivo. ..\Learning Modules\UserConfig.psm1. Substitua `<resourcegroup>` e `<user>` nas linhas 10 e 11 pelo valor usado quando você implantou o aplicativo.  Salve o arquivo!

2. No *ISE do PowerShell*, abra o script. ..\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 e defina:
    * **$DemoScenario = 1**, iniciar um trabalho em segundo plano que sincroniza o servidor de locatário e informações de configuração do pool no catálogo

3. Pressione **F5** para executar o script de sincronização. Uma nova sessão do PowerShell é aberta para sincronizar a configuração de recursos de locatário.
![processo de sincronização](media/saas-dbpertenant-dr-geo-replication/sync-process.png)

Deixe a janela do PowerShell em execução em segundo plano e continue com o restante do tutorial. 

> [!Note]
> O processo de sincronização se conecta ao catálogo por meio de um alias DNS. Esse alias é modificado durante a restauração e repatriação para apontar para o catálogo ativo. O processo de sincronização mantém o Catálogo atualizado com qualquer alteração de configuração de banco de dados ou pool feita na região de recuperação.  Durante a repatriação, essas alterações são aplicadas aos recursos equivalentes na região original.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>Criar réplicas de banco de dados secundárias na região de recuperação

Nesta tarefa, você inicia um processo que implanta uma instância de aplicativo duplicada e replica o catálogo e todos os bancos de dados de locatário para uma região de recuperação.

> [!Note]
> Este tutorial adiciona proteção de replicação geográfica ao aplicativo de exemplo Wingtip tickets. Em um cenário de produção para um aplicativo que usa a replicação geográfica, cada locatário seria provisionado com um banco de dados replicado geograficamente desde o início. Consulte [criando serviços altamente disponíveis usando o banco de dados SQL do Azure](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. No *ISE do PowerShell*, abra o script. ..\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 e defina os seguintes valores:
    * **$DemoScenario = 2**, criar ambiente de recuperação de imagem espelho e replicar bancos de dados de catálogo e locatário

2. Prima **F5** para executar o script. Uma nova sessão do PowerShell é aberta para criar as réplicas.
![processo de sincronização](media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>Examinar o estado normal do aplicativo

Neste ponto, o aplicativo está sendo executado normalmente na região original e agora é protegido pela replicação geográfica.  As réplicas secundárias somente leitura existem na região de recuperação para todos os bancos de dados. 

1. Na portal do Azure, examine os grupos de recursos e observe que um grupo de recursos foi criado com o sufixo-Recovery na região de recuperação. 

2. Explore os recursos no grupo de recursos de recuperação.  

3. Clique no banco de dados contoso Concert Hall no servidor _tenants1-DPT-&lt;usuário&gt;-Recovery_ .  Clique em replicação geográfica no lado esquerdo. 

    ![Link de replicação geográfica do contoso Concert](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

No mapa de regiões do Azure, observe o link de replicação geográfica entre o primário na região original e o secundário na região de recuperação.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>Fazer failover do aplicativo para a região de recuperação

### <a name="geo-replication-recovery-process-overview"></a>Visão geral do processo de recuperação de replicação geográfica

O script de recuperação executa as seguintes tarefas:

1. Desabilita o ponto de extremidade do Gerenciador de tráfego para o aplicativo Web na região original. Desabilitar o ponto de extremidade impede que os usuários se conectem ao aplicativo em um estado inválido caso a região original fique online durante a recuperação.

1. Usa um failover forçado do banco de dados de catálogo na região de recuperação para torná-lo o banco de dados primário e atualiza o alias _activecatalog_ para apontar para o servidor de catálogo de recuperação.

1. Atualiza o alias _Tenente_ para apontar para o servidor de locatário na região de recuperação. A alteração desse alias garante que os bancos de dados para quaisquer novos locatários sejam provisionados na região de recuperação. 

1. Marca todos os locatários existentes no catálogo de recuperação como offline para impedir o acesso a bancos de dados de locatário antes do failover.

1. Atualiza a configuração de todos os pools elásticos e os bancos de dados individuais replicados na região de recuperação para espelhar sua configuração na região original. (Essa tarefa só será necessária se os pools ou bancos de dados replicados no ambiente de recuperação forem reduzidos durante as operações normais para reduzir os custos).

1. Habilita o ponto de extremidade do Gerenciador de tráfego para o aplicativo Web na região de recuperação. Habilitar esse ponto de extremidade permite que o aplicativo provisione novos locatários. Neste estágio, os locatários existentes ainda estão offline.

1. Envia lotes de solicitações para forçar o failover de bancos de dados em ordem de prioridade.
    * Os lotes são organizados para que os bancos de dados tenham failover em paralelo em todos os pools.
    * As solicitações de failover são enviadas usando operações assíncronas para que sejam enviadas rapidamente e várias solicitações possam ser processadas simultaneamente.

   > [!Note]
   > Em um cenário de interrupção, os bancos de dados primários na região original ficam offline.  O failover forçado no secundário interrompe a conexão com o primário sem tentar aplicar nenhuma transação residual em fila. Em um cenário de análise de recuperação de desastre como este tutorial, se houver alguma atividade de atualização no momento do failover, pode haver alguma perda de dados. Posteriormente, durante a repatriação, quando você faz failover de bancos de dados na região de recuperação de volta para a região original, um failover normal é usado para garantir que não haja perda de dados.

1. Monitora o serviço do banco de dados SQL para determinar quando os bancos de dados passaram por failover. Após o failover de um banco de dados de locatário, ele atualiza o catálogo para registrar o estado de recuperação do banco de dados de locatário e marca o locatário como online.
    * Os bancos de dados de locatário podem ser acessados pelo aplicativo assim que são marcados online no catálogo.
    * Uma soma de valores de multiversão no banco de dados de locatário é armazenada no catálogo. Esse valor atua como uma impressão digital que permite que o processo repatriação determine se o banco de dados foi atualizado na região de recuperação.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>Executar o script para fazer failover para a região de recuperação

Agora imagine que haja uma interrupção na região em que o aplicativo é implantado e execute o script de recuperação:

1. No *ISE do PowerShell*, abra o script. ..\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 e defina os seguintes valores:
    * **$DemoScenario = 3**, recuperar o aplicativo em uma região de recuperação fazendo failover para réplicas

2. Prima **F5** para executar o script.  
    * O script é aberto em uma nova janela do PowerShell e, em seguida, inicia uma série de trabalhos do PowerShell que são executados em paralelo. Esses trabalhos realizam failover de bancos de dados de locatário para a região de recuperação.
    * A região de recuperação é a _região emparelhada_ associada à região do Azure na qual você implantou o aplicativo. Para obter mais informações, consulte [regiões emparelhadas do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Monitore o status do processo de recuperação na janela do PowerShell.
    ![processo de failover](media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> Para explorar o código dos trabalhos de recuperação, examine os scripts do PowerShell na pasta. ..\Learning Modules\Business continuidade e Recovery\DR-FailoverToReplica\RecoveryJobs de desastre.

### <a name="review-the-application-state-during-recovery"></a>Examinar o estado do aplicativo durante a recuperação

Enquanto o ponto de extremidade do aplicativo está desabilitado no Gerenciador de tráfego, o aplicativo não está disponível. Após o failover do catálogo para a região de recuperação e todos os locatários marcados como offline, o aplicativo volta a ficar online. Embora o aplicativo esteja disponível, cada locatário aparece offline no Hub de eventos até que seu banco de dados seja reprovado. É importante projetar seu aplicativo para lidar com bancos de dados de locatário offline.

1. Imediatamente após a recuperação do banco de dados do catálogo, atualize o Hub de eventos do Wingtip tickets em seu navegador da Web.
   * No rodapé, observe que o nome do servidor de catálogo agora tem um sufixo _-Recovery_ e está localizado na região de recuperação.
   * Observe que os locatários que ainda não foram restaurados, estão marcados como offline e não são selecionáveis.  

     > [!Note]
     > Com apenas alguns bancos de dados para recuperar, talvez você não consiga atualizar o navegador antes da conclusão da recuperação, para que você não veja os locatários enquanto estiverem offline. 
 
     ![Hub de eventos offline](media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

   * Se você abrir a página de eventos de um locatário offline diretamente, ele exibirá uma notificação de "locatário offline". Por exemplo, se contoso Concert Hall estiver offline, tente abrir http://events.wingtip-dpt.&lt; usuário&gt;. trafficmanager.net/contosoconcerthall ![página offline da Contoso](media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>Provisionar um novo locatário na região de recuperação
Mesmo antes do failover de todos os bancos de dados de locatário existentes, você pode provisionar novos locatários na região de recuperação.  

1. No *ISE do PowerShell*, abra o script. ..\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 e defina a seguinte propriedade:
    * **$DemoScenario = 4**, provisionar um novo locatário na região de recuperação

2. Pressione **F5** para executar o script e provisionar o novo locatário. 

3. A página de eventos do Hawthorn Hall é aberta no navegador quando ela é concluída. Observação do rodapé que o banco de dados do Hawthorn Hall é provisionado na região de recuperação.
    ![página de eventos do Hawthorn Hall](media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. No navegador, atualize a página do hub de eventos do Wingtip tickets para ver o Hawthorn Hall incluído. 
    * Se você provisionou o Hawthorn Hall sem esperar que os outros locatários restaurem, outros locatários ainda podem estar offline.


## <a name="review-the-recovered-state-of-the-application"></a>Examine o estado recuperado do aplicativo

Quando o processo de recuperação é concluído, o aplicativo e todos os locatários são totalmente funcionais na região de recuperação. 

1. Depois que a exibição na janela do console do PowerShell indicar que todos os locatários são recuperados, atualize o Hub de eventos.  Os locatários aparecerão online, incluindo o novo locatário, Hawthorn Hall.

    ![Locatários recuperados e novos no Hub de eventos](media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. Na [portal do Azure](https://portal.azure.com), abra a lista de grupos de recursos.  
    * Observe o grupo de recursos que você implantou, além do grupo de recursos de recuperação, com o sufixo _-Recovery_ .  O grupo de recursos de recuperação contém todos os recursos criados durante o processo de recuperação, além de novos recursos criados durante a interrupção.  

3. Abra o grupo de recursos de recuperação e observe os seguintes itens:
   * As versões de recuperação dos servidores de catálogo e tenants1, com o sufixo _-Recovery_ .  Todos os bancos de dados de catálogo e locatário restaurados nesses servidores têm os nomes usados na região original.

   * O _usuário do tenants2-DPT-&lt;&gt;-Recovery_ SQL Server.  Esse servidor é usado para provisionar novos locatários durante a interrupção.
   * O serviço de aplicativo chamado _Events-Wingtip-DPT-&lt;recoveryregion&gt;-&lt;usuário & gt_;, que é a instância de recuperação do aplicativo de eventos. 

     ![Recursos de recuperação do Azure](media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png) 
    
4. Abra o _usuário tenants2-DPT-&lt;&gt;-Recovery_ SQL Server.  Observe que ele contém o banco de dados _hawthornhall_ e o pool elástico, _Pool1_.  O banco de dados _hawthornhall_ é configurado como um banco de dados elástico no pool elástico do _Pool1_ .

5. Navegue de volta para o grupo de recursos e clique no banco de dados do contoso Concert Hall no servidor _tenants1-DPT-&lt;usuário&gt;-Recovery_ . Clique em replicação geográfica no lado esquerdo.
    
    ![Banco de dados contoso após failover](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Alterar dados de locatário 
Nesta tarefa, você atualiza um dos bancos de dados de locatário. 

1. Em seu navegador, encontre a lista de eventos para a sala de concerto da Contoso e anote o último nome do evento.
2. No *ISE do PowerShell*, no script. ..\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1, defina o seguinte valor:
    * **$DemoScenario = 5** Excluir um evento de um locatário na região de recuperação
3. Pressione **F5** para executar o script
4. Atualize a página de eventos do contoso Concert Hall (http://events.wingtip-dpt.&lt; User&gt;. trafficmanager.net/contosoconcerthall-substitua &lt;usuário&gt; pelo valor de usuário da implantação) e observe que o último evento foi excluído.

## <a name="repatriate-the-application-to-its-original-production-region"></a>Repatriar o aplicativo à sua região de produção original

Essa tarefa repatria o aplicativo à sua região original. Em um cenário real, você iniciaria repatriação quando a interrupção for resolvida.

### <a name="repatriation-process-overview"></a>Visão geral do processo de repatriação

![Arquitetura do repatriação](media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

O processo repatriação:
1. Cancela todas as solicitações de restauração de banco de dados pendentes ou em andamento.
2. Atualiza o alias _Tenente_ para apontar para o servidor dos locatários na região de origem. A alteração desse alias garante que os bancos de dados para todos os novos locatários agora serão provisionados na região de origem.
3. Propaga todos os dados de locatário alterados para a região original
4. Faz failover de bancos de dados de locatário em ordem de prioridade.

O failover move efetivamente o banco de dados para a região original. Quando o banco de dados falha, todas as conexões abertas são descartadas e o banco de dados fica indisponível por alguns segundos. Os aplicativos devem ser escritos com a lógica de repetição para garantir que eles se conectem novamente.  Embora essa breve desconexão geralmente não seja percebida, você pode optar por repatriarr os bancos de dados fora do horário comercial. 


### <a name="run-the-repatriation-script"></a>Executar o script repatriação
Agora vamos imaginar que a interrupção foi resolvida e execute o script repatriação.

1. No *ISE do PowerShell*, no script. ..\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1.

2. Verifique se o processo de sincronização do catálogo ainda está em execução em sua instância do PowerShell.  Se necessário, reinicie-o definindo:
    * **$DemoScenario = 1**, iniciar a sincronização de servidor de locatário, pool e informações de configuração do banco de dados no catálogo
    * Prima **F5** para executar o script.

3.  Em seguida, para iniciar o processo repatriação, defina:
    * **$DemoScenario = 6**, repatriar o aplicativo em sua região original
    * Pressione **F5** para executar o script de recuperação em uma nova janela do PowerShell.  O repatriação levará vários minutos e poderá ser monitorado na janela do PowerShell.
    ![processo repatriação](media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. Enquanto o script estiver em execução, atualize a página de Hub de eventos (http://events.wingtip-dpt.&lt; usuário&gt;. trafficmanager.net)
    * Observe que todos os locatários estão online e acessíveis durante esse processo.

5. Depois que o repatriação for concluído, atualize o Hub de eventos e abra a página de eventos do Hawthorn Hall. Observe que esse banco de dados foi repatriadodo para a região original.
    ![repatriado do hub de eventos](media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Criando o aplicativo para garantir que o aplicativo e o banco de dados estejam colocalizados 
O aplicativo foi projetado para que sempre se conecte de uma instância na mesma região que o banco de dados de locatário. Esse design reduz a latência entre o aplicativo e o banco de dados. Essa otimização pressupõe que a interação de aplicativo para banco de dados é chattier do que a interação entre o usuário e o aplicativo.  

Os bancos de dados de locatário podem ser distribuídos em regiões de recuperação e originais por algum tempo durante o repatriação. Para cada banco de dados, o aplicativo pesquisa a região na qual o banco de dados está localizado fazendo uma pesquisa de DNS no nome do servidor de locatário. No banco de dados SQL, o nome do servidor é um alias. O nome do servidor com alias contém o nome da região. Se o aplicativo não estiver na mesma região que o banco de dados, ele redirecionará para a instância na mesma região que o servidor de banco de dados.  O redirecionamento para a instância na mesma região que o banco de dados minimiza a latência entre o aplicativo e o banco de dados. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:
> [!div class="checklist"]
> 
> * Sincronizar informações de configuração do banco de dados e do pool elástico no catálogo de locatários
> * Configurar um ambiente de recuperação em uma região alternativa, abrangendo aplicativos, servidores e pools
> * Usar a _replicação geográfica_ para replicar os bancos de dados de catálogo e locatário para a região de recuperação
> * Fazer failover dos bancos de dados do aplicativo e do catálogo e do locatário para a região de recuperação 
> * Fazer failback do aplicativo, do catálogo e dos bancos de dados de locatário para a região original após a interrupção ser resolvida

Você pode saber mais sobre as tecnologias que o banco de dados SQL do Azure fornece para habilitar a continuidade de negócios na documentação de [visão geral da continuidade dos negócios](sql-database-business-continuity.md) .

## <a name="additional-resources"></a>Recursos adicionais

* [TUTORIAIS adicionais que se baseiam no aplicativo SaaS Wingtip](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
