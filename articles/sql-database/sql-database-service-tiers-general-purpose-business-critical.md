---
title: SQL Database do Azure - para fins gerais e crítico para a empresa | Documentos da Microsoft
description: O artigo aborda o fins gerais e a camada de serviço crítico de negócio no modelo de compra de vCore.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan, moslake
manager: craigg
ms.date: 02/23/2019
ms.openlocfilehash: db91e67254e9927caba68a84b38a1ee94da8d3ab
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750128"
---
# <a name="azure-sql-database-service-tiers"></a>Escalões de serviço de base de dados SQL do Azure

Base de dados SQL do Azure baseia-se na arquitetura de motor de base de dados do SQL Server que é ajustada para o ambiente de cloud para garantir a disponibilidade de 99,99% mesmo em caso de falhas de infraestrutura. Existem três modelos de arquiteturais que são utilizados na base de dados do Azure SQL:

- [Fins gerais](sql-database-service-tier-general-purpose.md) concebido para a maioria das cargas de trabalho genéricas.
- [Crítico para a empresa](sql-database-service-tier-business-critical.md) concebido para cargas de trabalho de baixa latência com uma das réplicas legível.
- [Hiperescala](sql-database-service-tier-hyperscale.md) concebido para bases de dados muito grandes (até 100 TB) com várias réplicas legíveis.

Este artigo aborda considerações de armazenamento e cópia de segurança para os escalões de serviço para fins gerais e crítico para a empresa no modelo de compra baseado em vCore.

> [!NOTE]
> Para obter detalhes sobre a camada de serviços de Hiperescala no modelo de compra baseado em vCore, consulte [camada de serviços de Hiperescala](sql-database-service-tier-hyperscale.md). Para obter uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte [compra de modelos e recursos do Azure SQL Database](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Armazenamento de dados e de registo

Considere o seguinte:

- O armazenamento atribuído é utilizado por ficheiros de dados (. MDF) e ficheiros de registo de ficheiros (LDF).
- Cada base de dados de computação tamanho suporta um tamanho máximo da base de dados, com o tamanho máximo predefinido de 32 GB.
- Quando configura o tamanho de base de dados necessária (tamanho do MDF), 30% de armazenamento adicional é adicionado automaticamente para suportar LDF
- Tamanho de armazenamento na instância gerida tem de ser especificado em múltiplos de 32 GB.
- Pode escolher qualquer tamanho de base de dados entre 10 GB e o máximo suportado
  - Para o armazenamento nos escalões de serviço standard ou para fins gerais, aumentar ou diminuir o tamanho em incrementos de 10 GB
  - Para armazenamento no premium ou crítico para a empresa escalões de serviço, aumentar ou diminuir o tamanho em incrementos de 250 GB
- No escalão de serviço para fins gerais, `tempdb` utiliza SSD anexado e este custo de armazenamento está incluído no preço vCore.
- No escalão de serviço crítico para a empresa, `tempdb` partilhas o SSD anexado com os arquivos MDF e LDF e o custo de armazenamento de tempDB está incluído no preço vCore.

> [!IMPORTANT]
> É cobrado o armazenamento total alocado para MDF e LDF.

Para monitorizar o tamanho total atual do MDF e LDF, utilize [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Para monitorizar o tamanho atual dos arquivos MDF e LDF individuais, utilize [database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ter reduzir uma base de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerir o espaço de ficheiro na base de dados do Azure SQL](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Cópias de segurança e armazenamento

Armazenamento para cópias de segurança da base de dados é alocado para suportar o ponto no tempo de restauro (PITR) e [longo período de retenção (LTR)](sql-database-long-term-retention.md) capacidades de base de dados SQL. Este armazenamento é alocado em separado para cada base de dados e faturado como duas Cobranças separado por base de dados.

- **PITR**: Cópias de segurança da base de dados individuais são copiadas para [armazenamento RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md) automaticamente. O tamanho de armazenamento aumenta dinamicamente à medida que novas cópias de segurança são criadas.  O armazenamento é utilizado por cópias de segurança completas semanais, cópias de segurança diferenciais diárias e cópias de segurança de registo de transações copiadas a cada 5 minutos. O consumo de armazenamento depende da taxa de alteração da base de dados e o período de retenção. Pode configurar um período de retenção separado para cada base de dados entre 7 e 35 dias. Uma quantidade mínima de armazenamento igual a 1 x do tamanho dos dados é fornecida sem custos adicionais. A maioria das bases de dados, este é o valor é o suficiente para armazenar a 7 dias de cópias de segurança.
- **LTR**: Base de dados SQL oferece a opção de configurar a retenção de longa duração das cópias de segurança completas para até 10 anos. Se a política LTR estiver ativada, as cópias de segurança theses são armazenadas no armazenamento RA-GRS automaticamente, mas pode controlar a frequência com que as cópias de segurança são copiadas. Para cumprir o requisito de conformidade diferentes, pode selecionar os períodos de retenção diferentes para cópias de segurança semanais, mensais e/ou anuais. Esta configuração irá definir a quantidade de armazenamento será utilizada para as cópias de segurança LTR. Pode utilizar a Calculadora de preços de LTR para estimar os custos de armazenamento de LTR. Para obter mais informações, veja [Retenção de longa duração](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Passos Seguintes

- Para obter detalhes sobre específicas de computação tamanhos e opções de tamanho de armazenamento disponíveis para a base de dados individual nos escalões fins gerais e de negócios críticos de serviço, consulte [limites de recursos baseados em vCore de base de dados SQL para bases de dados individuais](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)
- Para obter detalhes sobre específicas de computação tamanhos e opções de tamanho de armazenamento disponíveis para conjuntos elásticos nas camadas de fins gerais e de negócios críticos de serviço, consulte [limites de recursos baseados em vcore da base de dados SQL para conjuntos elásticos](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
