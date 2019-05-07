---
title: Otimizar a recolha de estatísticas de consulta numa base de dados do Azure para o PostgreSQL – servidor único
description: Este artigo descreve como pode otimizar a recolha de estatísticas de consulta numa base de dados do Azure para o PostgreSQL – servidor único
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 7425ee7916fd71625f336a7af35f6481d1ed2474
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068965"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql---single-server"></a>Otimizar a recolha de estatísticas de consulta numa base de dados do Azure para PostgreSQL - servidor único
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
