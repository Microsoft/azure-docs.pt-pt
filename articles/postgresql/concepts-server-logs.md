---
title: Registos - Base de Dados Azure para PostgreSQL - Servidor Único
description: Descreve a configuração, armazenamento e análise de registo slogantes na Base de Dados Azure para PostgreSQL - Servidor Único
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 2636e9a225002148e4cd79bb2176e0883aed623a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280498"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>Registos na Base de Dados Azure para PostgreSQL - Servidor Único
A Base de Dados Azure para PostgreSQL permite-lhe configurar e aceder aos registos padrão do Postgres. Os registos podem ser utilizados para identificar, resolução de problemas e erros de configuração de reparação e desempenho sub-ideal. O registo de informações que pode configurar e aceder inclui erros, informações de consulta, registos de autovácuo, ligações e pontos de verificação. (O acesso aos registos de transações não está disponível).

A exploração madeireira de auditoria é disponibilizada através de uma extensão de Postgres, pgaudit. Para saber mais, visite o artigo [conceitos de auditoria.](concepts-audit.md)


## <a name="configure-logging"></a>Configurar a exploração madeireira 
Pode configurar o registo padrão do Postgres no seu servidor utilizando os parâmetros do servidor de registo. Em cada base de dados Azure `log_checkpoints` para `log_connections` servidor PostgreSQL, e estão ligados por padrão. Existem parâmetros adicionais que pode ajustar de acordo com as suas necessidades de exploração madeireira: 

![Base de Dados Azure para PostgreSQL - Parâmetros de exploração](./media/concepts-server-logs/log-parameters.png)

Para saber mais sobre os parâmetros de registo postgres, visite as secções [Quando Registar](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) e O [Que Registar](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) as secções da documentação postgres. A maioria, mas não todos, os parâmetros de registo postgres estão disponíveis para configurar na Base de Dados Azure para PostgreSQL.

Para saber configurar parâmetros na Base de Dados Azure para PostgreSQL, consulte a [documentação](howto-configure-server-parameters-using-portal.md) do portal ou a [documentação CLI](howto-configure-server-parameters-using-cli.md). 

> [!NOTE]
> Configurar um elevado volume de registos, por exemplo, registo de demonstração de declaração, pode adicionar sobrecargas significativas de desempenho. 

## <a name="access-log-files"></a>Aceder a ficheiros .log
O formato de registo predefinido na Base de Dados Azure para PostgreSQL é .log. Uma linha de amostra deste tronco parece:

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

A Base de Dados Azure para PostgreSQL fornece um local de armazenamento de curto prazo para os ficheiros .log. Um novo ficheiro começa a cada 1 hora ou 100 MB, o que vier primeiro. Os registos são anexados ao ficheiro atual, uma vez que são emitidos a partir de Postgres.  

Pode definir o período de retenção para `log_retention_period` este armazenamento de registo de curto prazo utilizando o parâmetro. O valor predefinido é de três dias; o valor máximo é de sete dias. O local de armazenamento a curto prazo pode conter até 1 GB de ficheiros de registo. Após 1 GB, os ficheiros mais antigos, independentemente do período de retenção, serão eliminados para dar espaço a novos registos. 

Para a retenção a mais longo prazo de registos e análise de registo, pode descarregar os ficheiros .log e movê-los para um serviço de terceiros. Pode descarregar os ficheiros através do [portal Azure,](howto-configure-server-logs-in-portal.md) [Azure CLI](howto-configure-server-logs-using-cli.md). Em alternativa, pode configurar as definições de diagnóstico do Monitor Azure que emitem automaticamente os seus registos (em formato JSON) para locais de longo prazo. Saiba mais sobre esta opção na secção abaixo. 

Pode parar de gerar ficheiros .log, definindo o parâmetro `logging_collector` para OFF. Recomenda-se desligar a geração de ficheiros .log se estiver a utilizar as definições de diagnóstico do Monitor Azure. Esta configuração reduzirá o impacto de desempenho de uma exploração extraloga adicional.

## <a name="diagnostic-logs"></a>Registos de diagnósticos
A Base de Dados Azure para PostgreSQL está integrada com as definições de diagnóstico do Monitor Azure. As definições de diagnóstico permitem-lhe enviar os seus registos Postgres em formato JSON para registos de monitorização azure para análise e alerta, Centros de Eventos para streaming e Armazenamento Azure para arquivamento. 

> [!IMPORTANT]
> Esta função de diagnóstico para registos de servidores só está disponível nos níveis de preços otimizados para fins [gerais](concepts-pricing-tiers.md)e memória .


### <a name="configure-diagnostic-settings"></a>Configurar as definições de diagnóstico
Pode ativar as definições de diagnóstico do seu servidor Postgres utilizando o portal Azure, CLI, REST API e Powershell. A categoria de registo a selecionar é **PostgreSQLLogs**. (Existem outros registos que pode configurar se estiver a utilizar a [Consulta Store](concepts-query-store.md).)

Para ativar registos de diagnóstico utilizando o portal Azure:

   1. No portal, vá a *Definições* de Diagnóstico no menu de navegação do seu servidor Postgres.
   2. *Selecione Adicionar definição de diagnóstico*.
   3. Diga este cenário. 
   4. Selecione o seu ponto final preferido (conta de armazenamento, centro de eventos, análise de registo). 
   5. Selecione o tipo de log **PostgreSQLLogs**.
   7. Guarde a sua configuração.

Para ativar os registos de diagnóstico utilizando a Powershell, CLI ou REST API, visite o artigo de definições de [diagnóstico.](../azure-monitor/platform/diagnostic-settings.md)

### <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

A forma como acede aos registos depende do ponto final que escolher. Para o Armazenamento Azure, consulte o artigo da conta de armazenamento de [registos.](../azure-monitor/platform/resource-logs-collect-storage.md) Para Os Hubs de Eventos, consulte o artigo de [logs do Stream Azure.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

Para registos do Monitor Azure, os registos são enviados para o espaço de trabalho selecionado. Os registos Postgres utilizam o modo de recolha **AzureDiagnostics,** para que possam ser consultados a partir da tabela AzureDiagnostics. Os campos na mesa são descritos abaixo. Saiba mais sobre consulta e alerta na visão geral da consulta do [Monitor Azure.](../azure-monitor/log-query/log-query-overview.md)

Seguem-se consultas que pode tentar começar. Pode configurar alertas com base em consultas.

Procure todos os registos do Postgres para um determinado servidor no último dia
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
```

Pesquisar todas as tentativas de ligação não-local
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
A consulta acima mostrará resultados nas últimas 6 horas para qualquer registo de servidor do Postgres neste espaço de trabalho.

### <a name="log-format"></a>Formato de log

A tabela seguinte descreve os campos para o tipo **PostgreSQLLogs.** Dependendo do ponto final de saída que escolher, os campos incluídos e a ordem em que aparecem podem variar. 

|**Campo** | **Descrição** |
|---|---|
| TenantId | Sua identificação do inquilino |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Carimbo de tempo quando o registo foi gravado na UTC |
| Tipo | Tipo de tronco. Sempre`AzureDiagnostics` |
| SubscriptionId | GUID para a subscrição a que o servidor pertence |
| ResourceGroup | Nome do grupo de recursos a que o servidor pertence |
| ResourceProvider | Nome do fornecedor de recursos. Sempre`MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | Recurso URI |
| Recurso | Nome do servidor |
| Categoria | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Nível de exploração madeireira, exemplo: LOG, ERROR, NOTICE |
| Mensagem | Mensagem de registo primário | 
| Domain | Versão do servidor, exemplo: postgres-10 |
| Detalhe | Mensagem de registo secundária (se aplicável) |
| ColumnName | Nome da coluna (se aplicável) |
| SchemaName | Nome do esquema (se aplicável) |
| DatatypeName | Nome do tipo de dados (se aplicável) |
| Nome lógico do servidor | Nome do servidor | 
| _ResourceId | Recurso URI |
| Prefixo | Prefixo da linha de log |


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre o acesso a registos do [portal Azure](howto-configure-server-logs-in-portal.md) ou [do Azure CLI](howto-configure-server-logs-using-cli.md).
- Saiba mais sobre os preços do [Monitor Azure.](https://azure.microsoft.com/pricing/details/monitor/)
- Saiba mais sobre [registos de auditoria](concepts-audit.md)
