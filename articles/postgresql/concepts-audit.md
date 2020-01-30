---
title: Log de auditoria-banco de dados do Azure para PostgreSQL-servidor único
description: Conceitos para o log de auditoria do pgAudit no banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 45490e398abd8b5bd3c10adb95b56e1019d2bb94
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842474"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Log de auditoria no banco de dados do Azure para PostgreSQL-servidor único

Log de auditoria de atividades de banco de dados no banco de dados do Azure para PostgreSQL-um servidor único está disponível por meio da extensão de auditoria PostgreSQL: [pgAudit](https://www.pgaudit.org/). o pgAudit fornece o log de auditoria detalhado de sessão e/ou objeto.

> [!NOTE]
> pgAudit está em visualização no banco de dados do Azure para PostgreSQL.
> A extensão pode ser habilitada somente em servidores Uso Geral e com otimização de memória.

Se você quiser logs no nível de recursos do Azure para operações como o dimensionamento de computação e armazenamento, consulte o [log de atividades do Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="usage-considerations"></a>Considerações de uso
Por predefinição, as instruções de registo pgAudit são emitidas juntamente com as instruções de registo regulares através da função de registo padrão do Postgres. Na Base de Dados do Azure para PostgreSQL, estes ficheiros .log podem ser transferidos através do portal do Azure ou da CLI. O armazenamento máximo para a coleção de arquivos é de 1 GB e cada arquivo está disponível por um máximo de sete dias (o padrão é três dias). Esse serviço é uma opção de armazenamento de curto prazo.

Como alternativa, você pode configurar todos os logs a serem emitidos para o serviço log de diagnóstico do Azure Monitor. Se você habilitar Azure Monitor log de diagnóstico, os logs serão enviados automaticamente (no formato JSON) para o armazenamento do Azure, os hubs de eventos e/ou os logs de Azure Monitor, dependendo de sua escolha.

A ativação da pgAudit gera um grande volume de inícios de sessão num servidor, o que terá um impacto no desempenho e no armazenamento de registos. Recomendamos que utilize o serviço de registo de diagnósticos do Azure, que oferece opções de armazenamento de longo prazo, bem como funcionalidades de análise e alerta. Recomendamos que desligue o início de sessão padrão para reduzir o impacto sobre o desempenho de inícios de sessão adicionais:

   1. Defina o parâmetro `logging_collector` desligado. 
   2. Reinicie o servidor para aplicar essa alteração.

Para saber como configurar o log no armazenamento do Azure, nos hubs de eventos ou nos logs de Azure Monitor, visite a seção logs de diagnóstico do [artigo logs do servidor](concepts-server-logs.md).

## <a name="installing-pgaudit"></a>Instalando o pgAudit

Para instalar o pgAudit, você precisa incluí-lo nas bibliotecas de pré-carregamento compartilhadas do servidor. Uma alteração no parâmetro de `shared_preload_libraries` do Postgres requer que um servidor reinicie para fazer efeito. Você pode alterar os parâmetros usando o [portal do Azure](howto-configure-server-parameters-using-portal.md), [CLI do Azure](howto-configure-server-parameters-using-cli.md)ou a [API REST](/rest/api/postgresql/configurations/createorupdate).

Usando o [portal do Azure](https://portal.azure.com):

   1. Selecione o servidor da Base de Dados do Azure para PostgreSQL.
   2. Na barra lateral, selecione **parâmetros do servidor**.
   3. Procure o parâmetro `shared_preload_libraries`.
   4. Selecione **pgaudit**.
   5. Reinicie o servidor para aplicar a alteração.

   6. Conectar-se ao servidor usando um cliente (como psql) e habilitar a extensão pgAudit
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Se vir um erro, confirme que reiniciou o seu servidor depois de guardar `shared_preload_libraries`.

## <a name="pgaudit-settings"></a>configurações de pgAudit

o pgAudit permite que você configure o log de auditoria de sessão ou objeto. O [log de auditoria de sessão](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) emite logs detalhados de instruções executadas. O [log de auditoria de objeto](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) tem o escopo de auditoria para relações específicas. Você pode optar por configurar um ou ambos os tipos de registro em log. 

> [!NOTE]
> as configurações de pgAudit são especificadas identificador global e não podem ser especificadas em um nível de banco de dados ou função.

Depois de [instalar o pgAudit](#installing-pgaudit), você pode configurar seus parâmetros para iniciar o registro em log. A [documentação do pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) fornece a definição de cada parâmetro. Teste os parâmetros primeiro e confirme que você está obtendo o comportamento esperado.

> [!NOTE]
> A definição de `pgaudit.log_client` para ON redirecionará os registos para um processo de cliente (como o psql) em vez de ser escrito para arquivar. Esta definição deve, normalmente, ser deixada desativada. <br> <br>
> `pgaudit.log_level` só está ativado quando `pgaudit.log_client` estiver ligado.

> [!NOTE]
> No banco de dados do Azure para PostgreSQL, `pgaudit.log` não pode ser definida usando um atalho de sinal de `-` (menos), conforme descrito na documentação do pgAudit. Todas as classes de instruções necessárias (LEITURA, ESCRITA, etc.) devem ser especificadas individualmente.

### <a name="audit-log-format"></a>Formato do log de auditoria
Cada entrada de auditoria é indicada por `AUDIT:` próximo ao início da linha de log. O formato do restante da entrada é detalhado na [documentação do pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

Se precisar de outros campos para satisfazer os seus requisitos de auditoria, utilize o parâmetro Postgres `log_line_prefix`. `log_line_prefix` é uma cadeia que é saída no início de cada linha de registo de Postgres. Por exemplo, a seguinte definição `log_line_prefix` fornece carimbo de tempo, nome de utilizador, nome da base de dados e ID do processo:

```
t=%m u=%u db=%d pid=[%p]:
```

Para saber mais sobre `log_line_prefix`, visite a [documentação PostgreSQL.](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX)

### <a name="getting-started"></a>Introdução
Para começar rapidamente, defina `pgaudit.log` para `WRITE`e abra os logs para examinar a saída. 

## <a name="viewing-audit-logs"></a>Ver registos de auditoria
Se estiver a utilizar ficheiros .log, os seus registos de auditoria serão incluídos no mesmo ficheiro que os registos de erros do PostgreSQL. Pode descarregar ficheiros de registo a partir do [portal](howto-configure-server-logs-in-portal.md) Azure ou [CLI](howto-configure-server-logs-using-cli.md). 

Se estiver a utilizar o registo de diagnóstico Azure, a forma como acede aos registos depende do ponto final que escolher. Para o Armazenamento Azure, consulte o artigo da conta de armazenamento de [registos.](../azure-monitor/platform/resource-logs-collect-storage.md) Para Os Hubs de Eventos, consulte o artigo de [logs do Stream Azure.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

Para registos do Monitor Azure, os registos são enviados para o espaço de trabalho selecionado. Os registos Postgres utilizam o modo de recolha **AzureDiagnostics,** para que possam ser consultados a partir da tabela AzureDiagnostics. Os campos na mesa são descritos abaixo. Saiba mais sobre consulta e alerta na visão geral da consulta do [Monitor Azure.](../azure-monitor/log-query/log-query-overview.md)

Pode usar esta consulta para começar. Pode configurar alertas com base em consultas.

Procure todos os registos do Postgres para um determinado servidor no último dia
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Passos seguintes
- [Saiba mais sobre o log no banco de dados do Azure para PostgreSQL](concepts-server-logs.md)
- Saiba como definir parâmetros usando o [portal do Azure](howto-configure-server-parameters-using-portal.md), [CLI do Azure](howto-configure-server-parameters-using-cli.md)ou a [API REST](/rest/api/postgresql/configurations/createorupdate).
