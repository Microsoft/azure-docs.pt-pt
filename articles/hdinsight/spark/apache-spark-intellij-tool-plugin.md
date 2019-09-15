---
title: 'Tutorial-Azure Toolkit for IntelliJ: Aplicativo Spark para cluster HDInsight'
description: Tutorial-use a Azure Toolkit for IntelliJ para desenvolver aplicativos Spark escritos em escalares e enviá-los para um cluster HDInsight Spark.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: hrasheed
ms.openlocfilehash: c19d867553290ba956515fddc50c4fd836877860
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996039"
---
# <a name="tutorial-use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-hdinsight-cluster"></a>Tutorial: Usar Azure Toolkit for IntelliJ para criar aplicativos Apache Spark para o cluster HDInsight

Este tutorial demonstra como usar o plug-in Azure Toolkit for IntelliJ para desenvolver Apache Spark aplicativos escritos em [escalares](https://www.scala-lang.org/)e, em seguida, enviá-los para um cluster HDInsight Spark diretamente do IDE (ambiente de desenvolvimento integrado) do IntelliJ. Você pode usar o plug-in de algumas maneiras:

* Desenvolva e envie um aplicativo do Spark em um cluster HDInsight Spark.
* Acesse seus Azure HDInsight Spark recursos de cluster.
* Desenvolva e execute um aplicativo exscale Spark localmente.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Usar o plug-in Azure Toolkit for IntelliJ
> * Desenvolver Apache Spark aplicativos
> * Enviar aplicativo para o cluster HDInsight do Azure

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Kit de desenvolvimento Oracle Java](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  Este tutorial usa a versão do Java 8.0.202.

* IntelliJ ideia. Este artigo usa [a IntelliJ IDEA Community ver.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit for IntelliJ.  Consulte [instalando o Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalar o plug-in do Scala para o IntelliJ IDEA

Execute as seguintes etapas para instalar o plug-in escalar:

1. Abra o IntelliJ IDEA.

2. Na tela de boas-vindas, navegue até **Configurar** > **plug-ins** para abrir a janela plug- **ins** .

    ![Ativar o plug-in do Scala](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin1.png)

3. Selecione **instalar** para o plug-in escalar que está em destaque na nova janela.  

    ![Instalar o plug-in do Scala](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. Após a instalação bem-sucedida do plug-in, tem de reiniciar o IDE.

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Criar um aplicativo do Spark escalar para um cluster HDInsight Spark

1. Inicie o IntelliJ IDEA e selecione **criar novo projeto** para abrir a janela **novo projeto** .

2. Selecione **Azure Spark/HDInsight** no painel esquerdo.

3. Selecione **projeto do Spark (escala)** na janela principal.

4. Na lista suspensa **ferramenta de compilação** , selecione uma das seguintes opções:
   * Suporte ao assistente de criação de projeto do **Maven** para escalabilidade.
   * **SBT** para gerenciar as dependências e compilar para o projeto escalar.

     ![Caixa de diálogo O Novo Projeto](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Selecione **Seguinte**.

6. Na janela **novo projeto** , forneça as seguintes informações:  

    |  Propriedade   | Descrição   |  
    | ----- | ----- |  
    |Nome do projeto| Insira um nome.  Este tutorial utiliza `myApp`.|  
    |Local&nbsp;do projeto| Insira o local desejado para salvar o projeto.|
    |SDK do projeto| Isso pode estar em branco no primeiro uso da ideia.  Selecione **novo...** e navegue até o JDK.|
    |Versão do Spark|O assistente de criação integra a versão apropriada do SDK do Spark e do SDK do Scale. Se a versão do cluster do Spark for anterior à 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark2.x**. Este exemplo usa o **Spark 2.3.0 (escala 2.11.8)** .|

    ![Selecionar o SDK do Spark](./media/apache-spark-intellij-tool-plugin/intellij-new-project.png)

7. Selecione **Concluir**.  Pode levar alguns minutos para que o projeto fique disponível.

8. O projeto do Spark cria automaticamente um artefato para você. Para exibir o artefato, faça o seguinte:

   a. Na barra de menus, navegue até **arquivo** > **estrutura do projeto...** .

   b. Na janela **estrutura do projeto** , selecione **artefatos**.  

   c. Selecione **Cancelar** depois de exibir o artefato.

      ![Informações de artefato na caixa de diálogo](./media/apache-spark-intellij-tool-plugin/default-artifact-dialog.png)

9. Adicione o código-fonte do aplicativo fazendo o seguinte:

    a. Em projeto, navegue até **MyApp** > **src** > **Main** > **escala**.  

    b. Clique com o botão direito do mouse em **escala**e navegue até **nova** > **classe escalar**.

   ![Comandos para criar uma classe escalar a partir do projeto](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. Na caixa de diálogo **criar nova classe escala** , forneça um nome, selecione **objeto** na lista suspensa **tipo** e, em seguida, selecione **OK**.

     ![Caixa de diálogo Criar nova classe escala](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. O arquivo **MyApp. Scale** é aberto no modo de exibição principal. Substitua o código padrão pelo código encontrado abaixo:  

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

    O código lê os dados de HVAC. csv (disponíveis em todos os clusters do HDInsight Spark), recupera as linhas que têm apenas um dígito na sétima coluna no arquivo CSV e grava a saída `/HVACOut` no contêiner de armazenamento padrão do cluster.

## <a name="connect-to-your-hdinsight-cluster"></a>Conectar-se ao cluster HDInsight
O usuário pode [entrar na assinatura do Azure](#sign-in-to-your-azure-subscription)ou [vincular um cluster hdinsight](#link-a-cluster) usando o nome de usuário/senha do Ambari ou a credencial ingressada no domínio para se conectar ao cluster hdinsight.

### <a name="sign-in-to-your-azure-subscription"></a>Inicie sessão na sua subscrição do Azure

1. Na barra de menus, navegue até ferramenta de **exibição** > **Windows** > **Azure Explorer**.

   ![mostrar o Azure Explorer](./media/apache-spark-intellij-tool-plugin/show-azure-explorer1.png)

2. No Azure Explorer, clique com o botão direito do mouse no nó **Azure** e selecione **entrar**.

   ![Explorer clique com o botão direito do mouse em Azure](./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png)

3. Na caixa de diálogo **entrar no Azure** , escolha **logon de dispositivo**e, em seguida, selecione **entrar**.

    ![Exibir Gerenciador 2](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer2.png)

4. Na caixa de diálogo **logon do dispositivo do Azure** , clique em **copiar & abrir**.

   ![Exibir Explorer 5](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer5.png)

5. Na interface do navegador, Cole o código e clique em **Avançar**.

   ![Exibir Explorer 6](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer6.png)

6. Insira suas credenciais do Azure e, em seguida, feche o navegador.

   ![Exibir Explorer 7](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer7.png)

7. Depois que você estiver conectado, a caixa de diálogo **selecionar assinaturas** listará todas as assinaturas do Azure associadas às credenciais. Selecione sua assinatura e, em seguida, selecione o botão **selecionar** .

    ![A caixa de diálogo Selecionar assinaturas](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

8. No **Azure Explorer**, expanda **HDInsight** para exibir os clusters hdinsight Spark que estão em suas assinaturas.

    ![Exibir Explorer 3](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer3.png)

9. Para exibir os recursos (por exemplo, contas de armazenamento) associados ao cluster, você pode expandir ainda mais um nó de nome de cluster.

    ![Um nó de nome de cluster expandido](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer4.png)

### <a name="link-a-cluster"></a>Vincular um cluster

Você pode vincular um cluster HDInsight usando o nome de usuário gerenciado do Apache Ambari. Da mesma forma, para um cluster HDInsight ingressado no domínio, você pode vincular usando o domínio e o nome `user1@contoso.com`de usuário, como. Além disso, você pode vincular o cluster do serviço Livy.

1. Na barra de menus, navegue até ferramenta de **exibição** > **Windows** > **Azure Explorer**.

1. No Azure Explorer, clique com o botão direito do mouse no nó **HDInsight** e selecione **vincular um cluster**.

   ![menu de contexto do cluster de link](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. As opções disponíveis na janela **vincular um cluster** irão variar dependendo do valor selecionado na lista suspensa **tipo de recurso de link** .  Insira seus valores e, em seguida, selecione **OK**.

    * **Cluster HDInsight**  
  
        |Propriedade |Value |
        |----|----|
        |Tipo de recurso de link|Selecione **cluster HDInsight** na lista suspensa.|
        |Nome do cluster/URL| Insira o nome do cluster.|
        |Tipo de Autenticação| Deixar como **autenticação básica**|
        |Nome de utilizador| Insira o nome de usuário do cluster, o padrão é admin.|
        |Palavra-passe| Digite a senha para o nome de usuário.|

        ![caixa de diálogo Vincular cluster HDInsight](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Serviço Livy**  
  
        |Propriedade |Value |
        |----|----|
        |Tipo de recurso de link|Selecione **serviço Livy** na lista suspensa.|
        |Livy Endpoint| Enter Livy Endpoint|
        |Nome do Cluster| Insira o nome do cluster.|
        |Ponto de extremidade yarn|Opcional.|
        |Tipo de Autenticação| Deixar como **autenticação básica**|
        |Nome de utilizador| Insira o nome de usuário do cluster, o padrão é admin.|
        |Palavra-passe| Digite a senha para o nome de usuário.|

        ![caixa de diálogo Vincular cluster Livy](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

1. Você pode ver o cluster vinculado do nó **HDInsight** .

   ![cluster vinculado](./media/apache-spark-intellij-tool-plugin/hdinsight-linked-cluster.png)

1. Você também pode desvincular um cluster do **Azure Explorer**.

   ![cluster desvinculado](./media/apache-spark-intellij-tool-plugin/hdi-unlinked-cluster.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Executar um aplicativo do Spark escalar em um cluster Spark do HDInsight

Depois de criar um aplicativo escala, você pode enviá-lo para o cluster.

1. No projeto, navegue até **MyApp** > **src** > **Main** > escalaMyApp > .  Clique com o botão direito do mouse em **MyApp**e selecione **Enviar aplicativo Spark** (ele provavelmente estará localizado na parte inferior da lista).

      ![O comando enviar aplicativo Spark para HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. Na janela da caixa de diálogo **Enviar aplicativo Spark** , selecione **1. Spark no HDInsight**.

3. Na janela **Editar configuração** , forneça os seguintes valores e, em seguida, selecione **OK**:

    |Propriedade |Value |
    |----|----|
    |Clusters do Spark (somente Linux)|Selecione o cluster HDInsight Spark no qual você deseja executar o aplicativo.|
    |Selecione um artefato para enviar|Mantenha a configuração padrão.|
    |Nome da classe principal|O valor padrão é a classe principal do arquivo selecionado. Você pode alterar a classe selecionando as reticências ( **...** )  e escolhendo outra classe.|
    |Configurações de trabalho|Você pode alterar as chaves e/ou valores padrão. Para obter mais informações, consulte [API REST do Apache Livy](https://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Argumentos de linha de comando|Você pode inserir argumentos separados por espaço para a classe principal, se necessário.|
    |Jars referenciados e arquivos referenciados|Você pode inserir os caminhos para os jars e arquivos referenciados, se houver. Você também pode procurar arquivos no sistema de arquivos virtual do Azure, que atualmente dá suporte apenas ao cluster do ADLS Gen 2. Para obter mais informações: [Configuração de Apache Spark](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Consulte também [como carregar recursos no cluster](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer).|
    |Armazenamento de upload de trabalho|Expanda para revelar opções adicionais.|
    |Tipo de armazenamento|Selecione **usar blob do Azure para carregar** na lista suspensa.|
    |Conta de Armazenamento|Insira sua conta de armazenamento.|
    |Chave de Armazenamento|Insira sua chave de armazenamento.|
    |Contentor de armazenamento|Selecione o contêiner de armazenamento na lista suspensa uma vez que a **conta de armazenamento** e a chave de **armazenamento** foram inseridas.|

    ![A caixa de diálogo envio do Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

4. Selecione **SparkJobRun** para enviar seu projeto para o cluster selecionado. A guia **trabalho remoto do Spark no cluster** exibe o andamento da execução do trabalho na parte inferior. Você pode parar o aplicativo clicando no botão vermelho. Para saber como acessar a saída do trabalho, consulte a seção "acessar e gerenciar clusters do HDInsight Spark usando Azure Toolkit for IntelliJ" mais adiante neste artigo.  

    ![A janela de envio do Spark](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Depurar Apache Spark aplicativos localmente ou remotamente em um cluster HDInsight 

Também recomendamos outra maneira de enviar o aplicativo Spark para o cluster. Você pode fazer isso definindo os parâmetros no IDE de **configurações de execução/depuração** . Para obter mais informações, consulte [depurar Apache Spark aplicativos localmente ou remotamente em um cluster HDInsight com Azure Toolkit for IntelliJ por meio do ssh](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Acessar e gerenciar clusters do HDInsight Spark usando o Azure Toolkit for IntelliJ

Você pode executar várias operações usando Azure Toolkit for IntelliJ.  A maioria das operações é iniciada no **Azure Explorer**.  Na barra de menus, navegue até ferramenta de **exibição** > **Windows** > **Azure Explorer**.

### <a name="access-the-job-view"></a>Acessar a exibição do trabalho

1. No Azure Explorer, navegue até o **HDInsight** > \<seu cluster > > **trabalhos**.

    ![Nó de exibição de trabalho](./media/apache-spark-intellij-tool-plugin/intellij-job-view-node.png)

2. No painel direito, a guia **exibição de trabalho do Spark** exibe todos os aplicativos que foram executados no cluster. Selecione o nome do aplicativo para o qual você deseja ver mais detalhes.

    ![Detalhes da aplicação](./media/apache-spark-intellij-tool-plugin/intellij-view-job-logs.png)

3. Para exibir informações básicas de trabalho em execução, passe o mouse sobre o grafo de trabalho. Para exibir o grafo de estágios e as informações geradas por cada trabalho, selecione um nó no grafo de trabalho.

    ![Detalhes da etapa do trabalho](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Para exibir os logs usados com frequência, como *stderr*do driver, *stdout do driver*e informações de *diretório*, selecione a guia **log** .

    ![Detalhes do log](./media/apache-spark-intellij-tool-plugin/intellij-job-log-info.png)

5. Você também pode exibir a interface do usuário do histórico do Spark e a interface do usuário do YARN (no nível do aplicativo) selecionando um link na parte superior da janela.

### <a name="access-the-spark-history-server"></a>Acessar o servidor de histórico do Spark

1. No Azure Explorer, expanda **HDInsight**, clique com o botão direito do mouse no nome do cluster Spark e selecione **abrir interface do usuário de histórico do Spark**.  
2. Quando for solicitado, insira as credenciais de administrador do cluster, que você especificou ao configurar o cluster.

3. No painel do servidor de histórico do Spark, você pode usar o nome do aplicativo para procurar o aplicativo que acabou de ser executado. No código anterior, você define o nome do aplicativo usando `val conf = new SparkConf().setAppName("myApp")`. Portanto, o nome do aplicativo Spark é **MyApp**.

### <a name="start-the-ambari-portal"></a>Iniciar o portal do Ambari

1. No Azure Explorer, expanda **HDInsight**, clique com o botão direito do mouse no nome do cluster Spark e selecione **abrir portal de gerenciamento do cluster (Ambari)** .  

2. Quando for solicitado, insira as credenciais de administrador para o cluster. Você especificou essas credenciais durante o processo de configuração do cluster.

### <a name="manage-azure-subscriptions"></a>Gerenciar assinaturas do Azure

Por padrão, Azure Toolkit for IntelliJ lista os clusters Spark de todas as suas assinaturas do Azure. Se necessário, você pode especificar as assinaturas que deseja acessar.  

1. No Azure Explorer, clique com o botão direito do mouse no nó raiz **do Azure** e selecione **selecionar assinaturas**.  

2. Na janela **selecionar assinaturas** , desmarque as caixas de seleção ao lado das assinaturas que você não deseja acessar e, em seguida, selecione **fechar**.

## <a name="spark-console"></a>Console do Spark

Você pode executar o console local do Spark (escala) ou executar o console de sessão interativa do Spark Livy (escala).

### <a name="spark-local-consolescala"></a>Console local do Spark (escalabilidade)

Certifique-se de que você tenha satisfeito com o WINUTILS. Pré-requisito de EXE.

1. Na barra de menus, navegue para **executar** > **Editar configurações...** .

2. Na janela **executar/depurar configurações** , no painel esquerdo, navegue até **Apache Spark no hdinsight** >  **[Spark no hdinsight] MyApp**.

3. Na janela principal, selecione a guia **executar localmente** .

4. Forneça os seguintes valores e, em seguida, selecione **OK**:

    |Propriedade |Value |
    |----|----|
    |Classe principal do trabalho|O valor padrão é a classe principal do arquivo selecionado. Você pode alterar a classe selecionando as reticências ( **...** )  e escolhendo outra classe.|
    |Variáveis de ambiente|Verifique se o valor de HADOOP_HOME está correto.|
    |Local do WINUTILS. exe|Verifique se o caminho está correto.|

    ![Configuração do conjunto de console local](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. No projeto, navegue até **MyApp** > **src** > **Main** > escalaMyApp > .  

6. Na barra de menus, navegue até **ferramentas** > **console** > do Spark**executar console local do Spark (escala)** .

7. Em seguida, duas caixas de diálogo podem ser exibidas para perguntar se você deseja corrigir automaticamente as dependências. Nesse caso, selecione **correção automática**.

    ![Fix1 automático do Spark](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix1.png)

    ![Fix2 automático do Spark](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix2.png)

8. O console deve ser semelhante à imagem abaixo. No tipo `sc.appName`de janela do console, pressione Ctrl + Enter.  O resultado será mostrado. Você pode encerrar o console local clicando em botão vermelho.

    ![Resultado do console local](./media/apache-spark-intellij-tool-plugin/local-console-result.png)

### <a name="spark-livy-interactive-session-consolescala"></a>Console de sessão interativa do Spark Livy (escala)

Ele só tem suporte no IntelliJ 2018,2 e 2018,3.

1. Na barra de menus, navegue para **executar** > **Editar configurações...** .

2. Na janela **executar/depurar configurações** , no painel esquerdo, navegue até **Apache Spark no hdinsight** >  **[Spark no hdinsight] MyApp**.

3. Na janela principal, selecione a guia **executar remotamente no cluster** .

4. Forneça os seguintes valores e, em seguida, selecione **OK**:

    |Propriedade |Value |
    |----|----|
    |Clusters do Spark (somente Linux)|Selecione o cluster HDInsight Spark no qual você deseja executar o aplicativo.|
    |Nome da classe principal|O valor padrão é a classe principal do arquivo selecionado. Você pode alterar a classe selecionando as reticências ( **...** )  e escolhendo outra classe.|

    ![Configuração do conjunto de console interativo](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. No projeto, navegue até **MyApp** > **src** > **Main** > escalaMyApp > .  

6. Na barra de menus, navegue até **ferramentas** > **console** > do Spark**executar console de sessão interativa do Spark Livy (escala)** .

7. O console deve ser semelhante à imagem abaixo. No tipo `sc.appName`de janela do console, pressione Ctrl + Enter.  O resultado será mostrado. Você pode encerrar o console local clicando em botão vermelho.

    ![Resultado do console interativo](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Enviar seleção para o console do Spark

É conveniente prever o resultado do script enviando um código para o console local ou o Livy Interactive Session console (escala). Você pode realçar um código no arquivo escalar e clicar com o botão direito do mouse em **Enviar seleção para o console do Spark**. O código selecionado será enviado para o console e ser executado. O resultado será exibido após o código no console. O console verificará os erros, se houver.  

   ![Enviar seleção para o console do Spark](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="reader-only-role"></a>Função somente de leitor

Quando os usuários enviam o trabalho para um cluster com permissão de função somente de leitor, as credenciais de Ambari são necessárias.

### <a name="link-cluster-from-context-menu"></a>Vincular cluster no menu de contexto

1. Entre com a conta de função somente de leitor.

2. No **Azure Explorer**, expanda **hdinsight** para exibir os clusters hdinsight que estão em sua assinatura. Os clusters marcados como **"função: leitor"** têm apenas a permissão de função de leitor.

    ![Exibir Explorer 15](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer15.png)

3. Clique com o botão direito do mouse no cluster com permissão de função somente leitura. Selecione **vincular este cluster** no menu de contexto para vincular o cluster. Insira o nome de usuário e a senha do Ambari.

    ![exibir o Explorer 11](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer11.png)

4. Se o cluster for vinculado com êxito, o HDInsight será atualizado.
   O estágio do cluster será vinculado.
  
    ![Exibir Explorer 8](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Vincular cluster expandindo o nó trabalhos

1. Clique no nó **trabalhos** , a janela **acesso negado ao trabalho do cluster** é exibida.

2. Clique em **vincular este cluster** para vincular o cluster.

    ![Exibir Explorer 9](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer9.png)

### <a name="link-cluster-from-rundebug-configurations-window"></a>Vincular o cluster da janela Executar/depurar configurações

1. Crie uma configuração do HDInsight. Em seguida, selecione **executar remotamente no cluster**.

2. Selecione um cluster que tenha permissão de função somente de leitor para **clusters Spark (somente Linux)** . Mensagem de aviso mostrada. Você pode clicar em **vincular este cluster** para vincular o cluster.

   ![criar configuração 1](./media/apache-spark-intellij-tool-plugin/create-configuration.png)

### <a name="view-storage-accounts"></a>Exibir contas de armazenamento

* Para clusters com permissão de função somente de leitor, clique em nó **contas de armazenamento** , janela **acesso de armazenamento negado** aparece. Você pode clicar em **abrir Gerenciador de armazenamento do Azure** para abrir o Gerenciador de armazenamento.

   ![exibir o Explorer 14](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer14.png)

   ![Exibir Explorer 10](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer10.png)

* Para clusters vinculados, clique no nó **contas de armazenamento** , a janela **acesso de armazenamento negado** é exibida. Você pode clicar em **abrir o armazenamento do Azure** para abrir o Gerenciador de armazenamento.

   ![Exibir Explorer 13](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer13.png)

   ![exibir o Explorer 12](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer12.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Converter os aplicativos IntelliJ IDEA existentes para usar Azure Toolkit for IntelliJ

Você pode converter os aplicativos existentes do Spark Scale que você criou no IntelliJ IDEA para ser compatível com Azure Toolkit for IntelliJ. Em seguida, você pode usar o plug-in para enviar os aplicativos para um cluster HDInsight Spark.

1. Para um aplicativo do Spark Scale existente criado por meio de IntelliJ IDEA, abra o arquivo. IML associado.

2. No nível raiz, há um elemento de **módulo** como o seguinte:

        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Edite o elemento a `UniqueKey="HDInsightTool"` ser adicionado para que o elemento **Module** se pareça com o seguinte:

        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

3. Guarde as alterações. Seu aplicativo agora deve ser compatível com Azure Toolkit for IntelliJ. Você pode testá-lo clicando com o botão direito do mouse no nome do projeto no projeto. O menu pop-up agora tem a opção **Enviar aplicativo Spark para o HDInsight**.

## <a name="clean-up-resources"></a>Limpar recursos

Se você não pretende usar este aplicativo, exclua o cluster que você criou com as seguintes etapas:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Na caixa de **pesquisa** na parte superior, digite **HDInsight**.

1. Selecione **clusters HDInsight** em **Serviços**.

1. Na lista de clusters HDInsight que aparece, selecione o **...** ao lado do cluster que você criou para este tutorial.

1. Selecione **Eliminar**. Selecione **Sim**.

![Eliminar um cluster do HDInsight](./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png "Eliminar um cluster do HDInsight")

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você aprendeu a usar o plug-in Azure Toolkit for IntelliJ para desenvolver Apache Spark aplicativos escritos em [escalares](https://www.scala-lang.org/)e, em seguida, os enviou para um cluster HDInsight Spark diretamente do ambiente de desenvolvimento integrado IntelliJ ( IDE). Avance para o próximo artigo para ver como os dados registrados no Apache Spark podem ser obtidos em uma ferramenta de análise de BI, como Power BI.

> [!div class="nextstepaction"]
> [Analisar dados com ferramentas do BI](apache-spark-use-bi-tools.md)
