---
title: Utilizar sys_schema - Base de Dados Azure para o MySQL
description: Saiba como usar sys_schema para encontrar problemas de desempenho e manter a base de dados na Base de Dados Azure para o MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/30/2020
ms.openlocfilehash: 62a34a2dba459c6f65729cd5c6804378ee7f8b52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90902778"
---
# <a name="how-to-use-sys_schema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Como utilizar sys_schema para afinação de desempenho e manutenção de bases de dados em Azure Database for MySQL

O MySQL performance_schema, disponível pela primeira vez no MySQL 5.5, fornece instrumentação para muitos recursos vitais do servidor, tais como alocação de memória, programas armazenados, bloqueio de metadados, etc. No entanto, o performance_schema contém mais de 80 tabelas, e a obtenção da informação necessária requer frequentemente a junção de tabelas dentro do performance_schema, bem como tabelas do information_schema. Com base em performance_schema e information_schema, o sys_schema fornece uma poderosa coleção de pontos de [vista fáceis](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) de utilizar numa base de dados só de leitura e está totalmente ativado na Base de Dados Azure para a versão 5.7 do MySQL.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/sys-schema-views.png" alt-text="pontos de vista sobre sys_schema":::

Existem 52 pontos de vista no sys_schema, e cada vista tem um dos seguintes prefixos:

- Host_summary ou IO: Latências relacionadas com i/O.
- InnoDB: Estado do tampão innoDB e fechaduras.
- Memória: Utilização da memória pelo anfitrião e pelos utilizadores.
- Schema: Informações relacionadas com o esquema, tais como incremento automático, índices, etc.
- Declaração: Informação sobre declarações sql; pode ser afirmação que resultou em tomografia completa da mesa, ou tempo de consulta longa.
- Utilizador: Recursos consumidos e agrupados pelos utilizadores. Exemplos são ficheiros I/Os, ligações e memória.
- Aguarde: Aguarde eventos agrupados por anfitrião ou utilizador.

Agora vamos olhar para alguns padrões de uso comuns da sys_schema. Para começar, vamos agrupar os padrões de utilização em duas categorias: **Afinação de desempenho** e **manutenção da base de dados.**

## <a name="performance-tuning"></a>Otimização do desempenho

### <a name="sysuser_summary_by_file_io"></a>*sys.user_summary_by_file_io*

IO é a operação mais cara na base de dados. Podemos descobrir a latência média da IO consultando a *visão sys.user_summary_by_file_io.* Com o padrão de 125 GB de armazenamento a provisionado, a minha latência IO é de cerca de 15 segundos.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/io-latency-125GB.png" alt-text="pontos de vista sobre sys_schema":::

Como a Base de Dados Azure para o MySQL escala IO no que diz respeito ao armazenamento, depois de aumentar o meu armazenamento a provisionado para 1 TB, a minha latência IO reduz para 571 ms.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/io-latency-1TB.png" alt-text="pontos de vista sobre sys_schema":::

### <a name="sysschema_tables_with_full_table_scans"></a>*sys.schema_tables_with_full_table_scans*

Apesar de um planeamento cuidadoso, muitas consultas ainda podem resultar em exames de mesa completos. Para obter informações adicionais sobre os tipos de índices e como otimizá-los, pode consultar este artigo: [Como resolver problemas](./howto-troubleshoot-query-performance.md)no desempenho da consulta . As tomografias completas são intensivas em recursos e degradam o desempenho da sua base de dados. A maneira mais rápida de encontrar mesas com a tomografia completa é consultar a vista *sys.schema_tables_with_full_table_scans.*

:::image type="content" source="./media/howto-troubleshoot-sys-schema/full-table-scans.png" alt-text="pontos de vista sobre sys_schema":::

### <a name="sysuser_summary_by_statement_type"></a>*sys.user_summary_by_statement_type*

Para resolver problemas de desempenho na base de dados, pode ser benéfico identificar os eventos que ocorram dentro da sua base de dados, e usar a vista *sys.user_summary_by_statement_type* pode apenas fazer o truque.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/summary-by-statement.png" alt-text="pontos de vista sobre sys_schema":::

Neste exemplo, a Azure Database for MySQL passou 53 minutos a lavar o registo de consulta slog 44579 vezes. É muito tempo e muitos iOs. Pode reduzir esta atividade desativar o seu registo de consulta lenta ou diminuir a frequência do portal Azure de login de consulta lenta.

## <a name="database-maintenance"></a>Manutenção de bases de dados

### <a name="sysinnodb_buffer_stats_by_table"></a>*sys.innodb_buffer_stats_by_table*

[!IMPORTANT]
> Consultar esta vista pode ter impacto no desempenho. Recomenda-se a realização desta resolução de problemas durante as horas de trabalho fora do pico.

A piscina tampão InnoDB reside na memória e é o principal mecanismo de cache entre o DBMS e o armazenamento. O tamanho do conjunto de tampão InnoDB está ligado ao nível de desempenho e não pode ser alterado a menos que seja escolhido um produto diferente SKU. Tal como acontece com a memória no seu sistema operativo, as páginas antigas são trocadas para dar espaço a dados mais frescos. Para saber quais as tabelas que consomem a maior parte da memória do buffer pool InnoDB, pode consultar a *vista sys.innodb_buffer_stats_by_table.*

:::image type="content" source="./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png" alt-text="pontos de vista sobre sys_schema":::

No gráfico acima, é evidente que, para além das tabelas e vistas do sistema, cada tabela na base de dados mysqldatabase033, que acolhe um dos meus sites WordPress, ocupa 16 KB, ou 1 página, de dados na memória.

### <a name="sysschema_unused_indexes--sysschema_redundant_indexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Os índices são ótimas ferramentas para melhorar o desempenho da leitura, mas incorrem em custos adicionais para inserções e armazenamento. *Sys.schema_unused_indexes* e *sys.schema_redundant_indexes* fornecer informações sobre índices não reutilizados ou duplicados.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/unused-indexes.png" alt-text="pontos de vista sobre sys_schema":::

:::image type="content" source="./media/howto-troubleshoot-sys-schema/redundant-indexes.png" alt-text="pontos de vista sobre sys_schema":::

## <a name="conclusion"></a>Conclusão

Em resumo, o sys_schema é uma ótima ferramenta tanto para afinação de desempenho como para manutenção de bases de dados. Certifique-se de aproveitar esta funcionalidade na sua Base de Dados Azure para o MySQL. 

## <a name="next-steps"></a>Passos seguintes
- Para encontrar respostas de pares às suas perguntas mais preocupadas ou publicar uma nova pergunta/resposta, visite [o Microsoft Q&Uma página de perguntas](https://docs.microsoft.com/answers/topics/azure-database-mysql.html) ou Stack [Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
