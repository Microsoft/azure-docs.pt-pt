---
title: Serviço de base de dados SQL do Azure - vCore | Documentos da Microsoft
description: O modelo de compra baseado em vCore permite-lhe dimensionar recursos de computação e armazenamento, combine o desempenho no local e otimizar o preço de forma independente.
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
ms.date: 02/07/2019
ms.openlocfilehash: a14912c48f27c4f4d0e54239f8e0523ffa62324f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57888507"
---
# <a name="vcore-service-tiers-azure-hybrid-benefit-and-migration"></a>escalões de serviço de vCore, o benefício híbrido do Azure e migração

O modelo de compra baseado em vCore permite-lhe dimensionar recursos de computação e armazenamento, combine o desempenho no local e otimizar o preço de forma independente. Ele também permite-lhe escolher a geração de hardware:

- Gen4 - até 24 CPUs lógicas com base na Intel E5-2673 v3 (Haswell) 2,4 GHz, vCore = 1 PP (núcleos físicos), 7 GB por núcleo, anexado SSD
- Gen5 - até 80 CPUs lógicas com base na Intel E5-2673 v4 (Broadwell) 2,3 GHz, vCore = 1 LP (hyper-thread), 5.1 GB por núcleo, o rápido eNVM SSD

Hardware de Gen4 oferece substancialmente mais memória por vCore. No entanto, o Gen5 hardware permite-lhe aumentar verticalmente os recursos de computação muito superiores.

> [!NOTE]
> Para obter informações sobre escalões de serviço baseado em DTU, consulte [escalões de serviço baseado em DTU](sql-database-service-tiers-dtu.md). Para obter informações sobre a diferenciar os escalões de serviço baseado em vCore e de escalões de serviço baseado em DTU, consulte [modelos de compra do Azure SQL Database](sql-database-purchase-models.md).

## <a name="service-tier-characteristics"></a>Características de camada de serviço

O modelo de vCore oferece três escalões de serviço para fins gerais, de Hiperescala e críticas para a empresa. Escalões de serviço são diferenciados por uma variedade de tamanhos de computação, design de elevada disponibilidade, isolamento de falhas, tipos e tamanho de armazenamento e o intervalo de e/s. Tem de configurar separadamente o período de retenção e armazenamento necessário para cópias de segurança. No portal do Azure, aceda ao servidor (não a base de dados) > cópias de segurança gerida > configurar a política > ponto no tempo restaurar configuração > 7-35 dias.

A tabela seguinte ajuda-o a compreender as diferenças entre as três camadas:

||**Fins gerais**|**Crítico para a empresa**|**Hiperescala (pré-visualização)**|
|---|---|---|---|
|Melhor para|A maioria das cargas de trabalho da empresa. Ofertas de orçamento orientadas opções de equilibradas e dimensionáveis de computação e armazenamento.|Aplicações empresariais com requisitos elevados de E/S. Oferece maior resiliência a falhas com várias réplicas isoladas.|A maioria das cargas de trabalho empresariais com requisitos de escala de leitura e de armazenamento altamente escalável|
|Computação|Gen4: vCore de 1 a 24<br/>Gen5: vCore de 1 a 80|Gen4: vCore de 1 a 24<br/>Gen5: vCore de 1 a 80|Gen4: vCore de 1 a 24<br/>Gen5: vCore de 1 a 80|
|Memória|Gen4: 7 GB por núcleo<br>Gen5: 5.1 GB por núcleo | Gen4: 7 GB por núcleo<br>Gen5: 5.1 GB por núcleo |Gen4: 7 GB por núcleo<br>Gen5: 5.1 GB por núcleo|
|Armazenamento|Utiliza o armazenamento remoto:<br/>Base de dados: 5 GB – 4 TB<br/>Instância gerida: 32 GB - 8 TB |Utiliza o armazenamento SSD local:<br/>Base de dados: 5 GB – 4 TB<br/>Instância gerida: 32 GB - 4 TB |Flexível, o aumento automático do armazenamento, conforme necessário. Suporta até 100 TB de armazenamento e muito mais. Armazenamento SSD local para a cache de pool de local buffer e o armazenamento de dados local. Armazenamento remoto do Azure como arquivo de dados de longo prazo final. |
|Débito de e/s (aproximado)|Base de dados: 500 IOPS por vCore com IOPS máximos de 7000</br>Instância gerida: Depende [tamanho do ficheiro](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)|5000 IOPS por núcleo com 200 000 IOPS máximos|TBD|
|Disponibilidade|1 réplica, sem uma escala de leitura|3 réplicas, 1 [réplica de uma escala de leitura](sql-database-read-scale-out.md),<br/>HA com redundância de zona|?|
|Cópias de segurança|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dias (7 dias por predefinição)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dias (7 dias por predefinição)|com base em instantâneo de cópia de segurança no armazenamento remoto do Azure e restaurações utilizar estes instantâneos para a recuperação rápida. As cópias de segurança são instantâneas e não afetam o desempenho de e/s de computação. Restauros são muito rápidos e não um tamanho de operação de dados (a demorar minutos em vez de horas ou dias).|
|Dentro da memória|Não suportado|Suportadas|Não suportado|
|||

> [!NOTE]
> Pode obter uma base de dados SQL do Azure gratuita na camada de serviço básico em conjunto com uma conta gratuita do Azure para explorar o Azure. Para obter informações, consulte [criar uma base de dados gerida na cloud com a sua conta gratuita do Azure](https://azure.microsoft.com/free/services/sql-database/).

- Para obter mais informações, consulte [limites de recursos de vCore na base de dados única](sql-database-vcore-resource-limits-single-databases.md) e [limites de recursos de vCore na instância gerida](sql-database-managed-instance.md#vcore-based-purchasing-model).
- Para obter mais informações sobre os escalões de serviço para fins gerais e crítico para a empresa, consulte [escalões de serviço para fins gerais e crítico para a empresa](sql-database-service-tiers-general-purpose-business-critical.md).
- Para obter detalhes sobre a camada de serviços de Hiperescala no modelo de compra baseado em vCore, consulte [camada de serviços de Hiperescala](sql-database-service-tier-hyperscale.md).  

> [!IMPORTANT]
> Se precisar de menos de um vCore de capacidade de computação, utilize o modelo de compra baseado em DTU.

## <a name="azure-hybrid-benefit"></a>Benefício Híbrido do Azure

No modelo de compra baseado em vCore, podem trocar suas licenças existentes para as tarifas com desconto na base de dados SQL com o [benefício híbrido do SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Este benefício do Azure permite-lhe utilizar as suas licenças do SQL Server no local para economizar até 30% na base de dados do Azure SQL com as suas licenças do SQL Server no local com Software Assurance.

![preços](./media/sql-database-service-tiers/pricing.png)

Com o benefício híbrido do Azure, pode optar por pagar apenas a infraestrutura do Azure subjacente, usando sua licença existente do SQL Server para o motor de base de dados SQL em si (**BasePrice**) ou pagar pela infraestrutura subjacente e a licença do SQL Server (**LicenseIncluded**). Pode escolher ou alterar o seu modelo de licenciamento com o portal do Azure ou através de um dos seguintes APIs.

- Para definir ou atualizar o tipo de licença com o PowerShell:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase):
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql)

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

## <a name="migration-from-dtu-model-to-vcore-model"></a>Migração do modelo DTU para o modelo de vCore

### <a name="migration-of-a-database"></a>Migração de uma base de dados

Migrar uma base de dados do modelo de compra baseado em DTU para o modelo de compra baseado em vCore é semelhante a atualizar ou fazer downgrade entre o modelo de compra baseado em DTU de bases de dados de Standard e Premium.

### <a name="migration-of-databases-with-geo-replication-links"></a>Migração de bases de dados com ligações de georreplicação

Migrando do modelo baseado em DTU para o modelo baseado em vCore é semelhante a atualizar ou fazer downgrade as relações de georreplicação entre bases de dados Standard e Premium. Não requer a terminar a georreplicação, mas o usuário deve observar as regras de sequenciamento. Ao atualizar, tem de atualizar a base de dados secundária primeiro e, em seguida, atualizar o principal. Ao fazer downgrade, inverter a ordem: tem de mudar para a versão da base de dados principal primeiro e, em seguida, mudar o secundário.

Ao utilizar a georreplicação entre dois conjuntos elásticos, recomenda-se que designar um conjunto como principal e o outro – como o secundário. Nesse caso, migração de dados elásticas devem utilizar a mesma diretriz.  No entanto, é tecnicamente é possível que um conjunto elástico contém as bases de dados primárias e secundárias. Neste caso migrar corretamente deve tratar o conjunto com a utilização mais alta como "principal" e seguir as regras de sequenciamento em conformidade.  

A tabela seguinte fornece orientações para os cenários de migração específica:

|Escalão de serviço atual|Escalão de serviço de destino|Tipo de migração|Ações de utilizador|
|---|---|---|---|
|Standard|Fins gerais|Lateral|Pode migrar em qualquer ordem, mas certifique-se de um dimensionamento apropriado vCore *|
|Premium|Crítico para a Empresa|Lateral|Pode migrar em qualquer ordem, mas certifique-se de dimensionamento apropriado vCore *|
|Standard|Crítico para a Empresa|Atualizar|Deve migrar primeiro secundário|
|Crítico para a Empresa|Standard|Mudar para uma versão anterior|Deve migrar primeiro primário|
|Premium|Fins gerais|Mudar para uma versão anterior|Deve migrar primeiro primário|
|Fins gerais|Premium|Atualizar|Deve migrar primeiro secundário|
|Crítico para a Empresa|Fins gerais|Mudar para uma versão anterior|Deve migrar primeiro primário|
|Fins gerais|Crítico para a Empresa|Atualizar|Deve migrar primeiro secundário|
||||

\* Cada 100 DTUS no escalão Standard requer, pelo menos, 1 vCore e cada 125 DTU no escalão Premium requer, pelo menos, 1 vCore

### <a name="migration-of-failover-groups"></a>Migração de grupos de ativação pós-falha

Migração de grupos de ativação pós-falha com várias bases de dados requer individual migração de bases de dados primárias e secundárias. Durante esse processo, as mesmas considerações e regras de sequenciamento aplicam-se. Depois das bases de dados são convertidos para o modelo baseado em vCore, o grupo de ativação pós-falha permanecerão em vigor com as mesmas definições de política.

### <a name="creation-of-a-geo-replication-secondary"></a>Criação de uma secundária de georreplicação

Só pode criar usando o mesmo escalão de serviço como principal geo-secundária. Para a base de dados com a taxa de geração de log elevada, recomenda-se que o elemento secundário é criado com o mesmo tamanho de computação como principal. Se estiver a criar geo-secundária do conjunto elástico para um único banco de dados principal, recomenda-se que o conjunto tem o `maxVCore` definição que coincide com o tamanho de computação de base de dados primária. Se estiver a criar geo-secundária do conjunto elástico para um site primário em outro conjunto elástico, recomenda-se que os agrupamentos têm a mesma `maxVCore` definições

### <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Utilizando a cópia da base de dados para converter um banco de dados baseado em DTU para uma base de dados baseado em vCore

Pode copiar qualquer base de dados com um tamanho de computação baseado em DTU para uma base de dados com um tamanho de computação baseada em vCore sem restrições ou sequenciamento especial, desde que o tamanho de computação de destino suporta o tamanho máximo da base de dados da base de dados de origem. A cópia da base de dados cria um instantâneo dos dados desde a hora de início da operação de cópia e não efetua a sincronização de dados entre a origem e de destino.

## <a name="next-steps"></a>Passos Seguintes

- Para obter detalhes sobre específicas de computação tamanhos e opções de tamanho de armazenamento disponíveis para a base de dados, consulte [limites de recursos baseados em vCore de base de dados SQL para bases de dados individuais](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)
- Para obter detalhes sobre específicas de computação tamanhos e opções de tamanho de armazenamento disponíveis para conjuntos elásticos, veja [limites de recursos baseados em vcore da base de dados SQL para conjuntos elásticos](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
