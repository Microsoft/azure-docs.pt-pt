---
title: Operacional Store de consulta na base de dados SQL do Azure
description: Saiba como operar o Store de consulta na base de dados do Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 3ceb8569d952f2947870ce7314f869623b2d87f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584748"
---
# <a name="operating-the-query-store-in-azure-sql-database"></a>Operar o Store de consulta na base de dados SQL do Azure

Store de consulta no Azure é uma funcionalidade de base de dados totalmente gerido que recolhe e apresenta informações históricas detalhadas sobre todas as consultas de forma contínua. Pense sobre Store de consulta como sendo semelhante a dados gravador um avião que significativamente simplifica o desempenho das consultas de resolução de problemas para a cloud e os clientes locais. Este artigo explica os aspetos específicos de operar Store de consulta no Azure. Usando esta consulta previamente recolhidos dados, pode diagnosticar rapidamente e resolver problemas de desempenho e, portanto, passar mais tempo se concentrando em seu negócio. 

Consulta Store tem sido [globalmente disponível](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) na base de dados do Azure SQL desde Novembro de 2015. Store de consulta é a base para a análise de desempenho e funcionalidades de otimização, tal como [Assistente de base de dados SQL e o Dashboard de desempenho](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). No momento da publicação deste artigo, consulta Store está em execução em mais de 200 000 bases de dados do utilizador no Azure, a recolha de informações relacionadas com a consulta por vários meses, sem interrupção.

> [!IMPORTANT]
> A Microsoft está no processo de ativação Store de consulta para todas as bases de dados do SQL do Azure (novas e existentes). 

## <a name="optimal-query-store-configuration"></a>Configuração ideal Store de consulta

Esta secção descreve os padrões de configuração ideal que foram concebidos para garantir a fiabilidade do funcionamento das Store de consulta e dos recursos dependentes, tais como [Assistente de base de dados SQL e o Dashboard de desempenho](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Configuração predefinida está otimizada para a recolha de dados contínua, que é tempo mínimo gasto em OFF/READ_ONLY Estados.

| Configuração | Descrição | Predefinição | Comentário |
| --- | --- | --- | --- |
| MAX_STORAGE_SIZE_MB |Especifica o limite para o espaço de dados que Store de consulta pode demorar dentro do banco de dados do cliente |100 |Imposto para novas bases de dados |
| INTERVAL_LENGTH_MINUTES |Define o tamanho da janela de tempo durante o qual as estatísticas de tempo de execução coletados para planos de consulta são agregadas e persistentes. Todos os planos de consulta ativa tem uma linha, no máximo durante um período de tempo definido com esta configuração |60 |Imposto para novas bases de dados |
| STALE_QUERY_THRESHOLD_DAYS |Política de limpeza baseados no tempo que controla o período de retenção de estatísticas de tempo de execução persistentes e consultas Inativas |30 |Aplicados novas bases de dados e de bases de dados com o padrão anterior (367) |
| SIZE_BASED_CLEANUP_MODE |Especifica se a limpeza de dados automática ocorre quando o limite se aproxima do tamanho dos dados Store de consulta |AUTO |Imposta para todas as bases de dados |
| QUERY_CAPTURE_MODE |Especifica se a todas as consultas ou apenas um subconjunto de consultas são controladas |AUTO |Imposta para todas as bases de dados |
| FLUSH_INTERVAL_SECONDS |Especifica o período máximo durante o qual capturados estatísticas são mantidas na memória, antes de liberá-lo para o disco de tempo de execução |900 |Imposto para novas bases de dados |
|  | | | |

> [!IMPORTANT]
> Esses padrões são automaticamente aplicadas na fase final da ativação de consulta Store em todos os bancos de dados do SQL do Azure (consulte a observação importante de anterior). Depois deste aprimoramento, base de dados do Azure SQL não será alterada valores de configuração definidos por clientes, a menos que eles afetarem negativamente o carga de trabalho primária ou operações fiáveis do Store a consulta.

Se quiser manter-se com suas configurações personalizadas, utilize [ALTER DATABASE com opções de consulta Store](https://msdn.microsoft.com/library/bb522682.aspx) para reverter a configuração para o estado anterior. Confira [melhores práticas com a consulta Store](https://msdn.microsoft.com/library/mt604821.aspx) para saber como top optou por parâmetros de configuração ideal.

## <a name="next-steps"></a>Passos Seguintes

[Informações de desempenho da base de dados SQL](sql-database-performance.md)

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, veja os artigos seguintes:

- [Um gravador de dados da base de dados](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database)
- [A monitorização de desempenho utilizando a Store de consulta](https://msdn.microsoft.com/library/dn817826.aspx)
- [Cenários de Utilização do Arquivo de Consultas](https://msdn.microsoft.com/library/mt614796.aspx)
