---
title: 'Azure Toolkit para IntelliJ: Criar Spark aplicações para um cluster do HDInsight '
description: Utilizar o Azure Toolkit para IntelliJ para desenvolver aplicações do Spark escritas no Scala e enviá-los para um cluster do Spark do HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: maxluk
ms.openlocfilehash: 8fa3cd79011ab31349ec44edf52b8fd9048d0d37
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58077977"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Utilizar o Azure Toolkit para IntelliJ para criar aplicativos de Apache Spark para um cluster do HDInsight

Utilizar o Azure Toolkit para IntelliJ Plug-in para desenvolver [Apache Spark](https://spark.apache.org/) aplicativos escritos em [Scala](https://www.scala-lang.org/)e, em seguida, enviá-los para um cluster do Spark do HDInsight diretamente a partir de desenvolvimento integrado do IntelliJ (IDE ambiente). Pode utilizar o plug-in de diversas formas:

* Programe e envie um aplicação Scala Spark num cluster do HDInsight Spark.
* Aceder aos seus recursos de cluster do Azure HDInsight Spark.
* Desenvolva e execute uma aplicação Scala Spark localmente.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* [Kit de desenvolvimento Java de Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  Este tutorial utiliza o Java versão 8.0.202.
* IntelliJ IDEIA. Este artigo utiliza [ver. IntelliJ IDEIA Comunidade  2018.3.4](https://www.jetbrains.com/idea/download/).
* Azure Toolkit for IntelliJ.  Ver [instalar o Azure Toolkit para IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).
* WINUTILS. EXE.  Ver [problemas para executar o Hadoop no Windows](https://wiki.apache.org/hadoop/WindowsProblems).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalar o plug-in do Scala para o IntelliJ IDEA
Execute os seguintes passos para instalar o plug-in de Scala:

1. Abra o IntelliJ IDEA.

2. No ecrã de boas-vindos, navegue até **configurar** > **plug-ins** para abrir o **plug-ins** janela.
   
    ![Ativar o plug-in do Scala](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin.png)

3. Selecione **instalar** para o plug-in de Scala que estiver em destaque na nova janela.  

    ![Instalar o plug-in do Scala](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. Após a instalação bem-sucedida do plug-in, tem de reiniciar o IDE.


## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Criar uma aplicação Spark Scala para um cluster do HDInsight Spark

1. Inicie o IntelliJ IDEA e selecione **criar novo projeto** para abrir o **novo projeto** janela.

2. Selecione **do Azure Spark/HDInsight** no painel à esquerda.

3. Selecione **Spark projeto (Scala)** da janela principal.

4. Partir do **ferramenta de compilação** na lista pendente, selecione uma das seguintes ações:
   * **Maven** para suporte ao Assistente de criação do projeto Scala.
   * **SBT** para as dependências de gestão e a criação do projeto Scala.

     ![Caixa de diálogo O Novo Projeto](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Selecione **Seguinte**.

6. Na **novo projeto** janela, forneça as seguintes informações:  

    |  Propriedade   | Descrição   |  
    | ----- | ----- |  
    |Nome do projeto| Introduza um nome.  Este tutorial utiliza `myApp`.|  
    |Projeto&nbsp;localização| Introduza a localização pretendida para guardar o seu projeto.|
    |SDK do projeto| Isso pode ser em branco na primeira utilização IDÉIA.  Selecione **novo...**  e navegue para o JDK.|
    |Versão do Spark|O Assistente de criação integra-se a versão apropriada para o SDK do Spark e do Scala. Se a versão do cluster do Spark for anterior à 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark2.x**. Este exemplo utiliza **Spark 2.3.0 (Scala 2.11.8)**.|

    ![Selecionar o SDK do Spark](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

7. Selecione **Concluir**.  Pode demorar alguns minutos antes do projeto torna-se disponível.

8. O projeto de Spark cria automaticamente um artefacto por si. Para ver o artefacto, faça o seguinte:

   a. A partir da barra de menus, navegue para **arquivo** > **estrutura do projeto...** .

   b. Partir do **estrutura do projeto** janela, selecione **artefactos**.  

   c. Selecione **Cancelar** depois de visualizar o artefacto.

      ![Informações de artefacto na caixa de diálogo](./media/apache-spark-intellij-tool-plugin/default-artifact.png)

9. Adicione o código de origem da aplicação, fazendo o seguinte:

    a. A partir do projeto, navegue para **myApp** > **src** > **principal** > **scala**.  

    b. Com o botão direito **scala**e, em seguida, navegue até **New** > **Scala classe**.

   ![Comandos para a criação de uma classe Scala do projeto](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. Na **criar uma nova classe de Scala** caixa de diálogo caixa, forneça um nome, selecione **objeto** no **tipo** na lista pendente e, em seguida, selecione **OK**.

     ![Criar caixa de diálogo nova classe Scala](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. O **myApp.scala** , em seguida, abre-se na exibição principal do ficheiro. Substitua o código de padrão com o código encontrado abaixo:  

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

    O código lê os dados de Hvac (disponível em todos os clusters do HDInsight Spark), devolve as linhas que têm apenas um dígito na coluna sétima no ficheiro CSV e grava a saída para `/HVACOut` no contentor de armazenamento padrão para o cluster.

## <a name="connect-to-your-hdinsight-cluster"></a>Ligar ao cluster do HDInsight
O utilizador pode optar por [inicie sessão na subscrição do Azure](#sign-in-to-your-azure-subscription), ou [ligar um cluster do HDInsight](#link-a-cluster) com o Ambari nome de utilizador/palavra-passe ou domínio associado credenciais para ligar ao cluster do HDInsight.

### <a name="sign-in-to-your-azure-subscription"></a>Inicie sessão na sua subscrição do Azure

1. A partir da barra de menus, navegue para **View** > **ferramenta Windows** > **Azure Explorer**.
       
   ![A ligação Explorador do Azure](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. No Explorador do Azure, com o botão direito a **Azure** nó e, em seguida, selecione **sessão**.

3. Na **do Azure de sessão** caixa de diálogo, selecione **iniciar sessão**e, em seguida, introduza as credenciais do Azure.

    ![A caixa de diálogo início de sessão no Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. Depois de iniciar sessão, o **selecionar subscrições** caixa de diálogo apresenta uma lista de todas as subscrições do Azure que estão associadas com as credenciais. Selecione a sua subscrição e, em seguida, selecione o **selecione** botão.

    ![A caixa de diálogo Selecionar subscrições](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

5. Partir **Azure Explorer**, expanda **HDInsight** para ver os clusters do Spark do HDInsight que estão na sua subscrição.

    ![Clusters do Spark do HDInsight no Explorador do Azure](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

6. Para ver os recursos (por exemplo, contas de armazenamento) que estão associados com o cluster, pode expandir ainda mais um nó de nome do cluster.

    ![Um nó expandido do nome do cluster](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

### <a name="link-a-cluster"></a>Ligar um cluster
Pode ligar um cluster do HDInsight ao utilizar o nome de utilizador do Apache ambari destina geridos. Da mesma forma, para um cluster de HDInsight associado a um domínio, pode ligar com o domínio e o nome de utilizador, como user1@contoso.com. Também pode ligar o cluster de serviço do Livy.

1. A partir da barra de menus, navegue para **View** > **ferramenta Windows** > **Azure Explorer**.

2. No Explorador do Azure, com o botão direito a **HDInsight** nó e, em seguida, selecione **ligação de um Cluster**.

   ![menu de contexto do cluster de ligação](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

3. As opções disponíveis no **Cluster de um Link** janela irá variar dependendo de qual o valor é selecionar o **tipo de recurso de ligação** na lista pendente.  Introduza os seus valores e, em seguida, selecione **OK**.

    * **Cluster do HDInsight**  
  
        |Propriedade |Value |
        |----|----|
        |Tipo de recurso de ligação|Selecione **Cluster de HDInsight** na lista pendente.|
        |URL do nome do cluster| Introduza o nome do cluster.|
        |Tipo de Autenticação| Deixe como **autenticação básica**|
        |Nome de Utilizador| Introduza o nome de utilizador do cluster, a predefinição é o administrador.|
        |Palavra-passe| Introduza a palavra-passe para o nome de utilizador.|
    
        ![caixa de diálogo de cluster de hdinsight de ligação](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Serviço do Livy**  
  
        |Propriedade |Value |
        |----|----|
        |Tipo de recurso de ligação|Selecione **Livy serviço** na lista pendente.|
        |Livy Endpoint| Enter Livy Endpoint|
        |Nome do Cluster| Introduza o nome do cluster.|
        |Yarn Endpoint|Opcional.|
        |Tipo de Autenticação| Deixe como **autenticação básica**|
        |Nome de Utilizador| Introduza o nome de utilizador do cluster, a predefinição é o administrador.|
        |Palavra-passe| Introduza a palavra-passe para o nome de utilizador.|

        ![ligar a caixa de diálogo do livy cluster](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

4. Pode ver o seu cluster ligado a partir da **HDInsight** nó.

   ![cluster ligado](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

5. Também pode desassociar um cluster a partir **Explorador do Azure**.

   ![Desassociar de cluster](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Executar uma aplicação Spark Scala num cluster do HDInsight Spark
Depois de criar uma aplicação Scala, pode enviá-lo para o cluster.

1. A partir do projeto, navegue para **myApp** > **src** > **principal** > **scala**  >  **myApp**.  Com o botão direito **myApp**e selecione **submeter a aplicação de Spark** (provavelmente será localizado na parte inferior da lista).
    
      ![A aplicação de Spark submeter ao comando do HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. Na **submeter a aplicação de Spark** janela de caixa de diálogo, selecione **1. O Spark no HDInsight**.

3. Na **Upravit konfiguraci** janela, forneça os valores seguintes e, em seguida, selecione **OK**:

    |Propriedade |Value |
    |----|----|
    |Clusters do Spark (apenas Linux)|Selecione o cluster do Spark do HDInsight no qual pretende executar a sua aplicação.|
    |Selecione um Artefato para submeter|Deixe a predefinição.|
    |Nome da classe principal|O valor predefinido é a classe principal a partir do ficheiro selecionado. Pode alterar a classe ao selecionar as reticências (**...** ) e escolhendo a outra classe.|
    |Configurações da tarefa|Pode alterar as chaves predefinidas e/ou valores. Para obter mais informações, consulte [API de REST do Livy Apache](https://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Argumentos de linha de comandos|Pode inserir argumentos separados por espaço para a classe principal, se necessário.|
    |Jars referenciados e arquivos referenciados|Pode introduzir os caminhos para os ficheiros e Jars referenciadas se houver. Para obter mais informações: [Configuração do Apache Spark](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Consulte também [como carregar recursos em cluster](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer).|
    |Armazenamento de carregamento de tarefas|Expanda para revelar as opções adicionais.|
    |Tipo de armazenamento|Selecione **Use Azure Blob a carregar** na lista pendente.|
    |Conta de Armazenamento|Introduza a sua conta de armazenamento.|
    |Chave de Armazenamento|Introduza a chave de armazenamento.|
    |Contentor de Armazenamento|Selecione o contentor de armazenamento na lista pendente, uma vez **conta de armazenamento** e **chave de armazenamento** foi introduzido.|

    ![A caixa de diálogo de envio do Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

4. Selecione **SparkJobRun** para submeter o seu projeto para o cluster selecionado. O **remoto tarefa do Spark no Cluster** separador apresenta o progresso de execução de tarefa na parte inferior. Pode parar a aplicação ao clicar no botão vermelho. Para saber como acessar o resultado da tarefa, consulte o "acesso e gerir clusters do HDInsight Spark com o Azure Toolkit para IntelliJ" seção mais adiante neste artigo.  
      
    ![A janela de envio do Spark](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Depurar aplicações do Apache Spark localmente ou remotamente num cluster do HDInsight 
Recomendamos também que outra forma de submeter a aplicação Spark no cluster. Pode fazê-lo ao definir os parâmetros nos **configurações de execução/depuração** IDE. Para obter mais informações, consulte [depurar aplicações do Apache Spark localmente ou remotamente num cluster do HDInsight com o Azure Toolkit para IntelliJ através de SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Aceder e gerir clusters do HDInsight Spark com o Azure Toolkit para IntelliJ
Pode executar várias operações, utilizando o Azure Toolkit para IntelliJ.  A maioria das operações sejam iniciada a partir **Explorador do Azure**.  A partir da barra de menus, navegue para **View** > **ferramenta Windows** > **Azure Explorer**.

### <a name="access-the-job-view"></a>Aceda à vista de tarefa

1. A partir do Explorador do Azure, navegue para **HDInsight** > \<Your Cluster >> **tarefas**.

    ![Nó de vista de tarefa](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

2. No painel da direita, o **vista de tarefas do Spark** separador apresenta todos os aplicativos que foram executados no cluster. Selecione o nome da aplicação para o qual pretende ver mais detalhes.

    ![Detalhes da aplicação](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)

3. Para apresentar informações básicas de tarefa em execução, Paire o rato sobre o gráfico da tarefa. Para ver o gráfico de fases e as informações que gera todas as tarefas, selecione um nó no gráfico da tarefa.

    ![Detalhes da fase de tarefa](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Para ver frequentemente utilizados registos, como *Driver Stderr*, *Stdout do controlador*, e *informações de diretório*, selecione o **Log** separador.

    ![Detalhes do registo](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

5. Também pode ver o histórico de Spark da interface do Usuário e a IU do YARN (no nível do aplicativo) ao selecionar um link na parte superior da janela.

### <a name="access-the-spark-history-server"></a>Aceder ao servidor de histórico do Spark

1. No Azure Explorer, expanda **HDInsight**, o nome do cluster do Spark com o botão direito e, em seguida, selecione **IU do histórico do Spark Open**.  
2. Quando lhe for pedido, introduza as credenciais de administrador do cluster, o que especificou quando configurou o cluster.

3. No dashboard de servidor do histórico do Spark, pode utilizar o nome da aplicação para procurar a aplicação que já concluiu apenas em execução. No código anterior, define o nome do aplicativo usando `val conf = new SparkConf().setAppName("myApp")`. Por conseguinte, é o nome da sua aplicação de Spark **myApp**.

### <a name="start-the-ambari-portal"></a>Iniciar o portal do Ambari

1. No Azure Explorer, expanda **HDInsight**, o nome do cluster do Spark com o botão direito e, em seguida, selecione **aberto Portal(Ambari) de gerenciamento de Cluster**.  

2. Quando lhe for pedido, introduza as credenciais de administrador para o cluster. Estas credenciais que especificou durante o processo de configuração de cluster.

### <a name="manage-azure-subscriptions"></a>Gerir subscrições do Azure
Por predefinição, o Azure Toolkit para IntelliJ lista os clusters do Spark de todas as suas subscrições do Azure. Se necessário, pode especificar as subscrições que pretende aceder.  

1. No Explorador do Azure, com o botão direito a **Azure** nó raiz e, em seguida, selecione **selecionar subscrições**.  

2. Partir do **selecionar subscrições** janela, desmarque a verificação de caixas junto as subscrições que não deseja acessar e, em seguida, selecione **fechar**.

## <a name="spark-console"></a>Consola do Spark
Pode executar Console(Scala) Local do Spark ou executar Console(Scala) de sessão interativa do Spark Livy.

### <a name="spark-local-consolescala"></a>Spark Local Console(Scala)
Certifique-se de que cumpriu o WINUTILS. Pré-requisitos do EXE.

1. A partir da barra de menus, navegue para **execute** > **configurações editar...** .

2. Do **configurações de execução/depuração** janela, no painel esquerdo, navegue até à **Apache Spark no HDInsight** > **[Spark no HDInsight] myApp**.

3. Na janela principal, selecione o **executar localmente** separador.

4. Forneça os valores seguintes e, em seguida, selecione **OK**:

    |Propriedade |Value |
    |----|----|
    |Classe de tarefa principal|O valor predefinido é a classe principal a partir do ficheiro selecionado. Pode alterar a classe ao selecionar as reticências (**...** ) e escolhendo a outra classe.|
    |Variáveis de ambiente|Certifique-se de que o valor para HADOOP_HOME está correto.|
    |Localização de WINUTILS.exe|Certifique-se de que o caminho está correto.|

    ![Configuração do conjunto de consola local](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. A partir do projeto, navegue para **myApp** > **src** > **principal** > **scala**  >  **myApp**.  

6. A partir da barra de menus, navegue para **ferramentas** > **Spark consola** > **Console(Scala) Local de Spark executar**.

7. Em seguida, duas caixas de diálogo poderão ser apresentadas perguntar se pretende automaticamente corrige as dependências. Nesse caso, selecione **corrigir automaticamente**.

    ![Spark Auto Fix1](./media/apache-spark-intellij-tool-plugin/console-auto-fix1.png)

    ![Spark Auto Fix2](./media/apache-spark-intellij-tool-plugin/console-auto-fix2.png)

8. A consola deve ser semelhante à imagem abaixo. O tipo de janela de consola `sc.appName`e, em seguida, prima ctrl + Enter.  O resultado será mostrado. Pode terminar a consola local ao clicar no botão vermelho.

    ![Resultado da consola local](./media/apache-spark-intellij-tool-plugin/local-console-result.png)


### <a name="spark-livy-interactive-session-consolescala"></a>Console(Scala) do Livy sessão interativa do spark
Só é suportado no IntelliJ 2018.2 e 2018.3.

1. A partir da barra de menus, navegue para **execute** > **configurações editar...** .

2. Do **configurações de execução/depuração** janela, no painel esquerdo, navegue até à **Apache Spark no HDInsight** > **[Spark no HDInsight] myApp**.

3. Na janela principal, selecione o **executados remotamente num Cluster** separador.

4. Forneça os valores seguintes e, em seguida, selecione **OK**:

    |Propriedade |Value |
    |----|----|
    |Clusters do Spark (apenas Linux)|Selecione o cluster do Spark do HDInsight no qual pretende executar a sua aplicação.|
    |Nome da classe principal|O valor predefinido é a classe principal a partir do ficheiro selecionado. Pode alterar a classe ao selecionar as reticências (**...** ) e escolhendo a outra classe.|

    ![Configuração do conjunto de consola interativa](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. A partir do projeto, navegue para **myApp** > **src** > **principal** > **scala**  >  **myApp**.  

6. A partir da barra de menus, navegue para **ferramentas** > **Spark consola** > **executar os Livy de Spark em modo interativo da Console(Scala) de sessão**.

7. A consola deve ser semelhante à imagem abaixo. O tipo de janela de consola `sc.appName`e, em seguida, prima ctrl + Enter.  O resultado será mostrado. Pode terminar a consola local ao clicar no botão vermelho.

    ![Resultado da consola interativa](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Enviar a seleção para a consola do Spark

É conveniente para previr o resultado do script através do envio de algum código para a consola local ou Livy Console(Scala) de sessão interativo. Pode realçar algum código no ficheiro Scala, em seguida, clique com botão direito **enviar consola de seleção para Spark**. O código selecionado será enviado para a consola e ser realizado. O resultado será apresentado depois do código na consola do. A consola irá verificar os erros, se existente.  

   ![Enviar a seleção para a consola do Spark](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Converter aplicações existentes do IntelliJ IDEA para utilizar o Azure Toolkit para IntelliJ

Pode converter o Scala Spark existentes que criou no IntelliJ IDEA para ser compatível com o Azure Toolkit para IntelliJ de aplicativos. Pode, em seguida, utilizar o plug-in para submeter os aplicativos a um cluster do Spark do HDInsight.

1. Para uma aplicação Spark Scala existente que foi criada por meio do IntelliJ IDEA, abra o ficheiro de .iml associados.

1. Na raiz nível é uma **módulo** elemento semelhante ao seguinte:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Edite o elemento para adicionar `UniqueKey="HDInsightTool"` , para que o **módulo** elemento é semelhante ao seguinte:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

1. Guarde as alterações. A aplicação deverá estar agora compatível com o Azure Toolkit para IntelliJ. Pode testá-lo clicando com o nome do projeto no projeto. O menu de pop-up tem agora a opção **submeter a aplicação Spark para HDInsight**.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="error-in-local-run-use-a-larger-heap-size"></a>Erro na execução local: *Utilizar um maior tamanho de heap*
No Spark 1.6, se estiver a utilizar um SDK de Java de 32 bits durante a execução local, pode encontrar os seguintes erros:

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
        at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
        at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
        at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
        at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
        at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
        at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
        at LogQuery$.main(LogQuery.scala:53)
        at LogQuery.main(LogQuery.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

Estes erros acontecem porque o tamanho de heap não é suficientemente grande para o Spark executar. Spark requer, pelo menos, 471 MB. (Para obter mais informações, consulte [SPARK 12081](https://issues.apache.org/jira/browse/SPARK-12081).) Uma solução simple é usar um SDK de Java de 64 bits. Também pode alterar as definições de JVM no IntelliJ, adicionando as seguintes opções:

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Adicionar opções para a caixa "Opções de VM" no IntelliJ](./media/apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>FAQ
Se o cluster estiver ocupado, poderá receber o erro abaixo.

![Erro de obtenção de Intellij quando ocupado do cluster](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-upload.png)

![Erro de obtenção de Intellij quando ocupado do cluster](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-submit.png)

## <a name="feedback-and-known-issues"></a>Comentários e problemas conhecidos
Atualmente, a visualização de saídas de Spark diretamente não é suportada.

Se tiver comentários ou sugestões, ou se tiver quaisquer problemas ao utilizar este plug-in, envie um e-mail para hdivstool@microsoft.com.

## <a name="seealso"></a>Passos seguintes
* [Descrição geral: Apache Spark no HDInsight do Azure](apache-spark-overview.md)

### <a name="demo"></a>Demonstração
* Crie projeto de Scala (vídeo): [Criar aplicações do Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depuração remota (vídeo): [Utilizar o Azure Toolkit para IntelliJ para depurar aplicações de Apache Spark remotamente num Cluster do HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: Efetuar análise de dados interativa com o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura do edifício com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de registos de Web site com o Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Apache Spark com o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilizar o Azure Toolkit para IntelliJ para depurar aplicações de Apache Spark remotamente através de VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar o Azure Toolkit para IntelliJ para depurar aplicações de Apache Spark remotamente através de SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utilizar ferramentas do HDInsight para IntelliJ com a Sandbox da Hortonworks](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utilizar ferramentas do HDInsight no Azure Toolkit para Eclipse para criar aplicações do Apache Spark](apache-spark-eclipse-tool-plugin.md)
* [Utilizar blocos de notas do Zeppelin do Apache com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
