---
title: Dimensionar recursos de base de dados-base de dados SQL do Azure | Documentos da Microsoft
description: Este artigo descreve como dimensionar os recursos de computação e armazenamento disponíveis para uma base de dados na base de dados do Azure SQL.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: carlrab
ms.openlocfilehash: 5bc03b8fe8ea64d85154198b8009592f45805a7e
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031854"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Dimensionar recursos de base de dados na base de dados do Azure SQL

Este artigo descreve como dimensionar os recursos de computação e armazenamento disponíveis para uma base de dados na base de dados do Azure SQL. 

## <a name="vcore-based-purchasing-model-change-storage-size"></a>modelo de compra baseado em vCore: alterar o tamanho de armazenamento

- Pode aprovisionar um armazenamento até ao limite de tamanho máximo com incrementos de 1 GB. O armazenamento de dados configurável mínimo é de 5GB 
- Armazenamento para uma base de dados pode ser aprovisionado por aumentar ou diminuir o tamanho máximo, usando o [portal do Azure](https://portal.azure.com), [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql?r#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), o [Da CLI do azure](/cli/azure/sql/db#az-sql-db-update), ou o [REST API](/rest/api/sql/databases/update).
- Base de dados SQL aloca automaticamente 30% de armazenamento adicional para os ficheiros de registo e 32GB por vCore para TempDB, mas não deve exceder 384GB. TempDB está localizado em SSD anexado em todos os escalões de serviço.
- O preço de armazenamento para uma base de dados é a soma dos dados armazenamento e de registo de armazenamento quantidades multiplicado pelo preço de unidade de armazenamento da camada de serviços. O custo de TempDB está incluído no preço vCore. Para obter detalhes sobre o preço do armazenamento extra, consulte [preços de base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ter reduzir uma base de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerir o espaço de ficheiro na base de dados do Azure SQL](sql-database-file-space-management.md).

## <a name="vcore-based-purchasing-model-change-compute-resources"></a>modelo de compra baseado em vCore: recursos de computação de alteração

Após escolher inicialmente o número de vCores, pode dimensionar uma base de dados ou diminua verticalmente dinamicamente com base na experiência real usando o [portal do Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-server), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), o [CLI do Azure](/cli/azure/sql/db#az-sql-db-update), ou a [REST API](/rest/api/sql/databases/update). 

Alterar o serviço de camada e/ou tamanho de uma base de dados cria uma réplica da base de dados original com o novo tamanho de computação e, em seguida, troca ligações para a réplica de computação. Não são perdidos dados durante este processo mas durante o breve momento em que trocamos para a réplica, as ligações à base de dados são desativadas, pelo que algumas transações em voo poderão ser revertidas. O período de tempo para o comutador-over varia, mas é, geralmente, menos de 4 segundos é inferior a 30 segundos 99% do tempo. Se houver grandes números de transações em voo nas ligações do momento em que estão desativados, o período de tempo para o comutador-over pode demorar mais. 

A duração de todo o processo de aumento vertical depende do tamanho e do escalão de serviço da base de dados antes e após a alteração. Por exemplo, uma base de dados de 250 GB que está a alterar para, a partir ou dentro de uma camada de serviços de fins gerais, deve ser concluído dentro de seis horas. Para uma base de dados do mesmo tamanho que está a alterar os tamanhos de computação dentro da camada de serviço crítico para a empresa, aumentar verticalmente deverá ser concluída no prazo de três horas.

> [!TIP]
> Para monitorizar operações em curso, consulte: [gerir as operações com a API de REST de SQL](/rest/api/sql/Operations/List), [gerir as operações com a CLI](/cli/azure/sql/db/op), [monitorizar as operações com o T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e estes dois Comandos do PowerShell: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) e [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Se estiver a atualizar para um escalão de serviço superior ou tamanho de computação, o tamanho máximo da base de dados não aumenta a menos que especifique explicitamente um tamanho maior (maxsize).
* Para mudar a versão de uma base de dados, o espaço de base de dados utilizada tem de ser menor do que o máximo permitido de tamanho da camada de serviços de destino e o tamanho de computação. 
* Quando atualizar uma base de dados com [georreplicação](sql-database-geo-replication-portal.md) ativada, atualizar seus bancos de dados secundários para o escalão de serviço desejado e tamanho de computação antes de atualizar a base de dados primária (documentação de orientação geral para um melhor desempenho). Ao atualizar para o outro, atualizar a base de dados secundária primeiro é necessário.
* Quando a desatualização de uma base de dados com [georreplicação](sql-database-geo-replication-portal.md) ativada, mudar a versão de seus bancos de dados principal para o escalão de serviço desejado e tamanho de computação antes de reduzir a base de dados secundária (documentação de orientação geral para um melhor desempenho). Ao fazer downgrade para uma edição diferente, desatualização de base de dados primária em primeiro lugar é necessário.
* As novas propriedades para a base de dados não são aplicadas até que as alterações estejam concluídas.

## <a name="dtu-based-purchasing-model-change-storage-size"></a>Modelo de compra baseado em DTU: alterar o tamanho de armazenamento

- O preço DTU para bases de dados única inclui uma certa quantidade de armazenamento sem custos adicionais. Pode ser aprovisionado armazenamento extra para além da quantidade incluída por um custo adicional até ao limite de tamanho máximo em incrementos de 250 GB a 1 TB e, em seguida, em incrementos de 256 GB a partir de 1 TB. Para quantidades de armazenamento incluído e limites de tamanho máximo, consulte [base de dados: tamanhos de armazenamento e tamanhos de computação](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Armazenamento extra para uma base de dados pode ser aprovisionado por seu tamanho máximo do portal do Azure, o aumento [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), o [da CLI do Azure](/cli/azure/sql/db#az-sql-db-update), ou o [ REST API](/rest/api/sql/databases/update).
- O preço do armazenamento extra para uma base de dados é a quantidade de armazenamento extra multiplicada pelo preço de unidade de armazenamento extra da camada de serviços. Para obter detalhes sobre o preço do armazenamento extra, consulte [preços de base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ter reduzir uma base de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerir o espaço de ficheiro na base de dados do Azure SQL](sql-database-file-space-management.md).

## <a name="dtu-based-purchasing-model-change-compute-resources-dtus"></a>Modelo de compra baseado em DTU: recursos (DTUs) de computação de alteração

Após escolher inicialmente um escalão de serviço, o tamanho de computação e a quantidade de armazenamento, pode dimensionar uma base de dados ou diminua verticalmente dinamicamente com base na experiência real com o portal do Azure, [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), o [CLI do Azure](/cli/azure/sql/db#az-sql-db-update), ou o [REST API](/rest/api/sql/databases/update). 

Mostra o vídeo seguinte alterar dinamicamente o serviço de camada e tamanho de aumentar DTUs disponíveis para uma base de dados de computação.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

Alterar o serviço de camada e/ou tamanho de uma base de dados cria uma réplica da base de dados original com o novo tamanho de computação e, em seguida, troca ligações para a réplica de computação. Não são perdidos dados durante este processo mas durante o breve momento em que trocamos para a réplica, as ligações à base de dados são desativadas, pelo que algumas transações em voo poderão ser revertidas. O período de tempo para o comutador-over varia, mas é menos de 30 segundos 99% do tempo. Se houver grandes números de transações em voo nas ligações do momento em que estão desativados, o período de tempo para o comutador-over pode demorar mais. 

A duração de todo o processo de aumento vertical depende do tamanho e do escalão de serviço da base de dados antes e após a alteração. Por exemplo, uma base de dados de 250 GB que está a alterar para, a partir ou dentro de um escalão Standard, deve ser concluído dentro de seis horas. Para uma base de dados do mesmo tamanho que está a alterar os tamanhos de computação dentro da camada de serviço Premium, o aumento vertical deverá ser concluída no prazo de três horas.

> [!TIP]
> Para monitorizar operações em curso, consulte: [gerir as operações com a API de REST de SQL](/rest/api/sql/Operations/List), [gerir as operações com a CLI](/cli/azure/sql/db/op), [monitorizar as operações com o T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e estes dois Comandos do PowerShell: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) e [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Se estiver a atualizar para um escalão de serviço superior ou tamanho de computação, o tamanho máximo da base de dados não aumenta a menos que especifique explicitamente um tamanho maior (maxsize).
* Para mudar a versão de uma base de dados, o espaço de base de dados utilizada tem de ser menor do que o máximo permitido de tamanho da camada de serviços de destino e o tamanho de computação. 
* Quando a mudança do **Premium** para o **padrão** escalão, um custo de armazenamento extra se aplica ao tanto (1) o tamanho máximo da base de dados é suportado no tamanho de computação de destino, e (2) o tamanho máximo excede o incluído tamanho de computação da quantidade de armazenamento de destino. Por exemplo, se uma base de dados de P1 com um tamanho máximo de 500 GB é downsized S3, em seguida, um custo de armazenamento extra aplica-se como S3 suporta um tamanho máximo de 500 GB e a quantidade de armazenamento incluído é apenas de 250 GB. Por isso, a quantidade de armazenamento extra é 500 GB – 250 GB = 250 GB. Para obter os preços do armazenamento extra, consulte [preços de base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço utilizada é inferior à quantidade de armazenamento incluído, em seguida, isso encargos extra pode ser evitado ao reduzir o tamanho máximo da base de dados para o montante incluído. 
* Quando atualizar uma base de dados com [georreplicação](sql-database-geo-replication-portal.md) ativada, atualizar seus bancos de dados secundários para o escalão de serviço desejado e tamanho de computação antes de atualizar a base de dados primária (documentação de orientação geral para um melhor desempenho). Ao atualizar para o outro, atualizar a base de dados secundária primeiro é necessário.
* Quando a desatualização de uma base de dados com [georreplicação](sql-database-geo-replication-portal.md) ativada, mudar a versão de seus bancos de dados principal para o escalão de serviço desejado e tamanho de computação antes de reduzir a base de dados secundária (documentação de orientação geral para um melhor desempenho). Ao fazer downgrade para uma edição diferente, desatualização de base de dados primária em primeiro lugar é necessário.
* As ofertas de serviço de restauro diferem entre os vários escalões de serviço. Se estiver fazendo downgrade para o **básica** escalão, há um período de retenção de cópia de segurança inferior - veja [Backups de banco de dados SQL do Azure](sql-database-automated-backups.md).
* As novas propriedades para a base de dados não são aplicadas até que as alterações estejam concluídas.

## <a name="dtu-based-purchasing-model-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Modelo de compra baseado em DTU: limitações de P11 e P15 quando o número máximo de tamanho superior a 1 TB

Um tamanho máximo superior a 1 TB para P11 e P15 base de dados é suportada nas seguintes regiões: Leste da Austrália, Sudeste da Austrália, sul do Brasil, Canadá Central, leste do Canadá, centro dos E.U.A., Centro de França, Alemanha Central, leste do Japão, oeste do Japão, Coreia Central Centro-Norte, Europa do Norte, EUA Centro-Sul, Sudeste asiático, sul do Reino Unido, oeste do Reino Unido, E.U.A. Leste-2, E.U.A. oeste, US Gov Virginia e Europa Ocidental. As seguintes considerações e limitações aplicam-se às bases de dados P11 e P15 com um maior que 1 TB de tamanho máximo:

- Se escolher um tamanho máximo superior a 1 TB durante a criação de uma base de dados (com um valor de 4 TB ou 4096 GB), o comando create falha com um erro se a base de dados é aprovisionada numa região não suportada.
- Para P11 e P15 bases de dados existentes localizados em uma das regiões suportadas, pode aumentar o armazenamento máximo para além de 1 TB em incrementos de 256 GB até 4 TB. Para ver se um tamanho maior é suportado na sua região, utilize o [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) funcionar ou inspecionar o tamanho da base de dados no portal do Azure. Atualizando uma existente P11 ou P15 base de dados só pode ser executada por um início de sessão principal ao nível do servidor ou por membros da função de base de dados dbmanager. 
- Se uma operação de atualização é executada numa região suportada a configuração é atualizada automaticamente. A base de dados permanece online durante o processo de atualização. No entanto, não pode utilizar a quantia total de armazenamento para além de 1 TB de armazenamento até que os arquivos de banco de dados real foram atualizados para o novo tamanho máximo. O período de tempo necessário depende no tamanho da base de dados a ser atualizado. 
- Quando criar ou atualizar uma base de dados P11 ou P15, só pode escolher entre o tamanho máximo, 1 TB e 4 TB, em incrementos de 256 GB. Ao criar o P11/P15, a opção de armazenamento predefinida de 1 TB é pré-selecionada. Bases de dados localizados em uma das regiões suportadas, pode aumentar o número máximo de armazenamento para até um máximo de 4 TB para uma base de dados nova ou existente. Para todas as outras regiões, o tamanho máximo não pode ser aumentado superiores a 1 TB. O preço não é alterada quando seleciona 4 TB de armazenamento incluído.
- Se o tamanho máximo de uma base de dados estiver definido como superior a 1 TB, em seguida, ele não é possível alterar a 1 TB, mesmo que o armazenamento atual utilizado é abaixo 1 TB. Portanto, não pode mudar um P11 ou P15 com um tamanho máximo superior a 1 TB para um P11 TB 1 ou de 1 TB P15 ou tamanho, por exemplo, P1 – P6 de computação mais baixo). Esta restrição aplica-se também para o restauro e cenários de cópia, incluindo o ponto no tempo, georrestauro, longo-prazo-cópia de segurança-retenção e a cópia da base de dados. Depois de uma base de dados é configurada com um tamanho máximo superior a 1 TB, todas as operações de restauro desta base de dados tem de ser executadas em P11/P15 com um tamanho máximo superior a 1 TB.
- Para cenários de georreplicação ativa:
   - Como configurar uma relação de replicação geográfica: se a base de dados primária estiver P11 ou P15, o secondary(ies) também tem de ser P11 ou P15; tamanho de computação mais baixo são rejeitados como bases de dados secundárias, uma vez que eles não são capazes de oferecer suporte a mais de 1 TB.
   - Atualizar a base de dados principal numa relação de replicação geográfica: alterar o tamanho máximo para mais de 1 TB numa base de dados primária aciona a mesma alteração na base de dados secundário. Ambas as atualizações têm de ser efetuada com êxito para a alteração principal para entrar em vigor. Aplicam a limitações de região para a opção de mais de 1 TB. Se o elemento secundário for numa região que não suporta mais de 1 TB, o principal não está atualizado.
- Não é suportada a utilização do serviço de importação/exportação para carregar o P11/P15 bases de dados com mais de 1 TB. Utilizar SqlPackage.exe para [importe](sql-database-import.md) e [exportar](sql-database-export.md) dados.


## <a name="next-steps"></a>Passos Seguintes

Para geral limites de recursos, consulte [limites dos recursos baseados em vCore de base de dados SQL - bases de dados individuais](sql-database-vcore-resource-limits-single-databases.md) e [limites de recursos baseados em DTU de base de dados do SQL - conjuntos elásticos](sql-database-dtu-resource-limits-single-databases.md).
