---
title: Banco de dados SQL do Azure-uso geral e comercialmente crítico | Microsoft Docs
description: O artigo discute as camadas de serviço de uso geral e crítico para os negócios no modelo de compra baseado em vCore.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 02/23/2019
ms.openlocfilehash: decb4428321d5083d6ba7af134e223eb2fa5a912
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566699"
---
# <a name="azure-sql-database-service-tiers"></a>Camadas de serviço do banco de dados SQL do Azure

O banco de dados SQL do Azure baseia-se na arquitetura do mecanismo de banco de dados SQL Server que é ajustada para o ambiente de nuvem para garantir a disponibilidade de 99,99%, mesmo se houver uma falha de infraestrutura. Três camadas de serviço são usadas no banco de dados SQL do Azure, cada uma com um modelo de arquitetura diferente. Essas camadas de serviço são:

- [Uso geral](sql-database-service-tier-general-purpose.md), que é projetado para a maioria das cargas de trabalho genéricas.
- [Comercialmente crítico](sql-database-service-tier-business-critical.md), projetado para cargas de trabalho de baixa latência com uma réplica legível.
- [Hiperescala](sql-database-service-tier-hyperscale.md), projetada para bancos de dados muito grandes (até 100 TB) com várias réplicas legíveis.

Este artigo discute considerações de armazenamento e backup para as camadas de serviço de uso geral e crítico para os negócios no modelo de compra baseado em vCore.

> [!NOTE]
> Para obter informações sobre a camada de serviço de hiperescala no modelo de compra baseado em vCore, consulte [camada de serviço](sql-database-service-tier-hyperscale.md)de hiperescala. Para obter uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte [recursos e modelos de compra do banco de dados SQL do Azure](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Armazenamento de dados e de log

Os seguintes fatores afetam a quantidade de armazenamento usada para arquivos de dados e de log:

- O armazenamento alocado é usado por arquivos de dados (MDF) e arquivos de log (LDF).
- Cada tamanho de computação de banco de dados individual dá suporte a um tamanho máximo de banco de dados, com um tamanho máximo padrão de 32 GB.
- Quando você configura o tamanho necessário do banco de dados individual (o tamanho do arquivo MDF), 30% mais armazenamento adicional é adicionado automaticamente para dar suporte a arquivos LDF.
- O tamanho do armazenamento para uma instância gerenciada do banco de dados SQL deve ser especificado em múltiplos de 32 GB.
- Você pode selecionar qualquer tamanho de banco de dados individual entre 10 GB e o máximo com suporte.
  - Para armazenamento nas camadas de serviço padrão ou de uso geral, aumente ou diminua o tamanho em incrementos de 10 GB.
  - Para armazenamento nas camadas de serviço Premium ou comercialmente crítico, aumente ou diminua o tamanho em incrementos de 250 GB.
- Na camada de serviço de uso geral `tempdb` , o usa um SSD anexado e esse custo de armazenamento é incluído no preço vCore.
- Na camada de serviço comercialmente crítico `tempdb` , o compartilha o SSD anexado com os arquivos MDF e ldf, `tempdb` e o custo de armazenamento é incluído no preço vCore.

> [!IMPORTANT]
> Você é cobrado pelo armazenamento total alocado para arquivos MDF e LDF.

Para monitorar o tamanho total atual de seus arquivos MDF e LDF, use [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Para monitorar o tamanho atual dos arquivos MDF e LDF individuais, use [Sys. database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ter reduzir uma base de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerir o espaço de ficheiro na base de dados do Azure SQL](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Backups e armazenamento

O armazenamento de backups de banco de dados é alocado para dar suporte a recursos de PITR (restauração pontual) e [EPD (retenção de longo prazo)](sql-database-long-term-retention.md) do banco de dados SQL. Esse armazenamento é alocado separadamente para cada banco de dados e cobrado como dois encargos separados por banco de dados.

- **PITR**: Os backups de banco de dados individuais são copiados para o [armazenamento com redundância geográfica (ra-grs) de acesso de leitura](../storage/common/storage-designing-ha-apps-with-ragrs.md) automaticamente. O tamanho do armazenamento aumenta dinamicamente à medida que novos backups são criados. O armazenamento é usado por backups completos semanais, backups diferenciais diários e backups de log de transações, que são copiados a cada 5 minutos. O consumo de armazenamento depende da taxa de alteração do banco de dados e do período de retenção para backups. Você pode configurar um período de retenção separado para cada banco de dados entre 7 e 35 dias. Um valor mínimo de armazenamento igual a 100 por cento (1x) do tamanho do banco de dados é fornecido sem custo adicional. Para a maioria dos bancos de dados, esse valor é suficiente para armazenar 7 dias de backups.
- **LTR**: O banco de dados SQL oferece a opção de configurar a retenção de longo prazo de backups completos por até 10 anos. Se você configurar uma política EPD, esses backups serão armazenados automaticamente no armazenamento RA-GRS, mas você poderá controlar a frequência com que os backups são copiados. Para atender aos diferentes requisitos de conformidade, você pode selecionar períodos de retenção diferentes para backups semanais, mensais e/ou anuais. A configuração escolhida determina a quantidade de armazenamento que será usada para backups EPD. Para estimar o custo do armazenamento EPD, você pode usar a calculadora de preços EPD. Para obter mais informações, consulte [retenção de longo prazo do banco de dados SQL](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Passos Seguintes

- Para obter detalhes sobre os tamanhos de computação específicos e os tamanhos de armazenamento disponíveis para um único banco de dados nas camadas de serviço de uso geral e crítico para os negócios, consulte [limites de recursos baseados em vCore do banco de dados SQL para bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md).
- Para obter detalhes sobre os tamanhos de computação e tamanhos de armazenamento específicos disponíveis para pools elásticos nas camadas de serviço de uso geral e crítico para os negócios, consulte [limites de recursos baseados em vCore do banco de dados SQL para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md).
