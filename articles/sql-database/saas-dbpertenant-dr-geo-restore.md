---
title: 'Aplicações de SaaS: Azure base de dados SQL com redundância geográfica cópias de segurança para recuperação após desastre | Documentos da Microsoft'
description: Saiba como utilizar cópias de segurança georredundante de base de dados do Azure SQL para recuperar de uma aplicação SaaS multi-inquilino em caso de interrupção
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
ms.date: 01/14/2019
ms.openlocfilehash: c96f2dc2b44ea2118d9f0dd6c988017efcba5800
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58116780"
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Utilizar o restauro geográfico para recuperar uma aplicação SaaS multi-inquilino de cópias de segurança da base de dados

Este tutorial analisa um cenário de recuperação após desastre para uma aplicação SaaS multi-inquilino implementado com a base de dados por modelo de inquilino. Utilizar [georrestauro](sql-database-recovery-using-backups.md) para recuperar as bases de dados do catálogo e de inquilino de manter automaticamente cópias de segurança georredundante para uma região de recuperação alternativo. Depois da falha for resolvida, utilize [georreplicação](sql-database-geo-replication-overview.md) para repatriate bases de dados alterados para sua região original.

![Arquitetura de restauro geográfico](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

Georrestauro encontra-se a solução de recuperação após desastre de custo mais baixo para a base de dados do Azure SQL. No entanto, o restauro a partir de cópias de segurança georredundante pode resultar na perda de dados de até uma hora. Pode demorar um tempo considerável, dependendo do tamanho dos bancos de dados. 

> [!NOTE]
> Recupere aplicações com o RPO e RTO mais baixas possível através de georreplicação em vez do restauro geográfico.

Este tutorial explora o restauro e repatriation fluxos de trabalho. Saiba como:
> [!div class="checklist"]
> 
> * Base de dados de sincronização e informações de configuração do conjunto elástico para o catálogo de inquilino.
> * Configure um ambiente de imagem espelhada numa região de recuperação que inclui aplicativos, servidores e de agrupamentos.   
> * Recupere bases de dados do catálogo e de inquilino, utilizando o restauro geográfico.
> * Utilize a georreplicação para repatriate o catálogo de inquilino e bases de dados do inquilino foi alterado após a falha for resolvida.
> * O catálogo de atualizações à medida que cada base de dados é restaurada (ou repatriated) para controlar a localização atual da cópia ativa do banco de dados de cada inquilino.
> * Certifique-se de que o aplicativo e a base de dados de inquilino são sempre localizados conjuntamente na mesma região do Azure para reduzir a latência. 
 

Antes de começar este tutorial, conclua os seguintes pré-requisitos:
* Implemente a base de dados de Wingtip Tickets SaaS por aplicação de inquilino. Para implementar em menos de cinco minutos, veja [implementar e explorar a base de dados de Wingtip Tickets SaaS por aplicação de inquilino](saas-dbpertenant-get-started-deploy.md). 
* Instale o Azure PowerShell. Para obter detalhes, consulte [introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Introdução ao padrão de recuperação do restauro geográfico

Recuperação após desastre (DR) é uma consideração importante para muitos aplicativos, seja em motivos de conformidade ou de continuidade do negócio. Se houver uma interrupção de serviço prolongada, um plano de DR bem preparado pode minimizar interrupções nos negócios. Plano de DR da com base no restauro geográfico deve realizar várias metas:
 * Reserve capacidade tudo necessária na região de recuperação escolhido o mais rapidamente possível para garantir que está disponível para restaurar bancos de dados do inquilino.
 * Estabelece um ambiente de recuperação da imagem espelhada que reflete a configuração de agrupamento e a base de dados original. 
 * Permitir o cancelamento do processo de restauração em trânsito médio se a região original ficar online novamente.
 * Ative aprovisionamento rapidamente, para a nova integração de inquilino pode reiniciar o quanto antes do inquilino.
 * Ser otimizada para restaurar os inquilinos na ordem de prioridade.
 * Ser otimizada para obter inquilinos online logo que possível, fazendo passos em paralelo sempre que o prático.
 * Ser resiliente a falhas, reinicializável e idempotentes.
 * Repatriate bases de dados para suas regiões original com impacto mínimo para os inquilinos quando a indisponibilidade for resolvida.  

> [!NOTE]
> O aplicativo é recuperado para a região emparelhada da região em que a aplicação é implementada. Para obter mais informações, consulte [regiões emparelhadas do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).   

Este tutorial utiliza recursos do Azure SQL Database e a plataforma do Azure para enfrentar esses desafios:

* [Modelos Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), para reservar capacidade necessária todas as mais depressa possível. Modelos Azure Resource Manager são utilizados para Aprovisionar uma imagem espelhada dos servidores originais e conjuntos elásticos na região de recuperação. Um servidor separado e o agrupamento também são criados para o aprovisionamento de novos inquilinos.
* [Biblioteca de clientes de base de dados elástica](sql-database-elastic-database-client-library.md) (EDCL), para criar e manter um catálogo de base de dados do inquilino. O catálogo expandido inclui informações de configuração de agrupamento e a base de dados atualizadas periodicamente.
* [Recursos de recuperação de gestão de partições horizontais](sql-database-elastic-database-recovery-manager.md) de EDCL, para manter as entradas de localização de base de dados no catálogo durante a recuperação e repatriation.  
* [O restauro geográfico](sql-database-disaster-recovery.md), para recuperar as bases de dados do catálogo e de inquilino de manter automaticamente cópias de segurança georredundante. 
* [Operações assíncronas](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations), enviados por ordem de prioridade de inquilino, são colocados em fila para cada conjunto pelo sistema e processados em lotes para que o conjunto não está sobrecarregado. Estas operações podem ser canceladas antes ou durante a execução, se necessário.   
* [Replicação geográfica](sql-database-geo-replication-overview.md), para repatriate bases de dados para a região original após a falha. Não existe nenhuma perda de dados e um impacto mínimo sobre o inquilino ao utilizar a georreplicação.
* [Aliases DNS do SQL server](dns-alias-overview.md)para permitir que o processo de sincronização do catálogo para ligar ao catálogo do Active Directory, independentemente da respetiva localização.  

## <a name="get-the-disaster-recovery-scripts"></a>Obter scripts de recuperação de desastre

Os scripts de DR utilizados neste tutorial estão disponíveis no [Wingtip Tickets SaaS base de dados por inquilino repositório do GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Veja a [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter os passos transferir e os scripts de gestão da Wingtip Tickets de desbloqueio.

> [!IMPORTANT]
> Como todos os scripts de gestão de Wingtip Tickets, os scripts de DR são qualidade de exemplo e não devem ser utilizados na produção.

## <a name="review-the-healthy-state-of-the-application"></a>Reveja o estado de funcionamento da aplicação
Antes de começar o processo de recuperação, reveja o estado de bom estado de funcionamento normal do aplicativo.

1. No seu navegador da web, abra o hub de eventos da Wingtip Tickets (http://events.wingtip-dpt.&lt; usuário&gt;. trafficmanager.net, substitua &lt;utilizador&gt; com valor de utilizador da sua implementação).
    
   Desloque-se para a parte inferior da página e tenha em atenção o nome do servidor de catálogo e o local no rodapé. A localização é a região em que implementou a aplicação.    

   > [!TIP]
   > Paire o rato sobre a localização para aumentar a exibição.

   ![Estado de bom estado de funcionamento de hub de eventos na região original](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Selecione o inquilino de Contoso Concert Hall e abrir a página de eventos.

   No rodapé, observe o nome do servidor do inquilino. A localização é a mesma localização do servidor de catálogo.

   ![Região original de contoso Concert Hall](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png) 

3. Na [portal do Azure](https://portal.azure.com), reveja e abra o grupo de recursos em que implementou a aplicação.

   Observe que os recursos e região na qual os componentes do serviço de aplicações e os servidores de base de dados SQL estão implementadas.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>Sincronização da configuração do inquilino para o catálogo

Nesta tarefa, iniciar um processo para sincronizar a configuração de servidores, conjuntos elásticos e bases de dados para o catálogo de inquilino. Estas informações são utilizadas mais tarde para configurar um ambiente de imagem espelhada na região de recuperação.

> [!IMPORTANT]
> Para simplificar, o processo de sincronização e outra recuperação de longa execução e os processos de repatriation são implementados nestes exemplos de como tarefas do PowerShell locais ou sessões que são executados em seu início de sessão de utilizador do cliente. Os tokens de autenticação emitidos quando iniciar sessão expirarem após várias horas, e as tarefas, em seguida, irão falhar. Num cenário de produção, os processos de longa execução devem ser implementados como serviços do Azure fiáveis de algum tipo, um principal de serviço a ser executado. Ver [utilize o Azure PowerShell para criar um principal de serviço com um certificado](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

1. No ISE do PowerShell, abra o ficheiro de Modules\UserConfig.psm1 ...\Learning. Substitua `<resourcegroup>` e `<user>` em linhas 10 e 11 com o valor que usou quando implementou a aplicação. Guarde o ficheiro.

2. No ISE do PowerShell, abra o ...\Learning Modules\Business continuidade e desastre Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 script.

    Neste tutorial, executar cada um dos cenários em que este script do PowerShell, portanto, mantenha esse arquivo aberto.

3. Defina o seguinte:

    $DemoScenario = 1: Inicie uma tarefa em segundo plano que se sincroniza o servidor de inquilino e informações de configuração do conjunto para o catálogo.

4. Para executar o script de sincronização, selecione F5. 

    Estas informações são utilizadas mais tarde para se certificar de que a recuperação cria uma imagem espelhada de servidores, conjuntos e bases de dados na região de recuperação.  

    ![Processo de sincronização](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Deixe a janela do PowerShell em execução em segundo plano e continue com o resto deste tutorial.

> [!NOTE]
> O processo de sincronização liga-se para o catálogo por meio de um alias de DNS. O alias é modificado durante o restauro e repatriation para apontar para o catálogo do Active Directory. O processo de sincronização mantém o catálogo atualizados com a base de dados ou um conjunto de alterações de configuração feitas na região de recuperação. Durante a repatriation, estas alterações são aplicadas a recursos equivalentes na região original.

## <a name="geo-restore-recovery-process-overview"></a>Visão geral do processo de recuperação georrestauro

O processo de recuperação do restauro geográfico implementa a aplicação e restaura as bases de dados a partir de cópias de segurança para a região de recuperação.

O processo de recuperação faz o seguinte:

1. Desativa o ponto de final do Gestor de tráfego do Azure para a aplicação web na região original. Desativar o ponto final impede os utilizadores estabeleçam uma ligação para a aplicação num estado inválido deve a região original ficar online durante a recuperação.

2. Aprovisiona uma recuperação do catálogo de servidor na região de recuperação, geo-restaura a base de dados do catálogo e atualiza o alias de activecatalog para apontar para o servidor de catálogo restaurada. Alterar o alias de catálogo garante que o processo de sincronização do catálogo sincroniza sempre ao catálogo do Active Directory.

3. Marca todos os inquilinos existentes no catálogo de recuperação como offline para impedir o acesso às bases de dados do inquilino antes que eles sejam restaurados.

4. Aprovisiona uma instância da aplicação na região de recuperação e configura-o para utilizar o catálogo de restaurada nessa região. Para manter a latência ao mínimo possível, a aplicação de exemplo foi concebida para sempre ligar a uma base de dados de inquilinos na mesma região.

5. Aprovisiona um conjunto de servidor e elástico na qual novos inquilinos aprovisionados. Criar esses recursos garante que o aprovisionamento de novos clientes não interfere com a recuperação de inquilinos existentes.

6. Atualiza o novo alias de inquilino para apontar para o servidor de bases de dados de inquilinos novos na região de recuperação. Alterar este alias garante que as bases de dados para quaisquer novos inquilinos aprovisionados na região de recuperação.
        
7. Aprovisiona os servidores e conjuntos elásticos na região de recuperação para restaurar bancos de dados do inquilino. Estes servidores e os conjuntos são uma imagem espelhada da configuração na região original. Aprovisionamento conjuntos com antecedência reserva-se a capacidade necessária para restaurar todas as bases de dados.

    Uma falha numa região pode posicionar pressão significativo nos recursos disponíveis na região associada. Se contar com o restauro geográfico para DR, em seguida, reservar recursos rapidamente é recomendado. Considere georreplicação se for fundamental que um aplicativo é recuperado numa região específica. 

8. Permite que o ponto de final do Gestor de tráfego para a aplicação web na região de recuperação. Ativar este ponto final permite que o aplicativo aprovisionar novos inquilinos. Nesta fase, os inquilinos existentes estão ainda offline.

9. Envia lotes de pedidos de restauro de bases de dados por ordem de prioridade. 

    * Lotes são organizadas para que as bases de dados são restaurados em paralelo em todos os agrupamentos.  

    * Pedidos de restauro são enviadas de forma assíncrona, para que sejam submetidos rapidamente e em fila para execução em cada agrupamento.

    * Uma vez que os pedidos de restauro são processados em paralelo em todos os agrupamentos, é melhor distribuir inquilinos importantes em muitos conjuntos. 

10. Monitoriza o serviço de base de dados SQL para determinar quando são restaurados os bancos de dados. Depois de restaurar uma base de dados de inquilinos, está marcado online no catálogo e a uma soma de rowversion para a base de dados de inquilinos é registada. 

    * Bases de dados do inquilino podem ser acessados pela aplicação assim que estiver marcadas no catálogo online.

    * Uma soma dos valores de rowversion na base de dados de inquilino é armazenada no catálogo. Essa soma atua como uma impressão digital que permite que o processo de repatriation determinar se a base de dados foi atualizada na região de recuperação.       

## <a name="run-the-recovery-script"></a>Execute o script de recuperação

> [!IMPORTANT]
> Este tutorial restaura as bases de dados a partir de cópias de segurança georredundante. Embora estas cópias de segurança estão normalmente disponíveis dentro de 10 minutos, pode demorar até uma hora. O script faz uma pausa até que estejam disponíveis.

Imagine que houver uma falha na região em que a aplicação é implementada e execute o script de recuperação:

1. No ISE do PowerShell, no script ...\Learning Modules\Business continuidade e desastre Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1, defina o valor seguinte:

    $DemoScenario = 2: Recupere a aplicação para uma região de recuperação através do restauro de cópias de segurança georredundante.

2. Para executar o script, selecione F5.  

    * O script abre-se numa nova janela do PowerShell e, em seguida, inicia um conjunto de tarefas do PowerShell que são executadas em paralelo. Estas tarefas restaurar servidores, conjuntos e bases de dados para a região de recuperação.

    * A região de recuperação é a região emparelhada associado à região do Azure que implementou a aplicação. Para obter mais informações, consulte [regiões emparelhadas do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Monitorize o estado do processo de recuperação na janela do PowerShell.

    ![Processo de recuperação](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> Para explorar o código para as tarefas de recuperação, reveja os scripts do PowerShell na pasta ...\Learning Modules\Business continuidade e desastre Recovery\DR-RestoreFromBackup\RecoveryJobs.

## <a name="review-the-application-state-during-recovery"></a>Reveja o estado da aplicação durante a recuperação
Embora o ponto final da aplicação está desabilitado no Gestor de tráfego, o aplicativo não está disponível. O catálogo é restaurado e todos os inquilinos são marcadas como offline. O ponto final da aplicação na região de recuperação, em seguida, está ativado e o aplicativo esteja novamente online. Embora o aplicativo estiver disponível, os inquilinos aparecem como offline no hub de eventos até as bases de dados são restaurados. É importante projetar seu aplicativo para lidar com bases de dados do inquilino offline.

* Depois que foi recuperado a base de dados do catálogo, mas antes dos inquilinos estão online novamente, atualize o hub de eventos da Wingtip Tickets no seu browser.

  * No rodapé, tenha em atenção que agora tem o nome do servidor de catálogo sufixo - recuperação e está localizado na região de recuperação.

  * Tenha em atenção que os inquilinos que ainda não foram restaurados estão marcados como offline e que não sejam selecionáveis.   
 
    ![Processo de recuperação](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

  * Se abrir a página de eventos de um inquilino diretamente enquanto o inquilino estiver offline, a página é apresentada uma notificação offline do inquilino. Por exemplo, se a Contoso Concert Hall estiver offline, tente abrir http://events.wingtip-dpt.&lt; utilizador&gt;.trafficmanager.net/contosoconcerthall.

    ![Processo de recuperação](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Aprovisionar um inquilino novo na região de recuperação
Antes mesmo de restauração dos bancos de dados do inquilino, pode aprovisionar novos inquilinos na região de recuperação. Bases de dados do inquilino novo aprovisionados na região de recuperação são repatriated mais tarde com as bases de dados recuperadas.   

1. No ISE do PowerShell, no script ...\Learning Modules\Business continuidade e desastre Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1, defina a propriedade seguinte:

    $DemoScenario = 3: Aprovisione um inquilino novo na região de recuperação.

2. Para executar o script, selecione F5.

3. Quando o aprovisionamento estiver concluído, é aberta a página de eventos de Hawthorn Hall no browser. 

    Tenha em atenção que a base de dados Hawthorn Hall está localizado numa região de recuperação.

    ![Hall hawthorn aprovisionados na região de recuperação](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. No browser, atualize a página de hub de eventos de Wingtip Tickets para ver que hawthorn Hall incluídas. 

    Se aprovisionou Hawthorn Hall sem esperar por outros inquilinos restaurar, outros inquilinos podem ainda estar offline.

## <a name="review-the-recovered-state-of-the-application"></a>Reveja o estado recuperado da aplicação

Quando o processo de recuperação estiver concluída, a aplicação e todos os inquilinos estão totalmente funcionais na região de recuperação. 

1. Após a exibição na janela de consola do PowerShell indica que todos os inquilinos são recuperados, atualize o hub de eventos. 

    Os inquilinos todos os aparecem online, incluindo o novo inquilino, Hawthorn Hall.

    ![recuperada e novos inquilinos no hub de eventos](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Clique em Contoso Concert Hall e abrir a página de eventos. 

    No rodapé, tenha em atenção que a base de dados está localizada no servidor de recuperação, localizado na região de recuperação.

    ![Contoso na região de recuperação](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. Na [portal do Azure](https://portal.azure.com), abra a lista de grupos de recursos.  

    Observe que o grupo de recursos que implementou, bem como o recurso de recuperação de grupo, com o - sufixo de recuperação. O grupo de recursos de recuperação contém todos os recursos criados durante o processo de recuperação, além de recursos novos criados durante o período de inatividade. 

4. Abra o grupo de recursos de recuperação e tenha em atenção os seguintes itens:

   * As versões de recuperação dos servidores de catálogo e tenants1, com o - sufixo de recuperação. Os catálogo e de inquilino bancos de dados restaurados nestes servidores todos os tem os nomes utilizados na região original.

   * O tenants2-dpt -&lt;utilizador&gt;-servidor de SQL de recuperação. Este servidor é utilizado para aprovisionar novos inquilinos durante o período de inatividade.

   * O serviço de aplicações com o nome eventos-wingtip-dpt -&lt;recoveryregion&gt;-&lt;utilizador&gt;, que é a instância de recuperação da aplicação de eventos.

     ![Recursos da Contoso na região de recuperação](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Abra o tenants2-dpt -&lt;utilizador&gt;-servidor de SQL de recuperação. Tenha em atenção que contém o hawthornhall de base de dados e o conjunto elástico Pool1. A base de dados hawthornhall está configurado como uma base de dados elástica no conjunto elástico Pool1.

## <a name="change-the-tenant-data"></a>Alterar os dados de inquilino 
Nesta tarefa, atualizar uma das bases de dados inquilinas restaurada. As cópias de processo repatriation restaurar bases de dados que foram alterados para a região original. 

1. No seu browser, encontrar a lista de eventos para a Contoso Concert Hall, percorra os eventos e tenha em atenção o último evento, Strauss muito a sério.

2. No ISE do PowerShell, no script ...\Learning Modules\Business continuidade e desastre Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1, defina o valor seguinte:

    $DemoScenario = 4: Elimine um evento de um inquilino na região de recuperação.

3. Para executar o script, selecione F5.

4. Atualize a página de eventos de Contoso Concert Hall (http://events.wingtip-dpt.&lt; utilizador&gt;.trafficmanager.net/contosoconcerthall) e tenha em atenção que o evento seriamente Strauss está em falta.

Neste momento no tutorial, tiver recuperado o aplicativo, que está agora em execução na região de recuperação. Ter aprovisionado um novo inquilino na região de recuperação e modificar dados de um dos inquilinos restaurados.  

> [!NOTE]
> Outros tutoriais no exemplo não foram concebidos para executar a aplicação num Estado de recuperação. Se quiser explorar outros tutoriais, certifique-se de que repatriate o aplicativo pela primeira vez.

## <a name="repatriation-process-overview"></a>Visão geral do processo repatriation

O processo de repatriation reverte o aplicativo e respetivas bases de dados para a sua região original após uma falha for resolvida.

![O restauro geográfico repatriation](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 

O processo:

1. Para qualquer atividade de restauro em curso e cancela quaisquer pedidos de restauro de base de dados pendentes ou em trânsito.

2. Reativa no original região inquilino bases de dados que não foram alterados desde a falha. Esses bancos de dados incluem os não ainda a recuperar e os recuperados mas não foram alteradas mais tarde. As bases de dados reactivated são exatamente como o último acessados pelos seus inquilinos.

3. Aprovisiona uma imagem espelhada do agrupamento de servidor e elástico do novo inquilino na região original. Depois desta ação for concluída, o novo alias de inquilino é atualizado para apontar para este servidor. A atualizar o alias faz com que a inclusão de novo inquilino para ocorrer na região original em vez de região de recuperação.

3. Utiliza replicação geográfica para mover o catálogo para a região original a partir da região de recuperação.

4. Configuração do conjunto de atualizações na região original para que sejam consistentes com as alterações efetuadas na região de recuperação durante o período de inatividade.

5. Cria a servidores necessários e os conjuntos para alojar bases de dados novas criadas durante o período de inatividade.

6. Utiliza replicação geográfica para inquilino repatriate restaurada bases de dados que foram atualizadas de pós-restauro e todas as novas bases de dados inquilinas aprovisionados durante o período de inatividade. 

7. Limpa os recursos criados na região de recuperação durante o processo de restauração.

Para limitar o número de bases de dados de inquilinos que têm de ser repatriated, os passos 1 a 3 são realizados imediatamente.  

Passo 4 é feito apenas se o catálogo na região de recuperação foi modificado durante o período de inatividade. O catálogo é atualizado se novos inquilinos forem criados ou se qualquer configuração de base de dados ou o conjunto for alterada na região de recuperação.

É importante que o passo 7 faz com que a mínima interrupção para os inquilinos e não são perdidos dados. Para atingir esse objetivo, o processo utiliza a replicação geográfica.

Antes de cada base de dados é georreplicado, a base de dados correspondente na região original é eliminado. A base de dados na região de recuperação é, em seguida, georreplicado, criar uma réplica secundária na região original. Após a conclusão da replicação, o inquilino está marcado offline no catálogo, que quebra a todas as ligações à base de dados na região de recuperação. A base de dados é, em seguida, a ativação pós-falha, fazendo com que todas as pendentes de transações para processar no secundário. portanto, não existem dados é perdido. 

Na ativação pós-falha, as funções de base de dados são invertidas. O elemento secundário na região de original torna-se a base de dados de leitura / escrita primária e a base de dados na região de recuperação se torna uma secundária só de leitura. A entrada de inquilino no catálogo é atualizada para referenciar a base de dados na região original e o inquilino está marcada como online. Neste momento, repatriation da base de dados está concluída. 

Aplicações devem ser escritas com a lógica de repetição para garantir que eles voltar a ligar automaticamente quando as ligações estão interrompidas. Quando utilizarem o catálogo para mediar o restabelecimento de ligação, se ligam à base de dados repatriated na região original. Embora os desligar breve, muitas vezes, não é percebido, pode optar por repatriate bases de dados fora do horário comercial.

Depois de uma base de dados é repatriated, a base de dados secundária numa região de recuperação pode ser eliminado. A base de dados na região original, em seguida, baseia-se novamente georrestauro para proteção de DR.

Passo 8, os recursos na região de recuperação, incluindo os servidores de recuperação e agrupamentos, são eliminados.

## <a name="run-the-repatriation-script"></a>Execute o script de repatriation
Vamos imaginar que a indisponibilidade for resolvida e execute o script de repatriation.

Se seguiu o tutorial, o script reativa imediatamente Fabrikam Jazz Club e Dogwood Dojo na região original porque eles são alterados. Em seguida, repatriates o novo inquilino, Hawthorn Hall e Contoso Concert Hall porque este foi modificado. O script também repatriates o catálogo, que foi atualizado quando Hawthorn Hall foi aprovisionado.
  
1. No ISE do PowerShell, no ...\Learning Modules\Business continuidade e desastre Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 script, certifique-se de que o processo de sincronização do catálogo ainda está em execução na sua instância do PowerShell. Se necessário, reinicie-o através da definição:

    $DemoScenario = 1: Inicie a sincronização de servidor de inquilino, o conjunto e informações de configuração de base de dados para o catálogo.

    Para executar o script, selecione F5.

2.  Em seguida, para iniciar o processo de repatriation, defina:

    $DemoScenario = 5: Repatriate a aplicação em sua região original.

    Para executar o script de recuperação numa nova janela do PowerShell, selecione F5. Repatriation demora vários minutos e pode ser monitorado na janela do PowerShell.

3. Enquanto estiver a executar o script, atualize a página de hub de eventos (http://events.wingtip-dpt.&lt; utilizador&gt;. trafficmanager.net).

    Tenha em atenção que todos os inquilinos estão online e acessíveis ao longo deste processo.

4. Selecione o Fabrikam Jazz Club para abri-lo. Se não modificar este inquilino, tenha em atenção no rodapé que o servidor já está a ser revertida para o servidor original.

5. Abrir ou Atualize a página de eventos de Contoso Concert Hall. Observe, no rodapé, o que, inicialmente, a base de dados é ainda no servidor de - recuperação. 

6. Atualize a página de eventos de Contoso Concert Hall, quando o processo de repatriation estar concluído e tenha em atenção que a base de dados está agora na sua região original.

7. Atualize novamente o hub de eventos e abra Hawthorn Hall. Tenha em atenção que o seu banco de dados também está localizado na região original. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Limpar os recursos de região de recuperação após repatriation
Após a conclusão repatriation, é seguro eliminar os recursos na região de recuperação. 

> [!IMPORTANT]
> Elimine estes recursos imediatamente para parar toda a faturação para eles.

O processo de restauro cria todos os recursos de recuperação num grupo de recursos de recuperação. O processo de limpeza elimina este grupo de recursos e remove todas as referências aos recursos do catálogo. 

1. No ISE do PowerShell, no script ...\Learning Modules\Business continuidade e desastre Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1, defina:
    
    $DemoScenario = 6: Elimine recursos obsoletos da região de recuperação.

2. Para executar o script, selecione F5.

Depois de limpar os scripts, o aplicativo está de volta, quando começou. Neste momento, pode executar o script novamente ou experimentar outros tutoriais.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>Projetar o aplicativo para se certificar de que a aplicação e a base de dados localizadas conjuntamente 
O aplicativo foi projetado para ligar sempre a partir de uma instância na mesma região que a base de dados do inquilino. Esta conceção reduz a latência entre a aplicação e a base de dados. Essa otimização assume que a interação de bancos de aplicação é chattier que a interação de aplicação do utilizador.  

Bases de dados do inquilino podem estar distribuídos em recuperação e regiões originais durante algum tempo durante repatriation. Cada base de dados, o aplicativo procura a região na qual a base de dados está localizada, fazendo uma pesquisa de DNS no nome do servidor de inquilino. Na base de dados SQL, o nome do servidor é um alias. O nome do servidor de um alias contém o nome da região. Se o aplicativo não estiver na mesma região que a base de dados, será redirecionado para a instância na mesma região que o servidor de base de dados. Redirecionar para a instância na mesma região que a base de dados minimiza a latência entre a aplicação e a base de dados.  

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:
> [!div class="checklist"]
> 
> * Utilize o catálogo de inquilino para conter as informações de configuração atualizados periodicamente, o que permite um ambiente de recuperação da imagem espelhada criar noutra região.
> * Recupere bases de dados SQL do Azure para a região de recuperação com o restauro geográfico.
> * Atualize o catálogo de inquilino para refletir as localizações de base de dados de inquilino restaurada. 
> * Utilize um alias de DNS para que uma aplicação ligar ao catálogo de inquilino ao longo sem reconfiguração.
> * Utilize a georreplicação para repatriate bases de dados recuperadas para sua região original após uma falha for resolvida.

Experimente o [recuperação após desastre para uma aplicação SaaS multi-inquilino, utilizar a georreplicação de base de dados](saas-dbpertenant-dr-geo-replication.md) tutorial para saber como utilizar a georreplicação para reduzir drasticamente o tempo necessário para recuperar de uma aplicação multi-inquilino de larga escala.

## <a name="additional-resources"></a>Recursos adicionais

[Tutoriais adicionais que se baseiam na aplicação Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
