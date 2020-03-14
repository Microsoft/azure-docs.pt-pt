---
title: Nível de serviço Business Critical
description: Conheça o nível crítico de negócios da Base de Dados Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: fc328c34c1543a75fdc885087d44b28e24c0850a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268681"
---
# <a name="business-critical-tier---azure-sql-database"></a>Nível crítico de negócios - Base de Dados Azure SQL

> [!NOTE]
> O nível Business Critical chama-se Premium no modelo de compra DTU. Para uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte os modelos e recursos de compra da Base de [Dados Azure SQL.](sql-database-purchase-models.md)

A Base de Dados Azure SQL baseia-se na arquitetura do motor de base de dados do Servidor SQL que é ajustada para o ambiente em nuvem de forma a garantir uma disponibilidade de 99,99%, mesmo nos casos de falhas de infraestrutura. Existem três modelos arquitetônicos que são usados na Base de Dados Azure SQL:
- Finalidade Geral/Norma 
- Business Critical/Premium
- Hyperscale

O modelo premium/Business Critical service tier baseia-se num conjunto de processos de motor de base de dados. Este modelo arquitetónico baseia-se no facto de existir sempre um quórum de nódos de motor de base de dados disponíveis e ter um impacto de desempenho mínimo na sua carga de trabalho mesmo durante as atividades de manutenção.

O Azure atualiza e corrige o sistema operativo subjacente, os controladores e o Motor de Base de Dados do Servidor SQL de forma transparente com o tempo mínimo de paragem para os utilizadores finais. 

A disponibilidade premium está ativada nos níveis de serviço Premium e Business Critical da Base de Dados Azure SQL e é projetado para cargas de trabalho intensivas que não podem tolerar qualquer impacto de desempenho devido às operações de manutenção em curso.

No modelo premium, a base de dados Azure SQL integra a computação e o armazenamento no nó único. A elevada disponibilidade neste modelo arquitetónico é conseguida através da replicação de computação (processo do Motor de Base de Dados do Servidor SQL) e armazenamento (SSD ligado localmente) implantado em quatro clusters de nó, utilizando tecnologia semelhante a [Grupos](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)de Disponibilidade do Servidor SQL .

![Aglomerado de nós de motor de base de dados](media/sql-database-managed-instance/business-critical-service-tier.png)

Tanto o processo do motor de base de dados SQL como os ficheiros mdf/ldf subjacentes são colocados no mesmo nó com armazenamento SSD ligado localmente, proporcionando baixa latência à sua carga de trabalho. A alta disponibilidade é implementada utilizando tecnologia semelhante a Grupos de Disponibilidade de Servidor SQL [Always On.](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) Cada base de dados é um conjunto de nós de base de dados com uma base de dados primária acessível para a carga de trabalho do cliente, e três processos secundários contendo cópias de dados. O nó primário pressiona constantemente as alterações aos nódos secundários, a fim de garantir que os dados estão disponíveis em réplicas secundárias se o nó primário se despenhar por qualquer motivo. A failover é manuseada pelo Motor de Base de Dados do Servidor SQL – uma réplica secundária torna-se o nó primário e uma nova réplica secundária é criada para garantir nós suficientes no cluster. A carga de trabalho é automaticamente redirecionada para o novo nó primário.

Além disso, o cluster Business Critical tem uma capacidade incorporada de [Leitura Scale-Out](sql-database-read-scale-out.md) que fornece nó de leitura incorporado gratuito que pode ser usado para executar consultas apenas de leitura (por exemplo relatórios) que não devem afetar o desempenho da sua carga de trabalho primária.

## <a name="when-to-choose-this-service-tier"></a>Quando escolher este nível de serviço?

O nível de serviço Business Critical foi concebido para as aplicações que requerem respostas de baixa latência do armazenamento ssd subjacente (1-2 ms em média), recuperação rápida se a infraestrutura subjacente falhar, ou precisar de relatórios de descarregamento, análise e leitura apenas consultas à réplica secundária legível gratuitamente da base de dados primária.

As principais razões pelas quais deve escolher o nível de serviço Business Critical em vez do nível de Propósito Geral são:
-   Requisitos de latência baixos da IO – a carga de trabalho que necessita da resposta rápida da camada de armazenamento (1-2 milissegundos em média) deve utilizar o nível Business Critical. 
-   Comunicação frequente entre aplicação e base de dados. Aplicação que não pode alavancar o cache da camada de aplicação ou [solicitar lotação](sql-database-use-batching-to-improve-performance.md) e precisa enviar muitas consultas SQL que devem ser processadas rapidamente são bons candidatos para o nível Business Critical.
-   Grande número de atualizações – inserir, atualizar e eliminar operações modificam as páginas de dados na memória (página suja) que devem ser guardadas em ficheiros de dados com `CHECKPOINT` operação. A falha potencial do processo do motor da base de dados ou uma falha na base de dados com um grande número de páginas sujas pode aumentar o tempo de recuperação no nível de Propósito Geral. Utilize o nível Business Critical se tiver uma carga de trabalho que cause muitas alterações na memória. 
-   Transações de longo prazo que modificam dados. As transações que são abertas por mais tempo impedem a truncação de ficheiros de registo que podem aumentar o tamanho do registo e o número de [ficheiros de registo virtual (VLF)](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch). Um elevado número de VLF pode abrandar a recuperação da base de dados após a falha.
-   Carga de trabalho com relatórios e consultas analíticas que podem ser redirecionadas para a réplica secundária de leitura gratuita.
- Maior resiliência e recuperação mais rápida dos fracassos. Em caso de falha do sistema, a base de dados em instância primária será desativada e uma das réplicas secundárias tornar-se-á imediatamente uma nova base de dados primária de leitura que está pronta para processar as consultas. O motor de base de dados não precisa de analisar e refazer transações a partir do ficheiro de registo e carregar todos os dados no tampão de memória.
- Proteção avançada contra a corrupção de dados - O nível de business Critical aproveita as réplicas de bases de dados nos bastidores para fins de continuidade de negócios, e assim o serviço também aproveita a reparação automática de páginas, que é a mesma tecnologia usada para grupos de [espelhos e disponibilidade](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring)de bases de dados do SQL Server. No caso de uma réplica não conseguir ler uma página devido a um problema de integridade de dados, uma nova cópia da página será recuperada de outra réplica, substituindo a página ilegível sem perda de dados ou tempo de inatividade do cliente. Esta funcionalidade é aplicável no nível De Propósito Geral se a base de dados tiver réplica geosecundária.
- Maior disponibilidade - O nível Business Critical na configuração Multi-AZ garante uma disponibilidade de 99,995%, contra 99,99% do nível de Propósito Geral.
- Rápida geo-recuperação - O nível business critical configurado com geo-replicação tem um objetivo garantido ponto de recuperação (RPO) de 5 sec e objetivo de tempo de recuperação (RTO) de 30 seg para 100% das horas implantadas.

## <a name="next-steps"></a>Passos seguintes

- Encontre características de recursos (número de núcleos, IO, memória) do nível Business Critical em [Instância Gerida,](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)Base de dados única no [modelo vCore](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) ou [modelo DTU,](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier)ou piscina elástica no [modelo vCore](sql-database-vcore-resource-limits-elastic-pools.md#business-critical---provisioned-compute---gen4) e [modelo DTU](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits).
- Conheça os níveis [de Propósito Geral](sql-database-service-tier-general-purpose.md) e [Hiperescala.](sql-database-service-tier-hyperscale.md)
- Saiba mais sobre [o Tecido de Serviço.](../service-fabric/service-fabric-overview.md)
- Para mais opções para alta disponibilidade e recuperação de desastres, consulte A [Continuidade do Negócio](sql-database-business-continuity.md).
