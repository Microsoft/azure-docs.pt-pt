---
title: 'Falha na depuração do trabalho Spark com Azure Toolkit for IntelliJ (versão prévia) '
description: Diretrizes passo a passo sobre como usar as ferramentas do HDInsight no Azure Toolkit for IntelliJ para depurar aplicativos remotamente em clusters HDInsight por meio de SSH
keywords: depurar remotamente IntelliJ, depuração remota IntelliJ, SSH, IntelliJ, hdinsight, depurar IntelliJ, depuração
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: aff9f0f70377ebc6e741618b22ff82bc06251521
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295916"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Falha na depuração do trabalho Spark com Azure Toolkit for IntelliJ (versão prévia)

Este artigo fornece orientações passo a passo sobre como usar as ferramentas do HDInsight no [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) para executar aplicativos de **depuração de falha do Spark** . 

## <a name="prerequisites"></a>Pré-requisitos
* [Kit de desenvolvimento Oracle Java](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). Este tutorial usa a versão do Java 8.0.202.
  
* IntelliJ ideia. Este artigo usa [a IntelliJ IDEA Community ver. 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows).
  
* Azure Toolkit for IntelliJ. Consulte [instalando o Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

* Conecte-se ao cluster HDInsight. Consulte [conectar-se ao seu cluster HDInsight](apache-spark-intellij-tool-plugin.md).

* Gerenciador de Armazenamento do Microsoft Azure. Consulte [baixar Gerenciador de armazenamento do Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Criar um projeto com o modelo de depuração 

Criar um projeto do Spark 2.3.2 para continuar a depuração de falha, escolher o arquivo de exemplo de depuração de tarefa de falha neste documento.

1. Abra o IntelliJ IDEA. Abra a janela **novo projeto** .

   a. Selecione **Azure Spark/HDInsight** no painel esquerdo. 

   b. Selecione **projeto do Spark com falha de depuração da tarefa de falhas (visualização) (escala)** na janela principal.

     ![Criar um projeto de depuração](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. Selecione **Seguinte**.     
 
2. Na janela **novo projeto** , execute as seguintes etapas:

   ![Selecione o SDK do Spark](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. Insira um nome de projeto e o local do projeto.

   b. Na lista suspensa **SDK do projeto** , selecione **Java 1,8** para o cluster **Spark 2.3.2** .

   c. Na lista suspensa **versão do Spark** , selecione **Spark 2.3.2 (escala 2.11.8)** .

   d. Selecione **Concluir**.

3. Selecione **src** >  **Main**escalarparaabrirseucódigonoprojeto. >  Este exemplo usa o script **AgeMean_Div ()** .

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Executar um aplicativo de escala/Java Spark em um cluster HDInsight

Crie um aplicativo do Spark escala/Java e execute o aplicativo em um cluster Spark executando as seguintes etapas:

1. Clique em **Adicionar configuração** para abrir a janela **executar/depurar configurações** .

   ![Editar configurações](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png) 

2. Na caixa de diálogo **configurações de execução/depuração** , selecione o sinal de **+** adição (). Em seguida, selecione a opção **Apache Spark no HDInsight** .

   ![Adicionar nova configuração](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Alterne para **execução remota na guia cluster** . Insira informações para **nome**, **cluster do Spark**e **nome da classe principal**. Nossas ferramentas dão suporte à depuração com executores. O **numExectors**, o valor padrão é 5, e você melhor não definiu mais que 3. Para reduzir o tempo de execução, você pode adicionar **Spark. yarn. maxAppAttempts** às **configurações de trabalho** e definir o valor como 1. Clique no botão **OK** para salvar a configuração.

   ![Executar configurações de depuração](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. A configuração agora é salva com o nome fornecido. Para exibir os detalhes de configuração, selecione o nome da configuração. Para fazer alterações, selecione **Editar configurações**. 

5. Depois de concluir as configurações, você pode executar o projeto no cluster remoto.
   
   ![Botão execução remota](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. Você pode verificar a ID do aplicativo na janela saída.
   
   ![Botão execução remota](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)   

## <a name="download-failed-job-profile"></a>Baixar perfil de trabalho com falha

Se o envio do trabalho falhar, você poderá baixar o perfil de trabalho com falha no computador local para uma depuração adicional.

1. Abra **Gerenciador de armazenamento do Microsoft Azure**, localize a conta do HDInsight do cluster para o trabalho com falha, baixe os recursos de trabalho com falha do local correspondente: **\hdp\spark2-Events\\. Spark\\ -Failuresa\<ID do aplicativo >** a uma pasta local. A janela **atividades** mostrará o progresso do download.

   ![baixar arquivo de falha](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![baixar arquivo de falha](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)   

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Configurar o ambiente de depuração local e depurar em caso de falha

1. Abra o projeto original ou crie um novo projeto e associe-o ao código-fonte original. Somente a versão do Spark 2.3.2 tem suporte para a depuração de falha no momento.

2. Em IntelliJ IDEA, crie um arquivo de configuração de **depuração de falha do Spark** , selecione o arquivo FTD dos recursos de trabalho com falha baixados anteriormente para o campo local do contexto de falha do trabalho do **Spark** .
   
   ![Botão execução remota](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

4. Clique no botão Executar local na barra de ferramentas, o erro será exibido na janela Executar.
   
   ![Botão execução remota](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![Botão execução remota](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

5. Defina ponto de interrupção conforme o log indica, em seguida, clique no botão de depuração local para fazer a depuração local da mesma forma que seus projetos escalares/Java normais no IntelliJ.

5. Após a depuração, se o projeto for concluído com êxito, você poderá reenviar o trabalho com falha para o Spark no cluster HDInsight.

## <a name="seealso"></a>Passos seguintes
* [Sobre Depurar aplicativos Apache Spark](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>Demonstração
* Criar projeto escalar (vídeo): [Criar Apache Spark aplicativos escalares](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depuração remota (vídeo): [Usar Azure Toolkit for IntelliJ para depurar aplicativos Apache Spark remotamente em um cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: Fazer análises de dados interativas usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para analisar a temperatura de edifício usando dados de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para prever os resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando Apache Spark no HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar Azure Toolkit for IntelliJ para criar aplicativos Apache Spark para um cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Usar Azure Toolkit for IntelliJ para depurar Apache Spark aplicativos remotamente por meio de VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar as ferramentas do HDInsight para IntelliJ com a área restrita do Hortonworks](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Usar as ferramentas do HDInsight no Azure Toolkit for Eclipse para criar aplicativos Apache Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Usar notebooks do Apache Zeppelin com um cluster Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o Jupyter Notebook no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
