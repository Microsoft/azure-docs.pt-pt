---
title: Descrição geral da otimização automática
description: Azure SQL Database e Azure SQL Managed Instance analisa a consulta SQL e adapta-se automaticamente à carga de trabalho do utilizador.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, sstein
ms.date: 03/30/2020
ms.openlocfilehash: 180f6e8902dc881c99a74a6491eeb3012bc03d0f
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675217"
---
# <a name="automatic-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Sintonização automática na Base de Dados Azure SQL e Azure SQL Gestão de Instância
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A afinação automática Azure SQL Database e Azure SQL Managed Instance proporciona o desempenho máximo e cargas de trabalho estáveis através de afinação contínua do desempenho com base na IA e machine learning.

O ajuste automático é um serviço de desempenho inteligente totalmente gerido que utiliza inteligência incorporada para monitorizar continuamente as consultas executadas numa base de dados e melhora automaticamente o desempenho das mesmas. Isto é conseguido através da adaptação dinâmica da base de dados às mudanças de carga de trabalho e da aplicação de recomendações de sintonização. A sintonização automática aprende horizontalmente a partir de todas as bases de dados em Azure através de IA e melhora dinamicamente as suas ações de afinação. Quanto mais tempo uma base de dados funcionar com sintonização automática, melhor funciona.

A afinação automática Azure SQL Database e Azure SQL Managed Instance pode ser uma das funcionalidades mais importantes que pode permitir fornecer cargas de trabalho de base de dados estáveis e de pico.

## <a name="what-can-automatic-tuning-do-for-you"></a>O que pode fazer a sintonização automática por si

- Afinação automatizada do desempenho das bases de dados
- Verificação automatizada dos ganhos de desempenho
- Reversão automatizada e auto-correcção
- História de afinação
- Ação de afinação Scripts Transact-SQL (T-SQL) para implementações manuais
- Monitorização proativa do desempenho da carga de trabalho
- Reduzir a capacidade em centenas de milhares de bases de dados
- Impacto positivo para os recursos da DevOps e o custo total de propriedade

## <a name="safe-reliable-and-proven"></a>Seguro, Fiável e Comprovado

As operações de afinação aplicadas às bases de dados na Base de Dados Azure SQL são totalmente seguras para o desempenho das suas cargas de trabalho mais intensas. O sistema foi concebido com o cuidado de não interferir com as cargas de trabalho do utilizador. As recomendações de afinação automatizada são aplicadas apenas nos momentos de baixa utilização. O sistema também pode desativar temporariamente as operações de afinação automática para proteger o desempenho da carga de trabalho. Neste caso, a mensagem "Desativado pelo sistema" será mostrada no portal Azure. A sintonização automática diz respeito a cargas de trabalho com a maior prioridade de recursos.

Os mecanismos de afinação automática são maduros e foram aperfeiçoados em vários milhões de bases de dados em execução em Azure. As operações automatizadas de afinação aplicadas são verificadas automaticamente para garantir uma melhoria positiva do desempenho da carga de trabalho. As recomendações de desempenho regressámos são detetadas dinamicamente e prontamente revertidas. Através do histórico de afinação registado, existe um claro traço de melhorias de afinação feitas em cada base de dados em Azure SQL Database e Azure SQL Managed Instance.

![Como funciona a afinação automática](./media/automatic-tuning-overview/how-does-automatic-tuning-work.png)

A afinação automática Azure SQL Database está a partilhar a sua lógica principal com a função de sintonização automática SQL Server no motor da base de dados. Para obter informações técnicas adicionais sobre o mecanismo de inteligência incorporado, consulte [a sintonização automática do SQL Server](/sql/relational-databases/automatic-tuning/automatic-tuning).

Para uma visão geral de como funciona a sintonização automática e para cenários típicos de utilização, consulte o vídeo incorporado:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>Ativar o ajuste automático

- [Ativa a sintonização automática da Base de Dados Azure SQL no portal Azure](automatic-tuning-enable.md) ou utilizando a declaração [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) T-SQL.
- Ativa a sintonização automática para Azure SQL Managed Instance utilizando a declaração [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current) T-SQL.

## <a name="automatic-tuning-options"></a>Opções de afinação automática

As opções de afinação automática disponíveis na Base de Dados Azure SQL e na Azure SQL Managed Instance são:

| Opção de afinação automática | Base de dados única e suporte de base de dados em conjunto | Suporte de base de dados de casos |
| :----------------------------- | ----- | ----- |
| **CREATE INDEX** - Identifica índices que podem melhorar o desempenho da sua carga de trabalho, cria índices e verifica automaticamente que o desempenho das consultas melhorou. | Sim | Não |
| **ÍNDICE DROP** - Identifica índices redundantes e duplicados diariamente, com exceção de índices únicos, e índices que não foram utilizados durante muito tempo (>90 dias). Por favor, note que esta opção não é compatível com aplicações usando comutação de partição e dicas de índice. A queda de índices não suportados não é suportada para os níveis de serviço Premium e Business Critical. | Sim | Não |
| **FORCE LAST GOOD PLAN** (correção automática do plano) - Identifica as consultas Azure SQL utilizando um plano de execução mais lento do que o plano anterior, e consultas utilizando o último bom plano conhecido em vez do plano regressaltado. | Sim | Sim |

### <a name="automatic-tuning-for-sql-database"></a>Sintonização automática para base de dados SQL

A sintonização automática para a Base de Dados Azure SQL utiliza as recomendações do consultor de base de dados **CREATE INDEX** , **DROP Index** e FORCE LAST **GOOD PLAN** para otimizar o desempenho da sua base de dados. Para mais informações, consulte [recomendações do advisor da Base de Dados no portal Azure](database-advisor-find-recommendations-portal.md), em [PowerShell,](/powershell/module/az.sql/get-azsqldatabaserecommendedaction)e na [API REST](/rest/api/sql/serverautomatictuning).

Pode aplicar manualmente recomendações de sintonização utilizando o portal Azure ou pode deixar que a sintonização automática aplique recomendações de sintonização automáticas para si. O benefício de deixar o sistema aplicar de forma autónoma recomendações de sintonização para si é que valida automaticamente que existe um ganho positivo no desempenho da carga de trabalho, e se não houver uma melhoria significativa do desempenho detetada, reverterá automaticamente a recomendação de sintonização. Por favor, note que em caso de consultas afetadas por recomendações de sintonização que não são executadas com frequência, a fase de validação pode demorar até 72 horas por design.

Caso esteja a aplicar recomendações de sintonização através do T-SQL, a validação automática do desempenho e os mecanismos de inversão não estão disponíveis. As recomendações aplicadas desta forma permanecerão ativas e apresentadas na lista de recomendações de sintonização para 24-48 horas. antes que o sistema os retire automaticamente. Se quiser remover uma recomendação mais cedo, pode descartá-la do portal Azure.

As opções de afinação automática podem ser ativadas de forma independente ou desativadas por base de dados, ou podem ser configuradas ao nível do servidor e aplicadas em todas as bases de dados que herdam as definições do servidor. Os servidores podem herdar as predefinições do Azure para configurações de sintonização automática. Os padrãos do Azure neste momento estão definidos para FORCE_LAST_GOOD_PLAN está ativado, CREATE_INDEX está ativado e DROP_INDEX está desativado.

> [!IMPORTANT]
> A partir de março de 2020, as alterações ao padrão do Azure para afinação automática entrarão em vigor da seguinte forma:
>
> - Os novos defeitos do Azure serão FORCE_LAST_GOOD_PLAN = ativados, CREATE_INDEX = desativados e DROP_INDEX = desativados.
> - Os servidores existentes sem preferências de sintonização automática configuradas serão automaticamente configurados para HER as novas predefinições do Azure. Isto aplica-se a todos os clientes que têm atualmente configurações de servidor para afinação automática num estado indefinido.
> - Os novos servidores criados serão automaticamente configurados para HERR as novas predefinições do Azure (ao contrário do que se verifica quando a configuração de sintonização automática estava num estado indefinido após a criação de novos servidores).

Configurar opções de afinação automática num servidor e herdar as definições para bases de dados pertencentes ao servidor principal é um método recomendado para configurar a sintonização automática, uma vez que simplifica a gestão das opções de afinação automática para um grande número de bases de dados.

Para saber mais sobre a construção de notificações por email para recomendações de afinação automática, consulte [notificações por e-mail para afinação automática](automatic-tuning-email-notifications-configure.md).

### <a name="automatic-tuning-for-azure-sql-managed-instance"></a>Sintonização automática para Azure SQL Caso Gerido

A sintonização automática para SQL Managed Instance só suporta **FORCE LAST GOOD PLAN** . Para obter mais informações sobre a configuração das opções de afinação automática através do T-SQL, consulte [a sintonização automática introduz a correção automática do plano](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) e a correção automática do [plano](/sql/relational-databases/automatic-tuning/automatic-tuning?view=sql-server-ver15#automatic-plan-correction).

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a inteligência incorporada utilizada na afinação automática, consulte a [Inteligência Artificial tunes Azure SQL Database](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Para saber como funciona a sintonização automática sob o capot, consulte [indexar automaticamente milhões de bases de dados na Base de Dados SQL do Microsoft Azure](https://www.microsoft.com/research/uploads/prod/2019/02/autoindexing_azuredb.pdf).