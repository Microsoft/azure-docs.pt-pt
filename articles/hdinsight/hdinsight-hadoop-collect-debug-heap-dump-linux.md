---
title: Ativar capturas de área dinâmica para dados dos serviços de Apache Hadoop no HDInsight - Azure
description: Ative capturas de área dinâmica para dados para os serviços do Apache Hadoop de clusters do HDInsight baseado em Linux para depuração e análise.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 62c9dcc039c68b0b6c8b8bf29ed9f13f88936723
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059582"
---
# <a name="enable-heap-dumps-for-apache-hadoop-services-on-linux-based-hdinsight"></a>Ativar capturas de área dinâmica para dados dos serviços de Apache Hadoop no HDInsight baseado em Linux

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Capturas de área dinâmica para dados contém um instantâneo de memória do aplicativo, incluindo os valores das variáveis no momento que a captura foi criada. Portanto, eles são úteis para diagnosticar problemas que ocorrem no tempo de execução.

## <a name="whichServices"></a>Serviços

Pode ativar os registos da área dinâmica para os seguintes serviços:

* **O Apache hcatalog** -tempelton
* **Ramo de registo do Apache** -hiveserver2, metastore, derbyserver
* **mapreduce** -jobhistoryserver
* **Apache yarn** -resourcemanager nodemanager, timelineserver
* **Apache hdfs** -datanode secondarynamenode, namenode

Também pode ativar os registos da área dinâmica para o mapa e reduzir processos executaram pelo HDInsight.

## <a name="configuration"></a>Configuração de despejo de área dinâmica para dados de compreensão

Registos da área dinâmica estão ativados por passar as opções (por vezes conhecida como opta ativamente por participar, ou parâmetros) para o JVM quando um serviço é iniciado. Para a maioria [Apache Hadoop](https://hadoop.apache.org/) serviços, pode modificar o script de shell utilizado para iniciar o serviço para transmitir essas opções.

Cada script, existe uma exportação para  **\* \_OPTS**, que contém as opções passadas para o JVM. Por exemplo, no **hadoop env.sh** script, a linha que começa com `export HADOOP_NAMENODE_OPTS=` contém as opções para o serviço de NameNode.

Mapa e redução processos são ligeiramente diferentes, como essas operações são um processo subordinado do serviço do MapReduce. Cada um mapa ou reduzir o processo é executado num contentor subordinado, e existem duas entradas que contêm as opções de JVM. Ambos contidas no **mapred site**:

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [!NOTE]  
> Recomendamos que utilize [Apache Ambari](https://ambari.apache.org/) para modificar os scripts e de definições de site mapred, como Ambari lidar com a replicação de alterações em todos os nós do cluster. Consulte a [utilizando o Apache Ambari](#using-apache-ambari) secção para obter passos específicos.

### <a name="enable-heap-dumps"></a>Ativar capturas de área dinâmica para dados

A seguinte opção permite que os registos da área dinâmica quando ocorre um OutOfMemoryError:

    -XX:+HeapDumpOnOutOfMemoryError

O **+** indica que esta opção está ativada. A predefinição é Desativado.

> [!WARNING]  
> Capturas de área dinâmica para dados não estão ativadas para serviços do Hadoop no HDInsight por predefinição, como os ficheiros de informação podem ser grandes. Se habilitá-los para resolução de problemas, lembre-se de desabilitá-los depois reproduzida o problema e recolha de ficheiros de informação.

### <a name="dump-location"></a>Localização de cópia de segurança

A localização predefinida para o ficheiro de informação é o atual diretório de trabalho. Pode controlar onde o ficheiro é armazenado com a seguinte opção:

    -XX:HeapDumpPath=/path

Por exemplo, usando `-XX:HeapDumpPath=/tmp` faz com que as informações sejam armazenados no diretório /tmp dos.

### <a name="scripts"></a>Scripts

Também pode acionar um script quando uma **OutOfMemoryError** ocorre. Por exemplo, acionar uma notificação para que saiba o que ocorreu o erro. Utilize a seguinte opção para acionar um script numa __OutOfMemoryError__:

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]  
> Como o Apache Hadoop é um sistema distribuído, qualquer script usado deve ser colocado em todos os nós do cluster que o serviço é executado no.
> 
> O script deve também estar numa localização acessível pela conta de serviço é executado como e tem de fornecer permissões de execução. Por exemplo, pode desejar armazenar scripts no `/usr/local/bin` e utilize `chmod go+rx /usr/local/bin/filename.sh` para conceder a leitura e permissões de execução.

## <a name="using-apache-ambari"></a>Com o Apache Ambari

Para modificar a configuração para um serviço, utilize os seguintes passos:

1. Abra a IU web do Ambari para o seu cluster. O URL é https://YOURCLUSTERNAME.azurehdinsight.net.

    Quando lhe for pedido, autenticar para o site com o nome da conta HTTP (predefinição: administrador) e palavra-passe para o seu cluster.

   > [!NOTE]  
   > Pode solicitar-lhe que uma segunda vez Ambari para o nome de utilizador e palavra-passe. Se assim for, introduza o mesmo nome de conta e palavra-passe.

2. Utilizando a lista de no lado esquerdo, selecione a área de serviço que pretende modificar. Por exemplo, **HDFS**. Na área central, selecione o **configurações** separador.

    ![Imagem da web do Ambari com o separador de configurações de HDFS selecionado](./media/hdinsight-hadoop-heap-dump-linux/serviceconfig.png)

3. Usando o **filtro...**  entrada, introduza **opta ativamente por participar**. São apresentados apenas os itens que contenham esse texto.

    ![Lista filtrada](./media/hdinsight-hadoop-heap-dump-linux/filter.png)

4. Encontrar o  **\* \_OPTS** entrada para o serviço que pretende ativar a captura de área dinâmica para dados para e adicione as opções que pretende ativar. Na imagem seguinte, adicionei `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` para o **HADOOP\_NAMENODE\_OPTS** entrada:

    ![HADOOP_NAMENODE_OPTS with -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/](./media/hdinsight-hadoop-heap-dump-linux/opts.png)

   > [!NOTE]  
   > Quando ativar a área dinâmica para dados informações do Estado para o mapa ou reduzir o processo filho, procure para os campos com o nome **mapreduce.admin.map.child.java.opts** e **mapreduce.admin.reduce.child.java.opts**.

    Utilize o **guardar** botão para guardar as alterações. Pode inserir uma nota curta que descreve as alterações.

5. Depois de terem sido aplicadas as alterações, o **reinício necessário** ícone é apresentado ao lado de um ou mais serviços.

    ![Reinicie o ícone necessária e reinicie o botão](./media/hdinsight-hadoop-heap-dump-linux/restartrequiredicon.png)

6. Selecione cada serviço que precisa de um reinício e utilize o **ações de serviço** botão **ativar no modo de manutenção**. Modo de manutenção impede que os alertas que sejam gerados a partir do serviço quando ela é reiniciada.

    ![Ativar o menu do modo de manutenção](./media/hdinsight-hadoop-heap-dump-linux/maintenancemode.png)

7. Assim que tiver ativado o modo de manutenção, utilize o **reinicie** botão para o serviço **reiniciar todos os afetado**

    ![Reinicie a entrada de todos os afetados](./media/hdinsight-hadoop-heap-dump-linux/restartbutton.png)

   > [!NOTE]  
   > as entradas para o **reiniciar** botão pode ser diferente de outros serviços.

8. Assim que os serviços foram reiniciados, utilize o **ações de serviço** botão **Ativar desativar modo de manutenção**. Este Ambari para retomar a monitorização para os alertas para o serviço.

