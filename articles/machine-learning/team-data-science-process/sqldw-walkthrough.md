---
title: Criar e implantar um modelo usando o processo de ciência de dados de SQL Data Warehouse equipe
description: Crie e implante um modelo de aprendizado de máquina usando SQL Data Warehouse com um conjunto de informações publicamente disponível.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/24/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 76afafb59de762776b7d2614e383320b7d8f79e4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73669411"
---
# <a name="the-team-data-science-process-in-action-using-sql-data-warehouse"></a>O processo de ciência de dados de equipe em ação: usando SQL Data Warehouse
Neste tutorial, orientaremos você na criação e implantação de um modelo de aprendizado de máquina usando o SQL Data Warehouse (SQL DW) para um conjunto de informações publicamente disponível – o conjunto de NYC de corridas de [táxi](https://www.andresmh.com/nyctaxitrips/) . O modelo de classificação binária construído prevê se uma gorjeta é paga ou não por uma corrida, e modelos para classificação e regressão multiclasse também são discutidos para prever a distribuição para os valores de gorjeta pagos.

O procedimento segue o fluxo de trabalho [TDSP (processo de ciência de dados de equipe)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) . Mostramos como configurar um ambiente de ciência de dados, como carregar os dados no SQL DW e como usar o SQL DW ou um notebook IPython para explorar os dados e os recursos de engenharia para modelar. Em seguida, mostramos como criar e implantar um modelo com Azure Machine Learning.

## <a name="dataset"></a>O conjunto de NYC de viagens de táxi
Os dados de corrida de táxi de NYC consistem em cerca de 20 GB de arquivos CSV compactados (~ 48 GB descompactados), registrando mais de 173 milhões viagens individuais e as tarifas pagas por cada viagem. Cada registro de corrida inclui os locais e horários de retirada e chegada, o número de licença de hack (motorista) anônimo e o número de Medallion (ID exclusiva do táxi). Os dados abrangem todas as viagens no ano de 2013 e são fornecidos nos dois conjuntos a seguir para cada mês:

1. O arquivo **trip_data. csv** contém detalhes da viagem, como o número de passageiros, pontos de retirada e chegada, duração da corrida e duração da viagem. Aqui estão alguns registros de exemplo:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. O arquivo **trip_fare. csv** contém detalhes da tarifa paga para cada corrida, como tipo de pagamento, valor da tarifa, sobretaxa e impostos, dicas e tarifas e o valor total pago. Aqui estão alguns registros de exemplo:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

A **chave exclusiva** usada para unir\_dados e viagens\_tarifa é composta pelos três campos a seguir:

* Medallion
* licença de\_de hackers e
* retirando\_DateTime.

## <a name="mltasks"></a>Abordar três tipos de tarefas de previsão
Formulamos três problemas de previsão com base na *\_valor da dica* para ilustrar três tipos de tarefas de modelagem:

1. **Classificação binária**: para prever se uma gorjeta foi paga ou não por uma corrida, ou seja, uma *dica\_valor* maior que $0 é um exemplo positivo, enquanto uma *dica\_valor* de $0 é um exemplo negativo.
2. **Classificação multiclasse**: prever o intervalo de gorjetas pagas para a viagem. Dividimos a *dica\_valor* em cinco compartimentos ou classes:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. **Tarefa de regressão**: prever a quantidade de gorjeta paga por uma corrida.

## <a name="setup"></a>Configurar o ambiente de ciência de dados do Azure para análise avançada
Para configurar o ambiente de ciência de dados do Azure, siga estas etapas.

**Criar sua própria conta de armazenamento de BLOBs do Azure**

* Ao provisionar seu próprio armazenamento de BLOBs do Azure, escolha uma localização geográfica para o armazenamento de BLOBs do Azure no ou o mais próximo possível do **sul EUA Central**, que é onde os dados de táxi de NYC são armazenados. Os dados serão copiados usando AzCopy do contêiner de armazenamento de blob público para um contêiner em sua própria conta de armazenamento. Quanto mais próximo o armazenamento de BLOBs do Azure for o Sul EUA Central, mais rápido será a tarefa (etapa 4).
* Para criar sua própria conta de armazenamento do Azure, siga as etapas descritas em [sobre as contas de armazenamento do Azure](../../storage/common/storage-create-storage-account.md). Certifique-se de fazer anotações sobre os valores para as seguintes credenciais de conta de armazenamento, pois elas serão necessárias mais tarde neste guia.

  * **Nome da conta de armazenamento**
  * **Chave da conta de armazenamento**
  * **Nome do contêiner** (que você deseja que os dados sejam armazenados no armazenamento de BLOBs do Azure)

**Provisione sua instância do SQL DW do Azure.**
Siga a documentação em [criar uma SQL data warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) para provisionar uma instância de SQL data warehouse. Certifique-se de fazer notações nas credenciais de SQL Data Warehouse a seguir que serão usadas em etapas posteriores.

* **Nome do servidor**: \<nome do servidor >. Database. Windows. net
* **Nome do SQLDW (banco de dados)**
* **Nome de Utilizador**
* **Palavra-passe**

**Instale o Visual Studio e o SQL Server Data Tools.** Para obter instruções, consulte [instalar o Visual Studio 2015 e/ou SSDT (SQL Server Data Tools) para SQL data warehouse](../../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Conecte-se ao SQL DW do Azure com o Visual Studio.** Para obter instruções, consulte as etapas 1 & 2 em [conectar-se ao Azure SQL data warehouse com o Visual Studio](../../sql-data-warehouse/sql-data-warehouse-connect-overview.md).

> [!NOTE]
> Execute a seguinte consulta SQL no banco de dados criado no seu SQL Data Warehouse (em vez da consulta fornecida na etapa 3 do tópico conectar) para **criar uma chave mestra**.
>
>

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Crie um espaço de trabalho Azure Machine Learning em sua assinatura do Azure.** Para obter instruções, consulte [criar um Azure Machine Learning espaço de trabalho](../studio/create-workspace.md).

## <a name="getdata"></a>Carregar os dados em SQL Data Warehouse
Abra um console de comando do Windows PowerShell. Execute os seguintes comandos do PowerShell para baixar os arquivos de script SQL de exemplo que compartilhamos com você no GitHub para um diretório local que você especifica com o parâmetro *-DESTDIR*. Você pode alterar o valor de Parameter *-DESTDIR* para qualquer diretório local. Se *-DESTDIR* não existir, ele será criado pelo script do PowerShell.

> [!NOTE]
> Talvez seja necessário **Executar como administrador** ao executar o seguinte script do PowerShell se o diretório *DESTDIR* precisar de privilégio de administrador para criar ou gravar nele.
>
>

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Após a execução bem-sucedida, o diretório de trabalho atual muda para *-DESTDIR*. Você deve ser capaz de ver a tela como abaixo:

![Alterações no diretório de trabalho atual][19]

Em seu *-DESTDIR*, execute o seguinte script do PowerShell no modo de administrador:

    ./SQLDW_Data_Import.ps1

Quando o script do PowerShell for executado pela primeira vez, você será solicitado a inserir as informações de seu Azure SQL DW e sua conta de armazenamento de BLOBs do Azure. Quando esse script do PowerShell for executado pela primeira vez, as credenciais inseridas serão gravadas em um arquivo de configuração SQLDW. conf no diretório de trabalho atual. A execução futura desse arquivo de script do PowerShell tem a opção de ler todos os parâmetros necessários deste arquivo de configuração. Se precisar alterar alguns parâmetros, você pode optar por inserir os parâmetros na tela no prompt excluindo esse arquivo de configuração e inserindo os valores dos parâmetros conforme solicitado ou para alterar os valores de parâmetro editando o arquivo SQLDW. conf em seu *- Diretório DestDir*

> [!NOTE]
> Para evitar conflitos de nome de esquema com aqueles que já existem no SQL DW do Azure, ao ler parâmetros diretamente do arquivo SQLDW. conf, um número aleatório de 3 dígitos é adicionado ao nome do esquema do arquivo SQLDW. conf como o nome de esquema padrão para cada execução. O script do PowerShell pode solicitar um nome de esquema: o nome pode ser especificado no critério do usuário.
>
>

Este arquivo de **script do PowerShell** conclui as seguintes tarefas:

* **Baixa e instala o AzCopy**, se o AzCopy ainda não estiver instalado

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
* **Copia dados para sua conta de armazenamento de BLOBs privado** do blob público com AzCopy

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
* **Carrega dados usando o polybase (executando LoadDataToSQLDW. Sql) para o SQL DW do Azure** de sua conta de armazenamento de BLOBs privada com os comandos a seguir.

  * Criar um esquema

          EXEC (''CREATE SCHEMA {schemaname};'');
  * Criar uma credencial no escopo do banco de dados

          CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
          WITH IDENTITY = ''asbkey'' ,
          Secret = ''{StorageAccountKey}''
  * Criar uma fonte de dados externa para um blob de armazenamento do Azure

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
  * Crie um formato de arquivo externo para um arquivo CSV. Os dados são descompactados e os campos são separados com o caractere de barra vertical.

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
  * Criar tabelas de tarifa externa e viagem para o conjunto de NYC de táxi no armazenamento de BLOBs do Azure.

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

    - Carregar dados de tabelas externas no armazenamento de BLOBs do Azure para SQL Data Warehouse

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

    - Crie uma tabela de dados de exemplo (NYCTaxi_Sample) e insira dados nela de selecionar consultas SQL nas tabelas de corrida e tarifa. (Algumas etapas deste passo a passos precisam usar esta tabela de exemplo.)

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

A localização geográfica de suas contas de armazenamento afeta os tempos de carregamento.

> [!NOTE]
> Dependendo da localização geográfica da sua conta de armazenamento de BLOBs particular, o processo de copiar dados de um blob público para sua conta de armazenamento particular pode levar cerca de 15 minutos, ou ainda mais, e o processo de carregar dados de sua conta de armazenamento para o Azure O SQL DW pode levar 20 minutos ou mais.
>
>

Você precisará decidir o que fazer se tiver arquivos de origem e destino duplicados.

> [!NOTE]
> Se os arquivos. csv a serem copiados do armazenamento de blob público para sua conta de armazenamento de blob privado já existirem na sua conta de armazenamento de BLOBs particular, o AzCopy perguntará se você deseja substituí-los. Se você não quiser substituí-los, insira **n** quando solicitado. Se você quiser substituir **todos** eles, insira **um** quando solicitado. Você também pode inserir **y** para substituir os arquivos. csv individualmente.
>
>

![Saída de AzCopy][21]

Você pode usar seus próprios dados. Se seus dados estiverem em seu computador local em seu aplicativo de vida real, você ainda poderá usar o AzCopy para carregar dados locais para o armazenamento de BLOBs do Azure privado. Você só precisa alterar o local de **origem** , `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, no comando AzCopy do arquivo de script do PowerShell para o diretório local que contém os dados.

> [!TIP]
> Se os dados já estiverem em seu armazenamento de BLOBs do Azure privado em seu aplicativo real, você poderá ignorar a etapa AzCopy no script do PowerShell e carregar diretamente os dados para o SQL DW do Azure. Isso exigirá edições adicionais do script para adaptá-lo ao formato de seus dados.
>
>

Esse script do PowerShell também conecta as informações do Azure SQL DW aos arquivos de exemplo de exploração de dados SQLDW_Explorations. SQL, SQLDW_Explorations. ipynb e SQLDW_Explorations_Scripts. py para que esses três arquivos estejam prontos para serem testados imediatamente após o script do PowerShell é concluído.

Após uma execução bem-sucedida, você verá a tela como abaixo:

![Saída de uma execução de script bem-sucedida][20]

## <a name="dbexplore"></a>Exploração de dados e engenharia de recursos no Azure SQL Data Warehouse
Nesta seção, executaremos a exploração de dados e a geração de recursos executando consultas SQL no Azure SQL DW diretamente usando o **Visual Studio Data Tools**. Todas as consultas SQL usadas nesta seção podem ser encontradas no script de exemplo chamado *SQLDW_Explorations. SQL*. Este arquivo já foi baixado em seu diretório local pelo script do PowerShell. Você também pode recuperá-lo do [GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Mas o arquivo no GitHub não tem as informações do Azure SQL DW conectadas.

Conecte-se ao SQL DW do Azure usando o Visual Studio com o nome de logon e a senha do SQL DW e abra o pesquisador de **objetos do SQL** para confirmar se o banco de dados e as tabelas foram importados. Recupere o arquivo *SQLDW_Explorations. SQL* .

> [!NOTE]
> Para abrir um editor de consultas do PDW (Parallel data warehouse), use o comando **nova consulta** enquanto o PDW estiver selecionado no Pesquisador de **objetos do SQL**. O PDW não dá suporte ao editor de consultas SQL padrão.
>
>

Aqui estão os tipos de tarefas de exploração de dados e de geração de recursos executadas nesta seção:

* Explore as distribuições de dados de alguns campos em janelas de tempo variáveis.
* Investigue a qualidade dos dados dos campos de longitude e latitude.
* Gere rótulos de classificação binária e multiclasse com base na **\_valor da dica**.
* Gerar recursos e computar/comparar distâncias de viagem.
* Junte as duas tabelas e extraia uma amostra aleatória que será usada para criar modelos.

### <a name="data-import-verification"></a>Verificação de importação de dados
Essas consultas fornecem uma verificação rápida do número de linhas e colunas nas tabelas preenchidas anteriormente usando a importação em massa paralela do polybase,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Saída:** Você deve obter 173.179.759 linhas e 14 colunas.

### <a name="exploration-trip-distribution-by-medallion"></a>Exploração: distribuição de viagens por Medallion
Este exemplo de consulta identifica o medalhões (números de táxi) que concluíram mais de 100 corridas em um período de tempo especificado. A consulta se beneficiaria do acesso à tabela particionada, pois ela é condicional pelo esquema de partição de **retirada\_DateTime**. Consultar o conjunto de um inteiro também fará uso da tabela particionada e/ou da verificação de índice.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Saída:** A consulta deve retornar uma tabela com linhas especificando o 13.369 medalhões (táxis) e o número de corridas concluídas por eles em 2013. A última coluna contém a contagem do número de viagens concluídas.

### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Exploração: distribuição de viagens por Medallion e hack_license
Este exemplo identifica o medalhões (números de táxi) e números de hack_license (drivers) que concluíram mais de 100 corridas em um período de tempo especificado.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Saída:** A consulta deve retornar uma tabela com 13.369 linhas especificando as IDs de carro/driver 13.369 que concluíram mais de 100 viagens em 2013. A última coluna contém a contagem do número de viagens concluídas.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Avaliação de qualidade de dados: verificar registros com longitude e/ou latitude incorretos
Este exemplo investiga se qualquer um dos campos de longitude e/ou latitude contém um valor inválido (os graus radianos devem estar entre-90 e 90) ou têm coordenadas (0, 0).

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Saída:** A consulta retorna 837.467 corridas que têm campos de longitude e/ou latitude inválidos.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Exploração: distribuição de viagens gorjetas versus não inclinadas
Este exemplo localiza o número de corridas que foram gorjetas vs. o número que não foi arredondado em um período de tempo especificado (ou no conjunto de ativos se abrangendo o ano inteiro conforme ele é configurado aqui). Essa distribuição reflete que a distribuição de rótulo binário será usada posteriormente para a modelagem de classificação binária.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Saída:** A consulta deve retornar as seguintes frequências de gorjeta para o ano 2013:90.447.622 gorjetas e 82.264.709 não-gorjetas.

### <a name="exploration-tip-classrange-distribution"></a>Exploração: distribuição de classe/intervalo de gorjeta
Este exemplo computa a distribuição de intervalos de gorjeta em um determinado período de tempo (ou no conjunto de dados completo, se abrangendo o ano inteiro). Esta é a distribuição das classes de rótulo que serão usadas posteriormente para a modelagem de classificação multiclasse.

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

**Der**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Exploração: computação e comparação da distância de viagem
Este exemplo converte os pontos geográficos de retirada e chegada de longitude e latitude em SQL, computa a distância de corrida usando a diferença de pontos geográficos do SQL e retorna uma amostra aleatória dos resultados para comparação. O exemplo limita os resultados a coordenadas válidas usando a consulta de avaliação de qualidade de dados abordada anteriormente.

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

### <a name="feature-engineering-using-sql-functions"></a>Engenharia de recursos usando funções SQL
Às vezes, as funções SQL podem ser uma opção eficiente para a engenharia de recursos. Neste tutorial, definimos uma função SQL para calcular a distância direta entre os locais de retirada e chegada. Você pode executar os seguintes scripts SQL no **Visual Studio Data Tools**.

Aqui está o script SQL que define a função Distance.

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

Aqui está um exemplo para chamar essa função para gerar recursos em sua consulta SQL:

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Saída:** Essa consulta gera uma tabela (com 2.803.538 linhas) com latitudes e longitudes de retirada e chegada e as distâncias diretas correspondentes em milhas. Estes são os resultados das três primeiras linhas:

|  | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40,731804 |-74, 1083 |40,736622 |-73,988953 |.7169601222 |
| 2 |40,715794 |-74, 010635 |40,725338 |-74, 399 |.7448343721 |
| 3 |40,761456 |-73,999886 |40,766544 |-73,988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Preparar dados para construção de modelo
A consulta a seguir une as tabelas **nyctaxi\_Trip** e **nyctaxi\_Tarifa** , gera um rótulo de classificação binária **inclinado**, um rótulo de classificação multiclasse **Tip\_classe**e extrai um exemplo de o conjunto de associação completo. A amostragem é feita recuperando um subconjunto das viagens com base na hora da retirada.  Essa consulta pode ser copiada e colada diretamente no módulo [Azure Machine Learning Studio](https://studio.azureml.net) [importar dados][import-data] para ingestão direta de dados da instância do banco de dados SQL no Azure. A consulta exclui registros com coordenadas incorretas (0, 0).

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

Quando estiver pronto para continuar a Azure Machine Learning, você pode:

1. Salve a consulta SQL final para extrair e obter amostras dos dados e copiar e colar a consulta diretamente em um módulo [importar dados][import-data] no Azure Machine Learning ou
2. Persista os dados de amostra e projetados que você planeja usar para a criação de modelos em uma nova tabela do SQL DW e use a nova tabela no módulo [importar dados][import-data] no Azure Machine Learning. O script do PowerShell na etapa anterior fez isso para você. Você pode ler diretamente desta tabela no módulo importar dados.

## <a name="ipnb"></a>Exploração de dados e engenharia de recursos no IPython Notebook
Nesta seção, realizaremos a exploração de dados e a geração de recursos usando consultas do Python e do SQL no SQL DW criado anteriormente. Um IPython notebook de exemplo chamado **SQLDW_Explorations. ipynb** e um arquivo de script Python **SQLDW_Explorations_Scripts. py** foram baixados para o diretório local. Eles também estão disponíveis no [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Esses dois arquivos são idênticos em scripts Python. O arquivo de script do Python é fornecido a você caso você não tenha um servidor do IPython notebook. Esses dois arquivos Python de exemplo são projetados em **Python 2,7**.

As informações necessárias do Azure SQL DW no notebook IPython de exemplo e o arquivo de script Python baixados para seu computador local foram conectados pelo script do PowerShell anteriormente. Eles são executáveis sem qualquer modificação.

Se você já tiver configurado um espaço de trabalho do AzureML, poderá carregar diretamente o notebook IPython de exemplo no serviço do notebook IPython do AzureML e começar a executá-lo. Estas são as etapas para carregar o serviço do notebook IPython do AzureML:

1. Faça logon no seu espaço de trabalho do AzureML, clique em "estúdio" na parte superior e clique em "blocos de anotações" no lado esquerdo da página da Web.

    ![Clique em estúdio e NOTEBOOKs][22]
2. Clique em "novo" no canto inferior esquerdo da página da Web e selecione "Python 2". Em seguida, forneça um nome para o bloco de anotações e clique na marca de seleção para criar o novo bloco de anotações IPython em branco.

    ![Clique em novo e selecione Python 2][23]
3. Clique no símbolo "Jupyter" no canto superior esquerdo do novo notebook IPython.

    ![Clique no símbolo Jupyter][24]
4. Arraste e solte o bloco de anotações IPython de exemplo na página de **árvore** do seu serviço do notebook ipython do AzureML e clique em **carregar**. Em seguida, o notebook IPython de exemplo será carregado para o serviço do notebook IPython do AzureML.

    ![Clique em carregar][25]

Para executar o notebook IPython de exemplo ou o arquivo de script Python, os seguintes pacotes do Python são necessários. Se você estiver usando o serviço de notebook IPython do AzureML, esses pacotes foram pré-instalados.

- pandas
- numpy
- matplotlib
- pyodbc
- PyTables

A sequência recomendada ao criar soluções analíticas avançadas no AzureML com dados grandes é a seguinte:

* Leia em uma pequena amostra dos dados em um quadro de dados na memória.
* Execute algumas visualizações e explorações usando os dados de amostra.
* Experimente a engenharia de recursos usando os dados de amostra.
* Para exploração de dados maior, manipulação de dados e engenharia de recursos, use o Python para emitir consultas SQL diretamente no SQL DW.
* Decida o tamanho da amostra para ser adequado para a compilação do modelo de Azure Machine Learning.

A seguir estão alguns exemplos de exploração de dados, visualização de dados e engenharia de recursos. Mais explorações de dados podem ser encontradas no notebook IPython de exemplo e no arquivo de script Python de exemplo.

### <a name="initialize-database-credentials"></a>Inicializar credenciais do banco de dados
Inicialize as configurações de conexão do banco de dados nas seguintes variáveis:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Criar conexão de banco de dados
Aqui está a cadeia de conexão que cria a conexão com o banco de dados.

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

### <a name="read-in-a-small-data-sample-from-the-sql-data-warehouse-database"></a>Leia-em uma amostra de dados pequena do banco de dado SQL Data Warehouse
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

O tempo para ler a tabela de exemplo é de 14, 96495 segundos.
Número de linhas e colunas recuperadas = (1000, 21).

### <a name="descriptive-statistics"></a>Estatísticas descritivas
Agora você está pronto para explorar os dados de amostra. Começamos examinando algumas estatísticas descritivas para a **corrida\_distância** (ou quaisquer outros campos que você optar por especificar).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Visualização: exemplo de plotagem de caixa
Em seguida, examinamos a caixa para a distância da corrida para visualizar o quantis.

    df1.boxplot(column='trip_distance',return_type='dict')

![Saída de gráfico de caixa][1]

### <a name="visualization-distribution-plot-example"></a>Visualização: exemplo de plotagem de distribuição
Plotagens que visualizam a distribuição e um histograma para as distâncias de corrida de amostra.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Saída de gráfico de distribuição][2]

### <a name="visualization-bar-and-line-plots"></a>Visualização: plotagens de barras e linhas
Neste exemplo, nós colocamos a distância da corrida em cinco compartimentos e visualizamos os resultados do compartimentalização.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Podemos plotar a distribuição de bin acima em uma barra ou gráfico de linhas com:

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Saída de gráfico de barras][3]

e

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Saída de gráfico de linhas][4]

### <a name="visualization-scatterplot-examples"></a>Visualização: exemplos de dispersão
Mostramos gráficos de dispersão entre **viagens\_\_de tempo em\_s** e **viagem\_distância** para ver se há alguma correlação

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Dispersão a saída de relação entre a hora e a distância][6]

Da mesma forma, podemos verificar a relação entre o **código de\_de taxa** e a **viagem\_distância**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Dispersão a saída de relação entre o código e a distância][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Exploração de dados em dados de amostra usando consultas SQL no notebook IPython
Nesta seção, exploraremos distribuições de dados usando os dados de amostra que são persistidos na nova tabela que criamos acima. Observe que explorações semelhantes podem ser executadas usando as tabelas originais.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Exploração: relatar o número de linhas e colunas na tabela de amostra
    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Exploração: distribuição inclinada/não ultrapassada
    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Exploração: distribuição de classe Tip
    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Exploração: plotar a distribuição Tip por classe
    tip_class_dist['tip_freq'].plot(kind='bar')

![#26 de plotagem][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Exploração: distribuição diária de viagens
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Exploração: distribuição de viagens por Medallion
    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Exploração: distribuição de viagens por Medallion e licença de hack
    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Exploração: distribuição de tempo de viagem
    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Exploração: distribuição de distância da viagem
    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Exploração: distribuição de tipo de pagamento
    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Verifique a forma final da tabela destacados
    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Criar modelos no Azure Machine Learning
Agora estamos prontos para continuar a criação de modelos e implantação de modelo no [Azure Machine Learning](https://studio.azureml.net). Os dados estão prontos para serem usados em qualquer um dos problemas de previsão identificados anteriormente, isto é:

1. **Classificação binária**: para prever se uma gorjeta foi paga ou não por uma viagem.
2. **Classificação multiclasse**: prever o intervalo de gorjetas pagas, de acordo com as classes definidas anteriormente.
3. **Tarefa de regressão**: prever a quantidade de gorjeta paga por uma corrida.

Para iniciar o exercício de modelagem, faça logon no seu espaço de trabalho **Azure Machine Learning (clássico)** . Se você ainda não criou um espaço de trabalho do Machine Learning, consulte [criar um Azure Machine Learning Studio (clássico) espaço de trabalho](../studio/create-workspace.md).

1. Para começar a usar o Azure Machine Learning, consulte [o que é Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Faça logon no [Azure Machine Learning Studio](https://studio.azureml.net).
3. A home page do estúdio fornece uma grande quantidade de informações, vídeos, tutoriais, links para a referência de módulos e outros recursos. Para obter mais informações sobre Azure Machine Learning, consulte o [Azure Machine Learning centro de documentação](https://azure.microsoft.com/documentation/services/machine-learning/).

Um experimento de treinamento típico consiste nas seguintes etapas:

1. Crie um experimento **+ novo** .
2. Obtenha os dados no Azure Machine Learning Studio.
3. Pré-processe, transforme e manipule os dados conforme necessário.
4. Gere recursos conforme necessário.
5. Divida os dados em conjuntos de dado de treinamento/validação/teste (ou tenha conjuntos de dados separados para cada um).
6. Selecione um ou mais algoritmos de aprendizado de máquina dependendo do problema de aprendizado a ser resolvido. Por exemplo, classificação binária, classificação multiclasse, regressão.
7. Treine um ou mais modelos usando o conjunto de informações de treinamento.
8. Pontuar o conjunto de pontos de validação usando os modelos treinados.
9. Avalie os modelos para computar as métricas relevantes para o problema de aprendizado.
10. Ajuste os modelos e selecione o melhor modelo a ser implantado.

Neste exercício, já exploramos e projetamos os dados em SQL Data Warehouse e decidimos o tamanho da amostra para ingerir no Azure Machine Learning Studio. Este é o procedimento para criar um ou mais dos modelos de previsão:

1. Obtenha os dados em Azure Machine Learning Studio (clássico) usando o módulo [importar dados][import-data] , disponível na seção **entrada e saída de dados** . Para obter mais informações, consulte a página de referência do módulo [importar dados][import-data] .

    ![Dados de importação do Azure ML][17]
2. Selecione **Azure SQL Database** como a **fonte de dados** no painel **Propriedades** .
3. Insira o nome DNS do banco de dados no campo **nome do servidor de banco de dados** . Formato: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Insira o **nome do banco de dados** no campo correspondente.
5. Insira o *nome de usuário do SQL* no **nome da conta de usuário do servidor**e a *senha* na senha da conta de **usuário do servidor**.
7. Na área de texto Editar **consulta de banco de dados** , Cole a consulta que extrai os campos de banco de dados necessários (incluindo quaisquer campos computados, como os rótulos) e reduza os data para o tamanho de amostra desejado.

Um exemplo de um experimento de classificação binária com a leitura de dados diretamente do banco de SQL Data Warehouse está na figura abaixo (Lembre-se de substituir os nomes de tabela nyctaxi_trip e nyctaxi_fare pelo nome do esquema e os nomes de tabela que você usou em seu walkthrough). Experimentos semelhantes podem ser construídos para problemas de classificação e regressão de multiclasse.

![Treinamento do ML do Azure][10]

> [!IMPORTANT]
> Nos exemplos de consulta extração de dados de modelagem e amostragem fornecidos nas seções anteriores, **todos os rótulos para os três exercícios de modelagem são incluídos na consulta**. Uma etapa importante (obrigatória) em cada um dos exercícios de modelagem é **excluir** os rótulos desnecessários para os outros dois problemas e quaisquer outros **vazamentos de destino**. Por exemplo, ao usar a classificação binária, use o **rótulo indicado** e exclua os campos **Tip\_classe**, **tip\_valor**e **total\_valor**. Os últimos são vazamentos de destino, pois implicam a gorjeta paga.
>
> Para excluir colunas desnecessárias ou vazamentos de destino, você pode usar o módulo [selecionar colunas no conjunto][select-columns] de módulos ou [Editar metadados][edit-metadata]. Para obter mais informações, consulte [selecionar colunas nas páginas de referência do conjunto][select-columns] de dados e [Editar metadados][edit-metadata] .
>
>

## <a name="mldeploy"></a>Implantar modelos no Azure Machine Learning
Quando seu modelo estiver pronto, você poderá implantá-lo facilmente como um serviço Web diretamente do experimento. Para obter mais informações sobre a implantação de serviços Web do Azure ML, consulte [implantar um serviço web Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md).

Para implantar um novo serviço Web, você precisa:

1. Crie um experimento de pontuação.
2. Implante o serviço Web.

Para criar um experimento de Pontuação de um experimento de treinamento **concluído** , clique em **criar experimento de Pontuação** na barra de ação inferior.

![Pontuação do Azure][18]

Azure Machine Learning tentará criar um experimento de pontuação com base nos componentes do teste de treinamento. Em particular, ele irá:

1. Salve o modelo treinado e remova os módulos de treinamento do modelo.
2. Identifique uma **porta de entrada** lógica para representar o esquema de dados de entrada esperado.
3. Identifique uma **porta de saída** lógica para representar o esquema de saída do serviço Web esperado.

Quando o experimento de pontuação for criado, examine-o e ajuste conforme necessário. Um ajuste típico é substituir o conjunto de dados de entrada e/ou a consulta por um que exclua os campos de rótulo, pois eles não estarão disponíveis quando o serviço for chamado. Também é uma boa prática reduzir o tamanho do conjunto de dados de entrada e/ou da consulta para alguns registros, apenas o suficiente para indicar o esquema de entrada. Para a porta de saída, é comum excluir todos os campos de entrada e incluir apenas os **Rótulos pontuados** e as **probabilidades pontuadas** na saída usando o módulo [selecionar colunas no conjunto][select-columns] de dados.

Um experimento de Pontuação de exemplo é fornecido na figura abaixo. Quando estiver pronto para implantar, clique no botão **publicar serviço Web** na barra de ação inferior.

![Publicação do Azure ML][11]

## <a name="summary"></a>Resumo
Para recapitular o que fizemos neste tutorial explicativo, você criou um ambiente de ciência de dados do Azure, trabalhou com um grande conjunto de dados público, levando-o por meio do processo de ciência de dados de equipe, desde a aquisição de data para treinamento de modelo e, em seguida, para o implantação de um serviço Web Azure Machine Learning.

### <a name="license-information"></a>Informações de licença
Este exemplo explicativo e seus scripts que os acompanham e os notebooks IPython são compartilhados pela Microsoft sob a licença MIT. Verifique o arquivo LICENSE. txt no diretório do código de exemplo no GitHub para obter mais detalhes.

## <a name="references"></a>Referências
• [Andrés MONROY NYC táxi TRIPS Download Page](https://www.andresmh.com/nyctaxitrips/) • [frustrando os dados de corrida de táxi de NYC por Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/) • [NYC táxi e limusines Commission Research and Statistics](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

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
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
