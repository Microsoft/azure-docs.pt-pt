---
title: Recuperação de desastres para apps SaaS com Replicação Geo
description: Saiba como usar geo-réplicas de base de dados Azure SQL para recuperar uma aplicação SaaS multi-arrendatário em caso de paragem
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: dc2047832f8cfbf31c04c84eb7a70fee6631fa4b
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92330126"
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Recuperação de desastres para uma aplicação SaaS multi-arrendatário usando a geo-replicação da base de dados
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste tutorial, você explora um cenário completo de recuperação de desastres para uma aplicação SaaS multi-arrendatário implementada usando o modelo base de dados por inquilino. Para proteger a aplicação de uma paragem, você usa [_a geo-replicação_](active-geo-replication-overview.md) para criar réplicas para as bases de dados do catálogo e inquilinos em uma região de recuperação alternativa. Se ocorrer uma falha, rapidamente falha nestas réplicas para retomar as operações normais de negócio. No que diz causa de falhas, as bases de dados na região original tornam-se réplicas secundárias das bases de dados na região de recuperação. Assim que estas réplicas voltam a estar on-line, eles apanham automaticamente o estado das bases de dados na região de recuperação. Depois de a paralisação ser resolvida, falha-se nas bases de dados da região de produção original.

Este tutorial explora tanto os fluxos de trabalho de failover como os failback. Vai aprender a:
> [!div class="checklist"]
> 
> * Sync base de dados e informação de configuração de piscina elástica no catálogo do inquilino
> * Criar um ambiente de recuperação numa região alternativa, compreendendo aplicações, servidores e piscinas
> * Utilize _a geo-replicação_ para replicar as bases de dados do catálogo e dos inquilinos para a região de recuperação
> * Falha nas bases de dados de candidaturas e catálogos e inquilinos para a região de recuperação 
> * Mais tarde, falhe as bases de dados de aplicação, catálogo e inquilinos de volta à região original após a paralisação ser resolvida
> * Atualize o catálogo à medida que cada base de dados de inquilinos é falhada para rastrear a localização primária da base de dados de cada inquilino
> * Certifique-se de que a base de dados de inquilinos primários estão sempre na mesma região do Azure para reduzir a latência  
 

Antes de iniciar este tutorial, certifique-se de que os seguintes pré-requisitos estão completos:
* A base de dados Wingtip Tickets SaaS por app de inquilinos é implementada. Para implementar em menos de cinco minutos, consulte [Implementar e explorar a base de dados de Bilhetes SaaS wingtip por aplicação de inquilino](saas-dbpertenant-get-started-deploy.md)  
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>Introdução ao padrão de recuperação da geo-replicação

![Arquitetura de Recuperação](./media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)
 
A recuperação de desastres (DR) é uma consideração importante para muitas aplicações, seja por razões de conformidade ou continuidade do negócio. Se houver uma interrupção prolongada do serviço, um plano DR bem preparado pode minimizar a perturbação do negócio. A utilização de geo-replicação fornece o RPO e o RTO mais baixos mantendo réplicas de base de dados numa região de recuperação que pode ser falhada a curto prazo.

Um plano DR baseado na geo-replicação compreende três partes distintas:
* Configuração - criação e manutenção do ambiente de recuperação
* Recuperação - falha da aplicação e bases de dados para o ambiente de recuperação em caso de paragem,
* Repatriamento - falha da aplicação e bases de dados de volta à região original assim que a aplicação for resolvida 

Todas as peças devem ser cuidadosamente consideradas, especialmente se funcionarem à escala. No geral, o plano deve atingir vários objetivos:

* Configuração
    * Estabeleça e mantenha um ambiente de imagem espelhada na região de recuperação. Criar piscinas elásticas e replicar quaisquer bases de dados neste ambiente de recuperação reserva capacidade na região de recuperação. A manutenção deste ambiente inclui a replicação de novas bases de dados de inquilinos à medida que são aprovisionadas.  
* Recuperação
    * Sempre que seja utilizado um ambiente de recuperação escalonado para minimizar os custos do dia-a-dia, os pools e as bases de dados devem ser dimensionados para adquirir a plena capacidade operacional na região de recuperação
    * Permitir o fornecimento de novos inquilinos na região de recuperação o mais rapidamente possível  
    * Esteja otimizado para restaurar inquilinos em ordem prioritária
    * Esteja otimizado para colocar os inquilinos on-line o mais rápido possível, fazendo passos em paralelo onde prático
    * Seja resiliente ao fracasso, recomeçante e idempotente
    * Será possível cancelar o processo a meio do voo se a região original voltar on-line.
* Repatriamento 
    * Falha nas bases de dados da região de recuperação até réplicas na região original com impacto mínimo para os inquilinos: sem perda de dados e período mínimo off-line por inquilino.   

Neste tutorial, estes desafios são abordados utilizando funcionalidades da Base de Dados Azure SQL e da plataforma Azure:

* [Modelos de Gestor de Recursos Azure,](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)para reservar toda a capacidade necessária o mais rapidamente possível. Os modelos Azure Resource Manager são usados para providenciar uma imagem espelhada dos servidores de produção e piscinas elásticas na região de recuperação.
* [Geo-replicação](active-geo-replication-overview.md), para criar assíncronos apenas secundários de leitura replicados para todas as bases de dados. Durante uma paragem, falhas nas réplicas na região de recuperação.  Após a interrupção ser resolvida, falha nas bases de dados da região original sem perda de dados.
* Operações de failover [assíncronos enviadas](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) por ordem de prioridade do inquilino, para minimizar o tempo de insuperância para um grande número de bases de dados.
* [Funcionalidades de recuperação de gestão de fragmentos,](elastic-database-recovery-manager.md)para alterar as entradas de base de dados no catálogo durante a recuperação e repatriamento. Estas funcionalidades permitem que a aplicação se conecte às bases de dados dos inquilinos, independentemente da localização sem reconfigurar a app.
* [Pseudónimos DNS do servidor SQL,](../../sql-database/dns-alias-overview.md)para permitir o fornecimento sem emenda de novos inquilinos, independentemente da região em que a aplicação esteja a operar. Os pseudónimos DNS também são usados para permitir que o processo de sincronização do catálogo se conecte ao catálogo ativo, independentemente da sua localização.

## <a name="get-the-disaster-recovery-scripts"></a>Obtenha os scripts de recuperação de desastres 

> [!IMPORTANT]
> Como todos os scripts de gestão de Bilhetes Wingtip, os scripts DR são de qualidade de amostra e não devem ser usados na produção. 

Os scripts de recuperação utilizados neste tutorial e código fonte de aplicação wingtip estão disponíveis na [base de dados Wingtip Tickets SaaS por inquilino gitHub repositório](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/). Confira as [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para as etapas para descarregar e desbloquear os scripts de gestão de Bilhetes Wingtip.

## <a name="tutorial-overview"></a>Descrição geral do tutorial
Neste tutorial, utiliza-se pela primeira vez a geo-replicação para criar réplicas da aplicação Wingtip Tickets e das suas bases de dados numa região diferente. Depois, falhas nesta região para simular a recuperação de uma paragem. Quando concluída, a aplicação está totalmente funcional na região de recuperação.

Mais tarde, numa etapa separada de repatriamento, falha-se nas bases de dados do catálogo e dos inquilinos da região de recuperação para a região original. A aplicação e as bases de dados permanecem disponíveis durante o repatriamento. Quando concluída, a aplicação está totalmente funcional na região original.

> [!Note]
> O pedido é recuperado na _região emparelhada_ da região em que a aplicação é implementada. Para mais informações, consulte [as regiões emparelhadas Azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)

## <a name="review-the-healthy-state-of-the-application"></a>Reveja o estado saudável da aplicação

Antes de iniciar o processo de recuperação, reveja o estado normal e saudável da aplicação.
1. No seu navegador web, abra o Wingtip Tickets Events Hub ( http://events.wingtip-dpt.&lt (user &gt; .trafficmanager.net - &lt; substitua o utilizador &gt; pelo valor do utilizador da sua implementação).
    * Percorra a parte inferior da página e note o nome e a localização do servidor do catálogo no rodapé. A localização é a região em que implementou a app.
    *DICA: Passe o rato sobre a localização para ampliar o visor.* 
     ![ Eventos hub estado saudável na região original](./media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Clique no inquilino do Salão de Concertos Contoso e abra a sua página de eventos.
    * No rodapé, note o nome do servidor do inquilino. A localização será a mesma da localização do servidor do catálogo.

3. No [portal Azure,](https://portal.azure.com)abra o grupo de recursos no qual a app é implantada
    * Note a região em que os servidores são implantados. 

## <a name="sync-tenant-configuration-into-catalog"></a>Sync configuração do inquilino em catálogo

Nesta tarefa, inicia-se um processo que sincroniza a configuração dos servidores, piscinas elásticas e bases de dados no catálogo do inquilino. O processo mantém esta informação atualizada no catálogo.  O processo funciona com o catálogo ativo, seja na região original ou na região de recuperação. A informação de configuração é usada como parte do processo de recuperação para garantir que o ambiente de recuperação é consistente com o ambiente original, e depois durante o repatriamento para garantir que a região original é feita consistente com quaisquer alterações feitas no ambiente de recuperação. O catálogo também é usado para acompanhar o estado de recuperação dos recursos dos inquilinos

> [!IMPORTANT]
> Para simplificar, o processo de sincronização e outros processos de recuperação e repatriamento de longa duração são implementados nestes tutoriais como empregos ou sessões locais da PowerShell que funcionam sob o login do seu cliente. As fichas de autenticação emitidas quando iniciar sessão expirarão após várias horas e os trabalhos falharão. Num cenário de produção, os processos de longo prazo devem ser implementados como serviços fiáveis da Azure de algum tipo, funcionando sob um principal de serviço. Consulte [a Utilização Azure PowerShell para criar um principal de serviço com um certificado](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal).

1. No _PowerShell ISE,_ abra o ficheiro ...\Módulos de aprendizagem\UserConfig.psm1. Substitua `<resourcegroup>` e `<user>` nas linhas 10 e 11 pelo valor utilizado quando implementou a aplicação.  Guarde o ficheiro!

2. No *PowerShell ISE,* abra o seguinte script ...\Módulos de aprendizagem\Continuidade de Negócios e Desastres Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 e definido:
    * **$DemoScenario = 1**, Inicie um trabalho de fundo que sincroniza o servidor do inquilino e a informação de configuração da piscina no catálogo

3. Prima **F5** para executar o script de sincronização. É aberta uma nova sessão PowerShell para sincronizar a configuração dos recursos dos inquilinos.
![Screenshot que mostra a nova sessão PowerShell que é aberta para sincronizar a configuração dos recursos dos inquilinos.](./media/saas-dbpertenant-dr-geo-replication/sync-process.png)

Deixe a janela PowerShell em segundo plano e continue com o resto do tutorial. 

> [!Note]
> O processo de sincronização liga-se ao catálogo através de um pseudónimo DNS. Este pseudónimo é modificado durante a restauração e repatriamento para apontar para o catálogo ativo. O processo de sincronização mantém o catálogo atualizado com quaisquer alterações na base de dados ou na configuração da piscina efetuadas na região de recuperação.  Durante o repatriamento, estas alterações são aplicadas aos recursos equivalentes na região original.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>Criar réplicas de base de dados secundárias na região de recuperação

Nesta tarefa, inicia-se um processo que implementa uma instância de aplicações duplicada e replica o catálogo e todas as bases de dados de inquilinos para uma região de recuperação.

> [!Note]
> Este tutorial adiciona proteção de geo-replicação à aplicação de amostra de Bilhetes Wingtip. Num cenário de produção para uma aplicação que utilize a geo-replicação, cada inquilino seria aloque-se com uma base de dados geo-replicada desde o início. Consulte [a conceção de serviços altamente disponíveis utilizando a Base de Dados Azure SQL](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. No *PowerShell ISE,* abra o seguinte script ...\Módulos de aprendizagem\Continuidade de Negócios e Desastre Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 s e Definir os seguintes valores:
    * **$DemoScenario = 2**, Criar ambiente de recuperação de imagem espelhada e replicar catálogos e bases de dados de inquilinos

2. Prima **F5** para executar o script. É aberta uma nova sessão PowerShell para criar as réplicas.
![Processo de sincronização](./media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>Reveja o estado normal de aplicação

Neste momento, a aplicação está a funcionar normalmente na região original e agora está protegida por geo-replicação.  Réplicas secundárias só de leitura existem na região de recuperação de todas as bases de dados. 

1. No portal Azure, olhe para os seus grupos de recursos e note que foi criado um grupo de recursos com sufixo de recuperação na região de recuperação. 

2. Explore os recursos no grupo de recursos de recuperação.  

3. Clique na base de dados Do Salão de Concertos Contoso no servidor _ &lt; de &gt; recuperação do utilizador-utilizadores de 1-dpt._  Clique em Geo-Replication no lado esquerdo. 

    ![Ligação geo-replicação do Concerto Contoso](./media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

No mapa das regiões de Azure, note-se a ligação de geo-replicação entre as primárias na região original e a secundária na região de recuperação.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>Falha na aplicação na região de recuperação

### <a name="geo-replication-recovery-process-overview"></a>Visão geral do processo de recuperação de geo-replicação

O script de recuperação executa as seguintes tarefas:

1. Desativa o ponto final do Traffic Manager para a aplicação web na região original. A desativação do ponto final impede que os utilizadores se conectem à app num estado inválido caso a região original esteja online durante a recuperação.

1. Utiliza uma falha de força da base de dados do catálogo na região de recuperação para torná-la a base de dados primária, e atualiza o pseudónimo _activecatalog_ para apontar para o servidor do catálogo de recuperação.

1. Atualiza o _pseudónimo novo_ para apontar para o servidor de inquilinos na região de recuperação. A alteração deste pseudónimo garante que as bases de dados para novos inquilinos sejam a provisionadas na região de recuperação. 

1. Marca todos os inquilinos existentes no catálogo de recuperação como offline para impedir o acesso às bases de dados dos inquilinos antes de serem chumbados.

1. Atualiza a configuração de todas as piscinas elásticas e bases de dados únicas replicadas na região de recuperação para espelhar a sua configuração na região original. (Esta tarefa só é necessária se os pools ou bases de dados replicadas no ambiente de recuperação forem reduzidos durante as operações normais para reduzir os custos).

1. Permite o ponto final do Gestor de Tráfego para a aplicação web na região de recuperação. A ativação deste ponto final permite a aplicação de novos inquilinos. Nesta fase, os inquilinos existentes ainda estão offline.

1. Submete lotes de pedidos para forçar falhas nas bases de dados por ordem prioritária.
    * Os lotes são organizados para que as bases de dados sejam falhadas em paralelo em todas as piscinas.
    * Os pedidos de failover são submetidos usando operações assíncronos para que sejam submetidos rapidamente e vários pedidos podem ser processados simultaneamente.

   > [!Note]
   > Num cenário de paralisação, as bases de dados primárias da região original estão offline.  A força falha no secundário quebra a ligação ao primário sem tentar aplicar quaisquer transações residuais em fila. Num cenário de perfuração dr como este tutorial, se houver alguma atividade de atualização no momento do failover, pode haver alguma perda de dados. Mais tarde, durante o repatriamento, quando se falha sobre as bases de dados na região de recuperação de volta à região original, é usado um failover normal para garantir que não há perda de dados.

1. Monitoriza o serviço para determinar quando as bases de dados foram falhadas. Uma vez que uma base de dados de inquilinos é chumbada, ele atualiza o catálogo para registar o estado de recuperação da base de dados do inquilino e marcar o inquilino como on-line.
    * As bases de dados dos inquilinos podem ser acedidas pela aplicação assim que estiverem marcadas online no catálogo.
    * Uma soma de valores de remar na base de dados dos inquilinos está armazenada no catálogo. Este valor funciona como uma impressão digital que permite ao processo de repatriamento determinar se a base de dados foi atualizada na região de recuperação.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>Executar o roteiro para falhar na região de recuperação

Agora imagine que há uma paragem na região em que a aplicação é implementada e executar o roteiro de recuperação:

1. No *PowerShell ISE,* abra o seguinte script ...\Módulos de aprendizagem\Continuidade de Negócios e Desastre Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 s e Definir os seguintes valores:
    * **$DemoScenario = 3**, Recuperar a app numa região de recuperação falhando em réplicas

2. Prima **F5** para executar o script.  
    * O script abre numa nova janela PowerShell e, em seguida, inicia uma série de trabalhos PowerShell que funcionam em paralelo. Estes postos de trabalho falham nas bases de dados dos inquilinos para a região de recuperação.
    * A região de recuperação é a _região emparelhada_ associada à região de Azure em que implementou a aplicação. Para mais informações, consulte [as regiões emparelhadas Azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) 

3. Monitorize o estado do processo de recuperação na janela PowerShell.
    ![processo de failover](./media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> Para explorar o código para os trabalhos de recuperação, reveja os scripts PowerShell na pasta ...\Learning Modules\Business Continuity and Disaster Recovery\DR-FailoverToReplica\RecoveryJobs.

### <a name="review-the-application-state-during-recovery"></a>Reveja o estado de aplicação durante a recuperação

Enquanto o ponto final da aplicação é desativado no Gestor de Tráfego, a aplicação não está disponível. Depois de o catálogo ser chumbado para a região de recuperação e todos os inquilinos marcados offline, a aplicação é reativada online. Embora a aplicação esteja disponível, cada inquilino aparece offline no centro de eventos até que a sua base de dados seja chumbada. É importante desenhar a sua aplicação para lidar com bases de dados de inquilinos offline.

1. Imediatamente após a recuperação da base de dados do catálogo, refresque o Wingtip Tickets Events Hub no seu navegador web.
   * No rodapé, note que o nome do servidor do catálogo tem agora um sufixo _de recuperação_ e está localizado na região de recuperação.
   * Note que os inquilinos que ainda não foram restaurados, estão marcados como offline, e não são selecionáveis.  

     > [!Note]
     > Com apenas algumas bases de dados para recuperar, poderá não conseguir atualizar o navegador antes de a recuperação ter terminado, para que não veja os inquilinos enquanto estiverem offline. 
 
     ![Centro de eventos offline](./media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

   * Se abrir diretamente a página de Eventos de um inquilino offline, apresenta uma notificação de "inquilino offline". Por exemplo, se o Contoso Concert Hall estiver offline, tente abrir http://events.wingtip-dpt.&lt a página offline do utilizador &gt; .trafficmanager.net/contosoconcerthall ![ Contoso](./media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>Provisiona um novo inquilino na região de recuperação
Mesmo antes de todas as bases de dados de inquilinos existentes terem falhado, você pode providenciar novos inquilinos na região de recuperação.  

1. No *PowerShell ISE,* abra o seguinte script ...\Módulos de aprendizagem\Continuidade de Negócios e Desastre Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 s e Definir o seguinte imóvel:
    * **$DemoScenario = 4**, Provision a novo inquilino na região de recuperação

2. Pressione **f5** para executar o guião e providenciar o novo inquilino. 

3. A página de eventos do Hawthorn Hall abre no navegador quando termina. Note no rodapé que a base de dados hawthorn Hall está a provisionada na região de recuperação.
    ![Página de eventos de Hawthorn Hall](./media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. No navegador, atualize a página Wingtip Tickets Events Hub para ver o Hawthorn Hall incluído. 
    * Se você a provisionou Hawthorn Hall sem esperar que os outros inquilinos restaurassem, outros inquilinos ainda podem estar offline.


## <a name="review-the-recovered-state-of-the-application"></a>Rever o estado recuperado do pedido

Quando o processo de recuperação estiver concluído, a aplicação e todos os inquilinos estão totalmente funcionais na região de recuperação. 

1. Assim que o visor na janela da consola PowerShell indicar que todos os inquilinos estão recuperados, refresque o Centro de Eventos.  Os inquilinos vão aparecer todos online, incluindo o novo inquilino, Hawthorn Hall.

    ![recuperados e novos inquilinos no centro eventos](./media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. No [portal Azure,](https://portal.azure.com)abra a lista de grupos de recursos.  
    * Note o grupo de recursos que implementou, mais o grupo de recursos de recuperação, com o sufixo _de recuperação._  O grupo de recursos de recuperação contém todos os recursos criados durante o processo de recuperação, além de novos recursos criados durante a paralisação.  

3. Abra o grupo de recursos de recuperação e note os seguintes itens:
   * As versões de recuperação do catálogo e dos inquilinos1 servidores, com sufixo de _recuperação._  As bases de dados restauradas do catálogo e dos inquilinos destes servidores têm os nomes usados na região original.

   * Os _inquilinos2-dpt- &lt; utilizador &gt; -recovery_ SQL servidor.  Este servidor é utilizado para o fornecimento de novos inquilinos durante a paralisação.
   * O Serviço de Aplicações nomeado, _utilizador de região de recuperação de eventos &lt; &gt; - &lt;&gt_,que é a instância de recuperação da aplicação Eventos. 

     ![Recursos de recuperação do Azure](./media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png) 
    
4. Abra o servidor SQL _de recuperação de inquilinos2-dpt- &lt; utilizador. &gt; _  Note que contém a base de dados _hawthornhall_ e a piscina elástica, _Pool1_.  A base de _dados hawthornhall_ é configurada como uma base de dados elástica na piscina elástica _Pool1._

5. Volte para o grupo de recursos e clique na base de dados Do Contoso Concert Hall no servidor _de recuperação do &lt; &gt; utilizador-utilizadores._ Clique em Geo-Replication no lado esquerdo.
    
    ![Base de dados Contoso após falha](./media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Alterar dados de inquilinos 
Nesta tarefa, atualize uma das bases de dados dos inquilinos. 

1. No seu browser, encontre a lista de eventos para a Sala de Concertos Contoso e note o nome do último evento.
2. No *powerShell ISE,* no seguinte script ...\Módulos de aprendizagem\Continuidade de Negócios e Desastres Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1, definirá o seguinte valor:
    * **$DemoScenario = 5** Apagar um evento de um inquilino na região de recuperação
3. Prima **F5** para executar o script
4. Atualize a página de eventos da Sala de Concertos Contoso http://events.wingtip-dpt.&lt &gt; (.user .trafficmanager.net/contosoconcerthall - substitua &lt; o utilizador pelo valor do utilizador da sua &gt; implementação) e note que o último evento foi eliminado.

## <a name="repatriate-the-application-to-its-original-production-region"></a>Repatriar a aplicação para a sua região de produção original

Esta tarefa repatria a aplicação para a sua região original. Num cenário real, iniciaria o repatriamento quando a paralisação fosse resolvida.

### <a name="repatriation-process-overview"></a>Visão geral do processo de repatriamento

![Arquitetura de Repatriamento](./media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

O processo de repatriamento:
1. Cancela quaisquer pedidos de restauro de bases de dados pendentes ou a bordo.
2. Atualiza o _pseudónimo novo_ para apontar para o servidor dos inquilinos na região de origem. A alteração deste pseudónimo garante que as bases de dados para novos inquilinos serão agora a provisionadas na região de origem.
3. Sementes quaisquer dados de inquilino alterados para a região original
4. Falha nas bases de dados dos inquilinos por ordem prioritária.

O failover move efetivamente a base de dados para a região original. Quando a base de dados falha, quaisquer ligações abertas são deixadas e a base de dados não está disponível durante alguns segundos. As aplicações devem ser escritas com lógica de re-tentar para garantir que se conectam novamente.  Embora esta breve desconexão não seja muitas vezes notada, pode optar por repatriar bases de dados fora do horário comercial. 


### <a name="run-the-repatriation-script"></a>Executar o roteiro de repatriamento
Agora vamos imaginar que a paralisação está resolvida e executar o roteiro de repatriamento.

1. No *PowerShell ISE,* no seguinte script ...\Módulos de aprendizagem\Continuidade de Negócios e Desastre Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1.

2. Verifique se o processo de Sincronização de Catálogo ainda está em funcionamento na sua instância PowerShell.  Se necessário, reinicie-o definindo:
    * **$DemoScenario = 1**, Comece a sincronizar informações de configuração do servidor de inquilinos, piscina e base de dados no catálogo
    * Prima **F5** para executar o script.

3.  Em seguida, para iniciar o processo de repatriamento, definir:
    * **$DemoScenario = 6**, Repatriar a app para a sua região original
    * Prima **F5** para executar o script de recuperação numa nova janela PowerShell.  O repatriamento levará vários minutos e pode ser monitorizado na janela PowerShell.
    ![Processo de repatriamento](./media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. Enquanto o script está em execução, refresque a página Do Centro de Eventos http://events.wingtip-dpt.&lt (.user &gt; .trafficmanager.net)
    * Note que todos os inquilinos estão online e acessíveis em todo este processo.

5. Após o repatriamento estar completo, refresque o centro de eventos e abra a página de eventos para o Hawthorn Hall. Note que esta base de dados foi repatriada para a região original.
    ![Centro de eventos repatriado](./media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Conceber a aplicação para garantir que a app e a base de dados são apresentados 
A aplicação foi concebida de modo a que se conecte sempre a partir de um caso na mesma região que a base de dados dos inquilinos. Este design reduz a latência entre a aplicação e a base de dados. Esta otimização pressupõe que a interação app-to-database é mais chattier do que a interação utilizador-app.  

As bases de dados dos inquilinos podem ser distribuídas por regiões de recuperação e originais durante algum tempo durante o repatriamento. Para cada base de dados, a aplicação procura a região em que a base de dados está localizada fazendo uma pesquisas de DNS no nome do servidor do inquilino. Na Base de Dados SQL, o nome do servidor é um pseudónimo. O nome do servidor pseudónimo contém o nome da região. Se a aplicação não estiver na mesma região que a base de dados, redireciona para o caso na mesma região que o servidor. Redirecionar para instâncias na mesma região que a base de dados minimiza a latência entre app e base de dados. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:
> [!div class="checklist"]
> 
> * Sync base de dados e informação de configuração de piscina elástica no catálogo do inquilino
> * Criar um ambiente de recuperação numa região alternativa, compreendendo aplicações, servidores e piscinas
> * Utilize _a geo-replicação_ para replicar as bases de dados do catálogo e dos inquilinos para a região de recuperação
> * Falha nas bases de dados de candidaturas e catálogos e inquilinos para a região de recuperação 
> * Falhe na aplicação, no catálogo e nas bases de dados dos inquilinos para a região original após a paralisação ser resolvida

Pode saber mais sobre as tecnologias que a Base de Dados SQL de Azure fornece para permitir a continuidade do negócio na documentação geral de [continuidade do negócio.](business-continuity-high-availability-disaster-recover-hadr-overview.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que se baseiam na aplicação Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
