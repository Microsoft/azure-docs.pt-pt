---
title: Azure HDInsight arquiteturas de continuidade de negócios
description: Este artigo discute as diferentes arquiteturas possíveis de continuidade de negócios para a HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: hadoop alta disponibilidade
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: cb3ef1e802546d5a8b1574b304770fe7a364e2df
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91844081"
---
# <a name="azure-hdinsight-business-continuity-architectures"></a>Azure HDInsight arquiteturas de continuidade de negócios

Este artigo dá alguns exemplos de arquiteturas de continuidade de negócios que você pode considerar para Azure HDInsight. A tolerância para uma funcionalidade reduzida durante um desastre é uma decisão de negócio que varia de uma aplicação para outra. Pode ser aceitável que algumas aplicações não estejam disponíveis ou estejam parcialmente disponíveis com funcionalidade reduzida ou processamento atrasado por um período. Para outras aplicações, qualquer funcionalidade reduzida pode ser inaceitável.

> [!NOTE]
> As arquiteturas apresentadas neste artigo não são de forma alguma exaustivas. Você deve projetar suas próprias arquiteturas únicas uma vez que você fez determinações objetivas em torno da continuidade esperada do negócio, complexidade operacional e custo de propriedade.

## <a name="apache-hive-and-interactive-query"></a>Colmeia Apache e Consulta Interativa

[A replicação da colmeia V2](https://cwiki.apache.org/confluence/display/Hive/HiveReplicationv2Development#HiveReplicationv2Development-REPLSTATUS) é recomendada para a continuidade do negócio em aglomerados de consultas HDInsight e Interactive. As secções persistentes de um cluster de colmeia autónomo que precisam de ser replicadas são a Camada de Armazenamento e a metástase da Colmeia. Os clusters de colmeias num cenário multiutilizador com o Pacote de Segurança Empresarial precisam de Serviços de Domínio de Diretório Ativo Azure e Metastore Ranger.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hive-interactive-query.png" alt-text="Colmeia e arquitetura de consulta interativa":::

A replicação baseada em eventos de colmeia está configurada entre os aglomerados primários e secundários. Este é composto por duas fases distintas, bootstrapping e execuções incrementais:

* O bootstrapping replica todo o armazém da Colmeia, incluindo a informação da metástaia da Colmeia do primário ao secundário.

* As execuções incrementais são automatizadas no cluster primário e os eventos gerados durante as execuções incrementais são reproduzidos no cluster secundário. O cluster secundário alcança os eventos gerados a partir do cluster primário, garantindo que o cluster secundário é consistente com os eventos do cluster primário após a replicação.

O cluster secundário só é necessário no momento da replicação para executar a cópia distribuída, `DistCp` mas o armazenamento e as metastores precisam de ser persistentes. Pode optar por girar um cluster secundário scripted on demand antes da replicação, executar o script de replicação nele e, em seguida, derrubá-lo após uma replicação bem sucedida.

O aglomerado secundário é geralmente apenas de leitura. Pode fazer a leitura do cluster secundário, mas isto adiciona complexidade adicional que envolve replicar as mudanças do cluster secundário para o cluster primário.

### <a name="hive-event-based-replication-rpo--rto"></a>Replicação baseada em eventos de Hive RPO & RTO

* RPO: A perda de dados está limitada ao último evento de replicação incremental bem sucedido do primário ao secundário.

* RTO: O tempo entre a falha e o reinício das transações a montante e a jusante com o secundário.

### <a name="apache-hive-and-interactive-query-architectures"></a>Apache Hive e arquiteturas de consulta interativa

#### <a name="hive-active-primary-with-on-demand-secondary"></a>Colmeia ativa primária com secundário a pedido

Numa primária ativa com arquitetura *secundária a pedido,* as aplicações escrevem para a região primária ativa, enquanto não é atado nenhum cluster na região secundária durante as operações normais. A Metastaria e armazenamento SQL na região secundária são persistentes, enquanto o cluster HDInsight é scriptado e implantado a pedido apenas antes da replicação programada da Colmeia.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-on-demand-secondary.png" alt-text="Colmeia e arquitetura de consulta interativa":::

#### <a name="hive-active-primary-with-standby-secondary"></a>Colmeia ativa primária com standby secundário

Numa *primária ativa com suporte secundário,* as aplicações escrevem para a região primária ativa, enquanto um aglomerado secundário de espera diminuiu em modo de leitura apenas durante as operações normais. Durante as operações normais, pode optar por descarregar operações de leitura específicas da região para secundárias.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-standby-secondary.png" alt-text="Colmeia e arquitetura de consulta interativa":::

## <a name="apache-spark"></a>Apache Spark

As cargas de trabalho de faísca podem ou não envolver um componente da Colmeia. Para permitir que as cargas de trabalho do Spark SQL leiam e escrevam dados da Hive, os clusters HDInsight Spark partilham as metastões personalizadas da Hive a partir de clusters de consultas Hive/Interactive na mesma região. Nesses cenários, a replicação transversal das cargas de trabalho de Spark deve também acompanhar a replicação das metasteiros e armazenamento da Colmeia. Os cenários de failover nesta secção aplicam-se a ambos:

* [Spark SQL em tabelas ACID usando a configuração do Conector do Armazém de Colmeia (HWC)](./interactive-query/apache-hive-warehouse-connector.md) utilizando um cluster de consulta interativa HDInsight.
* Spark SQL carga de trabalho em tabelas não-ACID usando um cluster HDInsight Hadoop.

Para cenários em que a Spark trabalha em modo autónomo, os dados curados e os frascos de faíscas armazenados (para trabalhos livy) precisam de ser replicados da região primária para a região secundária regularmente utilizando a Azure Data `DistCP` Factory.

Recomendamos que utilize sistemas de controlo de versões para armazenar cadernos e bibliotecas Spark onde possam ser facilmente implantados em agrupamentos primários ou secundários. Certifique-se de que as soluções baseadas em cadernos e não portátil estão preparadas para carregar os suportes de dados corretos no espaço de trabalho primário ou secundário.

Se existirem bibliotecas específicas do cliente que estão para além do que o HDInsight fornece de forma nativa, devem ser rastreadas e carregadas periodicamente no cluster secundário de espera.  

### <a name="apache-spark-replication-rpo--rto"></a>Réplica apache Spark RPO & RTO

* RPO: A perda de dados está limitada à última replicação incremental bem sucedida (Faísca e Colmeia) do primário ao secundário.

* RTO: O tempo entre a falha e o reinício das transações a montante e a jusante com o secundário.

### <a name="apache-spark-architectures"></a>Arquiteturas Apache Spark

#### <a name="spark-active-primary-with-on-demand-secondary"></a>Faísca primária ativa com secundário a pedido

As aplicações lêem e escrevem para os Agrupamentos de Faíscas e Colmeias na região primária, enquanto não são a provisionados aglomerados na região secundária durante as operações normais. SqL Metastore, Hive Storage e Spark Storage são persistentes na região secundária. Os aglomerados spark e Hive são scriptados e implantados a pedido. A replicação da colmeia é usada para replicar o armazenamento de colmeias e as metástases de colmeia, enquanto a Azure Data Factory `DistCP` pode ser usada para copiar o armazenamento autónomo de Faíscas. Os aglomerados de colmeias precisam de ser implantados antes que cada replicação da Colmeia corra por causa da computação de `DistCp` dependência.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-on-demand-secondary-spark.png" alt-text="Colmeia e arquitetura de consulta interativa":::

#### <a name="spark-active-primary-with-standby-secondary"></a>Faísca primária ativa com standby secundário

As aplicações lêem e escrevem para os agrupamentos spark e colmeia na região primária, enquanto os agrupamentos de colmeia e faísca escalaram em modo de leitura apenas executados na região secundária durante as operações normais. Durante as operações normais, pode optar por descarregar as operações específicas da Hive e da Spark para o secundário.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-standby-secondary-spark.png" alt-text="Colmeia e arquitetura de consulta interativa":::

## <a name="apache-hbase"></a>Apache HBase

A HBase Export e a HBase Replication são formas comuns de permitir a continuidade do negócio entre clusters HBase HDInsight.

HBase Export é um processo de replicação de lote que usa o Utilitário de Exportação HBase para exportar tabelas do cluster HBase primário para o seu armazenamento subjacente Azure Data Lake Storage Gen 2. Os dados exportados podem então ser acedidos a partir do cluster HBase secundário e importados em tabelas que devem ser pré-visualizadas no secundário. Enquanto a HBase Export oferece granularidade de nível de mesa, em situações de atualização incremental, o motor de automação de exportação controla a gama de linhas incrementais a incluir em cada execução. Para obter mais informações, consulte [a cópia de segurança e replicação hbase hdinsight](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-backup-replication#export-then-import).

A replicação da HBase utiliza a replicação em tempo real entre os clusters HBase de forma totalmente automatizada. A replicação é feita ao nível da mesa. Ou todas as tabelas ou tabelas específicas podem ser direcionadas para a replicação. A replicação da HBase é eventualmente consistente, o que significa que as edições recentes para uma mesa na região primária podem não estar disponíveis para todos os secundários imediatamente. Os secundários são garantidos para eventualmente se tornarem consistentes com as primárias. A replicação hbase pode ser configurada entre dois ou mais clusters HDInsight HBase se:

* Primária e secundária estão na mesma rede virtual.
* Primária e secundária estão em VNets diferentes na mesma região.
* Primária e secundária estão em diferentes VNets espreitadas em diferentes regiões.

Para obter mais informações, consulte [configurar a replicação do cluster Apache HBase nas redes virtuais Azure](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-replication).

Existem algumas outras formas de realizar backups de clusters HBase como [copiar a pasta hbase,](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-backup-replication#copy-the-hbase-folder) [copiar tabelas](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-backup-replication#copy-tables) e [Snapshots](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-backup-replication#snapshots).

### <a name="hbase-rpo--rto"></a>HBase RPO & RTO

#### <a name="hbase-export"></a>Exportação HBase

* RPO: A perda de dados está limitada à última importação incremental de lote bem sucedido pelo secundário a partir do primário.
* RTO: O tempo entre a falha das operações primárias e o reinício das operações de I/O no secundário.

#### <a name="hbase-replication"></a>Replicação HBase

* RPO: A perda de dados está limitada ao último Envio WalEdit recebido no secundário.
* RTO: O tempo entre a falha das operações primárias e o reinício das operações de I/O no secundário.

### <a name="hbase-architectures"></a>Arquiteturas HBase

A replicação HBase pode ser configurada em três modos: Leader-Follower, Leader-Leader e Cíclico.

#### <a name="hbase-replication--leader--follower-model"></a>Replicação HBase: Leader – Modelo de seguidor

Nesta região inter-região criada, a replicação é unidirecional da região primária para a região secundária. Todas as tabelas ou tabelas específicas na primária podem ser identificadas para replicação unidirecional. Durante as operações normais, o cluster secundário pode ser usado para servir pedidos de leitura na sua própria região.

O cluster secundário funciona como um cluster HBase normal que pode hospedar as suas próprias mesas e pode servir leituras e escritos de aplicações regionais. No entanto, as escritas nas tabelas replicadas ou nas tabelas nativas a secundárias não são replicadas de volta às primárias.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-leader-follower.png" alt-text="Colmeia e arquitetura de consulta interativa":::

#### <a name="hbase-replication--leader--leader-model"></a>Replicação HBase: Leader – Modelo líder

Esta região inter-região é muito semelhante à configuração unidirecional, exceto que a replicação ocorre bidirecionalmente entre a região primária e a região secundária. As aplicações podem utilizar ambos os clusters em modos de leitura e atualizações são trocas assíncroneas entre eles.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-leader-leader.png" alt-text="Colmeia e arquitetura de consulta interativa":::

#### <a name="hbase-replication-multi-region-or-cyclic"></a>Replicação HBase: Multi-Região ou Cíclica

O modelo de replicação multi-região/cíclica é uma extensão da replicação HBase e poderia ser usado para criar uma arquitetura HBase globalmente redundante com múltiplas aplicações que lêem e escrevem para agrupamentos específicos de HBase da região. Os clusters podem ser configurado em várias combinações de Leader/Leader ou Leader/Follower, dependendo dos requisitos do negócio.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-cyclic.png" alt-text="Colmeia e arquitetura de consulta interativa":::

## <a name="apache-kafka"></a>Apache Kafka

Para permitir a disponibilidade de região cruzada HDInsight 4.0 suporta Kafka MirrorMaker que pode ser usado para manter uma réplica secundária do cluster primário kafka em uma região diferente. O MirrorMaker funciona como um par de produtores de consumo de alto nível, consome a partir de um tópico específico no cluster primário e produz para um tópico com o mesmo nome no secundário. A replicação do cluster transversal para a recuperação de desastres de alta disponibilidade utilizando o MirrorMaker vem com o pressuposto de que os produtores e consumidores precisam de falhar no cluster de réplicas. Para obter mais informações, consulte [Use MirrorMaker para replicar tópicos Apache Kafka com Kafka em HDInsight](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-mirroring)

Dependendo da vida útil do tópico quando a replicação começou, a replicação do tópico MirrorMaker pode levar a diferentes compensações entre tópicos de origem e réplica. Os clusters HDInsight Kafka também suportam a replicação da partição de tópicos, que é uma característica de alta disponibilidade a nível de cluster individual.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-replication.png" alt-text="Colmeia e arquitetura de consulta interativa":::

### <a name="apache-kafka-architectures"></a>Arquiteturas Apache Kafka

#### <a name="kafka-replication-active--passive"></a>Replicação kafka: Ativo - Passivo

Active-Passive configuração permite espelhamento assíncronos unidirecional de Ative a Passivo. Os produtores e os consumidores têm de estar conscientes da existência de um cluster ativo e passivo e devem estar prontos a chumbar no Passivo no caso de o Ativo falhar. Abaixo estão algumas vantagens e desvantagens de Active-Passive configuração.

Vantagens:

* A latência da rede entre clusters não afeta o desempenho do cluster Ativo.
* Simplicidade da replicação unidirecional.

Desvantagens:

* O aglomerado passivo pode permanecer subutilizado.
* Complexidade do design na incorporação de falta de consciência nos produtores e consumidores de aplicações.
* Possível perda de dados durante a falha do cluster Ative.
* Eventual consistência entre tópicos entre clusters ativos e passivos.
* Falhas nas primárias podem levar a inconsistência de mensagens em tópicos.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-active-passive.png" alt-text="Colmeia e arquitetura de consulta interativa":::

#### <a name="kafka-replication-active--active"></a>Replicação kafka: Ativo – Ativo

Active-Active configurado envolve dois aglomerados de HDInsight Kafka separados regionalmente com replicação assíncrona bidirecional com MirrorMaker. Neste desenho, as mensagens consumidas pelos consumidores em primeiro lugar também são disponibilizadas aos consumidores em secundário e vice-versa. Abaixo estão algumas vantagens e desvantagens de Active-Active configuração.

Vantagens:

* Devido ao seu estado duplicado, falhas e falhas são mais fáceis de executar.

Desvantagens:

* A configuração, gestão e monitorização é mais complexa do que a Active-Passive.
* O problema da replicação circular tem de ser resolvido.  
* A replicação bidirecional conduz a custos regionais mais elevados de saída de dados.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-active-active.png" alt-text="Colmeia e arquitetura de consulta interativa":::

## <a name="hdinsight-enterprise-security-package"></a>Pacote de segurança da empresa HDInsight

Esta configuração é usada para permitir a funcionalidade de vários utilizadores tanto em grupos primários como secundários, bem como [em conjuntos de réplicas AD DS Azure](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-replica-set) para garantir que os utilizadores podem autenticar em ambos os clusters. Durante as operações normais, as políticas ranger devem ser criadas no Secundário para garantir que os utilizadores estão restritos às operações de Leitura. A arquitetura abaixo explica como um ESP permitiu a Colmeia Ative Primary – Standby Secondary configuração pode parecer.

Replicação da Metastore Ranger:

Ranger Metastore é usado para armazenar e servir persistentemente as políticas ranger para controlar a autorização de dados. Recomendamos que mantenha políticas ranger independentes em primária e secundária e mantenha o secundário como uma réplica de leitura.
  
Se a exigência for manter as políticas ranger sincronizadas entre o primário e o secundário, utilize [a Importação/Exportação da Ranger](https://cwiki.apache.org/confluence/display/RANGER/User+Guide+For+Import-Export#:~:text=Ranger%20has%20introduced%20a%20new,can%20import%20and%20export%20policies.&text=Also%20can%20export%2Fimport%20a,repositories\)%20via%20Ranger%20Admin%20UI) para apoiar periodicamente e importar as políticas ranger do primário ao secundário.

Replicar as políticas dos Rangers entre o primário e o secundário pode fazer com que o secundário se torne ativado por escrito, o que pode levar a gravações inadvertidas sobre o secundário, levando a inconsistências de dados.  

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hdinsight-enterprise-security-package.png" alt-text="Colmeia e arquitetura de consulta interativa":::

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os itens discutidos neste artigo, consulte:

* [Referência Apache Ambari REST](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Instalar e configurar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)
* [Instalar e configurar módulo Azure PowerShell Az](/powershell/azure/)
* [Gerir HDInsight usando Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Provision Clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md)