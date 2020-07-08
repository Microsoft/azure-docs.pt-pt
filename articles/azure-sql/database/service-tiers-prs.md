---
title: Reforma do nível de serviço RS premium
description: O nível de serviço Premium RS para a Base de Dados Azure SQL está a ser reformado e o suporte para ele está a terminar - veja as opções de migração.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/07/2019
ms.openlocfilehash: e277c2660107e7bb741157b7d8c573ff69b9186e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84051207"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>O nível de serviço RS premium da base de dados Azure SQL (pré-visualização) está a ser retirado - opções de migração
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Em fevereiro de 2018, a Microsoft anunciou que o nível de serviço Premium RS na Base de Dados Azure SQL não seria lançado para disponibilidade geral e deixaria de ser suportado após 31 de janeiro de 2019. Este prazo de apoio foi prorrogado até 30 de junho de 2019. Este artigo explica as suas opções de migração do nível de serviço Premium RS para outro nível de serviço. Após 30 de junho de 2019, a Microsoft migrará automaticamente as suas bases de dados Premium RS para um nível de serviço geralmente disponível que mais corresponda aos requisitos de desempenho da sua base de dados Premium RS.

Seguem-se os destinos de migração e as opções de preços que podem ser adequados para os clientes Premium RS:

- vCore níveis de serviço

  Os níveis de serviço **De Finalidade Geral** e **Business Critical** no modelo de compra baseado em [vCore](service-tiers-vcore.md). Estes dois níveis de serviço estão em disponibilidade geral. O modelo de compra baseado em vCore também oferece o nível de serviço **Hyperscale** que adapta a pedido às necessidades da sua carga de trabalho com um dimensionamento automático até 100 TB por base de dados. O nível de serviço Hyperscale proporciona um desempenho IO comparável ao nível de serviço Premium no modelo de compra baseado em [DTU a](service-tiers-dtu.md) um preço mais próximo do nível de serviço Premium RS.
- Preços de dev/teste

  [O preço de dev/teste](https://azure.microsoft.com/pricing/dev-test/) proporciona poupanças até 55% contra taxas incluídas na licença com a subscrição do Visual Studio.
- Benefício Híbrido Azure e preços de capacidade reservados

  [O Azure Hybrid Benefit e os preços de capacidade reservados](https://azure.microsoft.com/pricing/details/sql-database/) proporcionam poupanças até 80% contra as taxas incluídas na licença. Para obter mais informações sobre estas opções, consulte [o Benefício Híbrido Azure para o SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) e a Base de [Dados Azure SQL reservada.](reserved-capacity-overview.md)

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>Atue agora para migrar as suas bases de dados Premium RS para níveis alternativos de serviço de base de dados SQL

Reveja as orientações deste artigo juntamente com os nossos preços e documentação para determinar o destino(s) de migração adequado para as suas cargas de trabalho Premium RS.

## <a name="migrate-compute-intensive-workloads-and-save"></a>Migrar cargas de trabalho intensivas em computação e poupar

Para as suas cargas de trabalho Premium RS intensivas em computação, recomendamos migrar para o nosso nível de serviço geralmente disponível baseado em vCore e economizar mais taxas versus licenças incluídas usando o Benefício Híbrido Azure para o SQL Server e ofertas de capacidade reservadas. Se preferir permanecer numa opção de compra baseada em DTU, pode migrar as suas bases de dados Premium RS intensivas em computação para um nível de serviço Standard e ainda economizar face ao preço de disponibilidade geral premium RS (se tivesse entrado em disponibilidade geral).

> [!WARNING]
> Migrar as suas cargas de trabalho Premium RS para os níveis de serviço Premium baseados em DTU pode aumentar os custos mensais em relação aos preços atuais do Premium RS. Recomendamos considerar os níveis de Hiperescala ou Business Critical com Benefício Híbrido Azure e preços de capacidade reservados para manter custos semelhantes ou inferiores aos Premium RS.

### <a name="premium-rs-databases"></a>Bases de dados Premium RS

|**Se está no...**|**Migrar para vCore comparável...**|**Migrar para uma base comparável de DTU...**|
|---|---|---|
|Premium RS 1|Final geral 1 vCore (Gen4)|Padrão 3|
|Premium RS 2|Final geral 2 vCores (Gen4)|Padrão 4|
|Premium RS 4|Final geral 4 vCores (Gen4)|Padrão 6|
|Premium RS 6|Final geral 6 vCores (Gen4)|Padrão 7|

### <a name="premium-rs-pools"></a>Piscinas Premium RS

|**Se está no...**|**Migrar para vCore comparável...**|**Migrar para uma base comparável de DTU...**|
|---|---|---|
|Piscina Premium RS 125 DTU|Final geral 1 vCore (Gen4)|Piscina padrão 100 eDTUs|
|Piscina Premium RS 250 DTU|Final geral 2 vCores (Gen4)|Piscina padrão 250 eDTUs|
|Piscina Premium RS 500 DTU|Final geral 4 vCores (Gen4)|Piscina padrão 500 eDTUs|
|Piscina Premium RS 1000 DTU|Final geral 8 vCores (Gen4)|Piscina padrão 1000 eDTUs|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>Otimize a poupança e o desempenho das suas cargas de trabalho intensivas em IO

Recomendamos migrar as suas bases de dados únicas intensivas em IO para o nosso nível de hiperescala baseado em VCore, atualmente em pré-visualização, e as suas piscinas de base de dados intensivas em IO para o nosso nível geralmente disponível Business Critical, para a combinação ideal de desempenho e custo.  As seguintes opções baseadas em vCore irão manter ou melhorar o seu desempenho atual e poderão economizar dinheiro quando combinado com o Azure Hybrid Benefit e com os preços de capacidade reservados.

|**Se está no...**|**Migrar para vCore comparável...**|**Migrar para uma base comparável de DTU...**|
|---|---|---|
|Premium RS 1| Hiperescala 1 vCore (Gen4) ou Business Critical 1 vCore (Gen4)|Premium 1|
|Premium RS 2| Hiperescala 2 vCores (Gen4) ou Business Critical 2 vCores (Gen4|Premium 2|
|Premium RS 4| Hiperescala 4 vCores (Gen4) ou Business Critical 4 vCores (Gen4)|Premium 4
|Premium RS 6| Hiperescala 6 vCores (Gen4) ou Business Critical 6 vCores (Gen4)|Premium 6|

|**Se está no...**|**Migrar para vCore comparável...**|**Migrar para uma base comparável de DTU...**|
|---|---|---|
|Piscina Premium RS 125 DTU|Business Critical 2 vCores (Gen4)|Piscina premium 125 eDTUs|
|Piscina Premium RS 250 DTU|Business Critical 2 vCores (Gen4)|Piscina premium 250 eDTUs|
|Piscina Premium RS 500 DTU|Business Critical 4 vCores (Gen4)|Piscina premium 500 eDTUs|
|Piscina Premium RS 1000 DTU|Business Critical 8 vCores (Gen4)|Piscina premium 1000 eDTUs|

## <a name="take-advantage-of-our-new-offers"></a>Aproveite as nossas novas ofertas

Os nossos níveis de serviço no modelo de compra baseado em vCore são elegíveis para ofertas especiais que podem economizar até 80% contra preços incluídos na licença. Utilize as licenças SQL Server Standard ou Enterprise edition com garantia de software ativa para economizar até 55% contra preços incluídos na licença com o [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) for SQL Server. Pode combinar o benefício híbrido com o preço de capacidade reservado da [Azure SQL Database](reserved-capacity-overview.md) e economizar até 80% quando se comprometer antecipadamente a um período de um ou três anos.  Ativar ambos os benefícios hoje do portal Azure.

Se tiver alguma dúvida ou preocupação em relação a esta alteração ou se necessitar de assistência migratória, contacte a [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>Migração de um nível de serviço Premium RS para um nível de serviço no DTU ou no modelo vCore

### <a name="migration-of-a-database"></a>Migração de uma base de dados

Migrar uma base de dados de um nível de serviço Premium RS para um nível de serviço no modelo DTU ou vCore é semelhante ao upgrade ou degradação entre os níveis de serviço no nível de serviço Premium RS.

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>Utilizar uma cópia de base de dados para converter uma base de dados Premium RS numa base de dados baseada em DTU ou vCore

Pode copiar qualquer base de dados com um tamanho de cálculo Premium RS para uma base de dados com um tamanho de computação baseado em DTU ou vCore sem restrições ou sequenciação especial, desde que o tamanho do cálculo-alvo suporte o tamanho máximo da base de dados da base de dados de origem. A cópia da base de dados cria uma imagem instantânea dos dados a partir do momento de início da operação de cópia e não realiza a sincronização de dados entre a fonte e o alvo.

## <a name="next-steps"></a>Próximos passos

- Para obter detalhes sobre tamanhos específicos de cálculo e opções de tamanho de armazenamento disponíveis para uma única base [de dados, consulte os limites de recursos baseados em SQL Database vCore para bases de dados individuais](resource-limits-vcore-single-databases.md)
- Para obter detalhes sobre tamanhos específicos de computação e opções de tamanho de armazenamento disponíveis para piscinas elásticas, consulte [os limites de recursos baseados na base de dados SQL vCore para piscinas elásticas.](resource-limits-vcore-elastic-pools.md)
