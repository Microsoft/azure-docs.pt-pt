---
title: Operar Repositório de Consultas
description: Saiba como operar o Repositório de Consultas no banco de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: jrasnik, carlrab
ms.date: 12/19/2018
ms.openlocfilehash: fa60992c85e69143bfd65cc1a1f420ed85c8fd93
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802759"
---
# <a name="operating-the-query-store-in-azure-sql-database"></a>Operando o Repositório de Consultas no banco de dados SQL do Azure

Repositório de Consultas no Azure é um recurso de banco de dados totalmente gerenciado que coleta continuamente e apresenta informações históricas detalhadas sobre todas as consultas. Você pode pensar em Repositório de Consultas de forma semelhante ao gravador de dados de voo de um avião que simplifica significativamente a solução de problemas de desempenho de consultas para clientes locais e na nuvem. Este artigo explica os aspectos específicos do Repositório de Consultas operacional no Azure. Usando esses dados de consulta previamente coletados, você pode diagnosticar e resolver problemas de desempenho rapidamente e, assim, passar mais tempo concentrando-se em seus negócios. 

O Repositório de Consultas foi [disponibilizado globalmente](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) no banco de dados SQL do Azure desde novembro de 2015. Repositório de Consultas é a base para a análise de desempenho e recursos de ajuste, como [Assistente do banco de dados SQL e painel de desempenho](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). No momento da publicação deste artigo, Repositório de Consultas está em execução em mais de 200.000 bancos de dados de usuário no Azure, coletando informações relacionadas a consultas por vários meses, sem interrupções.

> [!IMPORTANT]
> A Microsoft está no processo de ativação de Repositório de Consultas para todos os bancos de dados SQL do Azure (existentes e novos). 

## <a name="optimal-query-store-configuration"></a>Configuração de Repositório de Consultas ideal

Esta seção descreve os padrões de configuração ideais que são criados para garantir uma operação confiável do Repositório de Consultas e dos recursos dependentes, como o [painel de assistente do banco de dados SQL e de desempenho](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). A configuração padrão é otimizada para coleta de dados contínua, que é o tempo mínimo gasto nos Estados desligado/READ_ONLY.

| Configuração | Descrição | Predefinição | Comentário |
| --- | --- | --- | --- |
| MAX_STORAGE_SIZE_MB |Especifica o limite para o espaço de dados que Repositório de Consultas pode assumir dentro do banco de dado do cliente |100 |Imposto para novos bancos de dados |
| INTERVAL_LENGTH_MINUTES |Define o tamanho da janela de tempo durante o qual as estatísticas de tempo de execução coletadas para planos de consulta são agregadas e mantidas. Cada plano de consulta ativo tem no máximo uma linha por um período de tempo definido com essa configuração |60 |Imposto para novos bancos de dados |
| STALE_QUERY_THRESHOLD_DAYS |Política de limpeza baseada em tempo que controla o período de retenção de estatísticas de tempo de execução persistentes e consultas inativas |30 |Imposto para novos bancos de dados e bancos de dados com o padrão anterior (367) |
| SIZE_BASED_CLEANUP_MODE |Especifica se a limpeza automática de dados ocorre quando Repositório de Consultas o tamanho dos dados se aproxima do limite |Automático |Imposto para todos os bancos de dados |
| QUERY_CAPTURE_MODE |Especifica se todas as consultas ou apenas um subconjunto de consultas são controladas |Automático |Imposto para todos os bancos de dados |
| FLUSH_INTERVAL_SECONDS |Especifica o período máximo durante o qual as estatísticas de tempo de execução capturadas são mantidas na memória, antes da liberação para o disco |900 |Imposto para novos bancos de dados |
|  | | | |

> [!IMPORTANT]
> Esses padrões são aplicados automaticamente no estágio final da ativação de Repositório de Consultas em todos os bancos de dados SQL do Azure (consulte a observação importante anterior). Após essa iluminação, o banco de dados SQL do Azure não alterará os valores de configuração definidos pelos clientes, a menos que eles afetem negativamente a carga de trabalho primária ou as operações confiáveis do Repositório de Consultas.

Se você quiser manter suas configurações personalizadas, use [ALTER DATABASE com repositório de consultas opções](https://msdn.microsoft.com/library/bb522682.aspx) para reverter a configuração para o estado anterior. Confira [as práticas recomendadas com o repositório de consultas](https://msdn.microsoft.com/library/mt604821.aspx) para saber como o topo escolheu os parâmetros de configuração ideais.

## <a name="next-steps"></a>Passos seguintes

[Insight de desempenho do banco de dados SQL](sql-database-performance.md)

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, confira os seguintes artigos:

- [Um gravador de dados de vôo para seu banco de dado](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database)
- [Monitorando o desempenho usando o Repositório de Consultas](https://msdn.microsoft.com/library/dn817826.aspx)
- [Cenários de Utilização do Arquivo de Consultas](https://msdn.microsoft.com/library/mt614796.aspx)
