---
title: Habilitar o ajuste automático para o banco de dados SQL do Azure | Microsoft Docs
description: Você pode habilitar o ajuste automático no banco de dados SQL do Azure com facilidade.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: 457ee34daf368150a8703ea32a39b2350d654523
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569420"
---
# <a name="enable-automatic-tuning-to-monitor-queries-and-improve-workload-performance"></a>Habilitar o ajuste automático para monitorar consultas e melhorar o desempenho da carga de trabalho

O Azure SQL Database é um serviço de dados gerenciado automaticamente que monitora constantemente suas consultas e identifica a ação que você pode executar para melhorar o desempenho da carga de trabalho. Você pode revisar as recomendações e aplicá-las manualmente, ou deixar que o banco de dados SQL do Azure aplique automaticamente as ações corretivas – isso é conhecido como **modo de ajuste automático**.

O ajuste automático pode ser habilitado no nível do servidor ou do banco de dados por meio do [portal do Azure](sql-database-automatic-tuning-enable.md#azure-portal), chamadas à [API REST](sql-database-automatic-tuning-enable.md#rest-api) e comandos [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) .

> [!NOTE]
> Por Instância Gerenciada, a opção com suporte FORCE_LAST_GOOD_PLAN pode ser configurada somente por meio [de T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) . A configuração baseada em portal e as opções de ajuste automático de índice descritas neste artigo não se aplicam a Instância Gerenciada.

> [!NOTE]
> Não há suporte para a configuração de opções de ajuste automático por meio do modelo ARM (Azure Resource Manager) no momento.

## <a name="enable-automatic-tuning-on-server"></a>Habilitar ajuste automático no servidor

No nível do servidor, você pode optar por herdar a configuração de ajuste automático de "padrões do Azure" ou não para herdar a configuração. Os padrões do Azure são FORCE_LAST_GOOD_PLAN habilitados, o CREATE_INDEX está habilitado e o DROP_INDEX está desabilitado.

### <a name="azure-portal"></a>Portal do Azure

Para habilitar o ajuste automático no **servidor**lógico do banco de dados SQL do Azure, navegue até o servidor em portal do Azure e, em seguida, selecione **ajuste automático** no menu.

![Servidor](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Observe que, no momento, a opção **DROP_INDEX** não é compatível com aplicativos que usam a alternância de partição e dicas de índice e não deve ser habilitada nesses casos.
>

Selecione as opções de ajuste automático que você deseja habilitar e selecione **aplicar**.

As opções de ajuste automático em um servidor são aplicadas a todos os bancos de dados neste servidor. Por padrão, todos os bancos de dados herdam a configuração de seu servidor pai, mas isso pode ser substituído e especificado para cada banco de cada individualmente.

### <a name="rest-api"></a>API REST

Saiba mais sobre como usar a API REST para habilitar o ajuste automático em um servidor, consulte [SQL Server atualização de ajuste automático e obter métodos http](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Habilitar o ajuste automático em um banco de dados individual

O banco de dados SQL do Azure permite especificar individualmente a configuração de ajuste automático para cada banco de dados. No nível de banco de dados, você pode optar por herdar a configuração de ajuste automático do servidor pai, "padrões do Azure" ou não para herdar a configuração. Os padrões do Azure são definidos como FORCE_LAST_GOOD_PLAN está habilitado, CREATE_INDEX está habilitado e DROP_INDEX está desabilitado.

> [!TIP]
> A recomendação geral é gerenciar a configuração de ajuste automático no **nível do servidor** para que as mesmas definições de configuração possam ser aplicadas automaticamente em cada banco de dados. Configure o ajuste automático em um banco de dados individual somente se você precisar que esse banco de dados tenha configurações diferentes das outras que herdam as configurações do mesmo servidor.
>

### <a name="azure-portal"></a>Portal do Azure

Para habilitar o ajuste automático em um **único banco de dados**, navegue até o banco de dados em portal do Azure e selecione **ajuste automático**.

As configurações individuais de ajuste automático podem ser configuradas separadamente para cada banco de dados. Você pode configurar manualmente uma opção de ajuste automático individual ou especificar que uma opção herde suas configurações do servidor.

![Base de Dados](./media/sql-database-automatic-tuning-enable/database.png)

Observe que, no momento, a opção DROP_INDEX não é compatível com aplicativos que usam a alternância de partição e dicas de índice e não deve ser habilitada nesses casos.

Depois de selecionar a configuração desejada, clique em **aplicar**.

### <a name="rest-api"></a>API REST

Saiba mais sobre como usar a API REST para habilitar o ajuste automático em um único banco de dados, consulte [atualização de ajuste automático do banco de dados SQL e obter métodos http](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Para habilitar o ajuste automático em um único banco de dados por meio de T-SQL, conecte-se ao banco de dados e execute a seguinte consulta:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

Definir o ajuste automático como AUTO aplicará os padrões do Azure. Definindo-o para HERDAr, a configuração de ajuste automático será herdada do servidor pai. Escolhendo personalizado, você precisará configurar manualmente o ajuste automático.

Para configurar opções de ajuste automático individuais via T-SQL, conecte-se ao banco de dados e execute a consulta como esta:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
```

Definir a opção de ajuste individual como ON substituirá qualquer configuração que o banco de dados herdou e habilitará a opção de ajuste. Configurá-lo como OFF, também substituirá qualquer configuração herdada pelo banco de dados e desabilitará a opção de ajuste. A opção de ajuste automático, para a qual o padrão é especificado, herdará a configuração da configuração de ajuste automático no nível do banco de dados.  

> [!IMPORTANT]
> No caso da [replicação geográfica ativa](sql-database-auto-failover-group.md), o ajuste automático precisa ser configurado somente no banco de dados primário. Automaticamente as ações de ajuste aplicadas, como a criação ou exclusão de índice de exemplo, serão replicadas automaticamente para o secundário somente leitura. A tentativa de habilitar o ajuste automático por meio do T-SQL no secundário somente leitura resultará em uma falha, pois não há suporte para uma configuração de ajuste diferente no secundário somente leitura.
>

Encontre nossas mais está confinado no opções de T-SQL para configurar o ajuste automático, consulte [Opções ALTER DATABASE SET (Transact-SQL) para o servidor de banco de dados SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current).

## <a name="disabled-by-the-system"></a>Desabilitado pelo sistema

O ajuste automático está monitorando todas as ações que ele pega no banco de dados e, em alguns casos, ele pode determinar que o ajuste automático não pode funcionar corretamente no banco de dados. Nessa situação, a opção de ajuste será desabilitada pelo sistema. Na maioria dos casos, isso ocorre porque Repositório de Consultas não está habilitado ou está em estado somente leitura em um banco de dados específico.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Configurar notificações de email de ajuste automático

Consulte Guia de [notificações por email de ajuste automático](sql-database-automatic-tuning-email-notifications.md) .

## <a name="next-steps"></a>Passos seguintes

* Leia o [artigo ajuste automático](sql-database-automatic-tuning.md) para saber mais sobre o ajuste automático e como ele pode ajudá-lo a melhorar seu desempenho.
* Consulte [recomendações de desempenho](sql-database-advisor.md) para obter uma visão geral das recomendações de desempenho do banco de dados SQL do Azure.
* Consulte análise de [desempenho de consultas](sql-database-query-performance.md) para saber mais sobre como exibir o impacto no desempenho de suas principais consultas.
