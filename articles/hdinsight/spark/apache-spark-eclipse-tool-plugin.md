---
title: 'Kit de ferramentas Azure para Eclipse: Criar aplicativos Scala para HDInsight Spark'
description: Utilize ferramentas HDInsight no Kit de Ferramentas Azure para o Eclipse desenvolver aplicações Spark escritas em Scala e submeta-as a um cluster HDInsight Spark, diretamente do Eclipse IDE.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/13/2019
ms.openlocfilehash: 07ed22879180d8126711eba9af0a2cac5b7b2953
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272113"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Utilize o Kit de Ferramentas Azure para eclipse para criar aplicações Apache Spark para um cluster HDInsight

Utilize ferramentas HDInsight no Kit de Ferramentas Azure para [o Eclipse](https://www.eclipse.org/) desenvolver aplicações [Apache Spark](https://spark.apache.org/) escritas em [Scala](https://www.scala-lang.org/) e submeta-as a um cluster De spark Azure HDInsight, diretamente a partir do Eclipse IDE. Pode utilizar o plug-in hDInsight Tools de várias maneiras diferentes:

* Para desenvolver e submeter uma aplicação Scala Spark num cluster HDInsight Spark.
* Para aceder aos seus recursos de cluster Azure HDInsight Spark.
* Para desenvolver e executar uma aplicação Scala Spark localmente.

## <a name="prerequisites"></a>Pré-requisitos

* Aglomerado apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Java Developer Kit (JDK) versão 8](https://aka.ms/azure-jdks).

* [Eclipse IDE](https://www.eclipse.org/downloads/). Este artigo usa o Eclipse IDE para Java Developers.

## <a name="install-required-plug-ins"></a>Instale plug-ins necessários

### <a name="install-azure-toolkit-for-eclipse"></a>Instalar o Azure Toolkit for Eclipse

Para instruções de instalação, consulte a instalação do [Kit de Ferramentas Azure para eclipse](https://docs.microsoft.com/azure/java/eclipse/azure-toolkit-for-eclipse-installation).

### <a name="install-the-scala-plug-in"></a>Instale o plug-in Scala

Ao abrir o Eclipse, as Ferramentas HDInsight detetam automaticamente se instalou o plug-in Scala. Selecione **OK** para continuar e, em seguida, siga as instruções para instalar o plug-in a partir do Mercado eclipse. Reiniciar o IDE após a instalação estar concluída.

![Instalação automática do plug-in Scala](./media/apache-spark-eclipse-tool-plugin/auto-installation-scala1.png)

### <a name="confirm-plug-ins"></a>Confirmar plug-ins

1. Navegue para **ajudar o** > **Eclipse Marketplace...**.

1. Selecione o separador **Instalado.**

1. Devia ver pelo menos:
    * Azure Toolkit \<para versão Eclipse>.
    * Versão Scala \<IDE>.

## <a name="sign-in-to-your-azure-subscription"></a>Inscreva-se na sua subscrição Azure

1. Comece o Eclipse IDE.

1. Navegue para o show **de janelas** >  **ver** > **outro...**  >  **Assine. . .**

1. A partir do diálogo **Show View,** navegue até **ao Azure** > **Azure Explorer,** e depois selecione **Open**.

   ![Apache Spark Eclipse mostra vista](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer1.png)

1. A partir do **Azure Explorer,** clique no nó **Azure** e, em seguida, selecione **Iniciar sessão**.

1. Na caixa de diálogo **'Sinal Azul',** escolha o método de autenticação, selecione **Iniciar sessão**e concluir o processo de início de sessão.

   ![Sinal de Eclipse Azure apache faísca](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer2.png)

1. Depois de se ter inscrito, a caixa de diálogo **Das Assinaturas** lista todas as subscrições do Azure associadas às credenciais. Pressione **Selecione** para fechar a caixa de diálogo.

   ![Selecione caixa de diálogo de assinaturas](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)

1. Do **Azure Explorer,** navegue até **ao Azure** >  **HDInsight** para ver os clusters HDInsight Spark sob a sua subscrição.

   ![Aglomerados de faíscas HDInsight em Azure Explorer3](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer3.png)

1. Você pode expandir ainda mais um nó de nome cluster para ver os recursos (por exemplo, contas de armazenamento) associados ao cluster.

   ![Expandir um nome de cluster para ver recursos](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer4.png)

## <a name="link-a-cluster"></a>Ligar um cluster

Pode ligar um cluster normal utilizando o nome de utilizador gerido por Ambari. Da mesma forma, para um cluster HDInsight unido ao domínio, pode `user1@contoso.com`ligar utilizando o domínio e o nome de utilizador, tais como .

1. Do **Azure Explorer,** clique à direita **HDInsight,** e selecione **Link A Cluster**.

   ![Menu de cluster de link Azure Explorer](./media/apache-spark-eclipse-tool-plugin/link-a-cluster-context-menu.png)

1. Introduza **o nome do cluster,** **o nome**do utilizador e a **palavra-passe**e, em seguida, selecione **OK**. Opcionalmente, introduza a Conta de Armazenamento, a Chave de Armazenamento e, em seguida, selecione Recipiente de Armazenamento para o explorador de armazenamento trabalhar na vista da árvore esquerda

   ![Link Novo diálogo de cluster HDInsight](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog1.png)

   > [!NOTE]  
   > Utilizamos a chave de armazenamento, nome de utilizador e senha ligados se o cluster tiver registado na subscrição do Azure e linked um cluster.
   > ![Contas de armazenamento do Azure Explorer](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)
   >
   > Para o utilizador apenas do teclado, quando o foco atual estiver na **Chave de Armazenamento,** é necessário utilizar **o CTRL+TAB** para se concentrar no próximo campo do diálogo.

1. Pode ver o cluster ligado ao **HDInsight**. Agora pode submeter uma candidatura a este cluster ligado.

   ![Cluster ligado ao Azure Explorer hdi](./media/apache-spark-eclipse-tool-plugin/hdinsight-linked-cluster.png)

1. Também pode desligar um cluster do **Azure Explorer.**

   ![Aglomerado sem ligação do Azure Explorer](./media/apache-spark-eclipse-tool-plugin/hdi-unlinked-cluster.png)

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Criar um projeto Spark Scala para um cluster HDInsight Spark

1. A partir do espaço de trabalho Eclipse IDE, selecione **File** > **New** > **Project...**.

1. No assistente do **Novo Projeto,** selecione **HDInsight Project** > **Spark no HDInsight (Scala)**. Em seguida, selecione **Seguinte**.

   ![Selecionando o projeto Spark on HDInsight (Scala)](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)

1. Na caixa de diálogo New **HDInsight Scala Project,** forneça os seguintes valores e, em seguida, selecione **Seguinte:**
   * Introduza um nome para o projeto.
   * Na área **DE JRE,** certifique-se de que utilize um ambiente de **execução JRE** definido para **JavaSE-1.7** ou posterior.
   * Na área da **Spark Library,** pode escolher **Use Maven para configurar a opção Spark SDK.**  A nossa ferramenta integra a versão adequada para Spark SDK e Scala SDK. Também pode escolher a opção **De Adicionar Spark SDK manualmente,** transferir e adicionar Spark SDK manualmente.

   ![Nova caixa de diálogo do Projeto HDInsight Scala](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)

1. Na caixa de diálogo seguinte, reveja os detalhes e, em seguida, selecione **Terminar**.

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Criar uma aplicação Scala para um cluster HDInsight Spark

1. A partir do **Package Explorer,** expanda o projeto que criou anteriormente. Src **src**de clique à direita, selecione **New** > **Other...**.

1. Na caixa de diálogo **Select a Wizard,** selecione **Scala Wizards** > **Scala Object**. Em seguida, selecione **Seguinte**.

   ![Selecione um assistente Criar um Objeto Scala](./media/apache-spark-eclipse-tool-plugin/create-scala-project1.png)

1. Na caixa de diálogo **Create New File,** introduza um nome para o objeto e, em seguida, selecione **Terminar**. Um editor de texto vai abrir.

   ![Novo assistente de ficheiro criar novo arquivo](./media/apache-spark-eclipse-tool-plugin/create-scala-project2.png)

1. No editor de texto, substitua o conteúdo atual pelo código abaixo:

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object MyClusterApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("MyClusterApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }
    }
    ```

1. Executar a aplicação num cluster HDInsight Spark:

   a. A partir do Package Explorer, clique no nome do projeto e, em seguida, selecione **Enviar a Aplicação Spark para HDInsight**.

   b. Na caixa de diálogo **de submissão** de faíscas, forneça os seguintes valores e, em seguida, selecione **Enviar:**

   * Para **nome do cluster,** selecione o cluster HDInsight Spark no qual pretende executar a sua aplicação.
   * Selecione um artefacto do projeto Eclipse ou selecione um de um disco rígido. O valor predefinido depende do item que clica direito do Package Explorer.
   * Na lista de abandono do **nome principal** da classe principal, o assistente de submissão exibe todos os nomes de objetos do seu projeto. Selecione ou introduza um que pretenda executar. Se selecionou um artefacto de um disco rígido, tem de introduzir manualmente o nome da classe principal. 
   * Uma vez que o código de aplicação neste exemplo não requer quaisquer argumentos de linha de comando ou JARs ou ficheiros de referência, pode deixar as restantes caixas de texto vazias.

     ![Caixa de diálogo de submissão de faísca sonuosa](./media/apache-spark-eclipse-tool-plugin/create-scala-project3.png)

1. O separador **Spark Submission** deve começar a mostrar o progresso. Pode parar a aplicação selecionando o botão vermelho na janela **De Submissão** de Faíscas. Também pode ver os registos para esta aplicação específica executada selecionando o ícone do globo (denotado pela caixa azul na imagem).

   ![Janela de submissão de faísca sonuosa](./media/apache-spark-eclipse-tool-plugin/create-scala-project4.png)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Aceda e gerencie os clusters HDInsight Spark utilizando ferramentas HDInsight em Kit de Ferramentas Azure para Eclipse

Pode realizar várias operações utilizando ferramentas HDInsight, incluindo aceder à saída de trabalho.

### <a name="access-the-job-view"></a>Aceda à vista de trabalho

1. No **Azure Explorer,** expanda **o HDInsight,** depois o nome do cluster Spark e, em seguida, selecione **Jobs**.

   ![Nó de visão de trabalho do Azure Explorer Eclipse](./media/apache-spark-eclipse-tool-plugin/eclipse-job-view-node.png)

1. Selecione o nó **Jobs.** Se a versão Java for inferior a **1.8,** as Ferramentas HDInsight lembretem automaticamente que instala o plug-in **de clipse E(fx).** Selecione **OK** para continuar e, em seguida, siga o assistente para instalá-lo a partir do Eclipse Marketplace e reiniciar o Eclipse.

   ![Instale plugin E (fx)clipse em falta](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

1. Abra a Vista de Emprego do nó **jobs.** No painel certo, o separador **Spark Job View** apresenta todas as aplicações que foram executadas no cluster. Selecione o nome da aplicação para a qual pretende ver mais detalhes.

   ![Apache Eclipse visualiza detalhes de registos de trabalho](./media/apache-spark-eclipse-tool-plugin/eclipse-view-job-logs.png)

   Pode então tomar qualquer uma destas ações:

   * Paire no gráfico de trabalho. Exibe informações básicas sobre o trabalho de corrida. Selecione o gráfico de trabalho e poderá ver as etapas e informações que cada trabalho gera.

     ![Apache Spark trabalho gráfico informação de estágio](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * Selecione o separador **Log** para visualizar registos frequentemente utilizados, incluindo **Driver Stderr,** **Driver Stdout**e **Diretório Info**.

     ![Apache Spark Eclipse informações de registo de trabalho](./media/apache-spark-eclipse-tool-plugin/eclipse-job-log-info.png)

   * Abra a história da Faísca UI e o Apache Hadoop YARN UI (ao nível da aplicação) selecionando as hiperligações na parte superior da janela.

### <a name="access-the-storage-container-for-the-cluster"></a>Aceda ao recipiente de armazenamento para o cluster

1. No Azure Explorer, expanda o nó de raiz **HDInsight** para ver uma lista de clusters HDInsight Spark que estão disponíveis.

1. Expanda o nome do cluster para ver a conta de armazenamento e o recipiente de armazenamento predefinido para o cluster.

   ![Conta de armazenamento e recipiente de armazenamento predefinido](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer5.png)

1. Selecione o nome do recipiente de armazenamento associado ao cluster. No painel direito, clique duas vezes na pasta **HVACOut.** Abra um dos ficheiros **de peça** para ver a saída da aplicação.

### <a name="access-the-spark-history-server"></a>Aceda ao servidor de histórico spark

1. No Azure Explorer, clique no nome do cluster Spark e, em seguida, **selecione Open Spark History UI**. Quando for solicitado, entre nas credenciais de administração do cluster. Especificou-os enquanto aprovisionava o cluster.

1. No painel de servidores do spark history, utiliza o nome da aplicação para procurar a aplicação que acabou de executar. No código anterior, deteta o `val conf = new SparkConf().setAppName("MyClusterApp")`nome da aplicação utilizando . Então, o seu nome de aplicação Spark era **MyClusterApp**.

### <a name="start-the-apache-ambari-portal"></a>Inicie o portal Apache Ambari

1. No Azure Explorer, clique no nome do cluster Spark e, em seguida, **selecione Open Cluster Management Portal (Ambari)**.

1. Quando for solicitado, entre nas credenciais de administração do cluster. Especificou-os enquanto aprovisionava o cluster.

### <a name="manage-azure-subscriptions"></a>Gerir subscrições do Azure

Por predefinição, a Ferramenta HDInsight no Kit de Ferramentas Azure para Eclipse lista os clusters Spark de todas as suas subscrições Azure. Se necessário, pode especificar as subscrições para as quais pretende aceder ao cluster.

1. No Azure Explorer, clique no nó raiz **azure** e, em seguida, **selecione Gerir Assinaturas**.

1. Na caixa de diálogo, limpe as caixas de verificação para a subscrição a que não pretende aceder e, em seguida, selecione **Fechar**. Também pode selecionar **O Sign out** se quiser assinar a subscrição do Azure.

## <a name="run-a-spark-scala-application-locally"></a>Executar uma aplicação Spark Scala localmente

Pode utilizar ferramentas HDInsight no Kit de Ferramentas Azure para o Eclipse executar aplicações Spark Scala localmente na sua estação de trabalho. Normalmente, estas aplicações não precisam de acesso a recursos de cluster, como um recipiente de armazenamento, e você pode executá-los e testá-los localmente.

### <a name="prerequisite"></a>Pré-requisito

Enquanto estiver a executar a aplicação Spark Scala local num computador Windows, poderá obter uma exceção, como explicado no [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Esta exceção ocorre porque **winUtils.exe** está desaparecido no Windows.

Para resolver este erro, precisa de [Winutils.exe](https://github.com/steveloughran/winutils) para um local como **C:\WinUtils\bin,** e depois adicione a variável ambiental **HADOOP_HOME** e desloque o valor da variável a **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Executar uma aplicação local Spark Scala

1. Inicie o Eclipse e crie um projeto. Na caixa de diálogo **New Project,** faça as seguintes escolhas e, em seguida, selecione **Next**.

1. No assistente do **Novo Projeto,** selecione **HDInsight Project** > **Spark na Amostra de Execução Local HDInsight (Scala)**. Em seguida, selecione **Seguinte**.

   ![Novo projeto seleciona um diálogo de assistente](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)

1. Para fornecer os detalhes do projeto, siga os passos 3 a 6 da secção anterior [Configurar um projeto Spark Scala para um cluster HDInsight Spark](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

1. O modelo adiciona um código de amostra **(LogQuery)** sob a pasta **SRC** que pode executar localmente no seu computador.

   ![Localização da aplicação logquery local scala](./media/apache-spark-eclipse-tool-plugin/local-scala-application.png)

1. Clique à direita **LogQuery.scala** e selecione **Executar Como** > **1 Aplicação Scala**. A saída como esta aparece no separador **Consola:**

   ![Resultado da execução local da aplicação de faíscas](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="reader-only-role"></a>Papel só para leitores

Quando os utilizadores submetem o emprego a um cluster com permissão de papel apenas para leitores, as credenciais Ambari são necessárias.

### <a name="link-cluster-from-context-menu"></a>Link cluster do menu de contexto

1. Inscreva-se na conta de papel só para leitores.

2. Do **Azure Explorer,** expanda o **HDInsight** para visualizar os clusters HDInsight que estão na sua subscrição. Os clusters marcados **como "Role:Reader"** só têm permissão de papel apenas para leitores.

    ![Clusters hDInsight Spark no leitor de papéis do Explorador Azure](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer6.png)

3. Clique no cluster com permissão de papel apenas para o leitor. Selecione **Ligar este cluster** do menu de contexto para o cluster de ligação. Introduza o nome de utilizador ambari e a palavra-passe.

    ![Aglomerados de faíscas HDInsight na ligação Azure Explorer](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer7.png)

4. Se o cluster estiver ligado com sucesso, o HDInsight será refrescado.
   A fase do agrupamento ficará ligada.
  
    ![Aglomerados de faíscas HDInsight em Azure Explorer ligados](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Link cluster expandindo o nó jobs

1. Clique no nó **Jobs,** Cluster Job Access A janela **recusa-se.**

2. Clique em **Ligar este cluster** para ligar o cluster.

    ![Aglomerados de faíscas HDInsight em Azure Explorer9](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer9.png)

### <a name="link-cluster-from-spark-submission-window"></a>Cluster de ligação da janela de submissão de faíscas

1. Crie um Projeto HDInsight.

2. Clique na embalagem. Em seguida, selecione **Submeter a Aplicação spark para HDInsight**.

   ![Aglomerados de faíscas HDInsight no Azure Explorer submetem](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer11.png)

3. Selecione um cluster, que tenha permissão de função apenas para o Nome do **Cluster**. A mensagem de aviso mostra. Pode clicar em **Link este cluster** para link cluster.

   ![Aglomerados de faíscas HDInsight em Azure Explorer ligam este](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer15.png)

### <a name="view-storage-accounts"></a>Ver Contas de Armazenamento

* Para clusters com permissão de papel apenas para leitores, clique no nó **de Contas de Armazenamento,** **Storage Access A** janela negada aparece.

   ![Aglomerados de faíscas HDInsight no armazenamento do Explorador Azure](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer13.png)

   ![Aglomerados de faíscas HDInsight no Azure Explorer negados](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer12.png)

* Para clusters ligados, clique no nó **de Contas de Armazenamento,** a janela **De armazenamento Acesso Negado** aparece.

   ![Aglomerados hDInsight Spark no Azure Explorer negados2](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer14.png)

## <a name="known-problems"></a>Problemas conhecidos

Ao utilizar o **Link A Cluster,** sugiro que forneça credenciais de armazenamento.

![cluster de ligação com eclipses credenciais de armazenamento](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

Há dois modos para submeter os trabalhos. Se a credencial de armazenamento for fornecida, o modo de lote será utilizado para submeter o trabalho. Caso contrário, será utilizado o modo interativo. Se o cluster estiver ocupado, pode ter o erro abaixo.

![eclipse obter erro quando cluster ocupado](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png "eclipse obter erro quando cluster ocupado")

![eclipse obter erro quando fios ocupados cluster](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png "eclipse obter erro quando fios ocupados cluster")

## <a name="see-also"></a>Consulte também

* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: Realizar análise interativa de dados utilizando spark in HDInsight com ferramentas BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Use Spark no HDInsight para analisar a temperatura do edifício utilizando dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Use Spark no HDInsight para prever resultados da inspeção alimentar](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando Apache Spark em HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Criação e execução de aplicações

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Utilize o Kit de Ferramentas Azure para o IntelliJ para criar e submeter aplicações spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilize o Kit de Ferramentas Azure para o IntelliJ para depurar aplicações apache Spark remotamente através da VPN](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilize o Kit de Ferramentas Azure para o IntelliJ para depurar aplicações apache Spark remotamente através do SSH](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Use os cadernos Apache Zeppelin com um cluster Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o portátil Jupyter no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Gestão de recursos

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
