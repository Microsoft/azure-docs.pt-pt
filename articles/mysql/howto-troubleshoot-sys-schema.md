---
title: Usar o sys_schema para ajustar o desempenho e manter o banco de dados do Azure para MySQL
description: Saiba como usar o sys_schema para encontrar problemas de desempenho e manter o banco de dados no banco de dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.openlocfilehash: 7dc6b4744c74c56803127f63a8a6f29ca5a15090
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972781"
---
# <a name="how-to-use-sys_schema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Como usar o sys_schema para ajuste de desempenho e manutenção de banco de dados no banco de dados do Azure para MySQL

O MySQL performance_schema, primeiro disponível no MySQL 5,5, fornece instrumentação para muitos recursos de servidor vitais, como alocação de memória, programas armazenados, bloqueio de metadados, etc. No entanto, o performance_schema contém mais de 80 tabelas, e obter as informações necessárias geralmente requer a União de tabelas dentro do performance_schema, bem como tabelas do INFORMATION_SCHEMA. Com base em performance_schema e INFORMATION_SCHEMA, o sys_schema fornece uma poderosa coleção de [exibições amigáveis para o usuário](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) em um banco de dados somente leitura e é totalmente habilitado no banco de dados do Azure para MySQL versão 5,7.

![exibições de sys_schema](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

Há 52 exibições no sys_schema, e cada exibição tem um dos seguintes prefixos:

- Host_summary ou IO: Latências relacionadas a e/s.
- InnoDB: Status e bloqueios do buffer InnoDB.
- Memória: Uso de memória pelo host e pelos usuários.
- Esquema Informações relacionadas ao esquema, como incremento automático, índices, etc.
- Privacidade Informações sobre instruções SQL; pode ser uma instrução que resultou em verificação de tabela completa ou tempo de consulta longo.
- Usuário Recursos consumidos e agrupados por usuários. Os exemplos são e/SS de arquivos, conexões e memória.
- Esperado Eventos de espera agrupados por host ou usuário.

Agora, vejamos alguns padrões de uso comuns do sys_schema. Para começar, agruparemos os padrões de uso em duas categorias: **Ajuste de desempenho** e **manutenção de banco de dados**.

## <a name="performance-tuning"></a>Otimização do desempenho

### <a name="sysuser_summary_by_file_io"></a>*sys.user_summary_by_file_io*

IO é a operação mais cara no banco de dados. Podemos descobrir a latência média de e/s consultando a exibição *Sys. user_summary_by_file_io* . Com o padrão de 125 GB de armazenamento provisionado, minha latência de e/s é de cerca de 15 segundos.

![latência de e/s: 125 GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

Como o banco de dados do Azure para MySQL dimensiona a e/s com relação ao armazenamento, depois de aumentar o armazenamento provisionado para 1 TB, minha latência de e/s reduz para 571 MS.

![latência de e/s: 1TB](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschema_tables_with_full_table_scans"></a>*sys.schema_tables_with_full_table_scans*

Apesar do planejamento cuidadoso, muitas consultas ainda podem resultar em verificações de tabela completas. Para obter informações adicionais sobre os tipos de índices e como otimizá-los, você pode consultar este artigo: [Como solucionar problemas de desempenho de consulta](./howto-troubleshoot-query-performance.md). As verificações de tabela completas têm muitos recursos e prejudicam o desempenho do banco de dados. A maneira mais rápida de localizar tabelas com verificação de tabela completa é consultar a exibição *Sys. schema_tables_with_full_table_scans* .

![verificações de tabela completas](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysuser_summary_by_statement_type"></a>*sys.user_summary_by_statement_type*

Para solucionar problemas de desempenho do banco de dados, pode ser benéfico identificar os eventos que ocorrem dentro do banco de dados e usar a exibição *Sys. user_summary_by_statement_type* pode apenas fazer o truque.

![Resumo por instrução](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

Neste exemplo, o banco de dados do Azure para MySQL gastou 53 minutos liberando o log de consulta slog 44579 vezes. Isso é um longo tempo e muitos IOs. Você pode reduzir essa atividade desabilite o log de consultas lentas ou diminua a frequência de portal do Azure de logon de consulta lenta.

## <a name="database-maintenance"></a>Manutenção de banco de dados

### <a name="sysinnodb_buffer_stats_by_table"></a>*sys.innodb_buffer_stats_by_table*

O pool de buffers InnoDB reside na memória e é o mecanismo principal de cache entre o DBMS e o armazenamento. O tamanho do pool de buffers InnoDB é vinculado ao nível de desempenho e não pode ser alterado, a menos que uma SKU de produto diferente seja escolhida. Assim como acontece com a memória no seu sistema operacional, as páginas antigas são trocadas para liberar espaço para dados mais recentes. Para descobrir quais tabelas consomem a maior parte da memória do pool de buffers InnoDB, você pode consultar a exibição *Sys. innodb_buffer_stats_by_table* .

![Status do buffer InnoDB](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

No gráfico acima, é aparente que, além das tabelas e exibições do sistema, cada tabela no banco de dados mysqldatabase033, que hospeda um dos meus sites do WordPress, ocupa 16 KB ou uma página, de data na memória.

### <a name="sysschema_unused_indexes--sysschema_redundant_indexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Os índices são ótimas ferramentas para melhorar o desempenho de leitura, mas eles incorrem em custos adicionais para inserções e armazenamento. *Sys. schema_unused_indexes* e *Sys. schema_redundant_indexes* fornecem informações sobre índices não utilizados ou duplicados.

![índices não utilizados](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![índices redundantes](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>Conclusão

Em resumo, o sys_schema é uma excelente ferramenta para ajuste de desempenho e manutenção de banco de dados. Certifique-se de aproveitar esse recurso no banco de dados do Azure para MySQL. 

## <a name="next-steps"></a>Passos seguintes
- Para encontrar respostas de pares para suas perguntas mais interessadas ou postar uma nova pergunta/resposta, visite o [Fórum do MSDN](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) ou [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
