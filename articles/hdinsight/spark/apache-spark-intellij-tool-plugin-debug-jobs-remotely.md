---
title: 'Azure Toolkit: Debug Apache Spark apps remotamente - Azure HDInsight'
description: Aprenda a usar Ferramentas HDInsight em Azure Toolkit para IntelliJ para depurar remotamente aplicações Spark que funcionam em clusters HDInsight através de VPN.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 11/28/2017
ms.openlocfilehash: 7ec49ee4f07aff6e9b9f9d6fc43e37742d7e163a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86085128"
---
# <a name="use-azure-toolkit-for-intellij-to-debug-apache-spark-applications-remotely-in-hdinsight-through-vpn"></a>Utilize o Azure Toolkit para o IntelliJ para depurar as aplicações Apache Spark remotamente em HDInsight através da VPN

Recomendamos depurar as aplicações [Apache Spark](https://spark.apache.org/) remotamente através do SSH. Para obter instruções, consulte [aplicações Apache Spark remotamente depuradas num cluster HDInsight com O Kit de Ferramentas Azure para IntelliJ através de SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

Este artigo fornece orientações passo a passo sobre como usar as Ferramentas HDInsight em Azure Toolkit para IntelliJ submeter um trabalho spark num cluster HDInsight Spark e, em seguida, depurá-lo remotamente do seu computador de secretária. Para completar estas tarefas, deve executar os seguintes passos de alto nível:

1. Crie uma rede virtual Azure site-to-site ou ponto a ponto. Os passos neste documento pressupõem que utiliza uma rede site-to-site.
1. Crie um cluster Spark em HDInsight que faz parte da rede virtual site-to-site.
1. Verifique a conectividade entre o nó da cabeça do cluster e o seu ambiente de trabalho.
1. Crie uma aplicação Scala no IntelliJ IDEA e, em seguida, configuure-a para depuração remota.
1. Executar e depurar a aplicação.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura Azure**. Para mais informações, [consulte obter um teste gratuito de Azure.](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)
* **Um aglomerado de faíscas Apache em HDInsight.** Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Kit de desenvolvimento Oracle Java.** Pode instalá-lo a partir do site da [Oracle.](https://aka.ms/azure-jdks)
* **Ideia IntelliJ**. Este artigo utiliza a versão 2017.1. Pode instalá-lo a partir do website da [JetBrains.](https://www.jetbrains.com/idea/download/)
* **Ferramentas HDInsight em Azure Toolkit para IntelliJ**. As ferramentas HDInsight para IntelliJ estão disponíveis como parte do Azure Toolkit para IntelliJ. Para obter instruções sobre como instalar o Azure Toolkit, consulte [o Kit de Ferramentas Azure para IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **Inscreva-se na sua Assinatura Azure da IntelliJ IDEA**. Siga as instruções no [Use Azure Toolkit para IntelliJ para criar aplicações Apache Spark para um cluster HDInsight](apache-spark-intellij-tool-plugin.md).
* **Solução de exceção.** Enquanto executa a aplicação Spark Scala para depurar remotamente num computador Windows, poderá obter uma exceção. Esta exceção é explicada no [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) e ocorre devido a falta de um ficheiro WinUtils.exe no Windows. Para contornar este erro, tem de fazer o download [Winutils.exe](https://github.com/steveloughran/winutils) para um local como **C:\WinUtils\bin**. Adicione uma variável ambiente **HADOOP_HOME** e, em seguida, desaprove o valor da variável para **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Passo 1: Criar uma rede virtual Azure

Siga as instruções dos seguintes links para criar uma rede virtual Azure e, em seguida, verifique a conectividade entre o computador de secretária e a rede virtual:

* [Criar um VNet com uma ligação VPN site-to-site utilizando o portal Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Criar um VNet com uma ligação VPN site-to-site utilizando PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Configure uma ligação ponto-a-local a uma rede virtual utilizando o PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Passo 2: Criar um cluster HDInsight Spark

Recomendamos que também crie um cluster Apache Spark em Azure HDInsight que faz parte da rede virtual Azure que criou. Utilize as informações disponíveis em [clusters baseados em Create Linux em HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Como parte da configuração opcional, selecione a rede virtual Azure que criou no passo anterior.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>Passo 3: Verifique a conectividade entre o nó da cabeça do cluster e o seu ambiente de trabalho

1. Obtenha o endereço IP do nó da cabeça. Abra a UI Ambari para o cluster. A partir da lâmina de cluster, selecione **Dashboard**.

    ![Selecione Dashboard em Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-apache-ambari.png)

1. A partir da UI Ambari, **selecione Hosts**.

    ![Selecione Anfitriões em Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/apache-ambari-hosts1.png)

1. Vê-se uma lista de nós de cabeça, nós operários e nós de zookeeper. Os nós da cabeça têm um prefixo **hn*** Selecione o primeiro nó de cabeça.

    ![Encontre o nó da cabeça em Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-cluster-headnodes.png)

1. A partir do **painel resumo** na parte inferior da página que abre, copie o **endereço IP** do nó de cabeça e o **nome anfitrião**.

    ![Encontre o endereço IP em Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address1.png)

1. Adicione o endereço IP e o nome de anfitrião do nó de cabeça ao ficheiro dos **anfitriões** no computador onde pretende executar e depurar remotamente o trabalho Spark. Isto permite-lhe comunicar com o nó da cabeça utilizando o endereço IP, bem como o nome de anfitrião.

   a. Abra um ficheiro Notepad com permissões elevadas. A partir do menu **'Ficheiro',** selecione **'Abrir'** e, em seguida, encontre a localização do ficheiro dos anfitriões. Num computador Windows, a localização é **C:\Windows\System32\Drivers\etc\hosts**.

   b. Adicione as seguintes informações ao ficheiro **dos anfitriões:**

    ```
    # For headnode0
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    
    # For headnode1
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    ```

1. A partir do computador que ligou à rede virtual Azure que é utilizada pelo cluster HDInsight, verifique se pode pingar os nós da cabeça utilizando o endereço IP, bem como o nome de anfitrião.

1. Utilize o SSH para ligar ao nó da cabeça do cluster seguindo as instruções em [Ligar a um cluster HDInsight utilizando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). A partir do nó da cabeça do cluster, pingo no endereço IP do computador de secretária. Teste a conectividade de ambos os endereços IP atribuídos ao computador:

    - Um para a ligação de rede
    - Um para a rede virtual Azure

1. Repita os passos para o outro nó de cabeça.

## <a name="step-4-create-an-apache-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Passo 4: Criar uma aplicação Apache Spark Scala utilizando ferramentas HDInsight em Azure Toolkit para IntelliJ e configurgá-la para depuração remota

1. Abrir IntelliJ IDEA e criar um novo projeto. Na caixa de diálogo **Novo Projeto**, faça o seguinte:

    ![Selecione o novo modelo de projeto no IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Selecione **HDInsight**  >  **Spark em HDInsight (Scala)**.

    b. Selecione **Seguinte**.
1. Na próxima caixa de diálogo **do Novo Projeto,** faça o seguinte e, em seguida, selecione **Terminar:**

    - Introduza um nome do projeto e localização.

    - Na lista pendente **SDK do Projeto**, selecione **Java 1.8** para o cluster do Spark 2.x ou selecione **Java 1.7** para o cluster do Spark 1.x.

    - Na lista de drop-down da **versão Spark,** o assistente de criação de projeto Scala integra a versão adequada para o Spark SDK e o Scala SDK. Se a versão do cluster do Spark for anterior à 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark2.x**. Este exemplo utiliza o **Spark 2.0.2 (Scala 2.11.8)**.
  
   ![Selecione a versão SDK e Spark do projeto](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
1. O projeto Spark cria automaticamente um artefacto para si. Para ver o artefacto, faça o seguinte:

    a. No menu **File** (Ficheiro), selecione **Project Structure** (Estrutura do Projeto).

    b. Na caixa de diálogo Da Estrutura do **Projeto,** selecione **Artefactos** para visualizar o artefacto padrão que é criado. Também pode criar o seu próprio artefacto selecionando o sinal de mais **+** ().

   ![Artefactos intelliJ IDEA criam jarro](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-default-artifact.png)

1. Adicione bibliotecas ao seu projeto. Para adicionar uma biblioteca, faça o seguinte:

    a. Clique com o botão direito no nome do projeto na árvore do projeto e, em seguida, selecione **Definições de Módulo Aberto**.

    b. Na caixa de diálogo da Estrutura do **Projeto,** selecione **Bibliotecas,** selecione o símbolo ( **+** ) e, em seguida, selecione **A partir de Maven**.

    ![Biblioteca de descarregamento intelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-add-library.png)

    c. Na Biblioteca de Descarregamentos da caixa de diálogo do **Repositório Maven,** procure e adicione as seguintes bibliotecas:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`

1. Copie `yarn-site.xml` e do nó da cabeça do cluster e `core-site.xml` adicione-os ao projeto. Utilize os seguintes comandos para copiar os ficheiros. Pode utilizar [a Cygwin](https://cygwin.com/install.html) para executar os `scp` seguintes comandos para copiar os ficheiros dos nosmes da cabeça do cluster:

    ```bash
    scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .
    ```

    Como já adicionámos o endereço IP do nó de cabeça de cluster e os hostnames para o ficheiro dos anfitriões no ambiente de trabalho, podemos usar os `scp` comandos da seguinte forma:

    ```bash
    scp sshuser@nitinp:/etc/hadoop/conf/core-site.xml .
    scp sshuser@nitinp:/etc/hadoop/conf/yarn-site.xml .
    ```

    Para adicionar estes ficheiros ao seu projeto, copie-os sob a pasta **/src** na sua árvore do projeto, por `<your project directory>\src` exemplo.

1. Atualize o `core-site.xml` ficheiro para esporar as seguintes alterações:

   a. Substitua a chave encriptada. O `core-site.xml` ficheiro inclui a chave encriptada da conta de armazenamento associada ao cluster. No `core-site.xml` ficheiro que adicionou ao projeto, substitua a chave encriptada pela chave de armazenamento real associada à conta de armazenamento predefinida. Para obter mais informações, consulte [gerir as teclas de acesso à conta de armazenamento.](../../storage/common/storage-account-keys-manage.md)

    ```xml
    <property>
            <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
            <value>access-key-associated-with-the-account</value>
    </property>
    ```

   b. Remova as seguintes entradas `core-site.xml` de:

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

1. Adicione a classe principal para a sua aplicação. A partir do **Project Explorer**, **click src,** aponte para **New**, e, em seguida, selecione **classe Scala**.

    ![Ideia IntelliJ Selecione a classe principal](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)

1. Na caixa de diálogo **'Criar Nova Classe Scala',** forneça um nome, selecione **Objeto** na caixa **Kind** e, em seguida, selecione **OK**.

    ![Ideia IntelliJ Criar nova classe Scala](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)

1. No `MyClusterAppMain.scala` ficheiro, cole o seguinte código. Este código cria o contexto Spark e abre um `executeJob` método a partir do `SparkSample` objeto.

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

1. Repita os passos 8 e 9 para adicionar um novo objeto Scala chamado `*SparkSample` . Adicione o seguinte código a esta classe. Este código lê os dados do HVAC.csv (disponível em todos os clusters HDInsight Spark). Recupera as linhas que têm apenas um dígito na sétima coluna no ficheiro CSV e, em seguida, escreve a saída para **/AVACOut** sob o recipiente de armazenamento predefinido para o cluster.

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

1. Repita os passos 8 e 9 para adicionar uma nova classe chamada `RemoteClusterDebugging` . Esta classe implementa a estrutura de teste Spark que é usada para depurar as aplicações. Adicione o seguinte código à `RemoteClusterDebugging` classe:

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

      * Para `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")` , certifique-se de que o JAR de montagem spark está disponível no armazenamento do cluster no caminho especificado.
      * Para `setJars` , especificar o local onde o artefacto JAR é criado. Normalmente, `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar` é.

1. Na `*RemoteClusterDebugging` classe, clique com o botão direito da `test` palavra-chave e, em seguida, **selecione Create RemoteClusterDebugging Configuration**.

    ![Ideia IntelliJ Criar uma configuração remota](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

1. Na caixa de diálogo de **configuração de configuração Depurador Decluster Remoto,** forneça um nome para a configuração e, em seguida, selecione **o tipo de Teste** como o nome de **Teste**. Deixe todos os outros valores como as definições predefinidos. Selecione **Apply** (Aplicar) e **OK**.

    ![Criar configuração de depurar dedebugging decluster remoto](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)

1. Deve agora ver uma lista de recuos de configuração de **execução remota** na barra de menus.

    ![Intellij A lista de drop-down de execução remota](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-config-remote-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>Passo 5: Executar a aplicação em modo depurado

1. No seu projeto IntelliJ IDEA, abra `SparkSample.scala` e crie um ponto de rutura ao lado de `val rdd1` . No **menu Create Breakpoint para** o menu pop-up, selecione **linha em executar função ExecutarJob**.

    ![IntelliJ IDEA Adicione um ponto de rutura](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-create-breakpoint.png)

1. Para executar a aplicação, selecione o botão **Debug Run** ao lado da lista de recuos da configuração De execução **remota.**

    ![Ideia IntelliJ Selecione o botão Debug Run](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode-button.png)

1. Quando a execução do programa atinge o ponto de rutura, vê-se um separador **Debugger** na vidraça inferior.

    ![Ideia intelliJ Ver o separador Debugger](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-debugger-tab.png)

1. Para adicionar um relógio, selecione o **+** ícone ()

    ![IntelliJ debug-add-watch-variable](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    Neste exemplo, a aplicação quebrou antes da variável `rdd1` ser criada. Ao usar este relógio, podemos ver as primeiras cinco linhas na `rdd` variável. Selecione **Enter** (Introduzir).

    ![IntelliJ Executar o programa em modo depuramento](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    O que se vê na imagem anterior é que, em tempo de execução, poderá consultar terabytes de dados e depurar como a sua aplicação progride. Por exemplo, na saída mostrada na imagem anterior, pode ver-se que a primeira linha da saída é um cabeçalho. Com base nesta saída, pode modificar o seu código de aplicação para saltar a linha do cabeçalho, se necessário.

1. Pode agora selecionar o ícone **do Programa Retomar** para proceder à execução da sua aplicação.

    ![IntelliJ IDEA Selecione Programa de Currículo](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-remote-run.png)

1. Se a aplicação terminar com sucesso, deverá ver a saída como a seguinte:

    ![Saída da consola intelliJ IDEA depura](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete-window.png)

## <a name="next-steps"></a><a name="seealso"></a>Passos seguintes

* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demonstração

* Criar projeto Scala (vídeo): [Criar Aplicações Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depurador remoto (vídeo): [Use o Azure Toolkit para IntelliJ para depurar as aplicações Apache Spark remotamente num cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: Realize análise de dados interativas utilizando spark in HDInsight com ferramentas BI](apache-spark-use-bi-tools.md)
* [Faísca Apache com Machine Learning: Use faísca em HDInsight para analisar a temperatura do edifício usando dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Faísca Apache com Machine Learning: Use faísca em HDInsight para prever os resultados da inspeção alimentar](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de registo do site usando Apache Spark em HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações

* [Criar uma aplicação autónoma com o Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um aglomerado Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Use o Azure Toolkit para IntelliJ para criar aplicações Apache Spark para um cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Utilize o Azure Toolkit para o IntelliJ para depurar as aplicações Apache Spark remotamente através do SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utilize ferramentas HDInsight em Azure Toolkit para eclipse para criar aplicações Apache Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Use cadernos Apache Zeppelin com um cluster Apache Spark em HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o caderno Jupyter em um cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Track and debug jobs that run on a Apache Spark cluster in HDInsight](apache-spark-job-debugging.md)
