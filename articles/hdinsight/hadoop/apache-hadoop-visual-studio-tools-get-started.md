---
title: Apache Hadoop & Ferramentas do Lago de Dados do Estúdio Visual - Azure HDInsight
description: Saiba como instalar e utilizar ferramentas do Data Lake para o Estúdio Visual. Utilize a ferramenta para ligar aos clusters Apache Hadoop em Azure HDInsight e, em seguida, executar consultas de Colmeia.
keywords: ferramentas do hadoop,consulta do hive,visual studio, visual studio hadoop
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: how-to
ms.date: 04/14/2020
ms.openlocfilehash: 8d8e9784ea21bf5f2b6902e3d93c5c09c1ec5670
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944572"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Use ferramentas do Data Lake para o Estúdio Visual ligar-se ao Azure HDInsight e executar consultas de Hive Apache

Saiba como utilizar o Microsoft Azure Data Lake e stream Analytics Tools for Visual Studio (Data Lake Tools). Utilize a ferramenta para ligar aos [clusters Apache Hadoop em Azure HDInsight](apache-hadoop-introduction.md) e submeter consultas de Hive.  

Para obter mais informações sobre a utilização de HDInsight, consulte [Começar com HDInsight](apache-hadoop-linux-tutorial-get-started.md).  

Para obter mais informações sobre a ligação à Tempestade Apache, consulte [as topologias de Desenvolvimento C# para a Tempestade Apache utilizando as ferramentas data lake](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Pode utilizar o Data Lake Tools para Visual Studio para aceder ao Azure Data Lake Analytics e ao HDInsight. Para obter informações sobre o Data Lake Tools, veja [Desenvolver scripts U-SQL com o Data Lake Tools para Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Pré-requisitos

Para completar este artigo e utilizar ferramentas do Data Lake para o Estúdio Visual, precisa dos seguintes itens:

* Um cluster do Azure HDInsight. Para criar um cluster HDInsight, consulte [Começar a usar Apache Hadoop em Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Para executar consultas interativas de Hive Apache, você precisa de um cluster [de consulta interativa HDInsight.](../interactive-query/apache-interactive-query-get-started.md)  

* [Estúdio Visual](https://visualstudio.microsoft.com/downloads/). A [edição da Comunidade Visual Studio](https://visualstudio.microsoft.com/vs/community/) é gratuita. As instruções aqui apresentadas são para [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

## <a name="install-data-lake-tools-for-visual-studio"></a>Instalar as Ferramentas do Data Lake para Visual Studio  

Siga as instruções apropriadas para instalar ferramentas do Data Lake para a sua versão do Visual Studio:

* Para Visual Studio 2017 ou Visual Studio 2019:

    Durante a instalação do Visual Studio, certifique-se de que inclui a carga de trabalho de **desenvolvimento do Azure** ou a carga de trabalho **de armazenamento e processamento de dados.**  

    Para as instalações existentes do Visual Studio, vá à barra de menus IDE e selecione **Tools** Get Tools and Features para abrir o  >   Instalador de Estúdio Visual. No **separador Workloads,** selecione pelo menos a carga de trabalho de **desenvolvimento do Azure** (em **Cloud web &).** Ou selecione a carga **de trabalho de armazenamento e processamento de dados** (em **outros conjuntos de ferramentas).**

  ![Seleção de carga de trabalho, Instalador de Estúdio Visual](./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png)

* Para Visual Studio 2015:

    [Descarregue ferramentas do Data Lake.](https://www.microsoft.com/download/details.aspx?id=49504) Escolha a versão do Data Lake Tools que corresponde à sua versão do Visual Studio.

## <a name="update-data-lake-tools-for-visual-studio"></a>Atualizar ferramentas do lago de dados para estúdio visual  

Em seguida, certifique-se de atualizar Data Lake Tools para a versão mais recente.

1. Abra o Visual Studio.

2. Na janela **Iniciar,** **selecione Continue sem código**.

3. Na barra de menus Visual Studio IDE, escolha **extensões**  >  **de gestão de extensões**.

4. Na caixa de diálogo **'Gestão de Extensões',** expanda o nó **de atualizações.**

5. Se a lista de atualizações disponíveis incluir **Azure Data Lake e Stream Analytic Tools,** selecione-a. Em seguida, selecione o botão **'Actualizar'.** Depois de a caixa de diálogo **de descarregamento e instalação** aparecer e desaparecer, o Visual Studio adiciona a extensão do **Lago de Dados Azure e das Ferramentas Analíticas stream** à programação de atualização.

6. Feche todas as janelas do Estúdio Visual. Aparece a caixa de diálogo **vsix installer.**

7. Selecione **Licença** para ler os termos da licença e, em seguida, selecione **Close** to return to the **VSIX Installer dialog.**

8. Selecione **Modificar**. Inicia-se a instalação da atualização de extensão. Passado algum tempo, a caixa de diálogo muda para mostrar que está feita a fazer modificações. Selecione **Close** e, em seguida, reinicie o Visual Studio para completar a instalação.

> [!NOTE]  
> Pode utilizar apenas o Data Lake Tools versão 2.3.0.0 ou posterior para ligar a clusters da Consulta Interativa e executar consultas interativas do Hive.

## <a name="connect-to-azure-subscriptions"></a>Ligar a subscrições do Azure

Você pode usar ferramentas do Data Lake para o Estúdio Visual para conectar-se aos seus clusters HDInsight, fazer algumas operações básicas de gestão e executar consultas de Hive.

> [!NOTE]  
> Para obter informações sobre a ligação a um cluster genérico de Hadoop, consulte [Como escrever e submeter consultas de Hive usando o Visual Studio](/archive/blogs/xiaoyong/how-to-write-and-submit-hive-queries-using-visual-studio).

### <a name="connect-to-an-azure-subscription"></a>Ligar a uma subscrição do Azure

Para ligar à sua subscrição do Azure:

1. Abra o Visual Studio.

2. Na janela **Iniciar,** **selecione Continue sem código**.

3. Na barra de menus IDE, escolha **Ver**  >  **Explorador do Servidor**.

4. No **Server Explorer**, click direito **Azure**, selecione Connect to Microsoft **Azure Subscription** e complete o processo de autenticação. A partir do **Server Explorer,** expanda **o Azure**  >  **HDInsight** para ver uma lista de clusters HDInsight existentes.

5. Se não tiver nenhum cluster, crie um utilizando o portal Azure PowerShell ou o HDInsight SDK. Para obter mais informações, consulte [Configurar clusters em HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Lista de clusters HDInsight, Explorador de Servidores, Estúdio Visual](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png)

6. Expanda um cluster do HDInsight. O cluster contém nós para **bases de dados de colmeias.** Além disso, uma conta de armazenamento padrão, quaisquer contas de armazenamento adicionais ligadas e **Registo de Serviço Hadoop**. Pode expandir ainda mais as entidades.

Depois de se ligar à sua subscrição do Azure, pode fazer as tarefas seguintes.

### <a name="connect-to-azure-from-visual-studio"></a>Ligue-se ao Azure do Visual Studio

Ligar-se ao portal do Azure a partir do Visual Studio:

1. No **Server Explorer,** expanda **o Azure**  >  **HDInsight** e selecione o seu cluster.

2. Clique com o botão direito num cluster HDInsight e **selecione Gerir o Cluster no portal Azure**.

### <a name="offer-questions-and-feedback-from-visual-studio"></a>Oferecer perguntas e feedback do Visual Studio

Para fazer perguntas e, ou fornecer feedback do Visual Studio:

1. A partir do Server Explorer, escolha **Azure**  >  **HDInsight**.

2. Clique com o botão direito **hdInsight** e selecione ou **o MSDN Forum** para fazer perguntas ou **dar feedback** para dar feedback.

## <a name="link-to-or-edit-a-cluster"></a>Ligue ou edite um cluster

> [!NOTE]
> Atualmente, o único tipo de cluster HDInsight a que se pode ligar é um tipo de Colmeia.

Para ligar um cluster HDInsight:

1. Clique com o botão direito **HDInsight** e, em seguida, selecione **Link a HDInsight Cluster** para exibir a caixa de diálogo Do Cluster **HDInsight.**

2. Introduza um **Url de ligação** no formulário `https://CLUSTERNAME.azurehdinsight.net` . O **Nome cluster** preenche automaticamente a parte do nome do cluster do seu URL quando vai para outro campo. Em **seguida, introduza** um nome de utilizador e **palavra-passe** e selecione **Seguinte**.

    ![Link um cluster, HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

3. Selecione **Concluir**. Se a ligação do cluster for bem sucedida, o cluster é então listado sob o nó **HDInsight.**

Para atualizar um cluster ligado, clique com o botão direito no cluster e selecione **Editar**. Em seguida, pode atualizar a informação do cluster.

![Editar um cluster ligado, HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Explorar recursos ligados

No Explorador de Servidores, pode ver a conta do Storage predefinida e quaisquer contas do Storage ligadas. Se expandir a conta do Storage predefinida, pode ver os contentores incluídos na mesma. A conta do Storage predefinida e o contentor predefinido estão marcados.

![Ferramentas do Lago de Dados para Estúdio Visual ligaram recursos no Explorador de Servidor](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png)

Clique com o botão direito num recipiente e selecione **Ver O Recipiente** para ver o conteúdo do recipiente. Depois de abrir um recipiente, pode utilizar os botões da barra de ferramentas para **refrescar** a lista de conteúdos, **carregar blobs,** **eliminar bolhas selecionadas,** **Abrir Blob,** e baixar **(Save As**) blobs selecionados.

![Lista de contentores e operações de blob, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png)

## <a name="run-interactive-apache-hive-queries"></a>Executar consultas interativas de Colmeia Apache

O [Apache Hive](https://hive.apache.org) é uma infraestrutura de armazém de dados que está incorporada no Hadoop. O Hive é utilizado para resumo de dados, consultas e análises. Pode utilizar o Data Lake Tools para Visual Studio para executar consultas do Hive a partir do Visual Studio. Para mais informações sobre a Colmeia, veja [o que é Apache Hive e HiveQL em Azure HDInsight?](hdinsight-use-hive.md)

[A Consulta Interativa em Azure HDInsight](../interactive-query/apache-interactive-query-get-started.md) utiliza [a Colmeia em LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) em Apache Hive 2.1. A Consulta Interativa traz interatividade a consultas complexas, estilo armazém de dados, em conjuntos de dados grandes e armazenados. Executar consultas de Colmeia em Consulta Interativa é muito mais rápido do que os trabalhos tradicionais do lote de Colmeia. 

> [!NOTE]  
> Pode executar consultas interativas do Hive apenas quando ligar a um cluster do [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md).

Também pode usar ferramentas do Data Lake para o Visual Studio para ver o que está dentro de um trabalho de Colmeia. O Data Lake Tools para Visual Studio recolhe e analisa os registos do Yarn de determinadas tarefas do Hive.

A partir do **Server Explorer,** escolha **Azure**  >  **HDInsight** e selecione o seu cluster.  Este nó é o ponto de partida no **Server Explorer** para as secções a seguir.

### <a name="view-hivesampletable"></a>Veja hivesampletable

Todos os clusters HDInsight têm uma amostra padrão da tabela Hive chamada `hivesampletable` .  

A partir do seu cluster, escolha **bases de dados** de hive  >  **padrão**  >  **padrão hivesmpletable**.

* Para ver o `hivesampletable` esquema:

    Expandir **o hivesmpletable**. Os nomes e tipos de dados das `hivesampletable` colunas são mostrados.

* Para ver os `hivesampletable` dados:

    Clique à direita **para sermopletable** e selecione **Ver Top 100 Rows**. A lista de 100 resultados aparece na Tabela de Colmeia: janela **hivesampletable.** Esta ação equivale a executar a seguinte consulta de Hive utilizando o condutor hive ODBC:

    `SELECT * FROM hivesampletable LIMIT 100`

    Pode personalizar a contagem de linha alterando **o Número de linhas;** pode escolher 50, 100, 200 ou 1000 filas da lista de desistentes.

### <a name="create-hive-tables"></a>Criar tabelas do Hive

Para criar uma tabela do Hive, pode utilizar a GUI ou utilizar consultas do Hive. Para obter informações sobre a utilização de consultas de Colmeia, consulte [Criar e executar consultas de Colmeia.](#create-and-run-hive-queries)

1. A partir do seu cluster, escolha o padrão **das Bases de Dados da Colmeia**  >  .

2. Clique no **botão direito predefinido** e selecione **Criar Tabela**.

3. Configure a tabela.

4. Selecione o botão **'Criar Tabela'** para submeter o trabalho, que cria a nova tabela Hive.

    ![Criar janela de mesa, Hive, cluster HDInsight, Estúdio Visual](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png)

### <a name="create-and-run-hive-queries"></a>Criar e executar consultas de Colmeia

Tem duas opções para criar e executar consultas do Hive:

* Criar consultas ad-hoc
* Criar uma aplicação do Hive

#### <a name="create-an-ad-hoc-query"></a>Criar uma consulta ad-hoc

Para criar e executar uma consulta ad-hoc:

1. Clique com o botão direito no cluster onde pretende executar a consulta e **selecione Escreva uma Consulta de Colmeia**.  

2. Insira uma consulta de Colmeia.

    O editor do Hive suporta IntelliSense. O Data Lake Tools para Visual Studio suportam o carregamento de metadados remotos durante a edição do script do Hive. Por exemplo, se `SELECT * FROM` escrever, o IntelliSense lista todos os nomes de tabelas sugeridos. Quando é especificado um nome de tabela, o IntelliSense lista os nomes das colunas. As ferramentas suportam quase todas as instruções DML do Hive, subconsultas e os UDFs incorporados.

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

        Na primeira lista de drop-down, escolha **Interactive** e, em seguida, selecione **Executar**.

        ![Modo interativo, consulta ad-hoc da Hive, cluster HDInsight, Estúdio Visual](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

        Na primeira lista de drop-down, escolha **Batch** e, em seguida, selecione **Enviar por isso**. Ou selecione o ícone drop-down ao lado de **Submeter** e escolher **Advanced**.

        ![Modo lote, consulta ad-hoc da Hive, cluster HDInsight, Estúdio Visual](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)

        Se selecionar a opção de submissão avançada, aparece a caixa de diálogo **'Enviar script'.** Configure o **Nome do Trabalho,** **Argumentos,** **Configurações Adicionais** e **Diretório de Estado** para o script.

        ![Envie a caixa de diálogo script, consulta ad-hoc da Hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png)

      > [!NOTE]  
      > Não pode enviar lotes para agrupamentos de consultas interativos.  Deve utilizar o modo interativo.

#### <a name="create-a-hive-application"></a>Criar uma aplicação do Hive

Para criar e executar uma solução do Hive:

1. A partir da barra de menu, escolha **File**  >  **New**  >  **Project**.

2. Na **janela criar uma nova** janela de projeto, selecione a caixa de pesquisa e **digite Hive**. Em seguida, escolha **a Aplicação Hive** e selecione **Seguinte**.

3. Na janela **Configure** a sua nova janela do projeto, insira um **nome de Projeto,** selecione ou crie a **Localização** do projeto e, em seguida, selecione **Criar**.

    ![Nova aplicação Hive, Configure a sua nova janela de projeto, HDInsight Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png)

4. No **Explorador de Soluções**, faça duplo clique em **Script.hql** para abrir o script.

### <a name="view-job-summary-and-output"></a>Ver resumo de trabalho e saída

O resumo do trabalho varia ligeiramente entre o modo **Batch** e o modo **Interativo.**

![Janelas de resumo de trabalho de colmeia, modo lote e interativo, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png)

Utilize o ícone **Refresh** para atualizar o estado até que o estado do trabalho mude para **terminado**.  

* Para os detalhes do trabalho no modo **Batch,** selecione os links na parte inferior para ver a Consulta de **Trabalho,** Saída de **Emprego** ou **Log de Trabalho,** ou para Ver **Registos de Fios**.

* Para obter os detalhes do trabalho do modo **Interactive,** consulte os painéis **de saída Output** e **HiveServer2.**

    ![Produção de emprego interativo de Hive, cluster HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png)

### <a name="view-job-graph"></a>Ver gráfico de trabalho

Atualmente, os gráficos de trabalho só são mostrados para trabalhos de Colmeia que usam Tez como motor de execução.  Para obter informações sobre como ativar o Tez, veja [o que é Apache Hive e HiveQL em Azure HDInsight?](hdinsight-use-hive.md)  Consulte também, [Use Apache Tez em vez de Map Reduce](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Para ver todos os operadores dentro do vértice, clique duas vezes nos vértices do gráfico de trabalho. Também pode apontar para um operador específico para ver mais detalhes sobre o operador.

Mesmo que o Tez seja especificado como o motor de execução, o gráfico de trabalho pode não aparecer se não for lançada nenhuma aplicação Tez.  Esta situação pode ocorrer porque o trabalho não contém declarações de DML. Ou porque as declarações da DML podem regressar sem lançar uma aplicação Tez. Por exemplo, `SELECT * FROM table1` não vai lançar a aplicação Tez.

![Gráfico de trabalho da Colmeia Apache, Estúdio Visual](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png)

### <a name="view-task-execution-detail"></a>Ver detalhes de execução de tarefas

A partir do gráfico de trabalho, pode selecionar **Detalhes de Execução de Tarefas** para obter informações estruturadas e visualizadas para trabalhos de Colmeia. Também pode obter mais detalhes sobre o emprego. Se ocorrerem problemas de desempenho, pode utilizar a vista para obter mais detalhes sobre o problema. Por exemplo, pode obter informações sobre o funcionamento de cada tarefa e informações detalhadas sobre cada tarefa (leitura/escrita de dados, agendamento/início/fim, entre outras). Utilize as informações para otimizar as configurações da tarefa ou a arquitetura do sistema, com base nas informações visualizadas.

![Janela de visualização de execução de tarefas, ferramentas de estúdio visual do lago de dados](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png)

### <a name="view-hive-jobs"></a>Ver tarefas do Hive

Pode ver as consultas da tarefa, a saída da tarefa, os registos da tarefa e os registos Yarn para as tarefas do Hive.

Na versão mais recente das ferramentas, você pode ver o que está dentro dos seus trabalhos de Colmeia, recolhendo e superando troncos de Yarn. Um registo Yarn pode ajudar a investigar problemas de desempenho. Para obter mais informações sobre como o HDInsight recolhe registos de Fios, consulte [os registos de aplicações do Access Apache Hadoop YARN](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Para ver tarefas do Hive:

1. Clique com o botão direito num cluster HDInsight e selecione **Ver Empregos**.

    ![Ver Jobs, Apache Hive, HDInsight cluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png)

    É apresentada uma lista dos trabalhos do Hive executados no cluster.  

2. Selecione uma tarefa. Na janela **Hive Job Summary,** selecione um dos seguintes links:
    - **Consulta da Tarefa**
    - **Resultado da Tarefa**
    - **Registo da Tarefa**  
    - **Log de fios**

## <a name="run-apache-pig-scripts"></a>Executar scripts de porco Apache

1. A partir da barra de menu, escolha **File**  >  **New**  >  **Project**.

2. Na janela **Iniciar,** selecione a caixa de pesquisa e **introduza o Pig**. Em seguida, selecione **Aplicação do Porco** e selecione **Seguinte**.

3. Na **janela Configure a sua nova** janela do projeto, insira um nome de **Projeto,** e selecione ou crie uma **Localização** para o projeto. Em seguida, selecione **Criar**.

4. No painel IDE **Solution Explorer,** clique duas vezes em **Script.pig** para abrir o script.

## <a name="feedback-and-known-issues"></a>Comentários e problemas conhecidos

* Foi corrigido um problema no qual os resultados que são iniciados com valores nulos não são apresentados. Se estiver bloqueado neste problema, contacte a equipa de suporte.

* O script HQL que o Visual Studio cria está codificado, dependendo da configuração da região local do utilizador. O script não é executado corretamente se carregar o script para um cluster como um ficheiro binário.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a utilizar o pacote do Data Lake Tools para Visual Studio para ligar a clusters do HDInsight a partir do Visual Studio. Também aprendeu a executar uma consulta do Hive. 

* [Executar consultas do Apache Hive com as ferramentas do Data Lake para Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [O que é Apache Hive e HiveQL em Azure HDInsight?](hdinsight-use-hive.md)
* [Criar cluster do Apache Hadoop - Modelo](apache-hadoop-linux-tutorial-get-started.md)
* [Submeta empregos da Apache Hadoop em HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analise os dados do Twitter usando Apache Hive e Apache Hadoop em HDInsight](../hdinsight-analyze-twitter-data-linux.md)