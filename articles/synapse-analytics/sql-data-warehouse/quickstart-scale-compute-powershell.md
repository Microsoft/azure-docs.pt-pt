---
title: Computação em escala para piscina Synapse SQL (Azure PowerShell)
description: Pode escalar a computação para piscina Synapse SQL (armazém de dados) utilizando o Azure PowerShell.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c7ec8db212a24f1f23f393e4cb0e7f4150605a56
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350800"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-azure-powershell"></a>Quickstart: Scale compute for Synapse SQL pool com Azure PowerShell

Pode escalar a computação para piscina Synapse SQL (armazém de dados) utilizando o Azure PowerShell. [Dimensionar a computação](sql-data-warehouse-manage-compute-overview.md) para um melhor desempenho ou a escalar a computação novamente para reduzir os custos. 

Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este quickstart assume que já tem uma piscina SQL que pode escalar. Se precisar de criar um, use [o Portal Create and Connect para](create-data-warehouse-portal.md) criar um pool SQL chamado **mySampleDataWarehouse**.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na subscrição do Azure utilizando o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) e siga as instruções no ecrã.

```powershell
Connect-AzAccount
```

Para ver que subscrição está a utilizar, execute [a Subscrição Get-Az](/powershell/module/az.accounts/get-azsubscription).

```powershell
Get-AzSubscription
```

Se precisar de utilizar uma subscrição diferente da predefinida, execute o [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Procure pelas informações de armazém de dados

Localize o nome da base de dados, nome do servidor e grupo de recursos para o armazém de dados que planeia colocar em pausa e retomar.

Siga estes passos para encontrar as informações de localização para o seu armazém de dados.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique em **Azure Synapse Analytics (anteriormente SQL DW)** na página de navegação esquerda do portal Azure.
3. Selecione **mySampleDataWarehouse** da página **Azure Synapse Analytics (anteriormente SQL DW)** para abrir o armazém de dados.

    ![O nome do servidor e grupo de recursos](./media/quickstart-scale-compute-powershell/locate-data-warehouse-information.png)

4. Escreva o nome do armazém de dados, que vai ser utilizado como o nome da base de dados. Recorde-se que um armazém de dados é um tipo de base de dados. Anote também o nome do servidor e do grupo de recursos. Utilizará o nome do servidor e o nome do grupo de recursos na pausa e retomará os comandos.
5. Utilize apenas a primeira parte do nome do servidor nos cmdlets PowerShell. Na imagem anterior, o nome completo do servidor é sqlpoolservername.database.windows.net. Utilizamos o **nome de servidor sqlpoolcomo** nome de servidor no cmdlet PowerShell.

## <a name="scale-compute"></a>Dimensionar computação

No pool SQL, pode aumentar ou diminuir os recursos computacionais ajustando unidades de armazém de dados. O [Criar e Ligar - portal](create-data-warehouse-portal.md) criou **mySampleDataWarehouse** e inicializou-o com 400 DWUs. Os seguintes passos ajustam as DWUs para **mySampleDataWarehouse**.

Para alterar as unidades de armazém de dados, utilize o [cmdlet Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) PowerShell. O exemplo seguinte define as unidades de armazém de dados para DW300c para a base de dados **mySampleDataWarehouse**, que está hospedada no **grupo resourcegroup name** no **nome do servidor de**servidores do servidor .

```Powershell
Set-AzSqlDatabase -ResourceGroupName "resourcegroupname" -DatabaseName "mySampleDataWarehouse" -ServerName "sqlpoolservername" -RequestedServiceObjectiveName "DW300c"
```

## <a name="check-data-warehouse-state"></a>Verifique o estado do armazém de dados

Para ver o estado atual do armazém de dados, utilize o cmdlet [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) PowerShell. Este cmdlet mostra o estado da base de dados **mySampleDataWarehouse** em nome de grupo de **recursos** do ResourceGroup e **sqlpoolservername.database.windows.net servidor**.

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
Agora aprendeu a escalar a computação para piscina SQL. Para saber mais sobre o pool SQL, continue ao tutorial para carregar dados.

> [!div class="nextstepaction"]
>[Carregue os dados num pool SQL](load-data-from-azure-blob-storage-using-polybase.md)
