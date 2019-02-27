---
title: Migrar clusters do Apache Hadoop no local para o Azure HDInsight - motivação e benefícios
description: Aprenda a motivação e os benefícios para migrar clusters do Hadoop no local para Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 73a2f0754cafaa5da09ebd437ecd62813296ffd9
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890084"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>Migrar clusters do Apache Hadoop no local para o Azure HDInsight - motivação e benefícios

Este artigo é a primeira de uma série sobre melhores práticas para a migração de implementações no local Apache Hadoop ao meio ambiente do sistema para o Azure HDInsight. Esta série de artigos destina-se a pessoas que são responsáveis pelo design, implementação e migração de soluções do Apache Hadoop no HDInsight do Azure. As funções que podem tirar partido destes artigos incluem arquitetos de cloud, os administradores do Hadoop e engenheiros de DevOps. Os desenvolvedores de software, os engenheiros de dados e os cientistas de dados devem também se beneficiar a explicação de diferentes tipos de clusters de trabalho na cloud.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Por que migrar para o Azure HDInsight

O Azure HDInsight é uma distribuição de nuvem dos componentes do Hadoop do [Hortonworks Data Platform(HDP)](https://hortonworks.com/products/data-center/hdp/). O Azure HDInsight torna mais fácil, rápido e rentável processar quantidades enormes de dados. HDInsight inclui as arquiteturas de código-fonte aberto mais populares, tais como:

- Apache Hadoop
- Apache Spark
- Apache Hive com o LLAP
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="azure-hdinsight-advantages-over-on-premises-hadoop"></a>O Azure HDInsight vantagens em relação ao Hadoop no local

- **Baixo custo** -os custos podem ser reduzidos através de [criar clusters a pedido](../hdinsight-hadoop-create-linux-clusters-adf.md) e pagando apenas aquilo que utiliza. Computação e armazenamento desacoplados proporciona flexibilidade ao manter o volume de dados independente do tamanho do cluster.

- **Automatizada de criação do cluster** - automatizada criação do cluster requer configuração mínima e a configuração. Automatização pode ser utilizada para clusters a pedido.

- **Geridos hardware e configuração** -não é necessário se preocupar sobre o hardware físico ou a infraestrutura com um cluster do HDInsight. Especifique apenas a configuração do cluster, e o Azure o configura.

- **Facilmente escalável** -HDInsight permite-lhe [dimensionamento](../hdinsight-administer-use-portal-linux.md) cargas de trabalho ou reduzir verticalmente. Azure se encarrega de redistribuição de dados e a carga de trabalho reequilibrar sem interromper as tarefas de processamento de dados.

- **Disponibilidade global** -HDInsight está disponível em mais [regiões](https://azure.microsoft.com/regions/services/) que qualquer outra oferta de análise de macrodados. O Azure HDInsight também está disponível no Azure Government, na China e na Alemanha, o que lhe permite satisfazer as suas necessidades empresariais nas principais áreas soberanas.

- **Segura e em conformidade** -HDInsight permite-lhe proteger seus ativos de dados empresariais com [rede Virtual do Azure](../hdinsight-extend-hadoop-virtual-network.md), [encriptação](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)e a integração com [Active Directory do Azure Diretório](../domain-joined/apache-domain-joined-introduction.md). HDInsight também cumpre os mais populares da indústria e governamentais [normas de conformidade](https://azure.microsoft.com/overview/trusted-cloud).

- **Gerenciamento de versão simplificado** -Azure HDInsight gere a versão dos componentes do Hadoop ao meio ambiente do sistema e as mantém atualizadas. Atualizações de software são, normalmente, um processo complexo para implementações no local.

- **Clusters menores otimizadas para cargas de trabalho específicas com menos dependências entre os componentes** -uma configuração de Hadoop típico no local utiliza um cluster único que tem muitas finalidades. Com o Azure HDInsight, é possível criar clusters de carga de trabalho específica. Criação de clusters para cargas de trabalho específicas, remove a complexidade de manter um único cluster com a crescente complexidade.

- **Produtividade** -pode utilizar várias ferramentas para o Hadoop e Spark no seu ambiente de desenvolvimento preferido.

- **Extensibilidade com ferramentas personalizadas ou aplicações de terceiros** -clusters do HDInsight podem ser estendidas com componentes instalados e também pode ser integrados com as outras soluções de macrodados utilizando [num único clique](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/)  implementações a partir do mercado do Azure.

- **Facilitar a gestão, administração e monitoramento** -Azure HDInsight integra-se [registos do Azure Monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md) para fornecer uma única interface com a qual pode monitorizar todos os clusters.

- **Integração com outros serviços do Azure** -HDInsight pode ser facilmente integrado com outros serviços do Azure populares, como o seguinte:

    - Azure Data Factory (ADF)
    - Armazenamento de Blobs do Azure
    - Armazenamento do Azure Data Lake Ger2
    - Azure Cosmos DB
    - Base de Dados SQL do Azure
    - Azure Analysis Services

- **Autorrecuperação processos e componentes** -HDInsight verifique constantemente os componentes de infraestrutura e o código-fonte aberto com a própria infraestrutura de monitorização. Ele também automaticamente recupera falhas críticas, como indisponibilidade de componentes de código aberto e nós. Alertas são acionados no Ambari se qualquer componente de sistemas operacionais falhou.

Para obter mais informações, consulte o artigo [o que é o Azure HDInsight e a pilha de tecnologia do Apache Hadoop](../hadoop/apache-hadoop-introduction.md).

## <a name="migration-planning-process"></a>Processo de planejamento de migração

Os seguintes passos são recomendados para planejar uma migração de clusters do Hadoop no local para Azure HDInsight:

1. Compreenda a implementação de no local atual e topologias.
2. Compreenda o escopo do projeto atual, as linhas do tempo e conhecimentos de equipe.
3. Compreenda os requisitos do Azure.
4. Crie um plano detalhado, com base nas práticas recomendadas.

## <a name="gathering-details-to-prepare-for-a-migration"></a>Detalhes para se preparar para uma migração de recolha

Esta seção fornece questionários de modelo para o ajudar a reunir informações importantes sobre:

- A implementação no local
- Detalhes do projeto
- Requisitos do Azure

### <a name="on-premises-deployment-questionnaire"></a>Questionário de implementação no local

| **Pergunta** | **Exemplo** | **Resposta** |
|---|---|---|
|**tópico**: **Environment**|||
|Tipo de distribuição de cluster|Hortonworks, Cloudera, MapR| |
|Versão de distribuição do cluster|HDP 2.6.5, CDH 5.7|
|Grandes componentes do sistema ao meio ambiente de dados|HDFS, Yarn, Hive, LLAP, Impala, Kudu, HBase, Spark, MapReduce, Kafka, Zookeeper, Solr, Sqoop, Oozie, Ranger, Atlas, Falcon, Zeppelin, R|
|Tipos de cluster|Hadoop, Spark, Confluent Kafka, Storm, Solr|
|Número de clusters|4|
|Número de nós do mestre|2|
|Número de nós de trabalho|100|
|Número de nós de extremidade| 5|
|Total de espaço em disco|100 TB|
|Configuração do nó principal|m/y, cpu, disk, etc.|
|Configuração de nós de dados|m/y, cpu, disk, etc.|
|Configuração de nós de extremidade|m/y, cpu, disk, etc.|
|Encriptação de HDFS?|Sim|
|Elevada Disponibilidade|HA do HDFS, Metastore HA|
|Recuperação após desastre / cópia de segurança|Cluster de cópia de segurança?|  
|Sistemas que são dependentes do Cluster|SQL Server, Teradata, Power BI, MongoDB|
|Integrações de terceiros|Tableau, GridGain, Qubole, Informatica, Splunk|
|**tópico**: **Segurança**|||
|Segurança de perímetro|Firewalls|
|Cluster de autenticação e autorização|Active Directory, Ambari, Cloudera o Gestor de diretório, sem autenticação|
|Controlo de acesso do HDFS|  Manual, ssh utilizadores|
|Ramo de registo de autenticação e autorização|Sentry, LDAP, AD com o Kerberos, o Ranger|
|Auditoria|Ambari, navegador Cloudera, Ranger|
|Monitorização|Graphite, recolhidos, statsd, Telegraf, InfluxDB|
|Alertas|Kapacitor, Prometheus, Datadog|
|Duração da retenção de dados| 3 anos, 5 anos|
|Administradores de cluster|Administrador de único, vários administradores|

### <a name="project-details-questionnaire"></a>Questionário de detalhes do projeto

|**Pergunta**|**Exemplo**|**Resposta**|
|---|---|---|
|**tópico**: **Cargas de trabalho e a frequência**|||
|Tarefas de MapReduce|10 tarefas – duas vezes por dia||
|Tarefas do Hive|100 tarefas – cada hora||
|Tarefas de lote do Spark|50 tarefas – a cada 15 minutos||
|Tarefas de transmissão em fluxo do Spark|5 tarefas – a cada 3 minutos||
|Tarefas de transmissão em fluxo estruturada|5 tarefas – a cada minuto||
|Tarefas de formação do modelo de ML|2 tarefas, uma vez numa semana||
|Linguagens de Programação|Python, Scala, Java||
|Scripts|Shell, Python||
|**tópico**: **Dados**|||
|Origens de dados|Arquivos simples, Json, Kafka, RDBMS||
|Orquestração de dados|Fluxos de trabalho do Oozie, ventilação||
|Em pesquisas de memória|Apache Ignite, Redis||
|Destinos de dados|HDFS, RDBMS, Kafka, MPP ||
|**tópico**: **Metadados**|||
|Tipo de BD do Hive|Mysql, Postgres||
|Não. de metastores do Hive|2||
|Não. de tabelas do Hive|100||
|Não. de políticas do Ranger|20||
|Não. dos fluxos de trabalho do Oozie|100||
|**tópico**: **Dimensionamento**|||
|Volume de dados, incluindo a replicação|100 TB||
|Volume de ingestão de diário|50 GB||
|Taxa de crescimento de dados|10% por ano||
|Taxa de crescimento de nós de cluster|5% por ano
|**tópico**: **Utilização do cluster**|||
|Média de CPU de % utilizada|60%||
|Média % de memória utilizada|75%||
|Espaço em disco utilizado|75%||
|Média de rede de % utilizada|25%
|**tópico**: **Staff**|||
|Não. de administradores|2||
|Não. de desenvolvedores|10||
|Não. dos utilizadores finais|100||
|Competências|Hadoop, Spark||
|Não. de recursos disponíveis para iniciativas de migração|2||
|**tópico**: **Limitações**|||
|Limitações atuais|Latência é elevada||
|Desafios atuais|Problema de simultaneidade||

### <a name="azure-requirements-questionnaire"></a>Questionário de requisitos do Azure

|**tópico**: **Infraestrutura** |||
|---|---|---|
|**Pergunta**|**Exemplo**|**Resposta**|
| Região Preferencial|E.U.A Leste||
|VNet preferido?|Sim||
|HA / DR necessária?|Sim||
|Integração com outros serviços em nuvem?|ADF, CosmosDB||
|**tópico**:   **Movimento de Dados**  |||
|Preferência de carregamento inicial|DistCp, dados de caixa, ADF, WANDisco||
|Diferenças de transferência de dados|DistCp, o AzCopy||
|Transferência de dados incrementais em curso|DistCp, Sqoop||
|**tópico**:   **Monitorização e alertas** |||
|Utilizar a monitorização de terceiros de monitorização do Azure & alertas integrar o Vs|Utilizar o Azure, monitorização e alertas||
|**tópico**:   **Preferências de segurança** |||
|Pipeline de dados particulares e protegidos?|Sim||
|Cluster de associado de domínio (ESP)?|     Sim||
|No local sincronização do AD para a Cloud?|     Sim||
|Não. de utilizadores do AD para sincronizar?|          100||
|OK para sincronizar as palavras-passe para a cloud?|    Sim||
|Apenas utilizadores de cloud?|                 Sim||
|MFA necessário?|                       Não|| 
|Requisitos de autorização de dados?|  Sim||
|Controlo de acesso baseado em funções?|        Sim||
|Auditoria necessário?|                  Sim||
|Encriptação de dados Inativos?|          Sim||
|Encriptação de dados em trânsito?|       Sim||
|**tópico**:   **Preferências de Rearquitetura** |||
|Tipos de clusters específicos do cluster único vs|Tipos de clusters específicos||
|Armazenamento remoto do Vs de armazenamento colocalizados?|Armazenamento remoto||
|Tamanho de cluster mais pequeno que dados é armazenado remotamente?|Tamanho de cluster mais pequeno||
|Utilizar vários clusters menores em vez de um único cluster grande?|Utilizar vários clusters menores||
|Utilizar um metastore remoto?|Sim||
|Partilhar metastores entre clusters diferentes?|Sim||
|Eles possuem de desconstruir cargas de trabalho?|Substitua as tarefas do Hive com tarefas do Spark||
|Utilizar o ADF para orquestração de dados?|Não||
|HDInsight vs Hortonworks Data Platform no IaaS?|HDInsight||

## <a name="next-steps"></a>Passos Seguintes

Leia o artigo seguinte nesta série:

- [Práticas recomendadas de arquitetura para no local para migração do Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-architecture.md)
