---
title: 'Início rápido: pausar & retomar computação-PowerShell '
description: Use o PowerShell para pausar a computação no Azure SQL Data Warehouse para economizar custos. Retome a computação quando estiver pronto para usar o data warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 03/20/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 148f3f99aac5ce01bd88f1efaecb5821ae94f50a
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539171"
---
# <a name="quickstart-pause-and-resume-compute-in-azure-sql-data-warehouse-with-azure-powershell"></a>Início rápido: pausar e retomar a computação no Azure SQL Data Warehouse com Azure PowerShell

Use Azure PowerShell para pausar a computação no Azure SQL Data Warehouse para economizar custos. [Retome a computação](sql-data-warehouse-manage-compute-overview.md) quando estiver pronto para usar o data warehouse.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este guia de início rápido pressupõe que você já tem um SQL Data Warehouse que você pode pausar e retomar. Se você precisar criar um, poderá usar [Create e Connect-portal](create-data-warehouse-portal.md) para criar um data warehouse chamado **mySampleDataWarehouse**.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Faça logon na sua assinatura do Azure usando o comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) e siga as instruções na tela.

```powershell
Connect-AzAccount
```

Para ver qual assinatura você está usando, execute [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```powershell
Get-AzSubscription
```

Se você precisar usar uma assinatura diferente da padrão, execute [set-AzContext](/powershell/module/az.accounts/set-azcontext).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Procure pelas informações de armazém de dados

Localize o nome da base de dados, nome do servidor e grupo de recursos para o armazém de dados que planeia colocar em pausa e retomar.

Siga estes passos para encontrar as informações de localização para o seu armazém de dados.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique em **bases de dados SQL** na página da esquerda do Portal do Azure.
3. Selecione **mySampleDataWarehouse** da página de **bases de dados SQL**. O armazém de dados é aberto.

    ![O nome do servidor e grupo de recursos](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Anote o nome do data warehouse, que é o nome do banco de dados. Anote também o nome do servidor e do grupo de recursos.
6. Se o servidor for foo.database.windows.net, utilize apenas a primeira parte como nome de servidor nos cmdlets do PowerShell. Na imagem anterior, o nome completo do servidor é newserver-20171113.database.windows.net. Remova o sufixo e use **NewServer-20171113** como o nome do servidor no cmdlet do PowerShell.

## <a name="pause-compute"></a>Pausar computação

Para economizar custos, você pode pausar e retomar os recursos de computação sob demanda. Por exemplo, se você não estiver usando o banco de dados durante a noite e nos finais de semana, poderá pausá-lo durante esses horários e retomá-lo durante o dia. Não há nenhum custo para os recursos de computação enquanto o banco de dados está em pausa. No entanto, você continua a ser cobrado pelo armazenamento.

Para pausar um banco de dados, use o cmdlet [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase) . O exemplo a seguir pausa um data warehouse chamado **mySampleDataWarehouse** hospedado em um servidor chamado **NewServer-20171113**. O servidor está em um grupo de recursos do Azure chamado **MyResource**Group.


```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
```

Uma variação, este exemplo a seguir recupera o banco de dados no objeto $database. Em seguida, ele canaliza o objeto para [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase). Os resultados são armazenados no objeto resultDatabase. O comando final mostra os resultados.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```


## <a name="resume-compute"></a>Retomar computação

Para iniciar um banco de dados, use o cmdlet [resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase) . O exemplo a seguir inicia um banco de dados chamado mySampleDataWarehouse hospedado em um servidor chamado NewServer-20171113. O servidor está em um grupo de recursos do Azure chamado MyResource Group.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" -DatabaseName "mySampleDataWarehouse"
```

Uma variação, este exemplo a seguir recupera o banco de dados no objeto $database. Em seguida, ele canaliza o objeto para [resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase) e armazena os resultados em $resultDatabase. O comando final mostra os resultados.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-data-warehouse-operation"></a>Verificar o status da operação de data warehouse

Para verificar o status do seu data warehouse, use o cmdlet [Get-AzSqlDatabaseActivity](https://docs.microsoft.com/powershell/module/az.sql/Get-AzSqlDatabaseActivity#description) .

```
Get-AzSqlDatabaseActivity -ResourceGroupName "ResourceGroup01" -ServerName "Server01" -DatabaseName "Database02"
```

## <a name="clean-up-resources"></a>Limpar recursos

Estão a ser-lhe cobradas as unidades do armazém de dados e os dados armazenados no mesmo. Estes recursos de computação e armazenamento são faturados em separado.

- Se você quiser manter os dados no armazenamento, Pause a computação.
- Se quiser remover futuras cobranças, pode eliminar o armazém de dados.

Siga estes passos para limpar os recursos conforme quiser.

1. Entre no [portal do Azure](https://portal.azure.com)e clique em seu data warehouse.

    ![Limpar recursos](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Para interromper a computação, clique no botão **Pausar**. Quando o armazém de dados estiver em pausa, verá um botão **Iniciar**.  Para retomar a computação, clique em **Iniciar**.

3. Para remover o armazém de dados para não lhe ser cobrada a computação ou o armazenamento, clique em **Eliminar**.

4. Para remover o SQL Server que você criou, clique em **mynewserver-20171113.Database.Windows.net**e em **excluir**.  Tenha cuidado com esta eliminação, uma vez que eliminar o servidor também elimina todas as bases de dados atribuídas ao mesmo.

5. Para remover o grupo de recursos, clique em **myResourceGroup** e, em seguida, clique em **Eliminar grupo de recursos**.


## <a name="next-steps"></a>Passos seguintes

Agora você está pausado e retomou a computação para seu data warehouse. Para saber mais sobre o Azure SQL Data Warehouse, avance para o tutorial para carregar dados.

> [!div class="nextstepaction"]
> [Carregar dados em um SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
