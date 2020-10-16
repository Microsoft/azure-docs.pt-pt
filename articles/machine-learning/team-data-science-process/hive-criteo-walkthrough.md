---
title: Use cluster hadoop Azure HDInsight no conjunto de dados 1-TB - Processo de Ciência de Dados de Equipa
description: Utilizando o processo de ciência de dados de equipa para um cenário de ponta a ponta, empregando um cluster HDInsight Hadoop para construir e implantar um modelo utilizando um conjunto de dados disponível ao público de grande (1 TB)
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
ms.openlocfilehash: 99595e27b17db716b09325d5dd80633bf44ffb02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336654"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>O processo de ciência de dados da equipa em ação - Usando um cluster de hadoop Azure HDInsight num conjunto de dados de 1-TB

Este walkthrough demonstra como usar o Processo de Ciência de Dados de Equipa num cenário de ponta a ponta com um [cluster Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar, criar engenheiros e reduzir dados de amostras de um dos conjuntos de dados [criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) publicamente disponíveis. Utiliza a Azure Machine Learning para construir um modelo de classificação binária nestes dados. Também mostra como publicar um destes modelos como um serviço Web.

Também é possível usar um caderno IPython para realizar as tarefas apresentadas nesta passagem. Os utilizadores que gostariam de experimentar esta abordagem devem consultar o [criteo walkthrough usando um tópico de conexão Hive ODBC.](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb)

## <a name="criteo-dataset-description"></a><a name="dataset"></a>Descrição do conjunto de dados do Criteo
Os dados do Criteo são um conjunto de dados de previsão de clique que é de 370 GB de ficheiros de TSV comprimidos gzip (~1,3 TB descomprimidos), compreendendo mais de 4,3 mil milhões de registos. É retirado a partir de 24 dias de clique dados disponibilizados pela [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/). Para a conveniência dos cientistas de dados, os dados disponíveis para experimentar foram desapertados.

Cada registo deste conjunto de dados contém 40 colunas:

* a primeira coluna é uma coluna de etiqueta que indica se um utilizador clica num **adição** (valor 1) ou não clica numa (valor 0)
* as próximas 13 colunas são numéricas, e
* os últimos 26 são colunas categóricas

As colunas são anonimizadas e utilizam uma série de nomes enumerados: "Col1" (para a coluna do rótulo) a 'Col40' (para a última coluna categórica).

Aqui está um excerto das primeiras 20 colunas de duas observações (linhas) deste conjunto de dados:

> `Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20`
>
> `0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb`
>
> `0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb`

Faltam valores nas colunas numéricas e categóricas neste conjunto de dados. É descrito um método simples para manusear os valores em falta. Detalhes adicionais dos dados são explorados ao guardá-los em tabelas de Colmeia.

**Definição:** *Taxa clickthrough (CTR):* Esta métrica é a percentagem de cliques nos dados. Neste conjunto de dados criteo, o CTR é de cerca de 3,3% ou 0,033.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Exemplos de tarefas de previsão
Dois problemas de previsão da amostra são abordados nesta passagem:

1. **Classificação binária**: Prevê se um utilizador clicou num suplemento:

   * Classe 0: Sem Clique
   * Classe 1: Clique
2. **Regressão**: Prevê a probabilidade de um clique de anúncio a partir das funcionalidades do utilizador.

## <a name="set-up-an-hdinsight-hadoop-cluster-for-data-science"></a><a name="setup"></a>Configurar um cluster HDInsight Hadoop para a ciência dos dados
> [!NOTE]
> Este passo é tipicamente uma tarefa **de Administração.**

Crie o seu ambiente Azure Data Science para construir soluções de análise preditiva com clusters HDInsight em três etapas:

1. [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md): Esta conta de armazenamento é usada para armazenar dados no Azure Blob Storage. Os dados utilizados nos clusters HDInsight são armazenados aqui.
2. [Personalizar clusters Hadoop Azure HDInsight para a Ciência](customize-hadoop-cluster.md)dos Dados : Este passo cria um cluster Azure HDInsight Hadoop com 64 bits Anaconda Python 2.7 instalado em todos os nós. Existem dois passos importantes (descritos neste tópico) para completar ao personalizar o cluster HDInsight.

   * Ligue a conta de armazenamento criada no passo 1 com o seu cluster HDInsight quando for criada. Esta conta de armazenamento é utilizada para aceder a dados que podem ser processados dentro do cluster.
   * Ativar o acesso remoto ao nó de cabeça do cluster após a sua criação. Lembre-se das credenciais de acesso remoto que especifica aqui (diferentes das credenciais especificadas na criação do cluster): complete os seguintes procedimentos.
3. [Criar um espaço de trabalho Azure Machine Learning Studio (clássico):](../classic/create-workspace.md)Este espaço de trabalho Azure Machine Learning é utilizado para construir modelos de aprendizagem automática após uma exploração inicial de dados e amostragem no cluster HDInsight.

## <a name="get-and-consume-data-from-a-public-source"></a><a name="getdata"></a>Obtenha e consuma dados de uma fonte pública
O conjunto de dados [criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) pode ser acedido clicando no link, aceitando os termos de uso e fornecendo um nome. Um instantâneo é mostrado aqui:

![Aceitar termos criteo](./media/hive-criteo-walkthrough/hLxfI2E.png)

Clique **em Continuar a Baixar** para ler mais sobre o conjunto de dados e a sua disponibilidade.

Os dados residem num local de [armazenamento de bolhas Azure:](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/ . O "wasb" refere-se ao local de armazenamento da Mancha Azure.

1. Os dados deste armazenamento de bolhas Azure consistem em três sub-dobradeiras de dados não abertos.

   1. A sub-dobradeira *raw/count/* contém os primeiros 21 dias de dados - do dia \_ 00 ao dia \_ 20
   2. A sub-dobradeira *raw/train/* consiste num único dia de dados, dia \_ 21
   3. A sub-dobradeira *em bruto/teste/* consiste em dois dias de dados, dia \_ 22 e dia \_ 23
2. Os dados do gzip cru também estão disponíveis na pasta principal *em bruto/* como day_NN.gz, onde o NN vai de 00 a 23.

Uma abordagem alternativa ao acesso, exploração e modelação destes dados que não requer quaisquer downloads locais é explicado mais tarde nesta passagem quando criamos tabelas de Hive.

## <a name="log-in-to-the-cluster-headnode"></a><a name="login"></a>Faça login no headnode do cluster
Para iniciar sessão na cabeça do cluster, utilize o [portal Azure](https://ms.portal.azure.com) para localizar o cluster. Clique no ícone do elefante HDInsight à esquerda e, em seguida, clique duas vezes no nome do seu cluster. Navegue no separador **Configuração,** clique duas vezes no ícone CONNECT na parte inferior da página e introduza as suas credenciais de acesso remoto quando solicitado, levando-o para o cabeçalho do cluster.

Aqui está o que um primeiro login típico para o headnode cluster parece:

![Faça login no cluster](./media/hive-criteo-walkthrough/Yys9Vvm.png)

À esquerda está a "Linha de Comando Hadoop", que é o nosso cavalo de trabalho para a exploração de dados. Note dois URLs úteis - "Hadoop Yarn Status" e "Hadoop Name Node". O URL de estado do fio mostra o progresso do trabalho e o URL do nó nome dá detalhes sobre a configuração do cluster.

Agora está configurado e pronto para começar a primeira parte da passagem: exploração de dados usando a Hive e preparando os dados para o Azure Machine Learning.

## <a name="create-hive-database-and-tables"></a><a name="hive-db-tables"></a> Criar base de dados e tabelas de Colmeias
Para criar tabelas de Colmeia para o nosso conjunto de dados Criteo, abra a Linha de ***Comando Hadoop*** no ambiente de trabalho do nó de cabeça e insira o diretório da Colmeia inserindo o comando

```console
cd %hive_home%\bin
```

> [!NOTE]
> Executar todos os comandos da Hive nesta passagem a partir do bin Hive/diretório. Isto resolve automaticamente qualquer problema de caminho. Você pode usar os termos "Hive diretório prompt", "Hive bin/diretório", e "Hadoop Command Line" alternadamente.
>
> [!NOTE]
> Para executar qualquer consulta da Colmeia, pode-se sempre utilizar os seguintes comandos:
>
> `cd %hive_home%\bin`
> `hive`

Depois da Colmeia REPL aparecer com um sinal de "colmeia >", basta cortar e colar a consulta para executá-la.

O seguinte código cria uma base de dados "criteo" e gera então quatro tabelas:

* uma *tabela para gerar contagens* construídas nos dias \_ 00 até dia \_ 20,
* uma *tabela para uso como o conjunto de dados do comboio* construído no dia \_ 21, e
* duas *tabelas para utilização como conjuntos de dados de teste construídos* no dia \_ 22 e \_ dia 23, respectivamente.

Divida o conjunto de dados do teste em duas tabelas diferentes porque um dos dias é feriado. O objetivo é determinar se o modelo pode detetar diferenças entre umas férias e não férias a partir da taxa de clique.

A amostra de script [&#95;colmeia&#95;criar&#95;&#95;&#95;de base de dados criteo&#95;e&#95;.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) é apresentado aqui por conveniência:

```hiveql
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
```

Todas estas tabelas são externas para que possa indicar as suas localizações de Armazenamento (wasb) Azure Blob.

**Existem duas formas de executar qualquer consulta de Colmeia:**

* **Utilizando a linha de comando Hive REPL**: A primeira é emitir um comando "colmeia" e copiar e colar uma consulta na linha de comando Hive REPL:

  ```console
  cd %hive_home%\bin
  hive
  ```

     Agora, na linha de comando da REPL, cortar e colar a consulta executa-a.
* **Guardar consultas para um ficheiro e executar o comando**: A segunda é guardar as consultas para um ficheiro '.hql'[(amostra&#95;colmeia&#95;criar&#95;base de dados criteo&#95;&#95;e&#95;tabelas.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) e, em seguida, emitir o seguinte comando para executar a consulta:

  ```console
  hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql
  ```

### <a name="confirm-database-and-table-creation"></a>Confirmar base de dados e criação de tabelas
Em seguida, confirme a criação da base de dados com o seguinte comando a partir do bin/diretório hive:

```console
hive -e "show databases;"
```

Isto dá:

```output
criteo
default
Time taken: 1.25 seconds, Fetched: 2 row(s)
```

Isto confirma a criação da nova base de dados, "criteo".

Para ver que tabelas foram criadas, basta emitir o comando aqui a partir do bin/diretório hive:

```output
hive -e "show tables in criteo;"
```

Em seguida, deverá ver a seguinte saída:

```ouput
criteo_count
criteo_test_day_22
criteo_test_day_23
criteo_train
Time taken: 1.437 seconds, Fetched: 4 row(s)
```

## <a name="data-exploration-in-hive"></a><a name="exploration"></a> Exploração de dados na Colmeia
Agora está pronto para fazer uma exploração básica de dados na Colmeia. Começa-se por contar o número de exemplos nas tabelas de dados do comboio e do teste.

### <a name="number-of-train-examples"></a>Número de exemplos de comboios
O conteúdo da [amostra&#95;contagem de&#95;&#95;&#95;tabela&#95;exemplos.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) são mostrados aqui:

```hiveql
SELECT COUNT(*) FROM criteo.criteo_train;
```

Isto rende:

```output
192215183
Time taken: 264.154 seconds, Fetched: 1 row(s)
```

Em alternativa, pode-se também emitir o seguinte comando a partir do bin/diretório da Colmeia:

```console
hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql
```

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Número de exemplos de teste nos dois conjuntos de dados de teste
Agora conte o número de exemplos nos dois conjuntos de dados de teste. O conteúdo da [amostra&#95;contagem de&#95;&#95;teste criteo&#95;&#95;dia&#95;22&#95;tabela&#95;exemplos.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) estão aqui:

```hiveql
SELECT COUNT(*) FROM criteo.criteo_test_day_22;
```

Isto rende:

```output
189747893
Time taken: 267.968 seconds, Fetched: 1 row(s)
```

Como sempre, também pode ligar para o guião a partir do bin/diretório da Colmeia, emitindo o comando:

```console
hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql
```

Por fim, examina-se o número de exemplos de teste no conjunto de dados de teste com base no dia \_ 23.

O comando para o fazer é semelhante ao indicado (consulte a amostra&#95;contagem de&#95;&#95;[criteo&#95;&#95;dia&#95;23&#95;exemplos.hql):](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)

```hiveql
SELECT COUNT(*) FROM criteo.criteo_test_day_23;
```

Isto dá:

```output
178274637
Time taken: 253.089 seconds, Fetched: 1 row(s)
```

### <a name="label-distribution-in-the-train-dataset"></a>Distribuição de etiquetas no conjunto de dados do comboio
A distribuição da etiqueta no conjunto de dados do comboio é interessante. Para ver isto, mostre o conteúdo da [amostra&#95;colmeia&#95;etiqueta criteo&#95;&#95;distribuição&#95;comboio&#95;mesa.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

```hiveql
SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;
```

Isto dá a distribuição do rótulo:

```output
1       6292903
0       185922280
Time taken: 459.435 seconds, Fetched: 2 row(s)
```

A percentagem de rótulos positivos é de cerca de 3,3% (consistente com o conjunto de dados original).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Distribuição de histogramas de algumas variáveis numéricas no conjunto de dados do comboio
Você pode usar a função nativa "histograma numérica" da Hive \_ para descobrir como é a distribuição das variáveis numéricas. Aqui estão o conteúdo da [amostra&#95;colmeia&#95;criteo&#95;histograma&#95;nummeric.hql:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql)

```hiveql
SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
    (SELECT
    histogram_numeric(col2, 20) as col2_hist
    FROM
    criteo.criteo_train
    ) a
    LATERAL VIEW explode(col2_hist) exploded_table as hist;
```

Isto produz o seguinte:

```output
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
```

A VISTA LATERAL - a combinação de explosão em Hive serve para produzir uma saída semelhante ao SQL em vez da lista habitual. Nesta tabela, a primeira coluna corresponde ao centro do caixote do lixo e a segunda à frequência do caixote do lixo.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Percentiles aproximados de algumas variáveis numéricas no conjunto de dados do comboio
Também de interesse com variáveis numéricas é a computação de percentiles aproximados. O "percentil aprox" nativo da Colmeia \_ faz isto por nós. O conteúdo da [amostra&#95;colmeia&#95;criteo&#95;aproximadamente&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) são:

```hiveql
SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;
```

Isto rende:

```output
1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
Time taken: 564.953 seconds, Fetched: 1 row(s)
```

A distribuição de percentócitos está intimamente relacionada com a distribuição do histograma de qualquer variável numérica geralmente.

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Encontre o número de valores únicos para algumas colunas categóricas no conjunto de dados do comboio
Continuando a exploração de dados, encontre, para algumas colunas categóricas, o número de valores únicos que tomam. Para isso, mostre o conteúdo da [amostra&#95;colmeia&#95;criteo&#95;valores&#95;únicos&#95;categóricos.hql:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql)

```hiveql
SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;
```

Isto rende:

```output
19011825
Time taken: 448.116 seconds, Fetched: 1 row(s)
```

Col15 tem 19M valores únicos! Usar técnicas ingénuas como a "codificação de um só hot" para codificar tais variáveis categóricas de alta dimensão não é viável. Em particular, uma técnica poderosa e robusta chamada [Learning With Counts](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) para enfrentar este problema de forma eficiente é explicada e demonstrada.

Finalmente olhe para o número de valores únicos para algumas outras colunas categóricas também. O conteúdo da [amostra&#95;colmeia&#95;criteo&#95;valores&#95;únicos&#95;múltiplos&#95;categóricos.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) são:

```hiveql
SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
FROM criteo.criteo_train;
```

Isto rende:

```output
30935   15200   7349    20067   3
Time taken: 1933.883 seconds, Fetched: 1 row(s)
```

Mais uma vez, note que, exceto col20, todas as outras colunas têm muitos valores únicos.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Contagem de coocorrências de pares de variáveis categóricas no conjunto de dados do comboio

As distribuições de par de pares de variáveis categóricas também são de interesse. Isto pode ser determinado utilizando o código na [amostra&#95;colmeia&#95;criteo&#95;emparelhado&#95;categórico&#95;conta.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

```hiveql
SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;
```

Inverta as contagens pela sua ocorrência e olhe para o top 15 neste caso. Isto dá-nos:

```output
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
```

## <a name="down-sample-the-datasets-for-azure-machine-learning"></a><a name="downsample"></a> Para baixo, amostra os conjuntos de dados para Azure Machine Learning
Tendo explorado os conjuntos de dados e demonstrado como fazer este tipo de exploração para quaisquer variáveis (incluindo combinações), para baixo amostrar os conjuntos de dados para que os modelos em Azure Machine Learning possam ser construídos. Lembre-se que o foco do problema é: dado um conjunto de atributos de exemplo (valores de recurso de Col2 - Col40), prever se Col1 é um 0 (sem clique) ou um 1 (clique).

Para reduzir a amostra dos conjuntos de dados do comboio e do teste para 1% do tamanho original, utilize a função RAND nativa da Hive(). O próximo guião, [amostra&#95;colmeia&#95;criteo&#95;downsample&#95;comboio&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) faz isso para o conjunto de dados do comboio:

```hiveql
CREATE TABLE criteo.criteo_train_downsample_1perc (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;

---Now downsample and store in this table

INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;
```

Isto rende:

```output
Time taken: 12.22 seconds
Time taken: 298.98 seconds
```

A amostra de script [&#95;colmeia&#95;criteo&#95;downsample&#95;teste&#95;dia&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) faz-o para os dados do teste, dia \_ 22:

```hiveql
--- Now for test data (day_22)

CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;

INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;
```

Isto rende:

```output
Time taken: 1.22 seconds
Time taken: 317.66 seconds
```

Finalmente, a amostra de script [&#95;colmeia&#95;criteo&#95;downsample&#95;teste&#95;dia&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) faz-o para os dados de teste, dia \_ 23:

```hiveql
--- Finally test data day_23
CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;

INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;
```

Isto rende:

```output
Time taken: 1.86 seconds
Time taken: 300.02 seconds
```

Com isto, está pronto para usar o nosso comboio e conjuntos de dados de teste para a construção de modelos em Azure Machine Learning.

Há um componente importante final antes de passar para a Azure Machine Learning, que diz respeito à tabela de contagem. Na próxima subsecção, a tabela de contagem é discutida em alguns detalhes.

## <a name="a-brief-discussion-on-the-count-table"></a><a name="count"></a> Uma breve discussão na mesa de contagem
Como viu, várias variáveis categóricas têm uma alta dimensionalidade. No walkthrough, é apresentada uma poderosa técnica chamada [Learning With Counts](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) para codificar estas variáveis de forma eficiente e robusta. Mais informações sobre esta técnica estão no link fornecido.

>[!NOTE]
>Nesta passagem, o foco está em usar tabelas de contagem para produzir representações compactas de características categóricas de alta dimensão. Esta não é a única forma de codificar características categóricas; para obter mais informações sobre outras técnicas, os utilizadores interessados podem conferir mais informações sobre [a codificação](https://en.wikipedia.org/wiki/One-hot) e [o hashing de recursos.](https://en.wikipedia.org/wiki/Feature_hashing)
>

Para construir tabelas de contagem nos dados da contagem, utilize os dados na pasta em bruto/contagem. Na secção de modelação, os utilizadores são mostrados como construir estas tabelas de contagem para funcionalidades categóricas de raiz, ou, em alternativa, usar uma tabela de contagem pré-construída para as suas explorações. No que se segue, quando se referem "tabelas de contagem pré-construídas", referimo-nos à utilização das tabelas de contagem que foram fornecidas. Instruções detalhadas sobre como aceder a estas tabelas são fornecidas na secção seguinte.

## <a name="build-a-model-with-azure-machine-learning"></a><a name="aml"></a> Construir um modelo com Azure Machine Learning
O nosso processo de construção de modelos em Azure Machine Learning segue estes passos:

1. [Obtenha os dados das tabelas da Colmeia no Azure Machine Learning](#step1)
2. [Crie a experiência: limpe os dados e faça dele uma característica com tabelas de contagem](#step2)
3. [Construa, treine e marque o modelo](#step3)
4. [Avaliar o modelo](#step4)
5. [Publique o modelo como um serviço web](#step5)

Agora está pronto para construir modelos no estúdio Azure Machine Learning. Os nossos dados recolhidos são guardados como tabelas de Colmeia no cluster. Utilize o módulo de **dados de importação de aprendizagem de máquinas** Azure para ler estes dados. As credenciais para aceder à conta de armazenamento deste cluster são fornecidas no que se segue.

### <a name="step-1-get-data-from-hive-tables-into-azure-machine-learning-using-the-import-data-module-and-select-it-for-a-machine-learning-experiment"></a><a name="step1"></a> Passo 1: Obtenha dados das tabelas da Colmeia para o Azure Machine Learning utilizando o módulo de dados de importação e selecione-os para uma experiência de aprendizagem automática
Comece por selecionar uma experiência em branco **+NEW**  ->  **EXPERIMENT**  ->  **EXPERIMENT.** Em seguida, a partir da caixa **de pesquisa** na parte superior esquerda, procure por "Dados de Importação". Arraste e deixe cair o módulo **de Dados de Importação** na tela de experiência (a parte central do ecrã) para utilizar o módulo para acesso a dados.

Isto é o que os **Dados de Importação** parecem ao obter dados da tabela Hive:

![Dados de Importação obtém dados](./media/hive-criteo-walkthrough/i3zRaoj.png)

Para o módulo **Dados de Importação,** os valores dos parâmetros fornecidos no gráfico são apenas exemplos do tipo de valores que precisa de fornecer. Aqui está uma orientação geral sobre como preencher o conjunto de parâmetros para o módulo **de dados de importação.**

1. Escolha "Consulta de colmeia" para **Fonte de Dados**
2. Na caixa de consulta de **base de dados da Hive,** basta um simples SELECT * FROM <o nome da \_ base de \_ dados.o nome da sua \_ mesa> - é \_ suficiente.
3. **Hcatalog servidor URI**: Se o seu cluster é "abc", então isto é simplesmente: https: \/ /abc.azurehdinsight.net
4. **Nome da conta do utilizador Hadoop**: O nome de utilizador escolhido no momento da colocação em funcionamento do cluster. (NÃO o nome de utilizador do Acesso Remoto!)
5. **Palavra-passe da conta de utilizador Hadoop**: A palavra-passe para o nome de utilizador escolhido no momento da colocação em funcionamento do cluster. (NÃO a palavra-passe de Acesso Remoto!)
6. **Localização dos dados de saída**: Escolha "Azure"
7. **Nome da conta de Armazenamento Azure**: A conta de armazenamento associada ao cluster
8. **Chave da conta de armazenamento Azure**: A chave da conta de armazenamento associada ao cluster.
9. **Nome do recipiente azul**: Se o nome do cluster for "abc", então isto é simplesmente "abc", normalmente.

Assim que os **Dados de Importação** terminarem de obter dados (vê o tique-taque verde no Módulo), guarde estes dados como um Conjunto de Dados (com um nome à sua escolha). O que isto parece:

![Dados de Importação guardam dados](./media/hive-criteo-walkthrough/oxM73Np.png)

Clique com o botão direito na porta de saída do módulo **De Dados de Importação.** Isto revela uma opção De Guardar como conjunto **de dados** e uma opção **de Visualização.** A opção **Visualizar,** se clicada, apresenta 100 linhas dos dados, juntamente com um painel direito que é útil para algumas estatísticas sumárias. Para guardar dados, basta **selecionar Guardar como conjunto de dados** e seguir as instruções.

Para selecionar o conjunto de dados guardado para utilização numa experiência de aprendizagem automática, localize os conjuntos de dados utilizando a caixa **de pesquisa** mostrada na seguinte figura. Em seguida, basta escrever o nome que deu ao conjunto de dados parcialmente para aceder ao conjunto de dados e arrastar o conjunto de dados para o painel principal. Deixá-lo sobre o painel principal seleciona-o para utilização em modelagem de machine learning.

![Arraste o conjunto de dados para o painel principal](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> Faça isto tanto para o comboio como para os conjuntos de dados de teste. Além disso, lembre-se de usar o nome da base de dados e os nomes de tabelas que deu para o efeito. Os valores utilizados na figura são apenas para fins de ilustração.**
>
>

### <a name="step-2-create-an-experiment-in-azure-machine-learning-to-predict-clicks--no-clicks"></a><a name="step2"></a> Passo 2: Criar uma experiência em Azure Machine Learning para prever cliques / sem cliques
A nossa experiência do Azure Machine Learning Studio (clássico) é assim:

![Experiência de Aprendizagem automática](./media/hive-criteo-walkthrough/xRpVfrY.png)

Agora examine os componentes-chave desta experiência. Arraste os nossos conjuntos de dados de comboios e testes guardados para a nossa pesquisa experimentada primeiro.

#### <a name="clean-missing-data"></a>Limpar Dados em Falta
O módulo **De Dados Desaparecidos Limpos** faz o que o seu nome sugere: limpa dados em falta de forma a especificar o utilizador. Veja neste módulo para ver isto:

![Limpar dados em falta](./media/hive-criteo-walkthrough/0ycXod6.png)

Aqui, opte por substituir todos os valores em falta por um 0. Existem outras opções também, que podem ser vistas olhando para as descidas no módulo.

#### <a name="feature-engineering-on-the-data"></a>Engenharia de recursos nos dados
Pode haver milhões de valores únicos para algumas características categóricas de grandes conjuntos de dados. Usar métodos ingénuos como codificação de um quente para representar tais características categóricas de alta dimensão é totalmente inviável. Este walkthrough demonstra como usar características de contagem usando módulos de aprendizagem de máquinas Azure incorporados para gerar representações compactas destas variáveis categóricas de alta dimensão. O resultado final é um tamanho de modelo menor, tempos de treino mais rápidos e métricas de desempenho comparáveis à utilização de outras técnicas.

##### <a name="building-counting-transforms"></a>A contagem de edifícios transforma
Para criar funcionalidades de contagem, utilize o módulo **Build Counting Transform** que está disponível no Azure Machine Learning. O módulo é assim:

![Construa contando transformar propriedades de ](./media/hive-criteo-walkthrough/e0eqKtZ.png)
 ![ módulos construir módulo de conta de contagem](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT]
> Na caixa das **colunas** count, introduza as colunas com as que deseja realizar contagens. Tipicamente, estas são (como mencionado) colunas categóricas de alta dimensão. Lembre-se que o conjunto de dados criteo tem 26 colunas categóricas: do Col15 ao Col40. Aqui, conte com todos eles e dê os seus índices (de 15 a 40 separados por vírgulas como mostrado).
>

Para utilizar o módulo no modo MapReduce (apropriado para grandes conjuntos de dados), é necessário ter acesso a um cluster HdInsight Hadoop (o utilizado para a exploração de funcionalidades também pode ser reutilizado para este fim) e as suas credenciais. Os números anteriores ilustram como são os valores preenchidos (substitua os valores previstos para ilustração com os relevantes para o seu próprio caso de utilização).

![Parâmetros do módulo](./media/hive-criteo-walkthrough/05IqySf.png)

A figura anterior mostra como entrar no local da bolha de entrada. Esta localização tem os dados reservados para as tabelas de contagem de edifícios.

Quando este módulo terminar de funcionar, guarde a transformação para mais tarde clicando no módulo e selecionando a opção **Salvar como Transformar:**

![Opção "Salvar como Transformar"](./media/hive-criteo-walkthrough/IcVgvHR.png)

Na nossa arquitetura de experiência mostrada acima, o conjunto de dados "ytransform2" corresponde precisamente a uma transformação de contagem guardada. Para o resto desta experiência, presume-se que o leitor usou um módulo **Build Counting Transform** em alguns dados para gerar contagens, podendo então utilizar essas contagens para gerar funcionalidades de contagem nos conjuntos de dados do comboio e do teste.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Escolher quais as características da contagem a incluir como parte dos conjuntos de dados do comboio e do teste
Uma vez que a contagem se transforme pronta, o utilizador pode escolher as funcionalidades a incluir nos seus conjuntos de dados de comboio e teste utilizando o módulo **Descodudo Parâmetros de Tabela.** Para a completude, este módulo é mostrado aqui. Mas, por uma questão de simplicidade, não a usam na nossa experiência.

![Modificar os parâmetros da tabela contagem](./media/hive-criteo-walkthrough/PfCHkVg.png)

Neste caso, como se pode ver, as probabilidades de registo devem ser utilizadas e a coluna de back off é ignorada. Também pode definir parâmetros como o limiar do caixote do lixo, quantos exemplos pseudo-anteriores adicionar para suavizar, e se usar qualquer ruído laplacian ou não. Todas estas são funcionalidades avançadas e é de notar que os valores padrão são um bom ponto de partida para os utilizadores que são novos neste tipo de geração de funcionalidades.

##### <a name="data-transformation-before-generating-the-count-features"></a>Transformação de dados antes de gerar as características da contagem
Agora, o foco está num ponto importante sobre a transformação dos nossos dados de comboio e teste antes de gerar funcionalidades de contagem. Existem dois módulos **executo R Script** usados antes da transformação da contagem ser aplicada aos nossos dados.

![Executar módulos de script R](./media/hive-criteo-walkthrough/aF59wbc.png)

Aqui está o primeiro guião R:

![Primeiro guião R](./media/hive-criteo-walkthrough/3hkIoMx.png)

Este guião R renomea as nossas colunas para os nomes "Col1" para "Col40". Isto porque a transformação da contagem espera nomes deste formato.

O segundo guião R equilibra a distribuição entre classes positivas e negativas (classes 1 e 0, respectivamente) por amostragem negativa. O guião R aqui mostra como fazer isto:

![Segundo guião R](./media/hive-criteo-walkthrough/91wvcwN.png)

Neste simples script R, o "rácio pos \_ \_ neg" é usado para definir a quantidade de equilíbrio entre as classes positivas e negativas. Isto é importante, uma vez que a melhoria do desequilíbrio de classes tem geralmente benefícios de desempenho para problemas de classificação onde a distribuição da classe é distorcida (lembre-se que neste caso, você tem 3,3% de classe positiva e 96,7% classe negativa).

##### <a name="applying-the-count-transformation-on-our-data"></a>Aplicação da transformação da contagem nos nossos dados
Finalmente, pode utilizar o módulo **Apply Transformation** para aplicar as transformações de contagem nos nossos conjuntos de dados de comboio e teste. Este módulo toma a transformação da contagem guardada como uma entrada e os conjuntos de dados do comboio ou do teste como a outra entrada, e devolve dados com características de contagem. É mostrado aqui:

![Aplicar módulo de transformação](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Um excerto de como as características da contagem se parecem
É instrutivo ver como são as características da contagem no nosso caso. Aqui está um excerto disto:

![Características da contagem](./media/hive-criteo-walkthrough/FO1nNfw.png)

Este excerto mostra que, para as colunas contadas, obtém-se as contagens e odds de registo, além de quaisquer desvantagens relevantes.

Está agora pronto para construir um modelo Azure Machine Learning utilizando estes conjuntos de dados transformados. Na secção seguinte mostra como isto pode ser feito.

### <a name="step-3-build-train-and-score-the-model"></a><a name="step3"></a> Passo 3: Construir, treinar e marcar o modelo

#### <a name="choice-of-learner"></a>Escolha do aprendiz
Primeiro, tens de escolher um aprendiz. Usa uma árvore de decisão de duas classes como aprendiz. Aqui estão as opções padrão para este aluno:

![Two-Class impulsionados parâmetros da árvore de decisão](./media/hive-criteo-walkthrough/bH3ST2z.png)

Para a experiência, escolha os valores predefinidos. Os incumprimentos são significativos e uma boa maneira de obter linhas de base rápidas sobre o desempenho. Pode melhorar o desempenho varrendo parâmetros se optar por uma linha de base.

#### <a name="train-the-model"></a>Preparar o modelo
Para treinar, basta invocar um módulo **Train Model.** As duas entradas são o Two-Class Aprendiz de Árvore de Decisão Reforçada e o nosso conjunto de dados do comboio. Isto é mostrado aqui:

![Módulo modelo de trem](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>Classificar o modelo
Uma vez treinado um modelo, está pronto para marcar no conjunto de dados do teste e para avaliar o seu desempenho. Faça-o utilizando o módulo **'Modelo de Pontuação'** apresentado na seguinte figura, juntamente com um módulo **De Modelo avaliar:**

![Módulo do modelo de pontuação](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step-4-evaluate-the-model"></a><a name="step4"></a> Passo 4: Avaliar o modelo
Finalmente, deve analisar o desempenho do modelo. Normalmente, para dois problemas de classificação de classe (binário), uma boa medida é a AUC. Para visualizar esta curva, ligue o módulo **'Modelo de Pontuação'** a um módulo **De Modelo de Avaliação.** Clicar em **visualizar** no módulo **Modelo avaliar** produz um gráfico como o seguinte:

![Avaliar modelo BDT módulo](./media/hive-criteo-walkthrough/0Tl0cdg.png)

Em problemas de classificação binários (ou duas classes), uma boa medida de precisão de previsão é a Área Sob Curva (AUC). A seguinte secção mostra os nossos resultados usando este modelo no nosso conjunto de dados de teste. Clique com o botão direito na porta de saída do módulo **Modelo avaliar** e, em seguida, **visualizar**.

![Visualizar módulo de modelo de avaliação](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step-5-publish-the-model-as-a-web-service"></a><a name="step5"></a> Passo 5: Publicar o modelo como um serviço Web
A capacidade de publicar um modelo Azure Machine Learning como serviços web com um mínimo de alarido é uma característica valiosa para torná-lo amplamente disponível. Uma vez feito, qualquer pessoa pode fazer chamadas para o serviço web com dados de entrada para os quais precisam de previsões, e o serviço web usa o modelo para devolver essas previsões.

Em primeiro lugar, guarde o nosso modelo treinado como um objeto de Modelo Treinado clicando no módulo **Modelo de Comboio** e utilizando a opção Save as Train **Model.**

Em seguida, crie portas de entrada e saída para o nosso serviço web:

* uma porta de entrada retira dados da mesma forma que os dados que precisa de previsões para
* uma porta de saída devolve as etiquetas pontuadas e as probabilidades associadas.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Selecione algumas linhas de dados para a porta de entrada
É conveniente usar um módulo **de transformação de SQL para** selecionar apenas 10 linhas para servir como dados da porta de entrada. Selecione apenas estas linhas de dados para a nossa porta de entrada usando a consulta SQL mostrada aqui:

![Dados da porta de entrada](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Serviço Web
Agora está pronto para executar uma pequena experiência que pode ser usada para publicar o nosso serviço web.

#### <a name="generate-input-data-for-webservice"></a>Gerar dados de entrada para webservice
Como um quarto passo zero, uma vez que a tabela de contagem é grande, pegue algumas linhas de dados de teste e gere dados de saída a partir dele com características de contagem. Esta saída pode servir como o formato de dados de entrada para o nosso serviço web, como mostrado aqui:

![Criar dados de entrada de BDT](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> Para o formato de dados de entrada, utilize a saída do módulo **Conde Featurizer.** Uma vez terminada esta experiência, guarde a saída do módulo **Conde Featurizer** como conjunto de dados. Este Conjunto de Dados é utilizado para os dados de entrada no serviço web.
>
>

#### <a name="scoring-experiment-for-publishing-webservice"></a>Experiência de pontuação para publicação de webservice
Em primeiro lugar, a estrutura essencial é um módulo **Score Model** que aceita o nosso objeto de modelo treinado e algumas linhas de dados de entrada que foram gerados nos passos anteriores usando o módulo **Conde Featurizer.** Utilize "Select Columns in Dataset" para projetar as etiquetas pontuadas e as probabilidades de Pontuação.

![Selecionar Colunas no Conjunto de Dados](./media/hive-criteo-walkthrough/kRHrIbe.png)

Note como as **colunas selecionadas no** módulo Dataset podem ser utilizadas para "filtrar" dados a partir de um conjunto de dados. Os conteúdos são mostrados aqui:

![Filtragem com as colunas selecionadas no módulo Dataset](./media/hive-criteo-walkthrough/oVUJC9K.png)

Para obter as portas azuis de entrada e saída, basta clicar em preparar o **webservice** no canto inferior direito. Executar esta experiência também nos permite publicar o serviço web: clique no ícone **PUBLISH WEB SERVICE** no canto inferior direito, mostrado aqui:

![Publicar serviço web](./media/hive-criteo-walkthrough/WO0nens.png)

Assim que o serviço web for publicado, seja redirecionado para uma página que se pareça assim:

![Painel de atendimento web](./media/hive-criteo-walkthrough/YKzxAA5.png)

Note os dois links para webservices no lado esquerdo:

* O Serviço **DE PEDIDO/RESPOSTA** (ou RRS) destina-se a previsões únicas e é o que foi utilizado neste workshop.
* O **Serviço de EXECUÇÃO DE LOTE** (BES) é utilizado para previsões de lotes e requer que os dados de entrada utilizados para fazer previsões residam no Armazenamento Azure Blob.

Clicar no link **REQUEST/RESPONSE** leva-nos a uma página que nos dá código pré-enlatado em C#, python e R. Este código pode ser convenientemente utilizado para fazer chamadas para o webservice. A chave API nesta página precisa de ser utilizada para a autenticação.

É conveniente copiar este código python para uma nova célula no caderno IPython.

Aqui está um segmento de código python com a chave API correta.

![Código python](./media/hive-criteo-walkthrough/f8N4L4g.png)

A chave API predefinida foi substituída pela chave API do nosso webservice. Clicar em **executar** nesta célula num bloco de notas IPython dá a seguinte resposta:

![Resposta iPython](./media/hive-criteo-walkthrough/KSxmia2.png)

Para os dois exemplos de teste questionados no quadro JSON do script Python, obtém respostas no formulário "Etiquetas Pontuadas, Probabilidades Pontuadas". Neste caso, foram escolhidos os valores predefinidos que o código pré-enlatado prevê (0's para todas as colunas numéricas e o "valor" da cadeia para todas as colunas categóricas).

Em conclusão, o nosso walkthrough mostra como lidar com conjuntos de dados em larga escala usando Azure Machine Learning. Começou com um terabyte de dados, construiu um modelo de previsão, e implantou-o como um serviço web na nuvem.
