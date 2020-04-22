---
title: Use o Cluster hadoop Azure HDInsight em conjunto de dados de 1 TB - Processo de Ciência de Dados da Equipa
description: Utilizando o Processo de Ciência de Dados da Equipa para um cenário de ponta a ponta, empregando um cluster Hadoop HDInsight para construir e implantar um modelo usando um conjunto de dados grande (1 TB) disponível publicamente
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1198d3cc7ccc0013e7c894488027d8e162470247
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677591"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>O Processo de Ciência de Dados da Equipa em ação - Utilizando um Aglomerado de Hadoop Azure HDInsight num conjunto de dados de 1 TB

Este walkthrough demonstra como usar o Processo de Ciência de Dados da Equipa num cenário de ponta a ponta com um [cluster Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar, engenheiro de recursos e dados de amostras de um dos conjuntos de dados [criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) publicamente disponíveis. Utiliza o Azure Machine Learning para construir um modelo de classificação binária nestes dados. Também mostra como publicar um destes modelos como um serviço Web.

Também é possível usar um caderno IPython para realizar as tarefas apresentadas nesta passagem. Os utilizadores que gostariam de experimentar esta abordagem devem consultar o walkthrough criteo usando um tópico de [conexão Hive ODBC.](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb)

## <a name="criteo-dataset-description"></a><a name="dataset"></a>Descrição do conjunto de dados criteo
Os dados criteo são um conjunto de dados de previsão de clique que é de 370 GB de ficheiros TSV comprimidos gzip (~1,3 TB não comprimidos), compreendendo mais de 4,3 mil milhões de registos. É retirado a partir de 24 dias de dados de clique disponibilizados pela [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/). Para a conveniência dos cientistas de dados, os dados disponíveis para experimentar foram desapertados.

Cada registo neste conjunto de dados contém 40 colunas:

* a primeira coluna é uma coluna de etiquetas que indica se um utilizador clica num **add** (valor 1) ou não clica numa (valor 0)
* próximas 13 colunas são numéricas, e
* últimos 26 são colunas categóricas

As colunas são anoonizadas e utilizam uma série de nomes enumerados: "Col1" (para a coluna do rótulo) a 'Col40' (para a última coluna categórica).

Aqui está um excerto das primeiras 20 colunas de duas observações (linhas) deste conjunto de dados:

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb

Faltam valores tanto nas colunas numéricas como categóricas deste conjunto de dados. É descrito um método simples para lidar com os valores em falta. Detalhes adicionais dos dados são explorados ao armazená-los em mesas da Colmeia.

**Definição:** *Taxa de cliques (CTR):* Esta métrica é a percentagem de cliques nos dados. Neste conjunto de dados criteo, o CTR é de cerca de 3,3% ou 0,033.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Exemplos de tarefas de previsão
Dois problemas de previsão da amostra são abordados neste walkthrough:

1. **Classificação binária**: Prevê se um utilizador clicou num add:

   * Classe 0: Sem Clique
   * Classe 1: Clique
2. **Regressão**: Prevê a probabilidade de um clique de anúncio a partir das funcionalidades do utilizador.

## <a name="set-up-an-hdinsight-hadoop-cluster-for-data-science"></a><a name="setup"></a>Configurar um cluster Hadoop HDInsight para a ciência dos dados
> [!NOTE]
> Este passo é tipicamente uma tarefa **do Administrador.**

Instale o seu ambiente Azure Data Science para construir soluções de análise preditiva com clusters HDInsight em três etapas:

1. [Criar uma conta](../../storage/common/storage-account-create.md)de armazenamento : Esta conta de armazenamento é utilizada para armazenar dados no Armazenamento De Blob Azure. Os dados utilizados nos clusters HDInsight são armazenados aqui.
2. [Personalize os Clusters hadoop Azure HDInsight para data science](customize-hadoop-cluster.md): Este passo cria um cluster de Hadoop Azure HDInsight com 64 bits Anaconda Python 2.7 instalado em todos os nós. Há dois passos importantes (descritos neste tópico) para completar ao personalizar o cluster HDInsight.

   * Ligue a conta de armazenamento criada no passo 1 com o seu cluster HDInsight quando for criada. Esta conta de armazenamento é utilizada para aceder a dados que podem ser processados dentro do cluster.
   * Ativar o acesso remoto ao nó da cabeça do cluster após a sua criação. Lembre-se das credenciais de acesso remoto que especifica aqui (diferentedas das credenciais especificadas na criação do cluster): complete os seguintes procedimentos.
3. [Crie um espaço de trabalho Azure Machine Learning Studio (clássico)](../studio/create-workspace.md): Este espaço de trabalho Azure Machine Learning é utilizado para construir modelos de aprendizagem automática após uma primeira exploração de dados e amostragem no cluster HDInsight.

## <a name="get-and-consume-data-from-a-public-source"></a><a name="getdata"></a>Obtenha e consuma dados de uma fonte pública
O conjunto de dados [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) pode ser acedido clicando no link, aceitando os termos de utilização e fornecendo um nome. Uma foto é mostrada aqui:

![Aceitar termos Criteo](./media/hive-criteo-walkthrough/hLxfI2E.png)

Clique em **Continuar a Descarregar** para ler mais sobre o conjunto de dados e a sua disponibilidade.

Os dados residem num local de armazenamento wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/de [blob Azure:](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) . O "wasb" refere-se ao local de armazenamento de Blob Azure.

1. Os dados deste armazenamento de blob Azure consistem em três subpastas de dados desapertados.

   1. A subpasta *crua/contagem/* contém os primeiros\_21 dias\_de dados - do dia 00 ao dia 20
   2. A subpasta *crua/comboio/* consiste num único\_dia de dados, dia 21
   3. A subpasta *crua/teste/* consiste em dois\_dias de\_dados, dia 22 e dia 23
2. Os dados brutos do gzip também estão disponíveis na pasta principal *crua/* como day_NN.gz, onde a NN vai de 00 a 23.

Uma abordagem alternativa para aceder, explorar e modelar estes dados que não requerem quaisquer downloads locais é explicado mais tarde neste walkthrough quando criamos mesas de Colmeia.

## <a name="log-in-to-the-cluster-headnode"></a><a name="login"></a>Inicie sessão no headnode do cluster
Para iniciar sessão no cabeçada do cluster, utilize o [portal Azure](https://ms.portal.azure.com) para localizar o cluster. Clique no ícone do elefante HDInsight à esquerda e, em seguida, clique duas vezes no nome do seu cluster. Navegue para o separador **Configuração,** clique duas vezes no ícone CONNECT na parte inferior da página e introduza as suas credenciais de acesso remoto quando solicitado, levando-o ao headnode do cluster.

Aqui está o que um login típico primeiro para o nódoado cluster parece:

![Iniciar sessão em cluster](./media/hive-criteo-walkthrough/Yys9Vvm.png)

À esquerda está a "Linha de Comando Hadoop", que é o nosso cavalo de trabalho para a exploração de dados. Note dois URLs úteis - "Hadoop Yarn Status" e "Nó de Nome Hadoop". O URL do estado do fio mostra o progresso do trabalho e o URL do nó de nome dá detalhes sobre a configuração do cluster.

Agora está configurado e pronto para começar a primeira parte do walkthrough: a exploração de dados usando a Hive e obter dados prontos para o Azure Machine Learning.

## <a name="create-hive-database-and-tables"></a><a name="hive-db-tables"></a>Criar base de dados e tabelas da Hive
Para criar tabelas da Hive para o nosso conjunto de dados Criteo, abra a Linha de ***Comando Hadoop*** no ambiente de trabalho do nó de cabeça, e entre no diretório da Colmeia entrando no comando

    cd %hive_home%\bin

> [!NOTE]
> Executar todos os comandos da Hive neste passeio a partir do caixote da Colmeia/ aviso de diretório. Isto cuida automaticamente de quaisquer problemas de caminho. Pode utilizar os termos "Aviso de Diretório da Colmeia", "Hive bin/ diretório prompt", e "Hadoop Command Line" alternadamente.
>
> [!NOTE]
> Para executar qualquer consulta da Hive, pode-se sempre utilizar os seguintes comandos:
>
>        cd %hive_home%\bin
>        hive

Depois que o REPL da Colmeia aparece com um sinal de "colmeia >", basta cortar e colar a consulta para executá-la.

O seguinte código cria uma base de dados "criteo" e gera, em seguida, quatro tabelas:

* uma *tabela para gerar contagens* construídas nos dias\_00 a 20 dias,\_
* uma *tabela para uso como o conjunto de dados do comboio* construído no dia\_21, e
* duas *tabelas para utilização como conjuntos* de dados de teste construídos no dia\_22 e\_dia 23, respectivamente.

Divida o conjunto de dados do teste em duas tabelas diferentes porque um dos dias é feriado. O objetivo é determinar se o modelo pode detetar diferenças entre um feriado e o não feriado a partir da taxa de clique-through.

A amostra de guião [&#95;colmeia&#95;criar&#95;criteo&#95;base de dados&#95;e&#95;tabelas.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) é exibida aqui por conveniência:

    CREATE DATABASE IF NOT EXISTS criteo;
    DROP TABLE IF EXISTS criteo.criteo_count;
    CREATE TABLE criteo.criteo_count (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

    DROP TABLE IF EXISTS criteo.criteo_train;
    CREATE TABLE criteo.criteo_train (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

    DROP TABLE IF EXISTS criteo.criteo_test_day_22;
    CREATE TABLE criteo.criteo_test_day_22 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

    DROP TABLE IF EXISTS criteo.criteo_test_day_23;
    CREATE TABLE criteo.criteo_test_day_23 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

Todas estas tabelas são externas para que possa apontar para as suas localizações de Armazenamento De Blob Azure (wasb).

**Há duas maneiras de executar qualquer consulta da Colmeia:**

* **Utilização da linha de comando Hive REPL**: A primeira é emitir um comando de "colmeia" e copiar e colar uma consulta na linha de comando Hive REPL:

        cd %hive_home%\bin
        hive

     Agora, na linha de comando REPL, cortando e colando a consulta executa-a.
* **Guardar consultas num ficheiro e executar o comando**: O segundo é guardar as consultas para um ficheiro '.hql'[(amostra&#95;&#95;criação&#95;criteo&#95;base de dados&#95;e&#95;tabelas.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) e, em seguida, emitir o seguinte comando para executar a consulta:

        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql

### <a name="confirm-database-and-table-creation"></a>Confirmar base de dados e criação de tabelas
Em seguida, confirme a criação da base de dados com o seguinte comando a partir do ponto de vista do hive bin/diretório:

        hive -e "show databases;"

Isto dá:

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

Isto confirma a criação da nova base de dados, "criteo".

Para ver que mesas foram criadas, basta emitir o comando aqui a partir do hive bin/diretório prompt:

        hive -e "show tables in criteo;"

Em seguida, deve ver a seguinte saída:

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

## <a name="data-exploration-in-hive"></a><a name="exploration"></a>Exploração de dados na Colmeia
Agora está pronto para fazer uma exploração básica de dados na Colmeia. Começa-se por contar o número de exemplos nas tabelas de dados do comboio e do teste.

### <a name="number-of-train-examples"></a>Número de exemplos de comboios
O conteúdo da amostra&#95;&#95;contagem de [&#95;&#95;tabela&#95;exemplos.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) são mostrados aqui:

        SELECT COUNT(*) FROM criteo.criteo_train;

Isto rende:

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

Em alternativa, pode-se também emitir o seguinte comando do aviso de hive bin/diretório:

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Número de exemplos de teste nos dois conjuntos de dados de teste
Conte agora o número de exemplos nos dois conjuntos de dados de teste. O conteúdo da amostra&#95;a contagem de&#95;de [colmeia&#95;teste criteo&#95;&#95;dia&#95;22&#95;tabela&#95;exemplos.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) estão aqui:

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Isto rende:

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

Como de costume, pode também ligar para o guião a partir do caixote da colmeia/aviso de diretório, emitindo o comando:

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Finalmente, examina o número de exemplos de teste\_no conjunto de dados de teste com base no dia 23.

O comando para o fazer é semelhante ao mostrado (consulte a amostra&#95;&#95;contagem&#95;a contagem de [criteo&#95;&#95;dia&#95;23&#95;exemplos.hql):](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Isto dá:

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>Distribuição de etiquetas no conjunto de dados do comboio
A distribuição da etiqueta no conjunto de dados do comboio é interessante. Para ver isto, mostre o conteúdo da [amostra&#95;colmeia&#95;criteo&#95;etiqueta&#95;distribuição&#95;&#95;quadro de&#95;:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql)

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Isto produz a distribuição do rótulo:

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

A percentagem de rótulos positivos é de cerca de 3,3% (consistente com o conjunto de dados original).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Distribuição histograma de algumas variáveis numéricas no conjunto de dados do comboio
Você pode usar a função "histograma\_numérica" nativa da Hive para descobrir como é a distribuição das variáveis numéricas. Aqui está o conteúdo da [amostra&#95;colmeia&#95;criteo&#95;histograma&#95;numérico.hql:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql)

        SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
            (SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
            ) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

Isto produz o seguinte:

        26      155878415
        2606    92753
        6755    22086
        11202   6922
        14432   4163
        17815   2488
        21072   1901
        24113   1283
        27429   1225
        30818   906
        34512   723
        38026   387
        41007   290
        43417   312
        45797   571
        49819   428
        53505   328
        56853   527
        61004   160
        65510   3446
        Time taken: 317.851 seconds, Fetched: 20 row(s)

A VISÃO LATERAL - a combinação de explodir na Hive serve para produzir uma saída semelhante a SQL em vez da lista habitual. Nesta tabela, a primeira coluna corresponde ao centro do lixo e à segunda à frequência do caixote do lixo.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Percentículos aproximados de algumas variáveis numéricas no conjunto de dados do comboio
Também de interesse com variáveis numéricas é a computação de percentículos aproximados. O "percentil\_aprox" nativo da Hive faz isto por nós. O conteúdo da [amostra&#95;colmeia&#95;criteo&#95;percentil aproximada de&#95;.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) são:

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Isto rende:

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

A distribuição de percentículos está intimamente relacionada com a distribuição histograma de qualquer variável numérica geralmente.

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Encontre número de valores únicos para algumas colunas categóricas no conjunto de dados do comboio
Continuando a exploração de dados, encontre, para algumas colunas categóricas, o número de valores únicos que tomam. Para tal, mostre o conteúdo da [amostra&#95;colmeia&#95;criteo&#95;valores&#95;únicos&#95;categoricagóricos.hql:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql)

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Isto rende:

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

Col15 tem valores únicos de 19M! Usar técnicas ingénuas como "codificação one-hot" para codificar tais variáveis categóricas de alta dimensão não é viável. Em particular, uma técnica poderosa e robusta chamada [Learning With Counts](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) para enfrentar este problema de forma eficiente é explicada e demonstrada.

Finalmente, veja o número de valores únicos para outras colunas categóricas também. O conteúdo da [colmeia&#95;amostra&#95;criteo&#95;valores&#95;únicos&#95;múltiplos&#95;categóricos.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) são:

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

Isto rende:

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

Mais uma vez, note que, exceto Col20, todas as outras colunas têm muitos valores únicos.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Contagem de coocorrência de pares de variáveis categóricas no conjunto de dados do comboio

As distribuições de pares de variáveis categóricas também são interessantes. Isto pode ser determinado utilizando o código na [amostra&#95;colmeia&#95;criteo&#95;emparelhado&#95;&#95;contagens.hql:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql)

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Inverta as contagens pela sua ocorrência e olhe para o top 15 neste caso. Isto dá-nos:

        ad98e872        cea68cd3        8964458
        ad98e872        3dbb483e        8444762
        ad98e872        43ced263        3082503
        ad98e872        420acc05        2694489
        ad98e872        ac4c5591        2559535
        ad98e872        fb1e95da        2227216
        ad98e872        8af1edc8        1794955
        ad98e872        e56937ee        1643550
        ad98e872        d1fade1c        1348719
        ad98e872        977b4431        1115528
        e5f3fd8d        a15d1051        959252
        ad98e872        dd86c04a        872975
        349b3fec        a52ef97d        821062
        e5f3fd8d        a0aaffa6        792250
        265366bf        6f5c7c41        782142
        Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="down-sample-the-datasets-for-azure-machine-learning"></a><a name="downsample"></a>Abaixo a amostra dos conjuntos de dados para Azure Machine Learning
Tendo explorado os conjuntos de dados e demonstrado como fazer este tipo de exploração para quaisquer variáveis (incluindo combinações), aamostra os conjuntos de dados para que os modelos em Azure Machine Learning possam ser construídos. Lembre-se que o foco do problema é: dado um conjunto de atributos de exemplo (valores de características de Col2 - Col40), preveja se Col1 é um 0 (sem clique) ou um 1 (clique).

Para reduzir a amostra do comboio e testar os conjuntos de dados para 1% do tamanho original, utilize a função RAND nativa da Hive. O próximo guião, [amostra&#95;colmeia&#95;criteo&#95;downsample&#95;comboio&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) faz isto para o conjunto de dados do comboio:

        CREATE TABLE criteo.criteo_train_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        ---Now downsample and store in this table

        INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

Isto rende:

        Time taken: 12.22 seconds
        Time taken: 298.98 seconds

A amostra de guião [&#95;colmeia&#95;criteo&#95;&#95;teste&#95;dia&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) faz-o para os dados de teste, dia\_22:

        --- Now for test data (day_22)

        CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

Isto rende:

        Time taken: 1.22 seconds
        Time taken: 317.66 seconds


Finalmente, a amostra de guião [&#95;colmeia&#95;criteo&#95;teste&#95;&#95;dia&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) faz-o para os dados de teste, dia\_23:

        --- Finally test data day_23
        CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

Isto rende:

        Time taken: 1.86 seconds
        Time taken: 300.02 seconds

Com isto, está pronto para utilizar os nossos conjuntos de dados de treina-toque e teste para modelos de construção em Azure Machine Learning.

Há um componente último importante antes de passar para o Azure Machine Learning, que diz respeito à tabela de contagem. Na próxima subsecção, a mesa de contagem é discutida em alguns detalhes.

## <a name="a-brief-discussion-on-the-count-table"></a><a name="count"></a>Uma breve discussão sobre a mesa de contagem
Como viu, várias variáveis categóricas têm uma alta dimensionalidade. No walkthrough, é apresentada uma técnica poderosa chamada [Learning With Counts](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) para codificar estas variáveis de uma forma eficiente e robusta. Mais informações sobre esta técnica estão no link fornecido.

>[!NOTE]
>Neste passeio, o foco está em usar tabelas de contagem para produzir representações compactas de características categóricas de alta dimensão. Esta não é a única forma de codificar características categóricas; para obter mais informações sobre outras técnicas, os utilizadores interessados podem verificar a codificação e [o hashing](https://en.wikipedia.org/wiki/Feature_hashing)de características [one-hot-](https://en.wikipedia.org/wiki/One-hot)
>

Para construir tabelas de contagem nos dados da contagem, utilize os dados na pasta crua/contagem. Na secção de modelação, os utilizadores mostram como construir estas tabelas de contagem para características categóricas do zero, ou, em alternativa, usar uma tabela de contagem pré-construída para as suas explorações. No que se segue, quando se referem as "mesas de contagem pré-construídas", referimo-nos à utilização das tabelas de contagem que foram fornecidas. Na secção seguinte são fornecidas instruções detalhadas sobre como aceder a estas tabelas.

## <a name="build-a-model-with-azure-machine-learning"></a><a name="aml"></a>Construa um modelo com Aprendizagem automática Azure
O nosso processo de construção de modelos em Azure Machine Learning segue estes passos:

1. [Obtenha os dados das tabelas da Hive para a Aprendizagem automática de Azure](#step1)
2. [Criar a experiência: limpe os dados e torne-os uma característica com tabelas de contagem](#step2)
3. [Construir, treinar e marcar o modelo](#step3)
4. [Avaliar o modelo](#step4)
5. [Publique o modelo como um serviço web](#step5)

Agora está pronto para construir modelos no estúdio Azure Machine Learning. Os nossos dados amostrados são guardados como mesas da Colmeia no cluster. Utilize o módulo de dados de **importação** de aprendizagem automática Azure para ler estes dados. As credenciais de acesso à conta de armazenamento deste cluster são fornecidas no que se segue.

### <a name="step-1-get-data-from-hive-tables-into-azure-machine-learning-using-the-import-data-module-and-select-it-for-a-machine-learning-experiment"></a><a name="step1"></a>Passo 1: Obtenha dados das tabelas da Hive para o Azure Machine Learning utilizando o módulo de dados de importação e selecione-os para uma experiência de aprendizagem automática
Comece por selecionar uma**experiência em branco** **+NOVA** -> **EXPERIMENTO** -> . Em seguida, a partir da caixa de **pesquisa** no topo à esquerda, procure por "Dados de Importação". Arraste e deixe cair o módulo **de dados** de importação na tela de experiência (a parte central do ecrã) para utilizar o módulo para acesso a dados.

Isto é o que os Dados de **Importação** parecem ao obter dados da tabela Hive:

![Dados de Importação obtém dados](./media/hive-criteo-walkthrough/i3zRaoj.png)

Para o módulo dados de **importação,** os valores dos parâmetros fornecidos no gráfico são apenas exemplos do tipo de valores que precisa fornecer. Aqui está algumas orientações gerais sobre como preencher o parâmetro definido para o módulo **de dados de importação.**

1. Escolha "Consulta de Colmeia" para **Fonte de Dados**
2. Na caixa de consulta de base de dados\_da\_ **Hive,** basta um seleto simples * a partir <o nome da sua base de dados.o\_seu nome de mesa\_> - é suficiente.
3. **Hcatalog server URI**: Se o seu cluster é\/"abc", então isto é simplesmente: https: /abc.azurehdinsight.net
4. Nome da conta de **utilizador hadoop**: O nome de utilizador escolhido no momento da encomenda do cluster. (NÃO o nome do utilizador do Acesso Remoto!)
5. **Palavra-passe**da conta de utilizador hadoop : A palavra-passe para o nome de utilizador escolhido no momento da encomenda do cluster. (NÃO a senha de acesso remoto!)
6. **Localização dos dados de saída**: Escolha "Azure"
7. Nome da **conta de armazenamento azure**: A conta de armazenamento associada ao cluster
8. Chave da conta de **armazenamento azure**: A chave da conta de armazenamento associada ao cluster.
9. **Nome**do recipiente azul : Se o nome do cluster é "abc", então isto é simplesmente "abc", normalmente.

Assim que os **Dados de Importação** terminarem de obter dados (veja o tique verde no Módulo), guarde estes dados como dataset (com um nome à sua escolha). O que isto parece:

![Dados de Importação economizam dados](./media/hive-criteo-walkthrough/oxM73Np.png)

Clique à direita na porta de saída do módulo dados de **importação.** Isto revela uma opção **Save como dataset** e uma opção **Visualizar.** A opção **Visualizar,** se clicada, exibe 100 linhas dos dados, juntamente com um painel certo que é útil para algumas estatísticas sumárias. Para guardar dados, basta selecionar **Guardar como conjunto de dados** e seguir as instruções.

Para selecionar o conjunto de dados guardado para utilização numa experiência de aprendizagem automática, localize os conjuntos de dados utilizando a caixa **de pesquisa** mostrada na figura seguinte. Em seguida, basta escrever o nome que deu ao conjunto de dados parcialmente para aceder ao mesmo e arrastar o conjunto de dados para o painel principal. Deixá-lo no painel principal seleciona-o para utilização na modelação de machine learning.

![Arrastar o conjunto de dados para o painel principal](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> Faça isto tanto para o comboio como para os conjuntos de dados de teste. Além disso, lembre-se de usar o nome da base de dados e os nomes de tabela que deu para o efeito. Os valores utilizados na figura são exclusivamente para fins de ilustração.**
>
>

### <a name="step-2-create-an-experiment-in-azure-machine-learning-to-predict-clicks--no-clicks"></a><a name="step2"></a>Passo 2: Criar uma experiência em Azure Machine Learning para prever cliques / sem cliques
A nossa experiência Azure Machine Learning Studio (clássica) é assim:

![Experiência de Aprendizagem Automática](./media/hive-criteo-walkthrough/xRpVfrY.png)

Agora examina os componentes-chave desta experiência. Arraste os nossos conjuntos de dados de comboio sinuoso e teste para a nossa tela de experiência primeiro.

#### <a name="clean-missing-data"></a>Limpar Dados em Falta
O módulo **Clean Missing Data** faz o que o seu nome sugere: limpa os dados em falta de formaa que possa ser especificada pelo utilizador. Olhe para este módulo para ver isto:

![Limpar dados em falta](./media/hive-criteo-walkthrough/0ycXod6.png)

Aqui, opte por substituir todos os valores em falta por um 0. Existem também outras opções, que podem ser vistas olhando para as quedas no módulo.

#### <a name="feature-engineering-on-the-data"></a>Engenharia de recursos nos dados
Pode haver milhões de valores únicos para algumas características categóricas de grandes conjuntos de dados. A utilização de métodos ingénuos, como a codificação one-hot para representar tais características categóricas de alta dimensão, é totalmente inviável. Este walkthrough demonstra como usar as características da contagem usando módulos de Machine Learning Azure incorporados para gerar representações compactas destas variáveis categóricas de alta dimensão. O resultado final é um tamanho de modelo menor, tempos de treino mais rápidos e métricas de desempenho comparáveis à utilização de outras técnicas.

##### <a name="building-counting-transforms"></a>Contagem de edifícios transforma
Para construir funcionalidades de contagem, utilize o módulo **Build Counting Transform** que está disponível no Azure Machine Learning. O módulo é assim:

![Construir propriedades do](./media/hive-criteo-walkthrough/e0eqKtZ.png)
![módulo Transform Transform Construa o módulo Transform](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT]
> Na caixa das **colunas Count,** introduza as colunas que deseja executar. Tipicamente, estas são (como mencionado) colunas categóricas de alta dimensão. Lembre-se que o conjunto de dados criteo tem 26 colunas categóricas: de Col15 a Col40. Aqui, conte com todos eles e dê os seus índices (de 15 a 40 separados por vírgulas como mostrado).
>

Para utilizar o módulo no modo MapReduce (apropriado para grandes conjuntos de dados), precisa de acesso a um cluster Hadoop HDInsight (o utilizado para a exploração de funcionalidades também pode ser reutilizado para este fim) e as suas credenciais. Os números anteriores ilustram como são os valores preenchidos (substitua os valores fornecidos para ilustração com os relevantes para o seu próprio caso de utilização).

![Parâmetros do módulo](./media/hive-criteo-walkthrough/05IqySf.png)

A figura anterior mostra como entrar no local da bolha de entrada. Esta localização tem os dados reservados para tabelas de contagem de edifícios.

Quando este módulo terminar de funcionar, guarde a transformação para mais tarde clicando no módulo e selecionando a opção **Salvar as Transform:**

![Opção "Salvar como Transformar"](./media/hive-criteo-walkthrough/IcVgvHR.png)

Na nossa arquitetura de experimentação acima mostrada, o conjunto de dados "ytransform2" corresponde precisamente a uma transformação de contagem guardada. Para o resto desta experiência, presume-se que o leitor usou um módulo **Build Counting Transform** em alguns dados para gerar contagens, e pode então usar essas contagens para gerar funcionalidades de contagem no comboio e testar conjuntos de dados.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Escolher o que as características da contagem incluir como parte do comboio e conjuntos de dados de teste
Uma vez que uma contagem se transforme pronta, o utilizador pode escolher quais as funcionalidades a incluir no seu comboio e conjuntos de dados de teste utilizando o módulo de parâmetros de **contagem modificado.** Para a completude, este módulo é mostrado aqui. Mas por uma questão de simplicidade não a usas na nossa experiência.

![Modificar os parâmetros da tabela de contagem](./media/hive-criteo-walkthrough/PfCHkVg.png)

Neste caso, como se pode ver, as odds de registo devem ser usadas e a coluna de trás é ignorada. Também pode definir parâmetros como o limiar do caixote do lixo, quantos exemplos pseudo-anteriores adicionar para suavizar, e se usar qualquer ruído laplaciano ou não. Tudo isto são funcionalidades avançadas e é de notar que os valores predefinidos são um bom ponto de partida para os utilizadores que são novos neste tipo de geração de funcionalidades.

##### <a name="data-transformation-before-generating-the-count-features"></a>Transformação de dados antes de gerar as características da contagem
Agora, o foco está num ponto importante sobre a transformação dos nossos dados de comboio e teste antes de gerar realmente características de contagem. Existem dois módulos **execute R Script** usados antes da transformação da contagem ser aplicada aos nossos dados.

![Executar módulos de script R](./media/hive-criteo-walkthrough/aF59wbc.png)

Aqui está o primeiro guião R:

![Primeiro script R](./media/hive-criteo-walkthrough/3hkIoMx.png)

Este guião R renomea as nossas colunas para nomes "Col1" para "Col40". Isto porque a transformação da contagem espera nomes deste formato.

O segundo guião R equilibra a distribuição entre classes positivas e negativas (classes 1 e 0, respectivamente) através da redução da classe negativa. O guião R aqui mostra como fazer isto:

![Segundo roteiro R](./media/hive-criteo-walkthrough/91wvcwN.png)

Neste simples guião R,\_\_o "rácio pos neg" é usado para definir a quantidade de equilíbrio entre as classes positiva seletiva e negativa. Isto é importante, uma vez que a melhoria do desequilíbrio de classes geralmente tem benefícios de desempenho para problemas de classificação onde a distribuição de classes é distorcida (lembre-se que neste caso, você tem 3,3% de classe positiva e 96,7% classe negativa).

##### <a name="applying-the-count-transformation-on-our-data"></a>Aplicação da transformação da contagem nos nossos dados
Finalmente, pode utilizar o módulo **De Transformação de Aplicação** para aplicar as transformações da contagem em nosso comboio e testar conjuntos de dados. Este módulo leva a contagem guardada transformar-se como uma entrada e o comboio ou conjuntos de dados de teste como a outra entrada, e devolve dados com características de contagem. É mostrado aqui:

![Aplicar módulo de transformação](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Um excerto de como as características da contagem são
É instrutivo ver como são as características da contagem no nosso caso. Aqui está um excerto disto:

![Características do conde](./media/hive-criteo-walkthrough/FO1nNfw.png)

Este excerto mostra que para as colunas contadas, obtém-se as contagens e as odds de registo, além de quaisquer backoffs relevantes.

Está agora pronto para construir um modelo azure machine learning utilizando estes conjuntos de dados transformados. Na próxima secção mostra como isto pode ser feito.

### <a name="step-3-build-train-and-score-the-model"></a><a name="step3"></a>Passo 3: Construir, treinar e marcar o modelo

#### <a name="choice-of-learner"></a>Escolha do aluno
Primeiro, tens de escolher um aprendiz. Use uma árvore de decisão de duas classes como aprendiz. Aqui estão as opções padrão para este aprendiz:

![Parâmetros da árvore de decisão impulsionada de duas classes](./media/hive-criteo-walkthrough/bH3ST2z.png)

Para a experiência, escolha os valores padrão. Os incumprimentos são significativos e uma boa maneira de obter bases rápidas no desempenho. Pode melhorar o desempenho varrendo parâmetros se optar por uma vez que tenha uma linha de base.

#### <a name="train-the-model"></a>Dar formação sobre o modelo
Para treino, basta invocar um módulo **Modelo de Comboio.** As duas inputs são o aprendiz de árvore de decisão de duas classes e o nosso conjunto de dados do comboio. Isto é mostrado aqui:

![Módulo Modelo de Comboio](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>Pontuar o modelo
Uma vez que tenha um modelo treinado, está pronto para marcar no conjunto de dados do teste e avaliar o seu desempenho. Faça-o utilizando o módulo **'Modelo de Pontuação'** mostrado na figura seguinte, juntamente com um módulo **Modelo de Avaliação:**

![Módulo do modelo de pontuação](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step-4-evaluate-the-model"></a><a name="step4"></a>Passo 4: Avaliar o modelo
Finalmente, deve analisar o desempenho do modelo. Normalmente, para dois problemas de classificação (binários), uma boa medida é a AUC. Para visualizar esta curva, ligue o módulo **'Modelo de Pontuação'** a um módulo **'Modelo de Avaliação'.** Clicar **visualizar** no módulo **Modelo de Avaliação** produz um gráfico como o seguinte:

![Avaliar o modelo BDT do módulo](./media/hive-criteo-walkthrough/0Tl0cdg.png)

Nos problemas binários de classificação (ou duas classes), uma boa medida de precisão de previsão é a Área Sob Curva (AUC). A secção seguinte mostra os nossos resultados utilizando este modelo no nosso conjunto de dados de teste. Clique à direita na porta de saída do módulo **Modelo de Avaliação** e, em seguida, **visualizar**.

![Visualizar o módulo modelo de avaliação](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step-5-publish-the-model-as-a-web-service"></a><a name="step5"></a>Passo 5: Publicar o modelo como um serviço Web
A capacidade de publicar um modelo Azure Machine Learning como serviços web com um mínimo de alarido é uma característica valiosa para torná-lo amplamente disponível. Uma vez feito, qualquer pessoa pode fazer chamadas para o serviço web com dados de entrada para os dados necessários, e o serviço web usa o modelo para devolver essas previsões.

Em primeiro lugar, guarde o nosso modelo treinado como um objeto modelo treinado clicando no módulo **Modelo de Comboio** e utilizando a opção Save as Trained **Model.**

Em seguida, crie portas de entrada e saída para o nosso serviço web:

* uma porta de entrada toma dados da mesma forma que os dados que você precisa de previsões para
* uma porta de saída devolve as Etiquetas Pontuadas e as probabilidades associadas.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Selecione algumas linhas de dados para a porta de entrada
É conveniente utilizar um módulo de **transformação SQL aplicar** para selecionar apenas 10 linhas para servir como dados da porta de entrada. Selecione apenas estas linhas de dados para a nossa porta de entrada usando a consulta SQL mostrada aqui:

![Dados da porta de entrada](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Serviço Web
Agora está pronto para fazer uma pequena experiência que pode ser usada para publicar o nosso serviço web.

#### <a name="generate-input-data-for-webservice"></a>Gerar dados de entrada para o serviço web
Como um passo zero, uma vez que a tabela de contagem é grande, pegue algumas linhas de dados de teste e gere dados de saída a partir dele com características de contagem. Esta saída pode servir como formato de dados de entrada para o nosso serviço web, como mostrado aqui:

![Criar dados de entrada BDT](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> Para o formato de dados de entrada, utilize a SAÍDA do módulo **Count Featurizer.** Uma vez que esta experiência termine de funcionar, guarde a saída do módulo **Count Featurizer** como dataset. Este Conjunto de Dados é utilizado para os dados de entrada no serviço web.
>
>

#### <a name="scoring-experiment-for-publishing-webservice"></a>Experiência de pontuação para publicação de webservice
Em primeiro lugar, a estrutura essencial é um módulo **De Modelo de Pontuação** que aceita o nosso objeto modelo treinado e algumas linhas de dados de entrada que foram gerados nos passos anteriores usando o módulo **Count Featurizer.** Utilize "Select Columns in Dataset" para projetar as etiquetas Pontuadas e as probabilidades de Pontuação.

![Selecionar Colunas no Conjunto de Dados](./media/hive-criteo-walkthrough/kRHrIbe.png)

Note como as **Colunas Select no** módulo Dataset podem ser utilizadas para 'filtrar' dados a partir de um conjunto de dados. Os conteúdos são mostrados aqui:

![Filtrar com as Colunas Selecionadas no módulo Dataset](./media/hive-criteo-walkthrough/oVUJC9K.png)

Para obter as portas de entrada e saída azuis, basta clicar em preparar o **webservice** na parte inferior direita. Executar esta experiência também nos permite publicar o serviço web: clique no ícone **PUBLISH WEB SERVICE** na parte inferior direita, mostrado aqui:

![Publicar serviço Web](./media/hive-criteo-walkthrough/WO0nens.png)

Uma vez publicado o serviço web, seja redirecionado para uma página que assim parece:

![Painel de instrumentos de serviço web](./media/hive-criteo-walkthrough/YKzxAA5.png)

Note os dois links para serviços web do lado esquerdo:

* O Serviço **DE PEDIDO/RESPOSTA** (ou RRS) destina-se a previsões únicas e é o que foi utilizado neste workshop.
* O Serviço de **EXECUÇÃO** DE LOTE (BES) é utilizado para previsões de lote e requer que os dados de entrada utilizados para fazer com que as previsões residam no Armazenamento de Blob Azure.

Clicar no link **REQUEST/RESPONSE** leva-nos a uma página que nos dá código pré-enlatado em C#, python e R. Este código pode ser convenientemente utilizado para fazer chamadas para o serviço web. A chave API nesta página precisa de ser utilizada para autenticação.

É conveniente copiar este código python para uma nova célula no caderno IPython.

Aqui está um segmento de código python com a chave API correta.

![Código Python](./media/hive-criteo-walkthrough/f8N4L4g.png)

A chave API padrão foi substituída pela chave API do nosso serviço web. Clicar **Correr** nesta célula num caderno IPython dá a seguinte resposta:

![Resposta IPython](./media/hive-criteo-walkthrough/KSxmia2.png)

Para os dois exemplos de teste questionados no quadro JSON do guião Python, obtém-se respostas de volta na forma "Etiquetas pontuadas, probabilidades pontuadas". Neste caso, foram escolhidos os valores predefinidos que o código pré-enlatado fornece (0's para todas as colunas numéricas e o "valor" da cadeia para todas as colunas categóricas).

Em conclusão, a nossa passagem mostra como lidar com um conjunto de dados em larga escala utilizando o Azure Machine Learning. Começou com um terabyte de dados, construiu um modelo de previsão, e implementou-o como um serviço web na nuvem.

