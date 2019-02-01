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
ms.openlocfilehash: f3c6e39203fb0d864ecf952e0468959d66931e1f
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55491591"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Melhorar o desempenho de aplicações do Azure com o Assistente do Azure

Recomendações de desempenho do Assistente do Azure ajudam a melhorar a velocidade e a capacidade de resposta das suas aplicações críticas para a empresa. Pode obter recomendações de desempenho do Advisor o **desempenho** separador do dashboard do Advisor.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Reduzir o tempo DNS ao vivo no seu perfil do Gestor de tráfego para a ativação pós-falha para pontos finais de bom estado de funcionamento mais rapidamente

[Tempo para as definições de Live (TTL)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) no perfil do Traffic Manager permitem-lhe especificar como rapidamente mudar pontos de extremidade, se um determinado ponto de final deixa de responder às consultas. Reduzir os valores TTL significa que os clientes serão encaminhados para pontos finais de funcionamento mais rápidos.

O Azure Advisor identifica perfis do Gestor de tráfego com um valor de TTL mais configurado e recomenda configurar o valor de TTL para 20 segundos ou 60 segundos, dependendo se o perfil está configurado para [rápida ativação pós-falha](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Melhore o desempenho da base de dados com o Assistente da BD SQL

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
