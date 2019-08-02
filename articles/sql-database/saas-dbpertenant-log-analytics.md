---
title: Usar logs de Azure Monitor com um aplicativo de multilocatário do banco de dados SQL | Microsoft Docs
description: Configurar e usar logs de Azure Monitor com um aplicativo SaaS multilocatário do banco de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 01/25/2019
ms.openlocfilehash: 6b9b2239cfdf0f214ed2f2b179978fe2828d1be3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570507"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-sql-database-saas-app"></a>Configurar e usar logs de Azure Monitor com um aplicativo SaaS de banco de dados SQL multilocatário

Neste tutorial, você configura e usa logs de [Azure monitor](/azure/log-analytics/log-analytics-overview) para monitorar pools elásticos e bancos de dados. Este tutorial se baseia no [tutorial de gerenciamento e monitoramento de desempenho](saas-dbpertenant-performance-monitoring.md). Ele mostra como usar os logs de Azure Monitor para aumentar o monitoramento e os alertas fornecidos no portal do Azure. Os logs de Azure Monitor dão suporte ao monitoramento de milhares de pools elásticos e centenas de milhares de bancos de dados. Os logs de Azure Monitor fornecem uma única solução de monitoramento, que pode integrar o monitoramento de diferentes aplicativos e serviços do Azure entre várias assinaturas do Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Instalar e configurar logs de Azure Monitor.
> * Use logs de Azure Monitor para monitorar pools e bancos de dados.

Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

* O aplicativo de banco de dados por locatário SaaS Wingtip tickets é implantado. Para implantar em menos de cinco minutos, consulte [implantar e explorar o aplicativo de banco de dados por locatário SaaS Wingtip tickets](saas-dbpertenant-get-started-deploy.md).
* O Azure PowerShell está instalado. Para obter mais informações, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Consulte o [tutorial de monitoramento e gerenciamento de desempenho](saas-dbpertenant-performance-monitoring.md) para uma discussão sobre padrões e cenários de SaaS e como eles afetam os requisitos de uma solução de monitoramento.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>Monitorar e gerenciar o desempenho do banco de dados e do pool elástico com logs de Azure Monitor

Para o banco de dados SQL do Azure, o monitoramento e o alerta estão disponíveis em databases e pools no portal do Azure. Esse monitoramento e alerta internos são convenientes, mas também específicos ao recurso. Isso significa que ele é menos adequado para monitorar grandes instalações ou fornecer uma exibição unificada entre recursos e assinaturas.

Para cenários de alto volume, você pode usar logs de Azure Monitor para monitoramento e alertas. Azure Monitor é um serviço do Azure separado que permite a análise em logs de diagnóstico e telemetria coletados em um espaço de trabalho de potencialmente muitos serviços. Os logs de Azure Monitor fornecem uma linguagem de consulta interna e ferramentas de visualização de dados que permitem a análise de dados operacional. A solução de análise de SQL fornece vários modos de exibição e consultas de alertas e monitoramento de banco de dados e pool elástico predefinidos. Os logs de Azure Monitor também fornecem um designer de exibição personalizado.

As áreas de trabalho do OMS são agora referidas como áreas de trabalho do Log Analytics. Log Analytics os espaços de trabalho e soluções de análise são abertos no portal do Azure. O portal do Azure é o ponto de acesso mais recente, mas pode ser o que está por trás do portal do Operations Management Suite em algumas áreas.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Crie dados de diagnóstico de desempenho simulando uma carga de trabalho em seus locatários 

1. No ISE do PowerShell, abra *.. Repositório wingtipticketssaas-MultiTenantDb-Master\\módulos\\de aprendizado monitoramento e gerenciamento\\de desempenho demo-performancemonitoringandmanagement. ps1. \\* Mantenha este script aberto porque talvez você queira executar vários cenários de geração de carga durante este tutorial.
1. Se você ainda não tiver feito isso, provisione um lote de locatários para tornar o contexto de monitoramento mais interessante. Esse processo leva alguns minutos.

   a. Defina **$DemoScenario = 1**, _provisionar um lote de locatários_.

   b. Para executar o script e implantar mais 17 locatários, pressione F5.

1. Agora, inicie o gerador de carga para executar uma carga simulada em todos os locatários.

    a. Defina **$DemoScenario = 2**, _gerar carga de intensidade normal (aproximadamente 30 DTU)_ .

    b. Para executar o script, pressione F5.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts de aplicativo de banco de dados por locatário SaaS Wingtip tickets

Os scripts de banco de dados multilocatário do Wingtip tickets SaaS e o código-fonte do aplicativo estão disponíveis no repositório GitHub [repositório wingtipticketssaas-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) . Para obter as etapas para baixar e desbloquear os scripts do PowerShell do Wingtip tickets, consulte as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>Instalar e configurar Log Analytics espaço de trabalho e a solução de Análise de SQL do Azure

Azure Monitor é um serviço separado que deve ser configurado. Os logs de Azure Monitor coletam dados de log, telemetria e métricas em um espaço de trabalho Log Analytics. Assim como outros recursos no Azure, um espaço de trabalho Log Analytics deve ser criado. O espaço de trabalho não precisa ser criado no mesmo grupo de recursos que os aplicativos monitorados. No entanto, isso geralmente faz mais sentido. Para o aplicativo Wingtip tickets, use um único grupo de recursos para garantir que o espaço de trabalho seja excluído com o aplicativo.

1. No ISE do PowerShell, abra *.. \\\\Repositório wingtipticketssaas-MultiTenantDb-Master\\\\módulos de aprendizado monitoramento e gerenciamento de desempenho log Analytics demo-loganalytics. ps1. \\*
1. Para executar o script, pressione F5.

Agora você pode abrir logs de Azure Monitor no portal do Azure. Leva alguns minutos para coletar telemetria no espaço de trabalho Log Analytics e torná-lo visível. Quanto mais tempo você deixar o sistema coletando dados de diagnóstico, mais interessante será a experiência. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Use Log Analytics espaço de trabalho e a solução de análise de SQL para monitorar pools e bancos de dados


Neste exercício, abra Log Analytics espaço de trabalho no portal do Azure para examinar a telemetria coletada para os bancos de dados e pools.

1. Navegue para o [portal do Azure](https://portal.azure.com). Selecione **todos os serviços** para abrir log Analytics espaço de trabalho. Em seguida, pesquise Log Analytics.

   ![Abrir Log Analytics espaço de trabalho](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Selecione o espaço de trabalho chamado _wtploganalytics&gt;-&lt;User_.

1. Selecione **visão geral** para abrir a solução log analytics no portal do Azure.

   ![Descrição geral](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Pode levar alguns minutos antes que a solução esteja ativa. 

1. Selecione o bloco **análise de SQL do Azure** para abri-lo.

    ![Mosaico de descrição geral](media/saas-dbpertenant-log-analytics/overview.png)

1. As exibições na solução rolam para a lateral, com sua própria barra de rolagem interna na parte inferior. Atualize a página, se necessário.

1. Para explorar a página Resumo, selecione os blocos ou bancos de dados individuais para abrir um Gerenciador de busca detalhada.

    ![Painel do log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Altere a configuração de filtro para modificar o intervalo de tempo. Para este tutorial, selecione **última 1 hora**.

    ![Filtro de tempo](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Selecione um banco de dados individual para explorar o uso da consulta e as métricas para esse banco de dados.

    ![Análise de banco de dados](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Para ver as métricas de uso, role a página de análise para a direita.
 
     ![Métricas do banco de dados](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Role a página de análise para a esquerda e selecione o bloco do servidor na lista **informações do recurso** .  

    ![Lista de informações do recurso](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    É aberta uma página que mostra os pools e os bancos de dados no servidor.

    ![Servidor com pools e bancos de dados](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Selecione um pool. Na página pool que é aberta, role para a direita para ver as métricas do pool. 

    ![Métricas do pool](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. De volta ao espaço de trabalho Log Analytics, selecione **portal do OMS** para abrir o espaço de trabalho.

    ![Área de trabalho do Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

No espaço de trabalho Log Analytics, você pode explorar ainda mais os dados de log e métrica. 

O monitoramento e os alertas em logs de Azure Monitor são baseados em consultas sobre os dados no espaço de trabalho, ao contrário do alerta definido em cada recurso na portal do Azure. Ao basear alertas em consultas, você pode definir um único alerta que examina todos os bancos de dados, em vez de definir um por banco de dados. As consultas são limitadas apenas pelos dados disponíveis no espaço de trabalho.

Para obter mais informações sobre como usar os logs de Azure Monitor para consultar e definir alertas, consulte [trabalhar com regras de alerta em logs de Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Azure Monitor logs de encargos do banco de dados SQL com base no volume do espaço de trabalho. Neste tutorial, você criou um espaço de trabalho gratuito, limitado a 500 MB por dia. Depois que esse limite for atingido, os dados não serão mais adicionados ao espaço de trabalho.


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Instalar e configurar logs de Azure Monitor.
> * Use logs de Azure Monitor para monitorar pools e bancos de dados.

Experimente o [tutorial de análise de locatário](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Recursos adicionais

* [TUTORIAIS adicionais que se baseiam na implantação inicial do aplicativo de banco de dados por locatário SaaS Wingtip tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Registos do Azure Monitor](../azure-monitor/insights/azure-sql.md)
