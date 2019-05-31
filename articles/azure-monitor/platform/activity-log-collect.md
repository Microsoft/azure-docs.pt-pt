---
title: Recolher e analisar registos de atividades do Azure na área de trabalho do Log Analytics | Documentos da Microsoft
description: Recolher o registo de atividades do Azure nos Logs de Monitor do Azure e utilizar a solução de monitorização para analisar e pesquisar o registo de atividades do Azure em todas as suas subscrições do Azure.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.openlocfilehash: 5839fd40a128097e400f13acbe4fb6ef90c656b7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66248133"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Recolher e analisar registos de atividades do Azure na área de trabalho do Log Analytics no Azure Monitor
O [registo de atividades do Azure](activity-logs-overview.md) fornece informações sobre os eventos de nível de assinatura que ocorreram na sua subscrição do Azure. Este artigo descreve como recolher o registo de atividades para uma área de trabalho do Log Analytics e como utilizar o Log Analytics da atividade [solução de monitorização](../insights/solutions.md), que fornece consultas de registo e vistas para analisar estes dados. 

Ligar o registo de atividades para uma área de trabalho do Log Analytics fornece as seguintes vantagens:

- Consolide o registo de atividades de várias subscrições do Azure num só local para análise.
- Store entradas de registo de atividades durante mais de 90 dias.
- Correlacione dados de registo de atividades com outros dados de monitorização recolhidos pelo Monitor do Azure.
- Uso [registar as consultas](../log-query/log-query-overview.md) para efetuar análises complexas e informações mais detalhadas sobre as entradas de registo de atividades.

## <a name="connect-to-log-analytics-workspace"></a>Ligar à área de trabalho do Log Analytics
Um registo de atividades pode ser conectado a apenas uma área de trabalho, mas pode ser ligado um único espaço de trabalho para o registo de atividades para várias subscrições no mesmo inquilino do Azure. Para a coleção em vários inquilinos, consulte [recolher registos de atividades do Azure para uma área de trabalho do Log Analytics entre subscrições no diferentes do Azure Active Directory inquilinos](activity-log-collect-tenants.md).

Utilize o procedimento seguinte para ligar o registo de atividades para a área de trabalho do Log Analytics:

1. Partir do **áreas de trabalho do Log Analytics** menu no portal do Azure, selecione a área de trabalho para recolher o registo de atividades.
1. Na **origens de dados de área de trabalho** secção do menu da área de trabalho, selecione **registo de atividades do Azure**.
1. Clique na subscrição a que se pretende ligar.

    ![Áreas de Trabalho](media/activity-log-export/workspaces.png)

1. Clique em **Connect** para ligar o registo de atividades na subscrição para a área de trabalho selecionada. Se a subscrição já está ligada a outra área de trabalho, clique em **desligar** pela primeira vez ao desligar a ele.

    ![Ligar as áreas de trabalho](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>Analisar na área de trabalho do Log Analytics
Quando se liga um registo de atividade para uma área de trabalho do Log Analytics, as entradas serão escritas para a área de trabalho numa tabela denominada **AzureActivity** que pode obter com um [consulta de registo](../log-query/log-query-overview.md). A estrutura desta tabela varia consoante a [categoria de entrada de log](activity-logs-overview.md#categories-in-the-activity-log). Ver [esquema de eventos de registo de atividades do Azure](activity-log-schema.md) para obter uma descrição de cada categoria.

## <a name="activity-logs-analytics-monitoring-solution"></a>Solução de monitorização de análise de registos de atividade
A solução de monitorização do Azure Log Analytics inclui várias consultas de registo e as vistas de analisar os registos de registo de atividades na sua área de trabalho do Log Analytics.

### <a name="install-the-solution"></a>Instalar a solução
Utilize o procedimento [instalar uma solução de monitorização](../insights/solutions.md#install-a-monitoring-solution) para instalar o **Log Analytics da atividade** solução. Não é necessária nenhuma configuração adicional.

### <a name="use-the-solution"></a>Utilizar a solução
Soluções de monitorização são acessadas a partir da **Monitor** menu no portal do Azure. Selecione **mais** no **Insights** secção para abrir o **descrição geral** página com os mosaicos de solução. O **registos de atividades do Azure** mosaico apresenta uma contagem do número de **AzureActivity** registos na sua área de trabalho.

![Mosaico de registos de atividades do Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Clique nas **registos de atividades do Azure** mosaico para abrir o **registos de atividades do Azure** vista. A vista inclui as partes de visualização na tabela seguinte. Cada parte da lista até 10 itens que satisfaçam os critérios que partes para o intervalo de tempo especificado. Pode executar uma consulta de registo que devolve todos os registos correspondentes ao clicar em **ver todas as** na parte inferior da parte.

![Dashboard de registos de atividades do Azure](media/collect-activity-logs/activity-log-dash.png)

| Parte da visualização | Descrição |
| --- | --- |
| Entradas de registo de atividades do Azure | Mostra um gráfico de barras de entrada de registo de atividades do Azure superior totais de registo para o intervalo de datas selecionado e mostra uma lista dos chamadores de 10 atividade principais. Clique para executar uma pesquisa de registos para o gráfico de barras `AzureActivity`. Clique num item de autor da chamada para executar uma pesquisa de registos a devolver todas as entradas de registo de atividades para esse item. |
| Registos de atividades por Estado | Mostra um gráfico de anel para obter o estado de registo de atividades do Azure para o intervalo de datas selecionado e uma lista de registos de estado de dez principais. Clique no gráfico para executar uma consulta de registo para `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`. Clique num item de estado para executar uma pesquisa de registos que devolva todas as entradas de registo de atividades para esse registo de estado. |
| Registos de atividades por recurso | Mostra o número total de recursos com registos de atividade e apresenta uma lista de topo contagens de dez recursos com o registo para cada recurso. Clique na área total para executar uma pesquisa de registos para `AzureActivity | summarize AggregatedValue = count() by Resource`, que mostra todos os recursos do Azure disponíveis para a solução. Clique num recurso para executar uma consulta de registo retornando todos os registos de atividade para esse recurso. |
| Registos de atividade pelo fornecedor de recursos | Mostra o número total de fornecedores de recursos que produzem registos de atividades e lista as dez principais. Clique na área total para executar uma consulta de registo para `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`, que mostra todos os fornecedores de recursos do Azure. Clique num fornecedor de recursos para executar uma consulta de registo retornando todos os registos de atividade para o fornecedor. |

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [registo de atividades](activity-logs-overview.md).
- Saiba mais sobre o [plataforma de dados do Azure Monitor](data-platform.md).
- Uso [registar as consultas](../log-query/log-query-overview.md) para ver informações detalhadas do seu registo de atividades.
