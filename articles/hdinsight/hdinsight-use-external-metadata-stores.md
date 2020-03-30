---
title: Utilize lojas externas de metadados - Azure HDInsight
description: Utilize lojas de metadados externos com clusters Azure HDInsight e boas práticas.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: edb2d256d3e5d98c52dbdff1162e0e030ebe2be3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272165"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Use external metadata stores in Azure HDInsight (Utilizar arquivos de metadados externos no Azure HDInsight)

O HDInsight permite-lhe controlar os seus dados e metadados através da implementação de soluções de metadados chave e bases de dados de gestão para lojas de dados externas. Esta funcionalidade encontra-se atualmente disponível para [metastore Apache Hive,](#custom-metastore) [metastore Apache Oozie](#apache-oozie-metastore) e base de [dados Apache Ambari.](#custom-ambari-db)

A metaloja Apache Hive em HDInsight é uma parte essencial da arquitetura Apache Hadoop. Uma metaloja é o repositório de esquemacentral central que pode ser usado por outras ferramentas de acesso a big data, tais como Apache Spark, Interactive Query (LLAP), Presto ou Apache Pig. O HDInsight utiliza uma Base de Dados Azure SQL como metaloja da Hive.

![Arquitetura da loja de metadados hive HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Existem duas formas de configurar uma metaloja para os seus clusters HDInsight:

* [Metaloja padrão](#default-metastore)
* [Metaloja personalizada](#custom-metastore)

## <a name="default-metastore"></a>Metaloja padrão

Por padrão, o HDInsight cria uma metaloja com cada tipo de cluster. Em vez disso, pode especificar uma metaloja personalizada. A metaloja predefinida inclui as seguintes considerações:

* Sem custos adicionais. O HDInsight cria uma metaloja com cada tipo de cluster sem qualquer custo adicional para si.

* Cada metaloja padrão faz parte do ciclo de vida do cluster. Quando elimina um cluster, a metaloja correspondente e os metadados também são eliminados.

* Não pode partilhar a metaloja padrão com outros clusters.

* A metaloja predefinida utiliza o Azure SQL DB básico, que tem um limite de cinco DTU (unidade de transação de base de dados).
Esta metaloja padrão é normalmente usada para cargas de trabalho relativamente simples que não requerem vários clusters e não precisam de metadados preservados para além do ciclo de vida do cluster.

## <a name="custom-metastore"></a>Metaloja personalizada

O HDInsight também suporta metalojas personalizadas, que são recomendadas para clusters de produção:

* Especifica a sua própria Base de Dados Azure SQL como metaloja.

* O ciclo de vida da metaloja não está ligado a um ciclo de vida de clusters, para que possa criar e eliminar clusters sem perder metadados. Metadados como os seus schemas da Hive persistirão mesmo depois de eliminar e recriar o cluster HDInsight.

* Uma metaloja personalizada permite-lhe anexar vários clusters e tipos de cluster saqueados a essa metaloja. Por exemplo, uma única metaloja pode ser partilhada em clusters De Consulta Interativa, Hive e Spark em HDInsight.

* Paga-se pelo custo de uma metaloja (Azure SQL DB) de acordo com o nível de desempenho que escolher.

* Pode escalar a metaloja conforme necessário.

* O cluster e a metaloja externa devem ser alojados na mesma região.

![Caso de utilização da loja de metadados hive HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Criar e config Azure SQL Base de dados para a metaloja personalizada

Você precisa criar ou ter uma base de dados Azure SQL existente antes de configurar uma metaloja de Colmeia personalizada para um cluster HDInsight.  Para mais informações, consulte [Quickstart: Crie uma única base de dados em Azure SQL DB](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal).

Para garantir que o seu cluster HDInsight pode aceder à base de dados Azure SQL conectada, configure as regras de firewall da Base de Dados Azure SQL para permitir que os serviços e recursos do Azure acedam ao servidor.

Pode ativar esta opção no portal Azure clicando na **firewall do servidor set**, e clicando em **cima** de **serviços e recursos do Allow Azure para aceder a este servidor** para o servidor ou base de dados Azure SQL. Para mais informações, consulte [Criar e gerir as regras de firewall IP](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

![definir botão de firewall do servidor](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![permitir o acesso dos serviços azure](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Selecione uma metaloja personalizada durante a criação de cluster

Pode indicar o seu cluster para uma base de dados Azure SQL previamente criada durante a criação do cluster, ou pode configurar a Base de Dados SQL após a criação do cluster. Esta opção é especificada com as **configurações de Armazenamento > Metastore,** ao mesmo tempo que cria um novo aglomerado Hadoop, Spark ou Hive interativo do portal Azure.

![HDInsight Hive Metadate Store Portal Azure](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-best-practices"></a>As melhores práticas da metaloja da colmeia

Aqui estão algumas boas práticas gerais da metastore Da Hive HDInsight:

* Utilize uma metaloja personalizada sempre que possível, para ajudar a separar os recursos computacionais (o seu cluster de execução) e os metadados (armazenados na metaloja).

* Comece com um nível S2, que fornece 50 DTU e 250 GB de armazenamento. Se vir um estrangulamento, pode aumentar a base de dados.

* Se pretender que vários clusters HDInsight acedam a dados separados, utilize uma base de dados separada para a metaloja em cada cluster. Se partilhar uma metaloja em vários clusters HDInsight, significa que os clusters utilizam os mesmos metadados e ficheiros de dados subjacentes aos utilizadores.

* Volte periodicamente à sua metaloja personalizada. A Base de Dados Azure SQL gera cópias de segurança automaticamente, mas o prazo de retenção de cópia seleções varia. Para mais informações, consulte [Saiba mais sobre cópias automáticas](../sql-database/sql-database-automated-backups.md)de backups da Base de Dados SQL .

* Localize o seu cluster metastore e HDInsight na mesma região, para obter os custos de maior desempenho e de egress de rede mais baixos.

* Monitorize a sua metaloja para obter desempenho e disponibilidade utilizando ferramentas de monitorização de bases de dados Azure SQL, como o portal Azure ou os registos do Monitor Azure.

* Quando uma nova versão mais alta do Azure HDInsight é criada contra uma base de dados de metaloja personalizada existente, o sistema atualiza o esquema da metaloja, o que é irreversível sem restaurar a base de dados da cópia de segurança.

* Se partilhar uma metaloja em vários clusters, certifique-se de que todos os clusters são a mesma versão HDInsight. Diferentes versões da Hive usam diferentes esquemas de base de dados de metastore. Por exemplo, não é possível partilhar uma metaloja através da Hive 2.1 e da Hive 3.1.

* No HDInsight 4.0, a Spark e a Hive utilizam catálogos independentes para aceder a mesas SparkSQL ou Hive. Uma tabela criada pela Spark reside no catálogo Spark. Uma tabela criada pela Hive reside no catálogo da Colmeia. Isto é diferente do HDInsight 3.6 onde a Hive e a Spark partilharam o catálogo comum. A Integração da Hive e Spark no HDInsight 4.0 depende do Conector de Armazém hive (HWC). A HWC funciona como uma ponte entre A Faísca e a Colmeia. Saiba mais sobre o [Conector de Armazém da Colmeia.](../hdinsight/interactive-query/apache-hive-warehouse-connector.md)

## <a name="apache-oozie-metastore"></a>Metastore Apache Oozie

O Apache Oozie é um sistema de coordenação de fluxos de trabalho que gere as tarefas do Hadoop.  Oozie apoia os empregos de Hadoop para Apache MapReduce, Pig, Hive, entre outros.  A Oozie usa uma metaloja para armazenar detalhes sobre fluxos de trabalho atuais e concluídos. Para aumentar o desempenho ao utilizar o Oozie, pode utilizar a Base de Dados Azure SQL como uma metaloja personalizada. A metaloja também pode fornecer acesso aos dados de trabalho da Oozie depois de eliminar o seu cluster.

Para obter instruções sobre a criação de uma metaloja Oozie com base de dados Azure SQL, consulte [Use Apache Oozie para fluxos de trabalho](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>Ambari DB Personalizado

Para utilizar a sua própria base de dados externa com Apache Ambari no HDInsight, consulte a base de [dados Custom Apache Ambari](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Passos seguintes

* [Configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka e muito mais](./hdinsight-hadoop-provision-linux-clusters.md)
