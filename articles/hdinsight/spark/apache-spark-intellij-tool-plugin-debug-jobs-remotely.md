---
title: 'Kit de ferramentas Azure: Debug Apache Spark aplicações remotamente - Azure HDInsight'
description: Aprenda a utilizar ferramentas HDInsight no Kit de Ferramentas Azure para intelliJ para depurar remotamente aplicações Spark que funcionam em clusters HDInsight através de VPN.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.openlocfilehash: 393356bd8604f6e7622acd778817681aad31f1f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76935012"
---
# <a name="use-azure-toolkit-for-intellij-to-debug-apache-spark-applications-remotely-in-hdinsight-through-vpn"></a>Utilize o Kit de Ferramentas Azure para o IntelliJ para depurar aplicações Apache Spark remotamente no HDInsight através de VPN

Recomendamos depurar aplicações [Apache Spark](https://spark.apache.org/) remotamente através do SSH. Para obter instruções, consulte [remotamente as aplicações Apache Spark num cluster HDInsight com o Kit de Ferramentas Azure para IntelliJ através de SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

Este artigo fornece orientações passo a passo sobre como usar as Ferramentas HDInsight no Kit de Ferramentas Azure para o IntelliJ para submeter uma função Spark num cluster HDInsight Spark e, em seguida, desinvolá-lo remotamente do seu computador de secretária. Para completar estas tarefas, deve executar os seguintes passos de alto nível:

1. Crie uma rede virtual Azure site-to-site. Os passos deste documento pressupõem que você usa uma rede site-to-site.
1. Crie um cluster Spark no HDInsight que faça parte da rede virtual site-to-site.
1. Verifique a conectividade entre o nó da cabeça do cluster e o seu ambiente de trabalho.
1. Crie uma aplicação Scala no IntelliJ IDEA e, em seguida, configure-a para depuração remota.
1. Executar e depurar a aplicação.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição Azure.** Para mais informações, consulte [Um teste gratuito do Azure.](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)
* **Um cluster Apache Spark em HDInsight**. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Kit de desenvolvimento Oracle Java.** Pode instalá-lo a partir do site da [Oracle.](https://aka.ms/azure-jdks)
* **IntelliJ IDEA.** Este artigo usa a versão 2017.1. Pode instalá-lo a partir do site da [JetBrains.](https://www.jetbrains.com/idea/download/)
* **Ferramentas HDInsight em Kit de Ferramentas Azure para IntelliJ**. As ferramentas HDInsight para IntelliJ estão disponíveis como parte do Kit de Ferramentas Azure para intelliJ. Para obter instruções sobre como instalar o Kit de Ferramentas Azure, consulte Instalar o [Kit de Ferramentas Azure para o IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **Inscreva-se na sua Assinatura Azure a partir do IntelliJ IDEA**. Siga as instruções em [Use Azure Toolkit para intelliJ para criar aplicações Apache Spark para um cluster HDInsight](apache-spark-intellij-tool-plugin.md).
* **Supor a suver.** Durante a execução da aplicação Spark Scala para depuração remota num computador Windows, poderá obter uma exceção. Esta exceção é explicada no [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) e ocorre devido a um ficheiro WinUtils.exe desaparecido no Windows. Para contornar este erro, tem de baixar [winutils.exe](https://github.com/steveloughran/winutils) para um local como **C:\WinUtils\bin**. Adicione uma variável **ambiente HADOOP_HOME** e, em seguida, definir o valor da variável para **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Passo 1: Criar uma rede virtual Azure

Siga as instruções dos seguintes links para criar uma rede virtual Azure e, em seguida, verifique a conectividade entre o seu computador de secretária e a rede virtual:

* [Criar um VNet com uma ligação VPN site-to-site usando o portal Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Criar um VNet com uma ligação VPN site-to-site usando powerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Configure uma ligação ponto-a-local a uma rede virtual usando powerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Passo 2: Criar um cluster hDInsight Spark

Recomendamos que também crie um cluster Apache Spark no Azure HDInsight que faça parte da rede virtual Azure que criou. Utilize as informações disponíveis em [clusters baseados em Linux no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Como parte da configuração opcional, selecione a rede virtual Azure que criou no passo anterior.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>Passo 3: Verifique a conectividade entre o nó da cabeça do cluster e o seu ambiente de trabalho

1. Pegue o endereço IP do nó da cabeça. Abra ambari UI para o cluster. A partir da lâmina de cluster, selecione **Dashboard**.

    ![Selecione Dashboard em Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-apache-ambari.png)

1. A partir do Ambari UI, selecione **Hosts**.

    ![Selecione Anfitriões em Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/apache-ambari-hosts1.png)

1. Vê-se uma lista de nós de cabeça, nós operários e nós de zookeeper. Os nódosos da cabeça têm um prefixo **hn.** Selecione o primeiro nó de cabeça.

    ![Encontre o nó da cabeça em Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-cluster-headnodes.png)

1. A partir do painel **resumo** na parte inferior da página que abre, copie o **endereço IP** do nó da cabeça e o nome de **anfitrião**.

    ![Encontre o endereço IP em Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address1.png)

1. Adicione o endereço IP e o nome de anfitrião do nó de cabeça ao ficheiro dos **anfitriões** no computador onde pretende executar e desinver remotamente o trabalho spark. Isto permite-lhe comunicar com o nó da cabeça utilizando o endereço IP, bem como o nome de anfitrião.

   a. Abra um ficheiro De Notepad com permissões elevadas. A partir do menu **'Ficheiro',** selecione **Open**e, em seguida, encontre a localização do ficheiro dos anfitriões. Num computador Windows, a localização é **C:\Windows\System32\Drivers\etc\hosts**.

   b. Adicione as seguintes informações ao ficheiro dos **anfitriões:**

    ```
    # For headnode0
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    
    # For headnode1
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    ```

1. A partir do computador que ligou à rede virtual Azure que é utilizada pelo cluster HDInsight, verifique se pode piping os nós da cabeça utilizando o endereço IP, bem como o nome de anfitrião.

1. Utilize o SSH para se ligar ao nó da cabeça do cluster seguindo as instruções em [Ligar a um cluster HDInsight utilizando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). A partir do nó da cabeça do cluster, pingo o endereço IP do computador de secretária. Teste a conectividade a ambos os endereços IP atribuídos ao computador:

    - Um para a ligação de rede
    - Um para a rede virtual Azure

1. Repita os passos para o outro nó da cabeça.

## <a name="step-4-create-an-apache-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Passo 4: Criar uma aplicação Apache Spark Scala utilizando ferramentas HDInsight em Kit de Ferramentas Azure para IntelliJ e configurá-lo para depuração remota

1. Abra o IntelliJ IDEA e crie um novo projeto. Na caixa de diálogo **Novo Projeto**, faça o seguinte:

    ![Selecione o novo modelo de projeto no IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Selecione **HDInsight** > **Spark no HDInsight (Scala)**.

    b. Selecione **Seguinte**.
1. Na próxima caixa de diálogo **New Project,** faça o seguinte e, em seguida, selecione **Terminar:**

    - Introduza um nome do projeto e localização.

    - Na lista pendente **SDK do Projeto**, selecione **Java 1.8** para o cluster do Spark 2.x ou selecione **Java 1.7** para o cluster do Spark 1.x.

    - Na lista de drop-down da **versão Spark,** o assistente de criação do projeto Scala integra a versão adequada para o Spark SDK e o Scala SDK. Se a versão do cluster do Spark for anterior à 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark2.x**. Este exemplo utiliza o **Spark 2.0.2 (Scala 2.11.8)**.
  
   ![Selecione a versão SDK e Spark do projeto](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
1. O projeto Spark cria automaticamente um artefacto para si. Para ver o artefacto, faça o seguinte:

    a. No menu **File** (Ficheiro), selecione **Project Structure** (Estrutura do Projeto).

    b. Na caixa de diálogo **Project Structure,** selecione **Artefactos** para visualizar o artefacto predefinido que é criado. Também pode criar o seu próprio artefacto**+** selecionando o sinal de mais ().

   ![Artefactos intelliJ IDEA criam jarro](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-default-artifact.png)

1. Adicione bibliotecas ao seu projeto. Para adicionar uma biblioteca, faça o seguinte:

    a. Clique no nome do projeto na árvore do projeto e, em seguida, **selecione Definições**de Módulo Aberto .

    b. Na caixa de diálogo **Project Structure,** selecione **Bibliotecas,** selecione o símbolo (**+**) e, em seguida, selecione From **Maven**.

    ![Biblioteca de descarregamento IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-add-library.png)

    c. Na Biblioteca de Download a partir da caixa de diálogo **repositório Maven,** procure e adicione as seguintes bibliotecas:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`

1. `yarn-site.xml` Copie `core-site.xml` e do nó da cabeça do cluster e adicione-os ao projeto. Utilize os seguintes comandos para copiar os ficheiros. Pode utilizar [a Cygwin](https://cygwin.com/install.html) `scp` para executar os seguintes comandos para copiar os ficheiros dos nós da cabeça do cluster:

    ```bash
    scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .
    ```

    Como já adicionámos o endereço IP do nó do cluster e nomes `scp` de anfitriões para o ficheiro dos anfitriões no ambiente de trabalho, podemos usar os comandos da seguinte forma:

    ```bash
    scp sshuser@nitinp:/etc/hadoop/conf/core-site.xml .
    scp sshuser@nitinp:/etc/hadoop/conf/yarn-site.xml .
    ```

    Para adicionar estes ficheiros ao seu projeto, copie-os sob `<your project directory>\src`a pasta **/src** na árvore do seu projeto, por exemplo .

1. Atualize `core-site.xml` o ficheiro para efazer as seguintes alterações:

   a. Substitua a tecla encriptada. O `core-site.xml` ficheiro inclui a chave encriptada da conta de armazenamento associada ao cluster. No `core-site.xml` ficheiro que adicionou ao projeto, substitua a chave encriptada pela chave de armazenamento real associada à conta de armazenamento predefinida. Para mais informações, consulte Gerir as chaves de [acesso à conta](../../storage/common/storage-account-keys-manage.md)de armazenamento .

    ```xml
    <property>
            <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
            <value>access-key-associated-with-the-account</value>
    </property>
    ```

   b. Retire as seguintes entradas de: `core-site.xml`

    ```xml
    <property>
            <name>fs.azure.account.keyprovider.hdistoragecentral.blob.core.windows.net</name>
            <value>org.apache.hadoop.fs.azure.ShellDecryptionKeyProvider</value>
    </property>

    <property>
            <name>fs.azure.shellkeyprovider.script</name>
            <value>/usr/lib/python2.7/dist-packages/hdinsight_common/decrypt.sh</value>
    </property>

    <property>
            <name>net.topology.script.file.name</name>
            <value>/etc/hadoop/conf/topology_script.py</value>
    </property>
    ```

   c. Guarde o ficheiro.

1. Adicione a classe principal para a sua candidatura. Do **Project Explorer,** **src**de clique sc, ponto para **Novo,** e depois selecione **classe Scala**.

    ![IntelliJ IDEA Selecione a classe principal](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)

1. Na caixa de diálogo **Create New Scala Class,** forneça um nome, selecione **Object** na caixa **Kind** e, em seguida, selecione **OK**.

    ![IntelliJ IDEA Criar nova classe Scala](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)

1. No `MyClusterAppMain.scala` ficheiro, cola o seguinte código. Este código cria o contexto `executeJob` Spark e `SparkSample` abre um método a partir do objeto.

    ```scala
    import org.apache.spark.{SparkConf, SparkContext}

    object SparkSampleMain {
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("SparkSample")
                                    .set("spark.hadoop.validateOutputSpecs", "false")
        val sc = new SparkContext(conf)

        SparkSample.executeJob(sc,
                            "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
                            "wasb:///HVACOut")
        }
    }
    ```

1. Repita os passos 8 e 9 para `*SparkSample`adicionar um novo objeto Scala chamado . Adicione o seguinte código a esta aula. Este código lê os dados do HVAC.csv (disponíveis em todos os clusters HDInsight Spark). Recupera as linhas que têm apenas um dígito na sétima coluna do ficheiro CSV e, em seguida, escreve a saída para **/AVACOut** sob o recipiente de armazenamento predefinido para o cluster.

    ```scala
    import org.apache.spark.SparkContext

    object SparkSample {
        def executeJob (sc: SparkContext, input: String, output: String): Unit = {
        val rdd = sc.textFile(input)

        //find the rows which have only one digit in the 7th column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        val s = sc.parallelize(rdd.take(5)).cartesian(rdd).count()
        println(s)

        rdd1.saveAsTextFile(output)
        //rdd1.collect().foreach(println)
         }
    }
    ```

1. Repita os passos 8 e `RemoteClusterDebugging`9 para adicionar uma nova classe chamada . Esta classe implementa a estrutura de teste Spark que é usada para depurar as aplicações. Adicione o seguinte `RemoteClusterDebugging` código à classe:

    ```scala
        import org.apache.spark.{SparkConf, SparkContext}
        import org.scalatest.FunSuite

        class RemoteClusterDebugging extends FunSuite {

         test("Remote run") {
           val conf = new SparkConf().setAppName("SparkSample")
                                     .setMaster("yarn-client")
                                     .set("spark.yarn.am.extraJavaOptions", "-Dhdp.version=2.4")
                                     .set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")
                                     .setJars(Seq("""C:\workspace\IdeaProjects\MyClusterApp\out\artifacts\MyClusterApp_DefaultArtifact\default_artifact.jar"""))
                                     .set("spark.hadoop.validateOutputSpecs", "false")
           val sc = new SparkContext(conf)

           SparkSample.executeJob(sc,
             "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
             "wasb:///HVACOut")
         }
        }
    ```

     Há um par de coisas importantes a notar:

      * Para `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`, certifique-se de que o JAR de montagem de faíscas está disponível no armazenamento do cluster no caminho especificado.
      * Para `setJars`especificar a localização onde o artefacto JAR é criado. Normalmente, é. `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`

1. Na`*RemoteClusterDebugging` aula, clique na `test` palavra-chave e, em seguida, selecione **Create RemoteClusterDebugging Configuração**.

    ![IntelliJ IDEA Criar uma configuração remota](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

1. Na caixa de diálogo **Create RemoteClusterDebugging,** forneça um nome para a configuração e, em seguida, selecione o **tipo de teste** como o nome de **teste**. Deixe todos os outros valores como as definições predefinidas. Selecione **Apply** (Aplicar) e **OK**.

    ![Criar configuração de remoteClusterDebugging](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)

1. Agora deve ver uma lista de desativação de configuração **de execução remota** na barra de menus.

    ![IntelliJ A lista de drop-down remoto](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-config-remote-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>Passo 5: Executar a aplicação em modo dedepuração

1. No seu projeto IntelliJ `SparkSample.scala` IDEA, abra e `val rdd1`crie um breakpoint ao lado de . No **Menu Create Breakpoint para** o menu pop-up, selecione **a linha na função executarJob**.

    ![IntelliJ IDEA Adicionar um breakpoint](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-create-breakpoint.png)

1. Para executar a aplicação, selecione o botão **Debug Run** ao lado da lista de desativação de configuração **remote Run.**

    ![IntelliJ IDEA Selecione o botão Debug Run](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode-button.png)

1. Quando a execução do programa atinge o ponto de rutura, vê-se um separador **Debugger** no painel inferior.

    ![IntelliJ IDEA Ver o separador Debugger](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-debugger-tab.png)

1. Para adicionar um relógio,**+** selecione o ícone ( ).

    ![IntelliJ debug-add-watch-variável](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    Neste exemplo, a aplicação `rdd1` quebrou antes da criação da variável. Usando este relógio, podemos ver as primeiras `rdd`cinco linhas na variável. Selecione **Entrar**.

    ![IntelliJ Executar o programa em modo de depuração](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    O que se vê na imagem anterior é que, no tempo de funcionamento, poderá consultar terabytes de dados e desinviar a forma como a sua aplicação progride. Por exemplo, na saída mostrada na imagem anterior, pode ver que a primeira linha da saída é um cabeçalho. Com base nesta saída, pode modificar o seu código de aplicação para saltar a linha do cabeçalho, se necessário.

1. Agora pode selecionar o ícone do **Programa Currículo** para prosseguir com a sua execução de candidatura.

    ![IntelliJ IDEA Select Resume Program](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-remote-run.png)

1. Se a aplicação terminar com sucesso, deverá ver a saída como a seguinte:

    ![Saída de consola de debugger IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete-window.png)

## <a name="next-steps"></a><a name="seealso"></a>Passos seguintes

* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demonstração

* Criar projeto Scala (vídeo): [Criar aplicações Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depuração remota (vídeo): [Utilize o Kit de Ferramentas Azure para o IntelliJ para depurar aplicações apache Spark remotamente num cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: Realizar análise interativa de dados utilizando spark in HDInsight com ferramentas BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Use Spark no HDInsight para analisar a temperatura do edifício usando dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Use Spark no HDInsight para prever resultados da inspeção alimentar](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando Apache Spark em HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações

* [Criar uma aplicação autónoma com o Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Utilize o Kit de Ferramentas Azure para o IntelliJ para criar aplicações Apache Spark para um cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Utilize o Kit de Ferramentas Azure para o IntelliJ para depurar aplicações apache Spark remotamente através do SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utilize ferramentas HDInsight no Kit de Ferramentas Azure para eclipse para criar aplicações Apache Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Use os cadernos Apache Zeppelin com um cluster Apache Spark em HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o portátil Jupyter em um cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Track and debug jobs que funcionam num cluster Apache Spark em HDInsight](apache-spark-job-debugging.md)
