---
title: Backup e restauração do Azure SQL Data Warehouse-instantâneos, com redundância geográfica | Microsoft Docs
description: Saiba como o backup e a restauração funcionam no Azure SQL Data Warehouse. Use data warehouse backups para restaurar o data warehouse para um ponto de restauração na região primária. Use backups com redundância geográfica para restaurar para uma região geográfica diferente.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 10/21/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 1cf6444b155830326f4876d2d65bcdaa5923fc35
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72788819"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Backup e restauração no Azure SQL Data Warehouse

Saiba como usar o backup e a restauração no Azure SQL Data Warehouse. Use data warehouse pontos de restauração para recuperar ou copiar seus data warehouse para um estado anterior na região primária. Use data warehouse backups com redundância geográfica para restaurar para uma região geográfica diferente.

## <a name="what-is-a-data-warehouse-snapshot"></a>O que é um instantâneo data warehouse

Um *instantâneo data warehouse* cria um ponto de restauração que você pode aproveitar para recuperar ou copiar seus data warehouse para um estado anterior.  Como SQL Data Warehouse é um sistema distribuído, um instantâneo de data warehouse consiste em muitos arquivos que estão localizados no armazenamento do Azure. Os instantâneos capturam alterações incrementais dos dados armazenados em seu data warehouse.

Uma *restauração de data warehouse* é uma nova data warehouse que é criada a partir de um ponto de restauração de um data warehouse existente ou excluído. A restauração de sua data warehouse é uma parte essencial de qualquer estratégia de continuidade de negócios e recuperação de desastres, pois ela recria seus dados após a corrupção ou exclusão acidental. O data warehouse também é um mecanismo poderoso para criar cópias do seu data warehouse para fins de teste ou desenvolvimento.  SQL Data Warehouse taxas de restauração podem variar dependendo do tamanho do banco de dados e do local da data warehouse de origem e de destino. 

## <a name="automatic-restore-points"></a>Pontos de restauração automática

Instantâneos são um recurso interno do serviço que cria pontos de restauração. Você não precisa habilitar esse recurso. No entanto, o data warehouse deve estar no estado ativo para a criação do ponto de restauração. Se o data warehouse for pausado com frequência, os pontos de restauração automáticas não poderão ser criados, portanto, certifique-se de criar um ponto de restauração definido pelo usuário antes de pausar o data warehouse. Atualmente, os pontos de restauração automáticos não podem ser excluídos pelos usuários, pois o serviço usa esses pontos de restauração para manter os SLAs para recuperação.

SQL Data Warehouse Obtém instantâneos de seu data warehouse ao longo do dia criando pontos de restauração que estão disponíveis por sete dias. Este período de retenção não pode ser alterado. O SQL Data Warehouse dá suporte a um RPO (objetivo de ponto de recuperação) de oito horas. Você pode restaurar seu data warehouse na região primária de qualquer um dos instantâneos tirados nos últimos sete dias.

Para ver quando o último instantâneo foi iniciado, execute esta consulta em seu SQL Data Warehouse online.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Pontos de restauração definidos pelo usuário

Esse recurso permite que você acione manualmente os instantâneos para criar pontos de restauração do seu data warehouse antes e depois de grandes modificações. Esse recurso garante que os pontos de restauração sejam logicamente consistentes, o que fornece proteção de dados adicional no caso de quaisquer interrupções de carga de trabalho ou erros de usuário para o tempo de recuperação rápida. Os pontos de restauração definidos pelo usuário estão disponíveis por sete dias e são excluídos automaticamente em seu nome. Você não pode alterar o período de retenção de pontos de restauração definidos pelo usuário. **42 pontos de restauração definidos pelo usuário** são garantidos em qualquer ponto no tempo para que eles devam ser [excluídos](https://go.microsoft.com/fwlink/?linkid=875299) antes de criar outro ponto de restauração. Você pode disparar instantâneos para criar pontos de restauração definidos pelo usuário por meio do [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint#examples) ou do portal do Azure.

> [!NOTE]
> Se você precisar de pontos de restauração por mais de 7 dias, vote nesse recurso [aqui](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). Você também pode criar um ponto de restauração definido pelo usuário e restaurar do ponto de restauração recém-criado para um novo data warehouse. Depois de ter restaurado, você tem o data warehouse online e pode pausá-lo indefinidamente para economizar custos de computação. O banco de dados em pausa provoca encargos de armazenamento na taxa de armazenamento Premium do Azure. Se precisar de uma cópia ativa do data warehouse restaurado, você poderá retomar, o que deve levar apenas alguns minutos.

### <a name="restore-point-retention"></a>Retenção do ponto de restauração

A lista a seguir detalha os períodos de retenção do ponto de restauração:

1. SQL Data Warehouse exclui um ponto de restauração quando atinge o período de retenção de 7 dias **e** quando há pelo menos 42 pontos de restauração totais (incluindo os definidos pelo usuário e automaticamente)
2. Os instantâneos não são feitos quando um data warehouse é pausado
3. A idade de um ponto de restauração é medida pelos dias de calendário absolutos a partir do momento em que o ponto de restauração é feito, incluindo quando o data warehouse está em pausa
4. A qualquer momento, é garantido que um data warehouse seja capaz de armazenar até 42 pontos de restauração definidos pelo usuário e 42 pontos de restauração automáticos, contanto que esses pontos de restauração não tenham atingido o período de retenção de 7 dias
5. Se um instantâneo for obtido, a data warehouse será pausada por mais de 7 dias e, em seguida, retomará, é possível que o ponto de restauração persista até que haja 42 de pontos de restauração totais (incluindo os definidos pelo usuário e automaticamente)

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Retenção de instantâneo quando um data warehouse é Descartado

Quando você remove um data warehouse, SQL Data Warehouse cria um instantâneo final e o salva por sete dias. Você pode restaurar o data warehouse para o ponto de restauração final criado na exclusão. Se o data warehouse for descartado no estado pausado, nenhum instantâneo será obtido. Nesse cenário, certifique-se de criar um ponto de restauração definido pelo usuário antes de descartar o data warehouse.

> [!IMPORTANT]
> Se você excluir uma instância lógica do SQL Server, todos os bancos de dados que pertencem à instância também serão excluídos e não poderão ser recuperados. Não é possível restaurar um servidor excluído.

## <a name="geo-backups-and-disaster-recovery"></a>Backups geográficos e recuperação de desastre

SQL Data Warehouse executa um backup geográfico uma vez por dia para um [Data Center emparelhado](../best-practices-availability-paired-regions.md). O RPO para uma restauração geográfica é de 24 horas. Você pode restaurar o backup geográfico para um servidor em qualquer outra região em que SQL Data Warehouse tem suporte. Um backup geográfico garante que você possa restaurar data warehouse caso não seja possível acessar os pontos de restauração em sua região primária.

> [!NOTE]
> Se você precisar de um RPO mais curto para backups geográficos, vote nesse recurso [aqui](https://feedback.azure.com/forums/307516-sql-data-warehouse). Você também pode criar um ponto de restauração definido pelo usuário e restaurar do ponto de restauração recém-criado para um novo data warehouse em uma região diferente. Depois de ter restaurado, você tem o data warehouse online e pode pausá-lo indefinidamente para economizar custos de computação. O banco de dados em pausa provoca encargos de armazenamento na taxa de armazenamento Premium do Azure. Se precisar de uma cópia ativa do data warehouse, você poderá retomar, o que deve levar apenas alguns minutos.

## <a name="backup-and-restore-costs"></a>Custos de backup e restauração

Você notará que a fatura do Azure tem um item de linha para armazenamento e um item de linha para armazenamento de recuperação de desastre. O encargo de armazenamento é o custo total para armazenar seus dados na região primária junto com as alterações incrementais capturadas por instantâneos. Para obter uma explicação mais detalhada de como os instantâneos são cobrados, consulte [noções básicas sobre como os instantâneos acumulam encargos](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios). O encargo com redundância geográfica cobre o custo para armazenar os backups geográficos.  

O custo total para seu data warehouse primário e sete dias de alterações de instantâneo é arredondado para os TB mais próximos. Por exemplo, se o data warehouse for de 1,5 TB e os instantâneos capturarem 100 GB, você será cobrado por 2 TB de dados em taxas de armazenamento Premium do Azure.

Se você estiver usando o armazenamento com redundância geográfica, receberá um encargo de armazenamento separado. O armazenamento com redundância geográfica é cobrado na taxa padrão de armazenamento geograficamente redundante do acesso de leitura (RA-GRS).

Para obter mais informações sobre preços de SQL Data Warehouse, consulte [preços de SQL data warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/). Você não será cobrado pela saída de dados durante a restauração entre regiões.

## <a name="restoring-from-restore-points"></a>Restaurando de pontos de restauração

Cada instantâneo cria um ponto de restauração que representa a hora em que o instantâneo foi iniciado. Para restaurar um data warehouse, escolha um ponto de restauração e emita um comando de restauração.  

Você pode manter o data warehouse restaurado e o atual, ou excluir um deles. Se desejar substituir o data warehouse atual pelo data warehouse restaurado, você poderá renomeá-lo usando [ALTER DATABASE (Azure SQL data warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) com a opção Modify Name.

Para restaurar um data warehouse, consulte [restaurar um data warehouse usando o portal do Azure](sql-data-warehouse-restore-database-portal.md), [restaurar um data warehouse usando o PowerShell](sql-data-warehouse-restore-database-powershell.md)ou [restaurar um data warehouse usando APIs REST](sql-data-warehouse-restore-database-rest-api.md).

Para restaurar uma data warehouse excluída ou em pausa, você pode [criar um tíquete de suporte](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Restauração de assinatura cruzada

Se você precisar restaurar diretamente na assinatura, vote nesse recurso [aqui](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore). Restaure para um servidor lógico diferente e [' mova '](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) o servidor em assinaturas para executar uma restauração de assinatura cruzada. 

## <a name="geo-redundant-restore"></a>Restauração com redundância geográfica

Você pode [restaurar seu data warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-from-geo-backup#restore-from-an-azure-geographical-region-through-powershell) para qualquer região que dê suporte a SQL data warehouse no nível de desempenho escolhido.

> [!NOTE]
> Para executar uma restauração com redundância geográfica, você não deve ter recusado esse recurso.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o planejamento de desastres, consulte [visão geral da continuidade de negócios](../sql-database/sql-database-business-continuity.md)
