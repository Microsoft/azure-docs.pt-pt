---
title: Ativar depósitos de pilhas para serviços Apache Hadoop no HDInsight - Azure
description: Ative depósitos de pilhas para serviços Apache Hadoop de clusters HDInsight baseados em Linux para depuração e análise.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 9134eb6922b0ed37bbe6051b138da2c7c082b175
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75658802"
---
# <a name="enable-heap-dumps-for-apache-hadoop-services-on-linux-based-hdinsight"></a>Ativar depósitos de pilhas para serviços Apache Hadoop em HDInsight baseado em Linux

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

As lixeiras contêm uma imagem instantânea da memória da aplicação, incluindo os valores das variáveis no momento em que o despejo foi criado. Então são úteis para diagnosticar problemas que ocorrem no tempo de execução.

## <a name="services"></a>Serviços

Pode ativar depósitos de pilhas para os seguintes serviços:

* **Apache hcatalog** - tempelton
* **Colmeia Apache** - hiveserver2, metaloja, derbyserver
* **mapreduce** - jobhistoryserver
* **Fios Apache** - gestor de recursos, gestor de nós, timelineserver
* **Apache hdfs** - datanóde, secondarynamenode, namenode

Também pode ativar depósitos de pilhas para o mapa e reduzir os processos deejádos pelo HDInsight.

## <a name="understanding-heap-dump-configuration"></a>Compreender a configuração do despejo de monte

Os depósitos de heap são habilitados através de opções de passagem (por vezes conhecidas como opts, ou parâmetros) para o JVM quando um serviço é iniciado. Para a maioria dos serviços [Apache Hadoop,](https://hadoop.apache.org/) pode modificar o script de concha usado para iniciar o serviço para passar estas opções.

Em cada guião, há uma exportação para ** \* \_OPTS,** que contém as opções passadas para o JVM. Por exemplo, no **guião hadoop-env.sh,** `export HADOOP_NAMENODE_OPTS=` a linha que começa contém as opções para o serviço NameNode.

O mapa e a redução dos processos são ligeiramente diferentes, uma vez que estas operações são um processo infantil do serviço MapReduce. Cada mapa ou redução do processo corre num recipiente para crianças, e há duas entradas que contêm as opções JVM. Ambos contidos no **site mapeado.xml:**

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [!NOTE]  
> Recomendamos a utilização de [Apache Ambari](https://ambari.apache.org/) para modificar tanto os scripts como as definições mapeadas do site.xml, uma vez que Ambari lida com as mudanças de replicação entre nós no cluster. Consulte a secção [Utilizar Apache Ambari](#using-apache-ambari) para obter passos específicos.

### <a name="enable-heap-dumps"></a>Ativar capturas de área dinâmica para dados

A seguinte opção permite despejos de pilhas quando ocorre um Erro OutOfMemory:

    -XX:+HeapDumpOnOutOfMemoryError

Indica **+** que esta opção está ativada. A predefinição é Desativado.

> [!WARNING]  
> As descargas de heap não estão ativadas para os serviços Hadoop no HDInsight por padrão, uma vez que os ficheiros de despejo podem ser grandes. Se os permitir para resolução de problemas, lembre-se de os desativar depois de reproduzir o problema e recolher os ficheiros de despejo.

### <a name="dump-location"></a>Localização de despejo

A localização padrão para o ficheiro de despejo é o atual diretório de trabalho. Pode controlar onde o ficheiro é armazenado utilizando a seguinte opção:

    -XX:HeapDumpPath=/path

Por exemplo, `-XX:HeapDumpPath=/tmp` a utilização das lixeiras deve ser armazenada no diretório /tmp.

### <a name="scripts"></a>Scripts

Também pode ativar um script quando ocorre um **Erro OutOfMemory.** Por exemplo, desencadeando uma notificação para que saiba que o erro ocorreu. Utilize a seguinte opção para acionar um script num __Erro OutOfMemory:__

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]  
> Uma vez que o Apache Hadoop é um sistema distribuído, qualquer script utilizado deve ser colocado em todos os nós do cluster em que o serviço funciona.
> 
> O script também deve estar num local acessível pela conta que o serviço executa como, e deve fornecer permissões de execução. Por exemplo, pode desejar armazenar `/usr/local/bin` scripts `chmod go+rx /usr/local/bin/filename.sh` e usar para conceder permissões de leitura e execução.

## <a name="using-apache-ambari"></a>Usando Apache Ambari

Para modificar a configuração de um serviço, utilize os seguintes passos:

1. De um navegador web, navegue até, `https://CLUSTERNAME.azurehdinsight.net`onde `CLUSTERNAME` está o nome do seu cluster.

2. Utilizando a lista à esquerda, selecione a área de serviço que pretende modificar. Por exemplo, **HDFS**. Na área central, selecione o separador **Configs.**

    ![Imagem da web Ambari com separador HDFS Configs selecionado](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-service-config-tab.png)

3. Utilizando o **Filtro...** entrada, **insira os opt-ins**. Apenas são apresentados itens que contenham este texto.

    ![Lista filtrada de config Apache Ambari](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdinsight-filter-list.png)

4. Encontre ** \* \_** a entrada OPTS para o serviço para o qual pretende ativar depósitos de pilhas e adicione as opções que deseja ativar. Na seguinte imagem, adicionei `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` à entrada **\_HADOOP\_NAMENODE OPTS:**

    ![Apache Ambari hadoop-namenodeopta](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hadoop-namenode-opts.png)

   > [!NOTE]  
   > Ao permitir despejos de pilhas para o mapa ou reduzir o processo infantil, procure os campos chamados **mapreduce.admin.map.java.opts** e **mapreduce.admin.reduce.child.java.opts**.

    Utilize o botão **Guardar** para guardar as alterações. Pode introduzir uma pequena nota descrevendo as alterações.

5. Uma vez aplicadas as alterações, o ícone **requerido Restart** aparece ao lado de um ou mais serviços.

    ![reiniciar ícone necessário e botão de reiniciar](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/restart-required-icon.png)

6. Selecione cada serviço que necessite de reiniciar e utilize o botão **Desviação** de Ação de Serviço para ligar o modo de **manutenção**. O modo de manutenção evita que os alertas sejam gerados a partir do serviço quando o reiniciar.

    ![Ligue o menu do modo de manutenção hdi](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-maintenance-mode.png)

7. Depois de ativar o modo de manutenção, utilize o botão **Reiniciar** para o serviço reiniciar **tudo efetuado**

    ![Apache Ambari Reinicia todas as entradas afetadas](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-restart-all-button.png)

   > [!NOTE]  
   > As entradas para o botão **Restart** podem ser diferentes para outros serviços.

8. Uma vez reiniciados os serviços, utilize o botão **Desligações** de Serviço para desligar o modo de **manutenção**. Este Ambari para retomar a monitorização dos alertas para o serviço.