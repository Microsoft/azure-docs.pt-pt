---
title: Como uso sys_schema para ajuste de desempenho e manutenção de banco de dados na base de dados do Azure para MySQL
description: Este artigo descreve como utilizar sys_schema para detetar problemas de desempenho e manutenção de banco de dados na base de dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/01/2018
ms.openlocfilehash: 993c77056c09c1dc21d5317ddbfe8e937341718d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61422376"
---
# <a name="how-to-use-sysschema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Como utilizar sys_schema para manutenção de ajuste e a base de dados de desempenho na base de dados do Azure para MySQL

A MySQL performance_schema, primeiro disponíveis no MySQL 5.5, oferece a instrumentação para muitos recursos de servidor vitais, como a atribuição de memória, programas armazenados, metadados de bloqueio, etc. No entanto, o performance_schema contém mais de 80 tabelas e obter as informações necessárias, muitas vezes, requer a União de tabelas dentro da performance_schema, bem como tabelas a partir do information_schema. A criar em performance_schema e information_schema, o sys_schema fornece um eficiente conjunto de [modos de exibição amigáveis](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) numa base de dados só de leitura e ficar totalmente ativado na base de dados do Azure para MySQL versão 5.7.

![vistas de sys_schema](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

Existem 52 vistas na sys_schema e cada vista tem um dos seguintes prefixos:

- Host_summary ou e/s: E/s relacionados com latências.
- InnoDB: Estado da memória intermédia de InnoDB e bloqueios.
- Memória: Utilização da memória pelo anfitrião e os utilizadores.
- Esquema: Esquema-informações relacionadas com o incremento automático, índices, etc.
- Instrução: Informações sobre instruções SQL; pode ser declaração que resultaram numa análise da tabela completa ou longo tempo de consulta.
- Utilizador: Os recursos consumidos e agrupados por utilizadores. Os exemplos são ficheiros de e/s, ligações e memória.
- Espere: Aguarde eventos agrupados por host ou ao usuário.

Agora vamos examinar alguns padrões comuns de utilização do sys_schema. Para começar, podemos irá agrupar os padrões de utilização em duas categorias: **Ajuste de desempenho** e **manutenção de base de dados**.

## <a name="performance-tuning"></a>Otimização do desempenho

### <a name="sysusersummarybyfileio"></a>*sys.user_summary_by_file_io*

E/s é a operação mais cara na base de dados. Conseguimos perceber a latência de e/s média, consultando o *sys.user_summary_by_file_io* vista. Com o padrão de que 125 GB de armazenamento aprovisionado, meu latência de e/s é cerca de 15 segundos.

![Latência de e/s: 125 GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

Porque a base de dados do Azure para MySQL é dimensionado e/s em relação ao armazenamento, depois de aumentar o meu armazenamento aprovisionado a 1 TB, meu latência de e/s reduz a 571 ms.

![Latência de e/s: 1TB](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschematableswithfulltablescans"></a>*sys.schema_tables_with_full_table_scans*

Apesar de um planejamento cuidadoso, muitas consultas ainda podem fazer análises de tabela completa. Para obter mais informações sobre os tipos de índices e como otimizá-los, pode consultar este artigo: [Como resolver problemas de desempenho de consulta](./howto-troubleshoot-query-performance.md). Verificações de tabela completa são com muitos recursos e degradar o desempenho da sua base de dados. A forma mais rápida de localizar as tabelas com a análise da tabela completa é a consulta a *sys.schema_tables_with_full_table_scans* vista.

![verificações de tabela completa](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysusersummarybystatementtype"></a>*sys.user_summary_by_statement_type*

Para resolver problemas de desempenho da base de dados, talvez seja vantajoso identificar os eventos a acontecer dentro de sua base de dados e a utilizar o *sys.user_summary_by_statement_type* vista apenas pode ser suficiente.

![Resumo por instrução](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

Neste exemplo, base de dados do Azure para MySQL passou 53 minutos libere o log de consulta slog 44579 vezes. Isso é muito tempo e muitos IOs. Pode reduzir essa atividade por qualquer um dos desativar o registo de consulta lenta ou diminuir a frequência de início de sessão de consulta lenta portal do Azure.

## <a name="database-maintenance"></a>Manutenção de base de dados

### <a name="sysinnodbbufferstatsbytable"></a>*sys.innodb_buffer_stats_by_table*

O pool de buffers InnoDB reside na memória e é o mecanismo de cache principal entre o DBMS e armazenamento. O tamanho do conjunto de memória intermédia InnoDB está associado ao escalão de desempenho e não pode ser alterado, a menos que outro produto que SKU é escolhido. Tal como acontece com a memória no seu sistema operativo, páginas antigas são trocadas para disponibilizar espaço para dados atualizados. Para saber quais tabelas consomem a maior parte da memória de pool de buffer InnoDB, pode consultar o *sys.innodb_buffer_stats_by_table* vista.

![Estado da memória intermédia de InnoDB](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

No gráfico, é claro que que não tabelas de sistema e vistas, cada tabela na base de dados mysqldatabase033, que aloje um dos meus sites WordPress, ocupa 16 KB ou 1 página, de dados na memória.

### <a name="sysschemaunusedindexes--sysschemaredundantindexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Os índices são ferramentas excelentes para melhorar o desempenho de leitura, mas eles incorrem em custos adicionais para inserções e armazenamento. *Sys.schema_unused_indexes* e *sys.schema_redundant_indexes* apresentar informações sobre os índices não utilizados ou duplicados.

![índices não utilizados](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![índices redundantes](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>Conclusão

Em resumo, o sys_schema é uma ótima ferramenta para ambos os manutenção de ajuste e a base de dados de desempenho. Certifique-se tirar partido desta funcionalidade na sua base de dados do Azure para MySQL. 

## <a name="next-steps"></a>Passos Seguintes
- Para encontrar respostas de ponto a ponto às suas perguntas mais atento ou postar uma nova pergunta/resposta, visite [fórum MSDN](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL) ou [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
