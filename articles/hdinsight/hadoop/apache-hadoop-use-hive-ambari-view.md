---
title: Use Apache Ambari Hive View com Apache Hadoop em Azure HDInsight
description: Saiba como usar o Hive View a partir do seu navegador web para submeter consultas de Hive. O Hive View faz parte do Ambari Web UI fornecido com o seu cluster HDInsight baseado em Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: cc04a891bb32ede6c7bb72a339b728642cf343ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86207832"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Utilizar a Vista do Apache Ambari Hive com o Apache Hadoop no HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Aprenda a executar consultas de Colmeia usando Apache Ambari Hive View. O Hive View permite-lhe autorizar, otimizar e executar consultas de Hive a partir do seu navegador web.

## <a name="prerequisites"></a>Pré-requisitos

Um aglomerado hadoop em HDInsight. Ver [Começar com HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

## <a name="run-a-hive-query"></a>Executar uma consulta do Hive

1. A partir do [portal Azure,](https://portal.azure.com/)selecione o seu cluster.  Consulte [lista e mostre os agrupamentos](../hdinsight-administer-use-portal-linux.md#showClusters) para obter instruções. O cluster é aberto numa nova vista de portal.

1. A partir **de dashboards cluster,** selecione **Ambari vistas**. Quando solicitado para autenticar, utilize o nome de conta e palavra-passe de login do cluster (predefinido) `admin` que forneceu quando criou o cluster. Você também pode navegar para `https://CLUSTERNAME.azurehdinsight.net/#/main/views` no seu navegador onde é o nome do seu `CLUSTERNAME` cluster.

1. Na lista de pontos de vista, __selecione Hive View__.

    ![Apache Ambari seleciona a vista apache hive](./media/apache-hadoop-use-hive-ambari-view/select-apache-hive-view.png)

    A página de visualização da Colmeia é semelhante à seguinte imagem:

    ![Imagem da folha de consulta para a vista da Colmeia](./media/apache-hadoop-use-hive-ambari-view/ambari-worksheet-view.png)

1. A partir do separador __Consulta,__ cole as seguintes declarações do HiveQL na folha de cálculo:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]'
        GROUP BY t4;
    ```

    Estas declarações fazem as seguintes ações:

    |Instrução | Descrição |
    |---|---|
    |MESA DE LANÇAMENTO|Elimina a tabela e o ficheiro de dados, caso a tabela já exista.|
    |CRIAR TABELA EXTERNA|Cria uma nova tabela "externa" na Colmeia. As mesas externas armazenam apenas a definição de tabela na Colmeia. Os dados são deixados no local original.|
    |FORMATO DE LINHA|Mostra como os dados são formatados. Neste caso, os campos em cada tronco são separados por um espaço.|
    |ARMAZENADO COMO LOCALIZAÇÃO DE FICHEIRO DE TEXTO|Mostra onde os dados são armazenados, e que é armazenado como texto.|
    |SELECIONAR|Seleciona uma contagem de todas as linhas onde a coluna t4 contém o valor [ERROR].|

   > [!IMPORTANT]  
   > Deixe a seleção __da Base de Dados__ __por defeito__. Os exemplos deste documento utilizam a base de dados padrão incluída no HDInsight.

1. Para iniciar a consulta, **selecione Executar** abaixo da folha de cálculo. O botão torna-se laranja e o texto muda para **Parar**.

1. Depois de concluída a consulta, o separador **Resultados** apresenta os resultados da operação. O seguinte texto é o resultado da consulta:

    ```output
    loglevel       count
    [ERROR]        3
    ```

    Pode utilizar o **separador LOG** para visualizar as informações de registo que o trabalho criou.

   > [!TIP]  
   > Faça o download ou guarde os resultados da caixa de diálogo de lançamento **de Ações** no separador **Resultados.**

### <a name="visual-explain"></a>Explicação visual

Para visualizar uma visualização do plano de consulta, selecione o **separador Explicação Visual** abaixo da folha de cálculo.

A visão **de Explicação Visual** da consulta pode ser útil para entender o fluxo de consultas complexas.

### <a name="tez-ui"></a>Tez UI

Para visualizar o UI Tez para a consulta, selecione o separador **Tez UI** abaixo da folha de cálculo.

> [!IMPORTANT]  
> Tez não é usado para resolver todas as consultas. Você pode resolver muitas consultas sem usar Tez.

## <a name="view-job-history"></a>Ver histórico de tarefas

O separador __Jobs__ exibe uma história de consultas de Colmeia.

![Apache Hive ver empregos tab história](./media/apache-hadoop-use-hive-ambari-view/apache-hive-job-history.png)

## <a name="database-tables"></a>Tabelas de bases de dados

Pode utilizar o separador __Tabelas__ para trabalhar com tabelas dentro de uma base de dados da Colmeia.

![Imagem do separador tabelas apache colmeia](./media/apache-hadoop-use-hive-ambari-view/hdinsight-tables-tab.png)

## <a name="saved-queries"></a>Consultas salvas

A partir do separador **Consulta,** pode guardar opcionais consultas. Depois de guardar uma consulta, pode reutilizá-la a partir do separador __Consultas Salvas.__

![Vistas apache Hive salvou separador de consultas](./media/apache-hadoop-use-hive-ambari-view/ambari-saved-queries.png)

> [!TIP]  
> As consultas guardadas são armazenadas no armazenamento de cluster padrão. Pode encontrar as consultas guardadas no `/user/<username>/hive/scripts` caminho. Estes são armazenados como ficheiros de texto `.hql` simples.
>
> Se eliminar o cluster, mas mantiver o armazenamento, pode utilizar um utilitário como [O Explorador de Armazenamento de Azure](https://azure.microsoft.com/features/storage-explorer/) ou Data Lake Storage Explorer (do Portal [Azure)](https://portal.azure.com)para recuperar as consultas.

## <a name="user-defined-functions"></a>Funções definidas pelo utilizador

Pode estender a Colmeia através de funções definidas pelo utilizador (UDF). Use um UDF para implementar funcionalidade ou lógica que não seja facilmente modelada no HiveQL.

Declare e guarde um conjunto de UDFs utilizando o separador **UDF** no topo da Hive View. Estes UDFs podem ser usados com o **Editor de Consulta.**

![Apache Hive ver ecrã do separador UDFs](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Um botão **insira o udfs** aparece na parte inferior do **Editor de Consulta**. Esta entrada apresenta uma lista de down-down dos UDFs definidos na Vista de Colmeia. A seleção de um UDF adiciona declarações de HiveQL à sua consulta para ativar o UDF.

Por exemplo, se definiu um UDF com as seguintes propriedades:

* Nome do recurso: myudfs

* Caminho do recurso: /myudfs.jar

* Nome UDF: myawesomeudf

* Nome da classe UDF: com.myudfs.Awesome

A utilização do botão **Insert udfs** exibe uma entrada denominada **myudfs,** com outra lista de descida para cada UDF definida para esse recurso. Neste caso, é **myawesomeudf.** A seleção desta entrada adiciona o seguinte ao início da consulta:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Em seguida, pode utilizar o UDF na sua consulta. Por exemplo, `SELECT myawesomeudf(name) FROM people;`.

Para obter mais informações sobre a utilização de UDFs com Hive em HDInsight, consulte os seguintes artigos:

* [Usando Python com Hive Apache e Porco Apache em HDInsight](python-udf-hdinsight.md)
* [Use um Java UDF com Colmeia Apache em HDInsight](./apache-hadoop-hive-java-udf.md)

## <a name="hive-settings"></a>Configurações da colmeia

Pode alterar várias definições de Hive, tais como alterar o motor de execução da Colmeia de Tez (o padrão) para MapReduce.

## <a name="next-steps"></a>Passos seguintes

Para informações gerais sobre a Colmeia em HDInsight:

* [Use a Colmeia Apache com Apache Hadoop em HDInsight](hdinsight-use-hive.md)
* [Utilizar o cliente do Apache Beeline com o Apache Hive](apache-hadoop-use-hive-beeline.md)
