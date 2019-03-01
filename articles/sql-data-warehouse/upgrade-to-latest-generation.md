---
title: Atualizar para a geração mais recente do Azure SQL Data Warehouse | Documentos da Microsoft
description: Atualize o Azure SQL Data Warehouse para a última geração da arquitetura de hardware e o armazenamento do Azure.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.openlocfilehash: 70a59ddc1b15693cb2a50c1dc2da6dd9233e0811
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990022"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Otimizar o desempenho ao atualizar o SQL Data Warehouse
Atualize o Azure SQL Data Warehouse para a última geração da arquitetura de hardware e o armazenamento do Azure.

## <a name="why-upgrade"></a>Por que atualizar?
Agora pode atualizar facilmente para a camada de SQL Data Warehouse de computação otimizada geração 2 no portal do Azure para [regiões suportadas](gen2-migration-schedule.md#automated-schedule-and-region-availability-table). Se a sua região não suporta a atualização automática, pode atualizar para uma região suportada ou aguarde Self-atualizar para estar disponível na sua região. Atualize agora para aproveitar a última geração de hardware do Azure e a arquitetura de armazenamento melhorada, incluindo um desempenho mais rápido, escalabilidade e armazenamento em colunas ilimitado. 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>Aplica-se a
Esta atualização aplica-se para armazéns de dados de escalão de computação otimizada geração 1 no [regiões suportadas](gen2-migration-schedule.md#automated-schedule-and-region-availability-table).

## <a name="before-you-begin"></a>Antes de começar

1. Verifique se o seu [região](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) é suportado para a geração 1 para a migração de geração 2. Tenha em atenção as datas de migração automática. Para evitar conflitos com o processo automatizado, planeie a migração manual antes da data de início do processo automatizado.
2. Se estiver numa região que ainda não é suportada, continue a verificar para a sua região ser adicionado ou [atualização através de restauro](#Upgrade-from-an-Azure-geographical-region-using-restore-through-the-Azure-portal) para uma região suportada.
3. Se tiver suporte a sua região, [atualização através do portal do Azure](#Upgrade-in-a-supported-region-using-the-Azure-portal)
4. **Selecione o nível de desempenho sugeridos** para o armazém de dados com base no seu nível de desempenho atual na camada de computação otimizada geração 1 com o mapeamento abaixo:

   | Computação otimizada geração 1 camada | Computação otimizada geração 2 escalão |
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
>[!Note]
>Níveis de desempenho sugerida não são uma conversão direta. Por exemplo, recomendamos que vai de DW600 para DW500c.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Atualizar numa região suportada no portal do Azure

> [!NOTE]
> A migração de geração 1 para a geração 2 através do portal do Azure é permanente. Não é um processo para retornar à geração 1.  

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Se o armazém de dados de escalão de computação otimizada geração 1 a ser atualizado está em pausa, [retomar o armazém de dados](pause-and-resume-compute-portal.md).

   > [!NOTE]
   > Tem de executar o Azure SQL Data Warehouse para migrar para o ger2.

2. Esteja preparado para alguns minutos de tempo de inatividade. 

3. Identifique quaisquer referências de código para níveis de desempenho de computação otimizada geração 1 e modificá-los para seus equivalente nível de desempenho de computação otimizada geração 2. Seguem-se dois exemplos de onde deve atualizar as referências de código antes de atualizar:

   Comando do PowerShell de geração 1 original:

   ```powershell
   Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Modificado para:

   ```powershell
   Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE] 
   > -RequestedServiceObjectiveName "DW300" é alterado para - RequestedServiceObjectiveName "DW300**c**"
   >

   Comando original de geração 1. o T-SQL:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   Modificado para:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ; 
   ```
   > [!NOTE] 
   > SERVICE_OBJECTIVE = 'DW300' é alterado para SERVICE_OBJECTIVE = ' DW300**c**'



## <a name="start-the-upgrade"></a>Iniciar a atualização

1. Aceda ao seu armazém de dados de escalão de computação otimizada geração 1 no portal do Azure. Se o armazém de dados de escalão de computação otimizada geração 1 a ser atualizado está em pausa, [retomar o armazém de dados](pause-and-resume-compute-portal.md). 
2. Selecione **atualizar para ger2** cartão no separador tarefas:  ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)
    
    > [!NOTE]
    > Se não vir a **atualizar para ger2** cartão no separador tarefas, o tipo de subscrição é limitado na região atual.
    > [Submeter um pedido de suporte](sql-data-warehouse-get-started-create-support-ticket.md) para obter a sua subscrição na lista de permissões.


3. Certifique-se de que sua carga de trabalho foi concluída em execução e terem silenciado as antes de atualizar. Vai sofrer períodos de inatividade para alguns minutos até que o armazém de dados estiver novamente online como um armazém de dados de escalão de computação otimizada geração 2. **Selecione a atualização**:

   ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. **Monitorizar a sua atualização** ao verificar o estado no portal do Azure:

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)

   A primeira etapa do processo de atualização atravessa a operação de dimensionamento ("atualizando - Offline") em que serão eliminadas todas as sessões e ligações serão ignoradas. 

   A segunda etapa do processo de atualização é a migração de dados ("atualizando - Online"). Migração de dados é um processo de plano de fundo de online trickle. Este processo move lentamente para armazenamento de dados com a arquitetura de armazenamento antigo para a nova arquitetura de armazenamento utilizando uma cache SSD local. Durante este período, o seu armazém de dados estarão online para consultar e carregar. Os dados estarão disponíveis para consultar, independentemente de se ter sido migrada ou não. A migração de dados acontece às tarifas variadas, dependendo do tamanho dos dados, o nível de desempenho e o número de segmentos de columnstore. 

5. **Recomendação opcional:** Depois de concluída a operação de dimensionamento, pode acelerar o processo de segundo plano de migração de dados. Pode forçar o movimento de dados, executando [recompilação de índice Alter](sql-data-warehouse-tables-index.md) em todas as tabelas columnstore principal teria de ser consultar numa classe de recursos e SLO maior. Esta operação é **offline** em comparação com o processo de plano de fundo de trickle, que pode demorar horas a concluir consoante o número e os tamanhos de suas tabelas. No entanto, quando tiver terminado, migração de dados será muito mais rápida do devido a nova arquitetura de armazenamento avançado com grupos de linhas de alta qualidade.
 
> [!NOTE]
> A recriação de ALTER Index é uma operação offline e as tabelas não estarão disponíveis até que a recompilação de seja concluída.

A seguinte consulta gera os comandos necessários Alter Index Rebuild para acelerar a migração de dados:

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
## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Atualizar a partir de uma região geográfica do Azure através de restauro através do portal do Azure

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Criar um ponto de restauro definidas pelo utilizador através do portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue para o SQL data warehouse que pretende criar um ponto de restauro para.

3. Na parte superior da seção de visão geral, selecione **+ novo ponto de restauro**.

    ![Novo ponto de restauro](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_0.png)

4. Especifique um nome para o ponto de restauro.

    ![Nome do ponto de restauro](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Restaurar uma base de dados do Active Directory ou em pausa no portal do Azure
1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Navegue para o SQL data warehouse que pretende restaurar a partir de.
3. Na parte superior da seção de visão geral, selecione **restaurar**.

    ![ Descrição geral do Restauro](./media/sql-data-warehouse-restore-database-portal/restoring_0.png)

4. Selecione **pontos de restauro automático** ou **pontos de restauro definidas pelo utilizador**.

    ![Pontos de Restauro Automático](./media/sql-data-warehouse-restore-database-portal/restoring_1.png)

5. Para pontos de restauração de definidas pelo utilizador, **selecione um ponto de restauro** ou **criar um novo ponto de restauro definidas pelo utilizador**. Escolha que um servidor numa geração 2 suportada região geográfica. 

    ![Pontos de restauro definidas pelo utilizador](./media/sql-data-warehouse-restore-database-portal/restoring_2_udrp.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Restaurar a partir de uma região geográfica do Azure com o PowerShell
Para recuperar uma base de dados, utilize o [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) cmdlet.

> [!NOTE]
> Pode efetuar um georrestauro para ger2! Para tal, especifique um ServiceObjectiveName de geração 2 (por exemplo, DW1000**c**) como um parâmetro opcional.
>

1. Abra o Windows PowerShell.
2. Ligue a sua conta do Azure e listar todas as subscrições associadas à sua conta.
3. Selecione a subscrição que contém a base de dados para serem restaurados.
4. Obtenha a base de dados que pretende recuperar.
5. Crie o pedido de recuperação para a base de dados, especificando um ServiceObjectiveName de geração 2.
6. Verifique se o estado da base de dados geo-restaurada.

```Powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>" -RequestedServiceObjectiveName "DW300c"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Para configurar a sua base de dados após o restauro foi concluída, consulte [configurar a sua base de dados após a recuperação](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
>

A base de dados recuperada serão habilitados para TDE se a base de dados de origem é habilitado para TDE.


Se encontrar algum problema com o data warehouse, crie uma [pedido de suporte](sql-data-warehouse-get-started-create-support-ticket.md) e fazer referência a "Atualização de geração 2" como a causa possível.

## <a name="next-steps"></a>Passos Seguintes
O armazém de dados atualizada está online. Para tirar partido da arquitetura avançada, consulte [classes de recursos para a gestão da carga de trabalho](resource-classes-for-workload-management.md).
