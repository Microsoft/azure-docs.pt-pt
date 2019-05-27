---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/27/2019
ms.author: tamram
ms.openlocfilehash: 9a60c624b181a1efd2f6deebd349daa82214a8a4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159752"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Gerir entidades da tabela

Agora que tem uma tabela, vamos analisar como gerir entidades ou linhas na tabela. 

Entidades podem ter até 255 propriedades, incluindo três propriedades do sistema: **PartitionKey**, **RowKey**, e **Timestamp**. É responsável por inserir e atualizar os valores dos **PartitionKey** e **RowKey**. O servidor gere o valor de **Timestamp**, que não pode ser modificado. Em conjunto a **PartitionKey** e **RowKey** identificam todas as entidades dentro de uma tabela.

* **PartitionKey**: Determina a partição que a entidade é armazenada no.
* **RowKey**: Identifica de forma exclusiva a entidade na partição.

Pode definir até 252 propriedades personalizadas para uma entidade. 

### <a name="add-table-entities"></a>Adicionar entidades de tabelas

Adicionar entidades a uma tabela com **Add-AzTableRow**. Estes exemplos utilizam chaves de partição com valores `partition1` e `partition2`, e as chaves de linha igual a abreviaturas de estado. As propriedades de cada entidade que estão `username` e `userid`. 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>Consultar as entidades da tabela

Pode consultar as entidades numa tabela utilizando a **Get-AzTableRow** comando.

> [!NOTE]
> Os cmdlets **Get-AzureStorageTableRowAll**, **Get-AzureStorageTableRowByPartitionKey**, **Get-AzureStorageTableRowByColumnName**, e  **Get-AzureStorageTableRowByCustomFilter** foram preteridos e será removida numa atualização futura versão.

#### <a name="retrieve-all-entities"></a>Obter todas as entidades

```powershell
Get-AzTableRow -table $cloudTable | ft
```

Este comando produz resultados semelhantes a tabela seguinte:

| userid | username | partição | rowkey |
|----|---------|---------------|----|
| 1 | Chris | Partition1 | AC |
| 3 | Christine | Partition1 | WA |
| 2 | Jessie | Partition2 | NM |
| 4 | Steven | Partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Obter entidades para uma partição específica

```powershell
Get-AzTableRow -table $cloudTable -partitionKey $partitionKey1 | ft
```

Os resultados ter um aspeto semelhantes a tabela seguinte:

| userid | username | partição | rowkey |
|----|---------|---------------|----|
| 1 | Chris | Partition1 | AC |
| 3 | Christine | Partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Obter entidades para um valor específico numa coluna de específico

```powershell
Get-AzTableRow -table $cloudTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Esta consulta devolve um registo.

|Campo|value|
|----|----|
| userid | 1 |
| username | Chris |
| PartitionKey | Partition1 |
| RowKey      | AC |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Obter entidades utilizando um filtro personalizado 

```powershell
Get-AzTableRow `
    -table $cloudTable `
    -customFilter "(userid eq 1)"
```

Esta consulta devolve um registo.

|Campo|value|
|----|----|
| userid | 1 |
| username | Chris |
| PartitionKey | Partition1 |
| RowKey      | AC |

### <a name="updating-entities"></a>A atualização de entidades 

Existem três passos para a atualização de entidades. Em primeiro lugar, obter a entidade para alterar. Em segundo lugar, efetue a alteração. Em terceiro lugar, consolidar a alteração usando **AzTableRow atualização**.

Atualizar a entidade com o nome de utilizador = Jessie ter o nome de utilizador = 'Jessie2'. Este exemplo também mostra outra forma de criar um filtro personalizado usando tipos .NET.

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2"

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzTableRow -table $cloudTable

# To see the new record, query the table.
Get-AzTableRow -table $cloudTable `
    -customFilter "(username eq 'Jessie2')"
```

Os resultados mostram o registo de Jessie2.

|Campo|value|
|----|----|
| userid | 2 |
| username | Jessie2 |
| PartitionKey | Partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>A eliminação de entidades da tabela

Pode eliminar uma entidade ou todas as entidades na tabela.

#### <a name="deleting-one-entity"></a>A eliminar uma entidade

Para eliminar uma entidade única, obter uma referência a essa entidade e encaminhá-la em **Remove-AzTableRow**.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter
$userToDelete | Remove-AzTableRow -table $cloudTable

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzTableRow -table $cloudTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>Eliminar todas as entidades da tabela

Para eliminar todas as entidades na tabela, recuperá-los e encaminhar os resultados para o cmdlet remove. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzTableRow `
    -table $cloudTable | Remove-AzTableRow -table $cloudTable 

# List entities in the table (there won't be any).
Get-AzTableRow -table $cloudTable | ft
```
