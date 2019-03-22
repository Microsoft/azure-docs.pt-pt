---
title: Configurar alertas e notificações através do portal do Azure | Documentos da Microsoft
description: Utilize o portal do Azure para criar alertas de base de dados SQL, que podem acionar notificações ou automatização quando forem cumpridas condições que especificar.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 11/02/2018
ms.openlocfilehash: 93337e39a117c1f8d38f24dc416ff8ae95513a34
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57855593"
---
# <a name="create-alerts-for-azure-sql-database-and-data-warehouse-using-azure-portal"></a>Criar alertas para o Azure SQL Database e o armazém de dados com o portal do Azure

## <a name="overview"></a>Descrição geral
Este artigo mostra-lhe como configurar alertas de base de dados do Azure SQL e o armazém de dados com o portal do Azure. Alertas de podem enviar um email ou chamar um web hook quando alguns métrica (por exemplo, o tamanho de base de dados ou a utilização da CPU) atinge o limiar. Este artigo também disponibiliza melhores práticas para a configuração de períodos de alerta.    

> [!IMPORTANT]
> Esta funcionalidade ainda não está disponível na instância gerida. Como alternativa, pode usar o SQL Agent para enviar alertas por e-mail para algumas métricas com base na [vistas de gestão dinâmica](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Pode receber um alerta com base em métricas de monitorização para ou eventos nos seus serviços do Azure.

* **Valores de métrica** -o alerta for acionado quando o valor de uma métrica especificado ultrapassar um limiar atribuir em ambas as direções. Ou seja, aciona ambas quando a condição for satisfeita pela primeira vez e, em seguida, em seguida quando condição que já não é a ser cumprida.    
* **Eventos de registo de atividade** -pode acionar um alerta *cada* eventos ou, se ocorre um determinado número de eventos.

Pode configurar um alerta para fazer o seguinte quando é acionado:

* enviar notificações por e-mail para o administrador de serviços e coadministradores
* Envie e-mail para e-mails adicionais que especificar.
* Chamar um webhook

Pode configurar e obtenha informações sobre como utilizar as regras de alerta

* [Portal do Azure](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [Interface de linha de comandos (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [API de REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Criar uma regra de alerta numa métrica no portal do Azure
1. Na [portal](https://portal.azure.com/), localize o recurso que tem interesse em monitorizar e selecioná-lo.
2. Selecione **alertas (clássico)** na secção monitorização. O texto e o ícone podem variar um pouco para recursos diferentes.  
   
     ![Monitorização](media/sql-database-insights-alerts-portal/AlertsClassicButton.JPG)
  
   - **APENAS O SQL DW**: Clique nas **utilização da DWU** gráfico. Selecione **ver alertas clássicos**

3. Selecione o **Adicionar alerta de métrica (clássico)** botão e preencha os campos.
   
    ![Adicionar Alerta](media/sql-database-insights-alerts-portal/AddDBAlertPageClassic.JPG)
4. **Nome** seu alerta da regra e escolha uma **Descrição**, que mostra também nos e-mails de notificação.
5. Selecione o **métrica** que pretende monitorizar, em seguida, escolha um **condição** e **limiar** valor para a métrica. Escolha também os **período** de tempo que a regra de métrica deve ser satisfeita antes dos acionadores de alerta. Por exemplo, se usar o período de "PT5M" e o alerta procura por CPU superior a 80%, o alerta for acionado quando o **média** CPU já esteve acima de 80% durante 5 minutos. Depois de ocorre o primeiro acionador, novamente aciona quando a CPU média é inferior a 80% mais de 5 minutos. A medição de CPU ocorre a cada 1 minuto. Consulte a tabela abaixo para janelas de tempo suportado e a agregação escreva cada alertar a utilizações e não todos os alertas, use o valor médio.   
6. Verificar **os proprietários de E-Mail...**  se pretender que os administradores e coadministradores, para ser enviado por e-mail quando o alerta é acionado.
7. Se pretender que os e-mails adicionais para receber uma notificação quando o alerta é acionado, adicione-os na **adicionais do administrador email(s)** campo. Separe várias mensagens de correio eletrónico com ponto e vírgula - *e-mail\@contoso.com;email2\@contoso.com*
8. Colocar num URI válido no **Webhook** campo se desejar que ele chamado quando o alerta é acionado.
9. Selecione **OK** quando terminar de criar o alerta.   

Em poucos minutos, o alerta está ativo e aciona conforme descrito anteriormente.

## <a name="managing-your-alerts"></a>Gerir os alertas
Assim que tiver criado um alerta, pode selecioná-lo e:

* Ver um gráfico que mostra o limiar de métrica e os valores reais do dia anterior.
* Editar ou eliminá-lo.
* **Desativar** ou **ativar** -los, se deseja temporariamente parar ou retomar a receção de notificações para esse alerta.


## <a name="sql-database-alert-values"></a>Valores de alerta de base de dados SQL

| Tipo de Recurso | Nome da Métrica | Nome Amigável | Tipo de Agregação | Janela de tempo mínimo de alerta|
| --- | --- | --- | --- | --- |
| Base de dados SQL | cpu_percent | Percentagem de CPU | Média | 5 minutos |
| Base de dados SQL | physical_data_read_percent | Percentagem de ES de Dados | Média | 5 minutos |
| Base de dados SQL | log_write_percent | Percentagem de E/S de Registo | Média | 5 minutos |
| Base de dados SQL | dtu_consumption_percent | Percentagem de DTU | Média | 5 minutos |
| Base de dados SQL | armazenamento | Tamanho total da base de dados | Máximo | 30 minutos |
| Base de dados SQL | connection_successful | Ligações com êxito | Total | 10 minutos |
| Base de dados SQL | connection_failed | Ligações com Falhas | Total | 10 minutos |
| Base de dados SQL | blocked_by_firewall | Bloqueado pela Firewall | Total | 10 minutos |
| Base de dados SQL | Deadlock | Impasses | Total | 10 minutos |
| Base de dados SQL | storage_percent | Percentagem de tamanho da Base de Dados | Máximo | 30 minutos |
| Base de dados SQL | xtp_storage_percent | Percent(Preview) de armazenamento OLTP dentro da memória | Média | 5 minutos |
| Base de dados SQL | workers_percent | Percentagem de funções de trabalho | Média | 5 minutos |
| Base de dados SQL | sessions_percent | Percentagem de sessões | Média | 5 minutos |
| Base de dados SQL | dtu_limit | Limite DTU | Média | 5 minutos |
| Base de dados SQL | dtu_used | DTUS utilizadas | Média | 5 minutos |
||||||
| Conjunto elástico | cpu_percent | Percentagem de CPU | Média | 10 minutos |
| Conjunto elástico | physical_data_read_percent | Percentagem de ES de Dados | Média | 10 minutos |
| Conjunto elástico | log_write_percent | Percentagem de E/S de Registo | Média | 10 minutos |
| Conjunto elástico | dtu_consumption_percent | Percentagem de DTU | Média | 10 minutos |
| Conjunto elástico | storage_percent | Percentagem de armazenamento | Média | 10 minutos |
| Conjunto elástico | workers_percent | Percentagem de funções de trabalho | Média | 10 minutos |
| Conjunto elástico | eDTU_limit | limite de eDTU | Média | 10 minutos |
| Conjunto elástico | storage_limit | Limite de armazenamento | Média | 10 minutos |
| Conjunto elástico | eDTU_used | eDTU utilizado | Média | 10 minutos |
| Conjunto elástico | storage_used | Armazenamento utilizado | Média | 10 minutos |
||||||               
| SQL data warehouse | cpu_percent | Percentagem de CPU | Média | 10 minutos |
| SQL data warehouse | physical_data_read_percent | Percentagem de ES de Dados | Média | 10 minutos |
| SQL data warehouse | connection_successful | Ligações com êxito | Total | 10 minutos |
| SQL data warehouse | connection_failed | Ligações com Falhas | Total | 10 minutos |
| SQL data warehouse | blocked_by_firewall | Bloqueado pela Firewall | Total | 10 minutos |
| SQL data warehouse | service_level_objective | Escalão de serviço da base de dados | Total | 10 minutos |
| SQL data warehouse | dwu_limit | limite de dwu | Máximo | 10 minutos |
| SQL data warehouse | dwu_consumption_percent | Percentagem DWU | Média | 10 minutos |
| SQL data warehouse | dwu_used | DWU utilizado | Média | 10 minutos |
||||||


## <a name="next-steps"></a>Passos Seguintes
* [Obtenha uma visão geral da monitorização do Azure](../monitoring-and-diagnostics/monitoring-overview.md) incluindo os tipos de informações pode recolher e monitorizar.
* Saiba mais sobre [configurar webhooks nos alertas](../azure-monitor/platform/alerts-webhooks.md).
* Obter um [descrição geral dos registos de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md) e recolher métricas de alta frequência detalhadas no seu serviço.
* Obter um [descrição geral da coleção de métricas](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para se certificar de que o seu serviço está disponível e reativo.
