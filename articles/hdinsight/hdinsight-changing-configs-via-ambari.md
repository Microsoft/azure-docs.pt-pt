---
title: Otimizar clusters com Apache Ambari em Azure HDInsight
description: Utilize a UI web Apache Ambari para configurar e otimizar os clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/04/2020
ms.openlocfilehash: 52eb1b6c89ff02cb44fe731c2463ab02c284f26c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86086454"
---
# <a name="optimize-clusters-with-apache-ambari-in-azure-hdinsight"></a>Otimizar clusters com Apache Ambari em Azure HDInsight

O HDInsight fornece clusters Apache Hadoop para aplicações de processamento de dados em larga escala. Gerir, monitorizar e otimizar estes complexos clusters multi-nós pode ser um desafio. Apache Ambari é uma interface web para gerir e monitorizar clusters HDInsight Linux.

Para uma introdução à utilização do Ambari Web UI, consulte [gerir os clusters HDInsight utilizando o Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md)

Faça login em Ambari `https://CLUSTERNAME.azurehdidnsight.net` com as suas credenciais de cluster. O ecrã inicial apresenta um painel de visão geral.

![Painel de utilizadores apache Ambari exibido](./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png)

A UI web Ambari é usada para gerir anfitriões, serviços, alertas, configurações e vistas. O Ambari não pode ser usado para criar um cluster HDInsight ou serviços de upgrade. Também não é possível gerir pilhas e versões, desativar ou reativar anfitriões, ou adicionar serviços ao cluster.

## <a name="manage-your-clusters-configuration"></a>Gerir a configuração do seu cluster

As definições de configuração ajudam a sintonizar um determinado serviço. Para modificar as definições de configuração de um serviço, selecione o serviço a partir da barra lateral **de Serviços** (à esquerda). Em seguida, navegue para o **separador Configs** na página de detalhes de serviço.

![Barra lateral dos Serviços Apache Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png)

## <a name="modify-namenode-java-heap-size"></a>Modificar o tamanho da pilha de Java nome

O tamanho da pilha NameNode Java depende de muitos fatores, como a carga no cluster. Além disso, o número de ficheiros e o número de blocos. O tamanho padrão de 1 GB funciona bem com a maioria dos clusters, embora algumas cargas de trabalho possam requerer mais ou menos memória.

Para modificar o tamanho da pilha NameNode Java:

1. Selecione **HDFS** da barra lateral de serviços e navegue para o **separador Configs.**

    ![Configuração Apache Ambari HDFS](./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png)

1. Encontre a definição **NomeNode Java tamanho da pilha**. Também pode utilizar a caixa de texto do **filtro** para escrever e encontrar uma definição específica. Selecione o ícone da **caneta** ao lado do nome de definição.

    ![Apache Ambari NameNode Java tamanho de pilha](./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png)

1. Digite o novo valor na caixa de texto e, em seguida, prima **Enter** para guardar a alteração.

    ![Ambari Edit NameNode Java heap size1](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png)

1. O tamanho da pilha NameNode Java é alterado para 1 GB a partir de 2 GB.

    ![Nome editadoNode Java tamanho 2](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Guarde as suas alterações clicando no botão verde **Guarde** no topo do ecrã de configuração.

    !['Apache Ambari salvar configurações'](./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png)

## <a name="next-steps"></a>Passos seguintes

* [Gerir clusters HDInsight com a Web UI Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Otimizar Apache HBase](./optimize-hbase-ambari.md)
* [Otimizar Apache Hive](./optimize-hive-ambari.md)
* [Otimizar Apache Pig](./optimize-pig-ambari.md)
