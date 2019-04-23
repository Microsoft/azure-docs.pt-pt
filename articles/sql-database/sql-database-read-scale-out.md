---
title: SQL Database do Azure - leia consultas em réplicas | Documentos da Microsoft
description: A base de dados do SQL Azure fornece a capacidade de balancear a carga só de leitura cargas de trabalho com a capacidade de réplicas só de leitura - chamado escalável de leitura.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 04/19/2019
ms.openlocfilehash: cbcdcfd151951334246a4e85d9f521a15bb6269d
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006153"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Utilizar réplicas só de leitura para cargas de trabalho de consulta só de leitura de balanceamento de carga

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é suportado pelo SQL Database do Azure, mas todo o desenvolvimento futuro é para o módulo de Az.Sql. Para estes cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Como parte da [arquitetura de elevada disponibilidade](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability), cada base de dados da camada de serviço Premium, críticas para a empresa ou de Hiperescala automaticamente está aprovisionada com uma réplica primária e várias réplicas secundárias. As réplicas secundárias são aprovisionadas com o mesmo tamanho de computação, como a réplica primária. O **Escalamento leitura** funcionalidade permite-lhe para balanceamento de carga da base de dados SQL só de leitura cargas de trabalho com a capacidade de uma das réplicas só de leitura em vez de partilhar a réplica de leitura / escrita. Desta forma, a carga de trabalho só de leitura será isolada de carga de trabalho de leitura / escrita principal e não irá afetar o desempenho dele. A funcionalidade destina-se para as aplicações que incluem logicamente separadas só de leitura cargas de trabalho, tais como análises. Eles foi possível obter os benefícios de desempenho com essa capacidade adicional na não custos adicionais.

O diagrama seguinte ilustra-la utilizando uma base de dados críticas para a empresa.

![Réplicas só de leitura](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

A funcionalidade de leitura horizontal está ativada por predefinição em novos Premium, críticas para a empresa e bases de dados de grande escala. Se a cadeia de ligação de SQL está configurada com `ApplicationIntent=ReadOnly`, o aplicativo será redirecionado pelo gateway para uma réplica só de leitura dessa base de dados. Para obter informações sobre como utilizar o `ApplicationIntent` propriedade, veja [especificação de tipo de aplicação](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Se desejar garantir que a aplicação estabelece uma ligação para a réplica primária, independentemente do `ApplicationIntent` definir na cadeia de ligação de SQL, tem explicitamente de desativar o Escalamento horizontal leitura ao criar a base de dados ou quando alterar a respetiva configuração. Por exemplo, se atualizar a base de dados do escalão Standard ou de fins gerais para o escalão Premium, críticas para a empresa ou de Hiperescala e pretender certificar-se de que todas as suas conexões continuam para ir para a réplica primária, desative a expansão de leitura. Para obter detalhes sobre como desabilitá-lo, consulte [ativar e desativar a expansão de leitura](#enable-and-disable-read-scale-out).

> [!NOTE]
> Consulta dados Store, Extended Events, SQL Profiler e recursos de auditoria não são suportados nas réplicas só de leitura. 

## <a name="data-consistency"></a>Consistência dos dados

Um dos benefícios de réplicas é que as réplicas estão sempre no estado consistente de forma transacional, mas em diferentes alturas no tempo pode haver alguma latência pequena entre diferentes réplicas. Leitura de aumento horizontal oferece suporte a consistência de nível de sessão. Isso significa que, se a sessão só de leitura volta a ligar após um erro de ligação causado por indisponibilidade de réplica, poderá ser redirecionado para uma réplica que não é atualizado com a réplica de leitura / escrita de 100%. Da mesma forma, se uma aplicação escreve dados através de uma sessão de leitura / escrita e imediatamente lê-lo com uma sessão só de leitura, é possível que as atualizações mais recentes não são imediatamente visíveis na réplica. A latência é causada por uma operação de Refazer do log de transação assíncrona.

> [!NOTE]
> Latências de replicação dentro da região são baixas e esta situação é rara.

## <a name="connect-to-a-read-only-replica"></a>Ligar a uma réplica só de leitura

Ao ativar a expansão de leitura para uma base de dados, o `ApplicationIntent` opção na cadeia de ligação fornecida pelo cliente determina se a ligação é encaminhada para a réplica de escrita ou para uma réplica só de leitura. Especificamente, se o `ApplicationIntent` valor é `ReadWrite` (o valor predefinido), a ligação será direcionada para a réplica de leitura / escrita da base de dados. Isso é idêntico ao comportamento existente. Se o `ApplicationIntent` valor é `ReadOnly`, a ligação é encaminhada para uma réplica só de leitura.

Por exemplo, a seguinte cadeia de ligação liga-se o cliente para uma réplica só de leitura (substituindo os itens nos parênteses angulares com os valores corretos para o seu ambiente e remover os parênteses angulares):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Qualquer uma das cadeias de ligação seguinte liga-se o cliente para uma réplica de leitura / escrita (substituindo os itens nos parênteses angulares com os valores corretos para o seu ambiente e remover os parênteses angulares):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Certifique-se de que é uma ligação para uma réplica só de leitura

Pode verificar se estiver ligado a uma réplica só de leitura ao executar a consulta seguinte. Ela retornará READ_ONLY quando ligado a uma réplica só de leitura.

```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> Em qualquer momento, apenas uma das réplicas AlwaysON está acessível às sessões de só de leitura.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Monitorização e resolução de problemas de réplica só de leitura

Quando estiverem ligados a uma réplica só de leitura, pode aceder a métricas de desempenho utilizando o `sys.dm_db_resource_stats` DMV. Para acessar as estatísticas de plano de consulta, utilize o `sys.dm_exec_query_stats`, `sys.dm_exec_query_plan` e `sys.dm_exec_sql_text` DMVs.

> [!NOTE]
> A DMV `sys.resource_stats` na base de dados mestra lógica retorna dados de armazenamento e utilização de CPU da réplica primária.


## <a name="enable-and-disable-read-scale-out"></a>Ativar e desativar a expansão de leitura

Escalamento de leitura está ativado por predefinição nos escalões de serviço Premium, críticas para a empresa e de Hiperescala. Não é possível ativar a expansão de leitura nos escalões de serviço básico, Standard ou fins gerais. Escalamento de leitura é automaticamente desativado nas bases de dados de Hiperescala configuradas com 0 réplicas. 

Pode desativar e reativar o Escalamento de leitura em bases de dados individuais e bases de dados do conjunto elástico no escalão de serviço Premium ou críticas para a empresa, utilizando os seguintes métodos.

> [!NOTE]
> A capacidade de desativar a expansão de leitura é fornecida para compatibilidade com versões anteriores.

### <a name="azure-portal"></a>Portal do Azure

Pode gerir a definição de leitura Sacle-out no **configurar** painel base de dados. 

### <a name="powershell"></a>PowerShell

A gestão de escalamento de leitura no Azure PowerShell requer a Dezembro de 2016 do Azure PowerShell versão ou mais recente. Para a versão mais recente do PowerShell, consulte [do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Pode desativar ou reativar o Escalamento de leitura no Azure PowerShell invocando o [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet e passando o valor desejado – `Enabled` ou `Disabled` – para o `-ReadScale` parâmetro. 

Para desativar o Escalamento leitura na base de dados existente (substituindo os itens nos parênteses angulares com os valores corretos para o seu ambiente e remover os parênteses angulares):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```
Para desativar a leitura aumentar horizontalmente numa nova base de dados (substituindo os itens nos parênteses angulares com os valores corretos para o seu ambiente e remover os parênteses angulares):

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled -Edition Premium
```

Para voltar a activar leitura aumentar horizontalmente numa base de dados existente (substituindo os itens nos parênteses angulares com os valores corretos para o seu ambiente e remover os parênteses angulares):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

### <a name="rest-api"></a>API REST

Para criar uma base de dados com leitura Escalamento desabilitado ou para alterar a definição de uma base de dados existente, utilize o seguinte método com o `readScale` definida como `Enabled` ou `Disabled` como no abaixo de pedido de exemplo.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Disabled"
   }
}
```

Para obter mais informações, consulte [bases de dados - criar ou atualizar](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>Usando o TempDB na réplica só de leitura

A base de dados TempDB não é replicado para as réplicas só de leitura. Cada réplica tem sua própria versão do banco de dados TempDB é criado quando a réplica ser criada. Assegura que TempDB é atualizável e pode ser modificado durante a execução da consulta. Se a sua carga de trabalho só de leitura depende do uso de objetos de TempDB, deve criar esses objetos como parte do seu script de consulta. 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Usando o Escalamento de leitura com bancos de dados de georreplicação

Se estiver a utilizar o Escalamento de leitura para balanceamento de carga só de leitura cargas de trabalho num banco de dados que está a georreplicação (por exemplo, como um membro de um grupo de ativação pós-falha), certifique-se de que leitura Escalamento horizontal está ativado no principal e os georreplicado bases de dados secundárias. Esta configuração irá garantir que a mesma experiência de balanceamento de carga continua quando seu aplicativo se conecta à nova principal após a ativação pós-falha. Se estiver a ligar para o georreplicada secundária da base de dados com escala de leitura ativada, as sessões com `ApplicationIntent=ReadOnly` serão encaminhados para uma das réplicas da mesma forma, podemos encaminhar as ligações na base de dados primária.  As sessões sem `ApplicationIntent=ReadOnly` serão encaminhados para a réplica primária da secundária georreplicada, que também é só de leitura. Porque a base de dados secundária georreplicada tem um ponto de extremidade diferente da base de dados primária, historicamente para acessar o secundário é não era necessária para definir `ApplicationIntent=ReadOnly`. Para garantir a compatibilidade com versões anteriores, `sys.geo_replication_links` DMV mostra `secondary_allow_connections=2` (qualquer ligação de cliente é permitida).

> [!NOTE]
> Round robin ou quaisquer outras com balanceamento de carga encaminhamento entre as réplicas locais da base de dados secundária não é suportado.

## <a name="next-steps"></a>Passos Seguintes

- Para obter informações sobre a oferta de Hiperescala de base de dados SQL, consulte [camada de serviços de Hiperescala](./sql-database-service-tier-hyperscale.md).
