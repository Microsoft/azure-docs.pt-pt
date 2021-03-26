---
title: Registos - Base de Dados Azure para PostgreSQL - Servidor Único
description: Descreve a configuração, armazenamento e análise de registo na Base de Dados Azure para PostgreSQL - Servidor Único
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: 31b3c0a1894280e90f696f6c107b67a3af7b1880
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605353"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>Registos na Base de Dados Azure para PostgreSQL - Servidor Único

A Base de Dados Azure para PostgreSQL permite-lhe configurar e aceder aos registos padrão do Postgres. Os registos podem ser utilizados para identificar, resolver problemas e reparar erros de configuração e desempenho subóptima. Registar informações que pode configurar e aceder inclui erros, informações de consulta, registos de autovacuum, ligações e pontos de verificação. (O acesso aos registos de transações não está disponível).

O registo de auditoria é disponibilizado através de uma extensão postgres, pgaudit. Para saber mais, visite o artigo [conceitos de auditoria.](concepts-audit.md)


## <a name="configure-logging"></a>Confiria a exploração madeireira 
Pode configurar a sessão padrão de Postgres no seu servidor utilizando os parâmetros do servidor de registo. Em cada Base de Dados Azure para servidor PostgreSQL, `log_checkpoints` e `log_connections` estão ligados por padrão. Existem parâmetros adicionais que pode ajustar de acordo com as suas necessidades de registo registador: 

:::image type="content" source="./media/concepts-server-logs/log-parameters.png" alt-text="Base de Dados Azure para Pós-SQL - Parâmetros de registo":::

Para saber mais sobre os parâmetros de registo de postgres, visite as secções [De Início](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) e O [Que Registar](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) da documentação postgres. A maioria, mas não todos, os parâmetros de registo de postgres estão disponíveis para configurar na Base de Dados Azure para PostgreSQL.

Para saber como configurar parâmetros na Base de Dados Azure para PostgreSQL, consulte a [documentação](howto-configure-server-parameters-using-portal.md) do portal ou a documentação do [CLI](howto-configure-server-parameters-using-cli.md). 

> [!NOTE]
> Configurar um grande volume de registos, por exemplo, registo de declarações, pode adicionar uma sobrecarga de desempenho significativa. 

## <a name="access-log-files"></a>Aceder a ficheiros de .log
O formato de registo predefinido na Base de Dados Azure para PostgreSQL é .log. Uma linha de amostra deste tronco parece:

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

A Azure Database for PostgreSQL fornece um local de armazenamento de curto prazo para os ficheiros .log. Um novo ficheiro começa a cada 1 hora ou 100 MB, o que vier primeiro. Os registos são anexados ao ficheiro atual, uma vez que são emitidos a partir de Postgres.  

Pode definir o período de retenção para este armazenamento de registo de curto prazo utilizando o `log_retention_period` parâmetro. O valor predefinido é de três dias; o valor máximo é de sete dias. O local de armazenamento de curto prazo pode conter até 1 GB de ficheiros de registo. Após 1 GB, os ficheiros mais antigos, independentemente do período de retenção, serão eliminados para dar lugar a novos registos. 

Para uma retenção a mais longo prazo de registos e análise de registos, pode descarregar os ficheiros .log e movê-los para um serviço de terceiros. Pode descarregar os ficheiros utilizando o [portal Azure](howto-configure-server-logs-in-portal.md), [Azure CLI](howto-configure-server-logs-using-cli.md). Em alternativa, pode configurar as definições de diagnóstico do Azure Monitor que emite automaticamente os seus registos (em formato JSON) para locais de longo prazo. Saiba mais sobre esta opção na secção abaixo. 

Pode parar de gerar ficheiros .log definindo o parâmetro `logging_collector` para OFF. É aconselhável desligar .log geração de ficheiros se estiver a utilizar as definições de diagnóstico do Azure Monitor. Esta configuração reduzirá o impacto de desempenho de registos adicionais.

## <a name="resource-logs"></a>Registos do recurso

A Base de Dados Azure para PostgreSQL está integrada com as definições de diagnóstico do Monitor Azure. As definições de diagnóstico permitem-lhe enviar os seus registos postgres no formato JSON para registos do Monitor Azure para análise e alerta, Centros de Eventos para streaming e Armazenamento Azure para arquivamento. 

> [!IMPORTANT]
> Esta funcionalidade de diagnóstico para registos de servidores só está disponível nos [níveis de preços](concepts-pricing-tiers.md)otimizados para fins gerais e memória.


### <a name="configure-diagnostic-settings"></a>Configurar configurações de diagnóstico

Pode ativar as definições de diagnóstico para o seu servidor Postgres utilizando o portal Azure, CLI, REST API e Powershell. A categoria de registo a selecionar é **PostgreSQLLogs**. (Existem outros registos que pode configurar se estiver a utilizar [a Loja de Consultas](concepts-query-store.md).)

Para ativar registos de recursos utilizando o portal Azure:

   1. No portal, aceda a *Definições de Diagnóstico* no menu de navegação do seu servidor Postgres.
   2. Selecione *Adicionar Definição de Diagnóstico*.
   3. Diga o nome desta definição. 
   4. Selecione o seu ponto final preferido (conta de armazenamento, centro de eventos, análise de registo). 
   5. Selecione o tipo de registo **PostgreSQLLogs**.
   7. Guarde a sua configuração.

Para ativar registos de recursos utilizando a API Powershell, CLI ou REST, visite o artigo [de definições de diagnóstico.](../azure-monitor/essentials/diagnostic-settings.md)

### <a name="access-resource-logs"></a>Aceder a registos de recursos

A forma como acede aos registos depende do ponto final que escolher. Para o Azure Storage, consulte o artigo da [conta de armazenamento de registos.](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) Para Os Centros de Eventos, consulte o [artigo de registos Azure.](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

Para registos do Monitor Azure, os registos são enviados para o espaço de trabalho selecionado. Os registos Postgres utilizam o modo de recolha **AzureDiagnostics,** para que possam ser consultados a partir da tabela AzureDiagnostics. Os campos na tabela são descritos abaixo. Saiba mais sobre consulta e alerta na visão geral dos Registos do [Monitor Azure.](../azure-monitor/logs/log-query-overview.md)

Seguem-se as consultas que pode tentar começar. Pode configurar alertas com base em consultas.

Procure todos os registos postgres para um servidor particular no último dia
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where Category == "PostgreSQLLogs"
| where TimeGenerated > ago(1d) 
```

Procure todas as tentativas de ligação não locais
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
A consulta acima mostrará resultados nas últimas 6 horas para qualquer servidor postgres que faça login neste espaço de trabalho.

### <a name="log-format"></a>Formato de registo

A tabela seguinte descreve os campos para o tipo **PostgreSQLLogs.** Dependendo do ponto final de saída que escolher, os campos incluídos e a ordem em que aparecem podem variar. 

|**Campo** | **Descrição** |
|---|---|
| TenantId | Sua iD do seu inquilino |
| SourceSystem | `Azure` |
| TempoGerado [UTC] | Carimbo de tempo quando o registo foi gravado na UTC |
| Tipo | Tipo de tronco. Sempre `AzureDiagnostics` |
| SubscriptionId | GUID para a subscrição a que o servidor pertence |
| ResourceGroup | Nome do grupo de recursos a que o servidor pertence |
| ResourceProvider | Nome do fornecedor de recursos. Sempre `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | URI de recursos |
| Recurso | Nome do servidor |
| Categoria | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| erroLevel | Nível de registo, exemplo: LOG, ERROR, NOTICE |
| Mensagem | Mensagem de registo primário | 
| Domínio | Versão do servidor, exemplo: postgres-10 |
| Detalhes | Mensagem de registo secundário (se aplicável) |
| ColumnName | Nome da coluna (se aplicável) |
| SchemaName | Nome do esquema (se aplicável) |
| Nome de datatype | Nome do tipo de dados (se aplicável) |
| Nome LógicoServerName | Nome do servidor | 
| _ResourceId | URI de recursos |
| Prefixo | Prefixo da linha de registo |


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre o acesso a registos a partir do [portal Azure](howto-configure-server-logs-in-portal.md) ou [do Azure CLI](howto-configure-server-logs-using-cli.md).
- Saiba mais sobre [os preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
- Saiba mais sobre [registos de auditoria](concepts-audit.md)