---
title: Habilitar despejos de heap para serviços de Apache Hadoop no HDInsight – Azure
description: Habilite despejos de heap para serviços de Apache Hadoop de clusters HDInsight baseados em Linux para depuração e análise.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 9134eb6922b0ed37bbe6051b138da2c7c082b175
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75658802"
---
# <a name="enable-heap-dumps-for-apache-hadoop-services-on-linux-based-hdinsight"></a>Habilitar despejos de heap para serviços de Apache Hadoop no HDInsight baseado em Linux

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Os despejos de heap contêm um instantâneo da memória do aplicativo, incluindo os valores das variáveis no momento em que o despejo foi criado. Portanto, eles são úteis para diagnosticar problemas que ocorrem em tempo de execução.

## <a name="services"></a>Serviços

Você pode habilitar despejos de heap para os seguintes serviços:

* **Apache hcatalog** -Tempelton
* **Apache Hive** -hiveserver2, metastore, derbyserver
* **MapReduce** -jobhistoryserver
* **Apache yarn** -ResourceManager, NodeManager, timelineserver
* **Apache HDFS** -datanode, secondarynamenode, namenode

Você também pode habilitar despejos de heap para o mapa e reduzir os processos executados pelo HDInsight.

## <a name="understanding-heap-dump-configuration"></a>Compreendendo a configuração de despejo de heap

Os despejos de heap são habilitados por meio da passagem de opções (às vezes conhecidas como optas ou parâmetros) para a JVM quando um serviço é iniciado. Para a maioria dos serviços [Apache Hadoop](https://hadoop.apache.org/) , você pode modificar o script de shell usado para iniciar o serviço para passar essas opções.

Em cada script, há uma exportação para **\*\_** opções, que contém as que são passadas para a JVM. Por exemplo, no script **Hadoop-env.sh** , a linha que começa com `export HADOOP_NAMENODE_OPTS=` contém as opções para o serviço NameNode.

Os processos de mapeamento e redução são ligeiramente diferentes, pois essas operações são um processo filho do serviço MapReduce. Cada mapa ou redução de processo é executado em um contêiner filho, e há duas entradas que contêm as opções de JVM. Ambos estão contidos em **mapred-site. xml**:

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [!NOTE]  
> É recomendável usar o [Apache Ambari](https://ambari.apache.org/) para modificar os scripts e as configurações de mapred-site. xml, uma vez que o Ambari lida com a replicação de alterações entre nós no cluster. Consulte a seção [usando o Apache Ambari](#using-apache-ambari) para obter etapas específicas.

### <a name="enable-heap-dumps"></a>Ativar capturas de área dinâmica para dados

A opção a seguir habilita despejos de heap quando ocorre um OutOfMemoryError:

    -XX:+HeapDumpOnOutOfMemoryError

O **+** indica que essa opção está habilitada. A predefinição é Desativado.

> [!WARNING]  
> Os despejos de heap não são habilitados para serviços do Hadoop no HDInsight por padrão, pois os arquivos de despejo podem ser grandes. Se você habilitá-los para solução de problemas, lembre-se de desabilitá-los depois de ter reproduzido o problema e coletado os arquivos de despejo.

### <a name="dump-location"></a>Local do despejo

O local padrão para o arquivo de despejo é o diretório de trabalho atual. Você pode controlar onde o arquivo é armazenado usando a seguinte opção:

    -XX:HeapDumpPath=/path

Por exemplo, o uso de `-XX:HeapDumpPath=/tmp` faz com que os despejos sejam armazenados no diretório/tmp.

### <a name="scripts"></a>Scripts

Você também pode disparar um script quando ocorrer um **OutOfMemoryError** . Por exemplo, disparar uma notificação para que você saiba que o erro ocorreu. Use a seguinte opção para disparar um script em um __OutOfMemoryError__:

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]  
> Como Apache Hadoop é um sistema distribuído, qualquer script usado deve ser colocado em todos os nós no cluster em que o serviço é executado.
> 
> O script também deve estar em um local acessível pela conta em que o serviço é executado e deve fornecer permissões de execução. Por exemplo, você pode desejar armazenar scripts em `/usr/local/bin` e usar `chmod go+rx /usr/local/bin/filename.sh` para conceder permissões de leitura e execução.

## <a name="using-apache-ambari"></a>Usando o Apache Ambari

Para modificar a configuração de um serviço, use as seguintes etapas:

1. Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net`, em que `CLUSTERNAME` é o nome do cluster.

2. Usando a lista de à esquerda, selecione a área de serviço que você deseja modificar. Por exemplo, **HDFS**. Na área central, selecione a guia **configurações** .

    ![Imagem da Web Ambari com a guia Configurações do HDFS selecionada](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-service-config-tab.png)

3. Usando a entrada **Filter...** , insira as **optas**. Somente os itens que contêm esse texto são exibidos.

    ![Lista filtrada de configuração do Apache Ambari](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdinsight-filter-list.png)

4. Localize o **\*\_** entrada de opções para o serviço para o qual você deseja habilitar os despejos de heap e adicione as opções que deseja habilitar. Na imagem a seguir, adicionei `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` ao **HADOOP\_NAMENODE\_** entrada de opções:

    ![Apache Ambari Hadoop-namenode-optas por](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hadoop-namenode-opts.png)

   > [!NOTE]  
   > Ao habilitar despejos de heap para o processo de mapeamento ou redução de filho, procure os campos chamados **MapReduce. admin. map. Child. java.** e **MapReduce. admin. Reduza. Child. java.**

    Use o botão **salvar** para salvar as alterações. Você pode inserir uma breve observação descrevendo as alterações.

5. Depois que as alterações forem aplicadas, o ícone **reinicialização necessária** aparecerá ao lado de um ou mais serviços.

    ![reiniciar o ícone e o botão de reinicialização necessários](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/restart-required-icon.png)

6. Selecione cada serviço que precisa de uma reinicialização e use o botão **ações de serviço** para ativar o **modo de manutenção**. O modo de manutenção impede que os alertas sejam gerados a partir do serviço quando você o reinicia.

    ![Ativar o menu do modo de manutenção do HDI](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-maintenance-mode.png)

7. Depois de habilitar o modo de manutenção, use o botão **reiniciar** para que o serviço **reinicie todos os efeitos**

    ![Apache Ambari reiniciar todas as entradas afetadas](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-restart-all-button.png)

   > [!NOTE]  
   > As entradas do botão **reiniciar** podem ser diferentes para outros serviços.

8. Depois que os serviços forem reiniciados, use o botão **ações de serviço** para desativar o modo de **manutenção**. Este Ambari para retomar o monitoramento de alertas para o serviço.