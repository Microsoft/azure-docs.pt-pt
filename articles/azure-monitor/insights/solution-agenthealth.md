---
title: Integridade do Agente solução no Azure Monitor | Microsoft Docs
description: Este artigo destina-se a ajudá-lo a entender como usar essa solução para monitorar a integridade dos seus agentes relatando diretamente para Log Analytics ou System Center Operations Manager.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 03/19/2017
ms.openlocfilehash: 5a48bbff89f0d6a0be9adf2ad242dbca41eec6db
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555331"
---
#  <a name="agent-health-solution-in-azure-monitor"></a>Integridade do Agente solução no Azure Monitor
A solução Integridade do Agente no Azure ajuda você a entender, para todos os agentes que se reportam diretamente ao espaço de trabalho Log Analytics no Azure Monitor ou um grupo de gerenciamento System Center Operations Manager conectado a Azure Monitor, que não respondem e enviando dados operacionais.  Também pode controlar a quantidade de agentes que estão implementados, onde estão distribuídos geograficamente e fazer outras consultas, para estar a par da distribuição dos agentes implementados no Azure, noutros ambientes na cloud ou no local.    

## <a name="prerequisites"></a>Pré-requisitos
Antes de implantar essa solução, confirme se você tem suporte para os [agentes do Windows](../../log-analytics/log-analytics-windows-agent.md) que se reportam ao espaço de trabalho log Analytics ou relatórios para um grupo de [Gerenciamento de Operations Manager](../../azure-monitor/platform/om-agents.md) integrado ao seu espaço de trabalho.

## <a name="solution-components"></a>Componentes da solução
Esta solução consiste nos recursos seguintes que são adicionados à sua área de trabalho e a agentes ligados diretamente ou a grupos de gestão ligados do Operations Manager.

### <a name="management-packs"></a>Pacotes de gestão
Se o grupo de gerenciamento do System Center Operations Manager estiver conectado a um espaço de trabalho do Log Analytics, os pacotes de gerenciamento a seguir serão instalados no Operations Manager.  Estes pacotes de gestão também são instalados em computadores Windows ligados diretamente após adicionar esta solução. Estes pacotes de gestão não envolvem qualquer configuração ou gestão.

* Pacote de Informações de Canal Direto de HealthAssessment do Microsoft System Center Advisor (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Pacote de Informações de Canal de Servidor de HealthAssessment do Microsoft System Center Advisor (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

Para obter mais informações sobre como são atualizados os pacotes de gestão da solução, veja [Connect Operations Manager to Log Analytics (Ligar o Operations Manager ao Log Analytics)](../../azure-monitor/platform/om-agents.md).

## <a name="configuration"></a>Configuração
Adicione a solução Integridade do Agente ao seu espaço de trabalho do Log Analytics usando o processo descrito em [Adicionar soluções](solutions.md). Não há nenhuma configuração adicional.


## <a name="data-collection"></a>Recolha de dados
### <a name="supported-agents"></a>Agentes suportados
A tabela seguinte descreve as origens ligadas que são suportadas por esta solução.

| Origem Ligada | Suportadas | Descrição |
| --- | --- | --- |
| Agentes do Windows | Sim | Os eventos de heartbeat são recolhidos de agentes do Windows diretos.|
| Grupo de gestão do System Center Operations Manager | Sim | Os eventos de pulsação são coletados dos agentes subordinados ao grupo de gerenciamento a cada 60 segundos e, em seguida, encaminhados para o Azure Monitor. Uma conexão direta de agentes de Operations Manager para Azure Monitor não é necessária. Os dados do evento de pulsação são encaminhados do grupo de gerenciamento para o espaço de trabalho Log Analytics.|

## <a name="using-the-solution"></a>Utilizar a solução
Quando você adicionar a solução ao seu espaço de trabalho do Log Analytics, o bloco **integridade do agente** será adicionado ao seu painel. Esse mosaico mostra o número total de agentes e o número de agentes sem resposta nas últimas 24 horas.<br><br> ![Mosaico Solução Funcionamento de Agente no dashboard](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

Clique no mosaico **Funcionamento de Agente** para abrir o dashboard **Funcionamento de Agente**.  O dashboard inclui as colunas da tabela seguinte. Cada coluna mostra os dez principais eventos por ocorrência que correspondem aos critérios dessa coluna relativamente ao intervalo de tempo especificado. Pode executar uma pesquisa de registos que forneça a lista completa ao selecionar **Ver tudo**, no canto inferior direito de cada coluna, ou ao clicar no cabeçalho das colunas.

| Column | Descrição |
|--------|-------------|
| Contagem de agentes ao longo do tempo | Uma tendência da contagem de agentes ao longo de um período de sete dias, para agentes do Linux e do Windows.|
| Contagem de agentes sem resposta | Uma lista dos agentes que não enviaram um heartbeat nas últimas 24 horas.|
| Distribuição por Tipo de SO | Uma partição do número de agentes do Windows e do Linux que tem no seu ambiente.|
| Distribuição por Versão de Agente | Uma partição das diferentes versões de agentes instaladas no seu ambiente e uma contagem de cada uma das versões.|
| Distribuição por Categoria de Agente | Uma partição das diferentes categorias de agentes que estão a enviar eventos de heartbeat: agentes diretos, agentes do OpsMgr ou o Servidor de Gestão do OpsMgr.|
| Distribuição por Grupo de Gestão | Uma partição dos diferentes grupos de gerenciamento de Operations Manager em seu ambiente.|
| Localização Geográfica dos Agentes | Uma partição dos diferentes países/regiões em que você tem agentes e uma contagem total do número de agentes que foram instalados em cada país/região.|
| Contagem de Gateways Instalados | O número de servidores que têm o gateway de Log Analytics instalado e uma lista desses servidores.|

![Exemplo de dashboard da Solução Funcionamento de Agente](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Azure Monitor registros de log
A solução cria um tipo de registro no espaço de trabalho Log Analytics.  

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
| `Version` | Agente de Log Analytics ou versão do agente de Operations Manager.|
| `SCAgentChannel` | O valor é *Direto* e/ou *SCManagementServer*.|
| `IsGatewayInstalled` | Se Log Analytics Gateway estiver instalado, o valor será *true*, caso contrário, o valor será *false*.|
| `ComputerIP` | O endereço IP do computador.|
| `RemoteIPCountry` | A localização geográfica onde o computador está implementado.|
| `ManagementGroupName` | O nome do grupo de gestão do Operations Manager.|
| `SourceComputerId` | ID exclusivo do computador.|
| `RemoteIPLongitude` | Longitude da localização geográfica do computador.|
| `RemoteIPLatitude` | Latitude da localização geográfica do computador.|

Cada agente que se reporta a um servidor de gerenciamento de Operations Manager enviará duas pulsações, e o valor da propriedade SCAgentChannel incluirá **Direct** e **SCManagementServer** dependendo de quais fontes de dados e soluções de monitoramento você tem habilitado em sua assinatura. Se você se lembrar, os dados das soluções serão enviados diretamente de um servidor de gerenciamento de Operations Manager para Azure Monitor, ou devido ao volume de dados coletados no agente, serão enviados diretamente do agente para Azure Monitor. Para eventos de heartbeat que têm o valor **SCManagementServer**, o valor de ComputerIP é o endereço IP do servidor de gestão, pois os dados são efetivamente carregados por este.  Relativamente a heatbeats em que SCAgentChannel está definido como **Direto**, é o endereço IP público do agente.  

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
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Número de gateways de Log Analytics instalados |




## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [alertas no Azure monitor](../platform/alerts-overview.md) para obter detalhes sobre como gerar alertas de consultas de log. 
