---
title: Explorar os dados num cluster do Hadoop - Team Data Science Process
description: Usando o processo de ciência de dados de equipa para um cenário ponto-a-ponto, empregando um cluster de Hadoop do HDInsight para criar e implementar um modelo.
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
ms.openlocfilehash: a7aa5401cbba9fafda9f995a882934ef0edfa481
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57881151"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>O Team Data Science Process em ação: Utilizar clusters do Hadoop de HDInsight do Azure
Nestas instruções, utilizamos o [Team Data Science Process (TDSP)](overview.md) num cenário ponto-a-ponto. Utilizamos uma [cluster do Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar e os dados de engenharia da funcionalidade de publicamente disponíveis [NYC táxis viagens](https://www.andresmh.com/nyctaxitrips/) conjunto de dados e dimensionar os dados. Para lidar com classificação binária e várias classes e tarefas de previsão de regressão, desenvolvemos os modelos de dados com o Azure Machine Learning. 

Para obter instruções que mostra como lidar com um conjunto de dados maior, veja [processo de ciência de dados de equipa - com o Azure HDInsight Clusters do Hadoop num conjunto de dados de 1 TB](hive-criteo-walkthrough.md).

Também pode utilizar um IPython notebook para realizar as tarefas apresentadas no passo a passo que utiliza o conjunto de dados de 1 TB. Para obter mais informações, consulte [Criteo passo a passo através de uma ligação de ODBC do Hive](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).

## <a name="dataset"></a>Descrição do conjunto de dados de viagens de táxis de NYC
Os dados da viagem de táxis de NYC são cerca de 20 GB de arquivos de comprimido valores separados por vírgulas (CSV) (GB de ~ 48 descomprimido). Ele tem mais de 173 milhões de viagens individuais e inclui fares pagos para cada viagem. Cada registo de viagem inclui a pegada e redução de localização e tempo, hack anónimo (driver) número de licença e o número de medallion (a táxis de ID exclusivo). Os dados abrange todas as viagens no ano de 2013 e são fornecidos nas seguintes dois conjuntos de dados para todos os meses:

- Os ficheiros CSV de trip_data contêm detalhes de viagem. Isto inclui o número de passageiros, pegada e pontos de redução, duração de viagem e comprimento de viagem. Aqui estão alguns exemplos de registros:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
- Os ficheiros CSV de trip_fare contêm detalhes de Europeia pago para cada viagem. Isto inclui o tipo de pagamento, a quantidade de Europeia, sobretaxa e impostos, dicas e pedágio, e o valor total pago. Aqui estão alguns exemplos de registros:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A chave exclusiva para aderir a viagem\_dados e viagem\_Europeia é composta pelos campos: medallion, aceder de modo ilícito\_licença e recolha\_datetime. Para obter todos os detalhes relevantes para uma viagem particular, é suficiente para associar estas três chaves.

## <a name="mltasks"></a>Exemplos de tarefas de predição
Determine o tipo de previsões de indisponibilidade que deve escolher com base na análise de dados. Isto ajuda a esclarecer as tarefas que precisa incluir em seu processo. Seguem-se três exemplos de problemas de predição que abordamos nestas instruções. Eles se baseiam no *sugestão\_quantidade*:

- **Classificação binária**: Prever se ou não uma dica foi paga por uma viagem. Ou seja, um *sugestão\_quantidade* superior de US $0 é um exemplo positivo, enquanto uma *tip\_quantidade* de US $0 é um exemplo negativo.
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0
- **Classificação multiclasses**: Prever o intervalo de quantidades de sugestão pago para a viagem. Vamos dividir o *sugestão\_quantidade* em cinco classes:
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0 and tip_amount <= $5
        Class 2: tip_amount > $5 and tip_amount <= $10
        Class 3: tip_amount > $10 and tip_amount <= $20
        Class 4: tip_amount > $20
- **Tarefa de regressão**: Prever a quantidade de sugestão pago por uma viagem.  

## <a name="setup"></a>Configurar um cluster do HDInsight Hadoop para análises avançadas
> [!NOTE]
> Normalmente, trata-se uma tarefa de administração.
> 
> 

Pode configurar um ambiente do Azure para análise avançada que emprega um cluster do HDInsight em três passos:

1. [Criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md): Esta conta de armazenamento é utilizada para armazenar dados no armazenamento de Blobs do Azure. Os dados usados em clusters do HDInsight também residem aqui.
2. [Personalizar clusters do Hadoop de HDInsight do Azure para o processo de análise avançada e tecnologia](customize-hadoop-cluster.md). Este passo cria um cluster de Hadoop do HDInsight com 64 bits Anaconda Python 2.7 instalados em todos os nós. Existem dois passos importantes a serem lembrados ao personalizar o seu cluster do HDInsight.
   
   * Lembre-se de vincular a conta de armazenamento que criou no passo 1 com o seu cluster do HDInsight quando estiver a criar. Esta conta de armazenamento aceda aos dados que são processados dentro do cluster.
   * Depois de criar o cluster, ative o acesso remoto para o nó principal do cluster. Navegue para o **Configuration** separador e selecione **ativar remoto**. Neste passo Especifica as credenciais de utilizador utilizadas para início de sessão remoto.
3. [Criar uma área de trabalho do Azure Machine Learning](../studio/create-workspace.md): Utilize esta área de trabalho para criar modelos de aprendizagem automática. Esta tarefa é abordada depois de concluir uma exploração de dados inicial e para baixo-amostragem, ao utilizar o cluster do HDInsight.

## <a name="getdata"></a>Obter os dados de uma origem de pública
> [!NOTE]
> Normalmente, trata-se uma tarefa de administração.
> 
> 

Para copiar o [NYC táxis viagens](https://www.andresmh.com/nyctaxitrips/) conjunto de dados para o seu computador da localização pública, usar qualquer um dos métodos descritos [mover dados para e do armazenamento de Blobs do Azure](move-azure-blob.md).

Aqui, descrevemos como utilizar o AzCopy para transferir os ficheiros que contêm dados. Para transferir e instalar o AzCopy, siga as instruções em [introdução ao utilitário de linha de comandos AzCopy](../../storage/common/storage-use-azcopy.md).

1. A partir de uma janela de linha de comandos, execute os seguintes comandos do AzCopy, substituindo *< path_to_data_folder >* com o destino pretendido:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

1. Quando a cópia estiver concluída, verá um total de 24 arquivos compactados na pasta de dados escolhido. Descompacte os ficheiros transferidos para o mesmo diretório no seu computador local. Tome nota da pasta onde residem os ficheiros descomprimidos. Esta pasta é referida como o *< caminho\_ao\_unzipped_data\_ficheiros\>*  no que se segue.

## <a name="upload"></a>Carregar os dados para o contentor predefinido do cluster HDInsight Hadoop
> [!NOTE]
> Normalmente, trata-se uma tarefa de administração.
> 
> 

Nos seguintes comandos do AzCopy, substitua os seguintes parâmetros com os valores reais que especificou ao criar o cluster de Hadoop e descomprimir os ficheiros de dados.

* ***< Path_to_data_folder >*** o diretório (juntamente com o caminho) na sua máquina que contém os ficheiros de dados descompactado.  
* ***<storage account name of Hadoop cluster>*** A conta de armazenamento associada com o seu cluster do HDInsight.
* ***<default container of Hadoop cluster>*** O contentor predefinido utilizado pelo seu cluster. Tenha em atenção que o nome do contentor predefinido é, normalmente, o mesmo nome que o próprio cluster. Por exemplo, se o cluster for chamado "abc123.azurehdinsight.net", o contentor predefinido é abc123.
* ***<storage account key>*** A chave para a conta de armazenamento utilizada pelo seu cluster.

A partir de um prompt de comando ou uma janela do Windows PowerShell, execute os seguintes dois comandos do AzCopy.

Este comando carrega os dados da viagem para a ***nyctaxitripraw*** diretório no contentor predefinido do cluster de Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Este comando carrega os dados de Europeia para o ***nyctaxifareraw*** diretório no contentor predefinido do cluster de Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Os dados devem agora ser no armazenamento de BLOBs e prontas para serem consumidas dentro do cluster do HDInsight.

## <a name="#download-hql-files"></a>Inicie sessão no nó principal do cluster do Hadoop e preparar para a análise de dados exploratório
> [!NOTE]
> Normalmente, trata-se uma tarefa de administração.
> 
> 

Para acessar o nó principal do cluster para análise de dados exploratório e para baixo-amostragem dos dados, siga o procedimento descrito em [aceder o nó principal do Cluster do Hadoop](customize-hadoop-cluster.md).

Nestas instruções, utilizamos principalmente consultas escritas [Hive](https://hive.apache.org/), uma linguagem de consulta do tipo SQL, para efetuar explorations preliminar de dados. As consultas do Hive são armazenadas nos arquivos de .hql. Podemos, em seguida, dimensionar estes dados a ser utilizado dentro de Machine Learning para a criação de modelos.

Para preparar o cluster para análise de dados exploratório, transferir os ficheiros de .hql que contém os scripts de Hive relevantes partir [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) para um diretório local (C:\temp) no nó principal. Para tal, abra a linha de comandos a partir de nó principal do cluster e execute os seguintes dois comandos:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Estes dois comandos transferir todos os ficheiros de .hql necessários esta explicação passo a passo para o diretório local ***C:\temp&#92;***  no nó principal.

## <a name="#hive-db-tables"></a>Criar base de dados do Hive e tabelas particionadas por mês
> [!NOTE]
> Normalmente, trata-se uma tarefa de administração.
> 
> 

Está agora pronto para criar tabelas do Hive do conjunto de dados de táxis de NYC.
No nó principal do cluster de Hadoop, abra a linha de comandos do Hadoop no ambiente de trabalho do nó principal. Introduza o diretório de ramo de registo ao executar o seguinte comando:

    cd %hive_home%\bin

> [!NOTE]
> Executar todos os comandos de Hive nestas instruções a partir do contentor de Hive / linha de comandos do diretório. Isto resolve automaticamente quaisquer problemas de caminho. Utilizamos os termos "Prompt de diretório do Hive", "Hive bin / linha de comandos do diretório" e "Linha de comandos de Hadoop", alternadamente nestas instruções.
> 
> 

A partir de linha de comandos do diretório de ramo de registo, execute o seguinte comando na linha de comandos de Hadoop do nó principal. Isso envia a consulta do Hive para criar as tabelas de base de dados do Hive e:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Aqui está o conteúdo do **C:\temp\sample\_hive\_crie\_db\_e\_tables.hql** ficheiro. Esta ação cria a base de dados do Hive **nyctaxidb**e as tabelas **viagem** e **Europeia**.

    create database if not exists nyctaxidb;

    create external table if not exists nyctaxidb.trip
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double)  
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

    create external table if not exists nyctaxidb.fare
    (
        medallion string,
        hack_license string,
        vendor_id string,
        pickup_datetime string,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double)
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

Este script Hive cria duas tabelas:

* O **viagem** tabela contém detalhes de viagem de cada jornada (detalhes do controlador, pegada tempo, a distância de viagem e vezes).
* O **Europeia** tabela contém detalhes Europeia (quantidade Europeia, Gorjeta, portagens e sobretaxas).

Se precisar de qualquer assistência adicional com estes procedimentos, ou que pretende investigar aqueles alternativos, consulte a secção [Hive submeter consultas diretamente a partir da linha de comandos do Hadoop](move-hive-tables.md#submit).

## <a name="#load-data"></a>Carregar dados para tabelas do Hive por partição
> [!NOTE]
> Normalmente, trata-se uma tarefa de administração.
> 
> 

O conjunto de dados de táxis de NYC tem uma criação de partições natural por mês, o que usamos para permitir tempos mais rápidos de processamento e da consulta. Os seguintes comandos do PowerShell (emitidos a partir do diretório do Hive através da linha de comandos do Hadoop) carregar dados para a viagem e se comportarão de tabelas do Hive, particionadas por mês.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

O **amostra\_hive\_carregar\_dados\_por\_partitions.hql** arquivo contém o seguinte **carregar** comandos:

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Tenha em atenção que um número de consultas do Hive usados aqui no processo de exploração envolve a olhar para apenas uma ou duas partições. Mas estas consultas podem ser executadas em todo o conjunto de dados.

### <a name="#show-db"></a>Veja as bases de dados do cluster de Hadoop do HDInsight
Para mostrar as bases de dados criados no cluster de Hadoop do HDInsight dentro da janela de linha de comandos do Hadoop, execute o seguinte comando na linha de comandos de Hadoop:

    hive -e "show databases;"

### <a name="#show-tables"></a>Mostrar as tabelas do Hive a **nyctaxidb** base de dados
Para mostrar as tabelas a **nyctaxidb** base de dados, execute o seguinte comando na linha de comandos de Hadoop:

    hive -e "show tables in nyctaxidb;"

Podemos confirmar que as tabelas estão particionadas por executar o seguinte comando:

    hive -e "show partitions nyctaxidb.trip;"

Eis a saída esperada:

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 2.075 seconds, Fetched: 12 row(s)

Da mesma forma, podemos garantir que a tabela de Europeia esteja particionada, executando o seguinte comando:

    hive -e "show partitions nyctaxidb.fare;"

Eis a saída esperada:

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 1.887 seconds, Fetched: 12 row(s)

## <a name="#explore-hive"></a>Exploração de dados e de engenharia de funcionalidades no Hive
> [!NOTE]
> Normalmente, trata-se uma tarefa de cientista de dados.
> 
> 

Pode utilizar consultas do Hive para realizar a exploração de dados e a funcionalidade de tarefas para os dados carregados para as tabelas do Hive de engenharia. Seguem-se exemplos de tarefas:

* Ver os registos de 10 principais em ambas as tabelas.
* Explore as distribuições de dados de alguns campos em várias janelas de tempo.
* Investigue a qualidade dos dados dos campos de longitude e latitude.
* Gere etiquetas de classificação binária e várias classes com base na quantidade de dica.
* Gere recursos, as distâncias de viagem direto de computação.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Exploração: Ver os registos de 10 principais em viagem de tabela
> [!NOTE]
> Normalmente, trata-se uma tarefa de cientista de dados.
> 
> 

Para ver o aspeto dos dados, examine 10 registros de cada tabela. Para inspecionar os registos, execute as seguintes consultas de dois em separado da linha de comandos da diretório do Hive na consola da linha de comandos do Hadoop.

Para obter os registos de 10 principais na tabela viagem a partir do primeiro mês:

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Para obter os registos de 10 principais na tabela Europeia a partir do primeiro mês:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

Pode salvar os registros num arquivo para visualização conveniente. Uma pequena alteração para a consulta anterior faz isso:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Exploração: Ver o número de registos em cada um dos 12 partições
> [!NOTE]
> Normalmente, trata-se uma tarefa de cientista de dados.
> 
> 

De interesse é a forma como o número de viagens varia durante o ano de calendário. Agrupamento por mês mostra a distribuição de viagens.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Isso nos dá o resultado seguinte:

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 283.406 seconds, Fetched: 12 row(s)

Aqui, a primeira coluna é o mês e o segundo é o número de viagens nesse mês.

Pode também Contamos o número total de registos no nosso conjunto de dados de viagens, executando o seguinte comando na linha de comandos de diretório do Hive:

    hive -e "select count(*) from nyctaxidb.trip;"

Isso resulta em:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Utilizar comandos semelhantes às mostradas para o conjunto de dados de viagens, podemos emitir consultas do Hive no prompt de diretório do ramo de registo para o conjunto de dados de Europeia validar o número de registos de.

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Isso nos dá o resultado seguinte:

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 253.955 seconds, Fetched: 12 row(s)

Tenha em atenção que o mesmo número exato de viagens por mês, é devolvido para ambos os conjuntos de dados. Isso fornece a validação primeiro que os dados foram carregados corretamente.

Pode contar o número total de registos no conjunto de dados europeia utilizando o seguinte comando no prompt de diretório do ramo de registo de:

    hive -e "select count(*) from nyctaxidb.fare;"

Isso resulta em:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

O número total de registos em ambas as tabelas também é o mesmo. Isso fornece uma segunda validação que os dados foram carregados corretamente.

### <a name="exploration-trip-distribution-by-medallion"></a>Exploração: Distribuição de viagem por medallion
> [!NOTE]
> Normalmente, trata-se uma tarefa de cientista de dados.
> 
> 

Neste exemplo identifica os medallions (números de táxis) com mais de 100 viagens dentro de um determinado período de tempo. A consulta se beneficia do acesso a tabela particionada, porque ele está condicionada à variável de partição **mês**. Os resultados da consulta são escritos num ficheiro local, **queryoutput.tsv**, na `C:\temp` no nó principal.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Aqui está o conteúdo do **amostra\_hive\_viagem\_contagem\_por\_medallion.hql** ficheiro para inspeção.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Medallion no conjunto de dados de táxis NYC identifica um cab exclusivo. É possível identificar quais cabs são comparativamente ocupados fazendo quais fez mais do que um determinado número de viagens num período de tempo específico. O exemplo seguinte identifica os ficheiros. cab que fez mais do que uma centena de viagens no primeiro três meses e guarda os resultados da consulta para um ficheiro local, **C:\temp\queryoutput.tsv**.

Aqui está o conteúdo do **amostra\_hive\_viagem\_contagem\_por\_medallion.hql** ficheiro para inspeção.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

A partir da linha de comandos do diretório de ramo de registo, execute o seguinte comando:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Exploração: Distribuição de viagem pela licença medallion e hackers
> [!NOTE]
> Normalmente, trata-se uma tarefa de cientista de dados.
> 
> 

Ao explorar um conjunto de dados, com frequência que queremos examinar o número de ocorrências conjunta de grupos de valores. Esta seção fornece um exemplo de como fazê-lo para cabs e drivers.

O **amostra\_hive\_viagem\_contagem\_por\_medallion\_license.hql** ficheiro grupos do conjunto de dados europeia **medallion** e **hack_license**e retorna as contagens de cada combinação. Eis o seu conteúdo:

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

Esta consulta devolve as combinações do cab e do controlador, ordenadas pelo número de viagens de descendente.

A partir da linha de comandos do diretório de ramo de registo, execute:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

Os resultados da consulta são escritos num ficheiro local, **C:\temp\queryoutput.tsv**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Exploração: Avaliar a qualidade dos dados ao procurar por registos de longitude ou latitude inválidos
> [!NOTE]
> Normalmente, trata-se uma tarefa de cientista de dados.
> 
> 

É um objetivo comum de análise de dados exploratórios eliminar registos inválidos ou incorretos. O exemplo nesta secção, determina se os campos de longitude ou latitude contém um valor até agora fora da área de NYC. Uma vez que é provável que esses registros têm um valor de longitude-latitude errado, que Desejamos eliminá-los a partir de quaisquer dados que está a ser usados para modelagem.

Aqui está o conteúdo do **amostra\_hive\_qualidade\_assessment.hql** ficheiro para inspeção.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


A partir da linha de comandos do diretório de ramo de registo, execute:

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

O *-S* argumento incluído neste comando suprime a impressão de ecrã de estado das tarefas do Hive mapa/redução. Isto é útil porque torna a tela de impressão da saída de consulta do Hive mais legível.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Exploração: Distribuições de classe binário de dicas de viagem
> [!NOTE]
> Normalmente, trata-se uma tarefa de cientista de dados.
> 
> 

Para o problema de classificação binária descrito a [exemplos de tarefas de predição](hive-walkthrough.md#mltasks) secção, é útil saber se uma dica foi fornecida ou não. Esta distribuição de dicas é binária:

* Tendo em conta a TIP (classe 1, a tip\_valor > US $0)  
* nenhuma sugestão (classe 0, a tip\_quantidade = US $0)

O seguinte procedimento **amostra\_hive\_tipados\_frequencies.hql** ficheiro faz isso:

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

A partir da linha de comandos do diretório de ramo de registo, execute:

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Exploração: Distribuições de classe na definição de várias classes
> [!NOTE]
> Normalmente, trata-se uma tarefa de cientista de dados.
> 
> 

Para o problema de classificação multiclasses descrito a [exemplos de tarefas de predição](hive-walkthrough.md#mltasks) secção, este conjunto de dados também se presta a uma classificação natural para prever a quantidade de dicas tendo em conta. Podemos utilizar contentores para definir os intervalos de sugestão na consulta. Para obter as distribuições de classe para os vários intervalos de sugestão, utilize o **amostra\_hive\_sugestão\_intervalo\_frequencies.hql** ficheiro. Eis o seu conteúdo.

    SELECT tip_class, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount=0, 0,
            if(tip_amount>0 and tip_amount<=5, 1,
            if(tip_amount>5 and tip_amount<=10, 2,
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

Execute o seguinte comando a partir da consola da linha de comandos do Hadoop:

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>Exploração: Computar a distância direta entre duas localizações de longitude-latitude
> [!NOTE]
> Normalmente, trata-se uma tarefa de cientista de dados.
> 
> 

Pode querer saber se existe uma diferença entre a distância direta entre dois locais e a distância de viagem real da táxis. Um passageiro pode ser uma probabilidade menor de sugestão se eles descobrir que o driver intencionalmente apresentou-los por uma rota mais tempo.

Para ver a comparação entre a distância de viagem real e o [Haversine distância](https://en.wikipedia.org/wiki/Haversine_formula) entre dois pontos de longitude-latitude (a distância do "grande ciclo"), pode usar as funções trigonométricas disponíveis dentro do Hive:

    set R=3959;
    set pi=radians(180);

    insert overwrite directory 'wasb:///queryoutputdir'

    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
     *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
     *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
     /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
     +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
     pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
    from nyctaxidb.trip
    where month=1
    and pickup_longitude between -90 and -30
    and pickup_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
    and dropoff_latitude between 30 and 90;

A consulta anterior, R é igual ao raio da terra em quilómetros e pi é convertido em radianos. Tenha em atenção que os pontos de longitude-latitude são filtrados para remover os valores que estão longe de ser a área de NYC.

Neste caso, podemos escrever os resultados para um diretório chamado **queryoutputdir**. A sequência dos seguintes comandos cria primeiro este diretório de saída e, em seguida, executa o comando de ramo de registo.

A partir da linha de comandos do diretório de ramo de registo, execute:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


Os resultados da consulta são escritos para nove blobs do Azure (**queryoutputdir/000000\_0** para **queryoutputdir/000008\_0**), sob o contentor predefinido do cluster de Hadoop.

Para ver o tamanho dos blobs individuais, execute o seguinte comando da linha de comandos do diretório da Hive:

    hdfs dfs -ls wasb:///queryoutputdir

Para ver o conteúdo de um determinado arquivo, digamos **000000\_0**, utilize do Hadoop `copyToLocal` comando.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [!WARNING]
> `copyToLocal` pode ser muito lentas de ficheiros grandes e não é recomendado para utilização com eles.  
> 
> 

Das principais vantagens de ter estes dados residem num blob do Azure é que podemos explorar os dados dentro de Machine Learning, utilizando o [importar dados] [ import-data] módulo.

## <a name="#downsample"></a>Dimensionar dados e criar modelos de Machine Learning
> [!NOTE]
> Normalmente, trata-se uma tarefa de cientista de dados.
> 
> 

Após a fase de análise de dados exploratório, podemos agora está prontos para dimensionar os dados para a criação de modelos de Machine Learning. Nesta secção, vamos mostrar como utilizar uma consulta do Hive para dimensionar os dados. Machine Learning, em seguida, acede-lo a partir da [importar dados] [ import-data] módulo.

### <a name="down-sampling-the-data"></a>Os dados de amostragem para baixo
Existem dois passos deste procedimento. Em primeiro lugar, Junte-se a **nyctaxidb.trip** e **nyctaxidb.fare** tabelas em três chaves que estão presentes em todos os registos: **medallion**, **hack\_ licença**, e **recolha\_datetime**. Geramos uma etiqueta de classificação binária **colocado para**e uma etiqueta de classificação multiclasses **sugestão\_classe**.

Para poder utilizar os dados de objeto de amostragem de baixo diretamente a partir da [importar dados] [ import-data] módulo no Machine Learning, deve armazenar os resultados da consulta anterior para uma tabela do Hive interna. O que se segue, podemos criar uma tabela de ramo de registo interna e preencher o seu conteúdo com os dados associados e a amostragem de baixo.

A consulta aplica-se as funções padrão do Hive diretamente para gerar o seguinte a partir da **recolha\_datetime** campo:
- Hora do dia
- semana do ano
- dia da semana (1 representa segunda-feira e 7 representa Domingo)

A consulta também gera a distância direta entre as localizações de pegada e redução. Para obter uma lista completa de funções, consulte [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

A consulta, em seguida, baixo-samples os dados para que os resultados da consulta se encaixa em Azure Machine Learning Studio. Apenas cerca de 1 por cento do conjunto de dados original é importado para o studio.

Seguem-se o conteúdo do **amostra\_hive\_preparar\_para\_aml\_full.hql** ficheiro que prepara os dados para o modelo de criação no Machine Learning:

        set R = 3959;
        set pi=radians(180);

        create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
        )
        row format delimited fields terminated by ','
        lines terminated by '\n'
        stored as textfile;

        --- now insert contents of the join into the above internal table

        insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
        select
        t.medallion,
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        t.direct_distance,
        f.payment_type,
        f.fare_amount,
        f.surcharge,
        f.mta_tax,
        f.tip_amount,
        f.tolls_amount,
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
        if(tip_amount>0 and tip_amount<=5,1,
        if(tip_amount>5 and tip_amount<=10,2,
        if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class

        from
        (
        select
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        pickup_datetime,
        dropoff_datetime,
        passenger_count,
        trip_time_in_secs,
        trip_distance,
        pickup_longitude,
        pickup_latitude,
        dropoff_longitude,
        dropoff_latitude,
        ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
        rand() as sample_key

        from nyctaxidb.trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
        )t
        join
        (
        select
        medallion,
        hack_license,
        vendor_id,
        pickup_datetime,
        payment_type,
        fare_amount,
        surcharge,
        mta_tax,
        tip_amount,
        tolls_amount,
        total_amount
        from nyctaxidb.fare
        )f
        on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
        where t.sample_key<=0.01

Para executar esta consulta de linha de comandos do diretório da Hive:

    hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

Agora, temos uma tabela interna, **nyctaxidb.nyctaxi_downsampled_dataset**, que podem ser acedidos utilizando o [importar dados] [ import-data] módulo do Machine Learning. Além disso, podemos usar este conjunto de dados para a criação de modelos do Machine Learning.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>Utilizar o módulo de dados de importação no Machine Learning para acessar os dados de objeto de amostragem para baixo
Para emitir consultas do Hive no [importar dados] [ import-data] módulo do Machine Learning, precisa de acesso a um área de trabalho de aprendizagem automática. Também precisa de acesso às credenciais do cluster e a sua conta do storage associada.

Aqui estão alguns detalhes sobre o [importar dados] [ import-data] módulo e os parâmetros de entrada:

**URI do servidor de HCatalog**: Se for o nome do cluster **abc123**, isso é simplesmente: https://abc123.azurehdinsight.net.

**Nome de conta de utilizador do Hadoop**: O nome de utilizador escolhido para o cluster (não o nome de utilizador de acesso remoto).

**Palavra-passe de conta de utilizador Hadoop**: A palavra-passe escolhida para o cluster (e não a senha do acesso remoto).

**Localização de dados de saída**: Isso é escolhido para ser o Azure.

**Nome da conta de armazenamento do Azure**: Nome da conta de armazenamento predefinida associada ao cluster.

**Nome do contentor do Azure**: Isso é o nome de contentor predefinido para o cluster e, normalmente é o mesmo que o nome do cluster. Para um cluster chamado **abc123**, isso é abc123.

> [!IMPORTANT]
> Qualquer tabela que Desejamos consultar com o [importar dados] [ import-data] módulo no Machine Learning tem de ser uma tabela interna.
> 
> 

Eis como determinar se uma tabela **T** numa base de dados **D.db** é uma tabela interna. A partir da linha de comandos do diretório de ramo de registo, execute o seguinte comando:

    hdfs dfs -ls wasb:///D.db/T

Se a tabela é uma tabela interna e este é preenchido, seu conteúdo tiver de mostrar aqui.

Outra forma de determinar se uma tabela é uma tabela interna é utilizar o Explorador de armazenamento do Azure. Utilize-o para navegar para o nome de contentor predefinido do cluster e, em seguida, filtrar pelo nome da tabela. Se a tabela e o respetivo conteúdo aparecer, confirma que o que é uma tabela interna.

Eis uma captura de ecrã da consulta do Hive e o [importar dados] [ import-data] módulo:

![Captura de ecrã de consulta do Hive do módulo de importar dados](./media/hive-walkthrough/1eTYf52.png)

Porque os nossos dados de objeto de amostragem de baixo no contentor predefinido, a consulta do Hive resultante do Machine Learning é muito simples. É apenas uma **SELECIONAR * de nyctaxidb.nyctaxi\_downsampled\_dados**.

O conjunto de dados pode agora ser utilizado como ponto de partida para a criação de modelos do Machine Learning.

### <a name="mlmodel"></a>Criar modelos de Machine Learning
Agora, pode avançar para a criação de modelo e implementação de modelo na [Machine Learning](https://studio.azureml.net). Os dados estão prontos para nós utilizar como trabalhar com os problemas de predição identificados anteriormente:

- **Classificação binária**: Para prever se é ou não uma dica foi paga por uma viagem.

  **Aprendiz utilizado:** Regressão logística de duas classes

  a. Para este problema, a etiqueta de destino (ou classe) é **colocado para**. O conjunto de dados original objeto de amostragem de baixo tem algumas colunas que são vazamentos de destino para esta experiência de classificação. Em particular, **sugestão\_classe**, **tip\_quantidade**, e **total\_quantidade** revelam informações sobre o destino da etiqueta que não está disponível em tempo de teste. Vamos remover essas colunas de consideração ao utilizar o [Select Columns in Dataset] [ select-columns] módulo.

  O diagrama seguinte mostra nossos experimentação para prever se ou não uma dica foi paga por uma viagem de determinado:

  ![Diagrama da experimentação para prever se tip foi pago](./media/hive-walkthrough/QGxRz5A.png)

  b. Nesta experimentação, nossa distribuições de etiqueta de destino foram aproximadamente 1:1.

   A tabela a seguir mostra a distribuição de sugestão de etiquetas de classe para o problema de classificação binária:

  ![Gráfico de distribuição de etiquetas de classe de sugestão](./media/hive-walkthrough/9mM4jlD.png)

    Como resultado, obtemos uma área em que a curva (AUC) de 0.987, conforme mostrado na figura a seguir:

  ![Gráfico de valor AUC](./media/hive-walkthrough/8JDT0F8.png)

- **Classificação multiclasses**: Para prever o intervalo de quantidades de sugestão pagos para a viagem, utilizando as classes definidas anteriormente.

  **Aprendiz utilizado:** Regressão logística várias classes

  a. Para este problema, é nosso rótulo de destino (ou classe) **sugestão\_classe**, que pode efetuar uma das cinco valores (0,1,2,3,4). Como é o caso de classificação binária, temos algumas colunas que são vazamentos de destino para esta fase experimental. Em particular, **tipados**, **sugestão\_quantidade**, e **total\_quantidade** revelar informações sobre a etiqueta de destino que não está disponível em o tempo de teste. Vamos remover estas colunas utilizando a [Select Columns in Dataset] [ select-columns] módulo.

  O diagrama seguinte mostra a experimentação para prever no qual bin uma dica é provável que enquadram-se. Os contentores são: Classe 0: Sugestão = US $0, 1 de classe: Sugestão > US $0 e sugestão < = US $5, 2 de classe: Sugestão > US $5 e sugestão < = US $10, 3 de classe: Sugestão > US $10 e sugestão < = us $20 e 4 de classe: Sugestão > us $20.

  ![Diagrama da experimentação para prever bin sugestão](./media/hive-walkthrough/5ztv0n0.png)

  Agora vamos mostrar como a distribuição de classe do teste real se parece. Classe 0 e 1 de classe são predominantes e outras classes são raras.

  ![Gráfico de distribuição de classe de teste](./media/hive-walkthrough/Vy1FUKa.png)

  b. Nesta experimentação, utilizamos uma matriz de confusão para examinar as precisões de previsão. Isso é mostrado aqui:

  ![Matriz de confusão](./media/hive-walkthrough/cxFmErM.png)

  Tenha em atenção que, enquanto as precisões de classe nas classes predominantes são muito bons, o modelo não faz um bom trabalho "learning" nas classes mais raros.

- **Tarefa de regressão**: Para prever a quantidade de sugestão pagos por uma viagem.

  **Aprendiz utilizado:** árvore de decisões elevada

  a. Para este problema, a etiqueta de destino (ou classe) é **sugestão\_quantidade**. Neste caso são os vazamentos de destino: **tipados**, **sugestão\_classe**, e **total\_quantidade**. Todas essas variáveis revelam informações sobre a quantidade de sugestão que não está normalmente disponível em tempo de teste. Vamos remover estas colunas utilizando a [Select Columns in Dataset] [ select-columns] módulo.

  O diagrama seguinte mostra a experimentação para prever a quantidade de dica de determinado:

  ![Diagrama da experimentação para prever a quantidade de sugestão](./media/hive-walkthrough/11TZWgV.png)

  b. Para problemas de regressão, medimos os precisões de predição ao observar o erro ao quadrado em predições e o coeficiente de determinação:

  ![Captura de ecrã de estatísticas de predição](./media/hive-walkthrough/Jat9mrz.png)

  Aqui, o coeficiente de determinação é 0.709, implicando que cerca de 71% da variação é explicada pelos coeficientes do modelo.

> [!IMPORTANT]
> Para saber mais sobre o Machine Learning e como aceder e utilizá-lo, veja [o que é o Machine Learning](../studio/what-is-machine-learning.md). Além disso, o [Galeria de IA do Azure](https://gallery.cortanaintelligence.com/) abrange uma gama de experimentações e fornece uma introdução completa para o leque de funcionalidades do Machine Learning.
> 
> 

## <a name="license-information"></a>Informações de licença
Estas instruções de exemplo e seus scripts que acompanha este artigo são partilhadas pela Microsoft sob a licença do MIT. Para obter mais detalhes, consulte a **LICENSE.txt** ficheiro no diretório do código de exemplo no GitHub.

## <a name="references"></a>Referências
• [Andrés Monroy NYC táxis viagens a página de transferência](https://www.andresmh.com/nyctaxitrips/)  
• [Táxis de fOILing NYC dados de viagens por Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [Comissão de táxis de NYC e Limousine pesquisa e as estatísticas](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/



