---
title: Introdução ao Azure Data Lake Analytics com o Azure PowerShell
description: Utilize o Azure PowerShell para criar uma conta do Azure Data Lake Analytics e submeta uma tarefa de U-SQL.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.topic: conceptual
ms.date: 05/04/2017
ms.openlocfilehash: f74ebb4e36f9648b2f78e968877a9ef861888af8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58133446"
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Introdução ao Azure Data Lake Analytics com o Azure PowerShell
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Saiba como utilizar o Azure PowerShell para criar contas do Azure Data Lake Analytics e, em seguida, submeter e executar tarefas de U-SQL. Para mais informações sobre a Data Lake Analytics, consulte [Descrição geral da Análise do Azure Data Lake](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Antes de começar este tutorial, tem de ter as seguintes informações:

* **Uma conta do Azure Data Lake Analytics**. Consulte [Introdução ao Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).
* **Uma estação de trabalho com o Azure PowerShell**. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Este tutorial pressupõe que já esteja familiarizado com a utilização do Azure PowerShell. Em especial, precisa de saber como iniciar sessão no Azure. Se precisar de ajuda, consulte [Get started with Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) (Introdução ao Azure PowerShell).

Iniciar sessão com um nome de subscrição:

```
Connect-AzAccount -SubscriptionName "ContosoSubscription"
```

Em vez do nome de subscrição, também pode utilizar um D de subscrição para iniciar sessão:

```
Connect-AzAccount -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

Se for bem-sucedido, a saída desse comando tem o aspeto do seguinte texto:

```
Environment           : AzureCloud
Account               : joe@contoso.com
TenantId              : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionId        : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionName      : ContosoSubscription
CurrentStorageAccount :
```

## <a name="preparing-for-the-tutorial"></a>Preparação para o tutorial

Os fragmentos de código do PowerShell neste tutorial utilizam estas variáveis para armazenar estas informações:

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeStoreAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Obter informações sobre uma conta do Data Lake Analytics

```
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>Submeter uma tarefa de U-SQL

Crie uma variável do PowerShell para manter o script U-SQL.

```
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();

"@
```

Submeta o texto do script com o cmdlet `Submit-AdlJob` e os parâmetros `-Script`.

```
$job = Submit-AdlJob -Account $adla -Name "My Job" -Script $script
```

Como alternativa, pode submeter um ficheiro de script com o parâmetro `-ScriptPath`:

```
$filename = "d:\test.usql"
$script | out-File $filename
$job = Submit-AdlJob -Account $adla -Name "My Job" -ScriptPath $filename
```

Obtenha o estado de uma tarefa com `Get-AdlJob`. 

```
$job = Get-AdlJob -Account $adla -JobId $job.JobId
```

Em vez de chamar Get-AdlJob repetidamente até que uma tarefa seja concluída, utilize o cmdlet `Wait-AdlJob`.

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

Transfira o ficheiro de saída com `Export-AdlStoreItem`.

```
Export-AdlStoreItem -Account $adls -Path "/data.csv" -Destination "C:\data.csv"
```

## <a name="see-also"></a>Consulte também
* Para ver o mesmo tutorial, utilizando outras ferramentas, clique nos seletores de separador no topo da página.
* Para saber mais sobre U-SQL, consulte [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para tarefas de gestão, veja [Manage Azure Data Lake Analytics using Azure portal (Gerir o Azure Data Lake Analytics com o Portal do Azure)](data-lake-analytics-manage-use-portal.md).
