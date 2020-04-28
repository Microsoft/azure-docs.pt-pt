---
title: 'Benefícios: Migrar no local Apache Hadoop para Azure HDInsight'
description: Aprenda a motivação e benefícios para migrar no local aglomerados Hadoop para Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: 2440b93629416ea73fcf211cbe7bf5a3b72ab2e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74267330"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>Migrar no local Apache Hadoop clusters para Azure HDInsight - motivação e benefícios

Este artigo é o primeiro de uma série sobre as melhores práticas para migrar no local Apache Hadoop implementações eco-system para Azure HDInsight. Esta série de artigos é para pessoas responsáveis pelo design, implantação e migração de soluções Apache Hadoop em Azure HDInsight. As funções que podem beneficiar destes artigos incluem arquitetos em nuvem, administradores hadoop e engenheiros de DevOps. Desenvolvedores de software, engenheiros de dados e cientistas de dados também devem beneficiar da explicação de como diferentes tipos de clusters funcionam na nuvem.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Por que migrar para Azure HDInsight

Azure HDInsight é uma distribuição em nuvem de componentes Hadoop. O Azure HDInsight torna mais fácil, rápido e rentável processar quantidades enormes de dados. O HDInsight inclui os quadros de código aberto mais populares, tais como:

- Apache Hadoop
- Apache Spark
- Colmeia Apache com LLAP
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="azure-hdinsight-advantages-over-on-premises-hadoop"></a>Vantagens azure HDInsight sobre hadoop no local

- **Baixo custo** - Os [custos](../hdinsight-hadoop-create-linux-clusters-adf.md) podem ser reduzidos através da criação de clusters a pedido e pagando apenas pelo que utiliza. A computação e armazenamento dissociados proporcionam flexibilidade mantendo o volume de dados independente do tamanho do cluster.

- **Criação automatizada de cluster** - Criação automatizada de cluster requer configuração e configuração mínimas. A automatização pode ser usada para clusters a pedido.

- **Hardware e configuração geridos** - Não há necessidade de se preocupar com o hardware físico ou infraestrutura com um cluster HDInsight. Basta especificar a configuração do cluster, e o Azure configura-o.

- **Facilmente escalável** - O HDInsight permite-lhe [escalar](../hdinsight-administer-use-portal-linux.md) cargas de trabalho para cima ou para baixo. O Azure cuida da redistribuição de dados e do reequilíbrio da carga de trabalho sem interromper os trabalhos de processamento de dados.

- **Disponibilidade global** - O HDInsight está disponível em mais [regiões](https://azure.microsoft.com/regions/services/) do que qualquer outra oferta de análise de big data. O Azure HDInsight também está disponível no Azure Government, na China e na Alemanha, o que lhe permite satisfazer as suas necessidades empresariais nas principais áreas soberanas.

- **Seguro e conforme** - O HDInsight permite proteger os seus ativos de dados empresariais com a [Rede Virtual Azure,](../hdinsight-plan-virtual-network-deployment.md) [encriptação](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)e integração com o [Diretório Ativo Azure](../domain-joined/hdinsight-security-overview.md). O HDInsight também cumpre os [padrões](https://azure.microsoft.com/overview/trusted-cloud)de conformidade mais populares da indústria e do governo.

- **Gestão simplificada da versão** - O Azure HDInsight gere a versão dos componentes do ecossistema Hadoop e mantém-nos atualizados. As atualizações de software são geralmente um processo complexo para implementações no local.

- **Clusters menores otimizados para cargas de trabalho específicas com menos dependências entre componentes** - Uma configuração hadoop típica no local usa um único cluster que serve muitos propósitos. Com o Azure HDInsight, podem ser criados clusters específicos da carga de trabalho. Criar clusters para cargas de trabalho específicas remove a complexidade de manter um único cluster com crescente complexidade.

- **Produtividade** - Você pode usar várias ferramentas para Hadoop e Spark no seu ambiente de desenvolvimento preferido.

- **Extensibility com ferramentas personalizadas ou aplicações de terceiros** - os clusters HDInsight podem ser estendidos com componentes instalados e também podem ser integrados com outras soluções de big data utilizando implementações [de um clique](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) a partir do lugar do Mercado Azure.

- **Gestão fácil, administração e monitorização** - O Azure HDInsight integra-se com [os registos](../hdinsight-hadoop-oms-log-analytics-tutorial.md) do Monitor Azure para fornecer uma única interface com a qual pode monitorizar todos os seus clusters.

- **Integração com outros serviços Azure** - O HDInsight pode ser facilmente integrado com outros serviços populares do Azure, tais como:

    - Fábrica de Dados Azure (ADF)
    - Armazenamento de Blobs do Azure
    - Armazenamento do Azure Data Lake Ger2
    - Azure Cosmos DB
    - Base de Dados SQL do Azure
    - Azure Analysis Services

- **Processos e componentes de autorrecuperação** - O HDInsight verifica constantemente a infraestrutura e os componentes de código aberto utilizando a sua própria infraestrutura de monitorização. Também recupera automaticamente falhas críticas, tais como a indisponibilidade de componentes de código aberto e nósomas. Os alertas são ativados em Ambari se algum componente OSS falhar.

Para mais informações, consulte o artigo [O que é Azure HDInsight e a pilha de tecnologia Apache Hadoop](../hadoop/apache-hadoop-introduction.md).

## <a name="migration-planning-process"></a>Processo de planeamento migratório

Recomendam-se os seguintes passos para o planeamento de uma migração de clusters Hadoop no local para o Azure HDInsight:

1. Compreenda a atual implantação no local e topoologias.
2. Compreenda o âmbito atual do projeto, os prazos e a experiência da equipa.
3. Compreenda os requisitos do Azure.
4. Construir um plano detalhado baseado nas melhores práticas.

## <a name="gathering-details-to-prepare-for-a-migration"></a>Recolha de detalhes para preparar uma migração

Esta secção fornece questionários de modelo para ajudar a recolher informações importantes sobre:

- A implantação no local
- Detalhes do projeto
- Requisitos do Azure

### <a name="on-premises-deployment-questionnaire"></a>Questionário de implantação no local

| **Pergunta** | **Exemplo** | **Resposta** |
|---|---|---|
|**Tópico**: **Ambiente**|||
|Versão de Distribuição de Cluster|HDP 2.6.5, CDH 5.7|
|Componentes do ecossistema big data|HDFS, Fios, Colmeia, LLAP, Impala, Kudu, HBase, Spark, MapReduce, Kafka, Zookeeper, Solr, Sqoop, Oozie, Ranger, Atlas, Falcon, Zeppelin, R|
|Tipos de cluster|Hadoop, Faísca, Confluente Kafka, Tempestade, Solr|
|Número de aglomerados|4|
|Número de nós mestres|2|
|Número de nós operários|100|
|Número de nós de borda| 5|
|Espaço total do disco|100 TB|
|Configuração do Nó Mestre|m/y, cpu, disco, etc.|
|Configuração dos Nódos de Dados|m/y, cpu, disco, etc.|
|Configuração dos nódos de borda|m/y, cpu, disco, etc.|
|Encriptação HDFS?|Sim|
|Elevada Disponibilidade|HDFS HA, Metastore HA|
|Recuperação de Desastres / Backup|Aglomerado de reserva?|  
|Sistemas que dependem do Cluster|SQL Server, Teradata, Power BI, MongoDB|
|Integrações de terceiros|Tableau, GridGain, Qubole, Informatica, Splunk|
|**Tópico**: **Segurança**|||
|Segurança do perímetro|Firewalls|
|Autorização de autenticação de cluster&|Diretório Ativo, Ambari, Cloudera Manager, Sem autenticação|
|Controlo de Acesso HDFS|  Utilizadores manuais e ssh|
|Autorização de autenticação & da colmeia|Sentinela, LDAP, AD com Kerberos, Ranger|
|Auditoria|Ambari, Cloudera Navigator, Ranger|
|Monitorização|Grafite, colecionada, estatística, Telegraf, InfluxDB|
|Alertas|Kapacitor, Prometeu, Datadog|
|Duração da retenção de dados| 3 anos, 5 anos|
|Administradores de Cluster|Administrador único, múltiplos administradores|

### <a name="project-details-questionnaire"></a>Questionário de detalhes do projeto

|**Pergunta**|**Exemplo**|**Resposta**|
|---|---|---|
|**Tópico**: **Cargas de trabalho e frequência**|||
|MapReduzir empregos|10 empregos - duas vezes por dia||
|Empregos na colmeia|100 empregos - a cada hora||
|Trabalhos de lote de faíscas|50 empregos - a cada 15 minutos||
|Spark Streaming empregos|5 empregos - a cada 3 minutos||
|Trabalhos de streaming estruturados|5 empregos - a cada minuto||
|Trabalhos de formação de modelos ML|2 empregos - uma vez por semana||
|Línguas de programação|Python, Scala, Java||
|Scripting|Shell||
|**Tópico**: **Dados**|||
|Origens de dados|Ficheiros planos, Json, Kafka, RDBMS||
|Orquestração de dados|Fluxos de trabalho oozie, fluxo de ar||
|Em retrospetivas de memória|Apache Ignite||
|Destinos de dados|HDFS, RDBMS, Kafka, MPP ||
|**Tópico**: **Meta data**|||
|Tipo HIVE DB|Mysql||
|Número de metalojas da Colmeia|2||
|Número de mesas da Colmeia|100||
|Número de políticas ranger|20||
|Número de fluxos de trabalho da Oozie|100||
|**Tópico**: **Escala**|||
|Volume de dados, incluindo replicação|100 TB||
|Volume diário de ingestão|50 GB||
|Taxa de crescimento de dados|10% por ano||
|Taxa de crescimento dos Nós cluster|5% por ano
|**Tópico**: **Utilização do cluster**|||
|CpU médio % utilizado|60%||
|Memória média % utilizada|75%||
|Espaço de disco usado|75%||
|Rede média % utilizada|25%
|**Tópico**: **Pessoal**|||
|Número de Administradores|2||
|Número de Desenvolvedores|10||
|Número de utilizadores finais|100||
|Competências|Hadoop||
|Número de recursos disponíveis para os esforços migratórios|2||
|**Tópico**: **Limitações**|||
|Limitações atuais|Latência é alta||
|Desafios atuais|Emissão de condivisões||

### <a name="azure-requirements-questionnaire"></a>Questionário de requisitos do Azure

|**Tópico**: **Infraestrutura** |||
|---|---|---|
|**Pergunta**|**Exemplo**|**Resposta**|
| Região Preferida|E.U.A Leste||
|VNet preferido?|Sim||
|HA / DR Necessário?|Sim||
|Integração com outros serviços na nuvem?|ADF||
|**Tópico**: **Movimento de Dados**  |||
|Preferência inicial de carga|DistCp, Caixa de Dados, ADF, WANDisco||
|Delta de transferência de dados|DistCp, AzCopy||
|Transferência de dados incrementais em curso|DistCp||
|**Tópico**: **Monitorização & Alerta** |||
|Utilizar monitorização Azure & alerta vs Integrar monitorização de terceiros|Utilizar alerta de monitorização de & de monitorização Azure||
|**Tópico**: **Preferências de segurança** |||
|Oleoduto de dados privado e protegido?|Sim||
|Cluster de domínio (ESP)?|     Sim||
|On-Premises AD Sync to Cloud?|     Sim||
|Número de utilizadores de AD para sincronizar?|          100||
|Ok para sincronizar senhas na nuvem?|    Sim||
|Cloud apenas Utilizadores?|                 Sim||
|MFA necessário?|                       Não|| 
|Requisitos de autorização de dados?|  Sim||
|Controlo de Acesso baseado em papéis?|        Sim||
|Auditoria necessária?|                  Sim||
|Encriptação de dados em repouso?|          Sim||
|Encriptação de dados em trânsito?|       Sim||
|**Tópico**: **Preferências de Rearquitectura** |||
|Cluster único vs tipos específicos de cluster|Tipos específicos de cluster||
|Armazenamento colocalizado Vs Armazenamento remoto?|Armazenamento Remoto||
|Tamanho do cluster menor à medida que os dados são armazenados remotamente?|Tamanho menor do cluster||
|Usar vários aglomerados menores em vez de um único aglomerado grande?|Use vários aglomerados menores||
|Usar uma metaloja remota?|Sim||
|Partilhar metastores entre diferentes clusters?|Sim||
|Desconstruir cargas de trabalho?|Substituir empregos na Colmeia por empregos spark||
|Usar ADF para orquestração de dados?|Não||

## <a name="next-steps"></a>Passos seguintes

Leia o próximo artigo desta série:

- [As melhores práticas de arquitetura para a migração de Hadoop Azure HDInsight](apache-hadoop-on-premises-migration-best-practices-architecture.md)
