---
title: Melhorar o desempenho das aplicações da Azure com o Azure Advisor
description: Utilize o Advisor para otimizar o desempenho das suas implementações Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: d0b309fd35fa0a78685017e25eea0caf3f97da03
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84658408"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Melhorar o desempenho das aplicações da Azure com o Azure Advisor

As recomendações de desempenho do Azure Advisor ajudam a melhorar a rapidez e capacidade de resposta das suas aplicações críticas ao negócio. Pode obter recomendações de desempenho do Advisor no **separador Desempenho** do painel Advisor.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Reduza o tempo de DNS para viver no seu perfil de Gestor de Tráfego para falhar mais rapidamente para pontos finais saudáveis

[As definições de Tempo para Viver (TTL)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) no perfil do Gestor de Tráfego permitem especificar a rapidez com que se alterna os pontos finais se um determinado ponto final deixar de responder a consultas. A redução dos valores de TTL significa que os clientes serão encaminhados para pontos finais funcionais mais rapidamente.

O Azure Advisor identifica os perfis do Gestor de Tráfego com um TTL mais longo configurado e recomenda configurar o TTL a 20 segundos ou 60 segundos, dependendo se o perfil está configurado para [Fast Failover](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Melhorar o desempenho da base de dados com o Assistente da BD SQL

O Advisor fornece-lhe uma visão consistente e consolidada das recomendações para todos os seus recursos Azure. Integra-se com o SQL Database Advisor para lhe trazer recomendações para melhorar o desempenho da sua base de dados.O SQL Database Advisor avalia o desempenho das suas bases de dados analisando o seu histórico de utilização. Em seguida, oferece recomendações que são mais adequadas para executar a carga de trabalho típica da base de dados.

> [!NOTE]
> Para obter recomendações, uma base de dados deve ter cerca de uma semana de utilização, e dentro dessa semana deve haver alguma atividade consistente. O SQL Database Advisor pode otimizar mais facilmente para padrões de consulta consistentes do que para explosões aleatórias de atividade.

Para obter mais informações sobre o SqL Database Advisor, consulte [o SqL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="upgrade-your-storage-client-library-to-the-latest-version-for-better-reliability-and-performance"></a>Atualize a sua Biblioteca de Cliente de Armazenamento para a versão mais recente para melhorar a fiabilidade e o desempenho

A versão mais recente da Biblioteca de Clientes de Armazenamento/SDK contém correções para problemas comunicados pelos clientes e identificados proativamente através do nosso processo de GQ. A versão mais recente também apresenta otimizações de fiabilidade e desempenho, além de novas funcionalidades que podem melhorar a sua experiência geral com o Armazenamento do Azure. O Advisor fornece-lhe recomendações e passos para atualizar para a versão mais recente do SDK se estiver numa versão velha. As recomendações são para línguas apoiadas - C++ e .Net.

## <a name="improve-app-service-performance-and-reliability"></a>Melhorar o desempenho e fiabilidade do Serviço de Aplicações

O Azure Advisor integra recomendações de boas práticas para melhorar a sua experiência de Serviços de Aplicações e descobrir capacidades de plataforma relevantes. Exemplos de recomendações de Serviços de Aplicações são:
* Deteção de casos em que os recursos de memória ou CPU são esgotados por tempos de execução de aplicações com opções de mitigação.
* A deteção de casos em que a recolha de recursos como aplicações web e bases de dados pode melhorar o desempenho e reduzir o custo.

Para obter mais informações sobre as recomendações dos Serviços de Aplicações, consulte [as Melhores Práticas para o Serviço de Aplicações Azure.](https://azure.microsoft.com/documentation/articles/app-service-best-practices/)

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Utilize discos geridos para evitar o estrangulamento do disco I/O

O Advisor identificará máquinas virtuais que pertencem a uma conta de armazenamento que está a atingir o seu objetivo de escalabilidade. Esta condição torna os VMs suscetíveis ao estrangulamento de E/S. O Advisor recomendará que utilizem Discos Geridos para evitar a degradação do desempenho.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Melhorar o desempenho e a fiabilidade dos discos de máquinas virtuais utilizando o Armazenamento Premium

O Advisor identifica máquinas virtuais com discos standard que têm um elevado volume de transações na sua conta de armazenamento e recomenda a atualização para discos premium. 

O Azure Premium Storage oferece suporte de disco de alto desempenho e baixa latência para máquinas virtuais que executam cargas de trabalho intensivas de I/O. Os discos de máquinas virtuais que utilizam contas de armazenamento premium armazenam dados em unidades de estado sólido (SSDs). Para obter o melhor desempenho para a sua aplicação, recomendamos que tenha migrado quaisquer discos de máquinas virtuais que exijam um elevado IOPS para armazenamento premium.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Remova o distorção de dados na sua tabela de armazém de dados SQL para aumentar o desempenho da consulta

A distorção de dados pode causar movimentos de dados desnecessários ou estrangulamentos de recursos ao executar a sua carga de trabalho. O Advisor detetará dados de distribuição superiores a 15% e recomendará que redistribua os seus dados e revisite as seleções das chaves de distribuição da tabela. Para saber mais sobre a identificação e remoção de distorções, consulte [a resolução de problemas.](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Crie ou atualize estatísticas de tabelas desatualizadas na sua tabela de armazéns de dados SQL para aumentar o desempenho da consulta

O advisor identifica tabelas que não têm estatísticas atualizadas de [quadros](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) e recomenda a criação ou atualização de estatísticas de tabelas. O otimizador de armazém de dados SQL utiliza estáticas atualizadas para estimar a cardinalidade ou o número de linhas no resultado da consulta que permite ao otimizador de consulta criar um plano de consulta de alta qualidade para o desempenho mais rápido.

## <a name="improve-mysql-connection-management"></a>Melhorar a gestão das ligações MySQL

A análise do conselheiro ajuda a indicar que a sua aplicação conectada ao servidor MySQL pode não estar a gerir as ligações de forma eficiente. Este problema pode resultar num consumo de recursos desnecessário e numa latência global da aplicação mais elevada. Para melhorar a gestão das ligações, recomendamos que reduza o número de ligações de curta duração e elimine as ligações inativas desnecessárias. Pode fazê-lo ao configurar um conjunto de ligações do lado do servidor, como o ProxySQL.


## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Dimensione para otimizar a utilização de cache nas suas tabelas sql Data Warehouse para aumentar o desempenho da consulta

O Azure Advisor deteta se o seu SQL Data Warehouse tem uma elevada percentagem de cache usada e uma percentagem baixa de impacto. Esta condição indica um despejo de cache elevado, que pode afetar o desempenho do seu Armazém de Dados SQL. O Advisor sugere que escale o seu SQL Data Warehouse para garantir que alocar capacidade de cache suficiente para a sua carga de trabalho.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>Converter tabelas de Armazém de Dados SQL em tabelas replicadas para aumentar o desempenho da consulta

O advisora identifica tabelas que não são tabelas replicadas, mas que beneficiariam da conversão e sugere que você converta estas tabelas. As recomendações baseiam-se no tamanho da mesa replicada, no número de colunas, no tipo de distribuição de mesas e no número de divisórias da tabela SQL Data Warehouse. A heurística adicional pode ser fornecida na recomendação de contexto. Para saber mais sobre como esta recomendação é determinada, consulte [as Recomendações do Armazém de Dados SQL.](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables) 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Migrar a sua Conta de Armazenamento para o Azure Resource Manager para obter todas as funcionalidades mais recentes do Azure

Migrar o seu modelo de implementação da Conta de Armazenamento para O Gestor de Recursos Azure (Gestor de Recursos) para tirar partido das implementações do modelo, opções de segurança adicionais e a capacidade de fazer upgrade para uma conta GPv2 para utilização das funcionalidades mais recentes do Azure Storage. O Advisor identificará quaisquer contas de armazenamento autónomas que estejam a utilizar o modelo de implementação Classic e recomenda a migração para o modelo de implementação do Gestor de Recursos.

> [!NOTE]
> Os alertas clássicos no Azure Monitor foram retirados em agosto de 2019. Recomendamos que atualize a sua conta de armazenamento clássica para usar o Gestor de Recursos para manter a funcionalidade de alerta com a nova plataforma. Para mais informações, consulte [a Reforma dos Alertas Clássicos.](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement#retirement-of-classic-monitoring-and-alerting-platform)

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Desenhe as suas contas de armazenamento para evitar atingir o limite máximo de subscrição

Uma região de Azure pode suportar um máximo de 250 contas de armazenamento por subscrição. Uma vez atingido o limite, não poderá criar mais contas de armazenamento nessa região/combinação de subscrição. O Advisor verificará as suas subscrições e recomendações de superfície para que possa conceber menos contas de armazenamento para qualquer um que esteja perto de atingir o limite máximo.

## <a name="consider-increasing-the-size-of-your-vnet-gateway-sku-to-adress-high-p2s-use"></a>Considere aumentar o tamanho do seu VNet Gateway SKU para um uso P2S elevado

Cada SKU de gateway apenas suporta uma contagem especificada de ligações P2S em simultâneo. Se a contagem de ligação estiver perto do limite do gateway, as tentativas de ligação adicionais podem falhar. Aumentar o tamanho do seu gateway permitir-lhe-á suportar utilizadores P2S mais simultâneos. O conselheiro fornece recomendações e medidas a tomar, para isso.

## <a name="consider-increasing-the-size-of-your-vnet-gateway-sku-to-address-high-cpu"></a>Considere aumentar o tamanho do seu SKU de Gateway da VNet para lidar com a utilização elevada da CPU

Sob uma elevada carga de tráfego, o gateway de VPN pode deixar cair pacotes devido a uma elevada CPU. Deve considerar o upgrade do seu VPN Gateway SKU, uma vez que a sua VPN tem sido consistentemente em execução. Aumentar o tamanho do seu gateway VPN garantirá que as ligações não sejam largadas devido ao alto CPU. Recomendação de aconselhamento provdes para abordar esta questão de forma proactiva. 

## <a name="increase-batch-size-when-loading-to-maximize-load-throughput-data-compression-and-query-performance"></a>Aumente o tamanho do lote ao carregar para maximizar o débito de carga, a compressão de dados e o desempenho de consulta

O Advisor pode detetar que pode aumentar o desempenho e a produção da carga aumentando o tamanho do lote ao carregar na sua base de dados. Pode considerar a utilização da declaração COPY. Se não conseguir utilizar a instrução COPIAR, considere aumentar o tamanho do lote com os utilitários de carregamento, como a API SQLBulkCopy ou BCP – uma boa regra geral é que o tamanho do lote deve estar entre 100 mil e 1 milhão de linhas. Isto irá aumentar a produção de carga, compressão de dados e desempenho de consulta.

## <a name="co-locate-the-storage-account-within-the-same-region-to-minimize-latency-when-loading"></a>Colocalize a conta de armazenamento dentro da mesma região para minimizar a latência ao carregar

O Advisor pode detetar que está a carregar de uma região diferente da sua piscina SQL. Deve considerar o carregamento de uma conta de armazenamento que se encontre dentro da mesma região que o seu conjunto de SQL para minimizar a latência ao carregar dados. Isto ajudará a minimizar a latência e aumentar o desempenho da carga.

## <a name="unsupported-kubernetes-version-is-detected"></a>É detetada a versão kubernetes não suportada

O Advisor pode detetar se for detetada uma versão Kubernetes não suportada. A recomendação ajudará a garantir que o cluster Kubernetes funciona com uma versão suportada.

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>Otimize o desempenho dos seus servidores Azure MySQL, Azure PostgreSQL e Azure MariaDB 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>Corrija a pressão CPU dos seus servidores Azure MySQL, Azure PostgreSQL e Azure MariaDB com estrangulamentos cpu
Uma utilização muito elevada do CPU durante um período prolongado pode causar um desempenho de consulta lenta para a sua carga de trabalho. Aumentar o tamanho do CPU ajudará a otimizar o tempo de funcionamento das consultas de base de dados e melhorar o desempenho geral. O Azure Advisor identificará servidores com uma alta utilização do CPU que provavelmente estão a executar cargas de trabalho restritas de CPU e recomendará o escalonamento do seu cálculo.

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Reduza as restrições de memória nos servidores Azure MySQL, Azure PostgreSQL e Azure MariaDB ou mude para um SKU otimizado de memória
Uma relação de impacto de cache baixa pode resultar num desempenho de consulta mais lento e num aumento do IOPS. Isto pode ser devido a um mau plano de consulta ou executar uma carga de trabalho intensiva de memória. Fixar o plano de consulta ou [aumentar a memória](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers) da Base de Dados Azure para o servidor de base de dados PostgreSQL, o servidor de base de dados Azure MySQL ou o servidor Azure MariaDB ajudarão a otimizar a execução da carga de trabalho da base de dados. O Azure Advisor identifica os servidores afetados devido a este alto churn de piscina de tampão e recomenda a fixação do plano de consulta, movendo-se para um SKU mais alto com mais memória, ou aumentando o tamanho de armazenamento para obter mais IOPS.

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Use uma réplica de leitura Azure MySQL ou Azure PostgreSQL para escalar leituras para ler cargas de trabalho intensivas
O Azure Advisor aproveita a heurística baseada na carga de trabalho, como o rácio de leituras para escrever no servidor nos últimos sete dias para identificar cargas de trabalho intensivas de leitura. A sua base de dados Azure para recursos PostgreSQL ou base de dados Azure para recurso MySQL com uma relação de leitura/escrita muito alta pode resultar em cpu e/ou contenções de memória que conduzem a um desempenho de consulta lenta. A adição de uma [réplica](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) ajudará a escalonar as leituras para o servidor de réplicas, evitando restrições de CPU e/ou memória no servidor primário. O Advisor identificará servidores com cargas de trabalho tão elevadas e intensivas de leitura e recomendará a adição de uma [réplica de leitura](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas)para descarregar   algumas das cargas de trabalho de leitura.


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Dimensione o seu servidor Azure MySQL, Azure PostgreSQL ou Azure MariaDB para um SKU mais elevado para evitar restrições de ligação
Cada nova ligação ao servidor de base de dados ocupa alguma memória. O desempenho do servidor de base de dados degrada-se se as ligações ao seu servidor estiverem a falhar devido a um [limite superior](https://docs.microsoft.com/azure/postgresql/concepts-limits) na memória. O Azure Advisor identificará servidores em execução com muitas falhas de ligação e recomendará a atualização dos limites de ligações do seu servidor para fornecer mais memória ao seu servidor, aumentando o cálculo ou utilizando SKUs otimizados de memória, que têm mais cálculo por núcleo.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Dimensione a cache para um tamanho diferente ou SKU para melhorar o desempenho da Cache e da aplicação

As instâncias cache têm um melhor desempenho quando não funcionam sob alta pressão de memória, carga alta do servidor ou largura de banda de alta rede, o que pode fazer com que fiquem sem resposta, experimentem a perda de dados ou fiquem indisponíveis. O Advisor identificará as instâncias cache nestas condições e recomendará que aplique as melhores práticas para reduzir a pressão de memória, a carga do servidor ou a largura de banda da rede ou a escala para um tamanho diferente ou SKU com mais capacidade.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Adicione regiões com tráfego à sua conta DB Azure Cosmos

O conselheiro irá detetar as contas DB da Azure Cosmos que têm tráfego de uma região que não está configurada atualmente e recomendará a adição dessa região. Isto melhorará a latência dos pedidos provenientes dessa região e assegurará a disponibilidade em caso de interrupções na região. [Saiba mais sobre distribuição global de dados com a Azure Cosmos DB](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>Configure a sua política de indexação de DB Azure Cosmos com caminhos incluídos ou excluídos do cliente

O Azure Advisor identificará os contentores DB da Cosmos que estão a usar a política de indexação padrão, mas que podem beneficiar de uma política de indexação personalizada baseada no padrão de carga de trabalho. A política de indexação padrão indexa todas as propriedades, mas usar uma política de indexação personalizada com caminhos explícitos incluídos ou excluídos utilizados em filtros de consulta pode reduzir as RUs e armazenamento consumidos para indexação. [Saiba mais sobre a modificação das políticas de índices](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Configurar o tamanho da página de consulta do Azure Cosmos DB (MaxItemCount) para -1 

O Azure Advisor identificará os recipientes DB do Azure Cosmos que estão a usar o tamanho da página de consulta de 100 e recomendará a utilização de um tamanho de página de -1 para análises mais rápidas. [Saiba mais sobre Max Item Count](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Como aceder às recomendações de desempenho no Advisor

1. Inscreva-se no [portal Azure,](https://portal.azure.com)e depois abra [o Advisor](https://aka.ms/azureadvisordashboard).

2.  No painel 'Advisor', clique no **separador Desempenho.**

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre as recomendações do Advisor, consulte:

* [Introdução ao Conselheiro](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de custos do Assistente](advisor-cost-recommendations.md)
* [Recomendações de alta disponibilidade do conselheiro](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do conselheiro](advisor-security-recommendations.md)
* [Recomendações de excelência operacional do consultor](advisor-operational-excellence-recommendations.md)
