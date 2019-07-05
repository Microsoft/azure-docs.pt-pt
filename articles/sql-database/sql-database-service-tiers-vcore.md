---
title: Serviço de base de dados SQL do Azure - vCore | Documentos da Microsoft
description: O modelo de compra baseado em vCore permite-lhe dimensionar recursos de computação e armazenamento, combine o desempenho no local e otimizar o preço de forma independente.
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
ms.date: 06/26/2019
ms.openlocfilehash: e9d1ce3bcd3bf958be0a7837e8416300af03f5a2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449737"
---
# <a name="choose-among-the-vcore-service-tiers-and-migrate-from-the-dtu-service-tiers"></a>Escolha de entre os escalões de serviço de vCore e migrar dos escalões de serviço DTU

O núcleo virtual (vCore)-modelo de compra com base permite-lhe dimensionar recursos de computação e armazenamento, combine o desempenho no local e otimizar o preço de forma independente. Ele também permite que escolha a geração de hardware:

- **Gen4**: Até 24 CPUs lógicas com base no Intel E5-2673 v3 (Haswell) de processadores de 2,4 GHz, vCore = 1 PP (núcleos físicos), 7 GB por núcleo, anexado SSD
- **Gen5**: Até 80 CPUs lógicas com base na Intel E5-2673 v4 (Broadwell) 2.3 GHz processadores, vCore = 1 LP (hyper-thread), 5.1 GB por núcleo, o rápido eNVM SSD

Hardware de Gen4 oferece substancialmente mais memória por vCore. No entanto, o Gen5 hardware permite-lhe aumentar verticalmente os recursos de computação muito superiores.

> [!IMPORTANT]
> Novas Gen4 bases de dados já não são suportadas na região AustraliaEast.
> [!NOTE]
> Para obter informações sobre os escalões de serviço baseado em DTU, consulte [escalões para o modelo de compra baseado em DTU de serviço](sql-database-service-tiers-dtu.md). Para obter informações sobre as diferenças entre os escalões de serviço para o baseado em DTU e os modelos de compras baseado em vCore, consulte [modelos de compra do Azure SQL Database](sql-database-purchase-models.md).

## <a name="service-tier-characteristics"></a>Características de camada de serviço

O modelo de compra baseado em vCore oferece três escalões de serviço: para fins gerais, de hiperescala e crítico para a empresa. Estes escalões de serviço são diferenciados por uma variedade de tamanhos de computação, designs de elevada disponibilidade, métodos de isolamento de falhas, tipos e tamanhos de armazenamento e os intervalos de e/s.

Tem de configurar separadamente o período de retenção e armazenamento necessário para cópias de segurança. Para definir o período de retenção de cópia de segurança, abra o portal do Azure, vá para o servidor (não a base de dados) e, em seguida, aceda a **gerir cópias de segurança** > **configurar política**  >   **Ponto no tempo restaurar Configuration** > **7-35 dias**.

A tabela seguinte explica as diferenças entre as três camadas:

||**Fins gerais**|**Crítico para a empresa**|**Hiperescala**|
|---|---|---|---|
|Melhor para|A maioria das cargas de trabalho da empresa. Ofertas de orçamento e orientados a, equilibrada e dimensionáveis de computação e opções de armazenamento.|Aplicativos de negócios com requisitos de e/s elevados. Oferece maior resiliência a falhas, com várias réplicas isoladas.|A maioria das cargas de trabalho empresariais com requisitos de escala de leitura e de armazenamento altamente escalável.|
|Computação|**Aprovisionado computação**:<br/>Gen4: 1 a 24 vCores<br/>Gen5: 2 a 80 vCores<br/>**Computação sem servidor**:<br/>Gen5: 0,5 - 4 vCores|**Aprovisionado computação**:<br/>Gen4: 1 a 24 vCores<br/>Gen5: 2 a 80 vCores|**Aprovisionado computação**:<br/>Gen4: 1 a 24 vCores<br/>Gen5: 2 a 80 vCores|
|Memória|**Aprovisionado computação**:<br/>Gen4: 7 GB por vCore<br/>Gen5: 5.1 GB por vCore<br/>**Computação sem servidor**:<br/>Gen5: 3 GB por vCore|**Aprovisionado computação**:<br/>Gen4: 7 GB por vCore<br/>Gen5: 5.1 GB por vCore |**Aprovisionado computação**:<br/>Gen4: 7 GB por vCore<br/>Gen5: 5.1 GB por vCore|
|Armazenamento|Utiliza o armazenamento remoto.<br/>**Base de dados aprovisionada computação**:<br/>5 GB – 4 TB<br/>**Computação sem servidor da base de dados individual**:<br/>5 GB – 1 TB<br/>**Instância gerida**: 32 GB - 8 TB |Utiliza o armazenamento SSD local.<br/>**Base de dados aprovisionada computação**:<br/>5 GB – 4 TB<br/>**Instância gerida**:<br/>32 GB - 4 TB |Flexível o aumento automático do armazenamento, conforme necessário. Suporta até 100 TB de armazenamento. Utiliza o armazenamento SSD local para cache de conjunto de memória intermédia local e o armazenamento de dados local. Utiliza o armazenamento remoto do Azure como arquivo de dados de longo prazo final. |
|Débito de e/s (aproximado)|**Base de dados individual**: 500 IOPS por vCore com IOPS máximos de 7000.<br/>**Instância gerida**: Depende [tamanho do ficheiro](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes).|5000 IOPS por núcleo com 200 000 IOPS máximos|Hiperescala é uma arquitetura de várias camadas com colocação em cache em vários níveis. IOPs eficaz irão depender da carga de trabalho.|
|Disponibilidade|1 réplica, sem réplicas de escala de leitura|3 réplicas, 1 [réplica de uma escala de leitura](sql-database-read-scale-out.md),<br/>com redundância de zona elevada disponibilidade (HA)|1 réplica de leitura / escrita, além de 0 a 4 [réplicas de escala de leitura](sql-database-read-scale-out.md)|
|Cópias de segurança|[Armazenamento georredundante com acesso de leitura (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dias (7 dias por predefinição)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dias (7 dias por predefinição)|Backups baseados em instantâneo no armazenamento remoto do Azure. Restauros utilizam estes instantâneos para a recuperação rápida. As cópias de segurança são instantâneas e não afetam o desempenho de e/s de computação. Restauros são rápidos e não são uma operação de tamanho de dados (e tirar minutos em vez de horas ou dias).|
|Dentro da memória|Não suportado|Suportadas|Não suportado|
|||

> [!NOTE]
> Pode obter uma base de dados SQL do Azure gratuita na camada de serviço básico em conjunto com uma conta gratuita do Azure. Para obter mais informações, consulte [criar uma base de dados gerida na cloud com a sua conta gratuita do Azure](https://azure.microsoft.com/free/services/sql-database/).

- Para obter mais informações sobre limites de recursos de vCore, consulte [limites de recursos de vCore na base de dados individual](sql-database-vcore-resource-limits-single-databases.md) e [vCore limites de recursos numa instância gerida](sql-database-managed-instance.md#vcore-based-purchasing-model).
- Para obter mais informações sobre os escalões de críticos de serviço de negócios e fins gerais, veja [escalões de serviço de fins gerais e crítico para a empresa](sql-database-service-tiers-general-purpose-business-critical.md).
- Para obter mais informações sobre a camada de serviços de hiperescala no modelo de compra baseado em vCore, consulte [camada de serviços de Hiperescala](sql-database-service-tier-hyperscale.md).  

## <a name="azure-hybrid-benefit"></a>Benefício Híbrido do Azure

Na camada de computação aprovisionada, de modelo de compra baseado em vCore, podem trocar suas licenças existentes para as tarifas com desconto na base de dados SQL utilizando [benefício híbrido do SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Este benefício do Azure permite-lhe a economizar até 30 por cento na base de dados do Azure SQL, com as suas licenças do SQL Server no local com Software Assurance.

![Os preços](./media/sql-database-service-tiers/pricing.png)

Com o Azure Hybrid Benefit, pode optar por pagar apenas pela infraestrutura do Azure subjacente ao utilizar a sua licença existente do SQL Server para o motor de base de dados SQL em si (preços de computação Base) ou pode pagar para a infraestrutura subjacente e o SQL Server licença (licença incluída preços).

Pode escolher ou alterar o seu modelo de licenciamento com o portal do Azure ou ao utilizar uma das seguintes APIs:

- Para definir ou atualizar o tipo de licença com o PowerShell:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- Para definir ou atualizar o tipo de licença com a CLI do Azure:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Para definir ou atualizar o tipo de licença com a API REST:

  - [Bases de dados - criar ou atualizar](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Bases de dados - atualização](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Instâncias - geridas criar ou atualizar](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Instâncias geridas - atualização](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrar do modelo baseado em DTU para o modelo baseado em vCore

### <a name="migrate-a-database"></a>Migrar uma base de dados

Migrar uma base de dados do modelo de compra baseado em DTU para o modelo de compra baseado em vCore é semelhante a atualizar ou fazer downgrade entre os escalões de serviço standard e premium no modelo de compra baseado em DTU.

### <a name="migrate-databases-with-geo-replication-links"></a>Migrar bases de dados com ligações de georreplicação

Migrando do modelo baseado em DTU para o modelo de compra baseado em vCore é semelhante a atualizar ou fazer downgrade as relações de georreplicação entre bases de dados nos escalões de serviço standard e premium. Durante a migração, não tem de parar a replicação geográfica, mas tem de seguir estas regras de sequenciamento:

- Ao atualizar, tem de atualizar a base de dados secundária primeiro e, em seguida, atualizar o principal.
- Ao fazer downgrade, inverter a ordem: tem de mudar para a versão da base de dados principal primeiro e, em seguida, mudar o secundário.

Quando estiver a utilizar a georreplicação entre dois conjuntos elásticos, recomendamos que designar um conjunto como principal e o outro como secundário. Nesse caso, quando estiver a migrar os conjuntos elásticos deve utilizar a mesma diretriz de sequenciamento. No entanto, se tiver conjuntos elásticos que contêm bases de dados primárias e secundárias, tratar o conjunto com a utilização mais alta como principal e siga as regras de sequenciamento em conformidade.  

A tabela seguinte fornece orientações para cenários de migração específica:

|Escalão de serviço atual|Escalão de serviço de destino|Tipo de migração|Ações do usuário|
|---|---|---|---|
|Standard|Fins gerais|Lateral|Pode migrar em qualquer ordem, mas certifique-se de dimensionamento apropriado vCore *|
|Premium|Crítico para a empresa|Lateral|Pode migrar em qualquer ordem, mas certifique-se de dimensionamento apropriado vCore *|
|Standard|Crítico para a empresa|Atualizar|Deve migrar primeiro secundário|
|Crítico para a empresa|Standard|Mudar para uma versão anterior|Deve migrar primeiro primário|
|Premium|Fins gerais|Mudar para uma versão anterior|Deve migrar primeiro primário|
|Fins gerais|Premium|Atualizar|Deve migrar primeiro secundário|
|Crítico para a empresa|Fins gerais|Mudar para uma versão anterior|Deve migrar primeiro primário|
|Fins gerais|Crítico para a empresa|Atualizar|Deve migrar primeiro secundário|
||||

\* Cada 100 DTUs no escalão standard requerem, pelo menos, 1 vCore e cada 125 DTUs no escalão premium requerem, pelo menos, 1 vCore.

### <a name="migrate-failover-groups"></a>Migrar grupos de ativação pós-falha

Migração de grupos de ativação pós-falha com várias bases de dados requer individual migração de bases de dados primárias e secundárias. Durante esse processo, as mesmas considerações e regras de sequenciamento aplicam-se. Depois das bases de dados são convertidos para o modelo de compra baseado em vCore, o grupo de ativação pós-falha permanecerão em vigor com as mesmas definições de política.

### <a name="create-a-geo-replication-secondary-database"></a>Criar uma base de dados secundária de georreplicação

Pode criar uma georreplicação secundária da base de dados (geo-secundária) apenas com a utilização da mesma camada de serviço que tenha utilizado para a base de dados primária. Bases de dados com uma taxa elevada de geração de log, recomendamos que crie geo-secundária com o mesmo tamanho de computação como principal.

Se estiver a criar geo-secundária do conjunto elástico para um único banco de dados primário, certifique-se a `maxVCore` definição para o conjunto corresponde ao tamanho de computação da base de dados primário. Se estiver a criar geo-secundária para um site primário em outro conjunto elástico, recomendamos que os agrupamentos têm a mesma `maxVCore` definições.

### <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Utilize a cópia de base de dados para converter um banco de dados baseado em DTU para uma base de dados baseado em vCore

Pode copiar qualquer base de dados com um tamanho de computação baseado em DTU para uma base de dados com um tamanho de computação baseada em vCore sem restrições ou sequenciamento especial, desde que o tamanho de computação de destino suporta o tamanho máximo da base de dados da base de dados de origem. A cópia da base de dados cria um instantâneo dos dados desde a hora de início da operação de cópia e não a sincronizar dados entre a origem e de destino.

## <a name="next-steps"></a>Passos Seguintes

- Para os tamanhos de computação específicos e as opções de tamanho de armazenamento disponíveis para bases de dados individuais, consulte [limites de recursos baseados em vCore de base de dados SQL para bases de dados individuais](sql-database-vcore-resource-limits-single-databases.md).
- Para os tamanhos de computação específicos e as opções de tamanho de armazenamento disponíveis para conjuntos elásticos, veja [limites de recursos baseados em vcore da base de dados SQL para conjuntos elásticos](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
