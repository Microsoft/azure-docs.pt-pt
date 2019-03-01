---
title: Restauro - instantâneos, georredundante e cópia de segurança do armazém de dados SQL do Azure | Documentos da Microsoft
description: Saiba como funciona a cópia de segurança e restauro no Azure SQL Data Warehouse. Backups de armazém de dados de utilização para restaurar o seu armazém de dados para um ponto de restauro na região primária. Utilize cópias de segurança georredundante para restaurar para uma região geográfica diferente.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 0d2c7fbba8184fba81be5e93eae1dd816687ecb4
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992673"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Cópia de segurança e restauro no Azure SQL Data Warehouse

Saiba como utilizar a cópia de segurança e restaurar no Azure SQL Data Warehouse. Utilize pontos de restauro do armazém de dados para recuperar ou copiar o armazém de dados para um estado anterior na região primária. As cópias de segurança georredundante para restaurar para uma região geográfica diferente do armazém de dados de utilização.

## <a name="what-is-a-data-warehouse-snapshot"></a>O que é um instantâneo do armazém de dados

R *instantâneo de armazém de dados* cria um ponto de restauro que pode aproveitar para recuperar ou cópia do armazém de dados para um estado anterior.  Uma vez que o SQL Data Warehouse é um sistema distribuído, um instantâneo do armazém de dados consiste em muitos ficheiros que estão localizados no armazenamento do Azure. Instantâneos capturam as alterações incrementais dos dados armazenados no seu armazém de dados.

R *restauro do armazém de dados* é um novo armazém de dados que é criado a partir de um ponto de restauro de um existente ou o armazém de dados eliminado. Restaurar o seu armazém de dados é uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade comercial, porque ele cria novamente os dados depois de danos acidentais ou eliminação. Armazém de dados também é um mecanismo poderoso para criar cópias do seu armazém de dados para fins de teste ou desenvolvimento.  SQL Data Warehouse utiliza mecanismos de restauro rápido dentro da mesma região que tem sido medida para menos de 20 minutos para qualquer tamanho de dados.

## <a name="automatic-restore-points"></a>Pontos de Restauro Automático

Os instantâneos são uma funcionalidade incorporada do serviço que cria pontos de restauro. Não é necessário que ativar esta capacidade. Pontos de restauro automático atualmente não não possível eliminar por utilizadores onde o serviço utiliza estes restaurar aponta para manter os SLAs para recuperação.

O SQL Data Warehouse tira instantâneos do seu armazém de dados ao longo do dia a criação de pontos de restauro que estão disponíveis durante sete dias. Não é possível alterar o período de retenção. SQL Data Warehouse suporta um objetivo de ponto de recuperação de oito horas (RPO). Pode restaurar o armazém de dados na região primária a partir de qualquer um dos instantâneos realizados nos últimos sete dias.

Para ver quando o último instantâneo iniciado, execute esta consulta no seu armazém de dados online do SQL.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Pontos de Restauro Definidos pelo Utilizador

Esta funcionalidade permite-lhe manualmente os instantâneos de Acionador para criar pontos de restauração do seu armazém de dados antes e depois grandes modificações. Esta capacidade assegura que os pontos de restauração estão logicamente consistentes, que fornece proteção de dados adicional em caso de quaisquer interrupções de carga de trabalho ou erros de utilizador para o tempo de recuperação rápida. Pontos de restauro definidas pelo utilizador estão disponíveis durante sete dias e são automaticamente eliminados em seu nome. Não é possível alterar o período de retenção de pontos de restauro definidas pelo utilizador. **pontos de restauro de 42 definidas pelo utilizador** são garantidas em qualquer ponto no tempo para que estes têm de estar [eliminado](https://go.microsoft.com/fwlink/?linkid=875299) antes de criar outro ponto de restauro. Pode acionar instantâneos para criar pontos de restauro definidas pelo utilizador através da [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabaserestorepoint?view=azurermps-6.2.0#examples) ou o portal do Azure.

> [!NOTE]
> Se necessitar de mais de 7 dias de pontos de restauração, votar, para esta capacidade [aqui](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). Também pode criar um ponto de restauro definidas pelo utilizador e restaurar a partir do ponto de restauro recentemente criado para um novo armazém de dados. Depois de ter restaurado, ter o armazém de dados online e pode colocar em pausa indefinidamente para reduzir os custos de computação. A base de dados em pausa leva a custos de armazenamento à tarifa de armazenamento Premium do Azure. Se precisar de uma cópia ativa do armazém de dados restaurada, pode retomar o que deverá demorar apenas alguns minutos.
>

### <a name="restore-point-retention"></a>Retenção do ponto de restauro

Os seguintes detalhes de listas para períodos de retenção do ponto de restauro:

1. O SQL Data Warehouse elimina um ponto de restauro, quando ela atinge o período de retenção de 7 dias **e** quando existem, pelo menos, 42 pontos de restauro total (incluindo definidas pelo utilizador e automática)
2. Não são tirados instantâneos quando um armazém de dados está em pausa
3. A duração de um ponto de restauro é medida aos dias de calendário absoluto desde o momento em que o ponto de restauro está a ser utilizado incluindo quando o armazém de dados está em pausa
4. Em qualquer altura, é garantido que um armazém de dados poderá armazenar até 42 pontos de restauro definidas pelo utilizador e 42 pontos de restauro automático, desde que esses pontos de restauro não atingiu o período de retenção de 7 dias
5. Se um instantâneo, o armazém de dados, em seguida, fica em pausa durante mais de 7 dias e, em seguida, retoma, é possível que o ponto de restauro persistir até que haja 42 pontos de restauro total (incluindo definidas pelo utilizador e automática)

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Retenção de instantâneo quando é arrastado para um armazém de dados

Quando remover um armazém de dados, o SQL Data Warehouse cria um instantâneo final e guarda-o durante sete dias. Pode restaurar o armazém de dados para o ponto de restauro final criado na eliminação.

> [!IMPORTANT]
> Se eliminar uma instância do SQL server lógica, todas as bases de dados que pertencem à instância também são eliminados e não podem ser recuperados. Não é possível restaurar um servidor foi eliminado.
>

## <a name="geo-backups-and-disaster-recovery"></a>Após desastre e cópias de segurança geo recuperação

O SQL Data Warehouse efetua uma cópia de segurança geo uma vez por dia para uma [Centro de dados emparelhado](../best-practices-availability-paired-regions.md). O RPO para um georrestauro é de 24 horas. Pode restaurar a cópia de segurança geo para um servidor em qualquer outra região em que o SQL Data Warehouse é suportado. Uma cópia de segurança geo assegura que pode restaurar o armazém de dados no caso de não conseguir aceder os pontos de restauro na sua região primária.

Cópias de segurança geo são ativados por padrão. Se o seu armazém de dados é a geração 1, pode [para anular](/powershell/module/azurerm.sql/set-azurermsqldatabasegeobackuppolicy) se desejar. Não pode desativar cópias de segurança geo para a geração 2, como proteção de dados é uma incorporada garantida.

> [!NOTE]
> Se necessitar de um RPO mais curto para cópias de segurança geo, votar para esta capacidade [aqui](https://feedback.azure.com/forums/307516-sql-data-warehouse). Também pode criar um ponto de restauro definidas pelo utilizador e restaurar a partir do ponto de restauro recentemente criado para um novo armazém de dados numa região diferente. Depois de ter restaurado, ter o armazém de dados online e pode colocar em pausa indefinidamente para reduzir os custos de computação. A base de dados em pausa leva a custos de armazenamento à tarifa de armazenamento Premium do Azure. Se necessitar de uma cópia ativa do armazém de dados, é possível retomar o que deve demorar apenas alguns minutos.
>

## <a name="backup-and-restore-costs"></a>Custos de cópia de segurança e restauro

Notará que a fatura do Azure tem um item de linha para armazenamento e um item de linha para armazenamento de recuperação após desastre. O custo de armazenamento é o custo total de armazenamento dos dados na região primária, juntamente com as alterações incrementais capturado por instantâneos. Para obter uma explicação mais detalhada de como os instantâneos são cobrados, consulte [compreender como instantâneos de acumulam encargos](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios). A cobrança georredundante abrange o custo para o armazenamento de cópias de segurança geo.  

O total de custos para o seu armazém de dados primária e sete dias de alterações de instantâneo é arredondado para o TB mais próximo. Por exemplo, se o armazém de dados tiver 1,5 TB e 100 GB de captura de instantâneos, é-lhe cobrada para 2 TB de dados às tarifas de armazenamento Premium do Azure.

Se estiver a utilizar o armazenamento georredundante, receberá uma fatura de armazenamento separada. O armazenamento georredundante é cobrado à tarifa padrão de acesso de leitura geograficamente com redundância de armazenamento (RA-GRS).

Para obter mais informações sobre os preços do SQL Data Warehouse, consulte [preços do SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) e [custos de saída](https://azure.microsoft.com/pricing/details/bandwidth/) durante a restauração entre regiões.

## <a name="restoring-from-restore-points"></a>Restaurar a partir de pontos de restauro

Cada instantâneo cria um ponto de restauro que representa a hora de início de instantâneo. Para restaurar um armazém de dados, escolha um ponto de restauro e emitir um comando de restauro.  

Pode manter o armazém de dados restaurados e atual ou elimine um deles. Se pretende substituir o armazém de dados atual com o armazém de dados restaurada, pode alterá-lo usando [ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) com a opção de modificar o nome.

Para restaurar um armazém de dados, consulte [restaurar um armazém de dados com o portal do Azure](sql-data-warehouse-restore-database-portal.md), [restaurar um armazém de dados com o PowerShell](sql-data-warehouse-restore-database-powershell.md), ou [restaurar um armazém de dados com REST APIs](sql-data-warehouse-restore-database-rest-api.md).

Para restaurar um armazém de dados eliminada ou em pausa, pode [criar um pedido de suporte](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="geo-redundant-restore"></a>Restauro com redundância geográfica

Pode [restaurar o seu armazém de dados](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) para qualquer região que suporta o SQL Data Warehouse no seu nível de desempenho escolhido.

> [!NOTE]
> Para efetuar um restauro com redundância geográfica tem não tiver optado por fora desta funcionalidade.
>

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o planeamento de desastres, consulte [descrição geral da continuidade de negócio](../sql-database/sql-database-business-continuity.md)
