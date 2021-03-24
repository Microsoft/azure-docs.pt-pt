---
title: 'Azure Toolkit para Eclipse: Criar aplicativos Scala para HDInsight Spark'
description: Utilize ferramentas HDInsight em Azure Toolkit para eclipse para desenvolver aplicações Spark escritas em Scala e submetê-las a um cluster HDInsight Spark, diretamente do Eclipse IDE.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/13/2019
ms.openlocfilehash: e4f19afc0edf0f43b64548ae1c93ed5ff8dfa8a1
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866578"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Use o kit de ferramentas Azure para eclipse para criar aplicações Apache Spark para um cluster HDInsight

Utilize ferramentas HDInsight em Azure Toolkit para [eclipse](https://www.eclipse.org/) para desenvolver aplicações [Apache Spark](https://spark.apache.org/) escritas em [Scala](https://www.scala-lang.org/) e submetê-las a um cluster Azure HDInsight Spark, diretamente do Eclipse IDE. Pode utilizar o plug-in das Ferramentas HDInsight de várias maneiras:

* Para desenvolver e submeter uma aplicação Scala Spark num cluster HDInsight Spark.
* Para aceder aos seus recursos de cluster Azure HDInsight Spark.
* Para desenvolver e executar uma aplicação Scala Spark localmente.

## <a name="prerequisites"></a>Pré-requisitos

* Aglomerado de Faíscas Apache em HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Java Developer Kit (JDK) versão 8](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* [Eclipse IDE](https://www.eclipse.org/downloads/). Este artigo utiliza o Eclipse IDE para Desenvolvedores de Java.

## <a name="install-required-plug-ins"></a>Instalar plug-ins necessários

### <a name="install-azure-toolkit-for-eclipse"></a>Instalar o Azure Toolkit for Eclipse

Para obter instruções de instalação, consulte [instalar o kit de ferramentas Azure para eclipse](/azure/developer/java/toolkit-for-eclipse/installation).

### <a name="install-the-scala-plug-in"></a>Instale o plug-in Scala

Quando abre o Eclipse, as Ferramentas HDInsight detetam automaticamente se instalou o plug-in Scala. Selecione **OK** para continuar e, em seguida, siga as instruções para instalar o plug-in a partir do Eclipse Marketplace. Reinicie o IDE após a conclusão da instalação.

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/auto-installation-scala1.png" alt-text="Instalação automática do plug-in Scala" border="true":::

### <a name="confirm-plug-ins"></a>Confirmar plug-ins

1. Navegue para **ajudar o**  >  **Eclipse Marketplace...**.

1. Selecione o separador **Instalado.**

1. Devia ver pelo menos:
    * Azure Toolkit para Eclipse \<version> .
    * Scala \<version> IDE.

## <a name="sign-in-to-your-azure-subscription"></a>Inscreva-se na sua assinatura Azure

1. Iniciar eclipse IDE.

1. Navegue para ver a  >   **janela**  >  **outro...**  >  **Inscreva-se.**. .

1. A partir do diálogo **Show View,** navegue até **Azure**  >  **Azure Explorer** e, em seguida, selecione **Open**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer1.png" alt-text="Vista de show do Eclipse de Faísca de Apache" border="true":::

1. A partir do **Azure Explorer,** clique à direita no nó **Azure** e, em seguida, selecione **Iniciar sôm.**

1. Na caixa **de diálogo Azure Sign In,** escolha o método de autenticação, selecione **Iniciar sedura** e complete o processo de inscrição.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer2.png" alt-text="Sinal de Azure Eclipse de Faísca de Apache Spark" border="true":::

1. Depois de ter assinado, a caixa de diálogo **das Suas Subscrições** lista todas as subscrições do Azure associadas às credenciais. **Prima Selecione** para fechar a caixa de diálogo.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png" alt-text="Selecione caixa de diálogo de subscrições" border="true":::

1. A partir do **Azure Explorer,** navegue até **Azure**  >   **HDInsight** para ver os clusters HDInsight Spark sob a sua subscrição.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer3.png" alt-text="HdInsight Spark clusters em Azure Explorer3" border="true":::

1. Pode expandir ainda um nó de nome de cluster para ver os recursos (por exemplo, contas de armazenamento) associados ao cluster.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer4.png" alt-text="Expandir um nome de cluster para ver recursos" border="true":::

## <a name="link-a-cluster"></a>Ligue um cluster

Pode ligar um cluster normal utilizando o nome de utilizador gerido por Ambari. Da mesma forma, para um cluster HDInsight ligado a domínio, pode ligar-se utilizando o domínio e o nome de utilizador, tais como `user1@contoso.com` .

1. A partir do **Azure Explorer,** clique à direita **HDInsight** e selecione **Link A Cluster**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/link-a-cluster-context-menu.png" alt-text="Menu de cluster de ligação Azure Explorer" border="true":::

1. Introduza **o Nome do Cluster,** **o Nome do Utilizador** e **a Palavra-passe,** em seguida, selecione **OK**. Opcionalmente, insira a Conta de Armazenamento, a Chave de Armazenamento e, em seguida, selecione o Recipiente de Armazenamento para o explorador de armazenamento para trabalhar na vista da árvore esquerda

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog1.png" alt-text="Link Novo diálogo de cluster HDInsight" border="true":::

   > [!NOTE]  
   > Utilizamos a chave de armazenamento, nome de utilizador e palavra-passe ligados se o cluster tiver registado a subscrição do Azure e ligar um cluster.
   > :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png" alt-text="Contas de armazenamento do Azure Explorer" border="true":::
   >
   > Para o único utilizador do teclado, quando o foco atual estiver na **Chave de Armazenamento,** é necessário utilizar **o Ctrl+TAB** para se concentrar no próximo campo no diálogo.

1. Pode ver o cluster ligado em **HDInsight**. Agora pode submeter um pedido a este cluster ligado.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdinsight-linked-cluster.png" alt-text="Aglomerado ligado a Azure Explorer hdi" border="true":::

1. Também pode desvincular um cluster do **Azure Explorer.**

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdi-unlinked-cluster.png" alt-text="Aglomerado de Azure Explorer desvinculado" border="true":::

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Crie um projeto Spark Scala para um cluster HDInsight Spark

1. A partir do espaço de trabalho Eclipse IDE, selecione **File**  >  **New**  >  **Project...**.

1. No assistente do **Novo Projeto,** selecione **HDInsight Project**  >  **Spark on HDInsight (Scala)**. Em seguida, selecione **Seguinte**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png" alt-text="Selecionando o projeto Spark on HDInsight (Scala)" border="true":::

1. Na caixa de diálogo **New HDInsight Scala Project,** forneça os seguintes valores e, em seguida, selecione **Seguinte**:
   * Introduza um nome para o projeto.
   * Na área **JRE,** certifique-se de que o USO de um ambiente de **execução JRE** está definido para **JavaSE-1.7** ou mais tarde.
   * Na área da **Biblioteca Spark,** pode escolher Use Maven para configurar a opção **Spark SDK.**  A nossa ferramenta integra a versão adequada para Spark SDK e Scala SDK. Também pode escolher a opção **De adicionar SDK manualmente,** baixar e adicionar Spark SDK manualmente.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png" alt-text="Nova caixa de diálogo do Projeto HDInsight Scala" border="true":::

1. Na caixa de diálogo seguinte, reveja os detalhes e, em seguida, **selecione Terminar**.

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Crie uma aplicação Scala para um cluster HDInsight Spark

1. A partir do **Package Explorer,** expanda o projeto que criou anteriormente. Clique à direita **src**, selecione **New**  >  **Other...**.

1. Na caixa de diálogo **de assistente selecione uma** caixa de diálogo para **assistentes, selecione Scala Wizards**  >  **Scala Object**. Em seguida, selecione **Seguinte**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project1.png" alt-text="Selecione um assistente Criar um objeto Scala" border="true":::

1. Na caixa de diálogo **'Criar Novo Ficheiro',** introduza um nome para o objeto e, em seguida, selecione **Terminar**. Um editor de texto vai abrir.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project2.png" alt-text="Novo assistente de ficheiro cria novo arquivo" border="true":::

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

   a. A partir do Package Explorer, clique com o botão direito no nome do projeto e, em seguida, **selecione Submeter a Aplicação de Faísca para HDInsight**.

   b. Na caixa de diálogo **de submissão** de faíscas, forneça os seguintes valores e, em seguida, **selecione Enviar:**

   * Para **o Nome do Cluster**, selecione o cluster HDInsight Spark no qual pretende executar a sua aplicação.
   * Selecione um artefacto do projeto Eclipse ou selecione um de um disco rígido. O valor predefinido depende do item que clica à direita no Package Explorer.
   * Na lista de drop-down **de nome da classe principal,** o assistente de submissão apresenta todos os nomes de objetos do seu projeto. Selecione ou introduza um que pretende executar. Se selecionar um artefacto de um disco rígido, deve introduzir o nome da classe principal manualmente. 
   * Como o código de aplicação neste exemplo não requer argumentos de linha de comando ou JARs de referência ou ficheiros, pode deixar as restantes caixas de texto vazias.

     :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project3.png" alt-text="Caixa de diálogo de submissão de faíscas Apache Spark" border="true":::

1. O **separador De submissão de faíscas** deve começar a mostrar o progresso. Pode parar a aplicação selecionando o botão vermelho na janela **de submissão de faíscas.** Também pode visualizar os registos desta aplicação específica executada selecionando o ícone do globo (denotado pela caixa azul na imagem).

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project4.png" alt-text="Janela de submissão de faíscas apaches" border="true":::

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Aceda e gere clusters HDInsight Spark utilizando ferramentas HDInsight em Azure Toolkit para Eclipse

Pode executar várias operações utilizando Ferramentas HDInsight, incluindo aceder à saída do trabalho.

### <a name="access-the-job-view"></a>Aceda à vista de emprego

1. No **Azure Explorer,** expanda **o HDInsight,** em seguida, o nome do cluster Spark e, em seguida, selecione **Jobs**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-job-view-node.png" alt-text="Nó de vista de trabalho do Azure Explorer Eclipse" border="true":::

1. Selecione o nó **Jobs.** Se a versão Java for inferior **a 1.8,** as Ferramentas HDInsight automaticamente lembretes de instalar o plug-in **de clipsE (fx)clipse.** Selecione **OK** para continuar e, em seguida, siga o assistente para instalá-lo a partir do Eclipse Marketplace e reiniciar o Eclipse.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png" alt-text="Instale plugin e(fx)clipse em falta" border="true":::

1. Abra a vista de emprego do nó **Jobs.** No painel direito, o separador **Spark Job View** exibe todas as aplicações que foram executadas no cluster. Selecione o nome da aplicação para a qual deseja ver mais detalhes.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-job-logs.png" alt-text="Apache Eclipse visualizar detalhes de registos de trabalho" border="true":::

   Em seguida, pode tomar qualquer uma destas ações:

   * Paire no gráfico de trabalho. Mostra informações básicas sobre o trabalho em execução. Selecione o gráfico de trabalho e poderá ver as fases e informações que cada trabalho gera.

     :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png" alt-text="Informação do palco do gráfico de trabalho de Apache Spark" border="true":::

   * Selecione o **separador Registar** para visualizar registos frequentemente utilizados, incluindo **Driver Stderr,** **Driver Stdout** e **Diretório Info**.

     :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-job-log-info.png" alt-text="Informação de registo de trabalho do Apache Spark Eclipse" border="true":::

   * Abra a UI da história da faísca e a UI Apache Hadoop YARN (ao nível da aplicação) selecionando as hiperligações na parte superior da janela.

### <a name="access-the-storage-container-for-the-cluster"></a>Aceda ao recipiente de armazenamento para o cluster

1. No Azure Explorer, expanda o nó de raiz **HDInsight** para ver uma lista de clusters HDInsight Spark que estão disponíveis.

1. Expanda o nome do cluster para ver a conta de armazenamento e o recipiente de armazenamento predefinido para o cluster.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer5.png" alt-text="Conta de armazenamento e recipiente de armazenamento predefinido" border="true":::

1. Selecione o nome do recipiente de armazenamento associado ao cluster. No painel direito, clique duas vezes na pasta **AVACOut.** Abra uma das **partes-ficheiros** para ver a saída da aplicação.

### <a name="access-the-spark-history-server"></a>Aceda ao servidor histórico Spark

1. No Azure Explorer, clique com o botão direito do seu nome de cluster Spark e, em seguida, selecione **Open Spark History UI**. Quando for solicitado, insira as credenciais de administração para o cluster. Especificou-os enquanto aagrupava o cluster.

1. No painel de instrumentos do servidor Spark History, utiliza o nome da aplicação para procurar a aplicação que acabou de terminar de executar. No código anterior, define o nome da aplicação utilizando `val conf = new SparkConf().setAppName("MyClusterApp")` . Assim, o seu nome de aplicação Spark era **MyClusterApp.**

### <a name="start-the-apache-ambari-portal"></a>Inicie o portal Apache Ambari

1. No Azure Explorer, clique com o botão direito do seu nome de cluster Spark e, em seguida, selecione **O Portal de Gestão de Cluster Aberto (Ambari)**.

1. Quando for solicitado, insira as credenciais de administração para o cluster. Especificou-os enquanto aagrupava o cluster.

### <a name="manage-azure-subscriptions"></a>Gerir subscrições do Azure

Por predefinição, a Ferramenta HDInsight no Azure Toolkit para Eclipse lista os clusters Spark de todas as suas subscrições Azure. Se necessário, pode especificar as subscrições para as quais pretende aceder ao cluster.

1. No Azure Explorer, clique com o nó de raiz **Azure** e, em seguida, **selecione Gerir Subscrições**.

1. Na caixa de diálogo, limpe as caixas de verificação para a subscrição a que não pretende aceder e, em seguida, selecione **Close**. Também pode selecionar **'Iniciar's** se pretender assinar a subscrição do Azure.

## <a name="run-a-spark-scala-application-locally"></a>Executar uma aplicação Spark Scala localmente

Pode utilizar ferramentas HDInsight em Azure Toolkit para eclipse para executar aplicações Spark Scala localmente na sua estação de trabalho. Normalmente, estas aplicações não precisam de acesso a recursos de cluster, como um recipiente de armazenamento, e você pode executá-los e testá-los localmente.

### <a name="prerequisite"></a>Pré-requisito

Enquanto executa a aplicação local Spark Scala num computador Windows, poderá obter uma exceção, como explicado no [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Esta exceção ocorre porque **WinUtils.exe** falta no Windows.

Para resolver este erro, é necessário [Winutils.exe](https://github.com/steveloughran/winutils) a um local como **C:\WinUtils\bin**, e, em seguida, adicione a variável ambiental **HADOOP_HOME** e desavendo o valor da variável para **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Executar uma aplicação local Spark Scala

1. Inicie o Eclipse e crie um projeto. Na caixa de diálogo **New Project,** faça as seguintes escolhas e, em seguida, selecione **Seguinte**.

1. No assistente do **Novo Projeto,** selecione **HDInsight Project**  >  **Spark em HDInsight Local Run Sample (Scala)**. Em seguida, selecione **Seguinte**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png" alt-text="Novo projeto seleciona um diálogo de assistente" border="true":::

1. Para fornecer os detalhes do projeto, siga os passos 3 a 6 da secção anterior [Configurar um projeto Spark Scala para um cluster HDInsight Spark](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

1. O modelo adiciona um código de amostra **(LogQuery)** sob a pasta **src** que pode ser executado localmente no seu computador.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/local-scala-application.png" alt-text="Localização da aplicação de escala local LogQuery" border="true":::

1. Clique com o botão direito **LogQuery.scala** e selecione **Executar Como**  >  **1 Aplicação Scala**. Saída como esta aparece no **separador Consola:**

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png" alt-text="Resultado da execução local da aplicação de faísca" border="true":::

## <a name="reader-only-role"></a>Papel apenas para o leitor

Quando os utilizadores submetem trabalho a um cluster com permissão de função apenas para leitores, são necessárias credenciais Ambari.

### <a name="link-cluster-from-context-menu"></a>Cluster de ligação do menu de contexto

1. Inscreva-se com a conta de função apenas para leitor.

2. A partir do **Azure Explorer,** **expanda o HDInsight** para ver clusters HDInsight que estão na sua subscrição. Os clusters marcados **"Role:Reader"** só têm permissão de função apenas para leitor.

    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer6.png" alt-text="HDInsight Spark clusters no leitor de papel do Azure Explorer" border="true":::

3. Clique com o botão direito no cluster com permissão de função apenas para leitor. Selecione **Link este cluster** do menu de contexto para o cluster de ligação. Insira o nome de utilizador E palavra-passe Ambari.

    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer7.png" alt-text="HdInsight Spark clusters na ligação Azure Explorer" border="true":::

4. Se o cluster estiver ligado com sucesso, o HDInsight será atualizado.
   A fase do cluster ficará ligada.
  
    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer8.png" alt-text="HdInsight Spark clusters no Azure Explorer ligados" border="true":::

### <a name="link-cluster-by-expanding-jobs-node"></a>Link cluster através da expansão do nó jobs

1. Clique no nó **Jobs,** **Cluster Job Access Negada** janela aparece.

2. Clique **em Ligar este cluster** para ligar o cluster.

    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer9.png" alt-text="HdInsight Spark clusters em Azure Explorer9" border="true":::

### <a name="link-cluster-from-spark-submission-window"></a>Cluster de ligação da janela de submissão de faíscas

1. Criar um Projeto HDInsight.

2. Clique com a direita no pacote. Em seguida, **selecione Submeter a Aplicação de Faísca para HDInsight**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer11.png" alt-text="HdInsight Spark clusters em Azure Explorer submeter" border="true":::

3. Selecione um cluster, que tem permissão de função apenas para o leitor para **o Nome cluster**. A mensagem de aviso aparece. Pode clicar em **Ligar este cluster** para ligar o cluster.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer15.png" alt-text="HDInsight Spark clusters em Azure Explorer link esta" border="true":::

### <a name="view-storage-accounts"></a>Ver Contas de Armazenamento

* Para clusters com permissão de função apenas para leitor, clique no nó **contas de armazenamento,** a janela **"Storage Access Negado"** aparece.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer13.png" alt-text="HdInsight Spark clusters no armazenamento do Azure Explorer" border="true":::

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer12.png" alt-text="HdInsight Spark clusters em Azure Explorer negados" border="true":::

* Para clusters ligados, clique no nó **contas de armazenamento,** a janela **"Storage Access Negado"** aparece.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer14.png" alt-text="HdInsight Spark clusters em Azure Explorer negado2" border="true":::

## <a name="known-problems"></a>Problemas conhecidos

Ao utilizar **o Link A Cluster,** sugiro que forneça credenciais de armazenamento.

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png" alt-text="aglomerado de ligação com eclipses credenciais de armazenamento" border="true":::

Há dois modos para submeter os trabalhos. Se for fornecida credencial de armazenamento, o modo de lote será utilizado para submeter o trabalho. Caso contrário, utilizar-se-á o modo interativo. Se o cluster estiver ocupado, pode ficar com o erro abaixo.

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png " alt-text="eclipse obter erro quando cluster ocupado" border="true":::

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png " alt-text="eclipse obter erro quando cluster fio ocupado" border="true":::

## <a name="see-also"></a>Ver também

* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: Realizar análise de dados interativas usando Spark in HDInsight com ferramentas BI](apache-spark-use-bi-tools.md)
* [Faísca Apache com Machine Learning: Use faísca em HDInsight para analisar a temperatura do edifício usando dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Faísca Apache com Machine Learning: Use faísca em HDInsight para prever os resultados da inspeção alimentar](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de registo do site usando Apache Spark em HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Criação e execução de aplicações

* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um aglomerado Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Use o Azure Toolkit para o IntelliJ criar e submeter aplicações Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilize o Azure Toolkit para o IntelliJ para depurar as aplicações apache spark remotamente através da VPN](./apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilize o Azure Toolkit para o IntelliJ para depurar as aplicações Apache Spark remotamente através do SSH](./apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Use cadernos Apache Zeppelin com um cluster Apache Spark em HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para Jupyter Notebook em aglomerado de faíscas apaches para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Use pacotes externos com cadernos Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Gestão de recursos

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)