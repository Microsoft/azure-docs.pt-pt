---
title: Eliminar dados do Azure Data Explorer
description: Este artigo descreve a eliminação de cenários no Azure Data Explorer, incluindo purga, queda de extensões e eliminações baseadas em retenção.
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: dd0f8740d148a7817bcfe2fbad591ceeb1610d0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501423"
---
# <a name="delete-data-from-azure-data-explorer"></a>Eliminar dados do Azure Data Explorer

O Azure Data Explorer suporta vários cenários de eliminação descritos neste artigo. 

## <a name="delete-data-using-the-retention-policy"></a>Eliminar dados utilizando a política de retenção

O Azure Data Explorer elimina automaticamente os dados com base na política de [retenção.](/azure/kusto/management/retentionpolicy) Este método é a forma mais eficiente e livre de aborrecimentos de apagar dados. Desdefinir a política de retenção na base de dados ou no nível da tabela.

Considere uma base de dados ou tabela que esteja definida para 90 dias de retenção. Se forem necessários apenas 60 dias de dados, elimine os dados mais antigos da seguinte forma:

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>Eliminar dados diminuindo extensões

[A extensão (fragmento de dados)](/azure/kusto/management/extents-overview) é a estrutura interna onde os dados são armazenados. Cada extensão pode ter até milhões de registos. As extensões podem ser eliminadas individualmente ou como um grupo utilizando [comandos de extensão de queda.](/azure/kusto/management/extents-commands#drop-extents) 

### <a name="examples"></a>Exemplos

Pode eliminar todas as linhas numa mesa ou apenas numa extensão específica.

* Elimine todas as linhas numa tabela:

    ```kusto
    .drop extents from TestTable
    ```

* Eliminar uma extensão específica:

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>Eliminar linhas individuais usando purga

[A purga](/azure/kusto/concepts/data-purge) de dados pode ser usada para eliminar as linhas individuais. A eliminação não é imediata e requer recursos significativos do sistema. Como tal, é apenas aconselhado para cenários de conformidade.  

