---
title: Registos - Base de Dados Azure para PostgreSQL - Servidor Flexível
description: Descreve a configuração, armazenamento e análise de registo na Base de Dados Azure para PostgreSQL - Servidor Flexível
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: e09c01fcfb9c4725ac169151e85c8b030d8bb18c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606390"
---
# <a name="logs-in-azure-database-for-postgresql---flexible-server"></a>Registos na Base de Dados Azure para PostgreSQL - Servidor Flexível

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

A Base de Dados Azure para PostgreSQL permite-lhe configurar e aceder aos registos padrão dos Postgres. Os registos podem ser utilizados para identificar, resolver problemas e reparar erros de configuração e desempenho subóptima. Registar informações que pode configurar e aceder inclui erros, informações de consulta, registos de autovacuum, ligações e pontos de verificação. (O acesso aos registos de transações não está disponível).

O registo de auditoria é disponibilizado através de uma extensão de Postgres, `pgaudit` . Para saber mais, visite o artigo [conceitos de auditoria.](concepts-audit.md)

## <a name="configure-logging"></a>Confiria a exploração madeireira

Pode configurar a sessão padrão de Postgres no seu servidor utilizando os parâmetros do servidor de registo. Para saber mais sobre os parâmetros de registo de postgres, visite as secções [De Início](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) e O [Que Registar](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) da documentação postgres. A maioria, mas não todos, os parâmetros de registo de postgres estão disponíveis para configurar na Base de Dados Azure para PostgreSQL.

Para saber como configurar parâmetros na Base de Dados Azure para PostgreSQL, consulte a [documentação](howto-configure-server-parameters-using-portal.md) do portal ou a documentação do [CLI](howto-configure-server-parameters-using-cli.md).

> [!NOTE]
> Configurar um grande volume de registos, por exemplo, registo de declarações, pode adicionar uma sobrecarga de desempenho significativa. 

## <a name="accessing-logs"></a>Aceder a registos

A Base de Dados Azure para PostgreSQL está integrada com as definições de diagnóstico do Monitor Azure. As definições de diagnóstico permitem-lhe enviar os seus registos postgres no formato JSON para registos do Monitor Azure para análise e alerta, Centros de Eventos para streaming e Armazenamento Azure para arquivamento. 

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

- Saiba mais sobre como configurar e aceder a [registos.](howto-configure-and-access-logs.md)
- Saiba mais sobre [os preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
- Saiba mais sobre [registos de auditoria](concepts-audit.md)
