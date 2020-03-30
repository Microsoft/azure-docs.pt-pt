---
title: Registos do Azure Monitor com uma aplicação multiarrendatária
description: Configurar e utilizar registos do Monitor Azure com uma aplicação SaaS de base de dados Azure SQL multiarrendada
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 01/25/2019
ms.openlocfilehash: 7429a9d5e9a803f0e9a6f900c5d81e77e7477a48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214496"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-sql-database-saas-app"></a>Configurar e utilizar registos do Monitor Azure com uma aplicação SQL Database SaaS multitenant

Neste tutorial, configura e utiliza [os registos do Monitor Azure](/azure/log-analytics/log-analytics-overview) para monitorizar piscinas elásticas e bases de dados. Este tutorial baseia-se no tutorial de [monitorização e gestão](saas-dbpertenant-performance-monitoring.md)de Desempenho. Mostra como utilizar os registos do Monitor Azure para aumentar a monitorização e alerta fornecidos no portal Azure. Os registos do Azure Monitor suportam a monitorização de milhares de piscinas elásticas e centenas de milhares de bases de dados. Os registos do Azure Monitor fornecem uma única solução de monitorização, que pode integrar a monitorização de diferentes aplicações e serviços Azure em várias subscrições do Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Instale e configure os registos do Monitor Azure.
> * Utilize registos do Monitor Azure para monitorizar piscinas e bases de dados.

Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* A aplicação de bases de dados SaaS de bilhetes Wingtip SaaS por inquilino está implementada. Para implantar em menos de cinco minutos, consulte Implementar e explorar a aplicação de bases de [dados SaaS de bilhetes de dados SaaS](saas-dbpertenant-get-started-deploy.md).
* O Azure PowerShell está instalado. Para obter mais informações, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Consulte o tutorial de [monitorização e gestão](saas-dbpertenant-performance-monitoring.md) do Desempenho para uma discussão sobre cenários e padrões SaaS e como eles afetam os requisitos numa solução de monitorização.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>Monitore e gerencie base de dados e desempenho de piscina elástica com registos do Monitor Azure

Para a Base de Dados Azure SQL, a monitorização e alerta estão disponíveis em bases de dados e piscinas no portal Azure. Esta monitorização e alerta incorporados é conveniente, mas também é específico de recursos. Isto significa que é menos adequado para monitorizar grandes instalações ou fornecer uma visão unificada entre recursos e subscrições.

Para cenários de grande volume, pode utilizar registos do Monitor Azure para monitorização e alerta. O Azure Monitor é um serviço Azure separado que permite análises sobre registos recolhidos num espaço de trabalho a partir de potenciais muitos serviços. Os registos do Azure Monitor fornecem uma linguagem de consulta incorporada e ferramentas de visualização de dados que permitem análise operacional de dados. A solução SQL Analytics fornece várias análises elásticas predefinidas e monitorização de bases de dados e alerta de vistas e consultas. Os registos do Azure Monitor também fornecem um designer de vistas personalizado.

As áreas de trabalho do OMS são agora referidas como áreas de trabalho do Log Analytics. Os espaços de trabalho do Log Analytics e as soluções de análise abrem no portal Azure. O portal Azure é o mais recente ponto de acesso, mas pode ser o que está por trás do portal Da Suite de Gestão de Operações em algumas áreas.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Crie dados de diagnóstico de desempenho simulando uma carga de trabalho nos seus inquilinos 

1. No PowerShell ISE, abra *.. WingtipTicketsSaaS-MultiTenantDb-master\\Learning\\Modules Performance\\Monitoring and Management Demo-PerformanceMonitoringAndManagement.ps1 . \\* Mantenha este guião aberto porque talvez queira executar vários dos cenários de geração de carga durante este tutorial.
1. Se ainda não o fez, proíbe um lote de inquilinos para tornar o contexto de monitorização mais interessante. Este processo leva alguns minutos.

   a. Definir **$DemoScenario = 1**, _Provisionia um lote de inquilinos._

   b. Para executar o guião e implementar mais 17 inquilinos, prima F5.

1. Agora ligue o gerador de carga para executar uma carga simulada em todos os inquilinos.

    a. Definir **$DemoScenario = 2**, Gere uma carga de intensidade normal _(aproximadamente 30 DTU)_.

    b. Para executar o guião, prima F5.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtenha os scripts de aplicação de bases de dados SaaS de bilhetes de ala e de bilhetes por inquilino

Os scripts de base de dados multiarrendatários SaaS e código de fonte de aplicação estão disponíveis no repo [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub. Para que os passos descarreguem e desbloqueiem os scripts PowerShell dos Bilhetes wingtip, consulte a [orientação geral](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>Instale e configure o espaço de trabalho do Log Analytics e a solução Azure SQL Analytics

O Monitor Azure é um serviço separado que deve ser configurado. Os registos do Azure Monitor recolhem dados de registo, telemetria e métricas num espaço de trabalho do Log Analytics. Tal como outros recursos em Azure, deve ser criado um espaço de trabalho log Analytics. O espaço de trabalho não precisa de ser criado no mesmo grupo de recursos que as aplicações que monitoriza. Fazê-lo muitas vezes faz mais sentido. Para a aplicação Wingtip Tickets, utilize um único grupo de recursos para garantir que o espaço de trabalho seja eliminado com a aplicação.

1. No PowerShell ISE, abra *.. WingtipTicketsSaaS-MultiTenantDb-master\\Learning\\Modules Performance\\Monitoring\\and Management Log Analytics Demo-LogAnalytics.ps1 . \\*
1. Para executar o guião, prima F5.

Agora pode abrir os registos do Monitor Azure no portal Azure. Leva alguns minutos para recolher telemetria no espaço de trabalho log Analytics e torná-lo visível. Quanto mais tempo deixar o sistema recolher dados de diagnóstico, mais interessante é a experiência. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Utilize o espaço de trabalho log Analytics e a solução SQL Analytics para monitorizar piscinas e bases de dados


Neste exercício, abra o espaço de trabalho log analytics no portal Azure para olhar para a telemetria recolhida para as bases de dados e piscinas.

1. Navegue pelo [portal Azure.](https://portal.azure.com) Selecione **todos os serviços** para abrir o espaço de trabalho do Log Analytics. Em seguida, procure o Log Analytics.

   ![Espaço de trabalho open Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Selecione o espaço de trabalho denominado _wtploganalytics-&lt;utilizador&gt;_.

1. Selecione **Visão Geral** para abrir a solução de análise de registo no portal Azure.

   ![Descrição geral](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Pode levar alguns minutos até a solução estar ativa. 

1. Selecione o azulejo **Azure SQL Analytics** para abri-lo.

    ![Azulejo de visão geral](media/saas-dbpertenant-log-analytics/overview.png)

1. As vistas da solução percorrem lateralmente, com a sua própria barra de pergaminho interior na parte inferior. Refresque a página, se necessário.

1. Para explorar a página de resumo, selecione os azulejos ou bases de dados individuais para abrir um explorador de perfuração.

    ![Painel de análise de log analytics](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Mude a regulação do filtro para modificar o intervalo de tempo. Para este tutorial, selecione **Last 1 hora**.

    ![Filtro de tempo](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Selecione uma base de dados individual para explorar a utilização da consulta e métricas para essa base de dados.

    ![Análise de base de dados](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Para ver as métricas de utilização, percorra a página de análise para a direita.
 
     ![Métricas de base de dados](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Percorra a página de análise para a esquerda e selecione o azulejo do servidor na lista **de Informações** de Recursos.  

    ![Lista de Informações de Recursos](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Uma página abre que mostra as piscinas e bases de dados no servidor.

    ![Servidor com piscinas e bases de dados](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Selecione uma piscina. Na página da piscina que se abre, percorra para a direita para ver as métricas da piscina. 

    ![Métricas da piscina](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. De volta ao espaço de trabalho do Log Analytics, selecione **OMS Portal** para abrir o espaço de trabalho lá.

    ![Área de trabalho do Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

No espaço de trabalho do Log Analytics, pode explorar ainda mais o log e os dados métricos. 

A monitorização e alerta nos registos do Monitor Azure baseiam-se em consultas sobre os dados no espaço de trabalho, ao contrário do alerta definido em cada recurso no portal Azure. Ao basear alertas em consultas, pode definir um único alerta que olha para todas as bases de dados, em vez de definir um por base de dados. As consultas são limitadas apenas pelos dados disponíveis no espaço de trabalho.

Para obter mais informações sobre como utilizar os registos do Monitor Azure para consulta e definir alertas, consulte Trabalhar com regras de [alerta nos registos do Monitor Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Registos do Monitor Azure para encargos de base de dados SQL com base no volume de dados no espaço de trabalho. Neste tutorial, criou-se um espaço de trabalho gratuito, limitado a 500 MB por dia. Depois de atingido esse limite, os dados deixaram de ser adicionados ao espaço de trabalho.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Instale e configure os registos do Monitor Azure.
> * Utilize registos do Monitor Azure para monitorizar piscinas e bases de dados.

Experimente o tutorial de análise do [Inquilino.](saas-dbpertenant-log-analytics.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que se baseiam na implementação inicial de aplicações de bases de dados SaaS de bilhetes de base de dados SaaS por inquilino](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Registos do Monitor Azure](../azure-monitor/insights/azure-sql.md)
