---
title: Configurar e aceder registos - Servidor Flexível - Base de Dados de Azure para PostgreSQL
description: Como aceder a registos de bases de dados para Azure Database para PostgreSQL - Servidor Flexível
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 59a2ddcc68a7c5a3b6fa3a3b315f4294d1625204
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105607410"
---
# <a name="configure-and-access-logs-in-azure-database-for-postgresql---flexible-server"></a>Configurar e aceder registos na base de dados Azure para PostgreSQL - Servidor Flexível

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

Os registos PostgreSQL estão disponíveis em cada nó de um servidor flexível. Pode enviar registos para um servidor de armazenamento ou para um serviço de análise. Os registos podem ser utilizados para identificar, resolver problemas e reparar erros de configuração e desempenho subóptima.

## <a name="configure-diagnostic-settings"></a>Configurar configurações de diagnóstico

Pode ativar as definições de diagnóstico para o seu servidor Postgres utilizando o portal Azure, CLI, REST API e PowerShell. A categoria de registo a selecionar é **PostgreSQLLogs**.

Para ativar registos de recursos utilizando o portal Azure:

1. No portal, aceda a *Definições de Diagnóstico* no menu de navegação do seu servidor Postgres.
   
2. Selecione *Adicionar Definição de Diagnóstico*.
   :::image type="content" source="media/howto-logging/diagnostic-settings.png" alt-text="Adicione botão de definições de diagnóstico":::

3. Diga o nome desta definição. 

4. Selecione o seu ponto final preferido (conta de armazenamento, centro de eventos, análise de registo). 

5. Selecione o tipo de registo **PostgreSQLLogs**.
   :::image type="content" source="media/howto-logging/diagnostic-create-setting.png" alt-text="Escolha registos PostgreSQL":::

7. Guarde a sua configuração.

Para ativar os registos de recursos utilizando a PowerShell, CLI ou REST API, visite o artigo [de definições de diagnóstico.](../../azure-monitor/essentials/diagnostic-settings.md)

### <a name="access-resource-logs"></a>Aceder a registos de recursos

A forma como acede aos registos depende do ponto final que escolher. Para o Azure Storage, consulte o artigo da [conta de armazenamento de registos.](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) Para Os Centros de Eventos, consulte o [artigo de registos Azure.](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

Para registos do Monitor Azure, os registos são enviados para o espaço de trabalho selecionado. Os registos Postgres utilizam o modo de recolha **AzureDiagnostics,** para que possam ser consultados a partir da tabela AzureDiagnostics. Os campos na tabela são descritos abaixo. Saiba mais sobre consulta e alerta na visão geral dos Registos do [Monitor Azure.](../../azure-monitor/logs/log-query-overview.md)

Seguem-se as consultas que pode tentar começar. Pode configurar alertas com base em consultas.

Procure todos os registos postgres para um servidor particular no último dia

```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where Category == "PostgreSQLLogs"
| where TimeGenerated > ago(1d) 
```

Procure todas as tentativas de ligação não locais

```kusto
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```

A consulta acima mostrará resultados nas últimas 6 horas para qualquer servidor postgres que faça login neste espaço de trabalho.

## <a name="next-steps"></a>Passos seguintes

- [Começar com consultas de analítica de log](../../azure-monitor/logs/log-analytics-tutorial.md)
- Saiba mais sobre [os centros de eventos da Azure](../../event-hubs/event-hubs-about.md)