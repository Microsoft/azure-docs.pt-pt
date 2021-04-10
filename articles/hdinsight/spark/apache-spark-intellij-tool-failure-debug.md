---
title: Debug Spark job with IntelliJ Azure Toolkit (pré-visualização) - HDInsight
description: Orientação utilizando ferramentas HDInsight em Azure Toolkit para IntelliJ para depurar aplicações
keywords: depurar remotamente intellij, depurando remotamente intellij, ssh, intellij, hdinsight, debug intellij, depurando
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 5422fe324ca1f3ef5bb2d14fb04664c8fb03fe3c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866238"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Falha no trabalho de depuração com Azure Toolkit para IntelliJ (pré-visualização)

Este artigo fornece orientações passo a passo sobre como usar ferramentas HDInsight em [Azure Toolkit para IntelliJ](/azure/developer/java/toolkit-for-intellij) executar aplicações **Depurg Depuração de Falha de Faísca.**

## <a name="prerequisites"></a>Pré-requisitos

* [Kit de desenvolvimento Oracle Java.](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) Este tutorial utiliza a versão Java 8.0.202.
  
* Ideia intellij. Este artigo utiliza [IntelliJ IDEA Community ver. 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows).
  
* Azure Toolkit para IntelliJ. Consulte a instalação do conjunto de [ferramentas Azure para IntelliJ](/azure/developer/java/toolkit-for-intellij/installation).

* Ligue-se ao seu cluster HDInsight. Consulte [Connect to your HDInsight cluster](apache-spark-intellij-tool-plugin.md).

* Microsoft Azure Storage Explorer. Consulte [o Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Crie um projeto com modelo de depuragem

Crie um projeto spark2.3.2 para continuar a depurar falhas, tomar a tarefa de depurar ficheiros de amostras de falha neste documento.

1. Abra o IntelliJ IDEA. Abra a janela **do Novo Projeto.**

   a. Selecione **Azure Spark/HDInsight** a partir do painel esquerdo.

   b. Selecione **Spark Project com amostra de depuramento de tarefas de falha (Preview)(Scala)** da janela principal.

     :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png" alt-text="Intellij Criar um projeto de depurg" border="true":::

   c. Selecione **Seguinte**.

2. Na janela New **Project,** faça os seguintes passos:

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png" alt-text="Intellij New Project seleciona versão Spark" border="true":::

   a. Insira o nome do projeto e a localização do projeto.

   b. Na lista de drop-down do **Project SDK,** selecione **Java 1.8** para o cluster **Spark 2.3.2.**

   c. Na lista de down-down **da Versão Spark,** selecione **Spark 2.3.2 (Scala 2.11.8)**.

   d. Selecione **Concluir**.

3. Selecione **src**  >  **main**  >  **scala** para abrir o seu código no projeto. Este exemplo utiliza o **AgeMean_Div()** script.

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Executar uma aplicação Spark Scala/Java num cluster HDInsight

Crie uma aplicação Scala/Java de faísca e, em seguida, execute a aplicação num cluster Spark fazendo os seguintes passos:

1. Clique **em Adicionar Configuração** para abrir a janela **configurações run/Debug.**

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png" alt-text="Configuração de adicionar HDI Intellij" border="true":::

2. Na caixa de diálogo **de configurações Run/Debug,** selecione o sinal mais **+** (). Em seguida, selecione a faísca Apache na opção **HDInsight.**

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png" alt-text="Intellij Adicionar nova configuração" border="true":::

3. Mude para **executar remotamente no separador Cluster.** Introduza informações para **Nome,** **agrupamento de faíscas** e **nome da classe principal.** As nossas ferramentas suportam o depurar com **os Executores.** Os **numExectors,** o valor padrão é 5, e é melhor não definir mais do que 3. Para reduzir o tempo de funcionação, pode adicionar **spark.yarn.maxAppAttempts** em **Configurações de trabalho** e definir o valor para 1. Clique no botão **OK** para guardar a configuração.

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png" alt-text="Intellij Run depura configurações novas" border="true":::

4. A configuração é agora guardada com o nome que forneceu. Para visualizar os detalhes da configuração, selecione o nome de configuração. Para escoar as **alterações, selecione Configurações de Edição**.

5. Depois de completar as definições, pode executar o projeto contra o cluster remoto.

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png" alt-text="Botão de funcionação remota de intellij Debug Debug Remote Spark Job" border="true":::

6. Pode verificar o ID da aplicação a partir da janela de saída.

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png" alt-text="Resultado remoto de execução de Intellij Debug Debug Remote Spark Job" border="true":::

## <a name="download-failed-job-profile"></a>Baixar perfil de trabalho falhado

Se a submissão de emprego falhar, poderá transferir o perfil de trabalho falhado para a máquina local para posterior depuragem.

1. Abra o **Microsoft Azure Storage Explorer**, localize a conta HDInsight do cluster para o trabalho falhado, descarregue os recursos de trabalho falhados a partir do local correspondente: **\hdp\spark2-events \\ .spark-failures \\ \<application ID>** para uma pasta local. A janela **de atividades** mostrará o progresso do download.

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png" alt-text="Falha no download do Azure Storage Explorer" border="true":::

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png" alt-text="Azure Storage Explorer descarrega o sucesso" border="true":::

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Configure o ambiente local de depuração local e depurar sobre o fracasso

1. Abra o projeto original ou crie um novo projeto e associe-o ao código fonte original. Apenas a versão spark2.3.2 é suportada por falhas na depuragem atualmente.

1. IntelliJ IDEA, crie um ficheiro **Spark Failure Debug** config, selecione o ficheiro FTD a partir dos recursos de trabalho falhados anteriormente descarregados para o campo **de localização do Contexto de Falha** de Emprego de Spark.

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png" alt-text="configuração de falha de creta" border="true":::

1. Clique no botão de execução local na barra de ferramentas, o erro será exibido na janela 'Executar'.

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png" alt-text="configuração de falha de execução1" border="true":::

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png" alt-text="configuração de falha de execução2" border="true":::

1. Desaponte o ponto de rutura como o registo indica e, em seguida, clique no botão de depuração local para fazer depuração local tal como os seus projetos normais de Scala/Java em IntelliJ.

1. Depois de depurar, se o projeto estiver concluído com sucesso, poderá reenviar o trabalho falhado à sua faísca no cluster HDInsight.

## <a name="next-steps"></a><a name="seealso"></a>Passos seguintes

* [Visão geral: Aplicações Debug Apache Spark](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>Demonstração

* Criar projeto Scala (vídeo): [Criar Aplicações Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depurador remoto (vídeo): [Use o Azure Toolkit para IntelliJ para depurar as aplicações Apache Spark remotamente num cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: Faça análise de dados interativas utilizando Spark in HDInsight com ferramentas BI](apache-spark-use-bi-tools.md)
* [Faísca Apache com Machine Learning: Use faísca em HDInsight para analisar a temperatura do edifício usando dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Faísca Apache com Machine Learning: Use faísca em HDInsight para prever os resultados da inspeção alimentar](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de registo do site usando Apache Spark em HDInsight](./apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações

* [Criar uma aplicação autónoma com o Scala](./apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um aglomerado Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Use o Azure Toolkit para IntelliJ para criar aplicações Apache Spark para um cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Utilize o Azure Toolkit para o IntelliJ para depurar as aplicações apache spark remotamente através da VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use ferramentas HDInsight para IntelliJ com a Caixa de Areia Hortonworks](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Utilize ferramentas HDInsight em Azure Toolkit para eclipse para criar aplicações Apache Spark](./apache-spark-eclipse-tool-plugin.md)
* [Use cadernos Apache Zeppelin com um cluster Apache Spark em HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para Jupyter Notebook no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Use pacotes externos com cadernos Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)