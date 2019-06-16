---
title: Apache Hive com ferramentas do Data Lake (Apache Hadoop) para o Visual Studio - Azure HDInsight
description: Saiba como utilizar as ferramentas do Data Lake para Visual Studio para executar consultas do Apache Hive com o Apache Hadoop no HDInsight do Azure.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: hrasheed
ms.openlocfilehash: 7480dafe435e555bfba81ebd9242bb5724c0bf3f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65861602"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Executar consultas do Apache Hive com as ferramentas do Data Lake para Visual Studio

Saiba como utilizar as ferramentas do Data Lake para Visual Studio para consultar Apache Hive. As ferramentas do Data Lake permitem-lhe facilmente criar, submeter e monitorizar consultas do Hive para Apache Hadoop no HDInsight do Azure.

## <a id="prereq"></a>Pré-requisitos

* Um cluster do Apache Hadoop no HDInsight. Ver [introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Visual Studio (uma das seguintes versões):

    * Visual Studio 2015, 2017 (qualquer edição)
    * Visual Studio 2013 Community/Professional/Premium/Ultimate com a atualização 4

* Ferramentas do HDInsight para Visual Studio ou o Azure Data Lake tools para Visual Studio. Ver [começar a utilizar as ferramentas Hadoop do Visual Studio para o HDInsight](apache-hadoop-visual-studio-tools-get-started.md) para obter informações sobre como instalar e configurar as ferramentas.

## <a id="run"></a> Executar consultas do Apache Hive com o Visual Studio

Tem duas opções para criar e executar consultas do Hive:

* Criar consultas ad hoc
* Criar uma aplicação do Hive

### <a name="ad-hoc"></a>Ad hoc

Consultas ad hoc podem ser executadas em qualquer um **Batch** ou **Interactive** modo.

1. Open **Visual Studio**.

2. Partir **Explorador de servidores**, navegue até à **Azure** > **HDInsight**.

3. Expanda **HDInsight**e com o botão direito do cluster para executar a consulta e, em seguida, selecione onde pretende **escrever uma consulta do Hive**.

4. Introduza a seguinte consulta do hive:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Selecione **Executar**. Tenha em atenção que o modo de execução padrão é obtido **Interactive**.

    ![Captura de ecrã de Executar consultas interativas do Hive](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Para executar a mesma consulta no **Batch** modo, a lista de alternância na lista suspensa a partir de **Interactive** para **Batch**. Tenha em atenção que o botão de execução é alterado de **Execute** ao **submeter**.

    ![Captura de ecrã da submissão de uma consulta do Hive](./media/apache-hadoop-use-hive-visual-studio/vs-batch-query.png)

    O editor do Hive suporta IntelliSense. O Data Lake Tools para Visual Studio suportam o carregamento de metadados remotos durante a edição do script do Hive. Por exemplo, se digitar `SELECT * FROM`, o IntelliSense apresenta uma lista de todos os nomes de tabela sugeridos. Quando é especificado um nome de tabela, o IntelliSense lista os nomes das colunas. As ferramentas suportam quase todas as instruções DML do Hive, subconsultas e os UDFs incorporados. O IntelliSense sugere apenas os metadados do cluster selecionado na barra de ferramentas do HDInsight.

    ![Captura de ecrã de um exemplo do IntelliSense do HDInsight Visual Studio Tools IntelliSense 1](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-table-name.png "U-SQL IntelliSense")
   
    ![Captura de ecrã de um exemplo do IntelliSense do HDInsight Visual Studio Tools IntelliSense 2](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-column-name.png "U-SQL IntelliSense")

7. Selecione **Submeter** ou **Submeter (Avançado)** .

   Se selecionar a opção de submissão avançada, configura o **Nome da Tarefa**, **Argumentos**, **Configurações Adicionais** e **Diretório de Estado** para o script:

    ![Captura de ecrã da consulta do Hive do Hadoop HDInsight](./media/apache-hadoop-use-hive-visual-studio/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Submeter consultas")

### <a name="hive-application"></a>Aplicação do Hive

1. Open **Visual Studio**.

2. A partir da barra de menus, navegue para **arquivo** > **New** > **projeto**.

3. Do **novo projeto** janela, navegue até à **modelos** > **do Azure Data Lake** > **HIVE (HDInsight)**  >  **Hive aplicação**. 

4. Forneça um nome para este projeto e, em seguida, selecione **OK**.

5. Abra o **hql** ficheiro que é criado com este projeto e cole as seguintes declarações HiveQL:

    ```hiveql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Essas instruções executam as seguintes ações:

   * `DROP TABLE`: Se a tabela existe, essa instrução elimina-a.

   * `CREATE EXTERNAL TABLE`: Cria uma nova tabela de "externa" no Hive. Tabelas externas apenas armazenam a definição da tabela no Hive (resta os dados na localização original).

     > [!NOTE]  
     > Tabelas externas devem ser usadas quando espera que os dados subjacentes ser atualizados por uma origem externa. Por exemplo, uma tarefa de MapReduce ou serviço do Azure.
     >
     > Remover uma tabela externa faz **não** eliminar os dados, apenas a definição da tabela.

   * `ROW FORMAT`: Informa ao Hive como os dados estiverem formatados. Neste caso, os campos em cada registo são separados por um espaço.

   * `STORED AS TEXTFILE LOCATION`: Informa ao Hive que os dados são armazenados no diretório/dados de exemplo e que são armazenado como texto.

   * `SELECT`: Selecione uma contagem de todas as linhas em que coluna `t4` contém o valor `[ERROR]`. Esta declaração devolve um valor de `3` porque existem três linhas que contêm este valor.

   * `INPUT__FILE__NAME LIKE '%.log'` -Informa ao Hive que podemos deverá devolver apenas dados de ficheiros terminados em. log. Essa cláusula restringe a pesquisa para o ficheiro Sample log que contém os dados.

6. Na barra de ferramentas, selecione o **Cluster de HDInsight** que pretende utilizar para esta consulta. Selecione **submeter** para executar as instruções como uma tarefa do Hive.

   ![Submeter barra](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

7. O **resumo da tarefa do Hive** aparece e apresenta informações sobre a tarefa em execução. Utilize o **Atualize** link para atualizar a informação de tarefa, até o **estado da tarefa** é alterado para **concluído**.

   ![Resumo da tarefa exibindo uma tarefa concluída](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

8. Utilize o **saída da tarefa** ligação para ver o resultado da tarefa. Ele exibe `[ERROR] 3`, que é o valor devolvido por esta consulta.

### <a name="additional-example"></a>Exemplo de adicional

Este exemplo depende do `log4jLogs` tabela criada no passo anterior.

1. Partir **Explorador de servidores**, clique com o botão direito do cluster e selecione **escrever uma consulta do Hive**.

2. Introduza a seguinte consulta do hive:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Essas instruções executam as seguintes ações:

    * `CREATE TABLE IF NOT EXISTS`: Cria uma tabela se ainda não exista. Uma vez que o `EXTERNAL` palavra-chave não é utilizado, esta instrução cria uma tabela interna. Tabelas internas são armazenadas no armazém de dados de Hive e são geridas através do Hive.
    
    > [!NOTE]  
    > Ao contrário de `EXTERNAL` tabelas, remover uma tabela interna também elimina os dados subjacentes.

    * `STORED AS ORC`: Armazena os dados em formato de (ORC) em colunas de linha otimizada. ORC é um formato altamente otimizado e eficiente para armazenar os dados de Hive.
    
    * `INSERT OVERWRITE ... SELECT`: Seleciona as linhas do `log4jLogs` tabela que contêm `[ERROR]`, em seguida, insere os dados no `errorLogs` tabela.

3. Executar a consulta no **Batch** modo.

4. Para verificar se a tarefa criada a tabela, utilize **Explorador de servidores** e expanda **Azure** > **HDInsight** > seu cluster do HDInsight >  **Bases de dados de Hive** > **padrão**. O **registos de erros** tabela e o **log4jLogs** tabela estão listados.

## <a id="nextsteps"></a>Passos seguintes

Como pode ver, as ferramentas do HDInsight para Visual Studio fornecem uma forma fácil de trabalhar com consultas do Hive no HDInsight.

Para obter informações gerais sobre o Hive no HDInsight:

* [Utilizar o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras formas pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)

* [Utilizar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Para obter mais informações sobre as ferramentas do HDInsight para Visual Studio:

* [Introdução às ferramentas do HDInsight para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)