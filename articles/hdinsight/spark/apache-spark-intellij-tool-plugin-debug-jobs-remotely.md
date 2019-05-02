---
title: 'Azure Toolkit para IntelliJ: Depurar aplicações remotamente no HDInsight Spark '
description: Saiba como utilizar ferramentas do HDInsight no Azure Toolkit para IntelliJ para depurar remotamente aplicações do Spark que são executadas em clusters do HDInsight através de VPN.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.openlocfilehash: 30d52f1ac6a68a3202de59a0b4cab8edfb7ed042
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64701086"
---
# <a name="use-azure-toolkit-for-intellij-to-debug-apache-spark-applications-remotely-in-hdinsight-through-vpn"></a>Utilizar o Azure Toolkit para IntelliJ depurar aplicações do Apache Spark remotamente no HDInsight através de VPN

Recomendamos que a depuração [Apache Spark](https://spark.apache.org/) aplicações remotamente através de SSH. Para obter instruções, consulte [depurar remotamente aplicações Apache Spark num cluster do HDInsight com o Azure Toolkit para IntelliJ através de SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

Este artigo fornece orientações passo a passo sobre como utilizar as ferramentas do HDInsight no Azure Toolkit para IntelliJ para submeter uma tarefa do Spark num cluster do Spark do HDInsight e, em seguida, depurá-lo remotamente a partir do computador de secretária. Para concluir estas tarefas, tem de efetuar os seguintes passos de alto nível:

1. Crie um site a site ou ponto a site Azure virtual network. Os passos neste documento partem do princípio de que utiliza uma rede de site a site.
1. Crie um cluster do Spark no HDInsight que faz parte da rede virtual site a site.
1. Certifique-se a conectividade entre o nó principal do cluster e o seu ambiente de trabalho.
1. Criar uma aplicação Scala no IntelliJ IDEA e, em seguida, configure-o para depuração remota.
1. Executar e depurar a aplicação.

## <a name="prerequisites"></a>Pré-requisitos
* **Uma subscrição do Azure**. Para obter mais informações, consulte [obtenha uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Um cluster do Apache Spark no HDInsight**. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Kit de desenvolvimento do Java de Oracle**. Pode instalá-lo do [Web site do Oracle](https://aka.ms/azure-jdks).
* **IntelliJ IDEIA**. Este artigo utiliza a versão 2017.1. Pode instalá-lo do [Web site da JetBrains](https://www.jetbrains.com/idea/download/).
* **Ferramentas do HDInsight no Azure Toolkit para IntelliJ**. Ferramentas do HDInsight para IntelliJ estão disponíveis como parte do Azure Toolkit para IntelliJ. Para obter instruções sobre como instalar o Kit de ferramentas do Azure, consulte [instalar o Azure Toolkit para IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **Iniciar sessão na sua subscrição do Azure a partir IntelliJ IDEIA**. Siga as instruções em [utilizar o Azure Toolkit para IntelliJ criar aplicativos de Apache Spark para um cluster do HDInsight](apache-spark-intellij-tool-plugin.md).
* **Solução de exceção**. Ao executar a aplicação Spark Scala para depuração remota num computador Windows, poderá receber uma exceção. Essa exceção é explicada na [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356) e ocorre devido a um ficheiro de WinUtils.exe em falta no Windows. Para contornar este erro, deve [transfira o ficheiro executável](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) para uma localização, como **C:\WinUtils\bin**. Adicionar uma **HADOOP_HOME** variável de ambiente e, em seguida, defina o valor da variável a ser **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Passo 1: Criar uma rede virtual do Azure
Siga as instruções das ligações seguintes para criar uma rede virtual do Azure e, em seguida, certifique-se a conectividade entre o computador de secretária e a rede virtual:

* [Criar uma VNet com uma ligação de VPN de site a site com o portal do Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Criar uma VNet com uma ligação de VPN de site a site com o PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Configurar uma ligação de ponto a site a uma rede virtual com o PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Passo 2: Criar um cluster do Spark no HDInsight
Recomendamos que crie também um cluster do Apache Spark no HDInsight do Azure, que faz parte da rede virtual do Azure que criou. Utilize as informações disponíveis no [baseado em Linux criar clusters no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Como parte da configuração opcional, selecione a rede virtual do Azure que criou no passo anterior.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>Passo 3: Verificar a conectividade entre o nó principal do cluster e o seu ambiente de trabalho
1. Obtenha o endereço IP do nó principal. Abra a IU do Ambari para o cluster. No painel do cluster, selecione **Dashboard**.

    ![Selecione o Dashboard no Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-ambari-ui.png)
1. Na IU do Ambari, selecione **anfitriões**.

    ![Selecione os anfitriões em Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-hosts.png)
1. É apresentada uma lista de nós principais, nós de trabalho e nós zookeeper. Os nós principais tem um **hn*** prefixo. Selecione o nó principal primeiro.

    ![Localizar o nó principal no Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/cluster-headnodes.png)
1. Do **resumo** painel na parte inferior da página que é aberta, copie a **endereço IP** do nó principal e o **nome de anfitrião**.

    ![Encontrar o endereço IP no Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address.png)
1. Adicionar o endereço IP e o nome de anfitrião do nó principal para o **anfitriões** ficheiro no computador onde pretende executar e depurar remotamente a tarefa do Spark. Isto permite-lhe comunicar com o nó principal, utilizando o endereço IP, bem como o nome do anfitrião.

   a. Abra um ficheiro do bloco de notas com permissões elevadas. Do **arquivo** menu, selecione **aberto**e, em seguida, procure a localização do ficheiro de anfitriões. Num computador Windows, a localização é **C:\Windows\System32\Drivers\etc\hosts**.

   b. Adicione as seguintes informações para o **anfitriões** ficheiro:

           # For headnode0
           192.xxx.xx.xx hn0-nitinp
           192.xxx.xx.xx hn0-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net

           # For headnode1
           192.xxx.xx.xx hn1-nitinp
           192.xxx.xx.xx hn1-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
1. Do computador ligado à rede virtual do Azure que é utilizada pelo cluster do HDInsight, certifique-se de que pode efetuar o ping a nós principais, utilizando o endereço IP, bem como o nome do anfitrião.
1. Utilizar o SSH para ligar ao nó principal do cluster, seguindo as instruções em [ligar a um cluster do HDInsight através de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). No nó principal do cluster, ping no endereço IP do computador desktop. Teste a conectividade com os dois endereços IP atribuído ao computador:

    - Um para a ligação de rede
    - Um para a rede virtual do Azure

1. Repita os passos para o outro nó principal.

## <a name="step-4-create-an-apache-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Passo 4: Criar uma aplicação do Apache Spark Scala com ferramentas do HDInsight no Azure Toolkit para IntelliJ e configurá-la para depuração remota
1. Abra o IntelliJ IDEA e crie um novo projeto. Na caixa de diálogo **Novo Projeto**, faça o seguinte:

    ![Selecione o novo modelo de projeto no IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Selecione **HDInsight** > **Spark no HDInsight (Scala)**.

    b. Selecione **Seguinte**.
1. Nos próximos **novo projeto** caixa de diálogo, efetue o seguinte e, em seguida, selecione **concluir**:

    - Introduza um nome do projeto e localização.

    - Na lista pendente **SDK do Projeto**, selecione **Java 1.8** para o cluster do Spark 2.x ou selecione **Java 1.7** para o cluster do Spark 1.x.

    - Na **versão do Spark** na lista pendente, o Assistente de criação de projeto Scala integra-se a versão apropriada para o SDK do Spark e o SDK de Scala. Se a versão do cluster do Spark for anterior à 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark2.x**. Este exemplo utiliza o **Spark 2.0.2 (Scala 2.11.8)**.
  
   ![Selecione a versão SDK e do Spark do projeto](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
1. O projeto de Spark cria automaticamente um artefacto por si. Para ver o artefacto, faça o seguinte:

    a. No menu **File** (Ficheiro), selecione **Project Structure** (Estrutura do Projeto).

    b. Na **estrutura do projeto** caixa de diálogo, selecione **artefactos** para ver o artefacto de predefinição que é criado. Também pode criar seu próprio artefacto selecionando o sinal de adição (**+**).

   ![Criar o JAR](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/default-artifact.png)


1. Adicione bibliotecas ao seu projeto. Para adicionar uma biblioteca, efetue o seguinte:

    a. Com o botão direito no nome do projeto na árvore de projeto e, em seguida, selecione **abra as definições do módulo**. 

    b. Na **estrutura do projeto** caixa de diálogo, selecione **bibliotecas**, selecione o (**+**) símbolo e, em seguida, selecione **do Maven** .

    ![Adicionar uma biblioteca](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/add-library.png)

    c. Na **transferir a biblioteca do repositório Maven** diálogo caixa, procure e adicione as seguintes bibliotecas:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`
1. Cópia `yarn-site.xml` e `core-site.xml` do cluster de nó de avançar e adicioná-los ao projeto. Utilize os seguintes comandos para copiar os ficheiros. Pode usar [Cygwin](https://cygwin.com/install.html) para executar o seguinte `scp` nós principais de comandos para copiar os ficheiros do cluster:

        scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .

    Uma vez que já adicionamos o endereço IP de nó principal do cluster e nomes de anfitriões para o ficheiro de anfitriões na área de trabalho, podemos usar o `scp` comandos da seguinte forma:

        scp sshuser@hn0-nitinp:/etc/hadoop/conf/core-site.xml .
        scp sshuser@hn0-nitinp:/etc/hadoop/conf/yarn-site.xml .

    Para adicionar estes ficheiros ao seu projeto, copiá-los sob a **/src** pasta na sua árvore de projeto, por exemplo `<your project directory>\src`.
1. Atualização do `core-site.xml` ficheiro para efetuar as seguintes alterações:

   a. Substitua a chave de encriptação. O `core-site.xml` ficheiro inclui a chave de encriptação para a conta de armazenamento associada ao cluster. Na `core-site.xml` ficheiro que adicionou ao projeto, substitua a chave encriptada com a chave de armazenamento real associada à conta de armazenamento predefinida. Para obter mais informações, consulte [gerir as chaves de acesso de armazenamento](../../storage/common/storage-account-manage.md#access-keys).

           <property>
                 <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
                 <value>access-key-associated-with-the-account</value>
           </property>
   b. Remover as seguintes entradas de `core-site.xml`:

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
   c. Guarde o ficheiro.
1. Adicione a classe principal para a sua aplicação. Do **Explorador de projeto**, clique com botão direito **src**, aponte para **New**e, em seguida, selecione **Scala classe**.

    ![Selecione a classe principal](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)
1. Na **criar uma nova classe de Scala** caixa de diálogo caixa, forneça um nome, selecione **objeto** no **tipo** caixa e, em seguida, selecione **OK**.

    ![Criar nova classe Scala](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)
1. Na `MyClusterAppMain.scala` de ficheiros, cole o seguinte código. Este código cria o Spark, contexto e abre um `executeJob` método a partir do `SparkSample` objeto.

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

1. Repita os passos 8 e 9 para adicionar um novo objeto Scala chamado `*SparkSample`. Adicione o seguinte código para esta classe. Este código lê os dados de Hvac (disponível em todos os clusters do HDInsight Spark). Obtém as linhas que têm apenas um dígito na coluna sétima no ficheiro CSV e, em seguida, grava a saída para **/HVACOut** no contentor de armazenamento padrão para o cluster.

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
1. Repita a classe de passos 8 e 9 para adicionar uma nova chamada `RemoteClusterDebugging`. Essa classe implementa a estrutura de teste do Spark que é utilizada para depurar as aplicações. Adicione o seguinte código para o `RemoteClusterDebugging` classe:

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

     Existem algumas coisas importantes a ter em conta:

      * Para `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`, certifique-se de que o assembly de Spark JAR está disponível no armazenamento de cluster no caminho especificado.
      * Para `setJars`, especifique a localização onde o artefacto JAR é criado. Normalmente, é `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`.
1. Na`*RemoteClusterDebugging` classe, com o botão direito do `test` palavra-chave e, em seguida, selecione **criar configuração de RemoteClusterDebugging**.

    ![Criar uma configuração remota](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

1. Na **criar configuração de RemoteClusterDebugging** caixa de diálogo, forneça um nome para a configuração e, em seguida, selecione **tipo de teste** como o **nome do teste**. Deixe todos os outros valores como configurações padrão. Selecione **Apply** (Aplicar) e **OK**.

    ![Adicionar os detalhes de configuração](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)
1. Deverá ver agora uma **remoto execute** configuração na lista pendente na barra de menus.

    ![A lista de lista pendente de execução remota](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/config-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>Passo 5: Executar a aplicação no modo de depuração
1. No seu projeto de IntelliJ IDEA, abra `SparkSample.scala` e criar um ponto de interrupção junto a `val rdd1`. Na **criar o ponto de interrupção para** menu de pop-up, selecione **linha na função executeJob**.

    ![Adicionar um ponto de interrupção](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-breakpoint.png)
1. Para executar a aplicação, selecione o **depurar execute** junto aos **executar remoto** configuração na lista pendente.

    ![Selecione o botão de depuração executar](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode.png)
1. Quando a execução do programa atinge o ponto de interrupção, verá uma **depurador** separador no painel inferior.

    ![Consulte o separador do depurador](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch.png)
1. Para adicionar uma veja, selecione o (**+**) ícone.

    ![Selecione o ícone +](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    Neste exemplo, o aplicativo, interrompida antes da variável `rdd1` foi criado. Ao utilizar este watch, podemos ver as primeiras cinco linhas na variável `rdd`. Prima **Enter**.

    ![Execute o programa no modo de depuração](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    O que vê na imagem anterior é que, em tempo de execução, pode consultar terabytes de dados e de depuração como seu aplicativo avança. Por exemplo, na saída mostrada na imagem anterior, pode ver que a primeira linha da saída é um cabeçalho. Com base neste resultado, pode modificar o código da aplicação para ignorar a linha de cabeçalho, se necessário.
1. Agora, pode selecionar o **programa de retomar** ícone para continuar com seu aplicativo seja executado.

    ![Selecione o programa de retomar](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-run.png)
1. Se a aplicação é concluída com êxito, deverá ver o resultado como o seguinte:

    ![Saída da consola](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete.png)

## <a name="seealso"></a>Passos seguintes
* [Descrição geral: Apache Spark no HDInsight do Azure](apache-spark-overview.md)

### <a name="demo"></a>Demonstração
* Crie projeto de Scala (vídeo): [Criar aplicações do Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depuração remota (vídeo): [Utilizar o Azure Toolkit para IntelliJ depurar aplicações do Apache Spark remotamente num cluster do HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: Efetuar análise de dados interativa com o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura do edifício com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de registos de Web site com o Apache Spark no HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Apache Spark com o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilizar o Azure Toolkit para IntelliJ para criar aplicativos de Apache Spark para um cluster do HDInsight](apache-spark-intellij-tool-plugin.md)
* [Utilizar o Azure Toolkit para IntelliJ para depurar aplicações de Apache Spark remotamente através de SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utilizar ferramentas do HDInsight para IntelliJ com a Sandbox da Hortonworks](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utilizar ferramentas do HDInsight no Azure Toolkit para Eclipse para criar aplicações do Apache Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Utilizar blocos de notas do Zeppelin do Apache com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter num cluster do Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas que são executados num cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)
