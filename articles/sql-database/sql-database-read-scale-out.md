---
title: SQL Database do Azure - leia consultas em réplicas | Documentos da Microsoft
description: A base de dados do SQL Azure fornece a capacidade de carregar saldo só de leitura cargas de trabalho com a capacidade de réplicas só de leitura - chamado escalável de leitura.
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
ms.date: 02/25/2019
ms.openlocfilehash: 5401c852decf0bcae3e86d1914b7cb6b47b4422a
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317514"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads-preview"></a>Utilizar réplicas só de leitura para carregar saldo consulta só de leitura cargas de trabalho (pré-visualização)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Escalamento leitura** permite-lhe carregar saldo base de dados SQL do Azure só de leitura cargas de trabalho com a capacidade de uma réplica só de leitura.

Cada base de dados no escalão Premium ([modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md)) ou no escalão crítico para a empresa ([modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md)) é automaticamente aprovisionada com várias réplicas AlwaysON para suporta o SLA de disponibilidade.

![Réplicas só de leitura](media/sql-database-managed-instance/business-critical-service-tier.png)

Essas réplicas são aprovisionadas com o mesmo tamanho de computação, como a réplica de leitura / escrita utilizado pelas conexões de banco de dados regulares. O **Escalamento leitura** funcionalidade permite-lhe carregar saldo base de dados SQL só de leitura cargas de trabalho com a capacidade de uma das réplicas só de leitura em vez de partilhar a réplica de leitura / escrita. Desta forma, a carga de trabalho só de leitura será isolada de carga de trabalho de leitura / escrita principal e não irá afetar o desempenho dele. O recurso foi desenvolvido para as aplicações que incluem logicamente separados cargas de trabalho só de leitura, tais como análises e isso foi possível obter os benefícios de desempenho com essa capacidade adicional na não custos adicionais.

Para utilizar a funcionalidade de expansão de leitura com determinada base de dados, tem de ativar explicitamente-lo ao criar a base de dados ou, depois, alterando a respetiva configuração com o PowerShell, invocando o [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) ou o [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlets ou por meio da API de REST do Azure Resource Manager com o [bases de dados - criar ou atualizar](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) método.

Depois de leitura horizontal é ativada para uma base de dados, ligar a esse banco de dados de aplicações serão direcionadas para a réplica de leitura / escrita ou para uma réplica só de leitura dessa base de dados em conformidade com o `ApplicationIntent` propriedade configurada do aplicativo cadeia de ligação. Para obter informações sobre o `ApplicationIntent` propriedade, veja [especificação de tipo de aplicação](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Se o Escalamento de leitura está desabilitado ou defina a propriedade ReadScale numa camada de serviço não suportado, todas as ligações sejam direcionadas para a réplica de leitura / escrita, independentemente do `ApplicationIntent` propriedade.

> [!NOTE]
> Consulta dados Store e do Extended Events não são suportadas nas réplicas só de leitura.

## <a name="data-consistency"></a>Consistência dos dados

Um dos benefícios de réplicas é que as réplicas estão sempre no estado consistente de forma transacional, mas em diferentes alturas no tempo pode haver alguma latência pequena entre diferentes réplicas. Leitura de aumento horizontal oferece suporte a consistência de nível de sessão. Isso significa que, se a sessão só de leitura volta a ligar após um erro de ligação causado por indisponibilidade de réplica, pode ser redirecionado para uma réplica que não é atualizado com a réplica de leitura / escrita de 100%. Da mesma forma, se uma aplicação escreve dados através de uma sessão de leitura / escrita e imediatamente lê-lo com uma sessão só de leitura, é possível que as atualizações mais recentes não são imediatamente visíveis. Isto acontece porque o retrabalho de log de transação para as réplicas é assíncrono.

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

## <a name="enable-and-disable-read-scale-out"></a>Ativar e desativar a expansão de leitura

Escalamento de leitura é ativado por predefinição no [instância gerida](sql-database-managed-instance.md) escalão crítico para a empresa. Deve ser explicitamente ativada na [base de dados colocada no servidor de base de dados SQL](sql-database-servers.md) escalões Premium e crítico para a empresa. Os métodos para ativar e desativar a expansão de leitura é descrito aqui.

### <a name="powershell-enable-and-disable-read-scale-out"></a>PowerShell: Ativar e desativar a expansão de leitura

A gestão de escalamento de leitura no Azure PowerShell requer a Dezembro de 2016 do Azure PowerShell versão ou mais recente. Para a versão mais recente do PowerShell, consulte [do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Ativar ou desativar a leitura Escalamento horizontal no Azure PowerShell, invocando o [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet e passando o valor desejado – `Enabled` ou `Disabled` – para o `-ReadScale` parâmetro. Em alternativa, pode utilizar o [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet para criar uma nova base de dados com ler expansão ativada.

Por exemplo, para ativar a leitura Escalamento horizontal para um existente da base de dados (substituindo os itens nos parênteses angulares com os valores corretos para o seu ambiente e remover os parênteses angulares):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

Para desativar o Escalamento leitura para uma base de dados existente (substituindo os itens nos parênteses angulares com os valores corretos para o seu ambiente e remover os parênteses angulares):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```

Para criar uma nova base de dados com aumento horizontal leitura ativado (substituindo os itens nos parênteses angulares com os valores corretos para o seu ambiente e remover os parênteses angulares):

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled -Edition Premium
```

### <a name="rest-api-enable-and-disable-read-scale-out"></a>REST API: Ativar e desativar a expansão de leitura

Para criar uma base de dados com leitura Escalamento ativado, ou para ativar ou desativar a leitura Escalamento horizontal para uma base de dados existente, criar ou atualizar a entidade de base de dados correspondente com o `readScale` definida como `Enabled` ou `Disabled` como no abaixo de exemplo pedido.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Enabled"
   }
}
```

Para obter mais informações, consulte [bases de dados - criar ou atualizar](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Usando o Escalamento de leitura com bancos de dados de georreplicação

Se estiver a utilizar o Escalamento horizontal leitura carregar saldo só de leitura cargas de trabalho num banco de dados que está a georreplicação (por exemplo, como um membro de um grupo de ativação pós-falha), certifique-se de que leitura Escalamento horizontal está ativado no principal e os georreplicado bases de dados secundárias. Isto irá garantir o mesmo efeito de balanceamento de carga quando seu aplicativo se conecta à nova principal após a ativação pós-falha. Se estiver a ligar para o georreplicada secundária da base de dados com escala de leitura ativada, as sessões com `ApplicationIntent=ReadOnly` serão encaminhados para uma das réplicas da mesma forma, podemos encaminhar as ligações na base de dados primária.  As sessões sem `ApplicationIntent=ReadOnly` serão encaminhados para a réplica primária da secundária georreplicada, que também é só de leitura. Porque a base de dados secundária georreplicada tem um ponto de extremidade diferente da base de dados primária, historicamente para acessar o secundário é não era necessária para definir `ApplicationIntent=ReadOnly`. Para garantir a compatibilidade com versões anteriores, `sys.geo_replication_links` DMV mostra `secondary_allow_connections=2` (qualquer ligação de cliente é permitida).

> [!NOTE]
> Round robin ou qualquer outra com balanceamento de carga encaminhamento entre as réplicas locais da base de dados secundária não é suportada.

## <a name="next-steps"></a>Passos Seguintes

- Para obter informações sobre como utilizar o PowerShell para definir a leitura de aumento horizontal, consulte a [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) ou o [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlets.
- Para obter informações sobre como utilizar a API REST para definir a leitura de aumento horizontal, consulte [bases de dados - criar ou atualizar](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).
