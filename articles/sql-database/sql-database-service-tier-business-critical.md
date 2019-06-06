---
title: Camada de negócio críticos - serviço de base de dados do Azure SQL | Documentos da Microsoft
description: Saiba mais sobre a camada de críticos de negócio da base de dados do Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 90989a9105405f1784b3be9ab59f55cd3433feaf
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479220"
---
# <a name="business-critical-tier---azure-sql-database"></a>Camada de negócio críticos - base de dados do Azure SQL

> [!NOTE]
> Camada de negócio críticos é chamada Premium no modelo de compra DTU. Para obter uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte [compra de modelos e recursos do Azure SQL Database](sql-database-purchase-models.md).

Base de dados SQL do Azure baseia-se na arquitetura de motor de base de dados do SQL Server que é ajustada para o ambiente de cloud para garantir a disponibilidade de 99,99% mesmo em caso de falhas de infraestrutura. Existem três modelos de arquiteturais que são utilizados na base de dados do Azure SQL:
- Geral finalidade/Standard 
- Críticos de negócios/Premium
- Hyperscale

Modelo de camada de serviço crítico para a empresa/Premium baseia-se num cluster de processos de motor de base de dados. Este modelo de arquitetura baseia-se no fato de que existe é sempre um quórum de nós de motor de base de dados disponíveis e não tem impacto de desempenho mínimos na carga de trabalho, mesmo durante as atividades de manutenção.

O Azure é atualizado e patches do sistema operacional subjacente, drivers e o motor de base de dados do SQL Server transparente com o mínimo de tempo de inatividade para os utilizadores finais. 

Disponibilidade de Premium está ativada nos escalões de serviço Premium e crítico para a empresa da base de dados do Azure SQL e foi concebido para cargas de trabalho intensivas que não toleram nenhum impacto no desempenho devido a operações de manutenção contínua.

No modelo de premium, base de dados SQL do Azure integra-se de computação e armazenamento no nó único. Elevada disponibilidade neste modelo de arquitetura é conseguida através da replicação de computação (processo de motor de base de dados do SQL Server) e armazenamento (SSD ligado localmente) implementada num cluster de quatro nós, usando a tecnologia semelhante ao SQL Server [Always On Grupos de disponibilidade](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Cluster de nós de motor de base de dados](media/sql-database-managed-instance/business-critical-service-tier.png)

O SQL da base de processo do motor e subjacentes arquivos mdf/ldf são colocados no mesmo nó com armazenamento SSD localmente ao fornecimento de baixa latência para a sua carga de trabalho. Elevada disponibilidade é implementada utilizando a tecnologia semelhante ao SQL Server [grupos de Disponibilidade AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Cada base de dados é um cluster de nós de base de dados com uma base de dados principal que está acessível para a carga de trabalho do cliente e um três processos secundário que contêm cópias dos dados. O nó principal constantemente envia as alterações para nós secundários para garantir que os dados estão disponíveis em réplicas secundárias, no caso de falha de nó primário por qualquer motivo. Ativação pós-falha é processada pelo motor de base de dados do SQL Server – uma réplica secundária torna-se o nó principal e uma nova réplica secundária é criada para garantir que o suficiente nós do cluster. A carga de trabalho é automaticamente redirecionada para o novo nó primário.

Além disso, o cluster de crítico para a empresa tem incorporado [Escalamento leitura](sql-database-read-scale-out.md) capacidade proporcionada pelo livre de cobrar nó só de leitura incorporada que pode ser utilizada para executar consultas somente leitura (por exemplo, relatórios) que não devem afetar o desempenho da sua carga de trabalho principal.

## <a name="when-to-choose-this-service-tier"></a>Quando escolher este escalão de serviço?

Camada de serviços críticos de negócios foi concebida para as aplicações que requerem baixa latência respostas a partir do armazenamento SSD subjacente (1-2 ms em média), recuperação rápida se falhar a infraestrutura subjacente nem necessidade de off-load relatórios, análises e só de leitura consultas gratuito de réplica secundária legível cobrança de base de dados primária.

## <a name="next-steps"></a>Passos Seguintes

- Encontrar recursos características (número de memória de núcleos, e/s,) da camada de negócios críticos no [instância gerida](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)única base de dados no [modelo de vCore](sql-database-vcore-resource-limits-single-databases.md#business-critical-service-tier-for-provisioned-compute-tier) ou [modelo DTU](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier), ou elástica agrupamento na [modelo de vCore](sql-database-vcore-resource-limits-elastic-pools.md#business-critical-service-tier-storage-sizes-and-compute-sizes) e [modelo DTU](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits).
- Saiba mais sobre [fins gerais](sql-database-service-tier-general-purpose.md) e [Hiperescala](sql-database-service-tier-hyperscale.md) escalões.
- Saiba mais sobre [do Service Fabric](../service-fabric/service-fabric-overview.md).
- Para obter mais opções para alta disponibilidade e recuperação após desastre, veja [continuidade do negócio](sql-database-business-continuity.md).
