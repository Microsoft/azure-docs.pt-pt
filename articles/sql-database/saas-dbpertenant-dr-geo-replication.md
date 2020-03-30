---
title: Recuperação de desastres para aplicações SaaS com Geo Replication
description: Saiba como usar as georéplicas da Base de Dados Azure SQL para recuperar uma aplicação SaaS multi-inquilino em caso de paragem
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73811710"
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Recuperação de desastres para uma aplicação SaaS multi-inquilino usando geo-replicação de base de dados

Neste tutorial, você explora um cenário completo de recuperação de desastres para uma aplicação SaaS multi-inquilino implementada usando o modelo base de dados por inquilino. Para proteger a aplicação de uma paragem, você usa [_geo-replicação_](sql-database-geo-replication-overview.md) para criar réplicas para o catálogo e bases de dados de inquilinos em uma região de recuperação alternativa. Se ocorrer uma paragem, rapidamente falha estas réplicas para retomar as operações normais de negócio. No caso do fracasso, as bases de dados da região original tornam-se réplicas secundárias das bases de dados da região de recuperação. Uma vez que estas réplicas voltam a funcionar, eles automaticamente alcançam o estado das bases de dados na região de recuperação. Após a paralisação ser resolvida, você falha de volta às bases de dados da região de produção original.

Este tutorial explora tanto os fluxos de trabalho failover e failback. Vai aprender a:
> [!div class="checklist"]
> 
> * Base de dados de sincronização e informações de configuração de piscina elástica no catálogo de inquilinos
> * Criar um ambiente de recuperação numa região alternativa, composto por aplicações, servidores e piscinas
> * Utilize _a geo-replicação_ para replicar as bases de dados do catálogo e dos inquilinos para a região de recuperação
> * Falhar na aplicação e catálogo e bases de dados de inquilinos para a região de recuperação 
> * Mais tarde, falhe sobre a aplicação, catalogar e inquilinos bases de dados de volta para a região original após a paralisação ser resolvida
> * Atualize o catálogo à medida que cada base de dados de inquilinos não consegue rastrear a localização primária da base de dados de cada inquilino
> * Certifique-se de que a aplicação e a base de dados de inquilinos primários estão sempre colocalizadas na mesma região de Azure para reduzir a latência  
 

Antes de iniciar este tutorial, certifique-se de que os seguintes pré-requisitos estão preenchidos:
* A base de dados Wingtip Tickets SaaS por aplicação de inquilino sé implementada. Para implantar em menos de cinco minutos, consulte Implementar e explorar a base de [dados Wingtip Tickets SaaS por aplicação de inquilino](saas-dbpertenant-get-started-deploy.md)  
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>Introdução ao padrão de recuperação da geo-replicação

![Arquitetura de Recuperação](media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)
 
A recuperação de desastres (DR) é uma consideração importante para muitas aplicações, seja por razões de conformidade ou continuidade do negócio. Se houver uma interrupção prolongada do serviço, um plano DR bem preparado pode minimizar a perturbação do negócio. A utilização da geo-replicação fornece o RPO e o RTO mais baixos, mantendo réplicas de bases de dados numa região de recuperação que pode ser falhada a curto prazo.

Um plano DR baseado na geo-replicação compreende três partes distintas:
* Configuração - criação e manutenção do ambiente de recuperação
* Recuperação - falha da app e bases de dados para o ambiente de recuperação se ocorrer uma interrupção,
* Repatriamento - falha da app e bases de dados de volta à região original uma vez que a aplicação é resolvida 

Todas as peças têm de ser consideradas cuidadosamente, especialmente se operarem à escala. No geral, o plano deve atingir vários objetivos:

* Configuração
    * Estabeleça e mantenha um ambiente de imagem espelhada na região de recuperação. A criação de piscinas elásticas e a replicação de quaisquer bases de dados neste ambiente de recuperação reservam capacidade na região de recuperação. A manutenção deste ambiente inclui a replicação de novas bases de dados de inquilinos à medida que são provisionadas.  
* Recuperação
    * Sempre que se possa utilizar um ambiente de recuperação reduzido para minimizar os custos do dia-a-dia, as piscinas e as bases de dados devem ser dimensionadas para adquirir a plena capacidade operacional na região de recuperação
    * Permitir o fornecimento de novos inquilinos na região de recuperação o mais rapidamente possível  
    * Ser otimizado para restaurar inquilinos em ordem prioritária
    * Ser otimizado para colocar os inquilinos on-line o mais rápido possível, fazendo passos em paralelo onde prático
    * Seja resiliente ao fracasso, reinufrável e idempotente
    * Seja possível cancelar o processo a meio do voo se a região original voltar on-line.
* Repatriamento 
    * Falhar as bases de dados da região de recuperação para réplicas na região original com o mínimo impacto para os inquilinos: sem perda de dados e período mínimo fora da linha por inquilino.   

Neste tutorial, estes desafios são abordados utilizando funcionalidades da Base de Dados Azure SQL e da plataforma Azure:

* Modelos de Gestor de [Recursos Azure,](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)para reservar toda a capacidade necessária o mais rápido possível. Os modelos do Gestor de Recursos Azure são usados para fornecer uma imagem espelhada dos servidores de produção e piscinas elásticas na região de recuperação.
* [Geo-replicação,](sql-database-geo-replication-overview.md)para criar secundários de leitura apenas replicados assincronicamente para todas as bases de dados. Durante uma paragem, falha-se com as réplicas na região de recuperação.  Após a paralisação ser resolvida, você falha de volta às bases de dados da região original sem perda de dados.
* Operações de failover [assíncronas enviadas](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) por ordem de prioridade dos inquilinos, para minimizar o tempo de falha para um grande número de bases de dados.
* Funcionalidades de recuperação de [gestão de fragmentos,](sql-database-elastic-database-recovery-manager.md)para alterar as entradas de base de dados no catálogo durante a recuperação e repatriamento. Estas funcionalidades permitem que a aplicação se conectem às bases de dados dos inquilinos, independentemente da localização sem reconfigurar a aplicação.
* [Pseudónimos dNS do servidor SQL,](dns-alias-overview.md)para permitir o fornecimento sem emenda de novos inquilinos, independentemente da região em que a aplicação esteja a operar. Os pseudónimos do DNS também são utilizados para permitir que o processo de sincronização do catálogo se conectem ao catálogo ativo, independentemente da sua localização.

## <a name="get-the-disaster-recovery-scripts"></a>Obtenha os scripts de recuperação de desastres 

> [!IMPORTANT]
> Como todos os scripts de gestão de Bilhetes Wingtip, os scripts DR são de qualidade de amostra e não são utilizados na produção. 

Os scripts de recuperação utilizados neste tutorial e código de origem da aplicação Wingtip estão disponíveis na base de [dados Wingtip Tickets SaaS por inquilino GitHub repositório](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/). Confira as [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para os passos para descarregar e desbloquear os scripts de gestão de Bilhetes Wingtip.

## <a name="tutorial-overview"></a>Descrição geral do tutorial
Neste tutorial, você usa primeiro a geo-replicação para criar réplicas da aplicação Wingtip Tickets e suas bases de dados em uma região diferente. Depois, falha-se nesta região para simular a recuperação de uma paragem. Quando concluída, a aplicação está totalmente funcional na região de recuperação.

Mais tarde, num passo de repatriamento separado, falha-se nas bases de dados do catálogo e dos inquilinos na região de recuperação para a região original. A aplicação e as bases de dados permanecem disponíveis durante todo o repatriamento. Quando concluída, a aplicação está totalmente funcional na região original.

> [!Note]
> O pedido é recuperado na _região emparelhada_ da região em que o pedido é implementado. Para mais informações, consulte [regiões emparelhadas de Azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)

## <a name="review-the-healthy-state-of-the-application"></a>Rever o estado saudável da aplicação

Antes de iniciar o processo de recuperação, reveja o estado normal e saudável da aplicação.
1. No seu navegador web, abra ohttp://events.wingtip-dpt.&ltWingtip&gt;Tickets Events &lt;&gt; Hub (.utilizador .trafficmanager.net - substitua o utilizador pelo valor de utilizador da sua implementação).
    * Percorra a parte inferior da página e note o nome e localização do servidor do catálogo no rodapé. A localização é a região em que implementou a aplicação.
    *DICA: Passe o rato sobre o local para ampliar o visor.* 
    Eventos hub estado saudável na região ![original](media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Clique no inquilino da Sala de Concertos Contoso e abra a sua página de eventos.
    * No rodapé, repare no nome do servidor do inquilino. A localização será a mesma que a localização do servidor do catálogo.

3. No [portal Azure,](https://portal.azure.com)abra o grupo de recursos em que a app é implantada
    * Note a região em que os servidores são implantados. 

## <a name="sync-tenant-configuration-into-catalog"></a>Sincronizar a configuração do inquilino no catálogo

Nesta tarefa, inicia-se um processo que sincroniza a configuração dos servidores, piscinas elásticas e bases de dados no catálogo de inquilinos. O processo mantém esta informação atualizada no catálogo.  O processo funciona com o catálogo ativo, seja na região original ou na região de recuperação. A informação de configuração é usada como parte do processo de recuperação para garantir que o ambiente de recuperação é consistente com o ambiente original, e mais tarde durante o repatriamento para garantir que a região original é feita consistente com quaisquer alterações feitas no ambiente de recuperação. O catálogo também é usado para acompanhar o estado de recuperação dos recursos dos inquilinos

> [!IMPORTANT]
> Para a simplicidade, o processo de sincronização e outros processos de recuperação e repatriamento de longa duração são implementados nestes tutoriais como empregos ou sessões locais da PowerShell que funcionam sob o login do utilizador do seu cliente. Os tokens de autenticação emitidos quando iniciar sessão expirarão após várias horas e os trabalhos falharão. Num cenário de produção, os processos de longo prazo devem ser implementados como serviços azure fiáveis de algum tipo, funcionando sob um diretor de serviço. Consulte [a Utilização do PowerShell Azure para criar um diretor](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)de serviço com um certificado .

1. No _PowerShell ISE,_ abra o ficheiro ...\Learning Modules\UserConfig.psm1. `<resourcegroup>` Substitua `<user>` e nas linhas 10 e 11 com o valor utilizado quando implementou a aplicação.  Guarde o ficheiro!

2. No *PowerShell ISE,* abra o script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 e definido:
    * **$DemoScenario = 1**, Inicie um trabalho de fundo que sincroniza o servidor de inquilinos e a informação de configuração da piscina no catálogo

3. Pressione **F5** para executar o script de sincronização. Uma nova sessão powerShell é aberta para sincronizar a configuração dos recursos dos inquilinos.
![Processo de sincronização](media/saas-dbpertenant-dr-geo-replication/sync-process.png)

Deixe a janela PowerShell em segundo plano e continue com o resto do tutorial. 

> [!Note]
> O processo de sincronização liga-se ao catálogo através de um pseudónimo DNS. Este pseudónimo é modificado durante a restauração e repatriamento para apontar para o catálogo ativo. O processo de sincronização mantém o catálogo atualizado com qualquer base de dados ou alterações de configuração do pool feitas na região de recuperação.  Durante o repatriamento, estas alterações são aplicadas aos recursos equivalentes na região original.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>Criar réplicas de bases de dados secundárias na região de recuperação

Nesta tarefa, inicia-se um processo que implementa uma instância de aplicações duplicada e replica o catálogo e todas as bases de dados dos inquilinos para uma região de recuperação.

> [!Note]
> Este tutorial adiciona proteção de geo-replicação à aplicação de amostra de bilhetes wingtip. Num cenário de produção para uma aplicação que utiliza a geo-replicação, cada inquilino seria aprovisionado com uma base de dados geo-replicada desde o início. Consulte [design de serviços altamente disponíveis utilizando base de dados Azure SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. No *PowerShell ISE,* abra o ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 e definir os seguintes valores:
    * **$DemoScenario = 2**, Criar ambiente de recuperação de imagem espelhada e replicar bases de dados de catálogos e inquilinos

2. Prima **F5** para executar o script. Está aberta uma nova sessão powerShell para criar as réplicas.
![Processo de sincronização](media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>Rever o estado normal de candidatura

Neste momento, a aplicação está a funcionar normalmente na região original e está agora protegida por geo-replicação.  As réplicas secundárias apenas de leitura existem na região de recuperação para todas as bases de dados. 

1. No portal Azure, olhe para os seus grupos de recursos e note que foi criado um grupo de recursos com sufixo de recuperação na região de recuperação. 

2. Explore os recursos do grupo de recursos de recuperação.  

3. Clique na base de dados da Sala de Concertos Contoso no servidor de recuperação de _utilizadores dos&lt;inquilinos1-dpt.&gt;_  Clique na Geo-Replicação no lado esquerdo. 

    ![Ligação de geo-replicação do Concerto Contoso](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

No mapa das regiões de Azure, note-se a ligação geo-replicação entre as primárias da região original e a secundária na região de recuperação.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>Falhar na aplicação na região de recuperação

### <a name="geo-replication-recovery-process-overview"></a>Visão geral do processo de recuperação da geo-replicação

O script de recuperação executa as seguintes tarefas:

1. Desativa o ponto final do Gestor de Tráfego para a aplicação web na região original. Desativar o ponto final impede que os utilizadores se conectem à aplicação em estado inválido caso a região original fique online durante a recuperação.

1. Utiliza uma falha de força na base de dados do catálogo na região de recuperação para torná-la a base de dados primária, e atualiza o pseudónimo _do catálogo ativo_ para apontar para o servidor de catálogo de recuperação.

1. Atualiza o pseudónimo _do novo inquilino_ para apontar para o servidor de inquilinos na região de recuperação. A alteração deste pseudónimo garante que as bases de dados para quaisquer novos inquilinos estão aprovisionadas na região de recuperação. 

1. Marca todos os inquilinos existentes no catálogo de recuperação como offline para impedir o acesso às bases de dados dos inquilinos antes de serem falhados.

1. Atualiza a configuração de todas as piscinas elásticas e bases de dados únicas replicadas na região de recuperação para espelhar a sua configuração na região original. (Esta tarefa só é necessária se os pools ou bases de dados replicadas no ambiente de recuperação forem reduzidos durante as operações normais para reduzir os custos).

1. Permite o ponto final do Gestor de Tráfego para a aplicação web na região de recuperação. Ativar este ponto final permite a aplicação para aprovisionamento de novos inquilinos. Nesta fase, os inquilinos existentes ainda estão offline.

1. Submete lotes de pedidos para forçar falha sobre bases de dados em ordem prioritária.
    * Os lotes são organizados para que as bases de dados sejam falhadas em paralelo em todas as piscinas.
    * Os pedidos de failover são submetidos usando operações assíncronas para que sejam submetidos rapidamente e vários pedidos podem ser processados simultaneamente.

   > [!Note]
   > Num cenário de paralisação, as bases de dados primárias da região original estão offline.  A falha da força no secundário rompe a ligação com a primária sem tentar aplicar quaisquer transações residuais em fila. Num cenário de perfuração DR como este tutorial, se houver alguma atividade de atualização no momento da falha pode haver alguma perda de dados. Mais tarde, durante o repatriamento, quando falha as bases de dados na região de recuperação de volta à região original, uma falha normal é usada para garantir que não há perda de dados.

1. Monitoriza o serviço de base de dados SQL para determinar quando as bases de dados foram reprovadas. Uma vez que uma base de dados de inquilinos é falhada, atualiza o catálogo para registar o estado de recuperação da base de dados do inquilino e marcar o inquilino como on-line.
    * As bases de dados dos inquilinos podem ser acedidas pela aplicação assim que estiverem marcadas online no catálogo.
    * Uma soma de valores de versão de linha na base de dados dos inquilinos está armazenada no catálogo. Este valor funciona como uma impressão digital que permite ao processo de repatriamento determinar se a base de dados foi atualizada na região de recuperação.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>Executar o guião para falhar na região de recuperação

Agora imagine que há uma falha na região em que a aplicação é implementada e executar o script de recuperação:

1. No *PowerShell ISE,* abra o ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 e definir os seguintes valores:
    * **$DemoScenario = 3**, Recuperar a app numa região de recuperação falhando nas réplicas

2. Prima **F5** para executar o script.  
    * O guião abre numa nova janela powerShell e inicia uma série de trabalhos powerShell que funcionam em paralelo. Estes empregos falham nas bases de dados dos inquilinos para a região de recuperação.
    * A região de recuperação é a _região emparelhada_ associada à região de Azure em que implementou a aplicação. Para mais informações, consulte [regiões emparelhadas de Azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) 

3. Monitorize o estado do processo de recuperação na janela PowerShell.
    ![processo failover](media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> Para explorar o código para os trabalhos de recuperação, reveja os scripts PowerShell na pasta ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\RecoveryJobs.

### <a name="review-the-application-state-during-recovery"></a>Rever o estado de candidatura durante a recuperação

Enquanto o ponto final da aplicação está desativado no Traffic Manager, a aplicação não está disponível. Depois de o catálogo ter falhado na região de recuperação e de todos os inquilinos marcados offline, a aplicação é reposta online. Embora a aplicação esteja disponível, cada inquilino aparece offline no centro de eventos até que a sua base de dados seja falhada. É importante projetar a sua aplicação para lidar com bases de dados de inquilinos offline.

1. Imediatamente após a recuperação da base de dados do catálogo, refresque o Wingtip Tickets Events Hub no seu navegador web.
   * No rodapé, note que o nome do servidor do catálogo tem agora um sufixo _de recuperação_ e está localizado na região de recuperação.
   * Note que os inquilinos que ainda não estão restaurados, estão marcados como offline, e não são selecionáveis.  

     > [!Note]
     > Com apenas algumas bases de dados para recuperar, poderá não conseguir atualizar o navegador antes de a recuperação estar concluída, pelo que poderá não ver os inquilinos enquanto estiverem offline. 
 
     ![Eventos hub offline](media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

   * Se abrir a página de Eventos de um inquilino offline diretamente, exibe uma notificação de "inquilino offline". Por exemplo, se a Sala de Concertos&gt;Contoso ![estiver offline, tente abrir http://events.wingtip-dpt.&lt(utilizador).trafficmanager.net/contosoconcerthall página Offline Contoso](media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>Provisão de um novo inquilino na região de recuperação
Mesmo antes de todas as bases de dados de inquilinos existentes terem falhado, você pode fornecer novos inquilinos na região de recuperação.  

1. No *PowerShell ISE,* abra o ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 e desloque a seguinte propriedade:
    * **$DemoScenario = 4**, Provisão de um novo inquilino na região de recuperação

2. Pressione **F5** para executar o guião e providenciar o novo inquilino. 

3. A página de eventos hawthorn Hall abre no navegador quando estiver concluída. Nota do rodapé que a base de dados hawthorn Hall está aprovisionada na região de recuperação.
    ![Página de eventos do Hawthorn Hall](media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. No navegador, refresque a página Wingtip Tickets Events Hub para ver Hawthorn Hall incluído. 
    * Se você forprovisionou Hawthorn Hall sem esperar que os outros inquilinos restaurem, outros inquilinos ainda podem estar offline.


## <a name="review-the-recovered-state-of-the-application"></a>Rever o estado recuperado do pedido

Quando o processo de recuperação estiver concluído, a candidatura e todos os inquilinos estão totalmente funcionais na região de recuperação. 

1. Uma vez que o visor na janela da consola PowerShell indique que todos os inquilinos são recuperados, refresque o Centro de Eventos.  Os inquilinos vão aparecer todos online, incluindo o novo inquilino, Hawthorn Hall.

    ![recuperadoe novos inquilinos no centro de eventos](media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. No [portal Azure,](https://portal.azure.com)abra a lista de grupos de recursos.  
    * Note o grupo de recursos que implementou, mais o grupo de recursos de recuperação, com o sufixo _de recuperação._  O grupo de recursos de recuperação contém todos os recursos criados durante o processo de recuperação, além de novos recursos criados durante a paralisação.  

3. Abra o grupo de recursos de recuperação e repare nos seguintes itens:
   * As versões de recuperação do catálogo e dos inquilinos1 servidores, com _sufixo de recuperação._  As bases de dados de catálogo restaurados e inquilinos nestes servidores têm todos os nomes usados na região original.

   * Os _&lt;inquilinos2-dpt-&gt;servidor_ SQL de recuperação do utilizador.  Este servidor é utilizado para fornecer novos inquilinos durante a paralisação.
   * O Serviço de Aplicações nomeou, _eventos-wingtip-dpt-&lt;utilizador da região&gt;-&lt;_ de recuperação&gt (), que é a instância de recuperação da app Eventos. 

     ![Recursos de recuperação azure](media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png) 
    
4. Abra os _inquilinos2-dpt-&lt;user-recovery&gt;_ SQL servidor.  Note que contém a base de dados _hawthornhall_ e a piscina elástica, _Pool1_.  A base de dados _hawthornhall_ está configurada como uma base de dados elástica na piscina elástica _Pool1._

5. Navegue de volta para o grupo de recursos e clique na base de dados da Sala de Concertos Contoso no servidor de recuperação do _&lt;utilizador&gt;dos inquilinos1-dpt._ Clique na Geo-Replicação no lado esquerdo.
    
    ![Base de dados Contoso após falha](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Alterar os dados dos inquilinos 
Nesta tarefa, atualiza uma das bases de dados dos inquilinos. 

1. No seu navegador, encontre a lista de eventos para a Sala de Concertos Contoso e note o nome do último evento.
2. No *PowerShell ISE*, no ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1, definir o seguinte valor:
    * **$DemoScenario = 5** Apagar um evento de um inquilino na região de recuperação
3. Pressione **F5** para executar o script
4. Refresque a páginahttp://events.wingtip-dpt.&ltde&gt;eventos da &lt;&gt; Sala de Concertos Contoso ( .utilizador .trafficmanager.net/contosoconcerthall - utilizador substituto pelo valor de utilizador da sua implementação) e note que o último evento foi eliminado.

## <a name="repatriate-the-application-to-its-original-production-region"></a>Repatriar a aplicação à sua região de produção original

Esta tarefa repatria a aplicação para a sua região original. Num cenário real, iniciaria o repatriamento quando a paralisação fosse resolvida.

### <a name="repatriation-process-overview"></a>Visão geral do processo de repatriamento

![Arquitetura de Repatriamento](media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

O processo de repatriamento:
1. Cancela quaisquer pedidos de restauro de bases de dados pendentes ou a bordo.
2. Atualiza o pseudónimo _do novo inquilino_ para apontar para o servidor dos inquilinos na região de origem. A alteração deste pseudónimo garante que as bases de dados para quaisquer novos inquilinos serão agora aprovisionadas na região de origem.
3. Sementes quaisquer dados de inquilinos alterados para a região original
4. Falha nas bases de dados dos inquilinos por ordem prioritária.

Failover move eficazmente a base de dados para a região original. Quando a base de dados falha, quaisquer ligações abertas são retiradas e a base de dados não está disponível por alguns segundos. As aplicações devem ser escritas com lógica de retry para garantir que se conectem novamente.  Embora esta breve desconexão muitas vezes não seja notada, pode optar por repatriar bases de dados fora do horário comercial. 


### <a name="run-the-repatriation-script"></a>Executar o roteiro de repatriamento
Agora vamos imaginar que a paralisação está resolvida e executar o roteiro de repatriamento.

1. No *PowerShell ISE*, no script ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1.

2. Verifique se o processo De sincronia do catálogo ainda está em execução na sua instância PowerShell.  Se necessário, reinicie-o definindo:
    * **$DemoScenario = 1**, Comece a sincronizar o servidor, piscina e informações de configuração de bases de dados no catálogo
    * Prima **F5** para executar o script.

3.  Em seguida, para iniciar o processo de repatriamento, definir:
    * **$DemoScenario = 6**, Repatriar a app para a sua região original
    * Pressione **F5** para executar o script de recuperação numa nova janela PowerShell.  O repatriamento levará vários minutos e pode ser monitorizado na janela PowerShell.
    ![Processo de repatriamento](media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. Enquanto o script estiver em execução,&gt;refresque a página Events Hub (.userhttp://events.wingtip-dpt.&lt.trafficmanager.net)
    * Note que todos os inquilinos estão online e acessíveis ao longo deste processo.

5. Depois do repatriamento estar completo, refresque o centro de eventos e abra a página de eventos para Hawthorn Hall. Note que esta base de dados foi repatriada para a região original.
    ![Centro de eventos repatriado](media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Conceber a aplicação para garantir que a aplicação e a base de dados são colocalizadas 
A aplicação foi concebida de modo a ligar-se sempre a partir de um caso na mesma região que a base de dados dos inquilinos. Este desenho reduz a latência entre a aplicação e a base de dados. Esta otimização pressupõe que a interação app-a-database é mais chata do que a interação entre o utilizador e a aplicação.  

As bases de dados dos inquilinos podem ser distribuídas por regiões de recuperação e originais durante algum tempo durante o repatriamento. Para cada base de dados, a aplicação procura a região em que a base de dados está localizada fazendo uma pesquisa dNS sobre o nome do servidor do inquilino. Na Base de Dados SQL, o nome do servidor é um pseudónimo. O nome do servidor pseudónimo contém o nome da região. Se a aplicação não estiver na mesma região que a base de dados, redireciona para a instância na mesma região que o servidor de base de dados.  Redirecionando para o exemplo na mesma região que a base de dados minimiza a latência entre app e base de dados. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:
> [!div class="checklist"]
> 
> * Base de dados de sincronização e informações de configuração de piscina elástica no catálogo de inquilinos
> * Criar um ambiente de recuperação numa região alternativa, composto por aplicações, servidores e piscinas
> * Utilize _a geo-replicação_ para replicar as bases de dados do catálogo e dos inquilinos para a região de recuperação
> * Falhar na aplicação e catálogo e bases de dados de inquilinos para a região de recuperação 
> * Recue as bases de dados da aplicação, catálogo e inquilino para a região original após a paralisação ser resolvida

Pode saber mais sobre as tecnologias que a base de dados Azure SQL fornece para permitir a continuidade do negócio na documentação de visão geral da continuidade do [negócio.](sql-database-business-continuity.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que se baseiam na aplicação Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
