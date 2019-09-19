---
title: Log de auditoria usando pgAudit no banco de dados do Azure para PostgreSQL-servidor único
description: Conceitos para o log de auditoria do pgAudit no banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 8b4cbe309e310ef1fc384224c952a6f04385b1dd
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71092264"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Log de auditoria no banco de dados do Azure para PostgreSQL-servidor único

Log de auditoria de atividades de banco de dados no banco de dados do Azure para PostgreSQL-um servidor único está disponível por meio da extensão de auditoria PostgreSQL: [pgAudit](https://www.pgaudit.org/). o pgAudit fornece o log de auditoria detalhado de sessão e/ou objeto.

> [!NOTE]
> pgAudit pode ser habilitado somente em servidores Uso Geral e com otimização de memória.

## <a name="usage-considerations"></a>Considerações de uso
Por padrão, as instruções de log pgAudit são emitidas junto com suas instruções de log regulares usando o recurso de log padrão do Postgres. No banco de dados do Azure para PostgreSQL, esses arquivos. log podem ser baixados por meio do portal do Azure ou da CLI. O armazenamento máximo para a coleção de arquivos é de 1 GB e cada arquivo está disponível por um máximo de sete dias (o padrão é três dias). Esse serviço é uma opção de armazenamento de curto prazo.

Como alternativa, você pode configurar todos os logs a serem emitidos para o serviço log de diagnóstico do Azure Monitor. Se você habilitar Azure Monitor log de diagnóstico, os logs serão enviados automaticamente (no formato JSON) para o armazenamento do Azure, os hubs de eventos e/ou os logs de Azure Monitor, dependendo de sua escolha.

A habilitação de pgAudit gera um grande volume de log em um servidor, que tem um impacto no desempenho e no armazenamento de log. Recomendamos que você use o serviço log de diagnóstico do Azure, que oferece opções de armazenamento de longo prazo, bem como recursos de análise e alerta. Recomendamos que você desative o registro em log padrão para reduzir o impacto no desempenho de log adicional:

   1. Defina o parâmetro `logging_collector` como off. 
   2. Reinicie o servidor para aplicar essa alteração.

Para saber como configurar o log no armazenamento do Azure, nos hubs de eventos ou nos logs de Azure Monitor, visite a seção logs de diagnóstico do [artigo logs do servidor](concepts-server-logs.md).

## <a name="installing-pgaudit"></a>Instalando o pgAudit

Para instalar o pgAudit, você precisa incluí-lo nas bibliotecas de pré-carregamento compartilhadas do servidor. Uma alteração no parâmetro de `shared_preload_libraries` postgres requer a reinicialização do servidor para entrar em vigor. Você pode alterar os parâmetros usando o [portal do Azure](howto-configure-server-parameters-using-portal.md), [CLI do Azure](howto-configure-server-parameters-using-cli.md)ou a [API REST](/rest/api/postgresql/configurations/createorupdate).

Usando o [portal do Azure](https://portal.azure.com):

   1. Selecione o servidor da Base de Dados do Azure para PostgreSQL.
   2. Na barra lateral, selecione **parâmetros do servidor**.
   3. Procure o `shared_preload_libraries` parâmetro.
   4. Selecione **pgaudit**.
   5. Reinicie o servidor para aplicar a alteração.

   6. Conectar-se ao servidor usando um cliente (como psql) e habilitar a extensão pgAudit
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Se você vir um erro, confirme que você reiniciou o servidor após salvar `shared_preload_libraries`.

## <a name="pgaudit-settings"></a>configurações de pgAudit

o pgAudit permite que você configure o log de auditoria de sessão ou objeto. O [log de auditoria de sessão](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) emite logs detalhados de instruções executadas. O [log de auditoria de objeto](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) tem o escopo de auditoria para relações específicas. Você pode optar por configurar um ou ambos os tipos de registro em log. 

> [!NOTE]
> as configurações de pgAudit são especificadas identificador global e não podem ser especificadas em um nível de banco de dados ou função.

Depois de [instalar o pgAudit](#installing-pgaudit), você pode configurar seus parâmetros para iniciar o registro em log. A [documentação do pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) fornece a definição de cada parâmetro. Teste os parâmetros primeiro e confirme que você está obtendo o comportamento esperado.

> [!NOTE]
> Definir `pgaudit.log_client` como on redirecionará os logs para um processo de cliente (como psql) em vez de ser gravado no arquivo. Em geral, essa configuração deve ser deixada desabilitada.

> [!NOTE]
> `pgaudit.log_level`é habilitado somente quando `pgaudit.log_client` o está ativado. Além disso, no portal do Azure, atualmente há um bug com `pgaudit.log_level`: uma caixa de combinação é mostrada, indicando que vários níveis podem ser selecionados. No entanto, apenas um nível deve ser selecionado. 

> [!NOTE]
> No banco de dados do Azure `pgaudit.log` para PostgreSQL, não pode `-` ser definido usando um atalho de sinal (menos), conforme descrito na documentação do pgAudit. Todas as classes de instrução necessárias (leitura, gravação etc.) devem ser especificadas individualmente.

### <a name="audit-log-format"></a>Formato do log de auditoria
Cada entrada de auditoria é indicada `AUDIT:` próximo ao início da linha de log. O formato do restante da entrada é detalhado na [documentação do pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

Se você precisar de outros campos para atender aos seus requisitos de auditoria, use o `log_line_prefix`parâmetro Postgres. `log_line_prefix`é uma cadeia de caracteres que é saída no início de cada linha de log do Postgres. Por exemplo, a configuração `log_line_prefix` a seguir fornece timestamp, username, Database Name e Process ID:

```
t=%m u=%u db=%d pid=[%p]:
```

Para saber mais sobre `log_line_prefix`o, visite a [documentação do PostgreSQL](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX).

### <a name="getting-started"></a>Introdução
Para começar rapidamente, defina `pgaudit.log` como `WRITE`e abra seus logs para examinar a saída. 


## <a name="next-steps"></a>Passos Seguintes
- [Saiba mais sobre o log no banco de dados do Azure para PostgreSQL](concepts-server-logs.md)
- Saiba como definir parâmetros usando o [portal do Azure](howto-configure-server-parameters-using-portal.md), [CLI do Azure](howto-configure-server-parameters-using-cli.md)ou a [API REST](/rest/api/postgresql/configurations/createorupdate).
