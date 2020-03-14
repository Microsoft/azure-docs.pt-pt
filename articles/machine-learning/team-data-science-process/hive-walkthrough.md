---
title: Explorar os dados num cluster do Hadoop - Team Data Science Process
description: Usando o processo de ciência de dados de equipa para um cenário ponto-a-ponto, empregando um cluster de Hadoop do HDInsight para criar e implementar um modelo.
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
ms.openlocfilehash: 005d4fe1b6ec59e7f05be3dd2ab3e72d0e7aa8e0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283423"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>O processo de ciência de dados de equipa em ação: utilização do Azure HDInsight Hadoop clusters
Neste passeio, utilizamos o Processo de Ciência de [Dados da Equipa (TDSP)](overview.md) num cenário de ponta a ponta. Utilizamos um [cluster Hadoop Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar e recolher dados de engenharia de recursos do conjunto de dados de [NyC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) publicamente disponível e para recolher os dados. Para lidar com classificação binária e várias classes e tarefas de previsão de regressão, desenvolvemos os modelos de dados com o Azure Machine Learning. 

Para um walkthrough que mostra como lidar com um conjunto de dados maior, consulte o Processo de Ciência de [Dados da Equipa - Utilizando clusters de hadoop Azure HDInsight num conjunto de dados de 1 TB](hive-criteo-walkthrough.md).

Também pode utilizar um caderno IPython para realizar as tarefas apresentadas no walkthrough que utiliza o conjunto de dados de 1-TB. Para mais informações, consulte a passagem de [Criteo utilizando uma ligação Hive ODBC](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).

## <a name="dataset"></a>Descrição do conjunto de dados de viagens de táxi de NYC
Os dados da viagem de táxis de NYC são cerca de 20 GB de arquivos de comprimido valores separados por vírgulas (CSV) (GB de ~ 48 descomprimido). Ele tem mais de 173 milhões de viagens individuais e inclui fares pagos para cada viagem. Cada registo de viagem inclui a pegada e redução de localização e tempo, hack anónimo (driver) número de licença e o número de medallion (a táxis de ID exclusivo). Os dados abrange todas as viagens no ano de 2013 e são fornecidos nas seguintes dois conjuntos de dados para todos os meses:

- Os trip_data ficheiros CSV contêm detalhes da viagem: o número de passageiros, pontos de recolha e entrega, duração da viagem e duração da viagem. Aqui estão alguns exemplos de registros:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
- Os trip_fare ficheiros CSV contêm detalhes da tarifa paga por cada viagem: tipo de pagamento, valor da tarifa, sobretaxa e impostos, gorjetas e portagens, e o valor total pago. Aqui estão alguns exemplos de registros:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A chave única para se juntar à viagem\_dados e viagem\_tarifa é composta pelos campos: medalhão, licença de\_e recolha\_data. Para obter todos os detalhes relevantes para uma viagem particular, é suficiente para associar estas três chaves.

## <a name="mltasks"></a>Exemplos de tarefas de previsão
Determine o tipo de previsões que pretende fazer com base na análise de dados para ajudar a esclarecer as tarefas de processo necessárias. Aqui estão três exemplos de problemas de previsão que abordamos nesta passagem, tudo com base na *ponta\_quantidade:*

- **Classificação binária**: Preveja se uma gorjeta foi ou não paga para uma viagem. Ou seja, uma *gorjeta\_montante* superior a $0 é um exemplo positivo, enquanto uma gorjeta\_*valor* de $0 é um exemplo negativo.
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0
- **Classificação multiclasse**: Preveja o intervalo de valores de gorjeta pagos pela viagem. Dividimos a *gorjeta\_quantidade* em cinco classes:
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0 and tip_amount <= $5
        Class 2: tip_amount > $5 and tip_amount <= $10
        Class 3: tip_amount > $10 and tip_amount <= $20
        Class 4: tip_amount > $20
- Tarefa de **regressão**: Preveja o valor da gorjeta paga por uma viagem.  

## <a name="setup"></a>Criar um cluster Hadoop HDInsight para análise supértica
> [!NOTE]
> Normalmente, trata-se uma tarefa de administração.
> 
> 

Pode configurar um ambiente do Azure para análise avançada que emprega um cluster do HDInsight em três passos:

1. [Criar uma conta](../../storage/common/storage-account-create.md)de armazenamento : Esta conta de armazenamento é utilizada para armazenar dados no armazenamento do Azure Blob. Os dados usados em clusters do HDInsight também residem aqui.
2. [Personalize os clusters Hadoop Azure HDInsight para o Processo e Tecnologia Avançada](customize-hadoop-cluster.md)de Analytics. Este passo cria um cluster de Hadoop do HDInsight com 64 bits Anaconda Python 2.7 instalados em todos os nós. Existem dois passos importantes a serem lembrados ao personalizar o seu cluster do HDInsight.
   
   * Lembre-se de vincular a conta de armazenamento que criou no passo 1 com o seu cluster do HDInsight quando estiver a criar. Esta conta de armazenamento aceda aos dados que são processados dentro do cluster.
   * Depois de criar o cluster, ative o acesso remoto para o nó principal do cluster. Navegue no separador **Configuração** e selecione **Ativar Remote**. Neste passo Especifica as credenciais de utilizador utilizadas para início de sessão remoto.
3. [Crie um espaço de trabalho Azure Machine Learning:](../studio/create-workspace.md)Você usa este espaço de trabalho para construir modelos de aprendizagem automática. Esta tarefa é abordada depois de concluir uma exploração de dados inicial e para baixo-amostragem, ao utilizar o cluster do HDInsight.

## <a name="getdata"></a>Obtenha os dados de uma fonte pública
> [!NOTE]
> Normalmente, trata-se uma tarefa de administração.
> 
> 

Para copiar o conjunto de dados [de Viagens](https://www.andresmh.com/nyctaxitrips/) de Táxi de NYC para a sua máquina a partir da sua localização pública, utilize qualquer um dos métodos descritos em dados de Move de e para o armazenamento da [Blob Azure](move-azure-blob.md).

Aqui, descrevemos como utilizar o AzCopy para transferir os ficheiros que contêm dados. Para descarregar e instalar o AzCopy, siga as instruções em [Iniciar-se com o utilitário da linha de comando AzCopy](../../storage/common/storage-use-azcopy.md).

1. A partir de uma janela de comando, executar os seguintes comandos AzCopy, substituindo *\<path_to_data_folder>* com o destino desejado:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

1. Quando a cópia estiver concluída, verá um total de 24 arquivos compactados na pasta de dados escolhido. Descompacte os ficheiros transferidos para o mesmo diretório no seu computador local. Tome nota da pasta onde residem os ficheiros descomprimidos. Esta pasta é referida como o *caminho\<\_para\_unzipped_data\_ficheiros\>* no que se segue.

## <a name="upload"></a>Faça upload dos dados para o recipiente padrão do cluster Hadoop HDInsight
> [!NOTE]
> Normalmente, trata-se uma tarefa de administração.
> 
> 

Nos seguintes comandos do AzCopy, substitua os seguintes parâmetros com os valores reais que especificou ao criar o cluster de Hadoop e descomprimir os ficheiros de dados.

* ***\<path_to_data_folder>*** O diretório (juntamente com o caminho) na sua máquina que contém os ficheiros de dados desapertados.  
* ***\<nome da conta de armazenamento do cluster Hadoop>*** A conta de armazenamento associada ao seu cluster HDInsight.
* ***\<recipiente padrão do cluster Hadoop>*** O recipiente predefinido utilizado pelo seu cluster. O nome do recipiente predefinido é geralmente o mesmo nome que o próprio cluster. Por exemplo, se o cluster for chamado "abc123.azurehdinsight.net", o contentor predefinido é abc123.
* ***\<conta de armazenamento chave>*** A chave para a conta de armazenamento utilizada pelo seu cluster.

A partir de um prompt de comando ou uma janela do Windows PowerShell, execute os seguintes dois comandos do AzCopy.

Este comando envia os dados da viagem para o diretório ***nyctaxitripraw*** no recipiente padrão do cluster Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Este comando envia os dados da tarifa para o diretório ***nyctaxifareraw*** no recipiente padrão do cluster Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Os dados devem agora ser no armazenamento de BLOBs e prontas para serem consumidas dentro do cluster do HDInsight.

## <a name="#download-hql-files"></a>Inscreva-se no nó da cabeça do cluster Hadoop e prepare-se para análise de dados exploratórios
> [!NOTE]
> Normalmente, trata-se uma tarefa de administração.
> 
> 

Para aceder ao nó principal do cluster para análise de dados exploratórios e amostragem dos dados, siga o procedimento delineado no [Access o nó de cabeça do Cluster Hadoop](customize-hadoop-cluster.md).

Neste passeio, usamos principalmente consultas escritas na [Hive](https://hive.apache.org/), uma linguagem de consulta semelhante a SQL, para realizar explorações preliminares de dados. As consultas da Hive são armazenadas em ficheiros '.hql'. Podemos, em seguida, dimensionar estes dados a ser utilizado dentro de Machine Learning para a criação de modelos.

Para preparar o cluster para análise de dados exploratórios, descarregue os ficheiros '.hql' que contenham os scripts da Colmeia relevantes do [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) para um diretório local (C:\temp) no nó da cabeça. Abra o pedido de comando a partir do nó da cabeça do cluster, e executar os seguintes dois comandos:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Estes dois comandos descarregam todos os ficheiros '.hql' necessários neste passeio para o diretório local ***C:\temp&#92;***  no nó da cabeça.

## <a name="#hive-db-tables"></a>Criar base de dados da Hive e tabelas divididas por mês
> [!NOTE]
> Esta tarefa é normalmente para um administrador.
> 
> 

Está agora pronto para criar tabelas do Hive do conjunto de dados de táxis de NYC.
No nó principal do cluster de Hadoop, abra a linha de comandos do Hadoop no ambiente de trabalho do nó principal. Introduza o diretório de ramo de registo ao executar o seguinte comando:

    cd %hive_home%\bin

> [!NOTE]
> Executar todos os comandos de Hive nestas instruções a partir do contentor de Hive / linha de comandos do diretório. Isto resolve automaticamente quaisquer problemas de caminho. Utilizamos os termos "Prompt de diretório do Hive", "Hive bin / linha de comandos do diretório" e "Linha de comandos de Hadoop", alternadamente nestas instruções.
> 
> 

A partir do aviso de diretório da Hive, execute o seguinte comando na linha de comando Hadoop do nó de cabeça que cria a base de dados e tabelas da Hive:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Aqui está o conteúdo da **amostra C:\temp\\_a colmeia\_criar\_db\_e\_tabelas.hql** arquivo que cria a base de dados da Hive **nyctaxidb**, e a **viagem** de mesas e **tarifa.**

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

* A tabela de **viagem** contém detalhes da viagem de cada passeio (detalhes do condutor, tempo de recolha, distância de viagem e horários).
* A tabela de **tarifas** contém detalhes da tarifa (valor da tarifa, valor da gorjeta, portagens e sobretaxas).

Se precisar de assistência adicional com estes procedimentos, ou se quiser investigar os alternativos, consulte a secção [Enviar consultas diretamente a partir da linha de comando Hadoop](move-hive-tables.md#submit).

## <a name="#load-data"></a>Carregue os dados para as tabelas da Colmeia por divisórias
> [!NOTE]
> Esta tarefa é normalmente para um administrador.
> 
> 

O conjunto de dados de táxis de NYC tem uma criação de partições natural por mês, o que usamos para permitir tempos mais rápidos de processamento e da consulta. Os seguintes comandos do PowerShell (emitidos a partir do diretório do Hive através da linha de comandos do Hadoop) carregar dados para a viagem e se comportarão de tabelas do Hive, particionadas por mês.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

A **amostra\_colmeia\_carregar\_dados\_por divisórias\_.ficheiro hql** contém os seguintes comandos **LOAD:**

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Algumas das consultas da Colmeia usadas aqui no processo de exploração envolvem olhar apenas uma ou duas divisórias. Mas estas consultas podem ser executadas em todo o conjunto de dados.

### <a name="#show-db"></a>Mostrar bases de dados no cluster Hadoop HDInsight
Para mostrar as bases de dados criados no cluster de Hadoop do HDInsight dentro da janela de linha de comandos do Hadoop, execute o seguinte comando na linha de comandos de Hadoop:

    hive -e "show databases;"

### <a name="#show-tables"></a>Mostre as mesas da Colmeia na base de dados de **Nyctaxidb**
Para mostrar as tabelas na base de dados de **Nyctaxidb,** execute o seguinte comando na linha de comando Hadoop:

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

## <a name="#explore-hive"></a>Exploração de dados e engenharia de recursos na Colmeia
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

Pode guardar os registos de um ficheiro para visualização conveniente com uma pequena alteração na consulta anterior:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Exploração: Ver o número de registos em cada um dos 12 partições
> [!NOTE]
> Normalmente, trata-se uma tarefa de cientista de dados.
> 
> 

De interesse é a forma como o número de viagens varia durante o ano de calendário. Agrupamento por mês mostra a distribuição de viagens.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Este comando produz a seguinte saída:

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

Este comando cede:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Utilizar comandos semelhantes às mostradas para o conjunto de dados de viagens, podemos emitir consultas do Hive no prompt de diretório do ramo de registo para o conjunto de dados de Europeia validar o número de registos de.

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Este comando produz esta saída:

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

O mesmo número de viagens por mês é devolvido para ambos os conjuntos de dados, desde a primeira validação de que os dados foram carregados corretamente.

Pode contar o número total de registos no conjunto de dados europeia utilizando o seguinte comando no prompt de diretório do ramo de registo de:

    hive -e "select count(*) from nyctaxidb.fare;"

Este comando cede:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

O número total de registos em ambas as tabelas é também o mesmo, proporcionando uma segunda validação de que os dados foram carregados corretamente.

### <a name="exploration-trip-distribution-by-medallion"></a>Exploração: Distribuição de viagem por medallion
> [!NOTE]
> Esta análise é tipicamente uma tarefa de cientista de dados.
> 
> 

Neste exemplo identifica os medallions (números de táxis) com mais de 100 viagens dentro de um determinado período de tempo. A consulta beneficia do acesso à mesa dividido, porque é condicionado pelo **mês**variável de partição. Os resultados da consulta são escritos para um arquivo local, **queryoutput.tsv,** em `C:\temp` no nó da cabeça.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Aqui está o conteúdo da **amostra\_colmeia\_viagem\_contagem\_por\_medallion.hql** arquivo para inspeção.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Medallion no conjunto de dados de táxis NYC identifica um cab exclusivo. É possível identificar quais cabs são comparativamente ocupados fazendo quais fez mais do que um determinado número de viagens num período de tempo específico. O exemplo seguinte identifica táxis que fizeram mais de uma centena de viagens nos primeiros três meses, e poupa os resultados da consulta a um arquivo local, **C:\temp\queryoutput.tsv**.

Aqui está o conteúdo da **amostra\_colmeia\_viagem\_contagem\_por\_medallion.hql** arquivo para inspeção.

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
> Esta tarefa é tipicamente para um cientista de dados.
> 
> 

Ao explorar um conjunto de dados, queremos frequentemente examinar as distribuições de grupos de valores. Esta secção fornece um exemplo de como fazer esta análise para táxis e motoristas.

A **amostra\_a colmeia\_viagem\_contar\_por\_medalhão\_licença.hql** agrupa o conjunto de dados de tarifas sobre **medalhão** e **hack_license,** e devolve as contagens de cada combinação. Eis o seu conteúdo:

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

Esta consulta devolve as combinações do cab e do controlador, ordenadas pelo número de viagens de descendente.

A partir da linha de comandos do diretório de ramo de registo, execute:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

Os resultados da consulta são escritos para um arquivo local, **C:\temp\questionryoutput.tsv**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Exploração: Avaliar a qualidade dos dados ao verificar a existência de enviar registros de latitude ou longitude inválido
> [!NOTE]
> Normalmente, trata-se uma tarefa de cientista de dados.
> 
> 

É um objetivo comum de análise de dados exploratórios eliminar registos inválidos ou incorretos. O exemplo nesta secção, determina se os campos de longitude ou latitude contém um valor até agora fora da área de NYC. Uma vez que é provável que esses registros têm um valor de longitude-latitude errado, que Desejamos eliminá-los a partir de quaisquer dados que está a ser usados para modelagem.

Aqui está o conteúdo da **amostra\_colmeia\_avaliação\_de qualidade.hql** para inspeção.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


A partir da linha de comandos do diretório de ramo de registo, execute:

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

O argumento *-S* incluído neste comando suprime a impressão do ecrã de estado do Mapa da Colmeia/Reduzir os empregos. Este comando é útil porque torna a impressão do ecrã da saída de consulta da Colmeia mais legível.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Exploração: Distribuições de classe binário dicas de viagem
> [!NOTE]
> Normalmente, trata-se uma tarefa de cientista de dados.
> 
> 

Para o problema de classificação binária delineado na secção [Exemplos de tarefas de previsão,](hive-walkthrough.md#mltasks) é útil saber se foi dada ou não uma dica. Esta distribuição de dicas é binária:

* dica dada (Classe 1, gorjeta\_valor > $0)  
* nenhuma dica (Classe 0, gorjeta\_valor = $0)

A amostra a seguir **\_colmeia\_\_frequências.ficheiro hql** mostra o comando a executar:

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

A partir da linha de comandos do diretório de ramo de registo, execute:

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Exploração: Distribuições na definição de várias classes de classe
> [!NOTE]
> Normalmente, trata-se uma tarefa de cientista de dados.
> 
> 

Para o problema de classificação multiclasse delineado na secção [Exemplos de tarefas de previsão,](hive-walkthrough.md#mltasks) este conjunto de dados também se presta a uma classificação natural para prever a quantidade das dicas dadas. Podemos utilizar contentores para definir os intervalos de sugestão na consulta. Para obter as distribuições de classe para as várias gamas de pontas, use a **amostra\_colmeia\_ponta\_intervalo\_frequências.hql.** Eis o seu conteúdo.

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

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>Exploração: A distância direta entre duas localizações de longitude-latitude de computação
> [!NOTE]
> Normalmente, trata-se uma tarefa de cientista de dados.
> 
> 

Pode querer saber se existe uma diferença entre a distância direta entre dois locais e a distância de viagem real da táxis. Um passageiro pode ser uma probabilidade menor de sugestão se eles descobrir que o driver intencionalmente apresentou-los por uma rota mais tempo.

Para ver a comparação entre a distância real da viagem e a [distância haversina](https://en.wikipedia.org/wiki/Haversine_formula) entre dois pontos de longitude-latitude (a distância "grande círculo"), pode utilizar as funções trigonométricas disponíveis na Colmeia:

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

A consulta anterior, R é igual ao raio da terra em quilómetros e pi é convertido em radianos. Os pontos de longitude-latitude são filtrados para remover valores que estão longe da área de NYC.

Neste caso, escrevemos os resultados a um diretório chamado **queryoutputdir**. A sequência dos seguintes comandos cria primeiro este diretório de saída e, em seguida, executa o comando de ramo de registo.

A partir da linha de comandos do diretório de ramo de registo, execute:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


Os resultados da consulta são escritos a nove blobs Azure **(queryoutputdir/00000\_0** para **consultaoutputdir/000008\_0),** sob o recipiente padrão do cluster Hadoop.

Para ver o tamanho dos blobs individuais, execute o seguinte comando da linha de comandos do diretório da Hive:

    hdfs dfs -ls wasb:///queryoutputdir

Para ver o conteúdo de um determinado ficheiro, digamos **000000\_0**, use o comando `copyToLocal` de Hadoop.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [!WARNING]
> `copyToLocal` pode ser muito lento para ficheiros grandes, e não é recomendado para uso com eles.  
> 
> 

Uma vantagem fundamental de ter estes dados a residir numa bolha Azure é que podemos explorar os dados dentro do Machine Learning, utilizando o módulo [de Dados de Importação.][import-data]

## <a name="#downsample"></a>Dados de amostra saque e modelos de construção em Machine Learning
> [!NOTE]
> Normalmente, trata-se uma tarefa de cientista de dados.
> 
> 

Após a fase de análise de dados exploratório, podemos agora está prontos para dimensionar os dados para a criação de modelos de Machine Learning. Nesta secção, vamos mostrar como utilizar uma consulta do Hive para dimensionar os dados. O Machine Learning acede-o então a partir do módulo [de dados de importação.][import-data]

### <a name="down-sampling-the-data"></a>Os dados de amostragem para baixo
Existem dois passos deste procedimento. Primeiro juntamo-nos às mesas **nyctaxidb.trip** e **nyctaxidb.fare** em três chaves que estão presentes em todos os registos: **medalhão,** **licença hack\_** e recolha\_**data.** Em seguida, geramos uma etiqueta de classificação binária, **inclinada,** e uma etiqueta de classificação multiclasse, **ponta\_classe**.

Para poder utilizar os dados amostrados diretamente a partir do módulo de dados de [importação][import-data] em Machine Learning, deverá armazenar os resultados da consulta anterior a uma tabela interna da Colmeia. O que se segue, podemos criar uma tabela de ramo de registo interna e preencher o seu conteúdo com os dados associados e a amostragem de baixo.

A consulta aplica funções padrão da Hive diretamente para gerar os seguintes parâmetros de tempo a partir do campo **de recolha\_data:**
- Hora do dia
- semana do ano
- dia da semana ('1' significa segunda-feira, e '7' significa domingo)

A consulta também gera a distância direta entre as localizações de pegada e redução. Para obter uma lista completa dessas funções, consulte O Manual de [Idiomas UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

A consulta, em seguida, baixo-samples os dados para que os resultados da consulta se encaixa em Azure Machine Learning Studio. Apenas cerca de 1 por cento do conjunto de dados original é importado para o studio.

Aqui estão o conteúdo da amostra\_\_preparar\_para\_ficheiro **aml\_full.hql** que prepara dados para a construção de modelos em Machine Learning:

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

Temos agora uma tabela interna, **nyctaxidb.nyctaxi_downsampled_dataset,** que pode ser acedida através do módulo [de Dados][import-data] de Importação da Machine Learning. Além disso, podemos usar este conjunto de dados para a criação de modelos do Machine Learning.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>Utilizar o módulo de dados de importação no Machine Learning para acessar os dados de objeto de amostragem para baixo
Para emitir consultas de Hive no módulo de [dados de importação][import-data] de Machine Learning, você precisa de acesso a um espaço de trabalho de Machine Learning. Também precisa de acesso às credenciais do cluster e a sua conta do storage associada.

Aqui estão alguns detalhes sobre o módulo [de Dados de Importação][import-data] e os parâmetros para a entrada:

**HCatalog servidor URI**: Se o nome do cluster for **abc123,** utilize: https://abc123.azurehdinsight.net.

Nome da conta de **utilizador hadoop**: O nome de utilizador escolhido para o cluster (não o nome de utilizador de acesso remoto).

**Palavra-passe**da conta de utilizador hadoop : A palavra-passe escolhida para o cluster (não a palavra-passe de acesso remoto).

**Localização dos dados de saída**: Escolhido para ser Azure.

Nome da **conta de armazenamento azure**: Nome da conta de armazenamento por defeito associada ao cluster.

**Nome**do recipiente azul : O nome do recipiente predefinido para o cluster, e é tipicamente o mesmo que o nome do cluster. Para um aglomerado chamado **abc123,** o nome é abc123.

> [!IMPORTANT]
> Qualquer tabela que desejemos consultar utilizando o módulo [de dados de importação][import-data] em Machine Learning deve ser uma tabela interna.
> 
> 

Aqui está como determinar se uma tabela **T** numa base de dados **D.db** é uma mesa interna. A partir da linha de comandos do diretório de ramo de registo, execute o seguinte comando:

    hdfs dfs -ls wasb:///D.db/T

Se a tabela é uma tabela interna e este é preenchido, seu conteúdo tiver de mostrar aqui.

Outra forma de determinar se uma tabela é uma tabela interna é utilizar o Explorador de armazenamento do Azure. Utilize-o para navegar para o nome de contentor predefinido do cluster e, em seguida, filtrar pelo nome da tabela. Se a tabela e o respetivo conteúdo aparecer, confirma que o que é uma tabela interna.

Aqui está uma imagem da consulta da Colmeia e do módulo de [dados de importação:][import-data]

![Captura de ecrã de consulta do Hive do módulo de importar dados](./media/hive-walkthrough/1eTYf52.png)

Como os nossos dados amostrados residem no recipiente padrão, a consulta da Hive resultante do Machine Learning é simples. É apenas um **SELECT * From nyctaxidb.nyctaxi\_dados de\_amostrado.**

O conjunto de dados pode agora ser utilizado como ponto de partida para a criação de modelos do Machine Learning.

### <a name="mlmodel"></a>Construir modelos em Machine Learning
Agora pode passar à modelação e modelação de implementação em [Machine Learning.](https://studio.azureml.net) Os dados estão prontos para nós utilizar como trabalhar com os problemas de predição identificados anteriormente:

- **Classificação binária**: Para prever se uma gorjeta foi ou não paga para uma viagem.

  **Aprendiz utilizado:** Regressão logística de duas classes

  a. Para este problema, a etiqueta alvo (ou classe) é **inclinada**. O conjunto de dados original objeto de amostragem de baixo tem algumas colunas que são vazamentos de destino para esta experiência de classificação. Em particular, **dica\_classe,** **ponta\_quantidade**, e total\_**quantidade** revelam informações sobre o rótulo alvo que não está disponível no momento do teste. Removemos estas colunas da consideração utilizando as Colunas Select no módulo [Dataset.][select-columns]

  O diagrama seguinte mostra nossos experimentação para prever se ou não uma dica foi paga por uma viagem de determinado:

  ![Diagrama da experimentação para prever se tip foi pago](./media/hive-walkthrough/QGxRz5A.png)

  b. Nesta experimentação, nossa distribuições de etiqueta de destino foram aproximadamente 1:1.

   A tabela a seguir mostra a distribuição de sugestão de etiquetas de classe para o problema de classificação binária:

  ![Gráfico de distribuição de etiquetas de classe de sugestão](./media/hive-walkthrough/9mM4jlD.png)

    Como resultado, obtemos uma área em que a curva (AUC) de 0.987, conforme mostrado na figura a seguir:

  ![Gráfico de valor AUC](./media/hive-walkthrough/8JDT0F8.png)

- **Classificação multiclasse**: Para prever o intervalo de valores de gorjeta pagos pela viagem, utilizando as classes previamente definidas.

  **Aprendiz utilizado:** Regressão logística multiclasse

  a. Para este problema, o nosso rótulo alvo (ou classe) é **a\_classe,** que pode ter um de cinco valores (0,1,2,3,4). Como é o caso de classificação binária, temos algumas colunas que são vazamentos de destino para esta fase experimental. Em particular, **gorjeta,** **ponta\_quantidade**, e montante total **\_** revelar informações sobre o rótulo alvo que não está disponível no momento do teste. Removemos estas colunas utilizando as Colunas Select no módulo [Dataset.][select-columns]

  O diagrama seguinte mostra a experimentação para prever no qual bin uma dica é provável que enquadram-se. Os contentores são: classe 0: tip = US $0, 1 de classe: Sugestão > US $0 e sugestão < = US $5, 2 de classe: Sugestão > US $5 e sugestão < = US $10, 3 de classe: Sugestão > US $10 e sugestão < = us $20 e 4 de classe: Sugestão > us $20.

  ![Diagrama da experimentação para prever bin sugestão](./media/hive-walkthrough/5ztv0n0.png)

  Agora vamos mostrar como a distribuição de classe do teste real se parece. Classe 0 e 1 de classe são predominantes e outras classes são raras.

  ![Gráfico de distribuição de classe de teste](./media/hive-walkthrough/Vy1FUKa.png)

  b. Para esta experiência, usamos uma matriz de confusão para olhar para as precisões de previsão como mostrado aqui:

  ![Matriz de confusão](./media/hive-walkthrough/cxFmErM.png)

  Embora as precisões de classe nas aulas prevalentes sejam boas, o modelo não faz um bom trabalho de "aprendizagem" nas classes mais raras.

- Tarefa de **regressão**: Prever o valor da gorjeta paga por uma viagem.

  **Aprendiz utilizado:** Árvore de decisão reforçada

  a. Para este problema, a etiqueta alvo (ou classe) é **a ponta\_quantidade**. As fugas de alvo neste caso são: **gorjeta,** **ponta\_classe,** e **quantidade total de\_.** Todas essas variáveis revelam informações sobre a quantidade de sugestão que não está normalmente disponível em tempo de teste. Removemos estas colunas utilizando as Colunas Select no módulo [Dataset.][select-columns]

  O diagrama seguinte mostra a experimentação para prever a quantidade de dica de determinado:

  ![Diagrama da experimentação para prever a quantidade de sugestão](./media/hive-walkthrough/11TZWgV.png)

  b. Para problemas de regressão, medimos os precisões de predição ao observar o erro ao quadrado em predições e o coeficiente de determinação:

  ![Captura de ecrã de estatísticas de predição](./media/hive-walkthrough/Jat9mrz.png)

  Aqui, o coeficiente de determinação é 0.709, implicando que cerca de 71% da variação é explicada pelos coeficientes do modelo.

> [!IMPORTANT]
> Para saber mais sobre machine learning e como acessá-lo e usá-lo, consulte [o What's Machine Learning](../studio/what-is-machine-learning.md). Além disso, a [Galeria Azure AI](https://gallery.cortanaintelligence.com/) cobre uma gama de experiências e fornece uma introdução completa na gama de capacidades de Machine Learning.
> 
> 

## <a name="license-information"></a>Informações de licença
Estas instruções de exemplo e seus scripts que acompanha este artigo são partilhadas pela Microsoft sob a licença do MIT. Para mais informações, consulte o ficheiro **LICENSE.txt** no diretório do código da amostra no GitHub.

## <a name="references"></a>Referências
• Página de descarregamento de viagens de [táxi de Andrés Monroy NYC](https://www.andresmh.com/nyctaxitrips/)  
• [Foiling Dados da Viagem](https://chriswhong.com/open-data/foil_nyc_taxi/) de Táxi de NYC por Chris Whong   
• Pesquisa e Estatísticas da Comissão de [Táxis e Limusines](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page) da NYC

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/



