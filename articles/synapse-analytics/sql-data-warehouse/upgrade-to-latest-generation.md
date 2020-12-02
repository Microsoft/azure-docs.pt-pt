---
title: Upgrade para a última geração de piscinas SQL dedicadas (anteriormente SQL DW)
description: Upgrade Azure Synapse Analytics dedicou a piscina SQL (anteriormente SQL DW) à última geração de hardware e arquitetura de armazenamento Azure.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: b5a9d1781bd0498ac6ad74439b1572c52e3c345a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459265"
---
# <a name="optimize-performance-by-upgrading-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Otimizar o desempenho através da atualização da piscina sql dedicada (anteriormente SQL DW) em Azure Synapse Analytics

Atualize a sua piscina SQL dedicada (anteriormente SQL DW) para a última geração de hardware e arquitetura de armazenamento Azure.

## <a name="why-upgrade"></a>Porquê atualizar?

Pode agora fazer upgrade para a piscina SQL dedicada (anteriormente SQL DW) Nível de Gen2 otimizado compute no portal Azure para [regiões apoiadas.](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) Se a sua região não apoiar o auto-upgrade, pode fazer upgrade para uma região apoiada ou esperar que o auto-upgrade esteja disponível na sua região. Atualize agora para aproveitar a última geração de hardware Azure e arquitetura de armazenamento melhorada, incluindo desempenho mais rápido, maior escalabilidade e armazenamento colunar ilimitado.

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

> [!IMPORTANT]
> Esta atualização aplica-se a piscinas SQL dedicadas ao nível da Computação Otimizada da Gen1 (fornmerly SQL DW) em [regiões apoiadas.](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)

## <a name="before-you-begin"></a>Before you begin

1. Verifique se a sua [região](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) é apoiada para a migração GEN1 para GEN2. Note as datas de migração automática. Para evitar conflitos com o processo automatizado, planeie a sua migração manual antes da data de início do processo automatizado.
2. Se você estiver em uma região que ainda não está apoiada, continue a verificar se a sua região deve ser adicionada ou [upgrade usando o restauro](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal) para uma região apoiada.
3. Se a sua região for apoiada, [atualize através do portal Azure](#upgrade-in-a-supported-region-using-the-azure-portal)
4. **Selecione o nível** de desempenho sugerido para piscina SQL dedicada (anteriormente SQL DW) com base no seu nível de desempenho atual no nível de Gen1 otimizado compute, utilizando o mapeamento abaixo:

   | Nível de Gen1 otimizado compute | Nível de Gen2 otimizado compute |
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

> [!NOTE]
> Os níveis de desempenho sugeridos não são uma conversão direta. Por exemplo, recomendamos ir de DW600 a DW500c.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Upgrade numa região apoiada usando o portal Azure

- A migração da Gen1 para a Gen2 através do portal Azure é permanente. Não há um processo para regressar à Gen1.
- Piscina SQL dedicada (anteriormente SQL DW) deve estar correndo para migrar para a Gen2

### <a name="before-you-begin"></a>Before you begin

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Inicie sessão no [portal do Azure](https://portal.azure.com/).
- Certifique-se de que a piscina SQL dedicada (anteriormente SQL DW) está em funcionamento - deve ser para migrar para a Gen2

### <a name="powershell-upgrade-commands"></a>Comandos de upgrade powerShell

1. Se a piscina SQL dedicada ao nível da Compute Optimized Gen1 (anteriormente SQL DW) a ser atualizada for pausada, [retome a piscina SQL dedicada (anteriormente SQL DW)](pause-and-resume-compute-portal.md).

2. Prepare-se para alguns minutos de tempo de descanso.

3. Identifique quaisquer referências de código aos níveis de desempenho da Gen1 otimizada compute e modifique-as ao seu nível de desempenho equivalente da Compute Optimized Gen2. Abaixo estão dois exemplos de onde deve atualizar referências de código antes de atualizar:

   Comando Original Gen1 PowerShell:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Modificado para:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE]
   > -Serviço Solicitado O nome "DW300" é alterado para - Nome de miniatura do Serviço Solicitado "DW300 **c"**
   >

   Comando Original Gen1 T-SQL:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   Modificado para:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ;
   ```

   > [!NOTE]
   > SERVICE_OBJETIVE = 'DW300' é alterado para SERVICE_OBJETIVE = 'DW300 **c'**

## <a name="start-the-upgrade"></a>Inicie a atualização

1. Vá ao seu compute Optimized Gen1 pool SQL (anteriormente SQL DW) no portal Azure. Se a piscina SQL dedicada ao nível da Compute Optimized Gen1 (anteriormente SQL DW) a ser atualizada for interrompida, [retome a piscina SQL dedicada](pause-and-resume-compute-portal.md).
2. Selecione Upgrade para o cartão **Gen2** no separador Tarefas: ![ Upgrade_1](./media/upgrade-to-latest-generation/upgrade-to-gen2-1.png)

   > [!NOTE]
   > Se não vir o cartão **Upgrade para Gen2** no separador Tarefas, o seu tipo de subscrição é limitado na região atual.
   > [Envie um bilhete de apoio](sql-data-warehouse-get-started-create-support-ticket.md) para obter a sua assinatura aprovada.

3. Certifique-se de que a sua carga de trabalho está concluída em funcionamento e a 50da antes de ser melhorada. Você vai experimentar tempo de inatividade por alguns minutos antes que sua piscina DE SQL dedicada (anteriormente SQL DW) esteja novamente on-line como uma piscina SQL dedicada ao nível compute Optimized Gen2 (anteriormente SQL DW). **Selecione Upgrade**:

   ![Upgrade_2](./media/upgrade-to-latest-generation/upgrade-to-gen2-2.png)

4. **Monitorize a sua atualização** verificando o estado no portal Azure:

   ![Upgrade3](./media/upgrade-to-latest-generation/upgrade-to-gen2-3.png)

   O primeiro passo do processo de atualização passa pela operação de escala ("Upgrade - Offline") onde todas as sessões serão eliminadas e as ligações serão retiradas.

   O segundo passo do processo de atualização é a migração de dados ("Upgrade - Online"). A migração de dados é um processo de fundo online. Este processo move lentamente dados colunares da antiga arquitetura de armazenamento para a nova arquitetura de armazenamento usando uma cache SSD local. Durante este tempo, a sua piscina DE SQL dedicada (anteriormente SQL DW) estará online para consulta e carregamento. Os seus dados estarão disponíveis para consulta, independentemente de terem sido migrados ou não. A migração de dados ocorre a taxas variadas dependendo do tamanho dos seus dados, do seu nível de desempenho e do número dos segmentos da sua loja de colunas.

5. **Recomendação opcional:** Uma vez concluída a operação de dimensionamento, pode acelerar o processo de fundo de migração de dados. Pode forçar o movimento de dados executando [a reconstrução do Alter Index](sql-data-warehouse-tables-index.md) em todas as tabelas de lojas de colunas primárias que estaria a consultar numa SLO maior e classe de recursos. Esta operação está **offline** em comparação com o processo de fundo trickle, que pode levar horas a ser concluída dependendo do número e tamanhos das suas tabelas. No entanto, uma vez concluída, a migração de dados será muito mais rápida devido à nova arquitetura de armazenamento melhorada com grupos de linha de alta qualidade.

> [!NOTE]
> A reconstrução do Alter Index é uma operação offline e as tabelas não estarão disponíveis até que a reconstrução esteja concluída.

A seguinte consulta gera os comandos de Reconstrução de Índice de Alter necessários para acelerar a migração de dados:

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

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Upgrade a partir de uma região geográfica Azure usando restauro através do portal Azure

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Criar um ponto de restauro definido pelo utilizador utilizando o portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Navegue para a piscina SQL dedicada (anteriormente SQL DW) para a que pretende criar um ponto de restauração.

3. No topo da secção Overview, selecione **+Novo Ponto de Restauro**.

    ![Novo ponto de restauro](./media/upgrade-to-latest-generation/creating_restore_point_0.png)

4. Especifique um nome para o seu ponto de restauro.

    ![Nome do Ponto de Restauro](./media/upgrade-to-latest-generation/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Restaurar uma base de dados ativa ou pausada utilizando o portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Navegue para a piscina de SQL dedicada (anteriormente SQL DW) que pretende restaurar.
3. No topo da secção 'Vista Geral', selecione **'Restaurar'.**

    ![ Descrição geral do Restauro](./media/upgrade-to-latest-generation/restoring_0.png)

4. Selecione **pontos de restauro automáticos** ou **pontos de restauro definidos pelo utilizador**. Para pontos de restauro definidos pelo utilizador, **selecione um ponto de restauro definido pelo utilizador** ou Crie um novo ponto de restauro definido pelo **utilizador**. Para o servidor, **selecione Criar novo** e escolha um servidor numa região geográfica suportada pela Gen2.

    ![Pontos de Restauro Automático](./media/upgrade-to-latest-generation/restoring_1.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Restauro a partir de uma região geográfica azul usando PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para recuperar uma base de dados, utilize o [cmdlet Restore-AzSqlDatabase.](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

> [!NOTE]
> Você pode realizar um geo-restauro para a Gen2! Para tal, especifique um Nome Gen2 ServiceObjective (por exemplo, DW1000 **c**) como um parâmetro opcional.

1. Abra o Windows PowerShell.
2. Ligue-se à sua conta Azure e enuseça todas as subscrições associadas à sua conta.
3. Selecione a subscrição que contém a base de dados a ser restaurada.
4. Pegue a base de dados que quer recuperar.
5. Crie o pedido de recuperação da base de dados, especificando um Nome Gen2 ServiceObjective.
6. Verifique o estado da base de dados geo-restaurada.

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
> Para configurar a sua base de dados após a conclusão da restauração, consulte [configurar a sua base de dados após a recuperação](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

A base de dados recuperada será ativada pelo TDE se a base de dados de origem estiver ativada pelo TDE.

Se sentir algum problema com a sua piscina DE SQL dedicada, crie um pedido de [suporte](sql-data-warehouse-get-started-create-support-ticket.md) e referência "atualização Gen2" como a causa possível.

## <a name="next-steps"></a>Passos seguintes

A sua piscina SQL dedicada atualizada (anteriormente SQL DW) está online. Para tirar partido da arquitetura melhorada, consulte as [classes de Recursos para Gestão da Carga de Trabalho.](resource-classes-for-workload-management.md)
