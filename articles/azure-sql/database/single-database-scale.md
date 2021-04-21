---
title: Dimensionar recursos de base de dados única
description: Este artigo descreve como escalar os recursos de computação e armazenamento disponíveis para uma única base de dados na Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, references_regions
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 04/09/2021
ms.openlocfilehash: ae1b3cc41d709c28ba517d672eb98cb60a837a8d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779080"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Dimensionar recursos de base de dados individual na Base de Dados SQL do Azure

Este artigo descreve como escalar os recursos de computação e armazenamento disponíveis para uma Base de Dados Azure SQL no nível de computação provisionado. Alternativamente, o [nível de computação sem servidor](serverless-tier-overview.md) fornece cálculo de autoscalagem e contas por segundo para o cálculo utilizado.

Depois de escolher inicialmente o número de vCores ou DTUs, pode escalar uma única base de dados para cima ou para baixo dinamicamente com base na experiência real utilizando:

* [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database)
* [Portal do Azure](single-database-manage.md#the-azure-portal)
* [PowerShell](/powershell/module/az.sql/set-azsqldatabase)
* [CLI do Azure](/cli/azure/sql/db#az_sql_db_update)
* [API REST](/rest/api/sql/databases/update)


O vídeo que se segue mostra uma alteração dinâmica do nível de serviço e do tamanho do cálculo para aumentar os DTUs disponíveis para uma única base de dados.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> Em algumas circunstâncias, pode precisar de encolher uma base de dados para recuperar o espaço não usused. Para obter mais informações, consulte [Gerir o espaço de ficheiros na Base de Dados Azure SQL](file-space-manage.md).

## <a name="impact"></a>Impacto

A alteração do nível de serviço ou do tamanho do cálculo envolve principalmente o serviço que executa os seguintes passos:

1. Crie uma nova instância de computação para a base de dados. 

    Uma nova instância de computação é criada com o nível de serviço solicitado e o tamanho do cálculo. Para algumas combinações de alterações de nível de serviço e tamanho de cálculo, uma réplica da base de dados deve ser criada no novo caso do cálculo, que envolve copiar dados e pode influenciar fortemente a latência geral. Seja como for, a base de dados permanece on-line durante este passo, e as ligações continuam a ser direcionadas para a base de dados no caso do cálculo original.

2. Mude o encaminhamento das ligações para uma nova instância de computação.

    As ligações existentes à base de dados na instância de computação original são eliminadas. Quaisquer novas ligações são estabelecidas na base de dados no novo caso computacional. Para algumas combinações de alterações de nível de serviço e tamanho de cálculo, os ficheiros de base de dados são separados e religados durante o comutador.  Independentemente disso, o comutador pode resultar numa breve interrupção de serviço quando a base de dados não está disponível geralmente por menos de 30 segundos e muitas vezes durante apenas alguns segundos. Se houver transações de longa duração em execução quando as ligações são abandonadas, a duração deste passo pode demorar mais tempo para recuperar as transações abortadas. [A recuperação acelerada da base de dados](../accelerated-database-recovery.md) pode reduzir o impacto de abortar transações de longa duração.

> [!IMPORTANT]
> Não são perdidos dados durante qualquer etapa no fluxo de trabalho. Certifique-se de que implementou alguma [lógica de relagem](troubleshoot-common-connectivity-issues.md) nas aplicações e componentes que estão a utilizar a Base de Dados Azure SQL enquanto o nível de serviço é alterado.

## <a name="latency"></a>Latência

A latência estimada para alterar o nível de serviço, escalar o tamanho do cálculo de uma única base de dados ou piscina elástica, mover uma base de dados dentro/fora de uma piscina elástica, ou mover uma base de dados entre piscinas elásticas é parametrizada da seguinte forma:

|Escalão de serviço|Base de dados única básica,</br>Padrão (S0-S1)|Piscina elástica básica,</br>Standard (S2-S12), </br>Base de dados única ou piscina elástica para fins gerais|Premium ou Business Critical base de dados única ou piscina elástica|Hyperscale
|:---|:---|:---|:---|:---|
|**Base de dados única básica, </br> Standard (S0-S1)**|&bull;&nbsp;Latência do tempo constante independente do espaço utilizado</br>&bull;&nbsp;Tipicamente, menos de 5 minutos|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|
|**Piscina elástica básica, </br> Standard (S2-S12), </br> base de dados única de finalidade geral ou piscina elástica**|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Para bases de dados individuais, latência de tempo constante independente do espaço utilizado</br>&bull;&nbsp;Normalmente, menos de 5 minutos para bases de dados individuais</br>&bull;&nbsp;Para piscinas elásticas, proporcional ao número de bases de dados|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|
|**Premium ou Business Critical base de dados única ou piscina elástica**|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de base de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Tipicamente, menos de 1 minuto por GB de espaço usado|
|**Hyperscale**|N/D|N/D|N/D|&bull;&nbsp;Latência do tempo constante independente do espaço utilizado</br>&bull;&nbsp;Tipicamente, menos de 2 minutos|

> [!NOTE]
> Além disso, para as bases de dados Standard (S2-S12) e General Purpose, a latência para mover uma base de dados dentro/para fora de uma piscina elástica ou entre piscinas elásticas será proporcional ao tamanho da base de dados se a base de dados estiver a utilizar o armazenamento de Premium File Share[(PFS).](../../storage/files/storage-files-introduction.md)
>
> Para determinar se uma base de dados está a utilizar o armazenamento de PFS, execute a seguinte consulta no contexto da base de dados. Se o valor na coluna AccountType for `PremiumFileStorage` `PremiumFileStorage-ZRS` ou, a base de dados está a utilizar o armazenamento de PFS.

```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!NOTE]
> A propriedade redundante da zona permanecerá a mesma por padrão ao escalar do Negócio Crítico para o nível de Finalidade Geral. A latência para esta degradação quando o despedimento de zona estiver habilitado, bem como a latência para a mudança para a zona de redundância para o nível de finalidade geral serão proporcionais ao tamanho da base de dados.

> [!TIP]
> Para monitorizar as operações em curso, consulte: [Gerir operações utilizando a API DE REST SQL,](/rest/api/sql/operations/list) [Gerir operações utilizando operações CLI,](/cli/azure/sql/db/op) [Monitor utilizando o T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e estes dois comandos PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) e [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

## <a name="cancelling-changes"></a>Cancelamento de alterações

Uma operação de rescalamento de nível de serviço ou de cálculo pode ser cancelada.

### <a name="the-azure-portal"></a>O portal do Azure

Na lâmina de visão geral da base de **dados,** navegue para notificações e clique no azulejo indicando que há uma operação em curso:

![Operação em curso](./media/single-database-scale/ongoing-operations.png)

Em seguida, clique no botão marcado **Cancelar esta operação**.

![Cancelar operação em curso](./media/single-database-scale/cancel-ongoing-operation.png)

### <a name="powershell"></a>PowerShell

A partir de um pedido de comando PowerShell, desemordene o `$resourceGroupName` , e , em `$serverName` `$databaseName` seguida, executar o seguinte comando:

```azurecli
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

## <a name="additional-considerations"></a>Considerações adicionais

- Se estiver a atualizar para um nível de serviço ou tamanho de cálculo mais elevado, o tamanho máximo da base de dados não aumenta a menos que especifique explicitamente um tamanho maior (tamanho máximo).
- Para desclassificar uma base de dados, o espaço utilizado na base de dados deve ser menor do que o tamanho máximo permitido do nível de serviço alvo e do tamanho do cálculo.
- Ao reduzir de **Premium** para o nível **Standard,** um custo de armazenamento extra aplica-se se ambos (1) o tamanho máximo da base de dados for suportado no tamanho do cálculo alvo, e (2) o tamanho máximo exceder a quantidade de armazenamento incluída do tamanho do cálculo alvo. Por exemplo, se uma base de dados P1 com um tamanho máximo de 500 GB for reduzido para S3, então um custo de armazenamento extra se aplica, uma vez que o S3 suporta um tamanho máximo de 1 TB e o seu valor de armazenamento incluído é de apenas 250 GB. Assim, a quantidade extra de armazenamento é de 500 GB – 250 GB = 250 GB. Para preços de armazenamento extra, consulte [os preços da Base de Dados Azure SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço utilizado for inferior à quantidade de armazenamento incluída, então este custo extra pode ser evitado reduzindo o tamanho máximo da base de dados para a quantidade incluída.
- Ao atualizar uma base de dados com [geo-replicação](active-geo-replication-configure-portal.md) ativada, atualize as suas bases de dados secundárias para o nível de serviço e tamanho do cálculo pretendido antes de atualizar a base de dados primária (orientação geral para o melhor desempenho). Ao atualizar para uma edição diferente, é um requisito que a base de dados secundária é atualizada primeiro.
- Ao desclassificar uma base de dados com [geo-replicação](active-geo-replication-configure-portal.md) ativada, desclasse as suas bases de dados primárias para o nível de serviço e o tamanho do cálculo pretendido antes de degradar a base de dados secundária (orientação geral para o melhor desempenho). Ao reduzir para uma edição diferente, é um requisito que a base de dados primária é desclassificada primeiro.
- As ofertas de serviço de restauro diferem entre os vários escalões de serviço. Se estás a descer para o nível **básico,** há um período de retenção de reserva mais baixo. Consulte as [cópias de dados Azure SQL](automated-backups-overview.md).
- As novas propriedades da base de dados não são aplicadas até que as alterações estejam completas.
- Quando a cópia de dados é necessária para escalar uma base de dados (ver [Latência)](#latency)ao alterar o nível de serviço, a utilização de recursos elevados simultaneamente para a operação de escalagem pode causar tempos de escala mais longos. Com [a Recuperação acelerada da Base de Dados (ADR),](/sql/relational-databases/accelerated-database-recovery-concepts)o reversão de transações de longa duração não é uma fonte significativa de atraso, mas o elevado uso de recursos simultâneos pode deixar menos recursos de computação, armazenamento e largura de banda de rede para dimensionamento, particularmente para tamanhos de computação mais pequenos.

## <a name="billing"></a>Faturação

Você é cobrado por cada hora que uma base de dados existe usando o nível de serviço mais alto + tamanho de cálculo que se aplicava durante essa hora, independentemente do uso ou se a base de dados estava ativa por menos de uma hora. Por exemplo, se criar uma única base de dados e a eliminar cinco minutos depois, a sua conta reflete uma cobrança por uma hora de base de dados.

## <a name="change-storage-size"></a>Alterar o tamanho do armazenamento

### <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

- O armazenamento pode ser a provisionado até ao limite máximo de armazenamento de dados, utilizando incrementos de 1-GB. O armazenamento mínimo de dados configurável é de 1 GB. Para os limites máximos de armazenamento de dados em cada objetivo de serviço, consulte as páginas de documentação limite de recursos para [limites de recursos para bases de dados únicas utilizando o modelo de compra vCore](resource-limits-vcore-single-databases.md) e [limites de recursos para bases de dados únicas utilizando o modelo de compra DTU](resource-limits-dtu-single-databases.md).
- O armazenamento de dados para uma única base de dados pode ser a provisionado aumentando ou diminuindo o seu tamanho máximo utilizando o [portal Azure](https://portal.azure.com), [Transact-SQL,](/sql/t-sql/statements/alter-database-transact-sql#examples-1) [PowerShell,](/powershell/module/az.sql/set-azsqldatabase) [Azure CLI,](/cli/azure/sql/db#az_sql_db_update)ou [REST API](/rest/api/sql/databases/update). Se o valor do tamanho máximo for especificado em bytes, deve ser um múltiplo de 1 GB (bytes 1073741824).
- A quantidade de dados que podem ser armazenados nos ficheiros de dados de uma base de dados é limitada pelo tamanho máximo de armazenamento de dados configurado. Além desse armazenamento, a Azure SQL Database atribui automaticamente mais 30% de armazenamento para ser usado para o registo de transações.
- A Base de Dados Azure SQL atribui automaticamente 32 GB por vCore para a `tempdb` base de dados. `tempdb` está localizado no armazenamento SSD local em todos os níveis de serviço.
- O preço de armazenamento de uma única base de dados ou de um conjunto elástico é a soma dos valores de armazenamento de dados e de armazenamento de registos de transações multiplicados pelo preço unitário de armazenamento do nível de serviço. O custo `tempdb` está incluído no preço. Para mais informações sobre o preço de armazenamento, consulte [os preços da Base de Dados Azure SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, pode precisar de encolher uma base de dados para recuperar o espaço não usused. Para obter mais informações, consulte [Gerir o espaço de ficheiros na Base de Dados Azure SQL](file-space-manage.md).

### <a name="dtu-based-purchasing-model"></a>Modelo de compra baseado em DTU

- O preço do DTU para uma única base de dados inclui uma certa quantidade de armazenamento sem custos adicionais. O armazenamento extra para além do montante incluído pode ser previsto para um custo adicional até ao limite de tamanho máximo em incrementos de 250 GB até 1 TB, e depois em incrementos de 256 GB para além de 1 TB. Para as quantidades de armazenamento incluídas e limites de tamanho máximo, consulte [base de dados única: tamanhos de armazenamento e tamanhos de cálculo](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- O armazenamento extra para uma única base de dados pode ser a provisionado aumentando o seu tamanho máximo utilizando o portal Azure, [Transact-SQL,](/sql/t-sql/statements/alter-database-transact-sql#examples-1) [PowerShell,](/powershell/module/az.sql/set-azsqldatabase) [o Azure CLI,](/cli/azure/sql/db#az_sql_db_update)ou o [REST API](/rest/api/sql/databases/update).
- O preço do armazenamento extra para uma única base de dados é a quantia extra de armazenamento multiplicada pelo preço extra da unidade de armazenamento do nível de serviço. Para mais informações sobre o preço do armazenamento extra, consulte [os preços da Base de Dados Azure SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, pode precisar de encolher uma base de dados para recuperar o espaço não usused. Para obter mais informações, consulte [Gerir o espaço de ficheiros na Base de Dados Azure SQL](file-space-manage.md).

### <a name="geo-replicated-database"></a>Base de dados geo-replicada

Para alterar o tamanho da base de dados de uma base de dados secundária replicada, altere o tamanho da base de dados primária. Esta alteração será então replicada e implementada também na base de dados secundária.

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>Restrições P11 e P15 quando o tamanho máximo superior a 1 TB

Mais de 1 TB de armazenamento no nível Premium está atualmente disponível em todas as regiões, exceto: China Leste, China Norte, Alemanha Central e Alemanha Nordeste. Nestas regiões, o máximo de armazenamento no nível Premium é limitado a 1 TB. As seguintes considerações e limitações aplicam-se às bases de dados P11 e P15 com um tamanho máximo superior a 1 TB:

- Se o tamanho máximo de uma base de dados P11 ou P15 foi alguma vez definido para um valor superior a 1 TB, então só pode ser restaurado ou copiado para uma base de dados P11 ou P15.  Posteriormente, a base de dados pode ser redimensionada para um tamanho de cálculo diferente, desde que a quantidade de espaço atribuído no momento da operação de rescalamento não exceda os limites máximos de tamanho máximo do novo tamanho do cálculo.
- Para cenários de geo-replicação ativos:
  - Criação de uma relação de geo-replicação: Se a base de dados primária for P11 ou P15, o secundário também deve ser P11 ou P15. O tamanho do cálculo mais baixo é rejeitado como secundário, uma vez que não são capazes de suportar mais de 1 TB.
  - A atualização da base de dados primária numa relação de geo-replicação: Alterar o tamanho máximo para mais de 1 TB numa base de dados primária desencadeia a mesma alteração na base de dados secundária. Ambas as atualizações devem ser bem sucedidas para que a mudança nas primárias produza efeitos. Aplicam-se limitações regionais para a opção de mais de 1-TB. Se o secundário está numa região que não suporta mais de 1 TB, as primárias não são melhoradas.
- Não é suportado o serviço de importação/exportação para o carregamento de bases de dados P11/P15 com mais de 1 TB. Utilize SqlPackage.exe para [importar](database-import.md) e [exportar](database-export.md) dados.

## <a name="next-steps"></a>Passos seguintes

Para limites globais de recursos, consulte [Azure SQL Database vCore-based resource limits - bases de dados únicas](resource-limits-vcore-single-databases.md) e [limites de recursos baseados em DTU baseados em dados de dados DTU - bases de dados únicas](resource-limits-dtu-single-databases.md).
