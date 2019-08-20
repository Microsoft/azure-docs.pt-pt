---
title: Atualizar para a última geração do Azure SQL Data Warehouse | Microsoft Docs
description: Atualize o Azure SQL Data Warehouse para a última geração de arquitetura de armazenamento e hardware do Azure.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.openlocfilehash: 2864e3d29a0beccd2ef52732a85ea1495e1efab8
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575299"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Otimizar o desempenho ao atualizar o SQL Data Warehouse

Atualize o Azure SQL Data Warehouse para a última geração de arquitetura de armazenamento e hardware do Azure.

## <a name="why-upgrade"></a>Por que atualizar?

Agora você pode atualizar diretamente para a camada Gen2 otimizada de computação SQL Data Warehouse no portal do Azure para [regiões com suporte](gen2-migration-schedule.md#automated-schedule-and-region-availability-table). Se sua região não oferecer suporte à autoatualização, você poderá atualizar para uma região com suporte ou esperar que a autoatualização esteja disponível em sua região. Atualize agora para aproveitar a última geração de hardware do Azure e arquitetura de armazenamento avançada, incluindo desempenho mais rápido, escalabilidade mais alta e armazenamento de coluna ilimitado. 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>Aplica-se a

Essa atualização se aplica aos data warehouses da camada Gen1 otimizada de computação em [regiões com suporte](gen2-migration-schedule.md#automated-schedule-and-region-availability-table).

## <a name="before-you-begin"></a>Antes de começar

1. Verifique se sua [região](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) tem suporte para a migração de Gen1 para Gen2. Observe as datas de migração automática. Para evitar conflitos com o processo automatizado, planeje a migração manual antes da data de início do processo automatizado.
2. Se você estiver em uma região que ainda não tem suporte, continue a verificar sua região para ser adicionada ou [atualizada usando restaurar](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal) em uma região com suporte.
3. Se houver suporte para sua região, [atualize por meio do portal do Azure](#upgrade-in-a-supported-region-using-the-azure-portal)
4. **Selecione o nível de desempenho sugerido** para o data warehouse com base em seu nível de desempenho atual na camada Gen1 otimizada de computação usando o mapeamento abaixo:

   | Camada Gen1 otimizada de computação | Camada Gen2 otimizada de computação |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c            |
   |            DW600            |           DW500c            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

> [!Note]
> Os níveis de desempenho sugeridos não são uma conversão direta. Por exemplo, é recomendável ir de DW600 para DW500c.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Atualizar em uma região com suporte usando o portal do Azure

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!NOTE]
> A migração de Gen1 para Gen2 por meio da portal do Azure é permanente. Não há um processo para retornar ao Gen1.  

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Se a camada Gen1 otimizada de computação data warehouse a ser atualizada estiver em pausa, [retome o data warehouse](pause-and-resume-compute-portal.md).

   > [!NOTE]
   > O Azure SQL Data Warehouse deve estar em execução para migrar para o Gen2.

2. Esteja preparado por alguns minutos de tempo de inatividade. 

3. Identifique as referências de código para calcular os níveis de desempenho otimizados do Gen1 e modifique-os para seu nível de desempenho Gen2 otimizado de computação equivalente. Abaixo estão dois exemplos de onde você deve atualizar as referências de código antes de atualizar:

   Comando original do PowerShell do Gen1:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Modificado para:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE] 
   > -RequestedServiceObjectiveName "DW300" é alterado para-RequestedServiceObjectiveName "DW300**c**"
   >

   Comando T-SQL Gen1 original:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   Modificado para:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ; 
   ```
   > [!NOTE] 
   > SERVICE_OBJECTIVE = ' DW300 ' é alterado para SERVICE_OBJECTIVE = ' DW300**C'**

## <a name="start-the-upgrade"></a>Iniciar a atualização

1. Vá para a camada Gen1 otimizada de computação data warehouse na portal do Azure. Se a camada Gen1 otimizada de computação data warehouse a ser atualizada estiver em pausa, [retome o data warehouse](pause-and-resume-compute-portal.md). 
2. Selecione **atualizar para** o cartão Gen2 na guia tarefas:  ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)
    
    > [!NOTE]
    > Se você não vir a **atualização para** o cartão Gen2 na guia tarefas, o tipo de assinatura será limitado na região atual.
    > [Envie um tíquete de suporte](sql-data-warehouse-get-started-create-support-ticket.md) para colocar sua assinatura na lista de permissões.

3. Verifique se sua carga de trabalho concluiu a execução e foi desativada antes da atualização. Você experimentará tempo de inatividade por alguns minutos antes que seu data warehouse esteja novamente online como uma camada de Gen2 otimizada de computação data warehouse. **Selecione Atualizar**:

   ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. **Monitore sua atualização** verificando o status no portal do Azure:

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)

   A primeira etapa do processo de atualização passa pela operação de dimensionamento ("Atualizando-offline"), em que todas as sessões serão eliminadas e as conexões serão descartadas. 

   A segunda etapa do processo de atualização é a migração de dados ("Atualizando-online"). A migração de dados é um processo de segundo plano do trickle online. Esse processo move lentamente os dados de coluna da arquitetura de armazenamento antiga para a nova arquitetura de armazenamento usando um cache SSD local. Durante esse tempo, seu data warehouse estará online para consulta e carregamento. Seus dados estarão disponíveis para consulta, independentemente de terem sido migrados ou não. A migração de dados ocorre em taxas variadas, dependendo do tamanho dos dados, do nível de desempenho e do número de segmentos columnstore. 

5. **Recomendação opcional:** Quando a operação de dimensionamento for concluída, você poderá acelerar o processo em segundo plano de migração de dados. Você pode forçar a movimentação de dados executando [ALTER INDEX REBUILD](sql-data-warehouse-tables-index.md) em todas as tabelas columnstore primárias que você estaria consultando em uma classe de recurso e SLO maior. Esta operação está **offline** em comparação com o processo de segundo plano do Trickle, que pode levar horas para ser concluída, dependendo do número e dos tamanhos das tabelas. No entanto, uma vez concluída, a migração de dados será muito mais rápida devido à nova arquitetura de armazenamento aprimorada com grupos de alta qualidade.
 
> [!NOTE]
> ALTER INDEX REBUILD é uma operação offline e as tabelas não estarão disponíveis até que a recompilação seja concluída.

A consulta a seguir gera os comandos necessários ALTER INDEX REBUILD para agilizar a migração de dados:

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON [' 
       + Schema_name(tbl.schema_id) + '].[' 
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE 
                                                         WHEN ( 
                                                     (SELECT Count(*) 
                                                      FROM   sys.partitions 
                                                             part2 
                                                      WHERE  part2.index_id 
                                                             = idx.index_id 
                                                             AND 
                                                     idx.object_id = 
                                                     part2.object_id) 
                                                     > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              ELSE '' 
                                                       END ) + '; SELECT ''[' + 
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' + 
              Object_name(idx.object_id) + '] ' + ( 
              CASE 
                WHEN ( (SELECT Count(*) 
                        FROM   sys.partitions 
                               part2 
                        WHERE 
                     part2.index_id = 
                     idx.index_id 
                     AND idx.object_id 
                         = part2.object_id) > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              + ' completed'';' 
              ELSE ' completed'';' 
                                                    END ) 
FROM   sys.indexes idx 
       INNER JOIN sys.tables tbl 
               ON idx.object_id = tbl.object_id 
       LEFT OUTER JOIN sys.partitions part 
                    ON idx.index_id = part.index_id 
                       AND idx.object_id = part.object_id 
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE'; 
```

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Atualizar de uma região geográfica do Azure usando a restauração por meio do portal do Azure

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Criar um ponto de restauração definido pelo usuário usando o portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue até o SQL Data Warehouse para o qual você deseja criar um ponto de restauração.

3. Na parte superior da seção visão geral, selecione **+ novo ponto de restauração**.

    ![Novo ponto de restauro](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_0.png)

4. Especifique um nome para o ponto de restauração.

    ![Nome do ponto de restauração](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Restaurar um banco de dados ativo ou pausado usando o portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Navegue até o SQL Data Warehouse do qual você deseja restaurar.
3. Na parte superior da seção visão geral, selecione **restaurar**.

    ![ Descrição geral do Restauro](./media/sql-data-warehouse-restore-database-portal/restoring_0.png)

4. Selecione **pontos de restauração automáticos** ou **pontos de restauração definidos pelo usuário**.

    ![Pontos de Restauro Automático](./media/sql-data-warehouse-restore-database-portal/restoring_1.png)

5. Para pontos de restauração definidos pelo usuário, **Selecione um ponto de restauração** ou **crie um novo ponto de restauração definido pelo usuário**. Escolha um servidor em uma região geográfica com suporte do Gen2. 

    ![Pontos de restauração definidos pelo usuário](./media/sql-data-warehouse-restore-database-portal/restoring_2_udrp.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Restaurar de uma região geográfica do Azure usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para recuperar um banco de dados, use o cmdlet [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) .

> [!NOTE]
> Você pode executar uma restauração geográfica para Gen2! Para fazer isso, especifique um Gen2name (por exemplo, DW1000**c**) como um parâmetro opcional.

1. Abra o Windows PowerShell.
2. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
3. Selecione a assinatura que contém o banco de dados a ser restaurado.
4. Obtenha o banco de dados que você deseja recuperar.
5. Crie a solicitação de recuperação para o banco de dados, especificando um Gen2 de desobjecname.
6. Verifique o status do banco de dados restaurado geograficamente.

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>" -RequestedServiceObjectiveName "DW300c"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Para configurar o banco de dados após a conclusão da restauração, consulte [Configurar o banco de dados após a recuperação](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

O banco de dados recuperado será habilitado para TDE se o banco de dados de origem estiver habilitado para TDE.


Se você tiver problemas com seu data warehouse, crie uma [solicitação de suporte](sql-data-warehouse-get-started-create-support-ticket.md) e faça referência a "atualização Gen2" como a possível causa.

## <a name="next-steps"></a>Passos seguintes

O data warehouse atualizado está online. Para aproveitar a arquitetura avançada, consulte [classes de recursos para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md).
