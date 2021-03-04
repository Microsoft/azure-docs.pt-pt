---
title: Ativar o ajuste automático
description: Pode ativar a sintonização automática na sua base de dados facilmente utilizando o portal Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 03/03/2021
ms.openlocfilehash: d60810c291984e0f57df1968f69678de8179273c
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042526"
---
# <a name="enable-automatic-tuning-in-the-azure-portal-to-monitor-queries-and-improve-workload-performance"></a>Permitir a sintonização automática no portal Azure para monitorizar consultas e melhorar o desempenho da carga de trabalho
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

O Azure SQL Database gere automaticamente os serviços de dados que monitorizam constantemente as suas consultas e identifica a ação que pode realizar para melhorar o desempenho da sua carga de trabalho. Pode rever recomendações e aplicá-las manualmente, ou deixar a Azure SQL Database aplicar automaticamente ações corretivas - isto é conhecido como **modo de afinação automática**.

A sintonização automática pode ser ativada no servidor ou no nível da base de dados através de:

- O [portal Azure](automatic-tuning-enable.md#azure-portal)
- [Rest API](automatic-tuning-enable.md#rest-api) chama
- [Comandos T-SQL](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true)

> [!NOTE]
> Para Azure SQL Managed Instance, a opção suportada FORCE_LAST_GOOD_PLAN só pode ser configurada através [de T-SQL.](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) A configuração baseada no portal Azure e as opções automáticas de afinação de índices descritas neste artigo não se aplicam à Instância Gerida Azure SQL.

> [!NOTE]
> A configuração das opções de afinação automática através do modelo ARM (Azure Resource Manager) não é suportada neste momento.

## <a name="enable-automatic-tuning-on-server"></a>Ativar a sintonização automática no servidor

No nível do servidor pode optar por herdar a configuração de sintonização automática de "Azure Defaults" ou não herdar a configuração. Os padrãos azure são FORCE_LAST_GOOD_PLAN está ativado, CREATE_INDEX é desativado e DROP_INDEX é desativado.

> [!IMPORTANT]
> A partir de março de 2020, os novos incumprimentos do Azure para a sintonização automática são os seguintes:
>
> - FORCE_LAST_GOOD_PLAN = ativado, CREATE_INDEX = incapacitado e DROP_INDEX = incapacitado.
> - Os servidores existentes sem preferências de sintonização automática configuradas são automaticamente configurados para HER as predefinições do Azure. Isto aplica-se a todos os clientes que têm atualmente configurações de servidor para afinação automática num estado indefinido.
> - Os novos servidores criados serão automaticamente configurados para HER as predefinições do Azure (ao contrário do que se verificou quando a configuração de sintonização automática estava num estado indefinido após a criação de novos servidores).

### <a name="azure-portal"></a>Portal do Azure

Para ativar a sintonização automática num [servidor](logical-servers.md) na Base de Dados Azure SQL, navegue para o servidor no portal Azure e, em seguida, selecione **sintonização automática** no menu.

![O Screenshot mostra sintonização automática no portal Azure, onde pode aplicar opções para um servidor.](./media/automatic-tuning-enable/server.png)

> [!NOTE]
> Por favor, note que a opção **DROP_INDEX** neste momento não é compatível com aplicações que usam comutação de partição e dicas de índice e não deve ser ativada nestes casos. A queda de índices não suportados não é suportada para os níveis de serviço Premium e Business Critical.

Selecione as opções de afinação automática que pretende ativar e selecione **Aplicar**.

As opções de afinação automática num servidor são aplicadas em todas as bases de dados deste servidor. Por predefinição, todas as bases de dados herdam a configuração do servidor principal, mas esta pode ser ultrapassada e especificada para cada base de dados individualmente.

### <a name="rest-api"></a>API REST

Para saber mais sobre a utilização de uma API REST para permitir a sintonização automática num **servidor,** consulte os [métodos de sintonização automática do Servidor UPDATE e GET HTTP](/rest/api/sql/serverautomatictuning).

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Ativar a sintonização automática numa base de dados individual

A Base de Dados Azure SQL permite especificar individualmente a configuração de sintonização automática para cada base de dados. No nível de base de dados pode optar por herdar a configuração de sintonização automática do servidor principal, "Azure Defaults" ou não herdar a configuração. As Predefinições do Azure estão definidas para FORCE_LAST_GOOD_PLAN está ativada, CREATE_INDEX está desativada e DROP_INDEX está desativada.

> [!TIP]
> A recomendação geral é gerir a configuração de sintonização automática ao **nível** do servidor para que as mesmas definições de configuração possam ser aplicadas automaticamente em todas as bases de dados. Configure a sintonização automática numa base de dados individual apenas se precisar que essa base de dados tenha configurações diferentes das de outras que herdam as definições do mesmo servidor.

### <a name="azure-portal"></a>Portal do Azure

Para ativar a sintonização automática numa **única base de dados,** navegue até à base de dados no portal Azure e selecione **afinação automática**.

As definições de sintonização automática individuais podem ser configuradas separadamente para cada base de dados. Pode configurar manualmente uma opção de sintonização automática individual ou especificar que uma opção herda as suas definições a partir do servidor.

![O Screenshot mostra sintonização automática no portal Azure, onde pode aplicar opções para uma única base de dados.](./media/automatic-tuning-enable/database.png)

Por favor, note que DROP_INDEX opção neste momento não é compatível com aplicações que usam comutação de partição e dicas de índice e não deve ser ativada nestes casos.

Uma vez selecionada a configuração desejada, clique em **Aplicar**.

### <a name="rest-api"></a>API REST

Para saber mais sobre a utilização de uma API REST para permitir a sintonização automática numa única base de dados, consulte os [métodos Azure SQL Database de sintonização automática UPDATE e GET HTTP](/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Para permitir a sintonização automática numa única base de dados via T-SQL, ligue-se à base de dados e execute a seguinte consulta:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

A definição de sintonização automática para AUTO aplicará Azure Predefinidos. Definindo-o para HER, a configuração de sintonização automática será herdada do servidor principal. Escolhendo o CUSTOM, terá de configurar manualmente a sintonização automática.

Para configurar opções individuais de afinação automática via T-SQL, ligue-se à base de dados e execute a consulta como esta:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = ON, DROP_INDEX = OFF)
```

A definição da opção de sintonização individual para ON irá sobrepor qualquer definição que a base de dados herdada e ativar a opção de afinação. Defini-lo para OFF também irá sobrepor qualquer definição que a base de dados herdada e desativar a opção de afinação. A opção de sintonização automática, para a qual o DEFAULT é especificado, herdará a configuração de sintonização automática a partir das definições de nível do servidor.  

> [!IMPORTANT]
> No caso de [geo-replicação ativa,](auto-failover-group-overview.md)a sintonização automática só deve ser configurada na base de dados primária. As ações de afinação aplicadas automaticamente, tais como por exemplo, a criação ou eliminação de índices será automaticamente replicada para o secundário apenas de leitura. Tentar ativar a sintonização automática via T-SQL no secundário apenas de leitura resultará numa falha, uma vez que ter uma configuração de sintonização diferente no secundário apenas de leitura não é suportado.
>

Para obter mais opções de T-SQL para configurar a sintonização automática, consulte [OPções DE SET ALTER DATABASE (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current&preserve-view=true).

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="automated-recommendation-management-is-disabled"></a>A gestão automatizada de recomendações é desativada

Em caso de mensagens de erro que a gestão automatizada de recomendações tenha sido desativada, ou simplesmente desativada pelo sistema, as causas mais comuns são:
- A Loja de Consultas não está ativada, ou
- A Loja de Consulta está em modo apenas de leitura para uma base de dados especificada, ou
- A Loja de Consultas deixou de funcionar porque usou o espaço de armazenamento atribuído.

Podem ser considerados os seguintes passos para corrigir esta questão:
- Limpe a Loja de Consultas ou modifique o período de retenção de dados para "auto" utilizando o T-SQL. Veja como configurar a [política recomendada de retenção e captura para a Loja de Consultas.](/azure/azure-sql/database/query-performance-insight-use#recommended-retention-and-capture-policy)
- Use o SQL Server Management Studio (SSMS) e siga estes passos:
  - Ligue-se à Base de Dados Azure SQL
  - Clique direito na base de dados
  - Vá a Propriedades e clique na Loja de Consultas
  - Mude o modo de operação para Read-Write
  - Alterar o modo de captura da loja para Auto
  - Altere o modo de limpeza baseado em tamanho para automático

### <a name="permissions"></a>Permissões

Como a sintonização automática é uma função Azure, para usá-la, terá de utilizar as funções incorporadas do Azure. A utilização da autenticação SQL apenas não será suficiente para utilizar a funcionalidade a partir do portal Azure.

Para utilizar a sintonização automática, a permissão mínima necessária para conceder ao utilizador é a função de contribuinte da Base de [Dados SQL](../../role-based-access-control/built-in-roles.md#sql-db-contributor) incorporada da Azure. Também pode considerar a utilização de funções de privilégio mais elevadas, tais como SQL Server Contributor, SQL Managed Instance Contributor, Colaborador e Proprietário.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Configure notificações automáticas de afinação de e-mails

Para receber notificações automáticas de email sobre recomendações feitas pela sintonização automática, consulte o guia [de notificações de e-mail automático.](automatic-tuning-email-notifications-configure.md)

## <a name="next-steps"></a>Passos seguintes

- Leia o [artigo de afinação automática](automatic-tuning-overview.md) para saber mais sobre a sintonização automática e como pode ajudá-lo a melhorar o seu desempenho.
- Consulte [as recomendações](database-advisor-implement-performance-recommendations.md) de desempenho para uma visão geral das recomendações de desempenho da Base de Dados Azure SQL.
- Consulte [a Consulta Performance Insights](query-performance-insight-use.md) para aprender a visualizar o impacto de desempenho das suas principais consultas.
