---
title: Ler consultas sobre réplicas
description: A Azure SQL Database fornece a capacidade de carregar cargas de carga apenas de leitura usando a capacidade de réplicas apenas de leitura, chamadas de escala de leitura.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
titleSuffix: Azure SQL Database & SQL Managed Instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: a043eb5eed8f5554e42a113a3d7963be94da8a49
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985572"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Utilizar réplicas só de leitura para balancear cargas de trabalho de consulta só de leitura
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Como parte da [arquitetura de Alta Disponibilidade,](high-availability-sla.md#premium-and-business-critical-service-tier-availability)cada base de dados nos níveis de serviço Premium e Business Critical é automaticamente a forneccionada com uma réplica primária e várias réplicas secundárias. As réplicas secundárias são aviscadas com o mesmo tamanho computacional que a réplica primária. A funcionalidade *de escala de leitura* permite-lhe carregar cargas apenas cargas de carga SQL Database apenas usando a capacidade de uma das réplicas apenas de leitura em vez de partilhar a réplica de leitura-escrita. Desta forma, a carga de trabalho apenas de leitura será isolada da carga de trabalho principal de leitura-escrita e não afetará o seu desempenho. A funcionalidade destina-se às aplicações que incluem cargas de trabalho apenas de leitura separadas logicamente, como analíticas. Nos níveis de serviço Premium e Business Critical, as aplicações poderiam obter benefícios de desempenho usando esta capacidade adicional sem custos adicionais.

A funcionalidade de escala de leitura também está disponível no nível de serviço hyperscale quando pelo menos uma réplica secundária é criada. Várias réplicas secundárias podem ser usadas se as cargas de trabalho apenas de leitura exigirem mais recursos do que estão disponíveis numa réplica secundária. A arquitetura de alta disponibilidade dos níveis de serviço Básico, Standard e Finalidade Geral não inclui quaisquer réplicas. A funcionalidade de escala de leitura não está disponível nestes níveis de serviço.

O diagrama que se segue ilustra-o utilizando uma base de dados Business Critical.

![Réplicas só de leitura](./media/read-scale-out/business-critical-service-tier-read-scale-out.png)

A funcionalidade de escala de leitura é ativada por padrão nas novas bases de dados Premium, Business Critical e Hyperscale. Para Hyperscale, uma réplica secundária é criada por padrão para novas bases de dados. Se a sua cadeia de ligação SQL estiver `ApplicationIntent=ReadOnly` configurada, a aplicação será redirecionada pelo portal para uma réplica apenas de leitura dessa base de dados. Para obter informações sobre como utilizar o `ApplicationIntent` imóvel, consulte [especificar a intenção da aplicação.](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)

Se desejar garantir que a aplicação se liga à réplica primária, independentemente da `ApplicationIntent` definição da cadeia de ligação SQL, deve desativar explicitamente a escala de leitura ao criar a base de dados ou ao alterar a sua configuração. Por exemplo, se atualizar a sua base de dados do nível Standard ou General Purpose para nível Premium, Business Critical ou Hyperscale e quiser certificar-se de que todas as suas ligações continuam a ir para a réplica primária, desative a escala de leitura. Para obter detalhes sobre como desativá-lo, consulte [Ativar e desativar a escala de leitura](#enable-and-disable-read-scale-out).

> [!NOTE]
> As funcionalidades de Data Store, Eventos Alargados e Perfis SQL não são suportadas nas réplicas apenas de leitura.

## <a name="data-consistency"></a>Consistência de dados

Um dos benefícios das réplicas é que as réplicas estão sempre no estado transaccionalmente consistente, mas em diferentes pontos do tempo pode haver alguma pequena latência entre diferentes réplicas. A escala de leitura suporta a consistência ao nível da sessão. Significa que, se a sessão de leitura só se reconectar após um erro de ligação causado pela indisponibilidade de réplica, pode ser redirecionado para uma réplica que não está 100% atualizada com a réplica de leitura-escrita. Da mesma forma, se uma aplicação escrever dados usando uma sessão de leitura e lê-lo imediatamente usando uma sessão apenas de leitura, é possível que as últimas atualizações não sejam imediatamente visíveis na réplica. A latência é causada por uma operação de redo de registo de transações assíncronos.

> [!NOTE]
> As latências de replicação na região são baixas, e esta situação é rara.

## <a name="connect-to-a-read-only-replica"></a>Ligue-se a uma réplica só de leitura

Quando ativa a escala de leitura para uma base de `ApplicationIntent` dados, a opção na cadeia de ligação fornecida pelo cliente dita se a ligação é encaminhada para a réplica de escrita ou para uma réplica apenas de leitura. Especificamente, se o `ApplicationIntent` valor for `ReadWrite` (o valor predefinido), a ligação será direcionada para a réplica de leitura da base de dados. Isto é idêntico ao comportamento existente. Se o `ApplicationIntent` valor `ReadOnly` for, a ligação é encaminhada para uma réplica apenas de leitura.

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

Pode verificar se está ligado a uma réplica apenas de leitura executando a seguinte consulta. Voltará READ_ONLY quando estiver ligado a uma réplica só de leitura.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> A qualquer momento, apenas uma das réplicas AlwaysON está acessível pelas sessões ReadOnly.

## <a name="monitor-and-troubleshoot-a-read-only-replica"></a>Monitore e resolva uma réplica só de leitura

Quando ligado a uma réplica apenas de leitura, pode aceder às métricas de desempenho utilizando o `sys.dm_db_resource_stats` DMV. Para aceder às estatísticas do plano de consulta, use os `sys.dm_exec_query_stats` `sys.dm_exec_query_plan` `sys.dm_exec_sql_text` DMVs e DMVs.

> [!NOTE]
> O DMV `sys.resource_stats` na base de dados lógico principal devolve os dados de utilização e armazenamento do CPU da réplica primária.

## <a name="enable-and-disable-read-scale-out"></a>Ativar e desativar a escala de leitura

A escala de leitura é ativada por padrão nos níveis de serviço Premium, Business Critical e Hyperscale. A escala de leitura não pode ser ativada nos níveis de serviço Básico, Standard ou Finalidade Geral. A escala de leitura é automaticamente desativada em bases de dados de hiperescala configuradas com réplicas zero.

Pode desativar e reativar a escala de leitura em bases de dados únicas e bases de dados elásticas de piscinas no nível de serviço Premium ou Business Critical utilizando os seguintes métodos.

> [!NOTE]
> A capacidade de desativar a escala de leitura é prevista para uma compatibilidade retrógrada.

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

## <a name="using-tempdb-on-a-read-only-replica"></a>Usando o TempDB numa réplica só de leitura

A base de dados TempDB não é replicada nas réplicas apenas de leitura. Cada réplica tem a sua própria versão da base de dados TempDB que é criada quando a réplica é criada. Garante que o TempDB pode ser atualizado e modificado durante a sua execução de consultas. Se a sua carga de trabalho apenas de leitura depender da utilização de objetos TempDB, deverá criar estes objetos como parte do seu script de consulta.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Utilização de escala de leitura com bases de dados geo-replicadas

Se estiver a utilizar cargas de trabalho de leitura de saldo de carga para o equilíbrio de carga numa base de dados que é geo-replicada (por exemplo, como membro de um grupo de failover), certifique-se de que a escala de leitura é ativada tanto nas bases de dados primárias como nas bases de dados secundárias geo-replicadas. Esta configuração garantirá que a mesma experiência de equilíbrio de carga continua quando a sua aplicação se ligar à nova primária após o failover. 

Se estiver a ligar à base de dados secundária geo-replicada com a escala de leitura ativada, as suas sessões `ApplicationIntent=ReadOnly` serão encaminhadas para uma das réplicas da mesma forma que encaminhamos as ligações na base de dados primária.  As sessões sem `ApplicationIntent=ReadOnly` serão encaminhadas para a réplica primária do secundário geo-replicado, que também é apenas de leitura. Como a base de dados secundária geo-replicada tem um ponto final diferente da base de dados primária, historicamente para aceder ao secundário não era necessário `ApplicationIntent=ReadOnly` definir. Para garantir a retrocompatibilidade, `sys.geo_replication_links` a DMV mostra `secondary_allow_connections=2` (qualquer ligação ao cliente é permitida).

> [!NOTE]
> Não é suportado o robin redondo ou qualquer outro encaminhamento equilibrado de carga entre as réplicas locais da base de dados secundária.

## <a name="next-steps"></a>Próximos passos

Para obter informações sobre a oferta de hiperescala SQL Database, consulte [o nível de serviço hyperscale](service-tier-hyperscale.md).
