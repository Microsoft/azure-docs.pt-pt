---
title: Apache Hadoop & Visual Studio Data Lake Tools - Azure HDInsight
description: Aprenda a instalar e utilizar ferramentas de data lake para o Estúdio Visual para se conectar aos clusters Apache Hadoop em Azure HDInsight e, em seguida, executar consultas da Hive.
keywords: ferramentas do hadoop,consulta do hive,visual studio, visual studio hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 4ad58bc2d61f063dce2c23f60a65dcbec48a2303
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382141"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Use ferramentas de data lake para estúdio visual para conectar-se ao Azure HDInsight e executar consultas apache hive

Saiba como usar o Microsoft Azure Data Lake e stream Analytics Tools para estúdio visual (também chamado data lake tools) para se conectar aos [clusters Apache Hadoop em Azure HDInsight](apache-hadoop-introduction.md) e submeter consultas de Hive.  

Para obter mais informações sobre a utilização do HDInsight, consulte [Iniciar-se com hDInsight](apache-hadoop-linux-tutorial-get-started.md).  

Para obter mais informações sobre a ligação a um cluster apache storm, consulte [Desenvolver C# topoologias para apache storm usando as ferramentas data lake para estúdio visual](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Pode utilizar o Data Lake Tools para Visual Studio para aceder ao Azure Data Lake Analytics e ao HDInsight. Para obter informações sobre o Data Lake Tools, veja [Desenvolver scripts U-SQL com o Data Lake Tools para Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Pré-requisitos

Para completar este artigo e utilizar ferramentas data lake para estúdio visual, você precisa dos seguintes itens:

* Um cluster do Azure HDInsight. Para criar um cluster HDInsight, consulte [Começar por utilizar o Apache Hadoop em Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Para executar consultas interativas da Apache Hive, você precisa de um cluster [de consulta interativa HDInsight.](../interactive-query/apache-interactive-query-get-started.md)  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/). A [edição visual do Estúdio Comunitário](https://visualstudio.microsoft.com/vs/community/) é gratuita. As instruções aqui mostradas são para [o Estúdio Visual 2019](https://visualstudio.microsoft.com/downloads/).

## <a name="install-data-lake-tools-for-visual-studio"></a>Instalar as Ferramentas do Data Lake para Visual Studio  

Siga as instruções adequadas para instalar ferramentas data lake para a sua versão do Estúdio Visual:

- Para o Visual Studio 2017 ou para o Visual Studio 2019:

    Durante a instalação do Estúdio Visual, certifique-se de que inclui a carga de trabalho de **desenvolvimento do Azure** ou a carga de trabalho de **armazenamento e processamento de dados.**  

    Para as instalações do Estúdio Visual existentes, vá à barra de menus IDE e selecione **Tools** > **Get Tools and Features** para abrir o Instalador de Estúdios Visuais. No separador **Workloads,** selecione pelo menos a carga de trabalho de **desenvolvimento do Azure** (em **Web & Cloud)** ou a carga de trabalho de **armazenamento e processamento** de dados (em **Outros Conjuntos de Ferramentas).**

  ![Seleção de carga de trabalho, Instalador de Estúdio Visual](./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png)

- Para o Estúdio Visual 2015:

    [Descarregue as ferramentas do Lago de Dados.](https://www.microsoft.com/download/details.aspx?id=49504) Escolha a versão do Data Lake Tools que corresponde à sua versão do Visual Studio.

## <a name="update-data-lake-tools-for-visual-studio"></a>Atualizar ferramentas do Lago de Dados para Estúdio Visual  

Em seguida, certifique-se de atualizar data lake tools para a versão mais recente.

1. Abra o Visual Studio.

2. Na janela **Iniciar,** selecione **Continue sem código**.

3. Na barra de menus Visual Studio IDE, escolha **Extensões** > **Gerir Extensões**.

4. Na caixa de diálogo **'Gerir extensões',** expanda o nó de **Atualizações.**

5. Se a lista de atualizações disponíveis incluir o Lago de **Dados Azure e as Ferramentas Analíticas stream,** selecione-o. Em seguida, selecione o botão **Atualizar.** Depois da caixa de diálogo **Descarregamento e Instalação** aparecer e desaparecer, o Estúdio Visual adiciona a extensão do Lago de **Dados Azure e as Ferramentas Analíticas Stream** à programação da atualização.

6. Feche todas as janelas do Estúdio Visual. Aparece a caixa de diálogo do **instalador VSIX.**

7. Selecione **Licença** para ler os termos da licença e, em seguida, selecione **Fechar** para voltar à caixa de diálogo do **Instalador VSIX.**

8. Selecione **Modificar**. Começa a instalação da atualização de extensão. Passado algum tempo, a caixa de diálogo muda para mostrar que está feito a fazer modificações. Selecione **Fechar**e, em seguida, reiniciar o Estúdio Visual para completar a instalação.

> [!NOTE]  
> Pode utilizar apenas o Data Lake Tools versão 2.3.0.0 ou posterior para ligar a clusters da Consulta Interativa e executar consultas interativas do Hive.

## <a name="connect-to-azure-subscriptions"></a>Ligar a subscrições do Azure

Você pode usar Data Lake Tools para Estúdio Visual para conectar-se aos seus clusters HDInsight, fazer algumas operações básicas de gestão, e executar consultas da Hive.

> [!NOTE]  
> Para obter informações sobre a ligação a um cluster genérico de Hadoop, consulte como escrever e submeter consultas da [Hive usando o Visual Studio](https://blogs.msdn.microsoft.com/xiaoyong/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio/).

### <a name="connect-to-an-azure-subscription"></a>Ligar a uma subscrição do Azure

Para ligar à sua subscrição do Azure:

1. Abra o Visual Studio.

2. Na janela **Iniciar,** selecione **Continue sem código**.

3. Na barra de menui IDE, escolha **ver** > **Server Explorer.**

4. No **Server Explorer,** clique no **Azure,** selecione **Connect to Microsoft Azure Subscription**, e complete o processo de autenticação. A partir do **Server Explorer,** expanda **o Azure** > **HDInsight** para ver uma lista de clusters HDInsight existentes.

5. Se não tiver clusters, crie um utilizando o portal Azure, O PowerShell ou o HDInsight SDK. Para mais informações, consulte [Configurar clusters em HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Lista de clusterS HDInsight, Server Explorer, Estúdio Visual](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png)

6. Expanda um cluster do HDInsight. O cluster contém nós para bases de **dados da Hive,** uma conta de armazenamento predefinido, quaisquer contas de armazenamento adicionais ligadas, e registo de **serviço hadoop**. Pode expandir ainda mais as entidades.

Depois de se ligar à sua subscrição do Azure, pode fazer as tarefas seguintes.

### <a name="connect-to-azure-from-visual-studio"></a>Ligue-se ao Azure do Estúdio Visual

Ligar-se ao portal do Azure a partir do Visual Studio:

1. No **Server Explorer,** expanda **o Azure** > **HDInsight** e selecione o seu cluster.

2. Clique à direita num cluster HDInsight e selecione **Manage Cluster no portal Azure**.

### <a name="offer-questions-and-feedback-from-visual-studio"></a>Oferecer perguntas e feedback do Estúdio Visual

Para fazer perguntas e/ou fornecer feedback do Visual Studio:

1. Do Server Explorer, escolha **Azure** > **HDInsight**.

2. Clique no **HDInsight** e selecione o **MSDN Forum** para fazer perguntas ou **dar feedback** para dar feedback.

## <a name="link-to-or-edit-a-cluster"></a>Link para ou editar um cluster

> [!NOTE]
> Atualmente, o único tipo de cluster HDInsight a que pode ligar é um tipo de Colmeia.

Para ligar um cluster HDInsight:

1. Clique no **HDInsight**e, em seguida, selecione **Link a HDInsight Cluster** para exibir a caixa de diálogo do Cluster **HDInsight.**

2. Introduza um Url de **Ligação** no formulário *https\://cluster \<&nbsp;nome>.azurehdinsight.net*. O **Nome do Cluster** preenche automaticamente a parte do nome do cluster do seu URL quando se vai para outro campo. Em seguida, introduza um **nome de utilizador** e **palavra-passe,** e selecione **Seguinte**.

    ![Link a cluster, HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

3. Selecione **Concluir**. Se a ligação do cluster for bem sucedida, o cluster é listado sob o nó **HDInsight.**

Para atualizar um cluster ligado, clique no cluster e **selecione Editar**. Em seguida, pode atualizar a informação do cluster.

![Editar um cluster ligado, HDInsight, Estúdio Visual](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Explorar recursos ligados
No Explorador de Servidores, pode ver a conta do Storage predefinida e quaisquer contas do Storage ligadas. Se expandir a conta do Storage predefinida, pode ver os contentores incluídos na mesma. A conta do Storage predefinida e o contentor predefinido estão marcados.

![Ferramentas data lake para estúdio visual ligados recursos no Server Explorer](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png)

Clique num recipiente e selecione **Ver Recipiente** para ver o conteúdo do recipiente. Depois de abrir um recipiente, pode utilizar os botões da barra de ferramentas para **atualizar** a lista de conteúdos, **carregar Blob,** **Eliminar bolhas selecionadas,** **Open Blob**e descarregar (**Save As**) bolhas selecionadas.

![Lista de contentores e operações de blob, cluster HDInsight, Estúdio Visual](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png)

## <a name="run-interactive-apache-hive-queries"></a>Executar consultas interativas da Colmeia Apache
O [Apache Hive](https://hive.apache.org) é uma infraestrutura de armazém de dados que está incorporada no Hadoop. O Hive é utilizado para resumo de dados, consultas e análises. Pode utilizar o Data Lake Tools para Visual Studio para executar consultas do Hive a partir do Visual Studio. Para mais informações sobre a Hive, consulte [o que é Apache Hive e HiveQL no Azure HDInsight?](hdinsight-use-hive.md)

[Consulta interativa em Azure HDInsight](../interactive-query/apache-interactive-query-get-started.md) usa [Hive em LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) na Apache Hive 2.1. A Consulta Interativa traz interatividade a consultas complexas e ao estilo de armazém de dados em conjuntos de dados grandes e armazenados. Executar consultas de Hive em Consulta Interativa é muito mais rápido do que os trabalhos tradicionais de lote da Hive. 

> [!NOTE]  
> Pode executar consultas interativas do Hive apenas quando ligar a um cluster do [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md).

Também pode utilizar o Data Lake Tools para Visual Studio para ver o que está dentro de uma tarefa do Hive. O Data Lake Tools para Visual Studio recolhe e analisa os registos do Yarn de determinadas tarefas do Hive.

Do **Server Explorer,** escolha **o Azure** > **HDInsight** e selecione o seu cluster.  Este nó é o ponto de partida no **Server Explorer** para as secções a seguir.

### <a name="view-hivesampletable"></a>Ver colmeia

Todos os clusters HDInsight têm uma tabela de colmeia de amostra padrão chamada `hivesampletable`.  

Do seu cluster, escolha bases de **dados da Hive** >  > **colmeia** **padrão.**

- Para ver o `hivesampletable` esquema:

    Expandir **a colmeia.** Os nomes e tipos de dados das colunas `hivesampletable` são mostrados.

- Para ver os dados `hivesampletable`:

    Clique na **colmeia**do clique direito, e selecione **Ver Top 100 Rows**. A lista de 100 resultados aparece na **Tabela da Colmeia:** janela colmeia. Esta ação equivale a executar a seguinte consulta da Hive utilizando o condutor hive ODBC:

    `SELECT * FROM hivesampletable LIMIT 100`

    Pode personalizar a contagem de linhas alterando o **número de linhas;** pode escolher 50, 100, 200 ou 1000 filas da lista de lançamentos.

### <a name="create-hive-tables"></a>Criar tabelas do Hive
Para criar uma tabela do Hive, pode utilizar a GUI ou utilizar consultas do Hive. Para obter informações sobre a utilização de consultas da Hive, consulte Criar e executar consultas da [Hive.](#create-and-run-hive-queries)

1. Do seu cluster, escolha bases de **dados da Hive** > **padrão**.

2. Predefinidodo clique à direita , e selecione **Criar Tabela**.

3. Configure a tabela.

4. Selecione o botão **Criar Tabela** para submeter o trabalho, o que cria a nova tabela Hive.

    ![Criar janela de mesa, Hive, cluster HDInsight, Estúdio Visual](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png)

### <a name="create-and-run-hive-queries"></a>Criar e executar consultas da Hive
Tem duas opções para criar e executar consultas do Hive:

* Criar consultas ad-hoc
* Criar uma aplicação do Hive

#### <a name="create-an-ad-hoc-query"></a>Criar uma consulta ad-hoc

Para criar e executar uma consulta ad-hoc:

1. Clique no cluster onde pretende executar a consulta e selecione **Escrever uma Consulta**de Colmeia .  

2. Insira uma consulta da Colmeia.

    O editor do Hive suporta IntelliSense. O Data Lake Tools para Visual Studio suportam o carregamento de metadados remotos durante a edição do script do Hive. Por exemplo, se escrever `SELECT * FROM`, o IntelliSense lista todos os nomes de tabela sugeridos. Quando é especificado um nome de tabela, o IntelliSense lista os nomes das colunas. As ferramentas suportam quase todas as instruções DML do Hive, subconsultas e os UDFs incorporados.

    ![IntelliSense exemplo 1, Consulta ad-hoc da Hive, cluster HDInsight, Estúdio Visual](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png)

    ![IntelliSense exemplo 2, Consulta ad-hoc da Hive, cluster HDInsight, Estúdio Visual](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png)

    > [!NOTE]  
    > O IntelliSense sugere apenas os metadados do cluster selecionado na barra de ferramentas do HDInsight.

    Aqui está uma consulta de amostra que pode usar:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

3. Escolha o modo de execução:

    * **Interativo**  

        Na primeira lista de lançamentos, escolha **Interactive**, e, em seguida, selecione **Executar**.

        ![Modo interativo, consulta ad-hoc da Hive, cluster HDInsight, Estúdio Visual](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

        Na primeira lista de lançamentos, escolha **'Batch',** e, em seguida, selecione **Enviar** (ou selecione o ícone drop-down ao lado de **Enviar** e escolha **Avançado**).

        ![Modo lote, consulta ad-hoc da Hive, cluster HDInsight, Estúdio Visual](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)

        Se selecionar a opção de submissão avançada, aparece a caixa de diálogo **Do Script Enviar.** Configure o Nome de **Trabalho,** **Argumentos,** **Configurações Adicionais**e **Diretório** de Estado para o script.

        ![Envie caixa de diálogo script, consulta ad-hoc da Hive, cluster HDInsight, Estúdio Visual](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png)

      > [!NOTE]  
      > Não pode submeter lotes a aglomerados de consultas interativas.  Deve utilizar o modo interativo.

#### <a name="create-a-hive-application"></a>Criar uma aplicação do Hive

Para criar e executar uma solução do Hive:

1. Na barra de menus, escolha **File** > **New** > **Project**.

2. Na **Janela Criar uma nova** janela de projeto, selecione a caixa de pesquisa e escreva **Hive**. Em seguida, escolha **a Aplicação Hive** e selecione **Next**.

3. Na **configuração** da sua nova janela de projeto, introduza um **nome de Projeto,** selecione ou crie a **Localização**do projeto , e, em seguida, selecione **Criar**.

    ![Nova aplicação Hive, Configure a sua nova janela de projeto, HDInsight Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png)

4. No **Explorador de Soluções**, faça duplo clique em **Script.hql** para abrir o script.

### <a name="view-job-summary-and-output"></a>Ver resumo de trabalho e saída

O resumo do trabalho varia ligeiramente entre **o modo Lote** e **Interativo.**

![Janelas de resumo de trabalho da colmeia, lote e modo interativo, Estúdio Visual](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png)

Utilize o ícone **Refresh** para atualizar o estado até que o estado do trabalho mude para **Terminado**.  

* Para obter os detalhes de trabalho do modo **Batch,** selecione os links na parte inferior para ver a Consulta de **Trabalho,** Saída de **Trabalho,** ou **Registo de Trabalho,** ou para **ver registos**de fios .

* Para obter os detalhes do trabalho do modo **Interativo,** consulte as vidraças **de saída output** e **HiveServer2.**

    ![Saída interativa de trabalho da Hive, cluster HDInsight, Estúdio Visual](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png)

### <a name="view-job-graph"></a>Ver gráfico de trabalho

Atualmente, os gráficos de trabalho são apenas mostrados para trabalhos de Colmeia que usam Tez como motor de execução.  Para obter informações sobre a habilitação de Tez, veja [o que é Apache Hive e HiveQL no Azure HDInsight?](hdinsight-use-hive.md)  Ver também, [Use Apache Tez em vez de Map Reduce](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Para ver todos os operadores dentro do vértice, clique duas vezes nos vértices do gráfico de trabalho. Também pode apontar para um operador específico para ver mais detalhes sobre o operador.

Mesmo que a Tez seja especificada como motor de execução, o gráfico de trabalho pode não aparecer se não for lançada nenhuma aplicação Tez.  Esta situação pode ocorrer porque o trabalho não contém declarações dML, ou porque as declarações do DML podem voltar sem lançar uma aplicação Tez. Por exemplo, `SELECT * FROM table1` não vai lançar a aplicação Tez.

![Gráfico de trabalho da Colmeia Apache, Estúdio Visual](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png)

### <a name="view-task-execution-detail"></a>Ver detalhes de execução de tarefas

A partir do gráfico de trabalho, você pode selecionar Detalhes de **Execução de Tarefas** para obter informações estruturadas e visualizadas para trabalhos da Colmeia. Também pode conseguir mais detalhes sobre o trabalho. Se ocorrerem problemas de desempenho, pode utilizar a vista para obter mais detalhes sobre o problema. Por exemplo, pode obter informações sobre como cada tarefa funciona e informações detalhadas sobre cada tarefa (leitura/escrita de dados, horário/início/fim, e muito mais). Utilize as informações para otimizar as configurações da tarefa ou a arquitetura do sistema, com base nas informações visualizadas.

![Janela de vista de execução de tarefas, ferramentas de estúdio visual data lake](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png)

### <a name="view-hive-jobs"></a>Ver tarefas do Hive

Pode ver as consultas da tarefa, a saída da tarefa, os registos da tarefa e os registos Yarn para as tarefas do Hive.

Na versão mais recente das ferramentas, pode ver o conteúdo das tarefas do Hive ao recolher e analisar registos Yarn. Um registo Yarn pode ajudar a investigar problemas de desempenho. Para obter mais informações sobre como o HDInsight recolhe registos de fios, consulte os registos de [aplicações do Access Apache Hadoop YARN](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Para ver tarefas do Hive:

1. Clique num cluster HDInsight e selecione **'Ver Empregos'.**

    ![Ver Empregos, Hive Apache, cluster HDInsight, Estúdio Visual](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png)

    É apresentada uma lista dos trabalhos do Hive executados no cluster.  

2. Selecione uma tarefa. Na janela **Sumária de Trabalho** da Colmeia, selecione um dos seguintes links:
    - **Consulta da Tarefa**
    - **Resultado da Tarefa**
    - **Registo da Tarefa**  
    - **Log de fios**

## <a name="run-apache-pig-scripts"></a>Executar scripts de porco Apache

1. Na barra de menus, escolha **File** > **New** > **Project**.

2. Na janela **Iniciar,** selecione a caixa de pesquisa e introduza **O Porco**. Em seguida, selecione **Aplicação de porco** e selecione **Seguinte**.

3. Na **Configuração** da sua nova janela de projeto, insira um **nome de Projeto,** e selecione ou crie um **Local** para o projeto. Em seguida, selecione **Criar**.

4. No painel IDE **Solution Explorer,** clique duas vezes **no Script.pig** para abrir o script.

## <a name="feedback-and-known-issues"></a>Comentários e problemas conhecidos

* Foi corrigido um problema no qual os resultados que são iniciados com valores nulos não são apresentados. Se estiver bloqueado neste problema, contacte a equipa de suporte.

* O script HQL criado pelo Visual Studio é codificado consoante a definição de região local do utilizador. O script não é executado corretamente se carregar o script para um cluster como um ficheiro binário.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a utilizar o pacote do Data Lake Tools para Visual Studio para ligar a clusters do HDInsight a partir do Visual Studio. Também aprendeu a executar uma consulta do Hive. Para obter mais informações, veja estes artigos:

* [Executar consultas do Apache Hive com as ferramentas do Data Lake para Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [O que é Apache Hive e HiveQL no Azure HDInsight?](hdinsight-use-hive.md)
* [Criar o cluster Hadoop Apache - Modelo](apache-hadoop-linux-tutorial-get-started.md)
* [Submeta empregos apache Hadoop no HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analise os dados do Twitter usando a Apache Hive e Apache Hadoop no HDInsight](../hdinsight-analyze-twitter-data-linux.md)
