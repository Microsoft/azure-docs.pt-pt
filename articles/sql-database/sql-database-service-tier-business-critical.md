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
ms.openlocfilehash: ef1f420e4c4dbd38ad703eb0573fae36af078edb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496230"
---
# <a name="business-critical-tier---azure-sql-database"></a>Camada de Comercialmente Crítico-banco de dados SQL do Azure

> [!NOTE]
> Comercialmente Crítico camada é chamada Premium no modelo de compra de DTU. Para obter uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte [recursos e modelos de compra do banco de dados SQL do Azure](sql-database-purchase-models.md).

O banco de dados SQL do Azure é baseado em SQL Server Mecanismo de Banco de Dados arquitetura ajustada para o ambiente de nuvem a fim de garantir 99,99% de disponibilidade mesmo nos casos de falhas de infraestrutura. Há três modelos de arquitetura que são usados no banco de dados SQL do Azure:
- Uso Geral/Standard 
- Comercialmente Crítico/Premium
- Hyperscale

O modelo de camada de serviço Premium/Comercialmente Crítico baseia-se em um cluster de processos do mecanismo de banco de dados. Esse modelo de arquitetura depende de um fato de que sempre há um quorum de nós do mecanismo de banco de dados disponíveis e tem impacto mínimo sobre o desempenho na carga de trabalho mesmo durante as atividades de manutenção.

Atualizações e patches do Azure subjacentes ao sistema operacional, aos drivers e ao SQL Server Mecanismo de Banco de Dados de forma transparente com o mínimo de tempo de inatividade para os usuários finais. 

A disponibilidade Premium está habilitada nas camadas de serviço Premium e Comercialmente Crítico do banco de dados SQL do Azure e foi projetada para cargas de trabalho intensivas que não podem tolerar nenhum impacto no desempenho devido às operações de manutenção contínuas.

No modelo Premium, o banco de dados SQL do Azure integra a computação e o armazenamento em um único nó. A alta disponibilidade nesse modelo de arquitetura é obtida pela replicação de computação (SQL Server Mecanismo de Banco de Dados processo) e armazenamento (SSD anexado localmente) implantado no cluster de quatro nós, usando tecnologia semelhante a SQL Server [Always on grupos de disponibilidade ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Cluster de nós do mecanismo de banco de dados](media/sql-database-managed-instance/business-critical-service-tier.png)

O processo do mecanismo do banco de dados SQL e os arquivos MDF/ldf subjacentes são colocados no mesmo nó com o armazenamento SSD anexado localmente, fornecendo baixa latência à carga de trabalho. A alta disponibilidade é implementada usando uma tecnologia semelhante à SQL Server [Always on grupos de disponibilidade](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Cada banco de dados é um cluster de nós de banco de dados com um banco de dados primário que é acessível para carga de trabalho do cliente e um dos três processos secundários que contêm cópias de data. O nó primário envia constantemente as alterações para nós secundários para garantir que os dados estejam disponíveis em réplicas secundárias se o nó primário falhar por qualquer motivo. O failover é manipulado pelo SQL Server Mecanismo de Banco de Dados – uma réplica secundária torna-se o nó primário e uma nova réplica secundária é criada para garantir que nós sejam suficientes no cluster. A carga de trabalho é redirecionada automaticamente para o novo nó primário.

Além disso, Comercialmente Crítico cluster tem funcionalidade interna de [expansão de leitura](sql-database-read-scale-out.md) que fornece um nó somente leitura interno de custo livre que pode ser usado para executar consultas somente leitura (por exemplo, relatórios) que não devem afetar o desempenho do seu primário pico.

## <a name="when-to-choose-this-service-tier"></a>Quando escolher esta camada de serviço?

Comercialmente Crítico camada de serviço foi projetada para os aplicativos que exigem respostas de baixa latência do armazenamento SSD subjacente (1-2 ms em média), recuperação rápida se a infraestrutura subjacente falhar ou se for necessário descarregar relatórios, análises e somente leitura consultas à réplica secundária legível livre do banco de dados primário.

Os principais motivos pelos quais você deve escolher Comercialmente Crítico camada de serviço em vez de Uso Geral camada são:
-   Requisitos de latência de e/s baixos – a carga de trabalho que precisa da resposta rápida da camada de armazenamento (1-2 milissegundos em média) deve usar a camada de Comercialmente Crítico. 
-   Comunicação frequente entre o aplicativo e o banco de dados. O aplicativo que não pode aproveitar o cache da camada de aplicativo ou solicitar o envio [em lote](sql-database-use-batching-to-improve-performance.md) e precisa enviar muitas consultas SQL que devem ser processadas rapidamente são bons candidatos à camada de comercialmente crítico.
-   Grande número de atualizações – as operações de inserção, atualização e exclusão modificam as páginas de dados na memória (página suja) que devem ser salvas em arquivos de dados com a operação `CHECKPOINT`. A falha do processo do mecanismo de banco de dados potencial ou um failover do banco de dados com um grande número de páginas sujas pode aumentar o tempo de recuperação na camada Uso Geral. Use Comercialmente Crítico camada se você tiver uma carga de trabalho que cause muitas alterações na memória. 
-   Transações de longa execução que modificam dados. As transações que são abertas por um tempo maior impedem o truncamento do arquivo de log que pode aumentar o tamanho do log e o número de [arquivos de log virtuais (VLF)](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch). O alto número de VLF pode retardar a recuperação do banco de dados após o failover.
-   Carga de trabalho com consultas analíticas e de relatório que podem ser redirecionadas para a réplica somente leitura secundária de liberação de custo.
- Maior resiliência e recuperação mais rápida a partir das falhas. Em caso de falha do sistema, o banco de dados na instância primária será desabilitado e uma das réplicas secundárias se tornará imediatamente o novo banco de dados primário de leitura/gravação que está pronto para processar as consultas. O mecanismo de banco de dados não precisa analisar e refazer transações do arquivo de log e carregar todos os dados no buffer de memória.
- Proteção de corrupção de dados avançada-a camada de Comercialmente Crítico aproveita as réplicas de banco de dado nos bastidores para fins de continuidade dos negócios e, portanto, o serviço também utiliza o reparo automático de página, que é a mesma tecnologia usada para SQL Server banco de dados [espelhamento e grupos de disponibilidade](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring). Caso uma réplica não possa ler uma página devido a um problema de integridade de dados, uma cópia nova da página será recuperada de outra réplica, substituindo a página ilegível sem perda de dados ou tempo de inatividade do cliente. Essa funcionalidade será aplicável na camada Uso Geral se o banco de dados tiver uma réplica geográfica-secundária.
- Maior disponibilidade-a camada de Comercialmente Crítico na configuração multi-AZ garante 99,995% de disponibilidade, em comparação com 99,99% da camada Uso Geral.
- A Comercialmente Crítico camada de recuperação geográfica rápida configurada com replicação geográfica tem um RPO (objetivo de ponto de recuperação) garantido de 5 s e RTO (objetivo de tempo de recuperação) de 30 segundos para 100% de horas implantadas.

## <a name="next-steps"></a>Passos seguintes

- Encontre características de recursos (número de núcleos, e/s, memória) da camada de Comercialmente Crítico em [instância gerenciada](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), banco de dados individual no modelo de [VCORE](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) ou [modelo de DTU](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier)ou pool elástico no modelo de [VCORE](sql-database-vcore-resource-limits-elastic-pools.md#business-critical---provisioned-compute---gen4) e [modelo de DTU](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits).
- Saiba mais sobre as camadas de [uso geral](sql-database-service-tier-general-purpose.md) e [hiperescala](sql-database-service-tier-hyperscale.md) .
- Saiba mais sobre [Service Fabric](../service-fabric/service-fabric-overview.md).
- Para obter mais opções de alta disponibilidade e recuperação de desastres, consulte [continuidade de negócios](sql-database-business-continuity.md).
