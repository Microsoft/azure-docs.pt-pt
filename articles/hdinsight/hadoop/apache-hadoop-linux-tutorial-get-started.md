---
title: 'Início rápido: Criar clusters do Apache Hadoop com o Resource Manager e consultar dados com o Apache Hive - Azure HDInsight'
description: Saiba como criar clusters do HDInsight e consultar dados com o Hive.
keywords: guia de introdução do hadoop, hadoop linux, início rápido do hadoop, introdução do hive, início rápido do hive
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 12/27/2018
ms.openlocfilehash: 9400753d50eff84512448b3d46b50c924d00d182
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57446066"
---
# <a name="quickstart-get-started-with-apache-hadoop-and-apache-hive-in-azure-hdinsight-using-resource-manager-template"></a>Início rápido: Introdução ao Apache Hadoop e Apache Hive no HDInsight do Azure com o modelo do Resource Manager

Neste artigo, irá aprender a criar [Apache Hadoop](https://hadoop.apache.org/) clusters no HDInsight com um modelo do Resource Manager e, em seguida, executar tarefas do Hive no HDInsight. A maioria das tarefas do Hadoop são tarefas de lote. Cria um cluster, executa algumas tarefas e, em seguida, elimina o cluster. Neste artigo, irá realizar as três tarefas.

Neste início rápido, vai utilizar um modelo do Resource Manager para criar um cluster do Hadoop no HDInsight. Também pode criar um cluster com o [Portal do Azure](apache-hadoop-linux-create-cluster-get-started-portal.md).  Modelos semelhantes podem ser visualizados no [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular). A referência de modelo pode ser encontrada [aqui](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/allversions).

Atualmente, o HDInsight inclui [sete tipos diferentes de cluster](./apache-hadoop-introduction.md#cluster-types-in-hdinsight). Cada tipo de cluster suporta um conjunto diferente de componentes. Todos os tipos de cluster suportam o Hive. Para obter uma lista dos componentes suportados no HDInsight, consulte [Quais são as novidades nas versões de cluster do Hadoop fornecidas pelo HDInsight?](../hdinsight-component-versioning.md)  

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

<a name="create-cluster"></a>
## <a name="create-a-hadoop-cluster"></a>Criar um cluster do Hadoop

Nesta secção, vai criar um cluster do Hadoop no HDInsight com um modelo do Azure Resource Manager. Não precisa de ter experiência no modelo do Resource Manager para seguir este artigo. 

1. Clique no botão **Implementar no Azure** abaixo para iniciar sessão no Azure abrir o modelo do Resource Manager no portal do Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Introduza ou selecione os valores conforme sugerido na seguinte captura de ecrã:

    > [!NOTE]  
    > Os valores que forem fornecidos têm de ser exclusivos e devem seguir as diretrizes de nomenclatura. O modelo não realiza verificações de validação. Caso os valores fornecidos já estejam a ser utilizados ou não sigam as diretrizes, receberá um erro depois de ter submetido o modelo.    
    
    ![Linux de HDInsight obtém o modelo de Gestor de recursos de introdução no portal](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "cluster de implementar o Hadoop no Hdinsight com o portal do Azure e um modelo de Gestor do grupo de recursos")

    Introduza ou selecione os seguintes valores:
    
    |Propriedade  |Descrição  |
    |---------|---------|
    |**Subscrição**     |  Selecione a sua subscrição do Azure. |
    |**Grupo de recursos**     | Crie um grupo de recursos ou selecione um existente.  Um grupo de recursos é um contentor de componentes do Azure.  Neste caso, o grupo de recursos contém o cluster do HDInsight e a conta de armazenamento do Azure dependente. |
    |**Localização**     | Selecione uma localização do Azure onde quer criar o cluster.  Selecione uma localização mais próxima de si para obter um melhor desempenho. |
    |**Nome do Cluster**     | Introduza um nome para o cluster do Hadoop. Uma vez que todos os clusters no HDInsight partilham o mesmo espaço de nomes DNS, este nome tem de ser único. O nome pode conter apenas letras minúsculas, números e hífenes e tem de começar com uma letra.  Cada hífen tem de ser precedido e seguido de um caráter que não seja um hífen.  O nome também tem de ter entre 3 e 59 carateres de comprimento. |
    |**Tipo de Cluster**     | Selecione **hadoop**. |
    |**Nome e palavra-passe de início de sessão no cluster**     | O nome de início de sessão predefinido é **admin**. A palavra-passe tem de ter no mínimo 10 carateres e tem de conter, pelo menos, um número, uma letra maiúscula e uma letra minúscula, e um caráter não alfanumérico (exceto os carateres ' " `\). Certifique-se de que **não escolhe** uma palavra-passe comum, tal como "Pass@word1".|
    |**Nome de utilizador e palavra-passe de SSH**     | O nome de utilizador predefinido é **sshuser**.  Pode mudar o nome de utilizador do SSH.  A palavra-passe de utilizador do SSH tem os mesmos requisitos que a palavra-passe de início de sessão do cluster.|
       
    Algumas propriedades foram codificadas no modelo.  Pode configurar estes valores a partir do modelo. Para obter mais explicações sobre estas propriedades, consulte [Apache Hadoop criar clusters no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

3. Selecione **concordo com os termos e condições indicados acima**e, em seguida, selecione **Compra**. Receberá uma notificação que sua implementação está em curso.  A criação de um cluster demora cerca de 20 minutos.

4. Assim que o cluster é criado, receberá um **implementação concluída com êxito** notificação com uma **vá para o grupo de recursos** ligação.  Sua **grupo de recursos** página irá listar o novo cluster do HDInsight e o armazenamento de predefinido associada ao cluster. Cada cluster tem um [conta de armazenamento do Azure](../hdinsight-hadoop-use-blob-storage.md) ou uma [conta de armazenamento do Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md) dependência. É designada de conta de armazenamento predefinida. O cluster do HDInsight e a sua conta do storage predefinida tem de estar localizados conjuntamente na mesma região do Azure. A eliminação dos clusters não elimina a conta de armazenamento.

> [!NOTE]  
> Para conhecer outros métodos de criação de clusters e compreender as propriedades utilizadas neste tutorial, consulte [Criar clusters do HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).       


## <a name="use-vscode-to-run-hive-queries"></a>Utilizar o VSCode para executar consultas do Hive

Para saber como obter as Ferramentas do HDInsight no VSCode, veja [Utilizar Ferramentas do HDInsight para o Visual Studio Code](../hdinsight-for-vscode.md).

### <a name="submit-interactive-hive-queries"></a>Submeter consultas interativas do Hive

Com as Ferramentas do HDInsight para o VSCode, pode submeter consultas do Hive interativas para clusters de consulta interativa do HDInsight.

1. Crie uma nova pasta de trabalho e um novo ficheiro de script do Hive, se ainda não os tiver.

2. Ligue-se à sua conta do Azure e, em seguida, configure o cluster predefinido, se ainda não o tiver feito.

3. Copie e cole o seguinte código para o ficheiro do Hive e guarde-o.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
4. O editor de scripts com o botão direito e, em seguida, selecione **HDInsight: Hive interativo** para submeter a consulta. As ferramentas permitem-lhe também submeter um bloco de código em vez de todo o ficheiro de script através do menu de contexto. Logo depois, os resultados da consulta são apresentados num novo separador.

   ![Resultado do Hive interativo](./media/apache-hadoop-linux-tutorial-get-started/interactive-hive-result.png)

    - **RESULTADOS** painel: Pode guardar o resultado inteiro como ficheiro CSV, JSON ou o Excel para o caminho local ou, basta selecionar várias linhas.

    - **MENSAGENS** painel: Quando seleciona **linha** número, ele salta para a primeira linha do script em execução.

Executar a consulta interativa demora muito menos tempo do que [em execução uma tarefa do batch Apache Hive](#submit-hive-batch-scripts).

### <a name="submit-hive-batch-scripts"></a>Submeter scripts de lote do Hive

1. Crie uma nova pasta de trabalho e um novo ficheiro de script do Hive, se ainda não os tiver.

2. Ligue-se à sua conta do Azure e, em seguida, configure o cluster predefinido, se ainda não o tiver feito.

3. Copie e cole o seguinte código para o ficheiro do Hive e guarde-o.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
4. O editor de scripts com o botão direito e, em seguida, selecione **HDInsight: Hive Batch** para submeter uma tarefa do Hive. 

5. Selecione o cluster para o qual pretende submeter.  

    Depois de submeter uma tarefa do Hive, as informações de êxito de submissão e o ID da tarefa são apresentados no painel **SAÍDA**. A tarefa do Hive também abre o **BROWSER**, que mostra o estado e os registos da tarefa em tempo real.

   ![submeter o resultado da tarefa do Hive](./media/apache-hadoop-linux-tutorial-get-started/submit-Hivejob-result.png)

[Submeter consultas do Apache Hive interativas](#submit-interactive-hive-queries) demora muito menos tempo do que submeter uma tarefa do batch.

## <a name="use-visualstudio-to-run-hive-queries"></a>Utilizar o VisualStudio para executar consultas do Hive

Para saber como obter as Ferramentas do HDInsight no Visual Studio, veja [Utilizar as Ferramentas do Data Lake para o Visual Studio](./apache-hadoop-visual-studio-tools-get-started.md).

### <a name="run-hive-queries"></a>Executar consultas do Hive

Tem duas opções para criar e executar consultas do Hive:

* Criar consultas ad hoc
* Criar uma aplicação do Hive

Para criar e executar consultas ad hoc:

1. No **Explorador de Servidores**, selecione **Azure** > **Clusters do HDInsight**.

2. Clique com o botão direito do rato no cluster no qual pretende executar a consulta e, em seguida, selecione **Escrever uma Consulta do Hive**.  

3. Introduza as consultas do Hive. 

    O editor do Hive suporta IntelliSense. O Data Lake Tools para Visual Studio suportam o carregamento de metadados remotos durante a edição do script do Hive. Por exemplo, quando escreve **SELECT * FROM**, o IntelliSense apresenta uma lista de todos os nomes de tabela sugeridos. Quando é especificado um nome de tabela, o IntelliSense lista os nomes das colunas. As ferramentas suportam quase todas as instruções DML do Hive, subconsultas e os UDFs incorporados.
   
    ![Captura de ecrã de um exemplo do IntelliSense do HDInsight Visual Studio Tools IntelliSense 1](./media/apache-hadoop-linux-tutorial-get-started/vs-intellisense-table-name.png "U-SQL IntelliSense")
   
    ![Captura de ecrã de um exemplo do IntelliSense do HDInsight Visual Studio Tools IntelliSense 2](./media/apache-hadoop-linux-tutorial-get-started/vs-intellisense-column-name.png "U-SQL IntelliSense")
   
   > [!NOTE]  
   > O IntelliSense sugere apenas os metadados do cluster selecionado na barra de ferramentas do HDInsight.
   > 
   
4. Selecione **Submeter** ou **Submeter (Avançado)**. 
   
    ![Captura de ecrã da submissão de uma consulta do Hive](./media/apache-hadoop-linux-tutorial-get-started/vs-batch-query.png)

   Se selecionar a opção de submissão avançada, configura o **Nome da Tarefa**, **Argumentos**, **Configurações Adicionais** e **Diretório de Estado** para o script:

    ![Captura de ecrã da consulta do Hive do Hadoop HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Submeter consultas")

   Executar consultas interativas do Hive

   * Clique na seta para baixo para escolher **interativo**. 
   
   * Clique em **Executar**.

   ![Captura de ecrã de Executar consultas interativas do Hive](./media/apache-hadoop-linux-tutorial-get-started/vs-execute-hive-query.png)

Para criar e executar uma solução do Hive:

1. No menu **Ficheiro**, selecione **Novo** e, em seguida, selecione **Projeto**.
2. No painel da esquerda, selecione **HDInsight**. No painel do meio, selecione **Aplicação do Hive**. Introduza as propriedades e, em seguida, selecione **OK**.
   
    ![Captura de ecrã de um novo projeto do Hive no HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png "Criar aplicações do Hive do Visual Studio")
3. No **Explorador de Soluções**, faça duplo clique em **Script.hql** para abrir o script.
4. Introduza as consultas do Hive e submeta. (Consulte o passo 3 e 4 acima)  



## <a name="run-hive-queries"></a>Executar consultas do Hive

O [Apache Hive](hdinsight-use-hive.md) é o componente mais popular utilizado no HDInsight. Existem várias formas de executar tarefas do Hive no HDInsight. Neste tutorial, vai utilizar a vista Ambari Hive do portal. Para outros métodos para submeter tarefas do Hive, consulte [utilizar o Apache Hive no HDInsight](hdinsight-use-hive.md).

1. Para abrir o Ambari, a partir da **dashboards de clusters** mosaico, selecione **vistas Ambari**.  Também pode navegar até **https://&lt;ClusterName&gt;. azurehdinsight.net**, onde &lt;ClusterName&gt; é o cluster que criou na secção anterior.

    ![Introdução ao dashboard de clusters no HDInsight com Linux](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-open-cluster-dashboard.png "Introdução ao dashboard de clusters no HDInsight com Linux")

2. Introduza o nome de utilizador e a palavra-passe do Hadoop que especificou ao criar o cluster. O nome de utilizador predefinido é **admin**.

3. Selecione **2.0 de vista do Hive** conforme mostrado na captura de ecrã seguinte:
   
    ![Selecionar vistas Ambari](./media/apache-hadoop-linux-tutorial-get-started/selecthiveview.png "Menu do Ambari Hive Viewer")

4. No separador **CONSULTAS**, cole as seguintes declarações HiveQL na folha de cálculo:
   
        SHOW TABLES;

    ![Vistas do Hive do HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview-1.png "Editor de Consulta do Hive View do HDInsight")
   
   > [!NOTE]  
   > O Hive requer a utilização do ponto e vírgula.       


5. Selecione **Executar**. O separador **RESULTADOS** aparece por baixo do separador **CONSULTA** e apresenta informações sobre a tarefa. 
   
    Assim que a consulta estiver concluída, o **consulta** guia exibe os resultados da operação. Deverá ver uma tabela denomizada **hivesampletable**. Esta tabela do Hive de exemplo inclui todos os clusters do HDInsight.
   
    ![Vistas do Hive do HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview.png "Editor de Consulta do Hive View do HDInsight")

6. Repita os passos 4 e 5 para executar a seguinte consulta:
   
        SELECT * FROM hivesampletable;
   
7. Também pode guardar os resultados da consulta. Selecione o botão de menu à direita e especifique se quer transferir os resultados como um ficheiro CSV ou armazená-los na conta de armazenamento associada ao cluster.

    ![Guardar o resultado da consulta do Hive](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-hive-view-save-results.png "Guardar o resultado da consulta do Hive")

Depois de concluir uma tarefa deo Hive, pode [exportar os resultados para a SQL Database do Azure ou a base de dados do SQL Server](apache-hadoop-use-sqoop-mac-linux.md). Do mesmo modo, pode [visualizar os resultados com o Excel](apache-hadoop-connect-excel-power-query.md). Para obter mais informações sobre como utilizar o Hive no HDInsight, consulte [utilizar o Apache Hive e o HiveQL com o Apache Hadoop no HDInsight, para analisar um ficheiro de exemplo Apache log4j](hdinsight-use-hive.md).

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="clean-up-resources"></a>Limpar recursos
Depois de concluir o artigo, pode achar conveniente eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados. 

> [!NOTE]  
> Se avançar *imediatamente* para o tutorial seguinte para saber como executar operações de ETL com o Hadoop no HDInsight, é recomendado que mantenha o cluster em execução. Isto porque tem de voltar a criar um cluster do Hadoop no tutorial. No entanto, se não avançar já para o tutorial seguinte, tem de eliminar o cluster agora.

**Para eliminar o cluster e/ou a conta de armazenamento predefinida**

1. Volte ao separador do browser onde tem o portal do Azure. Deverá estar na página de descrição geral do cluster. Se apenas quiser eliminar o cluster, mas quiser manter a conta de armazenamento predefinida, selecione **Eliminar**.

    ![Eliminação de cluster do HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "Eliminar cluster do HDInsight")

2. Se quiser eliminar o cluster e a conta de armazenamento predefinida, selecione o nome do grupo de recursos (realçado na captura de ecrã anterior) para abrir a página do grupo de recursos.

3. Selecione **Eliminar grupo de recursos** para eliminar o grupo de recursos, o qual contém o cluster e a conta de armazenamento predefinida. Tenha em atenção que a eliminação do grupo de recursos elimina a conta de armazenamento. Se pretender manter a conta do Storage, opte por eliminar apenas o cluster.

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu a criar um cluster do HDInsight baseado em Linux com um modelo do Resource Manager, bem como a realizar consultas básicas do Hive. No artigo seguinte, irá saber como executar uma operação de ETL (extração, transformação e carregamento) com o Hadoop no HDInsight.

> [!div class="nextstepaction"]
>[Extrair, transformar e carregar dados com o Apache Hive no HDInsight](../hdinsight-analyze-flight-delay-data-linux.md)

Se estiver pronto para começar a trabalhar com os seus próprios dados e precisar de saber mais sobre a forma como o HDInsight armazena os dados ou como carregar dados para o HDInsight, consulte os seguintes artigos:

* Para obter informações sobre como o HDInsight utiliza o Armazenamento do Azure, consulte [Utilizar o Armazenamento do Azure com o HDInsight](../hdinsight-hadoop-use-blob-storage.md).
* Para obter informações sobre como criar um cluster do HDInsight com o armazenamento do Data Lake, veja [início rápido: Configurar clusters no HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* Para obter informações sobre como carregar dados para o HDInsight, veja [Upload data to HDInsight](../hdinsight-upload-data.md) (Carregar dados para o HDInsight).
* [Utilize a geração 2 de armazenamento do Azure Data Lake com clusters do HDInsight do Azure](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

Saiba mais sobre como analisar os dados com o HDInsight, veja os seguintes artigos:

* Para saber mais sobre como utilizar o Hive com HDInsight, incluindo como executar consultas do Hive a partir do Visual Studio, veja [utilizar o Apache Hive com HDInsight](hdinsight-use-hive.md).
* Para saber mais sobre o Pig, uma linguagem utilizada para transformar dados, veja [utilizar o Apache Pig com o HDInsight](hdinsight-use-pig.md).
* Para saber mais sobre o MapReduce, uma forma de escrever programas que processam dados no Hadoop, veja [Use MapReduce with HDInsight](hdinsight-use-mapreduce.md) (Utilizar o MapReduce com o HDInsight).
* Para saber mais sobre como utilizar as Ferramentas do HDInsight para o Visual Studio para analisar dados no HDInsight, consulte [Começar a utilizar as ferramentas Hadoop do Visual Studio para o HDInsight](apache-hadoop-visual-studio-tools-get-started.md).
* Para saber mais sobre como utilizar as Ferramentas do HDInsight para o VSCode para analisar dados no HDInsight, consulte [Utilizar as Ferramentas do HDInsight para o Visual Studio Code](../hdinsight-for-vscode.md).


Se gostaria de saber mais sobre como criar ou gerir um cluster do HDInsight, consulte os seguintes artigos:

* Para saber mais sobre a gestão do seu cluster do HDInsight baseado em Linux, veja [clusters do HDInsight gerir com o Apache Ambari](../hdinsight-hadoop-manage-ambari.md).
* Para saber mais sobre as opções que pode selecionar ao criar um cluster do HDInsight, consulte [Criar HDInsight no Linux utilizando opções personalizadas](../hdinsight-hadoop-provision-linux-clusters.md).

Para saber mais sobre a criação de cluster do HDInsight com modelos Azure Resource Manager, consulte:

* [Modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular).
* [Referência de modelo do Azure](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/allversions).

[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
