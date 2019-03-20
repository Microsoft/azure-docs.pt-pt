---
title: 'Azure Toolkit para Eclipse: Criar aplicações de Scala para o HDInsight Spark '
description: Utilize ferramentas do HDInsight no Azure Toolkit para Eclipse para desenvolver aplicações do Spark escritas no Scala e enviá-los para um cluster do HDInsight Spark, diretamente a partir do IDE do Eclipse.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/30/2017
ms.author: hrasheed
ms.openlocfilehash: 05df154db27dcf1b07506c6bf9c2a681408de61e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091822"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Utilizar o Azure Toolkit para Eclipse para criar aplicativos de Apache Spark para um cluster do HDInsight

Utilizar ferramentas do HDInsight no Azure Toolkit para [Eclipse](https://www.eclipse.org/) desenvolver [Apache Spark](https://spark.apache.org/) aplicações escritas em [Scala](https://www.scala-lang.org/) e enviá-los a um cluster do Azure HDInsight Spark, diretamente a partir do IDE do Eclipse. Pode usar as ferramentas do HDInsight Plug-in de algumas formas diferentes:

* Para desenvolver e envie um aplicação Scala Spark num cluster do HDInsight Spark.
* Para aceder aos recursos de cluster do Azure HDInsight Spark.
* Para desenvolver e executar uma aplicação Scala Spark localmente.

> [!IMPORTANT]  
> Pode utilizar esta ferramenta para criar e submeter aplicações apenas para um cluster do Spark do HDInsight no Linux.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos

* Cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Oracle Java Development Kit versão 8, que é utilizado para o tempo de execução do IDE do Eclipse. Pode baixá-lo do [Web site do Oracle](https://aka.ms/azure-jdks).
* Eclipse IDE. Este artigo utiliza o Eclipse Neon. Pode instalá-lo do [Web site do Eclipse](https://www.eclipse.org/downloads/).



## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>Instalar as ferramentas de HDInsight no Azure Toolkit para Eclipse e o Scala Plug-in

### <a name="install-azure-toolkit-for-eclipse"></a>Instalar o Azure Toolkit para Eclipse
Ferramentas do HDInsight para o Eclipse está disponível como parte do Azure Toolkit para Eclipse. Para obter instruções de instalação, consulte [instalar o Azure Toolkit para Eclipse](https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-installation).

### <a name="install-the-scala-plug-in"></a>Instalar o plug-in de Scala
Quando abre o Eclipse, o HDInsight ferramenta Deteta automaticamente se tiver instalado o Scala Plug-in. Selecione **OK** para continuar e, em seguida, siga as instruções para instalar o plug-in do Eclipse Marketplace.

![Instalação automática de Scala Plug-in](./media/apache-spark-eclipse-tool-plugin/auto-install-scala.png)

O utilizador pode optar por [inicie sessão na subscrição do Azure](#Sign-in-to-your-Azure-subscription), ou [ligar um cluster do HDInsight](#Link-a-cluster) com o Ambari nome de utilizador/palavra-passe ou domínio associado a um credential para iniciar. 

## <a name="sign-in-to-your-azure-subscription"></a>Inicie sessão na sua subscrição do Azure
1. Inicie o Eclipse IDE e abra o Explorador do Azure. Sobre o **janela** menu, selecione **Show View**e, em seguida, selecione **outros**. Na caixa de diálogo que se abre, expanda **Azure**, selecione **Explorador do Azure**e, em seguida, selecione **OK**.

   ![Caixa de diálogo Mostrar vista](./media/apache-spark-eclipse-tool-plugin/view-explorer-1.png)
1. Com o botão direito a **Azure** nó e, em seguida, selecione **iniciar sessão**.
1. Na **do Azure de sessão** caixa de diálogo caixa, escolha o método de autenticação, selecione **iniciar sessão**e introduza as credenciais do Azure.
   
   ![Azure caixa de início de sessão na caixa de diálogo](./media/apache-spark-eclipse-tool-plugin/view-explorer-2.png)
1. Depois de iniciar sessão, o **selecionar subscrições** caixa de diálogo apresenta uma lista de todas as subscrições Azure associadas com as credenciais. Clique em **selecione** para fechar a caixa de diálogo.

   ![Selecione a caixa de diálogo de subscrições](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
1. Sobre o **Azure Explorer** separador, expanda **HDInsight** para ver a clusters do Spark do HDInsight com a sua subscrição.
   
   ![Clusters do Spark do HDInsight no Explorador do Azure](./media/apache-spark-eclipse-tool-plugin/view-explorer-3.png)
1. Pode expandir ainda mais um nó de nome de cluster para ver os recursos (por exemplo, contas de armazenamento) associados ao cluster.
   
   ![Expandir um nome de cluster para ver os recursos](./media/apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>Ligar um cluster
Pode ligar um cluster normal usando o nome de utilizador do Ambari gerido. Da mesma forma, para um cluster de HDInsight associado a um domínio, pode ligar com o domínio e o nome de utilizador, como user1@contoso.com.

1. Selecione **ligar um cluster** partir **Explorador do Azure**.

   ![menu de contexto do cluster de ligação](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. Introduza **nome do Cluster**, **nome de utilizador** e **palavra-passe**, em seguida, clique no botão OK para ligar o cluster. Opcionalmente, introduza a conta de armazenamento, a chave de armazenamento e, em seguida, selecione o contentor de armazenamento para o Explorador de armazenamento trabalhar na vista de árvore à esquerda
   
   ![caixa de diálogo de cluster de ligação](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog.png)
   
   > [!NOTE]  
   > Usamos a chave de armazenamento ligada, o nome de utilizador e a palavra-passe se o cluster tanto registado na subscrição do Azure e ligadas um cluster.
   > ![Explorador de armazenamento no Eclipse](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)

1. Pode ver um cluster de ligado no **HDInsight** nó depois de clicar no botão OK, se as informações de entrada são adequadas. Agora pode enviar uma aplicação a este cluster ligado.

   ![cluster ligado](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

1. Também pode desassociar um cluster a partir **Explorador do Azure**.
   
   ![Desassociar de cluster](./media/apache-spark-intellij-tool-plugin/unlink.png)


## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Configurar um projeto de Spark Scala para um cluster do Spark do HDInsight

1. Na área de trabalho Eclipse IDE, selecione **arquivo**, selecione **New**e, em seguida, selecione **projeto**. 
1. No Assistente de novo projeto, expanda **HDInsight**, selecione **Spark no HDInsight (Scala)** e, em seguida, selecione **seguinte**.

   ![Selecionar o Spark no HDInsight (Scala) projeto](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
1. O Assistente de criação de projeto Scala Deteta automaticamente se tiver instalado o Scala Plug-in. Selecione **OK** para continuar a baixar o Scala Plug-in e, em seguida, siga as instruções para reiniciar o Eclipse.

   ![Verificação de scala](./media/apache-spark-eclipse-tool-plugin/auto-install-scala-2.png)
1. Na **novo projeto do HDInsight Scala** caixa de diálogo, forneça os valores seguintes e, em seguida, selecione **próxima**:
   * Introduza um nome para o projeto.
   * Na **JRE** área, certifique-se de que **utilizar um ambiente de execução JRE** está definida como **JavaSE 1.7** ou posterior.
   * Na **biblioteca de Spark** área, pode escolher **utilize Maven para configurar o SDK do Spark** opção.  Nossa ferramenta integra-se a versão apropriada para o SDK do Spark e do Scala. Também pode escolher **adicionar manualmente o SDK do Spark** opção, transferir e adicionar o SDK de Spark por manualmente.

   ![Caixa de diálogo novo projeto do HDInsight Scala](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
1. Na caixa de diálogo seguinte, selecione **concluir**. 
   
  
## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Criar uma aplicação Scala para um cluster do HDInsight Spark

1. No Eclipse IDE, do Explorador de pacotes, expanda o projeto que criou anteriormente, com o botão direito **src**, aponte para **New**e, em seguida, selecione **outros**.
1. Na **selecionar um assistente** caixa de diálogo caixa, expanda **Scala assistentes**, selecione **objeto Scala**e, em seguida, selecione **seguinte**.
   
   ![Selecione uma caixa de diálogo do Assistente](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
1. Na **criar novo arquivo** caixa de diálogo, introduza um nome para o objeto e, em seguida, selecione **concluir**.
   
   ![Criar caixa de diálogo novo ficheiro](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
1. Cole o seguinte código no editor de texto:
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        object MyClusterApp{
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows that have only one digit in the seventh column in the CSV
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACOut")
          }        
        }
1. Execute a aplicação num cluster do Spark do HDInsight:
   
   a. A partir do Explorador de pacotes, com o botão direito no nome do projeto e, em seguida, selecione **submeter a aplicação Spark para HDInsight**.        
   b. Na **Spark submissão** caixa de diálogo, forneça os valores seguintes e, em seguida, selecione **submeter**:
      
   * Para **nome do Cluster**, selecione o cluster do Spark do HDInsight no qual pretende executar a sua aplicação.
   * Selecione um artefato do projeto Eclipse ou selecione um na unidade de disco rígido. O valor predefinido depende do item que clique com botão direito do Explorador de pacotes.
   * Na **nome da classe principal** na lista pendente, o Assistente de submissão apresenta todos os nomes de objeto do seu projeto. Selecione ou introduza uma que pretende executar. Se tiver selecionado um artefato de uma unidade de disco rígida, tem de introduzir manualmente o nome da classe principal. 
   * Uma vez que o código da aplicação neste exemplo não necessita de argumentos de linha de comandos ou referenciar JARs ou ficheiros, pode deixar as restantes caixas de texto vazio.
        
     ![Caixa de diálogo de envio do Spark](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
1. O **Spark submissão** separador deve começar a exibir o progresso. Pode parar a aplicação ao selecionar o botão vermelho no **Spark submissão** janela. Também pode ver os registos para esta aplicação específico, executar, selecionando o ícone de globo (representado por caixa azul na imagem).
      
   ![Janela de envio do Spark](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)


## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Aceder e gerir clusters do Spark do HDInsight com ferramentas do HDInsight no Azure Toolkit para Eclipse
Pode executar várias operações, utilizando ferramentas do HDInsight, inclusive, acesso o resultado da tarefa.

### <a name="access-the-job-view"></a>Aceda à vista de tarefa
1. No Explorador do Azure, expanda **HDInsight**, expanda o nome de cluster do Spark e, em seguida, selecione **tarefas**. 

   ![Nó de vista de tarefa](./media/apache-spark-eclipse-tool-plugin/job-view-node.png)

1. Selecione o **tarefas** nó. Se a versão do Java é anterior à **1.8**, ferramentas do HDInsight automaticamente lembrete instalar o **clipse E (fx)** Plug-in. Selecione **OK** para continuar e, em seguida, siga o Assistente para instalá-lo a partir do mercado do Eclipse e reiniciar o Eclipse. 

   ![Instalar clipse E (fx)](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

1. Abrir a vista de tarefas a partir da **tarefas** nó. No painel da direita, o **vista de tarefas do Spark** separador apresenta todos os aplicativos que foram executados no cluster. Selecione o nome da aplicação para o qual pretende ver mais detalhes.

   ![Detalhes da aplicação](./media/apache-spark-eclipse-tool-plugin/view-job-logs.png)

   Em seguida, pode pegar qualquer uma destas ações:

   * Paire o rato sobre o gráfico da tarefa. Apresenta informações básicas sobre a tarefa em execução. Selecione o gráfico da tarefa, e pode ver as fases e informações de que todas as tarefas gera.

     ![Detalhes da fase de tarefa](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * Selecione o **Log** separador para ver com frequência utilizada registos, incluindo **Stderr de Driver**, **Driver Stdout**, e **informações de diretório**.

     ![Detalhes do registo](./media/apache-spark-eclipse-tool-plugin/Job-log-info.png)

   * Abra o histórico de Spark da interface do Usuário e a interface do Usuário do Apache Hadoop YARN (no nível do aplicativo), selecionando os hiperlinks na parte superior da janela.

### <a name="access-the-storage-container-for-the-cluster"></a>Aceder ao contentor de armazenamento para o cluster
1. No Explorador do Azure, expanda o **HDInsight** nó de raiz para ver uma lista dos clusters do Spark do HDInsight que estão disponíveis.
1. Expanda o nome do cluster para ver a conta de armazenamento e o contentor de armazenamento predefinido para o cluster.
   
   ![Contentor de armazenamento de conta e a predefinição de armazenamento](./media/apache-spark-eclipse-tool-plugin/view-explorer-5.png)
1. Selecione o nome do contentor de armazenamento associado ao cluster. No painel da direita, faça duplo clique o **HVACOut** pasta. Abra um da **parte -** ficheiros para ver a saída do aplicativo.

### <a name="access-the-spark-history-server"></a>Aceder ao servidor de histórico do Spark
1. No Explorador do Azure, o nome do cluster do Spark com o botão direito e, em seguida, selecione **IU do histórico do Spark Open**. Quando lhe for pedido, introduza as credenciais de administrador para o cluster. Especificar os mesmos durante o aprovisionamento do cluster.
1. No dashboard de servidor do histórico do Spark, utilize o nome da aplicação para procurar a aplicação que já concluiu apenas em execução. No código anterior, define o nome do aplicativo usando `val conf = new SparkConf().setAppName("MyClusterApp")`. Então, o nome da sua aplicação Spark foi **MyClusterApp**.

### <a name="start-the-apache-ambari-portal"></a>Iniciar o portal do Apache Ambari
1. No Explorador do Azure, o nome do cluster do Spark com o botão direito e, em seguida, selecione **abrir Portal de gestão de Cluster (Ambari)**. 
1. Quando lhe for pedido, introduza as credenciais de administrador para o cluster. Especificar os mesmos durante o aprovisionamento do cluster.

### <a name="manage-azure-subscriptions"></a>Gerir subscrições do Azure
Por predefinição, a ferramenta de HDInsight no Azure Toolkit para Eclipse lista os clusters do Spark de todas as suas subscrições do Azure. Se necessário, pode especificar as subscrições para o qual pretende aceder ao cluster. 

1. No Explorador do Azure, com o botão direito a **Azure** nó raiz e, em seguida, selecione **gerir subscrições**. 
1. Na caixa de diálogo, desmarque as caixas de verificação para a subscrição que não pretende aceder e, em seguida, selecione **fechar**. Também pode selecionar **terminar sessão** se de que pretende terminar a sua subscrição do Azure.

## <a name="run-a-spark-scala-application-locally"></a>Execute uma aplicação Spark Scala localmente
Pode utilizar ferramentas do HDInsight no Azure Toolkit para Eclipse para executar aplicações de Spark Scala localmente na sua estação de trabalho. Normalmente, esses aplicativos não precisam de acesso a recursos de cluster como um contentor de armazenamento e pode executar e testá-los localmente.

### <a name="prerequisite"></a>Pré-requisito
Enquanto estiver a executar a aplicação Spark Scala local num computador Windows, poderá receber uma exceção, conforme explicado na [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356). Essa exceção ocorre porque **WinUtils.exe** está em falta no Windows. 

Para resolver este erro, precisa [transferir executável](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) a uma localização como **C:\WinUtils\bin**e, em seguida, adicione a variável de ambiente **HADOOP_HOME** e defina o valor de a variável para **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Executar uma aplicação local do Spark Scala
1. Abra o Eclipse e crie um projeto. Na **novo projeto** caixa de diálogo, selecione as seguintes opções e, em seguida, selecione **próxima**.
   
   * No painel da esquerda, selecione **HDInsight**.
   * No painel direito, selecione **Spark no HDInsight Local executar exemplo (Scala)**.

   ![Caixa de diálogo Novo Projeto](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
   
1. Para fornecer os detalhes do projeto, siga os passos 3 a 6 da secção anterior [programa de configuração de um projeto de Spark Scala para um cluster do Spark do HDInsight](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

1. O modelo adiciona um código de exemplo (**LogQuery**) sob o **src** pasta que pode ser executado localmente no seu computador.
   
   ![Localização do LogQuery](./media/apache-spark-eclipse-tool-plugin/local-app.png)
   
1. Com o botão direito a **LogQuery** aplicativo, aponte para **Run As**e, em seguida, selecione **1 aplicação Scala**. Saída como esta opção é apresentada no **consola** separador:
   
   ![Aplicação de Spark local resultado da execução](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="known-problems"></a>Problemas conhecidos
Quando ligar um cluster, sugiro que forneça a credencial de armazenamento.

![O início de sessão interativo](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

Existem dois modos para submeter as tarefas. Se a credencial de armazenamento for fornecida, modo de lote será utilizado para submeter a tarefa. Caso contrário, será utilizado modo interativo. Se o cluster estiver ocupado, poderá receber o erro abaixo.

![Erro de obtenção do Eclipse quando ocupado do cluster](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png)

![Erro de obtenção do Eclipse quando ocupado do cluster](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png)

## <a name="feedback"></a>Comentários
Se tiver algum comentário ou se tiver quaisquer outros problemas ao utilizar esta ferramenta, envie-num e-mail para hdivstool@microsoft.com.

## <a name="seealso"></a>Ver também
* [Descrição geral: Apache Spark no HDInsight do Azure](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: Efetuar análise de dados interativa com o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura de construção com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de registos de Web site com o Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Apache Spark com o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilizar o Azure Toolkit para IntelliJ para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilizar o Azure Toolkit para IntelliJ para depurar aplicações de Apache Spark remotamente através de VPN](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar o Azure Toolkit para IntelliJ para depurar aplicações de Apache Spark remotamente através de SSH](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utilizar ferramentas do HDInsight para IntelliJ com a Sandbox da Hortonworks](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utilizar blocos de notas do Zeppelin do Apache com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)

