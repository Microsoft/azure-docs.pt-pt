---
title: Usar repositórios de metadados externos-Azure HDInsight
description: Use armazenamentos de metadados externos com clusters do Azure HDInsight e as práticas recomendadas.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: a495935216cb91a282cc3dbabafbc96a531dde91
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72800125"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Usar repositórios de metadados externos no Azure HDInsight

O metastore Apache Hive no HDInsight é uma parte essencial da arquitetura de Apache Hadoop. Um metastore é o repositório de esquema central que pode ser usado por outras ferramentas de acesso Big Data, como Apache Spark, LLAP (consulta interativa), presto ou Apache Pig. O HDInsight usa um banco de dados SQL do Azure como o metastore do Hive.

![Arquitetura do repositório de metadados do hive do HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Há duas maneiras de configurar um metastore para seus clusters HDInsight:

* [Metastore padrão](#default-metastore)
* [Metastore personalizado](#custom-metastore)

## <a name="default-metastore"></a>Metastore padrão

Por padrão, o HDInsight cria um metastore com cada tipo de cluster. Em vez disso, você pode especificar um metastore personalizado. O metastore padrão inclui as seguintes considerações:

* Sem custo adicional. O HDInsight cria um metastore com cada tipo de cluster sem nenhum custo adicional.

* Cada metastore padrão faz parte do ciclo de vida do cluster. Quando você exclui um cluster, o metastore correspondente e os metadados também são excluídos.

* Não é possível compartilhar o metastore padrão com outros clusters.

* O metastore padrão usa o BD SQL básico do Azure, que tem um limite de DTU (unidade de transação de banco de dados).
Esse metastore padrão é normalmente usado para cargas de trabalho relativamente simples que não exigem vários clusters e não precisam de metadados preservados além do ciclo de vida do cluster.

## <a name="custom-metastore"></a>Metastore personalizado

O HDInsight também dá suporte a metastores personalizados, que são recomendados para clusters de produção:

* Você especifica seu próprio banco de dados SQL do Azure como o metastore.

* O ciclo de vida do metastore não está vinculado a um ciclo de vida de clusters, para que você possa criar e excluir clusters sem perder metadados. Os metadados, como os esquemas do hive, persistirão mesmo depois que você excluir e recriar o cluster HDInsight.

* Um metastore personalizado permite anexar vários clusters e tipos de cluster a esse metastore. Por exemplo, um único metastore pode ser compartilhado entre clusters de consulta interativa, Hive e Spark no HDInsight.

* Você paga pelo custo de um metastore (banco de BD SQL do Azure) de acordo com o nível de desempenho escolhido.

* Você pode escalar verticalmente o metastore conforme necessário.

![Caso de uso do repositório de metadados do hive do HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Selecionar um metastore personalizado durante a criação do cluster

Você pode apontar seu cluster para um banco de dados SQL do Azure criado anteriormente durante a criação do cluster ou pode configurar o banco de dados SQL após a criação do cluster. Essa opção é especificada com as **configurações de metastore > de armazenamento** ao criar um novo cluster Hadoop, Spark ou Hive interativo do portal do Azure.

![portal do Azure de repositório de metadados do hive do HDInsight](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

Você também pode adicionar clusters adicionais a um metastore personalizado de portal do Azure ou de configurações do Ambari (Hive > avançado)

![Repositório de metadados do hive do HDInsight Ambari](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Práticas recomendadas de metastore do Hive

Aqui estão algumas práticas recomendadas de metastore do Hive do HDInsight geral:

* Use um metastore personalizado sempre que possível, para ajudar a separar recursos de computação (seu cluster em execução) e metadados (armazenados no metastore).

* Comece com uma camada S2, que fornece 50 DTU e 250 GB de armazenamento. Se você vir um afunilamento, poderá dimensionar o banco de dados.

* Se você pretende que vários clusters HDInsight acessem dados separados, use um banco de dado separado para o metastore em cada cluster. Se você compartilhar um metastore em vários clusters HDInsight, isso significará que os clusters usam os mesmos metadados e arquivos de dados de usuário subjacentes.

* Faça backup de seu metastore personalizado periodicamente. O banco de dados SQL do Azure gera backups automaticamente, mas o prazo de retenção de backup varia. Para obter mais informações, consulte [saiba mais sobre backups automáticos do banco de dados SQL](../sql-database/sql-database-automated-backups.md).

* Localize seu metastore e o cluster HDInsight na mesma região, para obter o melhor desempenho e encargos de saída de rede mais baixos.

* Monitore seu metastore para obter desempenho e disponibilidade usando as ferramentas de monitoramento do banco de dados SQL do Azure, como os logs de portal do Azure ou Azure Monitor.

* Quando uma nova versão mais recente do Azure HDInsight é criada em um banco de dados de metastore personalizado existente, o sistema atualiza o esquema do metastore, que é irreversível sem restaurar o banco de dados do backup.

* Se você compartilhar um metastore em vários clusters, verifique se todos os clusters são da mesma versão do HDInsight. Versões diferentes do hive usam esquemas de banco de dados de metastore diferentes. Por exemplo, você não pode compartilhar um metastore em clusters do hive 1,2 e com versão do hive 2,1.

* No HDInsight 4,0, o Spark e o hive usam catálogos independentes para acessar tabelas SparkSQL ou Hive. Uma tabela criada pelo Spark reside no catálogo do Spark. Uma tabela criada pelo Hive reside no catálogo do hive. Isso é diferente do HDInsight 3,6, em que o hive e o Spark compartilham o catálogo comum. A integração do hive e do Spark no HDInsight 4,0 depende do conector de depósito do hive (HWC). O HWC funciona como uma ponte entre o Spark e o hive. [Saiba mais sobre o conector do depósito do hive](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

## <a name="apache-oozie-metastore"></a>Metastore do Apache Oozie

O Apache Oozie é um sistema de coordenação de fluxo de trabalho que gerencia trabalhos do Hadoop.  O Oozie dá suporte a trabalhos do Hadoop para Apache MapReduce, Pig, Hive e outros.  O Oozie usa um metastore para armazenar detalhes sobre os fluxos de trabalho atuais e concluídos. Para aumentar o desempenho ao usar o Oozie, você pode usar o banco de dados SQL do Azure como um metastore personalizado. O metastore também pode fornecer acesso a dados de trabalho do Oozie depois de excluir o cluster.

Para obter instruções sobre como criar um metastore do Oozie com o banco de dados SQL do Azure, consulte [usar o Apache Oozie para fluxos de trabalho](hdinsight-use-oozie-linux-mac.md).

## <a name="next-steps"></a>Passos seguintes

* [Configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka e muito mais](./hdinsight-hadoop-provision-linux-clusters.md)
