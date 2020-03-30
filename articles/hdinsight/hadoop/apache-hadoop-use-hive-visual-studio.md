---
title: Apache Hive & ferramentas do Data Lake para Estúdio Visual - Azure HDInsight
description: Aprenda a usar as ferramentas data lake para o Estúdio Visual para executar consultas apache hive com Apache Hadoop no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 27ab13481525819eb1435f4c9ac256a21acd21fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687808"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Executar consultas do Apache Hive com as ferramentas do Data Lake para Visual Studio

Aprenda a usar as ferramentas data lake para o Estúdio Visual para consultar a Apache Hive. As ferramentas data lake permitem-lhe criar, submeter e monitorizar facilmente as consultas da Hive ao Hadoop Apache no Azure HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um aglomerado Apache Hadoop no HDInsight. Para obter informações sobre a criação deste item, consulte [Create Apache Hadoop cluster in Azure HDInsight usando o modelo de Gestor de Recursos](./apache-hadoop-linux-tutorial-get-started.md).

* [Estúdio Visual.](https://visualstudio.microsoft.com/vs/) Os passos neste artigo usam o Visual Studio 2019.

* Ferramentas HDInsight para visual studio ou ferramentas Azure Data Lake para Estúdio Visual. Para obter informações sobre a instalação e configuração das ferramentas, consulte [instalar ferramentas](apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio)do Lago de Dados para estúdio visual .

## <a name="run-apache-hive-queries-using-the-visual-studio"></a>Executar consultas de Hiv Apache usando o Estúdio Visual

Tem duas opções para criar e executar consultas do Hive:

* Criar consultas ad-hoc.
* Crie uma aplicação de Colmeia.

### <a name="create-an-ad-hoc-hive-query"></a>Criar uma consulta de Colmeia ad-hoc

As consultas ad hoc podem ser executadas em modo **Batch** ou **Interactive.**

1. Lançar **Estúdio Visual** e selecionar Continuar **sem código**.

2. A partir do **Server Explorer,** clique no **Azure,** selecione **Connect to Microsoft Azure Subscription...**, e complete o sinal no processo.

3. Expanda **o HDInsight,** clique à direita no cluster onde pretende executar a consulta e, em seguida, selecione **Escrever uma Consulta**de Colmeia .

4. Introduza a seguinte consulta de colmeia:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Selecione **Executar**. O modo de execução não se aplica à **Interactive**.

    ![Executar consulta interativa da Hive, Estúdio Visual](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Para executar a mesma consulta no modo **Batch,** altere a lista de drop-down de **Interactive** para **Batch**. O botão de execução muda de **Executar** para **Submeter**.

    ![Submeter consulta de hive lote, Estúdio Visual](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    O editor do Hive suporta IntelliSense. O Data Lake Tools para Visual Studio suportam o carregamento de metadados remotos durante a edição do script do Hive. Por exemplo, se `SELECT * FROM`escrever, o IntelliSense lista todos os nomes de tabela sugeridos. Quando é especificado um nome de tabela, o IntelliSense lista os nomes das colunas. As ferramentas suportam quase todas as instruções DML do Hive, subconsultas e os UDFs incorporados. O IntelliSense sugere apenas os metadados do cluster selecionado na barra de ferramentas do HDInsight.

7. Na barra de ferramentas de consulta (a área abaixo do separador de consulta e acima do texto de consulta), selecione **Enviar**, ou selecione a seta de pulldown ao lado de **Submeter** e escolha **Advanced** a partir da lista de retirada. Se selecionar a última opção,

8. Se selecionou a opção de submissão avançada, configure **o Nome de Trabalho,** **Os Argumentos,** **configurações Adicionais**e **o Diretório** de Estado na caixa de diálogo **do Script Enviar.** Em seguida, selecione **Submeter**.

    ![Submeta caixa de diálogo script, consulta de colmeia hadoop HDInsight](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png)

### <a name="create-a-hive-application"></a>Criar uma aplicação do Hive

Para executar uma consulta da Hive criando uma aplicação hive, siga estes passos:

1. Estúdio **Visual**Aberto.

2. Na janela **Iniciar,** selecione **Criar um novo projeto**.

3. Na **Janela Criar uma nova** janela de projeto, na caixa **de modelos de pesquisa,** introduza *a Hive*. Em seguida, escolha **a Aplicação Hive** e selecione **Next**.

4. Na **configuração** da sua nova janela de projeto, insira um **nome de Projeto,** selecione ou crie um **Local** para o novo projeto e, em seguida, selecione **Criar**.

5. Abra o ficheiro **Script.hql** que é criado com este projeto, e colá-lo nas seguintes declarações da HiveQL:

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

    * `CREATE EXTERNAL TABLE`: Cria uma nova tabela 'externa' na Colmeia. As tabelas externas apenas armazenam a definição de mesa na Colmeia. (Os dados são deixados no local original.)

        > [!NOTE]  
        > As tabelas externas devem ser utilizadas quando se espera que os dados subjacentes sejam atualizados por uma fonte externa, como um trabalho MapReduce ou um serviço Azure.
        >
        > Deixar cair uma tabela externa **não** elimina os dados, apenas a definição de tabela.

    * `ROW FORMAT`: Diz à Hive como os dados são formatados. Neste caso, os campos em cada tronco são separados por um espaço.

    * `STORED AS TEXTFILE LOCATION`: Diz à Hive que os dados são armazenados no diretório *exemplo/dados* e que são armazenados como texto.

    * `SELECT`: Seleciona uma contagem de `t4` todas as `[ERROR]`linhas onde a coluna contenha o valor . Esta declaração devolve `3`um valor de, porque três linhas contêm este valor.

    * `INPUT__FILE__NAME LIKE '%.log'`: Diz à Hive que só devolve dados de ficheiros que terminam em .log. Esta cláusula restringe a pesquisa ao ficheiro *sample.log* que contém os dados.

6. A partir da barra de ferramentas de ficheiro de consulta (que tem uma aparência semelhante à barra de ferramentas de consulta ad-hoc), selecione o cluster HDInsight que pretende utilizar para esta consulta. Em seguida, mude **a Interactive** para **Batch** (se necessário) e selecione **Enviar** para executar as declarações como um trabalho de Colmeia.

   O Resumo do Trabalho da **Colmeia** aparece e mostra informações sobre o trabalho de corrida. Utilize o link **Refresh** para atualizar as informações de trabalho, até que o Estado do **Trabalho** mude para **Concluído**.

   ![Resumo de trabalho completo da Hive, aplicação da Hive, Estúdio Visual](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

7. Selecione **Saída de Trabalho** para ver a saída deste trabalho. Exibe `[ERROR] 3`, que é o valor devolvido por esta consulta.

### <a name="additional-example"></a>Exemplo adicional

O exemplo seguinte baseia-se na `log4jLogs` tabela criada no procedimento anterior, Criar uma [aplicação da Colmeia.](#create-a-hive-application)

1. A partir do **Server Explorer,** clique no seu cluster e selecione **Escrever uma Consulta**de Colmeia .

2. Introduza a seguinte consulta de colmeia:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Estas declarações fazem as seguintes ações:

    * `CREATE TABLE IF NOT EXISTS`: Cria uma mesa se já não existir. Como `EXTERNAL` a palavra-chave não é usada, esta declaração cria uma mesa interna. As tabelas internas são armazenadas no armazém de dados da Hive e são geridas pela Hive.

        > [!NOTE]  
        > Ao `EXTERNAL` contrário das tabelas, a queda de uma tabela interna também elimina os dados subjacentes.

    * `STORED AS ORC`: Armazene os dados em formato colunar de *linha otimizado* (ORC). OrC é um formato altamente otimizado e eficiente para armazenar dados da Hive.

    * `INSERT OVERWRITE ... SELECT`: Seleciona linhas `log4jLogs` da tabela `[ERROR]`que contêm e, `errorLogs` em seguida, insere os dados na tabela.

3. Mude **interativo** para **lote,** se necessário, selecione **Submeter**.

4. Para verificar se o trabalho criou a tabela, vá ao **Server Explorer** e expanda **o Azure** > **HDInsight**. Expanda o seu cluster HDInsight e, em seguida, expanda o > **padrão de**bases de **dados da Hive**. A tabela **errorLogs** e a tabela **log4jLogs** estão listadas.

## <a name="next-steps"></a>Passos seguintes

Como pode ver, as ferramentas HDInsight para O Estúdio Visual proporcionam uma maneira fácil de trabalhar com consultas de Hive no HDInsight.

* Para obter informações gerais sobre a Hive no HDInsight, consulte [o que é Apache Hive e HiveQL no Azure HDInsight?](hdinsight-use-hive.md)

* Para obter informações sobre outras formas de trabalhar com Hadoop no HDInsight, consulte [Use MapReduce in Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)

* Para obter mais informações sobre as ferramentas HDInsight para Estúdio Visual, consulte[Use Data Lake Tools for Visual Studio para se conectar ao Azure HDInsight e executar consultas apache hive](apache-hadoop-visual-studio-tools-get-started.md)
