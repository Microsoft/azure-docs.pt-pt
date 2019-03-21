---
title: Dimensionar recursos de base de dados-base de dados SQL do Azure | Documentos da Microsoft
description: Este artigo descreve como dimensionar os recursos de computação e armazenamento disponíveis para uma base de dados na base de dados do Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
manager: craigg
ms.date: 03/20/2019
ms.openlocfilehash: c6dc49204c0a7e1cb0d1116e29746eed2fe52f8d
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286266"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Dimensionar recursos de base de dados na base de dados do Azure SQL

Este artigo descreve como dimensionar os recursos de computação e armazenamento disponíveis para uma base de dados na base de dados do Azure SQL.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é suportado pelo SQL Database do Azure, mas todo o desenvolvimento futuro é para o módulo de Az.Sql. Para estes cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

## <a name="change-compute-resources-vcores-or-dtus"></a>Recursos de computação de alteração (vCores ou DTUs)

Após escolher inicialmente o número de vCores ou DTUs, pode dimensionar uma base de dados ou diminua verticalmente dinamicamente com base na experiência real usando o [portal do Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [ PowerShell](/powershell/module/az.sql/set-azsqldatabase), o [CLI do Azure](/cli/azure/sql/db#az-sql-db-update), ou a [REST API](https://docs.microsoft.com/rest/api/sql/databases/update).

Mostra o vídeo seguinte alterar dinamicamente o serviço de camada e tamanho de aumentar DTUs disponíveis para uma base de dados de computação.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ter reduzir uma base de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerir o espaço de ficheiro na base de dados do Azure SQL](sql-database-file-space-management.md).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Impacto da alteração do tamanho de computação de camada ou ao redimensionar o serviço

Alterar o serviço de camada ou a computação de tamanho de uma base de dados envolve principalmente o serviço, efetuando os seguintes passos:

1. Criar nova instância de computação para a base de dados  

    É criada uma nova instância de computação para a base de dados com o escalão de serviço pedido e o tamanho de computação. Para algumas combinações de camada de serviços e as alterações do tamanho de computação, uma réplica da base de dados tem de ser criada a nova instância de computação que envolve a cópia de dados e pode influenciar fortemente da latência geral. Seja como for, a base de dados permanece online durante este passo e ligações continuam a ser direcionado para a base de dados na instância de computação do original.

2. Mude o encaminhamento de ligações a nova instância de computação

    Ligações existentes para a base de dados na instância de computação original são removidas. Quaisquer novas ligações são estabelecidas para a base de dados na nova instância de computação. Para algumas combinações de camada de serviços e as alterações do tamanho de computação, ficheiros de base de dados desligados e voltar a ligar durante a troca.  Seja como for, o comutador pode resultar numa interrupção do serviço breve quando a base de dados não está disponível em geral para menos de 30 segundos e, muitas vezes, apenas alguns segundos. Se existirem de longa execução transações em execução quando as ligações são ignoradas, a duração deste passo pode demorar mais tempo para recuperar transações abortadas. [Acelerado de recuperação de base de dados](sql-database-accelerated-database-recovery.md) pode reduzir o impacto de abortar transações de longa execução.

> [!IMPORTANT]
> Nenhum dado seja perdido durante qualquer passo no fluxo de trabalho.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latência de alterar o tamanho de computação de camada ou ao redimensionar o serviço

A latência de alterar a camada de serviços ou rescale o tamanho de computação de um único banco de dados ou conjunto elástico é parametrizada da seguinte forma:

|Camada de serviços|Base de dados única básica,</br>Standard (S0-S1)|Conjunto elástico básico,</br>Standard (S2-S12), </br>Hiperescala, </br>Base de dados única finalidade geral ou conjunto elástico|Base de dados única Premium ou críticas para a empresa ou o conjunto elástico|
|:---|:---|:---|:---|
|**Base de dados única básica,</br> Standard (S0-S1)**|&bull; &nbsp;Latência de tempo constante independente do espaço utilizado</br>&bull; &nbsp;Normalmente, menos de 5 minutos|&bull; &nbsp;Latência proporcional ao espaço de base de dados utilizado devido a cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço utilizado|&bull; &nbsp;Latência proporcional ao espaço de base de dados utilizado devido a cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço utilizado|
|**Conjunto elástico básico, </br>Standard (S2-S12), </br>Hiperescala, </br>base de dados para fins gerais ou conjunto elástico**|&bull; &nbsp;Latência proporcional ao espaço de base de dados utilizado devido a cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço utilizado|&bull; &nbsp;Latência de tempo constante independente do espaço utilizado</br>&bull; &nbsp;Normalmente, menos de 5 minutos|&bull; &nbsp;Latência proporcional ao espaço de base de dados utilizado devido a cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço utilizado|
|**Base de dados única Premium ou críticas para a empresa ou o conjunto elástico**|&bull; &nbsp;Latência proporcional ao espaço de base de dados utilizado devido a cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço utilizado|&bull; &nbsp;Latência proporcional ao espaço de base de dados utilizado devido a cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço utilizado|&bull; &nbsp;Latência proporcional ao espaço de base de dados utilizado devido a cópia de dados</br>&bull; &nbsp;Normalmente, menos de 1 minuto por GB de espaço utilizado|

> [!TIP]
> Para monitorizar operações em curso, consulte: [Gerir as operações com a API de REST de SQL](https://docs.microsoft.com/rest/api/sql/operations/list), [gerir as operações com a CLI](/cli/azure/sql/db/op), [monitorizar as operações com o T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e estes dois comandos do PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) e [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Considerações adicionais ao alterar escalão ou ao redimensionar o tamanho de computação de serviço

- Se estiver a atualizar para um escalão de serviço superior ou tamanho de computação, o tamanho máximo da base de dados não aumenta a menos que especifique explicitamente um tamanho maior (maxsize).
- Para mudar a versão de uma base de dados, o espaço de base de dados utilizada tem de ser menor do que o máximo permitido de tamanho da camada de serviços de destino e o tamanho de computação.
- Quando a mudança do **Premium** para o **padrão** escalão, um custo de armazenamento extra se aplica ao tanto (1) o tamanho máximo da base de dados é suportado no tamanho de computação de destino, e (2) o tamanho máximo excede o incluído tamanho de computação da quantidade de armazenamento de destino. Por exemplo, se uma base de dados de P1 com um tamanho máximo de 500 GB é downsized S3, em seguida, um custo de armazenamento extra aplica-se como S3 suporta um tamanho máximo de 500 GB e a quantidade de armazenamento incluído é apenas de 250 GB. Por isso, a quantidade de armazenamento extra é 500 GB – 250 GB = 250 GB. Para obter os preços do armazenamento extra, consulte [preços de base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço utilizada é inferior à quantidade de armazenamento incluído, em seguida, isso encargos extra pode ser evitado ao reduzir o tamanho máximo da base de dados para o montante incluído.
- Quando atualizar uma base de dados com [georreplicação](sql-database-geo-replication-portal.md) ativada, atualizar seus bancos de dados secundários para o escalão de serviço desejado e tamanho de computação antes de atualizar a base de dados primária (documentação de orientação geral para um melhor desempenho). Ao atualizar para o outro, atualizar a base de dados secundária primeiro é necessário.
- Quando a desatualização de uma base de dados com [georreplicação](sql-database-geo-replication-portal.md) ativada, mudar a versão de seus bancos de dados principal para o escalão de serviço desejado e tamanho de computação antes de reduzir a base de dados secundária (documentação de orientação geral para um melhor desempenho). Ao fazer downgrade para uma edição diferente, desatualização de base de dados primária em primeiro lugar é necessário.
- As ofertas de serviço de restauro diferem entre os vários escalões de serviço. Se estiver fazendo downgrade para o **básica** camada, há um período de retenção de cópia de segurança inferior. Ver [Backups de banco de dados SQL do Azure](sql-database-automated-backups.md).
- As novas propriedades para a base de dados não são aplicadas até que as alterações estejam concluídas.

### <a name="billing-during-rescaling"></a>Faturação durante ao redimensionar o

Será cobrado por cada hora, que uma base de dados existe ao utilizar o escalão de serviço mais elevado + tamanho aplicado durante essa hora, independentemente da utilização ou se a base de dados esteve ativo durante menos de uma hora de computação. Por exemplo, se criar uma base de dados e a eliminar passados cinco minutos, a fatura reflete um custo de hora de uma base de dados.

## <a name="change-storage-size"></a>Alterar o tamanho de armazenamento

### <a name="vcore-based-purchasing-model"></a>Modelo de compras baseado em vCore

- Pode aprovisionar um armazenamento até ao limite de tamanho máximo com incrementos de 1 GB. O armazenamento de dados configurável mínimo é de 5 GB
- Armazenamento para uma base de dados pode ser aprovisionado por aumentar ou diminuir o tamanho máximo, usando o [portal do Azure](https://portal.azure.com), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), o [Da CLI do azure](/cli/azure/sql/db#az-sql-db-update), ou o [REST API](https://docs.microsoft.com/rest/api/sql/databases/update).
- Base de dados SQL aloca automaticamente 30% de armazenamento adicional para os ficheiros de registo e 32GB por vCore para TempDB, mas não deve exceder 384GB. TempDB está localizado em SSD anexado em todos os escalões de serviço.
- O preço de armazenamento para uma base de dados é a soma dos dados armazenamento e de registo de armazenamento quantidades multiplicado pelo preço de unidade de armazenamento da camada de serviços. O custo de TempDB está incluído no preço vCore. Para obter detalhes sobre o preço do armazenamento extra, consulte [preços de base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ter reduzir uma base de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerir o espaço de ficheiro na base de dados do Azure SQL](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>Modelo de compra baseado em DTU

- O preço DTU para bases de dados única inclui uma certa quantidade de armazenamento sem custos adicionais. Pode ser aprovisionado armazenamento extra para além da quantidade incluída por um custo adicional até ao limite de tamanho máximo em incrementos de 250 GB a 1 TB e, em seguida, em incrementos de 256 GB a partir de 1 TB. Para quantidades de armazenamento incluído e limites de tamanho máximo, consulte [único banco de dados: Tamanhos de armazenamento e tamanhos de computação](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Armazenamento extra para uma base de dados pode ser aprovisionado por seu tamanho máximo do portal do Azure, o aumento [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), o [da CLI do Azure](/cli/azure/sql/db#az-sql-db-update), ou o [ REST API](https://docs.microsoft.com/rest/api/sql/databases/update).
- O preço do armazenamento extra para uma base de dados é a quantidade de armazenamento extra multiplicada pelo preço de unidade de armazenamento extra da camada de serviços. Para obter detalhes sobre o preço do armazenamento extra, consulte [preços de base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ter reduzir uma base de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerir o espaço de ficheiro na base de dados do Azure SQL](sql-database-file-space-management.md).

## <a name="dtu-based-purchasing-model-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Modelo de compra baseado em DTU: Limitações do P11 e P15 quando o número máximo de tamanho superior a 1 TB

Mais de 1 TB de armazenamento no escalão Premium está atualmente disponível em todas as regiões, exceto: Leste da China, Norte da China, Alemanha Central, Nordeste da Alemanha, e.u.a. centro-oeste, US DoD regiões e Governo dos E.u. a centro. Noutras regiões, o armazenamento máximo no escalão Premium está limitado a 1 TB. Para obter mais informações, consulte [limitações atuais do P11-P15](sql-database-single-database-scale.md#dtu-based-purchasing-model-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb). As seguintes considerações e limitações aplicam-se às bases de dados P11 e P15 com um maior que 1 TB de tamanho máximo:

- Se o tamanho máximo para uma base de dados P11 ou P15 foi alguma vez definido para um valor superior a 1 TB, em seguida, ele só pode ser restaurado ou copiado para uma base de dados P11 ou P15.  Posteriormente, a base de dados pode ser rescaled para um tamanho de computação diferentes, desde que a quantidade de espaço alocado no momento da operação ao redimensionar não excede os limites de tamanho máximo do novo tamanho de computação.
- Para cenários de georreplicação ativa:
  - Como configurar uma relação de replicação geográfica: Se a base de dados primária estiver P11 ou P15, o secondary(ies) também tem de ser P11 ou P15; tamanho de computação mais baixo são rejeitados como bases de dados secundárias, uma vez que eles não são capazes de oferecer suporte a mais de 1 TB.
  - Atualizar a base de dados principal numa relação de replicação geográfica: Alterar o tamanho máximo para mais de 1 TB numa base de dados primária aciona a mesma alteração na base de dados secundário. Ambas as atualizações têm de ser efetuada com êxito para a alteração principal para entrar em vigor. Aplicam a limitações de região para a opção de mais de 1 TB. Se o elemento secundário for numa região que não suporta mais de 1 TB, o principal não está atualizado.
- Não é suportada a utilização do serviço de importação/exportação para carregar o P11/P15 bases de dados com mais de 1 TB. Utilizar SqlPackage.exe para [importe](sql-database-import.md) e [exportar](sql-database-export.md) dados.

## <a name="next-steps"></a>Passos Seguintes

Para geral limites de recursos, consulte [limites dos recursos baseados em vCore de base de dados SQL - bases de dados individuais](sql-database-vcore-resource-limits-single-databases.md) e [limites de recursos baseados em DTU de base de dados do SQL - conjuntos elásticos](sql-database-dtu-resource-limits-single-databases.md).
