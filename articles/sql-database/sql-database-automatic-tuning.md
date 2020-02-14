---
title: Descrição geral da otimização automática
description: A Base de Dados Azure SQL analisa a consulta do SQL e adapta-se automaticamente à carga de trabalho do utilizador.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/06/2019
ms.openlocfilehash: 34f102b43de669b5ea03324db47ac4dfcb554133
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190765"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Sintonização automática na Base de Dados Azure SQL

Base de Dados Azure SQL A finação automática proporciona desempenho máximo e cargas de trabalho estáveis através de afinação contínua de desempenho baseada em IA e machine learning.

A afinação automática é um serviço de desempenho inteligente totalmente gerido que utiliza inteligência incorporada para monitorizar continuamente as consultas executadas numa base de dados, e melhora automaticamente o seu desempenho. Isto é conseguido através da adaptação dinâmica da base de dados às mudanças de carga de trabalho e da aplicação de recomendações de afinação. A afinação automática aprende horizontalmente a partir de todas as bases de dados em Azure através da IA e melhora dinamicamente as suas ações de afinação. Quanto mais tempo uma Base de Dados Azure SQL funcionar com a finação automática, melhor se executa.

Base de Dados Azure SQL A sintonização automática pode ser uma das funcionalidades mais importantes que pode permitir fornecer cargas de trabalho de base de dados estáveis e de pico.

## <a name="what-can-automatic-tuning-do-for-you"></a>O que a afinação automática pode fazer por si?

- Afinação automatizada do desempenho das bases de dados Azure SQL
- Verificação automatizada dos ganhos de desempenho
- Retrocesso automatizado e auto-correcção
- História de afinação
- Roteiros T-SQL de ação de afinação para implementações manuais
- Monitorização proactiva do desempenho da carga de trabalho
- Aumentar a capacidade em centenas de milhares de bases de dados
- Impacto positivo nos recursos da DevOps e o custo total da propriedade

## <a name="safe-reliable-and-proven"></a>Seguro, fiável e comprovado

As operações de afinação aplicadas às bases de dados Azure SQL são totalmente seguras para o desempenho das suas cargas de trabalho mais intensas. O sistema foi concebido com cuidado para não interferir com as cargas de trabalho do utilizador. Recomendações de afinação automatizadas são aplicadas apenas em momentos de baixa utilização. O sistema também pode desativar temporariamente as operações de afinação automática para proteger o desempenho da carga de trabalho. Neste caso, a mensagem "Desativada pelo sistema" será mostrada no portal Azure. A finação automática diz respeito às cargas de trabalho com a maior prioridade de recursos.

Os mecanismos automáticos de afinação são maduros e foram aperfeiçoados em vários milhões de bases de dados em funcionamento no Azure. As operações de afinação automatizadas aplicadas são verificadas automaticamente para garantir que haja uma melhoria positiva do desempenho da carga de trabalho. As recomendações de desempenho regredidas são detetadas dinamicamente e revertem prontamente. Através do histórico de afinação registado, existe um traço claro de melhorias de afinação feitas em cada Base de Dados Azure SQL. 

![Como funciona a afinação automática](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Base de Dados Azure SQL A finação automática está a partilhar a sua lógica central com o motor de afinação automática SQL Server. Para obter informações técnicas adicionais sobre o mecanismo de inteligência incorporado, consulte a [afinação automática do SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

## <a name="use-automatic-tuning"></a>Utilizar afinação automática

A afinação automática tem de ser ativada na sua subscrição. Para permitir a afinação automática utilizando o portal Azure, consulte [A finação automática .](sql-database-automatic-tuning-enable.md)

A afinação automática pode funcionar de forma autónoma através da aplicação automática de recomendações de afinação, incluindo a verificação automatizada dos ganhos de desempenho. 

Para um maior controlo, a aplicação automática de recomendações de afinação pode ser desligada, e recomendações de afinação podem ser aplicadas manualmente através do portal Azure. Também é possível usar a solução para ver recomendações de afinação automatizadas apenas e aplicá-las manualmente através de scripts e ferramentas à sua escolha. 

Para uma visão geral de como funciona a afinação automática e para cenários de utilização típicos, consulte o vídeo incorporado:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>Opções de afinação automática

As opções automáticas de afinação disponíveis na Base de Dados Azure SQL são:

| Opção de afinação automática | Base de dados única e suporte de base de dados agréis | Suporte de base de dados de instâncias |
| :----------------------------- | ----- | ----- |
| **CREATE INDEX** - Identifica índices que podem melhorar o desempenho da sua carga de trabalho, cria índices e verifica automaticamente que o desempenho das consultas melhorou. | Sim | Não | 
| **ÍNDICE DE QUEDA** - Identifica índices redundantes e duplicados diariamente, com exceção de índices únicos, e índices que não foram utilizados durante muito tempo (>90 dias). Por favor, note que esta opção não é compatível com aplicações que usam comutação de divisórias e dicas de índice. A queda de índices não utilizados não é suportada para os níveis de serviço Premium e Business Critical. | Sim | Não |
| **PLANO DE FORÇA ÚLTIMO BOM** (correção automática do plano) - Identifica consultas SQL utilizando um plano de execução mais lento do que o plano bom anterior, e consultas usando o último plano bom conhecido em vez do plano regredido. | Sim | Sim |

A afinação automática identifica as recomendações create **INDEX,** **DROP INDEX,** e **FORCE LAST GOOD PLAN** que podem otimizar o desempenho da sua base de dados e mostre-as no portal [Azure,](sql-database-advisor-portal.md)e expõe-nas através da [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) e da [REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning). Para saber mais sobre o PLANO LAST FORCE E configurar opções de afinação automática através do T-SQL, consulte a [afinação automática que introduz](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/)a correção automática do plano .

Pode aplicar manualmente recomendações de afinação utilizando o portal ou pode permitir que a sintonização automática aplique recomendações de afinação para si. Os benefícios de deixar o sistema aplicar de forma autónoma recomendações de afinação para si é que ele automaticamente valida que existe um ganho positivo para o desempenho da carga de trabalho, e se não houver uma melhoria significativa de desempenho detetada, irá reverter automaticamente a recomendação de afinação. Por favor, note que em caso de consultas afetadas por recomendações de afinação que não são executadas frequentemente, a fase de validação pode demorar até 72 horas por design.

Caso esteja a aplicar recomendações de afinação através do T-SQL, a validação automática de desempenho e os mecanismos de inversão não estão disponíveis. As recomendações aplicadas desta forma permanecerão ativas e apresentadas na lista de recomendações de afinação para 24-48 horas. antes que o sistema os retire automaticamente. Se quiser remover uma recomendação mais cedo, pode descartá-la do portal Azure.

As opções de afinação automática podem ser ativadas ou desativadas de forma independente por base de dados, ou podem ser configuradas em servidores de Base de Dados SQL e aplicadas em todas as bases de dados que herdam as definições do servidor. Os servidores de base de dados SQL podem herdar as predefinições do Azure para definições de afinação automática. As predefinições do Azure neste momento estão definidas para FORCE_LAST_GOOD_PLAN está ativada, CREATE_INDEX está ativada e DROP_INDEX está desativada.

> [!IMPORTANT]
> A partir de março de 2020, as alterações às incumprimentos do Azure para a sintonização automática entrarão em vigor da seguinte forma:
> - Os novos incumprimentos do Azure serão FORCE_LAST_GOOD_PLAN = ativados, CREATE_INDEX = desativados e DROP_INDEX = desativados.
> - Os servidores existentes sem preferências de afinação automática configuradas serão automaticamente configurados com as novas falhas do Azure. Isto aplica-se a todos os clientes que têm atualmente a afinação automática num estado indefinido.
> - Os novos servidores criados serão automaticamente configurados com os novos predefinições do Azure (ao contrário do que aconteceu anteriormente quando a configuração de afinação automática estava em estado indefinido após a criação de novos servidores).
>

Configurar opções de afinação automática num servidor e herdar as definições para bases de dados pertencentes ao servidor dos pais é um método recomendado para configurar a afinação automática, uma vez que simplifica a gestão das opções de afinação automática para um grande número de bases de dados.

## <a name="next-steps"></a>Passos seguintes

- Para permitir a sintonização automática na Base de Dados Azure SQL para gerir a sua carga de trabalho, consulte [Afinação automática .](sql-database-automatic-tuning-enable.md)
- Para rever manualmente e aplicar recomendações de afinação automática, consulte [encontrar e aplicar recomendações](sql-database-advisor-portal.md)de desempenho .
- Para aprender a usar o T-SQL para aplicar e ver recomendações de afinação automática, consulte Gerir a [sintonização automática via T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/).
- Para conhecer a construção de notificações por e-mail para recomendações de afinação automática, consulte notificações de [email para afinação automática](sql-database-automatic-tuning-email-notifications.md).
- Para aprender sobre a inteligência incorporada usada na afinação automática, consulte as bases de [dados Azure SQL](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/)de Inteligência Artificial.
- Para saber como funciona a afinação automática na Base de Dados Azure SQL e no servidor SQL 2017, consulte a [afinação automática do SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).
