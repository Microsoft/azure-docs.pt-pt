---
title: Registo de auditoria - Base de Dados de Azure para PostgreSQL - Hiperescala (Citus)
description: Conceitos para a auditoria pgAudit registando-se na Base de Dados Azure para PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 8a36062a2d29bcec10279d73211526a0dcba619e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101702118"
---
# <a name="audit-logging-in-azure-database-for-postgresql---hyperscale-citus"></a>Registo de auditoria na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)

O registo de auditoria das atividades de base de dados na Base de Dados Azure para PostgreSQL - Hiperescala (Citus) está disponível através da extensão de Auditoria PósgreSQL: [pgAudit](https://www.pgaudit.org/). o pgAudit fornece registos de auditoria de sessão detalhada e/ou objeto.

> [!IMPORTANT]
> pgAudit está em pré-visualização na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)

Se quiser registos ao nível de recursos do Azure para operações como a escala de cálculo e armazenamento, consulte o Registo de [Atividades Azure](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="usage-considerations"></a>Considerações de utilização
Por predefinição, as instruções de registo pgAudit são emitidas juntamente com as instruções de registo regulares através da função de registo padrão do Postgres. Na Base de Dados Azure para PostgreSQL - Hiperescala (Citus), pode configurar todos os registos a serem enviados para a loja de registos Azure Monitor para análise posterior no Log Analytics. Se ativar a registo de recursos do Azure Monitor, os seus registos serão automaticamente enviados (em formato JSON) para registos de armazenamento de Azure, Centros de Eventos e/ou Monitor Azure, dependendo da sua escolha.

## <a name="enabling-pgaudit"></a>Habilitação pgAudit

A extensão pgAudit está pré-instalada e ativada em todos os nós do grupo de servidores Hyperscale (Citus). Não são necessárias medidas para o ativar.

## <a name="pgaudit-settings"></a>definições pgAudit

o pgAudit permite-lhe configurar sessão ou registo de auditoria de objetos. [O registo de auditoria](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) de sessão emite registos detalhados de declarações executadas. [O registo de auditoria de objetos](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) é uma auditoria a relações específicas. Pode optar por configurar um ou ambos os tipos de registo. 

> [!NOTE]
> as definições pgAudit são especificadas globalmente e não podem ser especificadas a nível de base de dados ou de função.
>
> Além disso, as definições pgAudit são especificadas por nó num grupo de servidor. Para fazer uma alteração em todos os nós, deve aplicá-lo individualmente a cada nó.

Tem de configurar os parâmetros pgAudit para começar a registar registos. A [documentação pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) fornece a definição de cada parâmetro. Teste primeiro os parâmetros e confirme que está a obter o comportamento esperado.

> [!NOTE]
> A definição `pgaudit.log_client` para ON irá redirecionar os registos para um processo de cliente (como o psql) em vez de ser escrito para arquivar. Esta definição deve, normalmente, ser deixada desativada. <br> <br>
> `pgaudit.log_level` é ativado apenas quando `pgaudit.log_client` está ativado.

> [!NOTE]
> Na Base de Dados Azure para PostgreSQL - Hiperescala (Citus), `pgaudit.log` não pode ser configurado usando um `-` atalho de sinal (menos) conforme descrito na documentação pgAudit. Todas as aulas de declaração requeridas (LER, ESCREVER, etc.) devem ser especificadas individualmente.

## <a name="audit-log-format"></a>Formato do registo de auditoria
Cada entrada de auditoria é indicada `AUDIT:` perto do início da linha de registo. O formato do resto da entrada é detalhado na [documentação do PGAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

## <a name="getting-started"></a>Introdução
Para começar rapidamente, ajuste `pgaudit.log` para , e abra os `WRITE` registos do seu servidor para rever a saída. 

## <a name="viewing-audit-logs"></a>Visualização de registos de auditoria
A forma como acede aos registos depende do ponto final que escolher. Para o Azure Storage, consulte o artigo da [conta de armazenamento de registos.](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) Para Os Centros de Eventos, consulte o [artigo de registos Azure.](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

Para registos do Monitor Azure, os registos são enviados para o espaço de trabalho selecionado. Os registos Postgres utilizam o modo de recolha **AzureDiagnostics,** para que possam ser consultados a partir da tabela AzureDiagnostics. Os campos na tabela são descritos abaixo. Saiba mais sobre consulta e alerta na visão geral dos Registos do [Monitor Azure.](../azure-monitor/logs/log-query-overview.md)

Podes usar esta consulta para começar. Pode configurar alertas com base em consultas.

Procure por todas as entradas pgAudit em registos postgres para um servidor particular no último dia
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Passos seguintes

- [Saiba como configurar o registo na Base de Dados Azure para PostgreSQL - Hiperescala (Citus) e como aceder a registos](howto-hyperscale-logging.md)