---
title: Apache Hadoop & ferramentas de Data Lake do Visual Studio – Azure HDInsight
description: Saiba como instalar e usar o Data Lake Tools para Visual Studio para se conectar a clusters Apache Hadoop no Azure HDInsight e, em seguida, executar consultas Hive.
keywords: ferramentas do hadoop,consulta do hive,visual studio, visual studio hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 4ad58bc2d61f063dce2c23f60a65dcbec48a2303
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825029"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Usar as ferramentas de Data Lake para o Visual Studio para se conectar ao Azure HDInsight e executar consultas de Apache Hive

Saiba como usar Microsoft Azure Data Lake e ferramentas de Stream Analytics para Visual Studio (também chamado de ferramentas Data Lake) para se conectar a [clusters Apache Hadoop no Azure HDInsight](apache-hadoop-introduction.md) e enviar consultas de Hive.  

Para obter mais informações sobre como usar o HDInsight, consulte Introdução [ao hdinsight](apache-hadoop-linux-tutorial-get-started.md).  

Para obter mais informações sobre como se conectar a um cluster Apache Storm, consulte [desenvolver C# topologias para Apache Storm usando as ferramentas do data Lake para Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Pode utilizar o Data Lake Tools para Visual Studio para aceder ao Azure Data Lake Analytics e ao HDInsight. Para obter informações sobre o Data Lake Tools, veja [Desenvolver scripts U-SQL com o Data Lake Tools para Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo e usar as ferramentas de Data Lake para o Visual Studio, você precisará dos seguintes itens:

* Um cluster do Azure HDInsight. Para criar um cluster HDInsight, consulte Introdução [ao uso de Apache Hadoop no Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Para executar consultas interativas de Apache Hive, você precisa de um cluster de [consulta interativa do HDInsight](../interactive-query/apache-interactive-query-get-started.md) .  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/). O [Visual Studio Community Edition](https://visualstudio.microsoft.com/vs/community/) é gratuito. As instruções mostradas aqui são para o [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

## <a name="install-data-lake-tools-for-visual-studio"></a>Instalar as Ferramentas do Data Lake para Visual Studio  

Siga as instruções apropriadas para instalar o Data Lake Tools para sua versão do Visual Studio:

- Para Visual Studio 2017 ou Visual Studio 2019:

    Durante a instalação do Visual Studio, certifique-se de incluir a carga de trabalho de **desenvolvimento do Azure** ou a carga de trabalho de **processamento e armazenamento de dados** .  

    Para instalações existentes do Visual Studio, vá para a barra de menus do IDE e selecione **ferramentas** > **obter ferramentas e recursos** para abrir o instalador do Visual Studio. Na guia **cargas de trabalho** , selecione pelo menos a carga de trabalho de **desenvolvimento do Azure** (em **Web & Cloud**) ou a carga de trabalho de **armazenamento e processamento de dados** (em **outros conjuntos de ferramentas**).

  ![Seleção de carga de trabalho, Instalador do Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png)

- Para o Visual Studio 2015:

    [Baixe as ferramentas de data Lake](https://www.microsoft.com/download/details.aspx?id=49504). Escolha a versão do Data Lake Tools que corresponde à sua versão do Visual Studio.

## <a name="update-data-lake-tools-for-visual-studio"></a>Atualizar as ferramentas de Data Lake para o Visual Studio  

Em seguida, verifique se você atualizou as ferramentas de Data Lake para a versão mais recente.

1. Abra o Visual Studio.

2. Na janela **Iniciar** , selecione **continuar sem código**.

3. Na barra de menus do IDE do Visual Studio, escolha **extensões** > **gerenciar extensões**.

4. Na caixa de diálogo **gerenciar extensões** , expanda o nó **atualizações** .

5. Se a lista de atualizações disponíveis inclui **Azure data Lake e ferramentas analíticas de fluxo**, selecione-as. Em seguida, selecione o botão **Atualizar** . Depois que a caixa de diálogo **baixar e instalar** aparecer e desaparecer, o Visual Studio adicionará a Azure data Lake e a extensão de **ferramentas analíticas de fluxo** à agenda de atualização.

6. Feche todas as janelas do Visual Studio. A caixa de diálogo **instalador do VSIX** é exibida.

7. Selecione a **licença** para ler os termos de licença e selecione **fechar** para retornar à caixa de diálogo do **instalador do VSIX** .

8. Selecione **Modificar**. A instalação da atualização da extensão começa. Após alguns instantes, a caixa de diálogo é alterada para mostrar que isso é feito fazendo modificações. Selecione **fechar**e reinicie o Visual Studio para concluir a instalação.

> [!NOTE]  
> Pode utilizar apenas o Data Lake Tools versão 2.3.0.0 ou posterior para ligar a clusters da Consulta Interativa e executar consultas interativas do Hive.

## <a name="connect-to-azure-subscriptions"></a>Ligar a subscrições do Azure

Você pode usar as ferramentas de Data Lake para o Visual Studio para se conectar aos seus clusters HDInsight, realizar algumas operações básicas de gerenciamento e executar consultas Hive.

> [!NOTE]  
> Para obter informações sobre como se conectar a um cluster Hadoop genérico, consulte [como gravar e enviar consultas de Hive usando o Visual Studio](https://blogs.msdn.microsoft.com/xiaoyong/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio/).

### <a name="connect-to-an-azure-subscription"></a>Ligar a uma subscrição do Azure

Para ligar à sua subscrição do Azure:

1. Abra o Visual Studio.

2. Na janela **Iniciar** , selecione **continuar sem código**.

3. Na barra de menus do IDE, escolha **exibir** > **Gerenciador de servidores**.

4. Em **Gerenciador de servidores**, clique com o botão direito do mouse em **Azure**, selecione **conectar à assinatura do Microsoft Azure**e conclua o processo de autenticação. Em **Gerenciador de servidores**, expanda **Azure** > **hdinsight** para exibir uma lista de clusters HDInsight existentes.

5. Se você não tiver clusters, crie um usando o portal do Azure, Azure PowerShell ou o SDK do HDInsight. Para obter mais informações, consulte [Configurar clusters no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Lista de clusters HDInsight, Gerenciador de Servidores, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png)

6. Expanda um cluster do HDInsight. O cluster contém nós para **bancos de dados do hive**, uma conta de armazenamento padrão, todas as contas de armazenamento vinculadas adicionais e o **log do serviço do Hadoop**. Pode expandir ainda mais as entidades.

Depois de se ligar à sua subscrição do Azure, pode fazer as tarefas seguintes.

### <a name="connect-to-azure-from-visual-studio"></a>Conectar-se ao Azure por meio do Visual Studio

Ligar-se ao portal do Azure a partir do Visual Studio:

1. No **Gerenciador de servidores**, expanda o **Azure** > **HDInsight** e selecione o cluster.

2. Clique com o botão direito do mouse em um cluster HDInsight e selecione **gerenciar cluster em portal do Azure**.

### <a name="offer-questions-and-feedback-from-visual-studio"></a>Oferecer perguntas e comentários do Visual Studio

Para fazer perguntas e/ou fornecer comentários do Visual Studio:

1. Em Gerenciador de Servidores, escolha **Azure** > **HDInsight**.

2. Clique com o botão direito do mouse em **HDInsight** e selecione um **Fórum do MSDN** para fazer perguntas ou **forneça comentários** para fornecer comentários.

## <a name="link-to-or-edit-a-cluster"></a>Vincular ou editar um cluster

> [!NOTE]
> Atualmente, o único tipo de cluster HDInsight ao qual você pode vincular é um tipo de Hive.

Para vincular um cluster HDInsight:

1. Clique com o botão direito do mouse em **HDInsight**e selecione **vincular um cluster hdinsight** para exibir a caixa de diálogo **vincular um cluster hdinsight** .

2. Insira uma **URL de conexão** no formato *https\://\<nome&nbsp;do cluster >. azurehdinsight. net*. O **nome do cluster** preenche automaticamente com a parte do nome do cluster da URL quando você vai para outro campo. Em seguida, insira um **nome de usuário** e uma **senha**e selecione **Avançar**.

    ![Vincular um cluster, HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

3. Selecione **Concluir**. Se a vinculação do cluster for bem-sucedida, o cluster será listado no nó **HDInsight** .

Para atualizar um cluster vinculado, clique com o botão direito do mouse no cluster e selecione **Editar**. Em seguida, você pode atualizar as informações do cluster.

![Editar um cluster vinculado, HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Explorar recursos ligados
No Explorador de Servidores, pode ver a conta do Storage predefinida e quaisquer contas do Storage ligadas. Se expandir a conta do Storage predefinida, pode ver os contentores incluídos na mesma. A conta do Storage predefinida e o contentor predefinido estão marcados.

![Ferramentas de Data Lake para recursos vinculados do Visual Studio no Gerenciador de Servidores](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png)

Clique com o botão direito do mouse em um contêiner e selecione **Exibir contêiner** para exibir o conteúdo do contêiner. Depois de abrir um contêiner, você pode usar os botões da barra de ferramentas para **Atualizar** a lista de conteúdo, **carregar blob**, **excluir BLOBs selecionados**, **abrir blob**e baixar (**salvar como**) BLOBs selecionados.

![Lista de contêineres e operações de BLOB, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png)

## <a name="run-interactive-apache-hive-queries"></a>Executar consultas interativas de Apache Hive
O [Apache Hive](https://hive.apache.org) é uma infraestrutura de armazém de dados que está incorporada no Hadoop. O Hive é utilizado para resumo de dados, consultas e análises. Pode utilizar o Data Lake Tools para Visual Studio para executar consultas do Hive a partir do Visual Studio. Para obter mais informações sobre o Hive, consulte [o que é Apache Hive e HiveQL no Azure HDInsight?](hdinsight-use-hive.md).

A [consulta interativa no Azure HDInsight](../interactive-query/apache-interactive-query-get-started.md) usa o [Hive no LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) no Apache Hive 2,1. A consulta interativa traz interatividade para consultas de estilo data warehouse complexas em conjuntos de grandes e armazenados. A execução de consultas do hive na consulta interativa é muito mais rápida do que os trabalhos do lote do hive tradicional. 

> [!NOTE]  
> Pode executar consultas interativas do Hive apenas quando ligar a um cluster do [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md).

Também pode utilizar o Data Lake Tools para Visual Studio para ver o que está dentro de uma tarefa do Hive. O Data Lake Tools para Visual Studio recolhe e analisa os registos do Yarn de determinadas tarefas do Hive.

Em **Gerenciador de servidores**, escolha **Azure** > **HDInsight** e selecione o cluster.  Esse nó é o ponto de partida no **Gerenciador de servidores** para as seções seguirem.

### <a name="view-hivesampletable"></a>Exibir hivesampletable

Todos os clusters HDInsight têm uma tabela de hive de exemplo padrão chamada `hivesampletable`.  

No cluster, escolha **bancos de dados do Hive** > **padrão** > **hivesampletable**.

- Para exibir o esquema de `hivesampletable`:

    Expanda **hivesampletable**. Os nomes e os tipos de dados das colunas `hivesampletable` são mostrados.

- Para exibir os dados de `hivesampletable`:

    Clique com o botão direito do mouse em **hivesampletable**e selecione **exibir as primeiras 100 linhas**. A lista de resultados de 100 é exibida na janela **Hive: hivesampletable** . Essa ação é equivalente a executar a seguinte consulta de Hive usando o driver ODBC do hive:

    `SELECT * FROM hivesampletable LIMIT 100`

    Você pode personalizar a contagem de linhas alterando o **número de linhas**; Você pode escolher as linhas 50, 100, 200 ou 1000 na lista suspensa.

### <a name="create-hive-tables"></a>Criar tabelas do Hive
Para criar uma tabela do Hive, pode utilizar a GUI ou utilizar consultas do Hive. Para obter informações sobre como usar consultas do hive, consulte [criar e executar consultas do hive](#create-and-run-hive-queries).

1. No cluster, escolha **bancos de dados do Hive** > **padrão**.

2. Clique com o botão direito do mouse em **padrão**e selecione **criar tabela**.

3. Configure a tabela.

4. Selecione o botão **criar tabela** para enviar o trabalho, que cria a nova tabela Hive.

    ![Janela criar tabela, Hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png)

### <a name="create-and-run-hive-queries"></a>Criar e executar consultas do hive
Tem duas opções para criar e executar consultas do Hive:

* Criar consultas ad-hoc
* Criar uma aplicação do Hive

#### <a name="create-an-ad-hoc-query"></a>Criar uma consulta ad hoc

Para criar e executar uma consulta ad hoc:

1. Clique com o botão direito do mouse no cluster em que você deseja executar a consulta e selecione **gravar uma consulta do hive**.  

2. Insira uma consulta de Hive.

    O editor do Hive suporta IntelliSense. O Data Lake Tools para Visual Studio suportam o carregamento de metadados remotos durante a edição do script do Hive. Por exemplo, se você digitar `SELECT * FROM`, o IntelliSense listará todos os nomes de tabela sugeridos. Quando é especificado um nome de tabela, o IntelliSense lista os nomes das colunas. As ferramentas suportam quase todas as instruções DML do Hive, subconsultas e os UDFs incorporados.

    ![Exemplo do IntelliSense 1, consulta ad hoc do hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png)

    ![Exemplo 2 do IntelliSense, consulta ad hoc do hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png)

    > [!NOTE]  
    > O IntelliSense sugere apenas os metadados do cluster selecionado na barra de ferramentas do HDInsight.

    Aqui está uma consulta de exemplo que você pode usar:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

3. Escolha o modo de execução:

    * **Interativo**  

        Na primeira lista suspensa, escolha **interativo**e, em seguida, selecione **executar**.

        ![Modo interativo, consulta ad hoc do hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

        Na primeira lista suspensa, escolha **lote**e, em seguida, selecione **Enviar** (ou selecione o ícone suspenso ao lado de **Enviar** e escolha **avançado**).

        ![Modo de lote, consulta ad hoc do hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)

        Se você selecionar a opção de envio avançado, a caixa de diálogo **Enviar script** será exibida. Configure o **nome do trabalho**, os **argumentos**, **as configurações adicionais**e o **diretório de status** para o script.

        ![Caixa de diálogo Enviar script, consulta ad hoc do hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png)

      > [!NOTE]  
      > Você não pode enviar lotes para os clusters de consulta interativa.  Você deve usar o modo interativo.

#### <a name="create-a-hive-application"></a>Criar uma aplicação do Hive

Para criar e executar uma solução do Hive:

1. Na barra de menus, escolha **arquivo** > **novo** **projeto**de > .

2. Na janela **criar um novo projeto** , selecione a caixa Pesquisar e digite **Hive**. Em seguida, escolha **aplicativo Hive** e selecione **Avançar**.

3. Na janela **configurar seu novo projeto** , insira um **nome de projeto**, selecione ou crie o **local**do projeto e, em seguida, selecione **criar**.

    ![Novo aplicativo Hive, configurar sua nova janela de projeto, HDInsight Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png)

4. No **Explorador de Soluções**, faça duplo clique em **Script.hql** para abrir o script.

### <a name="view-job-summary-and-output"></a>Exibir Resumo e saída do trabalho

O resumo do trabalho varia ligeiramente entre o modo de **lote** e **interativo** .

![Resumo do trabalho do hive Windows, modo de lote e interativo, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png)

Use o ícone de **atualização** para atualizar o status até que o status do trabalho seja alterado para **concluído**.  

* Para obter os detalhes do trabalho no modo de **lote** , selecione os links na parte inferior para ver a **consulta do trabalho**, a **saída do trabalho**ou o log do **trabalho**ou para **exibir os logs do yarn**.

* Para obter detalhes do trabalho no modo **interativo** , consulte **os painéis saída e** saída **HiveServer2** .

    ![Saída de trabalho interativo do hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png)

### <a name="view-job-graph"></a>Exibir grafo de trabalho

Atualmente, os grafos de trabalho são mostrados apenas para trabalhos do hive que usam tez como o mecanismo de execução.  Para obter informações sobre como habilitar o tez, consulte [o que é Apache Hive e HiveQL no Azure HDInsight?](hdinsight-use-hive.md).  Consulte também, [Use Apache tez em vez da redução de mapa](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Para exibir todos os operadores dentro do vértice, clique duas vezes nos vértices do grafo do trabalho. Também pode apontar para um operador específico para ver mais detalhes sobre o operador.

Mesmo se tez for especificado como o mecanismo de execução, o grafo do trabalho poderá não aparecer se nenhum aplicativo tez for iniciado.  Essa situação pode ocorrer porque o trabalho não contém instruções DML ou porque as instruções DML podem retornar sem iniciar um aplicativo tez. Por exemplo, `SELECT * FROM table1` não iniciará o aplicativo tez.

![Apache Hive grafo de trabalho, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png)

### <a name="view-task-execution-detail"></a>Exibir detalhes de execução da tarefa

No grafo do trabalho, você pode selecionar **detalhes de execução da tarefa** para obter informações estruturadas e visualizadas para trabalhos do hive. Você também pode obter mais detalhes do trabalho. Se ocorrerem problemas de desempenho, pode utilizar a vista para obter mais detalhes sobre o problema. Por exemplo, você pode recuperar informações sobre como cada tarefa Opera e informações detalhadas sobre cada tarefa (leitura/gravação de dados, hora de agendamento/início/término e muito mais). Utilize as informações para otimizar as configurações da tarefa ou a arquitetura do sistema, com base nas informações visualizadas.

![Janela exibição de execução de tarefa, Data Lake Ferramentas do Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png)

### <a name="view-hive-jobs"></a>Ver tarefas do Hive

Pode ver as consultas da tarefa, a saída da tarefa, os registos da tarefa e os registos Yarn para as tarefas do Hive.

Na versão mais recente das ferramentas, pode ver o conteúdo das tarefas do Hive ao recolher e analisar registos Yarn. Um registo Yarn pode ajudar a investigar problemas de desempenho. Para obter mais informações sobre como o HDInsight coleta logs do yarn, consulte [acessar logs do aplicativo Apache HADOOP yarn](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Para ver tarefas do Hive:

1. Clique com o botão direito do mouse em um cluster HDInsight e selecione **exibir trabalhos**.

    ![Exibir trabalhos, Apache Hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png)

    É apresentada uma lista dos trabalhos do Hive executados no cluster.  

2. Selecione uma tarefa. Na janela **Resumo do trabalho do hive** , selecione um dos seguintes links:
    - **Consulta da Tarefa**
    - **Resultado da Tarefa**
    - **Registo da Tarefa**  
    - **Log do yarn**

## <a name="run-apache-pig-scripts"></a>Executar scripts do Apache Pig

1. Na barra de menus, escolha **arquivo** > **novo** **projeto**de > .

2. Na janela **Iniciar** , selecione a caixa Pesquisar e insira **Pig**. Em seguida, selecione **aplicativo Pig** e selecione **Avançar**.

3. Na janela **configurar seu novo projeto** , insira um **nome de projeto**e selecione ou crie um **local** para o projeto. Em seguida, selecione **Criar**.

4. No painel **Gerenciador de soluções** IDE, clique duas vezes em **script. Pig** para abrir o script.

## <a name="feedback-and-known-issues"></a>Comentários e problemas conhecidos

* Foi corrigido um problema no qual os resultados que são iniciados com valores nulos não são apresentados. Se estiver bloqueado neste problema, contacte a equipa de suporte.

* O script HQL criado pelo Visual Studio é codificado consoante a definição de região local do utilizador. O script não é executado corretamente se carregar o script para um cluster como um ficheiro binário.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a utilizar o pacote do Data Lake Tools para Visual Studio para ligar a clusters do HDInsight a partir do Visual Studio. Também aprendeu a executar uma consulta do Hive. Para obter mais informações, veja estes artigos:

* [Executar consultas do Apache Hive com as ferramentas do Data Lake para Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [O que é Apache Hive e HiveQL no Azure HDInsight?](hdinsight-use-hive.md)
* [Criar Apache Hadoop cluster-template](apache-hadoop-linux-tutorial-get-started.md)
* [Enviar Apache Hadoop trabalhos no HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analisar dados do Twitter usando Apache Hive e Apache Hadoop no HDInsight](../hdinsight-analyze-twitter-data-linux.md)
