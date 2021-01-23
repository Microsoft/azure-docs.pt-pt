---
title: Melhorar o desempenho das aplicações da Azure com o Advisor
description: Utilize recomendações de desempenho no Azure Advisor para melhorar a rapidez e capacidade de resposta das suas aplicações críticas ao negócio.
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: f546527011402b9ea33321d56356d8aabe2412c1
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735533"
---
# <a name="improve-the-performance-of-azure-applications-by-using-azure-advisor"></a>Melhorar o desempenho das aplicações da Azure utilizando o Azure Advisor

As recomendações de desempenho no Azure Advisor podem ajudar a melhorar a rapidez e capacidade de resposta das suas aplicações críticas ao negócio. Pode obter recomendações de desempenho do Advisor no **separador Desempenho** do painel Advisor.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Reduza o tempo de live do DNS no seu perfil de Gestor de Tráfego para falhar mais rapidamente para pontos finais saudáveis

Pode utilizar [as definições de tempo para viver (TTL)](../traffic-manager/traffic-manager-performance-considerations.md) no seu perfil de Gestor de Tráfego Azure para especificar a rapidez com que se alterna os pontos finais se um determinado ponto final deixar de responder a perguntas. Se reduzir os valores de TTL, os clientes serão encaminhados para pontos finais funcionais mais rapidamente.

O Azure Advisor identifica perfis de Gestor de Tráfego que têm um TTL mais longo configurado. Recomenda a configuração do TTL para 20 segundos ou 60 segundos, dependendo se o perfil está configurado para [fast failover](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-by-using-sql-database-advisor-temporarily-disabled"></a>Melhorar o desempenho da base de dados utilizando o SqL Database Advisor (temporariamente desativado)

O Azure Advisor fornece uma visão consistente e consolidada das recomendações para todos os seus recursos Azure. Integra-se com o SQL Database Advisor para lhe trazer recomendações para melhorar o desempenho das suas bases de dados. O SQL Database Advisor avalia o desempenho das suas bases de dados analisando o seu histórico de utilização. Em seguida, oferece recomendações que são mais adequadas para executar a carga de trabalho típica da base de dados.

> [!NOTE]
> Antes de obter recomendações, a sua base de dados tem de estar em uso durante cerca de uma semana, e dentro dessa semana tem de haver alguma atividade consistente. O SQL Database Advisor pode otimizar mais facilmente para padrões de consulta consistentes do que para explosões aleatórias de atividade.

Para mais informações, consulte o [SqL Database Advisor](../azure-sql/database/database-advisor-implement-performance-recommendations.md).

## <a name="upgrade-your-storage-client-library-to-the-latest-version-for-better-reliability-and-performance"></a>Atualize a sua biblioteca de clientes de Armazenamento para a versão mais recente para uma melhor fiabilidade e desempenho

A versão mais recente da biblioteca de clientes de armazenamento SDK contém correções a problemas relatados pelos clientes e identificados proativamente através do nosso processo DE QA. A versão mais recente também transporta fiabilidade e otimização de desempenho juntamente com novas funcionalidades que podem melhorar a sua experiência geral com a utilização do Azure Storage. O Advisor fornece recomendações e passos necessários para atualizar para a versão mais recente do SDK se estiver a utilizar uma versão velha. As recomendações são para línguas apoiadas: C++ e .NET.

## <a name="improve-app-service-performance-and-reliability"></a>Melhorar o desempenho e fiabilidade do Serviço de Aplicações

O Azure Advisor integra recomendações para melhorar a sua experiência de Serviço de Aplicações e descobrir capacidades de plataforma relevantes. Exemplos de recomendações do Serviço de Aplicações são:
* Deteção de casos em que os recursos de memória ou CPU são esgotados por tempos de execução de aplicações, com opções de mitigação.
* A deteção de casos em que a co-localização de recursos como aplicações web e bases de dados pode melhorar o desempenho e reduzir os custos.

Para mais informações, consulte [as melhores práticas para o Azure App Service](../app-service/app-service-best-practices.md).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Utilize discos geridos para evitar o estrangulamento do disco I/O

O conselheiro identifica máquinas virtuais que pertencem a uma conta de armazenamento que está a atingir o seu objetivo de escalabilidade. Esta condição torna os VMs suscetíveis ao estrangulamento de E/S. O advisor recomendará que utilizem discos geridos para evitar a degradação do desempenho.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Melhorar o desempenho e a fiabilidade dos discos de máquinas virtuais utilizando o Armazenamento Premium

O Advisor identifica máquinas virtuais com discos standard que têm um elevado volume de transações na sua conta de armazenamento. Recomenda a atualização para discos premium. 

O Azure Premium Storage oferece suporte de disco de alto desempenho e baixa latência para máquinas virtuais que executam cargas de trabalho intensivas de I/O. Os discos de máquinas virtuais que utilizam contas premium de armazenamento armazenam dados em unidades de estado sólido (SSDs). Para obter o melhor desempenho para a sua aplicação, recomendamos que migrar quaisquer discos de máquinas virtuais que exijam um elevado IOPS para Armazenamento Premium.

## <a name="remove-data-skew-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Remova o enviesamento de dados nas suas tabelas Azure Synapse Analytics para aumentar o desempenho da consulta

A distorção de dados pode causar movimentos de dados desnecessários ou estrangulamentos de recursos quando executar a sua carga de trabalho. O consultor deteta dados de distribuição superiores a 15%. Recomenda que redistribua os seus dados e revisite as seleções das chaves de distribuição da sua mesa. Para saber mais sobre a identificação e remoção de distorções, consulte [a resolução de problemas.](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="create-or-update-outdated-table-statistics-in-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Crie ou atualize estatísticas de tabelas desatualizadas nas tabelas Azure Synapse Analytics para aumentar o desempenho da consulta

O advisor identifica tabelas que não têm estatísticas atualizadas de [tabelas](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-statistics.md) e recomenda a criação ou atualização das estatísticas. O otimizador de consultas no Azure Synapse Analytics utiliza estatísticas atualizadas para estimar a cardinalidade ou o número de linhas nos resultados da consulta. Estas estimativas permitem ao otimizador de consultas criar um plano de consulta para fornecer o desempenho mais rápido.

## <a name="improve-mysql-connection-management"></a>Melhorar a gestão das ligações MySQL

A análise do conselho pode indicar que a sua aplicação conectada a um servidor MySQL pode não estar a gerir as ligações de forma eficiente. Esta condição poderia conduzir a um consumo desnecessário de recursos e a uma maior latência da aplicação. Para melhorar a gestão das ligações, recomendamos que reduza o número de ligações de curta duração e elimine as ligações inativas desnecessárias. Pode efetá-las configurando um pooler de ligação do lado do servidor, como o ProxySQL.


## <a name="scale-up-to-optimize-cache-utilization-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Dimensione para otimizar a utilização de cache nas suas tabelas Azure Synapse Analytics para aumentar o desempenho da consulta

O Azure Advisor deteta se as suas tabelas Azure Synapse Analytics têm uma alta percentagem de cache e uma percentagem baixa de impacto. Esta condição indica um despejo de cache elevado, o que pode afetar o desempenho da sua instância Azure Synapse Analytics. O Advisor recomenda que este escale o seu exemplo Azure Synapse Analytics para garantir que aloque capacidade de cache suficiente para a sua carga de trabalho.

## <a name="convert-azure-synapse-analytics-tables-to-replicated-tables-to-increase-query-performance"></a>Converter as tabelas Azure Synapse Analytics em tabelas replicadas para aumentar o desempenho da consulta

O conselheiro identifica tabelas que não são tabelas replicadas, mas que beneficiariam da conversão. Sugere que converta estas mesas. As recomendações baseiam-se em:
- O tamanho da mesa replicada. 
- O número de colunas. 
- O tipo de distribuição de mesa. 
- O número de divisórias na tabela Azure Synapse Analytics. 

Podem ser fornecidas heurísticas adicionais na recomendação de contexto. Para saber mais sobre como esta recomendação é determinada, consulte [as recomendações da Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-concept-recommendations.md#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-the-latest-azure-features"></a>Migrar a sua conta de armazenamento para o Azure Resource Manager para obter as mais recentes funcionalidades do Azure

Migrar o seu modelo de implementação da conta de armazenamento para o Azure Resource Manager para tirar partido de:
- Implementações de modelos.
- Opções de segurança adicionais.
- A capacidade de fazer o upgrade para uma conta GPv2 para que possa utilizar as mais recentes funcionalidades de Armazenamento Azure. 

O Advisor identifica quaisquer contas de armazenamento autónoma que estejam a utilizar o modelo clássico de implementação e recomenda a migração para o modelo de implementação do Gestor de Recursos.

> [!NOTE]
> Os alertas clássicos no Azure Monitor foram retirados em agosto de 2019. Recomendamos que atualize a sua conta de armazenamento clássica para usar o Gestor de Recursos para manter a funcionalidade de alerta com a nova plataforma. Para mais informações, consulte [a reforma dos alertas clássicos.](../azure-monitor/platform/monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)

## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>Desenhe as suas contas de armazenamento para evitar atingir o limite máximo de subscrição

Uma região de Azure suporta um máximo de 250 contas de armazenamento por subscrição. Depois deste limite ser atingido, não poderá criar contas de armazenamento nessa região/combinação de subscrição. O Advisor verifica as suas subscrições e fornece recomendações para que você concene para menos contas de armazenamento para qualquer subscrição/região que esteja perto de atingir o limite máximo.

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-p2s-use"></a>Considere aumentar o tamanho do seu VPN Gateway SKU para abordar o uso elevado de P2S

Cada Azure VPN Gateway SKU pode suportar apenas um número especificado de ligações P2S simultâneas. Se a contagem de ligação estiver perto do limite do gateway, podem falhar tentativas de ligação adicionais. Se aumentar o tamanho do seu gateway, poderá suportar utilizadores P2S mais simultâneos. O Advisor fornece recomendações e instruções para aumentar o tamanho do seu gateway.

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-cpu"></a>Considere aumentar o tamanho do seu VPN Gateway SKU para abordar o alto CPU

Sob alta carga de tráfego, o seu gateway VPN pode deixar cair pacotes por causa de alta CPU. Considere melhorar o seu VPN Gateway SKU. Aumentar o tamanho do seu gateway VPN garantirá que as ligações não sejam largadas por causa de um CPU elevado. O consultor fornece recomendações para resolver proactivamente este problema. 

## <a name="increase-batch-size-when-loading-to-maximize-load-throughput-data-compression-and-query-performance"></a>Aumente o tamanho do lote ao carregar para maximizar o débito de carga, a compressão de dados e o desempenho de consulta

O Advisor deteta se pode aumentar o desempenho e a produção da carga aumentando o tamanho do lote ao carregar na sua base de dados. Pode considerar a utilização da declaração COPY. Se não puder utilizar a declaração COPY, considere aumentar o tamanho do lote quando utilizar utilitários de carregamento como a API SQLBulkCopy ou o BCP. Uma boa regra geral é usar um tamanho de lote entre 100 mil e 1 milhão de linhas. O aumento do tamanho do lote aumentará a produção de carga, a compressão de dados e o desempenho da consulta.

## <a name="co-locate-the-storage-account-in-the-same-region-to-minimize-latency-when-loading"></a>Co-localizar a conta de armazenamento na mesma região para minimizar a latência no carregamento

O advisor deteta se está a carregar de uma região diferente da sua piscina SQL dedicada. Considere o carregamento de uma conta de armazenamento que está na mesma região que o seu pool DE SQL dedicado para minimizar a latência ao carregar dados. Esta alteração ajudará a minimizar a latência e a aumentar o desempenho da carga.

## <a name="use-a-supported-kubernetes-version"></a>Use uma versão kubernetes suportada

O advisor deteta versões não apoiadas de Kubernetes.

## <a name="optimize-the-performance-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers"></a>Otimize o desempenho da sua Base de Dados Azure para MySQL, Azure Database for PostgreSQL e Azure Database para servidores MariaDB

### <a name="fix-the-cpu-pressure-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-with-cpu-bottlenecks"></a>Corrija a pressão cpu da sua Base de Dados Azure para MySQL, Base de Dados Azure para PostgreSQL e Base de Dados Azure para servidores MariaDB com estrangulamentos cpu
Uma utilização elevada do CPU durante um período prolongado pode causar um desempenho de consulta lenta para a sua carga de trabalho. Aumentar o tamanho do CPU ajudará a otimizar o tempo de funcionação das consultas de base de dados e a melhorar o desempenho geral. O Advisor identifica servidores com uma alta utilização do CPU que provavelmente estão a executar cargas de trabalho restritas ao CPU e recomenda o escalonamento do seu cálculo.

### <a name="reduce-memory-constraints-on-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Reduza as restrições de memória na sua Base de Dados Azure para o MySQL, Base de Dados Azure para PostgreSQL e Base de Dados de Azure para servidores MariaDB, ou mude para um SKU otimizado de memória
Uma relação de impacto de cache baixa pode resultar num desempenho de consulta mais lento e num aumento do IOPS. Esta condição pode ser causada por um mau plano de consulta ou por uma carga de trabalho intensiva na memória. Fixar o plano de consulta ou [aumentar a memória](../postgresql/concepts-pricing-tiers.md) da Base de Dados Azure para PostgreSQL, Base de Dados Azure para o MySQL ou Azure Database para o servidor MariaDB ajudará a otimizar a execução da carga de trabalho da base de dados. O Azure Advisor identifica os servidores afetados por este alto churn de piscina de tampão. Recomenda que tome uma destas ações: 
- Corrija o plano de consulta
- Mude-se para um SKU que tem mais memória 
- Aumente o tamanho do armazenamento para obter mais IOPS.

### <a name="use-an-azure-database-for-mysql-or-azure-database-for-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Utilize uma base de dados Azure para MySQL ou Azure Database para pós-SQL ler réplica para escalar leituras para cargas de trabalho intensivas de leitura
O advisor usa heurística baseada na carga de trabalho, como a relação de leituras para escrever no servidor nos últimos sete dias para identificar cargas de trabalho intensivas de leitura. Uma base de dados Azure para postgreSQL ou Azure Database para recurso MySQL com uma relação de leitura/escrita elevada pode resultar em correções de CPU ou memória e levar a um desempenho de consulta lenta. A adição de uma [réplica](../postgresql/howto-read-replicas-portal.md) ajudará a reduzir as leituras para o servidor de réplicas e evitar restrições de CPU ou memória no servidor primário. O Advisor identifica servidores com cargas de trabalho intensivas de leitura e recomenda que adicione uma [réplica de leitura](../postgresql/concepts-read-replicas.md) para descarregar algumas das cargas de trabalho de leitura.


### <a name="scale-your-azure-database-for-mysql-azure-database-for-postgresql-or-azure-database-for-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Dimensione a sua Base de Dados de Azure para MySQL, Base de Dados Azure para PostgreSQL ou Base de Dados Azure para servidor MariaDB para um SKU mais elevado para evitar restrições de conexão
Cada nova ligação ao servidor de base de dados ocupa a memória. O desempenho do servidor de base de dados degrada-se se as ligações ao seu servidor estiverem a falhar devido a um [limite superior](../postgresql/concepts-limits.md) na memória. O Azure Advisor identifica servidores em execução com muitas falhas de ligação. Recomenda a atualização dos limites de ligação do seu servidor para fornecer mais memória ao seu servidor, tomando uma destas ações:
- Escalar a computação. 
- Use SKUs otimizados de memória, que têm mais cálculo por núcleo.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Dimensione a sua cache para um tamanho diferente ou SKU para melhorar o desempenho da cache e da aplicação

As instâncias de cache têm um melhor desempenho quando não estão a funcionar sob pressão de memória alta, carga alta do servidor ou alta largura de banda de rede. Estas condições podem fazê-las ficar sem resposta, experimentar a perda de dados ou ficar indisponíveis. O conselheiro identifica casos de cache nestas condições. Recomenda que tome uma destas ações:
- Aplicar as melhores práticas para reduzir a pressão da memória, a carga do servidor ou a largura de banda da rede.
- Escala para um tamanho diferente ou SKU que tem mais capacidade.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Adicione regiões com tráfego à sua conta DB Azure Cosmos

O advisor deteta contas DB da Azure Cosmos que têm tráfego de uma região que não está configurada atualmente. Recomenda a adição daquela região. Ao fazê-lo, melhora a latência dos pedidos provenientes dessa região e assegura a disponibilidade em caso de paralisação da região. [Saiba mais sobre a distribuição global de dados com a Azure Cosmos DB.](../cosmos-db/distribute-data-globally.md)

## <a name="configure-your-azure-cosmos-db-indexing-policy-by-using-custom-included-or-excluded-paths"></a>Configure a sua política de indexação Azure Cosmos DB utilizando caminhos personalizados incluídos ou excluídos

O advisor identifica os contentores DB da Azure Cosmos que estão a usar a política de indexação padrão, mas que podem beneficiar de uma política de indexação personalizada. Esta determinação baseia-se no padrão de carga de trabalho. A política de indexação padrão indexa todas as propriedades. Uma política de indexação personalizada com caminhos explícitos incluídos ou excluídos utilizados em filtros de consulta pode reduzir as RUs e o armazenamento consumido para indexação. [Saiba mais sobre a modificação das políticas de índice.](../cosmos-db/index-policy.md)

## <a name="set-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Deite o tamanho da página de consulta Azure Cosmos DB (MaxItemCount) para -1 

O Azure Advisor identifica os recipientes DB da Azure Cosmos que utilizam um tamanho de página de consulta de 100. Recomenda a utilização de uma página de -1 para análises mais rápidas. [Saiba mais sobre o MaxItemCount.](../cosmos-db/sql-api-query-metrics.md)

## <a name="consider-using-accelerated-writes-feature-in-your-hbase-cluster-to-improve-cluster-performance"></a>Considere usar a funcionalidade De Escritas Aceleradas no seu cluster HBase para melhorar o desempenho do cluster
O Azure Advisor analisa os registos do sistema nos últimos 7 dias e identifica se o seu cluster encontrou os seguintes cenários:
1. Latência do tempo de sincronização de WAL elevada 
2. Contagem elevada de pedidos de escrita (pelo menos 3 janelas de uma hora de mais de 1000 avg_write_requests/segundo/nó)

Estas condições são indicadores de que o seu cluster sofre de latências de escrita elevadas. Isto pode ser devido à carga de trabalho pesada realizada no seu aglomerado. Para melhorar o desempenho do seu cluster, pode considerar a utilização da funcionalidade De Escritas Aceleradas fornecida pela Azure HDInsight HBase. A funcionalidade Escritas Aceleradas para clusters Apache HBase do HDInsight anexa discos geridos por SSD premium a todos os RegionServer (nó de trabalho) em vez de utilizar o armazenamento na cloud. Como resultado, proporciona baixa latência de escrita e melhor resiliência para as suas aplicações. Para ler mais sobre esta funcionalidade, [saiba mais](../hdinsight/hbase/apache-hbase-accelerated-writes.md#how-to-enable-accelerated-writes-for-hbase-in-hdinsight)

## <a name="review-azure-data-explorer-table-cache-period-policy-for-better-performance-preview"></a>Rever Azure Data Explorer tabela-período de cache (política) para um melhor desempenho (Pré-visualização)
Esta recomendação expõe as tabelas do Azure Data Explorer que têm um grande número de consultas que procuram além do período de cache configurado (política). (Verá as 10 tabelas que acedem mais aos dados fora da cache, em percentagem de consultas). A ação recomendada para melhorar o desempenho do cluster: limitar as consultas nesta tabela ao intervalo de tempo mínimo necessário, (dentro do intervalo de tempo definido na política). Como alternativa, se os dados da totalidade do intervalo de tempo forem necessários, aumente o período de cache para o valor recomendado.

## <a name="improve-performance-by-optimizing-mysql-temporary-table-sizing"></a>Melhore o desempenho ao otimizar o dimensionamento de tabelas temporárias MySQL
A análise do conselho indica que o seu servidor MySQL pode estar a incorrer em sobrecargas desnecessárias de I/O devido a baixas definições de parâmetros de tabela temporária. Tal pode resultar em transações baseadas em discos desnecessárias e desempenho reduzido. Recomendamos que aumente os valores dos parâmetros “tmp_table_size” e “max_heap_table_size” para reduzir o número de transações baseadas em discos. [Saiba mais](https://aka.ms/azure_mysql_tmp_table)

## <a name="distribute-data-in-server-group-to-distribute-workload-among-nodes"></a>Distribuir dados no grupo de servidores para distribuir a carga de trabalho entre nós
O advisor identifica os grupos de servidores onde os dados não foram distribuídos, mas permanece no coordenador. Com base nisto, o Advisor recomenda que para benefícios completos da Hyperscale (Citus) distribuam dados em nós de trabalhadores para os seus grupos de servidores. Isto melhorará o desempenho da consulta utilizando recursos de cada nó no grupo do servidor. [Saiba mais](https://go.microsoft.com/fwlink/?linkid=2135201) 

## <a name="improve-user-experience-and-connectivity-by-deploying-vms-closer-to-windows-virtual-desktop-deployment-location"></a>Melhorar a experiência e conectividade do utilizador implementando VMs mais perto da localização de implementação virtual do Windows Desktop
Determinámos que as VMs estão localizadas numa região diferente ou longe do local a partir do qual os utilizadores se ligam com o Windows Virtual Desktop (WVD), o que pode provocar tempos de resposta de ligação prolongados e afetar a experiência geral do utilizador no WVD. Ao criar VMs para os conjuntos de anfitriões, deve tentar utilizar uma região mais próxima do utilizador. Ter uma maior proximidade garante a satisfação contínua com o serviço WVD e uma melhor qualidade da experiência geral. [Saiba mais sobre a latência da conexão aqui.](../virtual-desktop/connection-latency.md)

## <a name="upgrade-to-the-latest-version-of-the-immersive-reader-sdk"></a>Atualizar para a versão mais recente do SDK de Leitura Avançada
Identificámos recursos nesta subscrição com versões desatualizadas do SDK de Leitura Avançada. Utilizar a versão mais recente do SDK de Leitura Avançada proporciona-lhe uma segurança atualizada, desempenho e um conjunto expandido de funcionalidades para personalizar e melhorar a sua experiência de integração.
Saiba mais sobre [o leitor imersivo SDK.](../cognitive-services/immersive-reader/index.yml)

## <a name="improve-vm-performance-by-changing-the-maximum-session-limit"></a>Melhorar o desempenho do VM alterando o limite máximo de sessão

O Advisor deteta que você tem uma piscina hospedeira que tem o primeiro conjunto de profundidade como o algoritmo de equilíbrio de carga, e que o limite máximo de sessão da piscina do anfitrião é maior ou igual a 999999. O balanceamento de carga em profundidade utiliza o limite máximo de sessões para determinar o número máximo de utilizadores que podem ter sessões em simultâneo num anfitrião com uma única sessão. Se o limite máximo de sessão for demasiado elevado, todas as sessões de utilizador serão direcionadas para o mesmo anfitrião da sessão, o que causará problemas de desempenho e fiabilidade. Por isso, ao configurar uma piscina hospedeira para ter um primeiro equilíbrio de carga de profundidade, deve definir um limite de sessão máxima adequado de acordo com a configuração da sua implantação e capacidade dos seus VMs. 

Para saber mais sobre o equilíbrio de carga no Windows Virtual Desktop, consulte [o método de equilíbrio de carga virtual do Windows Desktop](../virtual-desktop/troubleshoot-set-up-overview.md).

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Como aceder às recomendações de desempenho no Advisor

1. Inscreva-se no [portal Azure,](https://portal.azure.com)e depois abra [o Advisor](https://aka.ms/azureadvisordashboard).

2.  No painel 'Advisor', selecione o **separador Desempenho.**

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre as recomendações do Advisor, consulte:

* [Introdução ao Conselheiro](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Pontuação do conselheiro](azure-advisor-score.md)
* [Recomendações de custos do Assistente](advisor-cost-recommendations.md)
* [Recomendações de fiabilidade do consultor](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do conselheiro](advisor-security-recommendations.md)
* [Recomendações de excelência operacional do consultor](advisor-operational-excellence-recommendations.md)
* [Conselheiro REST API](/rest/api/advisor/)