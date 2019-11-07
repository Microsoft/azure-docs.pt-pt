---
title: Banco de dados SQL do Azure-ajuste automático
description: O banco de dados SQL do Azure analisa a consulta SQL e se adapta automaticamente à carga de trabalho do usuário.
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
ms.openlocfilehash: bfac5a0eba68469d912efd02699624e1335e40e5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691106"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Ajuste automático no banco de dados SQL do Azure

O ajuste automático do banco de dados SQL do Azure fornece desempenho de pico e cargas de trabalho estáveis por meio do ajuste de desempenho contínuo com base em ia e Machine Learning.

O ajuste automático é um serviço de desempenho inteligente totalmente gerenciado que usa inteligência interna para monitorar continuamente as consultas executadas em um banco de dados e melhora automaticamente seu desempenho. Isso é obtido por meio da adaptação dinâmica do banco de dados para as cargas de trabalho em alteração e a aplicação de recomendações de ajuste. O ajuste automático aprende horizontalmente de todos os bancos de dados no Azure por meio do ia e aumenta dinamicamente suas ações de ajuste. Quanto mais tempo um banco de dados SQL do Azure é executado com o ajuste automático em, melhor é o desempenho.

O ajuste automático do banco de dados SQL do Azure pode ser um dos recursos mais importantes que você pode habilitar para fornecer cargas de trabalho de banco de dados de desempenho estável e de pico.

## <a name="what-can-automatic-tuning-do-for-you"></a>O que o ajuste automático pode fazer para você?

- Ajuste de desempenho automatizado de bancos de dados SQL do Azure
- Verificação automatizada de ganhos de desempenho
- Reversão automatizada e AutoCorreção
- Histórico de ajuste
- Como ajustar scripts T-SQL de ação para implantações manuais
- Monitoramento de desempenho de carga de trabalho proativa
- Capacidade de expansão em centenas de milhares de bancos de dados
- Impacto positivo nos recursos DevOps e no custo total de propriedade

## <a name="safe-reliable-and-proven"></a>Seguro, confiável e comprovado

As operações de ajuste aplicadas aos bancos de dados SQL do Azure são totalmente seguras para o desempenho de suas cargas de trabalho mais intensa. O sistema foi projetado com cuidado para não interferir nas cargas de trabalho do usuário. As recomendações de ajuste automatizado são aplicadas somente nos horários de uma baixa utilização. O sistema também pode desabilitar temporariamente as operações de ajuste automático para proteger o desempenho da carga de trabalho. Nesse caso, a mensagem "desabilitada pelo sistema" será mostrada na portal do Azure. O ajuste automático considera as cargas de trabalho com a prioridade de recursos mais alta.

Os mecanismos de ajuste automático são maduros e foram aperfeiçoados em vários milhões de bancos de dados em execução no Azure. As operações de ajuste automatizado aplicadas são verificadas automaticamente para garantir que haja uma melhoria positiva no desempenho da carga de trabalho. As recomendações de desempenho retornadas são detectadas dinamicamente e revertidas imediatamente. Por meio do histórico de ajuste registrado, existe um rastreamento claro dos aprimoramentos de ajuste feitos em cada banco de dados SQL do Azure. 

![Como funciona o ajuste automático](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

O ajuste automático do banco de dados SQL do Azure está compartilhando sua lógica principal com o SQL Server mecanismo de ajuste automático. Para obter informações técnicas adicionais sobre o mecanismo de inteligência interno, consulte [SQL Server ajuste automático](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

## <a name="use-automatic-tuning"></a>Usar ajuste automático

O ajuste automático precisa ser habilitado em sua assinatura. Para habilitar o ajuste automático usando portal do Azure, consulte [habilitar o ajuste automático](sql-database-automatic-tuning-enable.md).

O ajuste automático pode operar de forma autônoma por meio da aplicação automática de recomendações de ajuste, incluindo a verificação automatizada de ganhos de desempenho. 

Para obter mais controle, a aplicação automática de recomendações de ajuste pode ser desativada e as recomendações de ajuste podem ser aplicadas manualmente por meio de portal do Azure. Também é possível usar a solução para exibir apenas as recomendações de ajuste automatizado e aplicá-las manualmente por meio de scripts e ferramentas de sua escolha. 

Para obter uma visão geral de como o ajuste automático funciona e para cenários de uso típicos, consulte o vídeo incorporado:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>Opções de ajuste automático

As opções de ajuste automático disponíveis no banco de dados SQL do Azure são:

| Opção de ajuste automático | Banco de dados individual e suporte a banco de dados em pool | Suporte a banco de dados de instância |
| :----------------------------- | ----- | ----- |
| **Criar índice** – identifica índices que podem melhorar o desempenho de sua carga de trabalho, cria índices e verifica automaticamente se o desempenho das consultas foi melhorado. | Sim | Não | 
| **Drop index** -identifica índices redundantes e duplicados diariamente, exceto índices exclusivos, e índices que não foram usados por um longo tempo (> 90 dias). Observe que essa opção não é compatível com aplicativos que usam a alternância de partição e dicas de índice. Não há suporte para a remoção de índices não utilizados para as camadas de serviço Premium e Comercialmente Crítico. | Sim | Não |
| **Forçar último plano bom** (correção de plano automática) – identifica consultas SQL usando o plano de execução que é mais lento do que o bom plano anterior e consultas usando o último plano bom conhecido em vez do plano regressivo. | Sim | Sim |

O ajuste automático identifica as recomendações **criar índice**, **drop index**e **forçar o último plano** que pode otimizar o desempenho do banco de dados e os mostra no [portal do Azure](sql-database-advisor-portal.md)e os expõe por meio [de T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) e [ API REST](https://docs.microsoft.com/rest/api/sql/serverautomatictuning). Para saber mais sobre o último bom plano e a configuração das opções de ajuste automático por meio do T-SQL, consulte o [ajuste automático introduz a correção automática do plano](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/).

Você pode aplicar manualmente as recomendações de ajuste usando o portal ou pode permitir que o ajuste automático aplique recomendações de ajuste de forma autônoma para você. Os benefícios de permitir que o sistema aplique as recomendações de ajuste de forma autônoma para você é que ele valida automaticamente que existe um lucro positivo para o desempenho da carga de trabalho e, se não houver uma melhoria significativa de desempenho detectada, ela será reverter automaticamente a recomendação de ajuste. Observe que, no caso de consultas afetadas por recomendações de ajuste que não são executadas com frequência, a fase de validação pode levar até 72 horas por design.

Caso você esteja aplicando recomendações de ajuste por meio do T-SQL, os mecanismos validação de desempenho automático e reversão não estão disponíveis. As recomendações aplicadas de forma que permanecerão ativas e mostradas na lista de recomendações de ajuste para 24-48 horas. antes que o sistema as retire automaticamente. Se você quiser remover uma recomendação mais cedo, poderá descartá-la de portal do Azure.

As opções de ajuste automático podem ser habilitadas ou desabilitadas independentemente por banco de dados, ou podem ser configuradas em servidores de banco de dados SQL e aplicadas em cada banco de dados que herda as configurações do servidor. Os servidores de banco de dados SQL podem herdar os padrões do Azure para configurações de ajuste automático. No momento, os padrões do Azure são definidos como FORCE_LAST_GOOD_PLAN está habilitado, CREATE_INDEX está habilitado e DROP_INDEX está desabilitado.

Configurar opções de ajuste automático em um servidor e herdar configurações para bancos de dados pertencentes ao servidor pai é um método recomendado para configurar o ajuste automático, pois simplifica o gerenciamento de opções de ajuste automático para um grande número de bancos de dados.

## <a name="next-steps"></a>Passos seguintes

- Para habilitar o ajuste automático no banco de dados SQL do Azure para gerenciar sua carga de trabalho, consulte [habilitar o ajuste automático](sql-database-automatic-tuning-enable.md).
- Para examinar e aplicar manualmente as recomendações de ajuste automático, consulte [Localizar e aplicar recomendações de desempenho](sql-database-advisor-portal.md).
- Para saber como usar o T-SQL para aplicar e exibir as recomendações de ajuste automático, consulte [gerenciar o ajuste automático por meio do T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/).
- Para saber mais sobre como criar notificações por email para recomendações de ajuste automático, consulte [notificações por email para ajuste automático](sql-database-automatic-tuning-email-notifications.md).
- Para saber mais sobre a inteligência interna usada no ajuste automático, consulte [inteligência artificial ajusta os bancos de dados SQL do Azure](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Para saber mais sobre como o ajuste automático funciona no banco de dados SQL do Azure e no SQL Server 2017, consulte [SQL Server ajuste automático](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).
