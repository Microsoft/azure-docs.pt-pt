---
title: Data Lake tools para Visual Studio com a Sandbox da Hortonworks - Azure HDInsight
description: Saiba como utilizar as ferramentas do Azure Data Lake para Visual Studio com a sandbox da Hortonworks em execução numa local VM. Com essas ferramentas, pode criar e executar tarefas do Hive e Pig no sandbox e o resultado da tarefa de vista e o histórico.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: hrasheed
ms.openlocfilehash: 8172da3f573f78365cbe76f435a4817e64bc136c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64700361"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Utilizar as ferramentas do Azure Data Lake para Visual Studio com a Sandbox da Hortonworks

O Azure Data Lake inclui ferramentas para trabalhar com clusters do Apache Hadoop genéricos. Este documento fornece os passos necessários para utilizar as ferramentas do Data Lake com a Sandbox da Hortonworks em execução numa máquina virtual local.

Usando a Sandbox da Hortonworks permite-lhe trabalhar com o Hadoop localmente no seu ambiente de desenvolvimento. Depois de desenvolveram uma solução e pretende implementá-la em escala, em seguida, pode mover a um cluster do HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* A Sandbox da Hortonworks, em execução numa máquina virtual no seu ambiente de desenvolvimento. Este documento foi escrito e testado com a área de segurança em execução no Oracle VirtualBox. Para obter informações sobre como configurar a proteção de segurança, consulte o [começar com a sandbox da Hortonworks.](hadoop/apache-hadoop-emulator-get-started.md) documento.

* Visual Studio 2013, Visual Studio 2015 ou Visual Studio 2017 (qualquer edição).

* O [Azure SDK para .NET](https://azure.microsoft.com/downloads/) 2.7.1 ou posterior.

* O [do Azure Data Lake tools para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Configurar palavras-passe para a área de segurança

Certifique-se de que a Sandbox da Hortonworks está em execução. Em seguida, siga os passos a [começar a utilizar a Sandbox da Hortonworks](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) documento. Estes passos configurar a palavra-passe para SSH `root` conta e o Apache Ambari `admin` conta. Estas palavras-passe é utilizados quando ligar para a área de segurança a partir do Visual Studio.

## <a name="connect-the-tools-to-the-sandbox"></a>Ligar as ferramentas para a área de segurança

1. Abra o Visual Studio, selecione **View**e, em seguida, selecione **Explorador de servidores**.

2. Partir **Explorador de servidores**, com o botão direito a **HDInsight** entrada e, em seguida, selecione **ligar ao HDInsight Emulator**.

    ![Captura de ecrã do Explorador de servidores, com a ligar ao HDInsight Emulator realçado](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. Partir do **ligar ao HDInsight Emulator** caixa de diálogo, introduza a palavra-passe que configurou para o Ambari.

    ![Captura de ecrã da caixa de diálogo, com a caixa de texto de palavra-passe realçada](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Selecione **Seguinte** para continuar.

4. Utilize o **palavra-passe** campo para introduzir a palavra-passe que configurou para o `root` conta. Deixe os outros campos com o valor predefinido.

    ![Captura de ecrã da caixa de diálogo, com a caixa de texto de palavra-passe realçada](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    Selecione **Seguinte** para continuar.

5. Aguarde pela validação de serviços para concluir. Em alguns casos, a validação pode falhar e pedir-lhe para atualizar a configuração. Se a validação falhar, selecione **atualização**e aguarde que a configuração e a verificação para o serviço concluir.

    ![Captura de ecrã da caixa de diálogo, com o botão de atualização realçado](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [!NOTE]  
    > O processo de atualização utiliza Ambari para modificar a configuração de Sandbox da Hortonworks ao que se espera pelas ferramentas do Data Lake para Visual Studio.

6. Após a conclusão da validação, selecione **concluir** para toda a configuração.
    ![Captura de ecrã da caixa de diálogo, com o botão Concluir realçado](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

     >[!NOTE]  
     > Consoante a velocidade do seu ambiente de desenvolvimento e a quantidade de memória atribuída à máquina virtual, pode demorar vários minutos para configurar e validar os serviços.

Depois de seguir estes passos, tem agora uma **cluster local do HDInsight** entrada no Server Explorer, sob o **HDInsight** secção.

## <a name="write-an-apache-hive-query"></a>Escrever uma consulta Apache Hive

Ramo de registo fornece uma linguagem de consulta do tipo SQL (HiveQL) para trabalhar com dados estruturados. Utilize os seguintes passos para saber como executar consultas de demanda no cluster local.

1. Na **Explorador de servidores**, clique com o botão direito na entrada para o cluster local que adicionou anteriormente e, em seguida, selecione **escrever uma consulta do Hive**.

    ![Captura de ecrã do Explorador de servidores, com a escrita de uma consulta do Hive realçado](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    É apresentada uma nova janela de consulta. Aqui pode rapidamente escrever e submeter uma consulta para o cluster local.

2. Na nova janela de consulta, introduza o seguinte comando:

        select count(*) from sample_08;

    Para executar a consulta, selecione **submeter** na parte superior da janela. Deixe os outros valores (**Batch** e nome do servidor) com os valores predefinidos.

    ![Captura de ecrã da janela de consulta, com o botão Submit realçado](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    Também pode utilizar o menu pendente junto a **Submit** para selecionar **avançadas**. Opções avançadas permitem-lhe fornecer opções adicionais, ao submeter a tarefa.

    ![Caixa de diálogo de captura de ecrã de submeter Script](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. Depois de submeter a consulta, é apresentado o estado da tarefa. O estado da tarefa Exibe informações sobre a tarefa é processado pelo Hadoop. **Estado da tarefa** fornece o estado da tarefa. O estado é atualizado periodicamente, ou pode utilizar o ícone de atualização para atualizar manualmente o estado.

    ![Caixa de diálogo de captura de ecrã da vista de tarefas, com o estado da tarefa realçado](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    Depois do **estado da tarefa** é alterado para **concluído**, é apresentado um direcionado acíclicos Graph (DAG). Este diagrama descreve o caminho de execução foi determinado pelo Tez ao processar a consulta do Hive. Tez é o motor de execução padrão para o Hive no local cluster.

    > [!NOTE]  
    > Apache Tez também é a predefinição quando estiver a utilizar clusters do HDInsight baseado em Linux. Não é a predefinição no HDInsight baseado em Windows. Para usá-lo, tem de adicionar a linha `set hive.execution.engine = tez;` ao início da sua consulta do Hive.

    Utilize o **saída da tarefa** ligação para ver o resultado. Neste caso, é 823, o número de linhas na tabela sample_08. Pode ver informações de diagnóstico sobre a tarefa utilizando o **registo da tarefa** e **transferir o registo do YARN** ligações.

4. Também pode executar tarefas do Hive interativamente, alterando a **Batch** campo **Interactive**. Em seguida, selecione **Execute**.

    ![Captura de ecrã de interativa e Execute botões realçado](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    Uma consulta interativa transmite em fluxo o registo de saída gerado durante o processamento para o **HiveServer2 saída** janela.

    > [!NOTE]  
    > As informações são o mesmo que está disponível a partir da **registo da tarefa** vincular depois que uma tarefa foi concluída.

    ![Captura de ecrã do log de saída](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## <a name="create-a-hive-project"></a>Criar um projeto do Hive

Também pode criar um projeto que contém vários scripts do Hive. Utilize um projeto quando têm relacionados scripts ou quiser armazenar scripts num sistema de controle de versão.

1. No Visual Studio, selecione **arquivo**, **New**e, em seguida **projeto**.

2. Na lista de projetos, expanda **modelos**, expanda **do Azure Data Lake**e, em seguida, selecione **HIVE (HDInsight)**. Na lista de modelos, selecione **Hive de exemplo**. Introduza um nome e localização e, em seguida, selecione **OK**.

    ![Janela de captura de ecrã do novo projeto, com o Azure Data Lake, HIVE, o Hive de exemplo e OK realçado](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

O **exemplo de Hive** projeto contém dois scripts, **WebLogAnalysis.hql** e **SensorDataAnalysis.hql**. Pode submeter estes scripts ao utilizar o mesmo **submeter** botão na parte superior da janela.

## <a name="create-an-apache-pig-project"></a>Criar um projeto da Apache Pig

Embora o ramo de registo fornece uma linguagem de tipo SQL para trabalhar com dados estruturados, Pig funciona por executar transformações nos dados. PIg fornece uma linguagem (Pig Latin) que permite desenvolver um pipeline de transformações. Para utilizar o Pig com o cluster local, siga estes passos:

1. Abra o Visual Studio e selecione **arquivo**, **New**e, em seguida **projeto**. Na lista de projetos, expanda **modelos**, expanda **do Azure Data Lake**e, em seguida, selecione **Pig (HDInsight)**. Na lista de modelos, selecione **Pig aplicação**. Introduza um nome, a localização e, em seguida, selecione **OK**.

    ![Janela de captura de ecrã do novo projeto, com o Azure Data Lake, Pig, aplicação de Pig e OK realçado](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. Introduza o seguinte texto como conteúdo dos **script.pig** arquivo que foi criado com este projeto.

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int,
            ip_address:chararray,
            date:chararray,
            time:chararray,
            landing_page:chararray,
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Enquanto o Pig usa um idioma diferente do que o Hive, como executar as tarefas é consistente entre as duas linguagens, através da **submeter** botão. Selecionando o baixo beside **submeter** apresenta uma caixa de diálogo de submissão avançada para o Pig.

    ![Caixa de diálogo de captura de ecrã de submeter Script](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)

3. Também é apresentado o estado da tarefa e a saída, o mesmo que uma consulta do Hive.

    ![Captura de ecrã de uma tarefa Pig concluída](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## <a name="view-jobs"></a>Ver tarefas

Ferramentas do Data Lake também permitem-lhe ver facilmente as informações sobre as tarefas que tenha sido executada no Hadoop. Utilize os seguintes passos para ver as tarefas que foram executadas no local cluster.

1. Partir **Explorador de servidores**, clique com o botão direito no cluster local e, em seguida, selecione **ver tarefas**. É apresentada uma lista de tarefas enviados para o cluster.

    ![Captura de ecrã do Explorador de servidores, com ver tarefas realçado](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. Na lista de tarefas, selecione um para ver os detalhes da tarefa.

    ![Captura de ecrã do Browser de trabalho, com uma das tarefas realçadas](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    As informações apresentadas são semelhantes ao que vê depois de executar uma consulta do Hive ou Pig, incluindo links para ver o resultado e registar informações.

3. Também pode modificar e volte a submeter a tarefa a partir daqui.

## <a name="view-hive-databases"></a>Exibir bancos de dados do Hive

1. Na **Explorador de servidores**, expanda o **cluster de HDInsight local** entrada e, em seguida, expanda **bases de dados do Hive**. O **predefinido** e **xademo** bases de dados no local cluster são apresentados. Expandir uma base de dados mostra as tabelas na base de dados.

    ![Captura de ecrã do Explorador de servidores, com bancos de dados expandidos](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. Expandir uma tabela apresenta as colunas para a tabela. Para ver rapidamente os dados, uma tabela com o botão direito e selecione **vista as primeiras 100 linhas**.

    ![Captura de ecrã do Explorador de servidores, com a tabela expandida e vista primeiras 100 linhas selecionadas](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### <a name="database-and-table-properties"></a>Propriedades da base de dados e tabela

Pode ver as propriedades de uma base de dados ou tabela. Selecionando **propriedades** apresenta detalhes para o item selecionado na janela Propriedades. Por exemplo, consulte as informações mostradas na seguinte captura de ecrã:

![Janela de captura de ecrã de propriedades](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### <a name="create-a-table"></a>Criar uma tabela

Para criar uma tabela, uma base de dados com o botão direito e, em seguida, selecione **Create Table**.

![Captura de ecrã do Explorador de servidores, com Create Table realçado](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

Em seguida, pode criar a tabela com um formulário. Na parte inferior da captura de ecrã seguinte, pode ver o HiveQL não processado que é utilizado para criar a tabela.

![Captura de ecrã do formulário utilizado para criar uma tabela](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## <a name="next-steps"></a>Passos Seguintes

* [Aprender tudo de que a Sandbox da Hortonworks](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Tutorial do Apache Hadoop - guia de introdução HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
