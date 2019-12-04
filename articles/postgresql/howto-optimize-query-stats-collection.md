---
title: Otimizar coleta de estatísticas de consulta-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como você pode otimizar a coleta de estatísticas de consulta em um banco de dados do Azure para PostgreSQL-servidor único
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: f467f01118470eb51f7decf3bd6457917c566723
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770174"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql---single-server"></a>Otimizar a coleta de estatísticas de consulta em um banco de dados do Azure para PostgreSQL-servidor único
Este artigo descreve como otimizar a coleta de estatísticas de consulta em um servidor de banco de dados do Azure para PostgreSQL.

## <a name="use-pg_stats_statements"></a>Usar pg_stats_statements
**Pg_stat_statements** é uma extensão PostgreSQL habilitada por padrão no banco de dados do Azure para PostgreSQL. A extensão fornece um meio para acompanhar as estatísticas de execução de todas as instruções SQL executadas por um servidor. Esse módulo se conecta a todas as execuções de consulta e vem com um custo de desempenho não trivial. A habilitação de **pg_stat_statements** força gravações de texto de consulta em arquivos no disco.

Se você tiver consultas exclusivas com texto de consulta longa ou não monitorar ativamente **pg_stat_statements**, desabilite **pg_stat_statements** para obter o melhor desempenho. Para fazer isso, altere a configuração para `pg_stat_statements.track = NONE`.

Algumas cargas de trabalho do cliente viram uma melhoria de desempenho de 50% quando o **pg_stat_statements** está desabilitado. A desvantagem que você faz ao desabilitar o pg_stat_statements é a incapacidade de solucionar problemas de desempenho.

Para definir `pg_stat_statements.track = NONE`:

- Na portal do Azure, vá para a [página de gerenciamento de recursos PostgreSQL e selecione a folha parâmetros de servidor](howto-configure-server-parameters-using-portal.md).

  ![Folha de parâmetro de servidor PostgreSQL](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Use a configuração de servidor [CLI do Azure](howto-configure-server-parameters-using-cli.md) AZ postgres definida como `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`.

## <a name="use-the-query-store"></a>Usar o Repositório de Consultas 
O recurso [repositório de consultas](concepts-query-store.md) no banco de dados do Azure para PostgreSQL fornece um método mais eficaz para controlar as estatísticas de consulta. Recomendamos esse recurso como uma alternativa ao uso de *pg_stats_statements*. 

## <a name="next-steps"></a>Passos seguintes
Considere definir `pg_stat_statements.track = NONE` no [portal do Azure](howto-configure-server-parameters-using-portal.md) ou usando o [CLI do Azure](howto-configure-server-parameters-using-cli.md).

Para obter mais informações, veja: 
- [Cenários de utilização do Arquivo de Consultas](concepts-query-store-scenarios.md) 
- [Práticas recomendadas de Repositório de Consultas](concepts-query-store-best-practices.md) 
