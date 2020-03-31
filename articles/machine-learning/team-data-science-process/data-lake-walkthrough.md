---
title: Ciência de dados escaláveis com Lago de Dados Azure - Processo de Ciência de Dados da Equipa
description: Como utilizar o Lago de Dados Azure para fazer tarefas de exploração de dados e classificação binária num conjunto de dados.
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
ms.openlocfilehash: 9409f14b20684afa1a39d45e663ff316f405cc97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717927"
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Ciência de dados escalável com Lago de Dados Azure: Um Walkthrough de ponta a ponta
Este walkthrough mostra como usar o Lago de Dados Azure para fazer tarefas de exploração de dados e classificação binária numa amostra da viagem de táxi de NYC e dataset de tarifas para prever se uma gorjeta é ou não paga por uma tarifa. Percorre-o através dos passos do Processo de Ciência de Dados da [Equipa,](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)de ponta a ponta, desde a aquisição de dados até à formação de modelos, e depois até à implementação de um serviço web que publique o modelo.

## <a name="technologies"></a>Tecnologias

Estas tecnologias são usadas nesta passagem.
* Azure Data Lake Analytics
* U-SQL e Estúdio Visual
* Python
* Azure Machine Learning
* Scripts


### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
O [Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) tem todas as capacidades necessárias para facilitar aos cientistas de dados a loja de dados de qualquer tamanho, forma e velocidade, e para realizar o processamento de dados, análiseavançada e modelação de machine learning com alta escalabilidade de uma forma rentável.   Paga-se por trabalho, só quando os dados estão a ser processados. O Azure Data Lake Analytics inclui o U-SQL, uma linguagem que mistura a natureza declarativa do SQL com o poder expressivo de C# para fornecer capacidade de consulta distribuída escalável. Permite-lhe processar dados não estruturados aplicando esquemas na leitura, inserindo a lógica personalizada e funções definidas pelo utilizador (UDFs), e inclui a extebilidade para permitir um controlo fino de grãos sobre como executar à escala. Para saber mais sobre a filosofia de design por trás da U-SQL, consulte o [blog do Visual Studio.](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)

Data Lake Analytics é também uma peça-chave da Suite Cortana Analytics e trabalha com azure SQL Data Warehouse, Power BI e Data Factory. Esta combinação dá-lhe um grande dados em nuvem completa e plataforma de análise avançada.

Esta passagem começa por descrever como instalar os pré-requisitos e recursos necessários para completar as tarefas do processo de ciência de dados. Em seguida, descreve os passos de processamento de dados usando u-SQL e conclui mostrando como usar Python e Hive com Azure Machine Learning Studio (clássico) para construir e implementar os modelos preditivos.

### <a name="u-sql-and-visual-studio"></a>U-SQL e Estúdio Visual
Este walkthrough recomenda a utilização do Visual Studio para editar scripts U-SQL para processar o conjunto de dados. Os scripts U-SQL são descritos aqui e fornecidos num ficheiro separado. O processo inclui ingerir, explorar e provar os dados. Também mostra como executar um trabalho escrito U-SQL a partir do portal Azure. As tabelas de colmeia são criadas para os dados de um cluster HDInsight associado para facilitar a construção e implementação de um modelo de classificação binária no Azure Machine Learning Studio.

### <a name="python"></a>Python
Este walkthrough também contém uma secção que mostra como construir e implementar um modelo preditivo usando Python com Azure Machine Learning Studio. Fornece um caderno Jupyter com os scripts Python para os passos neste processo. O caderno inclui código para alguns passos adicionais de engenharia de recursos e construção de modelos, tais como classificação multiclasse e modelação de regressão, além do modelo de classificação binária aqui descrito. A tarefa de regressão é prever a quantidade da ponta com base em outras características da ponta.

### <a name="azure-machine-learning"></a>Azure Machine Learning 
O Azure Machine Learning Studio (clássico) é usado para construir e implementar os modelos preditivos usando duas abordagens: primeiro com scripts Python e depois com mesas de Colmeia num cluster HDInsight (Hadoop).

### <a name="scripts"></a>Scripts
Apenas os passos principais são delineados nesta passagem. Você pode baixar o **script u-SQL** completo e **o Caderno Jupyter** do [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar estes tópicos, deve ter o seguinte:

* Uma subscrição do Azure. Se ainda não tem um, consulte [o Get Azure free trial](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Recomendado] Estúdio Visual 2013 ou mais tarde. Se ainda não tiver uma destas versões instaladas, pode descarregar uma versão comunitária gratuita da [Visual Studio Community](https://www.visualstudio.com/vs/community/).

> [!NOTE]
> Em vez do Visual Studio, também pode usar o portal Azure para submeter consultas do Lago de Dados Azure. São fornecidas instruções sobre como fazê-lo tanto com o Visual Studio como no portal da secção intitulada Dados de **Processo com U-SQL**.
>
>


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Prepare o ambiente da ciência dos dados para o Lago de Dados De Azure
Para preparar o ambiente da ciência dos dados para esta passagem, crie os seguintes recursos:

* Armazenamento de lagos de dados azure (ADLS)
* Azure Data Lake Analytics (ADLA)
* Conta de armazenamento Azure Blob
* Conta Azure Machine Learning Studio (clássica)
* Ferramentas azure data lake para estúdio visual (recomendado)

Esta secção fornece instruções sobre como criar cada um destes recursos. Se optar por utilizar mesas de Colmeia com Machine Learning Azure, em vez de Python, para construir um modelo, também precisa de fornecer um cluster HDInsight (Hadoop). Este procedimento alternativo descrito na secção Opção 2.


> [!NOTE]
> A **Azure Data Lake Store** pode ser criada separadamente ou quando cria o **Azure Data Lake Analytics** como o armazenamento padrão. As instruções são referenciadas para a criação separada de cada um destes recursos, mas a conta de armazenamento do Lago de Dados não precisa de ser criada separadamente.
>
>

### <a name="create-an-azure-data-lake-storage"></a>Criar um armazenamento de lago de dados azure


Crie um ADLS a partir do [portal Azure.](https://portal.azure.com) Para mais detalhes, consulte [Criar um cluster HDInsight com data lake store utilizando](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)o portal Azure . Certifique-se de que configura a identidade AAD do cluster na lâmina **DataSource** da lâmina de **configuração opcional** descrita.

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Criar uma conta Azure Data Lake Analytics
Crie uma conta ADLA a partir do [portal Azure.](https://portal.azure.com) Para mais detalhes, consulte [Tutorial: inicie-se com o Azure Data Lake Analytics utilizando](../../data-lake-analytics/data-lake-analytics-get-started-portal.md)o portal Azure .

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Criar uma conta de armazenamento Azure Blob
Crie uma conta de armazenamento Azure Blob a partir do [portal Azure.](https://portal.azure.com) Para mais detalhes, consulte a secção criar uma conta de armazenamento nas [contas de Armazenamento Azure](../../storage/common/storage-create-storage-account.md).

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-classic-account"></a>Criar uma conta Azure Machine Learning Studio (clássica)
Inscreva-se/inte no Azure Machine Learning Studio (clássico) a partir da página de [estúdio azure machine learning.](https://azure.microsoft.com/services/machine-learning/) Clique no botão **Get agora iniciado** e, em seguida, escolha um "Espaço de Trabalho Gratuito" ou "Espaço de Trabalho Padrão". Agora está pronto para criar experiências no estúdio Azure Machine Learning.

### <a name="install-azure-data-lake-tools-recommended"></a>Instale ferramentas de lago de dados azure [recomendado]
Instale ferramentas azure data lake para a sua versão do Estúdio Visual de Ferramentas do [Lago de Dados Azure para estúdio visual](https://www.microsoft.com/download/details.aspx?id=49504).

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

Depois de terminar a instalação, abra o Estúdio Visual. Você deve ver o data lake aba o menu no topo. Os seus recursos Azure devem aparecer no painel esquerdo quando assinar na sua conta Azure.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>O conjunto de dados de viagens de táxi de NYC
O conjunto de dados utilizado aqui é um conjunto de dados disponível publicamente - o conjunto de [dados de Viagens de Táxi de NYC](https://www.andresmh.com/nyctaxitrips/). Os dados da NyC Taxi Trip consistem em cerca de 20 GB de ficheiros CSV comprimidos (~48 GB descomprimidos), registando mais de 173 milhões de viagens individuais e as tarifas pagas por cada viagem. Cada registo de viagem inclui os locais e horários de recolha e entrega, o número de carta de condução anoonizada (motorista) e o número de medalhão (id único do táxi). Os dados abrangem todas as viagens do ano de 2013 e são fornecidos nos dois conjuntos de dados seguintes para cada mês:

O CSV 'trip_data' contém detalhes da viagem, tais como o número de passageiros, pontos de recolha e entrega, duração da viagem e duração da viagem. Aqui estão alguns registos de amostras:

       medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868



O CSV 'trip_fare' contém detalhes da tarifa paga por cada viagem, tais como tipo de pagamento, valor das tarifas, sobretaxa e impostos, gorjetas e portagens, e o valor total pago. Aqui estão alguns registos de amostras:

       medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A chave única\_para juntar\_dados de viagem e tarifa de\_viagem\_é composta pelos seguintes três campos: medalhão, licença de hack e data de recolha. Os ficheiros CSV crus podem ser acedidos a partir de uma bolha de armazenamento Azure. O script U-SQL para esta adesão está na secção [de mesas de viagem e tarifas.](#join)

## <a name="process-data-with-u-sql"></a>Processar dados com U-SQL
As tarefas de processamento de dados ilustradas nesta secção incluem ingerir, verificar a qualidade, explorar e provar os dados. Como aderir às mesas de viagem e tarifas também é mostrado. A secção final mostra executar um trabalho escrito U-SQL do portal Azure. Aqui estão os links para cada subsecção:

* [Ingestão de dados: leia em dados da bolha pública](#ingest)
* [Verificação da qualidade dos dados](#quality)
* [Exploração de dados](#explore)
* [Junte-se às mesas de viagem e tarifas](#join)
* [Amostragem de dados](#sample)
* [Executar empregos U-SQL](#run)

Os scripts U-SQL são descritos aqui e fornecidos num ficheiro separado. Você pode baixar os **scripts U-SQL completos** do [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Para executar U-SQL, Open Visual Studio, clique em **File --> New --> Project**, escolha **U-SQL Project**, nomeie-o e guarde-o para uma pasta.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> É possível utilizar o Portal Azure para executar u-SQL em vez de Visual Studio. Pode navegar para o recurso Azure Data Lake Analytics no portal e submeter consultas diretamente, conforme ilustrado na seguinte figura:
>
>

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="data-ingestion-read-in-data-from-public-blob"></a><a name="ingest"></a>Ingestão de Dados: Ler em dados da bolha pública

A localização dos dados na bolha Azure é referenciada como **wasb://container\_conta de armazenamento\@\_\_blob\_de nome name.blob.core.windows.net/blob_name** e pode ser extraída através do **Extractors.Csv()**. Substitua o nome do seu próprio contentor\_e\@o\_\_nome da conta de armazenamento nas seguintes scripts para o nome da conta\_de armazenamento blob do nome do recipiente no endereço wasb. Uma vez que os nomes dos ficheiros estão no mesmo formato, é possível utilizar **dados\_\_\{\*\}de viagem .csv** para ler em todos os 12 ficheiros de viagem.

    ///Read in Trip data
    @trip0 =
        EXTRACT
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string
    // This is reading 12 trip data from blob
    FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
    USING Extractors.Csv();

Uma vez que existem cabeçalhos na primeira linha, é necessário remover os cabeçalhos e mudar os tipos de colunas em tipos apropriados. Pode guardar os dados processados para o Armazenamento do Lago De dados do Azure utilizando **swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ ou para a conta de armazenamento Azure Blob utilizando **wasb://container_name\@blob_storage_account_name.blob.core.windows.windows.net/blob_name**.

    // change data types
    @trip =
        SELECT
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        DateTime.Parse(pickup_datetime) AS pickup_datetime,
        DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
        Int32.Parse(passenger_count) AS passenger_count,
        Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
        Double.Parse(trip_distance) AS trip_distance,
        (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
        (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
        (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
        (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
    FROM @trip0
    WHERE medallion != "medallion";

    ////output data to ADL
    OUTPUT @trip
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_trip.csv"
    USING Outputters.Csv();

    ////Output data to blob
    OUTPUT @trip
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
    USING Outputters.Csv();

Da mesma forma, pode ler nos conjuntos de dados de tarifas. Clique no Armazenamento do Lago De Dados do Azure, pode optar por ver os seus dados no **portal Azure --> Data Explorer** ou File **Explorer** dentro do Visual Studio.

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="data-quality-checks"></a><a name="quality"></a>Verificação da qualidade dos dados
Após a leitura das tabelas de viagem e tarifa, os controlos de qualidade dos dados podem ser feitos da seguinte forma. Os ficheiros CSV resultantes podem ser de saída para armazenamento Azure Blob ou Armazenamento de Lago de Dados Azure.

Encontre o número de medalhões e o número único de medalhão:

    ///check the number of medallions and unique number of medallions
    @trip2 =
        SELECT
        medallion,
        vendor_id,
        pickup_datetime.Month AS pickup_month
        FROM @trip;

    @ex_1 =
        SELECT
        pickup_month,
        COUNT(medallion) AS cnt_medallion,
        COUNT(DISTINCT(medallion)) AS unique_medallion
        FROM @trip2
        GROUP BY pickup_month;
        OUTPUT @ex_1
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
    USING Outputters.Csv();

Encontre os medalhão que tiveram mais de 100 viagens:

    ///find those medallions that had more than 100 trips
    @ex_2 =
        SELECT medallion,
               COUNT(medallion) AS cnt_medallion
        FROM @trip2
        //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
        GROUP BY medallion
        HAVING COUNT(medallion) > 100;
        OUTPUT @ex_2
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
    USING Outputters.Csv();

Encontre esses registos inválidos em termos de pickup_longitude:

    ///find those invalid records in terms of pickup_longitude
    @ex_3 =
        SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
        FROM @trip
        WHERE
        pickup_longitude <- 90 OR pickup_longitude > 90;
        OUTPUT @ex_3
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
    USING Outputters.Csv();

Encontre valores em falta para algumas variáveis:

    //check missing values
    @res =
        SELECT *,
               (medallion == null? 1 : 0) AS missing_medallion
        FROM @trip;

    @trip_summary6 =
        SELECT
            vendor_id,
        SUM(missing_medallion) AS medallion_empty,
        COUNT(medallion) AS medallion_total,
        COUNT(DISTINCT(medallion)) AS medallion_total_unique
        FROM @res
        GROUP BY vendor_id;
    OUTPUT @trip_summary6
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
    USING Outputters.Csv();



### <a name="data-exploration"></a><a name="explore"></a>Exploração de dados
Faça alguma exploração de dados com os seguintes scripts para obter uma melhor compreensão dos dados.

Encontre a distribuição de viagens com gorjeta e sem gorjeta:

    ///tipped vs. not tipped distribution
    @tip_or_not =
        SELECT *,
               (tip_amount > 0 ? 1: 0) AS tipped
        FROM @fare;

    @ex_4 =
        SELECT tipped,
               COUNT(*) AS tip_freq
        FROM @tip_or_not
        GROUP BY tipped;
        OUTPUT @ex_4
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
    USING Outputters.Csv();

Encontre a distribuição do valor da gorjeta com valores de corte: 0, 5, 10 e 20 dólares.

    //tip class/range distribution
    @tip_class =
        SELECT *,
               (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
        FROM @fare;
    @ex_5 =
        SELECT tip_class,
               COUNT(*) AS tip_freq
        FROM @tip_class
        GROUP BY tip_class;
        OUTPUT @ex_5
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
    USING Outputters.Csv();

Encontre estatísticas básicas da distância da viagem:

    // find basic statistics for trip_distance
    @trip_summary4 =
        SELECT
            vendor_id,
            COUNT(*) AS cnt_row,
            MIN(trip_distance) AS min_trip_distance,
            MAX(trip_distance) AS max_trip_distance,
            AVG(trip_distance) AS avg_trip_distance
        FROM @trip
        GROUP BY vendor_id;
    OUTPUT @trip_summary4
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
    USING Outputters.Csv();

Encontre os percentículos da distância da viagem:

    // find percentiles of trip_distance
    @trip_summary3 =
        SELECT DISTINCT vendor_id AS vendor,
                        PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
        FROM @trip;
       // group by vendor_id;
    OUTPUT @trip_summary3
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
    USING Outputters.Csv();


### <a name="join-trip-and-fare-tables"></a><a name="join"></a>Junte-se às mesas de viagem e tarifas
As mesas de viagem e tarifa podem ser acompanhadas por medalhão, hack_license e pickup_time.

    //join trip and fare table

    @model_data_full =
    SELECT t.*,
    f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
    (f.tip_amount > 0 ? 1: 0) AS tipped,
    (f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @trip AS t JOIN  @fare AS f
    ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
    WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

    //// output to blob
    OUTPUT @model_data_full
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
    USING Outputters.Csv();

    ////output data to ADL
    OUTPUT @model_data_full
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_7_full_data.csv"
    USING Outputters.Csv();


Para cada nível de contagem de passageiros, calcule o número de registos, o valor médio da gorjeta, a variação do valor da gorjeta, a percentagem de viagens com gorjeta.

    // contingency table
    @trip_summary8 =
        SELECT passenger_count,
               COUNT(*) AS cnt,
               AVG(tip_amount) AS avg_tip_amount,
               VAR(tip_amount) AS var_tip_amount,
               SUM(tipped) AS cnt_tipped,
               (float)SUM(tipped)/COUNT(*) AS pct_tipped
        FROM @model_data_full
        GROUP BY passenger_count;
        OUTPUT @trip_summary8
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
    USING Outputters.Csv();


### <a name="data-sampling"></a><a name="sample"></a>Amostragem de dados
Primeiro, selecione aleatoriamente 0,1% dos dados da tabela aqueceda:

    //random select 1/1000 data for modeling purpose
    @addrownumberres_randomsample =
    SELECT *,
            ROW_NUMBER() OVER() AS rownum
    FROM @model_data_full;

    @model_data_random_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_randomsample
    WHERE rownum % 1000 == 0;

    OUTPUT @model_data_random_sample_1_1000
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
    USING Outputters.Csv();

Em seguida, faça uma amostragem estratificada por variável binária tip_class:

    //stratified random select 1/1000 data for modeling purpose
    @addrownumberres_stratifiedsample =
    SELECT *,
            ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
    FROM @model_data_full;

    @model_data_stratified_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_stratifiedsample
    WHERE rownum % 1000 == 0;
    //// output to blob
    OUTPUT @model_data_stratified_sample_1_1000
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv();
    ////output data to ADL
    OUTPUT @model_data_stratified_sample_1_1000
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv();


### <a name="run-u-sql-jobs"></a><a name="run"></a>Executar empregos U-SQL
Depois de editar scripts U-SQL, pode submetê-los ao servidor utilizando a sua conta Azure Data Lake Analytics. Clique em **Data Lake,** **Submeta O Trabalho,** selecione a sua **Conta Analytics,** escolha **paralelismo**e clique em **Enviar.**

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

Quando o trabalho é cumprido com sucesso, o estado do seu trabalho é apresentado no Estúdio Visual para monitorização. Após o trabalho estar concluído, pode até reproduzir o processo de execução do emprego e descobrir os passos de estrangulamento para melhorar a eficiência do seu trabalho. Também pode ir ao portal Azure para verificar o estado dos seus empregos na U-SQL.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

Agora pode verificar os ficheiros de saída no armazenamento do Azure Blob ou no portal Azure. Utilize os dados da amostra estratificados para a nossa modelação no próximo passo.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Construir e implementar modelos em Azure Machine Learning
Estão disponíveis duas opções para que você possa puxar dados para Azure Machine Learning para construir e

* Na primeira opção, utiliza os dados amostrados que foram escritos a um Azure Blob (na **amostra de dados** acima) e utiliza a Python para construir e implementar modelos a partir do Azure Machine Learning.
* Na segunda opção, consulta os dados no Lago de Dados Azure diretamente usando uma consulta da Hive. Esta opção requer que crie um novo cluster HDInsight ou utilize um cluster HDInsight existente onde as tabelas da Hive apontam para os dados do NY Taxi no Armazenamento do Lago De dados Azure.  Ambas as opções são discutidas nas seguintes secções.

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>Opção 1: Utilizar python para construir e implementar modelos de aprendizagem automática
Para construir e implementar modelos de machine learning utilizando python, crie um Caderno Jupyter na sua máquina local ou no Azure Machine Learning Studio. O Caderno Jupyter fornecido no [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) contém o código completo para explorar, visualizar dados, engenharia de funcionalidades, modelação e implementação. Neste artigo, apenas a modelação e implantação são cobertas.

### <a name="import-python-libraries"></a>Importabibliotecas Python
Para executar a amostra Jupyter Notebook ou o ficheiro de script Python, são necessários os seguintes pacotes Python. Se estiver a utilizar o serviço Deportátil de Aprendizagem automática Azure, estes pacotes foram pré-instalados.

    import pandas as pd
    from pandas import Series, DataFrame
    import numpy as np
    import matplotlib.pyplot as plt
    from time import time
    import pyodbc
    import os
    from azure.storage.blob import BlobService
    import tables
    import time
    import zipfile
    import random
    import sklearn
    from sklearn.linear_model import LogisticRegression
    from sklearn.cross_validation import train_test_split
    from sklearn import metrics
    from __future__ import division
    from sklearn import linear_model
    from azureml import services


### <a name="read-in-the-data-from-blob"></a>Ler nos dados da blob
* Fio de ligação

        CONTAINERNAME = 'test1'
        STORAGEACCOUNTNAME = 'XXXXXXXXX'
        STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
        BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
        blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
* Ler como texto

        t1 = time.time()
        data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
        t2 = time.time()
        print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))

  ![17](./media/data-lake-walkthrough/17-python_readin_csv.PNG)
* Adicione nomes de colunas e colunas separadas

        colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
        'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
        df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
* Mude algumas colunas para numérica

        cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
        ,'tipped','tip_class','rownum']
        for col in cols_2_float:
            df1[col] = df1[col].astype(float)

### <a name="build-machine-learning-models"></a>Construir modelos de aprendizagem automática
Aqui você constrói um modelo de classificação binário para prever se uma viagem é ou não inclinada. No Caderno Jupyter pode encontrar outros dois modelos: classificação multiclasse e modelos de regressão.

* Primeiro você precisa criar variáveis manequim que podem ser usadas em modelos de scikit-learn

        df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
        df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
* Criar quadro de dados para a modelação

        cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
        data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])

        X = data.iloc[:,1:]
        Y = data.tipped
* Treino e teste 60-40 split

        X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
* Regressão logística no conjunto de treino

        model = LogisticRegression()
        logit_fit = model.fit(X_train, Y_train)
        print ('Coefficients: \n', logit_fit.coef_)
        Y_train_pred = logit_fit.predict(X_train)

       ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)
* Conjunto de dados de teste de pontuação

        Y_test_pred = logit_fit.predict(X_test)
* Calcular métricas de avaliação

        fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
        print fpr_train, tpr_train, thresholds_train

        fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred)
        print fpr_test, tpr_test, thresholds_test

        #AUC
        print metrics.auc(fpr_train,tpr_train)
        print metrics.auc(fpr_test,tpr_test)

        #Confusion Matrix
        print metrics.confusion_matrix(Y_train,Y_train_pred)
        print metrics.confusion_matrix(Y_test,Y_test_pred)

       ![c2](./media/data-lake-walkthrough/c2-py-logit-evaluation.PNG)

### <a name="build-web-service-api-and-consume-it-in-python"></a>Construa a API do Serviço Web e consuma-a em Python
Quer operacionalizar o modelo de aprendizagem automática depois de ter sido construído. O modelo logístico binário é usado aqui como um exemplo. Certifique-se de que a versão scikit-learn na sua máquina local é 0.15.1 (O Azure Machine Learning Studio já está pelo menos nesta versão).

* Encontre as suas credenciais de espaço de trabalho a partir das definições do Azure Machine Learning Studio (clássico). No Estúdio de Aprendizagem automática Azure, clique em**Fichas**de Autorização de**Nome** -->  **de Definições** --> .

    ![c3](./media/data-lake-walkthrough/c3-workspace-id.PNG)

        workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
        auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

* Criar o Serviço Web

        @services.publish(workspaceid, auth_token)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(int) #0, or 1
        def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
            return logit_fit.predict(inputArray)
* Obtenha credenciais de serviço web

        url = predictNYCTAXI.service.url
        api_key =  predictNYCTAXI.service.api_key

        print url
        print api_key

        @services.service(url, api_key)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(float)
        def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            pass
* Ligue para o serviço Web API. Normalmente, espere 5-10 segundos após o passo anterior.

        NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)

       ![c4](./media/data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>Opção 2: Criar e implementar modelos diretamente no Azure Machine Learning
O Azure Machine Learning Studio (clássico) pode ler dados diretamente do Armazenamento do Lago De Dados Azure e depois ser usado para criar e implementar modelos. Esta abordagem utiliza uma tabela de colmeias que aponta para o Armazenamento do Lago De Dados Azure. Um cluster Azure HDInsight separado precisa de ser provisionado para a tabela Hive. 

### <a name="create-an-hdinsight-linux-cluster"></a>Criar um Cluster Linux HDInsight
Crie um Cluster HDInsight (Linux) a partir do [portal Azure](https://portal.azure.com). Para mais detalhes, consulte o **cluster Create a HDInsight com acesso à** secção de armazenamento de lagos de dados Azure em Criar um cluster [HDInsight com data lake store usando](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)o portal Azure .

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Criar mesa de colmeia no HDInsight
Agora cria-se tabelas Hive para serem usadas no Azure Machine Learning Studio (clássico) no cluster HDInsight utilizando os dados armazenados no Armazenamento do Lago De Dados Azure no passo anterior. Vá ao cluster HDInsight criado. Clique em **Configurações** --> **Propriedades** --> **Cluster ADLS Acesso** --> a Identidade**ADLS,** certifique-se de que a sua conta de Armazenamento de Lagos de Dados Azure é adicionada na lista com direitos de leitura, escrita e execução.

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Em seguida, clique no **Dashboard** ao lado do botão **Definições** e surge uma janela. Clique na **Vista Hive** no canto superior direito da página e deverá consultar o Editor de **Consulta**.

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Colar nos seguintes scripts da Colmeia para criar uma mesa. A localização da fonte de dados está na referência de Armazenamento de Lagos De Dados De Azure desta forma: **adl://data_lake_store_name.azuredatalakestore.net:443/folder_name/file_name**.

    CREATE EXTERNAL TABLE nyc_stratified_sample
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string,
      payment_type string,
      fare_amount string,
      surcharge string,
      mta_tax string,
      tolls_amount string,
      total_amount string,
      tip_amount string,
      tipped string,
      tip_class string,
      rownum string
      )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    LOCATION 'adl://data_lake_storage_name.azuredatalakestore.net:443/nyctaxi_folder/demo_ex_9_stratified_1_1000_copy.csv';


Quando a consulta estiver concluída, deve ver os resultados assim:

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Construir e implementar modelos no Azure Machine Learning Studio
Está agora pronto para construir e implementar um modelo que prevê se uma gorjeta é ou não paga com o Azure Machine Learning. Os dados da amostra estratificados estão prontos para serem utilizados neste problema de classificação binária (ponta ou não). Os modelos preditivos que utilizam a classificação multiclasse (tip_class) e a regressão (tip_amount) também podem ser construídos e implantados com o Azure Machine Learning Studio, mas aqui só é mostrado como lidar com o caso usando o modelo de classificação binária.

1. Obtenha os dados no Azure Machine Learning Studio (clássico) utilizando o módulo **de Dados de Importação,** disponível na secção de Entrada e Saída de **Dados.** Para mais informações, consulte a página de referência do [módulo de dados de importação.](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/)
2. Selecione **Hive Query** como fonte de **dados** no painel **Propriedades.**
3. Colar o seguinte script da Hive no editor de consulta da base de **dados da Hive**

        select * from nyc_stratified_sample;
4. Introduza o uri do cluster HDInsight (este URI pode ser encontrado no portal Azure), credenciais hadoop, localização de dados de saída e nome/nome da conta de armazenamento Azure/nome chave/contentor.

   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)

Um exemplo de uma experiência de classificação binária que lê dados da tabela Hive é mostrado na figura seguinte:

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

Depois da experiência ser criada, clique em **Configurar o** --> **Serviço Web Predictive Do Serviço** Web

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

Executar a experiência de pontuação criada automaticamente, quando terminar, clique em **Implementar O Serviço Web**

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

O painel de instrumentos do serviço web apresenta em breve:

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Resumo
Ao completar este walkthrough, criou um ambiente de ciência de dados para a construção de soluções escaláveis de ponta a ponta no Lago de Dados Azure. Este ambiente foi utilizado para analisar um grande conjunto de dados públicos, levando-o através dos passos canónicos do Processo de Ciência de Dados, desde a aquisição de dados até à formação de modelos, e depois à implementação do modelo como serviço web. A U-SQL foi usada para processar, explorar e provar os dados. Python e Hive foram usados com o Azure Machine Learning Studio (clássico) para construir e implementar modelos preditivos.

## <a name="whats-next"></a>Passos seguintes?
O caminho de aprendizagem para o Processo de Ciência de [Dados da Equipa (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) fornece links para tópicos que descrevem cada passo no processo de análise avançada. Há uma série de walkthroughs itemd in the [Team Data Science Process walkthroughs](walkthroughs.md) page que mostram como usar recursos e serviços em vários cenários de análise preditiva:

* [O Processo de Ciência de Dados da Equipa em ação: usando o Armazém de Dados SQL](sqldw-walkthrough.md)
* [O Processo de Ciência de Dados da equipa em ação: usando clusters hadoop HDInsight](hive-walkthrough.md)
* [O Processo de Ciência de Dados da Equipa: usando o Servidor SQL](sql-walkthrough.md)
* [Visão geral do processo de ciência de dados usando Spark on Azure HDInsight](spark-overview.md)
