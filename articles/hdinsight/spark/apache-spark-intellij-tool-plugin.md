---
title: 'Azure Toolkit para IntelliJ: Spark app - HDInsight'
description: Utilize o Kit de Ferramentas Azure para IntelliJ para desenvolver aplicações Spark escritas em Scala e submetê-las a um cluster HDInsight Spark.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: ced066623d23c6894745374ace812454c52f3109
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103420022"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-hdinsight-cluster"></a>Use o Azure Toolkit para IntelliJ para criar aplicações Apache Spark para cluster HDInsight

Este artigo demonstra como desenvolver aplicações Apache Spark no Azure HDInsight utilizando o plug-in **Azure Toolkit** para o IntelliJ IDE. [Azure HDInsight](../hdinsight-overview.md) é um serviço de análise de código aberto gerido na nuvem. O serviço permite-lhe usar estruturas de código aberto como Hadoop, Apache Spark, Apache Hive e Apache Kafka.

Pode utilizar o plug-in **Azure Toolkit** de algumas formas:

* Desenvolver e submeter uma aplicação Scala Spark a um cluster HDInsight Spark.
* Aceda aos seus recursos de cluster Azure HDInsight Spark.
* Desenvolver e executar uma aplicação Scala Spark localmente.

Neste artigo, vai aprender a:
> [!div class="checklist"]
> * Utilize o kit de ferramentas Azure para o plug-in IntelliJ
> * Desenvolver aplicações Apache Spark
> * Submeta uma candidatura ao cluster Azure HDInsight

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Kit de desenvolvimento Oracle Java.](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)  Este artigo utiliza a versão Java 8.0.202.

* Ideia intellij. Este artigo utiliza [IntelliJ IDEA Community ver.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit para IntelliJ.  Consulte a instalação do conjunto de [ferramentas Azure para IntelliJ](/azure/developer/java/toolkit-for-intellij/).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalar o plug-in do Scala para o IntelliJ IDEA

Passos para instalar o plugin Scala:

1. Abra o IntelliJ IDEA.

2. No ecrã de boas-vindas, navegue até **Configure**  >  **Plugins** para abrir a janela **Plugins.**

    ![IntelliJ IDEA permite plugin de scala](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin1.png)

3. Selecione **Instalar** para o plugin Scala que está em destaque na nova janela.  

    ![IntelliJ IDEA instala plugin scala](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. Após a instalação bem-sucedida do plug-in, tem de reiniciar o IDE.

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Crie uma aplicação Spark Scala para um cluster HDInsight Spark

1. Inicie a Ideia IntelliJ e selecione **Criar Novo Projeto** para abrir a janela New **Project.**

2. Selecione **Azure Spark/HDInsight** a partir do painel esquerdo.

3. Selecione **Spark Project (Scala)** da janela principal.

4. A partir da lista de drop-down da **ferramenta Build,** selecione uma das seguintes opções:
   * **Maven** para o suporte de assistente de criação de projeto Scala.
   * **SBT** para gerir as dependências e construção para o projeto Scala.

     ![IntelliJ IDEA Nova caixa de diálogo do projeto](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Selecione **Seguinte**.

6. Na janela **New Project,** forneça as seguintes informações:  

    |  Propriedade   | Descrição   |  
    | ----- | ----- |  
    |Nome do projeto| Insira um nome.  Este artigo `myApp` utiliza.|  
    |&nbsp;Localização do projeto| Entre no local para salvar o seu projeto.|
    |Projeto SDK| Este campo pode estar em branco no seu primeiro uso do IDEA.  Selecione **New...** e navegue para o seu JDK.|
    |Versão Spark|O assistente de criação integra a versão adequada para Spark SDK e Scala SDK. Se a versão do cluster do Spark for anterior à 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark2.x**. Este exemplo utiliza **a Faísca 2.3.0 (Scala 2.11.8)**.|

    ![Selecionando o Apache Spark SDK](./media/apache-spark-intellij-tool-plugin/intellij-new-project.png)

7. Selecione **Concluir**.  Pode levar alguns minutos até que o projeto fique disponível.

8. O projeto Spark cria automaticamente um artefacto para si. Para ver o artefacto, faça os seguintes passos:

   a. Da barra de menu, navegue para a Estrutura do Projeto **de**  >  **Arquivo...**.

   b. A partir da janela Estrutura do **Projeto,** selecione **Artefactos.**  

   c. Selecione **Cancelar**  depois de ver o artefacto.

      ![Informação de artefacto na caixa de diálogo](./media/apache-spark-intellij-tool-plugin/default-artifact-dialog.png)

9. Adicione o código fonte da sua aplicação fazendo os seguintes passos:

    a. Do Project, navegue até ao **myApp**  >  **src**  >  **main**  >  **scala.**  

    b. Scala com clique à direita e, em seguida, navegue para a Classe **New**  >  **Scala**.

   ![Comandos para a criação de uma classe Scala do Projeto](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. Na caixa de diálogo **'Criar Nova Classe Scala',** forneça um nome, selecione **Object** na lista drop-down **Kind** e, em seguida, selecione **OK**.

     ![Criar caixa de diálogo classe Scala nova](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. O ficheiro **myApp.scala** abre então na vista principal. Substitua o código predefinido pelo código abaixo encontrado:  

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

    O código lê os dados de HVAC.csv (disponível em todos os clusters HDInsight Spark), recupera as linhas que têm apenas um dígito na sétima coluna no ficheiro CSV e escreve a saída para `/HVACOut` debaixo do recipiente de armazenamento predefinido para o cluster.

## <a name="connect-to-your-hdinsight-cluster"></a>Conecte-se ao seu cluster HDInsight

O utilizador pode [iniciar seducação na subscrição do Azure,](#sign-in-to-your-azure-subscription)ou [ligar um cluster HDInsight](#link-a-cluster). Utilize o nome de utilizador/palavra-passe ambari ou a credencial de domínio para ligar ao seu cluster HDInsight.

### <a name="sign-in-to-your-azure-subscription"></a>Inscreva-se na sua assinatura Azure

1. A partir da barra de menu, navegue para **ver o Tool**  >  **Windows**  >  **Azure Explorer**.

   ![IntelliJ IDEA mostra explorador azul](./media/apache-spark-intellij-tool-plugin/show-azure-explorer1.png)

2. A partir do Azure Explorer, clique à direita no nó **Azure** e, em seguida, selecione **Iniciar Sôm.**

   ![IntelliJ IDEA explorador de botão direito azul](./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png)

3. Na caixa **de diálogo Azure In** dialog, escolha Iniciar **sessão** e, em seguida, selecione **Iniciar sessão .**

    !['IntelliJ IDEA azure login do dispositivo de entrada'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer2.png)

4. Na caixa de diálogo de início de **sessão do Dispositivo Azure,** clique em **Copiar&Abrir**.

   !['IntelliJ IDEA azure device login'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer5.png)

5. Na interface do navegador, cole o código e, em seguida, clique em **Seguinte**.

   !['Microsoft insira o diálogo de código para HDI'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer6.png)

6. Insira as suas credenciais Azure e, em seguida, feche o navegador.

   !['Microsoft insira o diálogo de e-mail para HDI'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer7.png)

7. Depois de ter assinado, a caixa de diálogo **Select Subscriptions** lista todas as subscrições do Azure que estão associadas às credenciais. Selecione a sua subscrição e, em seguida, selecione o botão **Selecionar.**

    ![A caixa de diálogo Selecionar Subscrições](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

8. A partir do **Azure Explorer,** expanda o **HDInsight** para ver os clusters HDInsight Spark que estão nas suas subscrições.

    ![IntelliJ IDEA Azure Explorer vista principal](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer3.png)

9. Para visualizar os recursos (por exemplo, contas de armazenamento) que estão associados ao cluster, pode expandir ainda mais um nó de nome cluster.

    ![Contas de armazenamento do Azure Explorer](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer4.png)

### <a name="link-a-cluster"></a>Ligue um cluster

Pode ligar um cluster HDInsight utilizando o nome de utilizador gerido por Apache Ambari. Da mesma forma, para um cluster HDInsight ligado a domínio, pode ligar-se utilizando o domínio e o nome de utilizador, tais como `user1@contoso.com` . Também pode ligar o cluster livy Service.

1. A partir da barra de menu, navegue para **ver o Tool**  >  **Windows**  >  **Azure Explorer**.

1. A partir do Azure Explorer, clique com o nó **HDInsight** e, em seguida, selecione **Link A Cluster**.

   ![Menu de contexto de cluster de ligação Azure Explorer](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. As opções disponíveis na janela **Do Cluster Link A** variarão consoante o valor que selecionar na lista de down-down do Tipo de Recurso de **Ligação.**  Introduza os seus valores e, em seguida, selecione **OK**.

    * **HDInsight Cluster**  
  
        |Propriedade |Valor |
        |----|----|
        |Tipo de recurso de ligação|Selecione **HDInsight Cluster** da lista de drop-down.|
        |Nome/URL do cluster| Insira o nome do cluster.|
        |Tipo de Autenticação| Sair como **Autenticação Básica**|
        |Nome de Utilizador| Introduza o nome de utilizador do cluster, o padrão é administrador.|
        |Palavra-passe| Introduza a palavra-passe para o nome de utilizador.|

        ![IntelliJ IDEA liga um diálogo de cluster](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Serviço Livy**  
  
        |Propriedade |Valor |
        |----|----|
        |Tipo de recurso de ligação|Selecione **Livy Service** da lista de drop-down.|
        |Ponta-de-final de Livy| Insira Livy Endpoint|
        |Nome do Cluster| Insira o nome do cluster.|
        |Ponta de fio|Opcional.|
        |Tipo de Autenticação| Sair como **Autenticação Básica**|
        |Nome de Utilizador| Introduza o nome de utilizador do cluster, o padrão é administrador.|
        |Palavra-passe| Introduza a palavra-passe para o nome de utilizador.|

        ![IntelliJ IDEA liga diálogo de cluster Livy](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

1. Pode ver o seu cluster ligado a partir do nó **HDInsight.**

   ![Aglomerado ligado a Azure Explorer1](./media/apache-spark-intellij-tool-plugin/hdinsight-linked-cluster.png)

1. Também pode desvincular um cluster do **Azure Explorer.**

   ![Aglomerado de Azure Explorer desvinculado](./media/apache-spark-intellij-tool-plugin/hdi-unlinked-cluster.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Executar uma aplicação Spark Scala em um cluster HDInsight Spark

Depois de criar uma aplicação Scala, pode submetê-la ao cluster.

1. Do Project, navegue para **o myApp**  >  **src**  >  **main**  >  **scala**  >  **myApp**.  Clique com o botão direito **no myApp** e selecione **'Enviar a Aplicação de Faíscas'** (provavelmente estará localizado na parte inferior da lista).

      ![A Aplicação de Faísca submeter ao comando HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. Na janela de diálogo **de inscrição** de faíscas, selecione **1. Faísca em HDInsight**.

3. Na janela de **configuração editar,** forneça os seguintes valores e, em seguida, selecione **OK**:

    |Propriedade |Valor |
    |----|----|
    |Aglomerados de faíscas (apenas Linux)|Selecione o cluster HDInsight Spark no qual pretende executar a sua aplicação.|
    |Selecione um artefacto para submeter|Deixe a definição predefinida.|
    |Nome da classe principal|O valor predefinido é a classe principal do ficheiro selecionado. Pode alterar a classe selecionando a elipse.  e escolher outra classe.|
    |Configurações de trabalho|Pode alterar as teclas predefinidos e, ou valores. Para mais informações, consulte [a Apache Livy REST API.](https://livy.incubator.apache.org/docs/latest/rest-api.html)|
    |Argumentos de linha de comando|Pode introduzir argumentos separados por espaço para a classe principal, se necessário.|
    |Frascos referenciados e ficheiros referenciados|Pode introduzir os caminhos para os frascos e ficheiros referenciados, se houver. Também pode navegar em ficheiros no sistema de ficheiros virtuais Azure, que atualmente apenas suporta o cluster ADLS Gen 2. Para mais informações: [Configuração de faíscas Apache](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Ver também, [Como carregar recursos para cluster.](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md)|
    |Armazenamento de upload de trabalho|Expandir para revelar opções adicionais.|
    |Tipo de armazenamento|**Selecione Use Azure Blob para fazer** o upload da lista de drop-down.|
    |Conta de Armazenamento|Insira a sua conta de armazenamento.|
    |Chave de armazenamento|Insira a sua chave de armazenamento.|
    |Recipiente de armazenamento|Selecione o seu recipiente de armazenamento da lista de drop-down assim que **a conta de armazenamento** e a chave de **armazenamento** erram.|

    ![A caixa de diálogo de submissão de faíscas](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

4. Selecione **SparkJobRun** para submeter o seu projeto ao cluster selecionado. O **trabalho de faísca remota no separador Cluster** mostra o progresso da execução do trabalho na parte inferior. Pode parar a aplicação clicando no botão vermelho.

    ![Janela de submissão de faíscas apaches](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Debug Apache Spark aplica aplicações local ou remotamente num cluster HDInsight

Recomendamos também outra forma de submeter o pedido spark ao cluster. Pode fazê-lo definindo os parâmetros nas **configurações IDE de Run/Debug.** Consulte [as aplicações Debug Apache Spark local ou remotamente num cluster HDInsight com o Azure Toolkit for IntelliJ através de SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Aceda e gere clusters HDInsight Spark utilizando o Azure Toolkit para IntelliJ

Pode fazer várias operações utilizando o Azure Toolkit para IntelliJ.  A maioria das operações são iniciadas a partir do **Azure Explorer.**  A partir da barra de menu, navegue para **ver o Tool**  >  **Windows**  >  **Azure Explorer**.

### <a name="access-the-job-view"></a>Aceda à vista de emprego

1. Do Azure Explorer, navegue até **HDInsight**  >  \<Your Cluster>  >  **Jobs**.

    ![Nó de vista de intelliJ Azure Explorer Job](./media/apache-spark-intellij-tool-plugin/intellij-job-view-node.png)

2. No painel direito, o separador **Spark Job View** exibe todas as aplicações que foram executadas no cluster. Selecione o nome da aplicação para a qual deseja ver mais detalhes.

    ![Detalhes da aplicação de visualização de emprego spark](./media/apache-spark-intellij-tool-plugin/intellij-view-job-logs.png)

3. Para apresentar informações básicas de trabalho de execução, paire sobre o gráfico de trabalho. Para visualizar o gráfico de estágios e a informação que cada trabalho gera, selecione um nó no gráfico de trabalho.

    ![Spark Job View Job Detalhes do estágio de trabalho](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Para visualizar registos frequentemente utilizados, tais como *Driver Stderr,* *Driver Stdout* e *Diretório Info,* selecione o **separador Registar.**

    ![Detalhes do registo de visualização de emprego de faísca](./media/apache-spark-intellij-tool-plugin/intellij-job-log-info.png)

5. Pode ver a UI da história da faísca e a UI YARN (ao nível da aplicação). Selecione um link na parte superior da janela.

### <a name="access-the-spark-history-server"></a>Aceda ao servidor histórico Spark

1. A partir do Azure Explorer, expanda **o HDInsight,** clique com o botão direito do seu nome de cluster Spark e, em seguida, selecione **Open Spark History UI**.  
2. Quando for solicitado, insira as credenciais de administração do cluster, que especificou quando configurar o cluster.

3. No painel de instrumentos do servidor Spark History, pode utilizar o nome da aplicação para procurar a aplicação que acabou de terminar de executar. No código anterior, define o nome da aplicação utilizando `val conf = new SparkConf().setAppName("myApp")` . O nome da sua aplicação Spark é **myApp.**

### <a name="start-the-ambari-portal"></a>Inicie o portal Ambari

1. A partir do Azure Explorer, expanda **o HDInsight,** clique com o botão direito do seu nome de cluster Spark e, em seguida, selecione **Open Cluster Management Portal (Ambari)**.  

2. Quando for solicitado, insira as credenciais de administração para o cluster. Especificou estas credenciais durante o processo de configuração do cluster.

### <a name="manage-azure-subscriptions"></a>Gerir subscrições do Azure

Por padrão, o Azure Toolkit para IntelliJ lista os clusters Spark de todas as suas subscrições Azure. Se necessário, pode especificar as subscrições a que pretende aceder.  

1. A partir do Azure Explorer, clique com o nó de raiz **Azure** e, em seguida, **selecione Select Subscrições**.  

2. A partir da janela **'Selecionar assinaturas',** limpe as caixas de verificação ao lado das subscrições a que não pretende aceder e, em seguida, selecione **Fechar**.

## <a name="spark-console"></a>Consola spark

Pode executar a Consola Local Spark (Scala) ou executar a Consola De Sessão Interativa Spark Livy (Scala).

### <a name="spark-local-consolescala"></a>Consola Local spark (Scala)

Certifique-se de ter satisfeito o pré-requisito WINUTILS.EXE.

1. A partir da barra de menu, navegue para  >  **configurações de edição de execução...**.

2. A partir da janela **configurações Run/Debug,** no painel esquerdo, navegue até **Apache Spark em HDInsight**  >  **[Spark on HDInsight] myApp**.

3. A partir da janela principal, selecione o **`Locally Run`** separador.

4. Forneça os seguintes valores e, em seguida, selecione **OK**:

    |Propriedade |Valor |
    |----|----|
    |Classe principal de emprego|O valor predefinido é a classe principal do ficheiro selecionado. Pode alterar a classe selecionando a elipse.  e escolher outra classe.|
    |Variáveis de ambiente|Certifique-se de que o valor para HADOOP_HOME está correto.|
    |WINUTILS.exe localização|Certifique-se de que o caminho está correto.|

    ![Configuração local do conjunto de consolas](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. Do Project, navegue para **o myApp**  >  **src**  >  **main**  >  **scala**  >  **myApp**.  

6. A partir da barra de menu, navegue para **ferramentas**  >  **Spark Console** Run Spark Local Console  >  **(Scala)**.

7. Em seguida, podem ser apresentados dois diálogos para lhe perguntar se pretende corrigir automaticamente as dependências. Em caso afirmativo, selecione **Auto Fix**.

    ![IntelliJ IDEA Spark Auto Fix diálogo1](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix1.png)

    ![IntelliJ IDEA Spark Auto Fix diálogo2](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix2.png)

8. A consola deve ser semelhante à imagem abaixo. No tipo de janela da consola `sc.appName` , e, em seguida, prima ctrl+Enter.  O resultado será mostrado. Pode terminar a consola local clicando no botão vermelho.

    ![Resultado da consola local IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin/local-console-result.png)

### <a name="spark-livy-interactive-session-consolescala"></a>Consola de sessão interativa Spark Livy (Scala)

1. A partir da barra de menu, navegue para  >  **configurações de edição de execução...**.

2. A partir da janela **configurações Run/Debug,** no painel esquerdo, navegue até **Apache Spark em HDInsight**  >  **[Spark on HDInsight] myApp**.

3. A partir da janela principal, selecione o **`Remotely Run in Cluster`** separador.

4. Forneça os seguintes valores e, em seguida, selecione **OK**:

    |Propriedade |Valor |
    |----|----|
    |Aglomerados de faíscas (apenas Linux)|Selecione o cluster HDInsight Spark no qual pretende executar a sua aplicação.|
    |Nome da classe principal|O valor predefinido é a classe principal do ficheiro selecionado. Pode alterar a classe selecionando a elipse.  e escolher outra classe.|

    ![Configuração do conjunto de consolas interativas](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. Do Project, navegue para **o myApp**  >  **src**  >  **main**  >  **scala**  >  **myApp**.  

6. A partir da barra de menu, navegue para **ferramentas**  >  **Spark Console** Run Spark  >  **Livy Interactive Session Console Console(Scala)**.

7. A consola deve ser semelhante à imagem abaixo. No tipo de janela da consola `sc.appName` , e, em seguida, prima ctrl+Enter.  O resultado será mostrado. Pode terminar a consola local clicando no botão vermelho.

    ![Resultado da consola interativa Intellij IDEA](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Enviar seleção para consola spark

É conveniente para si prever o resultado do script enviando algum código para a consola local ou consola de sessão interativa Livy (Scala). Pode destacar algum código no ficheiro Scala e, em seguida, clique com o botão direito **Enviar Seleção para consola de faíscas.** O código selecionado será enviado para a consola. O resultado será apresentado após o código na consola. A consola verificará os erros se existirem.  

   ![Enviar seleção para consola spark](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Integre-se com o HdInsight Identity Broker (HIB)

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Conecte-se ao seu cluster HDInsight ESP com o ID Broker (HIB)

Pode seguir os passos normais para iniciar seducação na subscrição do Azure para ligar ao seu cluster HDInsight ESP com o ID Broker (HIB). Após o sinstrução, verá a lista de clusters no Azure Explorer. Para obter mais instruções, consulte [Connect to your HDInsight cluster](#connect-to-your-hdinsight-cluster).

### <a name="run-a-spark-scala-application-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Executar uma aplicação Spark Scala num cluster HDInsight ESP com ID Broker (HIB)

Pode seguir os passos normais para submeter o trabalho ao cluster HDInsight ESP com ID Broker (HIB). Consulte a [aplicação Spark Scala num cluster HDInsight Spark](#run-a-spark-scala-application-on-an-hdinsight-spark-cluster) para obter mais instruções.

Fazemos o upload dos ficheiros necessários para uma pasta com o nome da sua conta de inscrição e pode ver o caminho de upload no ficheiro de configuração.

   ![caminho de upload na configuração](./media/apache-spark-intellij-tool-plugin/upload-path-in-the-configuration.png)

### <a name="spark-console-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Consola de faíscas num cluster HDInsight ESP com ID Broker (HIB)

Pode executar a Consola Local spark (Scala) ou executar a Consola Interativa Spark Livy (Scala) num cluster HDInsight ESP com ID Broker (HIB). Consulte a [Consola Spark](#spark-console) para obter mais instruções.

   > [!NOTE]  
   > Para o cluster HDInsight ESP com Id Broker (HIB), [ligue um cluster](#link-a-cluster) e [depurar aplicações Apache Spark remotamente](#debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster) não é suportado atualmente.

## <a name="reader-only-role"></a>Papel apenas para o leitor

Quando os utilizadores submetem trabalho a um cluster com permissão de função apenas para leitores, são necessárias credenciais Ambari.

### <a name="link-cluster-from-context-menu"></a>Cluster de ligação do menu de contexto

1. Inscreva-se com a conta de função apenas para leitor.

2. A partir do **Azure Explorer,** **expanda o HDInsight** para ver clusters HDInsight que estão na sua subscrição. Os clusters marcados **"Role:Reader"** só têm permissão de função apenas para leitor.

    !['IntelliJ Azure Explorer Role:Reader'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer15.png)

3. Clique com o botão direito no cluster com permissão de função apenas para leitor. Selecione **Link este cluster** do menu de contexto para o cluster de ligação. Insira o nome de utilizador E Palavra-Passe Ambari.

    ![IntelliJ Azure Explorer liga este cluster](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer11.png)

4. Se o cluster estiver ligado com sucesso, o HDInsight será atualizado.
   A fase do cluster ficará ligada.
  
    ![Diálogo ligado ao IntelliJ Azure Explorer](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Link cluster através da expansão do nó jobs

1. Clique no nó **Jobs,** **Cluster Job Access Negada** janela aparece.

2. Clique **em Ligar este cluster** para ligar o cluster.

    ![cluster acesso trabalho negado diálogo](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer9.png)

### <a name="link-cluster-from-rundebug-configurations-window"></a>Cluster de ligação a partir da janela configurações Run/Debug

1. Crie uma configuração HDInsight. Em seguida, selecione **Remotely Run in Cluster**.

2. Selecione um cluster, que tem permissão de função apenas para leitores para **clusters Spark (apenas Linux)**. A mensagem de aviso aparece. Pode clicar **em Ligar este cluster** para ligar o cluster.

   ![IntelliJ IDEA executar/depurar configuração criar](./media/apache-spark-intellij-tool-plugin/create-configuration.png)

### <a name="view-storage-accounts"></a>Ver Contas de Armazenamento

* Para clusters com permissão de função apenas para leitor, clique no nó **contas de armazenamento,** a janela **"Storage Access Negado"** aparece. Pode clicar no **Open Azure Storage Explorer** para abrir o Storage Explorer.

   !['IntelliJ IDEA Acesso ao armazenamento negado'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer14.png)

   ![Botão intellij IDEA Storage Access Negado](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer10.png)

* Para clusters ligados, clique no nó **contas de armazenamento,** a janela **"Storage Access Negado"** aparece. Pode clicar em **Open Azure Storage** para abrir o Storage Explorer.

   !['IntelliJ IDEA Acesso ao armazenamento negado2'](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer13.png)

   ![Botão IntelliJ IDEA Storage Access Denied2](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer12.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Converter aplicações IntelliJ IDEA existentes para utilizar o Azure Toolkit para IntelliJ

Pode converter as aplicações Spark Scala existentes que criou no IntelliJ IDEA para serem compatíveis com o Azure Toolkit para IntelliJ. Em seguida, pode utilizar o plug-in para submeter as aplicações a um cluster HDInsight Spark.

1. Para uma aplicação Spark Scala existente que foi criada através do IntelliJ IDEA, abra o `.iml` ficheiro associado.

2. Ao nível da raiz, encontra-se um elemento **de módulo** como o seguinte texto:

    ```xml
    <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
    ```

   Editar o elemento a adicionar `UniqueKey="HDInsightTool"` de modo a que o elemento do **módulo** se pareça com o seguinte texto:

    ```xml
    <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
    ```

3. Guarde as alterações. A sua aplicação deve agora ser compatível com o Azure Toolkit para IntelliJ. Pode testá-lo clicando no nome do projeto no Project. O menu pop-up tem agora a opção **Submeter a Aplicação de Faísca a HDInsight**.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine o cluster que criou com os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Na caixa **de pesquisa** no topo, escreva **HDInsight**.

1. Selecione **clusters HDInsight** em **Serviços**.

1. Na lista de clusters HDInsight que aparecem, selecione o **...** ao lado do cluster que criou para este artigo.

1. Selecione **Eliminar**. Selecione **Yes** (Sim).

![Portal Azure elimina cluster HDInsight](./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png "Excluir o cluster HDInsight")

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar o Azure Toolkit para o plug-in IntelliJ para desenvolver aplicações Apache Spark escritas em [Scala.](https://www.scala-lang.org/) Em seguida, submeteu-os a um cluster HDInsight Spark diretamente do ambiente de desenvolvimento integrado IntelliJ (IDE). Avance para o próximo artigo para ver como os dados registados no Apache Spark podem ser puxados para uma ferramenta de análise de BI, como o Power BI.

> [!div class="nextstepaction"]
> [Analise os dados do Apache Spark utilizando o Power BI](apache-spark-use-bi-tools.md)
