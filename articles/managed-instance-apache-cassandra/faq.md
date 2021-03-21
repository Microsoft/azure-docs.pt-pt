---
title: Perguntas frequentes sobre Azure Managed Instance para Apache Cassandra do portal Azure
description: Frequentemente fez perguntas sobre Azure Managed Instance para Apache Cassandra. Este artigo aborda questões sobre quando utilizar casos geridos, benefícios, limites de produção, regiões apoiadas e outros detalhes de configuração.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 1ba2b7d648c86912118b83a566bf2eb0800baee2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101748954"
---
# <a name="frequently-asked-questions-about-azure-managed-instance-for-apache-cassandra-preview"></a>Perguntas frequentes sobre Azure Managed Instance for Apache Cassandra (Preview)

Este artigo aborda perguntas frequentes sobre Azure Managed Instance for Apache Cassandra. Você aprenderá quando usar casos geridos, seus benefícios, limites de produção, regiões apoiadas, e seus detalhes de configuração.

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="general-faq"></a>FAQs Gerais

### <a name="what-are-the-benefits-azure-managed-instance-for-apache-cassandra"></a>Quais são os benefícios Azure Managed Instance para Apache Cassandra?

A base de dados Apache Cassandra é a escolha certa quando precisa de escalabilidade e alta disponibilidade sem comprometer o desempenho. É uma excelente plataforma para dados críticos da missão devido à escalabilidade linear e à comprovada tolerância a falhas em hardware de mercadoria ou infraestrutura em nuvem. Azure Managed Instance for Apache Cassandra é um serviço para gerir casos de centros de dados Apache Cassandra de código aberto implantados em Azure.

Pode ser usado inteiramente na nuvem ou como parte de uma nuvem híbrida e aglomerado no local. Este serviço é uma ótima escolha quando você quer configuração e controlo de grãos finos que você tem em Apache Cassandra de código aberto, sem qualquer sobrecarga de manutenção.

### <a name="why-should-i-use-this-service-instead-of-azure-cosmos-db-cassandra-api"></a>Por que devo usar este serviço em vez de Azure Cosmos DB Cassandra API?

Azure Managed Instance for Apache Cassandra é entregue pela equipa DB do Azure Cosmos. É um serviço gerido autónomo para implantação, manutenção e escala de centros de dados Apache Cassandra de código aberto e clusters. [A Azure Cosmos DB Cassandra API,](../cosmos-db/cassandra-introduction.md) por outro lado, é uma Plataforma-as-a-Service, fornecendo uma camada de interoperabilidade para o protocolo de arame Apache Cassandra. Se a sua expectativa é que a plataforma se comporte exatamente da mesma forma que qualquer cluster Apache Cassandra, deve escolher o serviço de instância gerido. Para saber mais, consulte o artigo [Azure Managed Instance for Apache Cassandra Vs Azure Cosmos DB Cassandra API.](compare-cosmosdb-managed-instance.md)

### <a name="is-azure-managed-instance-for-apache-cassandra-dependent-on-azure-cosmos-db"></a>A Azure Managed Instance for Apache Cassandra depende da Azure Cosmos DB?

Não, não há dependência arquitetónica entre Azure Managed Instance para Apache Cassandra e o backend Azure Cosmos DB. 

#### <a name="can-i-deploy-azure-managed-instance-for-apache-cassandra-in-any-region"></a>Posso colocar a Azure Managed Instance para Apache Cassandra em qualquer região?

O caso gerido estará disponível num número limitado de regiões durante a pré-visualização.

### <a name="what-are-the-storage-and-throughput-limits-of-azure-managed-instance-for-apache-cassandra"></a>Quais são os limites de armazenamento e produção de Azure Managed Instance para Apache Cassandra?

Estes limites dependem das SKUs da Máquina Virtual que escolher.

### <a name="what-is-the-cost-of-azure-managed-instance-for-apache-cassandra"></a>Qual é o custo de Azure Managed Instance para Apache Cassandra?

Os encargos com instâncias geridas baseiam-se no custo de VM subjacente, com uma pequena margem de lucro. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/managed-instance-apache-cassandra/).

### <a name="can-i-use-yaml-file-settings-to-configure-behavior"></a>Posso usar as definições de ficheiro YAML para configurar o comportamento?

Sim, pode incorporar configurações de ficheiros YAML como parte de uma implementação do modelo do Azure Resource Manager.

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>Como posso monitorizar as infraestruturas juntamente com a produção?

O servidor [Prometheus](https://prometheus.io/docs/introduction/overview/) está hospedado para monitorizar a atividade em todo o seu cluster, e expõe um ponto final. Isto mantém 10 minutos ou 10 GB de dados (qualquer que seja o limiar que atinge primeiro). Para utilizar esta monitorização, é necessário criar uma [federação](https://prometheus.io/docs/prometheus/latest/federation/) e uma ferramenta de painel apropriada, como a Grafana.

### <a name="does-azure-managed-instance-for-apache-cassandra-provide-full-backups"></a>A Azure Managed Instance for Apache Cassandra fornece cópias de segurança completas?

Sim, fornece cópias de segurança completas para o Azure Storage e restaura para um novo cluster

### <a name="how-can-i-migrate-data-from-my-existing-apache-cassandra-cluster-to-azure-managed-instance-for-apache-cassandra"></a>Como posso migrar dados do meu aglomerado apache cassandra existente para Azure Managed Instance para Apache Cassandra?

Azure Managed Instance for Apache Cassandra suporta todas as funcionalidades em Apache Cassandra para replicar e transmitir dados entre centros de dados.

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-azure-managed-instance-for-apache-cassandra"></a>Posso emparelhar um aglomerado Apache Cassandra no local com o Azure Managed Instance para Apache Cassandra?

Sim, pode configurar um cluster híbrido com centros de dados injetados pela Rede Virtual Azure implantados pelo serviço. Os centros de dados de instância gerida podem comunicar com centros de dados no local dentro do mesmo anel de cluster.

### <a name="where-can-i-give-feedback-on-azure-managed-instance-for-apache-cassandra-features"></a>Onde posso dar feedback sobre a Azure Managed Instance para as características de Apache Cassandra?

Fornecer feedback através do [feedback de voz](https://feedback.azure.com/forums/263030-azure-cosmos-db?category_id=398548) do utilizador utilizando a categoria "Managed Apache Cassandra".

Para corrigir um problema na sua conta, crie um [pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.

## <a name="deployment-specific-faq"></a>FAQ específico de implantação

### <a name="will-the-managed-instance-support-node-addition-cluster-status-and-node-status-commands"></a>Os comandos de nó de apoio, o estado do aglomerado e o estado do nó geridos?

Todos os comandos Nodetool *apenas de leitura,* tais como `status` estão disponíveis através do Azure CLI. No entanto, não existem operações como a *adição de nó,* porque gerimos a saúde dos nós no caso gerido. No modo Híbrido, pode ligar-se ao cluster com o *Nodetool*. No entanto, não é recomendada a utilização de Nodetool, uma vez que poderia desestabilizar o cluster. Pode igualmente invalidar qualquer apoio à produção SLA relativo à saúde dos centros de dados de instâncias geridos no cluster.

### <a name="what-happens-with-various-settings-for-table-metadata"></a>O que acontece com várias definições para metadados de mesa?

As definições para metadados de mesa, tais como filtro de flores, caching, leitura de chance de reparação, gc_grace e memtable_flush_period de compressão são totalmente suportadas como em qualquer ambiente Apache Cassandra auto-hospedado.

## <a name="next-steps"></a>Passos seguintes

Para aprender sobre perguntas frequentes noutras APIs, consulte:

* [Criar um cluster de instâncias gerido a partir do portal Azure](create-cluster-portal.md)
* [Implementar um Cluster de Faíscas Apache Gerido com Tijolos de Dados Azure](deploy-cluster-databricks.md)
* [Gerir a Azure Managed Instance para os recursos da Apache Cassandra utilizando o Azure CLI](manage-resources-cli.md)