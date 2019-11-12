---
title: Apache Hive & Data Lake Tools for Visual Studio – Azure HDInsight
description: Saiba como usar as ferramentas de Data Lake para o Visual Studio para executar Apache Hive consultas com Apache Hadoop no Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: hrasheed
ms.openlocfilehash: 5b10cc5a8b7468b222fec3f2e66a8258470047ae
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931838"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Executar Apache Hive consultas usando o Data Lake Tools para Visual Studio

Saiba como usar as ferramentas de Data Lake para o Visual Studio para consultar Apache Hive. As ferramentas de Data Lake permitem que você crie, envie e monitore consultas de Hive com facilidade para Apache Hadoop no Azure HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Apache Hadoop no HDInsight. Para obter informações sobre como criar esse item, consulte [criar Apache Hadoop cluster no Azure HDInsight usando o modelo do Resource Manager](./apache-hadoop-linux-tutorial-get-started.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/). As etapas neste artigo usam o Visual Studio 2019.

* Ferramentas do HDInsight para Visual Studio ou ferramentas de Azure Data Lake para Visual Studio. Para obter informações sobre como instalar e configurar as ferramentas, consulte [instalar o data Lake Tools para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio).

## <a name="run-apache-hive-queries-using-the-visual-studio"></a>Executar Apache Hive consultas usando o Visual Studio

Tem duas opções para criar e executar consultas do Hive:

* Criar consultas ad hoc.
* Crie um aplicativo do hive.

### <a name="create-an-ad-hoc-hive-query"></a>Criar uma consulta de Hive ad hoc

Consultas ad hoc podem ser executadas no modo de **lote** ou **interativo** .

1. Abra o **Visual Studio**.

2. Em **Gerenciador de servidores**, navegue até **Azure** > **HDInsight**.

3. Expanda **HDInsight**, clique com o botão direito do mouse no cluster em que você deseja executar a consulta e, em seguida, selecione **gravar uma consulta do hive**.

4. Insira a seguinte consulta de Hive:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Selecione **Executar**. O modo de execução usa como padrão a **interatividade**.

    ![Executar consulta de Hive interativa, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Para executar a mesma consulta no modo de **lote** , alterne a lista suspensa de **interativo** para **lote**. O botão de execução muda de **executar** para **Enviar**.

    ![Enviar consulta do hive do lote, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    O editor do Hive suporta IntelliSense. O Data Lake Tools para Visual Studio suportam o carregamento de metadados remotos durante a edição do script do Hive. Por exemplo, se você digitar `SELECT * FROM`, o IntelliSense listará todos os nomes de tabela sugeridos. Quando é especificado um nome de tabela, o IntelliSense lista os nomes das colunas. As ferramentas suportam quase todas as instruções DML do Hive, subconsultas e os UDFs incorporados. O IntelliSense sugere apenas os metadados do cluster selecionado na barra de ferramentas do HDInsight.

7. Na barra de ferramentas de consulta (a área abaixo da guia consulta e acima do texto da consulta), selecione **Enviar**ou selecione a seta suspensa ao lado de **Enviar** e escolha **avançado** na lista suspensa. Se você selecionar a última opção,

8. Se você selecionou a opção de envio avançado, configure **nome do trabalho**, **argumentos**, **configurações adicionais**e **diretório de status** na caixa de diálogo **Enviar script** . Em seguida, selecione **Enviar**.

    ![Caixa de diálogo Enviar script, consulta do hive do HDInsight Hadoop](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png)

### <a name="create-a-hive-application"></a>Criar uma aplicação do Hive

Para executar uma consulta de Hive criando um aplicativo Hive, siga estas etapas:

1. Abra o **Visual Studio**.

2. Na janela **Iniciar** , selecione **criar um novo projeto**.

3. Na janela **criar um novo projeto** , na caixa **Pesquisar modelos** , insira *Hive*. Em seguida, escolha **aplicativo Hive** e selecione **Avançar**.

4. Na janela **configurar seu novo projeto** , insira um **nome de projeto**, selecione ou crie um **local** para o novo projeto e, em seguida, selecione **criar**.

5. Abra o arquivo **script. HQL** criado com este projeto e cole as seguintes instruções HiveQL:

    ```hql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Essas instruções realizam as seguintes ações:

    * `DROP TABLE`: exclui a tabela, se ela existir.

    * `CREATE EXTERNAL TABLE`: cria uma nova tabela ' external ' no hive. As tabelas externas armazenam apenas a definição de tabela no hive. (Os dados são deixados no local original.)

        > [!NOTE]  
        > As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa, como um trabalho MapReduce ou um serviço do Azure.
        >
        > Descartar uma tabela externa **não** exclui os dados, apenas a definição da tabela.
    
    * `ROW FORMAT`: informa ao hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.

    * `STORED AS TEXTFILE LOCATION`: informa ao hive que os dados são armazenados no diretório de *exemplo/dados* e que são armazenados como texto.

    * `SELECT`: seleciona uma contagem de todas as linhas em que a coluna `t4` contém o valor `[ERROR]`. Essa instrução retorna um valor de `3`, porque três linhas contêm esse valor.

    * `INPUT__FILE__NAME LIKE '%.log'`: informa ao hive para retornar apenas dados de arquivos que terminam em. log. Essa cláusula restringe a pesquisa ao arquivo *Sample. log* que contém os dados.

6. Na barra de ferramentas do arquivo de consulta (que tem aparência semelhante à barra de ferramentas de consulta ad hoc), selecione o cluster HDInsight que você deseja usar para esta consulta. Em seguida, altere **interativo** para **lote** (se necessário) e selecione **Enviar** para executar as instruções como um trabalho do hive.

   O **Resumo do trabalho do hive** é exibido e exibe informações sobre o trabalho em execução. Use o link **Atualizar** para atualizar as informações do trabalho, até que o **status do trabalho** seja alterado para **concluído**.

   ![Resumo do trabalho do hive concluído, aplicativo Hive, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

7. Selecione **saída do trabalho** para exibir a saída desse trabalho. Ele exibe `[ERROR] 3`, que é o valor retornado por essa consulta.

### <a name="additional-example"></a>Exemplo adicional

O exemplo a seguir depende da tabela de `log4jLogs` criada no procedimento anterior, [criar um aplicativo Hive](#create-a-hive-application).

1. Em **Gerenciador de servidores**, clique com o botão direito do mouse no cluster e selecione **gravar uma consulta do hive**.

2. Insira a seguinte consulta de Hive:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Essas instruções realizam as seguintes ações:

    * `CREATE TABLE IF NOT EXISTS`: cria uma tabela, caso ela ainda não exista. Como a palavra-chave `EXTERNAL` não é usada, essa instrução cria uma tabela interna. As tabelas internas são armazenadas no data warehouse do hive e são gerenciadas pelo Hive.

        > [!NOTE]  
        > Ao contrário das tabelas `EXTERNAL`, remover uma tabela interna também exclui os dados subjacentes.

    * `STORED AS ORC`: armazena os dados no formato *colunar de linhas otimizadas* (ORC). O ORC é um formato altamente otimizado e eficiente para armazenar dados do hive.
    
    * `INSERT OVERWRITE ... SELECT`: seleciona linhas da tabela `log4jLogs` que contêm `[ERROR]`e, em seguida, insere os dados na tabela `errorLogs`.

3. Altere **interativo** para **lote** , se necessário, e selecione **Enviar**.

4. Para verificar se o trabalho criou a tabela, vá para **Gerenciador de servidores** e expanda **Azure** > **HDInsight**. Expanda seu cluster HDInsight e, em seguida, expanda **bancos de dados do Hive** > **padrão**. A tabela de **logs de erros** e a tabela **log4jLogs** são listadas.

## <a name="next-steps"></a>Passos seguintes

Como você pode ver, as ferramentas do HDInsight para Visual Studio fornecem uma maneira fácil de trabalhar com consultas de Hive no HDInsight.

Para obter informações gerais sobre o hive no HDInsight:

* [O que é Apache Hive e HiveQL no Azure HDInsight?](hdinsight-use-hive.md)

Para obter informações sobre outras maneiras que você pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o MapReduce no Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Para obter mais informações sobre as ferramentas do HDInsight para Visual Studio:

* [Usar as ferramentas de Data Lake para o Visual Studio para se conectar ao Azure HDInsight e executar consultas de Apache Hive](apache-hadoop-visual-studio-tools-get-started.md)
