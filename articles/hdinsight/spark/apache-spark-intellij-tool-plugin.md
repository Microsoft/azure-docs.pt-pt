---
title: 'Kit de ferramentas Azure para IntelliJ: App Spark - HDInsight'
description: Utilize o Kit de Ferramentas Azure para o IntelliJ para desenvolver aplicações Spark escritas em Scala e submeta-as a um cluster HDInsight Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/13/2020
ms.openlocfilehash: a3884fdfbbc215c305053d8615d690880f4026ea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81314150"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-hdinsight-cluster"></a>Utilize o Kit de Ferramentas Azure para o IntelliJ para criar aplicações Apache Spark para cluster HDInsight

Este artigo demonstra como desenvolver aplicações Apache Spark no Azure HDInsight utilizando o **plug-in Azure Toolkit** para o IntelliJ IDE. [O Azure HDInsight](../hdinsight-overview.md) é um serviço de análise gerido e de código aberto na nuvem. O serviço permite-lhe usar estruturas de código aberto como Hadoop, Apache Spark, Apache Hive e Apache Kafka.

Pode utilizar o plug-in **Azure Toolkit** de algumas formas:

* Desenvolva e envie uma aplicação Scala Spark para um cluster HDInsight Spark.
* Aceda aos seus recursos de cluster Azure HDInsight Spark.
* Desenvolva e execute uma aplicação Scala Spark localmente.

Neste artigo, vai aprender a:
> [!div class="checklist"]
> * Utilize o Kit de Ferramentas Azure para plug-in IntelliJ
> * Desenvolver aplicações Apache Spark
> * Envie uma aplicação ao cluster Azure HDInsight

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Kit de Desenvolvimento Oracle Java.](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)  Este artigo usa a versão Java 8.0.202.

* IntelliJ IDEA. Este artigo utiliza [intelliJ IDEA Community ver.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Kit de ferramentas Azure para IntelliJ.  Consulte [a instalação do Kit de Ferramentas Azure para IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalar o plug-in do Scala para o IntelliJ IDEA

Passos para instalar o plugin Scala:

1. Abra o IntelliJ IDEA.

2. No ecrã de boas-vindas, navegue para **Configurar** > **plugins** para abrir a janela **Plugins.**

    ![IntelliJ IDEA permite plugin scala](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin1.png)

3. Selecione **Instalar** para o plugin Scala que está em destaque na nova janela.  

    ![IntelliJ IDEA instala plugin scala](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. Após a instalação bem-sucedida do plug-in, tem de reiniciar o IDE.

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Criar uma aplicação Spark Scala para um cluster HDInsight Spark

1. Inicie o IntelliJ IDEA e selecione **Create New Project** para abrir a janela new **project.**

2. Selecione **Azure Spark/HDInsight** a partir do painel esquerdo.

3. Selecione **Spark Project (Scala)** a partir da janela principal.

4. A partir da lista de drop-down da **ferramenta Build,** selecione uma das seguintes opções:
   * **Maven** para o apoio ao assistente de criação de projetos scala.
   * **SBT** para gerir as dependências e construção para o projeto Scala.

     ![Caixa de diálogo IntelliJ IDEA Novo Projeto](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Selecione **Seguinte**.

6. Na janela **New Project,** forneça as seguintes informações:  

    |  Propriedade   | Descrição   |  
    | ----- | ----- |  
    |Nome do projeto| Insira um nome.  Este artigo `myApp`usa.|  
    |Localização&nbsp;do projeto| Insira o local para salvar o seu projeto.|
    |Projeto SDK| Este campo pode estar em branco na sua primeira utilização do IDEA.  Selecione **New...** e navegue para o seu JDK.|
    |Versão Spark|O assistente de criação integra a versão adequada para Spark SDK e Scala SDK. Se a versão do cluster do Spark for anterior à 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark2.x**. Este exemplo utiliza **o Spark 2.3.0 (Scala 2.11.8)**.|

    ![Selecionando o Apache Spark SDK](./media/apache-spark-intellij-tool-plugin/intellij-new-project.png)

7. Selecione **Concluir**.  Pode levar alguns minutos até que o projeto fique disponível.

8. O projeto Spark cria automaticamente um artefacto para si. Para ver o artefacto, faça os seguintes passos:

   a. Da barra de menus, navegue até a Estrutura do Projeto **arquivo...** > **Project Structure...**.

   b. A partir da janela Estrutura do **Projeto,** selecione **Artefactos**.  

   c. **Selecione Cancelar** depois de ver o artefacto.

      ![Informações de artefactos na caixa de diálogo](./media/apache-spark-intellij-tool-plugin/default-artifact-dialog.png)

9. Adicione o código fonte da sua aplicação fazendo os seguintes passos:

    a. Do Project, navegue até à**scala****principal** > do **myApp** > **SRC** > .  

    b. Clique na **scala**direita e, em seguida, navegue para a **Classe New** > **Scala**.

   ![Comandos para criar uma classe Scala do Projeto](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. Na caixa de diálogo **Create New Scala Class,** forneça um nome, selecione **Object** na lista de drop-down **Kind** e, em seguida, selecione **OK**.

     ![Criar nova caixa de diálogo classe Scala](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. O ficheiro **myApp.scala** abre-se então na vista principal. Substitua o código predefinido pelo código abaixo:  

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object myApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("myApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV file
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }

    }
    ```

    O código lê os dados do HVAC.csv (disponíveis em todos os clusters HDInsight Spark), recupera as linhas que têm apenas `/HVACOut` um dígito na sétima coluna do ficheiro CSV, e escreve a saída para debaixo do recipiente de armazenamento predefinido para o cluster.

## <a name="connect-to-your-hdinsight-cluster"></a>Ligue-se ao seu cluster HDInsight

O utilizador pode [iniciar sessão na subscrição do Azure,](#sign-in-to-your-azure-subscription)ou [ligar um cluster HDInsight](#link-a-cluster). Utilize o nome de utilizador/palavra-passe ou domínio ambari para se ligar ao seu cluster HDInsight.

### <a name="sign-in-to-your-azure-subscription"></a>Inscreva-se na sua subscrição Azure

1. A partir da barra de menus, navegue para **ver** > **ferramentas Windows** > **Azure Explorer**.

   ![IntelliJ IDEA mostra explorador azul](./media/apache-spark-intellij-tool-plugin/show-azure-explorer1.png)

2. Do Azure Explorer, clique no nó **Azure** e, em seguida, selecione **Sign In**.

   ![IntelliJ IDEA explorador direito clique azul](./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png)

3. Na caixa de diálogo **'Sinal Azul',** escolha **Iniciar sessão do dispositivo**e, em seguida, selecione Iniciar **sessão**.

    !['IntelliJ IDEA azure login do dispositivo'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer2.png)

4. Na caixa de diálogo de login do **dispositivo Azure,** clique em **Copiar&Abrir**.

   !['IntelliJ IDEA azure device login'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer5.png)

5. Na interface do navegador, colhe o código e, em seguida, clique **em Next**.

   !['Microsoft introduza o diálogo de código para HDI'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer6.png)

6. Introduza as suas credenciais Azure e, em seguida, feche o navegador.

   !['Microsoft introduza diálogo de e-mail para HDI'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer7.png)

7. Depois de se ter inscrito, a caixa de diálogo **Select Subscriptions** lista todas as subscrições do Azure que estão associadas às credenciais. Selecione a sua subscrição e, em seguida, selecione o botão **Select.**

    ![A caixa de diálogo Selecionar Subscrições](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

8. Do **Azure Explorer,** expanda o **HDInsight** para ver os clusters HDInsight Spark que estão nas suas subscrições.

    ![Vista principal do IntelliJ IDEA Azure Explorer](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer3.png)

9. Para ver os recursos (por exemplo, contas de armazenamento) que estão associados ao cluster, pode expandir ainda mais um nó de nome de cluster.

    ![Contas de armazenamento do Azure Explorer](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer4.png)

### <a name="link-a-cluster"></a>Ligar um cluster

Pode ligar um cluster HDInsight utilizando o nome de utilizador gerido apache Ambari. Da mesma forma, para um cluster HDInsight unido ao domínio, pode `user1@contoso.com`ligar utilizando o domínio e o nome de utilizador, tais como . Também pode ligar o cluster do Serviço Livy.

1. A partir da barra de menus, navegue para **ver** > **ferramentas Windows** > **Azure Explorer**.

1. A partir do Azure Explorer, clique no nó **HDInsight** e, em seguida, selecione **Link A Cluster**.

   ![Menu de contexto de cluster de ligação Azure Explorer](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. As opções disponíveis na janela **Link A Cluster** variarão dependendo do valor que selecionar da lista de drop-down do Link Resource **Type.**  Introduza os seus valores e, em seguida, selecione **OK**.

    * **HDInsight Cluster**  
  
        |Propriedade |Valor |
        |----|----|
        |Tipo de recurso de ligação|Selecione **HDInsight Cluster** a partir da lista de lançamentos.|
        |Nome/URL do Cluster| Introduza o nome do cluster.|
        |Tipo de Autenticação| Deixar como **Autenticação Básica**|
        |Nome de Utilizador| Introduza o nome do utilizador do cluster, o predefinido é o administrador.|
        |Palavra-passe| Introduza a palavra-passe para o nome do utilizador.|

        ![IntelliJ IDEA liga um diálogo de cluster](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Serviço Livy**  
  
        |Propriedade |Valor |
        |----|----|
        |Tipo de recurso de ligação|Selecione **Livy Service** da lista de lançamentos.|
        |Ponto Final de Livy| Insira Livy Endpoint|
        |Nome do Cluster| Introduza o nome do cluster.|
        |Ponto final de fio|Opcional.|
        |Tipo de Autenticação| Deixar como **Autenticação Básica**|
        |Nome de Utilizador| Introduza o nome do utilizador do cluster, o predefinido é o administrador.|
        |Palavra-passe| Introduza a palavra-passe para o nome do utilizador.|

        ![IntelliJ IDEA link Diálogo de cluster Livy](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

1. Pode ver o seu cluster ligado a partir do nó **HDInsight.**

   ![Cluster 1 ligado ao Azure Explorer](./media/apache-spark-intellij-tool-plugin/hdinsight-linked-cluster.png)

1. Também pode desligar um cluster do **Azure Explorer.**

   ![Aglomerado sem ligação do Azure Explorer](./media/apache-spark-intellij-tool-plugin/hdi-unlinked-cluster.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Executar uma aplicação Spark Scala em um cluster HDInsight Spark

Depois de criar uma aplicação Scala, pode submetê-la ao cluster.

1. Do Project, navegue até ao **myApp** > **src** > **main** > **scala** > **myApp**.  Clique no **myApp**e selecione **Submeter a Aplicação Spark** (provavelmente estará localizado na parte inferior da lista).

      ![A Aplicação de Ignição Submeter ao comando HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. Na janela de diálogo de pedido de **ignição,** selecione **1. Faísca no HDInsight**.

3. Na janela de **configuração Editar,** forneça os seguintes valores e, em seguida, selecione **OK:**

    |Propriedade |Valor |
    |----|----|
    |Aglomerados de faíscas (apenas Linux)|Selecione o cluster HDInsight Spark no qual pretende executar a sua aplicação.|
    |Selecione um Artefacto para submeter|Deixe a definição predefinida.|
    |Nome principal da classe|O valor predefinido é a classe principal do ficheiro selecionado. Pode alterar a classe selecionando a **...** elipse.  e escolher outra classe.|
    |Configurações de trabalho|Pode alterar as teclas padrão e, ou valores. Para mais informações, consulte [Apache Livy REST API](https://livy.incubator.apache.org/docs/latest/rest-api.html).|
    |Argumentos de linha de comando|Pode introduzir argumentos separados pelo espaço para a classe principal, se necessário.|
    |Frascos referenciados e ficheiros referenciados|Pode introduzir os caminhos para os frascos e ficheiros referenciados, se houver. Também pode navegar em ficheiros no sistema de ficheiros virtual Azure, que atualmente suporta apenas o cluster ADLS Gen 2. Para mais informações: [Configuração de Faísca apache](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Ver também, [Como carregar recursos para cluster](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer).|
    |Armazenamento de upload de trabalho|Expanda para revelar opções adicionais.|
    |Tipo de armazenamento|Selecione **Use o Azure Blob para fazer o upload** da lista de lançamentos.|
    |Conta de Armazenamento|Insira a sua conta de armazenamento.|
    |Chave de armazenamento|Introduza a sua chave de armazenamento.|
    |Recipiente de armazenamento|Selecione o seu recipiente de armazenamento da lista de depósitos assim que a Conta de **Armazenamento** e **a Chave** de Armazenamento estiverem inseridas.|

    ![A caixa de diálogo de submissão de faíscas](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

4. Selecione **SparkJobRun** para submeter o seu projeto ao cluster selecionado. O **separador Remote Spark Job no Cluster** mostra o progresso da execução do trabalho na parte inferior. Pode parar a aplicação clicando no botão vermelho.

    ![Janela de submissão de faísca sonuosa](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Depurar aplicações Apache Spark localmente ou remotamente num cluster HDInsight

Recomendamos também outra forma de submeter a aplicação Spark ao cluster. Pode fazê-lo definindo os **parâmetros nas configurações Run/Debug** IDE. Consulte [as aplicações Debug Apache Spark localmente ou remotamente num cluster HDInsight com o Kit de Ferramentas Azure para IntelliJ através do SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Aceda e gerencie clusters HDInsight Spark utilizando o Kit de Ferramentas Azure para intelliJ

Pode fazer várias operações utilizando o Azure Toolkit para o IntelliJ.  A maioria das operações são iniciadas pelo **Azure Explorer.**  A partir da barra de menus, navegue para **ver** > **ferramentas Windows** > **Azure Explorer**.

### <a name="access-the-job-view"></a>Aceda à vista de trabalho

1. Do Azure Explorer, navegue até **ao HDInsight** > \<o seu cluster> > **Jobs**.

    ![IntelliJ Azure Explorer Job ver o nó](./media/apache-spark-intellij-tool-plugin/intellij-job-view-node.png)

2. No painel certo, o separador **Spark Job View** apresenta todas as aplicações que foram executadas no cluster. Selecione o nome da aplicação para a qual pretende ver mais detalhes.

    ![Detalhes da aplicação de vista de emprego de faísca](./media/apache-spark-intellij-tool-plugin/intellij-view-job-logs.png)

3. Para mostrar informações básicas sobre o trabalho de execução, paire sobre o gráfico de trabalho. Para ver o gráfico de estágios e a informação que cada trabalho gera, selecione um nó no gráfico de trabalho.

    ![Detalhes do estágio de spark job view job](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Para visualizar registos frequentemente utilizados, tais como *Driver Stderr,* *Driver Stdout*e *Directy Info,* selecione o separador **Log.**

    ![Detalhes do log da vista do trabalho de faísca](./media/apache-spark-intellij-tool-plugin/intellij-job-log-info.png)

5. Pode ver a História da Faísca UI e a YARN UI (ao nível da aplicação). Selecione um link na parte superior da janela.

### <a name="access-the-spark-history-server"></a>Aceda ao servidor de histórico spark

1. Do Azure Explorer, expanda **o HDInsight,** clique à direita no nome do cluster Spark e, em seguida, selecione **Open Spark History UI**.  
2. Quando for solicitado, introduza as credenciais de administração do cluster, que especificou quando configurao o cluster.

3. No painel de instrumentos do servidor de histórico Spark, pode utilizar o nome da aplicação para procurar a aplicação que acabou de executar. No código anterior, deteta o `val conf = new SparkConf().setAppName("myApp")`nome da aplicação utilizando . O nome da sua aplicação Spark é **myApp**.

### <a name="start-the-ambari-portal"></a>Inicie o portal Ambari

1. Do Azure Explorer, expanda **o HDInsight,** clique à direita no nome do cluster Spark e, em seguida, selecione **Open Cluster Management Portal (Ambari)**.  

2. Quando for solicitado, entre nas credenciais de administração do cluster. Especificou estas credenciais durante o processo de configuração do cluster.

### <a name="manage-azure-subscriptions"></a>Gerir subscrições do Azure

Por padrão, o Azure Toolkit para IntelliJ lista os clusters Spark de todas as suas subscrições Azure. Se necessário, pode especificar as subscrições a que pretende aceder.  

1. Do Azure Explorer, clique no nó raiz **do Azure** e, em seguida, **selecione Select Subscriptions**.  

2. A partir da janela **Select Subscriptions,** limpe as caixas de verificação junto às subscrições a que não pretende aceder e, em seguida, selecione **Fechar**.

## <a name="spark-console"></a>Consola de faíscas

Pode executar a Spark Local Console (Scala) ou executar a Spark Livy Interactive Session Console (Scala).

### <a name="spark-local-consolescala"></a>Consola local spark (Scala)

Certifique-se de que satisfez o WINUTILS. Pré-requisito exe.

1. A partir da barra de menus, navegue para **executar** > configurações de**edição...**.

2. A partir da janela **de configurações Run/Debug,** no painel esquerdo, navegue até **Apache Spark no HDInsight** > **[Spark on HDInsight] myApp**.

3. A partir da janela **`Locally Run`** principal, selecione o separador.

4. Forneça os seguintes valores e, em seguida, selecione **OK:**

    |Propriedade |Valor |
    |----|----|
    |Classe principal de trabalho|O valor predefinido é a classe principal do ficheiro selecionado. Pode alterar a classe selecionando a **...** elipse.  e escolher outra classe.|
    |Variáveis de ambiente|Certifique-se de que o valor para HADOOP_HOME está correto.|
    |Localização WINUTILS.exe|Certifique-se de que o caminho está correto.|

    ![Configuração do conjunto de consolalocal](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. Do Project, navegue até ao **myApp** > **src** > **main** > **scala** > **myApp**.  

6. Da barra de menus, navegue até **tools** > **Spark Console** > **Run Spark Local Console (Scala)**.

7. Em seguida, podem ser apresentados dois diálogos para lhe perguntar se pretende corrigir automaticamente dependências. Em caso afirmativo, selecione **Auto Fix**.

    ![IntelliJ IDEA Auto Fix diálogo1](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix1.png)

    ![IntelliJ IDEA Auto Fix diálogo2](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix2.png)

8. A consola deve parecer semelhante à imagem abaixo. No tipo `sc.appName`de janela da consola , e, em seguida, prima ctrl+Enter.  O resultado será mostrado. Pode terminar a consola local clicando num botão vermelho.

    ![Resultado da consola local IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin/local-console-result.png)

### <a name="spark-livy-interactive-session-consolescala"></a>Consola de sessão interativa Spark Livy (Scala)

1. A partir da barra de menus, navegue para **executar** > configurações de**edição...**.

2. A partir da janela **de configurações Run/Debug,** no painel esquerdo, navegue até **Apache Spark no HDInsight** > **[Spark on HDInsight] myApp**.

3. A partir da janela **`Remotely Run in Cluster`** principal, selecione o separador.

4. Forneça os seguintes valores e, em seguida, selecione **OK:**

    |Propriedade |Valor |
    |----|----|
    |Aglomerados de faíscas (apenas Linux)|Selecione o cluster HDInsight Spark no qual pretende executar a sua aplicação.|
    |Nome principal da classe|O valor predefinido é a classe principal do ficheiro selecionado. Pode alterar a classe selecionando a **...** elipse.  e escolher outra classe.|

    ![Configuração interativa do conjunto de consolas](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. Do Project, navegue até ao **myApp** > **src** > **main** > **scala** > **myApp**.  

6. Da barra de menus, navegue até **tools** > **Spark Console** > **Run Spark Livy Interactive Session Console (Scala)**.

7. A consola deve parecer semelhante à imagem abaixo. No tipo `sc.appName`de janela da consola , e, em seguida, prima ctrl+Enter.  O resultado será mostrado. Pode terminar a consola local clicando num botão vermelho.

    ![Resultado da consola interativa IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Enviar Seleção para A Consola de Faíscas

É conveniente prever o resultado do script enviando algum código para a consola local ou para a Consola De Sessão Interativa Livy (Scala). Pode destacar algum código no ficheiro Scala e, em seguida, clique no clique direito **Enviar Seleção para a Consola spark**. O código selecionado será enviado para a consola. O resultado será apresentado após o código na consola. A consola verificará os erros se existir.  

   ![Enviar Seleção para A Consola de Faíscas](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Integrar com o HDInsight Identity Broker (HIB)

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Ligue-se ao seu cluster EsP HDInsight com id broker (HIB)

Pode seguir os passos normais para iniciar sessão na subscrição do Azure para se ligar ao seu cluster EsP HDInsight com o ID Broker (HIB). Após o início do início, verá a lista de clusters no Azure Explorer. Para obter mais instruções, consulte [A Ligação ao seu cluster HDInsight](#connect-to-your-hdinsight-cluster).

### <a name="run-a-spark-scala-application-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Executar uma aplicação Spark Scala em um cluster esp HDInsight com ID Broker (HIB)

Pode seguir os passos normais para submeter o trabalho ao cluster EsP HDInsight com id broker (HIB). Consulte [a aplicação "Executar uma Spark Scala" num cluster HDInsight Spark](#run-a-spark-scala-application-on-an-hdinsight-spark-cluster) para obter mais instruções.

Enviamos os ficheiros necessários para uma pasta com a sua conta de registo e pode ver o caminho de upload no ficheiro de configuração.

   ![caminho de upload na configuração](./media/apache-spark-intellij-tool-plugin/upload-path-in-the-configuration.png)

### <a name="spark-console-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Consola de faísca num cluster EsP HDInsight com id broker (HIB)

Pode executar a Spark Local Console (Scala) ou executar a Spark Livy Interactive Session Console (Scala) num cluster esp HDInsight com id broker (HIB). Consulte a [Consola spark](#spark-console) para obter mais instruções.

   > [!NOTE]  
   > Para o cluster EsP HDInsight com id broker (HIB), [ligar um cluster](#link-a-cluster) e [depurar aplicações Apache Spark remotamente](#debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster) não é suportado remotamente.

## <a name="reader-only-role"></a>Papel só para leitores

Quando os utilizadores submetem o emprego a um cluster com permissão de papel apenas para leitores, as credenciais Ambari são necessárias.

### <a name="link-cluster-from-context-menu"></a>Link cluster do menu de contexto

1. Inscreva-se na conta de papel só para leitores.

2. Do **Azure Explorer,** expanda o **HDInsight** para visualizar os clusters HDInsight que estão na sua subscrição. Os clusters marcados **como "Role:Reader"** só têm permissão de papel apenas para leitores.

    !['IntelliJ Azure Explorer Role:Reader'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer15.png)

3. Clique no cluster com permissão de papel apenas para o leitor. Selecione **Ligar este cluster** do menu de contexto para o cluster de ligação. Introduza o nome de utilizador ambari e a palavra-passe.

    ![IntelliJ Azure Explorer liga este cluster](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer11.png)

4. Se o cluster estiver ligado com sucesso, o HDInsight será refrescado.
   A fase do agrupamento ficará ligada.
  
    ![Diálogo ligado ao IntelliJ Azure Explorer](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Link cluster expandindo o nó jobs

1. Clique no nó **Jobs,** Cluster Job Access A janela **recusa-se.**

2. Clique em **Ligar este cluster** para ligar o cluster.

    ![acesso ao trabalho cluster negado diálogo](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer9.png)

### <a name="link-cluster-from-rundebug-configurations-window"></a>Cluster de ligação a partir da janela de configurações run/debug

1. Crie uma configuração HDInsight. Em seguida, selecione **Remotely Run in Cluster**.

2. Selecione um cluster, que tenha permissão de função apenas para os **clusters Spark (apenas Linux)**. A mensagem de aviso mostra. Pode clicar em **Link este cluster** para ligar o cluster.

   ![Criação de execução/depuração intelliJ IDEA](./media/apache-spark-intellij-tool-plugin/create-configuration.png)

### <a name="view-storage-accounts"></a>Ver Contas de Armazenamento

* Para clusters com permissão de papel apenas para leitores, clique no nó **de Contas de Armazenamento,** **Storage Access A** janela negada aparece. Pode clicar no **Open Azure Storage Explorer** para abrir o Storage Explorer.

   !['IntelliJ IDEA Storage Access Negado'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer14.png)

   ![Acesso de armazenamento intelliJ IDEA Negado](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer10.png)

* Para clusters ligados, clique no nó **de Contas de Armazenamento,** a janela **De armazenamento Acesso Negado** aparece. Pode clicar no **Open Azure Storage** para abrir o Storage Explorer.

   !['IntelliJ IDEA Storage Access Negado2'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer13.png)

   ![Acesso ao armazenamento intelliJ IDEA negado2 botão](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer12.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Converter as aplicações intelliJ IDEA existentes para utilizar o Kit de Ferramentas Azure para o IntelliJ

Pode converter as aplicações Spark Scala existentes que criou no IntelliJ IDEA para ser compatível com o Azure Toolkit para o IntelliJ. Em seguida, pode utilizar o plug-in para submeter as aplicações a um cluster HDInsight Spark.

1. Para uma aplicação Spark Scala existente que foi criada através `.iml` do IntelliJ IDEA, abra o ficheiro associado.

2. Ao nível da raiz, encontra-se um elemento **módulo** como o seguinte texto:

        ```
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
        ```

   Editar o elemento `UniqueKey="HDInsightTool"` para adicionar de modo a que o elemento do **módulo** se pareça com o seguinte texto:

        ```
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
        ```

3. Guarde as alterações. A sua aplicação deve agora ser compatível com o Kit de Ferramentas Azure para o IntelliJ. Pode testá-lo clicando no nome do projeto no Project. O menu pop-up tem agora a opção Submeter a **Aplicação Spark ao HDInsight**.

## <a name="clean-up-resources"></a>Limpar recursos

Se não vai continuar a utilizar esta aplicação, elimine o cluster que criou com os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Na caixa **de pesquisa** na parte superior, digite **HDInsight**.

1. Selecione **clusters HDInsight** em **Serviços**.

1. Na lista de clusters HDInsight que aparecem, selecione o **...** ao lado do cluster que criou para este artigo.

1. Selecione **Eliminar**. Selecione **Sim**.

![Portal Azure elimina cluster HDInsight](./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png "Eliminar o cluster HDInsight")

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar o Kit de Ferramentas Azure para o Plug-in IntelliJ para desenvolver aplicações Apache Spark escritas em [Scala](https://www.scala-lang.org/). Em seguida, submeteu-os a um cluster HDInsight Spark diretamente do ambiente integrado de desenvolvimento intelliJ (IDE). Avance para o próximo artigo para ver como os dados registados em Apache Spark podem ser puxados para uma ferramenta de análise bi como Power BI.

> [!div class="nextstepaction"]
> [Analise os dados da Apache Spark usando o Power BI](apache-spark-use-bi-tools.md)
