---
title: Cálculo de escala para piscina Sinaapse SQL (Azure PowerShell)
description: Pode escalar o cálculo para a piscina SYNAPSE SQL (data warehouse) utilizando a Azure PowerShell.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.openlocfilehash: 044b1b145f95e81b2aa2474950d4961131c3c88a
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079732"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-azure-powershell"></a>Quickstart: Computação em escala para piscina Sinaapse SQL com Azure PowerShell

Pode escalar o cálculo para a piscina SYNAPSE SQL (data warehouse) utilizando a Azure PowerShell. [Dimensionar a computação](sql-data-warehouse-manage-compute-overview.md) para um melhor desempenho ou a escalar a computação novamente para reduzir os custos.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="before-you-begin"></a>Before you begin

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este quickstart assume que já tem uma piscina SQL que pode escalar. Se precisar de criar um, utilize o [portal Create and Connect -](create-data-warehouse-portal.md) para criar uma piscina SQL chamada **mySampleDataWarehouse**.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição Azure utilizando o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e siga as instruções no ecrã.

```powershell
Connect-AzAccount
```

Para ver qual a subscrição que está a utilizar, execute [a Subscrição Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Get-AzSubscription
```

Se precisar de utilizar uma subscrição diferente do padrão, executar [Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Procure pelas informações de armazém de dados

Localize o nome da base de dados, nome do servidor e grupo de recursos para o armazém de dados que planeia colocar em pausa e retomar.

Siga estes passos para encontrar as informações de localização para o seu armazém de dados.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique em **Azure Synapse Analytics (anteriormente SQL DW)** na página de navegação esquerda do portal Azure.
3. Selecione **mySampleDataWarehouse** a partir da página **Azure Synapse Analytics (anteriormente SQL DW)** para abrir o armazém de dados.

    ![O nome do servidor e grupo de recursos](./media/quickstart-scale-compute-powershell/locate-data-warehouse-information.png)

4. Escreva o nome do armazém de dados, que vai ser utilizado como o nome da base de dados. Recorde-se que um armazém de dados é um tipo de base de dados. Anote também o nome do servidor e do grupo de recursos. Utilizará o nome do servidor e o nome do grupo de recursos nos comandos de pausa e currículo.
5. Utilize apenas a primeira parte do nome do servidor nos cmdlets PowerShell. Na imagem anterior, o nome completo do servidor é sqlpoolservername.database.windows.net. Usamos **o nome sqlpoolserver como** o nome do servidor no cmdlet PowerShell.

## <a name="scale-compute"></a>Dimensionar computação

Na piscina SQL, pode aumentar ou diminuir os recursos computativos ajustando as unidades de armazém de dados. O [Criar e Ligar - portal](create-data-warehouse-portal.md) criou **mySampleDataWarehouse** e inicializou-o com 400 DWUs. Os seguintes passos ajustam as DWUs para **mySampleDataWarehouse**.

Para alterar as unidades de armazém de dados, utilize o [cmdlet PowerShell Set-AzSqlDatabase.](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) O exemplo a seguir define as unidades de armazém de dados para DW300c para a base de **dados mySampleDataWarehouse,** que está hospedada no **grupo de recursos** de recursos do grupo de recursos no nome de servidor **sqlpoolservername**.

```Powershell
Set-AzSqlDatabase -ResourceGroupName "resourcegroupname" -DatabaseName "mySampleDataWarehouse" -ServerName "sqlpoolservername" -RequestedServiceObjectiveName "DW300c"
```

## <a name="check-data-warehouse-state"></a>Verifique o estado do armazém de dados

Para ver o estado atual do armazém de dados, utilize o [cmdlet Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell. Este cmdlet mostra o estado da base de dados **mySampleDataWarehouse** no grupo de **recursos** do Grupo de Recursos e **sqlpoolservername.database.windows.net de**servidor .

```powershell
$database = Get-AzSqlDatabase -ResourceGroupName resourcegroupname -ServerName sqlpoolservername -DatabaseName mySampleDataWarehouse
```

Que resulta em algo como:

```powershell
ResourceGroupName             : resourcegroupname
ServerName                    : sqlpoolservername
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300c
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/resourcegroupname/providers/Microsoft.Sql/servers/sqlpoolservername/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

Pode ver o **Estado** da base de dados na saída. Neste caso, pode ver que esta base de dados está online.  Quando executar este comando, deve receber um valor de Estado de Online, A Colocar em Pausa, A Retomar, A Dimensionar ou Em Pausa.

Para ver o estado por si só, utilize o seguinte comando:

```powershell
$database | Select-Object DatabaseName,Status
```

## <a name="next-steps"></a>Passos seguintes

Agora aprendeu a escalar a computação para a piscina SQL. Para saber mais sobre a piscina SQL, continue ao tutorial para carregar dados.

> [!div class="nextstepaction"]
>[Carregue os dados numa piscina SQL](load-data-from-azure-blob-storage-using-polybase.md)
