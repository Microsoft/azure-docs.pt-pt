---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/27/2019
ms.author: tamram
ms.openlocfilehash: 9a60c624b181a1efd2f6deebd349daa82214a8a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183934"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Gestão de entidades de tabela

Agora que tem uma mesa, vamos ver como gerir entidades, ou filas, na mesa. 

As entidades podem ter até 255 propriedades, incluindo três propriedades do sistema: **PartitionKey,** **RowKey,** e **Timestamp**. É responsável por inserir e atualizar os valores de **PartitionKey** e **RowKey**. O servidor gere o valor da **marca**temporal, que não pode ser modificada. Juntos, o **PartitionKey** e **o RowKey** identificam exclusivamente todas as entidades dentro de uma tabela.

* **PartitionKey**: Determina a partição em que a entidade está armazenada.
* **RowKey**: Identifica exclusivamente a entidade dentro da partição.

Pode definir até 252 propriedades personalizadas para uma entidade. 

### <a name="add-table-entities"></a>Adicionar entidades de tabela

Adicione entidades a uma tabela utilizando **Add-AzTableRow**. Estes exemplos usam chaves `partition1` `partition2`de partição com valores e, e chaves de fila iguais às abreviaturas do estado. As propriedades em `username` cada `userid`entidade são e . 

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

### <a name="query-the-table-entities"></a>Consulta às entidades de mesa

Pode consultar as entidades numa tabela utilizando o comando **Get-AzTableRow.**

> [!NOTE]
> Os cmdlets **Get-AzureStorageTableRowAll**, **Get-AzureStorageTableRowByPartitionKey,** **Get-AzureStorageTableRowByColumnName**e **Get-AzureStorageTableRowByCustomFilter** estão deprecíceados e serão removidos numa futura atualização da versão.

#### <a name="retrieve-all-entities"></a>Recuperar todas as entidades

```powershell
Get-AzTableRow -table $cloudTable | ft
```

Este comando produz resultados semelhantes ao quadro seguinte:

| usado | o nome de utilizador | partição | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partição1 | CA |
| 3 | Christine | partição1 | WA |
| 2 | Jessie | partição2 | NM |
| 4 | Steven | partição2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Recuperar entidades para uma partição específica

```powershell
Get-AzTableRow -table $cloudTable -partitionKey $partitionKey1 | ft
```

Os resultados são semelhantes ao quadro seguinte:

| usado | o nome de utilizador | partição | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partição1 | CA |
| 3 | Christine | partição1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Recuperar entidades por um valor específico numa coluna específica

```powershell
Get-AzTableRow -table $cloudTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Esta consulta recupera um recorde.

|campo|valor|
|----|----|
| usado | 1 |
| o nome de utilizador | Chris |
| PartitionKey | partição1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Recuperar entidades usando um filtro personalizado 

```powershell
Get-AzTableRow `
    -table $cloudTable `
    -customFilter "(userid eq 1)"
```

Esta consulta recupera um recorde.

|campo|valor|
|----|----|
| usado | 1 |
| o nome de utilizador | Chris |
| PartitionKey | partição1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Entidades de atualização 

Há três passos para atualizar as entidades. Primeiro, recuperar a entidade para mudar. Segundo, faça a mudança. Terceiro, comprometa a alteração utilizando **o Update-AzTableRow**.

Atualize a entidade com o nome de utilizador = 'Jessie' para ter o nome de utilizador = 'Jessie2'. Este exemplo também mostra outra forma de criar um filtro personalizado utilizando tipos .NET.

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

Os resultados mostram o disco da Jessie2.

|campo|valor|
|----|----|
| usado | 2 |
| o nome de utilizador | Jessie2 |
| PartitionKey | partição2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Entidades de mesa de adesão

Pode eliminar uma entidade ou todas as entidades da tabela.

#### <a name="deleting-one-entity"></a>Apagar uma entidade

Para eliminar uma única entidade, obtenha uma referência a essa entidade e encaneie-a em **Remove-AzTableRow**.

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

Para eliminar todas as entidades da tabela, recupere-as e encaneie os resultados para a remoção do cmdlet. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzTableRow `
    -table $cloudTable | Remove-AzTableRow -table $cloudTable 

# List entities in the table (there won't be any).
Get-AzTableRow -table $cloudTable | ft
```
