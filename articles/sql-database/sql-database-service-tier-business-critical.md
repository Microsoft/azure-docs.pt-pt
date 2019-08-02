---
title: Camada de Comercialmente Crítico-serviço de banco de dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre a camada de Comercialmente Crítico do banco de dados SQL do Azure
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
ms.openlocfilehash: 208224e10c3acfb17bc5fd89d2d66152943811dc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566756"
---
# <a name="business-critical-tier---azure-sql-database"></a>Camada de Comercialmente Crítico-banco de dados SQL do Azure

> [!NOTE]
> Comercialmente Crítico camada é chamada Premium no modelo de compra de DTU. Para obter uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte [recursos e modelos de compra do banco de dados SQL do Azure](sql-database-purchase-models.md).

O banco de dados SQL do Azure é baseado em SQL Server Mecanismo de Banco de Dados arquitetura ajustada para o ambiente de nuvem a fim de garantir 99,99% de disponibilidade mesmo nos casos de falhas de infraestrutura. Há três modelos de arquitetura que são usados no banco de dados SQL do Azure:
- Uso Geral/Standard 
- Comercialmente Crítico/Premium
- Hiperescala

O modelo de camada de serviço Premium/Comercialmente Crítico baseia-se em um cluster de processos do mecanismo de banco de dados. Esse modelo de arquitetura depende de um fato de que sempre há um quorum de nós do mecanismo de banco de dados disponíveis e tem impacto mínimo sobre o desempenho na carga de trabalho mesmo durante as atividades de manutenção.

Atualizações e patches do Azure subjacentes ao sistema operacional, aos drivers e ao SQL Server Mecanismo de Banco de Dados de forma transparente com o mínimo de tempo de inatividade para os usuários finais. 

A disponibilidade Premium está habilitada nas camadas de serviço Premium e Comercialmente Crítico do banco de dados SQL do Azure e foi projetada para cargas de trabalho intensivas que não podem tolerar nenhum impacto no desempenho devido às operações de manutenção contínuas.

No modelo Premium, o banco de dados SQL do Azure integra a computação e o armazenamento em um único nó. A alta disponibilidade nesse modelo de arquitetura é obtida pela replicação de computação (SQL Server Mecanismo de Banco de Dados processo) e armazenamento (SSD anexado localmente) implantado no cluster de quatro nós, usando tecnologia semelhante a SQL Server [Always on grupos de disponibilidade ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Cluster de nós do mecanismo de banco de dados](media/sql-database-managed-instance/business-critical-service-tier.png)

O processo do mecanismo do banco de dados SQL e os arquivos MDF/ldf subjacentes são colocados no mesmo nó com o armazenamento SSD anexado localmente, fornecendo baixa latência à carga de trabalho. A alta disponibilidade é implementada usando uma tecnologia semelhante à SQL Server [Always on grupos de disponibilidade](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Cada banco de dados é um cluster de nós de banco de dados com um banco de dados primário que é acessível para carga de trabalho do cliente e um dos três processos secundários que contêm cópias de data. O nó primário envia constantemente as alterações para nós secundários para garantir que os dados estejam disponíveis em réplicas secundárias se o nó primário falhar por qualquer motivo. O failover é manipulado pelo SQL Server Mecanismo de Banco de Dados – uma réplica secundária torna-se o nó primário e uma nova réplica secundária é criada para garantir que nós sejam suficientes no cluster. A carga de trabalho é redirecionada automaticamente para o novo nó primário.

Além disso, Comercialmente Crítico cluster tem funcionalidade interna de [expansão de leitura](sql-database-read-scale-out.md) que fornece um nó somente leitura interno de custo livre que pode ser usado para executar consultas somente leitura (por exemplo, relatórios) que não devem afetar o desempenho do seu primário pico.

## <a name="when-to-choose-this-service-tier"></a>Quando escolher esta camada de serviço?

Comercialmente Crítico camada de serviço foi projetada para os aplicativos que exigem respostas de baixa latência do armazenamento SSD subjacente (1-2 ms em média), recuperação rápida se a infraestrutura subjacente falhar ou se for necessário descarregar relatórios, análises e somente leitura consultas à réplica secundária legível livre do banco de dados primário.

## <a name="next-steps"></a>Passos Seguintes

- Encontre características de recursos (número de núcleos, e/s, memória) da camada de Comercialmente Crítico em [instância gerenciada](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), banco de dados individual no modelo de [VCORE](sql-database-vcore-resource-limits-single-databases.md#business-critical-service-tier-for-provisioned-compute-tier) ou [modelo de DTU](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier)ou pool elástico no modelo de [VCORE](sql-database-vcore-resource-limits-elastic-pools.md#business-critical-service-tier-storage-sizes-and-compute-sizes) e [modelo de DTU](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits).
- Saiba mais sobre as camadas de [uso geral](sql-database-service-tier-general-purpose.md) e hiperescala. [](sql-database-service-tier-hyperscale.md)
- Saiba mais sobre [Service Fabric](../service-fabric/service-fabric-overview.md).
- Para obter mais opções de alta disponibilidade e recuperação de desastres, consulte continuidade de [negócios](sql-database-business-continuity.md).
