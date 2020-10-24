---
title: Registo de auditoria - Base de Dados de Azure para PostgreSQL - Servidor Único
description: Conceitos para a auditoria pgAudit iniciar sessão na Base de Dados Azure para PostgreSQL - Servidor Único.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 615297a4bf47d80c9313f011b90d343b7ae680e3
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488049"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Registo de auditoria na Base de Dados Azure para PostgreSQL - Servidor Único

O registo de auditoria das atividades de base de dados na Base de Dados Azure para PostgreSQL - Servidor Único está disponível através da Extensão de Auditoria PósgreSQL: [pgAudit](https://www.pgaudit.org/). o pgAudit fornece registos de auditoria de sessão detalhada e/ou objeto.

> [!NOTE]
> pgAudit está em pré-visualização na Base de Dados Azure para PostgreSQL.
> A extensão só pode ser ativada nos servidores Otimizados para Fins Gerais e Memória.

Se quiser registos ao nível de recursos do Azure para operações como a escala de cálculo e armazenamento, consulte o Registo de [Atividades Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="usage-considerations"></a>Considerações de utilização
Por predefinição, as instruções de registo pgAudit são emitidas juntamente com as instruções de registo regulares através da função de registo padrão do Postgres. Na Base de Dados do Azure para PostgreSQL, estes ficheiros .log podem ser transferidos através do portal do Azure ou da CLI. O armazenamento máximo para a recolha de ficheiros é de 1 GB, e cada ficheiro está disponível por um máximo de sete dias (o padrão é de três dias). Este serviço é uma opção de armazenamento de curto prazo.

Em alternativa, pode configurar todos os registos a serem enviados para a loja Azure Monitor Para análise posterior no Log Analytics. Se ativar a registo de recursos do Azure Monitor, os seus registos serão automaticamente enviados (em formato JSON) para registos de armazenamento de Azure, Centros de Eventos e/ou Monitor Azure, dependendo da sua escolha.

A ativação da pgAudit gera um grande volume de inícios de sessão num servidor, o que terá um impacto no desempenho e no armazenamento de registos. Recomendamos que utilize registos Azure Monitor, que oferecem opções de armazenamento a mais longo prazo, bem como funcionalidades de análise e alerta. Recomendamos que desligue o início de sessão padrão para reduzir o impacto sobre o desempenho de inícios de sessão adicionais:

   1. Desa parte do parâmetro `logging_collector` para OFF. 
   2. Reinicie o servidor para aplicar esta alteração.

Para aprender a configurar o registo de registos de armazenamento, evento ou monitor Azure, visite a secção de registos de recursos do artigo de [registos](concepts-server-logs.md)do servidor .

## <a name="installing-pgaudit"></a>Instalação pgAudit

Para instalar o pgAudit, é necessário incluí-lo nas bibliotecas de pré-carregamento partilhadas do servidor. Uma alteração no parâmetro de `shared_preload_libraries` Postgres requer que um servidor reinicie a fazer efeito. Pode alterar parâmetros utilizando o [portal Azure](howto-configure-server-parameters-using-portal.md), [Azure CLI](howto-configure-server-parameters-using-cli.md)ou [REST API](/rest/api/postgresql/configurations/createorupdate).

Utilizando o [portal Azure:](https://portal.azure.com)

   1. Selecione o servidor da Base de Dados do Azure para PostgreSQL.
   2. Na barra lateral, selecione **Parâmetros do Servidor**.
   3. Procure o `shared_preload_libraries` parâmetro.
   4. Selecione **pgaudit**.
   5. Reinicie o servidor para aplicar a alteração.

   6. Ligue-se ao seu servidor usando um cliente (como o psql) e ative a extensão pgAudit
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Se vir um erro, confirme que reiniciou o servidor depois de ter guardado `shared_preload_libraries` .

## <a name="pgaudit-settings"></a>definições pgAudit

o pgAudit permite-lhe configurar sessão ou registo de auditoria de objetos. [O registo de auditoria](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) de sessão emite registos detalhados de declarações executadas. [O registo de auditoria de objetos](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) é uma auditoria a relações específicas. Pode optar por configurar um ou ambos os tipos de registo. 

> [!NOTE]
> as definições pgAudit são especificadas gloabally e não podem ser especificadas numa base de dados ou nível de função.

Uma vez [instalado o pgAudit,](#installing-pgaudit)pode configurar os seus parâmetros para iniciar a sessão. A [documentação pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) fornece a definição de cada parâmetro. Teste primeiro os parâmetros e confirme que está a obter o comportamento esperado.

> [!NOTE]
> A definição `pgaudit.log_client` para ON irá redirecionar os registos para um processo de cliente (como o psql) em vez de ser escrito para arquivar. Esta definição deve, normalmente, ser deixada desativada. <br> <br>
> `pgaudit.log_level` só está ativado quando `pgaudit.log_client` está ligado.

> [!NOTE]
> Na Base de Dados Azure para PostgreSQL, `pgaudit.log` não é possível configurar um `-` atalho de sinal (menos) conforme descrito na documentação pgAudit. Todas as classes de instruções necessárias (LEITURA, ESCRITA, etc.) devem ser especificadas individualmente.

### <a name="audit-log-format"></a>Formato do registo de auditoria
Cada entrada de auditoria é indicada `AUDIT:` perto do início da linha de registo. O formato do resto da entrada é detalhado na [documentação do PGAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

Se necessitar de outros campos para satisfazer os seus requisitos de auditoria, utilize o parâmetro Postgres `log_line_prefix` . `log_line_prefix` é uma cadeia que é saída no início de cada linha de log Postgres. Por exemplo, a seguinte `log_line_prefix` definição fornece etiqueta de tempo, nome de utilizador, nome de base de dados e ID de processo:

```
t=%m u=%u db=%d pid=[%p]:
```

Para saber mais `log_line_prefix` sobre, visite a [documentação postgreSQL.](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX)

### <a name="getting-started"></a>Introdução
Para começar rapidamente, ajuste `pgaudit.log` para , e abra seus `WRITE` registos para rever a saída. 

## <a name="viewing-audit-logs"></a>Visualização de registos de auditoria
Se estiver a utilizar ficheiros .log, os seus registos de auditoria serão incluídos no mesmo ficheiro que os registos de erro do PostgreSQL. Pode descarregar ficheiros de registo a partir do [portal](howto-configure-server-logs-in-portal.md) Azure ou [CLI](howto-configure-server-logs-using-cli.md). 

Se estiver a utilizar o registo de recursos Azure, a forma como acede aos registos depende do ponto final que escolher. Para o Azure Storage, consulte o artigo da [conta de armazenamento de registos.](../azure-monitor/platform/resource-logs.md#send-to-azure-storage) Para Os Centros de Eventos, consulte o [artigo de registos Azure.](../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs)

Para registos do Monitor Azure, os registos são enviados para o espaço de trabalho selecionado. Os registos Postgres utilizam o modo de recolha **AzureDiagnostics,** para que possam ser consultados a partir da tabela AzureDiagnostics. Os campos na tabela são descritos abaixo. Saiba mais sobre consulta e alerta na visão geral dos Registos do [Monitor Azure.](../azure-monitor/log-query/log-query-overview.md)

Podes usar esta consulta para começar. Pode configurar alertas com base em consultas.

Procure todos os registos postgres para um servidor particular no último dia
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Passos seguintes
- [Saiba mais sobre o registo na Base de Dados Azure para PostgreSQL](concepts-server-logs.md)
- Saiba como definir parâmetros utilizando o [portal Azure](howto-configure-server-parameters-using-portal.md), [Azure CLI,](howto-configure-server-parameters-using-cli.md)ou [REST API](/rest/api/postgresql/configurations/createorupdate).