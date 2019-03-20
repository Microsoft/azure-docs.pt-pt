---
title: Configurar origens de dados do agente no Azure Monitor | Documentos da Microsoft
description: Origens de dados definem os dados de registo que recolhe de Monitor do Azure por agentes e outro ligado origens.  Este artigo descreve o conceito de como o Azure Monitor utiliza origens de dados, explica os detalhes de como configurá-las e fornece um resumo das origens de dados diferentes disponíveis.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: e4550dde533044260c120b17c4f72758f90e8bff
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58102118"
---
# <a name="agent-data-sources-in-azure-monitor"></a>Origens de dados do agente no Azure Monitor
Os dados que recolhe a partir de agentes do Azure Monitor são definidos pelas origens de dados que configurou.  Os dados de agentes são armazenados como [registos de dados](data-collection.md) com um conjunto de registos.  Cada origem de dados cria registos de um determinado tipo com cada tipo de ter seu próprio conjunto de propriedades.

![Recolha de dados de registo](media/agent-data-sources/overview.png)

## <a name="summary-of-data-sources"></a>Resumo das origens de dados
A tabela seguinte lista as origens de dados do agente que estão atualmente disponíveis no Azure Monitor.  Cada um tem uma ligação para um artigo separado, fornecendo detalhes para essa origem de dados.   Também fornece informações no seu método e a frequência da coleção. 


| Origem de dados | Plataforma | Agente de monitorização da Microsoft | Agente do Operations Manager | Storage do Azure | Gestor de operações necessárias? | Dados de agente do Operations Manager enviados por grupo de gestão | Frequência da recolha |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Registos personalizados](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | na chegada |
| [Registos personalizados](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | na chegada |
| [Registos do IIS](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |depende da definição de Rollover de ficheiro de registo |
| [Contadores de desempenho](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |mínimo de 10 segundos, conforme agendado |
| [Contadores de desempenho](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |mínimo de 10 segundos, conforme agendado |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |do armazenamento do Azure: 10 minutos; de agente: na chegada |
| [Registos de eventos do Windows](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | na chegada |


## <a name="configuring-data-sources"></a>Configurar origens de dados
Configurar origens de dados do **dados** menu **definições avançadas** para a área de trabalho.  Qualquer configuração é entregue para todas as origens ligadas na sua área de trabalho.  Atualmente não é possível excluir quaisquer agentes desta configuração.

![Configurar eventos do Windows](media/agent-data-sources/configure-events.png)

1. No portal do Azure, selecione **áreas de trabalho do Log Analytics** > sua área de trabalho > **definições avançadas**.
2. Selecione **dados**.
3. Clique na origem de dados que pretende configurar.
4. Siga a ligação para a documentação para cada origem de dados na tabela acima para obter detalhes sobre a configuração.


## <a name="data-collection"></a>Recolha de dados
Configurações de origem de dados são entregues aos agentes que estão ligadas diretamente para o Azure Monitor dentro de alguns minutos.  Os dados especificados são recolhidos a partir do agente e fornecidos diretamente para o Azure Monitor em intervalos específicos para cada origem de dados.  Consulte a documentação para cada origem de dados para estas especificações.

Para agentes do System Center Operations Manager num grupo de gestão ligado, configurações de origem de dados são convertidas em pacotes de gestão e entregue ao grupo de gestão a cada 5 minutos por predefinição.  O agente baixa o pacote de gestão como qualquer outro e recolhe os dados especificados. Dependendo da origem de dados, os dados serão que seja enviado para um servidor de gestão que encaminha os dados para o Azure Monitor ou o agente irá enviar os dados para o Azure Monitor sem passar pelo servidor de gestão. Ver [detalhes de recolha de dados para a monitorização de soluções no Azure](../insights/solutions-inventory.md) para obter detalhes.  Pode ler sobre os detalhes da conexão do Operations Manager e o Azure Monitor e modificar a frequência que a configuração seja entregue no [configurar a integração com o System Center Operations Manager](om-agents.md).

Se o agente não consegue ligar ao Azure Monitor ou o Operations Manager, irá continuar a recolher dados que irá proporcionar quando estabelece uma ligação.  Dados podem ser perdidos se a quantidade de dados atinge o tamanho máximo da cache do cliente, ou se o agente não conseguir estabelecer uma ligação dentro de 24 horas.

## <a name="log-records"></a>Registros de log
Todos os dados de registo recolhidos pelo Azure Monitor é armazenado na área de trabalho como registros.  Registos recolhidos por diferentes origens de dados terá seu próprio conjunto de propriedades e ser identificados pelo respetivo **tipo** propriedade.  Consulte a documentação para cada origem de dados e a solução para obter detalhes sobre cada tipo de registo.

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [soluções de monitorização](../insights/solutions.md) que adicionar a funcionalidade para o Azure Monitor e também, recolher dados para a área de trabalho.
* Saiba mais sobre [registar as consultas](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de origens de dados e soluções de monitorização.  
* Configurar [alertas](alerts-overview.md) para ser notificado proativamente dos dados vitais recolhidos a partir de origens de dados e soluções de monitorização.