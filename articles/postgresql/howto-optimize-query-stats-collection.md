---
title: Otimizar a recolha de estatísticas de consulta - Azure Database for PostgreSQL - Single Server
description: Este artigo descreve como pode otimizar a recolha de estatísticas de consulta numa Base de Dados Azure para PostgreSQL - Servidor Único
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: bc731f6f6a5a60bce0851bf8fe5874f7149f3899
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90901469"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql---single-server"></a>Otimizar a recolha de estatísticas de consulta numa Base de Dados Azure para PostgreSQL - Servidor Único
Este artigo descreve como otimizar a recolha de estatísticas de consulta numa Base de Dados Azure para servidor PostgreSQL.

## <a name="use-pg_stats_statements"></a>Use pg_stats_statements
**Pg_stat_statements** é uma extensão PostgreSQL que é ativada por padrão na Base de Dados Azure para PostgreSQL. A extensão fornece um meio para rastrear as estatísticas de execução de todas as declarações SQL executadas por um servidor. Este módulo liga-se a todas as execuções de consultas e vem com um custo de desempenho não trivial. Ativar **pg_stat_statements** força a consulta de texto escreve para ficheiros no disco.

Se tiver consultas únicas com texto de consulta longa ou não monitorizar **ativamente pg_stat_statements,** desative **pg_stat_statements** para melhor desempenho. Para tal, altere a definição para `pg_stat_statements.track = NONE` .

Algumas cargas de trabalho dos clientes viram até uma melhoria de desempenho de 50% quando **pg_stat_statements** é desativada. A troca que faz quando desativa pg_stat_statements é a incapacidade de resolver problemas de desempenho.

Para `pg_stat_statements.track = NONE` definir:

- No portal Azure, aceda à [página de gestão de recursos PostgreSQL e selecione a lâmina dos parâmetros do servidor](howto-configure-server-parameters-using-portal.md).

  :::image type="content" source="./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png" alt-text="Lâmina de parâmetro do servidor PostgreSQL":::

- Utilize a configuração do servidor de postgres [Azure CLI](howto-configure-server-parameters-using-cli.md) az definida para `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE` .

## <a name="use-the-query-store"></a>Utilize a Loja de Consultas 
A funcionalidade [de Loja de Consultas](concepts-query-store.md) na Base de Dados Azure para PostgreSQL fornece um método mais eficaz para rastrear estatísticas de consulta. Recomendamos esta funcionalidade como alternativa à utilização *de pg_stats_statements*. 

## <a name="next-steps"></a>Passos seguintes
Considere a definição `pg_stat_statements.track = NONE` no [portal Azure](howto-configure-server-parameters-using-portal.md) ou utilizando o [CLI Azure](howto-configure-server-parameters-using-cli.md).

Para obter mais informações, consulte: 
- [Cenários de utilização da Loja de Consulta](concepts-query-store-scenarios.md) 
- [As melhores práticas da Loja de Consultas](concepts-query-store-best-practices.md) 
