---
title: Otimizar clusters com Apache Ambari em Azure HDInsight
description: Utilize a UI web Apache Ambari para configurar e otimizar os clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/04/2020
ms.openlocfilehash: a819c3a57efdc0ae87cf969fd7471818c51895f6
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793164"
---
# <a name="optimize-clusters-with-apache-ambari-in-azure-hdinsight"></a>Otimizar clusters com Apache Ambari em Azure HDInsight

O HDInsight fornece clusters Apache Hadoop para aplicações de processamento de dados em larga escala. Gerir, monitorizar e otimizar estes complexos clusters multi-nó pode ser um desafio. Apache Ambari é uma interface web para gerir e monitorizar os clusters HDInsight Linux.

Para uma introdução à utilização do Ambari Web UI, consulte [gerir os clusters HDInsight utilizando o Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md)

Faça login em `https://CLUSTERNAME.azurehdidnsight.net` Ambari com as suas credenciais de cluster. O ecrã inicial apresenta um painel de visão geral.

![Painel de instrumentos de utilizador Apache Ambari apresentado](./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png)

O Ambari web UI é usado para gerir anfitriões, serviços, alertas, configurações e vistas. Ambari não pode ser usado para criar um cluster HDInsight, ou serviços de upgrade. Também não pode gerir pilhas e versões, desativar ou recomissar anfitriões, ou adicionar serviços ao cluster.

## <a name="manage-your-clusters-configuration"></a>Gerencie a configuração do seu cluster

As definições de configuração ajudam a afinar um determinado serviço. Para modificar as definições de configuração de um serviço, selecione o serviço a partir da barra lateral dos **Serviços** (à esquerda). Em seguida, navegue para o separador **Configs** na página de detalhe de serviço.

![Barra lateral dos Serviços Apache Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png)

## <a name="modify-namenode-java-heap-size"></a>Modificar nomeNode tamanho monte java

O tamanho da pilha NameNode Java depende de muitos fatores, como a carga no cluster. Além disso, o número de ficheiros e os números de blocos. O tamanho padrão de 1 GB funciona bem com a maioria dos clusters, embora algumas cargas de trabalho possam requerer mais ou menos memória.

Para modificar o tamanho da pilha NameNode Java:

1. Selecione **HDFS** da barra lateral dos Serviços e navegue para o separador **Configs.**

    ![Configuração Apache Ambari HDFS](./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png)

1. Encontre a definição **NomeNode Java heap size**. Também pode utilizar a caixa de texto do **filtro** para escrever e encontrar uma determinada definição. Selecione o ícone da **caneta** ao lado do nome de definição.

    ![Apache Ambari NameNode Java heap tamanho](./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png)

1. Digite o novo valor na caixa de texto e, em seguida, prima **Enter** para salvar a alteração.

    ![Ambari Editar NameNode Java heap size1](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png)

1. O tamanho da pilha NameNode Java é alterado para 1 GB a partir de 2 GB.

    ![Nome editado Nome Node Java heap tamanho2](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Guarde as suas alterações clicando no botão **Verde Guardar** na parte superior do ecrã de configuração.

    !['Apache Ambari salvar configurações'](./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png)

## <a name="next-steps"></a>Passos seguintes

* [Gerir clusters HDInsight com a UI web Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Otimizar Apache HBase](./optimize-hbase-ambari.md)
* [Otimizar a Colmeia Apache](./optimize-hive-ambari.md)
* [Otimizar o Porco Apache](./optimize-pig-ambari.md)
