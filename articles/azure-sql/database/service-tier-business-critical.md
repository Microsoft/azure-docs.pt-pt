---
title: Nível de serviço critical de negócios
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Conheça o nível de serviço crítico de negócios para Azure SQL Database e Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 830ecc44d0def13e51cb06704bef429bb8860cd6
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92780228"
---
# <a name="business-critical-tier---azure-sql-database-and-azure-sql-managed-instance"></a>Business Critical tier - Azure SQL Database e Azure SQL Managed Instance 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> Business Critical tier chama-se Premium no modelo de compra DTU. Para comparar o modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte modelos e recursos de [compra da Base de Dados Azure SQL](purchasing-models.md).

A Azure SQL Database e Azure SQL Managed Instance são ambos baseados na arquitetura do motor de base de dados sql server que é ajustado para o ambiente em nuvem de forma a garantir 99,99% de disponibilidade mesmo em casos de falhas na infraestrutura. Existem três modelos arquitetónicos que são usados:
- Finalidade Geral/Padrão 
- Negócio Crítico/Premium
- Hyperscale

O modelo de nível de serviço Premium/Business Critical baseia-se num conjunto de processos de motor de base de dados. Este modelo arquitetónico baseia-se no facto de que existe sempre um quórum de nós de motor de base de dados disponíveis e tem um impacto de desempenho mínimo na sua carga de trabalho mesmo durante as atividades de manutenção. O nível de serviço de hiperescala está atualmente disponível apenas para Azure SQL Database (não SQL Managed Instance), e é um nível de desempenho de armazenamento e cálculo altamente escalável que aproveita a arquitetura Azure para escalar os recursos de armazenamento e cálculo para uma base de dados em Azure SQL Database substancialmente além dos limites disponíveis para os níveis de serviço Geral e Business Critical.

Azure atualiza e corrige sistema operativo subjacente, controladores e motor de base de dados SQL Server de forma transparente com o tempo mínimo de insuperação para os utilizadores finais. 

A disponibilidade premium é ativada nos níveis de serviço Premium e Business Critical e é projetada para cargas de trabalho intensivas que não podem tolerar qualquer impacto de desempenho devido às operações de manutenção em curso.

O cálculo e o armazenamento estão integrados no nó único no modelo premium. A elevada disponibilidade neste modelo arquitetónico é conseguida através da replicação do processo de motor de base de dados sql server e do armazenamento (SSD ligado localmente) para um cluster de quatro nós, utilizando tecnologia semelhante aos [grupos de disponibilidade](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)SQL Server Always On.

![Aglomerado de nosdes de motor de base de dados](./media/service-tier-business-critical/business-critical-service-tier.png)

Tanto o processo do motor de base de dados SQL Server como os ficheiros subjacentes .mdf/.ldf são colocados no mesmo nó com armazenamento SSD ligado localmente, proporcionando baixa latência à sua carga de trabalho. A alta disponibilidade é implementada utilizando tecnologia semelhante ao SQL Server [Always On availability groups](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Cada base de dados é um conjunto de nós de base de dados com uma base de dados primária acessível para cargas de trabalho dos clientes, e três processos secundários contendo cópias de dados. O nó primário empurra constantemente as alterações nos nós secundários de modo a garantir que os dados estão disponíveis em réplicas secundárias se o nó primário falhar por qualquer motivo. O failover é manuseado pelo motor de base de dados sql Server – uma réplica secundária torna-se o nó primário e é criada uma nova réplica secundária para garantir que existem nós suficientes no cluster. A carga de trabalho é redirecionada automaticamente para o novo nó primário.

Além disso, o cluster Business Critical tem uma capacidade incorporada de [Escala de Leitura](read-scale-out.md) que fornece um nó de leitura incorporado de custo gratuito que pode ser usado para executar consultas apenas de leitura (por exemplo, relatórios) que não devem afetar o desempenho da sua carga de trabalho primária.

## <a name="when-to-choose-this-service-tier"></a>Quando escolher este nível de serviço

O nível de serviço Critical do Negócios é projetado para aplicações que requerem respostas de baixa latência a partir do armazenamento SSD subjacente (1-2 ms em média), recuperação rápida se a infraestrutura subjacente falhar, ou precisar de desal carga relatórios, análises e consultas apenas de leitura para a réplica secundária legível gratuitamente da base de dados primária.

As principais razões pelas quais deve escolher o nível de serviço Business Critical em vez do nível de Finalidade Geral são:
-   **Baixos requisitos de latência de I/O** – cargas de trabalho que necessitam de uma resposta rápida da camada de armazenamento (1-2 milissegundos em média) devem utilizar o nível Business Critical. 
-   **Comunicação frequente entre aplicação e base de dados.** As aplicações que não podem alavancar o caching da camada de aplicação ou [solicitar o loteamento](../performance-improve-use-batching.md) e precisam enviar muitas consultas SQL que devem ser processadas rapidamente são bons candidatos para o nível Business Critical.
-   **Um grande número de atualizações** – inserir, atualizar e eliminar operações modificam as páginas de dados na memória (página suja) que devem ser guardadas em ficheiros de dados com `CHECKPOINT` operação. A potencial falha do processo do motor da base de dados ou uma falha na base de dados com um grande número de páginas sujas pode aumentar o tempo de recuperação no nível de Finalidade Geral. Use o nível Business Critical se tiver uma carga de trabalho que cause muitas alterações na memória. 
-   **Transações de longa duração que modificam dados.** As transações abertas por um período mais longo impedem a truncação de ficheiros de registo, o que pode aumentar o tamanho do registo e o número de ficheiros de [registo virtual (VLF)](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch). Um elevado número de VLFs pode abrandar a recuperação da base de dados após o failover.
-   **Carga de trabalho com reporte e consultas analíticas** que podem ser redirecionadas para a réplica de leitura secundária gratuita.
- **Maior resiliência e recuperação mais rápida das falhas.** Em caso de falha do sistema, a base de dados sobre a instância primária será desativada e uma das réplicas secundárias será imediatamente nova base de dados primária de leitura que está pronta para processar consultas. O motor de base de dados não precisa de analisar e refazer transações a partir do ficheiro de registo e carregar todos os dados no tampão de memória.
- **Proteção avançada de corrupção de dados.** Business Critical tier alavanca réplicas de base de dados nos bastidores para fins de continuidade do negócio, e assim o serviço também aproveita a reparação automática de páginas, que é a mesma tecnologia usada para [grupos de espelhamento e disponibilidade](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring)da base de dados SQL Server. No caso de uma réplica não conseguir ler uma página devido a um problema de integridade de dados, uma nova cópia da página será recuperada de outra réplica, substituindo a página ilegível sem perda de dados ou tempo de inatividade do cliente. Esta funcionalidade é aplicável no nível de Finalidade Geral se a base de dados tiver réplica geoconduser.
- **Maior disponibilidade** - O nível De Negócios Crítico na configuração Multi-AZ garante disponibilidade de 99,995%, contra 99,99% do nível de Finalidade Geral.
- **Rápida geo-recuperação** - O nível de operação Critical configurado com geo-replicação tem um objetivo garantido de ponto de recuperação (RPO) de 5 seg e objetivo de tempo de recuperação (RTO) de 30 seg para 100% das horas implantadas.

## <a name="next-steps"></a>Passos seguintes

- Encontrar características de recursos (número de núcleos, I/O, memória) do nível Business Critical em [SQL Managed Instance](../managed-instance/resource-limits.md#service-tier-characteristics), Base de dados única no [modelo vCore](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) ou [modelo DTU,](resource-limits-dtu-single-databases.md#premium-service-tier)ou piscina elástica no [modelo vCore](resource-limits-vcore-elastic-pools.md#business-critical---provisioned-compute---gen4) e [modelo DTU](resource-limits-dtu-elastic-pools.md#premium-elastic-pool-limits).
- Saiba mais sobre os níveis [de Finalidade Geral](service-tier-general-purpose.md) e [Hiperescala.](service-tier-hyperscale.md)
- Saiba mais sobre [o Tecido de Serviço.](../../service-fabric/service-fabric-overview.md)
- Para mais opções de elevada disponibilidade e recuperação de desastres, consulte [Continuidade de Negócios.](business-continuity-high-availability-disaster-recover-hadr-overview.md)