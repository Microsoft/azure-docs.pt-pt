---
title: Backup e restauro - instantâneos, geo-redundantes
description: Saiba como o backup e o restauro funcionam na piscina dedicada SQL da Azure Synapse Analytics. Utilize cópias de segurança para restaurar o seu armazém de dados para um ponto de restauro na região primária. Use backups geo-redundantes para restaurar uma região geográfica diferente.
services: synapse-analytics
author: joannapea
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: 0fa6777dc5b587150f630ed8ccc110d16448cc21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602252"
---
# <a name="backup-and-restore-in-azure-synapse-dedicated-sql-pool"></a>Backup e restauro na piscina SQL dedicada Azure Synapse

Aprenda a usar backup e restaurar na piscina Azure Synapse Dedicada SQL. Utilize pontos de restauro de piscinas SQL dedicados para recuperar ou copiar o seu armazém de dados para um estado anterior na região primária. Utilize backups geo redundantes de armazém de dados para restaurar uma região geográfica diferente.

## <a name="what-is-a-data-warehouse-snapshot"></a>O que é um instantâneo de armazém de dados

Um *instantâneo de armazém de dados* cria um ponto de restauro que pode aproveitar para recuperar ou copiar o seu armazém de dados para um estado anterior.  Uma vez que a piscina SQL dedicada é um sistema distribuído, um instantâneo de armazém de dados consiste em muitos ficheiros que estão localizados no armazenamento do Azure. As imagens captam alterações incrementais dos dados armazenados no seu armazém de dados.

Um *armazém de dados* restaurado é um novo armazém de dados que é criado a partir de um ponto de restauração de um armazém de dados existente ou eliminado. Restaurar o seu armazém de dados é uma parte essencial de qualquer estratégia de continuidade de negócios e recuperação de desastres porque recria os seus dados após corrupção acidental ou eliminação. O armazém de dados é também um mecanismo poderoso para criar cópias do seu armazém de dados para fins de teste ou desenvolvimento. As taxas de restauro de piscinas SQL dedicadas podem variar dependendo do tamanho e localização da base de dados do armazém de dados de origem e destino.

## <a name="automatic-restore-points"></a>Pontos de Restauro Automático

Os instantâneos são uma característica incorporada que cria pontos de restauro. Não tens de ativar esta capacidade. No entanto, a piscina de SQL dedicada deve estar em estado ativo para a criação de pontos de restauração. Se for pausado com frequência, não podem ser criados pontos de restauro automáticos, por isso certifique-se de criar um ponto de restauro definido pelo utilizador antes de fazer uma pausa na piscina SQL dedicada. Os pontos de restauro automáticos não podem ser atualmente eliminados pelos utilizadores, uma vez que o serviço utiliza estes pontos de restauro para manter as SLAs para recuperação.

As fotos do seu armazém de dados são tiradas ao longo do dia criando pontos de restauração que estão disponíveis durante sete dias. Este período de retenção não pode ser alterado. A piscina dedicada SQL suporta um objetivo de oito horas de ponto de recuperação (RPO). Pode restaurar o seu armazém de dados na região primária a partir de qualquer um dos instantâneos tirados nos últimos sete dias.

Para ver quando a última foto começou, execute esta consulta na sua piscina SQL dedicada online.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Pontos de Restauro Definidos pelo Utilizador

Esta funcionalidade permite-lhe ativar manualmente instantâneos para criar pontos de restauro do seu armazém de dados antes e depois de grandes modificações. Esta capacidade garante que os pontos de restauro são logicamente consistentes, o que fornece proteção adicional de dados em caso de interrupções de carga de trabalho ou erros do utilizador para um tempo de recuperação rápida. Os pontos de restauro definidos pelo utilizador estão disponíveis durante sete dias e são automaticamente eliminados em seu nome. Não é possível alterar o período de retenção dos pontos de restauro definidos pelo utilizador. **42 pontos de restauro definidos** pelo utilizador são garantidos a qualquer momento, pelo que devem ser [eliminados](/powershell/module/azurerm.sql/remove-azurermsqldatabaserestorepoint) antes de criar outro ponto de restauro. Pode ativar instantâneos para criar pontos de restauro definidos pelo utilizador através do [PowerShell](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsont#examples) ou do portal Azure.

> [!NOTE]
> Se necessitar de pontos de restauro superiores a 7 dias, por favor vote nesta capacidade [aqui](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). Também pode criar um ponto de restauro definido pelo utilizador e restaurar do ponto de restauro recém-criado para um novo armazém de dados. Uma vez restaurado, você tem a piscina SQL dedicada on-line e pode fazê-lo indefinidamente para economizar custos de cálculo. A base de dados pausada incorre em encargos de armazenamento na taxa de armazenamento Azure Synapse. Se precisar de uma cópia ativa do armazém de dados restaurado, pode retomar o que deve demorar apenas alguns minutos.

### <a name="restore-point-retention"></a>Restaurar a retenção de pontos

As seguintes listas listam os detalhes para períodos de retenção de pontos de restauração:

1. A piscina SQL dedicada elimina um ponto de restauro quando atinge o período de retenção de 7 dias **e** quando existem pelo menos 42 pontos de restauro total (incluindo o definido pelo utilizador e o automático).
2. As fotografias não são tiradas quando uma piscina SQL dedicada é pausada.
3. A idade de um ponto de restauração é medida pelos dias de calendário absoluto a partir do momento em que o ponto de restauração é tomado, incluindo quando a piscina SQL é pausada.
4. Em qualquer momento, um pool DE SQL dedicado é garantido para ser capaz de armazenar até 42 pontos de restauro definidos pelo utilizador e 42 pontos de restauro automáticos, desde que estes pontos de restauro não tenham atingido o período de retenção de 7 dias
5. Se um instantâneo for tirado, a piscina de SQL dedicada é então pausada por mais de 7 dias, e depois retomada, o ponto de restauro persistirá até que existam 42 pontos de restauro total (incluindo definidos pelo utilizador e automáticos)

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>Retenção instantânea quando uma piscina SQL é largada

Quando você larga uma piscina SQL dedicada, um instantâneo final é criado e guardado por sete dias. Pode restaurar a piscina SQL dedicada ao ponto de restauro final criado na eliminação. Se a piscina SQL dedicada for largada em estado de pausa, não é tirada nenhuma fotografia. Nesse cenário, certifique-se de criar um ponto de restauro definido pelo utilizador antes de deixar cair a piscina SQL dedicada.

> [!IMPORTANT]
> Se eliminar o servidor/espaço de trabalho que alberga uma piscina SQL dedicada, todas as bases de dados que pertencem ao servidor/espaço de trabalho também são eliminadas e não podem ser recuperadas. Não é possível restaurar um servidor apagado.

## <a name="geo-backups-and-disaster-recovery"></a>Geo-backups e recuperação de desastres

Um geo-backup é criado uma vez por dia para um [centro de dados emparelhado](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). A RPO para um geo-restauro é de 24 horas. Pode restaurar o geo-backup para um servidor em qualquer outra região onde a piscina SQL dedicada é suportada. Um geo-backup garante que pode restaurar o armazém de dados no caso de não conseguir aceder aos pontos de restauro na sua região primária.

Se não necessitar de geo-backups para a sua piscina SQL dedicada, pode desativá-las e economizar nos custos de armazenamento de recuperação de desastres. Para tal, consulte [como guiar: Desativar geo-backups para uma piscina SQL dedicada (anteriormente SQL DW)](disable-geo-backup.md). Note que se desativar geo-backups, não poderá recuperar a sua piscina SQL dedicada à região de Azure emparelhada se o seu centro de dados Azure primário não estiver disponível. 

> [!NOTE]
> Se necessitar de um RPO mais curto para geo-backups, vote nesta capacidade [aqui](https://feedback.azure.com/forums/307516-sql-data-warehouse). Também pode criar um ponto de restauro definido pelo utilizador e restaurar do ponto de restauro recém-criado para um novo armazém de dados numa região diferente. Uma vez restaurado, tem o armazém de dados on-line e pode fazê-lo indefinidamente para economizar custos de cálculo. A base de dados pausada incorre em encargos de armazenamento à taxa de armazenamento Azure Premium. Se precisar de uma cópia ativa do armazém de dados, pode retomar o que deve demorar apenas alguns minutos.

## <a name="data-residency"></a>Residência dos dados 

Se o seu centro de dados emparelhado estiver localizado fora do seu limite geográfico, pode garantir que os seus dados permanecem dentro do seu limite geográfico, optando por não armazenamento geo-redundante. Isto pode ser feito quando forte a sua piscina SQL dedicada (anteriormente SQL DW) através da opção de armazenamento geo-redundante ao criar ou restaurar uma piscina SQL dedicada (anteriormente SQL DW). 

Para confirmar que o seu centro de dados emparelhado está num país diferente, consulte as [Regiões Emparelhadas Azure.](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="backup-and-restore-costs"></a>Backup e restaurar custos

Você vai notar que a conta Azure tem um item de linha para armazenamento e um item de linha para armazenamento de recuperação de desastres. A taxa de armazenamento é o custo total para armazenar os seus dados na região primária, juntamente com as alterações incrementais capturadas por instantâneos. Para obter uma explicação mais detalhada de como os instantâneos são cobrados, consulte a  [Compreensão de como os Snapshots Acumulam Encargos](/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). A taxa geo-redundante cobre os custos de armazenamento dos geo-backups.  

O custo total do seu armazém de dados primário e sete dias de alterações instantâneas é arredondado para a tuberculose mais próxima. Por exemplo, se o seu armazém de dados for de 1,5 TB e os instantâneos capturarem 100 GB, é cobrado 2 TB de dados a taxas de armazenamento Azure Premium.

Se estiver a utilizar um armazenamento geo-redundante, receberá uma taxa de armazenamento separada. O armazenamento geo-redundante é faturado na taxa padrão de armazenamento Read-Access Geograficamente redundante (RA-GRS).

Para obter mais informações sobre os preços da Azure Synapse, consulte [os preços da Azure Synapse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/). Não é cobrado por saída de dados ao restaurar as regiões.

## <a name="restoring-from-restore-points"></a>Restaurar dos pontos de restauro

Cada instantâneo cria um ponto de restauro que representa o tempo que o instantâneo começou. Para restaurar um armazém de dados, você escolhe um ponto de restauro e emite um comando de restauro.  

Pode manter o armazém de dados restaurado e o atual, ou apagar um deles. Se pretender substituir o atual armazém de dados pelo armazém de dados restaurado, pode renomeá-lo utilizando [a BASE DE DADOS ALTER](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) com a opção MODIFICAR NOME.

Para restaurar um armazém de dados, consulte [restaurar uma piscina SQL dedicada.](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal)

Para restaurar um armazém de dados eliminado ou pausado, pode [criar um bilhete de apoio.](sql-data-warehouse-get-started-create-support-ticket.md)

## <a name="cross-subscription-restore"></a>Restauro de subscrição cruzada

Se precisar de restaurar diretamente através da subscrição, vote nesta capacidade [aqui](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore). Restaurar para um servidor diferente e ['Mover'](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) o servidor através de subscrições para executar uma restauração de subscrição cruzada.

## <a name="geo-redundant-restore"></a>Restauro geo-redundante

Você pode [restaurar a sua piscina SQL dedicada](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell) a qualquer região que suporte piscina SQL dedicada ao seu nível de desempenho escolhido.

> [!NOTE]
> Para realizar uma restauração geo-redundante não deve ter optado por sair desta funcionalidade.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre pontos de restauro, consulte [pontos de restauro definidos pelo utilizador](sql-data-warehouse-restore-points.md)