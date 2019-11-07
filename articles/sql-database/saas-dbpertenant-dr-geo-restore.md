---
title: 'Aplicativos SaaS: backups com redundância geográfica do banco de dados SQL do Azure para recuperação de desastre '
description: Aprenda a usar backups com redundância geográfica do banco de dados SQL do Azure para recuperar um aplicativo SaaS multilocatário no caso de uma interrupção
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: craigg
ms.reviewer: sstein
ms.date: 01/14/2019
ms.openlocfilehash: 2f058a5cd20fff845a1feafe42b66beb1afef766
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692208"
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Usar a restauração geográfica para recuperar um aplicativo SaaS multilocatário de backups de banco de dados

Este tutorial explora um cenário de recuperação de desastres completo para um aplicativo SaaS multilocatário implementado com o modelo de banco de dados por locatário. Você usa a [restauração geográfica](sql-database-recovery-using-backups.md) para recuperar os bancos de dados de catálogo e locatário dos backups com redundância geográfica mantidos automaticamente em uma região de recuperação alternativa. Depois que a interrupção for resolvida, você usará a [replicação geográfica](sql-database-geo-replication-overview.md) para repatriar bancos de dados alterados para sua região original.

![Arquitetura de restauração geográfica](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

A restauração geográfica é a solução de recuperação de desastres de menor custo para o banco de dados SQL do Azure. No entanto, a restauração de backups com redundância geográfica pode resultar em perda de dados de até uma hora. Pode levar um tempo considerável, dependendo do tamanho de cada banco de dados. 

> [!NOTE]
> Recupere aplicativos com o menor RPO e RTO possíveis usando a replicação geográfica em vez de uma restauração geográfica.

Este tutorial explora os fluxos de trabalho de restauração e repatriação. Saiba como:
> [!div class="checklist"]
> 
> * Sincronize o banco de dados e as informações de configuração do pool elástico no catálogo de locatários.
> * Configure um ambiente de imagem espelho em uma região de recuperação que inclua aplicativos, servidores e pools.   
> * Recupere bancos de dados de catálogo e locatário usando a restauração geográfica.
> * Use a replicação geográfica para repatriar o catálogo de locatários e altere os bancos de dados de locatário depois que a interrupção for resolvida.
> * Atualize o catálogo, pois cada banco de dados é restaurado (ou repatriado) para rastrear o local atual da cópia ativa do banco de dados de cada locatário.
> * Verifique se o aplicativo e o banco de dados de locatário estão sempre colocalizados na mesma região do Azure para reduzir a latência. 
 

Antes de iniciar este tutorial, conclua os seguintes pré-requisitos:
* Implante o aplicativo de banco de dados por locatário SaaS Wingtip tickets. Para implantar em menos de cinco minutos, consulte [implantar e explorar o aplicativo de banco de dados por locatário SaaS Wingtip tickets](saas-dbpertenant-get-started-deploy.md). 
* Instale o Azure PowerShell. Para obter detalhes, consulte [introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Introdução ao padrão de recuperação de restauração geográfica

A recuperação de desastres (DR) é uma consideração importante para muitos aplicativos, seja por motivos de conformidade ou continuidade de negócios. Se houver uma interrupção prolongada de serviço, um plano de DR bem preparado poderá minimizar a interrupção dos negócios. Um plano de DR baseado na restauração geográfica deve atingir várias metas:
 * Reserve toda a capacidade necessária na região de recuperação escolhida o mais rapidamente possível para garantir que ela esteja disponível para restaurar bancos de dados de locatário.
 * Estabeleça um ambiente de recuperação de imagem espelhada que reflita o pool original e a configuração do banco de dados. 
 * Permitir cancelamento do processo de restauração em meados de vôo se a região original voltar a ficar online.
 * Habilite o provisionamento de locatário rapidamente para que a nova integração de locatário possa ser reiniciada assim que possível.
 * Ser otimizado para restaurar locatários em ordem de prioridade.
 * Seja otimizado para obter locatários online assim que possível, seguindo as etapas em paralelo, onde for prático.
 * Ser resiliente a falhas, reiniciáveis e idempotentes.
 * Repatriar bancos de dados para sua região original com impacto mínimo sobre os locatários quando a interrupção for resolvida.  

> [!NOTE]
> O aplicativo é recuperado para a região emparelhada da região em que o aplicativo é implantado. Para obter mais informações, consulte [regiões emparelhadas do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).   

Este tutorial usa recursos do banco de dados SQL do Azure e da plataforma Azure para resolver esses desafios:

* [Azure Resource Manager modelos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), para reservar toda a capacidade necessária o mais rápido possível. Os modelos de Azure Resource Manager são usados para provisionar uma imagem espelho dos servidores originais e pools elásticos na região de recuperação. Um servidor e pool separados também são criados para provisionar novos locatários.
* EDCL ( [biblioteca de cliente do banco de dados elástico](sql-database-elastic-database-client-library.md) ) para criar e manter um catálogo de banco de dados de locatário. O catálogo estendido inclui informações de configuração de pool e banco de dados atualizadas periodicamente.
* [Recursos de recuperação de gerenciamento de fragmento](sql-database-elastic-database-recovery-manager.md) do EDCL, para manter entradas de local de banco de dados no catálogo durante a recuperação e repatriação.  
* [Restauração geográfica](sql-database-disaster-recovery.md), para recuperar os bancos de dados de catálogo e locatário dos backups com redundância geográfica mantidos automaticamente. 
* [As operações de restauração assíncronas](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations), enviadas na ordem de prioridade de locatário, são enfileiradas para cada pool pelo sistema e processadas em lotes para que o pool não seja sobrecarregado. Essas operações podem ser canceladas antes ou durante a execução, se necessário.   
* [Replicação geográfica](sql-database-geo-replication-overview.md), para repatriar bancos de dados para a região original após a interrupção. Não há perda de dados e impacto mínimo sobre o locatário ao usar a replicação geográfica.
* [Aliases de DNS do SQL Server](dns-alias-overview.md), para permitir que o processo de sincronização de catálogo se conecte ao catálogo ativo, independentemente de sua localização.  

## <a name="get-the-disaster-recovery-scripts"></a>Obter os scripts de recuperação de desastre

Os scripts de DR usados neste tutorial estão disponíveis no [repositório GitHub de banco de dados por locatário do Wingtip tickets SaaS](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Confira as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts de gerenciamento Wingtip tickets.

> [!IMPORTANT]
> Assim como todos os scripts de gerenciamento da Wingtip tickets, os scripts de DR são de amostra de qualidade e não devem ser usados na produção.

## <a name="review-the-healthy-state-of-the-application"></a>Examinar o estado de integridade do aplicativo
Antes de iniciar o processo de recuperação, examine o estado de integridade normal do aplicativo.

1. No navegador da Web, abra o Hub de eventos do Wingtip tickets (http://events.wingtip-dpt.&lt; User&gt;. trafficmanager.net, substitua &lt;usuário&gt; pelo valor de usuário de sua implantação).
    
   Role até a parte inferior da página e observe o nome e o local do servidor de catálogo no rodapé. O local é a região na qual você implantou o aplicativo.    

   > [!TIP]
   > Passe o mouse sobre o local para ampliar a tela.

   ![Estado íntegro do hub de eventos na região original](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Selecione o locatário contoso Concert Hall e abra sua página de evento.

   No rodapé, observe o nome do servidor do locatário. O local é o mesmo que o local do servidor de catálogo.

   ![Região original da Contoso Concert Hall](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png) 

3. No [portal do Azure](https://portal.azure.com), examine e abra o grupo de recursos no qual você implantou o aplicativo.

   Observe os recursos e a região em que os componentes do serviço de aplicativo e os servidores de banco de dados SQL são implantados.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>Sincronizar a configuração do locatário no catálogo

Nesta tarefa, você inicia um processo para sincronizar a configuração dos servidores, pools elásticos e bancos de dados no catálogo de locatários. Essas informações são usadas posteriormente para configurar um ambiente de imagem espelho na região de recuperação.

> [!IMPORTANT]
> Para simplificar, o processo de sincronização e outros processos de recuperação e repatriação de longa execução são implementados nesses exemplos como trabalhos locais do PowerShell ou sessões que são executadas no logon de usuário do cliente. Os tokens de autenticação emitidos quando você faz logon expiram após várias horas, e os trabalhos falharão. Em um cenário de produção, processos de execução longa devem ser implementados como serviços confiáveis do Azure de algum tipo, sendo executados sob uma entidade de serviço. Consulte [usar Azure PowerShell para criar uma entidade de serviço com um certificado](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

1. No ISE do PowerShell, abra o arquivo. ..\Learning Modules\UserConfig.psm1. Substitua `<resourcegroup>` e `<user>` nas linhas 10 e 11 pelo valor usado quando você implantou o aplicativo. Guarde o ficheiro.

2. No ISE do PowerShell, abra o script. ..\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1.

    Neste tutorial, você executará cada um dos cenários neste script do PowerShell, portanto, mantenha esse arquivo aberto.

3. Defina o seguinte:

    $DemoScenario = 1: iniciar um trabalho em segundo plano que sincroniza as informações de configuração do servidor de locatário e do pool no catálogo.

4. Para executar o script de sincronização, selecione F5. 

    Essas informações são usadas posteriormente para garantir que a recuperação crie uma imagem espelhada dos servidores, pools e bancos de dados na região de recuperação.  

    ![Processo de sincronização](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Deixe a janela do PowerShell em execução em segundo plano e continue com o restante deste tutorial.

> [!NOTE]
> O processo de sincronização se conecta ao catálogo por meio de um alias DNS. O alias é modificado durante Restore e repatriação para apontar para o catálogo ativo. O processo de sincronização mantém o Catálogo atualizado com qualquer alteração de configuração de banco de dados ou pool feita na região de recuperação. Durante a repatriação, essas alterações são aplicadas aos recursos equivalentes na região original.

## <a name="geo-restore-recovery-process-overview"></a>Visão geral do processo de recuperação de restauração geográfica

O processo de recuperação de restauração geográfica implanta o aplicativo e restaura bancos de dados de backups para a região de recuperação.

O processo de recuperação faz o seguinte:

1. Desabilita o ponto de extremidade do Gerenciador de tráfego do Azure para o aplicativo Web na região original. Desabilitar o ponto de extremidade impede que os usuários se conectem ao aplicativo em um estado inválido caso a região original fique online durante a recuperação.

2. Provisiona um servidor de catálogo de recuperação na região de recuperação, restaura geograficamente o banco de dados de catálogo e atualiza o alias activecatalog para apontar para o servidor de catálogo restaurado. A alteração do alias do catálogo garante que o processo de sincronização do catálogo sempre seja sincronizado com o catálogo ativo.

3. Marca todos os locatários existentes no catálogo de recuperação como offline para impedir o acesso a bancos de dados de locatário antes de serem restaurados.

4. Provisiona uma instância do aplicativo na região de recuperação e a configura para usar o catálogo restaurado nessa região. Para manter a latência mínima, o aplicativo de exemplo é projetado para sempre se conectar a um banco de dados de locatário na mesma região.

5. Provisiona um servidor e um pool elástico no qual novos locatários são provisionados. A criação desses recursos garante que o provisionamento de novos locatários não interfira na recuperação de locatários existentes.

6. Atualiza o novo alias de locatário para apontar para o servidor para novos bancos de dados de locatário na região de recuperação. A alteração desse alias garante que os bancos de dados para quaisquer novos locatários sejam provisionados na região de recuperação.
        
7. Provisiona servidores e pools elásticos na região de recuperação para restaurar bancos de dados de locatário. Esses servidores e pools são uma imagem espelhada da configuração na região original. O provisionamento de pools front-end reserva a capacidade necessária para restaurar todos os bancos de dados.

    Uma interrupção em uma região pode posicionar uma pressão significativa nos recursos disponíveis na região emparelhada. Se você depender da restauração geográfica para DR, a reserva rápida de recursos é recomendada. Considere a replicação geográfica se for essencial que um aplicativo seja recuperado em uma região específica. 

8. Habilita o ponto de extremidade do Gerenciador de tráfego para o aplicativo Web na região de recuperação. Habilitar esse ponto de extremidade permite que o aplicativo provisione novos locatários. Neste estágio, os locatários existentes ainda estão offline.

9. Envia lotes de solicitações para restaurar bancos de dados em ordem de prioridade. 

    * Os lotes são organizados para que os bancos de dados sejam restaurados em paralelo em todos os pools.  

    * As solicitações de restauração são enviadas de forma assíncrona para que sejam enviadas rapidamente e enfileiradas para execução em cada pool.

    * Como as solicitações de restauração são processadas em paralelo em todos os pools, é melhor distribuir locatários importantes entre vários pools. 

10. Monitora o serviço do banco de dados SQL para determinar quando os bancos de dados são restaurados. Depois que um banco de dados de locatário é restaurado, ele é marcado como online no catálogo e uma soma de todas as versões para o banco de dados de locatário é registrada. 

    * Os bancos de dados de locatário podem ser acessados pelo aplicativo assim que são marcados online no catálogo.

    * Uma soma de valores de multiversão no banco de dados de locatário é armazenada no catálogo. Essa soma funciona como uma impressão digital que permite que o processo repatriação determine se o banco de dados foi atualizado na região de recuperação.       

## <a name="run-the-recovery-script"></a>Executar o script de recuperação

> [!IMPORTANT]
> Este tutorial restaura os bancos de dados de backups com redundância geográfica. Embora esses backups estejam normalmente disponíveis em 10 minutos, pode levar até uma hora. O script pausa até que esteja disponível.

Imagine que haja uma interrupção na região em que o aplicativo é implantado e execute o script de recuperação:

1. No ISE do PowerShell, no script. ..\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1, defina o seguinte valor:

    $DemoScenario = 2: recuperar o aplicativo em uma região de recuperação restaurando de backups com redundância geográfica.

2. Para executar o script, selecione F5.  

    * O script é aberto em uma nova janela do PowerShell e, em seguida, inicia um conjunto de trabalhos do PowerShell que são executados em paralelo. Esses trabalhos restauram servidores, pools e bancos de dados para a região de recuperação.

    * A região de recuperação é a região emparelhada associada à região do Azure na qual você implantou o aplicativo. Para obter mais informações, consulte [regiões emparelhadas do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Monitore o status do processo de recuperação na janela do PowerShell.

    ![Processo de recuperação](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> Para explorar o código dos trabalhos de recuperação, examine os scripts do PowerShell na pasta. ..\Learning Modules\Business continuidade e Recovery\DR-RestoreFromBackup\RecoveryJobs de desastre.

## <a name="review-the-application-state-during-recovery"></a>Examinar o estado do aplicativo durante a recuperação
Enquanto o ponto de extremidade do aplicativo está desabilitado no Gerenciador de tráfego, o aplicativo não está disponível. O catálogo é restaurado e todos os locatários são marcados como offline. O ponto de extremidade do aplicativo na região de recuperação é então habilitado e o aplicativo está novamente online. Embora o aplicativo esteja disponível, os locatários aparecem offline no Hub de eventos até que seus bancos de dados sejam restaurados. É importante projetar seu aplicativo para lidar com bancos de dados de locatário offline.

* Depois que o banco de dados do catálogo for recuperado, mas antes que os locatários fiquem online novamente, atualize o Hub de eventos do Wingtip tickets em seu navegador da Web.

  * No rodapé, observe que o nome do servidor de catálogo agora tem um sufixo-Recovery e está localizado na região de recuperação.

  * Observe que os locatários que ainda não foram restaurados são marcados como offline e não são selecionáveis.   
 
    ![Processo de recuperação](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

  * Se você abrir a página de eventos de um locatário diretamente enquanto o locatário estiver offline, a página exibirá uma notificação offline do locatário. Por exemplo, se contoso Concert Hall estiver offline, tente abrir http://events.wingtip-dpt.&lt; User&gt;. trafficmanager.net/contosoconcerthall.

    ![Processo de recuperação](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Provisionar um novo locatário na região de recuperação
Mesmo antes de os bancos de dados de locatário serem restaurados, você pode provisionar novos locatários na região de recuperação. Novos bancos de dados de locatário provisionados na região de recuperação são repatriado com os bancos de dados recuperados mais tarde.   

1. No ISE do PowerShell, no script. ..\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1, defina a seguinte propriedade:

    $DemoScenario = 3: provisionar um novo locatário na região de recuperação.

2. Para executar o script, selecione F5.

3. A página de eventos do Hawthorn Hall é aberta no navegador quando o provisionamento é concluído. 

    Observe que o banco de dados Hawthorn Hall está localizado na região de recuperação.

    ![Hawthorn Hall provisionado na região de recuperação](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. No navegador, atualize a página do hub de eventos do Wingtip tickets para ver o Hawthorn Hall incluído. 

    Se você provisionou o Hawthorn Hall sem esperar que os outros locatários restaurem, outros locatários ainda podem estar offline.

## <a name="review-the-recovered-state-of-the-application"></a>Examine o estado recuperado do aplicativo

Quando o processo de recuperação é concluído, o aplicativo e todos os locatários são totalmente funcionais na região de recuperação. 

1. Depois que a exibição na janela do console do PowerShell indicar que todos os locatários são recuperados, atualize o Hub de eventos. 

    Os locatários aparecem online, incluindo o novo locatário, Hawthorn Hall.

    ![Locatários recuperados e novos no Hub de eventos](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Clique em contoso Concert Hall e abra sua página de eventos. 

    No rodapé, observe que o banco de dados está localizado no servidor de recuperação localizado na região de recuperação.

    ![Contoso na região de recuperação](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. Na [portal do Azure](https://portal.azure.com), abra a lista de grupos de recursos.  

    Observe o grupo de recursos que você implantou, além do grupo de recursos de recuperação, com o sufixo-Recovery. O grupo de recursos de recuperação contém todos os recursos criados durante o processo de recuperação, além de novos recursos criados durante a interrupção. 

4. Abra o grupo de recursos de recuperação e observe os seguintes itens:

   * As versões de recuperação dos servidores de catálogo e tenants1, com o sufixo-Recovery. Todos os bancos de dados de catálogo e locatário restaurados nesses servidores têm os nomes usados na região original.

   * O usuário do tenants2-DPT-&lt;&gt;-Recovery SQL Server. Esse servidor é usado para provisionar novos locatários durante a interrupção.

   * O serviço de aplicativo nomeado Events-Wingtip-DPT-&lt;recoveryregion&gt;-&lt;&gt;de usuário, que é a instância de recuperação do aplicativo de eventos.

     ![Recursos da Contoso na região de recuperação](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Abra o usuário tenants2-DPT-&lt;&gt;-Recovery SQL Server. Observe que ele contém o banco de dados hawthornhall e o pool elástico Pool1. O banco de dados hawthornhall é configurado como um banco de dados elástico no pool elástico Pool1.

## <a name="change-the-tenant-data"></a>Alterar os dados do locatário 
Nesta tarefa, você atualiza um dos bancos de dados de locatário restaurados. O processo repatriação copia bancos de dados restaurados que foram alterados para a região original. 

1. No navegador, encontre a lista de eventos para a sala de concerto da Contoso, percorra os eventos e observe o último evento, seriamente Strauss.

2. No ISE do PowerShell, no script. ..\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1, defina o seguinte valor:

    $DemoScenario = 4: excluir um evento de um locatário na região de recuperação.

3. Para executar o script, selecione F5.

4. Atualize a página de eventos do contoso Concert Hall (http://events.wingtip-dpt.&lt; User&gt;. trafficmanager.net/contosoconcerthall) e observe que o evento Strauss seriamente está ausente.

Neste ponto do tutorial, você recuperou o aplicativo, que agora está em execução na região de recuperação. Você provisionou um novo locatário na região de recuperação e modificou dados de um dos locatários restaurados.  

> [!NOTE]
> Outros tutoriais no exemplo não são projetados para execução com o aplicativo no estado de recuperação. Se você quiser explorar outros tutoriais, certifique-se de repatriar o aplicativo primeiro.

## <a name="repatriation-process-overview"></a>Visão geral do processo de repatriação

O processo repatriação reverte o aplicativo e seus bancos de dados para sua região original após uma interrupção ser resolvida.

![Repatriação de restauração geográfica](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 

O processo:

1. Interrompe qualquer atividade de restauração em andamento e cancela todas as solicitações de restauração de banco de dados pendentes ou em andamento.

2. Reativa os bancos de dados de locatário da região original que não foram alterados desde a interrupção. Esses bancos de dados incluem aqueles ainda não recuperados e os recuperados, mas não alterados posteriormente. Os bancos de dados reativados são exatamente o último acessados por seus locatários.

3. Provisiona uma imagem espelhada do servidor do novo locatário e do pool elástico na região original. Depois que essa ação for concluída, o novo alias do locatário será atualizado para apontar para esse servidor. A atualização do alias faz com que a integração do novo locatário ocorra na região original em vez da região de recuperação.

3. Usa a replicação geográfica para mover o catálogo para a região original da região de recuperação.

4. Atualiza a configuração do pool na região original para que ela seja consistente com as alterações feitas na região de recuperação durante a interrupção.

5. Cria os servidores e pools necessários para hospedar quaisquer bancos de dados novos criados durante a interrupção.

6. Usa a replicação geográfica para repatriar bancos de dados de locatário restaurados que foram atualizados após a restauração e todos os novos bancos de dados de locatário provisionados durante a interrupção. 

7. Limpa os recursos criados na região de recuperação durante o processo de restauração.

Para limitar o número de bancos de dados de locatário que precisam ser repatriado, as etapas 1 a 3 são feitas imediatamente.  

A etapa 4 só será feita se o catálogo na região de recuperação tiver sido modificado durante a interrupção. O catálogo será atualizado se novos locatários forem criados ou se qualquer configuração de banco de dados ou pool for alterada na região de recuperação.

É importante que a etapa 7 cause interrupção mínima para locatários e nenhum dado seja perdido. Para atingir essa meta, o processo usa a replicação geográfica.

Antes de cada banco de dados ser replicado geograficamente, o banco de dados correspondente na região original é excluído. Em seguida, o banco de dados na região de recuperação é replicado geograficamente, criando uma réplica secundária na região original. Após a conclusão da replicação, o locatário é marcado como offline no catálogo, o que interrompe todas as conexões com o banco de dados na região de recuperação. O banco de dados passará por failover, fazendo com que todas as transações pendentes sejam processadas no secundário, portanto, nenhum dado será perdido. 

No failover, as funções de banco de dados são revertidas. O secundário na região original torna-se o banco de dados de leitura/gravação primário e o banco de dados na região de recuperação se torna um secundário somente leitura. A entrada do locatário no catálogo é atualizada para referenciar o banco de dados na região original e o locatário está marcado como online. Neste ponto, o repatriação do banco de dados foi concluído. 

Os aplicativos devem ser escritos com a lógica de repetição para garantir que eles se reconectem automaticamente quando as conexões forem interrompidas. Quando eles usam o catálogo para o agente da reconexão, eles se conectam ao banco de dados repatriado na região original. Embora a breve desconexão geralmente não seja percebida, você pode optar por repatriar bancos de dados fora do horário comercial.

Depois que um banco de dados é repatriado, o banco de dados secundário na região de recuperação pode ser excluído. O banco de dados na região original depende novamente da restauração geográfica para a proteção de DR.

Na etapa 8, os recursos na região de recuperação, incluindo os servidores e pools de recuperação, são excluídos.

## <a name="run-the-repatriation-script"></a>Executar o script repatriação
Vamos imaginar que a interrupção foi resolvida e execute o script repatriação.

Se você seguiu o tutorial, o script reativa imediatamente o Fabrikam Jazz Club e o Dogwood dojo na região original porque eles não são alterados. Em seguida, ele repatria o novo locatário, Hawthorn Hall e contoso Concert Hall, pois ele foi modificado. O script também repatria o catálogo, que foi atualizado quando o Hawthorn Hall foi provisionado.
  
1. No ISE do PowerShell, no script. ..\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1, verifique se o processo de sincronização do catálogo ainda está em execução em sua instância do PowerShell. Se necessário, reinicie-o definindo:

    $DemoScenario = 1: iniciar a sincronização das informações de configuração do servidor de locatário, do pool e do banco de dados no catálogo.

    Para executar o script, selecione F5.

2.  Em seguida, para iniciar o processo repatriação, defina:

    $DemoScenario = 5: repatriar o aplicativo em sua região original.

    Para executar o script de recuperação em uma nova janela do PowerShell, selecione F5. O repatriação leva vários minutos e pode ser monitorado na janela do PowerShell.

3. Enquanto o script estiver em execução, atualize a página Hub de eventos (http://events.wingtip-dpt.&lt; usuário&gt;. trafficmanager.net).

    Observe que todos os locatários estão online e acessíveis durante esse processo.

4. Selecione o Fabrikam Jazz Club para abri-lo. Se você não modificou esse locatário, observe no rodapé que o servidor já foi revertido para o servidor original.

5. Abra ou atualize a página de eventos do contoso Concert Hall. Observe o rodapé que, inicialmente, o banco de dados ainda está no servidor de recuperação. 

6. Atualize a página de eventos do contoso Concert Hall quando o processo repatriação for concluído e observe que o banco de dados agora está em sua região original.

7. Atualize o Hub de eventos novamente e abra o Hawthorn Hall. Observe que seu banco de dados também está localizado na região original. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Limpar recursos de região de recuperação após repatriação
Após a conclusão do repatriação, é seguro excluir os recursos na região de recuperação. 

> [!IMPORTANT]
> Exclua esses recursos imediatamente para interromper toda a cobrança para eles.

O processo de restauração cria todos os recursos de recuperação em um grupo de recursos de recuperação. O processo de limpeza exclui esse grupo de recursos e remove todas as referências aos recursos do catálogo. 

1. No ISE do PowerShell, no script. ..\Learning Modules\Business Continuity and Disaster Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1, defina:
    
    $DemoScenario = 6: exclua os recursos obsoletos da região de recuperação.

2. Para executar o script, selecione F5.

Depois de limpar os scripts, o aplicativo volta de onde ele foi iniciado. Neste ponto, você pode executar o script novamente ou experimentar outros tutoriais.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>Criando o aplicativo para garantir que o aplicativo e o banco de dados estejam colocalizados 
O aplicativo é projetado para sempre se conectar de uma instância na mesma região que o banco de dados do locatário. Esse design reduz a latência entre o aplicativo e o banco de dados. Essa otimização pressupõe que a interação de aplicativo para banco de dados é chattier do que a interação entre o usuário e o aplicativo.  

Os bancos de dados de locatário podem ser distribuídos em regiões de recuperação e originais por algum tempo durante o repatriação. Para cada banco de dados, o aplicativo pesquisa a região na qual o banco de dados está localizado fazendo uma pesquisa de DNS no nome do servidor de locatário. No banco de dados SQL, o nome do servidor é um alias. O nome do servidor com alias contém o nome da região. Se o aplicativo não estiver na mesma região que o banco de dados, ele redirecionará para a instância na mesma região que o servidor de banco de dados. O redirecionamento para a instância na mesma região que o banco de dados minimiza a latência entre o aplicativo e o banco de dados.  

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:
> [!div class="checklist"]
> 
> * Use o catálogo de locatários para manter as informações de configuração atualizadas periodicamente, o que permite que um ambiente de recuperação de imagem espelhada seja criado em outra região.
> * Recupere bancos de dados SQL do Azure para a região de recuperação usando a restauração geográfica.
> * Atualize o catálogo de locatários para refletir locais de banco de dados de locatário restaurados. 
> * Use um alias DNS para permitir que um aplicativo se conecte ao catálogo de locatários sem reconfiguração.
> * Use a replicação geográfica para repatriarr bancos de dados recuperados para sua região original após uma interrupção ser resolvida.

Experimente a [recuperação de desastre para um aplicativo SaaS multilocatário usando o tutorial de replicação geográfica de banco de dados](saas-dbpertenant-dr-geo-replication.md) para aprender a usar a replicação geográfica para reduzir drasticamente o tempo necessário para recuperar um aplicativo multilocatário de grande escala.

## <a name="additional-resources"></a>Recursos adicionais

[TUTORIAIS adicionais que se baseiam no aplicativo SaaS Wingtip](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
