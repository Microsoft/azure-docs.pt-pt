---
title: Descrição geral da otimização automática
description: A Base de Dados Azure SQL e a Instância Gerida Azure SQL analisam a consulta SQL e adaptam-se automaticamente à carga de trabalho do utilizador.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/30/2020
ms.openlocfilehash: c29452929e350c3a1ff6059c2beb9fd4281ded03
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84043633"
---
# <a name="automatic-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Sintonização automática na Base de Dados Azure SQL e na instância gerida azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A base de dados Azure SQL e a afinação automática Azure SQL Managed Instance proporcionam desempenho máximo e cargas de trabalho estáveis através de afinação contínua de desempenho baseada em IA e machine learning.

A afinação automática é um serviço de desempenho inteligente totalmente gerido que utiliza inteligência incorporada para monitorizar continuamente as consultas executadas numa base de dados, e melhora automaticamente o seu desempenho. Isto é conseguido através da adaptação dinâmica da base de dados às mudanças de carga de trabalho e da aplicação de recomendações de afinação. A afinação automática aprende horizontalmente a partir de todas as bases de dados em Azure através da IA e melhora dinamicamente as suas ações de afinação. Quanto mais tempo uma base de dados funcionar com a finação automática, melhor se executa.

A base de dados Azure SQL e a sintonização automática Azure SQL Pode ser uma das funcionalidades mais importantes que pode permitir fornecer cargas de trabalho de base de dados estáveis e de pico.

## <a name="what-can-automatic-tuning-do-for-you"></a>O que a finação automática pode fazer por si

- Afinação automatizada do desempenho das bases de dados
- Verificação automatizada dos ganhos de desempenho
- Retrocesso automatizado e auto-correcção
- História de afinação
- Roteiros T-SQL de ação de afinação para implementações manuais
- Monitorização proactiva do desempenho da carga de trabalho
- Aumentar a capacidade em centenas de milhares de bases de dados
- Impacto positivo nos recursos da DevOps e o custo total da propriedade

## <a name="safe-reliable-and-proven"></a>Seguro, fiável e comprovado

As operações de afinação aplicadas às bases de dados da Base de Dados Azure SQL são totalmente seguras para o desempenho das suas cargas de trabalho mais intensas. O sistema foi concebido com cuidado para não interferir com as cargas de trabalho do utilizador. Recomendações de afinação automatizadas são aplicadas apenas em momentos de baixa utilização. O sistema também pode desativar temporariamente as operações de afinação automática para proteger o desempenho da carga de trabalho. Neste caso, a mensagem "Desativada pelo sistema" será mostrada no portal Azure. A finação automática diz respeito às cargas de trabalho com a maior prioridade de recursos.

Os mecanismos automáticos de afinação são maduros e foram aperfeiçoados em vários milhões de bases de dados em funcionamento no Azure. As operações de afinação automatizadas aplicadas são verificadas automaticamente para garantir que haja uma melhoria positiva do desempenho da carga de trabalho. As recomendações de desempenho regredidas são detetadas dinamicamente e revertem prontamente. Através do histórico de afinação registado, existe um traço claro de melhorias de afinação feitas em cada Base de Dados Azure SQL e Pôr SQL Managed Instance .

![Como funciona a afinação automática](./media/automatic-tuning-overview/how-does-automatic-tuning-work.png)

A afinação automática da Base de Dados Azure SQL está a partilhar a sua lógica central com a funcionalidade de afinação automática do SQL Server no motor de base de dados. Para obter informações técnicas adicionais sobre o mecanismo de inteligência incorporado, consulte a [afinação automática do SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

Para uma visão geral de como funciona a afinação automática e para cenários de utilização típicos, consulte o vídeo incorporado:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>Ativar o ajuste automático

- Ativa a sintonização automática para a Base de [Dados Azure SQL no portal Azure](automatic-tuning-enable.md) ou utilizando a declaração Alter [DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) T-SQL.
- Ativa a sintonização automática para a Instância Gerida Azure SQL utilizando a declaração Alter [DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current) T-SQL.

## <a name="automatic-tuning-options"></a>Opções de afinação automática

As opções de afinação automática disponíveis na Base de Dados Azure SQL e na Instância Gerida Azure SQL são:

| Opção de afinação automática | Base de dados única e suporte de base de dados agréis | Suporte de base de dados de instâncias |
| :----------------------------- | ----- | ----- |
| **CREATE INDEX** - Identifica índices que podem melhorar o desempenho da sua carga de trabalho, cria índices e verifica automaticamente que o desempenho das consultas melhorou. | Sim | Não |
| **ÍNDICE DE QUEDA** - Identifica índices redundantes e duplicados diariamente, com exceção de índices únicos, e índices que não foram utilizados durante muito tempo (>90 dias). Por favor, note que esta opção não é compatível com aplicações que usam comutação de divisórias e dicas de índice. A queda de índices não utilizados não é suportada para os níveis de serviço Premium e Business Critical. | Sim | Não |
| **PLANO DE FORÇA ÚLTIMO BOM** (correção automática do plano) - Identifica consultas SQL utilizando um plano de execução mais lento do que o plano bom anterior, e consultas usando o último plano bom conhecido em vez do plano regredido. | Sim | Sim |

### <a name="automatic-tuning-for-azure-sql-databases"></a>Sintonização automática para bases de dados Azure SQL

A sintonização automática para a Base de Dados Azure SQL utiliza o **ÍNDICE DE CRIAÇÃO,** **ÍNDICE DE QUEDA**e RECOMENDAÇÕES de assessoria de dados **FORCE LAST GOOD PLAN** para otimizar o desempenho da sua base de dados. Para mais informações, consulte [as recomendações do consultor da Base](database-advisor-find-recommendations-portal.md)de Dados no portal Azure , no [PowerShell,](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction)e na [REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

Pode aplicar manualmente recomendações de afinação utilizando o portal Azure ou pode permitir que a sintonização automática aplique recomendações de afinação para si. Os benefícios de permitir que o sistema aplique autonomamente recomendações de afinação para si é que ele valida automaticamente que existe um ganho positivo para o desempenho da carga de trabalho, e se não houver uma melhoria significativa de desempenho detetada, reverterá automaticamente a recomendação de afinação. Por favor, note que em caso de consultas afetadas por recomendações de afinação que não são executadas frequentemente, a fase de validação pode demorar até 72 horas por design.

Caso esteja a aplicar recomendações de afinação através do T-SQL, a validação automática de desempenho e os mecanismos de inversão não estão disponíveis. As recomendações aplicadas desta forma permanecerão ativas e apresentadas na lista de recomendações de afinação para 24-48 horas. antes que o sistema os retire automaticamente. Se quiser remover uma recomendação mais cedo, pode descartá-la do portal Azure.

As opções de afinação automática podem ser ativadas ou desativadas de forma independente por base de dados, ou podem ser configuradas ao nível do servidor e aplicadas em todas as bases de dados que herdam as definições do servidor. Os servidores podem herdar as predefinições do Azure para definições automáticas de afinação. As predefinições do Azure neste momento estão definidas para FORCE_LAST_GOOD_PLAN está ativada, CREATE_INDEX está ativada e DROP_INDEX está desativada.

> [!IMPORTANT]
> A partir de março de 2020, as alterações às incumprimentos do Azure para a sintonização automática entrarão em vigor da seguinte forma:
>
> - Os novos incumprimentos do Azure serão FORCE_LAST_GOOD_PLAN = ativados, CREATE_INDEX = desativados e DROP_INDEX = desativados.
> - Os servidores existentes sem preferências de afinação automática configuradas serão automaticamente configurados para HERDar as novas falhas do Azure. Isto aplica-se a todos os clientes que atualmente têm definições de servidor para afinação automática num estado indefinido.
> - Os novos servidores criados serão automaticamente configurados para HERDar os novos incumprimentos do Azure (ao contrário do que aconteceu anteriormente quando a configuração de afinação automática estava em estado indefinido após a criação de novos servidores).

Configurar opções de afinação automática num servidor e herdar as definições para bases de dados pertencentes ao servidor dos pais é um método recomendado para configurar a afinação automática, uma vez que simplifica a gestão das opções de afinação automática para um grande número de bases de dados.

Para conhecer a construção de notificações por e-mail para recomendações de afinação automática, consulte notificações de [e-mail para afinação automática](automatic-tuning-email-notifications-configure.md).

### <a name="automatic-tuning-for-azure-sql-managed-database"></a>Afinação automática para base de dados gerida azure SQL

A sintonização automática para base de dados gerida Azure SQL apenas suporta **FORÇA ÚLTIMA BOA PLANA**. Para obter mais informações sobre a configuração de opções de afinação automática através do T-SQL, consulte a [afinação automática introduz](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) a correção automática do plano e a [correção automática](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning?view=sql-server-ver15#automatic-plan-correction)do plano .

## <a name="next-steps"></a>Próximos passos

- Para aprender sobre a inteligência incorporada usada na afinação automática, consulte a Base de [Dados Azure SQL](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/)de Inteligência Artificial.
- Para saber como funciona a afinação automática sob o capot, consulte [automaticamente a indexação automática de milhões de bases de dados na Base de Dados SQL](https://www.microsoft.com/research/uploads/prod/2019/02/autoindexing_azuredb.pdf)do Microsoft Azure .
