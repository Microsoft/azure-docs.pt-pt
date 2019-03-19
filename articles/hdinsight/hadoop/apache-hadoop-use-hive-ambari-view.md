---
title: Utilizar vistas do Apache Ambari para trabalhar com o Hive no HDInsight (Apache Hadoop) - Azure
description: Saiba como utilizar a vista de ramo de registo de mensagens em fila do seu navegador da web para submeter consultas do Hive. A vista do Hive é parte da IU da Web do Ambari fornecido com o seu cluster do HDInsight baseado em Linux.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: cb68e93553be66d0d0be0edf61e491217bfe4d48
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58091312"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Utilize a vista do Apache Ambari Hive com o Apache Hadoop no HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Saiba como executar consultas do Hive com o Apache Ambari Hive View. A vista do Hive permite-lhe criar, otimizar e executar consultas do Hive a partir do seu navegador da web.

## <a name="prerequisites"></a>Pré-requisitos

* Um baseado em Linux Apache Hadoop em clusters do HDInsight versão 3.4 ou superior.

  > [!IMPORTANT]  
  > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* Um navegador da web

## <a name="run-a-hive-query"></a>Executar uma consulta do Hive

1. Abra o [Portal do Azure](https://portal.azure.com).

2. Selecione o cluster do HDInsight e, em seguida, selecione **vistas Ambari** partir do **ligações rápidas** secção.

    ![Seção links rápidos do portal](./media/apache-hadoop-use-hive-ambari-view/quicklinks.png)

    Quando lhe for pedido para autenticar, utilizar o início de sessão do cluster (predefinido `admin`) da conta nome e palavra-passe que indicou quando criou o cluster.

3. Na lista de vistas, selecione __vista do Hive__.

    ![A vista de ramo de registo selecionada](./media/apache-hadoop-use-hive-ambari-view/select-hive-view.png)

    A página de vista do Hive é semelhante à seguinte imagem:

    ![Imagem da folha de cálculo de consulta para a vista do Hive](./media/apache-hadoop-use-hive-ambari-view/ambari-hive-view.png)

4. Partir do __consulta__ separador, cole as seguintes declarações HiveQL na folha de cálculo:

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

    Essas instruções executam as seguintes ações:

   * `DROP TABLE`: Elimina a tabela e o arquivo de dados, no caso da tabela já existe.

   * `CREATE EXTERNAL TABLE`: Cria uma nova tabela de "externa" no Hive.
     Tabelas externas armazenam apenas a definição de tabela no Hive. Os dados são deixados na localização original.

   * `ROW FORMAT`: Mostra como os dados estiverem formatados. Neste caso, os campos em cada registo são separados por um espaço.

   * `STORED AS TEXTFILE LOCATION`: Mostra onde os dados são armazenados e que são armazenado como texto.

   * `SELECT`: Seleciona uma contagem de todas as linhas em que a coluna t4 contém o valor [erro].

     > [!IMPORTANT]  
     > Deixe o __base de dados__ seleção na __padrão__. Os exemplos neste documento utilizam a base de dados padrão incluído com o HDInsight.

5. Para iniciar a consulta, utilize o **Execute** botão abaixo da folha de cálculo. O botão fica laranja e o texto muda para **parar**.

6. Depois da consulta estiver concluída, o **resultados** guia exibe os resultados da operação. O texto seguinte é o resultado da consulta:

        loglevel       count
        [ERROR]        3

    Pode utilizar o **registos** separador para ver as informações de registo que criou a tarefa.

   > [!TIP]  
   > Baixe ou guardar resultados a partir do **guardar resultados** caixa de diálogo de lista suspensa no canto superior esquerdo do **resultados do processo de consulta** secção.

### <a name="visual-explain"></a>Explicar o elemento Visual

Para apresentar uma visualização do plano de consulta, selecione o **explicar Visual** separador abaixo da folha de cálculo.

O **explicar Visual** vista da consulta pode ser útil na compreensão do fluxo de consultas complexas. Pode ver um equivalente textual desta vista ao utilizar o **explicativo** botão no Editor de consultas.

### <a name="tez-ui"></a>IU do Tez

Para apresentar a IU do Tez para a consulta, selecione o **Tez** separador abaixo da folha de cálculo.

> [!IMPORTANT]  
> Tez não é utilizado para resolver todas as consultas. Pode resolver muitas consultas sem utilizar Tez. 

Se Tez foi usado para resolver a consulta, é apresentado o direcionado acíclicos Graph (DAG). Se desejar exibir o DAG para consultas que já executou no passado, ou se quiser depurar o processo de Tez, utilize o [Tez vista](../hdinsight-debug-ambari-tez-view.md) em vez disso.

## <a name="view-job-history"></a>Ver histórico de tarefas

O __tarefas__ separador apresenta um histórico das consultas do Hive.

![Imagem de histórico da tarefa](./media/apache-hadoop-use-hive-ambari-view/job-history.png)

## <a name="database-tables"></a>Tabelas de base de dados

Pode utilizar o __tabelas__ separador para trabalhar com tabelas numa base de dados do Hive.

![Imagem do separador de tabelas](./media/apache-hadoop-use-hive-ambari-view/tables.png)

## <a name="saved-queries"></a>Consultas guardadas

Partir do **consulta** separador, opcionalmente, pode guardar consultas. Depois de guardar uma consulta, é possível reutilizá-lo a partir da __consultas guardadas__ separador.

![Imagem do separador de consultas guardadas](./media/apache-hadoop-use-hive-ambari-view/saved-queries.png)

> [!TIP]  
> Consultas guardadas são armazenadas no armazenamento de cluster predefinido. Pode encontrar as consultas guardadas no caminho `/user/<username>/hive/scripts`. Eles são armazenados como texto sem formatação `.hql` ficheiros.
>
> Se eliminar o cluster, mas manter o armazenamento, pode utilizar um utilitário como [Explorador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) ou o Explorador de armazenamento do Data Lake (da [Portal do Azure](https://portal.azure.com)) para obter as consultas.

## <a name="user-defined-functions"></a>Funções definidas pelo utilizador

Pode estender do Hive através das funções definidas pelo utilizador (UDF). Utilize uma UDF para implementar a funcionalidade ou a lógica que não é facilmente modelada no HiveQL.

Declarar e guardar um conjunto de UDFs utilizando o **UDF** separador na parte superior da vista do Hive. Estas UDFs podem ser utilizados com o **Editor de consultas**.

![Imagem do separador UDF](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Depois de adicionar uma UDF para a vista do Hive, um **inserir udfs** botão aparece na parte inferior da **Editor de consultas**. Selecionar esta entrada apresenta uma lista de lista pendente de UDFs definidas na vista do Hive. Selecionar uma UDF adiciona declarações HiveQL para sua consulta para ativar a UDF.

Por exemplo, se as tiver definido uma UDF com as seguintes propriedades:

* Nome do recurso: myudfs

* Caminho do recurso: /myudfs.jar

* UDF name: myawesomeudf

* Nome da classe UDF: com.myudfs.Awesome

Utilizar o **inserir udfs** botão exibe uma entrada com o nome **myudfs**, com outro na lista pendente para cada UDF definido para esse recurso. Neste caso, é **myawesomeudf**. Selecionar esta entrada adiciona o seguinte para o início da consulta:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Em seguida, pode usar a UDF na sua consulta. Por exemplo, `SELECT myawesomeudf(name) FROM people;`.

Para obter mais informações sobre como utilizar UDFs com o Hive no HDInsight, consulte os artigos seguintes:

* [Utilizar Python com o Apache Hive e Apache Pig no HDInsight](python-udf-hdinsight.md)
* [Como adicionar um Apache Hive UDF personalizado ao HDInsight](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Definições do Hive

Pode alterar várias definições do Hive, tal como alterar o motor de execução para o Hive no Tez (predefinição) para MapReduce.

## <a id="nextsteps"></a>Passos seguintes

Para obter informações gerais sobre o Hive no HDInsight:

* [Utilizar o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras formas pode trabalhar com o Hadoop no HDInsight:

* [Utilizar o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)
* [Utilizar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)
