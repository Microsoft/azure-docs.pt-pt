---
title: Ativar depósitos de pilhas para serviços Apache Hadoop em HDInsight - Azure
description: Ativar depósitos de pilhas para serviços Apache Hadoop de clusters HDInsight baseados em Linux para depuração e análise.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 824ba2c3316ccb34b59a9e435b9a6e582f137090
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945928"
---
# <a name="enable-heap-dumps-for-apache-hadoop-services-on-linux-based-hdinsight"></a>Ativar depósitos de pilhas para serviços Apache Hadoop em HDInsight baseado em Linux

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Os depósitos de pilhas contêm uma imagem da memória da aplicação, incluindo os valores das variáveis no momento em que a lixeira foi criada. São úteis para diagnosticar problemas que ocorrem no tempo de execução.

## <a name="services"></a>Serviços

Pode ativar depósitos de pilhas para os seguintes serviços:

* **Apache hcatalog** - tempelton
* **Colmeia apache** - hiveserver2, metastore, derbyserver
* **mapreduce** - jobhistoryserver
* **Apache yarn** - gestão de recursos, nodemanager, timelineserver
* **Apache hdfs** - datanode, secondarynamenode, namenode

Também pode ativar depósitos de pilhas para o mapa e reduzir os processos percorriados por HDInsight.

## <a name="understanding-heap-dump-configuration"></a>Compreensão da configuração do despejo de pilhas

Os depósitos de pilhas são ativados através de opções de passagem (por vezes conhecidas como opts, ou parâmetros) para o JVM quando um serviço é iniciado. Para a maioria dos serviços [apache Hadoop,](https://hadoop.apache.org/) você pode modificar o script de concha usado para iniciar o serviço para passar estas opções.

Em cada script, há uma exportação para **\* \_ OPTS,** que contém as opções passadas para o JVM. Por exemplo, no **hadoop-env.sh** script, a linha que começa com contém as `export HADOOP_NAMENODE_OPTS=` opções para o serviço NameNode.

Mapear e reduzir os processos são ligeiramente diferentes, uma vez que estas operações são um processo infantil do serviço MapReduce. Cada mapa ou processo de redução é executado num recipiente para crianças, e existem duas entradas que contêm as opções JVM. Ambos contidos em **mapred-site.xml:**

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [!NOTE]  
> Recomendamos a utilização de [Apache Ambari](https://ambari.apache.org/) para modificar tanto os scripts como as definições de mapred-site.xml, uma vez que a Ambari lida com alterações replicando os nós no cluster. Consulte a secção [Utilização de Apache Ambari](#using-apache-ambari) para obter etapas específicas.

### <a name="enable-heap-dumps"></a>Ativar capturas de área dinâmica para dados

A seguinte opção permite depósitos de pilhas quando ocorre um OutOfMemoryError:

`-XX:+HeapDumpOnOutOfMemoryError`

Indica **+** que esta opção está ativada. A predefinição é Desativado.

> [!WARNING]  
> Os depósitos de pilhas não estão ativados para os serviços Hadoop em HDInsight por padrão, uma vez que os ficheiros de despejo podem ser grandes. Se os ativar para a resolução de problemas, lembre-se de os desativar depois de reproduzir o problema e recolher os ficheiros de despejo.

### <a name="dump-location"></a>Local de despejo

A localização padrão para o ficheiro de despejo é o diretório de trabalho atual. Pode controlar onde o ficheiro é armazenado utilizando a seguinte opção:

`-XX:HeapDumpPath=/path`

Por exemplo, a utilização `-XX:HeapDumpPath=/tmp` faz com que os despejos sejam armazenados no diretório /tmp.

### <a name="scripts"></a>Scripts

Também pode desencadear um script quando ocorre um **OutOfMemoryError.** Por exemplo, desencadear uma notificação para saber que o erro ocorreu. Utilize a seguinte opção para ativar um script num __OutOfMemoryError__:

`-XX:OnOutOfMemoryError=/path/to/script`

> [!NOTE]  
> Uma vez que o Apache Hadoop é um sistema distribuído, qualquer script utilizado deve ser colocado em todos os nós do cluster em que o serviço funciona.
> 
> O script também deve estar em um local acessível pela conta que o serviço executa como, e deve fornecer permissões de execução. Por exemplo, pode desejar armazenar scripts `/usr/local/bin` e usar para conceder `chmod go+rx /usr/local/bin/filename.sh` permissões de leitura e execução.

## <a name="using-apache-ambari"></a>Usando Apache Ambari

Para modificar a configuração de um serviço, utilize os seguintes passos:

1. A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net` para, onde `CLUSTERNAME` está o nome do seu cluster.

2. Utilizando a lista de à esquerda, selecione a área de serviço que pretende modificar. Por exemplo, **HDFS**. Na área central, selecione o **separador Configs.**

    ![Imagem de web Ambari com separador HDFS Configs selecionado](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-service-config-tab.png)

3. Utilizando o **filtro...** entrada, **insira opts**. São apresentados apenas itens que contenham este texto.

    ![Lista filtrada de Apache Ambari](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdinsight-filter-list.png)

4. Encontre a entrada **\* \_ OPTS** para o serviço para o qual pretende ativar depósitos de pilhas e adicione as opções que deseja ativar. Na seguinte imagem, adicionei `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` à entrada **HADOOP \_ NAMENODE \_ OPTS:**

    ![Apache Ambari hadoop-namenode-opts](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hadoop-namenode-opts.png)

   > [!NOTE]  
   > Ao permitir despejos de pilhas para o mapa ou reduzir o processo infantil, procure os campos nomeados **mapreduce.admin.map.child.java.opts** e **mapreduce.admin.reduce.child.java.opts**.

    Utilize o botão **Guardar** para guardar as alterações. Pode introduzir uma pequena nota descrevendo as alterações.

5. Uma vez aplicadas as alterações, o ícone **exigido restart** aparece ao lado de um ou mais serviços.

    ![reiniciar o ícone necessário e reiniciar botão](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/restart-required-icon.png)

6. Selecione cada serviço que necessita de um reinício e use o botão **Ações de Serviço** para **ligar o modo de manutenção**. O modo de manutenção evita que os alertas sejam gerados a partir do serviço quando o reinicia.

    ![Ligue o menu do modo de manutenção hdi](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-maintenance-mode.png)

7. Depois de ter ativado o modo de manutenção, utilize o botão **Reiniciar** para o serviço para **reiniciar tudo efetuado**

    ![Apache Ambari reinicia todas as entradas afetadas](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-restart-all-button.png)

   > [!NOTE]  
   > As entradas para o botão **Reiniciar** podem ser diferentes para outros serviços.

8. Uma vez reiniciados os serviços, utilize o botão **Ações de Serviço** para desligar o **modo de manutenção**. Este Ambari vai retomar a monitorização dos alertas para o serviço.