---
title: Extinção de camada de serviço de base de dados Premium RS para SQL do Azure | Documentos da Microsoft
description: O escalão de serviço Premium RS está a ser descontinuado e o suporte para ele termina - veja as opções de migração.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 7f184178343f69f522148777752c51afc5c5dcb6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65790414"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>Será descontinuada a camada de serviços do Azure SQL da base de dados Premium RS (pré-visualização) – opções para a migração

Em Fevereiro de 2018, a Microsoft anunciou que a camada de serviços na base de dados do Azure SQL Premium RS não deve ser lançada para disponibilidade geral e já não seria suportada após 31 de Janeiro de 2019. Este final do prazo de suporte foi estendida para 30 de Junho de 2019. Este artigo explica as opções de migração do escalão de serviço Premium RS para outro escalão de serviço. Após 30 de Junho de 2019 Microsoft será migrado automaticamente seus bancos de dados de Premium RS para um escalão de serviço disponível em geral que mais se aproxima dos requisitos de desempenho da base de dados Premium RS.

Seguem-se os destinos de migração e as opções de preços que podem ser adequadas para os clientes de Premium RS:

- escalões de serviço de vCore

  O **fins gerais** e **críticas para a empresa** camadas de serviço a [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md). Estes escalões de duas serviço encontram em disponibilidade geral. O modelo de compra baseado em vCore também oferece a **Hiperescala** escalão de serviço que se adapta a pedido para as necessidades da sua carga de trabalho com o dimensionamento automático até 100 TB por base de dados. A camada de serviços de Hiperescala fornece desempenho de e/s comparável para o escalão de serviço Premium no [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) por um preço mais próximo para o escalão de serviço Premium RS.
- Preços de Desenvolvimento/Teste

  [Preços de Dev/teste](https://azure.microsoft.com/pricing/dev-test/) proporciona poupanças até 55% em comparação com as taxas de licença incluída com a sua subscrição do Visual Studio.
- Benefício híbrido do Azure e a capacidade de reserva de preços

  [Benefício híbrido do Azure e a capacidade de reserva de preços](https://azure.microsoft.com/pricing/details/sql-database/) fornecem poupanças até 80% em comparação com as taxas de licença incluída. Para obter mais informações sobre estas opções, consulte [benefício híbrido do SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) e [capacidade de reserva de base de dados do Azure SQL](sql-database-reserved-capacity.md).

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>Agir agora para migrar as bases de dados de Premium RS para escalões de serviço de base de dados SQL alternativos

Reveja as orientações neste artigo, juntamente com os nossos preços e a documentação para determinar o destination(s) certo migração das cargas de trabalho Premium RS.

## <a name="migrate-compute-intensive-workloads-and-save"></a>Migrar cargas de trabalho de computação intensiva e guardar

Das cargas de trabalho intensivas de computação Premium RS, recomendamos que migre para nossa camada de serviços de fins gerais baseado em vCore em disponibilidade geral e economize mais em relação a taxas de licença incluída com o benefício híbrido do Azure para SQL Server e ofertas de capacidade de reserva. Se permaneceria em vez disso, uma opção de compra baseado em DTU, pode migrar as suas bases de dados de Premium RS de computação intensiva para um escalão de serviço Standard e ainda guardar em comparação com os preços de disponibilidade geral de Premium RS (se ele tinha ido em disponibilidade geral).

> [!WARNING]
> Migrar cargas de trabalho Premium RS para escalões de serviço baseado em DTU Premium pode aumentar os custos mensais em comparação com o preço atual do Premium RS. Recomendamos olhar para os escalões de Hiperescala ou críticas para a empresa com o benefício híbrido do Azure e a capacidade de reserva de preços para manter os custos de semelhantes ou menor do que o Premium RS.

### <a name="premium-rs-databases"></a>Bases de dados do Premium RS

|**Se estiver no momento...**|**Migre para o comparável baseado em vCore...**|**Migre para o comparável baseado em DTU...**|
|---|---|---|
|Premium RS 1|VCore de 1 de finalidade geral (Gen4)|Standard 3|
|Premium RS 2|VCores de 2 de finalidade geral (Gen4)|Standard 4|
|Premium RS 4|Geral 4 vCores para fins (Gen4)|Standard 6|
|Premium RS 6|VCores de 6 de finalidade geral (Gen4)|Standard 7|

### <a name="premium-rs-pools"></a>Conjuntos Premium RS

|**Se estiver no momento...**|**Migre para o comparável baseado em vCore...**|**Migre para o comparável baseado em DTU...**|
|---|---|---|
|O Premium RS 125 DTU do conjunto|VCore de 1 de finalidade geral (Gen4)|Conjunto standard de 100 eDTUs|
|250 DTU do conjunto Premium RS|VCores de 2 de finalidade geral (Gen4)|EDTUs do conjunto standard 250|
|500 DTU do conjunto Premium RS|Geral 4 vCores para fins (Gen4)|Conjunto standard 500 eDTUs|
|1000 DTUS do conjunto Premium RS|Geral 8 vCores para fins (Gen4)|Conjunto standard 1000 eDTUs|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>Otimizar economia e o desempenho das cargas de trabalho e/s intensiva

Recomendamos que migre as bases de dados individuais e/s intensiva para o nosso escalão de Hiperescala baseado em vCore, atualmente em pré-visualização e seus conjuntos de base de dados de e/s intensiva para o nosso escalão crítico para a empresa em disponibilidade geral, para a combinação ideal de desempenho e custo.  O seguinte baseado em vCore opções irá manter ou melhorar o desempenho atual e pode poupar dinheiro quando combinado com o benefício híbrido do Azure e reservada a capacidade de preços.

|**Se estiver no momento...**|**Migre para o comparável baseado em vCore...**|**Migre para o comparável baseado em DTU...**|
|---|---|---|
|Premium RS 1| VCore de 1 de Hiperescala (Gen4) ou 1 críticos de negócios vCore (Gen4)|Premium 1|
|Premium RS 2| Hiperescala 2 vCores (Gen4) ou 2 críticos de negócios vCores (Gen4|Premium 2|
|Premium RS 4| 4 de Hiperescala vCores (Gen4) ou vCores de 4 críticos de negócios (Gen4)|Premium 4
|Premium RS 6| 6 de Hiperescala vCores (Gen4) ou vCores de 6 críticos de negócios (Gen4)|Premium 6|

|**Se estiver no momento...**|**Migre para o comparável baseado em vCore...**|**Migre para o comparável baseado em DTU...**|
|---|---|---|
|O Premium RS 125 DTU do conjunto|Comercial crítica 2 vCores (Gen4)|EDTUs de conjunto 125 Premium|
|250 DTU do conjunto Premium RS|Comercial crítica 2 vCores (Gen4)|EDTUs de conjunto 250 Premium|
|500 DTU do conjunto Premium RS|Comercial crítica 4 vCores (Gen4)|Premium conjunto 500 eDTUs|
|1000 DTUS do conjunto Premium RS|Comercial crítica 8 vCores (Gen4)|Premium conjunto 1000 eDTUs|

## <a name="take-advantage-of-our-new-offers"></a>Tirar partido das nossas ofertas de novo

Nossos escalões de serviço no modelo de compra baseado em vCore são elegíveis para ofertas especiais, que podem poupar até 80% em comparação com licença incluída preços. Utilize as suas licenças de edição do SQL Server Standard ou Enterprise com Software Assurance ativo para poupar até 55% em comparação com licença incluída preços com o [benefício híbrido do SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Pode combinar o benefício híbrido com o [capacidade de reserva de base de dados do Azure SQL](sql-database-reserved-capacity.md) preços e poupe até 80% ao comprometer-se à cabeça para um ou três anos termo.  Ative os dois benefícios hoje mesmo no portal do Azure.

Se tiver dúvidas ou preocupações sobre isso, alterar ou se necessitar de assistência de migração, contacte [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>Migração de um escalão de serviço Premium RS para um escalão de serviço na DTU ou o modelo de vCore

### <a name="migration-of-a-database"></a>Migração de uma base de dados

Migrar uma base de dados de um serviço Premium RS da camada de DTU para um escalão de serviço em qualquer um ou o modelo de vCore é semelhante para atualizar ou fazer downgrade entre escalões de serviço da camada de serviço Premium RS.

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>Utilizando a cópia da base de dados para converter um banco de dados de Premium RS para uma base de dados baseado em DTU ou baseado em vCore

Pode copiar qualquer base de dados com um tamanho de computação Premium RS para uma base de dados com um tamanho de computação baseada em vCore ou baseado em DTU sem restrições ou sequenciamento especial, desde que o tamanho de computação de destino suporta o tamanho máximo da base de dados da base de dados de origem. A cópia da base de dados cria um instantâneo dos dados desde a hora de início da operação de cópia e não efetua a sincronização de dados entre a origem e de destino.

## <a name="next-steps"></a>Passos Seguintes

- Para obter detalhes sobre específicas de computação tamanhos e opções de tamanho de armazenamento disponíveis para a base de dados, consulte [limites de recursos baseados em vCore de base de dados SQL para bases de dados individuais](sql-database-vcore-resource-limits-single-databases.md)
- Para obter detalhes sobre específicas de computação tamanhos e opções de tamanho de armazenamento disponíveis para conjuntos elásticos, veja [limites de recursos baseados em vcore da base de dados SQL para conjuntos elásticos](sql-database-vcore-resource-limits-elastic-pools.md).
