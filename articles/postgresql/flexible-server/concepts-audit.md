---
title: Registo de auditoria - Base de Dados Azure para PostgreSQL - Servidor flexível
description: Conceitos para a auditoria pgAudit iniciar sessão na Base de Dados Azure para postgreSQL - servidor flexível.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 2fccf5431666990919faf7e6378b46c41d682437
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90937090"
---
# <a name="audit-logging-in-azure-database-for-postgresql---flexible-server"></a>Registo de auditoria na Base de Dados Azure para PostgreSQL - Servidor flexível

O registo de auditoria das atividades de base de dados na Base de Dados Azure para PostgreSQL - Servidor flexível está disponível através da extensão de Auditoria PósgreSQL: [pgAudit](https://www.pgaudit.org/). o pgAudit fornece registos de auditoria de sessão detalhada e/ou objeto.

> [!IMPORTANT]
> Azure Database for PostgreSQL - Servidor flexível está em pré-visualização

Se quiser registos ao nível de recursos do Azure para operações como a escala de cálculo e armazenamento, consulte o Registo de [Atividades Azure](../../azure-monitor/platform/platform-logs-overview.md).

## <a name="usage-considerations"></a>Considerações de utilização
Por predefinição, as instruções de registo pgAudit são emitidas juntamente com as instruções de registo regulares através da função de registo padrão do Postgres. Na Base de Dados Azure para o servidor PostgreSQL - Flexível, pode configurar todos os registos a serem enviados para a loja de registos do Monitor Azure para posterior análise no Log Analytics. Se ativar a registo de recursos do Azure Monitor, os seus registos serão automaticamente enviados (em formato JSON) para registos de armazenamento de Azure, Centros de Eventos e/ou Monitor Azure, dependendo da sua escolha.

Para aprender a configurar o registo de registos de armazenamento, evento ou monitor Azure, visite a secção de registos de recursos do artigo de [registos](concepts-logging.md)do servidor .

## <a name="enabling-pgaudit"></a>Habilitação pgAudit

Para ativar o pgAudit, precisa de se ligar ao seu servidor utilizando um cliente (como o psql) e ativar a extensão pgAudit executando o seguinte comando:
```SQL
CREATE EXTENSION pgaudit;
```

## <a name="pgaudit-settings"></a>definições pgAudit

o pgAudit permite-lhe configurar sessão ou registo de auditoria de objetos. [O registo de auditoria](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) de sessão emite registos detalhados de declarações executadas. [O registo de auditoria de objetos](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) é uma auditoria a relações específicas. Pode optar por configurar um ou ambos os tipos de registo. 

> [!NOTE]
> as definições pgAudit são especificadas globalmente e não podem ser especificadas a nível de base de dados ou de função.

Uma vez [ativado o pgAudit,](#enabling-pgaudit)pode configurar os seus parâmetros para iniciar a sessão. A [documentação pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) fornece a definição de cada parâmetro. Teste primeiro os parâmetros e confirme que está a obter o comportamento esperado.

> [!NOTE]
> A definição `pgaudit.log_client` para ON irá redirecionar os registos para um processo de cliente (como o psql) em vez de ser escrito para arquivar. Esta definição deve, normalmente, ser deixada desativada. <br> <br>
> `pgaudit.log_level` só está ativado quando `pgaudit.log_client` está ligado.

> [!NOTE]
> Na Base de Dados Azure para o servidor PostgreSQL - Flexível, `pgaudit.log` não pode ser definido usando um `-` atalho de sinal (menos) conforme descrito na documentação pgAudit. Todas as aulas de declaração requeridas (LER, ESCREVER, etc.) devem ser especificadas individualmente.

## <a name="audit-log-format"></a>Formato do registo de auditoria
Cada entrada de auditoria é indicada `AUDIT:` perto do início da linha de registo. O formato do resto da entrada é detalhado na [documentação do PGAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

## <a name="getting-started"></a>Introdução
Para começar rapidamente, ajuste `pgaudit.log` para , e abra os `WRITE` registos do seu servidor para rever a saída. 

## <a name="viewing-audit-logs"></a>Visualização de registos de auditoria
A forma como acede aos registos depende do ponto final que escolher. Para o Azure Storage, consulte o artigo da [conta de armazenamento de registos.](../../azure-monitor/platform/resource-logs-collect-storage.md) Para Os Centros de Eventos, consulte o [artigo de registos Azure.](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)

Para registos do Monitor Azure, os registos são enviados para o espaço de trabalho selecionado. Os registos Postgres utilizam o modo de recolha **AzureDiagnostics,** para que possam ser consultados a partir da tabela AzureDiagnostics. Os campos na tabela são descritos abaixo. Saiba mais sobre consulta e alerta na visão geral dos Registos do [Monitor Azure.](../../azure-monitor/log-query/log-query-overview.md)

Podes usar esta consulta para começar. Pode configurar alertas com base em consultas.

Procure por todas as entradas pgAudit em registos postgres para um servidor particular no último dia
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Passos seguintes
- [Saiba como iniciar sessão na Base de Dados Azure para PostgreSQL - Servidor flexível](concepts-logging.md)
- [Saiba como configurar iniciar sessão em Azure Database para PostgreSQL - Servidor flexível e como aceder a registos](howto-configure-and-access-logs.md)