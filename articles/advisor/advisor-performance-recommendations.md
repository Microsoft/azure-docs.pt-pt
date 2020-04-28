---
title: Melhorar o desempenho das aplicações do Azure com o Azure Advisor
description: Utilize o Advisor para otimizar o desempenho das suas implementações Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 405ec395feeb33b8511b9b915151b2ed9503c371
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75443062"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Melhorar o desempenho das aplicações do Azure com o Azure Advisor

As recomendações de desempenho do Azure Advisor ajudam a melhorar a rapidez e a capacidade de resposta das suas aplicações críticas ao negócio. Pode obter recomendações de desempenho do Advisor no separador **performance** do painel Advisor.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Reduza o tempo de DNS para viver no seu perfil de Traffic Manager para falhar em pontos finais saudáveis mais rapidamente

[As definições time to Live (TTL)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) no seu perfil do Traffic Manager permitem especificar a rapidez com que muda os pontos finais se um determinado ponto final parar de responder a consultas. A redução dos valores tTL significa que os clientes serão encaminhados para pontos finais funcionais mais rapidamente.

O Azure Advisor identifica os perfis do Traffic Manager com um TTL mais comprido configurado e recomenda configurar o TTL para 20 segundos ou 60 segundos, dependendo se o perfil está configurado para [fast failover](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Melhorar o desempenho da base de dados com o Assistente da BD SQL

O Advisor fornece-lhe uma visão consistente e consolidada das recomendações para todos os seus recursos Azure. Integra-se com o SQL Database Advisor para lhe trazer recomendações para melhorar o desempenho da sua base de dados SQL Azure.O SQL Database Advisor avalia o desempenho das suas bases de dados SQL Azure analisando o seu histórico de utilização. Em seguida, oferece recomendações que são mais adequadas para executar a carga de trabalho típica da base de dados.

> [!NOTE]
> Para obter recomendações, uma base de dados deve ter cerca de uma semana de utilização, e dentro dessa semana deve haver alguma atividade consistente. O SQL Database Advisor pode otimizar mais facilmente para padrões de consulta consistentes do que para explosões aleatórias de atividade.

Para mais informações sobre o SQL Database Advisor, consulte O Consultor de Base de [Dados SQL](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-app-service-performance-and-reliability"></a>Melhorar o desempenho e a fiabilidade do Serviço de Aplicações

O Azure Advisor integra recomendações de boas práticas para melhorar a sua experiência de Serviços de Aplicações e descobrir capacidades de plataforma relevantes. Exemplos de recomendações de Serviços de Aplicações são:
* Deteção de casos em que os recursos de memória ou CPU são esgotados por tempos de execução de apps com opções de mitigação.
* A deteção de casos em que a localização de recursos como aplicações web e bases de dados pode melhorar o desempenho e o menor custo.

Para obter mais informações sobre as recomendações dos Serviços de Aplicações, consulte [as Melhores Práticas para o Serviço de Aplicações Azure](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Utilizar discos geridos para evitar a aceleração do disco I/O

O Advisor identificará máquinas virtuais que pertencem a uma conta de armazenamento que está a atingir o seu alvo de escalabilidade. Esta condição torna os VMs suscetíveis à aceleração de E/O. O advisor recomendará que utilizem discos geridos para evitar a degradação do desempenho.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Melhorar o desempenho e a fiabilidade dos discos de máquinas virtuais utilizando o Armazenamento Premium

O Advisor identifica máquinas virtuais com discos standard que têm um elevado volume de transações na sua conta de armazenamento e recomenda a atualização para discos premium. 

O Armazenamento Azure Premium oferece suporte de disco de alto desempenho e baixa latência para máquinas virtuais que executam cargas de trabalho intensivas em I/O. Discos de máquinas virtuais que utilizam contas de armazenamento premium armazenam dados em unidades de estado sólido (SSDs). Para obter o melhor desempenho da sua aplicação, recomendamos que emigrou qualquer disco de máquina virtual que exija iOPS elevado para armazenamento premium.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Remova os dados distorcidos na tabela de depósito sql para aumentar o desempenho da consulta

O desvio de dados pode causar movimentos de dados desnecessários ou estrangulamentos de recursos ao executar a sua carga de trabalho. O Advisor irá detetar dados de distribuição superiores a 15% e recomendará que redistribua os seus dados e revisite as suas seleções de chaves de distribuição de tabelas. Para saber mais sobre identificar e remover o skew, veja [a resolução de problemas.](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Crie ou atualize estatísticas de tabelas desatualizadas na sua tabela de depósito de dados SQL para aumentar o desempenho da consulta

O Advisor identifica tabelas que não têm estatísticas atualizadas de [tabelas](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) e recomenda a criação ou atualização de estatísticas de tabelas. O optimizador de consulta de dados SQL usa estática atualizada para estimar a cardinalidade ou número de linhas no resultado da consulta que permite ao optimizador de consultas criar um plano de consulta de alta qualidade para o desempenho mais rápido.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Aumente para otimizar a utilização de cache nas tabelas do SQL Data Warehouse para aumentar o desempenho da consulta

O Azure Advisor deteta se o seu Armazém de Dados SQL tem uma elevada percentagem de cache usada e uma percentagem de impacto baixa. Esta condição indica despejo de cache elevada, o que pode afetar o desempenho do seu Armazém de Dados SQL. O consultor sugere que dimensione o seu Armazém de Dados SQL para garantir que aloca capacidade de cache suficiente para a sua carga de trabalho.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>Converter tabelas de Armazém de Dados SQL em tabelas replicadas para aumentar o desempenho da consulta

O Advisor identifica tabelas que não são mesas replicadas, mas que beneficiariam da conversão e sugere que converta estas tabelas. As recomendações baseiam-se no tamanho da tabela replicada, no número de colunas, no tipo de distribuição de tabelas e no número de divisórias da tabela SQL Data Warehouse. Pode ser fornecida uma heurística adicional na recomendação de contexto. Para saber mais sobre como esta recomendação é determinada, consulte as recomendações do Armazém de [Dados SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Migrar a sua Conta de Armazenamento para o Gestor de Recursos Azure para obter todas as funcionalidades mais recentes do Azure

Migrar o seu modelo de implementação da Conta de Armazenamento para o Gestor de Recursos Azure (Gestor de Recursos) para tirar partido das implementações do modelo, opções de segurança adicionais e a capacidade de fazer upgrade para uma conta GPv2 para a utilização das mais recentes funcionalidades do Azure Storage. O Advisor identificará quaisquer contas de armazenamento autónomas que estejam a utilizar o modelo de implementação Classic e recomenda a migração para o modelo de implementação do Gestor de Recursos.

> [!NOTE]
> Os alertas clássicos no Monitor Azure foram retirados em agosto de 2019. Recomendamos que atualize a sua conta de armazenamento clássica para utilizar o Gestor de Recursos para manter a funcionalidade de alerta com a nova plataforma. Para mais informações, consulte [Classic Alerts Retirement](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement#retirement-of-classic-monitoring-and-alerting-platform).

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Desenhe as suas contas de armazenamento para evitar atingir o limite máximo de subscrição

Uma região do Azure pode suportar um máximo de 250 contas de armazenamento por subscrição. Uma vez atingido o limite, não poderá criar mais contas de armazenamento nessa combinação região/subscrição. O Advisor irá verificar as suas subscrições e recomendações de superfície para que desenhe por menos contas de armazenamento para qualquer pessoa que esteja perto de atingir o limite máximo.

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>Otimize o desempenho dos seus servidores Azure MySQL, Azure PostgreSQL e Azure MariaDB 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>Corrija a pressão cpu dos seus servidores Azure MySQL, Azure PostgreSQL e Azure MariaDB com estrangulamentos de CPU
Uma utilização muito elevada do CPU durante um período prolongado pode causar um desempenho lento de consulta para a sua carga de trabalho. Aumentar o tamanho do CPU ajudará a otimizar o tempo de execução das consultas de base de dados e a melhorar o desempenho global. O Azure Advisor identificará servidores com uma alta utilização de CPU que provavelmente estão a executar cargas de trabalho restritas de CPU e recomendará a escala da sua computação.

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Reduza os constrangimentos de memória nos seus servidores Azure MySQL, Azure PostgreSQL e Azure MariaDB ou mude para um SKU otimizado pela memória
Uma relação de impacto de cache baixa pode resultar num desempenho de consulta mais lento e no aumento do IOPS. Isto pode ser devido a um mau plano de consulta ou a uma carga de trabalho intensiva de memória. A fixação do plano de consulta ou [o aumento](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers) da memória da Base de Dados Azure para servidor de base de dados PostgreSQL, servidor de base de dados Azure MySQL ou servidor Azure MariaDB ajudarão a otimizar a execução da carga de trabalho da base de dados. O Azure Advisor identifica os servidores afetados devido a este alto conjunto de tampão e recomenda a fixação do plano de consulta, passando para um SKU mais elevado com mais memória ou aumentando o tamanho do armazenamento para obter mais IOPS.

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Utilize uma réplica de leitura Azure MySQL ou Azure PostgreSQL para escalar as leituras para ler cargas de trabalho intensivas
O Azure Advisor aproveita a heurística baseada na carga de trabalho, como a relação de leituras para escritas no servidor nos últimos sete dias para identificar cargas de trabalho intensivas de leitura. A sua base de dados Azure para recurso PostgreSQL ou base de dados Azure para o recurso MySQL com uma relação leitura/escrita muito elevada pode resultar em cpu e/ou contençãos de memória que conduzam a um desempenho lento da consulta. A adição de uma [réplica](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) ajudará a escalonar as leituras para o servidor de réplicas, evitando restrições de CPU e/ou de memória no servidor primário. O Advisor identificará servidores com cargas de trabalho tão elevadas e recomendará a adição de uma [réplica](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) de leitura para descarregar algumas das cargas de trabalho de leitura.


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Dimensione o seu servidor Azure MySQL, Azure PostgreSQL ou Azure MariaDB para um SKU mais elevado para evitar restrições de ligação
Cada nova ligação ao seu servidor de base de dados ocupa alguma memória. O desempenho do servidor de base de dados degrada-se se as ligações ao servidor estiverem a falhar devido a um [limite superior](https://docs.microsoft.com/azure/postgresql/concepts-limits) na memória. O Azure Advisor identificará servidores em execução com muitas falhas de ligação e recomendará atualizar os limites de ligações do servidor para fornecer mais memória ao seu servidor, escalando a computação ou utilizando SKUs otimizados pela memória, que têm mais computação por núcleo.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Dimensione o seu Cache para um tamanho diferente ou SKU para melhorar o desempenho da Cache e da aplicação

As instâncias de cache são as melhores quando não estão a ser submetidas a alta pressão de memória, carga alta do servidor ou largura de banda de rede elevada, o que pode fazer com que fiquem sem resposta, experimentem a perda de dados ou fiquem indisponíveis. O Advisor identificará as instâncias cache nestas condições e recomendará a aplicação das melhores práticas para reduzir a pressão de memória, a carga do servidor ou a largura de banda da rede ou a escala para um tamanho diferente ou SKU com mais capacidade.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Adicione regiões com tráfego à sua conta Azure Cosmos DB

O Advisor irá detetar contas da Azure Cosmos DB que têm tráfego de uma região que não está configurada atualmente e recomendará adicionar aquela região. Isto melhorará a latência dos pedidos provenientes daquela região e assegurará a disponibilidade em caso de paralisação da região. [Saiba mais sobre a distribuição global de dados com a Azure Cosmos DB](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>Configure a sua política de indexação Do BD Azure Cosmos com caminhos incluídos ou excluídos do cliente

O Azure Advisor identificará os contentores Cosmos DB que estão a usar a política de indexação padrão, mas que poderiam beneficiar de uma política de indexação personalizada baseada no padrão de carga de trabalho. A política de indexação padrão indexa todas as propriedades, mas usando uma política de indexação personalizada com caminhos explícitos incluídos ou excluídos usados em filtros de consulta pode reduzir as RUs e armazenamento consumido para indexação. [Saiba mais sobre a modificação das políticas de índices](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Configurar o tamanho da página de consulta do Azure Cosmos DB (MaxItemCount) para -1 

O Azure Advisor identificará os contentores Azure Cosmos DB que estão a utilizar o tamanho da página de consulta de 100 e recomendará a utilização de uma página de -1 para exames mais rápidos. [Saiba mais sobre max item count](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Como aceder às recomendações de Desempenho em Advisor

1. Inscreva-se no [portal Azure](https://portal.azure.com)e, em seguida, abra [o Advisor.](https://aka.ms/azureadvisordashboard)

2.  No painel Advisor, clique no separador **Performance.**

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre recomendações do Advisor, consulte:

* [Introdução ao Consultor](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de Custos Consultivos](advisor-cost-recommendations.md)
* [Recomendações de Alta Disponibilidade Do Conselheiro](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do conselheiro](advisor-security-recommendations.md)
* [Recomendações de Excelência Operacional Consultiva](advisor-operational-excellence-recommendations.md)
