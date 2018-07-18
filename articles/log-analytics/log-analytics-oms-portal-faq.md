---
title: Perguntas comuns para a transição do portal do OMS para o portal do Azure para utilizadores do Log Analytics | Documentos da Microsoft
description: Respostas a perguntas comuns para utilizadores do Log Analytics, fazer a transição do portal do OMS para o portal do Azure.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 503d5913efe67bd0de738f68921b9631c63acfa8
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39116049"
---
# <a name="common-questions-for-transition-from-oms-portal-to-azure-portal-for-log-analytics-users"></a>Perguntas comuns para a transição do portal do OMS para o portal do Azure para utilizadores do Log Analytics
O log Analytics utilizado inicialmente o seu próprio portal chamado no portal do OMS para gerir a sua configuração e analisar os dados recolhidos.  Toda a funcionalidade deste portal foi movida para o portal do Azure onde irá continuar a ser desenvolvidas.

Este artigo responde a perguntas comuns para os usuários fazendo transição.  Se tiver utilizado o Log Analytics no portal do OMS, pode encontrar respostas aqui para como executar as mesmas tarefas no portal do Azure.

## <a name="do-i-need-to-migrate-anything"></a>É necessário migrar qualquer coisa?
Não. Não há nenhuma alteração sendo feitas para o Log Analytics por si só, portanto, não há nada que tem de ser migrada. A única coisa que é a alteração é a interface que utilizar para aceder à mesma. Na verdade, pode utilizar o portal do Azure agora para aceder a áreas de trabalho do mesmo, soluções, vistas e pesquisas que utilizar de registos no portal do OMS hoje mesmo.

## <a name="where-do-i-find-log-analytics-in-azure"></a>Onde posso encontrar o Log Analytics no Azure?
Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com).  Clique em **todos os serviços**e, na lista de recursos, escreva **do Log Analytics**. Selecione **do Log Analytics** e, em seguida, selecione a área de trabalho. É apresentada a página de resumo para a área de trabalho.

![Área de trabalho do Log Analytics](media/log-analytics-new-portal/log-analytics.png)

## <a name="how-do-i-manage-permissions"></a>Como posso gerir as permissões?
Se não tiver acesso à área de trabalho do Log Analytics no portal do Azure, tem de configurar as suas permissões usando [acesso baseado em função do Azure](../active-directory/role-based-access-control-configure.md). Para obter detalhes sobre a gestão de permissões de área de trabalho, consulte [gerir áreas de trabalho](../log-analytics/log-analytics-manage-access.md#manage-accounts-and-users). Para obter informações sobre o gerenciamento de permissões para alertas, consulte [começar com as funções, permissões e segurança com o Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md).

## <a name="how-do-i-create-a-new-workspace"></a>Como posso criar uma nova área de trabalho? 
Na lista de áreas de trabalho no portal do Azure, clique em **adicionar** na lista de áreas de trabalho.  Para obter detalhes completos, veja [criar uma área de trabalho do Log Analytics no portal do Azure](../log-analytics/log-analytics-quick-create-workspace.md).

![Página de descrição geral](media/log-analytics-new-portal/new-workspace.png)

## <a name="where-is-my-overview-page"></a>Onde posso encontrar minha página de descrição geral?
A tela principal no portal do OMS exibe os mosaicos para todas as soluções de gestão instalados na sua área de trabalho e todas as vistas personalizadas que criou. Esta mesma exibição está disponível no portal do Azure. Na área de trabalho, selecione **resumo de área de trabalho**.

![Página de descrição geral](media/log-analytics-new-portal/overview.png)

## <a name="how-do-i-open-log-search-and-view-designer"></a>Como abrir e exibir Designer de pesquisa de registos?
Ambos **pesquisa de registos** e **estruturador de vistas** estão disponíveis na página principal e no menu à esquerda de sua área de trabalho no portal do Azure, à direita junto a **descrição geral**.

## <a name="where-do-i-find-settings"></a>Onde posso encontrar as definições?
Muitas das definições do **definições** seção do portal do OMS estão disponíveis no **definições avançadas** menu no portal do Azure para a área de trabalho.

![Definições avançadas](media/log-analytics-new-portal/advanced-settings.png)

As secções seguintes fornecem uma lista completa de como pode aceder às definições que estavam disponíveis anteriormente no **definições** seção do portal do OMS.

### <a name="accounts"></a>Contas 
Definições de contas são geridas em locais diferentes no portal do Azure, conforme descrito na tabela seguinte.

| Definição no portal do OMS | Equivalente no portal do Azure |
|:---|:---|
| Conta de Automatização | **Conta de automatização** menu para a área de trabalho. |
| Subscrição do Azure e o plano de dados | **Escalão de preço** menu para a área de trabalho. |
| Gerir utilizadores | Utilizar o acesso baseado em funções do Azure para [gerir permissões para a sua área de trabalho](#how-do-i-manage-permissions). |
| Informações da Área de Trabalho | Informações disponíveis no **área de trabalho OMS** menu para a área de trabalho. |

### <a name="alerts"></a>Alertas
Regras de alerta com base em consultas do Log Analytics são agora geridas na [unified a experiência de alertas](#how-do-i-create-and-manage-alerts). 

### <a name="computer-groups"></a>Grupos de Computadores
Gerir grupos de computadores no **definições avançadas** menu para a área de trabalho. 

### <a name="connected-sources"></a>Origens Ligadas
Gerir a maioria das definições de origem ligado na **definições avançadas** menu para a área de trabalho. A tabela seguinte fornece detalhes para cada seção desse menu.

| Definição no portal do OMS | Equivalente no portal do Azure |
|:---|:---|
| Servidores Windows   | **Definições avançadas** menu para a área de trabalho. |
| Servidores Linux   | **Definições avançadas** menu para a área de trabalho. |
| Storage do Azure     | **Definições avançadas** menu para a área de trabalho. |
| System Center     | **Definições avançadas** menu para a área de trabalho. |
| Office 365        | Consulte a [documentação para solução de gestão do Office 365](../operations-management-suite/oms-solution-office-365.md) para obter detalhes de configuração. |
| Telemetria Windows | Ainda não está disponível no portal do Azure. |
| Conector ITSM    | Ver [produtos/serviços de estabelecer ligação ITSM com o conector de gestão do serviço de TI](../log-analytics/log-analytics-itsmc-connections.md) para obter instruções sobre como ligar o seu serviço ITSM com o Log Analytics. |

### <a name="data"></a>Dados
Gerir a maioria das definições de dados na **definições avançadas** menu para a área de trabalho. A tabela seguinte fornece detalhes para cada seção desse menu.

| Definição no portal do OMS | Equivalente no portal do Azure |
|:---|:---|
| Registos de Eventos do Windows           | **Definições avançadas** menu para a área de trabalho. |
| Contadores de desempenho do Windows | **Definições avançadas** menu para a área de trabalho. |
| Contadores de desempenho do Linux   | **Definições avançadas** menu para a área de trabalho. |
| Registos do IIS                     | **Definições avançadas** menu para a área de trabalho. |
| Campos Personalizados                | **Definições avançadas** menu para a área de trabalho. |
| Registos Personalizados                  | **Definições avançadas** menu para a área de trabalho. |
| Syslog                       | **Definições avançadas** menu para a área de trabalho. |
| Application Insights         | Esta solução foi preterida agora que o Log Analytics e Application Insights partilharem o mesmo motor de dados.  |
| Controlo de Ficheiros do Windows        | **Controlo de alterações** menu na automatização do Azure. Ver [controlar alterações no seu ambiente com a solução de controlo de alterações](../automation/automation-change-tracking.md) para obter detalhes. |
| Controlo de Registos do Windows        | **Controlo de alterações** menu na automatização do Azure. Ver [controlar alterações no seu ambiente com a solução de controlo de alterações](../automation/automation-change-tracking.md) para obter detalhes. |
| Controlo de Ficheiros do Linux          | **Controlo de alterações** menu na automatização do Azure. Ver [controlar alterações no seu ambiente com a solução de controlo de alterações](../automation/automation-change-tracking.md) para obter detalhes. |

### <a name="solutions"></a>Soluções
Gerir soluções na **soluções** menu para a área de trabalho. 

## <a name="how-do-i-install-and-remove-management-solutions"></a>Como instalar e remover soluções de gestão?
No portal do OMS, instalar soluções de gestão a partir da Galeria de soluções e removido-los a partir **definições**. No portal do Azure, [instalar soluções de gestão](../monitoring/monitoring-solutions.md#install-a-management-solution) no Azure Marketplace. [Remover soluções](../monitoring/monitoring-solutions.md#remove-a-management-solution) na lista de soluções instaladas.

## <a name="how-do-i-create-and-manage-alerts"></a>Como criar e gerir alertas
Regras de alerta com base em consultas do Log Analytics são agora geridas na [unified a experiência de alertas](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). Ver [como expandir alertas do Log Analytics para alertas do Azure](../monitoring-and-diagnostics/monitoring-alerts-extend-tool.md) para obter detalhes sobre como configurar e utilizar alertas no portal do Azure.

## <a name="how-do-i-access-my-dashboards"></a>Como posso acessar meus dashboards?
[Dashboards](../log-analytics/log-analytics-dashboards.md) no Log Analytics foram preteridos.  Pode visualizar dados no Log Analytics utilizando [estruturador de vistas](../log-analytics/log-analytics-view-designer.md) que tem a funcionalidade adicional e consulta de pin e vistas de dashboards do Azure.

## <a name="how-do-i-check-my-usage"></a>Como posso ver meu uso?
Agora pode facilmente ver e gerir a sua utilização e o custo do Log Analytics, selecionando **utilização e custos estimados** na sua área de trabalho.

![Utilização e custos estimados](media/log-analytics-new-portal/usage.png)


## <a name="can-i-still-use-the-classic-portal"></a>Posso continuar a utilizar o portal clássico?
Por um período limitado, pode ainda aceder ao portal através deste URL, com seu próprio nome de área de trabalho: https://\<seu nome de área de trabalho\>. portal.mms.microsoft.com. Recomendamos utilizar o portal do Azure no entanto e envie os seus comentários em LAUpgradeFeedback@microsoft.com em quaisquer problemas de bloqueio.

## <a name="next-steps"></a>Passos Seguintes

- [Encontrar e instalar soluções de gestão](../monitoring/monitoring-solutions.md) no portal do Azure.
- Saiba mais sobre [pesquisa de registos no portal do Azure](log-analytics-log-search-portals.md).