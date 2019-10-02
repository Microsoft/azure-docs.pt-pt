---
title: Migrar Apache Hadoop locais para o Azure HDInsight-motivação e benefícios
description: Conheça a motivação e os benefícios para migrar clusters Hadoop locais para o Azure HDInsight.
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: a65b775a516bfccac2dee5ce00bc7d6495df256d
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718366"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>Migrar clusters de Apache Hadoop locais para o Azure HDInsight-motivação e benefícios

Este artigo é o primeiro de uma série sobre as práticas recomendadas para migrar implantações locais de Apache Hadoop de sistemas de eco para o Azure HDInsight. Esta série de artigos destina-se a pessoas responsáveis pelo design, implantação e migração de soluções de Apache Hadoop no Azure HDInsight. As funções que podem se beneficiar desses artigos incluem arquitetos de nuvem, administradores do Hadoop e engenheiros de DevOps. Os desenvolvedores de software, os engenheiros de dados e os cientistas de dados também devem se beneficiar da explicação de como os diferentes tipos de clusters funcionam na nuvem.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Por que migrar para o Azure HDInsight

O Azure HDInsight é uma distribuição em nuvem de componentes do Hadoop. O Azure HDInsight torna mais fácil, rápido e rentável processar quantidades enormes de dados. O HDInsight inclui as estruturas de código aberto mais populares, como:

- Apache Hadoop
- Apache Spark
- Apache Hive com LLAP
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="azure-hdinsight-advantages-over-on-premises-hadoop"></a>Vantagens do Azure HDInsight em relação ao Hadoop local

- Custos **baixos** podem ser reduzidos com a [criação de clusters sob demanda](../hdinsight-hadoop-create-linux-clusters-adf.md) e o pagamento apenas pelo que você usa. A computação e o armazenamento dissociados fornecem flexibilidade mantendo o volume de dados independente do tamanho do cluster.

- **Criação automatizada de cluster** -a criação automatizada de clusters requer configuração e configuração mínimas. A automação pode ser usada para clusters sob demanda.

- **Hardware e configuração gerenciados** – não há necessidade de se preocupar com o hardware ou a infraestrutura física com um cluster HDInsight. Basta especificar a configuração do cluster e o Azure o configura.

- **Facilmente escalonável** – o HDInsight permite [escala](../hdinsight-administer-use-portal-linux.md) que você dimensione as cargas de trabalho para cima ou para baixo. O Azure cuida da redistribuição de dados e do rebalanceamento de carga de trabalho sem interromper os trabalhos de processamento de dados.

- **Disponibilidade global** – o HDInsight está disponível em mais [regiões](https://azure.microsoft.com/regions/services/) do que qualquer outra oferta de análise de Big Data. O Azure HDInsight também está disponível no Azure Government, na China e na Alemanha, o que lhe permite satisfazer as suas necessidades empresariais nas principais áreas soberanas.

- **Seguro e compatível** – o HDInsight permite que você proteja seus ativos de dados corporativos com a rede virtual, [criptografia](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)e integração [do Azure](../hdinsight-plan-virtual-network-deployment.md)com o [Azure Active Directory](../domain-joined/hdinsight-security-overview.md). O HDInsight também atende aos [padrões de conformidade](https://azure.microsoft.com/overview/trusted-cloud)mais populares do setor e do governo.

- **Gerenciamento de versão simplificado** – o Azure HDInsight gerencia a versão dos componentes do sistema de eco do Hadoop e os mantém atualizados. Atualizações de software geralmente são um processo complexo para implantações locais.

- **Clusters menores otimizados para cargas de trabalho específicas com menos dependências entre componentes** – uma configuração do Hadoop local típica usa um único cluster que atende a muitas finalidades. Com o Azure HDInsight, os clusters específicos da carga de trabalho podem ser criados. A criação de clusters para cargas de trabalho específicas elimina a complexidade de manter um único cluster com complexidade crescente.

- **Produtividade** – você pode usar várias ferramentas para Hadoop e Spark em seu ambiente de desenvolvimento preferido.

- **Extensibilidade com ferramentas personalizadas ou aplicativos** de terceiros-os clusters HDInsight podem ser estendidos com os componentes instalados e também podem ser integrados a outras soluções de Big data usando o @no__t de [um clique](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/)-2deployments do Azure Mercado.

- **Gerenciamento, administração e monitoramento fáceis** – o Azure HDInsight integra-se aos [logs de Azure monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md) To fornecem uma única interface com a qual você pode monitorar todos os seus clusters.

- **Integração com outros serviços do Azure** – o HDInsight pode ser facilmente integrado a outros serviços populares do Azure, como o seguinte:

    - Azure Data Factory (ADF)
    - Armazenamento de Blobs do Azure
    - Azure Data Lake Storage Gen2
    - Azure Cosmos DB
    - Base de Dados SQL do Azure
    - Azure Analysis Services

- **Processos e componentes** de auto-recuperação – o HDInsight verifica constantemente a infraestrutura e os componentes de software livre usando sua própria infraestrutura de monitoramento. Ele também recupera automaticamente falhas críticas, como indisponibilidade de nós e componentes de software livre. Os alertas serão disparados em Ambari se algum componente do OSS falhar.

Para obter mais informações, consulte o artigo [o que é o Azure HDInsight e a pilha de tecnologia Apache Hadoop](../hadoop/apache-hadoop-introduction.md).

## <a name="migration-planning-process"></a>Processo de planejamento de migração

As etapas a seguir são recomendadas para planejar uma migração de clusters Hadoop locais para o Azure HDInsight:

1. Entenda a implantação e as topologias locais atuais.
2. Entenda o escopo atual do projeto, as linhas do tempo e a experiência da equipe.
3. Entenda os requisitos do Azure.
4. Crie um plano detalhado com base nas práticas recomendadas.

## <a name="gathering-details-to-prepare-for-a-migration"></a>Coletando detalhes para se preparar para uma migração

Esta seção fornece questionários de modelo para ajudar a coletar informações importantes sobre:

- A implantação local
- Detalhes do projeto
- Requisitos do Azure

### <a name="on-premises-deployment-questionnaire"></a>Questionário de implantação local

| **Pergunta** | **Exemplo** | **Atenda** |
|---|---|---|
|**Tópico**: **Ambiente**|||
|Versão de distribuição do cluster|HDP 2.6.5, CDH 5,7|
|Componentes de sistema de eco de Big data|HDFS, Yarn, Hive, LLAP, Impala, Kudu, HBase, Spark, MapReduce, Kafka, Zookeeper, Solr, Sqoop, Oozie, Ranger, Atlas, Falcon, Zeppelin, R|
|Tipos de cluster|Hadoop, Spark, Kafkas, Storm, Solr|
|Número de clusters|4|
|Número de nós mestres|2|
|Número de nós de trabalho|100|
|Número de nós de borda| 5|
|Espaço total em disco|100 TB|
|Configuração do nó mestre|m/y, cpu, disk, etc.|
|Configuração de nós de dados|m/y, cpu, disk, etc.|
|Configuração de nós de borda|m/y, cpu, disk, etc.|
|Criptografia HDFS?|Sim|
|Elevada Disponibilidade|HDFS de alta disponibilidade, metastore HA|
|Recuperação de desastre/backup|Cluster de backup?|  
|Sistemas que dependem do cluster|SQL Server, Teradata, Power BI, MongoDB|
|Integrações de terceiros|Tableau, GridGain, Qubole, Informatica, Splunk|
|**Tópico**: **Segurança**|||
|Segurança de perímetro|Firewalls|
|Autenticação de cluster & autorização|Active Directory, Ambari, Cloudera Manager, sem autenticação|
|Controle de acesso do HDFS|  Usuários do SSH e manuais|
|Autenticação do hive & autorização|Sentry, LDAP, AD com Kerberos, Ranger|
|Auditoria|Ambari, Cloudera Navigator, Ranger|
|Monitorização|Graphite, Collected, stated, Telegraf, InfluxDB|
|Alertas|Kapacitor, Prometheus, Datadog|
|Duração da retenção de dados| 3 anos, 5 anos|
|Administradores de cluster|Administrador único, vários administradores|

### <a name="project-details-questionnaire"></a>Questionário detalhes do projeto

|**Pergunta**|**Exemplo**|**Atenda**|
|---|---|---|
|**Tópico**: **Cargas de trabalho e frequência**|||
|Trabalhos MapReduce|10 trabalhos--duas vezes por dia||
|Trabalhos do hive|100 trabalhos--a cada hora||
|Trabalhos em lotes do Spark|50 trabalhos--a cada 15 minutos||
|Trabalhos de streaming do Spark|5 trabalhos--a cada 3 minutos||
|Trabalhos de streaming estruturados|5 trabalhos--a cada minuto||
|Trabalhos de treinamento do modelo ML|2 trabalhos – uma vez em uma semana||
|Linguagens de Programação|Python, escala, Java||
|Processamento de Scripts|Shell, Python||
|**Tópico**: **Dados**|||
|Origens de dados|Arquivos simples, JSON, Kafka, RDBMS||
|Orquestração de dados|Fluxos de trabalho do Oozie, fluxo de ar||
|Pesquisas em memória|Apache Ignite, Redis||
|Destinos de dados|HDFS, RDBMS, Kafka, MPP ||
|**Tópico**: **Metadados**|||
|Tipo de BD do hive|Mysql, Postgres||
|Número de metastores do hive|2||
|Número de tabelas do hive|100||
|Número de políticas de Ranger|20||
|Número de fluxos de trabalho do Oozie|100||
|**Tópico**: **Dimensionamento**|||
|Volume de dados incluindo replicação|100 TB||
|Volume de ingestão diário|50 GB||
|Taxa de crescimento de dados|10% por ano||
|Taxa de crescimento de nós de cluster|5% por ano
|**Tópico**: **Utilização do cluster**|||
|% De média de CPU usada|60%||
|% De memória média usada|75%||
|Espaço em disco utilizado|75%||
|% De rede média usada|25%
|**Tópico**: **Receber**|||
|Número de administradores|2||
|Número de desenvolvedores|10||
|Número de usuários finais|100||
|Competências|Hadoop, Spark||
|Número de recursos disponíveis para os esforços de migração|2||
|**Tópico**: **Limitações**|||
|Limitações atuais|A latência é alta||
|Desafios atuais|Problema de simultaneidade||

### <a name="azure-requirements-questionnaire"></a>Questionário de requisitos do Azure

|**Tópico**: **Infra-estruturas** |||
|---|---|---|
|**Pergunta**|**Exemplo**|**Atenda**|
| Região Preferencial|Leste dos E.U.A.||
|VNet preferencial?|Sim||
|HA/DR necessários?|Sim||
|Integração com outros serviços de nuvem?|ADF, CosmosDB||
|**Tópico**:   **Movimento de Dados**  |||
|Preferência de carregamento inicial|DistCp, data Box, ADF, WANDisco||
|Delta de transferência de dados|DistCp, AzCopy||
|Transferência de dados incremental em andamento|DistCp, Sqoop||
|**Tópico**:   **Monitoramento & alertas** |||
|Usar o monitoramento do Azure & alertas do vs integrar o monitoramento de terceiros|Usar o monitoramento do Azure & alertas||
|**Tópico**:   **Preferências de segurança** |||
|Pipeline de dados particular e protegido?|Sim||
|ESP (cluster ingressado no domínio)?|     Sim||
|AD Sync local para nuvem?|     Sim||
|Número de usuários do AD a serem sincronizados?|          100||
|OK para sincronizar as senhas com a nuvem?|    Sim||
|Somente usuários de nuvem?|                 Sim||
|MFA necessária?|                       Não|| 
|Requisitos de autorização de dados?|  Sim||
|Controle de acesso baseado em função?|        Sim||
|A auditoria é necessária?|                  Sim||
|Criptografia de dados em repouso?|          Sim||
|Criptografia de dados em trânsito?|       Sim||
|**Tópico**:   **Preferências de rearquitetura** |||
|Cluster único vs. tipos de cluster específicos|Tipos de cluster específicos||
|Armazenamento colocalizado versus armazenamento remoto?|Armazenamento remoto||
|Tamanho de cluster menor, pois os dados são armazenados remotamente?|Tamanho de cluster menor||
|Usar vários clusters menores em vez de um único cluster grande?|Usar vários clusters menores||
|Usar um metastore remoto?|Sim||
|Compartilhar metastores entre clusters diferentes?|Sim||
|Cargas de trabalho do desconstruir?|Substituir trabalhos do hive por trabalhos do Spark||
|Usar o ADF para orquestração de dados?|Não||

## <a name="next-steps"></a>Passos seguintes

Leia o próximo artigo desta série:

- [Práticas recomendadas de arquitetura para migração local para Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-architecture.md)
