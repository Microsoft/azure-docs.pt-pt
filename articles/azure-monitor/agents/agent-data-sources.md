---
title: Log Analytics fontes de dados de agente no Azure Monitor
description: As fontes de dados definem os dados de registo que o Azure Monitor recolhe de agentes e outras fontes ligadas.  Este artigo descreve o conceito de como o Azure Monitor utiliza fontes de dados, explica os detalhes de como os configurar e fornece um resumo das diferentes fontes de dados disponíveis.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: 51cdee9c899feeb003a7d6301d2da0749fad65e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041944"
---
# <a name="log-analytics-agent-data-sources-in-azure-monitor"></a>Log Analytics fontes de dados de agente no Azure Monitor
Os dados que o Azure Monitor recolhe de máquinas virtuais com o agente [Log Analytics](./log-analytics-agent.md) são definidos pelas fontes de dados que configura no espaço de trabalho do [Log Analytics](../logs/data-platform-logs.md).   Cada fonte de dados cria registos de um tipo específico com cada tipo com o seu próprio conjunto de propriedades.

> [!IMPORTANT]
> Este artigo abrange fontes de dados para o [agente Log Analytics,](./log-analytics-agent.md) que é um dos agentes utilizados pelo Azure Monitor. Outros agentes recolhem dados diferentes e são configurados de forma diferente. Consulte [a visão geral dos agentes do Azure Monitor](agents-overview.md) para obter uma lista dos agentes disponíveis e os dados que podem recolher.

![Recolha de dados de registo](media/agent-data-sources/overview.png)

> [!IMPORTANT]
> As fontes de dados descritas neste artigo aplicam-se apenas às máquinas virtuais que executam o agente Log Analytics. 

## <a name="summary-of-data-sources"></a>Resumo das fontes de dados
A tabela que se segue lista as fontes de dados do agente que estão atualmente disponíveis com o agente Log Analytics.  Cada um tem uma ligação com um artigo separado que fornece detalhes para essa fonte de dados.   Também fornece informações sobre o seu método e frequência de recolha. 


| Origem de dados | Plataforma | Agente de análise de registo | Agente do Operations Manager | Storage do Azure | Diretor de Operações necessário? | Dados de agente do Gestor de Operações enviados através do grupo de gestão | Frequência da recolha |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Registos personalizados](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | na chegada |
| [Registos personalizados](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | na chegada |
| [Registos do IIS](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |depende da definição de rollover de ficheiros de log |
| [Contadores de desempenho](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |como programado, mínimo de 10 segundos |
| [Contadores de desempenho](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |como programado, mínimo de 10 segundos |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |do armazenamento Azure: 10 minutos; do agente: à chegada |
| [Registos do Evento Windows](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | na chegada |


## <a name="configuring-data-sources"></a>Configurar fontes de dados
Para configurar fontes de dados para agentes do Log Analytics, vá ao menu **de espaços de trabalho Do Log Analytics** no portal Azure e selecione um espaço de trabalho. Clique na **configuração dos agentes.** Selecione o separador para a fonte de dados que pretende configurar. Pode seguir os links da tabela acima para documentação para cada fonte de dados e detalhes sobre a sua configuração.

Qualquer configuração é entregue a todos os agentes ligados a esse espaço de trabalho.  Não é possível excluir agentes ligados desta configuração.

[![Configurar eventos windows](media/agent-data-sources/configure-events.png)](media/agent-data-sources/configure-events.png#lightbox)



## <a name="data-collection"></a>Recolha de dados
As configurações de origem de dados são entregues a agentes que estão diretamente ligados ao Azure Monitor dentro de poucos minutos.  Os dados especificados são recolhidos do agente e entregues diretamente ao Azure Monitor em intervalos específicos de cada fonte de dados.  Consulte a documentação de cada fonte de dados para estas especificidades.

Para os agentes do Gestor de Operações do System Center num grupo de gestão conectado, as configurações de fonte de dados são traduzidas em pacotes de gestão e entregues ao grupo de gestão a cada 5 minutos por padrão.  O agente descarrega o pacote de gestão como qualquer outro e recolhe os dados especificados. Dependendo da fonte de dados, os dados serão enviados para um servidor de gestão que reencaminha os dados para o Azure Monitor, ou o agente enviará os dados para o Azure Monitor sem passar pelo servidor de gestão. Consulte [os detalhes da recolha de dados para obter soluções de monitorização em Azure](../monitor-reference.md) para obter mais detalhes.  Pode ler sobre detalhes de ligar o Gestor de Operações e o Monitor Azure e modificar a frequência que a configuração é entregue na [Integração Configure com o Gestor de Operações do Centro de Sistema.](./om-agents.md)

Se o agente não conseguir ligar-se ao Azure Monitor ou ao Gestor de Operações, continuará a recolher dados que irá fornecer quando estabelecer uma ligação.  Os dados podem ser perdidos se a quantidade de dados atingir o tamanho máximo de cache para o cliente, ou se o agente não for capaz de estabelecer uma ligação dentro de 24 horas.

## <a name="log-records"></a>Registos de registos
Todos os dados de registo recolhidos pelo Azure Monitor são armazenados no espaço de trabalho como registos.  Os registos recolhidos por diferentes fontes de dados terão o seu próprio conjunto de propriedades e serão identificados pela sua propriedade **Tipo.**  Consulte a documentação de cada fonte de dados e solução para obter mais informações sobre cada tipo de registo.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais [sobre soluções de monitorização](../insights/solutions.md) que adicionam funcionalidade ao Azure Monitor e também recolha dados no espaço de trabalho.
* Saiba mais [sobre as consultas de registo](../logs/log-query-overview.md) para analisar os dados recolhidos a partir de fontes de dados e soluções de monitorização.  
* Configure [alertas](../alerts/alerts-overview.md) para notificá-lo proativamente de dados críticos recolhidos de fontes de dados e soluções de monitorização.