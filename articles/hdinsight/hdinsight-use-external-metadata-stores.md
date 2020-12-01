---
title: Utilize lojas de metadados externos - Azure HDInsight
description: Utilize lojas de metadados externos com clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 08/06/2020
ms.openlocfilehash: a001f3a13daf40a1af712f09e35d93fd045ea133
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350250"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Use external metadata stores in Azure HDInsight (Utilizar arquivos de metadados externos no Azure HDInsight)

O HDInsight permite-lhe assumir o controlo dos seus dados e metadados com lojas de dados externas. Esta funcionalidade está disponível para [metastore Apache Hive,](#custom-metastore) [metastóia Apache Oozie](#apache-oozie-metastore)e [base de dados Apache Ambari.](#custom-ambari-db)

A metastópia Apache Hive em HDInsight é uma parte essencial da arquitetura Apache Hadoop. Uma metástore é o repositório central de esquemas. A metástore é usada por outras grandes ferramentas de acesso a dados como Apache Spark, Interactive Query (LLAP), Presto ou Apache Pig. O HDInsight utiliza uma Base de Dados Azure SQL como metásta de Colmeia.

![HdInsight Hive Metadata Store Arquitetura](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Há duas formas de configurar uma meta-loja para os seus clusters HDInsight:

* [Metastlo padrão](#default-metastore)
* [Metástases personalizadas](#custom-metastore)

## <a name="default-metastore"></a>Metastlo padrão

Por padrão, o HDInsight cria uma meta-loja com cada tipo de cluster. Em vez disso, pode especificar uma meta-loja personalizada. A metástore predefinido inclui as seguintes considerações:

* Sem custo adicional. O HDInsight cria uma meta-loja com todos os tipos de cluster sem qualquer custo adicional para si.

* Cada metástore padrão faz parte do ciclo de vida do cluster. Quando elimina um cluster, a metástase correspondente e os metadados também são eliminados.

* Não pode partilhar a meta-loja padrão com outros clusters.

* A metástore predefinido é recomendada apenas para cargas de trabalho simples. Cargas de trabalho que não requerem múltiplos clusters e não precisam de metadados preservados para além do ciclo de vida do cluster.

> [!IMPORTANT]
> A metástore predefinida fornece uma Base de Dados Azure SQL com um **limite básico de DTU de nível 5 (não atualizável)**! Adequado para fins básicos de teste. Para cargas de trabalho grandes ou de produção, recomendamos migrar para uma meta-loja externa.

## <a name="custom-metastore"></a>Metástases personalizadas

O HDInsight também suporta metastões personalizadas, que são recomendadas para clusters de produção:

* Especifica a sua própria Base de Dados Azure SQL como a meta-loja.

* O ciclo de vida da metastore não está ligado a um ciclo de vida de clusters, para que possa criar e eliminar clusters sem perder metadados. Metadados como os seus esquemas de Hive persistirão mesmo depois de eliminar e recriar o cluster HDInsight.

* Uma metásta personalizada permite-lhe anexar vários clusters e tipos de clusters a essa meta-loja. Por exemplo, uma única meta-loja pode ser partilhada através de clusters Interactive Query, Hive e Spark em HDInsight.

* Você paga o custo de uma meta-loja (Azure SQL Database) de acordo com o nível de desempenho que você escolhe.

* Pode aumentar a meta-loja conforme necessário.

* O aglomerado e a metastore externa devem ser alojados na mesma região.

![Caso de utilização de loja de metadados de colmeia HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Criar e configurar base de dados Azure SQL para a metásta personalizada

Crie ou tenha uma Base de Dados Azure SQL existente antes de configurar uma metásta de Hive personalizada para um cluster HDInsight.  Para obter mais informações, consulte [Quickstart: Crie uma única base de dados na Base de Dados Azure SQL](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal).

Ao criar o cluster, o serviço HDInsight precisa de se conectar à metásta externa e verificar as suas credenciais. Configure as regras de firewall da Base de Dados Azure SQL para permitir que os serviços e recursos da Azure acedam ao servidor. Ative esta opção no portal Azure selecionando **a firewall do servidor set**. Em seguida, **selecione No** under **Deny public network access**, e **Yes** under **Allow Azure services and resources to access this server** for Azure SQL Database. Para obter mais informações, consulte [Criar e gerir as regras de firewall IP](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

Os pontos finais privados para lojas SQL só são suportados nos clusters criados com `outbound` o ResourceProviderConnection. Para saber mais, consulte esta [documentação.](./hdinsight-private-link.md)

![definir botão de firewall do servidor](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![permitir acesso de serviços azure](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Selecione uma meta-loja personalizada durante a criação do cluster

Pode apontar o seu cluster para uma Base de Dados Azure SQL previamente criada a qualquer momento. Para a criação de clusters através do portal, a opção é especificada a partir das **definições de Armazenamento > Metastore**.

![HdInsight Hive Metadata Store Portal Azure](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-guidelines"></a>Diretrizes da metásta rodoviária de colmeia

> [!NOTE]
> Utilize sempre que possível uma metásta personalizada para ajudar a separar os recursos de computação (o seu cluster de execução) e metadados (armazenados na metastore). Comece com o nível S2, que fornece 50 DTU e 250 GB de armazenamento. Se vir um estrangulamento, pode escalar a base de dados.

* Se pretender que vários clusters HDInsight acedam a dados separados, utilize uma base de dados separada para a metastore de cada cluster. Se partilhar uma meta-loja em vários clusters HDInsight, significa que os clusters usam os mesmos metadados e ficheiros de dados subjacentes ao utilizador.

* Apoie periodicamente a sua metastore personalizada. A Azure SQL Database gera cópias de segurança automaticamente, mas o prazo de retenção de backup varia. Para obter mais informações, consulte [as cópias de segurança automáticas da Base de Dados SQL.](../azure-sql/database/automated-backups-overview.md)

* Localize o seu cluster de metastões e HDInsight na mesma região. Esta configuração fornecerá o desempenho mais alto e os mais baixos encargos de saída de rede.

* Monitorize a sua meta-loja para obter desempenho e disponibilidade utilizando ferramentas de monitorização da base de dados Azure SQL ou registos do Monitor Azure.

* Quando uma nova versão mais alta do Azure HDInsight é criada contra uma base de dados de metástaria personalizada existente, o sistema atualiza o esquema da metastore. A atualização é irreversível sem restaurar a base de dados a partir de cópias de segurança.

* Se partilhar uma meta-loja em vários clusters, certifique-se de que todos os clusters são a mesma versão HDInsight. Diferentes versões de Hive usam diferentes esquemas de base de dados de metastore. Por exemplo, não é possível partilhar uma meta-loja através da Hive 2.1 e dos clusters versados da Hive 3.1.

* No HDInsight 4.0, a Spark e a Hive utilizam catálogos independentes para aceder às tabelas SparkSQL ou Hive. Uma mesa criada por Spark vive no catálogo Spark. Uma mesa criada pela Hive vive no catálogo da Hive. Este comportamento é diferente do HDInsight 3.6 onde a Hive e a Spark partilhavam o catálogo comum. A integração de colmeias e faíscas em HDInsight 4.0 depende do Conector do Armazém da Colmeia (HWC). A HWC funciona como uma ponte entre a Spark e a Hive. [Saiba mais sobre o Conector do Armazém da Colmeia.](../hdinsight/interactive-query/apache-hive-warehouse-connector.md)

* Em HDInsight 4.0 se quiser Partilhar a meta-loja entre a Hive e a Spark, pode fazê-lo alterando a propriedade metastore.catalog.default para colmeia no seu cluster Spark. Você pode encontrar esta propriedade em Ambari Advanced spark2-hive-site-override. É importante entender que a partilha de metastões só funciona para mesas de colmeia externas, isto não funcionará se tiver mesas de colmeias internas/geridas ou mesas ACID.  

## <a name="apache-oozie-metastore"></a>Metastóia Apache Oozie

O Apache Oozie é um sistema de coordenação de fluxos de trabalho que gere as tarefas do Hadoop. Oozie apoia os empregos de Hadoop para Apache MapReduce, Pig, Hive, e outros.  Oozie usa uma meta-loja para armazenar detalhes sobre fluxos de trabalho. Para aumentar o desempenho ao utilizar o Oozie, pode utilizar a Base de Dados Azure SQL como uma metástaria personalizada. A metástore fornece acesso aos dados de trabalho da Oozie depois de eliminar o seu cluster.

Para obter instruções sobre a criação de uma meta-loja Oozie com base de dados Azure SQL, consulte [Use Apache Oozie para fluxos de trabalho](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>Ambari DB Personalizado

Para utilizar a sua própria base de dados externa com Apache Ambari na HDInsight, consulte [a base de dados Personalizada Apache Ambari.](hdinsight-custom-ambari-db.md)

## <a name="next-steps"></a>Passos seguintes

* [Configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka e muito mais](./hdinsight-hadoop-provision-linux-clusters.md)