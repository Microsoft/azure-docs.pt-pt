---
title: 'Azure Toolkit for IntelliJ: Depurar aplicativos remotamente no HDInsight Spark '
description: Saiba como usar as ferramentas do HDInsight no Azure Toolkit for IntelliJ para depurar remotamente aplicativos Spark executados em clusters HDInsight por meio de VPN.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.openlocfilehash: a558c0e767610f1fefdf29ca461a476c7bfcee59
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327329"
---
# <a name="use-azure-toolkit-for-intellij-to-debug-apache-spark-applications-remotely-in-hdinsight-through-vpn"></a>Usar Azure Toolkit for IntelliJ para depurar Apache Spark aplicativos remotamente no HDInsight por meio de VPN

É recomendável depurar [Apache Spark](https://spark.apache.org/) aplicativos remotamente por meio de SSH. Para obter instruções, consulte [depurar remotamente Apache Spark aplicativos em um cluster HDInsight com Azure Toolkit for IntelliJ por meio do ssh](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

Este artigo fornece orientações passo a passo sobre como usar as ferramentas do HDInsight no Azure Toolkit for IntelliJ para enviar um trabalho do Spark em um cluster do HDInsight Spark e, em seguida, depurá-lo remotamente do computador desktop. Para concluir essas tarefas, você deve executar as seguintes etapas de alto nível:

1. Crie uma rede virtual do Azure site a site ou ponto a site. As etapas neste documento pressupõem que você use uma rede site a site.
1. Crie um cluster Spark no HDInsight que faça parte da rede virtual site a site.
1. Verifique a conectividade entre o nó principal do cluster e sua área de trabalho.
1. Crie um aplicativo escalar no IntelliJ IDEA e, em seguida, configure-o para depuração remota.
1. Execute e depure o aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Para obter mais informações, consulte [obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Um cluster apache Spark no HDInsight**. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Kit de desenvolvimento Oracle Java**. Você pode instalá-lo do [site da Oracle](https://aka.ms/azure-jdks).
* **INTELLIJ ideia**. Este artigo usa a versão 2017,1. Você pode instalá-lo no [site do JetBrains](https://www.jetbrains.com/idea/download/).
* **Ferramentas do HDInsight no Azure Toolkit for IntelliJ**. As ferramentas do HDInsight para IntelliJ estão disponíveis como parte do Azure Toolkit for IntelliJ. Para obter instruções sobre como instalar o kit de ferramentas do Azure, consulte [instalar Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **Entre em sua assinatura do Azure do INTELLIJ Idea**. Siga as instruções em [usar Azure Toolkit for IntelliJ para criar Apache Spark aplicativos para um cluster HDInsight](apache-spark-intellij-tool-plugin.md).
* **Exceção alternativa**. Ao executar o aplicativo do Spark escalar para depuração remota em um computador Windows, você pode obter uma exceção. Essa exceção é explicada em [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356) e ocorre devido a um arquivo WinUtils. exe ausente no Windows. Para contornar esse erro, você deve [baixar o arquivo executável](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) em um local como **C:\WinUtils\bin**. Adicione uma variável de ambiente **HADOOP_HOME** e, em seguida, defina o valor da variável para **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Passo 1: Criar uma rede virtual do Azure

Siga as instruções dos links a seguir para criar uma rede virtual do Azure e, em seguida, verifique a conectividade entre o computador desktop e a rede virtual:

* [Criar uma VNet com uma conexão VPN site a site usando o portal do Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Criar uma VNet com uma conexão VPN site a site usando o PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Configurar uma conexão ponto a site com uma rede virtual usando o PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Passo 2: Criar um cluster do Spark no HDInsight

Recomendamos que você também crie um cluster Apache Spark no Azure HDInsight que faça parte da rede virtual do Azure que você criou. Use as informações disponíveis em [Criar clusters baseados em Linux no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Como parte da configuração opcional, selecione a rede virtual do Azure que você criou na etapa anterior.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>Passo 3: Verifique a conectividade entre o nó principal do cluster e sua área de trabalho

1. Obtenha o endereço IP do nó principal. Abra a interface do usuário do Ambari para o cluster. Na folha do cluster, selecione **painel**.

    ![Selecionar painel no Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-apache-ambari.png)

1. Na interface do usuário do amAmbari, selecione **hosts**.

    ![Selecionar hosts no Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/apache-ambari-hosts1.png)

1. Você verá uma lista de nós de cabeçalho, nós de trabalho e nós Zookeeper. Os nós de cabeçalho têm um prefixo **HN**\*. Selecione o primeiro nó principal.

    ![Localizar o nó de cabeçalho no Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-cluster-headnodes.png)

1. No painel **Resumo** na parte inferior da página que é aberta, copie o **endereço IP** do nó principal e o nome do **host**.

    ![Localizar o endereço IP no Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address1.png)

1. Adicione o endereço IP e o nome do host do nó principal ao arquivo de **hosts** no computador em que você deseja executar e depure remotamente o trabalho do Spark. Isso permite que você se comunique com o nó principal usando o endereço IP, bem como o nome do host.

   a. Abra um arquivo do bloco de notas com permissões elevadas. No menu **arquivo** , selecione **abrir**e localize o local do arquivo de hosts. Em um computador Windows, o local é **C:\Windows\System32\Drivers\etc\hosts**.

   b. Adicione as seguintes informações ao arquivo de **hosts** :

    ```
    # For headnode0
    192.xxx.xx.xx hn0-nitinp
    192.xxx.xx.xx hn0-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    
    # For headnode1
    192.xxx.xx.xx hn1-nitinp
    192.xxx.xx.xx hn1-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    ```

1. No computador que você conectou à rede virtual do Azure que é usada pelo cluster HDInsight, verifique se você pode executar ping nos nós de cabeçalho usando o endereço IP, bem como o nome do host.

1. Use o SSH para se conectar ao nó de cabeçalho do cluster seguindo as instruções em [conectar-se a um cluster HDInsight usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). No nó principal do cluster, execute ping no endereço IP do computador desktop. Teste a conectividade com os dois endereços IP atribuídos ao computador:

    - Um para a conexão de rede
    - Um para a rede virtual do Azure

1. Repita as etapas para o outro nó de cabeçalho.

## <a name="step-4-create-an-apache-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Passo 4: Criar um aplicativo Apache Spark escalabilidade usando as ferramentas do HDInsight no Azure Toolkit for IntelliJ e configurá-lo para depuração remota

1. Abra o IntelliJ IDEA e crie um novo projeto. Na caixa de diálogo **Novo Projeto**, faça o seguinte:

    ![Selecione o novo modelo de projeto em IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Selecione **HDInsight** > **Spark no HDInsight (Scala)** .

    b. Selecione **Seguinte**.
1. Na próxima caixa de diálogo **novo projeto** , faça o seguinte e, em seguida, selecione **concluir**:

    - Introduza um nome do projeto e localização.

    - Na lista pendente **SDK do Projeto**, selecione **Java 1.8** para o cluster do Spark 2.x ou selecione **Java 1.7** para o cluster do Spark 1.x.

    - Na lista suspensa **versão do Spark** , o assistente de criação de projeto escalar integra a versão apropriada para o SDK do Spark e o SDK do Scale. Se a versão do cluster do Spark for anterior à 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark2.x**. Este exemplo utiliza o **Spark 2.0.2 (Scala 2.11.8)** .
  
   ![Selecione o SDK do projeto e a versão do Spark](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
1. O projeto do Spark cria automaticamente um artefato para você. Para exibir o artefato, faça o seguinte:

    a. No menu **File** (Ficheiro), selecione **Project Structure** (Estrutura do Projeto).

    b. Na caixa de diálogo **estrutura do projeto** , selecione **artefatos** para exibir o artefato padrão criado. Você também pode criar seu próprio artefato selecionando o sinal de adição **+** ().

   ![Artefatos da ideia IntelliJ criar jar](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-default-artifact.png)

1. Adicione bibliotecas ao seu projeto. Para adicionar uma biblioteca, faça o seguinte:

    a. Clique com o botão direito do mouse no nome do projeto na árvore do projeto e selecione **abrir configurações do módulo**.

    b. Na caixa de diálogo **estrutura do projeto** , selecione **bibliotecas**, selecione o **+** símbolo () e, em seguida, selecione **do Maven**.

    ![Biblioteca de download do IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-add-library.png)

    c. Na caixa de diálogo **baixar biblioteca do repositório do Maven** , pesquise e adicione as seguintes bibliotecas:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`

1. Copie `yarn-site.xml` e`core-site.xml` do nó principal do cluster e adicione-os ao projeto. Use os comandos a seguir para copiar os arquivos. Você pode usar [Cygwin](https://cygwin.com/install.html) para executar os seguintes `scp` comandos para copiar os arquivos dos nós de cabeçalho do cluster:

    ```bash
    scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .
    ```

    Como já adicionamos o endereço IP do nó principal do cluster e os nomes de host para o arquivo de hosts na área `scp` de trabalho, podemos usar os comandos da seguinte maneira:

    ```bash
    scp sshuser@hn0-nitinp:/etc/hadoop/conf/core-site.xml .
    scp sshuser@hn0-nitinp:/etc/hadoop/conf/yarn-site.xml .
    ```

    Para adicionar esses arquivos ao seu projeto, copie-os na pasta **/src** na árvore do projeto, por exemplo `<your project directory>\src`.

1. Atualize o `core-site.xml` arquivo para fazer as seguintes alterações:

   a. Substitua a chave criptografada. O `core-site.xml` arquivo inclui a chave criptografada para a conta de armazenamento associada ao cluster. `core-site.xml` No arquivo que você adicionou ao projeto, substitua a chave criptografada pela chave de armazenamento real associada à conta de armazenamento padrão. Para obter mais informações, consulte [gerenciar suas chaves de acesso de armazenamento](../../storage/common/storage-account-manage.md#access-keys).

    ```xml
    <property>
            <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
            <value>access-key-associated-with-the-account</value>
    </property>
    ```

   b. Remova as seguintes entradas de `core-site.xml`:

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

1. Adicione a classe principal para seu aplicativo. No **Gerenciador de projetos**, clique com o botão direito do mouse em **src**, aponte para **novo**e selecione **classe escalar**.

    ![IntelliJ IDEA selecionar a classe principal](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)

1. Na caixa de diálogo **criar nova classe escala** , forneça um nome, selecione **objeto** na caixa **tipo** e, em seguida, selecione **OK**.

    ![IntelliJ IDEA criar nova classe escalar](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)

1. `MyClusterAppMain.scala` No arquivo, Cole o código a seguir. Esse código cria o contexto do Spark e abre `executeJob` um método `SparkSample` do objeto.

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

1. Repeat steps 8 and 9 to add a new Scala object called `*SparkSample`. Add the following code to this class. This code reads the data from the HVAC.csv (available in all HDInsight Spark clusters). It retrieves the rows that only have one digit in the seventh column in the CSV file, and then writes the output to **/HVACOut** under the default storage container for the cluster.

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

1. Repita as etapas 8 e 9 para adicionar uma nova classe `RemoteClusterDebugging`chamada. Essa classe implementa a estrutura de teste do Spark que é usada para depurar os aplicativos. Adicione o seguinte código à `RemoteClusterDebugging` classe:

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

     Há algumas coisas importantes a serem observadas:

      * Para `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`o, verifique se o JAR do assembly do Spark está disponível no armazenamento do cluster no caminho especificado.
      * Para `setJars`, especifique o local onde o artefato jar é criado. Normalmente, é `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`.

1. Na classe, clique com o botão direito `test` do mouse na palavra-chave e selecione **criar configuração de RemoteClusterDebugging.** `*RemoteClusterDebugging`

    ![IntelliJ IDEA criar uma configuração remota](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

1. Na caixa de diálogo **criar configuração do RemoteClusterDebugging** , forneça um nome para a configuração e, em seguida, selecione **tipo de teste** como o **nome do teste**. Deixe todos os outros valores como as configurações padrão. Selecione **Apply** (Aplicar) e **OK**.

    ![Criar configuração do RemoteClusterDebugging](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)

1. Agora você deve ver uma lista suspensa de configuração de **execução remota** na barra de menus.

    ![IntelliJ a lista suspensa execução remota](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-config-remote-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>Passo 5: Executar o aplicativo no modo de depuração

1. Em seu projeto IntelliJ IDEA, abra `SparkSample.scala` e crie um ponto de interrupção `val rdd1`ao lado de. No menu de **criação de ponto de interrupção para** pop-up, selecione **linha na função executeJob**.

    ![IntelliJ IDEA adicionar um ponto de interrupção](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-create-breakpoint.png)

1. Para executar o aplicativo, selecione o botão **executar depuração** ao lado da lista suspensa configuração de **execução remota** .

    ![IntelliJ ideia selecione o botão de execução de depuração](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode-button.png)

1. Quando a execução do programa atingir o ponto de interrupção, você verá uma guia **depurador** no painel inferior.

    ![IntelliJ IDEA exibir a guia depurador](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-debugger-tab.png)

1. Para adicionar uma inspeção, selecione o ícone **+** ().

    ![IntelliJ Debug-Add-Watch-variável](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    Neste exemplo, o aplicativo quebrou antes da criação da `rdd1` variável. Ao usar esta inspeção, podemos ver as primeiras cinco linhas na variável `rdd`. Prima **Enter**.

    ![IntelliJ executar o programa no modo de depuração](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    O que você vê na imagem anterior é que, em tempo de execução, você pode consultar terabytes de dados e depurar como o aplicativo progride. Por exemplo, na saída mostrada na imagem anterior, você pode ver que a primeira linha da saída é um cabeçalho. Com base nessa saída, você pode modificar o código do aplicativo para ignorar a linha de cabeçalho, se necessário.

1. Agora você pode selecionar o ícone **retomar programa** para prosseguir com a execução do aplicativo.

    ![IntelliJ IDEA selecionar retomar programa](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-remote-run.png)

1. Se o aplicativo for concluído com êxito, você deverá ver uma saída semelhante à seguinte:

    ![Saída do console do depurador IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete-window.png)

## <a name="seealso"></a>Passos seguintes

* [Sobre Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demonstração

* Criar projeto escalar (vídeo): [Criar Apache Spark aplicativos escalares](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depuração remota (vídeo): [Usar Azure Toolkit for IntelliJ para depurar aplicativos Apache Spark remotamente em um cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: Executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para analisar a temperatura de edifício usando dados de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando Apache Spark no HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações

* [Criar uma aplicação autónoma com o Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Usar Azure Toolkit for IntelliJ para criar aplicativos Apache Spark para um cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Usar Azure Toolkit for IntelliJ para depurar Apache Spark aplicativos remotamente por meio de SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Usar as ferramentas do HDInsight no Azure Toolkit for Eclipse para criar aplicativos Apache Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Usar notebooks do Apache Zeppelin com um cluster Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o Jupyter notebook em um cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos que são executados em um cluster Apache Spark no HDInsight](apache-spark-job-debugging.md)
