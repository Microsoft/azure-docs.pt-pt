---
title: Camada de serviços de fins gerais - base de dados SQL do Azure | Documentos da Microsoft
description: Saiba mais sobre o escalão de fins gerais da base de dados do Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop-msft
ms.reviewer: sstein
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: dc379f1ee67174cd806840e4244054701d18f0d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709167"
---
# <a name="general-purpose-service-tier---azure-sql-database"></a>Camada de serviços de fins gerais - base de dados do Azure SQL

> [!NOTE]
> A camada de serviços de fins gerais no modelo de compra baseado em vCore denomina-se a camada de serviço standard no modelo de compra baseado em DTU. Para obter uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte [compra de modelos e recursos do Azure SQL Database](sql-database-purchase-models.md).

Base de dados SQL do Azure baseia-se na arquitetura de motor de base de dados de SQL Server adaptada para o ambiente de cloud para garantir a disponibilidade de 99,99% mesmo em caso de falhas de infraestrutura. Existem três escalões de serviço que são utilizados no Azure SQL Database, cada uma com diferentes modelos de arquiteturais. Estes escalões de serviço são:

- Fins gerais
- Crítico para a empresa
- Hyperscale

O modelo de arquitetura para a camada de serviços de fins gerais baseia-se uma separação de computação e armazenamento. Este modelo de arquitetura baseia-se de elevada disponibilidade e fiabilidade de armazenamento de Blobs do Azure que replica os ficheiros de base de dados e garante sem perda de dados se subjacente a falha de infraestrutura de forma transparente acontece.

A figura seguinte mostra os quatro nós no modelo de arquitetura de padrão com as camadas de armazenamento e computação separados.

![Separação de computação e armazenamento](media/sql-database-managed-instance/general-purpose-service-tier.png)

No modelo de arquitetura para a camada de serviços de fins gerais, existem duas camadas:

- Uma camada de computação sem monitoração de estado que está a executar o `sqlserver.exe` processar e contém apenas transitórios e em cache os dados (por exemplo – cache de planos, conjunto de memória intermédia, agrupamento de armazenamento de coluna). Este nó do SQL Server sem monitoração de estado é operada por recursos de infraestrutura do serviço do Azure que inicializa o processo, controla o estado de funcionamento do nó e efetua a ativação pós-falha para outro lugar, se necessário.
- Uma camada de dados com monitoração de estado com ficheiros de base de dados (.mdf/.ldf) que estão armazenados no armazenamento de Blobs do Azure. Armazenamento de Blobs do Azure garante que haverá sem perda de dados de qualquer registo que é colocado em qualquer arquivo de banco de dados. O armazenamento do Azure tem disponibilidade/redundância de dados incorporada que garante que todos os registos no ficheiro de registo ou a página no arquivo de dados será preservada mesmo no caso de falha de processo do SQL Server.

Sempre que a atualização do motor de base de dados ou o sistema operativo, alguma parte da infraestrutura subjacente falha ou se detetar algum problema crítico no processo do SQL Server, o Azure Service Fabric moverá o processo do SQL Server sem monitoração de estado para outro nó de computação sem monitoração de estado. Existe um conjunto de nós de reserva que está à espera para executar o novo serviço de computação se acontecer de uma ativação pós-falha de nó primário para minimizar o tempo de ativação pós-falha. Dados na camada de armazenamento do Azure não são afetados e arquivos de dados/do registo são anexados ao processo do SQL Server recentemente inicializado. Este processo garante 99,99% de disponibilidade, mas ele pode ter alguns impactos no desempenho na carga de trabalho pesada, que está a executar devido ao tempo de transição e o fato do novo nó do SQL Server é iniciado com a cache de frio.

## <a name="when-to-choose-this-service-tier"></a>Quando escolher este escalão de serviço

Escalão de serviço de fins geral é uma camada de serviços de predefinição na base de dados do SQL do Azure que foi concebida para a maioria das cargas de trabalho genéricas. Se precisar de um motor de base de dados totalmente gerido com um SLA de 99,99% com uma latência de armazenamento entre 5 e 10 ms, que corresponde à IaaS do SQL do Azure na maioria dos casos, o escalão fins gerais é a opção para.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [críticas para a empresa](sql-database-service-tier-business-critical.md) e [Hiperescala](sql-database-service-tier-hyperscale.md) escalões.
- Saiba mais sobre [do Service Fabric](../service-fabric/service-fabric-overview.md).
- Para obter mais opções para alta disponibilidade e recuperação após desastre, veja [continuidade do negócio](sql-database-business-continuity.md).
