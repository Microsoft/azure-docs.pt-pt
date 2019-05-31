---
title: Melhorar o desempenho de aplicações do Azure com o Assistente do Azure | Documentos da Microsoft
description: Utilize o Assistente para otimizar o desempenho das suas implementações do Azure.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: 8fdae1e12e56dcbcb56941726b0c089ad59b8fc8
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254657"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Melhorar o desempenho de aplicações do Azure com o Assistente do Azure

Recomendações de desempenho do Assistente do Azure ajudam a melhorar a velocidade e a capacidade de resposta das suas aplicações críticas para a empresa. Pode obter recomendações de desempenho do Advisor o **desempenho** separador do dashboard do Advisor.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Reduzir o tempo DNS ao vivo no seu perfil do Gestor de tráfego para a ativação pós-falha para pontos finais de bom estado de funcionamento mais rapidamente

[Tempo para as definições de Live (TTL)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) no perfil do Traffic Manager permitem-lhe especificar como rapidamente mudar pontos de extremidade, se um determinado ponto de final deixa de responder às consultas. Reduzir os valores TTL significa que os clientes serão encaminhados para pontos finais de funcionamento mais rápidos.

O Azure Advisor identifica perfis do Gestor de tráfego com um valor de TTL mais configurado e recomenda configurar o valor de TTL para 20 segundos ou 60 segundos, dependendo se o perfil está configurado para [rápida ativação pós-falha](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Melhore o desempenho de base de dados com o Assistente da BD SQL

O assistente fornece-lhe uma vista consolidada consistente de recomendações para todos os seus recursos do Azure. Ele se integra com o Assistente de base de dados SQL para lhe oferecer recomendações para melhorar o desempenho da base de dados do SQL Azure. Assistente da base de dados SQL avalia o desempenho das suas bases de dados do SQL Azure, ao analisar o histórico de utilização. Em seguida, oferece recomendações que melhor se adequam para executar a carga de trabalho normal da base de dados.

> [!NOTE]
> Para obter recomendações, uma base de dados tem de ter sobre uma semana de utilização e, dentro dessa semana deve haver alguma atividade consistente. Assistente da base de dados SQL pode otimizar mais facilmente para padrões de consulta consistente que para extrapolações aleatórias da atividade.

Para obter mais informações sobre o Assistente de base de dados SQL, consulte [Assistente de base de dados SQL](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-app-service-performance-and-reliability"></a>Melhorar o desempenho do serviço de aplicações e a confiabilidade

O Assistente do Azure integra-se as recomendações de melhores práticas para melhorar a sua experiência de serviços de aplicação e detetar recursos de plataforma relevantes. São exemplos de recomendações dos serviços de aplicações:
* Deteção de instâncias em que a memória ou a recursos da CPU são esgotados por tempos de execução de aplicação com as opções de atenuação.
* Deteção de instâncias onde collocating recursos, como as aplicações web e bases de dados pode melhorar o desempenho e reduzir os custos.

Para obter mais informações sobre as recomendações de serviços de aplicações, consulte [melhores práticas para o serviço de aplicações do Azure](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Utilizar Managed Disks para impedir a limitação de e/s de disco

O assistente irá identificar máquinas virtuais que pertencem a uma conta de armazenamento que está a atingir seu destino de escalabilidade. Esta condição torna as VMs suscetíveis a limitação de e/s. O assistente irá recomendar utilizarem os Managed Disks para prevenir a degradação do desempenho.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Melhorar o desempenho e a fiabilidade dos discos da máquina virtual ao utilizar o armazenamento Premium

Advisor identifica as máquinas virtuais com discos standard que tenham um grande volume de transações na sua conta de armazenamento e recomenda a atualização para os discos premium. 

Armazenamento Premium do Azure fornece suporte de discos de elevado desempenho e de baixa latência para máquinas virtuais que executam cargas de trabalho de e/S intensivas. Discos da máquina virtual que utilizem contas de armazenamento premium armazenam dados em unidades de estado sólido (SSDs). Para obter o melhor desempenho para a sua aplicação, recomendamos que tiver migrado qualquer discos de máquinas virtuais que requerem elevado IOPS para o armazenamento premium.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Remover distorção na sua tabela de armazém de dados SQL para melhorar o desempenho de consulta de dados

Distorção de dados pode provocar afunilamentos de movimento ou recurso de dados desnecessários ao executar a sua carga de trabalho. O assistente Deteta os dados de distribuição inclinar superiores a 15% e recomendável redistribuir os seus dados e examine as seleções de chave de distribuição de tabela. Para saber mais sobre identificar e remover distorção, veja [resolução de problemas distorção](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Criar ou atualizar estatísticas de tabela Desatualizadas em sua tabela de armazém de dados SQL para melhorar o desempenho de consulta

Advisor identifica as tabelas que não tenham atualizado [estatísticas de tabela](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) e recomenda criar ou atualizar estatísticas de tabela. Consulta otimizador utiliza ukazatelé atualizado para estimar a cardinalidade ou o número de linhas no resultado da consulta que permite que o otimizador de consultas criar um plano de consulta de alta qualidade para um desempenho mais rápido do armazém de dados do SQL.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Aumentar verticalmente para otimizar a utilização da cache em suas tabelas de SQL Data Warehouse para aumentar o desempenho de consulta

O Assistente do Azure Deteta se o SQL Data Warehouse tem cache elevada utilizada percentagem e percentagem de acessos uma baixa. Esta condição indica expulsão de cache elevada, o que pode afetar o desempenho do seu armazém de dados SQL. O assistente sugere que aumenta verticalmente o SQL Data Warehouse para garantir que alocar capacidade suficiente cache para a sua carga de trabalho.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>Converter tabelas do armazém de dados SQL em tabelas replicadas para melhorar o desempenho de consulta

O assistente identifica as tabelas que não são tabelas replicadas mas beneficiariam com a conversão e sugere que converta essas tabelas. Recomendações baseiam-se no tamanho da tabela replicada, número de colunas, o tipo de distribuição da tabela e o número de partições da tabela SQL Data Warehouse. Heurística adicionais pode ser fornecidas na recomendação para o contexto. Para saber mais sobre como é determinada a esta recomendação, veja [recomendações de armazém de dados SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Migrar a sua conta de armazenamento para o Azure Resource Manager para obter todas as funcionalidades do Azure mais recente

Migre o seu modelo de implementação da conta de armazenamento no Azure Resource Manager (Resource Manager) para tirar partido das implementações de modelo, opções de segurança adicionais e a capacidade de atualizar para uma conta GPv2 para utilização de funcionalidades mais recentes do armazenamento do Azure. O assistente irá identificar quaisquer contas de armazenamento autónomo que estão a utilizar o modelo de implementação clássica e recomenda a migração para o modelo de implementação do Resource Manager.

> [!NOTE]
> Alertas Clássicos no Azure Monitor estão programadas para extinção em Junho de 2019. Recomendamos que Atualize a sua conta de armazenamento clássica para utilizar o Resource Manager para manter a funcionalidade de alerta com a nova plataforma. Para obter mais informações, consulte [clássico de alertas de reforma](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Projetar suas contas de armazenamento para impedir a atingir o limite máximo de subscrição

Uma região do Azure pode suportar um máximo de 250 contas de armazenamento por subscrição. Assim que o limite for atingido, não será possível criar as contas de armazenamento mais nessa combinação de subscrição/região. O assistente irá verificar as suas subscrições e superfície recomendações para a criação de contas de armazenamento menos para qualquer um que estiverem prestes a atingir o limite máximo.

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>Otimizar o desempenho dos seus servidores do Azure MySQL, PostgreSQL do Azure e Azure MariaDB 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>Corrigir a pressão da CPU dos seus servidores do Azure MySQL, PostgreSQL do Azure e Azure MariaDB com afunilamentos de CPU
Muito alta utilização da CPU durante um período prolongado pode fazer com que o desempenho de consulta lenta de sua carga de trabalho. Aumentar o tamanho de CPU irá ajudar a otimizar o tempo de execução das consultas da base de dados e melhorar o desempenho geral. O Assistente do Azure irá identificar servidores com uma alta utilização da CPU que são provavelmente a executar cargas de trabalho de CPU restrita e recomendar a dimensionar a computação.

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Reduza as restrições de memória nos seus servidores do Azure MySQL, PostgreSQL do Azure e Azure MariaDB ou mudança para uma memória otimizada SKU
Uma taxa de acertos de cache baixos pode resultar em maior de IOPS e de desempenho de consulta mais lento. Isto pode dever-se um plano de consulta incorreta ou a executar uma carga de trabalho intensivas em termos de memória. Corrigir o plano de consulta ou [aumentar a memória](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers) da base de dados do Azure para servidor de base de dados do PostgreSQL, o servidor de base de dados MySQL do Azure ou Azure MariaDB server ajudarão a otimizar a execução da carga de trabalho da base de dados. O Assistente do Azure identifica servidores afetados devido a estas alterações de conjunto de memória intermédia de alta e recomenda a corrigir o plano de consulta, mover para um SKU superior com mais memória ou a aumentar o tamanho de armazenamento para obter mais IOPS.

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Utilize um MySQL do Azure ou a réplica de leitura do PostgreSQL do Azure para aumentar horizontalmente leituras para cargas de trabalho de leitura intensivas
O Assistente do Azure tira partido da heurística com base na carga de trabalho, como a proporção de leituras por oposição a escritas no servidor nos últimos sete dias para identificar cargas de trabalho de leitura intensiva. Sua base de dados do Azure para PostgreSQL recursos ou a base de dados do Azure para MySQL recursos com um rácio de leitura/escrita muito alto pode resultar em contenções de CPU e/ou de memória que leva a diminuir o desempenho de consulta. Adicionar uma [réplica](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) ajudará no aumento horizontal de leituras para o servidor de réplica, impedindo que as restrições de memória e/ou da CPU no servidor primário. O assistente irá identificar os servidores com essas cargas de trabalho de leitura intensiva elevadas e recomendamos adicionar um [ler réplica](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) para descarregar algumas das cargas de trabalho de leitura.


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Dimensionar o seu servidor Azure MySQL, PostgreSQL do Azure ou Azure MariaDB para um SKU superior para impedir que as restrições de ligação
Cada nova ligação ao seu servidor de base de dados ocupa alguma memória. Degrada o desempenho do servidor de base de dados se ligações ao seu servidor estão a falhar devido uma [limite superior](https://docs.microsoft.com/azure/postgresql/concepts-limits) na memória. O Assistente do Azure irá identificar os servidores em execução com muitas falhas de ligação e recomendável fazer a atualização de limites de ligações do seu servidor para fornecer mais memória para o servidor ao aumentar verticalmente a computação ou utilizar a memória otimizada SKUs, que tem mais computação por núcleo.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Dimensionar a sua Cache para um tamanho diferente ou SKU para melhorar a Cache e o desempenho da aplicação

Instâncias de cache têm um melhor desempenho quando não está em execução sob pressão de memória elevada, carga do servidor de elevada ou largura de banda de rede de alta que pode fazer com que eles deixar de responder, a experiência de perda de dados ou fique indisponível. O assistente irá identificar instâncias de Cache nestas condições e recomendamos a aplicar as práticas recomendadas para reduzir a pressão de memória, a carga do servidor ou a largura de banda de rede ou a dimensionar para um tamanho diferente ou um SKU com mais capacidade.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Adicionar regiões com o tráfego para a sua conta do Azure Cosmos DB

O assistente irá detetar as contas do Azure Cosmos DB que existe tráfego a partir de uma região que não esteja atualmente configurada e recomendamos adicionar essa região. Isto irá melhorar a latência para pedidos provenientes de nessa região e irá garantir a disponibilidade em caso de falhas de região. [Saiba mais sobre a distribuição de dados global com o Azure Cosmos DB](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>Configurar o Azure Cosmos DB política de indexação com o cliente incluídos ou excluídos caminhos

O Assistente do Azure identificará os contentores do Cosmos DB que estiver a utilizar a política de indexação predefinida, mas poderiam se beneficiar de uma política de indexação personalizada com base no padrão de carga de trabalho. A predefinição de política de indexação indexa todas as propriedades, mas utilizando uma política de indexação personalizada com caminhos de excluídas ou incluídos explícitos utilizados em filtros de consulta pode reduzir o RUs e armazenamento consumidos para indexação. [Saiba mais sobre como modificar as políticas de índice](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Configurar o tamanho de página de consulta (MaxItemCount) de Azure Cosmos DB como -1 

O Assistente do Azure identificará os contentores do Azure Cosmos DB que estiver a utilizar o tamanho da página de consulta de 100 e recomendada a utilização de um tamanho de página de -1 para análises mais rápidos. [Saiba mais sobre o número de itens de máx.](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Como acessar as recomendações de desempenho no Advisor

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e, em seguida, abra [Advisor](https://aka.ms/azureadvisordashboard).

2.  No dashboard do assistente, clique nas **desempenho** separador.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as recomendações do assistente, consulte:

* [Introdução ao Advisor](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de custos do Assistente](advisor-performance-recommendations.md)
* [Recomendações de elevada disponibilidade do Assistente](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
