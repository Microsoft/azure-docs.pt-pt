---
title: Nível de serviço para fins gerais
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Saiba mais sobre o nível de serviço de Finalidade Geral para Azure SQL Database e Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/07/2019
ms.openlocfilehash: ee218253309995e721c97f4a7f7b4547b32f7c36
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85986646"
---
# <a name="general-purpose-service-tier---azure-sql-database-and-azure-sql-managed-instance"></a>Nível de serviço para fins gerais - Base de Dados Azure SQL e Azure SQL Gestão de Instância
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> O nível de serviço de Finalidade Geral no modelo de compra baseado em vCore é chamado de nível de serviço padrão no modelo de compra baseado em DTU. Para comparar o modelo de compra baseado em vCore com o modelo de compra baseado na DTU, consulte [modelos e recursos de compra.](purchasing-models.md)

A Azure SQL Database e Azure SQL Managed Instance são baseados na arquitetura do motor de base de dados SQL Server adaptado para o ambiente em nuvem de forma a garantir uma disponibilidade de 99,99% mesmo em caso de falhas de infraestrutura. 

Existem dois níveis de serviço utilizados pela Base de Dados Azure SQL e pela SQL Managed Instance: 

- Fins Gerais
- Crítico para a Empresa

A Azure SQL Database também tem um terceiro nível de serviço, que atualmente está indisponível para Azure SQL Managed Instance:

- Hyperscale

O modelo arquitetónico para o nível de serviço de Finalidade Geral baseia-se numa separação entre computação e armazenamento. Este modelo arquitetónico baseia-se na elevada disponibilidade e fiabilidade do armazenamento da Azure Blob que replica de forma transparente ficheiros de base de dados e não garante nenhuma perda de dados se a falha subjacente da infraestrutura acontecer.

A figura a seguir mostra quatro nós no modelo arquitetónico padrão com as camadas de computação e armazenamento separadas.

![Separação do cálculo e do armazenamento](./media/service-tier-general-purpose/general-purpose-service-tier.png)

No modelo arquitetónico para o nível de serviço de Finalidade Geral, existem duas camadas:

- Uma camada de computação apátrida que está a executar o `sqlservr.exe` processo e contém apenas dados transitórios e em cache (por exemplo – cache de plano, piscina tampão, piscina de loja de colunas). Este nó apátrida é operado pela Azure Service Fabric que inicializa o processo, controla a saúde do nó, e executa falhas em outro lugar, se necessário.
- Uma camada de dados imponente com ficheiros de base de dados (.mdf/.ldf) que são armazenados no armazenamento de Azure Blob. O armazenamento Azure Blob garante que não haverá perda de dados de qualquer registo que seja colocado em qualquer ficheiro de base de dados. O Azure Storage tem disponibilidade/redundância de dados incorporada que garante que todos os registos em ficheiros de registo ou página em ficheiro de dados serão preservados mesmo que o processo se despenhe.

Sempre que o motor de base de dados ou o sistema operativo forem atualizados, alguma parte da infraestrutura subjacente falha, ou se algum problema crítico for detetado no processo, o `sqlservr.exe` Azure Service Fabric deslocará o processo apátrida para outro nó de computação apátrida. Há um conjunto de nós sobressalentes que está à espera de executar um novo serviço de computação se um failover do nó primário acontecer de forma a minimizar o tempo de insusição. Os dados na camada de armazenamento Azure não são afetados e os ficheiros de dados/registo são anexados ao processo recém-inicializado. Este processo garante uma disponibilidade de 99,99%, mas pode ter alguns impactos no desempenho em cargas de trabalho pesadas que estão a funcionar devido ao tempo de transição e o facto de o novo nó começar com cache frio.

## <a name="when-to-choose-this-service-tier"></a>Quando escolher este nível de serviço

O nível de serviço de finalidade geral é um nível de serviço predefinido na Base de Dados Azure SQL e na Azure SQL Managed Instance que é projetado para a maioria das cargas de trabalho genéricas. Se necessitar de um motor de base de dados totalmente gerido com 99,99% SLA com latência de armazenamento entre 5 e 10 ms que combine com o SQL Server numa máquina virtual Azure na maioria dos casos, o nível de Finalidade Geral é a opção para si.

## <a name="next-steps"></a>Passos seguintes

- Encontrar características de recurso (número de núcleos, E/S, memória) do nível de finalidade geral/padrão em [SQL Managed Instance](../managed-instance/resource-limits.md#service-tier-characteristics), base de dados única no [modelo vCore](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) ou [modelo DTU,](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes)ou piscina elástica no [modelo vCore](resource-limits-vcore-elastic-pools.md#general-purpose---provisioned-compute---gen4) e [modelo DTU](resource-limits-dtu-elastic-pools.md#standard-elastic-pool-limits).
- Saiba mais sobre os níveis [de Business Critical](service-tier-business-critical.md) e [Hyperscale.](service-tier-hyperscale.md)
- Saiba mais sobre [o Tecido de Serviço.](../../service-fabric/service-fabric-overview.md)
- Para mais opções de elevada disponibilidade e recuperação de desastres, consulte [Continuidade de Negócios.](business-continuity-high-availability-disaster-recover-hadr-overview.md)
