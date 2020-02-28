---
title: Solução de Saúde agente no Monitor Azure / Microsoft Docs
description: Este artigo destina-se a ajudá-lo a entender como usar esta solução para monitorizar a saúde dos seus agentes reportando diretamente ao Log Analytics ou System Center Operations Manager.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/06/2020
ms.openlocfilehash: 7093e20473b799a3f05ddf30803721636732241e
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663265"
---
#  <a name="agent-health-solution-in-azure-monitor"></a>Solução de Saúde agente no Monitor Azure
A solução Agent Health em Azure ajuda-o a compreender, para todos os agentes que reportam diretamente ao espaço de trabalho log Analytics no Azure Monitor ou a um grupo de gestão de operações do System Center ligado ao Monitor Azure, que não respondem e apresentação de dados operacionais.  Também pode controlar a quantidade de agentes que estão implementados, onde estão distribuídos geograficamente e fazer outras consultas, para estar a par da distribuição dos agentes implementados no Azure, noutros ambientes na cloud ou no local.    

## <a name="prerequisites"></a>Pré-requisitos
Antes de implementar esta solução, confirme que atualmente tem [suportado agentes do Windows](../../log-analytics/log-analytics-windows-agent.md) reportando ao espaço de trabalho log Analytics ou reportando a um grupo de [gestão de Gestor](../../azure-monitor/platform/om-agents.md) de Operações integrado com o seu espaço de trabalho.

## <a name="solution-components"></a>Componentes da solução
Esta solução consiste nos recursos seguintes que são adicionados à sua área de trabalho e a agentes ligados diretamente ou a grupos de gestão ligados do Operations Manager.

### <a name="management-packs"></a>Pacotes de gestão
Se o seu grupo de gestão de Gestão de Operações do System Center estiver ligado a um espaço de trabalho de Log Analytics, os seguintes pacotes de gestão são instalados no Gestor de Operações.  Estes pacotes de gestão também são instalados em computadores Windows ligados diretamente após adicionar esta solução. Estes pacotes de gestão não envolvem qualquer configuração ou gestão.

* Pacote de Informações de Canal Direto de HealthAssessment do Microsoft System Center Advisor (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Pacote de Informações de Canal de Servidor de HealthAssessment do Microsoft System Center Advisor (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

Para obter mais informações sobre como são atualizados os pacotes de gestão da solução, veja [Connect Operations Manager to Log Analytics (Ligar o Operations Manager ao Log Analytics)](../../azure-monitor/platform/om-agents.md).

## <a name="configuration"></a>Configuração
Adicione a solução De Saúde do Agente ao seu espaço de trabalho Log Analytics utilizando o processo descrito em [soluções Add](solutions.md). Não há nenhuma configuração adicional.


## <a name="data-collection"></a>Recolha de dados
### <a name="supported-agents"></a>Agentes suportados
A tabela seguinte descreve as origens ligadas que são suportadas por esta solução.

| Origem Ligada | Suportado | Descrição |
| --- | --- | --- |
| Agentes do Windows | Sim | Os eventos de heartbeat são recolhidos de agentes do Windows diretos.|
| Grupo de gestão do System Center Operations Manager | Sim | Os eventos heartbeat são recolhidos de agentes que reportam ao grupo de gestão a cada 60 segundos e depois encaminhados para o Azure Monitor. Não é necessária uma ligação direta dos agentes do Diretor de Operações ao Monitor Azure. Os dados do evento heartbeat são encaminhados do grupo de gestão para o espaço de trabalho log Analytics.|

## <a name="using-the-solution"></a>Utilizar a solução
Quando adicionar a solução ao seu espaço de trabalho Log Analytics, o azulejo **Agent Health** será adicionado ao seu painel de instrumentos. Esse mosaico mostra o número total de agentes e o número de agentes sem resposta nas últimas 24 horas.<br><br> ![Mosaico Solução Funcionamento de Agente no dashboard](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

Clique no mosaico **Funcionamento de Agente** para abrir o dashboard **Funcionamento de Agente**.  O dashboard inclui as colunas da tabela seguinte. Cada coluna mostra os dez principais eventos por ocorrência que correspondem aos critérios dessa coluna relativamente ao intervalo de tempo especificado. Pode executar uma pesquisa de registos que forneça a lista completa ao selecionar **Ver tudo**, no canto inferior direito de cada coluna, ou ao clicar no cabeçalho das colunas.

| Coluna | Descrição |
|--------|-------------|
| Contagem de agentes ao longo do tempo | Uma tendência da contagem de agentes ao longo de um período de sete dias, para agentes do Linux e do Windows.|
| Contagem de agentes sem resposta | Uma lista dos agentes que não enviaram um heartbeat nas últimas 24 horas.|
| Distribuição por Tipo de SO | Uma partição do número de agentes do Windows e do Linux que tem no seu ambiente.|
| Distribuição por Versão de Agente | Uma partição das diferentes versões de agentes instaladas no seu ambiente e uma contagem de cada uma das versões.|
| Distribuição por Categoria de Agente | Uma partição das diferentes categorias de agentes que estão a enviar eventos de heartbeat: agentes diretos, agentes do OpsMgr ou o Servidor de Gestão do OpsMgr.|
| Distribuição por Grupo de Gestão | Uma partição dos diferentes grupos de Gestão de Gestão de Operações no seu ambiente.|
| Localização Geográfica dos Agentes | Uma divisão dos diferentes países/regiões onde você tem agentes e uma contagem total do número de agentes que foram instalados em cada país/região.|
| Contagem de Gateways Instalados | O número de servidores que têm o gateway Log Analytics instalado, e uma lista destes servidores.|

![Exemplo de dashboard da Solução Funcionamento de Agente](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Registos de registos do Monitor Azure
A solução cria um tipo de registo no espaço de trabalho log Analytics.  

### <a name="heartbeat-records"></a>Registos de heartbeats
É criado um registo com o tipo de **Heartbeat**.  Estes registos têm as propriedades da tabela seguinte.  

| Propriedade | Descrição |
| --- | --- |
| `Type` | *Heartbeat*|
| `Category` | O valor é *Agente Direto*, *Agente do SCOM* ou *Servidor de Gestão do SCOM*.|
| `Computer` | O nome do computador.|
| `OSType` | Sistema operativo Windows ou Linux.|
| `OSMajorVersion` | Versão principal do sistema operativo.|
| `OSMinorVersion` | Versão inferior do sistema operativo.|
| `Version` | Log Analytics Agent ou versão do Agente de Operações.|
| `SCAgentChannel` | O valor é *Direto* e/ou *SCManagementServer*.|
| `IsGatewayInstalled` | Se o gateway Log Analytics estiver instalado, o valor é *verdadeiro,* caso contrário o valor é *falso*.|
| `ComputerIP` | O endereço IP público do computador. Em VMs Azure, isto mostrará o IP público se um estiver disponível. Para os VMs que utilizam IPs privados, este irá exibir o endereço Azure SNAT (não o endereço IP privado). |
| `RemoteIPCountry` | A localização geográfica onde o computador está implementado.|
| `ManagementGroupName` | O nome do grupo de gestão do Operations Manager.|
| `SourceComputerId` | ID exclusivo do computador.|
| `RemoteIPLongitude` | Longitude da localização geográfica do computador.|
| `RemoteIPLatitude` | Latitude da localização geográfica do computador.|

Cada agente que reporte a um servidor de gestão do Gestor de Operações enviará dois batimentos cardíacos, e o valor da propriedade do SCAgentChannel incluirá tanto o **Direct** como **o SCManagementServer** dependendo das fontes de dados e das soluções de monitorização que tenha ativado na sua subscrição. Se se lembrar, os dados de soluções são enviados diretamente de um servidor de gestão do Gestor de Operações para o Monitor De Operações, ou devido ao volume de dados recolhidos no agente, são enviados diretamente do agente para o Monitor Azure. Para eventos de heartbeat que têm o valor **SCManagementServer**, o valor de ComputerIP é o endereço IP do servidor de gestão, pois os dados são efetivamente carregados por este.  Relativamente a heatbeats em que SCAgentChannel está definido como **Direto**, é o endereço IP público do agente.  

## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo
A tabela seguinte disponibiliza pesquisas de registos de exemplo para registos que esta solução recolhe.

| Consulta | Descrição |
|:---|:---|
| Heartbeat &#124; distinct Computer |Número total de agentes |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |Contagem de agentes sem resposta nas últimas 24 horas |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |Contagem de agentes sem resposta nos últimos 15 minutos |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Computadores online (nas últimas 24 horas) |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Total de Agentes Offline nos Últimos 30 minutos (nas últimas 24 horas) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Ver uma tendência do número de agentes ao longo do tempo por OSType|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Distribuição por Tipo de SO |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Distribuição por Versão de Agente |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Distribuição por Categoria de Agente |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Distribuição por Grupo de Gestão |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |Localização Geográfica dos Agentes |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Número de gateways de log analytics instalados |




## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [alertas no Monitor Azure](../platform/alerts-overview.md) para obter detalhes sobre a geração de alertas a partir de consultas de registo. 
