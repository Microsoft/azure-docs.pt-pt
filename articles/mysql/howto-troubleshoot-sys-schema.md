---
title: Utilize sys_schema - Base de Dados Azure para MySQL
description: Aprenda a usar sys_schema para encontrar problemas de desempenho e manter base de dados na Base de Dados Azure para mySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: a35a586a519ff78e8b32d986b92bd008b2c6b858
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067871"
---
# <a name="how-to-use-sys_schema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Como utilizar sys_schema para afinação de desempenho e manutenção de bases de dados na Base de Dados Azure para MySQL

O MySQL performance_schema, disponível pela primeira vez no MySQL 5.5, fornece instrumentação para muitos recursos vitais do servidor, tais como alocação de memória, programas armazenados, bloqueio de metadados, etc. No entanto, o performance_schema contém mais de 80 tabelas, e obter as informações necessárias muitas vezes requer juntar mesas dentro do performance_schema, bem como tabelas do information_schema. Com base tanto na performance_schema como na information_schema, o sys_schema fornece uma poderosa coleção de [visualizações fáceis](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) de utilizar numa base de dados só para leitura e está totalmente habilitado na Base de Dados Azure para a versão 5.7 do MySQL.

![vistas de sys_schema](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

Existem 52 pontos de vista no sys_schema, e cada vista tem um dos seguintes prefixos:

- Host_summary ou IO: Relações de lestabeleceções relacionadas com I/O.
- InnoDB: Estado do tampão innoDB e fechaduras.
- Memória: Utilização da memória pelo anfitrião e utilizadores.
- Schema: Informações relacionadas com schema, tais como incremento automático, índices, etc.
- Declaração: Informações sobre declarações sQL; pode ser uma declaração que resultou em digitalização completa da tabela, ou longo tempo de consulta.
- Utilizador: Recursos consumidos e agrupados pelos utilizadores. Exemplos são ficheiroS I/Os, ligações e memória.
- Atenção: Aguarde os eventos agrupados pelo anfitrião ou utilizador.

Agora vamos ver alguns padrões comuns de uso do sys_schema. Para começar, vamos agrupar os padrões de utilização em duas categorias: **afinação** de desempenho e manutenção da Base de **Dados.**

## <a name="performance-tuning"></a>Otimização do desempenho

### <a name="sysuser_summary_by_file_io"></a>*sys.user_summary_by_file_io*

A IO é a operação mais cara na base de dados. Podemos descobrir a latência média de IO consultando a visão *de sys.user_summary_by_file_io.* Com o padrão de 125 GB de armazenamento previsto, a minha latência de IO é de cerca de 15 segundos.

![io latência: 125 GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

Como a Base de Dados Azure para MySQL escala IO no que diz respeito ao armazenamento, depois de aumentar o meu armazenamento provisionado para 1 TB, a minha latência iO reduz para 571 ms.

![lo lo lncy: 1TB](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschema_tables_with_full_table_scans"></a>*sys.schema_tables_with_full_table_scans*

Apesar do planeamento cuidadoso, muitas consultas ainda podem resultar em exames de mesa completos. Para obter informações adicionais sobre os tipos de índices e como otimizá-los, pode consultar este artigo: Como resolver o desempenho da [consulta.](./howto-troubleshoot-query-performance.md) As análises completas à tabela são intensivas em recursos e degradam o desempenho da sua base de dados. A maneira mais rápida de encontrar mesas com digitalização de mesa completa é consultar as *sys.schema_tables_with_full_table_scans* vista.

![digitalizações de mesa completas](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysuser_summary_by_statement_type"></a>*sys.user_summary_by_statement_type*

Para resolver problemas de desempenho na base de dados, pode ser benéfico identificar os eventos que ocorram dentro da sua base de dados, e usar a visão *sys.user_summary_by_statement_type* pode apenas fazer o truque.

![resumo por declaração](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

Neste exemplo, a Base de Dados Azure para mySQL passou 53 minutos a descarregar o log de slog 44579 vezes. É muito tempo e muitos IOs. Pode reduzir esta atividade desativando o seu registo de consulta lenta ou diminuindo a frequência do portal Azure de consulta lenta.

## <a name="database-maintenance"></a>Manutenção de bases de dados

### <a name="sysinnodb_buffer_stats_by_table"></a>*sys.innodb_buffer_stats_by_table*

O tampão InnoDB reside na memória e é o principal mecanismo de cache entre o DBMS e o armazenamento. O tamanho do tampão InnoDB está ligado ao nível de desempenho e não pode ser alterado a menos que seja escolhido um Produto diferente SKU. Tal como acontece com a memória no seu sistema operativo, as páginas antigas são trocadas para dar espaço a dados mais frescos. Para descobrir quais as tabelas que consomem a maior parte da memória do pool tampão InnoDB, pode consultar a vista *sys.innodb_buffer_stats_by_table.*

![Estado do tampão innoDB](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

No gráfico acima, é evidente que, para além das tabelas e vistas do sistema, cada tabela na base de dados mysqldatabase033, que acolhe um dos meus sites WordPress, ocupa 16 KB, ou 1 página, de dados na memória.

### <a name="sysschema_unused_indexes--sysschema_redundant_indexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Os índices são ótimas ferramentas para melhorar o desempenho da leitura, mas incorrem em custos adicionais para inserções e armazenamento. *Sys.schema_unused_indexes* e *sys.schema_redundant_indexes* fornecer insights sobre índices não utilizados ou duplicados.

![índices não utilizados](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![índices redundantes](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>Conclusão

Em resumo, o sys_schema é uma excelente ferramenta tanto para a sintonização de desempenho como para a manutenção da base de dados. Certifique-se de aproveitar esta funcionalidade na sua Base de Dados Azure para MySQL. 

## <a name="next-steps"></a>Passos seguintes
- Para encontrar respostas dos pares às suas perguntas mais preocupadas ou publicar uma nova pergunta/resposta, visite o [fórum MSDN](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) ou [stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
