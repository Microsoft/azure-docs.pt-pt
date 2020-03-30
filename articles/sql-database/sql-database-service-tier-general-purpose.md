---
title: Nível de serviço de uso geral
description: Conheça o nível de propósito geral da Base de Dados Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/07/2019
ms.openlocfilehash: 7c57755ae63f8af5a2a4faa4764bc6a9597e8c2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255889"
---
# <a name="general-purpose-service-tier---azure-sql-database"></a>Nível de serviço para fins gerais - Base de Dados Azure SQL

> [!NOTE]
> O nível de serviço de uso geral no modelo de compra baseado em vCore é chamado de nível de serviço padrão no modelo de compra baseado em DTU. Para uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte os modelos e recursos de compra da Base de [Dados Azure SQL.](sql-database-purchase-models.md)

A Base de Dados Azure SQL baseia-se na arquitetura do motor de base de dados SQL Server adaptada para o ambiente em nuvem de forma a garantir uma disponibilidade de 99,99%, mesmo em casos de falhas de infraestrutura. Existem três níveis de serviço que são usados na Base de Dados Azure SQL, cada um com diferentes modelos arquitetônicos. Estes níveis de serviço são:

- Fins gerais
- Crítico de negócios
- Hyperscale

O modelo arquitetónico para o nível de serviço de uso geral baseia-se numa separação de cálculo e armazenamento. Este modelo arquitetónico baseia-se na elevada disponibilidade e fiabilidade do armazenamento da Blob Azure que replica transparentemente ficheiros de bases de dados e garante nenhuma perda de dados se ocorrer uma falha de infraestrutura subjacente.

A figura que se segue mostra quatro nós no modelo arquitetónico padrão com a computação separada e camadas de armazenamento.

![Separação de cálculo e armazenamento](media/sql-database-managed-instance/general-purpose-service-tier.png)

No modelo arquitetónico para o nível de serviço de uso geral, existem duas camadas:

- Uma camada de cálculo apátrida que está a executar o processo e contém apenas `sqlservr.exe` dados transitórios e cached (por exemplo – cache de plano, piscina tampão, piscina de colunas). Este nó de Servidor SQL apátrida é operado pela Azure Service Fabric que inicia o processo, controla a saúde do nó e executa falhas noutro local, se necessário.
- Uma camada de dados audaz com ficheiros de base de dados (.mdf/.ldf) que são armazenados no armazenamento da Blob Azure. O armazenamento da Azure Blob garante que não haverá perda de dados de qualquer registo que seja colocado em qualquer ficheiro de base de dados. O Azure Storage tem disponibilidade/redundância de dados incorporados que garante que todos os registos no ficheiro de registo ou página no ficheiro de dados serão preservados mesmo que o processo do SQL Server se desfaça.

Sempre que o motor ou sistema operativo de base de dados for atualizado, uma parte da infraestrutura subjacente falha, ou se algum problema crítico for detetado no processo Do Servidor SQL, o Tecido de Serviço Azure moverá o processo apátrida do Servidor SQL para outro nó de cálculo apátrida. Há um conjunto de nóteos sobressalentes que está à espera de executar um novo serviço de computação se uma falha do nó primário acontecer de forma a minimizar o tempo de falha. Os dados na camada de armazenamento do Azure não são afetados e os ficheiros de dados/registos estão ligados ao processo do Servidor SQL recém-inicializado. Este processo garante uma disponibilidade de 99,99%, mas pode ter alguns impactos de desempenho na carga de trabalho pesada que está a decorrer devido ao tempo de transição e o facto de o novo nó SQL Server começar com cache fria.

## <a name="when-to-choose-this-service-tier"></a>Quando escolher este nível de serviço

O nível de serviço General Purpose é um nível de serviço predefinido na Base de Dados Azure SQL que foi concebido para a maioria das cargas de trabalho genéricas. Se precisar de um motor de base de dados totalmente gerido com 99,99% SLA com latência de armazenamento entre 5 e 10 ms que corresponda ao Azure SQL IaaS na maioria dos casos, o nível De propósito Geral é a opção para si.

## <a name="next-steps"></a>Passos seguintes

- Encontre características de recursos (número de núcleos, IO, memória) do modelo General Purpose/Standard em [Instância Gerida,](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)Base de dados única no [modelo vCore](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) ou [modelo DTU,](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)ou piscina elástica no [modelo vCore](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose---provisioned-compute---gen4) e [modelo DTU](sql-database-dtu-resource-limits-elastic-pools.md#standard-elastic-pool-limits).
- Conheça os níveis [Business Critical](sql-database-service-tier-business-critical.md) e [Hyperscale.](sql-database-service-tier-hyperscale.md)
- Saiba mais sobre [o Tecido de Serviço.](../service-fabric/service-fabric-overview.md)
- Para mais opções para alta disponibilidade e recuperação de desastres, consulte A [Continuidade do Negócio](sql-database-business-continuity.md).
