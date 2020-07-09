---
title: Otimizar a recolha de estatísticas de consulta - Azure Database for PostgreSQL - Single Server
description: Este artigo descreve como pode otimizar a recolha de estatísticas de consulta numa Base de Dados Azure para PostgreSQL - Servidor Único
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: a4c2051a3f52ae363d8dc704aef790d8ce77efbd
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116204"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql---single-server"></a>Otimizar a recolha de estatísticas de consulta numa Base de Dados Azure para PostgreSQL - Servidor Único
Este artigo descreve como otimizar a recolha de estatísticas de consulta numa Base de Dados Azure para servidor PostgreSQL.

## <a name="use-pg_stats_statements"></a>Use pg_stats_statements
**Pg_stat_statements** é uma extensão PostgreSQL que é ativada por padrão na Base de Dados Azure para PostgreSQL. A extensão fornece um meio para rastrear as estatísticas de execução de todas as declarações SQL executadas por um servidor. Este módulo liga-se a todas as execuções de consultas e vem com um custo de desempenho não trivial. Ativar **pg_stat_statements** força a consulta de texto escreve para ficheiros no disco.

Se tiver consultas únicas com texto de consulta longa ou não monitorizar **ativamente pg_stat_statements,** desative **pg_stat_statements** para melhor desempenho. Para tal, altere a definição para `pg_stat_statements.track = NONE` .

Algumas cargas de trabalho dos clientes viram até uma melhoria de desempenho de 50% quando **pg_stat_statements** é desativada. A troca que faz quando desativa pg_stat_statements é a incapacidade de resolver problemas de desempenho.

Para `pg_stat_statements.track = NONE` definir:

- No portal Azure, aceda à [página de gestão de recursos PostgreSQL e selecione a lâmina dos parâmetros do servidor](howto-configure-server-parameters-using-portal.md).

  ![Lâmina de parâmetro do servidor PostgreSQL](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Utilize a configuração do servidor de postgres [Azure CLI](howto-configure-server-parameters-using-cli.md) az definida para `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE` .

## <a name="use-the-query-store"></a>Utilize a Loja de Consultas 
A funcionalidade [de Loja de Consultas](concepts-query-store.md) na Base de Dados Azure para PostgreSQL fornece um método mais eficaz para rastrear estatísticas de consulta. Recomendamos esta funcionalidade como alternativa à utilização *de pg_stats_statements*. 

## <a name="next-steps"></a>Próximos passos
Considere a definição `pg_stat_statements.track = NONE` no [portal Azure](howto-configure-server-parameters-using-portal.md) ou utilizando o [CLI Azure](howto-configure-server-parameters-using-cli.md).

Para obter mais informações, consulte: 
- [Cenários de utilização do Arquivo de Consultas](concepts-query-store-scenarios.md) 
- [Melhores práticas do Query Store](concepts-query-store-best-practices.md) 
