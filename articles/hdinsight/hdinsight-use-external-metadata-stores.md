---
title: Utilizar arquivos de metadados externos - Azure HDInsight
description: Utilize arquivos de metadados externos com clusters do HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 3daa71c91d1e49a497a979b9b5b89df1fcb9418c
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889686"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Utilizar arquivos de metadados externos no Azure HDInsight

O metastore Apache Hive no HDInsight é uma parte essencial da arquitetura do Apache Hadoop. Um metastore é o repositório do esquema central que pode ser utilizado por outras ferramentas de acesso de grandes volumes de dados, como o Apache Spark, consulta interativa (LLAP), Presto ou Apache Pig. HDInsight utiliza uma base de dados do SQL do Azure como o metastore Hive.

![Arquitetura de Store de metadados do Hive do HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Existem duas formas, que pode configurar um metastore para os seus clusters do HDInsight:

* [Metastore predefinida](#default-metastore)
* [Metastore personalizado](#custom-metastore)

## <a name="default-metastore"></a>Metastore predefinida

Por predefinição, o HDInsight cria um metastore com todos os tipos de cluster. Em vez disso, pode especificar um metastore personalizado. O metastore predefinida inclui as seguintes considerações:
- Sem custos adicionais. HDInsight cria um metastore com todos os tipos de cluster sem qualquer custo adicional para.
- Cada metastore predefinida faz parte do ciclo de vida do cluster. Quando eliminar um cluster, o metastore correspondente e os metadados também são eliminados.
- Não é possível partilhar o metastore predefinida com outros clusters.
- O metastore predefinida utiliza o básico BD SQL do Azure, que tem um limite relativamente cinco do DTU (unidade de transação de base de dados).
Este metastore predefinida é normalmente utilizado para cargas de trabalho relativamente simples, que não necessitam de vários clusters e não precisam de metadados preservados para além do ciclo de vida do cluster.


## <a name="custom-metastore"></a>Metastore personalizado

HDInsight também suporta metastores personalizada, que são recomendados para clusters de produção:
- Especificar sua própria base de dados do SQL do Azure como o metastore.
- O ciclo de vida do metastore não está associado a um ciclo de vida de clusters, para que possa criar e eliminar os clusters sem perder os metadados. Metadados, como seus esquemas de Hive persistirá mesmo depois de eliminar e voltar a criar o cluster do HDInsight.
- Um metastore personalizado permite-lhe ligar vários clusters e os tipos de cluster para esse metastore. Por exemplo, um metastore único pode ser compartilhado em clusters do Interactive Query, o Hive e o Spark no HDInsight.
- Paga pelo custo de um metastore (Azure SQL DB), de acordo com o nível de desempenho que escolher.
- Pode aumentar verticalmente o metastore conforme necessário.

![Caso de utilização do HDInsight Hive metadados Store](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)


### <a name="select-a-custom-metastore-during-cluster-creation"></a>Selecionar um metastore personalizado durante a criação do cluster

Pode apontar o seu cluster para uma base de dados de SQL do Azure criado anteriormente durante a criação do cluster, ou pode configurar a base de dados SQL após a criação do cluster. Esta opção é especificada com o armazenamento > definições de Metastore ao criar um novo Hadoop, Spark ou interativas do Hive cluster a partir do portal do Azure.

![Portal do Azure de Store de metadados do Hive do HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-azure-portal.png)

Também pode adicionar clusters adicionais a um metastore personalizado no portal do Azure ou das configurações de Ambari (Hive > Avançadas)

![Ambari Store de metadados do Hive do HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Melhores práticas de metastore de Hive

Aqui estão alguns geral Hive do HDInsight metastore melhores práticas:

- Utilize um metastore personalizado sempre que possível, para ajudar a recursos de computação separada (seu cluster em execução) e metadados (armazenado no metastore).
- Comece com um escalão S2, que fornece 50 DTUS e 250 GB de armazenamento. Se vir um estrangulamento, pode dimensionar a base de dados.
- Se pretende vários clusters do HDInsight para aceder a dados separado, utilize uma base de dados separado para o metastore em cada cluster. Se partilhar um metastore em vários clusters do HDInsight, significa que os clusters utilizam os mesmos metadados e arquivos de dados de utilizador subjacentes.
- Criar cópias de segurança seu metastore personalizado periodicamente. Base de dados SQL do Azure gera as cópias de segurança automaticamente, mas o período de tempo de retenção de cópia de segurança varia. Para obter mais informações, consulte [Saiba mais sobre cópias de segurança de base de dados SQL automáticas](../sql-database/sql-database-automated-backups.md).
- Localize o metastore e o cluster do HDInsight na mesma região, para um desempenho mais elevado e custos de saída de rede mais baixos.
- Monitorize o metastore para desempenho e disponibilidade usando ferramentas de monitorização de base de dados SQL do Azure, como o portal do Azure ou os registos do Azure Monitor.
- Quando uma versão nova e superior do Azure HDInsight é criada num banco de dados existente do metastore personalizado, o sistema atualiza o esquema de metastore, que é irreversível sem restaurar a base de dados de cópia de segurança.
- Se partilhar um metastore em vários clusters, certifique-se de que todos os clusters são a mesma versão do HDInsight. Diferentes versões de Hive usam esquemas de banco de dados da metastore diferentes. Por exemplo, é possível partilhar um metastore em clusters com a versão 1.2 do Hive e Hive 2.1. 

##  <a name="apache-oozie-metastore"></a>O Apache Oozie Metastore

Apache Oozie é um sistema de coordenação do fluxo de trabalho que gere as tarefas do Hadoop.  Oozie suporta tarefas do Hadoop para Apache MapReduce, Pig, Hive e outras pessoas.  Oozie utiliza um metastore para armazenar detalhes sobre os fluxos de trabalho atuais e concluídos. Para aumentar o desempenho ao utilizar o Oozie, pode utilizar a base de dados do Azure SQL como um metastore personalizado. O metastore também pode fornecer acesso aos dados da tarefa de Oozie depois de eliminar o cluster.

Para obter instruções sobre como criar um metastore Oozie com o Azure SQL Database, consulte [utilização Apache Oozie para fluxos de trabalho](hdinsight-use-oozie-linux-mac.md).

## <a name="next-steps"></a>Passos Seguintes

- [Configurar clusters no HDInsight com o Apache Hadoop, Apache Spark, Apache Kafka e muito mais](./hdinsight-hadoop-provision-linux-clusters.md)
