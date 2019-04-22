---
title: Criar e gerir recursos do Azure Cosmos DB com o PowerShell
description: Utilize o Azure Powershell gerir as contas do Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 01c351ad08399c0b42e831e325b3f818741d1d83
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58904377"
---
# <a name="manage-azure-cosmos-resources-using-powershell"></a>Gerir recursos do Cosmos do Azure com o PowerShell

O guia seguinte descreve os comandos para automatizar a gestão das suas contas de base de dados do Azure Cosmos DB com o Azure Powershell. Ele também inclui comandos para gerir as chaves da conta e as prioridades de ativação pós-falha no [contas de base de dados de várias regiões][distribute-data-globally]. A atualizar a sua conta de base de dados permite-lhe modificar as políticas de consistência e adicionar/remover regiões. Para a gestão de várias plataformas da sua conta do Azure Cosmos DB, pode usar [CLI do Azure](cli-samples.md), o [API de REST do fornecedor de recursos][rp-rest-api], ou o [portal do Azure ](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Introdução

Siga as instruções em [como instalar e configurar o Azure PowerShell] [ powershell-install-configure] para instalar e iniciar sessão na sua conta do Azure Resource Manager no Powershell.

### <a name="notes"></a>Notas

* Se desejar executar os seguintes comandos sem a necessidade de confirmação do usuário, acrescentar o `-Force` sinalizador para o comando.
* Todos os comandos seguintes são síncronos.

## <a id="create-documentdb-account-powershell"></a> Criar uma conta do Azure Cosmos DB

Este comando permite-lhe criar uma conta de base de dados do Azure Cosmos DB. Configurar a sua nova conta de base de dados como uma única região ou [multirregião] [ distribute-data-globally] com uma determinada [política de consistência](consistency-levels.md).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name>  -Location "<resource-group-location>" -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` O nome da localização da região de escrita da conta de base de dados. Esta localização é necessária para ter um valor de prioridade de ativação pós-falha de 0. Tem de existir exatamente uma região de escrita por conta de base de dados.
* `<read-region-location>` O nome da localização da região de leitura da conta de base de dados. Esta localização é necessária para ter um valor de prioridade de ativação pós-falha de maior que 0. Podem existir mais do que um regiões de leitura por conta de base de dados.
* `<ip-range-filter>` Especifica o conjunto de endereços IP ou intervalos de endereços IP no formulário CIDR para serem incluídos como a lista permitida de IPs de cliente para uma conta de base de dados específica. Endereços/intervalos IP tem de ser vírgula separados e não pode conter quaisquer espaços. Para obter mais informações, consulte [suporte de Firewall do Azure Cosmos DB](firewall-support.md)
* `<default-consistency-level>` O nível de consistência predefinida da conta do Azure Cosmos DB. Para obter mais informações, consulte [níveis de consistência no Azure Cosmos DB](consistency-levels.md).
* `<max-interval>` Quando utilizado com consistência de estagnação limitada, este valor representa a quantidade de tempo de envelhecimento (em segundos) pela tolerar. Intervalo aceite para este valor é 1 e 100.
* `<max-staleness-prefix>` Quando utilizado com consistência de estagnação limitada, este valor representa o número de pedidos obsoletos pela tolerar. Intervalo aceite para este valor é 1 – 2 147 483 647.
* `<resource-group-name>` O nome da [grupo de recursos do Azure] [ azure-resource-groups] para que a nova conta de base de dados do Azure Cosmos DB pertence.
* `<resource-group-location>` A localização do grupo de recursos do Azure para o qual a nova conta de base de dados do Azure Cosmos DB pertence.
* `<database-account-name>` O nome da conta de base de dados do Azure Cosmos DB a ser criada. Só pode utilizar letras minúsculas, números, o '-' de carateres e tem de ter entre 3 e 50 carateres.

Exemplo: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Location "West US" -Name "docdb-test" -Properties $CosmosDBProperties

### <a name="notes"></a>Notas
* O exemplo anterior cria uma conta de base de dados com duas regiões. Também é possível criar uma conta de base de dados com uma região (que é designada como a região de escrita e tem um valor de prioridade de ativação pós-falha de 0) ou mais de duas regiões. Para obter mais informações, consulte [contas de base de dados de várias regiões][distribute-data-globally].
* As localizações tem de ser em que regiões do Azure Cosmos DB está disponível em geral. A lista atual de regiões é fornecida no [página de regiões do Azure](https://azure.microsoft.com/regions/#services).

## <a id="update-documentdb-account-powershell"></a> Atualizar uma conta de base de dados do Azure Cosmos DB

Este comando permite-lhe atualizar as propriedades da conta de base de dados do Azure Cosmos DB. Isto inclui a política de consistência e as localizações que a conta de base de dados existe no.

> [!NOTE]
> Este comando permite-lhe adicionar e remover regiões, mas não permite-lhe modificar prioridades de ativação pós-falha. Para modificar as prioridades de ativação pós-falha, consulte [abaixo](#modify-failover-priority-powershell).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name> -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` O nome da localização da região de escrita da conta de base de dados. Esta localização é necessária para ter um valor de prioridade de ativação pós-falha de 0. Tem de existir exatamente uma região de escrita por conta de base de dados.
* `<read-region-location>` O nome da localização da região de leitura da conta de base de dados. Esta localização é necessária para ter um valor de prioridade de ativação pós-falha de maior que 0. Podem existir mais do que um regiões de leitura por conta de base de dados.
* `<default-consistency-level>` O nível de consistência predefinida da conta do Azure Cosmos DB. Para obter mais informações, consulte [níveis de consistência no Azure Cosmos DB](consistency-levels.md).
* `<ip-range-filter>` Especifica o conjunto de endereços IP ou intervalos de endereços IP no formulário CIDR para serem incluídos como a lista permitida de IPs de cliente para uma conta de base de dados específica. Endereços/intervalos IP tem de ser vírgula separados e não pode conter quaisquer espaços. Para obter mais informações, consulte [suporte de Firewall do Azure Cosmos DB](firewall-support.md)
* `<max-interval>` Quando utilizado com consistência de estagnação limitada, este valor representa a quantidade de tempo de envelhecimento (em segundos) pela tolerar. Intervalo aceite para este valor é 1 e 100.
* `<max-staleness-prefix>` Quando utilizado com consistência de estagnação limitada, este valor representa o número de pedidos obsoletos pela tolerar. Intervalo aceite para este valor é 1 – 2 147 483 647.
* `<resource-group-name>` O nome da [grupo de recursos do Azure] [ azure-resource-groups] para que a nova conta de base de dados do Azure Cosmos DB pertence.
* `<resource-group-location>` A localização do grupo de recursos do Azure para o qual a nova conta de base de dados do Azure Cosmos DB pertence.
* `<database-account-name>` O nome da conta de base de dados do Azure Cosmos DB, a ser atualizado.

Exemplo: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Properties $CosmosDBProperties

## <a id="delete-documentdb-account-powershell"></a> Eliminar uma conta de base de dados do Azure Cosmos DB

Este comando permite-lhe eliminar uma conta de base de dados existente do Azure Cosmos DB.

    Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"
    
* `<resource-group-name>` O nome da [grupo de recursos do Azure] [ azure-resource-groups] para que a nova conta de base de dados do Azure Cosmos DB pertence.
* `<database-account-name>` O nome da conta de base de dados do Azure Cosmos DB a ser eliminada.

Exemplo:

    Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="get-documentdb-properties-powershell"></a> Obter propriedades de uma conta de base de dados do Azure Cosmos DB

Este comando permite-lhe obter as propriedades de uma conta de base de dados existente do Azure Cosmos DB.

    Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` O nome da [grupo de recursos do Azure] [ azure-resource-groups] para que a nova conta de base de dados do Azure Cosmos DB pertence.
* `<database-account-name>` O nome da conta de base de dados do Azure Cosmos DB.

Exemplo:

    Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="update-tags-powershell"></a> Etiquetas de atualização de uma conta de base de dados do Azure Cosmos DB

O exemplo a seguir descreve como definir [etiquetas de recursos do Azure] [ azure-resource-tags] para o Azure Cosmos DB conta de base de dados.

> [!NOTE]
> Este comando pode ser combinado com os comandos de criar ou atualizar, acrescentando a `-Tags` sinalizador com o parâmetro correspondente.

Exemplo:

    $tags = @{"dept" = "Finance"; environment = "Production"}
    Set-AzResource -ResourceType "Microsoft.DocumentDB/databaseAccounts"  -ResourceGroupName "rg-test" -Name "docdb-test" -Tags $tags

## <a id="list-account-keys-powershell"></a> Listar chaves de conta

Quando cria uma conta do Azure Cosmos DB, o serviço gera duas chaves de acesso mestre que podem ser utilizadas para autenticação quando aceder a conta do Azure Cosmos DB. Ao fornecer duas chaves de acesso, o Azure Cosmos DB permite-lhe voltar a gerar as chaves sem interrupções na sua conta do Azure Cosmos DB. Chaves só de leitura para a autenticação de operações só de leitura também estão disponíveis. Existem duas chaves de leitura / escrita (primárias e secundárias) e duas chaves só de leitura (primário e secundário).

    $keys = Invoke-AzResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` O nome da [grupo de recursos do Azure] [ azure-resource-groups] para que a nova conta de base de dados do Azure Cosmos DB pertence.
* `<database-account-name>` O nome da conta de base de dados do Azure Cosmos DB.

Exemplo:

    $keys = Invoke-AzResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="list-connection-strings-powershell"></a> Lista cadeias de ligação

Para contas de MongoDB, a cadeia de ligação para ligar a sua aplicação MongoDB à conta de base de dados pode ser obtida com o seguinte comando.

    $keys = Invoke-AzResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` O nome da [grupo de recursos do Azure] [ azure-resource-groups] para que a nova conta de base de dados do Azure Cosmos DB pertence.
* `<database-account-name>` O nome da conta de base de dados do Azure Cosmos DB.

Exemplo:

    $keys = Invoke-AzResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="regenerate-account-key-powershell"></a> Regenerar chave da conta

Deve alterar as chaves de acesso à sua conta do Azure Cosmos DB periodicamente para ajudar a manter as suas ligações mais segura. Duas chaves de acesso são atribuídas para que possa manter as ligações à conta do Azure Cosmos DB através de uma chave de acesso enquanto volta a gerar a chave de acesso.

    Invoke-AzResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"keyKind"="<key-kind>"}

* `<resource-group-name>` O nome da [grupo de recursos do Azure] [ azure-resource-groups] para que a nova conta de base de dados do Azure Cosmos DB pertence.
* `<database-account-name>` O nome da conta de base de dados do Azure Cosmos DB.
* `<key-kind>` Um dos quatro tipos de chaves: ["Primário" | " Secundário "|" PrimaryReadonly "|" SecondaryReadonly"] que gostaria de voltar a gerar.

Exemplo:

    Invoke-AzResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"keyKind"="Primary"}

## <a id="modify-failover-priority-powershell"></a> Modificar a prioridade de ativação pós-falha de uma conta de base de dados do Azure Cosmos DB

Para contas de base de dados de várias regiões, pode alterar a prioridade de ativação pós-falha das várias regiões que a conta de base de dados do Azure Cosmos DB existe no. Para obter mais informações sobre a ativação pós-falha na sua conta de base de dados do Azure Cosmos DB, consulte [distribuir dados globalmente com o Azure Cosmos DB][distribute-data-globally].

    $failoverPolicies = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0},@{"locationName"="<read-region-location>"; "failoverPriority"=1})
    Invoke-AzResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"failoverPolicies"=$failoverPolicies}

* `<write-region-location>` O nome da localização da região de escrita da conta de base de dados. Esta localização é necessária para ter um valor de prioridade de ativação pós-falha de 0. Tem de existir exatamente uma região de escrita por conta de base de dados.
* `<read-region-location>` O nome da localização da região de leitura da conta de base de dados. Esta localização é necessária para ter um valor de prioridade de ativação pós-falha de maior que 0. Podem existir mais do que um regiões de leitura por conta de base de dados.
* `<resource-group-name>` O nome da [grupo de recursos do Azure] [ azure-resource-groups] para que a nova conta de base de dados do Azure Cosmos DB pertence.
* `<database-account-name>` O nome da conta de base de dados do Azure Cosmos DB.

Exemplo:

    $failoverPolicies = @(@{"locationName"="East US"; "failoverPriority"=0},@{"locationName"="West US"; "failoverPriority"=1})
    Invoke-AzResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"failoverPolicies"=$failoverPolicies}

## <a name="next-steps"></a>Passos Seguintes

* Para ligar através do .NET, veja [ligar e consultar com .NET](create-sql-api-dotnet.md).
* Para ligar com node. js, veja [ligar e consultar com node. js e uma aplicação MongoDB](create-mongodb-nodejs.md).

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
