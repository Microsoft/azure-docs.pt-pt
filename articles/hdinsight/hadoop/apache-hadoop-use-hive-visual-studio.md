---
title: Apache Hive com ferramentas do Data Lake (Apache Hadoop) para o Visual Studio - Azure HDInsight
description: Saiba como utilizar as ferramentas do Data Lake para Visual Studio para executar consultas do Apache Hive com o Apache Hadoop no HDInsight do Azure.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 5cc079a5e6d78269d2df9f13dc8ccf8d026163e4
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202270"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Executar consultas do Apache Hive com as ferramentas do Data Lake para Visual Studio

Saiba como utilizar as ferramentas do Data Lake para Visual Studio para consultar Apache Hive. As ferramentas do Data Lake permitem-lhe facilmente criar, submeter e monitorizar consultas do Hive para Apache Hadoop no HDInsight do Azure.

## <a id="prereq"></a>Pré-requisitos

* Um cluster do Azure HDInsight (Apache Hadoop no HDInsight)

  > [!IMPORTANT]  
  > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* Visual Studio (uma das seguintes versões):

    * Visual Studio 2013 Community/Professional/Premium/Ultimate com a atualização 4

    * Visual Studio 2015 (qualquer edição)

    * Visual Studio 2017 (qualquer edição)

* Ferramentas do HDInsight para Visual Studio ou o Azure Data Lake tools para Visual Studio. Ver [começar a utilizar as ferramentas Hadoop do Visual Studio para o HDInsight](apache-hadoop-visual-studio-tools-get-started.md) para obter informações sobre como instalar e configurar as ferramentas.

## <a id="run"></a> Executar consultas do Apache Hive com o Visual Studio

1. Open **Visual Studio** e selecione **New** > **projeto** > **Azure Data Lake**  >   **HIVE** > **Hive aplicação**. Forneça um nome para este projeto.

2. Abra o **hql** ficheiro que é criado com este projeto e cole as seguintes declarações HiveQL:

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

3. Na barra de ferramentas, selecione o **Cluster de HDInsight** que pretende utilizar para esta consulta. Selecione **submeter** para executar as instruções como uma tarefa do Hive.

   ![Submeter barra](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

4. O **resumo da tarefa do Hive** aparece e apresenta informações sobre a tarefa em execução. Utilize o **Atualize** link para atualizar a informação de tarefa, até o **estado da tarefa** é alterado para **concluído**.

   ![Resumo da tarefa exibindo uma tarefa concluída](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

5. Utilize o **saída da tarefa** ligação para ver o resultado da tarefa. Ele exibe `[ERROR] 3`, que é o valor devolvido por esta consulta.

6. Também pode executar consultas do Hive sem criar um projeto. Usando **Explorador de servidores**, expanda **Azure** > **HDInsight**, o servidor do HDInsight com o botão direito e, em seguida, selecione **escrever uma consulta do Hive** .

7. Na **temp.hql** documento que é apresentada, adicione as seguintes declarações HiveQL:

   ```hiveql
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

8. Na barra de ferramentas, selecione **submeter** para executar a tarefa. Utilize o **estado da tarefa** para determinar se a tarefa foi concluída com êxito.

9. Para verificar se a tarefa criada a tabela, utilize **Explorador de servidores** e expanda **Azure** > **HDInsight** > seu cluster do HDInsight >  **Bases de dados de Hive** > **padrão**. O **registos de erros** tabela e o **log4jLogs** tabela estão listados.

## <a id="nextsteps"></a>Passos seguintes

Como pode ver, as ferramentas do HDInsight para Visual Studio fornecem uma forma fácil de trabalhar com consultas do Hive no HDInsight.

Para obter informações gerais sobre o Hive no HDInsight:

* [Utilizar o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras formas pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)

* [Utilizar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Para obter mais informações sobre as ferramentas do HDInsight para Visual Studio:

* [Introdução às ferramentas do HDInsight para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
