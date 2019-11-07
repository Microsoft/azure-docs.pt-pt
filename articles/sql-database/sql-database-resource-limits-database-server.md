---
title: Limites de recursos do servidor do banco de dados SQL do Azure
description: Este artigo fornece uma visão geral dos limites de recursos do servidor do banco de dados SQL do Azure para bancos de dados individuais e pools elásticos. Ele também fornece informações sobre o que acontece quando esses limites de recursos são atingidos ou ultrapassados.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 04/18/2019
ms.openlocfilehash: b358e69df1df579e91a9098c120c7e6b4e3f2ead
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687497"
---
# <a name="sql-database-resource-limits-for-azure-sql-database-server"></a>Limites de recursos do banco de dados SQL para o servidor do banco de dados SQL

Este artigo fornece uma visão geral dos limites de recursos do banco de dados SQL para um servidor de banco de dados SQL que gerencia bancos de dados individuais e pools elásticos. Ele também fornece informações sobre o que acontece quando esses limites de recursos são atingidos ou ultrapassados.

> [!NOTE]
> Para limites de instâncias gerenciadas, consulte [limites de recursos do banco de dados SQL para instâncias gerenciadas](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Limites máximos de recursos

| Recurso | Limite |
| :--- | :--- |
| Bancos de dados por servidor | 5000 |
| Número padrão de servidores por assinatura em qualquer região | 20 |
| Número máximo de servidores por assinatura em qualquer região | 200 |  
| Cota de DTU/eDTU por servidor | 54.000 |  
| cota vCore por servidor/instância | 540 |
| Máximo de pools por servidor | Limitado pelo número de DTUs ou vCores. Por exemplo, se cada pool for 1000 DTUs, um servidor poderá dar suporte a pools de 54.|
|||

> [!NOTE]
> Para obter mais/eDTU de DTU, cota de vCore ou mais servidores do que o valor padrão, uma nova solicitação de suporte pode ser enviada no portal do Azure para a assinatura com o tipo de problema "cota". A cota de DTU/eDTU e o limite de banco de dados por servidor restringem o número de pools elásticos por servidor.
> [!IMPORTANT]
> À medida que o número de bancos de dados se aproxima do limite por servidor do SQL Database, pode ocorrer o seguinte:
> - Aumento da latência na execução de consultas no banco de dados mestre.  Isso inclui exibições de estatísticas de utilização de recursos, como sys. resource_stats.
> - Aumento da latência em operações de gerenciamento e pontos de vista do portal de renderização que envolvem a enumeração de bancos de dados no servidor.

### <a name="storage-size"></a>Tamanho do armazenamento
- Para bancos de dados individuais rources, consulte limites de [recursos baseados em DTU](sql-database-dtu-resource-limits-single-databases.md) ou [limites de recursos baseados em vCore](sql-database-vcore-resource-limits-single-databases.md) para os limites de tamanho de armazenamento por tipo de preço.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>O que acontece quando os limites de recursos do banco de dados são atingidos

### <a name="compute-dtus-and-edtus--vcores"></a>Computação (DTUs e eDTUs/vCores)

Quando a utilização de computação de banco de dados (medida por DTUs e eDTUs ou vCores) se torna alta, a latência de consulta aumenta e pode até atingir o tempo limite. Sob essas condições, as consultas podem ser enfileiradas pelo serviço e são fornecidas recursos para execução à medida que o recurso se torna gratuito.
Ao encontrar alta utilização de computação, as opções de mitigação incluem:

- Aumentar o tamanho de computação do banco de dados ou do pool elástico para fornecer ao banco de dados mais recursos de computação. Consulte [dimensionar recursos de banco de dados individual](sql-database-single-database-scale.md) e [dimensionar recursos de pool elástico](sql-database-elastic-pool-scale.md).
- Otimização de consultas para reduzir a utilização de recursos de cada consulta. Para obter mais informações, consulte [ajuste/dicas de consulta](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Storage

Quando o espaço de banco de dados usado atinge o limite de tamanho máximo, as inserções e atualizações de banco de dados que aumentam a falha de tamanho e os clientes recebem uma [mensagem de erro](sql-database-develop-error-messages.md). As seleções e exclusões do banco de dados continuam a ser bem-sucedidos.

Ao encontrar alta utilização de espaço, as opções de mitigação incluem:

- Aumentar o tamanho máximo do banco de dados ou do pool elástico ou adicionar mais armazenamento. Consulte [dimensionar recursos de banco de dados individual](sql-database-single-database-scale.md) e [dimensionar recursos de pool elástico](sql-database-elastic-pool-scale.md).
- Se o banco de dados estiver em um pool elástico, como alternativa, o banco de dados poderá ser movido para fora do pool para que seu espaço de armazenamento não seja compartilhado com outros bancos de dados.
- Reduza um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Sessões e trabalhadores (solicitações)

O número máximo de sessões e trabalhos é determinado pela camada de serviço e pelo tamanho da computação (DTUs e eDTUs). Novas solicitações são rejeitadas quando os limites de sessão ou de trabalho são atingidos e os clientes recebem uma mensagem de erro. Embora o número de conexões disponíveis possa ser controlado pelo aplicativo, o número de trabalhos simultâneos geralmente é mais difícil de estimar e controlar. Isso é especialmente verdadeiro durante períodos de pico de carga quando os limites de recursos do banco de dados são alcançados e os trabalhadores se acumulam devido a consultas em execução mais longas.

Ao encontrar alta utilização de sessão ou de trabalho, as opções de mitigação incluem:

- Aumentando a camada de serviço ou o tamanho de computação do banco de dados ou do pool elástico. Consulte [dimensionar recursos de banco de dados individual](sql-database-single-database-scale.md) e [dimensionar recursos de pool elástico](sql-database-elastic-pool-scale.md).
- Otimizar consultas para reduzir a utilização de recursos de cada consulta se a causa da maior utilização do trabalhador for devido à contenção de recursos de computação. Para obter mais informações, consulte [ajuste/dicas de consulta](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="transaction-log-rate-governance"></a>Governança de taxa de log de transações 
A governança de taxa do log de transações é um processo no banco de dados SQL do Azure usado para limitar altas taxas de ingestão para cargas de trabalho, como inserção em massa, seleção INTO e compilações de índice. Esses limites são rastreados e aplicados no nível de subsegundos à taxa de geração de registro de log, limitando a taxa de transferência, independentemente de quantos IOs podem ser emitidos em relação aos arquivos de dados.  As taxas de geração de log de transações são atualmente dimensionadas linearmente até um ponto dependente de hardware, com a taxa de log máxima permitida de 96 MB/s com o modelo de compra vCore. 

> [!NOTE]
> O IOs físico real para os arquivos de log de transações não são governados ou limitados. 

As taxas de log são definidas de modo que elas possam ser alcançadas e mantidas em vários cenários, enquanto o sistema geral pode manter sua funcionalidade com impacto minimizado na carga do usuário. A governança de taxa de log garante que os backups de log de transações permaneçam dentro dos SLAs de recuperação publicados.  Essa governança também impede uma pendência excessiva em réplicas secundárias.

À medida que os registros de log são gerados, cada operação é avaliada e avaliada se deve ser atrasada para manter uma taxa máxima de log desejada (MB/s por segundo). Os atrasos não são adicionados quando os registros de log são liberados para armazenamento, em vez disso, a governança de taxa de log é aplicada durante a própria geração de taxa de log.

As taxas de geração de log reais impostas em tempo de execução também podem ser influenciadas por mecanismos de comentários, reduzindo temporariamente as taxas de log permitidas para que o sistema possa se estabilizar. Gerenciamento de espaço de arquivo de log, evitando a execução de condições de espaço de log e os mecanismos de replicação de grupo de disponibilidade podem diminuir temporariamente os limites gerais do sistema. 

A modelagem de tráfego do administrador da taxa de log é apresentada por meio dos seguintes tipos de espera (expostos na DMV [Sys. dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) ):

| Tipo de espera | Notas |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Limitação de banco de dados |
| POOL_LOG_RATE_GOVERNOR | Limitação de pool |
| INSTANCE_LOG_RATE_GOVERNOR | Limitação de nível de instância |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Controle de comentários, replicação física do grupo de disponibilidade em Premium/Comercialmente Crítico não está acompanhando |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Controle de comentários, limitando as taxas para evitar uma condição de espaço de log insuficiente |
|||

Ao encontrar um limite de taxa de log que está atrasando a escalabilidade desejada, considere as seguintes opções:
- Escale verticalmente para uma camada maior a fim de obter a taxa máxima de logs de 96 MB/s. 
- Se os dados que estão sendo carregados forem transitórios, ou seja, os dados de preparo em um processo de ETL, eles poderão ser carregados em tempdb (que é minimamente registrado). 
- Para cenários analíticos, carregue em uma tabela coberta por columnstore clusterizado. Isso reduz a taxa de log necessária devido à compactação. Essa técnica aumenta a utilização da CPU e só é aplicável a conjuntos de dados que se beneficiam de índices columnstore clusterizados. 

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre limites gerais do Azure, consulte [assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).
- Para obter informações sobre DTUs e eDTUs, consulte [DTUs e eDTUs](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Para obter informações sobre os limites de tamanho do tempdb, consulte [tempdb no banco de dados SQL do Azure](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
