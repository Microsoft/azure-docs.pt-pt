---
title: Continuidade do negócio Azure HDInsight
description: Este artigo apresenta uma visão geral das melhores práticas, disponibilidade de uma região única e opções de otimização para o planeamento de continuidade de negócios Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: hadoop alta disponibilidade
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: f83f5aec264aeae1a729e81932843825a0ce6673
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546947"
---
# <a name="azure-hdinsight-business-continuity"></a>Continuidade do negócio Azure HDInsight

Os clusters Azure HDInsight dependem de muitos serviços Azure, como armazenamento, bases de dados, Diretório Ativo, Serviços de Domínio de Diretório Ativo, networking e Key Vault. Uma aplicação de análise bem concebida, altamente disponível e tolerante a falhas deve ser concebida com redundância suficiente para resistir a perturbações regionais ou locais num ou mais destes serviços. Este artigo apresenta uma visão geral das melhores práticas, disponibilidade de uma região única e opções de otimização para o planeamento da continuidade do negócio.

## <a name="general-best-practices"></a>Melhores práticas gerais

Esta secção discute algumas boas práticas para você considerar durante o planeamento de continuidade do negócio.

* Determine a funcionalidade mínima de negócio que necessitará se houver um desastre e porquê. Por exemplo, avalie se precisa de capacidades de failover para a camada de transformação de dados (mostrada em amarelo) *e* a camada de serviço de dados (mostrada em azul), ou se apenas precisa de falha para a camada de serviço de dados.

   :::image type="content" source="media/hdinsight-business-continuity/data-layers.png" alt-text="transformação de dados e camadas de serviço de dados":::

* Segmentar os seus clusters com base na carga de trabalho, ciclo de vida de desenvolvimento e departamentos. Ter mais clusters reduz as chances de uma única grande falha que afeta vários processos de negócio diferentes.

* Faça as suas regiões secundárias lerem apenas. Regiões de failover com capacidades de leitura e escrita podem levar a arquiteturas complexas.

* Os aglomerados transitórios são mais fáceis de gerir quando há um desastre. Desenhe as suas cargas de trabalho de forma a que os clusters possam ser ciclizados e nenhum estado seja mantido em aglomerados.

* Muitas vezes, as cargas de trabalho ficam inacabadas se houver uma catástrofe e precisam de ser reiniciadas na nova região. Desenhe as suas cargas de trabalho para ser idempotente na natureza.

* Utilize a automatização durante as implementações do cluster e garanta que as definições de configuração do cluster são escritas na medida do possível para garantir uma implementação rápida e totalmente automatizada em caso de desastre.

* Utilize ferramentas de monitorização Azure em HDInsight para detetar comportamentos anormais no cluster e definir notificações de alerta correspondentes. Pode implementar as soluções de gestão específicas do cluster HDInsight pré-configuradas que recolhem métricas de desempenho importantes do tipo de cluster específico. Para obter mais informações, consulte [a Azure Monitoring for HDInsight](./hdinsight-hadoop-oms-log-analytics-tutorial.md).  

* Subscreva os alertas de saúde da Azure para ser notificado sobre questões de serviço, manutenção planeada, avisos de saúde e segurança para uma subscrição, serviço ou região. Notificações de saúde que incluem a causa do problema e ETA resoluta ajudam-no a executar melhor o failover e os failbacks. Para mais informações, consulte [a documentação do Serviço Azure Health.](../service-health/index.yml)

## <a name="single-region-availability"></a>Disponibilidade de uma região única

Um sistema HDInsight básico tem os seguintes componentes. Todos os componentes têm os seus próprios mecanismos de tolerância à falha na região única.

* Compute (máquinas virtuais): Cluster Azure HDInsight
* Metastore(s): Base de Dados Azure SQL
* Armazenamento: Azure Data Lake Gen2 ou Blob
* Autenticação: Azure Ative Directory, Azure Ative Directory Domain Services, Pacote de Segurança Empresarial
* Resolução do nome de domínio: Azure DNS

Existem outros serviços opcionais que podem ser usados, como a Azure Key Vault e a Azure Data Factory.

:::image type="content" source="media/hdinsight-business-continuity/hdinsight-components.png" alt-text="transformação de dados e camadas de serviço de dados":::

### <a name="azure-hdinsight-cluster-compute"></a>Cluster Azure HDInsight (computação)

A HDInsight oferece um SLA disponível de 99,9%. Para fornecer alta disponibilidade numa única implementação, o HDInsight é acompanhado por muitos serviços que estão em modo de alta disponibilidade por padrão. Os mecanismos de tolerância a falhas em HDInsight são fornecidos pelos serviços de alta disponibilidade do ecossistema Microsoft e Apache OSS.

Os seguintes serviços destinam-se a estar altamente disponíveis:

#### <a name="infrastructure"></a>Infraestrutura

* Headnodes ativos e de standby
* Múltiplos nóns de gateway
* Três nódoas de Quorum zookeeper
* Nó de trabalhador distribuídos por domínios de falha e atualização

#### <a name="service"></a>Serviço

* Servidor Apache Ambari
* Severidades da linha do tempo da aplicação para YARN
* Servidor de Histórico de Trabalho para Hadoop MapReduce
* Apache Livy
* HDFS
* Gestor de Recursos YARN
* HBase Master

Consulte a documentação sobre [serviços de alta disponibilidade apoiados pela Azure HDInsight](./hdinsight-high-availability-components.md) para saber mais.

Nem sempre é preciso um evento catastrófico para impactar a funcionalidade do negócio. Incidentes de serviço em um ou mais dos seguintes serviços numa única região também podem levar à perda da funcionalidade empresarial esperada.

### <a name="hdinsight-metastore"></a>Metásta de HDInsight

A HDInsight utiliza [a Base de Dados Azure SQL](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/) como uma meta-loja, que fornece um SLA de 99,99%. Três réplicas de dados persistem dentro de um centro de dados com replicação sincronizada. Se houver uma perda de réplica, uma réplica alternativa é servida sem problemas. [A geo-replicação ativa](../azure-sql/database/active-geo-replication-overview.md) é suportada fora da caixa com um máximo de quatro centros de dados. Quando há um failover, manual ou data center, a primeira réplica na hierarquia tornar-se-á automaticamente capaz de ler-escrever. Para mais informações, consulte [a continuidade do negócio da Base de Dados Azure SQL.](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md)

### <a name="hdinsight-storage"></a>Armazenamento HDInsight

O HDInsight recomenda o Azure Data Lake Storage Gen2 como camada de armazenamento subjacente. [O Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_5/), incluindo o Azure Data Lake Storage Gen2, fornece um SLA de 99,9%. O HDInsight utiliza o serviço LRS no qual persistem três réplicas de dados dentro de um centro de dados, e a replicação é sincronizada. Quando há uma perda de réplica, uma réplica é servida sem problemas.

### <a name="azure-active-directory"></a>Azure Active Directory

[O Azure Ative Directory](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/) fornece um SLA de 99,9%. Ative Directory é um serviço global com múltiplos níveis de redundância interna e recuperabilidade automática. Para obter mais informações, veja como [a Microsoft melhora continuamente a fiabilidade do Azure Ative Directory](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/).

### <a name="azure-active-directory-domain-services-ad-ds"></a>Serviços de Domínio do Diretório Ativo Azure (DS AD)

[A Azure Ative Directory Domain Services](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) fornece um SLA de 99,9%. O Azure AD DS é um serviço altamente disponível hospedado em centros de dados distribuídos globalmente. Os conjuntos de réplicas são uma funcionalidade de pré-visualização em Azure AD DS que permite a recuperação de desastres geográficos se uma região do Azure ficar offline. Para obter mais informações, consulte [os conceitos e funcionalidades de conjuntos de réplicas para a Azure Ative Directory Domain Services](../active-directory-domain-services/concepts-replica-sets.md) para saber mais.  

### <a name="azure-dns"></a>DNS do Azure

[O Azure DNS](https://azure.microsoft.com/support/legal/sla/dns/v1_1/) fornece um SLA de 100%. HDInsight usa DNS Azure em vários locais para resolução de nome de domínio.

## <a name="multi-region-cost-and-complexity-optimizations"></a>Otimizações de custos e complexidades multi-regiões

Melhorar a continuidade das empresas utilizando a recuperação de desastres de alta disponibilidade da região transversal requer desenhos arquitetónicos de maior complexidade e custos mais elevados. Os quadros seguintes detalham algumas áreas técnicas que podem aumentar o custo total de propriedade.

### <a name="cost-optimizations"></a>Otimizações de custos

|Área|Causa da escalada de custos|Estratégias de otimização|
|----|------------------------|-----------------------|
|Armazenamento de Dados|Duplicação de dados/tabelas primárias numa região secundária|Replicar apenas dados curados|
|Data Egress|As transferências de dados da região transversal de saída têm um preço. Rever as diretrizes de preços da largura de banda|Replicar apenas dados curados para reduzir a pegada de saída da região|
|Cálculo do Cluster|Cluster/s hdinsight adicional na região secundária|Utilize scripts automatizados para implementar o cálculo secundário após a falha primária. Utilize autoscaling para manter o tamanho do cluster secundário no mínimo. Use SKUs VM mais baratos. Criar secundários em regiões onde os SKUs VM podem ser descontados.|
|Autenticação |Cenários multiuser na região secundária incorrerão em configurações adicionais de Azure AD DS|Evite configurações multiusais na região secundária.|

### <a name="complexity-optimizations"></a>Otimizações de complexidade

|Área|Causa de escalada de complexidade|Estratégias de otimização|
|----|------------------------|-----------------------|
|Leia padrões de escrita |Exigindo que tanto o primário como o secundário sejam lidos e escritos habilitados |Desenhe o secundário para ser lido apenas|
|Zero RPO & RTO |Que requer perda de dados zero (RPO=0) e tempo de inatividade zero (RTO=0) |Desenhe RPO e RTO de forma a reduzir o número de componentes que precisam de falhar.|
|Funcionalidade do negócio |Exigindo a funcionalidade de negócio completa do primário no secundário |Avalie se pode executar com o subconjunto crítico mínimo da funcionalidade do negócio no secundário.|
|Conectividade |Que requer todos os sistemas a montante e a jusante do primário para ligar ao secundário também|Limite a conectividade secundária a um subconjunto crítico mínimo.|

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os itens discutidos neste artigo, consulte:

* [Azure HDInsight arquiteturas de continuidade de negócios](./hdinsight-business-continuity-architecture.md)
* [Azure HDInsight estudo de caso de arquitetura de solução altamente disponível](./hdinsight-high-availability-case-study.md)
* [O que é Apache Hive e HiveQL em Azure HDInsight?](./hadoop/hdinsight-use-hive.md)