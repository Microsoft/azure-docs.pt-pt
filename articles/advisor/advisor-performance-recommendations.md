---
title: Melhorar o desempenho de aplicativos do Azure com o Azure Advisor | Microsoft Docs
description: Use o Advisor para otimizar o desempenho de suas implantações do Azure.
services: advisor
documentationcenter: NA
author: saket-ms
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: sagupt
ms.openlocfilehash: 08af119e7f9e72de4a8ae4a1842289a4fd5d925e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144145"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Melhorar o desempenho de aplicativos do Azure com o Azure Advisor

As recomendações de desempenho do Azure Advisor ajudam a melhorar a velocidade e a capacidade de resposta de seus aplicativos críticos para os negócios. Você pode obter recomendações de desempenho do Advisor na guia **desempenho** do painel do Advisor.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Reduzir a vida útil do DNS em seu perfil do Gerenciador de tráfego para fazer failover para pontos de extremidade íntegros mais rapidamente

[As configurações de vida útil (TTL)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) em seu perfil do Gerenciador de tráfego permitem que você especifique a rapidez para alternar os pontos de extremidade se um determinado ponto de interrupção parar de responder às consultas. Reduzir os valores de TTL significa que os clientes serão roteados para os pontos de extremidade em funcionamento mais rapidamente.

O Azure Advisor identifica perfis do Gerenciador de tráfego com um TTL mais longo configurado e recomenda a configuração da TTL para 20 segundos ou 60 segundos, dependendo se o perfil está configurado para [failover rápido](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Melhorar o desempenho da base de dados com o Assistente da BD SQL

O Advisor fornece uma exibição consistente e consolidada de recomendações para todos os seus recursos do Azure. Ele se integra com Assistente do Banco de Dados SQL para oferecer recomendações para melhorar o desempenho do seu banco de dados SQL Azure. Assistente do Banco de Dados SQL avalia o desempenho de seus bancos de dados do SQL Azure analisando seu histórico de uso. Em seguida, ele oferece recomendações que são mais adequadas para executar a carga de trabalho típica do banco de dados.

> [!NOTE]
> Para obter recomendações, um banco de dados deve ter aproximadamente uma semana de uso e, nessa semana, deve haver alguma atividade consistente. Assistente do Banco de Dados SQL pode otimizar mais facilmente para padrões de consulta consistentes do que para intermitências aleatórias de atividade.

Para obter mais informações sobre Assistente do Banco de Dados SQL, consulte [Assistente do banco de dados SQL](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-app-service-performance-and-reliability"></a>Melhorar o desempenho e a confiabilidade do serviço de aplicativo

O Azure Advisor integra as práticas recomendadas para melhorar a experiência dos serviços de aplicativos e descobrir os recursos relevantes da plataforma. Exemplos de recomendações de serviços de aplicativos são:
* Detecção de instâncias em que os recursos de memória ou CPU são esgotados por tempos de execução de aplicativo com opções de mitigação.
* Detecção de instâncias em que posicionar recursos como aplicativos Web e bancos de dados pode melhorar o desempenho e reduzir o custo.

Para obter mais informações sobre recomendações de serviços de aplicativos, consulte [práticas recomendadas para o serviço de Azure app](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Usar Managed Disks para impedir a limitação de e/s de disco

O Advisor identificará as máquinas virtuais que pertencem a uma conta de armazenamento que está atingindo seu destino de escalabilidade. Essa condição torna as VMs suscetíveis à limitação de e/s. O Advisor recomendará que eles usem Managed Disks para evitar a degradação do desempenho.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Melhorar o desempenho e a confiabilidade dos discos de máquina virtual usando o armazenamento Premium

O Advisor identifica máquinas virtuais com discos padrão que têm um alto volume de transações em sua conta de armazenamento e recomenda a atualização para discos Premium. 

O armazenamento Premium do Azure fornece suporte de disco de alto desempenho e baixa latência para máquinas virtuais que executam cargas de trabalho com e/s intensivas. Os discos de máquina virtual que usam contas de armazenamento Premium armazenam dados em SSDs (unidades de estado sólido). Para obter o melhor desempenho para seu aplicativo, recomendamos que você migre qualquer disco de máquina virtual que exija IOPS alto para o armazenamento Premium.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Remover a distorção de dados na tabela do SQL data warehouse para aumentar o desempenho da consulta

A distorção de dados pode causar gargalos de recursos ou movimentação de dados desnecessários ao executar sua carga de trabalho. O Advisor detectará uma distorção de dados de distribuição maior que 15% e recomendará que você redistribua seus dados e revisite as seleções da chave de distribuição de tabela. Para saber mais sobre como identificar e remover a distorção, confira [Solucionando problemas de distorção](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Criar ou atualizar estatísticas de tabela desatualizadas na tabela do SQL data warehouse para aumentar o desempenho da consulta

O Advisor identifica tabelas que não têm [Estatísticas de tabela](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) atualizadas e recomenda a criação ou atualização de estatísticas de tabela. O otimizador de consulta do SQL data warehouse usa estáticos atualizados para estimar a cardinalidade ou o número de linhas no resultado da consulta que permite que o otimizador de consulta crie um plano de consulta de alta qualidade para um desempenho mais rápido.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Escalar verticalmente para otimizar a utilização de cache em suas tabelas de SQL Data Warehouse para aumentar o desempenho da consulta

O supervisor do Azure detecta se o SQL Data Warehouse tem alto percentual de cache usado e uma porcentagem de baixo impacto. Essa condição indica uma remoção de cache alta, o que pode afetar o desempenho do seu SQL Data Warehouse. O Advisor sugere que você Escale verticalmente sua SQL Data Warehouse para garantir que você aloque capacidade de cache suficiente para sua carga de trabalho.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>Converter tabelas SQL Data Warehouse em tabelas replicadas para aumentar o desempenho da consulta

O Advisor identifica tabelas que não são tabelas replicadas, mas se beneficiam da conversão e sugere que você converta essas tabelas. As recomendações são baseadas no tamanho da tabela replicada, no número de colunas, no tipo de distribuição de tabela e no número de partições da tabela de SQL Data Warehouse. Heurística adicional pode ser fornecida na recomendação para o contexto. Para saber mais sobre como essa recomendação é determinada, consulte [SQL data warehouse recomendações](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Migre sua conta de armazenamento para Azure Resource Manager para obter todos os recursos mais recentes do Azure

Migre seu modelo de implantação de conta de armazenamento para Azure Resource Manager (Resource Manager) para aproveitar as implantações de modelo, opções de segurança adicionais e a capacidade de atualizar para uma conta do GPv2 para a utilização dos recursos mais recentes do armazenamento do Azure. O Advisor identificará todas as contas de armazenamento autônomas que estão usando o modelo de implantação clássico e recomendará migrar para o modelo de implantação do Gerenciador de recursos.

> [!NOTE]
> Os alertas clássicos no Azure Monitor foram desativados em agosto de 2019. Recomendamos que você atualize sua conta de armazenamento clássico para usar o Resource Manager para manter a funcionalidade de alerta com a nova plataforma. Para obter mais informações, consulte [clássico alertas](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement#retirement-of-classic-monitoring-and-alerting-platform)de desativação.

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Projete suas contas de armazenamento para evitar atingir o limite máximo de assinaturas

Uma região do Azure pode dar suporte a um máximo de 250 contas de armazenamento por assinatura. Depois que o limite for atingido, você não poderá criar mais contas de armazenamento nessa combinação de região/assinatura. O Advisor verificará suas assinaturas e recomendações de superfície para você projetar para menos contas de armazenamento para qualquer um que esteja próximo de atingir o limite máximo.

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>Otimizar o desempenho do Azure MySQL, Azure PostgreSQL e servidores MariaDB do Azure 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>Corrija a pressão da CPU de seus servidores Azure MySQL, PostgreSQL do Azure e MariaDB do Azure com afunilamentos de CPU
A utilização muito alta da CPU por um período estendido pode causar um desempenho de consulta lento para sua carga de trabalho. Aumentar o tamanho da CPU ajudará a otimizar o tempo de execução das consultas de banco de dados e melhorar o desempenho geral. O assistente do Azure identificará os servidores com uma alta utilização da CPU que provavelmente está executando cargas de trabalho restritas de CPU e recomendará dimensionar sua computação.

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Reduzir as restrições de memória em seus servidores Azure MySQL, PostgreSQL do Azure e MariaDB do Azure ou mover para um SKU otimizado para memória
Uma taxa de acertos de cache baixa pode resultar em um desempenho de consulta mais lento e maior IOPS. Isso pode ser devido a um plano de consulta inadequado ou à execução de uma carga de trabalho com uso intensivo de memória. Corrigir o plano de consulta ou [aumentar a memória](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers) do servidor de banco de dados do Azure para PostgreSQL, servidor de banco de dados MySQL do Azure ou Azure MariaDB Server ajudará a otimizar a execução da carga de trabalho do banco de dados. O Azure Advisor identifica os servidores afetados devido a essa alta variação do pool de buffers e recomenda a correção do plano de consulta, a mudança para um SKU superior com mais memória ou o aumento do tamanho do armazenamento para obter mais IOPS.

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Use uma réplica de leitura do Azure MySQL ou Azure PostgreSQL para expandir leituras para cargas de trabalho com uso intensivo de leitura
O Azure Advisor aproveita a heurística baseada em carga de trabalho, como a taxa de leituras para gravações no servidor nos últimos sete dias para identificar cargas de trabalho com uso intensivo de leitura. O recurso de banco de dados do Azure para PostgreSQL ou de banco de dados do Azure para MySQL com uma taxa de leitura/gravação muito alta pode resultar em contenções de CPU e/ou memória que levam ao desempenho de consulta lento. Adicionar uma [réplica](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) ajudará a expandir as leituras para o servidor de réplica, impedindo restrições de CPU e/ou memória no servidor primário. O Advisor identificará os servidores com cargas de trabalho com alto volume de leitura e recomendará adicionar uma [réplica de leitura](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) para descarregar algumas das cargas de trabalho de leitura.


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Dimensione seu Azure MySQL, Azure PostgreSQL ou Azure MariaDB Server para uma SKU superior para evitar restrições de conexão
Cada nova conexão com o servidor de banco de dados ocupa alguma memória. O desempenho do servidor de banco de dados degrada se as conexões com o servidor estão falhando devido a um [limite superior](https://docs.microsoft.com/azure/postgresql/concepts-limits) na memória. O assistente do Azure identificará os servidores em execução com muitas falhas de conexão e recomendará atualizar os limites de conexões do servidor para fornecer mais memória ao servidor, expandindo a computação ou usando SKUs com otimização de memória, que têm mais computação por núcleo.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Dimensione seu cache para um tamanho ou SKU diferente para melhorar o desempenho do aplicativo e do cache

As instâncias de cache têm melhor desempenho quando não estão sendo executadas sob alta pressão de memória, alta carga de servidor ou alta largura de banda de rede, o que pode fazer com que elas não respondam, experimentem a perda de dados ou fiquem indisponíveis. O Advisor identificará as instâncias de cache nessas condições e recomendará aplicar as práticas recomendadas para reduzir a pressão de memória, a carga do servidor ou a largura de banda da rede ou o dimensionamento para um tamanho ou SKU diferente com mais capacidade.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Adicionar regiões com tráfego à sua conta de Azure Cosmos DB

O Advisor detectará Azure Cosmos DB contas que têm tráfego de uma região que não está configurada no momento e recomendamos a adição dessa região. Isso melhorará a latência de solicitações provenientes dessa região e garantirá a disponibilidade em caso de interrupções de região. [Saiba mais sobre a distribuição de dados globais com o Azure Cosmos DB](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>Configurar a política de indexação de Azure Cosmos DB com os caminhos incluídos ou excluídos do cliente

O assistente do Azure identificará Cosmos DB contêineres que estão usando a política de indexação padrão, mas pode se beneficiar de uma política de indexação personalizada com base no padrão de carga de trabalho. A política de indexação padrão indexa todas as propriedades, mas usar uma política de indexação personalizada com caminhos explícitos incluídos ou excluídos usados em filtros de consulta pode reduzir o RUs e o armazenamento consumidos para indexação. [Saiba mais sobre como modificar políticas de índice](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Configurar o tamanho da página de consulta do Azure Cosmos DB (MaxItemCount) para -1 

O assistente do Azure identificará Azure Cosmos DB contêineres que estão usando o tamanho da página de consulta de 100 e recomendamos usar um tamanho de página de-1 para verificações mais rápidas. [Saiba mais sobre a contagem máxima de itens](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Como acessar as recomendações de desempenho no Advisor

1. Entre no [portal do Azure](https://portal.azure.com)e abra o [Advisor](https://aka.ms/azureadvisordashboard).

2.  No painel do Advisor, clique na guia **desempenho** .

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as recomendações do Advisor, consulte:

* [Introdução ao Advisor](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de custo do Advisor](advisor-performance-recommendations.md)
* [Recomendações de alta disponibilidade do Advisor](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de excelência operacional do Advisor](advisor-operational-excellence-recommendations.md)
