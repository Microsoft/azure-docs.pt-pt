---
title: Usar cluster de Azure HDInsight Hadoop em um conjunto de dados de 1 TB-processo de ciência de dado de equipe
description: Usando o processo de ciência de dados de equipe para um cenário de ponta a ponta que emprega um cluster Hadoop do HDInsight para criar e implantar um modelo usando um conjunto de dados de um grande (1 TB) publicamente disponível
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
ms.openlocfilehash: eca19b3774ad285cb143ffc2b6c53360bec85fa4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492358"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>O processo de ciência de dados de equipe em ação – usando um cluster Azure HDInsight Hadoop em um conjunto de dados de 1 TB

Este tutorial demonstra como usar o processo de ciência de dados de equipe em um cenário de ponta a ponta com um [cluster Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar, criar engenharia de recursos e reduzir dados de exemplo de um dos conjuntos de dado [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) publicamente disponíveis . Ele usa Azure Machine Learning para criar um modelo de classificação binária nesses dados. Ele também mostra como publicar um desses modelos como um serviço Web.

Também é possível usar um bloco de anotações IPython para realizar as tarefas apresentadas neste passo a passos. Os usuários que gostariam de experimentar essa abordagem devem consultar o [passo a passos Criteo usando um tópico de conexão ODBC do hive](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) .

## <a name="dataset"></a>Descrição do conjunto de Criteo
O Criteo data é um conjunto de dados de previsão de clique que é de aproximadamente 370GB de arquivos TSV compactados gzip (~ 1,3 TB descompactados), que abrangem mais de 4.300.000.000 registros. Ele é obtido de 24 dias após os dados de clique disponibilizados por [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/). Para a conveniência dos cientistas de dados, os dados disponíveis nos quais experimentamos foram descompactados.

Cada registro neste conjunto de registros contém 40 colunas:

* a primeira coluna é uma coluna de rótulo que indica se um usuário clica em um **Add** (valor 1) ou não clica em um (valor 0)
* as próximas 13 colunas são numéricas e
* últimas 26 são colunas categóricas

As colunas são anonimáveis e usam uma série de nomes enumerados: "Col1" (para a coluna de rótulo) como ' Col40 ' (para a última coluna categórica).

Aqui está um trecho das primeiras 20 colunas de duas observações (linhas) deste conjunto de registros:

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb

Há valores ausentes nas colunas numéricas e categóricas neste conjunto de DataSet. Um método simples para manipular os valores ausentes é descrito. Detalhes adicionais dos dados são explorados ao armazená-los em tabelas do hive.

**Definição:** *taxa de clickthrough (CTR):* é a porcentagem de cliques nos dados. Neste conjunto de Criteo, o CTR é de aproximadamente 3,3% ou 0, 33.

## <a name="mltasks"></a>Exemplos de tarefas de previsão
Dois problemas de previsão de exemplo são abordados neste passo a passos:

1. **Classificação binária**: prevê se um usuário clicou em uma adição:

   * Classe 0: nenhum clique
   * Classe 1: clique em
2. **Regressão**: prevê a probabilidade de um anúncio clicar em recursos do usuário.

## <a name="setup"></a>Configurar um cluster Hadoop do HDInsight para ciência de dados
**Observação:** Normalmente, essa é uma tarefa de **administrador** .

Configure seu ambiente de ciência de dados do Azure para criar soluções de análise preditiva com clusters HDInsight em três etapas:

1. [Criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md): essa conta de armazenamento é usada para armazenar dados no armazenamento de BLOBs do Azure. Os dados usados em clusters HDInsight são armazenados aqui.
2. [Personalizar clusters de Azure HDInsight Hadoop para ciência de dados](customize-hadoop-cluster.md): esta etapa cria um cluster de Azure HDInsight Hadoop com o Anaconda Python 2,7 de 64 bits instalado em todos os nós. Há duas etapas importantes (descritas neste tópico) a serem concluídas ao personalizar o cluster HDInsight.

   * Você deve vincular a conta de armazenamento criada na etapa 1 com o cluster HDInsight quando ele é criado. Essa conta de armazenamento é usada para acessar dados que podem ser processados no cluster.
   * Você deve habilitar o acesso remoto ao nó principal do cluster após sua criação. Lembre-se das credenciais de acesso remoto que você especificar aqui (diferentes das especificadas para o cluster na criação): você precisa delas para concluir os procedimentos a seguir.
3. [Criar um espaço de trabalho Azure Machine Learning Studio (clássico)](../studio/create-workspace.md): este Azure Machine Learning espaço de trabalho é usado para criar modelos de aprendizado de máquina após uma exploração inicial de dados e uma amostragem inativa no cluster HDInsight.

## <a name="getdata"></a>Obter e consumir dados de uma fonte pública
O conjunto de [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) pode ser acessado clicando no link, aceitando os termos de uso e fornecendo um nome. Um instantâneo do que isso parece é mostrado aqui:

![Aceitar os termos do Criteo](./media/hive-criteo-walkthrough/hLxfI2E.png)

Clique em **continuar para baixar** para ler mais sobre o conjunto de informações e sua disponibilidade.

Os dados residem em um local público [do armazenamento de BLOBs do Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) : wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. O "WASB" refere-se ao local do armazenamento de BLOBs do Azure.

1. Os dados nesse armazenamento de blob público consistem em três subpastas de dados descompactados.

   1. A subpasta *RAW/Count/* contém os primeiros 21 dias de dados-do dia\_00 a dia\_20
   2. A subpasta *RAW/Train/* consiste em um único dia de dados, dia\_21
   3. A subpasta *RAW/Test/* consiste em dois dias de dados, dia\_22 e dia\_23
2. Para aqueles que desejam começar com os dados de gzip brutos, eles também estão disponíveis na pasta principal *RAW/* as day_NN. gz, em que NN vai de 00 a 23.

Uma abordagem alternativa para acessar, explorar e modelar esses dados que não exigem downloads locais é explicada posteriormente neste passo a passos quando criamos tabelas Hive.

## <a name="login"></a>Faça logon no cluster cabeçalho
Para fazer logon no cabeçalho do cluster, use o [portal do Azure](https://ms.portal.azure.com) para localizar o cluster. Clique no ícone de elefante do HDInsight à esquerda e clique duas vezes no nome do cluster. Navegue até a guia **configuração** , clique duas vezes no ícone conectar na parte inferior da página e insira suas credenciais de acesso remoto quando solicitado. Isso leva você para o cabeçalho do cluster.

Aqui está a aparência de um primeiro logon típico no cluster cabeçalho:

![Fazer logon no cluster](./media/hive-criteo-walkthrough/Yys9Vvm.png)

À esquerda está a "linha de comando do Hadoop", que é nossa força de Observe duas URLs úteis: "status do Hadoop yarn" e "nó de nome do Hadoop". A URL de status yarn mostra o progresso do trabalho e a URL do nó de nome fornece detalhes sobre a configuração do cluster.

Agora você está pronto para iniciar a primeira parte do passo a passos: exploração de dados usando o hive e preparar dados para Azure Machine Learning.

## <a name="hive-db-tables"></a>Criar banco de dados e tabelas do hive
Para criar tabelas do hive para nosso conjunto de Criteo, abra a ***linha de comando do Hadoop*** na área de trabalho do nó principal e insira o diretório do hive inserindo o comando

    cd %hive_home%\bin

> [!NOTE]
> Execute todos os comandos do hive neste passo a passos do prompt do diretório bin/do hive. Isso cuida de todos os problemas de caminho automaticamente. Você pode usar os termos "prompt do diretório do hive", "prompt do diretório bin/do hive" e "linha de comando do Hadoop" de maneira intercambiável.
>
> [!NOTE]
> Para executar qualquer consulta de Hive, é possível usar sempre os seguintes comandos:
>
>

        cd %hive_home%\bin
        hive

Após a replicação do hive aparecer com um sinal de "Hive >", basta recortar e colar a consulta para executá-la.

O código a seguir cria um banco de dados "criteo" e, em seguida, gera quatro tabelas:

* uma *tabela para gerar contagens* criadas no dia de dias\_00 a dia\_20,
* uma *tabela para uso como o conjunto* de acordos de treinamento criado no dia\_21 e
* duas *tabelas para uso como conjuntos de testes de conjunto de teste* criados no dia\_22 e dia\_23, respectivamente.

Divida o conjunto de teste em duas tabelas diferentes porque um dos dias é um feriado. O objetivo é determinar se o modelo pode detectar diferenças entre um feriado e um período de folga da taxa de clique.

O [&#95;exemplo de script&#95;Hive&#95;Create&#95;criteo&#95;Database&#95;e Tables. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) é exibido aqui para sua conveniência:

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

Todas essas tabelas são externas para que você possa simplesmente apontar para seus locais de armazenamento de BLOBs do Azure (WASB).

**Há duas maneiras de executar qualquer consulta de Hive:**

1. **Usando a linha de comando do hive repl**: a primeira é emitir um comando "Hive" e copiar e colar uma consulta na linha de comando do hive repl. Para fazer isso, faça o seguinte:

        cd %hive_home%\bin
        hive

     Agora, na linha de comando REPL, cortar e colar a consulta a executa.
2. **Salvando consultas em um arquivo e executando o comando**: a segunda é salvar as consultas em um arquivo. HQL ([Hive&#95;&#95;de exemplo&#95;,&#95;Create&#95;criteo&#95;Database e Tables. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) e, em seguida, emitir o seguinte comando para executar a consulta:

        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql

### <a name="confirm-database-and-table-creation"></a>Confirmar criação de banco de dados e tabela
Em seguida, confirme a criação do banco de dados com o seguinte comando no prompt do diretório bin/do hive:

        hive -e "show databases;"

Isso fornece:

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

Isso confirma a criação do novo banco de dados, "criteo".

Para ver quais tabelas foram criadas, basta emitir o comando aqui no prompt do diretório bin/do hive:

        hive -e "show tables in criteo;"

Em seguida, você verá a seguinte saída:

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

## <a name="exploration"></a>Exploração de dados no hive
Agora você está pronto para fazer uma exploração de dados básica no hive. Você começa contando o número de exemplos nas tabelas de dados de treinamento e teste.

### <a name="number-of-train-examples"></a>Número de exemplos de treinamento
O conteúdo dos [exemplos&#95;da&#95;tabela&#95;&#95;&#95;de treinamento de contagem de hive de exemplo. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) é mostrado aqui:

        SELECT COUNT(*) FROM criteo.criteo_train;

Isso produz:

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

Como alternativa, um também pode emitir o seguinte comando no prompt do diretório bin/do hive:

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Número de exemplos de teste nos dois conjuntos de testes de teste
Agora, conte o número de exemplos nos dois conjuntos de DataSets de teste. O conteúdo dos [exemplos&#95;de&#95;contagem&#95;Hive&#95;criteo&#95;teste&#95;de&#95;exemplo&#95;22 table examples. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) estão aqui:

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Isso produz:

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

Como de costume, você também pode chamar o script do prompt do diretório bin/do hive emitindo o comando:

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Por fim, você examina o número de exemplos de teste no conjunto de testes com base no dia\_23.

O comando para fazer isso é semelhante ao que acabou de ser mostrado (consulte [a&#95;contagem&#95;&#95;hive de&#95;exemplo&#95;criteo&#95;teste&#95;Day 23 examples. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Isso fornece:

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>Distribuição de rótulo no conjunto de
A distribuição de rótulo no conjunto de os DataSet é de interesse. Para ver isso, mostre o conteúdo [de&#95;exemplo&#95;Hive&#95;criteo&#95;rótulo&#95;de&#95;distribuição treinar tabela. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Isso produz a distribuição do rótulo:

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

Observe que a porcentagem de rótulos positivos é de cerca de 3,3% (consistente com o conjunto de informações original).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Distribuições de histograma de algumas variáveis numéricas no conjunto de caracteres de treinamento
Você pode usar a função nativa "histograma\_numeric" do hive para descobrir a aparência da distribuição das variáveis numéricas. Aqui estão os conteúdos de [Sample&#95;Hive&#95;criteo&#95;histograma&#95;numeric. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

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

A combinação de exibição LATERAL – explodir no hive serve para produzir uma saída do tipo SQL em vez da lista usual. Observe que, nessa tabela, a primeira coluna corresponde ao centro do compartimento e a segunda à frequência de compartimento.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Percentuais aproximados de algumas variáveis numéricas no conjunto de e de treinamento
Além disso, o interesse com variáveis numéricas é o cálculo de percentils aproximados. O "percentil\_aproximadamente" do hive faz isso para nós. O conteúdo de [Sample&#95;Hive&#95;criteo&#95;de&#95;exemplo aproximado de percentils. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) é:

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Isso produz:

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

A distribuição de percentils está fortemente relacionada à distribuição de histograma de qualquer variável numérica normalmente.

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Localizar o número de valores exclusivos para algumas colunas categóricas no conjunto de linhas de treinamento
Continuando a exploração de dados, encontre, para algumas colunas categóricas, o número de valores exclusivos que eles levam. Para fazer isso, mostre o conteúdo [de&#95;Sample&#95;Hive&#95;criteo&#95;valores&#95;exclusivos categóricos. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Isso produz:

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

Observe que Col15 tem em 19m valores exclusivos! O uso de técnicas simples como "codificação One-Hot" para codificar essas variáveis categóricas de alta dimensional não é viável. Em particular, uma técnica poderosa e robusta chamada [aprendizado com contagens](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) para lidar com esse problema com eficiência é explicada e demonstrada.

Finalmente, examine também o número de valores exclusivos para algumas outras colunas categóricas. O conteúdo do [Hive&#95;&#95;de exemplo&#95;criteo&#95;valores&#95;exclusivos&#95;múltiplos categóricos. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) são:

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

Isso produz:

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

Novamente, observe que, exceto para Col20, todas as outras colunas têm muitos valores exclusivos.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Contagens de ocorrências de pares de variáveis categóricas no conjunto de e de treinamento

As contagens de coocorrências de pares de variáveis categóricas também são de interesse. Isso pode ser determinado usando o código no [Hive&#95;&#95;criteo&#95;de exemplo contagens&#95;&#95;categóricas emparelhadas. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Inverter ordem as contagens por sua ocorrência e examinar os 15 principais nesse caso. Isso nos dá:

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

## <a name="downsample"></a>Reduzir os conjuntos de valores para Azure Machine Learning
Depois de explorar os conjuntos de dados e demonstrado como fazer esse tipo de exploração para qualquer variável (incluindo combinações), diminua os conjuntos de valores para que os modelos no Azure Machine Learning possam ser compilados. Lembre-se de que o foco do problema é: dado um conjunto de atributos de exemplo (valores de recurso de Col2-Col40), preveja se Col1 é um 0 (não clique) ou um 1 (clique).

Para reduzir a amostra dos conjuntos de datas de treinamento e teste para 1% do tamanho original, use a função RAND () nativa do hive. O próximo script, [Sample&#95;Hive&#95;criteo&#95;Renovate&#95;Train&#95;DataSet. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) faz isso para o conjunto de os conjuntos de teste:

        CREATE TABLE criteo.criteo_train_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        ---Now downsample and store in this table

        INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

Isso produz:

        Time taken: 12.22 seconds
        Time taken: 298.98 seconds

O script [Sample&#95;Hive&#95;criteo&#95;&#95;reresolução&#95;de&#95;teste&#95;do dia 22 DataSet. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) faz isso para dados de teste, dia\_22:

        --- Now for test data (day_22)

        CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

Isso produz:

        Time taken: 1.22 seconds
        Time taken: 317.66 seconds


Por fim, o [exemplo&#95;de&#95;script&#95;Hive&#95;criteo&#95;reresolução do dia&#95;de&#95;teste 23 DataSet. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) faz isso para dados de teste, dia\_23:

        --- Finally test data day_23
        CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

Isso produz:

        Time taken: 1.86 seconds
        Time taken: 300.02 seconds

Com isso, você está pronto para usar nossos conjuntos de testes de treinamento e teste reduzidos para a criação de modelos no Azure Machine Learning.

Há um componente final importante antes de passar para Azure Machine Learning, que se refere à tabela de contagem. Na próxima subseção, a tabela de contagem é discutida em alguns detalhes.

## <a name="count"></a>Uma breve discussão sobre a tabela de contagem
Como vimos, várias variáveis categóricas têm uma dimensionalidade muito alta. No passo a passo, uma técnica poderosa chamada [aprendizado com contagens](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) para codificar essas variáveis de maneira eficiente e robusta é apresentada. Mais informações sobre essa técnica estão no link fornecido.

[!NOTE]
>Neste tutorial, o foco está no uso de tabelas de contagem para produzir representações compactas de recursos categóricos de alta dimensionalidade. Essa não é a única maneira de codificar recursos categóricos; para obter mais informações sobre outras técnicas, os usuários interessados podem conferir [a codificação One-Hot-Encoding](https://en.wikipedia.org/wiki/One-hot) e o [hash de recursos](https://en.wikipedia.org/wiki/Feature_hashing).
>

Para criar tabelas de contagem nos dados de contagem, use os dados na pasta RAW/Count. Na seção modelagem, os usuários são mostrados como criar essas tabelas de contagem para recursos categóricos do zero ou como alternativa para usar uma tabela de contagem pré-criada para suas explorações. Em o que vem a seguir, quando "tabelas de contagem pré-criados" são referenciadas, queremos usar as tabelas de contagem que foram fornecidas. Instruções detalhadas sobre como acessar essas tabelas são fornecidas na próxima seção.

## <a name="aml"></a>Criar um modelo com Azure Machine Learning
Nosso processo de criação de modelo no Azure Machine Learning segue estas etapas:

1. [Obter os dados de tabelas do hive em Azure Machine Learning](#step1)
2. [Criar o experimento: limpar os dados e torná-lo um recurso com tabelas de contagem](#step2)
3. [Criar, treinar e pontuar o modelo](#step3)
4. [Avaliar o modelo](#step4)
5. [Publicar o modelo como um serviço Web](#step5)

Agora você está pronto para criar modelos no Azure Machine Learning Studio. Nossos dados de amostra abaixo são salvos como tabelas Hive no cluster. Use o módulo Azure Machine Learning **importar dados** para ler esses dados. As credenciais para acessar a conta de armazenamento desse cluster são fornecidas com o seguinte.

### <a name="step1"></a>Etapa 1: obter dados de tabelas do hive em Azure Machine Learning usando o módulo importar dados e selecioná-los para um experimento do Machine Learning
Comece selecionando um teste **+ novo** -> **experimento** -> **experimento em branco**. Em seguida, na caixa de **pesquisa** no canto superior esquerdo, pesquise "importar dados". Arraste e solte o módulo **importar dados** na tela do experimento (a parte intermediária da tela) para usar o módulo para acesso a dados.

É assim que os **dados de importação** se assemelham ao obter dados da tabela Hive:

![Importar dados obtém dados](./media/hive-criteo-walkthrough/i3zRaoj.png)

Para o módulo **importar dados** , os valores dos parâmetros que são fornecidos no gráfico são apenas exemplos da espécie de valores que você precisa fornecer. Aqui estão algumas diretrizes gerais sobre como preencher o conjunto de parâmetros para o módulo **importar dados** .

1. Escolha "consulta de Hive" para a **fonte de dados**
2. Na caixa **consulta do banco de dados do hive** , um simples SELECT * de < seu banco de dados do\_\_nome. sua tabela de\_\_nome >-é suficiente.
3. **URI do servidor Hcatalog**: se o cluster for "ABC", isso será simplesmente: https://abc.azurehdinsight.net
4. **Nome da conta de usuário do Hadoop**: o nome de usuário escolhido no momento da Comissão do cluster. (Não o nome de usuário do acesso remoto!)
5. **Senha da conta de usuário do Hadoop**: a senha para o nome de usuário escolhido no momento da Comissão do cluster. (Não a senha de acesso remoto!)
6. **Local dos dados de saída**: escolha "Azure"
7. **Nome da conta de armazenamento do Azure**: a conta de armazenamento associada ao cluster
8. **Chave de conta de armazenamento do Azure**: a chave da conta de armazenamento associada ao cluster.
9. **Nome do contêiner do Azure**: se o nome do cluster for "ABC", isso será simplesmente "ABC", geralmente.

Depois que os **dados de importação** terminarem de obter dados (você verá a marca de seleção verde no módulo), salve esses dados como um DataSet (com um nome de sua escolha). Como isso se parece:

![Importar dados salvar dados](./media/hive-criteo-walkthrough/oxM73Np.png)

Clique com o botão direito do mouse na porta de saída do módulo **importar dados** . Isso revela uma opção **salvar como conjunto** de e uma opção de **Visualização** . A opção **Visualizar** , se clicada, exibe 100 linhas dos dados, juntamente com um painel direito que é útil para algumas estatísticas de resumo. Para salvar dados, basta selecionar **salvar como DataSet** e seguir as instruções.

Para selecionar o conjunto de informações salvo para uso em um experimento do Machine Learning, localize os conjuntos de valores usando a caixa de **pesquisa** mostrada na figura a seguir. Em seguida, basta digitar o nome que você atribuiu ao conjunto de dado parcialmente para acessá-lo e arrastar o conjunto de dado para o painel principal. Soltá-lo no painel principal seleciona-o para uso na modelagem de aprendizado de máquina.

![Arraste DataSet para o painel principal](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> Faça isso para os conjuntos de testes de treinamento e de teste. Além disso, lembre-se de usar o nome do banco de dados e os nomes de tabela que você deu para essa finalidade. Os valores usados na figura são apenas para fins de ilustração. * *
>
>

### <a name="step2"></a>Etapa 2: criar um experimento simples em Azure Machine Learning para prever cliques/não clique
Nosso experimento Azure Machine Learning Studio (clássico) é semelhante ao seguinte:

![Machine Learning experimento](./media/hive-criteo-walkthrough/xRpVfrY.png)

Agora examine os principais componentes deste experimento. Arraste primeiro nossos conjuntos de testes de treinamento e teste salvos para a nossa tela de experimento.

#### <a name="clean-missing-data"></a>Limpar Dados em Falta
O módulo **limpar dados ausentes** faz o que seu nome sugere: limpa dados ausentes de maneiras que podem ser especificadas pelo usuário. Examine este módulo para ver isso:

![Limpar dados ausentes](./media/hive-criteo-walkthrough/0ycXod6.png)

Aqui, você optou por substituir todos os valores ausentes por 0. Também há outras opções, que podem ser vistas examinando os menus suspensos no módulo.

#### <a name="feature-engineering-on-the-data"></a>Engenharia de recursos nos dados
Pode haver milhões de valores exclusivos para alguns recursos categóricos de grandes conjuntos de altos. O uso de métodos simples, como codificação One-Hot para representar recursos categóricos altamente dimensionais, é totalmente impossível. Este tutorial demonstra como usar os recursos de contagem usando módulos internos de Azure Machine Learning para gerar representações compactas dessas variáveis categóricas de alta dimensionalidade. O resultado final é um tamanho de modelo menor, tempos de treinamento mais rápidos e métricas de desempenho que são bastante comparáveis ao uso de outras técnicas.

##### <a name="building-counting-transforms"></a>Criando transformações de contagem
Para criar recursos de contagem, use o módulo **criar transformação de contagem** que está disponível no Azure Machine Learning. O módulo é semelhante ao seguinte:

![Compilar propriedades do módulo transformação de contagem](./media/hive-criteo-walkthrough/e0eqKtZ.png)
![módulo criar transformação de contagem](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT]
> Na caixa **contar colunas** , insira as colunas nas quais você deseja realizar contagens. Normalmente, elas são (conforme mencionado) colunas categóricas de alta dimensionalidade. Lembre-se de que o conjunto de Criteo tem 26 colunas categóricas: de Col15 para Col40. Aqui, conte em todos eles e dê a seus índices (de 15 a 40 separados por vírgulas, conforme mostrado).
>

Para usar o módulo no modo MapReduce (apropriado para grandes conjuntos de altos), você precisa ter acesso a um cluster HDInsight Hadoop (aquele usado para exploração de recursos também pode ser reutilizado para essa finalidade) e suas credenciais. As figuras anteriores ilustram como os valores preenchidos se parecem (substitua os valores fornecidos para ilustração com aqueles relevantes para seu próprio caso de uso).

![Parâmetros do módulo](./media/hive-criteo-walkthrough/05IqySf.png)

A figura anterior mostra como inserir o local do blob de entrada. Esse local tem os dados reservados para a criação de tabelas de contagem no.

Quando esse módulo terminar a execução, salve a transformação para mais tarde clicando com o botão direito do mouse no módulo e selecionando a opção **salvar como transformação** :

![Opção "salvar como transformação"](./media/hive-criteo-walkthrough/IcVgvHR.png)

Em nossa arquitetura de experimento mostrada acima, o conjunto de "ytransform2" corresponde precisamente a uma transformação de contagem salva. Para o restante deste experimento, supõe-se que o leitor usou um módulo **criar transformação de contagem** em alguns dados para gerar contagens e, em seguida, pode usar essas contagens para gerar recursos de contagem nos conjuntos de dado de treinamento e teste.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Escolhendo quais recursos de contagem incluir como parte dos conjuntos de testes de treinamento e teste
Quando uma transformação de contagem estiver pronta, o usuário poderá escolher quais recursos incluir em seus conjuntos de testes de treinamento e teste usando o módulo **Modificar parâmetros de tabela de contagem** . Para fins de integridade, este módulo é mostrado aqui. Mas, em interesses de simplicidade, não o utiliza em nosso experimento.

![Modificar parâmetros da tabela de contagem](./media/hive-criteo-walkthrough/PfCHkVg.png)

Nesse caso, como pode ser visto, as chances de log devem ser usadas e a coluna de retirada é ignorada. Você também pode definir parâmetros como o limite de compartimento de lixo, quantos exemplos pseudo antes devem ser adicionados para suavização e se deseja usar qualquer ruído laplaciana ou não. Todos esses são recursos avançados e é importante observar que os valores padrão são um bom ponto de partida para os usuários que são novos para esse tipo de geração de recursos.

##### <a name="data-transformation-before-generating-the-count-features"></a>Transformação de dados antes de gerar os recursos de contagem
Agora, o foco está em um ponto importante sobre a transformação de nossos dados de treinamento e de teste antes de realmente gerar recursos de contagem. Observe que há dois módulos **Executar script R** usados antes que a transformação contagem seja aplicada aos nossos dados.

![Executar módulos de script R](./media/hive-criteo-walkthrough/aF59wbc.png)

Este é o primeiro script do R:

![Primeiro script R](./media/hive-criteo-walkthrough/3hkIoMx.png)

Este script R renomeia nossas colunas para os nomes "Col1" para "Col40". Isso ocorre porque a transformação de contagem espera nomes desse formato.

O segundo script R balanceia a distribuição entre as classes positivas e negativas (classes 1 e 0, respectivamente) por meio da amostragem da classe negativa. O script R aqui mostra como fazer isso:

![Segundo script R](./media/hive-criteo-walkthrough/91wvcwN.png)

Neste script R simples, a taxa de\_de\_de PDV neg "é usada para definir a quantidade de saldo entre as classes positiva e negativa. Isso é importante, pois melhorar o desequilíbrio de classe geralmente tem benefícios de desempenho para problemas de classificação em que a distribuição de classe está distorcida (Lembre-se de que, nesse caso, você tem 3,3% de classe positiva e 96,7% de classe negativa).

##### <a name="applying-the-count-transformation-on-our-data"></a>Aplicando a transformação de contagem em nossos dados
Por fim, você pode usar o módulo **aplicar transformação** para aplicar as transformações de contagem em nossos conjuntos de testes de treinamento e de teste. Esse módulo usa a transformação de contagem salva como uma entrada e os conjuntos de dados de treinamento ou de teste como a outra entrada e retorna dados com recursos de contagem. Ele é mostrado aqui:

![Aplicar módulo de transformação](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Um trecho de como os recursos de contagem se parecem
É instrutivo ver a aparência dos recursos de contagem em nosso caso. Aqui está um trecho disso:

![Contar recursos](./media/hive-criteo-walkthrough/FO1nNfw.png)

Esse trecho mostra que, para as colunas contadas em, você obtém as chances e a probabilidade de log, além dos inpensares relevantes.

Agora você está pronto para criar um modelo de Azure Machine Learning usando esses conjuntos de valores transformados. Na próxima seção mostra como isso pode ser feito.

### <a name="step3"></a>Etapa 3: criar, treinar e pontuar o modelo

#### <a name="choice-of-learner"></a>Opção de aprendiz
Primeiro, você precisa escolher um aprendiz. Use uma árvore de decisão aumentada de duas classes como nosso aprendiz. Aqui estão as opções padrão para este aprendiz:

![Parâmetros de árvore de decisão aumentada de duas classes](./media/hive-criteo-walkthrough/bH3ST2z.png)

Para o experimento, escolha os valores padrão. Observe que os padrões são geralmente significativos e uma boa maneira de obter linhas de base rápidas sobre o desempenho. Você pode melhorar o desempenho ao varrer parâmetros se optar por ter uma linha de base.

#### <a name="train-the-model"></a>Dar formação sobre o modelo
Para treinamento, basta invocar um módulo **modelo** de treinamento. As duas entradas para ela são o aprendiz da árvore de decisão aumentada de duas classes e nosso conjunto de informações de treinamento. Isso é mostrado aqui:

![Módulo treinar modelo](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>Pontuar o modelo
Depois de ter um modelo treinado, você estará pronto para pontuar o conjunto de teste e avaliar seu desempenho. Faça isso usando o módulo **modelo de Pontuação** mostrado na figura a seguir, juntamente com um módulo **modelo de avaliação** :

![Módulo do modelo de pontuação](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step4"></a>Etapa 4: avaliar o modelo
Por fim, você deve analisar o desempenho do modelo. Geralmente, para problemas de classificação de duas classes (binários), uma boa medida é o AUC. Para visualizar isso, conecte o módulo **modelo de Pontuação** a um módulo **modelo de avaliação** para isso. Clicar em **Visualizar** no módulo **modelo de avaliação** produz um gráfico como o seguinte:

![Avaliar modelo de BDT de módulo](./media/hive-criteo-walkthrough/0Tl0cdg.png)

Em problemas de classificação binária (ou duas classes), uma boa medida de precisão da previsão é a área sob a curva (AUC). A seção a seguir mostra nossos resultados usando esse modelo em nosso conjunto de testes. Para obter isso, clique com o botão direito do mouse na porta de saída do módulo **avaliar modelo** e, em seguida, **visualize**.

![Visualizar módulo de modelo de avaliação](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step5"></a>Etapa 5: publicar o modelo como um serviço Web
A capacidade de publicar um modelo de Azure Machine Learning como serviços Web com um mínimo de confusão é um recurso valioso para torná-lo amplamente disponível. Depois disso, qualquer pessoa pode fazer chamadas para o serviço Web com dados de entrada para os quais precisam de previsões e o serviço Web usa o modelo para retornar essas previsões.

Para fazer isso, primeiro salve nosso modelo treinado como um objeto de modelo treinado. Isso é feito clicando com o botão direito do mouse no módulo **modelo de treinamento** e usando a opção **salvar como modelo treinado** .

Em seguida, crie portas de entrada e saída para nosso serviço Web:

* uma porta de entrada usa dados da mesma forma que os dados para os quais você precisa de previsões
* uma porta de saída retorna os rótulos pontuados e as probabilidades associadas.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Selecione algumas linhas de dados para a porta de entrada
É conveniente usar um módulo **aplicar transformação SQL** para selecionar apenas 10 linhas para servir como os dados de porta de entrada. Selecione apenas essas linhas de dados para nossa porta de entrada usando a consulta SQL mostrada aqui:

![Dados de porta de entrada](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Serviço Web
Agora você está pronto para executar um pequeno experimento que pode ser usado para publicar nosso serviço Web.

#### <a name="generate-input-data-for-webservice"></a>Gerar dados de entrada para WebService
Como uma etapa inicial, como a tabela de contagem é grande, use algumas linhas de dados de teste e gere dados de saída com base em recursos de contagem. Isso pode servir como o formato de dados de entrada para nosso serviço Web. Isso é mostrado aqui:

![Criar dados de entrada do BDT](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> Para o formato de dados de entrada, use a saída do módulo **contagem Featurizer** . Quando esse experimento terminar a execução, salve a saída do módulo **Featurizer de contagem** como um conjunto de uma. Esse DataSet é usado para os dados de entrada no WebService.
>
>

#### <a name="scoring-experiment-for-publishing-webservice"></a>Experimento de Pontuação para publicar o WebService
Em primeiro lugar, ela é mostrada como isso é. A estrutura essencial é um módulo de **modelo de Pontuação** que aceita nosso objeto de modelo treinado e algumas linhas de dados de entrada que foram geradas nas etapas anteriores usando o módulo **Featurizer de contagem** . Use "selecionar colunas no conjunto de um" para projetar os rótulos pontuados e as probabilidades de pontuação.

![Selecionar Colunas no Conjunto de Dados](./media/hive-criteo-walkthrough/kRHrIbe.png)

Observe como o módulo **selecionar colunas no conjunto** de dados pode ser usado para a "filtragem" de saída de um DataSet. O conteúdo é mostrado aqui:

![Filtrando com o módulo selecionar colunas no conjunto de DataSet](./media/hive-criteo-walkthrough/oVUJC9K.png)

Para obter as portas de entrada e saída azuis, basta clicar em **preparar WebService** na parte inferior direita. A execução desse experimento também nos permite publicar o serviço Web: clique no ícone **publicar serviço Web** no canto inferior direito, mostrado aqui:

![Publicar serviço Web](./media/hive-criteo-walkthrough/WO0nens.png)

Depois que o WebService for publicado, seja redirecionado para uma página que tem a seguinte aparência:

![Painel do serviço Web](./media/hive-criteo-walkthrough/YKzxAA5.png)

Observe os dois links para WebServices no lado esquerdo:

* O serviço de **solicitação/resposta** (ou RRS) destina-se a previsões únicas e é o que foi utilizado neste workshop.
* O BES (serviço de **execução em lote** ) é usado para previsões de lote e requer que os dados de entrada usados para fazer previsões residam no armazenamento de BLOBs do Azure.

Clicar na **solicitação/resposta** de link nos leva a uma página que nos fornece código pré-configurado no C#, Python e R. Esse código pode ser usado convenientemente para fazer chamadas para o WebService. Observe que a chave de API nesta página precisa ser usada para autenticação.

É conveniente copiar esse código Python para uma nova célula no notebook IPython.

Aqui está um segmento de código Python com a chave de API correta.

![Código Python](./media/hive-criteo-walkthrough/f8N4L4g.png)

Observe que a chave de API padrão foi substituída pela chave de API do WebServices. Clicar em **executar** nessa célula em um notebook ipython produz a seguinte resposta:

![Resposta do IPython](./media/hive-criteo-walkthrough/KSxmia2.png)

Para os dois exemplos de teste que perguntaram (na estrutura JSON do script Python), você obtém respostas no formato "rótulos pontuados, probabilidades pontuadas". Nesse caso, foram escolhidos os valores padrão que o código pré-configurado fornece (0 para todas as colunas numéricas e a cadeia de caracteres "valor" para todas as colunas categóricas).

Isso conclui nosso passo a passos mostrando como lidar com conjuntos de grandes escalas usando Azure Machine Learning. Você começou com um terabyte de dados, construiu um modelo de previsão e o implantou como um serviço Web na nuvem.

