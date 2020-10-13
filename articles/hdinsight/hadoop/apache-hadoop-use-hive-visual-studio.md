---
title: Apache Hive & ferramentas do Data Lake para Estúdio Visual - Azure HDInsight
description: Aprenda a usar as ferramentas do Data Lake para o Estúdio Visual para executar consultas de Hive Apache com Apache Hadoop em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: c8645ae9cb901b9fc95f00665d73e223a24fda63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86076458"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Executar consultas do Apache Hive com as ferramentas do Data Lake para Visual Studio

Aprenda a usar as ferramentas do Data Lake para o Estúdio Visual para consultar a Colmeia Apache. As ferramentas Data Lake permitem-lhe criar, submeter e monitorizar facilmente consultas de Hive a Apache Hadoop em Azure HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado Apache Hadoop em HDInsight. Para obter informações sobre a criação deste item, consulte [o cluster Create Apache Hadoop em Azure HDInsight utilizando o modelo de Gestor de Recursos](./apache-hadoop-linux-tutorial-get-started.md).

* [Estúdio Visual](https://visualstudio.microsoft.com/vs/). Os passos deste artigo usam o Visual Studio 2019.

* Ferramentas HDInsight para ferramentas visual Studio ou Azure Data Lake para Estúdio Visual. Para obter informações sobre a instalação e configuração das ferramentas, consulte [instalar ferramentas do Lago de Dados para o Estúdio Visual.](apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio)

## <a name="run-apache-hive-queries-using-the-visual-studio"></a>Executar consultas de Colmeia Apache usando o Estúdio Visual

Tem duas opções para criar e executar consultas do Hive:

* Crie consultas ad-hoc.
* Crie uma aplicação de Colmeia.

### <a name="create-an-ad-hoc-hive-query"></a>Criar uma consulta de colmeia ad-hoc

As consultas ad hoc podem ser executadas no modo **Batch** ou **Interactive.**

1. Lançar **Visual Studio** e selecionar Continue sem **código**.

2. A partir do **Server Explorer,** clique à direita **Azure,** selecione **Connect to Microsoft Azure Subscription...** e complete o sinal em processo.

3. Expanda **o HDInsight,** clique com o botão direito no cluster onde pretende executar a consulta e, em seguida, selecione **Escreva uma Consulta de Colmeia**.

4. Insira a seguinte consulta de colmeia:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Selecione **Execute** (Executar). O modo de execução é desresposição **da Interactive**.

    ![Executar consulta interativa de Hive, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Para executar a mesma consulta no modo **Batch,** altere a lista de drop-down de **Interactive** para **Batch**. O botão de execução muda de **Executar** para **Enviar**.

    ![Submeter consulta de hive de lote, Visual Studio](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    O editor do Hive suporta IntelliSense. O Data Lake Tools para Visual Studio suportam o carregamento de metadados remotos durante a edição do script do Hive. Por exemplo, se `SELECT * FROM` escrever, o IntelliSense lista todos os nomes de tabelas sugeridos. Quando é especificado um nome de tabela, o IntelliSense lista os nomes das colunas. As ferramentas suportam quase todas as instruções DML do Hive, subconsultas e os UDFs incorporados. O IntelliSense sugere apenas os metadados do cluster selecionado na barra de ferramentas do HDInsight.

7. Na barra de ferramentas de consulta (a área abaixo do separador de consulta e acima do texto de consulta), selecione **Enviar,** ou selecione a seta de pulldown ao lado de **Enviar** e escolher **Advanced** da lista de retirada. Se selecionar a última opção,

8. Se selecionou a opção de submissão avançada, configurar **o Nome do Trabalho,** **Argumentos,** **Configurações Adicionais**e **Diretório de Estado** na caixa de diálogo do Script de **Submissão.** Em seguida, **selecione Enviar por isso .**

    ![Envie a caixa de diálogo do Script, consulta hdInsight Hadoop Hive](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png)

### <a name="create-a-hive-application"></a>Criar uma aplicação do Hive

Para executar uma consulta de Colmeia criando uma aplicação de Colmeia, siga estes passos:

1. **Estúdio Visual**Aberto .

2. Na janela **Iniciar,** **selecione Criar um novo projeto.**

3. Na **janela Criar uma nova** janela do projeto, na caixa de pesquisa de **modelos,** insira *a Colmeia.* Em seguida, escolha **a Aplicação Hive** e selecione **Seguinte**.

4. Na janela **Configure** a sua nova janela do projeto, insira um **nome de Projeto,** selecione ou crie uma **Localização** para o novo projeto e, em seguida, selecione **Criar**.

5. Abra o ficheiro **Script.hql** que é criado com este projeto, e cole nas seguintes declarações do HiveQL:

    ```hql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Estas declarações fazem as seguintes ações:

    * `DROP TABLE`: Elimina a tabela se existir.

    * `CREATE EXTERNAL TABLE`: Cria uma nova tabela 'externa' na Colmeia. As mesas externas armazenam apenas a definição de tabela na Colmeia. (Os dados são deixados no local original.)

        > [!NOTE]  
        > As tabelas externas devem ser utilizadas quando espera que os dados subjacentes sejam atualizados por uma fonte externa, como um trabalho MapReduce ou um serviço Azure.
        >
        > Deixar cair uma tabela externa **não** apaga os dados, apenas a definição de tabela.

    * `ROW FORMAT`: Diz à Hive como os dados são formatados. Neste caso, os campos em cada tronco são separados por um espaço.

    * `STORED AS TEXTFILE LOCATION`: Diz à Hive que os dados são armazenados no *diretório de exemplo/dados,* e que são armazenados como texto.

    * `SELECT`: Selecione uma contagem de todas as linhas onde a coluna `t4` contém o valor `[ERROR]` . Esta afirmação devolve um valor `3` de, porque três linhas contêm este valor.

    * `INPUT__FILE__NAME LIKE '%.log'`: Diz à Hive apenas para devolver dados de ficheiros que terminam em .log. Esta cláusula restringe a pesquisa ao ficheiro *sample.log* que contém os dados.

6. A partir da barra de ferramentas de ficheiro de consulta (que tem uma aparência semelhante à barra de ferramentas de consulta ad-hoc), selecione o cluster HDInsight que pretende utilizar para esta consulta. Em seguida, **altere Interactive** para **Batch** (se necessário) e selecione **Submeter-se** para executar as declarações como uma tarefa de Colmeia.

   O **Resumo do Trabalho da Colmeia** aparece e exibe informações sobre o trabalho em execução. Utilize o link **Refresh** para atualizar as informações do trabalho, até **que o Estado do Trabalho** mude para **Concluído**.

   ![Resumo de trabalho da Colmeia concluída, aplicação de Colmeia, Estúdio Visual](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

7. Selecione **Job Output** para ver a saída deste trabalho. Apresenta, `[ERROR] 3` que é o valor devolvido por esta consulta.

### <a name="additional-example"></a>Exemplo adicional

O exemplo a seguir baseia-se na `log4jLogs` tabela criada no procedimento anterior, Criar uma [aplicação De Colmeia](#create-a-hive-application).

1. A partir do **Server Explorer,** clique com o botão direito no seu cluster e selecione **Escreva uma Consulta de Colmeia**.

2. Insira a seguinte consulta de colmeia:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Estas declarações fazem as seguintes ações:

    * `CREATE TABLE IF NOT EXISTS`: Cria uma mesa se já não existir. Como a `EXTERNAL` palavra-chave não é usada, esta afirmação cria uma tabela interna. As tabelas internas são armazenadas no armazém de dados da Colmeia e são geridas pela Hive.

        > [!NOTE]  
        > Ao contrário das `EXTERNAL` tabelas, deixar cair uma tabela interna também elimina os dados subjacentes.

    * `STORED AS ORC`: Armazena os dados em formato *colunar de linha otimizado* (ORC). O ORC é um formato altamente otimizado e eficiente para armazenar dados da Hive.

    * `INSERT OVERWRITE ... SELECT`: Seleciona linhas da `log4jLogs` tabela que contêm `[ERROR]` e, em seguida, insere os dados na `errorLogs` tabela.

3. Altere **interactive** para **lote,** se necessário, em seguida, selecione **Enviar .**

4. Para verificar se o trabalho criou a tabela, vá ao **Server Explorer** e expanda **o Azure**  >  **HDInsight**. Expanda o seu cluster HDInsight e, em seguida, expanda o padrão das **Bases de Dados de Colmeia**  >  **default**. A tabela **ErrorLogs** e a tabela **log4jLogs** estão listadas.

## <a name="next-steps"></a>Passos seguintes

Como pode ver, as ferramentas HDInsight para Visual Studio fornecem uma maneira fácil de trabalhar com consultas de Hive em HDInsight.

* Para obter informações gerais sobre a Colmeia em HDInsight, veja [o que é Apache Hive e HiveQL em Azure HDInsight?](hdinsight-use-hive.md)

* Para obter informações sobre outras formas de trabalhar com Hadoop em HDInsight, consulte [Use MapReduce in Apache Hadoop on HDInsight](hdinsight-use-mapreduce.md)

* Para obter mais informações sobre as ferramentas HDInsight para o Estúdio Visual, consulte[Use Data Lake Tools for Visual Studio para ligar ao Azure HDInsight e executar consultas de Hive Apache](apache-hadoop-visual-studio-tools-get-started.md)
