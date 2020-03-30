---
title: Configure fontes de dados de agente no Monitor Azure [ Microsoft Docs
description: Fontes de dados definem os dados de registo que o Azure Monitor recolhe de agentes e outras fontes ligadas.  Este artigo descreve o conceito de como o Azure Monitor utiliza fontes de dados, explica os detalhes de como configurá-los e fornece um resumo das diferentes fontes de dados disponíveis.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: aec3fe2386ce916c556f6da295a8554fff140259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249103"
---
# <a name="agent-data-sources-in-azure-monitor"></a>Fontes de dados do agente no Monitor Azure
Os dados que o Azure Monitor recolhe dos agentes são definidos pelas fontes de dados que configura.  Os dados dos agentes são armazenados como dados de [registo](data-platform-logs.md) com um conjunto de registos.  Cada fonte de dados cria registos de um tipo específico com cada tipo com o seu próprio conjunto de propriedades.

![Recolha de dados de registo](media/agent-data-sources/overview.png)

## <a name="summary-of-data-sources"></a>Resumo das fontes de dados
A tabela seguinte lista as fontes de dados do agente que estão atualmente disponíveis no Monitor Azure.  Cada um tem um link para um artigo separado que fornece detalhes para essa fonte de dados.   Também fornece informações sobre o seu método e frequência de recolha. 


| Origem de dados | Plataforma | Agente de análise de log | Agente do Operations Manager | Storage do Azure | Gestor de Operações necessário? | Dados do agente de operações enviados através de grupo de gestão | Frequência da recolha |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Registos personalizados](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | à chegada |
| [Registos personalizados](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | à chegada |
| [Registos do IIS](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |depende da definição de rollover de ficheiros de log |
| [Contadores de desempenho](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |como previsto, no mínimo 10 segundos |
| [Contadores de desempenho](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |como previsto, no mínimo 10 segundos |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |do armazenamento Azure: 10 minutos; do agente: à chegada |
| [Registos do Evento Windows](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | à chegada |


## <a name="configuring-data-sources"></a>Configurar fontes de dados
Configura as fontes de dados do menu **Dados** em **Definições Avançadas** para o espaço de trabalho.  Qualquer configuração é entregue a todas as fontes conectadas no seu espaço de trabalho.  Não pode atualmente excluir nenhum agente desta configuração.

![Configurar eventos windows](media/agent-data-sources/configure-events.png)

1. No portal Azure, selecione espaços de **trabalho Log Analytics** > o seu espaço de trabalho > **Definições Avançadas**.
2. Selecione **Dados**.
3. Clique na fonte de dados que pretende configurar.
4. Siga o link para a documentação de cada fonte de dados na tabela acima para obter detalhes sobre a sua configuração.


## <a name="data-collection"></a>Recolha de dados
As configurações de fonte de dados são entregues a agentes que estão diretamente ligados ao Monitor Azure dentro de poucos minutos.  Os dados especificados são recolhidos do agente e entregues diretamente ao Monitor Azure em intervalos específicos de cada fonte de dados.  Consulte a documentação para cada fonte de dados para estas especificidades.

Para os agentes do System Center Operations Manager num grupo de gestão conectado, as configurações de fonte de dados são traduzidas em pacotes de gestão e entregues ao grupo de gestão a cada 5 minutos por padrão.  O agente descarrega o pacote de gestão como qualquer outro e recolhe os dados especificados. Dependendo da fonte de dados, os dados serão enviados para um servidor de gestão que reencaminha os dados para o Monitor Do Azure, ou o agente enviará os dados para o Monitor Azure sem passar pelo servidor de gestão. Consulte detalhes da recolha de [dados para monitorizar soluções em Azure](../insights/solutions-inventory.md) para mais detalhes.  Pode ler detalhes sobre a ligação do Gestor de Operações e do Monitor Azure e modificar a frequência que a configuração é entregue na [Configure Integration com o System Center Operations Manager](om-agents.md).

Se o agente não conseguir ligar-se ao Azure Monitor ou ao Diretor de Operações, continuará a recolher dados que irá fornecer quando estabelecer uma ligação.  Os dados podem ser perdidos se a quantidade de dados atingir o tamanho máximo da cache para o cliente, ou se o agente não conseguir estabelecer uma ligação dentro de 24 horas.

## <a name="log-records"></a>Registos de registos
Todos os dados de registo recolhidos pelo Azure Monitor são armazenados no espaço de trabalho como registos.  Os registos recolhidos por diferentes fontes de dados terão o seu próprio conjunto de propriedades e serão identificados pela sua propriedade **Tipo.**  Consulte a documentação de cada fonte de dados e solução para obter detalhes sobre cada tipo de registo.

## <a name="next-steps"></a>Passos seguintes
* Conheça [soluções](../insights/solutions.md) de monitorização que acrescentem funcionalidade ao Monitor Azure e também recolha dados no espaço de trabalho.
* Saiba mais sobre consultas de [registo](../log-query/log-query-overview.md) para analisar os dados recolhidos a partir de fontes de dados e soluções de monitorização.  
* Configure [alertas](alerts-overview.md) para notificá-lo proativamente de dados críticos recolhidos a partir de fontes de dados e soluções de monitorização.
