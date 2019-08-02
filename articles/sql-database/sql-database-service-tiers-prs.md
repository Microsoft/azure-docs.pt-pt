---
title: Aposentadoria da camada de serviço do Azure Banco de Dados SQL Premium RS | Microsoft Docs
description: A camada de serviço Premium RS está sendo desativada e o suporte para ela está terminando-consulte Opções de migração.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/07/2019
ms.openlocfilehash: d2637618ea9e2a0a0d0369eddce01fae073be221
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566630"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>A camada de serviço do Azure Banco de Dados SQL Premium RS (versão prévia) está sendo desativada-opções para migração

Em fevereiro de 2018, a Microsoft anunciou que a camada de serviço Premium RS no banco de dados SQL do Azure não seria liberada para disponibilidade geral e não teria mais suporte após 31 de janeiro de 2019. Esse prazo final de suporte foi estendido para 30 de junho de 2019. Este artigo explica as opções para migrar do Premium RS camada de serviço para outra camada de serviço. Após 30 de junho de 2019, a Microsoft migrará automaticamente seus bancos de dados Premium RS para uma camada de serviço geralmente disponível que atenda melhor aos requisitos de desempenho de seu banco de dados Premium RS.

A seguir estão os destinos de migração e as opções de preço que podem ser adequadas para clientes Premium RS:

- camadas de serviço vCore

  As camadas de serviço **uso geral** e **comercialmente crítico** no [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md). Essas duas camadas de serviço estão em disponibilidade geral. O modelo de compra baseado em vCore também oferece a camada de serviço de hiperescala que se adapta sob demanda às necessidades da carga de trabalho com o dimensionamento automático de até 100 TB por banco de dados. A camada de serviço de hiperescala fornece desempenho de e/s comparável à camada de serviço Premium no [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) a um preço mais próximo da camada de serviço Premium RS.
- Preços de Desenvolvimento/Teste

  Os [preços de desenvolvimento/teste](https://azure.microsoft.com/pricing/dev-test/) fornecem economias de até 55% versus taxas incluídas na licença com sua assinatura do Visual Studio.
- Preço de capacidade reservada e Benefício Híbrido do Azure

  Os [preços de capacidade reservada e benefício híbrido do Azure](https://azure.microsoft.com/pricing/details/sql-database/) fornecem economias de até 80% versus taxas de licença incluída. Para obter mais informações sobre essas opções, consulte [benefício híbrido do Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) e [capacidade reservada do banco de dados SQL do Azure](sql-database-reserved-capacity.md).

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>Aja agora para migrar seus bancos de dados Premium RS para camadas de serviço do banco de dados SQL alternativas

Examine as diretrizes neste artigo junto com nossos preços e documentação para determinar os destinos de migração corretos para suas cargas de trabalho de Premium RS.

## <a name="migrate-compute-intensive-workloads-and-save"></a>Migre cargas de trabalho de computação intensiva e economize

Para suas cargas de trabalho de Premium RS de computação intensiva, recomendamos migrar para nossa camada de serviço de Uso Geral com base em vCore disponível e economizar mais versus taxas de licença incluída usando o Benefício Híbrido do Azure para SQL Server e ofertas de capacidade reservada. Se você preferir permanecer em uma opção de compra baseada em DTU, poderá migrar seus bancos de dados de Premium RS de computação intensiva para uma camada de serviço Standard e ainda salvar versus o preço de disponibilidade geral Premium RS (se tiver tido a disponibilidade geral).

> [!WARNING]
> Migrar suas cargas de trabalho de Premium RS para camadas de serviço Premium baseadas em DTU pode aumentar os custos mensais em comparação com os preços atuais de Premium RS. É recomendável considerar as camadas de hiperescala ou Comercialmente Crítico com Benefício Híbrido do Azure e os preços de capacidade reservada para manter custos semelhantes ou menores do que Premium RS.

### <a name="premium-rs-databases"></a>Bancos de dados Premium RS

|**Se você estiver atualmente em...**|**Migrar para base de vCore comparável...**|**Migrar para base de DTU comparável...**|
|---|---|---|
|Premium RS 1|VCore Uso Geral 1 (Gen4)|Standard 3|
|Premium RS 2|Uso Geral 2 vCores (Gen4)|Padrão 4|
|Premium RS 4|Uso Geral 4 vCores (Gen4)|Standard 6|
|Premium RS 6|Uso Geral 6 vCores (Gen4)|Standard 7|

### <a name="premium-rs-pools"></a>Pools de Premium RS

|**Se você estiver atualmente em...**|**Migrar para base de vCore comparável...**|**Migrar para base de DTU comparável...**|
|---|---|---|
|Premium RS o pool de DTU 125|VCore Uso Geral 1 (Gen4)|Pool Standard 100 eDTUs|
|Premium RS o pool de DTU 250|Uso Geral 2 vCores (Gen4)|Pool Standard 250 eDTUs|
|Premium RS o pool de DTU 500|Uso Geral 4 vCores (Gen4)|Pool Standard 500 eDTUs|
|Premium RS o pool de DTU 1000|Uso Geral 8 vCores (Gen4)|Pool Standard 1000 eDTUs|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>Otimize a economia e o desempenho para suas cargas de trabalho com uso intensivo de e/s

É recomendável migrar seus bancos de dados individuais com uso intensivo de e/s para nossa camada de hiperescala baseada em vCore, atualmente em versão prévia, e seus pools de armazenamento com uso intensivo de e/s para nossa camada de Comercialmente Crítico disponível, para a combinação ideal de desempenho e custo.  As opções baseadas em vCore a seguir manterão ou melhorarão o desempenho atual e poderão economizar dinheiro quando combinado com o Benefício Híbrido do Azure e os preços de capacidade reservada.

|**Se você estiver atualmente em...**|**Migrar para base de vCore comparável...**|**Migrar para base de DTU comparável...**|
|---|---|---|
|Premium RS 1| Gen4 (hiperescala 1 vCore) ou Comercialmente Crítico 1 vCore (Gen4)|Premium 1|
|Premium RS 2| VCores de hiperescala 2 (Gen4) ou Comercialmente Crítico 2 vCores (Gen4|Premium 2|
|Premium RS 4| Hiperescala 4 vCores (Gen4) ou Comercialmente Crítico 4 vCores (Gen4)|Premium 4
|Premium RS 6| VCores de hiperescala 6 (Gen4) ou Comercialmente Crítico 6 vCores (Gen4)|Premium 6|

|**Se você estiver atualmente em...**|**Migrar para base de vCore comparável...**|**Migrar para base de DTU comparável...**|
|---|---|---|
|Premium RS o pool de DTU 125|Comercialmente Crítico 2 vCores (Gen4)|Pool Premium 125 eDTUs|
|Premium RS o pool de DTU 250|Comercialmente Crítico 2 vCores (Gen4)|Pool Premium 250 eDTUs|
|Premium RS o pool de DTU 500|Comercialmente Crítico 4 vCores (Gen4)|Pool Premium 500 eDTUs|
|Premium RS o pool de DTU 1000|Comercialmente Crítico 8 vCores (Gen4)|Pool Premium 1000 eDTUs|

## <a name="take-advantage-of-our-new-offers"></a>Aproveite nossas novas ofertas

Nossas camadas de serviço no modelo de compra baseado em vCore são elegíveis para ofertas especiais que podem poupar a você até 80% versus preços incluídos na licença. Use suas licenças do SQL Server Standard ou Enterprise Edition com o Software Assurance ativo para economizar até 55% versus preços incluídos na licença com o [benefício híbrido do Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Você pode combinar o benefício híbrido com o preço da [capacidade reservada do banco de dados SQL do Azure](sql-database-reserved-capacity.md) e economizar até 80% quando você confirmar o início de um período de um ou três anos.  Ative os dois benefícios hoje mesmo a partir de portal do Azure.

Se você tiver dúvidas ou preocupações com relação a essa alteração ou se precisar de assistência de migração, entre em contato com a [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>Migração de uma camada de serviço Premium RS para uma camada de serviço no modelo de DTU ou vCore

### <a name="migration-of-a-database"></a>Migração de um banco de dados

A migração de um banco de dados de uma camada de serviço Premium RS para uma camada de serviço no modelo de DTU ou vCore é semelhante à atualização ou ao downgrade entre as camadas de serviço na camada de serviço Premium RS.

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>Usando cópia de banco de dados para converter um banco de dados Premium RS em um banco de dados baseado em DTU ou vCore

Você pode copiar qualquer banco de dados com um Premium RS tamanho de computação para um banco de dados com um tamanho de computação baseado em DTU ou vCore sem restrições ou sequenciamento especial, contanto que o tamanho de computação de destino dê suporte ao tamanho máximo do banco de dados de origem. A cópia de banco de dados cria um instantâneo de data a partir da hora de início da operação de cópia e não executa a sincronização de dados entre a origem e o destino.

## <a name="next-steps"></a>Passos Seguintes

- Para obter detalhes sobre tamanhos de computação específicos e opções de tamanho de armazenamento disponíveis para um banco de dados individual, consulte [limites de recursos baseados em vCore do banco de dados SQL para bancos únicos](sql-database-vcore-resource-limits-single-databases.md)
- Para obter detalhes sobre tamanhos de computação específicos e opções de tamanho de armazenamento disponíveis para pools elásticos, consulte [limites de recursos baseados em vCore do banco de dados SQL para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md).
