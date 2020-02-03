---
title: Construir e implementar um modelo utilizando a Azure Synapse Analytics - Team Data Science Process
description: Construa e implemente um modelo de machine learning utilizando o Azure Synapse Analytics com um conjunto de dados disponível publicamente.
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
ms.openlocfilehash: e64b951a8bb96b25a6ef917b4cebe077d6dd6657
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718451"
---
# <a name="the-team-data-science-process-in-action-using-azure-synapse-analytics"></a>O Processo de Ciência de Dados da Equipa em ação: usando a Azure Synapse Analytics
Neste tutorial, percorremos-lhe a construção e implementação de um modelo de machine learning utilizando o Azure Synapse Analytics para um conjunto de dados disponível publicamente -- o conjunto de dados de Viagens de Táxi de [NYC.](https://www.andresmh.com/nyctaxitrips/) O modelo de classificação binária construído prevê se uma gorjeta é ou não paga por uma viagem.  Os modelos incluem classificação multiclasse (se há ou não uma gorjeta) e regressão (distribuição pelos valores de gorjeta pagos).

O procedimento segue o fluxo de trabalho do Processo de Ciência de Dados da [Equipa (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) Mostramos como configurar um ambiente de ciência de dados, como carregar os dados no Azure Synapse Analytics, e como usar o Azure Synapse Analytics ou um Notebook IPython para explorar os dados e as funcionalidades de engenharia para modelar. Em seguida, mostramos como criar e implementar um modelo com o Azure Machine Learning.

## <a name="dataset"></a>O conjunto de dados de viagens de táxi de NYC
Os dados da viagem de táxis de NYC consiste em cerca de 20 GB de arquivos compactados de CSV (GB de ~ 48 descomprimido), gravar mais de 173 milhões de viagens individuais e os fares pago para cada viagem. Cada registo de viagem inclui as localizações de recolha e de redução e vezes, o número de licença de hackers anónimos (driver) e o número de medallion (de táxis de ID exclusivo). Os dados abrange todas as viagens no ano de 2013 e são fornecidos no seguintes dois conjuntos de dados para todos os meses:

1. O ficheiro **trip_data.csv** contém detalhes da viagem, tais como número de passageiros, pontos de recolha e entrega, duração da viagem e duração da viagem. Aqui estão alguns exemplos de registros:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. O ficheiro **trip_fare.csv** contém detalhes da tarifa paga por cada viagem, tais como tipo de pagamento, valor da tarifa, sobretaxa e impostos, gorjetas e portagens, e o valor total pago. Aqui estão alguns exemplos de registros:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A **chave única** usada para juntar dados de viagem\_e viagem\_tarifa é composta pelos seguintes três campos:

* Medallion,
* hack\_licença e
* pick-up\_data.

## <a name="mltasks"></a>Abordar três tipos de tarefas de previsão
Formulamos três problemas de previsão com base na *ponta\_quantidade* para ilustrar três tipos de tarefas de modelação:

1. **Classificação binária**: Prever se uma gorjeta foi ou não paga para uma viagem, isto é, uma *dica\_valor* superior a $0 é um exemplo positivo, enquanto uma gorjeta\_*valor* de $0 é um exemplo negativo.
2. **Classificação multiclasse**: Para prever o intervalo de gorjeta paga pela viagem. Dividimos a *gorjeta\_quantidade* em cinco caixotes ou classes:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. Tarefa de **regressão**: Prever o valor da gorjeta paga por uma viagem.

## <a name="setup"></a>Criar o ambiente de ciência de dados azure para análise avançada
Para configurar o ambiente de ciência de dados do Azure, siga estes passos.

**Crie a sua própria conta de armazenamento de blob Azure**

* Quando fornecer o seu próprio armazenamento de blob Azure, escolha um geo-local para o seu armazenamento de blob Azure dentro ou o mais próximo possível **do Centro-Sul dos EUA,** que é onde os dados do Táxi nyc são armazenados. Os dados serão copiados com o AzCopy do contentor de armazenamento de BLOBs público para um contentor na sua própria conta de armazenamento. Quanto mais próximo seu armazenamento de Blobs do Azure é Centro-Sul, mais rapidamente será possível concluir esta tarefa (etapa 4).
* Para criar a sua própria conta de Armazenamento Azure, siga os passos delineados nas [contas de Armazenamento Azure](../../storage/common/storage-create-storage-account.md). Certifique-se de que fazer anotações nos valores para as seguintes credenciais de conta de armazenamento, como eles serão necessários mais tarde nestas instruções.

  * **Nome da conta de armazenamento**
  * **Chave da conta de armazenamento**
  * **Nome** do recipiente (que pretende que os dados sejam armazenados no armazenamento de blob Azure)

**Forme a sua instância azure Synapse Analytics.**
Siga a documentação na Create and consulta de um Armazém de [Dados Azure SQL no portal Azure](../../sql-data-warehouse/create-data-warehouse-portal.md) para fornecer uma instância Azure Synapse Analytics. Certifique-se de que faz notações sobre as seguintes credenciais azure Synapse Analytics que serão usadas em etapas posteriores.

* **Nome do servidor**: \<servidor Name>.database.windows.net
* **Nome SQLDW (Base de Dados)**
* **Nome de Utilizador**
* **Palavra-passe**

**Instale ferramentas de dados do Estúdio Visual e do Servidor SQL.** Para obter instruções, consulte [Iniciar-se com o Visual Studio 2019 para o SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Ligue-se ao seu Azure Synapse Analytics com o Visual Studio.** Para obter instruções, consulte os passos 1 e 2 em [Connect to Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-connect-overview.md).

> [!NOTE]
> Faça a seguinte consulta SQL na base de dados que criou no seu Azure Synapse Analytics (em vez da consulta fornecida no passo 3 do tópico de ligação,) para **criar uma chave mestra**.
>
>

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Crie um espaço de trabalho Azure Machine Learning sob a sua assinatura Azure.** Para obter instruções, consulte Criar um espaço de [trabalho azure machine learning](../studio/create-workspace.md).

## <a name="getdata"></a>Carregue os dados no Azure Synapse Analytics
Abra uma consola de comandos do Windows PowerShell. Execute os seguintes comandos PowerShell para descarregar os ficheiros de script SQL que partilhamos consigo no GitHub para um diretório local que especifica com o parâmetro *-DestDir*. Pode alterar o valor do parâmetro *-DestDir* para qualquer diretório local. Se *-DestDir* não existir, será criado pelo script PowerShell.

> [!NOTE]
> Poderá ser necessário **executar como Administrador** ao executar o seguinte script PowerShell se o seu diretório *DestDir* precisar de privilégio de Administrador para criar ou escrever para ele.
>
>

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Após a execução bem sucedida, o seu diretório de trabalho atual muda para *-DestDir*. Deve ser capaz de ver a tela, conforme mostrado abaixo:

![Alterações do diretório de trabalho atual][19]

No seu *-DestDir,* execute o seguinte script PowerShell no modo administrador:

    ./SQLDW_Data_Import.ps1

Quando o script PowerShell for executado pela primeira vez, será-lhe pedido que insera as informações a partir do seu Azure Synapse Analytics e da sua conta de armazenamento de blob Azure. Quando tiver concluído este script do PowerShell em execução pela primeira vez, as credenciais de entrada irá ter sido escrita para um ficheiro de configuração SQLDW.conf no diretório de trabalho presente. A execução futura deste ficheiro de script do PowerShell tem a opção de ler que necessário todos os parâmetros deste ficheiro de configuração. Se precisar de alterar alguns parâmetros, pode optar por inserir os parâmetros no ecrã após solicitação, apagando este ficheiro de configuração e inserindo os valores dos parâmetros conforme solicitado ou alterando os valores do parâmetro editando o ficheiro SQLDW.conf no seu diretório *-DestDir.*

> [!NOTE]
> A fim de evitar conflitos de nome sinuoso com aqueles que já existem no seu Azure Azure Synapse Analytics, ao ler parâmetros diretamente do ficheiro SQLDW.conf, um número aleatório de 3 dígitos é adicionado ao nome schema do ficheiro SQLDW.conf como o esquema padrão nome para cada corrida. O script do PowerShell poderá pedir-lhe um nome de esquema: o nome pode ser especificado a critério do utilizador.
>
>

Este ficheiro **de script PowerShell** completa as seguintes tarefas:

* **Downloads e instala AzCopy,** se a AzCopy ainda não estiver instalada

        $AzCopy_path = SearchAzCopy
        if ($AzCopy_path -eq $null){
               Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
            InstallAzCopy
            $AzCopy_path = SearchAzCopy
        }
            $env_path = $env:Path
            for ($i=0; $i -lt $AzCopy_path.count; $i++){
                if ($AzCopy_path.count -eq 1){
                    $AzCopy_path_i = $AzCopy_path
                } else {
                    $AzCopy_path_i = $AzCopy_path[$i]
                }
                if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
                    Write-Host $AzCopy_path_i 'not in system path, add it...'
                    [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
                    $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
                    $env_path = $env:Path
                }
* **Copie dados para a sua conta privada** de armazenamento blob da bolha pública com AzCopy

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
* **Carregue dados utilizando a Polybase (executando LoadDataToSQLDW.sql) para o seu Azure Synapse Analytics** a partir da sua conta privada de armazenamento blob com os seguintes comandos.

  * Criar um esquema

          EXEC (''CREATE SCHEMA {schemaname};'');
  * Criar uma credencial com âmbito de base de dados

          CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
          WITH IDENTITY = ''asbkey'' ,
          Secret = ''{StorageAccountKey}''
  * Criar uma fonte de dados externa para uma bolha de armazenamento Azure

          CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
          WITH
          (
              TYPE = HADOOP,
              LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
              CREDENTIAL = {KeyAlias}
          )
          ;

          CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
          WITH
          (
              TYPE = HADOOP,
              LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
              CREDENTIAL = {KeyAlias}
          )
          ;
  * Crie um formato de ficheiro externo para um ficheiro csv. Dados não comprimidos e os campos são separados pelo caráter de pipe.

          CREATE EXTERNAL FILE FORMAT {csv_file_format}
          WITH
          (
              FORMAT_TYPE = DELIMITEDTEXT,
              FORMAT_OPTIONS
              (
                  FIELD_TERMINATOR ='','',
                  USE_TYPE_DEFAULT = TRUE
              )
          )
          ;
  * Crie Europeia externa e as tabelas de viagem para conjunto de dados de táxis de NYC no armazenamento de Blobs do Azure.

          CREATE EXTERNAL TABLE {external_nyctaxi_fare}
          (
              medallion varchar(50) not null,
              hack_license varchar(50) not null,
              vendor_id char(3),
              pickup_datetime datetime not null,
              payment_type char(3),
              fare_amount float,
              surcharge float,
              mta_tax float,
              tip_amount float,
              tolls_amount float,
              total_amount float
          )
          with (
              LOCATION    = ''/nyctaxifare/'',
              DATA_SOURCE = {nyctaxi_fare_storage},
              FILE_FORMAT = {csv_file_format},
              REJECT_TYPE = VALUE,
              REJECT_VALUE = 12
          )

            CREATE EXTERNAL TABLE {external_nyctaxi_trip}
            (
                   medallion varchar(50) not null,
                   hack_license varchar(50)  not null,
                   vendor_id char(3),
                   rate_code char(3),
                   store_and_fwd_flag char(3),
                   pickup_datetime datetime  not null,
                   dropoff_datetime datetime,
                   passenger_count int,
                   trip_time_in_secs bigint,
                   trip_distance float,
                   pickup_longitude varchar(30),
                   pickup_latitude varchar(30),
                   dropoff_longitude varchar(30),
                   dropoff_latitude varchar(30)
            )
            with (
                LOCATION    = ''/nyctaxitrip/'',
                DATA_SOURCE = {nyctaxi_trip_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12
            )

    - Carregue dados de tabelas externas no armazenamento de blob Azure para a Azure Synapse Analytics

            CREATE TABLE {schemaname}.{nyctaxi_fare}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_fare}
            ;

            CREATE TABLE {schemaname}.{nyctaxi_trip}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_trip}
            ;

    - Criar uma tabela de dados de exemplo (NYCTaxi_Sample) e inserir dados ao mesmo de selecionar as consultas SQL nas tabelas de viagem e Europeia. (Alguns passos deste passadiço precisam de usar esta mesa de amostra.)

            CREATE TABLE {schemaname}.{nyctaxi_sample}
            WITH
            (
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            (
                SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
                tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
                tip_class = CASE
                        WHEN (tip_amount = 0) THEN 0
                        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                        ELSE 4
                    END
                FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
                WHERE datepart("mi",t.pickup_datetime) = 1
                AND t.medallion = f.medallion
                AND   t.hack_license = f.hack_license
                AND   t.pickup_datetime = f.pickup_datetime
                AND   pickup_longitude <> ''0''
                AND   dropoff_longitude <> ''0''
            )
            ;

A localização geográfica das suas contas de armazenamento afeta os tempos de carregamento.

> [!NOTE]
> Dependendo da localização geográfica da sua conta privada de armazenamento blob, o processo de cópia de dados de uma bolha pública para a sua conta de armazenamento privado pode demorar cerca de 15 minutos, ou ainda mais, e o processo de carregamento de dados da sua conta de armazenamento para o seu Azure A Azure Synapse Analytics pode demorar 20 minutos ou mais.
>
>

Terá de decidir o que significam, se tiver ficheiros de origem e de destino duplicados.

> [!NOTE]
> Se os ficheiros a. csv ser copiados do armazenamento de BLOBs público à sua conta de armazenamento de blob privado de existir na sua conta de armazenamento de blob privado, AzCopy irá pedir-lhe se pretende substituí-los. Se não quiser substitui-los, input **n** quando solicitado. Se quiser substituir **todos,** **insere-se num** quando solicitado. Também pode inserir **y** para substituir ficheiros .csv individualmente.
>
>

![Saída do AzCopy][21]

Pode utilizar os seus dados. Se os dados estão na sua máquina no local em seu aplicativo da vida real, pode utilizar o AzCopy para carregar dados no local para o seu armazenamento de Blobs do Azure privado. Basta alterar a localização **Source,** `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, no comando AzCopy do ficheiro script PowerShell para o diretório local que contém os seus dados.

> [!TIP]
> Se os seus dados já se encontrarem no seu armazenamento privado de blob Azure na sua aplicação real, pode ignorar o passo AzCopy no script PowerShell e enviar diretamente os dados para a Azure Azure Synapse Analytics. Isso exigirá mais edições do script para adaptá-lo para o formato dos seus dados.
>
>

Este script PowerShell também liga as informações do Azure Synapse Analytics nos ficheiros de exemplo de exploração de dados SQLDW_Explorations.sql, SQLDW_Explorations.ipynb e SQLDW_Explorations_Scripts.py para que estes três ficheiros estejam prontos para serem experimentados instantaneamente após o script PowerShell completar.

Após uma execução concluída com êxito, verá o ecrã, conforme mostrado abaixo:

![Saída de uma execução de script com êxito][20]

## <a name="dbexplore"></a>Exploração de dados e engenharia de recursos na Azure Synapse Analytics
Nesta secção, realizamos a exploração de dados e a geração de recursos executando consultas SQL contra a Azure Synapse Analytics diretamente usando **Ferramentas**de Dados do Estúdio Visual . Todas as consultas SQL utilizadas nesta secção podem ser encontradas no script da amostra chamado *SQLDW_Explorations.sql*. Este ficheiro já foi transferido para o diretório no local pelo script do PowerShell. Também pode recuperá-lo do [GitHub.](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql) Mas o ficheiro no GitHub não tem a informação Azure Synapse Analytics ligada.

Ligue-se ao seu Azure Synapse Analytics utilizando o Visual Studio com o nome de login e senha de login Azure Synapse Analytics e abra o **SQL Object Explorer** para confirmar que a base de dados e as tabelas foram importadas. Recupere o ficheiro *SQLDW_Explorations.sql.*

> [!NOTE]
> Para abrir um editor de consulta paralelo de dados (PDW), utilize o comando **New Query** enquanto o seu PDW é selecionado no **SQL Object Explorer**. O editor de consultas SQL padrão não é suportado pelo PDW.
>
>

Aqui estão os tipos de tarefas de exploração de dados e geração de recursos realizadas nesta secção:

* Explore as distribuições de dados de alguns campos em várias janelas de tempo.
* Investigue a qualidade dos dados dos campos de longitude e latitude.
* Gere etiquetas de classificação binárias e multiclasse com base na **quantidade\_ponta**.
* Gerar recursos e a computação/compare distâncias de viagem.
* Junte-se as duas tabelas e extraia uma amostra aleatória que será utilizada para criar modelos.

### <a name="data-import-verification"></a>Verificação de importação de dados
Estas consultas fornecem uma verificação rápida do número de linhas e colunas nas tabelas anteriormente utilizando em massa de paralela do Polybase importar,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Saída:** Deve ter 173.179.759 filas e 14 colunas.

### <a name="exploration-trip-distribution-by-medallion"></a>Exploração: Distribuição de viagem por medallion
Esta consulta de exemplo identifica medallions (números de táxis) concluído mais de 100 viagens durante um período de tempo especificado. A consulta beneficiaria do acesso à mesa dividido, uma vez que está condicionada pelo regime de partição da **recolha\_data.** Consultar o conjunto de dados completo também fará com que utilize da tabela particionada e/ou análise de índice.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Saída:** A consulta deverá devolver uma tabela com linhas que especifiquem os 13.369 medalhões (táxis) e o número de viagens concluídas em 2013. A última coluna contém a contagem do número de viagens concluída.

### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Exploração: Distribuição de viagem por medallion e hack_license
Neste exemplo identifica os medallions (números de táxis) e hack_license números (controladores) que foram concluídos mais do que 100 viagens dentro de um período de tempo especificado.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Saída:** A consulta deverá devolver uma tabela com 13.369 filas que especifiquem as 13.369 identificações de carros/condutores que completaram mais de 100 viagens em 2013. A última coluna contém a contagem do número de viagens concluída.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Avaliação de qualidade de dados: Certifique-se de registos com longitude incorreto e/ou latitude
Neste exemplo investiga se qualquer um dos campos de longitude e/ou latitude optar por conter um valor inválido (radian graus devem estar entre -90 e 90), ou tem (0, 0) coordenadas.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Saída:** A consulta devolve 837.467 viagens que têm campos de longitude e/ou latitude inválidos.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Exploração: Colocado para vs. a distribuição de viagens não colocado para
Neste exemplo localiza o número de viagens que foram colocado para vs. o número que não foram tipados num período de tempo especificado (ou no conjunto de dados completo se abrangendo o ano completo, como ele é definido aqui). Essa distribuição reflete a distribuição de etiqueta de binário para ser utilizado mais tarde para a Modelagem de classificação binária.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Saída:** A consulta deve devolver as seguintes frequências de gorjeta para o ano de 2013: 90.447.622 gorjetas e 82.264.709 não gorjeta.

### <a name="exploration-tip-classrange-distribution"></a>Exploração: Distribuição de classe/intervalo de sugestão
Neste exemplo calcula a distribuição de intervalos de sugestão num determinado período de tempo (ou no conjunto de dados completo se abrangendo o ano completo). Esta distribuição das classes de etiquetas será mais tarde utilizada para modelação de classificação multiclasse.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

**Saída:**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Computação de exploração: E compare a distância de viagem
Neste exemplo converte a recolha e de redução de longitude e latitude para geografia SQL aponta, computa a distância de viagem com a diferença de pontos de geografia SQL e devolve uma amostra aleatória dos resultados para comparação. O exemplo limita os resultados para as coordenadas válidos apenas usando a consulta de avaliação de qualidade de dados abordada anteriormente.

    /****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
          DECLARE @distance decimal(28, 10)
          -- Convert to radians
          SET @Lat1 = @Lat1 / 57.2958
          SET @Long1 = @Long1 / 57.2958
          SET @Lat2 = @Lat2 / 57.2958
          SET @Long2 = @Long2 / 57.2958
          -- Calculate distance
          SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
          --Convert to miles
          IF @distance <> 0
          BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
          END
          RETURN @distance
    END
    GO

    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

### <a name="feature-engineering-using-sql-functions"></a>Engenharia de funcionalidades com as funções SQL
Por vezes, as funções SQL podem ser uma opção eficiente para engenharia de funcionalidades. Nestas instruções, definimos uma função SQL para calcular a distância direta entre as localizações de recolha e de redução. Pode executar os seguintes scripts SQL em **Ferramentas**de Dados do Estúdio Visual .

Aqui está o script SQL que define a função de distância.

    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function calculate the direct distance between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
          DECLARE @distance decimal(28, 10)
          -- Convert to radians
          SET @Lat1 = @Lat1 / 57.2958
          SET @Long1 = @Long1 / 57.2958
          SET @Lat2 = @Lat2 / 57.2958
          SET @Long2 = @Long2 / 57.2958
          -- Calculate distance
          SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
          --Convert to miles
          IF @distance <> 0
          BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
          END
          RETURN @distance
    END
    GO

Eis um exemplo para chamar esta função para gerar recursos na sua consulta SQL:

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Saída:** Esta consulta gera uma tabela (com 2.803.538 linhas) com latitudes e longitudes de recolha e abandono e as distâncias diretas correspondentes em milhas. Aqui estão os resultados das três primeiras filas:

|  | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40.731804 |-74.001083 |40.736622 |-73.988953 |.7169601222 |
| 2 |40.715794 |-74,010635 |40.725338 |-74.00399 |.7448343721 |
| 3 |40.761456 |-73.999886 |40.766544 |-73.988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Preparar dados para a criação de modelo
A seguinte consulta junta-se à **viagem de\_de nyctaxi** e às tabelas de **tarifas\_nyctaxi,** gera uma etiqueta de classificação binária **inclinada**, uma dica de classificação de várias classes **\_classe**, e extrai uma amostra do conjunto de dados completo. A amostragem é feita pela recuperação de um subconjunto de viagens de ida e volta com base no tempo de recolha.  Esta consulta pode ser copiada e depois colada diretamente no Módulo de Dados de Importação de [Dados de Importação] [(Clássico)](https://studio.azureml.net) import e dados de[importação] para ingestão direta de dados a partir da instância de Base de Dados SQL em Azure. A consulta exclui registos com incorreto (0, 0) coordenadas.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

Quando estiver pronto para avançar para o Azure Machine Learning, pode:

1. Guarde a consulta final do SQL para extrair e recolher os dados e copiar a consulta diretamente num módulo[de dados de importação] de dados de importação em Azure Machine Learning, ou
2. Persistir os dados amostrados e projetados que pretende utilizar para a construção de modelos numa nova tabela Azure Synapse Analytics e utilizar a nova tabela no módulo[de dados de importação de dados] de importação de dados de [importação]em Azure Machine Learning. O guião da PowerShell em passo anterior fez esta tarefa por si. Pode ler diretamente a partir desta tabela no módulo importar dados.

## <a name="ipnb"></a>Exploração de dados e engenharia de recursos no caderno IPython
Nesta secção, realizaremos a exploração de dados e a geração de recursos usando consultas Python e SQL contra o Azure Synapse Analytics criado anteriormente. Um caderno IPython chamado **SQLDW_Explorations.ipynb** e um ficheiro de guião Python **SQLDW_Explorations_Scripts.py** foram descarregados para o seu diretório local. Também estão disponíveis no [GitHub.](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW) Esses dois arquivos são idênticos em scripts de Python. O ficheiro de script de Python é fornecido a, no caso de não ter um servidor IPython Notebook. Estes dois ficheiros Python da amostra foram concebidos sob **python 2.7**.

As informações necessárias do Azure Synapse Analytics na amostra IPython Notebook e no ficheiro de script Python descarregado seletiva para a sua máquina local foram previamente ligadas pelo script PowerShell. Eles são executáveis sem qualquer modificação.

Se já criou um espaço de trabalho azure machine learning, pode enviar diretamente a amostra IPython Notebook para o serviço Deporpor Dopor IPython AzureML e começar a executá-la. Aqui estão os passos para enviar para o serviço Depor portátil AzureML IPython:

1. Inicie sessão no seu espaço de trabalho Azure Machine Learning, clique em **Studio"** no topo e clique em **NOTEBOOKS** no lado esquerdo da página web.

    ![Clique em Studio, em seguida, blocos de notas][22]
2. Clique em **NOVO** no canto inferior esquerdo da página web e selecione **Python 2**. Em seguida, forneça um nome para o bloco de notas e clique na marca de verificação para criar o novo em branco IPython Notebook.

    ![Clique em novo, em seguida, selecione o Python 2][23]
3. Clique no símbolo **Jupyter** no canto superior esquerdo do novo IPython Notebook.

    ![Clique o símbolo de Jupyter][24]
4. Arraste e largue a amostra IPython Notebook para a página de **árvoredo** do seu serviço Deporpor IPython AzureML e clique em **Upload**. Em seguida, o exemplo que ipython Notebook será carregado para o serviço AzureML IPython Notebook.

    ![Clique em carregar][25]

Para executar o exemplo IPython Notebook ou o Python ficheiro de script, o Python seguintes pacotes são necessários. Se estiver a utilizar o serviço AzureML IPython Notebook, esses pacotes foram previamente instalados.

- pandas
- numpy
- matplotlib
- pyodbc
- PyTables

Ao construir soluções analíticas avançadas em Azure Machine Learning com grandes dados, aqui está a sequência recomendada:

* Leia-se numa pequena amostra dos dados num quadro de dados na memória.
* Efetue algumas visualizações e explorations usando os dados de amostras.
* Experimentar a engenharia de funcionalidades com os dados de amostras.
* Para uma maior exploração de dados, manipulação de dados e engenharia de recursos, utilize python para emitir consultas SQL diretamente contra o Azure Synapse Analytics.
* Decida o tamanho da amostra adequada para a criação de modelo do Azure Machine Learning.

O seguinte é alguns exploração de dados, visualização de dados e funcionalidade de exemplos de engenharia. Mais explorations de dados podem ser encontrados no exemplo IPython Notebook e o ficheiro de script de Python de exemplo.

### <a name="initialize-database-credentials"></a>Inicializar as credenciais da base de dados
Inicialize as definições de ligação de base de dados nas seguintes variáveis:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Criar ligação de base de dados
Segue-se a cadeia de ligação que cria a ligação à base de dados.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Relatar o número de linhas e colunas na tabela < nyctaxi_trip >
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Número total de linhas = 173179759
* Número total de colunas = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_fare"></a>Relatar o número de linhas e colunas na tabela < nyctaxi_fare >
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Número total de linhas = 173179759
* Número total de colunas = 11

### <a name="read-in-a-small-data-sample-from-the-azure-synapse-analytics-database"></a>Leia uma pequena amostra de dados da Base de Dados de Análise Synapse Azure
    t0 = time.time()

    query = '''
        SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
        WHERE datepart("mi",t.pickup_datetime) = 1
        AND   t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Tempo de leitura que a tabela de exemplo é 14.096495 segundos.
Número de linhas e colunas obter = (1000, 21).

### <a name="descriptive-statistics"></a>Estatísticas descritivas
Agora, está pronto para explorar os dados de amostras. Começamos por analisar algumas estatísticas descritivas para a **viagem\_distância** (ou quaisquer outros campos que você escolher especificar).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Visualização: Exemplo de plotagem de caixa
Em seguida, observe o desenho de caixa para a distância de viagem visualizar o quantiles.

    df1.boxplot(column='trip_distance',return_type='dict')

![Saída de plotagem de caixa][1]

### <a name="visualization-distribution-plot-example"></a>Visualização: Exemplo de plotagem de distribuição
Desenhos que visualizar a distribuição e um histograma para as distâncias de amostragem de viagem.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Saída de plotagem de distribuição][2]

### <a name="visualization-bar-and-line-plots"></a>Barras de visualização: E gráficos de linhas
Neste exemplo, a distância de viagem para cinco contentores de discretização e visualizar os resultados de discretização.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Podemos pode representar a distribuição de bin acima numa barra ou linha de plotagem com:

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Saída de desenho da barra][3]

e

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Saída de desenho de linha][4]

### <a name="visualization-scatterplot-examples"></a>Visualização: Exemplos de gráfico de dispersão
Mostramos enredo de dispersão entre **a viagem\_\_do tempo em\_segs** e **viagem\_distância** para ver se há alguma correlação

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Saída de gráfico de dispersão de relação entre a hora e a distância][6]

Da mesma forma, podemos verificar a relação entre a **taxa\_código** e a **viagem\_distância.**

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Saída de gráfico de dispersão de relação entre o código e a distância][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Exploração de dados em amostras de dados através de consultas SQL no IPython notebook
Nesta secção, exploramos a distribuição de dados utilizando os dados amostrados que persistem na nova tabela que criamos acima. Explorações semelhantes podem ser realizadas utilizando as tabelas originais.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Exploração: Número de linhas e colunas na tabela de amostragem de relatório
    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Exploração: Colocado para/não ultrapassado distribuição
    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Exploração: Distribuição de classe de sugestão
    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Exploração: A distribuição de sugestão de desenho pela classe
    tip_class_dist['tip_freq'].plot(kind='bar')

![#26 de desenho][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Exploração: Distribuição diária de viagens
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Exploração: Distribuição de viagem por medallion
    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Exploração: Distribuição de viagem pela licença medallion e hackers
    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Exploração: Distribuição de tempo de viagem
    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Exploração: Distribuição de distância de viagem
    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Exploração: Distribuição de tipo de pagamento
    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Verifique se o formulário final da tabela caracterizadas
    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Construir modelos em Aprendizagem automática Azure
Estamos agora prontos para avançar para a modelação e modelação de implantação em [Azure Machine Learning.](https://studio.azureml.net) Os dados estão prontos para ser utilizado em qualquer um dos problemas de predição identificados anteriormente, ou seja:

1. **Classificação binária**: Para prever se uma gorjeta foi ou não paga para uma viagem.
2. **Classificação multiclasse**: Prever o intervalo de gorjeta paga, de acordo com as classes previamente definidas.
3. Tarefa de **regressão**: Prever o valor da gorjeta paga por uma viagem.

Para iniciar o exercício de modelação, inicie sessão no seu espaço de trabalho **Azure Machine Learning (clássico).** Se ainda não criou um espaço de trabalho de aprendizagem automática, consulte [Create a Azure Machine Learning Studio (clássico) workspace](../studio/create-workspace.md).

1. Para começar com o Azure Machine Learning, veja [o que é o Azure Machine Learning Studio (clássico)?](../studio/what-is-ml-studio.md)
2. Inicie sessão no [Azure Machine Learning Studio (clássico)](https://studio.azureml.net).
3. A página inicial do Machine Learning Studio (clássico) fornece uma grande quantidade de informação, vídeos, tutoriais, links para a Referência de Módulos, e outros recursos. Para mais informações sobre o Azure Machine Learning, consulte o Centro de Documentação de [Aprendizagem automática azure.](https://azure.microsoft.com/documentation/services/machine-learning/)

Uma experimentação de preparação típica inclui os seguintes passos:

1. Crie uma experiência **+NEW.**
2. Obtenha os dados no Azure Machine Learning Studio (clássico).
3. Pré-processar, transformar e manipular os dados conforme necessário.
4. Gere recursos conforme necessário.
5. Dividir os dados em conjuntos de dados de treinamento/validação/teste (ou tem conjuntos de dados separados para cada um).
6. Selecione um ou mais algoritmos de machine learning consoante o problema de aprendizagem para resolver. Por exemplo, classificação binária, classificação multiclasse, regressão.
7. Prepare um ou mais modelos com o conjunto de dados de treinamento.
8. Classificar o conjunto de dados de validação utilizando o modelo treinado (s).
9. Avalie o modelo (s) para calcular as métricas relevantes para o problema de aprendizagem.
10. Sintonize os modelos e selecione o melhor modelo para implementar.

Neste exercício, já explorámos e concebemos os dados no Azure Synapse Analytics, e decidimos sobre o tamanho da amostra para ingerir no Azure Machine Learning Studio (clássico). Este é o procedimento para criar um ou mais dos modelos de predição:

1. Obtenha os dados no Azure Machine Learning Studio (clássico) utilizando o módulo[de dados de importação de] dados de importação de dados de [importação,]disponível na secção de Entrada e Saída de **Dados.** Para mais informações, consulte a página de referência do módulo de dados de importação de dados de[importação de dados] de [importação.]

    ![O Azure ML importar dados][17]
2. Selecione **Base de Dados Azure SQL** como fonte de **dados** no painel **Propriedades.**
3. Introduza o nome DNS da base de dados no campo de nome do **servidor base de dados.** Formato: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Introduza o **nome Base** de Dados no campo correspondente.
5. Introduza o nome de *utilizador SQL* no nome da **conta**do servidor e a *palavra-passe* na **palavra-passe**da conta do servidor .
7. Na área de edição de consulta de base de **dados,** reexa a consulta que extrai os campos de base de dados necessários (incluindo quaisquer campos computacionais, como as etiquetas) e amostras abaixo dos dados para o tamanho da amostra pretendido.

Um exemplo de uma experiência de classificação binária que lê dados diretamente da base de dados Azure Synapse Analytics está na figura abaixo (lembre-se de substituir os nomes de tabela nyctaxi_trip e nyctaxi_fare pelo nome do esquema e os nomes de tabela que usou no seu walkthrough). Experiências semelhantes podem ser construídas para a classificação de várias classes e problemas de regressão.

![O Azure ML Train][10]

> [!IMPORTANT]
> Nos exemplos de extração e amostragem de dados de modelação fornecidos em secções anteriores, **todas as etiquetas para os três exercícios de modelação estão incluídas na consulta**. Um passo importante (necessário) em cada um dos exercícios de modelação é **excluir** os rótulos desnecessários para os outros dois problemas, e quaisquer outras fugas de **alvo.** Por exemplo, quando utilizar a classificação binária, utilize a etiqueta **inclinada** e exclua a ponta dos campos **\_classe,** **ponta\_quantidade**, e quantidade total de **\_** . Essas últimas são vazamentos de destino, uma vez que eles implicam a dica pago.
>
> Para excluir quaisquer colunas ou fugas de alvo desnecessárias, pode utilizar as [Colunas Select no][select-columns] módulo Dataset ou nos Metadados de [Edição][edit-metadata]. Para mais informações, consulte [Selecione Colunas em Dataset][select-columns] e edite páginas de referência de [Metadados.][edit-metadata]
>
>

## <a name="mldeploy"></a>Implementar modelos em Aprendizagem automática Azure
Quando o modelo estiver pronto, pode implementá-la facilmente como um serviço web diretamente a partir da experimentação. Para obter mais informações sobre a implementação de serviços web Azure ML, consulte [A implantação de um serviço web Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md).

Para implementar um novo serviço web, terá de:

1. Crie uma experimentação de classificação.
2. Implemente o serviço web.

Para criar uma experiência de pontuação a partir de uma experiência de treino **terminada,** clique em **CREATE SCORING EXPERIMENT** na barra de ação inferior.

![Classificação do Azure][18]

O Azure Machine Learning irá tentar criar uma experiência de classificação com base nos componentes da experimentação de preparação. Em particular, irá:

1. Guardar o modelo preparado e remova os módulos de treinamento de modelo.
2. Identifique uma porta de **entrada** lógica para representar o esquema de dados de entrada esperado.
3. Identifique uma porta de **saída** lógica para representar o esquema de saída de serviço web esperado.

Quando a experiência de pontuação for criada, reveja os resultados e ajuste-se conforme necessário. Um ajuste típico é substituir o conjunto de dados de entrada ou consulta por um que exclui os campos de etiqueta, porque estes campos de etiquetanão serão mapeados para o esquema ao chamar o serviço. É também uma boa prática reduzir o tamanho do conjunto de dados de entrada e/ou consulta a alguns registos, o suficiente para indicar o esquema de entrada. Para a porta de saída, é comum excluir todos os campos de entrada e apenas incluir as **Etiquetas Pontuadas** e **Probabilidades Pontuadas** na saída utilizando as Colunas Select no módulo [Dataset.][select-columns]

Um exemplo de experimentação de classificação é fornecido na imagem abaixo. Quando estiver pronto para ser implantado, clique no botão **PUBLISH WEB SERVICE** na barra de ação inferior.

![O Azure ML publicar][11]

## <a name="summary"></a>Resumo
Para recapitular o que fizemos neste tutorial passo a passo, criou um ambiente de ciência de dados do Azure, trabalhado com um grande conjunto de dados público, ele utilizar o processo de ciência de dados de equipa, todo o caminho de aquisição de dados para preparação de modelos e, em seguida, para o implementação de um serviço web Azure Machine Learning.

### <a name="license-information"></a>Informações de licença
Estas instruções de exemplo e que o acompanha scripts e o IPython notebook(s) são partilhadas pela Microsoft sob a licença do MIT. Verifique o ficheiro LICENSE.txt no diretório do código da amostra no GitHub para obter mais detalhes.

## <a name="references"></a>Referências
- [Página de descarregamento de viagens de táxi de Andrés Monroy NYC](https://www.andresmh.com/nyctaxitrips/)
- [FOILing NYC's Taxi Trip Data by Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)
- [Pesquisa e Estatísticas da Comissão de Táxis e Limusines da NYC](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sqldw-walkthrough/azuremltrain.png
[11]: ./media/sqldw-walkthrough/azuremlpublish.png
[12]: ./media/sqldw-walkthrough/ssmsconnect.png
[13]: ./media/sqldw-walkthrough/executescript.png
[14]: ./media/sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/sqldw-walkthrough/bulkimport.png
[17]: ./media/sqldw-walkthrough/amlreader.png
[18]: ./media/sqldw-walkthrough/amlscoring.png
[19]: ./media/sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/sqldw-walkthrough/ps_load_data.png
[21]: ./media/sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[de dados de importação]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
