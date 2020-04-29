---
title: Propósito geral e crítico comercial
description: O artigo discute o propósito geral e os níveis de serviço críticos do negócio no modelo de compra baseado em vCore.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 01/30/2020
ms.openlocfilehash: 09cc9e1475616700aa77cdf92fd7ca808cd4290c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76937842"
---
# <a name="azure-sql-database-service-tiers"></a>Níveis de serviço de base de dados Azure SQL

A Base de Dados Azure SQL baseia-se na arquitetura do motor de base de dados SQL Server que é ajustada para o ambiente em nuvem para garantir uma disponibilidade de 99,99%, mesmo que haja uma falha de infraestrutura. Três níveis de serviço são usados na Base de Dados Azure SQL, cada um com um modelo arquitetônico diferente. Estes níveis de serviço são:

- [Objetivo geral](sql-database-service-tier-general-purpose.md), concebido para cargas horárias orientadas para o orçamento.
- [Hyperscale](sql-database-service-tier-hyperscale.md), projetado para a maioria das cargas de trabalho empresariais, fornecendo armazenamento altamente escalável, leitura de escala e capacidades de restauro de base de dados rápidas.
- Crítico de [negócios](sql-database-service-tier-business-critical.md),que é projetado para cargas de trabalho de baixa latência com elevada resiliência a falhas e falhas rápidas.

Este artigo discute diferenças de ser os níveis de serviço, as considerações de armazenamento e backup para o fim geral e os níveis de serviço críticos do negócio no modelo de compra baseado em vCore.

## <a name="service-tier-comparison"></a>Comparação de nível de serviço

A tabela seguinte descreve as principais diferenças entre os níveis de serviço para a última geração (Gen5). Note que as características do nível de serviço podem ser diferentes na Base de Dados Única e na Instância Gerida.

| | Tipo de recurso | Fins Gerais |  Hyperscale | Business Critical |
|:---:|:---:|:---:|:---:|:---:|
| **O melhor para** | |  Oferece opções de cálculo e armazenamento equilibradas orientadas para o orçamento. | A maioria das cargas de trabalho de negócios. Tamanho de armazenamento de auto-escalaaté 100 TB, escalação de computação vertical e horizontal fluida, restauração rápida da base de dados. | Aplicações OLTP com elevada taxa de transação e baixa latência iO. Oferece a maior resiliência a falhas e falhas rápidas usando múltiplas réplicas sincronizadamente atualizadas.|
|  **Disponível no tipo de recurso:** ||Base de dados única / piscina elástica / instância gerida | Base de dados individual | Base de dados única / piscina elástica / instância gerida |
| **Tamanho da computação**|Base de dados única / piscina elástica | 1 a 80 vCores | 1 a 80 vCores | 1 a 80 vCores |
| | Instância gerida | 4, 8, 16, 24, 32, 40, 64, 80 vCores | N/D | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| | Piscinas de instânciageridas | 2, 4, 8, 16, 24, 32, 40, 64, 80 vCores | N/D | N/D |
| **Tipo de armazenamento** | Todos | Armazenamento remoto premium (por exemplo) | Armazenamento dissociado com cache SSD local (por exemplo) | Armazenamento sSD local super rápido (por exemplo) |
| **Tamanho da base de dados** | Base de dados única / piscina elástica | 5 GB - 4 TB | Até 100 TB | 5 GB - 4 TB |
| | Instância gerida  | 32 GB - 8 TB | N/D | 32 GB - 4 TB |
| **Tamanho do armazenamento** | Base de dados única / piscina elástica | 5 GB - 4 TB | Até 100 TB | 5 GB - 4 TB |
| | Instância gerida  | 32 GB - 8 TB | N/D | 32 GB - 4 TB |
| **Tamanho TempDB** | Base de dados única / piscina elástica | [32 GB por vCore](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB por vCore](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB por vCore](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Instância gerida  | [24 GB por vCore](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N/D | Até 4 TB - [limitado pelo tamanho do armazenamento](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
| **Log write entrada** | Base de dados individual | [1.875 MB/s por vCore (máx. 30 MB/s)](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/s por vCore (máx. 96 MB/s)](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Instância gerida | [3 MB/s por vCore (máx. 22 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N/D | [4 MB/s por vcore (máx. 48 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**Disponibilidade**|Todos| 99,99% |  [99,95% com uma réplica secundária, 99,99% com mais réplicas](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99,99% <br/> [99,995% com base de dados única redundante da zona](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Cópias de segurança**|Todos|RA-GRS, 7-35 dias (7 dias por padrão)| RA-GRS, 7 dias, recuperação constante do tempo no tempo (PITR) | RA-GRS, 7-35 dias (7 dias por padrão) |
|**OLTP em memória** | | N/D | N/D | Disponível |
|**Réplicas só de leitura**| | 0 embutido <br> 0 - 4 utilizando [a geo-replicação](sql-database-active-geo-replication.md) | 0 - 4 embutidos | 1 incorporado, incluído no preço <br> 0 - 4 utilizando [a geo-replicação](sql-database-active-geo-replication.md) |
|**Preços/faturação** | Base de dados individual | [vCore, armazenamento reservado e armazenamento de reserva](https://azure.microsoft.com/pricing/details/sql-database/single/) são cobrados. <br/>O IOPS não é cobrado. | [vCore para cada réplica e armazenamento usado](https://azure.microsoft.com/pricing/details/sql-database/single/) são carregados. <br/>IOPS ainda não cobrado. | [vCore, armazenamento reservado e armazenamento de reserva](https://azure.microsoft.com/pricing/details/sql-database/single/) são cobrados. <br/>O IOPS não é cobrado. |
|| Instância Gerida | [vCore, armazenamento reservado e armazenamento de reserva](https://azure.microsoft.com/pricing/details/sql-database/managed/) é cobrado. <br/>IOPS não é cobrado| N/D | [vCore, armazenamento reservado e armazenamento de reserva](https://azure.microsoft.com/pricing/details/sql-database/managed/) é cobrado. <br/>O IOPS não é cobrado.| 
|**Modelos de desconto**| | [Instâncias reservadas](sql-database-reserved-capacity.md)<br/>[Benefício Híbrido Azure](sql-database-azure-hybrid-benefit.md) (não disponível em assinaturas de dev/teste)<br/>[Subscrições enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) e [pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0023p/) Dev/Test| [Benefício Híbrido Azure](sql-database-azure-hybrid-benefit.md) (não disponível em assinaturas de dev/teste)<br/>[Subscrições enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) e [pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0023p/) Dev/Test| [Instâncias reservadas](sql-database-reserved-capacity.md)<br/>[Benefício Híbrido Azure](sql-database-azure-hybrid-benefit.md) (não disponível em assinaturas de dev/teste)<br/>[Subscrições enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) e [pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0023p/) Dev/Test|

Para obter mais informações, consulte as diferenças detalhadas entre os níveis de serviço na base de [dados single (vCore)](sql-database-vcore-resource-limits-single-databases.md), piscinas de bases de [dados únicas (vCore)](sql-database-dtu-resource-limits-single-databases.md), base de [dados única (DTU)](sql-database-dtu-resource-limits-single-databases.md), bases de [dados únicas (DTU)](sql-database-dtu-resource-limits-single-databases.md)e páginas [De Instância Gerida.](sql-database-managed-instance-resource-limits.md)

> [!NOTE]
> Para obter informações sobre o nível de serviço de hiperescala no modelo de compra baseado em vCore, consulte o [nível de serviço de hiperescala](sql-database-service-tier-hyperscale.md). Para uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte os modelos e recursos de compra da Base de [Dados Azure SQL.](sql-database-purchase-models.md)

## <a name="data-and-log-storage"></a>Armazenamento de dados e registos

Os seguintes fatores afetam a quantidade de armazenamento utilizado para dados e ficheiros de registo, e aplicam-se ao General Purpose e ao Business Critical. Para mais detalhes sobre dados e armazenamento de registo em Hyperscale, consulte o [nível de serviço Hyperscale](sql-database-service-tier-hyperscale.md).

- O armazenamento atribuído é utilizado por ficheiros de dados (MDF) e ficheiros de registo (LDF).
- Cada tamanho de computação de base de dados suporta um tamanho máximo de base de dados, com um tamanho máximo padrão de 32 GB.
- Quando configura ruma o tamanho de base de dados único necessário (o tamanho do ficheiro MDF), 30% mais armazenamento adicional é adicionado automaticamente para suportar ficheiros LDF.
- O tamanho do armazenamento de uma instância gerida pela Base de Dados SQL deve ser especificado em múltiplos de 32 GB.
- Pode selecionar qualquer tamanho de base de dados único entre 10 GB e o máximo suportado.
  - Para armazenamento nos níveis de serviço padrão ou geral, aumente ou diminua o tamanho em incrementos de 10 GB.
  - Para armazenamento nos níveis de serviço críticos premium ou empresariais, aumente ou diminua o tamanho em incrementos de 250 GB.
- No nível de `tempdb` serviço para fins gerais, utiliza um SSD anexado, e este custo de armazenamento está incluído no preço vCore.
- No nível de `tempdb` serviço crítico do negócio, partilha o SSD `tempdb` anexado com os ficheiros MDF e LDF, e o custo de armazenamento está incluído no preço vCore.

> [!IMPORTANT]
> É cobrado o armazenamento total atribuído aos ficheiros MDF e LDF.

Para monitorizar o tamanho total atual dos seus ficheiros MDF e LDF, utilize [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Para monitorizar o tamanho atual dos ficheiros MDF e LDF individuais, utilize [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ser necessário encolher uma base de dados para recuperar espaço não utilizado. Para mais informações, consulte Gerir o espaço de ficheiros na Base de [Dados Azure SQL](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Backups e armazenamento

O armazenamento de cópias de dados é atribuído para suportar as capacidades de restauro pontual (PITR) e [de retenção a longo prazo (LTR)](sql-database-long-term-retention.md) da Base de Dados SQL. Este armazenamento é atribuído separadamente para cada base de dados e faturado como dois encargos por base de dados separados.

- **PITR**: As cópias de cópias de cópias de dados individuais para [o armazenamento geo-redundante de acesso a leitura (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md) automaticamente. O tamanho do armazenamento aumenta dinamicamente à medida que novas cópias de segurança são criadas. O armazenamento é utilizado semanalmente por cópias completas semanais, cópias diárias de backups diferenciais e cópias de registo de transações, que são copiadas a cada 5 minutos. O consumo de armazenamento depende da taxa de alteração da base de dados e do período de retenção das cópias de segurança. Pode configurar um período de retenção separado para cada base de dados entre 7 e 35 dias. Um valor mínimo de armazenamento igual a 100% (1x) do tamanho da base de dados não é fornecido sem custos adicionais. Para a maioria das bases de dados, este valor é suficiente para armazenar 7 dias de backups.
- **LTR**: SQL Database oferece-lhe a opção de configurar a retenção a longo prazo de cópias de segurança completas por um período máximo de 10 anos. Se configurar uma política LTR, estas cópias de segurança são armazenadas automaticamente no armazenamento DE RA-GRS, mas pode controlar a frequência com que as cópias são copiadas. Para satisfazer diferentes requisitos de conformidade, pode selecionar diferentes períodos de retenção para backups semanais, mensais e/ou anuais. A configuração que escolher determina quanto armazenamento será usado para cópias de segurança LTR. Para estimar o custo do armazenamento LTR, pode utilizar a calculadora de preços LTR. Para mais informações, consulte a [Base de Dados SQL](sql-database-long-term-retention.md)de retenção a longo prazo .

## <a name="next-steps"></a>Passos seguintes

- Para obter detalhes sobre os tamanhos específicos da computação e os tamanhos de armazenamento disponíveis para uma única base de dados nos níveis de serviço críticos de finalidade geral e comercial, consulte os limites de recursos baseados na Base de [Dados SQL vCore para bases](sql-database-vcore-resource-limits-single-databases.md)de dados únicas .
- Para mais detalhes sobre os tamanhos específicos da computação e tamanhos de armazenamento disponíveis para piscinas elásticas nos níveis de serviço críticos de finalidade geral e negócios, consulte os limites de recursos baseados na Base de [Dados SQL vCore para piscinas elásticas.](sql-database-vcore-resource-limits-elastic-pools.md)
