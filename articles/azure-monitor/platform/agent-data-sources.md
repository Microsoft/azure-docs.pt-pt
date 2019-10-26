---
title: Configurar fontes de dados do agente no Azure Monitor | Microsoft Docs
description: As fontes de dados definem os dados de log que Azure Monitor coleta de agentes e outras fontes conectadas.  Este artigo descreve o conceito de como Azure Monitor usa fontes de dados, explica os detalhes de como configurá-las e fornece um resumo das diferentes fontes de dados disponíveis.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: f7960adca1c3665dbf8737cb10a898c58b1542ec
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932823"
---
# <a name="agent-data-sources-in-azure-monitor"></a>Fontes de dados do agente no Azure Monitor
Os dados que Azure Monitor coleta de agentes são definidos pelas fontes de dados que você configura.  Os dados dos agentes são armazenados como [dados de log](data-platform-logs.md) com um conjunto de registros.  Cada fonte de dados cria registros de um tipo específico com cada tipo com seu próprio conjunto de propriedades.

![Coleta de dados de log](media/agent-data-sources/overview.png)

## <a name="summary-of-data-sources"></a>Resumo de fontes de dados
A tabela a seguir lista as fontes de dados do Agent que estão disponíveis no momento no Azure Monitor.  Cada um tem um link para um artigo separado, fornecendo detalhes para essa fonte de dados.   Ele também fornece informações sobre seus métodos e a frequência de coleta. 


| Fonte de dados | Plataforma | Agente do log Analytics | Agente de Operations Manager | Storage do Azure | Operations Manager necessário? | Operations Manager dados do agente enviados por meio do grupo de gerenciamento | Frequência da recolha |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Registos personalizados](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | na chegada |
| [Registos personalizados](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | na chegada |
| [Registos do IIS](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |depende da configuração de substituição do arquivo de log |
| [Contadores de desempenho](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |conforme agendado, mínimo de 10 segundos |
| [Contadores de desempenho](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |conforme agendado, mínimo de 10 segundos |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |do armazenamento do Azure: 10 minutos; do agente: na chegada |
| [Logs de eventos do Windows](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | na chegada |


## <a name="configuring-data-sources"></a>Configurando fontes de dados
Configure as fontes de dados no menu **dados** em **Configurações avançadas** para o espaço de trabalho.  Qualquer configuração é entregue a todas as fontes conectadas no seu espaço de trabalho.  Atualmente, não é possível excluir nenhum agente dessa configuração.

![Configurar eventos do Windows](media/agent-data-sources/configure-events.png)

1. No portal do Azure, selecione **log Analytics espaços de trabalho** > seu espaço de trabalho > **Configurações avançadas**.
2. Selecione **dados**.
3. Clique na fonte de dados que você deseja configurar.
4. Siga o link para a documentação de cada fonte de dados na tabela acima para obter detalhes sobre sua configuração.


## <a name="data-collection"></a>Recolha de dados
As configurações de fonte de dados são entregues aos agentes que estão conectados diretamente a Azure Monitor em alguns minutos.  Os dados especificados são coletados do agente e entregues diretamente a Azure Monitor em intervalos específicos para cada fonte de dados.  Consulte a documentação de cada fonte de dados para obter essas especificações.

Para agentes de System Center Operations Manager em um grupo de gerenciamento conectado, as configurações de fonte de dados são convertidas em pacotes de gerenciamento e entregues ao grupo de gerenciamento a cada 5 minutos por padrão.  O agente baixa o pacote de gerenciamento como qualquer outro e coleta os dados especificados. Dependendo da fonte de dados, os dados serão enviados para um servidor de gerenciamento que encaminha os dados para a Azure Monitor, ou o agente enviará os dados para Azure Monitor sem passar pelo servidor de gerenciamento. Consulte [detalhes da coleta de dados para soluções de monitoramento no Azure](../insights/solutions-inventory.md) para obter detalhes.  Você pode ler sobre os detalhes da conexão Operations Manager e Azure Monitor e modificar a frequência com que a configuração é fornecida em [Configurar a integração com o System Center Operations Manager](om-agents.md).

Se o agente não puder se conectar ao Azure Monitor ou Operations Manager, ele continuará a coletar dados que ele fornecerá quando estabelecer uma conexão.  Os dados podem ser perdidos se a quantidade de dados atingir o tamanho máximo do cache para o cliente ou se o agente não puder estabelecer uma conexão dentro de 24 horas.

## <a name="log-records"></a>Registros de log
Todos os dados de log coletados pelo Azure Monitor são armazenados no espaço de trabalho como registros.  Os registros coletados por fontes de dados diferentes terão seu próprio conjunto de propriedades e serão identificados pela propriedade **Type** .  Consulte a documentação para cada fonte de dados e solução para obter detalhes sobre cada tipo de registro.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [soluções de monitoramento](../insights/solutions.md) que adicionam funcionalidade ao Azure monitor e também coletam dados no espaço de trabalho.
* Saiba mais sobre [consultas de log](../log-query/log-query-overview.md) para analisar os dados coletados de fontes de dados e soluções de monitoramento.  
* Configure [alertas](alerts-overview.md) para notificá-lo proativamente sobre dados críticos coletados de fontes de dados e soluções de monitoramento.
