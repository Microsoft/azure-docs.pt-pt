---
title: 'Azure Toolkit para IntelliJ: Depurar aplicações do Spark remotamente através de SSH '
description: Orientações passo a passo sobre como utilizar ferramentas do HDInsight no Azure Toolkit para IntelliJ para depurar aplicações remotamente nos HDInsight clusters através de SSH
keywords: depurar remotamente intellij, ssh, intellij, depuração remota intellij, hdinsight, depurar intellij, depuração
services: hdinsight
ms.service: hdinsight
author: hrasheed
ms.author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/25/2017
ms.openlocfilehash: 87a774fad6b7e45f233102b7fb026aa9663dceb0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58005920"
---
# <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Depurar aplicações do Apache Spark localmente ou remotamente num cluster do HDInsight com o Azure Toolkit para IntelliJ através de SSH

Este artigo fornece orientações passo a passo sobre como utilizar as ferramentas do HDInsight [Azure Toolkit para IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) para depurar aplicações remotamente num cluster do HDInsight. Para depurar o projeto, também pode ver o [aplicações do Spark do HDInsight de depurar com o Azure Toolkit para IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) vídeo.

**Pré-requisitos**
* **Ferramentas do HDInsight no Azure Toolkit para IntelliJ**. Essa ferramenta faz parte do Azure Toolkit para IntelliJ. Para obter mais informações, consulte [instalar o Azure Toolkit para IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation). E **Azure Toolkit para IntelliJ**. Utilize este kit de ferramentas para criar aplicativos do Apache Spark para um cluster do HDInsight. Para obter mais informações, siga as instruções em [utilizar o Azure Toolkit para IntelliJ criar aplicativos de Apache Spark para um cluster do HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).

* **SSH do HDInsight service com a gestão de nome de utilizador e palavra-passe**. Para obter mais informações, consulte [ligar ao HDInsight (Apache Hadoop) através de SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) e [utilizar o SSH Tunneling to access Ambari túnel web UI, JobHistory, NameNode, Apache Oozie e outras interfaces do usuário web](https://docs.microsoft.com/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>Aprenderá a efetuar local, execute e depuração
### <a name="scenario-1-create-a-spark-scala-application"></a>Cenário 1: Criar uma aplicação Spark Scala 

1. Inicie o IntelliJ IDEA e, em seguida, crie um projeto. Na caixa de diálogo **Novo Projeto**, faça o seguinte:

   a. Selecione **do Azure Spark/HDInsight**. 

   b. Selecione um modelo Java ou Scala, com base na sua preferência. Selecione entre as seguintes opções:

   - **Projeto de Spark (Java)**

   - **Projeto de Spark (Scala)**

   - **Projeto de Spark com exemplos (Scala)**

   - **Projeto de Spark com tarefas de falha (pré-visualização) (Scala) de exemplos de depuração**

     Este exemplo utiliza um **Project Spark com exemplos (Scala)** modelo.

   c. Na lista **Ferramenta de compilação**, selecione uma das seguintes opções, de acordo com as suas necessidades:

   - **Maven**, para o suporte de assistente de criação do projeto Scala

   - **SBT**, para gerir as dependências e a complicação do projeto Scala 

     ![Criar um projeto de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. Selecione **Seguinte**.     
 
1. Nos próximos **novo projeto** janela, efetue o seguinte procedimento:

   ![Selecione o SDK do Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Introduza um nome de projeto e a localização do projeto.

   b. Na **SDK do Project** na lista pendente, selecione **Java 1.8** para **2.x do Spark** de cluster ou selecione **Java 1.7** para **1.x do Spark**  cluster.

   c. Na **versão do Spark** na lista pendente, o Assistente de criação de projeto Scala integra-se a versão correta para o SDK do Spark e do Scala. Se a versão de cluster do spark é anterior ao 2.0, selecione **Spark 1.x**. Caso contrário, selecione **2.x do Spark.** Este exemplo utiliza o **Spark 2.0.2 (Scala 2.11.8)**.

   d. Selecione **Concluir**.

1. Selecione **src** > **principal** > **scala** para abrir o seu código no projeto. Este exemplo utiliza a **SparkCore_wasbloTest** script.

### <a name="prerequisite-for-windows"></a>Pré-requisitos para Windows
Enquanto estiver a executar a aplicação Spark Scala local num computador Windows, pode receber uma exceção, conforme explicado [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356). A exceção ocorrer porque WinUtils.exe está em falta no Windows. 

Para resolver este erro, [transferir executável](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) para uma localização, como **C:\WinUtils\bin**. Em seguida, adicione a variável de ambiente **HADOOP_HOME**e defina o valor da variável a ser **C:\WinUtils**.

### <a name="scenario-2-perform-local-run"></a>Cenário 2: Realizar a execução local
1. Abra o **SparkCore_wasbloTest** do script, o editor de scripts com o botão direito e, em seguida, selecione a opção **executar 'XXX [tarefa do Spark]'** para efetuar a execução local.
1. Executar uma vez local foi concluída, pode ver o ficheiro de saída, guarde o Explorador de projeto atual **dados** > **__predefinição__**.

    ![Resultado de execução local](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-result.png)
1. Nossas ferramentas tem definido a predefinição de depurar local Configuração de execução automaticamente quando executar o local de execução e local. Abra a configuração **[Spark no HDInsight] XXX** no canto superior direito, pode ver a **[Spark no HDInsight] XXX** já criado sob **Apache Spark no HDInsight**. Mude para o **executar localmente** separador.

    ![Configuração de execução local](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)
    - [Variáveis de ambiente](#prerequisite-for-windows): Se já configurou a variável de ambiente de sistema **HADOOP_HOME** ao **C:\WinUtils**, pode automaticamente detetar que não há necessidade de adicionar manualmente.
    - [Localização de WinUtils.exe](#prerequisite-for-windows): Se não tiver definido a variável de ambiente de sistema, pode encontrar a localização, clicando em seu botão.
    - Escolha uma das duas opções, e que não são necessários no MacOS e Linux.
1. Também pode definir a configuração manualmente antes de efetuar a depuração local de execução e local. Na captura de ecrã anterior, selecione o sinal de adição (**+**). Em seguida, selecione o **Apache Spark no HDInsight** opção. Introduza informações em **Name**, **nome da classe principal** para salvar, em seguida, clique no botão de execução local.

### <a name="scenario-3-perform-local-debugging"></a>Cenário 3: Efetuar a depuração local
1. Abra o **SparkCore_wasbloTest** do script, pontos de interrupção do conjunto.
1. O editor de scripts com o botão direito e, em seguida, selecione a opção **depurar ' [Spark no HDInsight] XXX "** para efetuar a depuração local.   



## <a name="learn-how-to-perform-remote-run-and-debugging"></a>Aprenderá a efetuar remotos, executar e depurar
### <a name="scenario-1-perform-remote-run"></a>Cenário 1: Realizar a execução remota

1. Para aceder a **editar configurações** menu, selecione o ícone no canto superior direito. Neste menu, pode criar ou editar as configurações para depuração remota.

   ![Editar configurações](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

1. Na **configurações de execução/depuração** caixa de diálogo caixa, selecione o sinal de adição (**+**). Em seguida, selecione o **Apache Spark no HDInsight** opção.

   ![Adicionar a nova configuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
1. Mude para o **executados remotamente num Cluster** separador. Introduza informações em **Name**, **cluster do Spark**, e **nome da classe principal**. Em seguida, clique em **configuração avançada (depuração remota)**. Suportam de nossas ferramentas de depuração com **executores**. O **numExectors**, o valor predefinido é 5. Seria melhor não definido como superior a 3.

   ![Executar configurações de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. Na **(depuração remota) de configuração avançada** parte, selecione **depuração remota do Spark ativar**. Introduza o nome de utilizador do SSH e, em seguida, introduza uma palavra-passe ou utilizar um ficheiro de chave privada. Se pretender efetuar a depuração remota, terá de configurá-lo. Não é necessário configurá-lo se pretender utilizar remoto a executar.

   ![Ativar a depuração remota do Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. Agora, a configuração é guardada com o nome que indicou. Para ver os detalhes de configuração, selecione o nome da configuração. Para fazer alterações, selecione **editar configurações**. 

1. Depois de concluir as definições de configurações, pode executar o projeto no cluster remoto ou efetuar a depuração remota.
   
   ![Botão de execução remota](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run.png)

1. Clique nas **desligar** botão que os registos de envio não são apresentados no painel do lado esquerdo. No entanto, ele ainda está em execução no back-end.

   ![Botão de execução remota](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/remote-run-result.png)



### <a name="scenario-2-perform-remote-debugging"></a>Cenário 2: Efetuar a depuração remota
1. Configurar pontos de rutura e, em seguida, clique nas **depuração remota** ícone. A diferença com envio remoto é essa necessidade de nome de utilizador/palavra-passe SSH para ser configurado.

   ![Selecione o ícone de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Quando a execução do programa atinge o ponto de interrupção, verá uma **Driver** separador e duas **Executor** separadores o **depurador** painel. Selecione o **programa de retomar** ícone para continuar a executar o código, que, em seguida, atinge o ponto de interrupção seguinte. Terá de mudar para o correto **Executor** separador para localizar o executor de destino para depurar. Pode ver os registos de execução no correspondente **consola** separador.

   ![Separador de depuração](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Cenário 3: Efetuar a depuração remota e da correção de bug
1. Configurar dois pontos de rutura e, em seguida, selecione o **depurar** ícone para iniciar o processo de depuração remoto.

1. O código para no primeiro ponto de interrupção, e o parâmetro e informações variáveis são mostrados na **variáveis** painel. 

1. Selecione o **programa de retomar** ícone para continuar. O código para o segundo ponto. A exceção é recebida como esperado.

   ![Gerar erro](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

1. Selecione o **programa de retomar** ícone novamente. O **submissão de Spark do HDInsight** janela exibe um erro de "tarefa de execução falhou".

   ![Submissão de erro](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

1. Para atualizar dinamicamente o valor da variável ao utilizar o IntelliJ capacidade de depuração, selecione **depurar** novamente. O **variáveis** é apresentado o painel novamente. 

1. Com o botão direito do destino no **depurar** separador e, em seguida, selecione **definir o valor**. Em seguida, introduza um novo valor para a variável. Em seguida, selecione **Enter** para guardar o valor. 

   ![Definir valor](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value.png) 

1. Selecione o **programa de retomar** ícone para continuar a executar o programa. Desta vez, nenhuma exceção é capturada. Pode ver que o projeto é executado com êxito sem quaisquer exceções.

   ![Depurar sem exceção](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Passos seguintes
* [Descrição geral: Apache Spark no HDInsight do Azure](apache-spark-overview.md)

### <a name="demo"></a>Demonstração
* Crie projeto de Scala (vídeo): [Criar aplicações do Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depuração remota (vídeo): [Utilizar o Azure Toolkit para IntelliJ depurar aplicações do Apache Spark remotamente num cluster do HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: Efetuar análise de dados interativa com o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura do edifício com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de registos de Web site com o Apache Spark no HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Apache Spark com o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilizar o Azure Toolkit para IntelliJ para criar aplicativos de Apache Spark para um cluster do HDInsight](apache-spark-intellij-tool-plugin.md)
* [Utilizar o Azure Toolkit para IntelliJ para depurar aplicações de Apache Spark remotamente através de VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar ferramentas do HDInsight para IntelliJ com a Sandbox da Hortonworks](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utilizar ferramentas do HDInsight no Azure Toolkit para Eclipse para criar aplicações do Apache Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Utilizar blocos de notas do Zeppelin do Apache com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
