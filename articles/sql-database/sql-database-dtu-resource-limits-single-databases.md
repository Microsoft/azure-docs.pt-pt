---
title: Bases de dados individuais de limites de recursos baseados em DTU de base de dados SQL do Azure | Documentos da Microsoft
description: Esta página descreve alguns limites comuns do recurso baseado em DTU para bases de dados individuais na base de dados do Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/25/2019
ms.openlocfilehash: e7424af894070b320cde9d4d0922dd6a3fb680c3
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316868"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>Limites de recursos para bases de dados individuais com o modelo de compra baseado em DTU

Este artigo fornece os limites de recursos detalhados para a base de dados do Azure SQL únicas bases de dados com o modelo de compra baseado em DTU.

Para baseado em DTU compra modelo limites de recursos para conjuntos elásticos, veja [limites de recursos baseados em DTU - conjuntos elásticos](sql-database-vcore-resource-limits-elastic-pools.md). Para limites de recursos baseados em vCore, consulte [limites de recursos baseados em vCore - bases de dados individuais](sql-database-vcore-resource-limits-single-databases.md) e [limites de recursos baseados em vCore - conjuntos elásticos](sql-database-vcore-resource-limits-elastic-pools.md). Para obter mais informações sobre os diferentes modelos de compras, veja [escalões de serviço e modelos de compra](sql-database-purchase-models.md).

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ter reduzir uma base de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerir o espaço de ficheiro na base de dados do Azure SQL](sql-database-file-space-management.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Base de dados: Tamanhos de armazenamento e tamanhos de computação

As tabelas seguintes mostram os recursos disponíveis para uma base de dados em cada escalão de serviço e tamanho de computação. Pode definir o escalão de serviço, o tamanho de computação e a quantidade de armazenamento para uma base de dados individual utilizando o [portal do Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), o [ CLI do Azure](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases), ou o [API de REST](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

### <a name="basic-service-tier"></a>Camada de serviços básicos

| **Tamanho de computação** | **Básica** |
| :--- | --: |
| Máximo de DTUs | 5 |
| Armazenamento incluído (GB) | 2 |
| Opções de armazenamento máximo (GB) | 2 |
| Armazenamento em OLTP na memória máx. (GB) |N/A |
| Máximo de trabalhadores simultâneos (pedidos) | 30 |
| Sessões simultâneas máx. | 300 |
|||

### <a name="standard-service-tier"></a>Camada de serviços padrão

| **Tamanho de computação** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Máximo de DTUs | 10 | 20 | 50 | 100 |
| Armazenamento incluído (GB) | 250 | 250 | 250 | 250 |
| Opções de armazenamento máximo (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Armazenamento em OLTP na memória máx. (GB) | N/A | N/D | N/D | N/A |
| Máximo de trabalhadores simultâneos (pedidos)| 60 | 90 | 120 | 200 |
| Sessões simultâneas máx. |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Camada de serviço Standard (continuada)

| **Tamanho de computação** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Máximo de DTUs | 200 | 400 | 800 | 1600 | 3000 |
| Armazenamento incluído (GB) | 250 | 250 | 250 | 250 | 250 |
| Opções de armazenamento máximo (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Armazenamento em OLTP na memória máx. (GB) | N/A | N/D | N/D | N/D |N/A |
| Máximo de trabalhadores simultâneos (pedidos)| 400 | 800 | 1600 | 3200 |6000 |
| Sessões simultâneas máx. |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Camada de serviços premium

| **Tamanho de computação** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Máximo de DTUs | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Armazenamento incluído (GB) | 500 | 500 | 500 | 500 | 4096 | 4096 |
| Opções de armazenamento máximo (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 | 4096 |
| Armazenamento em OLTP na memória máx. (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Máximo de trabalhadores simultâneos (pedidos)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Sessões simultâneas máx. | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

> [!IMPORTANT]
> Mais de 1 TB de armazenamento no escalão Premium está atualmente disponível em todas as regiões, exceto: Leste da China, Norte da China, Alemanha Central, Nordeste da Alemanha, e.u.a. centro-oeste, US DoD regiões e Governo dos E.u. a centro. Noutras regiões, o armazenamento máximo no escalão Premium está limitado a 1 TB.  Para obter mais informações, consulte [limitações atuais do P11-P15](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

## <a name="single-database-change-storage-size"></a>Base de dados: Alterar o tamanho de armazenamento

- O preço DTU para bases de dados única inclui uma certa quantidade de armazenamento sem custos adicionais. Pode ser aprovisionado armazenamento extra para além da quantidade incluída por um custo adicional até ao limite de tamanho máximo em incrementos de 250 GB a 1 TB e, em seguida, em incrementos de 256 GB a partir de 1 TB. Para quantidades de armazenamento incluído e limites de tamanho máximo, consulte [único banco de dados: Tamanhos de armazenamento e tamanhos de computação](#single-database-storage-sizes-and-compute-sizes).
- Armazenamento extra para uma base de dados pode ser aprovisionado, aumente o seu tamanho máximo utilizando o [portal do Azure](sql-database-single-database-scale.md#dtu-based-purchasing-model-change-storage-size), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), o [Azure CLI](/cli/azure/sql/db#az-sql-db-update), ou o [API de REST](https://docs.microsoft.com/rest/api/sql/databases/update).
- O preço do armazenamento extra para uma base de dados é a quantidade de armazenamento extra multiplicada pelo preço de unidade de armazenamento extra da camada de serviços. Para obter detalhes sobre o preço do armazenamento extra, consulte [preços de base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-dtus"></a>Base de dados: DTUs de alteração

Após escolher inicialmente um escalão de serviço, o tamanho de computação e a quantidade de armazenamento, pode dimensionar uma base de dados ou diminua verticalmente dinamicamente com base na experiência real usando o [portal do Azure](sql-database-single-database-scale.md#dtu-based-purchasing-model-change-compute-resources-dtus), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [ PowerShell](/powershell/module/az.sql/set-azsqldatabase), o [CLI do Azure](/cli/azure/sql/db#az-sql-db-update), ou a [REST API](https://docs.microsoft.com/rest/api/sql/databases/update).

Mostra o vídeo seguinte alterar dinamicamente o serviço de camada e tamanho de aumentar DTUs disponíveis para uma base de dados de computação.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

Alterar o serviço de camada e/ou tamanho de uma base de dados cria uma réplica da base de dados original com o novo tamanho de computação e, em seguida, troca ligações para a réplica de computação. Não são perdidos dados durante este processo mas durante o breve momento em que trocamos para a réplica, as ligações à base de dados são desativadas, pelo que algumas transações em voo poderão ser revertidas. O período de tempo para o comutador-over varia, mas é menos de 30 segundos 99% do tempo. Se houver grandes números de transações em voo nas ligações do momento em que estão desativados, o período de tempo para o comutador-over pode demorar mais.

A duração de todo o processo de aumento vertical depende do tamanho e do escalão de serviço da base de dados antes e após a alteração. Por exemplo, uma base de dados de 250 GB que está a alterar para, a partir ou dentro de um escalão Standard, deve ser concluído dentro de seis horas. Para uma base de dados do mesmo tamanho que está a alterar os tamanhos de computação dentro da camada de serviço Premium, o aumento vertical deverá ser concluída no prazo de três horas.

> [!TIP]
> Para monitorizar operações em curso, consulte: [Gerir as operações com a API de REST de SQL](https://docs.microsoft.com/rest/api/sql/databaseoperations/listbydatabase
), [gerir as operações com a CLI](/cli/azure/sql/db/op), [monitorizar as operações com o T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e estes dois comandos do PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) e [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

- Se estiver a atualizar para um escalão de serviço superior ou tamanho de computação, o tamanho máximo da base de dados não aumenta a menos que especifique explicitamente um tamanho maior (maxsize).
- Para mudar a versão de uma base de dados, o espaço de base de dados utilizada tem de ser menor do que o máximo permitido de tamanho da camada de serviços de destino e o tamanho de computação.
- Quando a mudança do **Premium** para o **padrão** escalão, um custo de armazenamento extra se aplica ao tanto (1) o tamanho máximo da base de dados é suportado no tamanho de computação de destino, e (2) o tamanho máximo excede o incluído tamanho de computação da quantidade de armazenamento de destino. Por exemplo, se uma base de dados de P1 com um tamanho máximo de 500 GB é downsized S3, em seguida, um custo de armazenamento extra aplica-se como S3 suporta um tamanho máximo de 500 GB e a quantidade de armazenamento incluído é apenas de 250 GB. Por isso, a quantidade de armazenamento extra é 500 GB – 250 GB = 250 GB. Para obter os preços do armazenamento extra, consulte [preços de base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço utilizada é inferior à quantidade de armazenamento incluído, em seguida, isso encargos extra pode ser evitado ao reduzir o tamanho máximo da base de dados para o montante incluído.
- Quando atualizar uma base de dados com [georreplicação](sql-database-geo-replication-portal.md) ativada, atualizar seus bancos de dados secundários para o escalão de serviço desejado e tamanho de computação antes de atualizar a base de dados primária (documentação de orientação geral para um melhor desempenho). Ao atualizar para o outro, atualizar a base de dados secundária primeiro é necessário.
- Quando a desatualização de uma base de dados com [georreplicação](sql-database-geo-replication-portal.md) ativada, mudar a versão de seus bancos de dados principal para o escalão de serviço desejado e tamanho de computação antes de reduzir a base de dados secundária (documentação de orientação geral para um melhor desempenho). Ao fazer downgrade para uma edição diferente, desatualização de base de dados primária em primeiro lugar é necessário.
- As ofertas de serviço de restauro diferem entre os vários escalões de serviço. Se estiver fazendo downgrade para o **básica** camada, há um período de retenção de cópia de segurança inferior. Ver [Backups de banco de dados SQL do Azure](sql-database-automated-backups.md).
- As novas propriedades para a base de dados não são aplicadas até que as alterações estejam concluídas.

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Base de dados: Limitações do P11 e P15 quando o número máximo de tamanho superior a 1 TB

As seguintes considerações e limitações aplicam-se às bases de dados P11 e P15 com um maior que 1 TB de tamanho máximo:

- Se escolher um tamanho máximo superior a 1 TB durante a criação de uma base de dados (com um valor de 4 TB ou 4096 GB), o comando create falha com um erro se a base de dados é aprovisionada numa região não suportada.
- Para P11 e P15 bases de dados existentes localizados em uma das regiões suportadas, pode aumentar o armazenamento máximo para além de 1 TB em incrementos de 256 GB até 4 TB. Para ver se um tamanho maior é suportado na sua região, utilize o [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) funcionar ou inspecionar o tamanho da base de dados no portal do Azure. Atualizando uma existente P11 ou P15 base de dados só pode ser executada por um início de sessão principal ao nível do servidor ou por membros da função de base de dados dbmanager.
- Se uma operação de atualização é executada numa região suportada a configuração é atualizada automaticamente. A base de dados permanece online durante o processo de atualização. No entanto, não pode utilizar a quantia total de armazenamento para além de 1 TB de armazenamento até que os arquivos de banco de dados real foram atualizados para o novo tamanho máximo. O período de tempo necessário depende no tamanho da base de dados a ser atualizado.
- Quando criar ou atualizar uma base de dados P11 ou P15, só pode escolher entre o tamanho máximo, 1 TB e 4 TB, em incrementos de 256 GB. Ao criar o P11/P15, a opção de armazenamento predefinida de 1 TB é pré-selecionada. Bases de dados localizados em uma das regiões suportadas, pode aumentar o número máximo de armazenamento para até um máximo de 4 TB para uma base de dados nova ou existente. Para todas as outras regiões, o tamanho máximo não pode ser aumentado superiores a 1 TB. O preço não é alterada quando seleciona 4 TB de armazenamento incluído.
- Se o tamanho máximo de uma base de dados estiver definido como superior a 1 TB, em seguida, ele não é possível alterar a 1 TB, mesmo que o armazenamento atual utilizado é abaixo 1 TB. Portanto, não pode mudar um P11 ou P15 com um tamanho máximo superior a 1 TB para um P11 TB 1 ou de 1 TB P15 ou tamanho, por exemplo, P1 – P6 de computação mais baixo). Esta restrição aplica-se também para o restauro e cenários de cópia, incluindo o ponto no tempo, georrestauro, longo-prazo-cópia de segurança-retenção e a cópia da base de dados. Depois de uma base de dados é configurada com um tamanho máximo superior a 1 TB, todas as operações de restauro desta base de dados tem de ser executadas em P11/P15 com um tamanho máximo superior a 1 TB.
- Para cenários de georreplicação ativa:
  - Como configurar uma relação de replicação geográfica: Se a base de dados primária estiver P11 ou P15, o secondary(ies) também tem de ser P11 ou P15; tamanhos de computação mais baixos são rejeitados como bases de dados secundárias, uma vez que eles não são capazes de oferecer suporte a mais de 1 TB.
  - Atualizar a base de dados principal numa relação de replicação geográfica: Alterar o tamanho máximo para mais de 1 TB numa base de dados primária aciona a mesma alteração na base de dados secundário. Ambas as atualizações têm de ser efetuada com êxito para a alteração principal para entrar em vigor. Aplicam a limitações de região para a opção de mais de 1 TB. Se o elemento secundário for numa região que não suporta mais de 1 TB, o principal não está atualizado.
- Não é suportada a utilização do serviço de importação/exportação para carregar o P11/P15 bases de dados com mais de 1 TB. Utilizar SqlPackage.exe para [importe](sql-database-import.md) e [exportar](sql-database-export.md) dados.

## <a name="next-steps"></a>Passos Seguintes

- Para limites de recursos de vCore para uma base de dados, consulte [limites de recursos para bases de dados individuais com o modelo de compra baseado em vCore](sql-database-vcore-resource-limits-single-databases.md)
- Para limites de recursos de vCore para conjuntos elásticos, consulte [limites de recursos para conjuntos elásticos com o modelo de compra baseado em vCore](sql-database-vcore-resource-limits-elastic-pools.md)
- Para limites de recursos DTU para conjuntos elásticos, consulte [limites de recursos para conjuntos elásticos com o modelo de compra baseado em DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Para limites de recursos para instâncias geridas, consulte [geridos limites de recursos de instância](sql-database-managed-instance-resource-limits.md).
- Para obter informações sobre os limites do Azure gerais, consulte [subscrição do Azure e limites do serviço, quotas e restrições](../azure-subscription-service-limits.md).
- Para obter informações sobre limites de recursos num servidor de base de dados, consulte [descrição geral dos limites de recursos num servidor de base de dados SQL](sql-database-resource-limits-database-server.md) para obter informações sobre os limites nos níveis de servidor e de subscrição.