---
title: Otimizar a recolha de estatísticas de consulta numa base de dados do Azure para o servidor PostgreSQL
description: Este artigo descreve como pode otimizar a recolha de estatísticas de consulta numa base de dados do Azure para o servidor PostgreSQL.
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 10/25/2018
ms.date: 02/18/2019
ms.openlocfilehash: 428a22f79008130448bef65f14322d6880b1b367
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60422713"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql-server"></a>Otimizar a recolha de estatísticas de consulta numa base de dados do Azure para o servidor PostgreSQL 
Este artigo descreve como otimizar a recolha de estatísticas de consulta numa base de dados do Azure para o servidor PostgreSQL.

## <a name="use-pgstatsstatements"></a>Utilizar pg_stats_statements
**Pg_stat_statements** é uma extensão de PostgreSQL que está ativada por predefinição na base de dados do Azure para PostgreSQL. A extensão fornece um meio para controlar as estatísticas de execução de todas as instruções SQL executadas por um servidor. Este módulo conecta-se em cada execução de consulta e vem com um custo de desempenho não trivial. Habilitando **pg_stat_statements** forças consultar texto escritas arquivos no disco.

Se tiver consultas exclusivas com texto de consulta longo ou que não a monitorizar ativamente **pg_stat_statements**, desativar **pg_stat_statements** para um melhor desempenho. Para tal, alterar a definição para `pg_stat_statements.track = NONE`.

Algumas cargas de trabalho do cliente já VI até uma melhoria de desempenho de 50 por cento quando **pg_stat_statements** está desativada. O compromisso que toma quando desativar pg_stat_statements é a incapacidade de resolver problemas de desempenho.

Para definir `pg_stat_statements.track = NONE`:

- No portal do Azure, vá para o [gestão de recursos do PostgreSQL página e selecione o painel de parâmetros de servidor](howto-configure-server-parameters-using-portal.md).

  ![Painel de parâmetro de servidor PostgreSQL](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Utilize o [CLI do Azure](howto-configure-server-parameters-using-cli.md) definida uma configuração de az postgres server `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`.

## <a name="use-the-query-store"></a>Utilizar o Store de consulta 
O [Query Store](concepts-query-store.md) funcionalidade na base de dados do Azure para PostgreSQL fornece um método mais eficaz para controlar as estatísticas de consulta. Recomendamos que esta funcionalidade, como uma alternativa ao uso *pg_stats_statements*. 

## <a name="next-steps"></a>Passos Seguintes
Considere definir `pg_stat_statements.track = NONE` no [portal do Azure](howto-configure-server-parameters-using-portal.md) ou ao utilizar os [CLI do Azure](howto-configure-server-parameters-using-cli.md).

Para obter mais informações, consulte: 
- [Cenários de utilização do Arquivo de Consultas](concepts-query-store-scenarios.md) 
- [Melhores práticas de Store de consulta](concepts-query-store-best-practices.md) 
