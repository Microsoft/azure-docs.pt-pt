---
title: Ler consultas sobre réplicas
description: A Base de Dados Azure SQL fornece a capacidade de carregar cargas de trabalho apenas de leitura utilizando a capacidade de réplicas apenas de leitura - chamada Read Scale-Out.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: 1a1b9907cd931716949d92d948a7d541fd2d5057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206950"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Utilizar réplicas só de leitura para balancear cargas de trabalho de consulta só de leitura

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Como parte da arquitetura de [alta disponibilidade,](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)cada base de dados no nível de serviço Premium e Business Critical é automaticamente aprovisionado com uma réplica primária e várias réplicas secundárias. As réplicas secundárias são aprovisionadas com o mesmo tamanho de computação que a réplica primária. A função **Read Scale-Out** permite-lhe equilibrar as cargas de trabalho de leitura sql de base de dados de balanço utilizando a capacidade de uma das réplicas apenas para leitura em vez de partilhar a réplica de leitura. Desta forma, a carga de trabalho só de leitura vai ser isolada da carga de trabalho de leitura/escrita principal e não vai afetar o desempenho. A funcionalidade destina-se às aplicações que incluem trabalhos de trabalho de leitura separados logicamente, como aanálise. Nos níveis de serviço Premium e Business Critical, as aplicações poderiam obter benefícios de desempenho usando esta capacidade adicional sem custos adicionais.

A funcionalidade **Read Scale-Out** também está disponível no nível de serviço Hyperscale quando é criada pelo menos uma réplica secundária. Várias réplicas secundárias podem ser usadas se as cargas de trabalho apenas de leitura requerem mais recursos do que disponíveis numa réplica secundária. A arquitetura de alta disponibilidade dos níveis de serviço Basic, Standard e General Purpose não inclui réplicas. A funcionalidade **Read Scale-Out** não está disponível nestes níveis de serviço.

O diagrama seguinte ilustra-o utilizando uma base de dados Business Critical.

![Réplicas de leitura](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

A funcionalidade Read Scale-Out é ativada por padrão em novas bases de dados Premium, Business Critical e Hyperscale. Para a Hyperscale, uma réplica secundária é criada por padrão para novas bases de dados. Se a sua cadeia de ligação SQL estiver configurada com, `ApplicationIntent=ReadOnly`a aplicação será redirecionada pela porta de entrada para uma réplica apenas de leitura dessa base de dados. Para obter informações `ApplicationIntent` sobre como utilizar a propriedade, consulte [a especificação da intenção da aplicação](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Se pretender garantir que a aplicação se liga à `ApplicationIntent` réplica primária, independentemente da definição na cadeia de ligação SQL, deve desativar explicitamente a leitura ao criar a base de dados ou ao alterar a sua configuração. Por exemplo, se atualizar a sua base de dados do nível Standard ou General Purpose para o nível Premium, Business Critical ou Hyperscale e pretender certificar-se de que todas as suas ligações continuam a ir para a réplica primária, desative a Leitura Scale-out. Para obter mais detalhes sobre como desativá-lo, consulte [Ativar e desativar a leitura da escala .](#enable-and-disable-read-scale-out)

> [!NOTE]
> As funcionalidades da Consulta Data Store, Eventos Estendidos e Perfis SQL não são suportadas nas réplicas apenas para leitura.

## <a name="data-consistency"></a>Consistência de dados

Um dos benefícios das réplicas é que as réplicas estão sempre no estado transacionalmente consistente, mas em diferentes pontos do tempo pode haver alguma pequena latência entre réplicas diferentes. Ler Scale-Out suporta consistência ao nível da sessão. Isto significa que, se a sessão de leitura se reconecta após um erro de ligação causado pela indisponibilidade de réplica, pode ser redirecionada para uma réplica que não está 100% atualizada com a réplica de leitura. Da mesma forma, se uma aplicação escrever dados usando uma sessão de leitura-escrita e lê-lo imediatamente usando uma sessão apenas de leitura, é possível que as últimas atualizações não sejam imediatamente visíveis na réplica. A latência é causada por uma operação de redoção de registo de transações assíncrona.

> [!NOTE]
> As tardios de replicação na região são baixas e esta situação é rara.

## <a name="connect-to-a-read-only-replica"></a>Ligar a uma réplica só de leitura

Quando ativa o Read Scale-Out `ApplicationIntent` para uma base de dados, a opção na cadeia de ligação fornecida pelo cliente dita se a ligação é encaminhada para a réplica de escrita ou para uma réplica apenas para leitura. Especificamente, `ApplicationIntent` se `ReadWrite` o valor for (o valor predefinido), a ligação será direcionada para a réplica de leitura da base de dados. Isto é idêntico ao comportamento existente. Se `ApplicationIntent` o `ReadOnly`valor for, a ligação é encaminhada para uma réplica apenas para leitura.

Por exemplo, a seguinte cadeia de ligação liga o cliente a uma réplica apenas para leitura (substituindo os itens nos suportes angulares pelos valores corretos para o seu ambiente e largando os suportes angulares):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Qualquer uma das seguintes cordas de ligação liga o cliente a uma réplica de leitura -escrita (substituindo os itens nos suportes angulares pelos valores corretos para o seu ambiente e largando os suportes angulares):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Verifique se uma ligação é a uma réplica apenas para leitura

Pode verificar se está ligado a uma réplica apenas para leitura, executando a seguinte consulta. Regressará READ_ONLY quando ligado a uma réplica só para leitura.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> A qualquer momento, apenas uma das réplicas AlwaysON é acessível pelas sessões ReadOnly.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Réplica de leitura de monitorização e resolução de problemas

Quando ligado a uma réplica apenas para leitura, `sys.dm_db_resource_stats` pode aceder às métricas de desempenho utilizando o DMV. Para aceder às estatísticas do `sys.dm_exec_query_stats` `sys.dm_exec_query_plan` plano `sys.dm_exec_sql_text` de consulta, use os DMVs e DMVs.

> [!NOTE]
> O DMV `sys.resource_stats` na base de dados lógica principal devolve dados de utilização e armazenamento de CPU da réplica primária.

## <a name="enable-and-disable-read-scale-out"></a>Ativar e desativar a leitura Scale-Out

Ler Scale-Out é ativado por padrão nos níveis de serviço Premium, Business Critical e Hyperscale. Ler Scale-Out não pode ser ativado nos níveis de serviço Basic, Standard ou General Purpose. Ler Scale-Out é automaticamente desativado em bases de dados de hiperescala configuradas com 0 réplicas.

Pode desativar e reativar o Read Scale-Out em bases de dados individuais e bases de dados elásticas de piscinas em nível de serviço Premium ou Business Critical utilizando os seguintes métodos.

> [!NOTE]
> A capacidade de desativar o Read Scale-Out é fornecida para a retrocompatibilidade.

### <a name="azure-portal"></a>Portal do Azure

Pode gerir a definição de 'Read Scale-out' na lâmina de base de dados **Configure.**

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager (RM) ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará a receber correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para mais informações sobre a sua compatibilidade, consulte [A introdução do novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

Gerir a Read Scale-Out no Azure PowerShell requer o lançamento da PowerShell de dezembro de 2016 ou mais recente. Para obter o mais recente lançamento da PowerShell, consulte [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Pode desativar ou reativar o Read Scale-Out no Azure PowerShell invocando o [cmdlet set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) e passando o valor desejado – `Enabled` ou `Disabled` - para o `-ReadScale` parâmetro.

Para desativar a leitura da escala numa base de dados existente (substituindo os itens nos suportes angulares pelos valores corretos para o seu ambiente e deixando cair os suportes angulares):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

Para desativar a leitura da escala numa nova base de dados (substituindo os itens nos suportes angulares pelos valores corretos para o seu ambiente e deixando cair os suportes angulares):

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

Para reativar a escala de leitura numa base de dados existente (substituindo os itens nos suportes angulares pelos valores corretos para o seu ambiente e largando os suportes angulares):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>API REST

Para criar uma base de dados com uma leitura de si desativada ou `readScale` para `Enabled` alterar `Disabled` a definição de uma base de dados existente, utilize o seguinte método com a propriedade definida para ou como no pedido de amostra abaixo.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

Para mais informações, consulte Bases de [Dados - Criar ou Atualizar](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>Utilização de TempDB na réplica apenas para leitura

A base de dados TempDB não é replicada para as réplicas apenas para leitura. Cada réplica tem a sua própria versão da base de dados TempDB que é criada quando a réplica é criada. Garante que o TempDB é atualizado e pode ser modificado durante a sua execução de consulta. Se a sua carga de trabalho apenas para leitura depender da utilização de objetos TempDB, deve criar estes objetos como parte do seu script de consulta.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Utilização de Leitura Scale-Out com bases de dados geo-replicadas

Se estiver a utilizar o Read Scale-Out para carregar as cargas de trabalho apenas de leitura numa base de dados geo-replicada (por exemplo, como membro de um grupo failover), certifique-se de que a leitura é ativada nas bases de dados secundárias primárias e geo-replicadas. Esta configuração garantirá que a mesma experiência de equilíbrio de carga continua quando a sua aplicação se conecta à nova primária após a falha. Se estiver a ligar-se à base de dados secundária geo-replicada `ApplicationIntent=ReadOnly` com a leitura ativada, as suas sessões serão encaminhadas para uma das réplicas da mesma forma que encaminhamos as ligações na base de dados primária.  As sessões sem `ApplicationIntent=ReadOnly` serão encaminhadas para a réplica primária do secundário geo-replicado, que também é apenas leitura. Como a base de dados secundária geo-replicada tem um ponto final diferente da base de dados `ApplicationIntent=ReadOnly`primária, historicamente para aceder ao secundário não era necessário definir. Para garantir a retrocompatibilidade, `sys.geo_replication_links` o DMV mostra `secondary_allow_connections=2` (qualquer ligação ao cliente é permitida).

> [!NOTE]
> Não é suportado um tordo redondo ou qualquer outro encaminhamento equilibrado entre as réplicas locais da base de dados secundária.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre a oferta de hiperescala de base de dados SQL, consulte o [nível de serviço Hyperscale](./sql-database-service-tier-hyperscale.md).
