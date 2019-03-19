---
title: Criar e implementar um modelo com o SQL Data Warehouse - Team Data Science Process
description: Criar e implementar um modelo de machine learning com o SQL Data Warehouse com um conjunto de dados publicamente disponível.
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
ms.openlocfilehash: adbf3e4fbc0f9d49458dadaebcdd2a090030654e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57838889"
---
# <a name="the-team-data-science-process-in-action-using-sql-data-warehouse"></a>O processo de ciência de dados de equipa em ação: utilizar o SQL Data Warehouse
Neste tutorial, iremos guiá-lo através da criação e implementação de um modelo de aprendizagem automática com o SQL Data Warehouse (SQL DW) para um conjunto de dados publicamente disponível – a [NYC táxis viagens](https://www.andresmh.com/nyctaxitrips/) conjunto de dados. O modelo de classificação binária construído prevê se ou não uma dica é pago por uma viagem e modelos de classificação multiclasses e regressão também são discutidos que prever a distribuição para os valores de sugestão pagos.

O procedimento segue a [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) fluxo de trabalho. Vamos mostrar como configurar um ambiente de ciência de dados, como carregar os dados no armazém de dados SQL e como utilizar o armazém de dados SQL ou um IPython Notebook para explorar os dados e o engenheiro de funcionalidades do modelo. Em seguida, mostramos como criar e implementar um modelo com o Azure Machine Learning.

## <a name="dataset"></a>O conjunto de dados de viagens de táxis de NYC
Os dados da viagem de táxis de NYC consiste em cerca de 20GB de arquivos compactados de CSV (GB de ~ 48 descomprimido), gravar mais de 173 milhões de viagens individuais e os fares pago para cada viagem. Cada registo de viagem inclui as localizações de recolha e de redução e tempos, número de licença hack anónimo (driver) e o número de medallion (id exclusivo de táxis). Os dados abrange todas as viagens no ano de 2013 e são fornecidos no seguintes dois conjuntos de dados para todos os meses:

1. O **trip_data.csv** ficheiro contém os detalhes de viagem, como o número de passageiros, pontos de recolha e de redução, duração de viagem e comprimento de viagem. Aqui estão alguns exemplos de registros:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. O **trip_fare.csv** ficheiro contém detalhes de Europeia pago para cada viagem, como o tipo de pagamento, a quantidade de Europeia, sobretaxa e impostos, dicas e pedágio e o valor total pago. Aqui estão alguns exemplos de registros:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

O **chave exclusiva** utilizada para associar a viagem\_dados e viagem\_Europeia é composta por três campos seguintes:

* Medallion,
* Hack\_licença e
* recolha\_datetime.

## <a name="mltasks"></a>Três tipos de tarefas de predição de endereços
Podemos formular três problemas de previsão com base na *sugestão\_quantidade* para ilustrar os três tipos de modelagem de tarefas:

1. **Classificação binária**: Para prever se é ou não uma dica foi paga por uma viagem, ou seja, um *sugestão\_quantidade* superior de US $0 é um exemplo positivo, enquanto uma *tip\_quantidade* de US $0 é um exemplo negativo.
2. **Classificação multiclasses**: Para prever o intervalo de sugestão pagos para a viagem. Vamos dividir o *sugestão\_quantidade* em cinco contentores ou classes:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. **Tarefa de regressão**: Para prever a quantidade de sugestão pagos por uma viagem.

## <a name="setup"></a>Configurar o ambiente de ciência de dados do Azure para análise avançada
Para configurar o ambiente de ciência de dados do Azure, siga estes passos.

**Criar sua própria conta de armazenamento de Blobs do Azure**

* Quando aprovisionar o seu próprio armazenamento de Blobs do Azure, escolha uma localização geográfica para seu armazenamento de Blobs do Azure em ou o mais próximo possível **Centro-Sul**, que é onde os dados de táxis de NYC são armazenados. Os dados serão copiados com o AzCopy do contentor de armazenamento de BLOBs público para um contentor na sua própria conta de armazenamento. Quanto mais próximo seu armazenamento de Blobs do Azure é Centro-Sul, mais rapidamente será possível concluir esta tarefa (etapa 4).
* Para criar sua própria conta de armazenamento do Azure, siga os passos descritos em [sobre as contas de armazenamento](../../storage/common/storage-create-storage-account.md). Certifique-se de que fazer anotações nos valores para as seguintes credenciais de conta de armazenamento, como eles serão necessários mais tarde nestas instruções.

  * **Nome da conta de armazenamento**
  * **Chave da conta de armazenamento**
  * **Nome do contentor** (o que pretende que os dados sejam armazenados no armazenamento de Blobs do Azure)

**Aprovisione a instância de armazém de dados do Azure SQL.**
Siga a documentação em [criar um SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) para Aprovisionar uma instância do SQL Data Warehouse. Certifique-se de que faça notações sobre as seguintes credenciais do SQL Data Warehouse que serão utilizadas em passos posteriores.

* **Nome do servidor**: <server Name>. database.windows.net
* **Nome do SQL dw (base de dados)**
* **Nome de Utilizador**
* **Palavra-passe**

**Instale o Visual Studio e SQL Server Data Tools.** Para obter instruções, consulte [instalar o Visual Studio 2015 e/ou SSDT (SQL Server Data Tools) para o SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Ligar ao seu armazém de dados SQL do Azure com o Visual Studio.** Para obter instruções, veja os passos 1 e 2 na [ligar ao Azure SQL Data Warehouse com o Visual Studio](../../sql-data-warehouse/sql-data-warehouse-connect-overview.md).

> [!NOTE]
> Executar a seguinte consulta SQL na base de dados que criou no seu armazém de dados SQL (em vez da consulta fornecida no passo 3 do tópico connect,) **crie uma chave mestra**.
>
>

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Crie uma área de trabalho do Azure Machine Learning com a sua subscrição do Azure.** Para obter instruções, consulte [criar uma área de trabalho do Azure Machine Learning](../studio/create-workspace.md).

## <a name="getdata"></a>Carregar os dados para o SQL Data Warehouse
Abra uma consola de comandos do Windows PowerShell. Execute o PowerShell seguinte comandos para transferir o exemplo SQL arquivos de script que podemos compartilhar com no GitHub para um diretório local que especificar com o parâmetro *- DestDir*. Pode alterar o valor do parâmetro *- DestDir* para qualquer diretório local. Se *- DestDir* não existir, será criado pelo script do PowerShell.

> [!NOTE]
> Talvez seja preciso **executar como administrador** ao executar o seguinte script do PowerShell, se seu *DestDir* diretório tem o privilégio de administrador para criar ou para escrever no mesmo.
>
>

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Após a execução bem-sucedida, o seu atual diretório de trabalho muda para *- DestDir*. Deve ser capaz de ver a tela, conforme mostrado abaixo:

![Alterações do diretório de trabalho atual][19]

No seu *- DestDir*, execute o seguinte script do PowerShell no modo de administrador:

    ./SQLDW_Data_Import.ps1

Quando o script do PowerShell é executado pela primeira vez, será solicitado para introduzir as informações do seu armazém de dados de SQL do Azure e a sua conta de armazenamento de Blobs do Azure. Quando tiver concluído este script do PowerShell em execução pela primeira vez, as credenciais de entrada irá ter sido escrita para um ficheiro de configuração SQLDW.conf no diretório de trabalho presente. A execução futura deste ficheiro de script do PowerShell tem a opção de ler que necessário todos os parâmetros deste ficheiro de configuração. Se precisar de alterar alguns parâmetros, pode escolher para os parâmetros na tela após a linha de comandos de entrada ao eliminar este ficheiro de configuração e inserir os valores de parâmetros, conforme solicitado ou alterar os valores de parâmetro ao editar o ficheiro de SQLDW.conf no seu *- DestDir* diretório.

> [!NOTE]
> Para evitar conflitos de nomes de esquema com aqueles que já existem no seu Azure SQL DW, quando a ler parâmetros diretamente a partir do ficheiro de SQLDW.conf, um número aleatório de 3 dígitos é adicionado ao nome do esquema do arquivo SQLDW.conf como o nome do esquema padrão para cada execução. O script do PowerShell poderá pedir-lhe um nome de esquema: o nome pode ser especificado a critério do utilizador.
>
>

Isso **script do PowerShell** ficheiro conclui as seguintes tarefas:

* **Transfere e instala o AzCopy**, se AzCopy não estiver já instalado

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
* **Copia os dados à sua conta de armazenamento de blob privado** partir do blob público com o AzCopy

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
* **Carrega dados com o Polybase (executando LoadDataToSQLDW.sql) para o Azure SQL DW** da sua conta de armazenamento de blob privado com os seguintes comandos.

  * Criar um esquema

          EXEC (''CREATE SCHEMA {schemaname};'');
  * Criar uma credencial com âmbito de base de dados

          CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
          WITH IDENTITY = ''asbkey'' ,
          Secret = ''{StorageAccountKey}''
  * Criar uma origem de dados externos para um blob de armazenamento do Azure

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

    - Carregar dados a partir das tabelas externas no armazenamento de Blobs do Azure para o SQL Data Warehouse

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

    - Criar uma tabela de dados de exemplo (NYCTaxi_Sample) e inserir dados ao mesmo de selecionar as consultas SQL nas tabelas de viagem e Europeia. (Alguns passos destas instruções tem de utilizar esta tabela de exemplo.)

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
> Consoante a localização geográfica da conta de armazenamento de blob privado, o processo de cópia de dados de um blob público para a sua conta de armazenamento privado pode demorar cerca de 15 minutos, ou até mesmo mais tempo e o processo de carregamento de dados da sua conta de armazenamento para o Azure Armazém de dados SQL pode levar de 20 minutos ou mais.
>
>

Terá de decidir o que significam, se tiver ficheiros de origem e de destino duplicados.

> [!NOTE]
> Se os ficheiros a. csv ser copiados do armazenamento de BLOBs público à sua conta de armazenamento de blob privado de existir na sua conta de armazenamento de blob privado, AzCopy irá pedir-lhe se pretende substituí-los. Se não pretende substituí-los, de entrada **n** quando lhe for pedido. Se pretender substituir **todos os** deles, de entrada **um** quando lhe for pedido. Também pode introduzir **y** para substituir os ficheiros. csv individualmente.
>
>

![Saída do AzCopy][21]

Pode utilizar os seus dados. Se os dados estão na sua máquina no local em seu aplicativo da vida real, pode utilizar o AzCopy para carregar dados no local para o seu armazenamento de Blobs do Azure privado. Só precisa de alterar o **origem** localização, `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, no comando do AzCopy do ficheiro de script do PowerShell para o diretório de local que contém os seus dados.

> [!TIP]
> Se seus dados já estão no armazenamento de Blobs do Azure privado em seu aplicativo da vida real, pode ignorar o passo de AzCopy no script do PowerShell e carregar diretamente os dados para o Azure SQL DW. Isso exigirá mais edições do script para adaptá-lo para o formato dos seus dados.
>
>

Este script do Powershell também se conecta as informações do armazém de dados do Azure SQL os ficheiros de exemplo de exploração de dados SQLDW_Explorations.sql SQLDW_Explorations.ipynb e SQLDW_Explorations_Scripts.py para que estes três ficheiros estiver prontos para experimentar instantaneamente após o script do PowerShell é concluída.

Após uma execução concluída com êxito, verá o ecrã, conforme mostrado abaixo:

![Saída de uma execução de script com êxito][20]

## <a name="dbexplore"></a>Exploração de dados e de engenharia de funcionalidades no Azure SQL Data Warehouse
Nesta secção, executamos de geração de exploração e a funcionalidade de dados executando consultas SQL diretamente com o Azure SQL DW **ferramentas de dados do Visual Studio**. Todas as consultas SQL utilizadas nesta secção podem ser encontradas no exemplo de script com o nome *SQLDW_Explorations.sql*. Este ficheiro já foi transferido para o diretório no local pelo script do PowerShell. Também pode obtê-lo a partir [GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Mas o ficheiro no GitHub, não tem as informações do armazém de dados do Azure SQL ligadas.

Ligar ao seu armazém de dados de SQL do Azure com o Visual Studio com o nome de início de sessão do armazém de dados SQL e a palavra-passe e abrir o **Object Explorer do SQL** para confirmar as tabelas de base de dados e foram importadas. Obter o *SQLDW_Explorations.sql* ficheiro.

> [!NOTE]
> Para abrir um editor de consultas do armazém de dados paralela (PDW), utilize o **nova consulta** comando enquanto seu PDW está selecionado na **SQL Object Explorer**. O editor de consultas SQL padrão não é suportado pelo PDW.
>
>

Seguem-se o tipo de dados tarefas de geração de exploração e a funcionalidade efetuadas nesta secção:

* Explore as distribuições de dados de alguns campos em várias janelas de tempo.
* Investigue a qualidade dos dados dos campos de longitude e latitude.
* Gerar etiquetas de classificação binária e várias classes com base na **sugestão\_quantidade**.
* Gerar recursos e a computação/compare distâncias de viagem.
* Junte-se as duas tabelas e extraia uma amostra aleatória que será utilizada para criar modelos.

### <a name="data-import-verification"></a>Verificação de importação de dados
Estas consultas fornecem uma verificação rápida do número de linhas e colunas nas tabelas anteriormente utilizando em massa de paralela do Polybase importar,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Saída:** Obterá 173,179,759 linhas e colunas de 14.

### <a name="exploration-trip-distribution-by-medallion"></a>Exploração: Distribuição de viagem por medallion
Esta consulta de exemplo identifica medallions (números de táxis) concluído mais de 100 viagens durante um período de tempo especificado. A consulta poderia tirar proveito do acesso a tabela particionada, uma vez que ele está condicionada ao esquema de partição da **recolha\_datetime**. Consultar o conjunto de dados completo também fará com que utilize da tabela particionada e/ou análise de índice.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Saída:** A consulta deverá devolver uma tabela com linhas especificando as 13,369 medallions (capotados) e o número de viagens concluído por eles em 2013. A última coluna contém a contagem do número de viagens concluída.

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Exploração: Distribuição de viagem por medallion e hack_license
Neste exemplo identifica os medallions (números de táxis) e hack_license números (controladores) que foram concluídos mais do que 100 viagens dentro de um período de tempo especificado.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Saída:** A consulta deverá devolver uma tabela com 13,369 linhas especificando o carro/controlador 13,369 IDs que concluíram a mais que 100 viagens em 2013. A última coluna contém a contagem do número de viagens concluída.

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

**Saída:** A consulta devolve 837,467 viagens que têm campos de longitude e/ou latitude inválidos.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Exploração: Colocado para vs. a distribuição de viagens não colocado para
Neste exemplo localiza o número de viagens que foram colocado para vs. o número que não foram tipados num período de tempo especificado (ou no conjunto de dados completo se abrangendo o ano completo, como ele é definido aqui). Essa distribuição reflete a distribuição de etiqueta de binário para ser utilizado mais tarde para a Modelagem de classificação binária.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Saída:** A consulta deverá devolver as seguintes frequências de sugestão para o ano de 2013: 90,447,622 82,264,709 e colocado para não-tipados.

### <a name="exploration-tip-classrange-distribution"></a>Exploração: Sugestão de distribuição de classe/intervalo
Neste exemplo calcula a distribuição de intervalos de sugestão num determinado período de tempo (ou no conjunto de dados completo se abrangendo o ano completo). Esta é a distribuição das classes de etiqueta que será utilizado mais tarde para a Modelagem de classificação multiclasses.

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

### <a name="exploration-compute-and-compare-trip-distance"></a>Exploração: Computação e compare a distância de viagem
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
Por vezes, as funções SQL podem ser uma opção eficiente para engenharia de funcionalidades. Nestas instruções, definimos uma função SQL para calcular a distância direta entre as localizações de recolha e de redução. Pode executar os seguintes scripts SQL num **ferramentas de dados do Visual Studio**.

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

**Saída:** Esta consulta gera uma tabela (com 2,803,538 linhas) com a recolha e redução latitudes e longitudes e as distâncias diretas correspondentes no quilómetros. Aqui estão os resultados para os primeiros 3 linhas:

|  | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40.731804 |-74.001083 |40.736622 |-73.988953 |.7169601222 |
| 2 |40.715794 |-74,010635 |40.725338 |-74.00399 |.7448343721 |
| 3 |40.761456 |-73.999886 |40.766544 |-73.988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Preparar dados para a criação de modelo
A seguinte consulta associa a **nyctaxi\_viagem** e **nyctaxi\_Europeia** tabelas, gera uma etiqueta de classificação binária **colocado para**, um etiqueta de classificação de Roc **sugestão\_classe**e extrai uma amostra do conjunto de dados associado ao completo. A amostragem é feita pela recuperação de um subconjunto de viagens de ida e volta com base no tempo de recolha.  Esta consulta pode ser copiada e colada diretamente no [Azure Machine Learning Studio](https://studio.azureml.net) [importar dados] [ import-data] módulo direto para ingestão de dados da instância de base de dados SQL no Azure. A consulta exclui registos com incorreto (0, 0) coordenadas.

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

1. Guardar a consulta SQL final para extrair e os dados de exemplo e copiar-colar a consulta diretamente para um [importar dados] [ import-data] módulo no Azure Machine Learning, ou
2. Manter os dados de amostras e engenharia que planeia utilizar para o modelo de criação de uma nova tabela de armazém de dados SQL e utilizar a nova tabela no [importar dados] [ import-data] módulo no Azure Machine Learning. O script do PowerShell no passo anterior fez isso para. Pode ler diretamente a partir desta tabela no módulo importar dados.

## <a name="ipnb"></a>Exploração de dados e de engenharia de funcionalidades no IPython notebook
Nesta secção, iremos efetuar exploração de dados e a geração de recursos com o Python e consultas SQL com o armazém de dados SQL criado anteriormente. Um bloco de notas de IPython do exemplo com o nome **SQLDW_Explorations.ipynb** e um ficheiro de script de Python **SQLDW_Explorations_Scripts.py** tenham sido transferidas para o diretório no local. Também estão disponíveis no [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Esses dois arquivos são idênticos em scripts de Python. O ficheiro de script de Python é fornecido a, no caso de não ter um servidor IPython Notebook. Estes dois de exemplo Python ficheiros destinam-se sob **Python 2.7**.

As informações de armazém de dados do Azure SQL necessárias no exemplo IPython Notebook e o ficheiro de script de Python transferido no seu computador local tiverem sido ligadas pelo script do PowerShell anteriormente. Eles são executáveis sem qualquer modificação.

Se já tiver configurado uma área de trabalho do AzureML, pode carregar o exemplo IPython Notebook para o serviço do AzureML IPython Notebook diretamente e começar a executá-lo. Eis os passos para carregar para o serviço AzureML IPython Notebook:

1. Inicie sessão na sua área de trabalho do AzureML, clique em "Studio" na parte superior e clique em "PORTÁTEIS", no lado esquerdo da página da web.

    ![Clique em Studio, em seguida, blocos de notas][22]
2. Clique em "Novo" no canto inferior esquerdo da página da web e selecione "Python 2". Em seguida, forneça um nome para o bloco de notas e clique na marca de verificação para criar o novo em branco IPython Notebook.

    ![Clique em novo, em seguida, selecione o Python 2][23]
3. Clique no símbolo de "Jupyter" no canto superior esquerdo do novo IPython Notebook.

    ![Clique o símbolo de Jupyter][24]
4. Arraste e largue o exemplo IPython Notebook para o **árvore** página do seu serviço AzureML IPython Notebook e clique em **carregar**. Em seguida, o exemplo que ipython Notebook será carregado para o serviço AzureML IPython Notebook.

    ![Clique em carregar][25]

Para executar o exemplo IPython Notebook ou o Python ficheiro de script, o Python seguintes pacotes são necessários. Se estiver a utilizar o serviço AzureML IPython Notebook, esses pacotes foram previamente instalados.

    - pandas
    - numpy
    - matplotlib
    - pyodbc
    - PyTables

A seqüência recomendada durante a criação de soluções de análise avançadas no AzureML com dados de grandes dimensões é a seguinte:

* Leia-se numa pequena amostra dos dados num quadro de dados na memória.
* Efetue algumas visualizações e explorations usando os dados de amostras.
* Experimentar a engenharia de funcionalidades com os dados de amostras.
* Para exploração de dados maior, a manipulação de dados e a engenharia de funcionalidades, utilize o Python para a criação de consultas SQL diretamente o armazém de dados SQL.
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

### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Relatar o número de linhas e colunas na tabela < nyctaxi_trip >
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

### <a name="report-number-of-rows-and-columns-in-table-nyctaxifare"></a>Relatar o número de linhas e colunas na tabela < nyctaxi_fare >
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

### <a name="read-in-a-small-data-sample-from-the-sql-data-warehouse-database"></a>Leia-num exemplo de dados pequenas da base de dados do armazém de dados do SQL
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
Agora, está pronto para explorar os dados de amostras. Vamos começar com observar algumas estatísticas descritivas para o **viagem\_distância** (ou quaisquer outros campos optar por especificar).

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

### <a name="visualization-bar-and-line-plots"></a>Visualização: Barras e gráficos de linhas
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
Vamos mostrar gráfico de dispersão entre **viagem\_tempo\_no\_segundos** e **viagem\_distância** para ver se há qualquer correlação

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Saída de gráfico de dispersão de relação entre a hora e a distância][6]

Da mesma forma podemos verificar a relação entre **taxa\_código** e **viagem\_distância**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Saída de gráfico de dispersão de relação entre o código e a distância][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Exploração de dados em amostras de dados através de consultas SQL no IPython notebook
Nesta secção, vamos explorar dados as distribuições que utilizem dados amostrados que são persistidos na nova tabela que criámos acima. Tenha em atenção que podem ser executadas explorations semelhante usando as tabelas originais.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Exploração: Relatar o número de linhas e colunas na tabela de amostragem
    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Exploração: Distribuição colocado para/não ativada
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

## <a name="mlmodel"></a>Criar modelos no Azure Machine Learning
Vamos agora está prontos para avançar para a criação de modelo e implementação de modelo na [do Azure Machine Learning](https://studio.azureml.net). Os dados estão prontos para ser utilizado em qualquer um dos problemas de predição identificados anteriormente, ou seja:

1. **Classificação binária**: Para prever se é ou não uma dica foi paga por uma viagem.
2. **Classificação multiclasses**: Para prever o intervalo de sugestão paga, de acordo com as classes definidas anteriormente.
3. **Tarefa de regressão**: Para prever a quantidade de sugestão pagos por uma viagem.

Para iniciar o exercício de modelagem, inicie sessão na sua **do Azure Machine Learning** área de trabalho. Se ainda não tiver criado um área de trabalho de aprendizagem automática, consulte [criar uma área de trabalho do studio Azure Machine Learning](../studio/create-workspace.md).

1. Para começar a utilizar com o Azure Machine Learning, consulte o artigo [o que é o Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Inicie sessão no [do Azure Machine Learning Studio](https://studio.azureml.net).
3. A página inicial do Studio fornece uma grande quantidade de informações, vídeos, tutoriais, links para a referência de módulos e outros recursos. Para obter mais informações sobre o Azure Machine Learning, consulte a [Centro de documentação do Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Uma experimentação de preparação típica inclui os seguintes passos:

1. Criar uma **+ novo** experimentar.
2. Obter os dados para o Azure Machine Learning studio.
3. Pré-processar, transformar e manipular os dados conforme necessário.
4. Gere recursos conforme necessário.
5. Dividir os dados em conjuntos de dados de treinamento/validação/teste (ou tem conjuntos de dados separados para cada um).
6. Selecione um ou mais algoritmos de machine learning consoante o problema de aprendizagem para resolver. Por exemplo, classificação binária, classificação de várias classes, regressão.
7. Prepare um ou mais modelos com o conjunto de dados de treinamento.
8. Classificar o conjunto de dados de validação utilizando o modelo treinado (s).
9. Avalie o modelo (s) para calcular as métricas relevantes para o problema de aprendizagem.
10. Bem, Otimize o modelo (s) e selecionar o melhor modelo para implementar.

Neste exercício, temos já explorou e desenvolvido os dados no armazém de dados SQL e decidir o tamanho da amostra para ingerir no Azure Machine Learning studio. Este é o procedimento para criar um ou mais dos modelos de predição:

1. Obter os dados no Azure Machine Learning studio com o [importar dados] [ import-data] módulo, disponível na **dados de entrada e saída** secção. Para obter mais informações, consulte a [importar dados] [ import-data] página de referência do módulo.

    ![O Azure ML importar dados][17]
2. Selecione **Azure SQL Database** como o **origem de dados** no **propriedades** painel.
3. Introduza o nome DNS de base de dados na **nome do servidor de base de dados** campo. Formato: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Introduza o **nome da base de dados** o campo correspondente.
5. Introduza o *nome de utilizador SQL* no **nome de conta de utilizador do servidor**e o *palavra-passe* no **palavra-passe de conta de utilizador servidor**.
7. Na **consulta de base de dados** editar área de texto, cole a consulta que extrai os campos de banco de dados necessários (incluindo quaisquer campos calculados, como os rótulos) e baixo exemplos os dados para o tamanho da amostra pretendido.

Um exemplo de uma experimentação de classificação binária ler os dados diretamente a partir da base de dados do SQL Data Warehouse é a figura abaixo (Lembre-se substituir o nyctaxi_trip de nomes de tabela e nyctaxi_fare pelo nome do esquema e os nomes de tabela que utilizou no seu passo a passo). Experiências semelhantes podem ser construídas para a classificação de várias classes e problemas de regressão.

![O Azure ML Train][10]

> [!IMPORTANT]
> Os dados de modelagem de extração e fazendo a amostragem de exemplos de consulta fornecidos nas secções anteriores, **todas as etiquetas para os três exercícios de modelagem são incluídas na consulta**. É um passo importante (obrigatório) em cada um dos exercícios de modelagem **excluir** as etiquetas desnecessárias para os outros dois problemas e qualquer outro **vazamentos de destino**. Por exemplo, ao utilizar a classificação binária, utilize a etiqueta **tipados** e excluir os campos **sugestão\_classe**, **tip\_quantidade**e a **total\_quantidade**. Essas últimas são vazamentos de destino, uma vez que eles implicam a dica pago.
>
> Para excluir qualquer colunas desnecessárias ou vazamentos de destino, pode utilizar o [Select Columns in Dataset] [ select-columns] módulo ou o [Editar metadados][edit-metadata]. Para obter mais informações, consulte [Select Columns in Dataset] [ select-columns] e [Editar metadados] [ edit-metadata] páginas de referência.
>
>

## <a name="mldeploy"></a>Implementar modelos no Azure Machine Learning
Quando o modelo estiver pronto, pode implementá-la facilmente como um serviço web diretamente a partir da experimentação. Para obter mais informações sobre a implementação de serviços web do Azure ML, consulte [implementar um serviço web do Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).

Para implementar um novo serviço web, terá de:

1. Crie uma experimentação de classificação.
2. Implemente o serviço web.

Criar uma experiência de classificação de um **concluído** experimentação de preparação, clique em **criar classificação EXPERIMENTAR** na barra de ação mais baixa.

![Classificação do Azure][18]

O Azure Machine Learning irá tentar criar uma experiência de classificação com base nos componentes da experimentação de preparação. Em particular, irá:

1. Guardar o modelo preparado e remova os módulos de treinamento de modelo.
2. Identificar uma lógica **porta de entrada** para representar o esquema de dados de entrada esperado.
3. Identificar uma lógica **porta de saída** para representar o esquema de saída do serviço web esperado.

Quando a experimentação de classificação é criada, revê-lo e fazer a ajustar conforme necessário. Um ajuste de típico é substituir o conjunto de dados de entrada e/ou a consulta com um que exclui os campos de etiqueta, à medida que estes não estarão disponíveis quando o serviço é chamado. Também é uma boa prática para reduzir o tamanho do conjunto de dados de entrada e/ou a consulta para alguns registos, apenas o suficiente para indicar o esquema de entrada. Para a porta de saída, é comum para excluir campos de entrada tudo e incluir apenas os **etiquetas classificadas** e **probabilidades classificadas** no saída com o [selecionar colunas no conjunto de dados] [ select-columns] módulo.

Um exemplo de experimentação de classificação é fornecido na imagem abaixo. Quando estiver pronto para implementar, clique nas **publicar WEB SERVICE** botão na barra de ação mais baixo.

![O Azure ML publicar][11]

## <a name="summary"></a>Resumo
Para recapitular o que fizemos neste tutorial passo a passo, criou um ambiente de ciência de dados do Azure, trabalhado com um grande conjunto de dados público, ele utilizar o processo de ciência de dados de equipa, todo o caminho de aquisição de dados para preparação de modelos e, em seguida, para o implementação de um serviço web Azure Machine Learning.

### <a name="license-information"></a>Informações de licença
Estas instruções de exemplo e que o acompanha scripts e o IPython notebook(s) são partilhadas pela Microsoft sob a licença do MIT. Verifique o ficheiro de LICENSE.txt no diretório do código de exemplo no GitHub para obter mais detalhes.

## <a name="references"></a>Referências
• [Página de transferência de viagens de táxis de NYC Andrés Monroy](https://www.andresmh.com/nyctaxitrips/) • [táxis de FOILing NYC dados de viagens por Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/) • [Comissão de táxis de NYC e Limousine pesquisa e as estatísticas](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

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
