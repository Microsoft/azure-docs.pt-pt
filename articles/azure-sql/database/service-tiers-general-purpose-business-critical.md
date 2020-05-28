---
title: Objectivogeral e níveis de serviço críticos do negócio
titleSuffix: Azure SQL Database & SQL Managed Instance
description: O artigo discute os níveis de serviço críticos de propósito geral e negócios no modelo de compra baseado em vCore utilizado pela Azure SQL Database e pela Azure SQL Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 01/30/2020
ms.openlocfilehash: 1783285704870dbcaeac731dc085bddf8851c7be
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84049828"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-service-tiers"></a>Base de Dados Azure SQL e níveis de serviço Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A Base de Dados Azure SQL e a Azure SQL Managed Instance baseiam-se na arquitetura do motor de base de dados SQL Server que é ajustada para o ambiente em nuvem para garantir uma disponibilidade de 99,99%, mesmo que haja uma falha de infraestrutura. Dois níveis de serviço são utilizados pela Azure SQL Database e pela Azure SQL Managed Instance, cada uma com um modelo arquitetônico diferente. Estes níveis de serviço são:

- [Objetivo geral](service-tier-general-purpose.md), concebido para cargas horárias orientadas para o orçamento.
- [Business critical](service-tier-business-critical.md), que é projetado para cargas de trabalho de baixa latência com elevada resiliência a falhas e falhas rápidas.

A Base de Dados Azure SQL tem um nível de serviço adicional: 

- [Hyperscale](service-tier-hyperscale.md), projetado para a maioria das cargas de trabalho empresariais, fornecendo armazenamento altamente escalável, leitura de escala e capacidades de restauro de base de dados rápidas.

Este artigo discute diferenças entre os níveis de serviço, as considerações de armazenamento e backup para o propósito geral e os níveis de serviço críticos do negócio no modelo de compra baseado em vCore.

## <a name="service-tier-comparison"></a>Comparação de nível de serviço

A tabela seguinte descreve as principais diferenças entre os níveis de serviço para a última geração (Gen5). Note que as características do nível de serviço podem ser diferentes na Base de Dados SQL e na Instância Gerida SQL.

| | Tipo de recurso | Fins Gerais |  Hyperscale | Business Critical |
|:---:|:---:|:---:|:---:|:---:|
| **O melhor para** | |  Oferece opções de cálculo e armazenamento equilibradas orientadas para o orçamento. | A maioria das cargas de trabalho de negócios. Tamanho de armazenamento de auto-escalaaté 100 TB, escalação de computação vertical e horizontal fluida, restauração rápida da base de dados. | Aplicações OLTP com elevada taxa de transação e baixa latência iO. Oferece a maior resiliência a falhas e falhas rápidas usando múltiplas réplicas sincronizadamente atualizadas.|
|  **Disponível no tipo de recurso:** ||Base de Dados SQL / Instância Gerida SQL | Base de Dados SQL Azure Única | Base de Dados SQL / Instância Gerida SQL |
| **Tamanho da computação**| Base de Dados SQL | 1 a 80 vCores | 1 a 80 vCores | 1 a 80 vCores |
| | Instância Gerida do SQL | 4, 8, 16, 24, 32, 40, 64, 80 vCores | N/D | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| | Piscinas de instância gerida sQL | 2, 4, 8, 16, 24, 32, 40, 64, 80 vCores | N/D | N/D |
| **Tipo de armazenamento** | Todos | Armazenamento remoto premium (por exemplo) | Armazenamento dissociado com cache SSD local (por exemplo) | Armazenamento sSD local super rápido (por exemplo) |
| **Tamanho da base de dados** | Base de Dados SQL | 5 GB - 4 TB | Até 100 TB | 5 GB - 4 TB |
| | Instância Gerida do SQL  | 32 GB - 8 TB | N/D | 32 GB - 4 TB |
| **Tamanho do armazenamento** | Base de Dados SQL | 5 GB - 4 TB | Até 100 TB | 5 GB - 4 TB |
| | Instância Gerida do SQL  | 32 GB - 8 TB | N/D | 32 GB - 4 TB |
| **Tamanho TempDB** | Base de Dados SQL | [32 GB por vCore](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB por vCore](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB por vCore](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Instância Gerida do SQL  | [24 GB por vCore](../managed-instance/resource-limits.md#service-tier-characteristics) | N/D | Até 4 TB - [limitado pelo tamanho do armazenamento](../managed-instance/resource-limits.md#service-tier-characteristics) |
| **Log write entrada** | Base de Dados SQL | [1.875 MB/s por vCore (máx. 30 MB/s)](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/s por vCore (máx. 96 MB/s)](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Instância Gerida do SQL | [3 MB/s por vCore (máx. 22 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) | N/D | [4 MB/s por vcore (máx. 48 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) |
|**Disponibilidade**|Todos| 99,99% |  [99,95% com uma réplica secundária, 99,99% com mais réplicas](service-tier-hyperscale-frequently-asked-questions-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99,99% <br/> [99,995% com base de dados única redundante da zona](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Cópias de segurança**|Todos|RA-GRS, 7-35 dias (7 dias por padrão)| RA-GRS, 7 dias, recuperação constante do tempo no tempo (PITR) | RA-GRS, 7-35 dias (7 dias por padrão) |
|**OLTP em memória** | | N/D | N/D | Disponível |
|**Réplicas só de leitura**| | 0 embutido <br> 0 - 4 utilizando [a geo-replicação](active-geo-replication-overview.md) | 0 - 4 embutidos | 1 incorporado, incluído no preço <br> 0 - 4 utilizando [a geo-replicação](active-geo-replication-overview.md) |
|**Preços/faturação** | Base de Dados SQL | [vCore, armazenamento reservado e armazenamento de reserva](https://azure.microsoft.com/pricing/details/sql-database/single/) são cobrados. <br/>O IOPS não é cobrado. | [vCore para cada réplica e armazenamento usado](https://azure.microsoft.com/pricing/details/sql-database/single/) são carregados. <br/>IOPS ainda não cobrado. | [vCore, armazenamento reservado e armazenamento de reserva](https://azure.microsoft.com/pricing/details/sql-database/single/) são cobrados. <br/>O IOPS não é cobrado. |
|| Instância Gerida do SQL | [vCore, armazenamento reservado e armazenamento de reserva](https://azure.microsoft.com/pricing/details/sql-database/managed/) é cobrado. <br/>IOPS não é cobrado| N/D | [vCore, armazenamento reservado e armazenamento de reserva](https://azure.microsoft.com/pricing/details/sql-database/managed/) é cobrado. <br/>O IOPS não é cobrado.| 
|**Modelos de desconto**| | [Instâncias reservadas](reserved-capacity-overview.md)<br/>[Benefício Híbrido Azure](../azure-hybrid-benefit.md) (não disponível em assinaturas de dev/teste)<br/>[Subscrições enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) e [pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0023p/) Dev/Test| [Benefício Híbrido Azure](../azure-hybrid-benefit.md) (não disponível em assinaturas de dev/teste)<br/>[Subscrições enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) e [pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0023p/) Dev/Test| [Instâncias reservadas](reserved-capacity-overview.md)<br/>[Benefício Híbrido Azure](../azure-hybrid-benefit.md) (não disponível em assinaturas de dev/teste)<br/>[Subscrições enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) e [pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0023p/) Dev/Test|

Para mais informações, consulte as diferenças detalhadas entre os níveis de serviço na Base de [Dados Azure SQL (vCore)](resource-limits-vcore-single-databases.md), única Base de [Dados Azure SQL (DTU)](resource-limits-dtu-single-databases.md), base de [dados Azure SQL (DTU) e](resource-limits-dtu-single-databases.md)páginas De instância gerida [sql Azure.](../managed-instance/resource-limits.md)

> [!NOTE]
> Para obter informações sobre o nível de serviço de hiperescala no modelo de compra baseado em vCore, consulte o [nível de serviço de hiperescala](service-tier-hyperscale.md). Para uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte [modelos e recursos](purchasing-models.md)de compra.

## <a name="data-and-log-storage"></a>Armazenamento de dados e registos

Os seguintes fatores afetam a quantidade de armazenamento utilizado para dados e ficheiros de registo, e aplicam-se ao Propósito Geral e ao Business Critical. Para mais detalhes sobre dados e armazenamento de registo em Hyperscale, consulte o [nível de serviço Hyperscale](service-tier-hyperscale.md).

- O armazenamento atribuído é utilizado por ficheiros de dados (MDF) e ficheiros de registo (LDF).
- Cada tamanho de computação de base de dados suporta um tamanho máximo de base de dados, com um tamanho máximo padrão de 32 GB.
- Quando configura ruma o tamanho de base de dados único necessário (o tamanho do ficheiro MDF), 30% mais armazenamento adicional é adicionado automaticamente para suportar ficheiros LDF.
- Pode selecionar qualquer tamanho de base de dados único entre 10 GB e o máximo suportado.
  - Para armazenamento nos níveis de serviço padrão ou geral, aumente ou diminua o tamanho em incrementos de 10 GB.
  - Para armazenamento nos níveis de serviço críticos premium ou empresariais, aumente ou diminua o tamanho em incrementos de 250 GB.
- No nível de serviço para fins gerais, `tempdb` utiliza um SSD anexado, e este custo de armazenamento está incluído no preço vCore.
- No nível de serviço crítico do negócio, `tempdb` partilha o SSD anexado com os ficheiros MDF e LDF, e o custo de `tempdb` armazenamento está incluído no preço vCore.
- O tamanho do armazenamento de uma instância gerida por SQL deve ser especificado em múltiplos de 32 GB.


> [!IMPORTANT]
> É cobrado o armazenamento total atribuído aos ficheiros MDF e LDF.

Para monitorizar o tamanho total atual dos seus ficheiros MDF e LDF, utilize [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Para monitorizar o tamanho atual dos ficheiros MDF e LDF individuais, utilize [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ser necessário encolher uma base de dados para recuperar espaço não utilizado. Para mais informações, consulte Gerir o espaço de ficheiros na Base de [Dados Azure SQL](file-space-manage.md).

## <a name="backups-and-storage"></a>Backups e armazenamento

O armazenamento de cópias de dados é atribuído para suportar as capacidades de restauro pontual (PITR) e [de retenção a longo prazo (LTR)](long-term-retention-overview.md) da Base de Dados SQL e da Instância Gerida SQL. Este armazenamento é atribuído separadamente para cada base de dados e faturado como dois encargos por base de dados separados.

- **PITR**: As cópias de cópias de cópias de dados individuais para [o armazenamento geo-redundante de acesso a leitura (RA-GRS)](../../storage/common/geo-redundant-design.md) automaticamente. O tamanho do armazenamento aumenta dinamicamente à medida que novas cópias de segurança são criadas. O armazenamento é utilizado semanalmente por cópias completas semanais, cópias diárias de backups diferenciais e cópias de registo de transações, que são copiadas a cada 5 minutos. O consumo de armazenamento depende da taxa de alteração da base de dados e do período de retenção das cópias de segurança. Pode configurar um período de retenção separado para cada base de dados entre 7 e 35 dias. Um valor mínimo de armazenamento igual a 100% (1x) do tamanho da base de dados não é fornecido sem custos adicionais. Para a maioria das bases de dados, este valor é suficiente para armazenar 7 dias de backups.
- **LTR**: Também tem a opção de configurar a retenção a longo prazo de cópias de segurança completas por um período máximo de 10 anos (esta funcionalidade encontra-se em [pré-visualização pública limitada para a Instância Gerida sQL](long-term-retention-overview.md#managed-instance-support). Se configurar uma política LTR, estas cópias de segurança são armazenadas automaticamente no armazenamento DE RA-GRS, mas pode controlar a frequência com que as cópias são copiadas. Para satisfazer diferentes requisitos de conformidade, pode selecionar diferentes períodos de retenção para backups semanais, mensais e/ou anuais. A configuração que escolher determina quanto armazenamento será usado para cópias de segurança LTR. Para estimar o custo do armazenamento LTR, pode utilizar a calculadora de preços LTR. Para mais informações, consulte a [Base de Dados SQL](long-term-retention-overview.md)de retenção a longo prazo .

## <a name="next-steps"></a>Próximos passos

Para mais detalhes sobre os tamanhos específicos de cálculo e armazenamento disponíveis nos níveis de serviço críticos de finalidade geral e negócios, consulte: 

- [limites de recursos baseados em vCore para](resource-limits-vcore-single-databases.md)base de dados Azure SQL .
- [limites de recursos baseados em vCore para bases de dados agrofadas na Base de Dados Azure SQL](resource-limits-vcore-elastic-pools.md).
- [limites de recursos baseados em vCore para a instância gerida azure SQL](../managed-instance/resource-limits.md). 

