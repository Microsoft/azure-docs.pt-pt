---
title: Camada de serviço de uso geral-banco de dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre a camada de uso geral do banco de dados SQL do Azure
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
ms.openlocfilehash: 2573adcb199ab32467493729842b6c47e3add64b
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515310"
---
# <a name="general-purpose-service-tier---azure-sql-database"></a>Camada de serviço de uso geral-banco de dados SQL do Azure

> [!NOTE]
> A camada de serviço de uso geral no modelo de compra baseado em vCore é chamada de camada de serviço Standard no modelo de compra baseado em DTU. Para obter uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte [recursos e modelos de compra do banco de dados SQL do Azure](sql-database-purchase-models.md).

O banco de dados SQL do Azure baseia-se na arquitetura do mecanismo de banco de dados SQL Server adaptada para o ambiente de nuvem a fim de garantir 99,99% de disponibilidade mesmo nos casos de falhas de infraestrutura. Há três camadas de serviço que são usadas no banco de dados SQL do Azure, cada uma com modelos de arquitetura diferentes. Essas camadas de serviço são:

- Utilização geral
- Crítico para a empresa
- Hiperescala

O modelo de arquitetura da camada de serviço de uso geral baseia-se em uma separação de computação e armazenamento. Esse modelo de arquitetura depende da alta disponibilidade e da confiabilidade do armazenamento de BLOBs do Azure que Replica de forma transparente os arquivos de banco de dados e garante que não haja perda de dado se ocorrer uma falha de infraestrutura subjacente.

A figura a seguir mostra quatro nós no modelo de arquitetura padrão com a computação separada e as camadas de armazenamento.

![Separação de computação e armazenamento](media/sql-database-managed-instance/general-purpose-service-tier.png)

No modelo de arquitetura da camada de serviço de uso geral, há duas camadas:

- Uma camada de computação sem estado que está `sqlserver.exe` executando o processo e contém somente dados transitórios e em cache (por exemplo – cache de planos, pool de buffers, pool de repositório de coluna). Esse nó de SQL Server sem estado é operado pelo Service Fabric do Azure que inicializa o processo, controla a integridade do nó e executa o failover para outro local, se necessário.
- Uma camada de dados com monitoração de estado com arquivos (. MDF/. ldf) armazenados no armazenamento de BLOBs do Azure. O armazenamento de BLOBs do Azure garante que não haverá nenhuma perda de dados de nenhum registro que seja colocado em qualquer arquivo de banco de dado. O armazenamento do Azure tem redundância/disponibilidade de dados interna que garante que todos os registros no arquivo de log ou na página no arquivo de dados serão preservados mesmo se SQL Server processo falhar.

Sempre que o mecanismo de banco de dados ou o sistema operacional for atualizado, alguma parte da infraestrutura subjacente falhará ou, se algum problema crítico for detectado no processo de SQL Server, o Azure Service Fabric moverá o processo de SQL Server sem estado para outro nó de computação sem estado. Há um conjunto de nós sobressalentes que está aguardando para executar o novo serviço de computação se ocorrer um failover do nó primário a fim de minimizar o tempo de failover. Os dados na camada de armazenamento do Azure não são afetados e os arquivos de dados/log são anexados ao processo de SQL Server inicializado recentemente. Esse processo garante a disponibilidade de 99,99%, mas pode haver algum impacto no desempenho na carga de trabalho pesada em execução devido ao tempo de transição e ao fato de que o novo nó de SQL Server começa com o cache frio.

## <a name="when-to-choose-this-service-tier"></a>Quando escolher esta camada de serviço

Uso Geral camada de serviço é uma camada de serviço padrão no banco de dados SQL do Azure que foi projetada para a maioria das cargas de trabalho genéricas. Se você precisar de um mecanismo de banco de dados totalmente gerenciado com SLA de 99,99% com latência de armazenamento entre 5 e 10 MS que correspondam ao IaaS do SQL do Azure na maioria dos casos, Uso Geral camada é a opção para você.

## <a name="next-steps"></a>Passos Seguintes

- Encontre características de recursos (número de núcleos, e/s, memória) da camada Uso Geral/Standard em [instância gerenciada](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), banco de dados individual no modelo de [VCORE](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-for-provisioned-compute) ou [modelo de DTU](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)ou pool elástico em modelo de [VCORE](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes) e [modelo de DTU](sql-database-dtu-resource-limits-elastic-pools.md#standard-elastic-pool-limits).
- Saiba mais sobre as camadas de [comercialmente crítico](sql-database-service-tier-business-critical.md) e hiperescala. [](sql-database-service-tier-hyperscale.md)
- Saiba mais sobre [Service Fabric](../service-fabric/service-fabric-overview.md).
- Para obter mais opções de alta disponibilidade e recuperação de desastres, consulte continuidade de [negócios](sql-database-business-continuity.md).
