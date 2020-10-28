---
title: Registos do Azure Monitor com uma aplicação multitenant
description: Configurar e utilizar registos Azure Monitor com uma aplicação SaaS de Base de Dados Azure SQL multitenant
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
ms.openlocfilehash: a078ba6147d4d874a890f406563111b6fdb82ed6
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92780908"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-azure-sql-database-saas-app"></a>Configurar e utilizar registos Azure Monitor com uma aplicação SaaS de Base de Dados Azure SQL multitenant
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste tutorial, você configura e utiliza [os registos do Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) para monitorizar piscinas elásticas e bases de dados. Este tutorial baseia-se no [tutorial de monitorização e gestão de desempenho.](saas-dbpertenant-performance-monitoring.md) Mostra como utilizar registos do Azure Monitor para aumentar a monitorização e alerta fornecidas no portal Azure. Os registos do Azure Monitor suportam a monitorização de milhares de piscinas elásticas e centenas de milhares de bases de dados. Os registos do Azure Monitor fornecem uma única solução de monitorização, que pode integrar a monitorização de diferentes aplicações e serviços Azure em várias subscrições do Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Instale e configuize registos do Monitor Azure.
> * Utilize registos do Monitor Azure para monitorizar piscinas e bases de dados.

Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* A aplicação Wingtip Tickets SaaS para o arrendamento. Para implementar em menos de cinco minutos, consulte [implementar e explorar a aplicação de base de dados SaaS por insurreto de bilhetes wingtip.](./saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais informações, veja [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps).

Consulte o [tutorial de monitorização e gestão](saas-dbpertenant-performance-monitoring.md) de desempenho para uma discussão sobre cenários e padrões do SaaS e como afetam os requisitos numa solução de monitorização.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>Monitorize e gere o desempenho da base de dados e do desempenho elástico da piscina com registos do Azure Monitor

Para a Base de Dados Azure SQL, a monitorização e alerta estão disponíveis em bases de dados e piscinas no portal Azure. Esta monitorização e alerta incorporados é conveniente, mas também é específica de recursos. Isso significa que é menos adequado para monitorizar grandes instalações ou fornecer uma visão unificada entre recursos e subscrições.

Para cenários de grande volume, pode utilizar registos do Monitor Azure para monitorização e alerta. O Azure Monitor é um serviço Azure separado que permite análises sobre registos recolhidos num espaço de trabalho de potencialmente muitos serviços. Os registos do Azure Monitor fornecem uma linguagem de consulta incorporada e ferramentas de visualização de dados que permitem a análise de dados operacionais. A solução SQL Analytics fornece vários pool elásticos predefinidos e monitorização de bases de dados e alertando vistas e consultas. Os registos do Azure Monitor também fornecem um designer de visualização personalizado.

As áreas de trabalho do OMS são agora referidas como áreas de trabalho do Log Analytics. Log Analytics espaços de trabalho e soluções de análise abertas no portal Azure. O portal Azure é o mais recente ponto de acesso, mas pode ser o que está por trás do portal Da Suite de Gestão de Operações em algumas áreas.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Crie dados de diagnóstico de desempenho simulando uma carga de trabalho nos seus inquilinos 

1. No PowerShell ISE, abra *.. \\ WingtipTicketsSa-MultiTenantDb-master \\ Learning Modules \\ De monitorização e Gestão \\Demo-PerformanceMonitoringAndManagement.ps1* . Mantenha este script aberto porque pode querer executar vários dos cenários de geração de carga durante este tutorial.
1. Se ainda não o fez, providenciará um lote de inquilinos para tornar o contexto de monitorização mais interessante. Este processo leva alguns minutos.

   a. Definir **$DemoScenario = 1** , _Provision a lote de inquilinos_ .

   b. Para executar o guião e colocar mais 17 inquilinos, pressione F5.

1. Agora ligue o gerador de carga para executar uma carga simulada em todos os inquilinos.

    a. Definir **$DemoScenario = 2** , _Gerar carga de intensidade normal (aproximadamente 30 DTU)_ .

    b. Para executar o guião, prima F5.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtenha os scripts de aplicação de aplicações de ingressos saas de ponta da ponta da asa

Os scripts de base de dados multitenantes Wingtip Tickets SaaS e o código fonte de aplicação estão disponíveis no [wingtipTicketsSaAS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub. Para obter medidas para descarregar e desbloquear os scripts PowerShell dos bilhetes wingtip, consulte a [orientação geral](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>Instale e configuure log analytics espaço de trabalho e a solução Azure SQL Analytics

O Azure Monitor é um serviço separado que deve ser configurado. Os registos do Azure Monitor recolhem dados de registo, telemetria e métricas num espaço de trabalho do Log Analytics. Tal como outros recursos em Azure, deve ser criado um espaço de trabalho Log Analytics. O espaço de trabalho não precisa de ser criado no mesmo grupo de recursos que as aplicações que monitoriza. Fazê-lo muitas vezes faz mais sentido. Para a aplicação Wingtip Tickets, utilize um único grupo de recursos para garantir que o espaço de trabalho é eliminado com a aplicação.

1. No PowerShell ISE, abra *.. \\ WingtipTicketsSaA-MultiTenantDb-master \\ Learning Modules \\ Monitoring e Management \\ Log Analytics \\Demo-LogAnalytics.ps1* .
1. Para executar o guião, prima F5.

Agora pode abrir registos do Azure Monitor no portal Azure. Demora alguns minutos a recolher telemetria no espaço de trabalho do Log Analytics e a torná-la visível. Quanto mais tempo saíres do sistema a recolher dados de diagnóstico, mais interessante é a experiência. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Use o espaço de trabalho do Log Analytics e a solução SQL Analytics para monitorizar piscinas e bases de dados


Neste exercício, abra o espaço de trabalho do Log Analytics no portal Azure para ver a telemetria recolhida para as bases de dados e piscinas.

1. Navegue pelo [portal Azure](https://portal.azure.com). Selecione **Todos os serviços** para abrir o espaço de trabalho do Log Analytics. Então, procure por Log Analytics.

   ![Espaço de trabalho Open Log Analytics](./media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Selecione o espaço de trabalho denominado _wtploganalytics- &lt; utilizador &gt;_ .

1. Selecione **visão geral** para abrir a solução de análise de registo no portal Azure.

   ![Descrição geral](./media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Pode levar alguns minutos até que a solução esteja ativa. 

1. Selecione o azulejo **Azure SQL Analytics** para o abrir.

    ![Azulejo geral](./media/saas-dbpertenant-log-analytics/overview.png)

1. As vistas na solução percorrem de lado, com a sua própria barra de deslocamento interior na parte inferior. Refresque a página se necessário.

1. Para explorar a página do resumo, selecione os azulejos ou bases de dados individuais para abrir um explorador de perfuração.

    ![Painel de análise de registo](./media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Altere a definição do filtro para modificar o intervalo de tempo. Para este tutorial, selecione **Last 1 hora** .

    ![Filtro de tempo](./media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Selecione uma base de dados individual para explorar a utilização e métricas de consulta para essa base de dados.

    ![Análise de bases de dados](./media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Para ver as métricas de utilização, percorra a página de análise para a direita.
 
     ![Métricas de base de dados](./media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Desloque a página de análise para a esquerda e selecione o azulejo do servidor na lista **de Informações de Recursos.**  

    ![Lista de Informações de Recursos](./media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Abre-se uma página que mostra as piscinas e bases de dados no servidor.

    ![Servidor com piscinas e bases de dados](./media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Selecione uma piscina. Na página da piscina que abre, percorra para a direita para ver as métricas da piscina. 

    ![Métricas da piscina](./media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. De volta ao espaço de trabalho Log Analytics, selecione **OMS Portal** para abrir o espaço de trabalho lá.

    ![Área de trabalho do Log Analytics](./media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

No espaço de trabalho Log Analytics, pode explorar mais os dados de registo e métrica. 

A monitorização e alerta nos registos do Azure Monitor baseiam-se em consultas sobre os dados no espaço de trabalho, ao contrário do alerta definido em cada recurso no portal Azure. Ao basear alertas em consultas, pode definir um único alerta que analisa todas as bases de dados, em vez de definir um por base de dados. As consultas são limitadas apenas pelos dados disponíveis no espaço de trabalho.

Para obter mais informações sobre como utilizar registos do Azure Monitor para consultar e definir alertas, consulte [Trabalhar com regras de alerta nos registos do Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

Registos do Azure Monitor para taxas de base de dados SQL com base no volume de dados no espaço de trabalho. Neste tutorial, criou um espaço de trabalho gratuito, que está limitado a 500 MB por dia. Após esse limite ser atingido, os dados deixaram de ser adicionados ao espaço de trabalho.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Instale e configuize registos do Monitor Azure.
> * Utilize registos do Monitor Azure para monitorizar piscinas e bases de dados.

Experimente o tutorial de análise do [Inquilino.](saas-dbpertenant-log-analytics.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que se baseiam na implementação inicial de aplicações de aplicações wingtip saaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Registos do Azure Monitor](../../azure-monitor/insights/azure-sql.md)