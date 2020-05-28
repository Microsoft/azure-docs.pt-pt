---
title: Nível de serviço de uso geral
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Conheça o nível de serviço de uso geral para a Base de Dados Azure SQL e a Instância Gerida Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/07/2019
ms.openlocfilehash: 7fd446e6894c3c88701527bb2d3668a570888a06
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84049912"
---
# <a name="general-purpose-service-tier---azure-sql-database--sql-managed-instance"></a>Nível de serviço para fins gerais - Base de Dados Azure SQL & Instância Gerida SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> O nível de serviço de uso geral no modelo de compra baseado em vCore é chamado de nível de serviço padrão no modelo de compra baseado em DTU. Para uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte [modelos e recursos](purchasing-models.md)de compra.

A Base de Dados Azure SQL e a Azure SQL Managed Instance baseiam-se na arquitetura do motor de base de dados SQL Server adaptada para o ambiente em nuvem de forma a garantir uma disponibilidade de 99,99%, mesmo em casos de falhas de infraestrutura. 

Existem dois níveis de serviço utilizados pela Azure SQL Database e pela SQL Managed Instance: 

- Fins gerais
- Crítico de negócios

A Base de Dados Azure SQL também tem um terceiro nível de serviço, que está atualmente indisponível para a Instância Gerida Azure SQL: 

- Hyperscale

O modelo arquitetónico para o nível de serviço de uso geral baseia-se numa separação de cálculo e armazenamento. Este modelo arquitetónico baseia-se na elevada disponibilidade e fiabilidade do armazenamento da Blob Azure que replica transparentemente ficheiros de bases de dados e garante nenhuma perda de dados se ocorrer uma falha de infraestrutura subjacente.

A figura que se segue mostra quatro nós no modelo arquitetónico padrão com a computação separada e camadas de armazenamento.

![Separação de cálculo e armazenamento](./media/service-tier-general-purpose/general-purpose-service-tier.png)

No modelo arquitetónico para o nível de serviço de uso geral, existem duas camadas:

- Uma camada de cálculo apátrida que está a executar o `sqlservr.exe` processo e contém apenas dados transitórios e cached (por exemplo – cache de plano, piscina tampão, piscina de colunas). Este nó apátrida é operado pela Azure Service Fabric que inicia o processo, controla a saúde do nó e executa falhas noutro local, se necessário.
- Uma camada de dados audaz com ficheiros de base de dados (.mdf/.ldf) que são armazenados no armazenamento da Blob Azure. O armazenamento da Azure Blob garante que não haverá perda de dados de qualquer registo que seja colocado em qualquer ficheiro de base de dados. O Azure Storage tem disponibilidade/redundância de dados incorporados que garante que todos os registos no ficheiro de registo ou página no ficheiro de dados serão preservados mesmo que o processo se despenhe.

Sempre que o motor ou sistema operativo da base de dados for atualizado, uma parte da infraestrutura subjacente falha, ou se algum problema crítico for detetado no processo, o `sqlservr.exe` Azure Service Fabric deslocará o processo apátrida para outro nó de cálculo apátrida. Há um conjunto de nóteos sobressalentes que está à espera de executar um novo serviço de computação se uma falha do nó primário acontecer de forma a minimizar o tempo de falha. Os dados na camada de armazenamento do Azure não são afetados e os ficheiros de dados/registos estão ligados ao processo recém-inicializado. Este processo garante uma disponibilidade de 99,99%, mas pode ter alguns impactos de desempenho em cargas de trabalho pesadas que estão a decorrer devido ao tempo de transição e ao facto de o novo nó começar com cache frio.

## <a name="when-to-choose-this-service-tier"></a>Quando escolher este nível de serviço

O nível de serviço General Purpose é um nível de serviço predefinido na Base de Dados Azure SQL e na Instância Gerida Azure SQL que é projetado para a maioria das cargas de trabalho genéricas. Se precisar de um motor de base de dados totalmente gerido com 99,99% SLA com latência de armazenamento entre 5 e 10 ms que correspondam ao SQL Server num VM Azure na maioria dos casos, o nível De propósito Geral é a opção para si.

## <a name="next-steps"></a>Próximos passos

- Encontre características de recursos (número de núcleos, IO, memória) do modelo Geral Propósito/Padrão em [Instância Gerida SQL,](../managed-instance/resource-limits.md#service-tier-characteristics)base de dados única no [modelo vCore](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) ou [modelo DTU,](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes)ou piscina elástica no [modelo vCore](resource-limits-vcore-elastic-pools.md#general-purpose---provisioned-compute---gen4) e [modelo DTU](resource-limits-dtu-elastic-pools.md#standard-elastic-pool-limits).
- Conheça os níveis [Business Critical](service-tier-business-critical.md) e [Hyperscale.](service-tier-hyperscale.md)
- Saiba mais sobre [o Tecido de Serviço.](../../service-fabric/service-fabric-overview.md)
- Para mais opções para alta disponibilidade e recuperação de desastres, consulte A [Continuidade do Negócio](business-continuity-high-availability-disaster-recover-hadr-overview.md).
