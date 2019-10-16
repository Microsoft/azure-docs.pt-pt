---
title: Log de auditoria usando pgAudit no banco de dados do Azure para PostgreSQL-servidor único
description: Conceitos para o log de auditoria do pgAudit no banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 49ad7334c418e29c821320608be729e060b4a8ae
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331339"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Log de auditoria no banco de dados do Azure para PostgreSQL-servidor único

Log de auditoria de atividades de banco de dados no banco de dados do Azure para PostgreSQL-um servidor único está disponível por meio da extensão de auditoria PostgreSQL: [pgAudit](https://www.pgaudit.org/). o pgAudit fornece o log de auditoria detalhado de sessão e/ou objeto.

> [!NOTE]
> pgAudit está em visualização no banco de dados do Azure para PostgreSQL.
> A extensão pode ser habilitada somente em servidores Uso Geral e com otimização de memória.

Se você quiser logs no nível de recursos do Azure para operações como o dimensionamento de computação e armazenamento, consulte o [log de atividades do Azure](../azure-monitor/platform/activity-logs-overview.md).

## <a name="usage-considerations"></a>Considerações de uso
Por predefinição, as instruções de registo pgAudit são emitidas juntamente com as instruções de registo regulares através da função de registo padrão do Postgres. Na Base de Dados do Azure para PostgreSQL, estes ficheiros .log podem ser transferidos através do portal do Azure ou da CLI. O armazenamento máximo para a coleção de arquivos é de 1 GB e cada arquivo está disponível por um máximo de sete dias (o padrão é três dias). Esse serviço é uma opção de armazenamento de curto prazo.

Como alternativa, você pode configurar todos os logs a serem emitidos para o serviço log de diagnóstico do Azure Monitor. Se você habilitar Azure Monitor log de diagnóstico, os logs serão enviados automaticamente (no formato JSON) para o armazenamento do Azure, os hubs de eventos e/ou os logs de Azure Monitor, dependendo de sua escolha.

A ativação da pgAudit gera um grande volume de inícios de sessão num servidor, o que terá um impacto no desempenho e no armazenamento de registos. Recomendamos que utilize o serviço de registo de diagnósticos do Azure, que oferece opções de armazenamento de longo prazo, bem como funcionalidades de análise e alerta. Recomendamos que desligue o início de sessão padrão para reduzir o impacto sobre o desempenho de inícios de sessão adicionais:

   1. Defina o parâmetro `logging_collector` como OFF. 
   2. Reinicie o servidor para aplicar essa alteração.

Para saber como configurar o log no armazenamento do Azure, nos hubs de eventos ou nos logs de Azure Monitor, visite a seção logs de diagnóstico do [artigo logs do servidor](concepts-server-logs.md).

## <a name="installing-pgaudit"></a>Instalando o pgAudit

Para instalar o pgAudit, você precisa incluí-lo nas bibliotecas de pré-carregamento compartilhadas do servidor. Uma alteração no parâmetro `shared_preload_libraries` do postgres requer a reinicialização do servidor para entrar em vigor. Você pode alterar os parâmetros usando o [portal do Azure](howto-configure-server-parameters-using-portal.md), [CLI do Azure](howto-configure-server-parameters-using-cli.md)ou a [API REST](/rest/api/postgresql/configurations/createorupdate).

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
> Se você vir um erro, confirme que você reiniciou o servidor depois de salvar `shared_preload_libraries`.

## <a name="pgaudit-settings"></a>configurações de pgAudit

o pgAudit permite que você configure o log de auditoria de sessão ou objeto. O [log de auditoria de sessão](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) emite logs detalhados de instruções executadas. O [log de auditoria de objeto](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) tem o escopo de auditoria para relações específicas. Você pode optar por configurar um ou ambos os tipos de registro em log. 

> [!NOTE]
> as configurações de pgAudit são especificadas identificador global e não podem ser especificadas em um nível de banco de dados ou função.

Depois de [instalar o pgAudit](#installing-pgaudit), você pode configurar seus parâmetros para iniciar o registro em log. A [documentação do pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) fornece a definição de cada parâmetro. Teste os parâmetros primeiro e confirme que você está obtendo o comportamento esperado.

> [!NOTE]
> Definir `pgaudit.log_client` como ON irá redirecionar os logs para um processo de cliente (como psql) em vez de ser gravado no arquivo. Esta definição deve, normalmente, ser deixada desativada.

> [!NOTE]
> `pgaudit.log_level` só é habilitado quando `pgaudit.log_client` está ativado. Além disso, no portal do Azure, atualmente há um bug com `pgaudit.log_level`: uma caixa de combinação é mostrada, indicando que vários níveis podem ser selecionados. No entanto, apenas um nível deve ser selecionado. 

> [!NOTE]
> No banco de dados do Azure para PostgreSQL, não é possível definir `pgaudit.log` usando um atalho de sinal de `-` (menos), conforme descrito na documentação do pgAudit. Todas as classes de instruções necessárias (LEITURA, ESCRITA, etc.) devem ser especificadas individualmente.

### <a name="audit-log-format"></a>Formato do log de auditoria
Cada entrada de auditoria é indicada por `AUDIT:` próximo ao início da linha de log. O formato do restante da entrada é detalhado na [documentação do pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

Se você precisar de outros campos para atender aos seus requisitos de auditoria, use o parâmetro postgres `log_line_prefix`. `log_line_prefix` é uma cadeia de caracteres que é saída no início de cada linha de log do Postgres. Por exemplo, a configuração `log_line_prefix` a seguir fornece timestamp, username, Database Name e Process ID:

```
t=%m u=%u db=%d pid=[%p]:
```

Para saber mais sobre `log_line_prefix`, visite a [documentação do PostgreSQL](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX).

### <a name="getting-started"></a>Introdução
Para começar rapidamente, defina `pgaudit.log` como `WRITE` e abra os logs para examinar a saída. 


## <a name="next-steps"></a>Passos seguintes
- [Saiba mais sobre o log no banco de dados do Azure para PostgreSQL](concepts-server-logs.md)
- Saiba como definir parâmetros usando o [portal do Azure](howto-configure-server-parameters-using-portal.md), [CLI do Azure](howto-configure-server-parameters-using-cli.md)ou a [API REST](/rest/api/postgresql/configurations/createorupdate).
