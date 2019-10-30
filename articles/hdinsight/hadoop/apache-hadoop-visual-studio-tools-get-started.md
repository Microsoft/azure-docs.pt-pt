---
title: Apache Hadoop & ferramentas de Data Lake do Visual Studio – Azure HDInsight
description: Saiba como instalar e usar o Data Lake Tools para Visual Studio para se conectar a clusters Apache Hadoop no Azure HDInsight e, em seguida, executar consultas Hive.
keywords: ferramentas do hadoop,consulta do hive,visual studio, visual studio hadoop
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 7386250cf09d1cfbce8c34f85eaa57d07e67ed35
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044554"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Usar as ferramentas de Data Lake para o Visual Studio para se conectar ao Azure HDInsight e executar consultas de Apache Hive

Saiba como usar [Microsoft Azure data Lake e ferramentas de Stream Analytics para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) (também chamado de ferramentas data Lake) para se conectar a clusters Apache Hadoop no [Azure HDInsight](../hdinsight-hadoop-introduction.md) e enviar consultas de Hive.  

Para obter mais informações sobre a utilização do HDInsight, veja [Introduction to HDInsight](../hdinsight-hadoop-introduction.md) (Introdução ao HDInsight) e [Get started with HDInsight ](apache-hadoop-linux-tutorial-get-started.md) (Começar a utilizar o HDInsight).  

Para obter mais informações sobre como se conectar a um cluster Apache Storm, consulte [ C# desenvolver topologias para Apache Storm no HDInsight usando o Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Pode utilizar o Data Lake Tools para Visual Studio para aceder ao Azure Data Lake Analytics e ao HDInsight. Para obter informações sobre o Data Lake Tools, veja [Desenvolver scripts U-SQL com o Data Lake Tools para Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo e usar as ferramentas de Data Lake para o Visual Studio, você precisará dos seguintes itens:

* Um cluster do Azure HDInsight. Para criar um cluster HDInsight, consulte Introdução [ao uso de Apache Hadoop no Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Para executar consultas interativas de Apache Hive, você precisa de um cluster de [consulta interativa do HDInsight](../interactive-query/apache-interactive-query-get-started.md) .  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) (2013 ou mais recente).  O [Visual Studio Community Edition](https://visualstudio.microsoft.com/vs/community/) é gratuito.  Consulte também [instalar o visual studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) e o [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Há pequenas variações de interface com o Visual Studio 2019.

  > [!IMPORTANT]  
  > Não há mais suporte para ferramentas de Data Lake para Visual Studio 2013.

## <a name="install-data-lake-tools-for-visual-studio"></a>Instalar as Ferramentas do Data Lake para Visual Studio  
<a name="install-or-update-data-lake-tools-for-visual-studio"></a>

* Visual Studio 2017 ou Visual Studio 2019  
  Durante a instalação, certifique-se de incluir pelo menos cargas de trabalho **desenvolvimento do Azure** ou **armazenamento e processamento de dados**.  

  Para instalações existentes, na barra de menus, navegue até **ferramentas** > **obter ferramentas e recursos...** para abrir o instalador do Visual Studio.  Em seguida, selecione pelo menos cargas de trabalho **desenvolvimento do Azure** ou **armazenamento e processamento de dados**.

  ![Captura de tela de Instalador do Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/vs-2017-installation.png)

* Visual Studio 2013 e 2015  
  [Baixe as ferramentas de data Lake](https://www.microsoft.com/download/details.aspx?id=49504). Escolha a versão do Data Lake Tools que corresponde à sua versão do Visual Studio.  

> [!NOTE]  
> Atualmente, está disponível apenas a versão em inglês do Data Lake Tools para Visual Studio.

## <a name="update-data-lake-tools-for-visual-studio"></a>Atualizar as ferramentas de Data Lake para o Visual Studio  

1. Abra o Visual Studio.

2. Na barra de menus, navegue até **ferramentas** > **extensões e atualizações...** .

3. Na janela **extensões e atualizações** , expanda **atualizações** à esquerda.

4. Se uma atualização estiver disponível, as **ferramentas analíticas Azure data Lake e Stream** serão exibidas na janela principal.  Selecione **Atualizar**.

> [!NOTE]  
> Pode utilizar apenas o Data Lake Tools versão 2.3.0.0 ou posterior para ligar a clusters da Consulta Interativa e executar consultas interativas do Hive.

## <a name="connect-to-azure-subscriptions"></a>Ligar a subscrições do Azure
Pode utilizar o Data Lake Tools para Visual Studio para se ligar aos seus clusters do HDInsight, realizar algumas operações de gestão básicas e executar consultas do Hive.

> [!NOTE]  
> Para obter informações sobre a ligação a um cluster do Hadoop genérico, veja [Escrever e submeter consultas do Hive com o Visual Studio](https://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx).

Para ligar à sua subscrição do Azure:

1. Abra o Visual Studio.

2. Na barra de menus, navegue até **exibir** > **Gerenciador de servidores**.

3. Em Gerenciador de Servidores, clique com o botão direito do mouse em **Azure**, selecione **conectar a Microsoft Azure assinatura...** e conclua o processo de entrada.

4. Em Gerenciador de Servidores, uma lista de clusters HDInsight existentes é exibida. Se não tiver clusters, pode criar um através do Portal do Azure, do Azure PowerShell ou do SDK do HDInsight. Para obter mais informações, veja [Create HDInsight clusters](../hdinsight-hadoop-provision-linux-clusters.md) (Criar clusters do HDInsight).

   ![Data Lake as ferramentas para a lista de clusters do Visual Studio no Gerenciador de Servidores](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png "Data Lake as ferramentas para a lista de clusters do Visual Studio no Gerenciador de Servidores")

5. Expanda um cluster do HDInsight. **Os bancos de dados do hive**, uma conta de armazenamento padrão, as contas de armazenamento vinculadas e o **log de serviço do Hadoop** são exibidos. Pode expandir ainda mais as entidades.

Depois de se conectar à sua assinatura do Azure, você poderá executar as seguintes tarefas.

Ligar-se ao portal do Azure a partir do Visual Studio:

1. Em Gerenciador de Servidores, navegue até **Azure** > **HDInsight** e selecione o cluster.

2. Clique com o botão direito do mouse em um cluster HDInsight e selecione **gerenciar cluster no portal do Azure [sic]** .

Para fazer perguntas e/ou fornecer comentários do Visual Studio:

1. Em Gerenciador de Servidores, navegue até **Azure** > **HDInsight**.

2. Clique com o botão direito do mouse em **HDInsight** e selecione um **Fórum do MSDN** para fazer perguntas ou **forneça comentários** para fornecer comentários.

## <a name="link-a-cluster"></a>Vincular um cluster
Você pode vincular um cluster clicando com o botão direito do mouse no **HDInsight** e, em seguida, selecionando **vincular um cluster HDInsight**. Insira **a URL de conexão**, o nome de **usuário** e a **senha**, clique em **Avançar** e em **concluir**. o cluster deve estar listado no nó HDInsight com êxito.

![Captura de tela da caixa de diálogo ferramentas de Data Lake para o cluster de link do Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

Clique com o botão direito do mouse no cluster vinculado, selecione **Editar**, o usuário pode atualizar as informações do cluster. A adição de um cluster HDInsight só dá suporte ao hive por enquanto.

![Captura de tela das ferramentas de Data Lake para a atualização do cluster de link do Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Explorar recursos ligados
No Explorador de Servidores, pode ver a conta do Storage predefinida e quaisquer contas do Storage ligadas. Se expandir a conta do Storage predefinida, pode ver os contentores incluídos na mesma. A conta do Storage predefinida e o contentor predefinido estão marcados. Clique com o botão direito do rato em qualquer um dos contentores para ver os conteúdos do contentor.

![Ferramentas de Data Lake para recursos vinculados do Visual Studio no Gerenciador de Servidores](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png "Listar recursos vinculados")

Depois de abrir um contentor, pode utilizar os botões seguintes para carregar, eliminar e transferir blobs:

![Ferramentas de Data Lake para operações de blob do Visual Studio no Gerenciador de Servidores](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png "Carregar, excluir e baixar BLOBs no Gerenciador de Servidores")

## <a name="run-interactive-apache-hive-queries"></a>Executar consultas interativas de Apache Hive
O [Apache Hive](https://hive.apache.org) é uma infraestrutura de armazém de dados que está incorporada no Hadoop. O Hive é utilizado para resumo de dados, consultas e análises. Pode utilizar o Data Lake Tools para Visual Studio para executar consultas do Hive a partir do Visual Studio. Para obter mais informações sobre o Hive, consulte [usar o Apache Hive com o HDInsight](hdinsight-use-hive.md).

A [Consulta Interativa](../interactive-query/apache-interactive-query-get-started.md) utiliza o [Hive no LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) no Apache Hive 2.1. A Consulta Interativa traz interatividade a consultas complexas de estilo do armazém de dados em conjuntos de dados grandes e armazenados. Executar consultas do Hive no Interactive Query é muito mais rápido em comparação com as tradicionais tarefas de lote do Hive. 

> [!NOTE]  
> Pode executar consultas interativas do Hive apenas quando ligar a um cluster do [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md).

Também pode utilizar o Data Lake Tools para Visual Studio para ver o que está dentro de uma tarefa do Hive. O Data Lake Tools para Visual Studio recolhe e analisa os registos do Yarn de determinadas tarefas do Hive.

Em Gerenciador de Servidores, navegue até **Azure** > **HDInsight** e selecione o cluster.  Esse será o ponto de partida em Gerenciador de Servidores para as seções seguirem.

### <a name="view-hivesampletable"></a>Exibir hivesampletable
Todos os clusters HDInsight têm uma tabela de hive de exemplo padrão chamada `hivesampletable`.  

No cluster, navegue até **bancos de dados do Hive** > **padrão** > **hivesampletable**.

* Para exibir `hivesampletable` esquema:  
Expanda **hivesampletable**.

* Para exibir dados de `hivesampletable`:  
Clique com o botão direito do mouse em **hivesampletable**e selecione **exibir as primeiras 100 linhas**.  É equivalente a executar a seguinte consulta do Hive com o controlador ODBC do Hive:

   `SELECT * FROM hivesampletable LIMIT 100`

  Pode personalizar a contagem de linhas.

  ![Captura de tela de uma consulta de esquema do hive do Visual Studio do HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-hive-schema.png "Resultados da consulta do hive")

### <a name="create-hive-tables"></a>Criar tabelas do Hive
Para criar uma tabela do Hive, pode utilizar a GUI ou utilizar consultas do Hive. Para obter informações sobre como usar consultas do hive, consulte [executar consultas de Apache Hive](#run.queries).

1. No cluster, navegue até **bancos de dados do Hive** > **padrão**.

2. Clique com o botão direito do mouse em **padrão**e selecione **criar tabela**.

3. Configure a tabela conforme desejado.  

4. Selecione **Criar Tabela** para submeter a tarefa de criação da nova tabela do Hive.

    ![Captura de tela da janela criar tabela do Ferramentas do Visual Studio do HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png "Criar tabela do hive")

### <a name="run.queries"></a>Criar e executar consultas do hive
Tem duas opções para criar e executar consultas do Hive:

* Criar consultas ad-hoc
* Criar uma aplicação do Hive

Para criar e executar consultas ad hoc:

1. Clique com o botão direito do mouse no cluster em que você deseja executar a consulta e selecione **gravar uma consulta do hive**.  

2. Insira a seguinte consulta de Hive:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

    O editor do Hive suporta IntelliSense. O Data Lake Tools para Visual Studio suportam o carregamento de metadados remotos durante a edição do script do Hive. Por exemplo, se você digitar `SELECT * FROM`, o IntelliSense listará todos os nomes de tabela sugeridos. Quando é especificado um nome de tabela, o IntelliSense lista os nomes das colunas. As ferramentas suportam quase todas as instruções DML do Hive, subconsultas e os UDFs incorporados.

    ![Captura de tela de um exemplo do HDInsight Ferramentas do Visual Studio IntelliSense 1](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png "IntelliSense U-SQL")

    ![Captura de tela de um exemplo do HDInsight Ferramentas do Visual Studio IntelliSense 2](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png "IntelliSense U-SQL")

   > [!NOTE]  
   > O IntelliSense sugere apenas os metadados do cluster selecionado na barra de ferramentas do HDInsight.

3. Escolha o modo de execução:

    * **Interativo**  

      Verifique se **interativo** está selecionado e, em seguida, selecione **executar**.

      ![Captura de tela de consulta e execução](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

      Verifique se o **lote** está selecionado e, em seguida, selecione **Enviar**.  Se você selecionar a opção de envio avançado, configure o **nome do trabalho**, os **argumentos**, **as configurações adicionais**e o **diretório de status** para o script.

      ![Opções de consulta e lote do Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)  

      ![Captura de tela de uma consulta de Hive Hadoop do HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png "Enviar consultas")

      > [!NOTE]  
      > Você não pode enviar lotes para os clusters de consulta interativa.  Você deve usar o modo interativo.

Para criar e executar uma solução do Hive:

1. Na barra de menus, navegue até **arquivo** > **novo** > **projeto...** .

2. No painel esquerdo, navegue até **instalado** > **Azure Data Lake** **hive do > (HDInsight)** .  

3. No painel do meio, selecione **Aplicação do Hive**. Introduza as propriedades e, em seguida, selecione **OK**.

    ![Captura de tela de um HDInsight Ferramentas do Visual Studio novo projeto do hive](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png "Criar aplicativos do hive por meio do Visual Studio")

4. No **Explorador de Soluções**, faça duplo clique em **Script.hql** para abrir o script.

### <a name="view-job-summary-and-output"></a>Exibir Resumo e saída do trabalho

O resumo do trabalho varia ligeiramente entre o modo de **lote** e **interativo** .

![Exibição da guia Resumo do trabalho Apache Hive](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png "Resumo do trabalho do hive")

Use o botão **Atualizar** para atualizar o status até que o status do trabalho seja alterado para **concluído**.  

* Para obter os detalhes do trabalho no modo de **lote** , selecione os links na parte inferior para ver a **consulta de trabalho**, **saída de trabalho**, log de **trabalho**ou **log de yarn**.

* Para obter os detalhes do trabalho do modo **interativo** , consulte **saída** de guias e **saída de HiveServer2**.

  ![Detalhes do trabalho do Visual Studio Apache Hive](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png "Detalhes do trabalho do hive")

### <a name="view-job-graph"></a>Exibir grafo de trabalho

Atualmente, os grafos de trabalho são mostrados apenas para trabalhos do hive que usam tez como o mecanismo de execução.  Para obter informações sobre como habilitar o tez, consulte [usar o Apache Hive no HDInsight](hdinsight-use-hive.md).  Consulte também, [Use Apache tez em vez da redução de mapa](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Para ver todos os operadores dentro do vértice, faça duplo clique nos vértices do gráfico da tarefa . Também pode apontar para um operador específico para ver mais detalhes sobre o operador.

O grafo do trabalho pode não aparecer mesmo se tez for especificado como o mecanismo de execução se nenhum aplicativo tez for iniciado.  Isso pode acontecer porque o trabalho não contém instruções DML ou as instruções DML podem retornar sem iniciar um aplicativo tez. Por exemplo, `SELECT * FROM table1` não iniciará o aplicativo tez.

![Grafo de trabalho do Visual Studio Apache Hive](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png "Resumo do trabalho do hive")

### <a name="task-execution-detail"></a>Detalhes de execução da tarefa

No grafo do trabalho, você pode selecionar **detalhes de execução da tarefa** para obter informações estruturadas e visualizadas para trabalhos do hive. Também pode obter mais detalhes de tarefas. Se ocorrerem problemas de desempenho, pode utilizar a vista para obter mais detalhes sobre o problema. Por exemplo, pode obter informações sobre como funciona cada tarefa e informações detalhadas sobre cada tarefa (leitura/escrita de dados, hora de agendamento/início/fim e assim sucessivamente). Utilize as informações para otimizar as configurações da tarefa ou a arquitetura do sistema, com base nas informações visualizadas.

![Janela de exibição de execução de tarefa do Data Lake Ferramentas do Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png "Exibição de execução de tarefa")


### <a name="view-hive-jobs"></a>Ver tarefas do Hive
Pode ver as consultas da tarefa, a saída da tarefa, os registos da tarefa e os registos Yarn para as tarefas do Hive.

Na versão mais recente das ferramentas, pode ver o conteúdo das tarefas do Hive ao recolher e analisar registos Yarn. Um registo Yarn pode ajudar a investigar problemas de desempenho. Para obter mais informações sobre a forma como o HDInsight recolhe registos Yarn, veja [Access HDInsight application logs programmatically (Aceder programaticamente aos registos de aplicações do HDInsight)](../hdinsight-hadoop-access-yarn-app-logs.md).

Para ver tarefas do Hive:

1. Clique com o botão direito do mouse em um cluster HDInsight e selecione **exibir trabalhos**. É apresentada uma lista dos trabalhos do Hive executados no cluster.  

2. Selecione uma tarefa. Na janela **Resumo da Tarefa do Hive**, selecione uma das seguintes opções:
    - **Consulta da Tarefa**
    - **Resultado da Tarefa**
    - **Registo da Tarefa**  
    - **Registo do Yarn**

    ![Captura de tela da janela exibir trabalhos do hive Ferramentas do Visual Studio do HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png "Ver tarefas do Hive")


## <a name="run-apache-pig-scripts"></a>Executar scripts do Apache Pig

1. Na barra de menus, navegue até **arquivo** > **novo** > **projeto...** .

2. No painel esquerdo, navegue até **instalado** > **Azure data Lake** > **Pig (HDInsight)** .  

3. No painel central, selecione **aplicativo Pig**. Introduza as propriedades e, em seguida, selecione **OK**.

4. Em **Gerenciador de soluções**, clique duas vezes em **script. Pig** para abrir o script.

## <a name="feedback-and-known-issues"></a>Comentários e problemas conhecidos
* Foi corrigido um problema no qual os resultados que são iniciados com valores nulos não são apresentados. Se estiver bloqueado neste problema, contacte a equipa de suporte.
* O script HQL criado pelo Visual Studio é codificado consoante a definição de região local do utilizador. O script não é executado corretamente se carregar o script para um cluster como um ficheiro binário.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a utilizar o pacote do Data Lake Tools para Visual Studio para ligar a clusters do HDInsight a partir do Visual Studio. Também aprendeu a executar uma consulta do Hive. Para obter mais informações, veja estes artigos:

* [Executar consultas do Apache Hive com as ferramentas do Data Lake para Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Utilizar o Hive do Hadoop no HDInsight](hdinsight-use-hive.md)
* [Introdução ao uso de Apache Hadoop no HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Enviar Apache Hadoop trabalhos no HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analisar dados do Twitter com o Apache Hadoop no HDInsight](../hdinsight-analyze-twitter-data.md)

