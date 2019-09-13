---
title: Apache Hive com o Data Lake Tools para Visual Studio – Azure HDInsight
description: Saiba como usar as ferramentas de Data Lake para o Visual Studio para executar Apache Hive consultas com Apache Hadoop no Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: hrasheed
ms.openlocfilehash: 129f200bc9f61d70f4403b1154978d57e09fee26
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70917493"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Executar Apache Hive consultas usando o Data Lake Tools para Visual Studio

Saiba como usar as ferramentas de Data Lake para o Visual Studio para consultar Apache Hive. As ferramentas de Data Lake permitem que você crie, envie e monitore consultas de Hive com facilidade para Apache Hadoop no Azure HDInsight.

## <a id="prereq"></a>Pré-requisitos

* Um cluster Apache Hadoop no HDInsight. Consulte [introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Visual Studio (uma das seguintes versões):

    * Visual Studio 2015, 2017 (qualquer edição)
    * Visual Studio 2013 Community/Professional/Premium/Ultimate com atualização 4

* Ferramentas do HDInsight para Visual Studio ou ferramentas de Azure Data Lake para Visual Studio. Consulte Introdução ao [uso das ferramentas do Hadoop do Visual Studio para HDInsight](apache-hadoop-visual-studio-tools-get-started.md) para obter informações sobre como instalar e configurar as ferramentas.

## <a id="run"></a>Executar Apache Hive consultas usando o Visual Studio

Tem duas opções para criar e executar consultas do Hive:

* Criar consultas ad hoc
* Criar uma aplicação do Hive

### <a name="ad-hoc"></a>Ad hoc

Consultas ad hoc podem ser executadas no modo de **lote** ou **interativo** .

1. Abra o **Visual Studio**.

2. Em **Gerenciador de servidores**, navegue até **Azure** > **HDInsight**.

3. Expanda o **HDInsight**e clique com o botão direito do mouse no cluster em que você deseja executar a consulta e, em seguida, selecione **gravar uma consulta do hive**.

4. Insira a seguinte consulta de Hive:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Selecione **Executar**. Observe que o modo de execução é padronizado como **interativo**.

    ![Captura de ecrã de Executar consultas interativas do Hive](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Para executar a mesma consulta no modo de **lote** , alterne a lista suspensa de **interativo** para **lote**. Observe que o botão de execução muda de **executar** para **Enviar**.

    ![Captura de ecrã da submissão de uma consulta do Hive](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    O editor do Hive suporta IntelliSense. O Data Lake Tools para Visual Studio suportam o carregamento de metadados remotos durante a edição do script do Hive. Por exemplo, se você digitar `SELECT * FROM`, o IntelliSense listará todos os nomes de tabela sugeridos. Quando é especificado um nome de tabela, o IntelliSense lista os nomes das colunas. As ferramentas suportam quase todas as instruções DML do Hive, subconsultas e os UDFs incorporados. O IntelliSense sugere apenas os metadados do cluster selecionado na barra de ferramentas do HDInsight.

    ![Captura de ecrã de um exemplo do IntelliSense do HDInsight Visual Studio Tools IntelliSense 1](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-table-name.png "U-SQL IntelliSense")
   
    ![Captura de ecrã de um exemplo do IntelliSense do HDInsight Visual Studio Tools IntelliSense 2](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-column-name.png "U-SQL IntelliSense")

7. Selecione **Submeter** ou **Submeter (Avançado)** .

   Se selecionar a opção de submissão avançada, configura o **Nome da Tarefa**, **Argumentos**, **Configurações Adicionais** e **Diretório de Estado** para o script:

    ![Captura de ecrã da consulta do Hive do Hadoop HDInsight](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png "Submeter consultas")

### <a name="hive-application"></a>Aplicativo do hive

1. Abra o **Visual Studio**.

2. Na barra de menus, navegue até **arquivo** > **novo** > **projeto**.

3. Na janela **novo projeto** , navegue até **modelos** > **Azure data Lake** > **aplicativo Hive** **do hive (HDInsight)**  > . 

4. Forneça um nome para este projeto e, em seguida, selecione **OK**.

5. Abra o arquivo **script. HQL** criado com este projeto e cole as seguintes instruções HiveQL:

    ```hiveql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Essas instruções executam as seguintes ações:

   * `DROP TABLE`: Se a tabela existir, essa instrução a excluirá.

   * `CREATE EXTERNAL TABLE`: Cria uma nova tabela ' external ' no hive. As tabelas externas só armazenam a definição de tabela no hive (os dados são deixados no local original).

     > [!NOTE]  
     > As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa. Por exemplo, um trabalho MapReduce ou um serviço do Azure.
     >
     > Descartar uma tabela externa **não** exclui os dados, apenas a definição da tabela.

   * `ROW FORMAT`: Informa ao hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.

   * `STORED AS TEXTFILE LOCATION`: Informa ao hive que os dados são armazenados no diretório de exemplo/dados e que são armazenados como texto.

   * `SELECT`: Selecione uma contagem de todas as linhas em `t4` que a coluna `[ERROR]`contém o valor. Essa instrução retorna um valor de `3` porque há três linhas que contêm esse valor.

   * `INPUT__FILE__NAME LIKE '%.log'`– Informa ao hive que só devemos retornar dados de arquivos que terminam em. log. Essa cláusula restringe a pesquisa ao arquivo Sample. log que contém os dados.

6. Na barra de ferramentas, selecione o **cluster HDInsight** que você deseja usar para esta consulta. Selecione **Enviar** para executar as instruções como um trabalho do hive.

   ![Barra de envio](./media/apache-hadoop-use-hive-visual-studio/hdinsight-toolbar-submit.png)

7. O **Resumo do trabalho do hive** é exibido e exibe informações sobre o trabalho em execução. Use o link **Atualizar** para atualizar as informações do trabalho, até que o **status do trabalho** seja alterado para **concluído**.

   ![Resumo do trabalho exibindo um trabalho concluído](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

8. Use o link **saída do trabalho** para exibir a saída desse trabalho. Ele exibe `[ERROR] 3`, que é o valor retornado por essa consulta.

### <a name="additional-example"></a>Exemplo adicional

Este exemplo se baseia na `log4jLogs` tabela criada na etapa anterior.

1. Em **Gerenciador de servidores**, clique com o botão direito do mouse no cluster e selecione **gravar uma consulta do hive**.

2. Insira a seguinte consulta de Hive:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Essas instruções executam as seguintes ações:

    * `CREATE TABLE IF NOT EXISTS`: Cria uma tabela se ela ainda não existir. Como a `EXTERNAL` palavra-chave não é usada, essa instrução cria uma tabela interna. As tabelas internas são armazenadas no data warehouse do hive e são gerenciadas pelo Hive.
    
    > [!NOTE]  
    > Ao `EXTERNAL` contrário das tabelas, remover uma tabela interna também exclui os dados subjacentes.

    * `STORED AS ORC`: Armazena os dados no formato colunar de linhas otimizadas (ORC). O ORC é um formato altamente otimizado e eficiente para armazenar dados do hive.
    
    * `INSERT OVERWRITE ... SELECT`: Seleciona linhas da `log4jLogs` tabela que contêm `[ERROR]`e `errorLogs` insere os dados na tabela.

3. Execute a consulta no modo de **lote** .

4. Para verificar se o trabalho criou a tabela, use **Gerenciador de servidores** e expanda **Azure** > **hdinsight** > > seu cluster hdinsight > bancos de **dados do hive** **padrão**. A tabela de **logs de erros** e a tabela **log4jLogs** são listadas.

## <a id="nextsteps"></a>Passos seguintes

Como você pode ver, as ferramentas do HDInsight para Visual Studio fornecem uma maneira fácil de trabalhar com consultas de Hive no HDInsight.

Para obter informações gerais sobre o hive no HDInsight:

* [Usar Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras maneiras que você pode trabalhar com o Hadoop no HDInsight:

* [Usar o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)

* [Usar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Para obter mais informações sobre as ferramentas do HDInsight para Visual Studio:

* [Introdução às ferramentas do HDInsight para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)