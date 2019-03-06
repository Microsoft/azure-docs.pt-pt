---
title: Utilizar o Cluster de Hadoop do HDInsight do Azure num conjunto de dados de 1 TB - Team Data Science Process
description: Usando o processo de ciência de dados de equipa para um cenário de ponto-a-ponto empregando um cluster de Hadoop do HDInsight para criar e implementar um modelo com um conjunto de dados publicamente disponível grande do (1 TB)
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/29/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 5cb3a029795dd69c80cfa580aa1bd135c67e609e
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451957"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>O processo de ciência de dados de equipa em ação – usando um Cluster de Hadoop do HDInsight do Azure num conjunto de dados de 1 TB

Estas instruções demonstram como utilizar o processo de ciência de dados de equipa num cenário ponto a ponto com uma [cluster do Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar, engenheiro de recursos e reduzir os dados de exemplo de um do publicamente disponíveis [ Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) conjuntos de dados. Ele utiliza o Azure Machine Learning para criar um modelo de classificação binária sobre estes dados. Ela também mostra como publicar um desses modelos como um serviço Web.

Também é possível utilizar um IPython notebook para realizar as tarefas apresentadas nestas instruções. Os utilizadores que gostariam de tentar esta abordagem devem consultar a [Criteo passo a passo através de uma ligação de ODBC do Hive](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) tópico.

## <a name="dataset"></a>Descrição do conjunto de dados de Criteo
Criteo dados são um conjunto de dados de predição de clique aproximadamente 370 GB de arquivos TSV comprimidos gzip (~1.3TB descomprimidos), que consiste em mais de mil milhões de 4.3 registos. Está a ser utilizado dos dias de 24 de clique em dados disponibilizados pelo [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/). Para a conveniência de cientistas de dados, os dados disponíveis para nós para experimentar foi descompactados.

Cada registo este conjunto de dados contém 40 colunas:

* a primeira coluna é uma coluna de etiqueta que indica se um utilizador clica num **adicionar** (valor de 1) ou não clica nelas (valor 0)
* em seguida 13 colunas sejam numéricas, e
* últimas 26 são colunas categóricas

As colunas são anónimos e usar uma série de nomes enumerados: "Col1" (para a coluna de etiqueta) para "Col40" (para a última coluna categórica).

Este é um trecho das 20 primeiros colunas de duas observações (linhas) deste conjunto de dados:

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb

Existem valores em falta nas colunas numéricas e categóricos este conjunto de dados. Um método simples para lidar com os valores em falta é descrito. Detalhes adicionais dos dados são exploradas quando armazená-los para tabelas do Hive.

**Definição:** *Taxa de Clickthrough (CTR):* Esta é a percentagem de cliques nos dados. Este conjunto de dados Criteo, o CTR é cerca de 3.3% ou 0.033.

## <a name="mltasks"></a>Exemplos de tarefas de predição
Dois problemas de predição de exemplo são abordados nestas instruções:

1. **Classificação binária**: Preveja se um usuário clicou um complemento:

   * Classe de 0: Não clique
   * Classe 1: Clique
2. **Regressão**: Prevê a probabilidade de um clique de ad de recursos do usuário.

## <a name="setup"></a>Cluster de cópia de segurança uma HDInsight Hadoop para ciência de dados
**Nota:** Normalmente, é uma **administrador** tarefas.

Configure o ambiente de ciência de dados do Azure para a criação de soluções de Análise Preditiva com clusters do HDInsight em três passos:

1. [Criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md): Esta conta de armazenamento é utilizada para armazenar dados no armazenamento de Blobs do Azure. Os dados usados em clusters do HDInsight são armazenados aqui.
2. [Personalizar Clusters do Hadoop do Azure HDInsight para ciência de dados](customize-hadoop-cluster.md): Este passo cria um cluster com 64 bits Anaconda Python 2.7 é instalado em todos os nós do Azure HDInsight Hadoop. Existem dois passos importantes (descritos neste tópico) para concluir ao personalizar o cluster do HDInsight.

   * Tem de associar a conta de armazenamento que criou no passo 1 com o seu cluster do HDInsight quando é criado. Esta conta de armazenamento é utilizada para aceder aos dados que podem ser processados dentro do cluster.
   * Tem de ativar o acesso remoto para o nó principal do cluster depois de criado. Lembre-se as credenciais de acesso remoto que especificar aqui (diferentes das especificado para o cluster em sua criação): necessário para concluir os procedimentos seguintes.
3. [Criar uma área de trabalho do Azure Machine Learning studio](../studio/create-workspace.md): Esta área de trabalho do Azure Machine Learning é utilizada para a criação de modelos de aprendizagem automática após uma exploração de dados inicial e para baixo de amostragem no cluster do HDInsight.

## <a name="getdata"></a>Obter e consumir dados de uma fonte público
O [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) conjunto de dados pode ser acedido ao clicar no link, aceitar os termos de utilização e fornecer um nome. Um instantâneo da aparência é mostrado aqui:

![Aceite os termos de Criteo](./media/hive-criteo-walkthrough/hLxfI2E.png)

Clique em **continuar para o Download** para ler mais sobre o conjunto de dados e sua disponibilidade.

Os dados residem num público [armazenamento de Blobs do Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) localização: wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. "wasb" refere-se para a localização de armazenamento de Blobs do Azure.

1. Os dados neste armazenamento de BLOBs público é composta por três subpastas de dados descompactados.

   1. A subpasta *não processados/contagem/* contém os primeiro 21 dias de dados, desde o primeiro dia\_00 ao dia\_20
   2. A subpasta *não processados/train/* consiste num único dia de dados, dia\_21
   3. A subpasta *não processados/teste/* consiste em dois dias de dados, dia\_22 e o dia\_23
2. Para aqueles que deseja iniciar com os dados não processados gzip, estes também estão disponíveis na pasta principal *não processados /* como day_NN.gz, onde o NN vai da 00 e 23.

Uma abordagem alternativa para aceder, explorar e o modelo de dados que não requerem quaisquer transferências locais são explicados posteriormente nestas instruções quando criamos tabelas do Hive.

## <a name="login"></a>Inicie sessão no nó principal do cluster
Para iniciar sessão no nó principal do cluster, utilize o [portal do Azure](https://ms.portal.azure.com) para localizar o cluster. Clique no ícone de elefante do HDInsight no lado esquerdo e, em seguida, faça duplo clique no nome do cluster. Navegue para o **configuração** separador, faça duplo clique no ícone de ligação na parte inferior da página e introduza as suas credenciais de acesso remoto quando lhe for pedido. Isto leva-o ao nó principal do cluster.

Eis o aspeto de um típico primeiro para iniciar sessão no nó principal do cluster:

![Inicie sessão no cluster](./media/hive-criteo-walkthrough/Yys9Vvm.png)

À esquerda é "Hadoop linha de comandos", que é o nosso trabalho pesado para a exploração de dados. Observe os dois URLs útil - "Hadoop Yarn Status" e "Hadoop nome de nó". O URL de estado do yarn mostra o progresso da tarefa e o URL do nome de nó fornece detalhes sobre a configuração do cluster.

Agora que estão configurados e prontos para começar a primeira parte do passo a passo: exploração de dados utilizando o Hive e a preparar dados para o Azure Machine Learning.

## <a name="hive-db-tables"></a> Criar base de dados do Hive e tabelas
Para criar tabelas do Hive para nosso conjunto de dados Criteo, abra a ***linha de comandos do Hadoop*** na área de trabalho do nó principal e introduza o diretório do Hive ao introduzir o comando

    cd %hive_home%\bin

> [!NOTE]
> Executar todos os comandos de Hive nestas instruções a partir do contentor de Hive / linha de comandos do diretório. Isso se encarrega de quaisquer problemas de caminho automaticamente. Pode utilizar os termos "Prompt de diretório do Hive", "Hive bin / linha de comandos do diretório" e "linha de comandos do Hadoop" alternadamente.
>
> [!NOTE]
> Para executar qualquer consulta de Hive, sempre é possível usar os seguintes comandos:
>
>

        cd %hive_home%\bin
        hive

Depois do REPL de Hive é apresentada com um "ramo de registo >"iniciar sessão, basta cortar e cole a consulta para executá-lo.

O código a seguir cria uma base de dados "criteo" e, em seguida, gera 4 tabelas:

* um *tabela para gerar contagens* criado no dia de dias\_00 ao dia\_20,
* um *tabela para utilização como o conjunto de dados de formação* criado no dia\_21, e
* dois *tabelas para utilização como os conjuntos de dados de teste* criado no dia\_22 e o dia\_23, respetivamente.

Divida o conjunto de dados de teste em duas tabelas diferentes porque um dos dias é um feriado. O objetivo consiste em determinar se o modelo pode detetar as diferenças entre um feriado e não feriado da taxa de cliques.

O script [exemplo&#95;hive&#95;criar&#95;criteo&#95;base de dados&#95;e&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) é apresentado aqui para conveniência:

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

Todas essas tabelas são externas para que pode simplesmente apontar para as respetivas localizações de armazenamento de Blobs do Azure (wasb).

**Existem duas formas de executar a consulta do Hive do qualquer:**

1. **Usando a linha de comandos de REPL Hive**: O primeiro é emitir um comando de "hive" e copie e cole uma consulta no REPL ramo de registo da linha de comandos. Para tal, execute:

        cd %hive_home%\bin
        hive

     Agora no REPL da linha de comandos, cortando e colando a consulta executa-o.
2. **Guardar consultas para um ficheiro e executar o comando**: A segunda é salvar as consultas num arquivo de .hql ([exemplo&#95;hive&#95;criar&#95;criteo&#95;base de dados&#95;e&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) e, em seguida, emita o comando seguinte para executar a consulta:

        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql

### <a name="confirm-database-and-table-creation"></a>Confirmar a criação de base de dados e tabela
Em seguida, confirme a criação da base de dados com o comando seguinte a partir do contentor de Hive / linha de comandos do diretório:

        hive -e "show databases;"

Isso fornece:

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

Isto confirma a criação da base de dados nova, "criteo".

Para ver que tabelas foram criadas, simplesmente emitir o comando do contentor de Hive / linha de comandos do diretório:

        hive -e "show tables in criteo;"

Em seguida, deverá ver o resultado seguinte:

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

## <a name="exploration"></a> Exploração de dados no Hive
Agora, está pronto para fazer alguma exploração de dados básica no Hive. Comece por contar o número de exemplos de formação e testar as tabelas de dados.

### <a name="number-of-train-examples"></a>Número de exemplos de train
O conteúdo do [exemplo&#95;hive&#95;contagem&#95;preparar&#95;tabela&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) são mostrados aqui:

        SELECT COUNT(*) FROM criteo.criteo_train;

Isso resulta em:

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

Em alternativa, um também pode emitir o comando seguinte a partir do contentor de Hive / linha de comandos do diretório:

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Número de exemplos de teste em dois conjuntos de dados de teste
Agora, conte o número de exemplos em dois conjuntos de dados de teste. O conteúdo do [exemplo&#95;hive&#95;contagem&#95;criteo&#95;de teste&#95;dia&#95;22&#95;tabela&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) estão aqui:

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Isso resulta em:

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

Como sempre, também pode chamar o script do contentor de Hive / diretório perguntar por emissão do comando:

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Por fim, examinar o número de exemplos de teste do conjunto de dados de teste com base no dia\_23.

O comando para fazer isso é semelhante à mostrada apenas (consulte a [exemplo&#95;hive&#95;contagem&#95;criteo&#95;de teste&#95;dia&#95;23&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Isso fornece:

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>Distribuição de etiquetas do conjunto de dados de formação
A distribuição de etiqueta no conjunto de dados train é de interesse. Para ver isso, Mostrar conteúdo do [exemplo&#95;hive&#95;criteo&#95;etiqueta&#95;distribuição&#95;treinar&#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Isso resulta na distribuição de etiqueta:

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

Tenha em atenção que a percentagem de etiquetas positivas é cerca de 3.3% (em conformidade com o conjunto de dados original).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Distribuições de histograma de algumas variáveis numéricas no conjunto de dados de formação
Pode usar nativo do Hive "histograma\_numérico" função para obter informações sobre a distribuição das variáveis numérico semelhante ao seguinte. Seguem-se o conteúdo do [exemplo&#95;hive&#95;criteo&#95;histograma&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

        SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
            (SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
            ) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

Isso produz o seguinte:

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

A LATERAL do Vista - explodir combinação no Hive serve para produzir uma saída semelhante a SQL em vez da lista habitual. Tenha em atenção que desta tabela, a primeira coluna corresponde ao centro de reciclagem e outra para a frequência de bin.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Percentis aproximadas de algumas variáveis numéricas no conjunto de dados de formação
Também é a computação de percentis aproximadas de interesse com variáveis numéricas. Ramo de registo do nativo "percentil\_aprox" faz isso para nós. O conteúdo do [exemplo&#95;hive&#95;criteo&#95;aproximado&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) são:

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Isso resulta em:

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

A distribuição de percentis está estritamente relacionada com a distribuição de histograma de qualquer variável numérica normalmente.

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Localizar o número de valores exclusivos para algumas colunas categóricas no conjunto de dados de formação
Continuar a exploração de dados, encontre, de para algumas colunas categóricas, o número de valores exclusivos, que eles levam. Para tal, Mostrar conteúdo do [exemplo&#95;hive&#95;criteo&#95;exclusivo&#95;valores&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Isso resulta em:

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

Tenha em atenção que Col15 tem valores exclusivos de 19M! Usando técnicas de ingênua, como "acesso frequente uma codificação" codificar altamente dimensionais categóricos variáveis não é viável. Em especial, chamado de uma técnica avançada e robusta [Learning com contagens](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) para lidar com esse problema com eficiência é explicado e demonstrada.

Por fim ver o número de valores exclusivos para algumas outras colunas categóricas também. O conteúdo do [exemplo&#95;hive&#95;criteo&#95;exclusivo&#95;valores&#95;vários&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) são:

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

Isso resulta em:

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

Novamente, tenha em atenção que, com exceção Col20, todas as outras colunas tem muitos valores exclusivos.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Contagens de ocorrência conjunta de pares de categóricas variáveis no conjunto de dados de formação

As contagens de ocorrência conjunta de pares de variáveis categóricas também é de interesse. Isto pode ser determinado com o código na [exemplo&#95;hive&#95;criteo&#95;emparelhadas&#95;categóricos&#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Inverso ordenar as contagens por sua ocorrência e ver a parte superior a 15 neste caso. Isso nos dá:

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

## <a name="downsample"></a> Os conjuntos de dados de exemplo para baixo para o Azure Machine Learning
Ter explorado os conjuntos de dados e demonstrou como fazer esse tipo de exploração de quaisquer variáveis (incluindo combinações), para baixo do exemplo de conjuntos de dados para que os modelos no Azure Machine Learning podem ser criados. Lembre-se que o foco do problema é: devido um conjunto de atributos de exemplo (valores da característica de Col2 - Col40), prever se Col1 é 0 (não clique) ou 1 (clique).

Para o comboio de exemplo e os conjuntos de dados de teste para 1% do tamanho original, utilizar a função RAND() nativa do Hive. O próximo script [exemplo&#95;hive&#95;criteo&#95;downsample&#95;treinar&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) faz isso para o conjunto de dados de treinamento:

        CREATE TABLE criteo.criteo_train_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        ---Now downsample and store in this table

        INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

Isso resulta em:

        Time taken: 12.22 seconds
        Time taken: 298.98 seconds

O script [exemplo&#95;hive&#95;criteo&#95;downsample&#95;de teste&#95;dia&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) faz tudo por dados de teste, dia\_22:

        --- Now for test data (day_22)

        CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

Isso resulta em:

        Time taken: 1.22 seconds
        Time taken: 317.66 seconds


Por fim, o script [exemplo&#95;hive&#95;criteo&#95;downsample&#95;de teste&#95;dia&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) faz tudo por dados de teste, dia\_23:

        --- Finally test data day_23
        CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

Isso resulta em:

        Time taken: 1.86 seconds
        Time taken: 300.02 seconds

Com isso, está pronto para utilizar o nosso train amostragem baixo e testar os conjuntos de dados para a criação de modelos no Azure Machine Learning.

Há um componente importante final antes de passar para o Azure Machine Learning, que diz respeito a tabela de contagem. Na próxima seção secundárias, a tabela contagem é abordada em detalhes.

## <a name="count"></a> Uma breve discussão sobre a tabela de contagem
Como viu, várias variáveis categóricas têm uma dimensionalidade muito alta. No passo a passo, uma técnica avançada chamado [Learning com contagens](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) para codificar estas variáveis numa eficiente, é apresentada a maneira robusta. Obter mais informações sobre essa técnica são a ligação indicada.

[!NOTE]
>Nestas instruções, o foco é sobre o uso tabelas de contagem para produzir compactas representações de recursos categóricos altamente dimensionais. Não é a única forma de codificar categóricas funcionalidades; Para obter mais informações sobre outras técnicas, podem consultar usuários interessados [um frequente-codificação](http://en.wikipedia.org/wiki/One-hot) e [hashing de funcionalidade](http://en.wikipedia.org/wiki/Feature_hashing).
>

Para criar tabelas de contagem dos dados de contagem, utilize os dados na pasta não processados/contagem de. Na secção de modelagem, os utilizadores são mostrados como criar estas tabelas de contagem de funcionalidades categóricas do zero, ou, em alternativa a utilizar uma tabela de contagem criados previamente para seus explorations. No que se segue, quando "previamente criados contagem tabelas" são chamados, queremos dizer usando as tabelas de contagem foram fornecidas. São fornecidas instruções detalhadas sobre como aceder essas tabelas na próxima seção.

## <a name="aml"></a> Criar um modelo com o Azure Machine Learning
O nosso modelo de criação de processo no Azure Machine Learning segue estes passos:

1. [Obter os dados de tabelas do Hive no Azure Machine Learning](#step1)
2. [Criar a experimentação: limpar os dados e torná-lo um recurso com tabelas de contagem](#step2)
3. [Criar, dar formação e Pontuar o modelo](#step3)
4. [Avaliar o modelo](#step4)
5. [Publique o modelo como um serviço web](#step5)

Agora, está pronto para criar modelos no Azure Machine Learning studio. Nossos dados de amostras baixo é salvo como tabelas do Hive no cluster. Utilizar o Azure Machine Learning **importar dados** módulo para ler estes dados. As credenciais para aceder à conta de armazenamento deste cluster são fornecidas no que se segue.

### <a name="step1"></a> Passo 1: Obter dados de tabelas do Hive no Azure Machine Learning utilizando o módulo importar dados e selecione-o para uma experimentação do machine learning
Comece por selecionar uma **+ novo** -> **experimentação** -> **experimentação em branco**. Em seguida, a partir da **pesquisa** caixa na parte superior esquerda e procure "Importar dados". Arraste e largue os **importar dados** módulo para a experimentação tela (a parte do meio do ecrã) para utilizar o módulo para acesso a dados.

Este é o que o **importar dados** é semelhante ao obter dados da tabela do Hive:

![Importar dados obtém os dados](./media/hive-criteo-walkthrough/i3zRaoj.png)

Para o **importar dados** módulo, os valores dos parâmetros que são fornecidos no gráfico são apenas exemplos de tipo de valores tem de fornecer. Aqui estão algumas Diretrizes gerais sobre como a preencher o parâmetro definido para o **importar dados** módulo.

1. Escolha "Consulta do Hive" para **origem de dados**
2. Na **consulta de base de dados do Hive** caixa, um simple, SELECIONE * FROM < sua\_base de dados\_name.your\_tabela\_nome >-é suficiente.
3. **URI do servidor de Hcatalog**: Se o cluster for "abc", em seguida, isso é simplesmente: https://abc.azurehdinsight.net
4. **Nome de conta de utilizador do Hadoop**: O nome de utilizador escolhido no momento de comissionamento o cluster. (Não o acesso remoto nome de utilizador!)
5. **Palavra-passe de conta de utilizador Hadoop**: A palavra-passe para o nome de utilizador escolhida no momento de comissionamento o cluster. (Não o acesso remoto palavra-passe!)
6. **Localização de dados de saída**: Escolha "Azure"
7. **Nome da conta de armazenamento do Azure**: A conta de armazenamento associada ao cluster
8. **Chave de conta de armazenamento do Azure**: A chave da conta de armazenamento associada ao cluster.
9. **Nome do contentor do Azure**: Se o nome do cluster é "abc", em seguida, simplesmente, "abc", normalmente é.

Uma vez a **importar dados** acabar de obtenção de dados (vir de escala verde no módulo), salvar esses dados como um conjunto de dados (com um nome à sua escolha). O que isso é semelhante a:

![Importar dados guardam dados](./media/hive-criteo-walkthrough/oxM73Np.png)

A porta de saída com o botão direito a **importar dados** módulo. Isso revela uma **guardar como conjunto de dados** opção e uma **Visualize** opção. O **Visualize** opção, se clicado, exibe 100 linhas de dados, juntamente com um painel direito, que é útil para algumas estatísticas de resumo. Para guardar os dados, basta selecionar **guardar como conjunto de dados** e siga as instruções.

Para selecionar o conjunto de dados guardado para uso numa experimentação do machine learning, localize os conjuntos de dados com o **pesquisa** caixa mostrada na figura a seguir. Em seguida, basta digitar o nome que deu o conjunto de dados parcialmente para acessá-lo e arrastar o conjunto de dados para o painel principal. Soltando-o para o painel principal, seleciona-o para utilização na modelagem do machine learning.

![Arraste o conjunto de dados para o painel principal](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> Fazê-lo para a formação e os conjuntos de dados de teste. Além disso, lembre-se utilizar o nome de base de dados e os nomes das tabelas que deu para esta finalidade. Os valores utilizados na figura são apenas para ilustração purposes.* *
>
>

### <a name="step2"></a> Passo 2: Criar uma experimentação simples no Azure Machine Learning para prever cliques / sem cliques
Nosso experimentação do Azure Machine Learning studio tem esta aparência:

![Experimentação do Machine Learning](./media/hive-criteo-walkthrough/xRpVfrY.png)

Agora examine os principais componentes desta experiência. Arraste a nossa formação guardada e teste primeiro a conjuntos de dados para nossa tela de experimentação.

#### <a name="clean-missing-data"></a>Limpar dados em falta
O **Clean Missing Data** módulo faz o que o nome sugere: ele limpa os dados em falta de formas que podem ser especificado pelo utilizador. Examine este módulo para ver isso:

![Apagar dados em falta](./media/hive-criteo-walkthrough/0ycXod6.png)

Aqui, optou por substituir todos os valores em falta com um 0. Existem outras opções, que podem ser vistas ao observar as listas pendentes no módulo.

#### <a name="feature-engineering-on-the-data"></a>"Feature Engineering" nos dados
Pode haver milhões de valores exclusivos para algumas funcionalidades categóricos de grandes conjuntos de dados. A utilização de métodos de ingênua como frequente de uma codificação para representar recursos altamente dimensionais categóricos é inteiramente impraticável. Estas instruções demonstram como usar os recursos de contagem usando módulos internos do Azure Machine Learning para gerar representações compactas destas variáveis categóricos altamente dimensionais. O resultado final é um tamanho mais pequeno do modelo, tempos mais rápidos de treinamento e métricas de desempenho que são bastante comparadas a usar outras técnicas.

##### <a name="building-counting-transforms"></a>Criando transformações de contagem
Para criar recursos de contagem, utilize o **criar contagem transformar** módulo que está disponível no Azure Machine Learning. O módulo tem esta aparência:

![Criar propriedades do módulo de contagem de transformar](./media/hive-criteo-walkthrough/e0eqKtZ.png)
![criar contagem transformar módulo](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT]
> Na **contagem de colunas** , introduza essas colunas que deseja realizar contagens em. Normalmente, estes são (conforme mencionado) altamente dimensionais colunas categóricas. Lembre-se de que o conjunto de dados Criteo tem 26 colunas categóricas: de Col15 para Col40. Aqui, pode contar com todos eles e dar a seus índices (a partir de 15 para 40 separados por vírgulas, conforme mostrado).
>

Para utilizar o módulo no modo de MapReduce (adequado para grandes conjuntos de dados), terá acesso a um cluster de Hadoop do HDInsight (utilizada para exploração de recurso pode ser reutilizada para essa finalidade também) e as suas credenciais. Os números anteriores mostram que os preenchidos valores como (substitua os valores fornecidos para fins de ilustração com àquelas relevantes para o seu caso de utilização).

![Parâmetros do módulo](./media/hive-criteo-walkthrough/05IqySf.png)

A figura anterior mostra como inserir a localização do blob de entrada. Esta localização tem os dados reservados para a criação de tabelas de contagem.

Quando este módulo termina a respetiva execução, guarde a transformação para mais tarde clicando com o botão direito do módulo e selecionando o **guardar como transformação** opção:

![Opção "Guardar como transformação"](./media/hive-criteo-walkthrough/IcVgvHR.png)

Em nossa arquitetura de experimentação mostrada acima, o conjunto de dados "ytransform2" corresponde precisamente para uma transformação de contagem guardado. Para o resto desta experiência, é assumido que o leitor é utilizada uma **criar contagem transformar** módulo em alguns dados para gerar contagens e pode usar essas contagens para gerar a contagem de recursos sobre os conjuntos de dados de formação e teste.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Escolher quais contagem de recursos para incluir como parte dos conjuntos de dados de formação e teste
Uma vez uma prontas de transformação de contagem, o usuário pode escolher quais recursos a incluir no raciocínio e testar a conjuntos de dados com o **modificar os parâmetros de tabela contagem** módulo. Para ser completo, este módulo é mostrado aqui. Mas, no interesse de simplicidade não, na verdade, utilizá-lo em nossa experiência.

![Modifique os parâmetros de tabela de contagem](./media/hive-criteo-walkthrough/PfCHkVg.png)

Neste caso, como pode ser visto, as probabilidades de log estão a ser utilizado e o término da coluna é ignorado. Também pode definir parâmetros, como o limiar de reciclagem de lixo, como muitos exemplos pseudo-anteriores para adicionar para suavização e se deve utilizar qualquer ruído Laplaciana ou não. Todas estas funcionalidades avançadas e está a ser observado que os valores predefinidos são um bom ponto de partida para os utilizadores que são novos para este tipo de geração de recursos.

##### <a name="data-transformation-before-generating-the-count-features"></a>Transformação de dados antes de gerar as funcionalidades de contagem
Agora o foco é um importante ponto sobre como transformar nosso train e testar dados antes de realmente gerando recursos de contagem. Tenha em atenção que existem duas **executar Script do R** módulos utilizados antes da transformação de contagem é aplicada aos nossos dados.

![Executar os módulos de R Script](./media/hive-criteo-walkthrough/aF59wbc.png)

Este é o primeiro script de R:

![Primeiro script de R](./media/hive-criteo-walkthrough/3hkIoMx.png)

Este script de R muda o nome de nossas colunas aos nomes de "Col1" para "Col40". Isto acontece porque a transformação de contagem espera nomes desse formato.

O segundo script de R equilibra a distribuição entre as classes de positivas e negativas (classes 1 e 0 respetivamente), a classe negativa de amostragem para baixo. O script R aqui mostra como fazer isso:

![Segundo script de R](./media/hive-criteo-walkthrough/91wvcwN.png)

Neste script de R simples, o "pos\_neg\_rácio" é usado para definir a quantidade de equilíbrio entre o positivo e as classes negativas. Isso é importante para fazer uma vez que melhorar desequilíbrio de classe normalmente tem benefícios de desempenho para problemas de classificação em que a distribuição de classe é inclinados (Lembre-se que neste caso, tem classe positivo do 3.3% e % de 96.7 de classe negativo).

##### <a name="applying-the-count-transformation-on-our-data"></a>Aplicar a transformação de contagem em nossos dados
Por fim, pode utilizar o **transformação de aplicar** módulo para aplicar as transformações de contagem em nossa train e conjuntos de dados de teste. Este módulo usa a transformação de contagem guardado como uma entrada e conjuntos de dados train ou de teste como outro entrada e retorna dados com funcionalidades de contagem. É mostrado aqui:

![Aplicar o módulo de transformação](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Aspeto de um trecho dos quais as funcionalidades de contagem
É instrutivo observar a aparência dos recursos de contagem em nosso caso. Este é um trecho isso:

![Funcionalidades de contagem](./media/hive-criteo-walkthrough/FO1nNfw.png)

Neste trecho mostra que para as colunas contabilizadas, obter as contagens e inicie sessão probabilidades, além de qualquer backoffs relevantes.

Está agora pronto para criar um modelo do Azure Machine Learning com estes conjuntos de dados transformados. Na próxima seção mostra como isso pode ser feito.

### <a name="step3"></a> Passo 3: Criar, dar formação e Pontuar o modelo

#### <a name="choice-of-learner"></a>Escolha de aprendiz
Em primeiro lugar, tem de escolher um aprendiz. Utilize uma árvore de decisões elevada de duas classes como nosso aprendiz. Aqui estão as opções predefinidas para esse Aprendiz:

![Parâmetros de árvore de decisões elevada de duas classes](./media/hive-criteo-walkthrough/bH3ST2z.png)

Para a experimentação, escolha os valores predefinidos. Tenha em atenção que as predefinições são, normalmente, com significado e uma boa maneira de obter linhas de base rápidas no desempenho. Pode melhorar o desempenho varrendo parâmetros, se optar por depois de ter uma linha de base.

#### <a name="train-the-model"></a>Dar formação sobre o modelo
Para obter treinamento, basta invocar um **Train Model** módulo. As duas entradas a ela são o aprendiz de árvore de decisões elevada de duas classes e o nosso conjunto de dados de formação. Isso é mostrado aqui:

![Módulo do modelo de formação](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>Pontuar o modelo
Depois de ter um modelo preparado, está pronto para classificar o conjunto de dados de teste e avaliar o desempenho dele. Fazer isso usando o **modelo de pontuação** módulo mostrado na figura a seguir, juntamente com um **Evaluate Model** módulo:

![Módulo do modelo de pontuação](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step4"></a> Passo 4: Avaliar o modelo
Por fim, deve analisar o desempenho do modelo. Normalmente, para problemas de classificação (binária) de classe dois, uma boa medida é o AUC. Para visualizar isso, conectar a **modelo de pontuação** módulo para um **Evaluate Model** módulo para isso. Clicar **Visualize** sobre o **Evaluate Model** módulo gera um gráfico semelhante ao seguinte:

![Avaliar modelo BDT de módulo](./media/hive-criteo-walkthrough/0Tl0cdg.png)

No binário (ou classe duas) problemas de classificação, uma boa medida de precisão de previsão é a área em curva (AUC). A seção a seguir mostra os resultados dos nossos usando esse modelo no nosso conjunto de dados de teste. Para obter isso, faça duplo clique na porta de saída do **Evaluate Model** módulo e, em seguida **Visualize**.

![Visualizar o módulo avaliar modelo](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step5"></a> Passo 5: Publique o modelo como um serviço Web
A capacidade de publicar um modelo do Azure Machine Learning como serviços da web com um mínimo de confusão é um recurso valioso para fazer amplamente disponíveis. Depois disso, qualquer pessoa pode fazer chamadas para o serviço web com dados de entrada que eles precisam previsões para e o serviço web usa o modelo para retornar as previsões de indisponibilidade.

Para fazer isso, primeiro a guardar o nosso modelo preparado como um objeto de modelo preparado. Isso é feito clicando com o **Train Model** módulo e utilizando o **guardar como modelo preparado** opção.

Em seguida, Criar entrada e saída portas para o nosso serviço da web:

* uma porta de entrada aceita dados a mesma forma que os dados que precisa previsões para
* uma porta de saída devolve as etiquetas classificadas e as probabilidades associadas.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Selecionar algumas linhas de dados para a porta de entrada
É conveniente utilizar um **transformação de SQL aplicar** módulo para selecionar apenas 10 linhas para servir os dados de porta de entrada. Selecione apenas essas linhas de dados para a nossa porta de entrada usando a consulta SQL mostrada aqui:

![Porta de entrada de dados](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Serviço Web
Agora, está pronto para executar uma experimentação pequeno que pode ser utilizada para publicar o nosso serviço da web.

#### <a name="generate-input-data-for-webservice"></a>Gerar dados de entrada do serviço Web
Como passo zeroth, uma vez que a tabela contagem for grande, poucas linhas de dados de teste e gerar dados de saída do mesmo com funcionalidades de contagem. Isso pode servir como o formato de dados de entrada para o nosso serviço Web. Isso é mostrado aqui:

![Criar dados de entrada BDT](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> Para o formato de dados de entrada, utilize a saída do **contagem Featurizer** módulo. Depois de esta experimentação estiver concluída, a executar, guardar o resultado dos **contagem Featurizer** módulo como um conjunto de dados. Este conjunto de dados é utilizado para os dados de entrada no serviço do Web.
>
>

#### <a name="scoring-experiment-for-publishing-webservice"></a>Experimentação de classificação do serviço Web de publicação
Em primeiro lugar, é mostrado como fica isso. A estrutura essencial é um **modelo de pontuação** módulo que aceita o nosso objeto de modelo preparado e algumas linhas de dados de entrada que foram geradas nos passos anteriores mediante a **contagem Featurizer** módulo. Utilize "Select Columns in Dataset" ao projeto as etiquetas de Scored e as probabilidades de pontuação.

![Selecionar colunas no conjunto de dados](./media/hive-criteo-walkthrough/kRHrIbe.png)

Observe como o **Select Columns in Dataset** módulo pode ser utilizado para "Filtrar" dados de um conjunto de dados. Os conteúdos são mostrados aqui:

![Com a selecionar colunas no conjunto de dados módulo de filtragem](./media/hive-criteo-walkthrough/oVUJC9K.png)

Para obter as portas de saída e entrada azul, basta clicar **preparar o serviço Web** na parte inferior direita. Esta experiência a executar também nos permite publicar o serviço web: clique a **publicar WEB SERVICE** ícone no parte inferior direita mostrado aqui:

![Publicar serviço Web](./media/hive-criteo-walkthrough/WO0nens.png)

Assim que o serviço Web é publicado, obter redirecionado para uma página que procura assim:

![Dashboard de serviço da Web](./media/hive-criteo-walkthrough/YKzxAA5.png)

Tenha em atenção as duas ligações para webservices no lado esquerdo:

* O **SOLICITAÇÃO/resposta** serviço (ou RRS) destina-se para predições únicas e é o que tem sido utilizado neste workshop.
* O **execução de lotes** Service (BES) é utilizado para previsões de batch e requer que os dados de entrada utilizada para fazer previsões residem no armazenamento de Blobs do Azure.

Clicar no link **SOLICITAÇÃO/resposta** leva a uma página que nos dá predefinidas código em c#, python e R. Este código pode ser convenientemente utilizado para fazer chamadas para o serviço Web. Tenha em atenção que a chave de API nesta página tem de ser utilizado para autenticação.

É conveniente Copie este código de python para uma nova célula no IPython notebook.

Aqui está um segmento de código de python com a chave de API correta.

![Código de Python](./media/hive-criteo-walkthrough/f8N4L4g.png)

Tenha em atenção que a chave de API predefinida foi substituída por chave de API do nosso webservices. Clicar **executar** nesta célula numa IPython notebook produz a seguinte resposta:

![Resposta de IPython](./media/hive-criteo-walkthrough/KSxmia2.png)

Para os dois exemplos mais frequentes sobre (na estrutura JSON do script de python) de teste, obtém respostas na forma "Scored etiquetas, as probabilidades de Scored". Neste caso, os valores predefinidos foram escolhidos que o código predefinido fornece (de 0 para todas as colunas numéricas e a cadeia de caracteres "valor" para todas as colunas categóricas).

Isto conclui o nosso passo a passo que mostra como lidar com o conjunto de dados em grande escala com o Azure Machine Learning. Utilizar um terabyte de dados, construído um modelo de predição e implementado como um serviço da web na cloud.

