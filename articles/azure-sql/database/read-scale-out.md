---
title: Ler consultas sobre réplicas
description: O Azure SQL fornece a capacidade de usar a capacidade de réplicas apenas de leitura para ler cargas de trabalho, chamadas "Read Scale-Out".
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: bd393a897052dd0bd49851eee424c99ad1fcfb1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319432"
---
# <a name="use-read-only-replicas-to-offload-read-only-query-workloads"></a>Use réplicas apenas de leitura para descarregar cargas de trabalho de consulta apenas de leitura
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Como parte da arquitetura de [Alta Disponibilidade](high-availability-sla.md#premium-and-business-critical-service-tier-availability), cada base de dados, base de dados elástica e caso gerido no nível de serviço Premium e Business Critical é automaticamente a provisionado com uma réplica de leitura primária e várias réplicas secundárias apenas de leitura. As réplicas secundárias são aviscadas com o mesmo tamanho computacional que a réplica primária. A funcionalidade *de escala de leitura* permite descarregar cargas de trabalho apenas de leitura utilizando a capacidade de cálculo de uma das réplicas apenas de leitura, em vez de executá-las na réplica de leitura-escrita. Desta forma, algumas cargas de trabalho apenas de leitura podem ser isoladas das cargas de trabalho de leitura-escrita, e não afetarão o seu desempenho. A funcionalidade destina-se às aplicações que incluem cargas de trabalho apenas de leitura separadas logicamente, como analíticas. Nos níveis de serviço Premium e Business Critical, as aplicações poderiam obter benefícios de desempenho usando esta capacidade adicional sem custos adicionais.

A funcionalidade *de escala de leitura* também está disponível no nível de serviço hyperscale quando pelo menos uma réplica secundária é criada. Réplicas secundárias múltiplas podem ser usadas para cargas de trabalho apenas de equilíbrio de carga que requerem mais recursos do que disponíveis numa réplica secundária.

A arquitetura de alta disponibilidade dos níveis de serviço Básico, Standard e Finalidade Geral não inclui quaisquer réplicas. A *funcionalidade de escala de leitura* não está disponível nestes níveis de serviço.

O diagrama seguinte ilustra a característica.

![Réplicas só de leitura](./media/read-scale-out/business-critical-service-tier-read-scale-out.png)

A funcionalidade *de escala de leitura* é ativada por padrão nas novas bases de dados Premium, Business Critical e Hyperscale. Para Hyperscale, uma réplica secundária é criada por padrão para novas bases de dados. 

> [!NOTE]
> A escala de leitura está sempre ativada no nível de serviço Business Critical de Caso Gerido.

Se a sua cadeia de ligação SQL estiver configurada, `ApplicationIntent=ReadOnly` a aplicação será redirecionada para uma réplica apenas de leitura dessa base de dados ou instância gerida. Para obter informações sobre como utilizar a `ApplicationIntent` propriedade, consulte [Especificar a Intenção de Aplicação](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Se desejar garantir que a aplicação se liga à réplica primária, independentemente da `ApplicationIntent` definição da cadeia de ligação SQL, deve desativar explicitamente a escala de leitura ao criar a base de dados ou ao alterar a sua configuração. Por exemplo, se atualizar a sua base de dados do nível Standard ou General Purpose para nível Premium, Business Critical ou Hyperscale e quiser certificar-se de que todas as suas ligações continuam a ir para a réplica primária, desative a escala de leitura. Para obter mais informações sobre como desativá-lo, consulte [Ativar e desativar a escala de leitura](#enable-and-disable-read-scale-out).

> [!NOTE]
> As funcionalidades de Loja de Consultas e perfis SQL não são suportadas em réplicas apenas de leitura. 

## <a name="data-consistency"></a>Consistência de dados

Um dos benefícios das réplicas é que as réplicas estão sempre no estado transaccionalmente consistente, mas em diferentes pontos do tempo pode haver alguma pequena latência entre diferentes réplicas. A escala de leitura suporta a consistência ao nível da sessão. Significa que, se a sessão de leitura só se reconectar após um erro de ligação causado pela indisponibilidade de réplica, pode ser redirecionado para uma réplica que não está 100% atualizada com a réplica de leitura-escrita. De igual forma, se uma aplicação escrever dados com uma sessão de leitura-escrita e os ler de imediato com uma sessão só de leitura, será possível que as últimas atualizações não estejam imediatamente visíveis na réplica. A latência é causada por uma operação de fase de rollforward de registo de transações assíncronas.

> [!NOTE]
> As latências de replicação na região são baixas, e esta situação é rara. Para monitorizar a latência da replicação, consulte [a monitorização e resolução de problemas da réplica apenas de leitura](#monitoring-and-troubleshooting-read-only-replicas).

## <a name="connect-to-a-read-only-replica"></a>Ligue-se a uma réplica só de leitura

Quando ativa a escala de leitura para uma base de `ApplicationIntent` dados, a opção na cadeia de ligação fornecida pelo cliente dita se a ligação é encaminhada para a réplica de escrita ou para uma réplica apenas de leitura. Especificamente, se o `ApplicationIntent` valor for `ReadWrite` (o valor predefinido), a ligação será direcionada para a réplica de leitura-escrita. Isto é idêntico ao comportamento quando `ApplicationIntent` não está incluído na cadeia de ligação. Se o `ApplicationIntent` valor `ReadOnly` for, a ligação é encaminhada para uma réplica apenas de leitura.

Por exemplo, a seguinte cadeia de ligação liga o cliente a uma réplica apenas de leitura (substituindo os itens nos suportes angulares com os valores corretos para o seu ambiente e largando os suportes angulares):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Qualquer uma das seguintes cordas de ligação liga o cliente a uma réplica de leitura-escrita (substituindo os itens nos suportes angulares com os valores corretos para o seu ambiente e largando os suportes angulares):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Verifique se uma ligação é para uma réplica apenas de leitura

Pode verificar se está ligado a uma réplica apenas de leitura, executando a seguinte consulta no contexto da sua base de dados. Voltará READ_ONLY quando estiver ligado a uma réplica só de leitura.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability');
```

> [!NOTE]
> Nos níveis de serviço Premium e Business Critical, apenas uma das réplicas só de leitura está acessível a qualquer momento. A hiperescala suporta várias réplicas só de leitura.

## <a name="monitoring-and-troubleshooting-read-only-replicas"></a>Monitorização e resolução de problemas de réplicas apenas de leitura

Quando ligados a uma réplica apenas de leitura, as Vistas de Gestão Dinâmica (DMVs) refletem o estado da réplica e podem ser questionadas para fins de monitorização e resolução de problemas. O motor da base de dados fornece múltiplas vistas para expor uma grande variedade de dados de monitorização. 

As vistas comumente utilizadas são:

| Nome | Objetivo |
|:---|:---|
|[sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Fornece métricas de utilização de recursos durante a última hora, incluindo CPU, IO de dados e utilização de gravação de registos em relação aos limites objetivos do serviço.|
|[sys.dm_os_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)| Fornece estatísticas de espera agregadas para a instância do motor da base de dados. |
|[sys.dm_database_replica_states](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database)| Fornece réplicas de estado de saúde e estatísticas de sincronização. Redo o tamanho da fila e a taxa de redo servem como indicadores de latência de dados na réplica apenas de leitura. |
|[sys.dm_os_performance_counters](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)| Fornece contadores de desempenho do motor de base de dados.|
|[sys.dm_exec_query_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)| Fornece estatísticas de execução por consulta, tais como número de execuções, tempo de CPU utilizado, etc.|
|[sys.dm_exec_query_plan()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql)| Fornece planos de consulta em cache. |
|[sys.dm_exec_sql_text()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)| Fornece texto de consulta para um plano de consulta em cache.|
|[sys.dm_exec_query_profiles](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| Proporciona progressos de consulta em tempo real enquanto as consultas estão em execução.|
|[sys.dm_exec_query_plan_stats()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| Fornece o último plano de execução real conhecido, incluindo estatísticas de tempo de execução para uma consulta.|
|[sys.dm_io_virtual_file_stats()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)| Fornece estatísticas de IOPS de armazenamento, produção e latência para todos os ficheiros de base de dados. |

> [!NOTE]
> Os `sys.resource_stats` `sys.elastic_pool_resource_stats` DMVs e DMVs na base de dados lógica devolvem dados de utilização de recursos da réplica primária.

### <a name="monitoring-read-only-replicas-with-extended-events"></a>Monitorização de réplicas apenas de leitura com Eventos Alargados

Não é possível criar uma sessão de eventos prolongada quando ligada a uma réplica apenas de leitura. No entanto, na Base de Dados Azure SQL, as definições de sessões de [Eventos Estendidos,](xevent-db-diff-from-svr.md) criadas e alteradas na réplica primária, replicam-se apenas em réplicas de leitura, incluindo réplicas de geo-réplicas, e capturam eventos em réplicas apenas de leitura.

Uma sessão de evento prolongada sobre uma réplica apenas de leitura que é baseada numa definição de sessão da réplica primária pode ser iniciada e interrompida independentemente da réplica primária. Quando uma sessão de evento prolongada é largada na réplica primária, também é largada em todas as réplicas apenas de leitura.

### <a name="transaction-isolation-level-on-read-only-replicas"></a>Nível de isolamento de transações em réplicas apenas de leitura

As consultas que funcionam em réplicas apenas de leitura são sempre mapeadas para o nível de isolamento de transações [instantâneas.](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server) O isolamento instantâneo usa a versão de linha para evitar cenários de bloqueio onde os leitores bloqueiam escritores.

Em casos raros, se uma transação de isolamento instantâneo aceder a metadados de objetos modificados noutra transação simultânea, poderá receber o erro [3961](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-3961-database-engine-error), "A transação de isolamento instantâneo falhou na base de dados '%.*ls' porque o objeto acedido pela declaração foi modificado por uma declaração DDL noutra transação simultânea desde o início desta transação. Tal não é permitido porque os metadados não têm a mesma versão. Uma atualização simultânea aos metadados pode levar a inconsistência se misturada com isolamento instantâneo."

### <a name="long-running-queries-on-read-only-replicas"></a>Consultas de longa duração sobre réplicas só de leitura

As consultas em execução em réplicas apenas de leitura precisam de aceder aos metadados para os objetos referenciados na consulta (tabelas, índices, estatísticas, etc.) Em casos raros, se um objeto de metadados for modificado na réplica primária enquanto uma consulta mantém um bloqueio no mesmo objeto na réplica apenas de leitura, a consulta pode [bloquear](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/troubleshoot-primary-changes-not-reflected-on-secondary#BKMK_REDOBLOCK) o processo que aplica alterações da réplica primária para a réplica apenas de leitura. Se tal consulta fosse executada por muito tempo, faria com que a réplica apenas de leitura estivesse significativamente dessincronizada com a réplica primária. 

Se uma consulta de longa duração sobre uma réplica só de leitura causar este tipo de bloqueio, será automaticamente encerrada e a sessão receberá o erro 1219, "A sua sessão foi desligada devido a uma operação DDL de alta prioridade".

> [!NOTE]
> Se receber o erro 3961 ou o erro 1219 ao executar consultas contra uma réplica apenas de leitura, recandidutar a consulta.

> [!TIP]
> Nos níveis de serviço Premium e Business Critical, quando ligados a uma réplica apenas de leitura, o `redo_queue_size` `redo_rate` DMV [sys.dm_database_replica_states](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database) pode ser utilizado para monitorizar o processo de sincronização de dados, servindo como indicadores de latência de dados na réplica apenas de leitura.
> 

## <a name="enable-and-disable-read-scale-out"></a>Ativar e desativar a escala de leitura

A escala de leitura é ativada por padrão nos níveis de serviço Premium, Business Critical e Hyperscale. A escala de leitura não pode ser ativada nos níveis de serviço Básico, Standard ou Finalidade Geral. A escala de leitura é automaticamente desativada em bases de dados de hiperescala configuradas com réplicas zero.

Pode desativar e reativar a escala de leitura em bases de dados únicas e bases de dados elásticas nos níveis de serviço Premium ou Business Critical utilizando os seguintes métodos.

> [!NOTE]
> Para bases de dados individuais e bases de dados elásticas de piscinas, a capacidade de desativar a escala de leitura é prevista para uma compatibilidade retrógrada. A escala de leitura não pode ser desativada em casos geridos pela Business Critical.

### <a name="azure-portal"></a>Portal do Azure

Pode gerir a definição de escala de leitura na lâmina da base de **dados Configure.**

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo Azure Resource Manager continuará a receber correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos Azure Resource Manager são substancialmente idênticos. Para obter mais informações sobre a sua compatibilidade, consulte [a introdução do novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

Gerir a escala de leitura em Azure PowerShell requer o lançamento do Azure PowerShell de dezembro de 2016 ou mais recente. Para o mais recente lançamento powerShell, consulte [a Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Pode desativar ou reativar a escala de leitura em Azure PowerShell invocando o cmdlet [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) e passando no valor pretendido `Enabled` (ou `Disabled` ) para o `-ReadScale` parâmetro.

Para desativar a escala numa base de dados existente (substituindo os itens nos suportes angulares pelos valores corretos para o seu ambiente e deixando cair os suportes angulares):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

Para desativar a escala numa nova base de dados (substituindo os itens nos suportes angulares pelos valores corretos para o seu ambiente e deixando cair os suportes angulares):

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

Para voltar a ativar a escala de leitura numa base de dados existente (substituindo os itens nos suportes angulares pelos valores corretos para o seu ambiente e deixando cair os suportes angulares):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>API REST

Para criar uma base de dados com desativação de escala de leitura ou para alterar a definição para uma base de dados existente, utilize o seguinte método com a `readScale` propriedade definida para `Enabled` `Disabled` ou, como no seguinte pedido de amostra.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

Para obter mais informações, consulte [bases de dados - Criar ou atualizar](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-the-tempdb-database-on-a-read-only-replica"></a>Usando a `tempdb` base de dados numa réplica apenas de leitura

A `tempdb` base de dados da réplica primária não é replicada nas réplicas apenas de leitura. Cada réplica tem a sua própria `tempdb` base de dados que é criada quando a réplica é criada. Isto garante que `tempdb` é atualizável e pode ser modificado durante a sua execução de consultas. Se a sua carga de trabalho apenas de leitura depender da utilização de `tempdb` objetos, deverá criar estes objetos como parte do seu script de consulta.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Utilização de escala de leitura com bases de dados geo-replicadas

As bases de dados secundárias geo-replicadas têm a mesma arquitetura de Alta Disponibilidade que as bases de dados primárias. Se estiver a ligar-se à base de dados secundária geo-replicada com a escala de leitura ativada, as suas sessões `ApplicationIntent=ReadOnly` serão encaminhadas para uma das réplicas de alta disponibilidade da mesma forma que são encaminhadas na base de dados primária. As sessões sem `ApplicationIntent=ReadOnly` serão encaminhadas para a réplica primária do secundário geo-replicado, que também é apenas de leitura. 

Desta forma, a criação de uma geo-réplica fornece mais duas réplicas apenas de leitura para uma base de dados primária de leitura, para um total de três réplicas apenas de leitura. Cada geo-réplica adicional fornece outro par de réplicas apenas de leitura. As réplicas de geo-réplicas podem ser criadas em qualquer região de Azure, incluindo a região da base de dados primária.

> [!NOTE]
> Não existe um round-robin automático ou qualquer outro encaminhamento equilibrado de carga entre as réplicas de uma base de dados secundária geo-replicada.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre a oferta de hiperescala sql Database, consulte [o nível de serviço hyperscale](service-tier-hyperscale.md).
