---
title: Explore dados num cluster Hadoop - Team Data Science Process
description: Utilizando o Processo de Ciência de Dados da Equipa para um cenário de ponta a ponta, empregando um cluster Hadoop HDInsight para construir e implementar um modelo.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283423"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>O Processo de Ciência de Dados da Equipa em ação: Use aglomerados hadoop Azure HDInsight
Neste passeio, utilizamos o Processo de Ciência de [Dados da Equipa (TDSP)](overview.md) num cenário de ponta a ponta. Utilizamos um [cluster Hadoop Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar e recolher dados de engenharia de recursos do conjunto de dados de [NyC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) publicamente disponível e para recolher os dados. Para lidar com tarefas preditivas de classificação binária e multiclasse e regressão, construímos modelos dos dados com o Azure Machine Learning. 

Para um walkthrough que mostra como lidar com um conjunto de dados maior, consulte o Processo de Ciência de [Dados da Equipa - Utilizando clusters de hadoop Azure HDInsight num conjunto de dados de 1 TB](hive-criteo-walkthrough.md).

Também pode utilizar um caderno IPython para realizar as tarefas apresentadas no walkthrough que utiliza o conjunto de dados de 1-TB. Para mais informações, consulte a passagem de [Criteo utilizando uma ligação Hive ODBC](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>Descrição do conjunto de dados de viagens de táxi de NYC
Os dados da NyC Taxi Trip são cerca de 20 GB de ficheiros comprimidos separados pela vírda (CSV) (~48 GB de sem comprimição). Tem mais de 173 milhões de viagens individuais, e inclui as tarifas pagas por cada viagem. Cada registo de viagem inclui o local e hora de recolha e entrega, o número de carta de condução anoonizada (motorista) e o número de medalhão (o ID único do táxi). Os dados abrangem todas as viagens do ano de 2013, e são fornecidos nos dois conjuntos de dados seguintes para cada mês:

- Os trip_data ficheiros CSV contêm detalhes da viagem: o número de passageiros, pontos de recolha e entrega, duração da viagem e duração da viagem. Aqui estão alguns registos de amostras:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
- Os trip_fare ficheiros CSV contêm detalhes da tarifa paga por cada viagem: tipo de pagamento, valor da tarifa, sobretaxa e impostos, gorjetas e portagens, e o valor total pago. Aqui estão alguns registos de amostras:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A chave única\_para juntar\_dados de viagem e tarifa\_de viagem\_é composta pelos campos: medalhão, licença de hack e data de recolha. Para obter todos os detalhes relevantes para uma determinada viagem, é suficiente para se juntar a estas três chaves.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Exemplos de tarefas de previsão
Determine o tipo de previsões que pretende fazer com base na análise de dados para ajudar a esclarecer as tarefas de processo necessárias. Aqui estão três exemplos de problemas de previsão que abordamos nesta passagem, todos com base na quantidade de *gorjeta:\_*

- **Classificação binária**: Preveja se uma gorjeta foi ou não paga para uma viagem. Ou seja, um valor de *gorjeta\_* superior a $0 é um exemplo positivo, enquanto uma *gorjeta\_* de $0 é um exemplo negativo.
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0
- **Classificação multiclasse**: Preveja o intervalo de valores de gorjeta pagos pela viagem. Dividimos a *quantidade de gorjeta\_* em cinco classes:
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0 and tip_amount <= $5
        Class 2: tip_amount > $5 and tip_amount <= $10
        Class 3: tip_amount > $10 and tip_amount <= $20
        Class 4: tip_amount > $20
- Tarefa de **regressão**: Preveja o valor da gorjeta paga por uma viagem.  

## <a name="set-up-an-hdinsight-hadoop-cluster-for-advanced-analytics"></a><a name="setup"></a>Criar um cluster Hadoop HDInsight para análise supértica
> [!NOTE]
> Esta é tipicamente uma tarefa de administração.
> 
> 

Você pode configurar um ambiente Azure para análise avançada que emprega um cluster HDInsight em três etapas:

1. [Criar uma conta](../../storage/common/storage-account-create.md)de armazenamento : Esta conta de armazenamento é utilizada para armazenar dados no armazenamento do Azure Blob. Os dados utilizados nos clusters HDInsight também residem aqui.
2. [Personalize os clusters Hadoop Azure HDInsight para o Processo e Tecnologia Avançada](customize-hadoop-cluster.md)de Analytics. Este passo cria um cluster HDInsight Hadoop com 64 bits Anaconda Python 2.7 instalado em todos os nós. Há dois passos importantes a lembrar enquanto personaliza o seu cluster HDInsight.
   
   * Lembre-se de ligar a conta de armazenamento criada no passo 1 com o seu cluster HDInsight quando estiver a criá-la. Esta conta de armazenamento acede a dados que são processados dentro do cluster.
   * Depois de criar o cluster, ative o Acesso Remoto ao nó da cabeça do cluster. Navegue no separador **Configuração** e selecione **Ativar Remote**. Este passo especifica as credenciais de utilizador utilizadas para o login remoto.
3. [Crie um espaço de trabalho Azure Machine Learning:](../studio/create-workspace.md)Você usa este espaço de trabalho para construir modelos de aprendizagem automática. Esta tarefa é abordada após completar uma exploração inicial de dados e amostragem, utilizando o cluster HDInsight.

## <a name="get-the-data-from-a-public-source"></a><a name="getdata"></a>Obtenha os dados de uma fonte pública
> [!NOTE]
> Esta é tipicamente uma tarefa de administração.
> 
> 

Para copiar o conjunto de dados [de Viagens](https://www.andresmh.com/nyctaxitrips/) de Táxi de NYC para a sua máquina a partir da sua localização pública, utilize qualquer um dos métodos descritos em dados de Move de e para o armazenamento da [Blob Azure](move-azure-blob.md).

Aqui, descrevemos como usar o AzCopy para transferir os ficheiros que contêm dados. Para descarregar e instalar o AzCopy, siga as instruções em [Iniciar-se com o utilitário da linha de comando AzCopy](../../storage/common/storage-use-azcopy.md).

1. A partir de uma janela de comando, corra * \<* os seguintes comandos AzCopy, substituindo path_to_data_folder>pelo destino desejado:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

1. Quando a cópia estiver concluída, verá um total de 24 ficheiros com fecho na pasta de dados escolhida. Descarregue os ficheiros descarregados para o mesmo diretório na sua máquina local. Tome nota da pasta onde residem os ficheiros não comprimidos. Esta pasta é referida como o * \<caminho\_\_para unzipped_data\_ficheiros\> * no que se segue.

## <a name="upload-the-data-to-the-default-container-of-the-hdinsight-hadoop-cluster"></a><a name="upload"></a>Faça upload dos dados para o recipiente padrão do cluster Hadoop HDInsight
> [!NOTE]
> Esta é tipicamente uma tarefa de administração.
> 
> 

Nos seguintes comandos AzCopy, substitua os seguintes parâmetros pelos valores reais que especificou ao criar o cluster Hadoop e desapertar os ficheiros de dados.

* *** \<path_to_data_folder>*** O diretório (juntamente com o caminho) na sua máquina que contém os ficheiros de dados desapertados.  
* ***nome da conta de armazenamento do cluster Hadoop>\<*** A conta de armazenamento associada ao seu cluster HDInsight.
* ***recipiente padrão de aglomerado hadoop>\<*** O recipiente predefinido utilizado pelo seu cluster. O nome do recipiente predefinido é geralmente o mesmo nome que o próprio cluster. Por exemplo, se o cluster for chamado "abc123.azurehdinsight.net", o recipiente predefinido é abc123.
* ***>chave da conta de armazenamento \<*** A chave para a conta de armazenamento utilizada pelo seu cluster.

A partir de um pedido de comando ou de uma janela Windows PowerShell, execute os seguintes dois comandos AzCopy.

Este comando envia os dados da viagem para o diretório ***nyctaxitripraw*** no recipiente padrão do cluster Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Este comando envia os dados da tarifa para o diretório ***nyctaxifareraw*** no recipiente padrão do cluster Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Os dados devem agora estar no armazenamento blob, e prontos para serem consumidos dentro do cluster HDInsight.

## <a name="sign-in-to-the-head-node-of-hadoop-cluster-and-prepare-for-exploratory-data-analysis"></a><a name="#download-hql-files"></a>Inscreva-se no nó da cabeça do cluster Hadoop e prepare-se para análise de dados exploratórios
> [!NOTE]
> Esta é tipicamente uma tarefa de administração.
> 
> 

Para aceder ao nó principal do cluster para análise de dados exploratórios e amostragem dos dados, siga o procedimento delineado no [Access o nó de cabeça do Cluster Hadoop](customize-hadoop-cluster.md).

Neste passeio, usamos principalmente consultas escritas na [Hive](https://hive.apache.org/), uma linguagem de consulta semelhante a SQL, para realizar explorações preliminares de dados. As consultas da Hive são armazenadas em ficheiros '.hql'. Em seguida, reduzimos estes dados para serem usados no Machine Learning para modelos de construção.

Para preparar o cluster para análise de dados exploratórios, descarregue os ficheiros '.hql' que contenham os scripts da Colmeia relevantes do [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) para um diretório local (C:\temp) no nó da cabeça. Abra o pedido de comando a partir do nó da cabeça do cluster, e executar os seguintes dois comandos:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Estes dois comandos descarregam todos os ficheiros '.hql' necessários nesta passagem para o diretório local ***C:\temp&#92;*** no nó da cabeça.

## <a name="create-hive-database-and-tables-partitioned-by-month"></a><a name="#hive-db-tables"></a>Criar base de dados da Hive e tabelas divididas por mês
> [!NOTE]
> Esta tarefa é normalmente para um administrador.
> 
> 

Está agora pronto para criar tabelas de Colmeias para o conjunto de dados de táxi de NYC.
No nó da cabeça do aglomerado hadoop, abra a linha de comando Hadoop no ambiente de trabalho do nó de cabeça. Insira o diretório da Colmeia executando o seguinte comando:

    cd %hive_home%\bin

> [!NOTE]
> Executar todos os comandos da Hive neste passeio a partir do caixote da Colmeia/ aviso de diretório. Isto lida automaticamente com quaisquer problemas de caminho. Usamos os termos "Hive diretório prompt", "Hive bin/ diretório prompt", e "Linha de comando Hadoop" alternadamente neste passeio.
> 
> 

A partir do aviso de diretório da Hive, execute o seguinte comando na linha de comando Hadoop do nó de cabeça que cria a base de dados e tabelas da Hive:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Aqui está o conteúdo da **colmeia\_C:\temp\sample\_criar\_db\_e\_tables.hql** file que cria a base de dados da Hive **nyctaxidb**, e as tabelas **trip** e **tarifa**.

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

Este roteiro da Colmeia cria duas tabelas:

* A tabela de **viagem** contém detalhes da viagem de cada passeio (detalhes do condutor, tempo de recolha, distância de viagem e horários).
* A tabela de **tarifas** contém detalhes da tarifa (valor da tarifa, valor da gorjeta, portagens e sobretaxas).

Se precisar de assistência adicional com estes procedimentos, ou se quiser investigar os alternativos, consulte a secção [Enviar consultas diretamente a partir da linha de comando Hadoop](move-hive-tables.md#submit).

## <a name="load-data-to-hive-tables-by-partitions"></a><a name="#load-data"></a>Carregue os dados para as tabelas da Colmeia por divisórias
> [!NOTE]
> Esta tarefa é normalmente para um administrador.
> 
> 

O conjunto de dados de táxi de NYC tem uma divisão natural por mês, que usamos para permitir tempos de processamento e consulta mais rápidos. Os seguintes comandos PowerShell (emitidos a partir do diretório da Hive utilizando a linha de comando Hadoop) carregam dados para as tabelas de viagem e tarifas hive, divididas por mês.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

Os dados **LOAD** **\_\_\_de\_carga\_da colmeia por divisórias.ficheiro hql** contém os seguintes comandos LOAD:

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Algumas das consultas da Colmeia usadas aqui no processo de exploração envolvem olhar apenas uma ou duas divisórias. Mas estas consultas podem ser executadas em todo o conjunto de dados.

### <a name="show-databases-in-the-hdinsight-hadoop-cluster"></a><a name="#show-db"></a>Mostrar bases de dados no cluster Hadoop HDInsight
Para mostrar as bases de dados criadas no cluster Hadoop HDInsight dentro da janela da linha de comando Hadoop, execute o seguinte comando na linha de comando Hadoop:

    hive -e "show databases;"

### <a name="show-the-hive-tables-in-the-nyctaxidb-database"></a><a name="#show-tables"></a>Mostre as mesas da Colmeia na base de dados de **Nyctaxidb**
Para mostrar as tabelas na base de dados de **Nyctaxidb,** execute o seguinte comando na linha de comando Hadoop:

    hive -e "show tables in nyctaxidb;"

Podemos confirmar que as mesas são divididas executando o seguinte comando:

    hive -e "show partitions nyctaxidb.trip;"

Aqui está a saída esperada:

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

Da mesma forma, podemos garantir que a tabela de tarifas é dividida executando o seguinte comando:

    hive -e "show partitions nyctaxidb.fare;"

Aqui está a saída esperada:

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

## <a name="data-exploration-and-feature-engineering-in-hive"></a><a name="#explore-hive"></a>Exploração de dados e engenharia de recursos na Colmeia
> [!NOTE]
> Esta é tipicamente uma tarefa de cientista de dados.
> 
> 

Você pode usar consultas da Hive para realizar tarefas de exploração de dados e de engenharia de recursos para os dados carregados nas tabelas da Colmeia. Aqui estão exemplos de tais tarefas:

* Veja os 10 melhores registos em ambas as mesas.
* Explore a distribuição de dados de alguns campos em diferentes janelas de tempo.
* Investigue a qualidade dos dados dos campos de longitude e latitude.
* Gere etiquetas de classificação binárias e multiclasse com base na quantidade de gorjeta.
* Gere funcionalidades calculando as distâncias de viagem diretas.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Exploração: Veja os 10 melhores registos em viagem de mesa
> [!NOTE]
> Esta é tipicamente uma tarefa de cientista de dados.
> 
> 

Para ver como são os dados, examine 10 registos de cada tabela. Para inspecionar os registos, verifique as duas seguintes consultas separadamente do aviso de diretório da Hive na consola de linha de comando Hadoop.

Para obter os 10 melhores recordes na tabela de viagem do primeiro mês:

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Para obter os 10 melhores recordes na tabela de tarifas do primeiro mês:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

Pode guardar os registos de um ficheiro para visualização conveniente com uma pequena alteração na consulta anterior:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Exploração: Veja o número de registos em cada uma das 12 divisórias
> [!NOTE]
> Esta é tipicamente uma tarefa de cientista de dados.
> 
> 

De interesse é como o número de viagens varia durante o ano civil. Agrupamento por mês mostra a distribuição de viagens.

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

Aqui, a primeira coluna é o mês, e a segunda é o número de viagens para esse mês.

Também podemos contar o número total de registos no nosso conjunto de dados de viagem executando o seguinte comando no conselho de direção da Hive:

    hive -e "select count(*) from nyctaxidb.trip;"

Este comando cede:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Utilizando comandos semelhantes aos mostrados para o conjunto de dados da viagem, podemos emitir consultas da Hive do diretório da Hive para o conjunto de dados de tarifas para validar o número de registos.

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

Pode contar o número total de registos no conjunto de dados da tarifa utilizando o seguinte comando do aviso de diretório da Hive:

    hive -e "select count(*) from nyctaxidb.fare;"

Este comando cede:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

O número total de registos em ambas as tabelas é também o mesmo, proporcionando uma segunda validação de que os dados foram carregados corretamente.

### <a name="exploration-trip-distribution-by-medallion"></a>Exploração: Distribuição de viagem por medalhão
> [!NOTE]
> Esta análise é tipicamente uma tarefa de cientista de dados.
> 
> 

Este exemplo identifica os medalhões (números de táxi) com mais de 100 viagens num determinado período de tempo. A consulta beneficia do acesso à mesa dividido, porque é condicionado pelo **mês**variável de partição. Os resultados da consulta são escritos para um arquivo local, **queryoutput.tsv,** no `C:\temp` nó da cabeça.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Aqui está o conteúdo da **contagem\_\_de viagem\_\_da colmeia por\_medalhão.hql** arquivo para inspeção.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

O medalhão no conjunto de dados de táxi de NYC identifica um táxi único. Você pode identificar quais táxis estão comparativamente ocupados perguntando quais os que fizeram mais do que um certo número de viagens em um determinado período de tempo. O exemplo seguinte identifica táxis que fizeram mais de uma centena de viagens nos primeiros três meses, e poupa os resultados da consulta a um arquivo local, **C:\temp\queryoutput.tsv**.

Aqui está o conteúdo da **contagem\_\_de viagem\_\_da colmeia por\_medalhão.hql** arquivo para inspeção.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

A partir do aviso de diretório da Colmeia, executar o seguinte comando:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Exploração: Distribuição de viagem por medalhão e licença de hack
> [!NOTE]
> Esta tarefa é tipicamente para um cientista de dados.
> 
> 

Ao explorar um conjunto de dados, queremos frequentemente examinar as distribuições de grupos de valores. Esta secção fornece um exemplo de como fazer esta análise para táxis e motoristas.

A **\_amostra\_de\_\_viagem da colmeia conta\_por medalhão\_licença.hql** arquivo sintetiza o conjunto de dados de tarifas em **medalhão** e **hack_license**, e devolve contagens de cada combinação. Aqui estão os seus conteúdos:

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

Esta consulta devolve as combinações de táxi e motorista, encomendadas pelo número descendente de viagens.

A partir do aviso de diretório da Colmeia, corra:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

Os resultados da consulta são escritos para um arquivo local, **C:\temp\questionryoutput.tsv**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Exploração: Avaliação da qualidade dos dados através da verificação de registos de longitude ou latitude inválidos
> [!NOTE]
> Esta é tipicamente uma tarefa de cientista de dados.
> 
> 

Um objetivo comum da análise de dados exploratórios é eliminar registos inválidos ou maus. O exemplo nesta secção determina se os campos de longitude ou latitude contêm um valor muito fora da área de NYC. Uma vez que é provável que esses registos tenham um valor de longitude errónea, queremos eliminá-los de quaisquer dados que sejam utilizados para modelação.

Aqui está o conteúdo da avaliação da **\_qualidade\_\_da colmeia da amostra.ficheiro hql** para inspeção.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


A partir do aviso de diretório da Colmeia, corra:

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

O argumento *-S* incluído neste comando suprime a impressão do ecrã de estado do Mapa da Colmeia/Reduzir os empregos. Este comando é útil porque torna a impressão do ecrã da saída de consulta da Colmeia mais legível.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Exploração: Distribuição de classe binária de dicas de viagem
> [!NOTE]
> Esta é tipicamente uma tarefa de cientista de dados.
> 
> 

Para o problema de classificação binária delineado na secção [Exemplos de tarefas de previsão,](hive-walkthrough.md#mltasks) é útil saber se foi dada ou não uma dica. Esta distribuição de dicas é binária:

* dica dada (Classe\_1, valor da gorjeta > $0)  
* nenhuma dica (Classe\_0, valor da gorjeta = $0)

A seguinte **amostra\_de\_frequências com pontas da colmeia.ficheiro\_hql** mostra o comando a executar:

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

A partir do aviso de diretório da Colmeia, corra:

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Exploração: Distribuição de classes no ambiente multiclasse
> [!NOTE]
> Esta é tipicamente uma tarefa de cientista de dados.
> 
> 

Para o problema de classificação multiclasse delineado na secção [Exemplos de tarefas de previsão,](hive-walkthrough.md#mltasks) este conjunto de dados também se presta a uma classificação natural para prever a quantidade das dicas dadas. Podemos usar caixotes para definir intervalos de gorjeta na consulta. Para obter as distribuições de classe para as várias gamas de dicas, use a **\_amostra de frequências de gama\_\_\_de pontas de colmeia.ficheiro hql.** Aqui está o seu conteúdo.

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

Executar o seguinte comando a partir da consola de linha de comando Hadoop:

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>Exploração: Calcular a distância direta entre dois locais de longitude-latitude
> [!NOTE]
> Esta é tipicamente uma tarefa de cientista de dados.
> 
> 

Talvez queira saber se há uma diferença entre a distância direta entre dois locais e a distância real de viagem do táxi. Um passageiro pode ser menos propenso a dar gorjeta se descobrir que o condutor os levou intencionalmente por uma rota mais longa.

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

Na consulta anterior, R é o raio da Terra em milhas, e pi é convertido em radians. Os pontos de longitude-latitude são filtrados para remover valores que estão longe da área de NYC.

Neste caso, escrevemos os resultados a um diretório chamado **queryoutputdir**. A sequência dos seguintes comandos cria primeiro este diretório de saída, e depois executa o comando da Colmeia.

A partir do aviso de diretório da Colmeia, corra:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


Os resultados da consulta são escritos a nove blobs Azure **(queryoutputdir/000000\_0** para **consultaoutputdir/000008\_0),** sob o recipiente padrão do cluster Hadoop.

Para ver o tamanho das bolhas individuais, execute o seguinte comando do diretório da Colmeia:

    hdfs dfs -ls wasb:///queryoutputdir

Para ver o conteúdo de um determinado ficheiro, digamos **\_0000000 0,** use o comando de `copyToLocal` Hadoop.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [!WARNING]
> `copyToLocal`pode ser muito lento para ficheiros grandes, e não é recomendado para uso com eles.  
> 
> 

Uma vantagem fundamental de ter estes dados a residir numa bolha Azure é que podemos explorar os dados dentro do Machine Learning, utilizando o módulo [de Dados de Importação.][import-data]

## <a name="down-sample-data-and-build-models-in-machine-learning"></a><a name="#downsample"></a>Dados de amostra saque e modelos de construção em Machine Learning
> [!NOTE]
> Esta é tipicamente uma tarefa de cientista de dados.
> 
> 

Após a fase exploratória de análise de dados, estamos agora prontos para recolher os dados para modelos de construção em Machine Learning. Nesta secção, mostramos como usar uma consulta da Hive para recolher os dados. O Machine Learning acede-o então a partir do módulo [de dados de importação.][import-data]

### <a name="down-sampling-the-data"></a>Amostragem dos dados
Há dois passos neste procedimento. Primeiro juntamo-nos às mesas **nyctaxidb.trip** e **nyctaxidb.fare** em três chaves que estão presentes em todos os registos: **medalhão,** **licença de hack\_** e data de **recolha.\_** Em seguida, geramos uma etiqueta de classificação binária, **inclinada,** e uma etiqueta de classificação multiclasse, **classe de ponta\_**.

Para poder utilizar os dados amostrados diretamente a partir do módulo de dados de [importação][import-data] em Machine Learning, deverá armazenar os resultados da consulta anterior a uma tabela interna da Colmeia. No que se segue, criamos uma mesa de Colmeia interna e povoamos o seu conteúdo com os dados amostrados e amostrados.

A consulta aplica funções padrão da Hive diretamente para gerar os seguintes parâmetros de tempo do campo de data de **recolha:\_**
- hora do dia
- semana do ano
- dia da semana ('1' significa segunda-feira, e '7' significa domingo)

A consulta também gera a distância direta entre os locais de recolha e entrega. Para obter uma lista completa dessas funções, consulte O Manual de [Idiomas UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

A consulta então diminui os dados para que os resultados da consulta possam caber no Azure Machine Learning Studio. Apenas cerca de 1% do conjunto de dados original é importado para o estúdio.

Aqui estão os conteúdos da **colmeia\_\_de amostra\_\_preparem-se para\_o ficheiro aml full.hql** que prepara dados para a construção de modelos em Machine Learning:

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

Para executar esta consulta a partir do conselho de direção da Hive:

    hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

Temos agora uma tabela interna, **nyctaxidb.nyctaxi_downsampled_dataset,** que pode ser acedida através do módulo [de Dados][import-data] de Importação da Machine Learning. Além disso, podemos usar este conjunto de dados para a construção de modelos de Machine Learning.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>Utilize o módulo de dados de importação em Machine Learning para aceder aos dados amostrados
Para emitir consultas de Hive no módulo de [dados de importação][import-data] de Machine Learning, você precisa de acesso a um espaço de trabalho de Machine Learning. Também precisa de acesso às credenciais do cluster e da sua conta de armazenamento associada.

Aqui estão alguns detalhes sobre o módulo [de Dados de Importação][import-data] e os parâmetros para a entrada:

**HCatalog servidor URI**: Se o nome do cluster https://abc123.azurehdinsight.netfor **abc123,** então utilize: .

Nome da conta de **utilizador hadoop**: O nome de utilizador escolhido para o cluster (não o nome de utilizador de acesso remoto).

**Palavra-passe**da conta de utilizador hadoop : A palavra-passe escolhida para o cluster (não a palavra-passe de acesso remoto).

**Localização dos dados de saída**: Escolhido para ser Azure.

Nome da **conta de armazenamento azure**: Nome da conta de armazenamento por defeito associada ao cluster.

**Nome**do recipiente azul : O nome do recipiente predefinido para o cluster, e é tipicamente o mesmo que o nome do cluster. Para um aglomerado chamado **abc123,** o nome é abc123.

> [!IMPORTANT]
> Qualquer tabela que desejemos consultar utilizando o módulo [de dados de importação][import-data] em Machine Learning deve ser uma tabela interna.
> 
> 

Aqui está como determinar se uma tabela **T** numa base de dados **D.db** é uma mesa interna. A partir do aviso de diretório da Colmeia, executar o seguinte comando:

    hdfs dfs -ls wasb:///D.db/T

Se a mesa é uma mesa interna e está povoada, o seu conteúdo deve mostrar aqui.

Outra forma de determinar se uma mesa é uma tabela interna é usar o Azure Storage Explorer. Utilize-o para navegar até ao nome do recipiente predefinido do cluster e, em seguida, filtrar pelo nome da mesa. Se a mesa e o seu conteúdo aparecerem, isto confirma que se trata de uma tabela interna.

Aqui está uma imagem da consulta da Colmeia e do módulo de [dados de importação:][import-data]

![Screenshot da consulta da Hive para módulo de dados de importação](./media/hive-walkthrough/1eTYf52.png)

Como os nossos dados amostrados residem no recipiente padrão, a consulta da Hive resultante do Machine Learning é simples. É apenas um **SELECT * From\_nyctaxidb.nyctaxi\_downsampled data**.

O conjunto de dados pode agora ser usado como ponto de partida para a construção de modelos de Machine Learning.

### <a name="build-models-in-machine-learning"></a><a name="mlmodel"></a>Construir modelos em Machine Learning
Agora pode passar à modelação e modelação de implementação em [Machine Learning.](https://studio.azureml.net) Os dados estão prontos para usarmos para resolver os problemas de previsão identificados anteriormente:

- **Classificação binária**: Para prever se uma gorjeta foi ou não paga para uma viagem.

  **Aprendiz utilizado:** Regressão logística de duas classes

  a. Para este problema, a etiqueta alvo (ou classe) é **inclinada**. O conjunto de dados original com amostras tem algumas colunas que são fugas de alvo para esta experiência de classificação. Em particular, **a classe tip,\_** o valor da **\_ponta**e o valor **total\_** revelam informações sobre o rótulo-alvo que não está disponível no momento do teste. Removemos estas colunas da consideração utilizando as Colunas Select no módulo [Dataset.][select-columns]

  O diagrama seguinte mostra a nossa experiência para prever se uma gorjeta foi ou não paga por uma determinada viagem:

  ![Diagrama de experiência para prever se a gorjeta foi paga](./media/hive-walkthrough/QGxRz5A.png)

  b. Para esta experiência, as nossas distribuições de etiquetas-alvo foram aproximadamente 1:1.

   O gráfico seguinte mostra a distribuição de etiquetas de classe de ponta para o problema de classificação binária:

  ![Gráfico de distribuição de etiquetas de classe de gorjeta](./media/hive-walkthrough/9mM4jlD.png)

    Como resultado, obtemos uma área sob a curva (AUC) de 0,987, como mostra o seguinte número:

  ![Gráfico de valor AUC](./media/hive-walkthrough/8JDT0F8.png)

- **Classificação multiclasse**: Para prever o intervalo de valores de gorjeta pagos pela viagem, utilizando as classes previamente definidas.

  **Aprendiz utilizado:** Regressão logística multiclasse

  a. Para este problema, o nosso rótulo alvo (ou classe) é a **classe tip,\_** que pode ter um de cinco valores (0,1,2,3,4). Como no caso de classificação binária, temos algumas colunas que são fugas de alvo para esta experiência. Em particular, **gorjeta**, valor da **\_ponta**, e **quantidade\_total** revelam informações sobre o rótulo-alvo que não está disponível no momento do teste. Removemos estas colunas utilizando as Colunas Select no módulo [Dataset.][select-columns]

  O diagrama seguinte mostra a experiência para prever em que caixote é provável que caia uma ponta. Os caixotes são: Classe 0: dica = $0, Classe 1: dica > $0 e dica <= $5, Classe 2: dica > $5 e dica <= $10, Classe 3: dica > $10 e gorjeta <= $20, e Classe 4: dica > $20.

  ![Diagrama de experiência para prever o caixote para a ponta](./media/hive-walkthrough/5ztv0n0.png)

  Agora mostramos como é a distribuição da classe de teste. Classe 0 e classe 1 são predominantes, e as outras classes são raras.

  ![Gráfico de distribuição de classe de teste](./media/hive-walkthrough/Vy1FUKa.png)

  b. Para esta experiência, usamos uma matriz de confusão para olhar para as precisões de previsão como mostrado aqui:

  ![Matriz de confusão](./media/hive-walkthrough/cxFmErM.png)

  Embora as precisões de classe nas aulas prevalentes sejam boas, o modelo não faz um bom trabalho de "aprendizagem" nas classes mais raras.

- Tarefa de **regressão**: Prever o valor da gorjeta paga por uma viagem.

  **Aprendiz utilizado:** Árvore de decisão reforçada

  a. Para este problema, a etiqueta alvo (ou classe) é a **quantidade de ponta\_**. As fugas de alvo neste caso são: **gorjeta,** **classe de gorjeta,\_** e quantidade **total\_**. Todas estas variáveis revelam informações sobre o valor da ponta que é normalmente indisponível no momento do teste. Removemos estas colunas utilizando as Colunas Select no módulo [Dataset.][select-columns]

  O diagrama seguinte mostra a experiência para prever a quantidade da dada dica:

  ![Diagrama de experiência para prever quantidade de ponta](./media/hive-walkthrough/11TZWgV.png)

  b. Para os problemas de regressão, medimos as exatidão da previsão olhando para o erro quadrado nas previsões, e o coeficiente de determinação:

  ![Screenshot das estatísticas de previsão](./media/hive-walkthrough/Jat9mrz.png)

  Aqui, o coeficiente de determinação é de 0,709, o que implica que cerca de 71% da variação é explicada pelos coeficientes do modelo.

> [!IMPORTANT]
> Para saber mais sobre machine learning e como acessá-lo e usá-lo, consulte [o What's Machine Learning](../studio/what-is-machine-learning.md). Além disso, a [Galeria Azure AI](https://gallery.cortanaintelligence.com/) cobre uma gama de experiências e fornece uma introdução completa na gama de capacidades de Machine Learning.
> 
> 

## <a name="license-information"></a>Informações de licença
Esta amostra de passagem e os seus scripts que acompanham são partilhados pela Microsoft sob a licença do MIT. Para mais informações, consulte o ficheiro **LICENSE.txt** no diretório do código da amostra no GitHub.

## <a name="references"></a>Referências
• Página de descarregamento de viagens de [táxi de Andrés Monroy NYC](https://www.andresmh.com/nyctaxitrips/)  
• [FOILing Dados da Viagem](https://chriswhong.com/open-data/foil_nyc_taxi/) de Táxi de NYC por Chris Whong   
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



