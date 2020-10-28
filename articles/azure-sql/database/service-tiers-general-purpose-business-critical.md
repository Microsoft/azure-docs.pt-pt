---
title: Finalidade geral e níveis críticos de serviços empresariais
titleSuffix: Azure SQL Database & SQL Managed Instance
description: O artigo discute os níveis gerais de serviço crítico e de negócio no modelo de compra baseado em vCore utilizado pela Azure SQL Database e pela Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 01/30/2020
ms.openlocfilehash: 33c63ffc4220da6d98c462039897067e4ba69491
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793165"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-service-tiers"></a>Azure SQL Database e Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A azure SQL Database e Azure SQL Managed Instance são baseados na arquitetura do motor de base de dados sql server SQL que é ajustado para o ambiente em nuvem para garantir 99,99% de disponibilidade, mesmo que haja uma falha de infraestrutura. Dois níveis de serviço são utilizados pela Azure SQL Database e pela Azure SQL Managed Instance, cada uma com um modelo arquitetónico diferente. Estes níveis de serviço são:

- [Finalidade geral](service-tier-general-purpose.md), concebida para cargas de trabalho orientadas para o orçamento.
- [Critical business](service-tier-business-critical.md), que é projetado para cargas de carga de baixa latência com alta resiliência a falhas e falhas rápidas.

A base de dados Azure SQL tem um nível de serviço adicional: 

- [Hiperescala](service-tier-hyperscale.md), que é projetada para a maioria das cargas de trabalho de negócios, fornecendo armazenamento altamente escalável, leitura de escala e capacidades rápidas de restauro da base de dados.

Este artigo discute diferenças entre os níveis de serviço, as considerações de armazenamento e backup para o fim geral e os níveis críticos de serviços no modelo de compra baseado em vCore.

## <a name="service-tier-comparison"></a>Comparação do nível de serviço

A tabela seguinte descreve as principais diferenças entre os níveis de serviço para a última geração (Gen5). Note que as características do nível de serviço podem ser diferentes na Base de Dados SQL e na SQL Managed Instance.

|-| Tipo de recurso | Fins Gerais |  Hyperscale | Crítico para a Empresa |
|:---:|:---:|:---:|:---:|:---:|
| **Melhor para** | |  Oferece opções de cálculo e armazenamento equilibrados orientados para o orçamento. | A maioria das cargas de trabalho. Tamanho de armazenamento de escala automática até 100 TB, escala de cálculo vertical e horizontal de fluido, restauração rápida da base de dados. | Aplicações OLTP com alta taxa de transação e baixa latência IO. Oferece maior resiliência a falhas e falhas rápidas usando múltiplas réplicas sincronizadas atualizadas.|
|  **Disponível no tipo de recurso:** ||SQL Database / SQL Managed Instance | Única base de dados Azure SQL | SQL Database / SQL Managed Instance |
| **Tamanho do cálculo**| SQL Database | 1 a 80 vCores | 1 a 80 vCores | 1 a 80 vCores |
| | Instância Gerida do SQL | 4, 8, 16, 24, 32, 40, 64, 80 vCores | N/D | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| | Piscinas de instância gerida SQL | 2, 4, 8, 16, 24, 32, 40, 64, 80 vCores | N/D | N/D |
| **Tipo de armazenamento** | Todos | Armazenamento remoto premium (por exemplo) | Armazenamento desacopdo com cache SSD local (por exemplo) | Armazenamento SSD local super rápido (por exemplo) |
| **Tamanho da base de dados** | SQL Database | 5 GB - 4 TB | Até 100 TB | 5 GB - 4 TB |
| | Instância Gerida do SQL  | 32 GB - 8 TB | N/D | 32 GB - 4 TB |
| **Tamanho do armazenamento** | SQL Database | 5 GB - 4 TB | Até 100 TB | 5 GB - 4 TB |
| | Instância Gerida do SQL  | 32 GB - 8 TB | N/D | 32 GB - 4 TB |
| **Tamanho tempDB** | SQL Database | [32 GB por vCore](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB por vCore](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB por vCore](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Instância Gerida do SQL  | [24 GB por vCore](../managed-instance/resource-limits.md#service-tier-characteristics) | N/D | Até 4 TB - [limitado pelo tamanho de armazenamento](../managed-instance/resource-limits.md#service-tier-characteristics) |
| **Log write produção** | SQL Database | [1.875 MB/s por vCore (máx. 30 MB/s)](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/s por vCore (máx. 96 MB/s)](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Instância Gerida do SQL | [3 MB/s por vCore (máx. 22 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) | N/D | [4 MB/s por vcore (máx. 48 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) |
|**Disponibilidade**|Todos| 99,99% |  [99,95% com uma réplica secundária, 99,99% com mais réplicas](service-tier-hyperscale-frequently-asked-questions-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99,99% <br/> [99,995% com zona redundante base de dados única](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Cópias de segurança**|Todos|RA-GRS, 7-35 dias (7 dias por defeito)| RA-GRS, 7 dias, recuperação constante do tempo no tempo (PITR) | RA-GRS, 7-35 dias (7 dias por defeito) |
|**OLTP dentro da memória** | | N/D | N/D | Disponível |
|**Réplicas só de leitura**| | 0 embutido <br> 0 - 4 utilizando [a geo-replicação](active-geo-replication-overview.md) | 0 - 4 embutidos | 1 incorporado, incluído no preço <br> 0 - 4 utilizando [a geo-replicação](active-geo-replication-overview.md) |
|**Preços/faturação** | SQL Database | [vCore, armazenamento reservado e armazenamento de reserva](https://azure.microsoft.com/pricing/details/sql-database/single/) são cobrados. <br/>O IOPS não é cobrado. | [vCore para cada réplica e armazenamento usado](https://azure.microsoft.com/pricing/details/sql-database/single/) são carregados. <br/>IOPS ainda não cobrado. | [vCore, armazenamento reservado e armazenamento de reserva](https://azure.microsoft.com/pricing/details/sql-database/single/) são cobrados. <br/>O IOPS não é cobrado. |
|| Instância Gerida do SQL | [vCore, armazenamento reservado e armazenamento de reserva](https://azure.microsoft.com/pricing/details/sql-database/managed/) são cobrados. <br/>IOPS não é cobrado| N/D | [vCore, armazenamento reservado e armazenamento de reserva](https://azure.microsoft.com/pricing/details/sql-database/managed/) são cobrados. <br/>O IOPS não é cobrado.| 
|**Modelos de desconto**| | [Instâncias reservadas](reserved-capacity-overview.md)<br/>[Benefício Híbrido Azure](../azure-hybrid-benefit.md) (não disponível em subscrições de dev/teste)<br/>[Assinaturas Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) e [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0023p/) Dev/Test| [Benefício Híbrido Azure](../azure-hybrid-benefit.md) (não disponível em subscrições de dev/teste)<br/>[Assinaturas Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) e [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0023p/) Dev/Test| [Instâncias reservadas](reserved-capacity-overview.md)<br/>[Benefício Híbrido Azure](../azure-hybrid-benefit.md) (não disponível em subscrições de dev/teste)<br/>[Assinaturas Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) e [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0023p/) Dev/Test|

Para mais informações, consulte as diferenças detalhadas entre os níveis de serviço na [Base de Dados Azure SQL (vCore)](resource-limits-vcore-single-databases.md), [única Base de Dados Azure SQL (DTU)](resource-limits-dtu-single-databases.md), [base de dados Azure SQL (DTU)](resource-limits-dtu-single-databases.md)e páginas [de Instância Gerida Azure SQL.](../managed-instance/resource-limits.md)

> [!NOTE]
> Para obter informações sobre o nível de serviço de hiperescala no modelo de compra baseado em vCore, consulte o nível de [serviço de hiperescala](service-tier-hyperscale.md). Para comparar o modelo de compra baseado em vCore com o modelo de compra baseado na DTU, consulte [modelos e recursos de compra.](purchasing-models.md)

## <a name="data-and-log-storage"></a>Armazenamento de dados e registos

Os seguintes fatores afetam a quantidade de armazenamento utilizado para dados e ficheiros de registo, e aplicam-se a Finalidade Geral e Crítica de Negócio. Para obter detalhes sobre os dados e o armazenamento de registo em Hyperscale, consulte [o nível de serviço hyperscale](service-tier-hyperscale.md).

- O armazenamento atribuído é utilizado por ficheiros de dados (MDF) e ficheiros de registo (LDF).
- Cada tamanho de cálculo de base de dados suporta um tamanho máximo de base de dados, com um tamanho máximo predefinido de 32 GB.
- Quando configurar o tamanho de base de dados único necessário (o tamanho do ficheiro MDF), 30% mais armazenamento adicional é automaticamente adicionado para suportar ficheiros LDF.
- Pode selecionar qualquer tamanho de base de dados entre 10 GB e o máximo suportado.
  - Para armazenamento nos níveis de serviço de finalidade padrão ou geral, aumente ou diminua o tamanho em incrementos de 10 GB.
  - Para armazenamento nos níveis de serviço crítico premium ou empresarial, aumente ou diminua o tamanho em incrementos de 250 GB.
- No nível de serviço para fins `tempdb` gerais, utiliza um SSD anexado, e este custo de armazenamento está incluído no preço vCore.
- No nível de serviço crítico de negócios, `tempdb` partilha o SSD anexado com os ficheiros MDF e LDF, e o `tempdb` custo de armazenamento está incluído no preço vCore.
- O tamanho de armazenamento de uma 222 Instance gerida deve ser especificado em múltiplos de 32 GB.


> [!IMPORTANT]
> É cobrado o armazenamento total atribuído aos ficheiros MDF e LDF.

Para monitorizar o tamanho total atual dos seus ficheiros MDF e LDF, utilize [sp_spaceused](/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Para monitorizar o tamanho atual dos ficheiros MDF e LDF individuais, utilize [sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Em algumas circunstâncias, pode precisar de encolher uma base de dados para recuperar o espaço não usused. Para obter mais informações, consulte [Gerir o espaço de ficheiros na Base de Dados Azure SQL](file-space-manage.md).

## <a name="backups-and-storage"></a>Backups e armazenamento

O armazenamento para cópias de segurança de bases de dados é atribuído para suportar as capacidades de restauro pontual (PITR) e [de retenção a longo prazo (LTR)](long-term-retention-overview.md) da Base de Dados SQL e da SQL Managed Instance. Este armazenamento é atribuído separadamente para cada base de dados e faturado como dois encargos por base de dados separados.

- **PITR** : As cópias de segurança individuais da base de dados são copiadas para [o armazenamento geo-redundante de acesso à leitura (RA-GRS)](../../storage/common/geo-redundant-design.md) automaticamente. O tamanho do armazenamento aumenta dinamicamente à medida que novas cópias de segurança são criadas. O armazenamento é utilizado por cópias de segurança completas semanais, cópias de segurança diferenciais diárias e cópias de segurança de registo de transações, que são copiadas a cada 5 minutos. O consumo de armazenamento depende da taxa de alteração da base de dados e do período de retenção para cópias de segurança. Pode configurar um período de retenção separado para cada base de dados entre 7 e 35 dias. Um valor mínimo de armazenamento igual a 100 por cento (1x) do tamanho da base de dados é fornecido sem custos adicionais. Para a maioria das bases de dados, este valor é suficiente para armazenar 7 dias de backups.
- **LTR** : Você também tem a opção de configurar a retenção a longo prazo de backups completos por até 10 anos (esta funcionalidade está em [pré-visualização pública limitada para SQL Managed Instance](long-term-retention-overview.md#sql-managed-instance-support). Se configurar uma política LTR, estas cópias de segurança são armazenadas automaticamente no armazenamento RA-GRS, mas pode controlar a frequência com que as cópias de segurança são copiadas. Para satisfazer diferentes requisitos de conformidade, pode selecionar diferentes períodos de retenção para backups semanais, mensais e/ou anualmente. A configuração que escolher determina a quantidade de armazenamento que será utilizado para cópias de segurança LTR. Para estimar o custo do armazenamento LTR, pode utilizar a calculadora de preços LTR. Para obter mais informações, consulte [a base de dados SQL a longo prazo.](long-term-retention-overview.md)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os tamanhos específicos de computação e armazenamento disponíveis nos níveis gerais de serviço e de serviço crítico de negócios, consulte: 

- [vCore limites de recursos para Azure SQL Database](resource-limits-vcore-single-databases.md).
- [vCore limites de recursos para bases de dados agrizadas na Base de Dados Azure SQL](resource-limits-vcore-elastic-pools.md).
- [vCore-based limites de recursos para Azure SQL Managed Instance](../managed-instance/resource-limits.md).