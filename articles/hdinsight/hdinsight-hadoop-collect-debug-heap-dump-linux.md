---
title: Habilitar despejos de heap para serviços de Apache Hadoop no HDInsight – Azure
description: Habilite despejos de heap para serviços de Apache Hadoop de clusters HDInsight baseados em Linux para depuração e análise.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 68be0d7d13785c9631044766a290eec93637ea64
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70879951"
---
# <a name="enable-heap-dumps-for-apache-hadoop-services-on-linux-based-hdinsight"></a>Habilitar despejos de heap para serviços de Apache Hadoop no HDInsight baseado em Linux

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Os despejos de heap contêm um instantâneo da memória do aplicativo, incluindo os valores das variáveis no momento em que o despejo foi criado. Portanto, eles são úteis para diagnosticar problemas que ocorrem em tempo de execução.

## <a name="whichServices"></a>Serviços

Você pode habilitar despejos de heap para os seguintes serviços:

* **Apache hcatalog** -Tempelton
* **Apache Hive** -hiveserver2, metastore, derbyserver
* **MapReduce** -jobhistoryserver
* **Apache yarn** -ResourceManager, NodeManager, timelineserver
* **Apache HDFS** -datanode, secondarynamenode, namenode

Você também pode habilitar despejos de heap para o mapa e reduzir os processos executados pelo HDInsight.

## <a name="configuration"></a>Compreendendo a configuração de despejo de heap

Os despejos de heap são habilitados por meio da passagem de opções (às vezes conhecidas como optas ou parâmetros) para a JVM quando um serviço é iniciado. Para a maioria dos serviços [Apache Hadoop](https://hadoop.apache.org/) , você pode modificar o script de shell usado para iniciar o serviço para passar essas opções.

Em cada  **\*script, há uma \_exportação para as**opções, que contém a opção passada para a JVM. Por exemplo, no script **Hadoop-env.sh** , a linha que começa com `export HADOOP_NAMENODE_OPTS=` contém as opções para o serviço NameNode.

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

Por exemplo, usar `-XX:HeapDumpPath=/tmp` faz com que os despejos sejam armazenados no diretório/tmp.

### <a name="scripts"></a>Scripts

Você também pode disparar um script quando ocorrer um **OutOfMemoryError** . Por exemplo, disparar uma notificação para que você saiba que o erro ocorreu. Use a seguinte opção para disparar um script em um __OutOfMemoryError__:

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]  
> Como Apache Hadoop é um sistema distribuído, qualquer script usado deve ser colocado em todos os nós no cluster em que o serviço é executado.
> 
> O script também deve estar em um local acessível pela conta em que o serviço é executado e deve fornecer permissões de execução. Por exemplo, você pode desejar armazenar scripts no `/usr/local/bin` e usar `chmod go+rx /usr/local/bin/filename.sh` o para conceder permissões de leitura e execução.

## <a name="using-apache-ambari"></a>Usando o Apache Ambari

Para modificar a configuração de um serviço, use as seguintes etapas:

1. Abra a interface do usuário da Web do amAmbari para seu cluster. A URL é https://YOURCLUSTERNAME.azurehdinsight.net.

    Quando solicitado, autentique-se no site usando o nome da conta HTTP (padrão: admin) e a senha do cluster.

   > [!NOTE]  
   > Você pode ser solicitado uma segunda vez por Ambari para o nome de usuário e a senha. Nesse caso, insira o mesmo nome de conta e senha.

2. Usando a lista de à esquerda, selecione a área de serviço que você deseja modificar. Por exemplo, **HDFS**. Na área central, selecione a guia **configurações** .

    ![Imagem da Web Ambari com a guia Configurações do HDFS selecionada](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-service-config-tab.png)

3. Usando a entrada **Filter...** , insira as **optas**. Somente os itens que contêm esse texto são exibidos.

    ![Lista filtrada](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdinsight-filter-list.png)

4. Localize a  **\* entradadeopçõesparaoserviçoparaoqualvocêdesejahabilitarosdespejosdeheapeadicioneasopçõesquedesejahabilitar.\_** Na imagem a seguir, adicionei `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` à entrada de opções do **HADOOP\_NAMENODE:\_**

    ![HADOOP_NAMENODE_OPTS com-XX: + HeapDumpOnOutOfMemoryError-XX: HeapDumpPath =/tmp/](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hadoop-namenode-opts.png)

   > [!NOTE]  
   > Ao habilitar despejos de heap para o processo de mapeamento ou redução de filho, procure os campos chamados **MapReduce. admin. map. Child. java.** e **MapReduce. admin. Reduza. Child. java.**

    Use o botão **salvar** para salvar as alterações. Você pode inserir uma breve observação descrevendo as alterações.

5. Depois que as alterações forem aplicadas, o ícone **reinicialização necessária** aparecerá ao lado de um ou mais serviços.

    ![reiniciar o ícone e o botão de reinicialização necessários](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/restart-required-icon.png)

6. Selecione cada serviço que precisa de uma reinicialização e use o botão **ações de serviço** para ativar o **modo de manutenção**. O modo de manutenção impede que os alertas sejam gerados a partir do serviço quando você o reinicia.

    ![Ativar o menu do modo de manutenção](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-maintenance-mode.png)

7. Depois de habilitar o modo de manutenção, use o botão **reiniciar** para que o serviço **reinicie todos os efeitos**

    ![Reiniciar todas as entradas afetadas](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-restart-all-button.png)

   > [!NOTE]  
   > As entradas do botão **reiniciar** podem ser diferentes para outros serviços.

8. Depois que os serviços forem reiniciados, use o botão **ações de serviço** para desativar o modo de **manutenção**. Este Ambari para retomar o monitoramento de alertas para o serviço.

