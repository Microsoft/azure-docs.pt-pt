---
title: SQL Database do Azure - para fins gerais e crítico para a empresa | Documentos da Microsoft
description: O artigo aborda os fins gerais e as camadas de serviços críticos de negócios no modelo de compra baseado em vCore.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
manager: craigg
ms.date: 02/23/2019
ms.openlocfilehash: e5426bb7c8eba9d58dbf0472360c6ce0b19c9bc4
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431336"
---
# <a name="azure-sql-database-service-tiers"></a>Escalões de serviço de base de dados SQL do Azure

Base de dados SQL do Azure baseia-se na arquitetura de motor de base de dados do SQL Server que é ajustada para o ambiente de cloud garantir a disponibilidade de 99,99 por cento, mesmo se ocorrer uma falha de infraestrutura. Três escalões de serviço são usados na SQL Database do Azure, cada um com um modelo de arquitetura diferente. Estes escalões de serviço são:

- [Fins gerais](sql-database-service-tier-general-purpose.md), que foi concebido para cargas de trabalho mais genéricas.
- [Crítico para a empresa](sql-database-service-tier-business-critical.md), que foi concebido para cargas de trabalho de baixa latência com uma das réplicas legível.
- [Hiperescala](sql-database-service-tier-hyperscale.md), que foi concebido para bases de dados muito grandes (até 100 TB) com várias réplicas legíveis.

Este artigo aborda considerações de armazenamento e cópia de segurança para os fins gerais e as camadas de serviços críticos de negócios no modelo de compra baseado em vCore.

> [!NOTE]
> Para obter informações sobre a camada de serviços de hiperescala no modelo de compra baseado em vCore, consulte [camada de serviços de hiperescala](sql-database-service-tier-hyperscale.md). Para obter uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte [compra de modelos e recursos do Azure SQL Database](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Armazenamento de dados e de registo

Os seguintes fatores afetam a quantidade de armazenamento utilizada para os ficheiros de dados e de registo:

- O armazenamento atribuído é utilizado por ficheiros de dados (. MDF) e ficheiros de registo (LDF).
- Cada base de dados de computação tamanho suporta um tamanho máximo da base de dados, com o tamanho máximo predefinido de 32 GB.
- Quando configura o tamanho de base de dados necessária (o tamanho do ficheiro MDF), o armazenamento adicional de 30 por cento mais é adicionado automaticamente para suportar arquivos LDF.
- O tamanho de armazenamento para uma instância gerida da base de dados SQL tem de ser especificado em múltiplos de 32 GB.
- Pode selecionar qualquer tamanho de base de dados entre 10 GB e o máximo suportado.
  - Para o armazenamento nos escalões de serviço standard ou para fins gerais, aumentar ou diminuir o tamanho em incrementos de 10 GB.
  - Para armazenamento no premium ou camadas de serviços críticos de negócios, aumentar ou diminuir o tamanho em incrementos de 250 GB.
- No escalão de serviço para fins gerais, `tempdb` utiliza SSD anexado e este custo de armazenamento está incluído no preço vCore.
- Na camada de serviço críticos de negócio, `tempdb` compartilha o SSD anexado com os arquivos MDF e LDF e o `tempdb` custo de armazenamento está incluído no preço vCore.

> [!IMPORTANT]
> É cobrado o armazenamento total alocado para arquivos MDF e LDF.

Para monitorizar o tamanho total atual de seus arquivos MDF e LDF, utilize [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Para monitorizar o tamanho atual dos arquivos MDF e LDF individuais, utilize [database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Em algumas circunstâncias, poderá ter reduzir uma base de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerir o espaço de ficheiro na base de dados do Azure SQL](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Cópias de segurança e armazenamento

Armazenamento para cópias de segurança da base de dados é alocado para suportar o restauro de ponto no tempo (PITR) e [retenção a longo prazo (LTR)](sql-database-long-term-retention.md) capacidades de base de dados SQL. Este armazenamento é alocado em separado para cada base de dados e faturado como duas Cobranças separado por base de dados.

- **PITR**: Cópias de segurança da base de dados individuais são copiadas para [armazenamento do acesso de leitura georredundante (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md) automaticamente. O tamanho de armazenamento aumenta dinamicamente à medida que novas cópias de segurança são criadas. O armazenamento é utilizado por cópias de segurança completas semanais, cópias de segurança diferenciais diárias e backups de log de transação, o que são copiados a cada 5 minutos. O consumo de armazenamento depende da taxa de alteração da base de dados e o período de retenção para cópias de segurança. Pode configurar um período de retenção separado para cada base de dados entre 7 e 35 dias. Uma quantidade mínima de armazenamento igual a 100 por cento (1 x), do tamanho da base de dados é fornecida sem custos adicionais. A maioria das bases de dados, este é o valor é o suficiente para armazenar a 7 dias de cópias de segurança.
- **LTR**: Base de dados SQL oferece a opção de configuração de retenção de longa duração das cópias de segurança completas para até 10 anos. Se configurar uma política LTR, estas cópias de segurança são armazenadas no armazenamento RA-GRS, automaticamente, mas pode controlar a frequência com que as cópias de segurança são copiadas. Para atender aos requisitos de conformidade diferentes, pode selecionar os períodos de retenção diferentes para cópias de segurança semanais, mensais, e/ou anuais. A configuração que escolher determina a quantidade de armazenamento será utilizado para cópias de segurança LTR. Para estimar os custos de armazenamento LTR, pode utilizar a Calculadora de preços de LTR. Para obter mais informações, consulte [retenção de longa duração de base de dados SQL](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Passos Seguintes

- Para obter detalhes sobre as específicas de computação disponíveis para uma base de dados individual nos escalões de serviço críticos de negócios e fins gerais de tamanhos de armazenamento e tamanhos, veja [limites de recursos baseados em vCore de base de dados SQL para bases de dados individuais](sql-database-vcore-resource-limits-single-databases.md).
- Para obter detalhes sobre as específicas de computação disponíveis para conjuntos elásticos de fins gerais e camadas de serviços críticos de negócios de tamanhos de armazenamento e tamanhos, veja [limites de recursos baseados em vcore da base de dados SQL para conjuntos elásticos](sql-database-vcore-resource-limits-elastic-pools.md).
