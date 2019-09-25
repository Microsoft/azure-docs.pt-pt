---
title: Alertas e notificações de instalação usando o portal do Azure | Microsoft Docs
description: Use o portal do Azure para criar alertas de banco de dados SQL, que podem disparar notificações ou automação quando as condições especificadas são atendidas.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 11/02/2018
ms.openlocfilehash: 3103b556098d0b9ccbfca7348ab70c183a69753d
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262233"
---
# <a name="create-alerts-for-azure-sql-database-and-data-warehouse-using-azure-portal"></a>Criar alertas para o banco de dados SQL do Azure e data warehouse usando portal do Azure

## <a name="overview"></a>Descrição geral
Este artigo mostra como configurar o banco de dados SQL do Azure e data warehouse alertas usando o portal do Azure. Os alertas podem enviar um email ou chamar um gancho da Web quando alguma métrica (por exemplo, tamanho do banco de dados ou uso da CPU) atingir o limite. Este artigo também fornece as práticas recomendadas para definir períodos de alerta.    

> [!IMPORTANT]
> Este recurso ainda não está disponível no Instância Gerenciada. Como alternativa, você pode usar o SQL Agent para enviar alertas de email para algumas métricas baseadas em [exibições de gerenciamento dinâmico](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Você pode receber um alerta com base em métricas de monitoramento para, ou eventos, seus serviços do Azure.

* **Valores de métrica** -o alerta é disparado quando o valor de uma métrica especificada cruza um limite que você atribui em qualquer direção. Ou seja, ele é disparado quando a condição é atendida pela primeira vez e depois quando essa condição não é mais atendida.    
* **Eventos do log de atividades** – um alerta pode disparar em *cada* evento ou somente quando ocorre um determinado número de eventos.

Você pode configurar um alerta para fazer o seguinte quando ele disparar:

* enviar notificações por email para o administrador de serviços e os administradores
* Envie um email para emails adicionais que você especificar.
* chamar um webhook

Você pode configurar e obter informações sobre regras de alerta usando o

* [Azure portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [CLI (interface de linha de comando)](../azure-monitor/platform/alerts-classic-portal.md)
* [API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Criar uma regra de alerta em uma métrica com o portal do Azure
1. No [portal](https://portal.azure.com/), localize o recurso que você está interessado em monitorar e selecione-o.
2. Selecione **alertas (clássicos)** na seção monitoramento. O texto e o ícone podem variar um pouco para diferentes recursos.  
   
     ![Monitorização](media/sql-database-insights-alerts-portal/AlertsClassicButton.JPG)
  
   - **SOMENTE SQL DW**: Clique no grafo de **uso do DWU** . Selecione **exibir alertas clássicos**

3. Selecione o botão **adicionar alerta de métrica (clássico)** e preencha os campos.
   
    ![Adicionar alerta](media/sql-database-insights-alerts-portal/AddDBAlertPageClassic.JPG)
4. **Nomeie** sua regra de alerta e escolha uma **Descrição**, que também é mostrada em emails de notificação.
5. Selecione a **métrica** que você deseja monitorar e escolha uma **condição** e um valor de **limite** para a métrica. Escolha também o **período** de tempo que a regra de métrica deve ser satisfeita antes que o alerta seja disparado. Por exemplo, se você usar o período "PT5M" e seu alerta procurar pela CPU acima de 80%, o alerta será disparado quando a CPU **média** estiver acima de 80% por 5 minutos. Depois que o primeiro gatilho ocorrer, ele será disparado novamente quando a CPU média estiver abaixo de 80% em 5 minutos. A medição de CPU ocorre a cada 1 minuto. Consulte a tabela abaixo para obter as janelas de tempo com suporte e o tipo de agregação que cada alerta usa; nem todos os alertas usam o valor médio.   
6. Verifique os **proprietários dos emails...** se você quiser que os administradores e os coadministradores sejam enviados por email quando o alerta for disparado.
7. Se você quiser que emails adicionais recebam uma notificação quando o alerta for disparado, adicione-os no campo **email (s) de administrador adicional** . Separe vários emails com ponto e vírgula – *envie\@um email para contoso.\@com; EMAIL2 contoso.com*
8. Coloque um URI válido no campo **webhook** se desejar que ele seja chamado quando o alerta for disparado.
9. Selecione **OK** quando terminar para criar o alerta.   

Em alguns minutos, o alerta está ativo e é disparado conforme descrito anteriormente.

## <a name="managing-your-alerts"></a>Gerenciando seus alertas
Depois de criar um alerta, você pode selecioná-lo e:

* Exiba um grafo que mostra o limite de métrica e os valores reais do dia anterior.
* Edite ou exclua-o.
* **Desabilite** ou **habilite** -o se quiser interromper temporariamente ou retomar as notificações para esse alerta.


## <a name="sql-database-alert-values"></a>Valores de alerta do banco de dados SQL

| Tipo de Recurso | Nome da Métrica | Nome Amigável | Tipo de Agregação | Janela de tempo de alerta mínimo|
| --- | --- | --- | --- | --- |
| Base de dados SQL | cpu_percent | Percentagem de CPU | Average | 5 minutos |
| Base de dados SQL | physical_data_read_percent | Percentagem de E/S de dados | Average | 5 minutos |
| Base de dados SQL | log_write_percent | Percentagem de E/S de registo | Average | 5 minutos |
| Base de dados SQL | dtu_consumption_percent | Percentagem de DTU | Average | 5 minutos |
| Base de dados SQL | armazenamento | Tamanho total do banco de dados | Máximo | 30 minutos |
| Base de dados SQL | connection_successful | Conexões com êxito | Total | 10 minutos |
| Base de dados SQL | connection_failed | Ligações Falhadas | Total | 10 minutos |
| Base de dados SQL | blocked_by_firewall | Bloqueado pelo firewall | Total | 10 minutos |
| Base de dados SQL | Bloqueado | Impasses | Total | 10 minutos |
| Base de dados SQL | storage_percent | Percentagem de tamanho da Base de Dados | Máximo | 30 minutos |
| Base de dados SQL | xtp_storage_percent | Porcentagem de armazenamento OLTP na memória (visualização) | Average | 5 minutos |
| Base de dados SQL | workers_percent | Percentagem de funcionários | Average | 5 minutos |
| Base de dados SQL | sessions_percent | Porcentagem de sessões | Average | 5 minutos |
| Base de dados SQL | dtu_limit | Limite de DTU | Average | 5 minutos |
| Base de dados SQL | dtu_used | DTU usado | Average | 5 minutos |
||||||
| Conjunto elástico | cpu_percent | Percentagem de CPU | Average | 10 minutos |
| Conjunto elástico | physical_data_read_percent | Percentagem de E/S de dados | Average | 10 minutos |
| Conjunto elástico | log_write_percent | Percentagem de E/S de registo | Average | 10 minutos |
| Conjunto elástico | dtu_consumption_percent | Percentagem de DTU | Average | 10 minutos |
| Conjunto elástico | storage_percent | Porcentagem de armazenamento | Average | 10 minutos |
| Conjunto elástico | workers_percent | Percentagem de funcionários | Average | 10 minutos |
| Conjunto elástico | eDTU_limit | limite de eDTU | Average | 10 minutos |
| Conjunto elástico | storage_limit | Limite de armazenamento | Average | 10 minutos |
| Conjunto elástico | eDTU_used | eDTU usado | Average | 10 minutos |
| Conjunto elástico | storage_used | Armazenamento utilizado | Average | 10 minutos |
||||||               
| Armazém de dados SQL | cpu_percent | Percentagem de CPU | Average | 10 minutos |
| Armazém de dados SQL | physical_data_read_percent | Percentagem de E/S de dados | Average | 10 minutos |
| Armazém de dados SQL | connection_successful | Conexões com êxito | Total | 10 minutos |
| Armazém de dados SQL | connection_failed | Ligações Falhadas | Total | 10 minutos |
| Armazém de dados SQL | blocked_by_firewall | Bloqueado pelo firewall | Total | 10 minutos |
| Armazém de dados SQL | service_level_objective | Camada de serviço do banco de dados | Total | 10 minutos |
| Armazém de dados SQL | dwu_limit | limite de DWU | Máximo | 10 minutos |
| Armazém de dados SQL | dwu_consumption_percent | Porcentagem de DWU | Average | 10 minutos |
| Armazém de dados SQL | dwu_used | DWU usado | Average | 10 minutos |
||||||


## <a name="next-steps"></a>Passos seguintes
* [Obtenha uma visão geral do monitoramento do Azure](../monitoring-and-diagnostics/monitoring-overview.md) , incluindo os tipos de informações que você pode coletar e monitorar.
* Saiba mais sobre como [Configurar WebHooks em alertas](../azure-monitor/platform/alerts-webhooks.md).
* Obtenha uma [visão geral dos logs de diagnóstico](../azure-monitor/platform/resource-logs-overview.md) e colete métricas detalhadas de alta frequência em seu serviço.
* Obtenha uma [visão geral da coleção de métricas](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para certificar-se de que seu serviço está disponível e responsivo.
