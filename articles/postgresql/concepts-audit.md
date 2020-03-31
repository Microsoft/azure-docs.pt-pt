---
title: Registo de auditoria - Base de Dados Azure para PostgreSQL - Servidor Único
description: Conceitos para auditoria pgAudit login em Azure Database for PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 45490e398abd8b5bd3c10adb95b56e1019d2bb94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842474"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Registo de auditoria na Base de Dados Azure para PostgreSQL - Servidor Único

Auditar as atividades de base de dados na Base de Dados Azure para PostgreSQL - Servidor Único está disponível através da extensão de auditoria PostgreSQL: [pgAudit](https://www.pgaudit.org/). pgAudit fornece registo de sessão detalhada e/ou auditoria de objetos.

> [!NOTE]
> pgAudit está em pré-visualização na Base de Dados Azure para PostgreSQL.
> A extensão só pode ser ativada em servidores otimizados para fins gerais e memória.

Se quiser registos de nível de recursos Azure para operações como a computação e a escala de armazenamento, consulte o Registo de [Atividades do Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="usage-considerations"></a>Considerações de utilização
Por predefinição, as instruções de registo pgAudit são emitidas juntamente com as instruções de registo regulares através da função de registo padrão do Postgres. Na Base de Dados do Azure para PostgreSQL, estes ficheiros .log podem ser transferidos através do portal do Azure ou da CLI. O armazenamento máximo para a recolha de ficheiros é de 1 GB, e cada ficheiro está disponível por um máximo de sete dias (o predefinido é de três dias). Este serviço é uma opção de armazenamento de curto prazo.

Em alternativa, pode configurar todos os registos a emitir para o serviço de registo de diagnóstico do Azure Monitor. Se ativar o registo de diagnóstico do Azure Monitor, os seus registos serão automaticamente enviados (em formato JSON) para o Azure Storage, Event Hubs e/ou Registos do Monitor Azure, dependendo da sua escolha.

A ativação da pgAudit gera um grande volume de inícios de sessão num servidor, o que terá um impacto no desempenho e no armazenamento de registos. Recomendamos que utilize o serviço de registo de diagnósticos do Azure, que oferece opções de armazenamento de longo prazo, bem como funcionalidades de análise e alerta. Recomendamos que desligue o início de sessão padrão para reduzir o impacto sobre o desempenho de inícios de sessão adicionais:

   1. Defina o `logging_collector` parâmetro para desligar. 
   2. Reiniciar o servidor para aplicar esta alteração.

Para saber como configurar o registo de logapara o Azure Storage, Os Centros de Eventos ou os registos do Monitor Azure, visite a secção de registos de diagnóstico do artigo de [registos](concepts-server-logs.md)do servidor .

## <a name="installing-pgaudit"></a>Instalação pgAudit

Para instalar o pgAudit, é necessário incluí-lo nas bibliotecas de pré-carga partilhadas do servidor. Uma alteração no parâmetro `shared_preload_libraries` do Postgres requer que um servidor reinicie para fazer efeito. Pode alterar parâmetros utilizando o [portal Azure,](howto-configure-server-parameters-using-portal.md) [Azure CLI](howto-configure-server-parameters-using-cli.md)ou [REST API](/rest/api/postgresql/configurations/createorupdate).

Utilizando o [portal Azure:](https://portal.azure.com)

   1. Selecione o servidor da Base de Dados do Azure para PostgreSQL.
   2. Na barra lateral, selecione **Parâmetros de servidor**.
   3. Procura o `shared_preload_libraries` parâmetro.
   4. Selecione **pgaudit**.
   5. Reiniciar o servidor para aplicar a alteração.

   6. Ligue-se ao seu servidor utilizando um cliente (como o psql) e ative a extensão pgAudit
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Se vir um erro, confirme que reiniciou o seu servidor depois de guardar `shared_preload_libraries`.

## <a name="pgaudit-settings"></a>definições pgAudit

pgAudit permite configurar registo de sessão ou auditoria de objetos. [A exploração de registos de auditoria](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) de sessão emite registos detalhados de declarações executadas. [A exploração madeireira](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) de auditoria de objetos é auditada em relações específicas. Pode optar por configurar um ou ambos os tipos de exploração. 

> [!NOTE]
> as definições de pgAudit são especificadas gloabally e não podem ser especificadas a uma base de dados ou a nível de função.

Depois de instalado o [pgAudit,](#installing-pgaudit)pode configurar os seus parâmetros para iniciar a exploração. A [documentação pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) fornece a definição de cada parâmetro. Teste primeiro os parâmetros e confirme que está a ter o comportamento esperado.

> [!NOTE]
> A `pgaudit.log_client` definição para ON redirecionará os registos para um processo de cliente (como o psql) em vez de ser escrito para arquivar. Esta definição deve, normalmente, ser deixada desativada. <br> <br>
> `pgaudit.log_level`só está ativado quando `pgaudit.log_client` está ligado.

> [!NOTE]
> Na Base de Dados Azure `pgaudit.log` para PostgreSQL, não pode ser configurado utilizando um `-` atalho de sinal (menos) conforme descrito na documentação pgAudit. Todas as classes de instruções necessárias (LEITURA, ESCRITA, etc.) devem ser especificadas individualmente.

### <a name="audit-log-format"></a>Formato de registo de auditoria
Cada entrada de `AUDIT:` auditoria é indicada perto do início da linha de registo. O formato do resto da entrada é detalhado na [documentação pgAudit.](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)

Se precisar de outros campos para satisfazer os seus requisitos `log_line_prefix`de auditoria, utilize o parâmetro Postgres . `log_line_prefix`é uma cadeia que é saída no início de cada linha de registo postgres. Por exemplo, `log_line_prefix` a seguinte definição fornece carimbo de tempo, nome de utilizador, nome da base de dados e ID do processo:

```
t=%m u=%u db=%d pid=[%p]:
```

Para saber `log_line_prefix`mais sobre, visite a [documentação PostgreSQL.](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX)

### <a name="getting-started"></a>Introdução
Para começar rapidamente, prepare-se `pgaudit.log` para `WRITE`, e abra os seus registos para rever a saída. 

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
- [Saiba mais sobre a sessão de registo na Base de Dados Azure para PostgreSQL](concepts-server-logs.md)
- Aprenda a definir parâmetros utilizando o [portal Azure,](howto-configure-server-parameters-using-portal.md) [Azure CLI](howto-configure-server-parameters-using-cli.md)ou [REST API](/rest/api/postgresql/configurations/createorupdate).
