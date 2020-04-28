---
title: Trabalho de Debug Spark com IntelliJ Azure Toolkit (pré-visualização) - HDInsight
description: Orientação utilizando ferramentas HDInsight no Kit de Ferramentas Azure para intelliJ para depurar aplicações
keywords: depuração remotamente intellij, depuração remota intellij, ssh, intellij, hdinsight, depuração intellij, depuração
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 1a0a6cf5a26854539dc4bbb0ae0254bbf08dad1f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73494598"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Falha no trabalho de faísca com Kit de Ferramentas Azure para IntelliJ (pré-visualização)

Este artigo fornece orientações passo a passo sobre como usar ferramentas HDInsight no [Kit de Ferramentas Azure para intelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) executar aplicações **Spark Failure Debug.**

## <a name="prerequisites"></a>Pré-requisitos

* [Kit de Desenvolvimento Oracle Java.](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) Este tutorial utiliza a versão Java 8.0.202.
  
* IntelliJ IDEA. Este artigo utiliza [intelliJ IDEA Community ver. 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows).
  
* Kit de ferramentas Azure para IntelliJ. Consulte [a instalação do Kit de Ferramentas Azure para IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

* Ligue-se ao seu cluster HDInsight. Consulte [a Ligação ao seu cluster HDInsight](apache-spark-intellij-tool-plugin.md).

* Explorador de Armazenamento do Microsoft Azure. Consulte [o Download Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Criar um projeto com modelo de depuração

Crie um projeto spark2.3.2 para continuar a depurar falhas, tome o ficheiro de amostra de depuração de tarefas de falha neste documento.

1. Abra o IntelliJ IDEA. Abra a janela do **Novo Projeto.**

   a. Selecione **Azure Spark/HDInsight** a partir do painel esquerdo.

   b. Selecione **Projeto Spark com falha de amostra de depuração (Pré-visualização)(Scala)** da janela principal.

     ![Intellij Criar um projeto de depuração](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. Selecione **Seguinte**.

2. Na janela **New Project,** faça os seguintes passos:

   ![Intellij New Project seleciona versão Spark](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. Insira o nome do projeto e a localização do projeto.

   b. Na lista de drop-down do **Project SDK,** selecione **Java 1.8** para o cluster **Spark 2.3.2.**

   c. Na lista de abandono da **Versão Spark,** selecione **Spark 2.3.2 (Scala 2.11.8)**.

   d. Selecione **Concluir**.

3. Selecione src**main** > **scala** **src** > para abrir o seu código no projeto. Este exemplo utiliza o guião **AgeMean_Div().**

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Executar uma aplicação Spark Scala/Java num cluster HDInsight

Crie uma aplicação Scala/Java de faísca, em seguida, executar a aplicação num cluster Spark fazendo os seguintes passos:

1. Clique em **adicionar configuração** para abrir a janela **de configurações run/debug.**

   ![HDI Intellij Adicionar configuração](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png)

2. Na caixa de diálogo **'Configurações Executar/Depuração',** selecione o sinal de mais ().**+** Em seguida, selecione a Apache Spark na opção **HDInsight.**

   ![Intellij Adicionar nova configuração](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Mude para **Executar remotamente no** separador Cluster. Introduza informações para **Nome,** **cluster de faíscas**e **nome principal da classe**. As nossas ferramentas suportam o depurado com **executores.** Os **numExectors,** o valor padrão é 5, e é melhor não se fixar em 3. Para reduzir o tempo de execução, pode adicionar **spark.yarn.maxAppAttempts** em **configurações** de trabalho e definir o valor para 1. Clique no botão **OK** para salvar a configuração.

   ![Configurações de depuração intellij Run novas](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. A configuração é agora guardada com o nome que forneceu. Para visualizar os detalhes da configuração, selecione o nome de configuração. Para efazer alterações, selecione **Configurações de Edição**.

5. Depois de completar as definições de configurações, pode executar o projeto contra o cluster remoto.

   ![Botão de execução remoto de spark remote spark remote de intellij](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. Pode verificar o ID da aplicação a partir da janela de saída.

   ![Resultado remoto de spark de inbug](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)

## <a name="download-failed-job-profile"></a>Baixar perfil de trabalho falhado

Se a submissão de emprego falhar, poderá transferir o perfil de trabalho falhado para a máquina local para posterior depuração.

1. Open **Microsoft Azure Storage Explorer**, localize a conta HDInsight do cluster para o trabalho falhado, descarregue os recursos de emprego falhados a partir do local correspondente: **\hdp\spark2-events\\.spark-failures\\\<application ID>** para uma pasta local. A janela de **atividades** mostrará o progresso do download.

   ![Falha no download do Azure Storage Explorer](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![Sucesso de download do Azure Storage Explorer](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Configure o ambiente de depuração local e depuração sobre a falha

1. Abra o projeto original ou crie um novo projeto e associe-o ao código fonte original. Apenas a versão spark2.3.2 é suportada para depuração de falhas atualmente.

1. No IntelliJ IDEA, crie um ficheiro config **Debug Spark Failure,** selecione o ficheiro FTD dos recursos de trabalho falhados anteriormente descarregados para o campo de **localização spark Job Failure Context.**

   ![configuração de falha de creta](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

1. Clique no botão de execução local na barra de ferramentas, o erro será exibido na janela Executar.

   ![configuração de falha de execução1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![configuração de falha de execução2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

1. Detete o ponto de rutura como o registo indica e, em seguida, clique no botão de depuração local para fazer depuração local, tal como os seus projetos normais scala /Java em IntelliJ.

1. Depois de depurar, se o projeto terminar com sucesso, poderá reenviar o trabalho falhado à sua faísca no cluster HDInsight.

## <a name="next-steps"></a><a name="seealso"></a>Passos seguintes

* [Visão geral: Aplicações Debug Apache Spark](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>Demonstração

* Criar projeto Scala (vídeo): [Criar aplicações Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depuração remota (vídeo): [Utilize o Kit de Ferramentas Azure para o IntelliJ para depurar aplicações apache Spark remotamente num cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Cenários

* [Apache Spark com BI: Faça análise de dados interativos utilizando spark in HDInsight com ferramentas BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Use Spark no HDInsight para analisar a temperatura do edifício usando dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Use Spark no HDInsight para prever resultados da inspeção alimentar](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando Apache Spark em HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações

* [Criar uma aplicação autónoma com o Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Utilize o Kit de Ferramentas Azure para o IntelliJ para criar aplicações Apache Spark para um cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Utilize o Kit de Ferramentas Azure para o IntelliJ para depurar aplicações apache Spark remotamente através da VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilize ferramentas HDInsight para IntelliJ com Caixa de Areia Hortonworks](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utilize ferramentas HDInsight no Kit de Ferramentas Azure para eclipse para criar aplicações Apache Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Use os cadernos Apache Zeppelin com um cluster Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o portátil Jupyter no cluster Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos

* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
