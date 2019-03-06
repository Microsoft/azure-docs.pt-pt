---
title: Limites de recursos de servidor de base de dados SQL do Azure | Documentos da Microsoft
description: Este artigo fornece uma descrição geral do servidor de base de dados do Azure SQL limites de recursos para conjuntos elásticos e bases de dados individuais. Ele também fornece informações sobre o que acontece quando esses limites de recursos são atingidos ou excedidos.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan,moslake,josack
manager: craigg
ms.date: 03/01/2019
ms.openlocfilehash: 801b7de4b82c37503f2a14619112cbf46ca60a43
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447086"
---
# <a name="sql-database-resource-limits-for-azure-sql-database-server"></a>Limites de recursos de base de dados SQL para o servidor de base de dados do Azure SQL

Este artigo fornece uma visão geral dos limites de recursos da base de dados SQL para um servidor de base de dados SQL que gere os conjuntos elásticos e bases de dados individuais. Ele também fornece informações sobre o que acontece quando esses limites de recursos são atingidos ou excedidos.

> [!NOTE]
> Para limites de instâncias geridas, consulte [limites de recursos de base de dados SQL para instâncias geridas](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Limites de recursos máximo

| Recurso | Limite |
| :--- | :--- |
| Bases de dados por servidor | 5000 |
| Número de servidores por subscrição em qualquer região predefinido | 20 |
| Número máx. de servidores por subscrição em qualquer região | 200 |  
| DTU / quota de eDTU por servidor | 54,000 |  
| quota de vCore por instância do servidor | 540 |
| Conjuntos de máx. por servidor | Limitado pelo número de DTUs ou vCores. Por exemplo, se cada um dos conjuntos é 1000 DTUs, um servidor pode suportar 54 conjuntos.|
||||

> [!NOTE]
> Para obter mais quota de /eDTU DTU, quota de vCore ou mais servidores que o valor predefinido, pode ser submetido um novo pedido de suporte no portal do Azure para a subscrição com o tipo de problema "Quota". As DTU / limite de quota e base de dados de eDTU por servidor restringe o número de conjuntos elásticos por servidor.
> [!IMPORTANT]
> Como o número de bases de dados se aproxima do limite por servidor de base de dados SQL, pode ocorrer o seguinte:
> - A aumentar a latência em execução de consultas na base de dados mestra.  Isto inclui as vistas de estatísticas de utilização de recursos, tais como resource_stats.
> - Aumentar a latência nas operações de gestão e composição de pontos de vista portais que envolvem a enumerar as bases de dados no servidor.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>O que acontece quando atingir os limites de recursos da base de dados

### <a name="compute-dtus-and-edtus--vcores"></a>Computação (DTUs e eDTUs / vCores)

Quando a utilização de computação de base de dados (medida por DTUs e eDTUs ou vCores) se tornar elevada, aumentos de latência de consulta e pode até mesmo limite de tempo. Nestas condições, consultas podem ser colocados em fila pelo serviço e são fornecidas a recursos para execução como recurso estar livres.
Quando se deparar com a utilização de computação de alto, as opções de atenuação incluem:

- Aumentar o tamanho de computação da base de dados ou conjunto elástico para fornecer a base de dados com mais recursos de computação. Ver [Dimensionar recursos de base de dados individual](sql-database-single-database-scale.md) e [dimensionar os recursos do conjunto elástico](sql-database-elastic-pool-scale.md).
- Otimizar as consultas para reduzir a utilização de recursos de cada consulta. Para obter mais informações, consulte [ajuste de consulta/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Armazenamento

Quando utilizado de espaço de base de dados atinge o limite de tamanho máximo, insere a base de dados e as atualizações que aumentam o tamanho de dados falharem e os clientes recebem um [mensagem de erro](sql-database-develop-error-messages.md). SELECIONA de base de dados e eliminações continuam a ter êxito.

Quando se deparar com utilização elevada de espaço, as opções de atenuação incluem:

- Aumentar o tamanho máximo da base de dados ou elástica do agrupamento ou adicionar mais armazenamento. Ver [Dimensionar recursos de base de dados individual](sql-database-single-database-scale.md) e [dimensionar os recursos do conjunto elástico](sql-database-elastic-pool-scale.md).
- Se a base de dados num conjunto elástico, em seguida, em alternativa a base de dados pode ser movida fora do conjunto, para que o seu espaço de armazenamento não é partilhado com outras bases de dados.
- Reduzir uma base de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerir o espaço de ficheiro na base de dados do Azure SQL](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Sessões e funções de trabalho (pedidos)

O número máximo de sessões e funções de trabalho é determinado pela camada de serviço e tamanho (DTUs e eDTUs) de computação. Novos pedidos são rejeitados quando atingir os limites de sessão ou de trabalho e os clientes recebem uma mensagem de erro. Embora o número de ligações disponíveis pode ser controlado pelo aplicativo, o número de trabalhadores simultâneos, muitas vezes, é mais difícil de fazer uma estimativa e controle. Isso é especialmente verdadeiro durante períodos de carga de pico quando atingir os limites de recursos da base de dados e funções de trabalho acumuladas devido a consultas mais tempo de execução.

Quando se deparar com alta utilização de sessão ou de trabalho, as opções de atenuação incluem:

- Aumentar o serviço de camada ou tamanho do conjunto elástico ou base de dados de computação. Ver [Dimensionar recursos de base de dados individual](sql-database-single-database-scale.md) e [dimensionar os recursos do conjunto elástico](sql-database-elastic-pool-scale.md).
- Otimização de consultas para reduzir a utilização de recursos de cada consulta, se a causa da utilização da função de trabalho maior é devido à contenção de recursos de computação. Para obter mais informações, consulte [ajuste de consulta/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="transaction-log-rate-governance"></a>Governação de taxa de registo de transação 
Governação de taxa de registo de transação é um processo na base de dados do SQL do Azure utilizada para limitar as taxas de ingestão elevada para cargas de trabalho, como em massa insert, SELECT INTO e compilações de índice. Estes limites são controlados e impostos no nível de frações de segundos para a taxa de geração de registos do log, a limitação de taxa de transferência, independentemente de quantos IOs pode ser emitida em relação a arquivos de dados.  Velocidades de geração de log de transação atualmente dimensionadas de forma linear até um ponto que seja dependente do hardware, com o log máximo taxa permitido que está a ser 48 MB/s com o modelo de compra de vCore. 

> [!NOTE]
> O IOs físico real para os ficheiros de registo de transações não é regido ou limitado. 

As taxas de log são definidas, de modo que podem ser obtidos e constante numa variedade de cenários, enquanto o sistema geral pode manter sua funcionalidade com um impacto minimizado para a carga de utilizador. Governação de taxa de registo garante que as cópias de segurança permanecem na capacidade de recuperação publicada SLAs o registo de transação.  Este governação também impede que um registo de segurança excessivo em réplicas secundárias.

Como os registos são gerados, cada operação é avaliada e avaliada para se deve ser atrasada para manter uma taxa de registo pretendido máximo (MB/s por segundo). Os atrasos não são adicionados quando os registros de log são libertados para o armazenamento, em vez disso, governação de taxa de registo é aplicada durante a geração de taxa de registo em si.

A geração de log real taxas de imposto em tempo de execução também podem ser influenciadas por mecanismos de comentários, reduzindo temporariamente as taxas de log permitido para que o sistema pode estabilizar. Gestão de espaços de ficheiro de registo, evitando a deparar fora de condições do espaço de registo e o grupo de disponibilidade com mecanismos de replicação temporariamente pode diminuir os limites de todo o sistema. 

Formação de tráfego do registo taxa Governador é exibida por meio dos seguintes tipos de espera (exposto no [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV):

| Tipo de espera | Notas |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Limitar a base de dados |
| POOL_LOG_RATE_GOVERNOR | Limitação do conjunto |
| INSTANCE_LOG_RATE_GOVERNOR | Limitação de nível de instância |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Controlo de comentários, replicação física do grupo de disponibilidade no Premium/críticas para a empresa não manter atualizado |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Controlo de comentários, limitação de taxas para evitar um fora de condição de espaço de registo |
||||

Ao se deparar com um limite de taxa de registo é hampering escalabilidade desejada, considere as seguintes opções:
- Aumentar verticalmente para um escalão superior, para que a velocidade máxima de log MB/s 48. 
- Se os dados a serem carregados são transitórios, ou seja, testar dados num processo de ETL, que pode ser carregado para a tempdb (que é registado no mínimo). 
- Para cenários de análise, carregar para uma tabela columnstore em cluster abordado. Isso reduz a taxa de registo necessário devido à compactação. Essa técnica aumente a utilização da CPU e só é aplicável a conjuntos de dados que tiram partido de índices columnstore em cluster. 

## <a name="next-steps"></a>Passos Seguintes

- Para obter informações sobre os limites do Azure gerais, consulte [subscrição do Azure e limites do serviço, quotas e restrições](../azure-subscription-service-limits.md).
- Para obter informações sobre DTUs e eDTUs, veja [DTUs e eDTUs](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Para obter informações sobre limites de tamanho de tempdb, consulte [TempDB na base de dados do Azure SQL](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
