---
title: 'Benefícios: Migrar no local Apache Hadoop para Azure HDInsight'
description: Aprenda a motivação e os benefícios para migrar no local os clusters Hadoop para Azure HDInsight.
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 11/15/2019
ms.openlocfilehash: 975d72df32027888e217d5da9171dba0ba61f257
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943253"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>Migrar para as instalações apache Hadoop clusters para Azure HDInsight - motivação e benefícios

Este artigo é o primeiro de uma série sobre boas práticas para migrar no local implantações do ecossistema Apache Hadoop para Azure HDInsight. Esta série de artigos destina-se a pessoas responsáveis pelo design, implantação e migração de soluções Apache Hadoop em Azure HDInsight. Os papéis que podem beneficiar destes artigos incluem arquitetos de nuvem, administradores hadoop e engenheiros de DevOps. Desenvolvedores de software, engenheiros de dados e cientistas de dados também devem beneficiar da explicação de como diferentes tipos de clusters funcionam na nuvem.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Por que migrar para Azure HDInsight

Azure HDInsight é uma distribuição em nuvem de componentes Hadoop. O Azure HDInsight torna mais fácil, rápido e rentável processar quantidades enormes de dados. O HDInsight inclui os quadros de código aberto mais populares, tais como:

- Apache Hadoop
- Apache Spark
- Colmeia Apache com LLAP
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="azure-hdinsight-advantages-over-on-premises-hadoop"></a>Vantagens Azure HDInsight sobre as instalações da Hadoop

- **Baixo custo** - Os custos podem ser [reduzidos criando clusters a pedido](../hdinsight-hadoop-create-linux-clusters-adf.md) e pagando apenas pelo que usa. O cálculo e armazenamento dissociados proporcionam flexibilidade mantendo o volume de dados independentemente do tamanho do cluster.

- **Criação automatizada de cluster** - A criação automatizada de clusters requer uma configuração e configuração mínimas. A automatização pode ser usada para clusters a pedido.

- **Hardware e configuração geridos** - Não há necessidade de se preocupar com o hardware físico ou infraestrutura com um cluster HDInsight. Basta especificar a configuração do cluster, e Azure o configura.

- **Facilmente escalável** - HDInsight permite-lhe [escalar](../hdinsight-administer-use-portal-linux.md) cargas de trabalho para cima ou para baixo. O Azure cuida da redistribuição de dados e do reequilíbrio da carga de trabalho sem interromper os trabalhos de processamento de dados.

- **Disponibilidade global** - HDInsight está disponível em mais [regiões](https://azure.microsoft.com/regions/services/) do que qualquer outra oferta de big data analytics. O Azure HDInsight também está disponível no Azure Government, na China e na Alemanha, o que lhe permite satisfazer as suas necessidades empresariais nas principais áreas soberanas.

- **Seguro e compatível** - O HDInsight permite-lhe proteger os seus ativos de dados empresariais com [Azure Virtual Network,](../hdinsight-plan-virtual-network-deployment.md) [encriptação](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)e integração com [o Azure Ative Directory](../domain-joined/hdinsight-security-overview.md). O HDInsight também cumpre os [padrões de conformidade](https://azure.microsoft.com/overview/trusted-cloud) mais populares da indústria e do governo.

- **Gestão de versão simplificada** - Azure HDInsight gere a versão dos componentes do ecossistema Hadoop e mantém-nos atualizados. As atualizações de software são geralmente um processo complexo para implementações no local.

- **Agrupamentos menores otimizados para cargas de trabalho específicas com menos dependências entre componentes** - Uma configuração típica no local Hadoop usa um único cluster que serve muitos propósitos. Com a Azure HDInsight, podem ser criados clusters específicos da carga de trabalho. A criação de clusters para cargas de trabalho específicas elimina a complexidade de manter um único cluster com crescente complexidade.

- **Produtividade** - Pode utilizar várias ferramentas para Hadoop e Spark no seu ambiente de desenvolvimento preferido.

- **Extensibilidade com ferramentas personalizadas ou aplicações de terceiros** - os clusters HDInsight podem ser estendidos com componentes instalados e também podem ser integrados com as outras grandes soluções de dados utilizando implementações [de um clique](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) a partir do mercado Azure.

- **Gestão, administração e monitorização fáceis** - Azure HDInsight [integra-se com registos do Azure Monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md) para fornecer uma única interface com a qual pode monitorizar todos os seus clusters.

- **Integração com outros serviços Azure** - HDInsight pode facilmente ser integrada com outros serviços populares da Azure, tais como:

    - Fábrica de Dados Azure (ADF)
    - Armazenamento de Blobs do Azure
    - Armazenamento do Azure Data Lake Ger2
    - Azure Cosmos DB
    - Base de Dados SQL do Azure
    - Azure Analysis Services

- **Processos e componentes de auto-cura** - O HDInsight verifica constantemente a infraestrutura e os componentes de código aberto utilizando a sua própria infraestrutura de monitorização. Também recupera automaticamente falhas críticas, tais como indisponibilidade de componentes e nós de código aberto. Os alertas são acionados em Ambari se algum componente DAS estiver falhado.

Para mais informações, consulte o artigo [O que é Azure HDInsight e a pilha de tecnologia Apache Hadoop.](../hadoop/apache-hadoop-introduction.md)

## <a name="migration-planning-process"></a>Processo de planeamento migratório

Recomenda-se o planeamento de uma migração de aglomerados Hadoop no local para a Azure HDInsight:

1. Compreender a atual implantação no local e as topologias.
2. Compreenda o âmbito atual do projeto, os prazos e a experiência da equipa.
3. Compreenda os requisitos do Azure.
4. Construa um plano detalhado baseado nas melhores práticas.

## <a name="gathering-details-to-prepare-for-a-migration"></a>Reunir detalhes para preparar uma migração

Esta secção fornece questionários de modelo para ajudar a recolher informações importantes sobre:

- A implantação no local
- Detalhes do projeto
- Requisitos do Azure

### <a name="on-premises-deployment-questionnaire"></a>Questionário de implantação no local

| **Pergunta** | **Exemplo** | **Resposta** |
|---|---|---|
|**Tópico**: **Ambiente**|||
|Versão cluster distribution|HDP 2.6.5, CDH 5.7|
|Componentes do ecossistema big data|HDFS, Yarn, Hive, LLAP, Impala, Kudu, HBase, Spark, MapReduce, Kafka, Zookeeper, Solr, Sqoop, Oozie, Ranger, Atlas, Falcon, Zeppelin, R|
|Tipos de cluster|Hadoop, Spark, Confluent Kafka, Storm, Solr|
|Número de aglomerados|4|
|Número de nós mestres|2|
|Número de nós de trabalhadores|100|
|Número de nós de borda| 5|
|Espaço total do disco|100 TB|
|Configuração do nó mestre|m/y, cpu, disco, etc.|
|Configuração de nó de dados|m/y, cpu, disco, etc.|
|Configuração de nó de borda|m/y, cpu, disco, etc.|
|Encriptação HDFS?|Yes|
|Elevada Disponibilidade|HDFS HA, Metasteiros HA|
|Recuperação de desastres / Backup|Aglomerado de reserva?|  
|Sistemas dependentes do Cluster|SQL Server, Teradata, Power BI, MongoDB|
|Integrações de terceiros|Tableau, GridGain, Qubole, Informatica, Splunk|
|**Tópico**: **Segurança**|||
|Segurança do perímetro|Firewalls|
|Autenticação de agrupamento & autorização|Diretório Ativo, Ambari, Gestor Cloudera, Sem autenticação|
|Controlo de Acesso HDFS|  Utilizadores manuais e ssh|
|Autorização de autenticação & de colmeia|Sentry, LDAP, AD com Kerberos, Ranger|
|Auditoria|Ambari, Cloudera Navigator, Ranger|
|Monitorização|Grafite, colecionada, estatísticas, Telegraf, InfluxDB|
|Alertas|Kapacitor, Prometeu, Datadog|
|Duração da retenção de dados| 3 anos, 5 anos|
|Administradores de clusters|Administrador Único, Administradores Múltiplos|

### <a name="project-details-questionnaire"></a>Questionário de detalhes do projeto

|**Pergunta**|**Exemplo**|**Resposta**|
|---|---|---|
|**Tópico**: **Cargas de Trabalho e Frequência**|||
|Trabalhos de MapReduce|10 empregos - duas vezes por dia||
|Trabalhos de colmeia|100 empregos - a cada hora||
|Trabalhos de lote de faíscas|50 empregos - a cada 15 minutos||
|Trabalhos de streaming de faíscas|5 empregos - a cada 3 minutos||
|Trabalhos estruturados de streaming|5 empregos - a cada minuto||
|Empregos de formação de modelos ML|2 empregos - uma vez por semana||
|Linguagens de Programação|Python, Scala, Java||
|Scripting|Shell||
|**Tópico**: **Dados**|||
|Origens de dados|Arquivos planos, Json, Kafka, RDBMS||
|Orquestração de dados|Fluxos de trabalho Oozie, Fluxo de Ar||
|Em memórias|Apache Ignite||
|Destinos de dados|HDFS, RDBMS, Kafka, MPP ||
|**Tópico**: **Dados de meta**|||
|Tipo DB de colmeia|Mysql||
|Número de metastões de Colmeia|2||
|Número de mesas de colmeia|100||
|Número de políticas ranger|20||
|Número de fluxos de trabalho da Oozie|100||
|**Tópico**: **Escala**|||
|Volume de dados, incluindo replicação|100 TB||
|Volume de ingestão diária|50 GB||
|Taxa de crescimento de dados|10% ao ano||
|Taxa de crescimento dos nóns de cluster|5% ao ano
|**Tópico**: **Utilização do cluster**|||
|CPU médio utilizado|60%||
|Memória média % utilizada|75%||
|Espaço de disco usado|75%||
|Rede média % utilizada|25%
|**Tópico**: **Staff**|||
|Número de Administradores|2||
|Número de Desenvolvedores|10||
|Número de utilizadores finais|100||
|Competências|Hadoop||
|Número de recursos disponíveis para os esforços de migração|2||
|**Tópico**: **Limitações**|||
|Limitações atuais|A latência é alta.||
|Desafios atuais|Problema de concordância||

### <a name="azure-requirements-questionnaire"></a>Questionário sobre requisitos Azure

|**Pergunta**|**Exemplo**|**Resposta**|
|---|---|---|
|**Tópico**: **Infraestrutura** |||
| Região Preferida|E.U.A Leste||
|VNet preferido?|Yes||
|HA / DR Necessário?|Yes||
|Integração com outros serviços em nuvem?|ADF||
|**Tópico**:   **Movimento de Dados**  |||
|Preferência inicial de carga|DistCp, Caixa de Dados, ADF, WANDisco||
|Delta de transferência de dados|DistCp||
|Transferência contínua de dados incrementais|DistCp||
|**Tópico**:   **Alerta de & de monitorização** |||
|Utilize & de monitorização do Azure alertando vs Integrar monitorização de terceiros|Utilizar alerta de & de monitorização Azure||
|**Tópico**:   **Preferências de segurança** |||
|Oleoduto de dados privado e protegido?|Yes||
|Cluster de domínios unidos (ESP)?|     Yes||
|Sincronização de anúncios no local para cloud?|     Yes||
|Número de utilizadores de AD para sincronizar?|          100||
|Ok para sincronizar senhas para a nuvem?|    Yes||
|Cloud apenas Utilizadores?|                 Yes||
|MFA necessária?|                       No|| 
|Requisitos de autorização de dados?|  Yes||
|Controlo de acesso baseado em funções?|        Yes||
|Auditoria necessária?|                  Yes||
|Encriptação de dados em repouso?|          Yes||
|Encriptação de dados em trânsito?|       Yes||
|**Tópico**:   **Preferências de re-arquitetura** |||
|Cluster único vs Tipos específicos de cluster|Tipos específicos de cluster||
|Armazenamento cotado vs armazenamento remoto?|Armazenamento remoto||
|O tamanho do cluster menor à medida que os dados são armazenados remotamente?|Tamanho menor do cluster||
|Utilizar múltiplos aglomerados menores em vez de um único aglomerado grande?|Use vários aglomerados menores||
|Usar uma metásta remota?|Yes||
|Partilhar metas-lojas entre diferentes clusters?|Yes||
|Desconstruir cargas de trabalho?|Substitua os empregos de Colmeia por empregos de faíscas||
|Utilizar a ADF para orquestração de dados?|Não||

## <a name="next-steps"></a>Próximos passos

Leia o próximo artigo desta série:

- [Melhores práticas de arquitetura para migração de Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-architecture.md)
