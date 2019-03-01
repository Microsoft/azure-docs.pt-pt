---
title: Registos do servidor na base de dados do Azure para PostgreSQL
description: Este artigo descreve como base de dados do Azure para PostgreSQL gera registos de consulta e o erro e, como retenção de registo está configurado.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: 99deef907818ffdb1ce858c8e988e26cbd53a1a1
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195103"
---
# <a name="server-logs-in-azure-database-for-postgresql"></a>Registos do servidor na base de dados do Azure para PostgreSQL 
Base de dados do Azure para PostgreSQL gera erro de consulta e registos. Registos de consulta e o erro podem ser utilizados para identificar, resolução de problemas e reparar erros de configuração e de desempenho inferior ao ideal. (O acesso aos logs de transação não está incluído.) 

## <a name="configure-logging"></a>Configurar o registo 
Pode configurar o registo no seu servidor com os parâmetros de servidor de Registro em log. Em cada novo servidor **log_checkpoints** e **log_connections** são ativados por padrão. Existem parâmetros adicionais, pode ajustar de acordo com as suas necessidades de registo: 

![Base de dados do Azure para PostgreSQL - parâmetros de registo](./media/concepts-server-logs/log-parameters.png)

Para obter mais informações sobre estes parâmetros, consulte do PostgreSQL [relatório de erros e o registo](https://www.postgresql.org/docs/current/static/runtime-config-logging.html) documentação. Para saber como configurar a base de dados do Azure para PostgreSQL parâmetros, veja a [documentação do portal](howto-configure-server-parameters-using-portal.md) ou o [documentação da CLI](howto-configure-server-parameters-using-cli.md).

## <a name="access-server-logs-through-portal-or-cli"></a>Registos do servidor de acesso através do portal ou a CLI
Se ativou os registos, pode acessá-los da base de dados do Azure para PostgreSQL através de armazenamento de registo do [portal do Azure](howto-configure-server-logs-in-portal.md), [CLI do Azure](howto-configure-server-logs-using-cli.md)e APIs REST do Azure. Rodar os ficheiros de registo para a cada 1 hora ou 100MB de tamanho, o que ocorrer primeiro. Pode definir o período de retenção para este armazenamento de registo utilizando o **log\_retenção\_período** parâmetro associado ao servidor. O valor predefinido é 3 dias; o valor máximo é de 7 dias. O servidor tem de ter suficiente alocado de armazenamento para armazenar os ficheiros de registo. (Este parâmetro de retenção não regem os registos de diagnóstico do Azure.)


## <a name="diagnostic-logs"></a>Registos de diagnósticos
Base de dados do Azure para PostgreSQL está integrado com os registos de diagnóstico do Azure Monitor. Assim que tiver ativado os registos no servidor PostgreSQL, pode optar por fazê-los emitidos para [registos do Azure Monitor](../azure-monitor/log-query/log-query-overview.md), os Hubs de eventos ou o armazenamento do Azure. Para saber mais sobre como ativar os registos de diagnóstico, consulte a seção de procedimentos do [documentação de registos de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md). 

> [!IMPORTANT]
> Esta funcionalidade de diagnóstico para os registos do servidor só está disponível nos fins gerais e com otimização de memória [escalões de preço](concepts-pricing-tiers.md).

A tabela seguinte descreve as novidades em cada registo. Consoante o ponto final de saída que escolher, os campos incluídos e a ordem em que aparecem pode variar. 

|**Campo** | **Descrição** |
|---|---|
| TenantId | O ID de inquilino |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Carimbo de hora quando o registo foi registado em UTC |
| Type | Tipo do registo. Sempre `AzureDiagnostics` |
| SubscriptionId | GUID da subscrição que o servidor pertence a |
| ResourceGroup | Nome do grupo de recursos do servidor pertence a |
| ResourceProvider | Nome do fornecedor de recursos. Sempre `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | URI do recurso |
| Recurso | Nome do servidor |
| Categoria | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Exemplo de nível de registo: LOG, ERROR, NOTICE |
| Mensagem | Mensagem do registo principal | 
| Domain | Versão do servidor, por exemplo: postgres 10 |
| Detalhe | Mensagem do registo secundário (se aplicável) |
| ColumnName | Nome da coluna (se aplicável) |
| SchemaName | Nome do esquema (se aplicável) |
| DatatypeName | Nome do tipo de dados (se aplicável) |
| LogicalServerName | Nome do servidor | 
| _ResourceId | URI do recurso |

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre como aceder aos registos a partir da [portal do Azure](howto-configure-server-logs-in-portal.md) ou [CLI do Azure](howto-configure-server-logs-using-cli.md).
- Saiba mais sobre [preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
