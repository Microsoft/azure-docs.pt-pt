---
title: Backup e restauro - instantâneos, geo-redundantes
description: Saiba como obras de backup e restauro funcionam na piscina Azure Synapse Analytics SQL. Utilize cópias de segurança para restaurar o seu armazém de dados num ponto de restauro na região primária. Utilize backups geo-redundantes para restaurar uma região geográfica diferente.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/04/2020
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: ae53380572e753a8bcfa20fcd165fa015766263e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349254"
---
# <a name="backup-and-restore-in-azure-synapse-sql-pool"></a>Backup e restauro na piscina Azure Synapse SQL

Aprenda a usar backup e restaure na piscina Azure Synapse SQL. Utilize pontos de restauro de piscina SQL para recuperar ou copiar o seu armazém de dados para um estado anterior na região primária. Utilize cópias de segurança georedundantes de armazém de dados para restaurar uma região geográfica diferente.

## <a name="what-is-a-data-warehouse-snapshot"></a>O que é um instantâneo de armazém de dados

Um instantâneo de armazém de *dados* cria um ponto de restauro que pode alavancar para recuperar ou copiar o seu armazém de dados para um estado anterior.  Uma vez que o pool SQL é um sistema distribuído, um instantâneo de armazém de dados é composto por muitos ficheiros que estão localizados no armazenamento do Azure. Os instantâneos captam alterações incrementais dos dados armazenados no seu armazém de dados.

Um armazém de *dados restaurado* é um novo armazém de dados que é criado a partir de um ponto de restauro de um armazém de dados existente ou eliminado. Restaurar o seu armazém de dados é uma parte essencial de qualquer estratégia de continuidade de negócios e recuperação de desastres porque recria os seus dados após corrupção acidental ou eliminação. O armazém de dados é também um poderoso mecanismo para criar cópias do seu armazém de dados para fins de teste ou desenvolvimento.  As taxas de restauro do pool SQL podem variar dependendo do tamanho da base de dados e localização do armazém de dados de origem e alvo. 

## <a name="automatic-restore-points"></a>Pontos de Restauro Automático

Os instantâneos são uma característica incorporada do serviço que cria pontos de restauro. Não tens de ativar esta capacidade. No entanto, a piscina SQL deve estar em estado ativo para restaurar a criação de pontos. Se o pool SQL for interrompido com frequência, os pontos de restauro automáticos podem não ser criados, por isso certifique-se de criar um ponto de restauro definido pelo utilizador antes de parar a piscina SQL. Os pontos de restauro automáticos não podem ser eliminados pelos utilizadores, uma vez que o serviço utiliza estes pontos de restauro para manter os SLAs para recuperação.

As fotos do seu armazém de dados são tiradas ao longo do dia criando pontos de restauro que estão disponíveis por sete dias. Este período de retenção não pode ser alterado. A piscina SQL suporta um objetivo de ponto de recuperação de oito horas (RPO). Pode restaurar o seu armazém de dados na região primária a partir de qualquer uma das fotos tiradas nos últimos sete dias.

Para ver quando a última fotografia começou, execute esta consulta na sua piscina SQL online.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Pontos de Restauro Definidos pelo Utilizador

Esta funcionalidade permite-lhe acionar manualmente instantâneos para criar pontos de restauro do seu armazém de dados antes e depois de grandes modificações. Esta capacidade garante que os pontos de restauro são logicamente consistentes, o que fornece proteção adicional de dados em caso de interrupções de carga de trabalho ou erros do utilizador durante um tempo de recuperação rápido. Os pontos de restauro definidos pelo utilizador estão disponíveis durante sete dias e são automaticamente eliminados em seu nome. Não é possível alterar o período de retenção dos pontos de restauro definidos pelo utilizador. **42 pontos de restauro definidos pelo utilizador** são garantidos em qualquer momento, pelo que devem ser [eliminados](https://go.microsoft.com/fwlink/?linkid=875299) antes de criar outro ponto de restauro. Pode ativar instantâneos para criar pontos de restauro definidos pelo utilizador através do [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint#examples) ou do portal Azure.

> [!NOTE]
> Se necessitar de pontos de restauro com mais de 7 dias, por favor vote nesta capacidade [aqui](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). Também pode criar um ponto de restauro definido pelo utilizador e restaurar do ponto de restauro recém-criado para um novo armazém de dados. Uma vez restaurado, você tem a piscina SQL on-line e pode fazer uma pausa indefinida para economizar custos de computação. A base de dados pausada incorre em custos de armazenamento na taxa de armazenamento Azure Premium. Se precisar de uma cópia ativa do armazém de dados restaurado, pode retomar o que deve demorar apenas alguns minutos.

### <a name="restore-point-retention"></a>Restaurar a retenção de pontos

As seguintes listas de detalhes para os períodos de retenção de pontos de restauro:

1. A piscina SQL elimina um ponto de restauro quando atinge o período de retenção de 7 dias **e** quando existem pelo menos 42 pontos de restauro totais (incluindo ambos definidos pelo utilizador e automáticos).
2. As fotos não são tiradas quando uma piscina SQL é interrompida.
3. A idade de um ponto de restauro é medida pelos dias de calendário absoluto a partir do momento em que o ponto de restauro é tomado, incluindo quando a piscina SQL é interrompida.
4. Em qualquer momento, um pool SQL é garantido para ser capaz de armazenar até 42 pontos de restauro definidos pelo utilizador e 42 pontos de restauro automáticos, desde que estes pontos de restauro não tenham atingido o período de retenção de 7 dias
5. Se for tirada uma fotografia, a piscina SQL é então interrompida por mais de 7 dias e, em seguida, retoma, o ponto de restauro persistirá até que existam 42 pontos de restauro totais (incluindo definidos pelo utilizador e automáticos)

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>Retenção de instantâneos quando uma piscina SQL é largada

Quando você larga uma piscina SQL, um instantâneo final é criado e guardado por sete dias. Você pode restaurar a piscina SQL para o ponto final de restauro criado na eliminação. Se a piscina SQL for largada em estado de pausa, não se tira fotografias. Nesse cenário, certifique-se de criar um ponto de restauro definido pelo utilizador antes de deixar cair a piscina SQL.

> [!IMPORTANT]
> Se eliminar uma instância lógica do servidor SQL, todas as bases de dados que pertencem à instância também são eliminadas e não podem ser recuperadas. Não é possível restaurar um servidor apagado.

## <a name="geo-backups-and-disaster-recovery"></a>Geo-backups e recuperação de desastres

Uma geo-cópia de segurança é criada uma vez por dia para um centro de [dados emparelhado.](../../best-practices-availability-paired-regions.md) O RPO para um geo-restauro é de 24 horas. Pode restaurar a geo-cópia de segurança a um servidor em qualquer outra região onde o pool SQL é suportado. Um geo-backup garante que pode restaurar o armazém de dados caso não possa aceder aos pontos de restauro na sua região primária.

> [!NOTE]
> Se necessitar de um RPO mais curto para geo-backups, vote nesta capacidade [aqui](https://feedback.azure.com/forums/307516-sql-data-warehouse). Também pode criar um ponto de restauro definido pelo utilizador e restaurar do ponto de restauro recém-criado para um novo armazém de dados numa região diferente. Uma vez restaurado, tem o armazém de dados on-line e pode fazer uma pausa indefinida para economizar custos de computação. A base de dados pausada incorre em custos de armazenamento na taxa de armazenamento Azure Premium. Se precisar de uma cópia ativa do armazém de dados, pode retomar o que deve demorar apenas alguns minutos.

## <a name="backup-and-restore-costs"></a>Custos de backup e restauro

Você vai notar que a conta Azure tem um item de linha para armazenamento e um item de linha para armazenamento de recuperação de desastres. A carga de armazenamento é o custo total para armazenar os seus dados na região primária juntamente com as alterações incrementais capturadas por instantâneos. Para obter uma explicação mais detalhada de como os instantâneos são carregados, consulte a [Compreensão de como os instantâneos acumulam encargos](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios). A carga geo-redundante cobre o custo para armazenar os geo-backups.  

O custo total do seu armazém de dados primários e sete dias de alterações instantâneas é arredondado para a tuberculose mais próxima. Por exemplo, se o seu armazém de dados for de 1,5 TB e os instantâneos capturarem 100 GB, você é cobrado por 2 TB de dados a taxas de Armazenamento Azure Premium.

Se estiver a utilizar um armazenamento geo-redundante, recebe uma carga de armazenamento separada. O armazenamento geo-redundante é faturado à taxa padrão de armazenamento geograficamente redundante de leitura (RA-GRS).

Para obter mais informações sobre os preços do Azure Synapse, consulte [o preço do Azure Synapse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/). Não é cobrado por saída de dados ao restaurar todas as regiões.

## <a name="restoring-from-restore-points"></a>Restaurar dos pontos de restauro

Cada instantâneo cria um ponto de restauro que representa o momento em que o instantâneo começou. Para restaurar um armazém de dados, você escolhe um ponto de restauro e emite um comando de restauro.  

Pode manter o armazém de dados restaurado e o atual, ou apagar um deles. Se pretender substituir o armazém de dados em vigor pelo armazém de dados restaurado, pode renomeá-lo utilizando a [ALTER DATABASE (piscina SQL)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) com a opção MODIFICADO NOME.

Para restaurar um armazém de dados, consulte [Restaurar uma piscina SQL](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal).

Para restaurar um armazém de dados apagado ou pausado, pode [criar um bilhete](sql-data-warehouse-get-started-create-support-ticket.md)de apoio .

## <a name="cross-subscription-restore"></a>Restauro de subscrição cruzada

Se precisar de restaurar diretamente através da subscrição, vote nesta capacidade [aqui](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore). Restaurar para um servidor lógico diferente e ['Mover'](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) o servidor através de subscrições para realizar uma restauração de subscrição cruzada. 

## <a name="geo-redundant-restore"></a>Restauro geo-redundante

Você pode [restaurar a sua piscina SQL](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell) para qualquer região que suporte a piscina SQL ao seu nível de desempenho escolhido.

> [!NOTE]
> Para realizar um restauro geo-redundante não deve ter optado por não ter optado por não ter optado por não ter optado por não ter optado por não ter optado por não ter optado por não ter optado por sair desta funcionalidade.

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre planeamento de desastres, consulte a [visão geral da continuidade do Negócio](../../sql-database/sql-database-business-continuity.md)
