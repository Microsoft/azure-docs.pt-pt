---
title: Ciência de dados dimensionável com o Azure Data Lake - processo de ciência de dados de equipa
description: Como utilizar o Azure Data Lake para realizar tarefas de classificação de exploração e o binário dados num conjunto de dados.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: cc37109eda2690b4407f9cd0c92851b7c0e3f915
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57835247"
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Ciência de dados dimensionável com o Azure Data Lake: Uma passo a passo-a-ponto
Estas instruções mostram como utilizar o Azure Data Lake para fazer a exploração de dados e tarefas de classificação binária de uma amostra da viagem de táxis de NYC e se comportarão de conjunto de dados para prever se ou não uma dica é pago por uma Europeia. Ele explica-lhe os passos para o [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)ponto-a- ponto, de aquisição de dados para modelar o treinamento e, em seguida, para a implementação de um serviço web que publica o modelo.

### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
O [Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) com todas as capacidades necessárias para tornar mais fácil para os cientistas de dados para armazenar dados de qualquer tamanho, forma e velocidade e a conduzir o processamento de dados, análises avançadas e aprendizagem de modelagem com alta escalabilidade de uma forma rentável.   Paga-se numa base por tarefa, apenas quando os dados, na verdade, está a ser processados. O Azure Data Lake Analytics inclui U-SQL, uma linguagem que alia a natureza declarativa do SQL ao poder expressivo do c# para fornecer escalável distribuído de capacidade de consulta. Permite-lhe processar dados não estruturados ao aplicar o esquema na leitura, inserir lógica personalizada e funções definidas pelo utilizador (UDFs) e inclui extensibilidade para permitir o controlo detalhado sobre como executar à escala. Para saber mais sobre a filosofia de design por trás do U-SQL, veja [mensagem de blogue do Visual Studio](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

O Data Lake Analytics também é uma parte essencial do Cortana Analytics Suite e funciona com o Azure SQL Data Warehouse, o Power BI e o Data Factory. Isto dá-lhe uma plataforma de análise avançada e macrodados na cloud completa.

Este passo a passo começa com a descrição de como instalar os pré-requisitos e os recursos que são necessárias para concluir tarefas do processo de ciência de dados. Em seguida, ele descreve os passos de processamento de dados com o U-SQL e é concluído com que mostram como utilizar Python e o Hive com o Azure Machine Learning Studio para criar e implementar modelos de previsão.

### <a name="u-sql-and-visual-studio"></a>U-SQL e o Visual Studio
Este passo a passo recomenda com o Visual Studio para editar scripts U-SQL para processar o conjunto de dados. Os scripts U-SQL são descritos aqui e fornecidos num arquivo separado. O processo inclui ingestão, explorar e os dados de amostragem. Ela também mostra como executar uma tarefa de script de U-SQL a partir do portal do Azure. Tabelas do Hive são criadas para os dados num cluster do HDInsight associado para facilitar a criação e implementação de um modelo de classificação binária no Azure Machine Learning Studio.

### <a name="python"></a>Python
Estas instruções também contém uma seção que mostra como criar e implementar um modelo preditivo com o Python com o Azure Machine Learning Studio. Ele fornece um bloco de notas do Jupyter com os scripts de Python para obter os passos neste processo. O bloco de notas inclui código para algumas funcionalidades adicionais engenharia passos e modelos de construção, como a classificação de várias classes e regressão de modelagem, além do modelo de classificação binária descrito aqui. É a tarefa de regressão prever a quantidade de sugestão com base em outros recursos de dica.

### <a name="azure-machine-learning"></a>Azure Machine Learning
O Azure Machine Learning Studio é utilizado para criar e implementar modelos de previsão. Isso é feito com duas abordagens: primeiro com scripts do Python e, em seguida, com tabelas do Hive num cluster do HDInsight (Hadoop).

### <a name="scripts"></a>Scripts
Apenas os principais passos descritos nestas instruções. Pode baixar o completo **script U-SQL** e **bloco de notas do Jupyter** partir [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

## <a name="prerequisites"></a>Pré-requisitos
Antes de iniciar estes tópicos, tem de ter o seguinte:

* Uma subscrição do Azure. Se ainda não tiver uma, veja [avaliação gratuita do Azure obter](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Recomendável] Visual Studio 2013 ou posterior. Se ainda não tiver um destas versões instaladas, pode transferir uma versão de Comunidade gratuita partir [Visual Studio Community](https://www.visualstudio.com/vs/community/).

> [!NOTE]
> Em vez do Visual Studio, também pode utilizar o portal do Azure para submeter consultas do Azure Data Lake. São fornecidas instruções sobre como fazer então, ambos com o Visual Studio e no portal da seção intitulada **processar dados com o U-SQL**.
>
>


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Preparar o ambiente de ciência de dados para o Azure Data Lake
Para preparar o ambiente de ciência de dados para este passo a passo, crie os seguintes recursos:

* Azure Data Lake Store (ADLS)
* Azure Data Lake Analytics (ADLA)
* Conta de armazenamento de Blobs do Azure
* Conta do Azure Machine Learning Studio
* O Azure Data Lake Tools para Visual Studio (recomendado)

Esta secção fornece instruções sobre como criar cada um destes recursos. Se optar por utilizar tabelas do Hive com o Azure Machine Learning, em vez de Python, para criar um modelo, também terá de aprovisionar um cluster do HDInsight (Hadoop). Este procedimento alternativo no descrito na secção opção 2.


> [!NOTE]
> O **do Azure Data Lake Store** podem ser criados a separadamente ou ao criar o **Azure Data Lake Analytics** como armazenamento predefinido. As instruções são referenciadas para a criação de cada um destes recursos em separado, mas a conta de armazenamento do Data Lake não tem de ser criada em separado.
>
>

### <a name="create-an-azure-data-lake-store"></a>Criar um Store do Azure Data Lake


Criar um ADLS a partir da [portal do Azure](https://portal.azure.com). Para obter detalhes, consulte [criar um cluster do HDInsight com Data Lake Store através do portal do Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de que configurar a identidade do AAD de Cluster no **DataSource** painel da **configuração opcional** painel descrito aqui.

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Criar uma conta do Azure Data Lake Analytics
Criar uma conta ADLA a partir da [portal do Azure](https://portal.azure.com). Para obter detalhes, consulte [Tutorial: introdução ao Azure Data Lake Analytics com o portal do Azure](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Criar uma conta de armazenamento de Blobs do Azure
Criar uma conta de armazenamento de Blobs do Azure da [portal do Azure](https://portal.azure.com). Para obter detalhes, veja o tópico Criar uma seção de conta de armazenamento no [sobre as contas de armazenamento](../../storage/common/storage-create-storage-account.md).

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-account"></a>Configurar uma conta do Azure Machine Learning Studio
Inicie sessão para cima ou para o Azure Machine Learning Studio a partir da [Azure Machine Learning studio](https://azure.microsoft.com/services/machine-learning/) página. Clique nas **comece agora mesmo** botão e, em seguida, escolha uma "Área de trabalho gratuita" ou "Área de trabalho Standard". Agora, está a preparar para criar experimentações no Azure Machine Learning studio.

### <a name="install-azure-data-lake-tools-recommended"></a>Instalar as ferramentas do Azure Data Lake [recomendável]
Instalar o Azure Data Lake Tools para a sua versão do Visual Studio a partir [do Azure Data Lake Tools para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

Após a instalação for concluída com êxito, abra o Visual Studio. Deverá ver o separador no menu na parte superior do Data Lake. Os recursos do Azure devem aparecer no painel esquerdo, ao iniciar sessão na conta do Azure.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>O conjunto de dados de viagens de táxis de NYC
O conjunto de dados utilizado aqui é um conjunto de dados publicamente disponível – a [conjunto de dados de viagens de táxis de NYC](https://www.andresmh.com/nyctaxitrips/). Os dados da viagem de táxis de NYC consiste em cerca de 20 GB de arquivos compactados de CSV (GB de ~ 48 descomprimido), gravar mais de 173 milhões de viagens individuais e os fares pago para cada viagem. Cada registo de viagem inclui as localizações de recolha e de redução e vezes, o número de licença de hackers anónimos (driver) e o número de medallion (de táxis de ID exclusivo). Os dados abrange todas as viagens no ano de 2013 e são fornecidos no seguintes dois conjuntos de dados para todos os meses:

O 'trip_data CSV contém detalhes de viagem, como o número de passageiros, recolha e pontos de redução, duração de viagem e comprimento de viagem. Aqui estão alguns exemplos de registros:

       medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868



O trip_fare CSV contém detalhes de Europeia pago para cada viagem, como o tipo de pagamento, a quantidade de Europeia, sobretaxa e impostos, dicas e pedágio e o valor total pago. Aqui estão alguns exemplos de registros:

       medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
       89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
       0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
       DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A chave exclusiva para aderir a viagem\_dados e viagem\_Europeia é composta por três campos seguintes: medallion, aceder de modo ilícito\_licença e recolha\_datetime. Os ficheiros CSV não processados podem ser acedidos a partir de um blob de armazenamento do Azure público. O script de U-SQL para esta associação é no [associar as tabelas de viagem e Europeia](#join) secção.

## <a name="process-data-with-u-sql"></a>Processar dados com o U-SQL
As tarefas de processamento de dados ilustradas nesta secção incluem a ingerir, a verificação de qualidade, explorar e os dados de amostragem. Também é mostrado como associar as tabelas de viagem e Europeia. A seção final mostra execução uma tarefa de script de U-SQL no portal do Azure. Seguem-se ligações para cada subseção:

* [Ingestão de dados: de leitura de dados de blob público](#ingest)
* [Verificações de qualidade de dados](#quality)
* [Exploração de dados](#explore)
* [Junte-se a tabelas de viagem e Europeia](#join)
* [Amostragem de dados](#sample)
* [Executar tarefas de U-SQL](#run)

Os scripts U-SQL são descritos aqui e fornecidos num arquivo separado. Pode baixar o completo **scripts U-SQL** partir [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Para executar o U-SQL, abra o Visual Studio, clique em **ficheiro--> New--> Project**, escolha **projeto U-SQL**, dê um nome e guarde-o para uma pasta.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> É possível utilizar o Portal do Azure para executar o U-SQL em vez do Visual Studio. Pode navegar para o recurso do Azure Data Lake Analytics no portal e submeter consultas diretamente como ilustrado na figura a seguir:
>
>

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="ingest"></a>Ingestão de dados: Leitura de dados de blob público

A localização dos dados no blob do Azure é referenciada como **wasb://container\_name\@blob\_armazenamento\_conta\_name.blob.core.windows.net/blob_name**e pode ser extraído usando **Extractors.Csv()**. Substitua o seu nome de contentor e o nome de conta de armazenamento em scripts seguintes para o contentor\_name\@blob\_armazenamento\_conta\_nome o endereço de wasb. Uma vez que os nomes dos ficheiros estão no mesmo formato, é possível usar **viagem\_dados\_\{\*\}. csv** ler em todos os arquivos de 12 de viagem.

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

Uma vez que existem cabeçalhos na primeira linha, terá de remover os cabeçalhos e alterar os tipos de coluna para aqueles apropriado. Pode salvar os dados processados para a utilização do armazenamento do Azure Data Lake **swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ ou para utilizar da conta de armazenamento de Blobs do Azure **wasb: / / container_name\@blob_storage_account_name.blob.core.windows.net/blob_name**.

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

Da mesma forma pode ler em conjuntos de dados Europeia. Contexto do Azure Data Lake Store, pode optar por ver os seus dados no **portal do Azure--> Data Explorer** ou **Explorador de ficheiros** dentro do Visual Studio.

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="quality"></a>Verificações de qualidade de dados
Depois de tabelas de viagem e Europeia foram lidos no, verificações de qualidade de dados podem ser feitas da seguinte forma. Os arquivos resultantes de CSV podem ser enviadas para o armazenamento de Blobs do Azure ou do Azure Data Lake Store.

Encontre o número de medallions e o número exclusivo de medallions:

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

Encontre esses medallions que tinha mais de 100 viagens:

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

Localize valores em falta para algumas variáveis:

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



### <a name="explore"></a>Exploração de dados
Fazer alguma exploração de dados com os scripts seguintes para obter uma melhor compreensão dos dados.

Encontre a distribuição de viagens tipadas e não colocado para:

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

Encontre a distribuição de Gorjeta com valores de truncado: 0, 5, 10 e 20 dólares.

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

Encontre estatísticas básicas de distância de viagem:

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

Localize os percentis de distância de viagem:

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


### <a name="join"></a>Junte-se a tabelas de viagem e Europeia
Tabelas de viagem e Europeia podem ser associadas ao medallion, hack_license e pickup_time.

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


Para cada nível de contagem de passageiros, calcule o número de registos, Gorjeta média, desvio da quantidade de sugestão, percentagem de viagens colocado para.

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


### <a name="sample"></a>Amostragem de dados
Em primeiro lugar, selecione aleatoriamente 0,1% dos dados da tabela associado ao:

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

Em seguida, fazer amostragem stratified binário tip_class variável:

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


### <a name="run"></a>Executar tarefas de U-SQL
Quando concluir a edição de scripts U-SQL, pode enviá-los para o servidor com a sua conta do Azure Data Lake Analytics. Clique em **Data Lake**, **submeter tarefa**, selecione seu **conta Analytics**, selecione **paralelismo**e clique em **Submit**  botão.

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

Quando a tarefa é compilada com êxito, o estado da sua tarefa é apresentado no Visual Studio para a monitorização. Após a conclusão da tarefa em execução, pode, inclusivamente, reproduzir o processo de execução de tarefa e Descubra os passos de estrangulamento para melhorar sua eficiência de tarefa. Também pode aceder ao portal do Azure para verificar o estado das suas tarefas de U-SQL.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

Agora pode verificar os ficheiros de saída no armazenamento de Blobs do Azure ou no portal do Azure. Utilize os dados de exemplo stratified para nosso modelagem no próximo passo.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Criar e implementar modelos no Azure Machine Learning
Duas opções estão disponíveis extrair dados para o Azure Machine Learning para criar e

* A primeira opção, vai utilizar os dados de exemplo que foi escritos para um Blob do Azure (na **amostragem de dados** passo acima) e utilizar o Python para criar e implementar modelos do Azure Machine Learning.
* Na segunda opção, consultar os dados no Azure Data Lake diretamente, usando uma consulta do Hive. Esta opção requer que crie um novo cluster de HDInsight ou utilizar um cluster do HDInsight existente em que as tabelas do Hive apontam para os dados de táxis de NY no armazenamento do Azure Data Lake.  Ambas estas opções são abordadas nas secções seguintes.

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>Opção 1: Utilizar Python para criar e implementar modelos de machine learning
Para criar e implementar modelos de aprendizagem automática com o Python, crie um bloco de notas do Jupyter no seu computador local ou no Azure Machine Learning Studio. O bloco de notas do Jupyter fornecida no [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) contém o código completo para explorar, visualizar dados, engenharia de funcionalidades, modelação e implementação. São abordados neste artigo, apenas a modelação e a implantação.

### <a name="import-python-libraries"></a>Bibliotecas de Python de importação
Para executar o exemplo de notas do Jupyter e o Python ficheiro de script, o Python seguintes pacotes são necessários. Se estiver a utilizar o serviço de bloco de notas do Azure Machine Learning, esses pacotes foram previamente instalados.

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


### <a name="read-in-the-data-from-blob"></a>Leitura de dados de blob
* Cadeia de Ligação

        CONTAINERNAME = 'test1'
        STORAGEACCOUNTNAME = 'XXXXXXXXX'
        STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
        BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
        blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
* Leitura numa como texto

        t1 = time.time()
        data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
        t2 = time.time()
        print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))

  ![17](./media/data-lake-walkthrough/17-python_readin_csv.PNG)
* Adicionar nomes de colunas e separar colunas

        colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
        'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
        df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
* Alterar algumas colunas em numérico

        cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
        ,'tipped','tip_class','rownum']
        for col in cols_2_float:
            df1[col] = df1[col].astype(float)

### <a name="build-machine-learning-models"></a>Criar modelos de machine learning
Aqui é criar um modelo de classificação binária para prever se uma viagem é colocado para ou não. O bloco de notas do Jupyter encontrará outros dois modelos: classificação multiclasses e modelos de regressão.

* Primeiro precisa para criar variáveis fictícias que podem ser utilizadas na scikit-saiba modelos

        df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
        df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
* Criar o quadro de dados para a Modelagem

        cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
        data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])

        X = data.iloc[:,1:]
        Y = data.tipped
* Preparação e teste de divisão de 60 e 40

        X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
* Regressão logística no conjunto de treinamento

        model = LogisticRegression()
        logit_fit = model.fit(X_train, Y_train)
        print ('Coefficients: \n', logit_fit.coef_)
        Y_train_pred = logit_fit.predict(X_train)

       ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)
* Classificar o conjunto de dados de teste

        Y_test_pred = logit_fit.predict(X_test)
* Calcular a métrica de avaliação

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

### <a name="build-web-service-api-and-consume-it-in-python"></a>Criar API de serviço da Web e seu uso em Python
Quer operacionalizar o modelo de machine learning depois que ele foi projetado. O modelo de logística binário é usado aqui como um exemplo. Certificar-se de que o scikit-saiba a versão no seu computador local é 0.15.1. Não precisa se preocupar sobre isso, se utilizar o Azure Machine Learning studio.

* Encontre as credenciais da área de trabalho a partir das definições do Azure Machine Learning studio. No Azure Machine Learning Studio, clique em **configurações** --> **nome** --> **Tokens de autorização**.

    ![c3](./media/data-lake-walkthrough/c3-workspace-id.PNG)

        workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
        auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

* Criar o serviço Web

        @services.publish(workspaceid, auth_token)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(int) #0, or 1
        def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
            return logit_fit.predict(inputArray)
* Obter as credenciais do serviço web

        url = predictNYCTAXI.service.url
        api_key =  predictNYCTAXI.service.api_key

        print url
        print api_key

        @services.service(url, api_key)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(float)
        def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            pass
* Chame a API de serviço da Web. Terá de aguardar 5 a 10 segundos após o passo anterior.

        NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)

       ![c4](./media/data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>Opção 2: Criar e implementar modelos diretamente no Azure Machine Learning
O Azure Machine Learning Studio pode ler os dados diretamente a partir do Azure Data Lake Store e, em seguida, ser utilizado para criar e implementar modelos. Esta abordagem utiliza uma tabela de Hive que aponta para o Store do Azure Data Lake. Isto requer que um cluster separado do Azure HDInsight ser aprovisionado, no qual a tabela de Hive é criada. As secções seguintes mostram como fazer isso.

### <a name="create-an-hdinsight-linux-cluster"></a>Criar um Cluster do Linux de HDInsight
Criar um Cluster do HDInsight (Linux) a partir da [portal do Azure](https://portal.azure.com). Para obter detalhes, consulte a **criar um cluster do HDInsight com acesso ao Azure Data Lake Store** secção [criar um cluster do HDInsight com Data Lake Store através do portal do Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Criar uma tabela do Hive no HDInsight
Agora, criar tabelas do Hive a ser utilizado no Azure Machine Learning Studio no cluster do HDInsight com os dados armazenados no Azure Data Lake Store no passo anterior. Vá para o cluster de HDInsight que criou. Clique em **configurações** --> **propriedades** --> **identidade do AAD do Cluster** --> **ADLS acesso**, Certifique-se de que a sua conta do Azure Data Lake Store é adicionada na lista com a leitura, gravação e direitos de execução.

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Em seguida, clique em **Dashboard** junto aos **definições** botão e uma janela aparece. Clique em **vista do Hive** no canto superior direito da página e deverá ver a **Editor de consultas**.

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Cole os seguintes scripts de Hive para criar uma tabela. A localização da origem de dados está na referência do Azure Data Lake Store, desta forma: **adl://data_lake_store_name.azuredatalakestore.net:443/nome_da_pasta/nome_ficheiro**.

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


Quando a consulta termina a execução, verá os resultados da seguinte forma:

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Criar e implementar modelos no Azure Machine Learning Studio
Agora está pronto para criar e implementar um modelo que preveja se ou não uma dica é pago com o Azure Machine Learning. Os dados de exemplo stratified estão prontos para ser utilizada nesta classificação binária (sugestão ou não) problema. Modelos de previsão através da classificação de várias classes (tip_class) e regressão (tip_amount) também podem ser criados e implementados com o Azure Machine Learning Studio, mas aqui só é exibido como lidar com o caso com o modelo de classificação binária.

1. Obter os dados no Azure Machine Learning studio com o **importar dados** módulo, disponível na **dados de entrada e saída** secção. Para obter mais informações, consulte a [módulo importar dados](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) página de referência.
2. Selecione **consulta do Hive** como o **origem de dados** no **propriedades** painel.
3. Cole o seguinte script do Hive no **consulta de base de dados de Hive** editor

        select * from nyc_stratified_sample;
4. Introduza o cluster de URI do HDInsight (isso pode ser encontrado no portal do Azure), as credenciais do Hadoop, na localização de dados de saída e o nome de nome/chave/contentor da conta de armazenamento do Azure.

   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)

Um exemplo de uma leitura de experimentação de classificação binária dados de tabela do Hive são mostrados na figura a seguir:

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

Depois de criar a experimentação, clique em **segurança de serviço Web** --> **preditiva Web Service**

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

Executar automaticamente criado classificação experimentação, quando terminar, clique em **implementar serviço Web**

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

Dashboard do serviço web apresenta em breve:

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Resumo
Ao concluir este passo a passo, criou um ambiente de ciência de dados para a criação de soluções ponto-a-ponto no Azure Data Lake. Este ambiente foi utilizado para analisar um conjunto de dados público grande, colocar os passos canônico do processo de ciência de dados, de aquisição de dados por meio de preparação de modelos e, em seguida, para a implementação do modelo como um serviço web. U-SQL foi utilizado para processar, explorar e os dados de exemplo. Foram utilizadas Python e o Hive com o Azure Machine Learning Studio para criar e implementar modelos preditivos.

## <a name="whats-next"></a>Passos seguintes?
O percurso de aprendizagem para o [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) fornece ligações para tópicos que descrevem cada etapa do processo de análise avançada. Há uma série de orientações passo a passo, discriminada na [instruções passo a passo do processo de ciência de dados de equipa](walkthroughs.md) página que demonstram como usar recursos e serviços em vários cenários de Análise Preditiva:

* [O processo de ciência de dados de equipa em ação: utilizar o SQL Data Warehouse](sqldw-walkthrough.md)
* [O processo de ciência de dados de equipa em ação: com clusters do HDInsight Hadoop](hive-walkthrough.md)
* [O processo de ciência de dados de equipa: utilizar o SQL Server](sql-walkthrough.md)
* [Descrição geral do processo de ciência de dados com o Spark no Azure HDInsight](spark-overview.md)
