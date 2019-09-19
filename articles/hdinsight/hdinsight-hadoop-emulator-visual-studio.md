---
title: Ferramentas de Data Lake para Visual Studio com Hortonworks sandbox – Azure HDInsight
description: Saiba como usar as ferramentas de Azure Data Lake para o Visual Studio com a Hortonworks sandbox em execução em uma VM local. Com essas ferramentas, você pode criar e executar trabalhos do hive e do Pig na área restrita e exibir a saída e o histórico do trabalho.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 1edab776ec93f057ebf7e37ac887747f86a27db9
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098779"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Usar as ferramentas de Azure Data Lake para Visual Studio com a área restrita Hortonworks

Azure Data Lake inclui ferramentas para trabalhar com clusters Apache Hadoop genéricos. Este documento fornece as etapas necessárias para usar as ferramentas de Data Lake com a área restrita Hortonworks em execução em uma máquina virtual local.

O uso da área restrita do Hortonworks permite que você trabalhe com o Hadoop localmente em seu ambiente de desenvolvimento. Depois de desenvolver uma solução e de implantá-la em escala, você pode mover para um cluster HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* A área restrita do Hortonworks, em execução em uma máquina virtual em seu ambiente de desenvolvimento. Este documento foi escrito e testado com a área restrita em execução no Oracle VirtualBox. Para obter informações sobre como configurar a área restrita, consulte a [introdução à área restrita do Hortonworks.](hadoop/apache-hadoop-emulator-get-started.md) Document.

* Visual Studio.

* O [SDK do Azure para .net](https://azure.microsoft.com/downloads/) 2.7.1 ou posterior.

* As [ferramentas de Azure data Lake para o Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Configurar senhas para a área restrita

Verifique se a área restrita Hortonworks está em execução. Em seguida, siga as etapas no documento introdução ao [Hortonworks sandbox](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) . Essas etapas configuram a senha para `root` a conta SSH e a conta `admin` do Apache Ambari. Essas senhas são usadas quando você se conecta à área restrita do Visual Studio.

## <a name="connect-the-tools-to-the-sandbox"></a>Conectar as ferramentas à área restrita

1. Abra o Visual Studio, selecione **Exibir**e, em seguida, selecione **Gerenciador de servidores**.

2. Em **Gerenciador de servidores**, clique com o botão direito do mouse na entrada do **Hdinsight** e selecione **conectar ao emulador do hdinsight**.

    ![Gerenciador de Servidores, com conectar ao emulador do HDInsight realçado](./media/hdinsight-hadoop-emulator-visual-studio/connect-hdinsight-emulator.png)

3. Na caixa de diálogo **conectar ao emulador do HDInsight** , digite a senha que você configurou para Ambari.

    ![Captura de tela da caixa de diálogo com a caixa de texto senha do ambari realçada](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Selecione **Seguinte** para continuar.

4. Use o campo **senha** para inserir a senha que você configurou `root` para a conta. Deixe os outros campos no valor padrão.

    ![Captura de tela da caixa de diálogo com a caixa de texto senha raiz realçada](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password1.png)

    Selecione **Seguinte** para continuar.

5. Aguarde a conclusão da validação dos serviços. Em alguns casos, a validação pode falhar e solicitar que você atualize a configuração. Se a validação falhar, selecione **Atualizar**e aguarde a configuração e a verificação para que o serviço seja concluído.

    ![Captura de tela da caixa de diálogo com o botão atualizar realçado](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update-window.png)

    > [!NOTE]  
    > O processo de atualização usa Ambari para modificar a configuração da área restrita do Hortonworks para o que é esperado pelas ferramentas de Data Lake para o Visual Studio.

6. Após a conclusão da validação, selecione **concluir** para concluir a configuração.
    ![Captura de tela da caixa de diálogo com o botão Concluir realçado](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect-dialog.png)

     >[!NOTE]  
     > Dependendo da velocidade do ambiente de desenvolvimento e da quantidade de memória alocada para a máquina virtual, pode levar vários minutos para configurar e validar os serviços.

Depois de seguir essas etapas, agora você terá uma entrada de **cluster local do hdinsight** no Gerenciador de servidores, na seção **HDInsight** .

## <a name="write-an-apache-hive-query"></a>Gravar uma consulta de Apache Hive

O hive fornece uma linguagem de consulta semelhante a SQL (HiveQL) para trabalhar com dados estruturados. Use as etapas a seguir para saber como executar consultas sob demanda no cluster local.

1. Em **Gerenciador de servidores**, clique com o botão direito do mouse na entrada do cluster local que você adicionou anteriormente e, em seguida, selecione **gravar uma consulta do hive**.

    ![Captura de tela de Gerenciador de Servidores, com a consulta Write a Hive realçada](./media/hdinsight-hadoop-emulator-visual-studio/write-apache-hive-query.png)

    É apresentada uma nova janela de consulta. Aqui você pode rapidamente escrever e enviar uma consulta para o cluster local.

2. Na janela nova consulta, digite o seguinte comando:

        select count(*) from sample_08;

    Para executar a consulta, selecione **Enviar** na parte superior da janela. Deixe os outros valores (**lote** e nome do servidor) com os valores padrão.

    ![Captura de tela da janela de consulta, com o botão enviar realçado](./media/hdinsight-hadoop-emulator-visual-studio/query-window-submit-hive.png)

    Você também pode usar o menu suspenso ao lado de **Enviar** para selecionar **avançado**. As opções avançadas permitem que você forneça opções adicionais ao enviar o trabalho.

    ![Captura de tela do hive da caixa de diálogo Enviar script](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-hive.png)

3. Depois de enviar a consulta, o status do trabalho é exibido. O status do trabalho exibe informações sobre o trabalho conforme ele é processado pelo Hadoop. O **estado do trabalho** fornece o status do trabalho. O estado é atualizado periodicamente ou você pode usar o ícone de atualização para atualizar o estado manualmente.

    ![Captura de tela da caixa de diálogo exibição do trabalho, com o estado do trabalho realçado](./media/hdinsight-hadoop-emulator-visual-studio/job-view-dialog-box1.png)

    Depois que o **estado do trabalho** muda para **concluído**, um DAG (grafo direcionado acíclico) é exibido. Este diagrama descreve o caminho de execução que foi determinado pelo tez ao processar a consulta do hive. Tez é o mecanismo de execução padrão para o hive no cluster local.

    > [!NOTE]  
    > Apache Tez também é o padrão quando você está usando clusters HDInsight baseados em Linux. Ele não é o padrão no HDInsight baseado no Windows. Para usá-lo, você deve adicionar a linha `set hive.execution.engine = tez;` ao início da consulta do hive.

    Use o link **saída do trabalho** para exibir a saída. Nesse caso, é 823, o número de linhas na tabela sample_08. Você pode exibir informações de diagnóstico sobre o trabalho usando o **log de trabalho** e baixar links de **log do yarn** .

4. Você também pode executar trabalhos do hive interativamente alterando o campo de **lote** para **interativo**. Em seguida, selecione **executar**.

    ![Captura de tela dos botões interativo e executar realçados](./media/hdinsight-hadoop-emulator-visual-studio/hdi-interactive-query.png)

    Uma consulta interativa transmite o log de saída gerado durante o processamento para a janela de **saída do HiveServer2** .

    > [!NOTE]  
    > As informações são as mesmas que estão disponíveis no link do **log de trabalho** após a conclusão de um trabalho.

    ![Captura de tela da saída do HiveServer2](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output-box.png)

## <a name="create-a-hive-project"></a>Criar um projeto do hive

Você também pode criar um projeto que contém vários scripts do hive. Use um projeto quando você tiver scripts relacionados ou desejar armazenar scripts em um sistema de controle de versão.

1. No Visual Studio, selecione **arquivo**, **novo**e **projeto**.

2. Na lista de projetos, expanda **modelos**, expanda **Azure data Lake**e, em seguida, selecione **Hive (HDInsight)** . Na lista de modelos, selecione **exemplo de Hive**. Insira um nome e um local e, em seguida, selecione **OK**.

    ![Janela novo projeto, com Azure Data Lake, exemplo de Hive e OK](./media/hdinsight-hadoop-emulator-visual-studio/new-apache-hive-project.png)

O projeto de **exemplo do hive** contém dois scripts, **WebLogAnalysis. HQL** e **SensorDataAnalysis. HQL**. Você pode enviar esses scripts usando o mesmo botão **Enviar** na parte superior da janela.

## <a name="create-an-apache-pig-project"></a>Criar um projeto do Apache Pig

Embora o hive forneça uma linguagem semelhante ao SQL para trabalhar com dados estruturados, o Pig funciona executando transformações nos dados. O Pig fornece uma linguagem (Pig Latin) que permite desenvolver um pipeline de transformações. Para usar o Pig com o cluster local, siga estas etapas:

1. Abra o Visual Studio e selecione **arquivo**, **novo**e **projeto**. Na lista de projetos, expanda **modelos**, expanda **Azure data Lake**e, em seguida, selecione **Pig (HDInsight)** . Na lista de modelos, selecione **aplicativo Pig**. Insira um nome, um local e, em seguida, selecione **OK**.

    ![Captura de tela da janela novo projeto, com Azure Data Lake, Pig, aplicativo Pig e OK realçado](./media/hdinsight-hadoop-emulator-visual-studio/new-apche-pig-project.png)

2. Insira o texto a seguir como o conteúdo do arquivo **script. Pig** que foi criado com este projeto.

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

    Embora o Pig use um idioma diferente do hive, a forma como você executa os trabalhos é consistente entre os dois idiomas, por meio do botão **Enviar** . Selecionar a lista suspensa ao lado de **Enviar** exibe uma caixa de diálogo de envio avançado para Pig.

    ![Captura de tela da caixa de diálogo Enviar script Pig](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-pig1.png)

3. O status e a saída do trabalho também são exibidos, o mesmo que uma consulta de Hive.

    ![Captura de tela de um trabalho Pig concluído](./media/hdinsight-hadoop-emulator-visual-studio/completed-apache-pig.png)

## <a name="view-jobs"></a>Exibir trabalhos

Data Lake ferramentas também permitem que você exiba facilmente as informações sobre os trabalhos que foram executados no Hadoop. Use as etapas a seguir para ver os trabalhos que foram executados no cluster local.

1. Em **Gerenciador de servidores**, clique com o botão direito do mouse no cluster local e selecione **exibir trabalhos**. É exibida uma lista de trabalhos que foram enviados ao cluster.

    ![Captura de tela de Gerenciador de Servidores, com exibir trabalhos realçados](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-view-jobs.png)

2. Na lista de trabalhos, selecione um para exibir os detalhes do trabalho.

    ![Captura de tela do navegador de trabalho, com um dos trabalhos realçado](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-job-details.png)

    As informações exibidas são semelhantes às que você vê depois de executar uma consulta Hive ou Pig, incluindo links para exibir as informações de saída e de log.

3. Você também pode modificar e reenviar o trabalho aqui.

## <a name="view-hive-databases"></a>Exibir bancos de dados do hive

1. Em **Gerenciador de servidores**, expanda a entrada de **cluster local do HDInsight** e, em seguida, expanda **bancos de dados do hive**. Os bancos de dados **padrão** e **xademo** no cluster local são exibidos. Expandir um banco de dados mostra as tabelas no banco de dados.

    ![Captura de tela de Gerenciador de Servidores, com bancos de dados expandidos](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases-view.png)

2. Expandir uma tabela exibe as colunas dessa tabela. Para exibir rapidamente os dados, clique com o botão direito do mouse em uma tabela e selecione **exibir as primeiras 100 linhas**.

    ![Gerenciador de Servidores, com tabela expandida e exibir as primeiras 100 linhas selecionadas](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-top-100-rows.png)

### <a name="database-and-table-properties"></a>Propriedades de tabela e banco de dados

Você pode exibir as propriedades de um banco de dados ou tabela. A seleção de **Propriedades** exibe detalhes do item selecionado na janela Propriedades. Por exemplo, consulte as informações mostradas na captura de tela a seguir:

![Captura de tela de janela Propriedades](./media/hdinsight-hadoop-emulator-visual-studio/hdi-properties-window.png)

### <a name="create-a-table"></a>Criar uma tabela

Para criar uma tabela, clique com o botão direito do mouse em um banco de dados e selecione **criar tabela**.

![Captura de tela de Gerenciador de Servidores, com criar tabela realçada](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-create-table.png)

Em seguida, você pode criar a tabela usando um formulário. Na parte inferior da captura de tela a seguir, você pode ver o HiveQL bruto usado para criar a tabela.

![Captura de tela do formulário usado para criar uma tabela](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form-box.png)

## <a name="next-steps"></a>Passos Seguintes

* [Aprendendo a ropes da área restrita do Hortonworks](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Tutorial de Apache Hadoop-introdução ao HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
