---
title: 'Azure Toolkit for Eclipse: Criar aplicativos escalares para o HDInsight Spark '
description: Use as ferramentas do HDInsight no Azure Toolkit for Eclipse para desenvolver aplicativos Spark escritos em escala e enviá-los para um cluster HDInsight Spark diretamente do IDE do eclipse.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/30/2017
ms.author: hrasheed
ms.openlocfilehash: 87ac2283e96081297fdd54c1c131aa5b78a5973a
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814272"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Usar Azure Toolkit for Eclipse para criar aplicativos Apache Spark para um cluster HDInsight

Use as ferramentas do HDInsight no kit de ferramentas do Azure para [Eclipse](https://www.eclipse.org/) para desenvolver [Apache Spark](https://spark.apache.org/) aplicativos escritos em [escala](https://www.scala-lang.org/) e enviá-los para um cluster Azure HDInsight Spark, diretamente do IDE do eclipse. Você pode usar o plug-in de ferramentas do HDInsight de algumas maneiras diferentes:

* Para desenvolver e enviar um aplicativo do Spark em um cluster HDInsight Spark.
* Para acessar seus Azure HDInsight Spark recursos de cluster.
* Para desenvolver e executar um aplicativo do Spark em escala local.

> [!IMPORTANT]  
> Você pode usar essa ferramenta para criar e enviar aplicativos somente para um cluster HDInsight Spark no Linux.
> 
> 

## <a name="prerequisites"></a>Pré-requisitos

* Apache Spark cluster no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Oracle Java Development Kit versão 8, que é usado para o tempo de execução do IDE do eclipse. Você pode baixá-lo do [site da Oracle](https://aka.ms/azure-jdks).
* IDE do eclipse. Este artigo usa o eclipse Neon. Você pode instalá-lo no [site do eclipse](https://www.eclipse.org/downloads/).



## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>Instalar as ferramentas do HDInsight no Azure Toolkit for Eclipse e no plug-in escalar

### <a name="install-azure-toolkit-for-eclipse"></a>Instalar Azure Toolkit for Eclipse
As ferramentas do HDInsight para Eclipse estão disponíveis como parte do Azure Toolkit for Eclipse. Para obter instruções de instalação, consulte [instalando Azure Toolkit for Eclipse](https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-installation).

### <a name="install-the-scala-plug-in"></a>Instalar o plug-in escalar
Quando você abre o eclipse, a ferramenta HDInsight detecta automaticamente se você instalou o plug-in escalar. Selecione **OK** para continuar e siga as instruções para instalar o plug-in do eclipse Marketplace.

![Instalação automática do plug-in de escalabilidade](./media/apache-spark-eclipse-tool-plugin/auto-install-scala.png)

O usuário pode [entrar na assinatura do Azure](#sign-in-to-your-azure-subscription)ou [vincular um cluster HDInsight usando o](#link-a-cluster) nome de usuário/senha do Ambari ou a credencial ingressada no domínio para iniciar. 

## <a name="sign-in-to-your-azure-subscription"></a>Inicie sessão na sua subscrição do Azure
1. Inicie o IDE do Eclipse e abra o Azure Explorer. No menu **janela** , selecione **Mostrar exibição**e, em seguida, selecione **outros**. Na caixa de diálogo que é aberta, expanda **Azure**, selecione **Azure Explorer**e, em seguida, selecione **OK**.

   ![Caixa de diálogo Mostrar exibição](./media/apache-spark-eclipse-tool-plugin/view-explorer-1.png)
1. Clique com o botão direito do mouse no nó **Azure** e selecione **entrar**.
1. Na caixa de diálogo **entrar no Azure** , escolha o método de autenticação, selecione **entrar**e insira suas credenciais do Azure.
   
   ![Caixa de diálogo entrar no Azure](./media/apache-spark-eclipse-tool-plugin/view-explorer-2.png)
1. Depois que você estiver conectado, a caixa de diálogo **selecionar assinaturas** listará todas as assinaturas do Azure associadas às credenciais. Clique em **selecionar** para fechar a caixa de diálogo.

   ![Caixa de diálogo Selecionar assinaturas](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
1. Na guia **Azure Explorer** , expanda **hdinsight** para ver os clusters hdinsight Spark em sua assinatura.
   
   ![Clusters do HDInsight Spark no Azure Explorer3](./media/apache-spark-eclipse-tool-plugin/view-explorer-3.png)
1. Você pode expandir ainda mais um nó de nome de cluster para ver os recursos (por exemplo, contas de armazenamento) associados ao cluster.
   
   ![Expandindo um nome de cluster para ver os recursos](./media/apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>Vincular um cluster
Você pode vincular um cluster normal usando o nome de usuário gerenciado Ambari. Da mesma forma, para um cluster HDInsight ingressado no domínio, você pode vincular usando o domínio e o nome `user1@contoso.com`de usuário, como.

1. Selecione **vincular um cluster** do **Azure Explorer**.

   ![menu de contexto do cluster de link](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. Insira o **nome do cluster**, o nome de **usuário** e a **senha**e clique no botão OK para vincular o cluster. Opcionalmente, insira a conta de armazenamento, a chave de armazenamento e, em seguida, selecione contêiner de armazenamento para o Gerenciador de armazenamento funcionar no modo de exibição de árvore à esquerda
   
   ![caixa de diálogo Vincular cluster](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog.png)
   
   > [!NOTE]  
   > Usamos a chave de armazenamento vinculada, o nome de usuário e a senha se o cluster fizer logon na assinatura do Azure e vinculado a um cluster.
   > ![Gerenciador de armazenamento no Eclipse](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)

1. Você poderá ver um cluster vinculado no nó **HDInsight** depois de clicar no botão OK, se as informações de entrada estiverem corretas. Agora você pode enviar um aplicativo para esse cluster vinculado.

   ![cluster vinculado](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

1. Você também pode desvincular um cluster do **Azure Explorer**.
   
   ![cluster desvinculado](./media/apache-spark-intellij-tool-plugin/unlink.png)


## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Configurar um projeto de escala do Spark para um cluster HDInsight Spark

1. No espaço de trabalho do IDE do eclipse, selecione **arquivo**, **novo**e **projeto**. 
1. No assistente de novo projeto, expanda **hdinsight**, selecione **Spark no HDInsight (escala)** e, em seguida, selecione **Avançar**.

   ![Selecionando o projeto Spark no HDInsight (escala)](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
1. O assistente de criação de projeto escalar detecta automaticamente se você instalou o plug-in escalar. Selecione **OK** para continuar baixando o plug-in escalat e siga as instruções para reiniciar o eclipse.

   ![Verificação de escalabilidade](./media/apache-spark-eclipse-tool-plugin/auto-install-scala-2.png)
1. Na caixa de diálogo **novo projeto de escala do HDInsight** , forneça os seguintes valores e, em seguida, selecione **Avançar**:
   * Insira um nome para o projeto.
   * Na área **JRE** , verifique se **usar um ambiente de execução JRE** está definido como **javase-1,7** ou posterior.
   * Na área de **biblioteca do Spark** , você pode escolher usar o **Maven para configurar a opção SDK do Spark** .  Nossa ferramenta integra a versão apropriada para o SDK do Spark e o SDK do Scale. Você também pode escolher a opção **Adicionar SDK do Spark manualmente** , baixar e adicionar o SDK do Spark manualmente.

   ![Caixa de diálogo novo projeto de escala do HDInsight](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
1. Na próxima caixa de diálogo, selecione **concluir**. 
   
  
## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Criar um aplicativo escalar para um cluster HDInsight Spark

1. No IDE do eclipse, no explorador de pacotes, expanda o projeto que você criou anteriormente, clique com o botão direito do mouse em **src**, aponte para **novo**e, em seguida, selecione **outros**.
1. Na caixa de diálogo **selecionar um assistente** , expanda **assistentes escalares**, selecione **objeto escalar**e, em seguida, selecione **Avançar**.
   
   ![Caixa de diálogo Selecionar um assistente](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
1. Na caixa de diálogo **criar novo arquivo** , insira um nome para o objeto e, em seguida, selecione **concluir**.
   
   ![Caixa de diálogo Criar novo arquivo](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
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
1. Execute o aplicativo em um cluster HDInsight Spark:
   
   a. No explorador de pacotes, clique com o botão direito do mouse no nome do projeto e selecione **Enviar aplicativo Spark ao HDInsight**.        
   b. Na caixa de diálogo **envio do Spark** , forneça os seguintes valores e, em seguida, selecione **Enviar**:
      
   * Para **nome do cluster**, selecione o cluster HDInsight Spark no qual você deseja executar o aplicativo.
   * Selecione um artefato do projeto Eclipse ou selecione um do disco rígido. O valor padrão depende do item em que você clica com o botão direito do mouse no Gerenciador de pacotes.
   * Na lista suspensa **nome da classe principal** , o assistente de envio exibe todos os nomes de objeto do seu projeto. Selecione ou insira um que você deseja executar. Se você selecionou um artefato de um disco rígido, deverá inserir o nome da classe principal manualmente. 
   * Como o código do aplicativo neste exemplo não requer argumentos de linha de comando ou JARs ou arquivos de referência, você pode deixar as caixas de texto restantes vazias.
        
     ![Caixa de diálogo envio do Spark](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
1. A guia **envio do Spark** deve começar a exibir o progresso. Você pode parar o aplicativo selecionando o botão vermelho na janela **envio do Spark** . Você também pode exibir os logs para essa execução de aplicativo específica selecionando o ícone de globo (indicado pela caixa azul na imagem).
      
   ![Janela de envio do Spark](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)


## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Acessar e gerenciar clusters do HDInsight Spark usando as ferramentas do HDInsight no Azure Toolkit for Eclipse
Você pode executar várias operações usando as ferramentas do HDInsight, incluindo o acesso à saída do trabalho.

### <a name="access-the-job-view"></a>Acessar a exibição do trabalho
1. No Azure Explorer, expanda **HDInsight**, expanda o nome do cluster Spark e selecione **trabalhos**. 

   ![Nó de exibição de trabalho](./media/apache-spark-eclipse-tool-plugin/job-view-node.png)

1. Selecione o nó **trabalhos** . Se a versão do Java for inferior a **1,8**, as ferramentas do HDInsight lembrarão automaticamente que você instalará o plug-in **E (FX) Clipse** . Selecione **OK** para continuar e, em seguida, siga o assistente para instalá-lo do eclipse Marketplace e reinicie o eclipse. 

   ![Instalar o E (FX) Clipse](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

1. Abra a exibição de trabalho no nó **trabalhos** . No painel direito, a guia **exibição de trabalho do Spark** exibe todos os aplicativos que foram executados no cluster. Selecione o nome do aplicativo para o qual você deseja ver mais detalhes.

   ![Detalhes da aplicação](./media/apache-spark-eclipse-tool-plugin/view-job-logs.png)

   Você pode executar qualquer uma destas ações:

   * Focalize o grafo do trabalho. Ele exibe informações básicas sobre o trabalho em execução. Selecione o grafo de trabalho e você poderá ver os estágios e as informações que cada trabalho gera.

     ![Detalhes da etapa do trabalho](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * Selecione a guia **log** para exibir os logs usados com frequência, incluindo o **Driver stderr**, o **Driver stdout**e as **informações de diretório**.

     ![Detalhes do log](./media/apache-spark-eclipse-tool-plugin/Job-log-info.png)

   * Abra a interface do usuário do histórico do Spark e a interface do usuário do Apache Hadoop YARN (no nível do aplicativo) selecionando os hiperlinks na parte superior da janela.

### <a name="access-the-storage-container-for-the-cluster"></a>Acessar o contêiner de armazenamento para o cluster
1. No Azure Explorer, expanda o nó raiz do **hdinsight** para ver uma lista de clusters do hdinsight Spark que estão disponíveis.
1. Expanda o nome do cluster para ver a conta de armazenamento e o contêiner de armazenamento padrão para o cluster.
   
   ![Conta de armazenamento e contêiner de armazenamento padrão](./media/apache-spark-eclipse-tool-plugin/view-explorer-5.png)
1. Selecione o nome do contêiner de armazenamento associado ao cluster. No painel direito, clique duas vezes na pasta **hvacout** . Abra um dos arquivos de **parte** para ver a saída do aplicativo.

### <a name="access-the-spark-history-server"></a>Acessar o servidor de histórico do Spark
1. No Azure Explorer, clique com o botão direito do mouse no nome do cluster Spark e selecione **abrir interface do usuário de histórico do Spark**. Quando for solicitado, insira as credenciais de administrador para o cluster. Você especificou isso ao provisionar o cluster.
1. No painel do servidor de histórico do Spark, use o nome do aplicativo para procurar o aplicativo que você acabou de executar. No código anterior, você define o nome do aplicativo usando `val conf = new SparkConf().setAppName("MyClusterApp")`. Portanto, o nome do aplicativo Spark foi **MyClusterApp**.

### <a name="start-the-apache-ambari-portal"></a>Iniciar o portal do Apache Ambari
1. No Azure Explorer, clique com o botão direito do mouse no nome do cluster Spark e selecione **abrir portal de gerenciamento do cluster (Ambari)** . 
1. Quando for solicitado, insira as credenciais de administrador para o cluster. Você especificou isso ao provisionar o cluster.

### <a name="manage-azure-subscriptions"></a>Gerenciar assinaturas do Azure
Por padrão, a ferramenta HDInsight no Azure Toolkit for Eclipse lista os clusters Spark de todas as suas assinaturas do Azure. Se necessário, você pode especificar as assinaturas para as quais deseja acessar o cluster. 

1. No Azure Explorer, clique com o botão direito do mouse no nó raiz **do Azure** e selecione **gerenciar assinaturas**. 
1. Na caixa de diálogo, desmarque as caixas de seleção da assinatura que você não deseja acessar e, em seguida, selecione **fechar**. Você também pode selecionar **sair** se quiser sair de sua assinatura do Azure.

## <a name="run-a-spark-scala-application-locally"></a>Executar um aplicativo do Spark escalar localmente
Você pode usar as ferramentas do HDInsight no Azure Toolkit for Eclipse para executar aplicativos do Spark escalar localmente em sua estação de trabalho. Normalmente, esses aplicativos não precisam acessar recursos de cluster, como um contêiner de armazenamento, e você pode executá-los e testá-los localmente.

### <a name="prerequisite"></a>Pré-requisito
Enquanto estiver executando o aplicativo do Spark escala local em um computador Windows, você poderá receber uma exceção, conforme explicado no [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). Essa exceção ocorre porque o **WinUtils. exe** está ausente no Windows. 

Para resolver esse erro, você precisa [baixar o executável](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) em um local como **C:\WinUtils\bin**e, em seguida, adicionar a variável de ambiente **HADOOP_HOME** e definir o valor da variável como **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Executar um aplicativo local do Spark escala
1. Inicie o Eclipse e crie um projeto. Na caixa de diálogo **novo projeto** , faça as seguintes escolhas e, em seguida, selecione **Avançar**.
   
   * No painel da esquerda, selecione **HDInsight**.
   * No painel direito, selecione **exemplo de execução local do Spark no HDInsight (escala)** .

   ![Caixa de diálogo Novo Projeto](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
   
1. Para fornecer os detalhes do projeto, siga as etapas 3 a 6 da seção anterior [configurar um projeto do Spark escalar para um cluster HDInsight Spark](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

1. O modelo adiciona um código de exemplo (**LogQuery**) na pasta **src** que você pode executar localmente no seu computador.
   
   ![Local do LogQuery](./media/apache-spark-eclipse-tool-plugin/local-app.png)
   
1. Clique com o botão direito do mouse no aplicativo **LogQuery** , aponte para **Executar como**e selecione **1 aplicativo escala**. Saída como esta aparece na guia **console** :
   
   ![Resultado da execução local do aplicativo Spark](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="reader-only-role"></a>Função somente de leitor
Quando os usuários enviam o trabalho para um cluster com permissão de função somente de leitor, as credenciais de Ambari são necessárias.

### <a name="link-cluster-from-context-menu"></a>Vincular cluster no menu de contexto

1. Entre com a conta de função somente de leitor.
       
2. No **Azure Explorer**, expanda **hdinsight** para exibir os clusters hdinsight que estão em sua assinatura. Os clusters marcados como **"função: leitor"** têm apenas a permissão de função de leitor.

    ![Clusters HDInsight Spark no leitor de função do Azure Explorer](./media/apache-spark-eclipse-tool-plugin/view-explorer-6.png)

3. Clique com o botão direito do mouse no cluster com permissão de função somente leitura. Selecione **vincular este cluster** no menu de contexto para vincular o cluster. Insira o nome de usuário e a senha do Ambari.

    ![Clusters do HDInsight Spark no link do Azure Explorer](./media/apache-spark-eclipse-tool-plugin/view-explorer-7.png)

4. Se o cluster for vinculado com êxito, o HDInsight será atualizado.
   O estágio do cluster será vinculado.
  
    ![Clusters do HDInsight Spark no Azure Explorer vinculado](./media/apache-spark-eclipse-tool-plugin/view-explorer-8.png)



### <a name="link-cluster-by-expanding-jobs-node"></a>Vincular cluster expandindo o nó trabalhos

1. Clique no nó **trabalhos** , a janela **acesso negado ao trabalho do cluster** é exibida.
   
2. Clique em **vincular este cluster** para vincular o cluster.
   
    ![Clusters do HDInsight Spark no Azure Explorer9](./media/apache-spark-eclipse-tool-plugin/view-explorer-9.png)

### <a name="link-cluster-from-spark-submission-window"></a>Vincular cluster da janela de envio do Spark

1. Crie um projeto do HDInsight.

2. Clique com o botão direito do mouse no pacote. Em seguida, selecione **Enviar aplicativo Spark ao HDInsight**.
   
   ![Clusters do HDInsight Spark no Azure Explorer enviar](./media/apache-spark-eclipse-tool-plugin/view-explorer-11.png)

3. Selecione um cluster que tenha permissão de função somente de leitor para o **nome do cluster**. Mensagem de aviso mostrada. Você pode clicar em **vincular este cluster** para vincular o cluster.
   
   ![Clusters do HDInsight Spark no Azure Explorer link isso](./media/apache-spark-eclipse-tool-plugin/view-explorer-15.png)
   
### <a name="view-storage-accounts"></a>Exibir contas de armazenamento

* Para clusters com permissão de função somente de leitor, clique em nó **contas de armazenamento** , janela **acesso de armazenamento negado** aparece. 
     
   ![Clusters do HDInsight Spark no armazenamento do Azure Explorer](./media/apache-spark-eclipse-tool-plugin/view-explorer-13.png)

   ![Clusters HDInsight Spark no Azure Explorer negados](./media/apache-spark-eclipse-tool-plugin/view-explorer-12.png)

* Para clusters vinculados, clique no nó **contas de armazenamento** , a janela **acesso de armazenamento negado** é exibida. 
     
   ![Clusters do HDInsight Spark no Azure Explorer denied2](./media/apache-spark-eclipse-tool-plugin/view-explorer-14.png)

## <a name="known-problems"></a>Problemas conhecidos
Ao vincular um cluster, eu o sugiro a fornecer credenciais de armazenamento.

![Entrada interativa](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

Há dois modos para enviar os trabalhos. Se a credencial de armazenamento for fornecida, o modo de lote será usado para enviar o trabalho. Caso contrário, o modo interativo será usado. Se o cluster estiver ocupado, você poderá obter o erro abaixo.

![erro de Get do Eclipse quando o cluster está ocupado](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png "erro de Get do Eclipse quando o cluster está ocupado")

![erro de Get do Eclipse quando o cluster está ocupado yarn](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png "erro de Get do Eclipse quando o cluster está ocupado yarn")

## <a name="seealso"></a>Ver também
* [Sobre Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: Executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para analisar a temperatura de edifício usando dados de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Criando e executando aplicativos
* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar Azure Toolkit for IntelliJ para criar e enviar aplicativos do Spark escalares](apache-spark-intellij-tool-plugin.md)
* [Usar Azure Toolkit for IntelliJ para depurar Apache Spark aplicativos remotamente por meio de VPN](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar Azure Toolkit for IntelliJ para depurar Apache Spark aplicativos remotamente por meio de SSH](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Usar notebooks do Apache Zeppelin com um cluster Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o Jupyter Notebook no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Gerenciando recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)

