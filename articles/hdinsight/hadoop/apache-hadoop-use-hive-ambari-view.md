---
title: Use a vista da colmeia Apache Ambari com apache Hadoop em Azure HDInsight
description: Saiba como usar a Vista da Colmeia a partir do seu navegador web para submeter consultas da Hive. A Vista Hive faz parte do Ambari Web UI fornecido com o seu cluster HDInsight baseado em Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/23/2020
ms.openlocfilehash: a230d4f775207afc37172dae9f0709d66a6d1aa1
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116626"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Utilizar a Vista do Apache Ambari Hive com o Apache Hadoop no HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Aprenda a executar consultas da Hive usando a Visão da Colmeia Apache Ambari. O Hive View permite-lhe autor, otimizar e executar consultas da Hive a partir do seu navegador web.

## <a name="prerequisites"></a>Pré-requisitos

Um aglomerado de Hadoop no HDInsight. Ver [Começar com HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

## <a name="run-a-hive-query"></a>Executar uma consulta do Hive

1. A partir do [portal Azure,](https://portal.azure.com/)selecione o seu cluster.  Consulte a Lista e mostre instruções aos [agrupamentos.](../hdinsight-administer-use-portal-linux.md#showClusters) O cluster é aberto numa nova vista do portal.

1. A partir de **dashboards Cluster,** selecione **vistas Ambari**. Quando solicitado a autenticar, utilize o nome `admin`da conta de login do cluster (predefinido ) e a palavra-passe que forneceu quando criou o cluster. Também pode navegar `https://CLUSTERNAME.azurehdinsight.net/#/main/views` no seu `CLUSTERNAME` navegador onde está o nome do seu cluster.

1. A partir da lista de pontos de vista, selecione __Hive View__.

    ![Apache Ambari seleciona vista Apache Hive](./media/apache-hadoop-use-hive-ambari-view/select-apache-hive-view.png)

    A página de visualização da Colmeia é semelhante à seguinte imagem:

    ![Imagem da folha de cálculo da consulta para a vista da Colmeia](./media/apache-hadoop-use-hive-ambari-view/ambari-worksheet-view.png)

1. A partir do __separador Consulta,__ colá-cola as seguintes declarações da HiveQL na folha de cálculo:

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

    |Declaração | Descrição |
    |---|---|
    |MESA DE LANÇAMENTO|Elimina a tabela e o ficheiro de dados, caso a tabela já exista.|
    |CRIAR TABELA EXTERNA|Cria uma nova tabela "externa" na Colmeia. As tabelas externas armazenam apenas a definição de mesa na Colmeia. Os dados são deixados no local original.|
    |FORMATO DE LINHA|Mostra como os dados são formatados. Neste caso, os campos em cada tronco são separados por um espaço.|
    |LOCALIZAÇÃO ARMAZENADA COMO FICHEIRO DE TEXTO|Mostra onde os dados são armazenados, e que são armazenados como texto.|
    |SELECIONAR|Seleciona uma contagem de todas as linhas onde a coluna t4 contém o valor [ERROR].|

   > [!IMPORTANT]  
   > Deixe a seleção base de __dados__ __em padrão__. Os exemplos deste documento utilizam a base de dados predefinida incluída no HDInsight.

1. Para iniciar a consulta, selecione **Executar** abaixo da folha de cálculo. O botão fica laranja e o texto muda para **Parar**.

1. Depois de terminada a consulta, o separador **Resultados** apresenta os resultados da operação. O seguinte texto é o resultado da consulta:

        loglevel       count
        [ERROR]        3

    Pode utilizar o separador **LOG** para visualizar as informações de registo que o trabalho criou.

   > [!TIP]  
   > Faça o download ou economize resultados da caixa de diálogo drop-down das **Ações** no separador **Resultados.**

### <a name="visual-explain"></a>Explicação visual

Para visualizar o plano de consulta, selecione o separador **Explicação Visual** abaixo da folha de cálculo.

A visão **de Explicação Visual** da consulta pode ser útil para entender o fluxo de consultas complexas.

### <a name="tez-ui"></a>Tez UI

Para mostrar o Tez UI para a consulta, selecione o separador **Tez UI** abaixo da folha de cálculo.

> [!IMPORTANT]  
> Tez não é usado para resolver todas as consultas. Pode resolver muitas consultas sem usar o Tez.

## <a name="view-job-history"></a>Ver histórico de tarefas

O separador __Jobs__ exibe um histórico de consultas da Hive.

![Apache Hive ver empregos tab histórico](./media/apache-hadoop-use-hive-ambari-view/apache-hive-job-history.png)

## <a name="database-tables"></a>Tabelas de bases de dados

Pode utilizar o separador __Tabelas__ para trabalhar com as tabelas dentro de uma base de dados da Hive.

![Imagem do separador de mesas apache hive](./media/apache-hadoop-use-hive-ambari-view/hdinsight-tables-tab.png)

## <a name="saved-queries"></a>Consultas guardadas

A partir do separador **Consulta,** você pode opcionalmente guardar consultas. Depois de guardar uma consulta, pode reutilizá-la a partir do separador __"Perguntas Guardadas".__

![Vistas de Apache Hive guardadas separador de consultas](./media/apache-hadoop-use-hive-ambari-view/ambari-saved-queries.png)

> [!TIP]  
> As consultas guardadas são armazenadas no armazenamento de cluster predefinido. Pode encontrar as consultas guardadas `/user/<username>/hive/scripts`no caminho. Estes são armazenados como `.hql` ficheiros de texto simples.
>
> Se eliminar o cluster, mas manter o armazenamento, pode utilizar um utilitário como o [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) ou o Data Lake Storage Explorer (do [Portal Azure)](https://portal.azure.com)para recuperar as consultas.

## <a name="user-defined-functions"></a>Funções definidas pelo utilizador

Pode estender a Hive através de funções definidas pelo utilizador (UDF). Utilize uma UDF para implementar funcionalidade ou lógica que não seja facilmente modelada no HiveQL.

Declare e guarde um conjunto de UDFs utilizando o separador **UDF** no topo da Vista da Colmeia. Estes UDFs podem ser usados com o Editor de **Consulta.**

![Apache Hive visualizar uDFs separador](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Um botão **inserção udfs** aparece na parte inferior do Editor de **Consulta**. Esta entrada apresenta uma lista de uDFs definido soro na Vista da Colmeia. A seleção de um UDF adiciona declarações da HiveQL à sua consulta para ativar a UDF.

Por exemplo, se definiu uma UDF com as seguintes propriedades:

* Nome do recurso: myudfs

* Caminho de recursos: /myudfs.jar

* Nome da UDF: myawesomeudf

* Nome da classe UDF: com.myudfs.Awesome

A utilização do botão **Insert udfs** apresenta uma entrada chamada **myudfs,** com outra lista de drop-down para cada UDF definida para esse recurso. Neste caso, é **o meu incrível.** A seleção desta entrada adiciona o seguinte ao início da consulta:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Em seguida, pode usar a UDF na sua consulta. Por exemplo, `SELECT myawesomeudf(name) FROM people;`.

Para obter mais informações sobre a utilização de UDFs com Hive no HDInsight, consulte os seguintes artigos:

* [Usando Python com Apache Hive e Apache Pig em HDInsight](python-udf-hdinsight.md)
* [Use uma Java UDF com Hive Apache em HDInsight](./apache-hadoop-hive-java-udf.md)

## <a name="hive-settings"></a>Configurações da colmeia

Pode alterar várias definições da Colmeia, tais como alterar o motor de execução da Colmeia de Tez (o padrão) para MapReduce.

## <a name="next-steps"></a>Passos seguintes

Para informações gerais sobre a Hive no HDInsight:

* [Use a Colmeia Apache com Hadoop Apache no HDInsight](hdinsight-use-hive.md)
* [Utilizar o cliente do Apache Beeline com o Apache Hive](apache-hadoop-use-hive-beeline.md)
