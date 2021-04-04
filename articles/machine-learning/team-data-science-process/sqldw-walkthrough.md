---
title: Construa e implemente um modelo usando Azure Synapse Analytics - Processo de Ciência de Dados de Equipa
description: Construa e implemente um modelo de machine learning utilizando o Azure Synapse Analytics com um conjunto de dados disponível ao público.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, devx-track-python, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b638cb2b33f24220e7ceb852402862c707cc7bc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "93315999"
---
# <a name="the-team-data-science-process-in-action-using-azure-synapse-analytics"></a>O processo de ciência de dados da equipa em ação: usando a Azure Synapse Analytics
Neste tutorial, percorremos a construção e implementamos um modelo de machine learning usando a Azure Synapse Analytics para um conjunto de dados publicamente disponível - o conjunto de dados [de Viagens de Táxi de NYC.](https://www.andresmh.com/nyctaxitrips/) O modelo de classificação binária construído prevê se uma gorjeta é ou não paga por uma viagem.  Os modelos incluem classificação multiclasse (se há ou não uma ponta) e regressão (a distribuição pelos valores da gorjeta paga).

O procedimento segue o fluxo de trabalho [do Processo de Ciência de Dados de Equipa (TDSP).](./index.yml) Mostramos como configurar um ambiente de ciência de dados, como carregar os dados no Azure Synapse Analytics, e como usar a Azure Synapse Analytics ou um Notebook IPython para explorar os dados e as funcionalidades de engenheiro para modelar. Em seguida, mostramos como construir e implementar um modelo com Azure Machine Learning.

## <a name="the-nyc-taxi-trips-dataset"></a><a name="dataset"></a>O conjunto de dados de viagens de táxi de NYC
Os dados da NY Taxi Trip consistem em cerca de 20 GB de ficheiros CSV comprimidos (~48 GB descomprimidos), registando mais de 173 milhões de viagens individuais e as tarifas pagas por cada viagem. Cada registo de viagem inclui os locais e horários de recolha e entrega, o número da carta de condução (motorista) anonimizada e o número do medalhão (ID único do táxi). Os dados cobrem todas as viagens do ano de 2013 e são fornecidos nos dois conjuntos de dados seguintes para cada mês:

1. O ficheiro **trip_data.csv** contém detalhes da viagem, tais como o número de passageiros, pontos de recolha e entrega, duração da viagem e duração da viagem. Aqui estão alguns registos de amostras:

`medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. O **ficheirotrip_fare.csv** contém detalhes da tarifa paga por cada viagem, como tipo de pagamento, valor da tarifa, sobretaxa e impostos, gorjetas e portagens, e o valor total pago. Aqui estão alguns registos de amostras:

`medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

A **chave única** usada para juntar dados de viagem e tarifa de \_ viagem é composta pelos \_ seguintes três campos:

* medalhão,
* licença de hack \_ e
* \_hora de recolha.

## <a name="address-three-types-of-prediction-tasks"></a><a name="mltasks"></a>Endereçar três tipos de tarefas de previsão
Formulamos três problemas de previsão com base na *\_ quantidade* de ponta para ilustrar três tipos de tarefas de modelação:

1. **Classificação binária**: Prever se uma gorjeta foi ou não paga por uma viagem, isto é, um *\_ valor* de gorjeta superior a $0 é um exemplo positivo, enquanto um *\_ valor* de gorjeta de $0 é um exemplo negativo.
2. **Classificação multiclasse**: Para prever o intervalo de gorjeta paga pela viagem. Dividimos a *\_ quantidade de gorjeta* em cinco caixotes ou classes:

`Class 0 : tip_amount = $0`

`Class 1 : tip_amount > $0 and tip_amount <= $5`

`Class 2 : tip_amount > $5 and tip_amount <= $10`

`Class 3 : tip_amount > $10 and tip_amount <= $20`

`Class 4 : tip_amount > $20`

3. **Tarefa de regressão**: Prever o valor da gorjeta paga por uma viagem.

## <a name="set-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Criar o ambiente de ciência de dados Azure para análises avançadas
Para configurar o seu ambiente Azure Data Science, siga estes passos.

**Crie a sua própria conta de armazenamento de blob Azure**

* Quando forte o seu próprio armazenamento de blob Azure, escolha um geolocalização para o seu armazenamento de bolhas Azure em ou o mais próximo possível **do South Central US,** que é onde os dados do Táxi NYC estão armazenados. Os dados serão copiados utilizando o AzCopy do recipiente de armazenamento de bolhas públicas para um recipiente na sua própria conta de armazenamento. Quanto mais perto estiver o seu armazenamento de blob Azure para south central eua, mais rápido esta tarefa (Passo 4) será concluída.
* Para criar a sua própria conta de Armazenamento Azure, siga os passos descritos nas [contas de Armazenamento About Azure](../../storage/common/storage-account-create.md). Certifique-se de tomar notas sobre os valores para seguir as credenciais de conta de armazenamento, uma vez que serão necessários mais tarde nesta passagem.

  * **Nome da conta de armazenamento**
  * **Chave da conta de armazenamento**
  * **Nome do recipiente** (que pretende que os dados sejam armazenados no armazenamento da bolha Azure)

**Provisionar o seu exemplo Azure Synapse Analytics.**
Siga a documentação na [Create e questione um Azure Synapse Analytics no portal Azure](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md) para obter uma instância Azure Synapse Analytics. Certifique-se de que faz notações nas seguintes credenciais Azure Synapse Analytics que serão usadas em etapas posteriores.

* **Nome do servidor**: \<server Name> .database.windows.net
* **Nome SQLDW (Base de Dados)**
* **Nome de Utilizador**
* **Palavra-passe**

**Instale ferramentas de dados do Estúdio Visual e do Servidor SQL.** Para obter instruções, consulte [Começar com o Visual Studio 2019 para a Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Ligue-se ao Azure Synapse Analytics com o Visual Studio.** Para obter instruções, consulte os passos 1 & 2 em [Connect to SQL Analytics in Azure Synapse Analytics](../../synapse-analytics/sql/connect-overview.md).

> [!NOTE]
> Faça a seguinte consulta SQL na base de dados que criou no Azure Synapse Analytics (em vez da consulta fornecida no passo 3 do tópico de ligação,) para **criar uma chave mestra**.
>
>

```sql
BEGIN TRY
       --Try to create the master key
    CREATE MASTER KEY
END TRY
BEGIN CATCH
       --If the master key exists, do nothing
END CATCH;
```

**Crie um espaço de trabalho Azure Machine Learning sob a sua assinatura Azure.** Para obter instruções, consulte [Criar um espaço de trabalho para aprendizagem de máquinas Azure](../classic/create-workspace.md).

## <a name="load-the-data-into-azure-synapse-analytics"></a><a name="getdata"></a>Carregue os dados em Azure Synapse Analytics
Abra uma consola de comando Windows PowerShell. Execute os seguintes comandos PowerShell para descarregar os ficheiros de scripts SQL exemplo que partilhamos consigo no GitHub para um diretório local que especifica com o parâmetro *-DestDir*. Pode alterar o valor do parâmetro *-DestDir* para qualquer diretório local. Se *-DestDir* não existir, será criado pelo script PowerShell.

> [!NOTE]
> Poderá ter de **executar como Administrador** ao executar o seguinte script PowerShell se o seu diretório *DestDir* necessitar de privilégio de Administrador para criar ou escrever para ele.
>
>

```azurepowershell
$source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
$ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
$wc = New-Object System.Net.WebClient
$wc.DownloadFile($source, $ps1_dest)
.\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'
```

Após a execução bem sucedida, o seu diretório de trabalho atual muda para *-DestDir*. Você deve ser capaz de ver o ecrã como abaixo:

![Mudanças de diretório de trabalho atuais][19]

No seu *-DestDir,* execute o seguinte script PowerShell no modo de administrador:

```azurepowershell
./SQLDW_Data_Import.ps1
```

Quando o script PowerShell for executado pela primeira vez, ser-lhe-á pedido que insira as informações da sua Azure Synapse Analytics e da sua conta de armazenamento de blob Azure. Quando este script PowerShell terminar em execução pela primeira vez, as credenciais que insere terão sido escritas para um ficheiro de configuração SQLDW.conf no atual diretório de trabalho. A futura execução deste ficheiro de script PowerShell tem a opção de ler todos os parâmetros necessários a partir deste ficheiro de configuração. Se precisar de alterar alguns parâmetros, pode optar por inserir os parâmetros no ecrã após a introdução deste ficheiro de configuração e introduzindo os valores dos parâmetros conforme solicitado ou alterar os valores dos parâmetros editando o ficheiro SQLDW.conf no seu diretório *-DestDir.*

> [!NOTE]
> Para evitar conflitos de nomes de esquema com os que já existem no seu Azure Azure Synapse Analytics, ao ler parâmetros diretamente do ficheiro SQLDW.conf, é adicionado um número aleatório de 3 dígitos ao nome de esquema do ficheiro SQLDW.conf como o nome de esquema padrão para cada execução. O script PowerShell pode solicitar-lhe um nome de esquema: o nome pode ser especificado a critério do utilizador.
>
>

Este ficheiro **de script PowerShell** completa as seguintes tarefas:

* **Descarrega e instala a AzCopy,** se o AzCopy ainda não estiver instalado

  ```azurepowershell
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
  ```

* **Copia dados para a sua conta de armazenamento de bolhas privadas** da bolha pública com AzCopy

  ```azurepowershell
  Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
  $start_time = Get-Date
  AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
  $end_time = Get-Date
  $time_span = $end_time - $start_time
  $total_seconds = [math]::Round($time_span.TotalSeconds,2)
  Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
  Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
  ```

* **Carrega os dados utilizando a Polybase (executando LoadDataToSQLDW.sql) para o seu Azure Synapse Analytics** a partir da sua conta de armazenamento de bolhas privadas com os seguintes comandos.

  * Criar um esquema

    ```sql
    EXEC (''CREATE SCHEMA {schemaname};'');
    ```

  * Criar uma credencial de âmbito de base de dados

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
    WITH IDENTITY = ''asbkey'' ,
    Secret = ''{StorageAccountKey}''
    ```

  * Criar uma fonte de dados externa para uma bolha de armazenamento Azure

    ```sql
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
    ```

  * Crie um formato de ficheiro externo para um ficheiro CSV. Os dados não são recomprimidos e os campos são separados com o caractere do tubo.

    ```sql
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
    ```

  * Crie tabelas de tarifas e de viagem externas para conjunto de dados de táxi nyc no armazenamento de bolhas Azure.

    ```sql
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
    ```

    - Carregar dados de tabelas externas no armazenamento de blob Azure para Azure Synapse Analytics

      ```sql
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
      ```

    - Crie uma tabela de dados de amostra (NYCTaxi_Sample) e insira-lhe dados a partir da seleção de consultas SQL nas tabelas de viagem e tarifas. (Alguns passos desta passagem precisam de usar esta tabela de amostras.)

      ```sql
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
      ```

A localização geográfica das suas contas de armazenamento afeta os tempos de carga.

> [!NOTE]
> Dependendo da localização geográfica da sua conta de armazenamento de bolhas privadas, o processo de cópia de dados de uma bolha pública para a sua conta de armazenamento privado pode demorar cerca de 15 minutos, ou até mais, e o processo de carregamento de dados da sua conta de armazenamento para o Azure Azure Synapse Analytics pode demorar 20 minutos ou mais.
>
>

Terá de decidir o que fazer se tiver ficheiros duplicados de origem e destino.

> [!NOTE]
> Se os ficheiros .csv a serem copiados do armazenamento de bolhas públicas para a sua conta de armazenamento de bolhas privadas já existem na sua conta de armazenamento de bolhas privadas, a AzCopy perguntar-lhe-á se pretende sobrepor-se a eles. Se não quiser exagerar, insira **n** quando solicitado. Se quiser substituir **todos,** insira **um** quando solicitado. Também pode inserir **y** para substituir ficheiros .csv individualmente.
>
>

![Saída da AzCopy][21]

Pode usar os seus próprios dados. Se os seus dados estiverem na sua máquina de segurança na sua aplicação de vida real, ainda pode utilizar a AzCopy para enviar dados no local para o seu armazenamento privado de bolhas Azure. Basta alterar a  localização `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"` Source, no comando AzCopy do ficheiro de script PowerShell para o diretório local que contém os seus dados.

> [!TIP]
> Se os seus dados já estiverem no seu armazenamento privado de blob Azure na sua aplicação real, pode saltar o passo AzCopy no script PowerShell e fazer o upload direto dos dados para a Azure Azure Synapse Analytics. Isto requer edições adicionais do script para adaptá-lo ao formato dos seus dados.
>
>

Este script PowerShell também liga as informações do Azure Synapse Analytics nos ficheiros de exemplo de exploração de dados SQLDW_Explorations.sql, SQLDW_Explorations.ipynb e SQLDW_Explorations_Scripts.py para que estes três ficheiros estejam prontos a ser testados instantaneamente após o script PowerShell estar concluído.

Após uma execução bem sucedida, verá o ecrã como abaixo:

![Saída de uma execução de script bem sucedida][20]

## <a name="data-exploration-and-feature-engineering-in-azure-synapse-analytics"></a><a name="dbexplore"></a>Exploração de dados e engenharia de recursos em Azure Synapse Analytics
Nesta secção, realizamos a exploração de dados e geração de recursos executando consultas SQL contra a Azure Synapse Analytics usando diretamente **Ferramentas de Dados do Estúdio Visual**. Todas as consultas SQL utilizadas nesta secção podem ser encontradas no script da amostra denominado *SQLDW_Explorations.sql*. Este ficheiro já foi descarregado para o seu diretório local pelo script PowerShell. Também pode recuperá-lo do [GitHub.](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql) Mas o ficheiro no GitHub não tem a informação do Azure Synapse Analytics ligada.

Ligue-se ao Azure Synapse Analytics utilizando o Visual Studio com o nome de login e palavra-passe Azure Synapse Analytics e abra o **SQL Object Explorer** para confirmar que a base de dados e as tabelas foram importadas. Recupere o ficheiro *SQLDW_Explorations.sql.*

> [!NOTE]
> Para abrir um editor de consulta do Armazém de Dados Paralelos (PDW), utilize o comando **New Query** enquanto o seu PDW é selecionado no **SQL Object Explorer**. O editor de consulta padrão SQL não é suportado pela PDW.
>
>

Aqui estão os tipos de tarefas de exploração de dados e geração de recursos realizadas nesta secção:

* Explore a distribuição de dados de alguns campos em janelas de tempo variadas.
* Investigue a qualidade dos dados dos campos de longitude e latitude.
* Gere etiquetas de classificação binárias e multiclasses com base na **\_ quantidade de gorjeta**.
* Gere características e computação/compare distâncias de viagem.
* Junte-se às duas tabelas e extraa uma amostra aleatória que será usada para construir modelos.

### <a name="data-import-verification"></a>Verificação das importações de dados
Estas consultas proporcionam uma rápida verificação do número de linhas e colunas nas tabelas povoadas anteriormente utilizando a importação paralela de massas da Polybase,

-- Reportar o número de linhas na tabela <nyctaxi_trip> sem digitalização de mesa

   ```sql
   SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
   ```

- Reportar o número de colunas na tabela <nyctaxi_trip>

   ```sql
   SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'
   ```

**Saída:** Deve ter 173.179.759 filas e 14 colunas.

### <a name="exploration-trip-distribution-by-medallion"></a>Exploração: Distribuição de viagem por medalhão
Este exemplo identifica os medalhão (números de táxi) que completaram mais de 100 viagens num período de tempo especificado. A consulta beneficiaria do acesso à mesa dividido, uma vez que está condicionada pelo esquema de partição da **\_ data** de recolha . A consulta do conjunto de dados completo também utilizará a tabela dividida e/ou a varredura de índice.

```sql
SELECT medallion, COUNT(*)
FROM <schemaname>.<nyctaxi_fare>
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
GROUP BY medallion
HAVING COUNT(*) > 100
```

**Saída:** A consulta deverá devolver uma tabela com filas que especificam os 13.369 medalhão (táxis) e o número de viagens concluídas em 2013. A última coluna contém a contagem do número de viagens concluídas.

### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Exploração: Distribuição de viagem por medalhão e hack_license
Este exemplo identifica os medalhão (números de táxi) e hack_license números (motoristas) que completaram mais de 100 viagens num período de tempo especificado.

```sql
SELECT medallion, hack_license, COUNT(*)
FROM <schemaname>.<nyctaxi_fare>
WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
GROUP BY medallion, hack_license
HAVING COUNT(*) > 100
```

**Saída:** A consulta deverá devolver uma tabela com 13.369 filas especificando os 13.369 IDs de carro/condutor que completaram mais de 100 viagens em 2013. A última coluna contém a contagem do número de viagens concluídas.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Avaliação da qualidade dos dados: Verificar registos com longitude e/ou latitude incorretas
Este exemplo investiga se algum dos campos de longitude e/ou latitude contém um valor inválido (os graus radian devem estar entre -90 e 90), ou têm (0,0) coordenadas.

```sql
SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
OR    (pickup_longitude = '0' AND pickup_latitude = '0')
OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))
```

**Saída:** A consulta devolve 837.467 viagens que têm campos de longitude e/ou latitude inválidos.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Exploração: Tiped vs. não tiped viagens distribuição
Este exemplo encontra o número de viagens que foram inclinadas vs. o número que não foram inclinados num determinado período de tempo (ou em conjunto de dados completos se cobrir o ano inteiro tal como está configurado aqui). Esta distribuição reflete a distribuição da etiqueta binária para ser posteriormente utilizada para modelação de classificação binária.

```sql
SELECT tipped, COUNT(*) AS tip_freq FROM (
  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
  FROM <schemaname>.<nyctaxi_fare>
  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tipped
```

**Saída:** A consulta deverá devolver as seguintes frequências de gorjeta para o ano de 2013: 90.447.622 inclinados e 82.264.709 não-tiped.

### <a name="exploration-tip-classrange-distribution"></a>Exploração: Distribuição classe/gama de ponta
Este exemplo calcula a distribuição das gamas de ponta num determinado período de tempo (ou no conjunto de dados completo se cobrir o ano inteiro). Esta distribuição de classes de rótulos será usada mais tarde para modelação de classificação multiclasse.

```sql
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
```

**Saída:**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Exploração: Calcular e comparar distância de viagem
Este exemplo converte a longitude e a latitude de pickup e dropoff para pontos de geografia SQL, calcula a distância de viagem usando a diferença de pontos de geografia SQL, e devolve uma amostra aleatória dos resultados para comparação. O exemplo limita os resultados a coordenadas válidas apenas utilizando a consulta de avaliação da qualidade dos dados abrangida anteriormente.

```sql
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
```

### <a name="feature-engineering-using-sql-functions"></a>Engenharia de recursos utilizando funções SQL
Por vezes, as funções SQL podem ser uma opção eficiente para a engenharia de recursos. Nesta passagem, definimos uma função SQL para calcular a distância direta entre os locais de recolha e entrega. Pode executar os seguintes scripts SQL em **Ferramentas de Dados do Estúdio Visual.**

Aqui está o script SQL que define a função distância.

```sql
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
```

Aqui está um exemplo para chamar esta função para gerar funcionalidades na sua consulta SQL:

-- Consulta de amostra para chamar a função para criar funcionalidades

   ```sql
SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
FROM <schemaname>.<nyctaxi_trip>
WHERE datepart("mi",pickup_datetime)=1
AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
AND pickup_longitude != '0' AND dropoff_longitude != '0'
   ```

**Saída:** Esta consulta gera uma tabela (com 2.803.538 linhas) com latitudes de recolha e entrega e longitudes e as distâncias diretas correspondentes em milhas. Aqui estão os resultados das três primeiras linhas:

| (Número de linha) | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | Direcciondance |
| --- | --- | --- | --- | --- | --- |
| 1 |40.731804 |-74.001083 |40.736622 |-73.988953 |.7169601222 |
| 2 |40.715794 |-74,010635 |40.725338 |-74.00399 |.7448343721 |
| 3 |40.761456 |-73.999886 |40.766544 |-73.988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Preparar dados para a construção de modelos
A seguinte consulta junta-se às tabelas **de tarifas nyctaxi \_ e** **nyctaxi, \_** gera uma etiqueta de classificação binária **inclinada**, uma **\_ classe** de ponta de etiqueta de classificação multi-classe , e extrai uma amostra do conjunto de dados completo. A amostragem é feita recuperando um subconjunto das viagens com base no tempo de recolha.  Esta consulta pode ser copiada e colada diretamente no [Azure Machine Learning Studio (clássico)](https://studio.azureml.net) Módulo[de importação de dados de importação de] [dados]para ingestão direta de dados a partir da instância SQL Database em Azure. A consulta exclui registos com coordenadas incorretas (0,0).

```sql
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
```

Quando estiver pronto para prosseguir para a Azure Machine Learning, pode:

1. Guarde a consulta final do SQL para extrair e recolher os dados e copiar a consulta diretamente num módulo[de dados de importação] em Azure Machine Learning, ou
2. Persista os dados recolhidos e projetados que pretende utilizar para a construção de modelos numa nova tabela Azure Synapse Analytics e utilize a nova tabela no módulo[de dados de importação de dados de] importação de [dados de importação]em Azure Machine Learning. O script PowerShell em passo anterior fez esta tarefa por si. Pode ler diretamente a partir desta tabela no módulo De Dados de Importação.

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Exploração de dados e engenharia de recursos no caderno IPython
Nesta secção, realizaremos a exploração de dados e geração de recursos utilizando consultas python e SQL contra o Azure Synapse Analytics criado anteriormente. Um caderno IPython chamado **SQLDW_Explorations.ipynb** e um ficheiro de script Python **SQLDW_Explorations_Scripts.py** foram transferidos para o seu diretório local. Também estão disponíveis no [GitHub.](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW) Estes dois ficheiros são idênticos nos scripts python. O ficheiro de script Python é-lhe fornecido caso não tenha um servidor de Caderno IPython. Estes dois ficheiros Python de amostra são projetados sob **Python 2.7**.

As informações necessárias do Azure Synapse Analytics na amostra IPython Notebook e no ficheiro de script Python descarregado para a sua máquina local foram previamente ligadas pelo script PowerShell. São executáveis sem qualquer modificação.

Se já criou um espaço de trabalho para aprendizagem automática Azure, pode enviar diretamente o caderno IPython da amostra para o serviço AzureML IPython Notebook e começar a executá-lo. Aqui estão os passos para fazer o upload para o serviço AzureML IPython Notebook:

1. Inicie sessão no seu espaço de trabalho Azure Machine Learning, clique em **Studio"** no topo e clique em **NOTEBOOKS** no lado esquerdo da página web.

    ![Clique em Estúdio e depois NOTEBOOKS][22]
2. Clique em **NEW** no canto inferior esquerdo da página web e selecione **Python 2**. Em seguida, forneça um nome ao bloco de notas e clique na marca de verificação para criar o novo caderno IPython em branco.

    ![Clique em NEW e, em seguida, selecione Python 2][23]
3. Clique no símbolo **Jupyter** no canto superior esquerdo do novo Caderno IPython.

    ![Clique no símbolo do Jupyter][24]
4. Arraste e deixe cair a amostra IPython Notebook para a página de **árvore** do seu serviço de caderno AzureML IPython e clique em **Upload**. Em seguida, a amostra IPython Notebook será enviada para o serviço AzureML IPython Notebook.

    ![Clique em Upload][25]

Para executar a amostra IPython Notebook ou o ficheiro de script Python, são necessários os seguintes pacotes Python. Se estiver a utilizar o serviço AzureML IPython Notebook, estes pacotes foram pré-instalados.

- pandas
- numpy
- matplotlib
- pyodbc
- PyTables

Ao construir soluções analíticas avançadas na Azure Machine Learning com dados grandes, aqui está a sequência recomendada:

* Leia numa pequena amostra dos dados num quadro de dados na memória.
* Execute algumas visualizações e explorações utilizando os dados amostrados.
* Experimente a engenharia de recursos utilizando os dados amostrados.
* Para uma maior exploração de dados, manipulação de dados e engenharia de recursos, utilize python para emitir consultas SQL diretamente contra o Azure Synapse Analytics.
* Decida o tamanho da amostra adequado para o edifício do modelo Azure Machine Learning.

Os seguintes são alguns dados de exploração, visualização de dados e exemplos de engenharia de recursos. Mais explorações de dados podem ser encontradas na amostra IPython Notebook e no ficheiro de script python da amostra.

### <a name="initialize-database-credentials"></a>Inicializar credenciais de base de dados
Inicialize as definições de ligação da sua base de dados nas seguintes variáveis:

```sql
SERVER_NAME=<server name>
DATABASE_NAME=<database name>
USERID=<user name>
PASSWORD=<password>
DB_DRIVER = <database driver>
```

### <a name="create-database-connection"></a>Criar ligação de base de dados
Aqui está a cadeia de ligação que cria a ligação à base de dados.

```sql
CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
conn = pyodbc.connect(CONNECTION_STRING)
```

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Reportar o número de linhas e colunas na tabela <nyctaxi_trip>

```sql
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
```

* Número total de linhas = 173179759
* Número total de colunas = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_fare"></a>Reportar o número de linhas e colunas na tabela <nyctaxi_fare>

```sql
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
```

* Número total de linhas = 173179759
* Número total de colunas = 11

### <a name="read-in-a-small-data-sample-from-the-azure-synapse-analytics-database"></a>Read-in uma pequena amostra de dados da Base de Dados Azure Synapse Analytics

```sql
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
```

A hora de ler a tabela de amostras é de 14.096495 segundos.
Número de linhas e colunas recuperadas = (1000, 21).

### <a name="descriptive-statistics"></a>Estatísticas descritivas
Agora está pronto para explorar os dados recolhidos. Começamos por analisar algumas estatísticas descritivas para a distância de **viagem \_** (ou quaisquer outros campos que escolha especificar).

```sql
df1['trip_distance'].describe()
```

### <a name="visualization-box-plot-example"></a>Visualização: Exemplo de enredo de caixa
Em seguida, olhamos para o enredo da caixa para a distância de viagem para visualizar os quantiles.

```sql
df1.boxplot(column='trip_distance',return_type='dict')
```

![Saída do enredo da caixa][1]

### <a name="visualization-distribution-plot-example"></a>Visualização: Exemplo do enredo de distribuição
Parcelas que visualizam a distribuição e um histograma para as distâncias de viagem amostradas.

```sql
fig = plt.figure()
ax1 = fig.add_subplot(1,2,1)
ax2 = fig.add_subplot(1,2,2)
df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
df1['trip_distance'].hist(ax=ax2, bins=100, color='k')
```

![Saída do enredo de distribuição][2]

### <a name="visualization-bar-and-line-plots"></a>Visualização: Parcelas de barras e linhas
Neste exemplo, colocamos a distância de viagem em cinco caixotes e visualizamos os resultados de vinculação.

```sql
trip_dist_bins = [0, 1, 2, 4, 10, 1000]
df1['trip_distance']
trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
trip_dist_bin_id
```

Podemos traçar a distribuição do caixote do lixo acima em um bar ou enredo de linha com:

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')
```

![Saída do enredo do bar][3]

e

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')
```

![Saída do enredo de linha][4]

### <a name="visualization-scatterplot-examples"></a>Visualização: Exemplos de scatterplot
Mostramos o enredo de dispersão entre **o tempo de viagem em \_ \_ \_ segundos** e a distância da **viagem \_** para ver se há alguma correlação

```sql
plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])
```

![Dispersão da produção da relação entre o tempo e a distância][6]

Da mesma forma, podemos verificar a relação entre o **\_ código de taxa** e **a distância \_ de viagem.**

```sql
plt.scatter(df1['passenger_count'], df1['trip_distance'])
```

![Dispersão da produção da relação entre código e distância][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Exploração de dados em dados amostrados usando consultas SQL em caderno IPython
Nesta secção, exploramos as distribuições de dados utilizando os dados amostrados que persistem na nova tabela que criamos acima. Explorações semelhantes podem ser realizadas usando as tabelas originais.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Exploração: Número de linhas e colunas na tabela amostrada

```sql
nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
print 'Number of rows in sample = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
print 'Number of columns in sample = %d' % ncols.iloc[0,0]
```

#### <a name="exploration-tippednot-tripped-distribution"></a>Exploração: Distribuição com ponta/não tropeçada

```sql
query = '''
SELECT tipped, count(*) AS tip_freq
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY tipped
    '''

    pd.read_sql(query, conn)
```

#### <a name="exploration-tip-class-distribution"></a>Exploração: Distribuição de classe de ponta

```sql
query = '''
    SELECT tip_class, count(*) AS tip_freq
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY tip_class
'''

tip_class_dist = pd.read_sql(query, conn)
```

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Exploração: Traçar a distribuição da ponta por classe

```sql
tip_class_dist['tip_freq'].plot(kind='bar')
```

![#26 do enredo][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Exploração: Distribuição diária de viagens

```sql
query = '''
    SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY CONVERT(date, dropoff_datetime)
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-per-medallion"></a>Exploração: Distribuição de viagem por medalhão

```sql
query = '''
    SELECT medallion,count(*) AS c
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY medallion
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Exploração: Distribuição de viagem por medalhão e licença de hack

```sql
query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
pd.read_sql(query,conn)
```

#### <a name="exploration-trip-time-distribution"></a>Exploração: Distribuição do tempo de viagem

```sql
query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distance-distribution"></a>Exploração: Distribuição à distância de viagem

```sql
query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
pd.read_sql(query,conn)
```

#### <a name="exploration-payment-type-distribution"></a>Exploração: Distribuição do tipo de pagamento

```sql
query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
pd.read_sql(query,conn)
```

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Verifique a forma final da tabela apresentando

```sql
query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
pd.read_sql(query,conn)
```

## <a name="build-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Construa modelos em Azure Machine Learning
Estamos agora prontos para avançar para a construção de modelos e implantação de modelos em [Azure Machine Learning.](https://studio.azureml.net) Os dados estão prontos para serem utilizados em qualquer um dos problemas de previsão identificados anteriormente, nomeadamente:

1. **Classificação binária**: Para prever se uma gorjeta foi ou não paga por uma viagem.
2. **Classificação multiclasse**: Para prever o intervalo de gorjeta paga, de acordo com as classes previamente definidas.
3. **Tarefa de regressão**: Prever o valor da gorjeta paga por uma viagem.

Para iniciar o exercício de modelação, inicie sessão no seu espaço de trabalho **Azure Machine Learning (clássico).** Se ainda não criou um espaço de trabalho de aprendizagem automática, consulte [Create a Azure Machine Learning Studio (clássico) espaço de trabalho](../classic/create-workspace.md).

1. Para começar com a Azure Machine Learning, veja [o que é O Azure Machine Learning Studio (clássico)?](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
2. Faça login no [Azure Machine Learning Studio (clássico)](https://studio.azureml.net).
3. A página inicial do Machine Learning Studio (clássico) fornece uma grande quantidade de informações, vídeos, tutoriais, links para a Referência de Módulos e outros recursos. Para obter mais informações sobre a Azure Machine Learning, consulte o [Centro de Documentação de Aprendizagem de Máquinas Azure.](https://azure.microsoft.com/documentation/services/machine-learning/)

Uma experiência de treino típica consiste nos seguintes passos:

1. Crie uma experiência **+NEW.**
2. Obtenha os dados no Azure Machine Learning Studio (clássico).
3. Pré-processar, transformar e manipular os dados conforme necessário.
4. Gerar características conforme necessário.
5. Divida os dados em conjuntos de dados de formação/validação/teste (ou tenha conjuntos de dados separados para cada um).
6. Selecione um ou mais algoritmos de aprendizagem automática dependendo do problema de aprendizagem para resolver. Por exemplo, classificação binária, classificação multiclasse, regressão.
7. Treine um ou mais modelos utilizando o conjunto de dados de treino.
8. Marque o conjunto de dados de validação utilizando o(s) modelo(s) treinado.
9. Avaliar os modelos para calcular as métricas relevantes para o problema de aprendizagem.
10. Sintonize os modelos e selecione o melhor modelo a ser implantado.

Neste exercício, já exploramos e concebemos os dados no Azure Synapse Analytics, e decidimos sobre o tamanho da amostra para ingerir no Azure Machine Learning Studio (clássico). Aqui está o procedimento para construir um ou mais dos modelos de previsão:

1. Obtenha os dados no Azure Machine Learning Studio (clássico) utilizando o módulo [de dados de importação de dados de] importação de [dados de importação,]disponível na secção entrada e saída de **dados.** Para obter mais informações, consulte a página de referência do módulo[de dados de importação de dados de importação de dados de] importação de [dados de importação.]

    ![Dados de importação de Azure ML][17]
2. Selecione **Azure SQL Database** como **fonte de dados** no painel **Propriedades.**
3. Introduza o nome DNS da base de dados no campo **de nome do servidor Database.** Formato: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Introduza o **nome base de dados** no campo correspondente.
5. Introduza o *nome de utilizador SQL* no nome da **conta de utilizador** do Servidor e na *palavra-passe* da **conta de utilizador** do Servidor .
7. Na **área de consulta de base de dados** editar a área de texto, cole a consulta que extrai os campos de base de dados necessários (incluindo quaisquer campos calculados, como os rótulos) e diminui as amostras dos dados para o tamanho da amostra pretendido.

Um exemplo de uma experiência de classificação binária que lê dados diretamente da base de dados Azure Synapse Analytics está na figura abaixo (lembre-se de substituir os nomes de tabelas nyctaxi_trip e nyctaxi_fare pelo nome de esquema e pelos nomes de tabela que usou na sua passagem). Experiências semelhantes podem ser construídas para problemas de classificação e regressão multiclasse.

![Comboio Azure ML][10]

> [!IMPORTANT]
> Nos exemplos de extração de dados de modelação e de amostragem fornecidos nas secções anteriores, **todas as etiquetas para os três exercícios de modelação estão incluídas na consulta**. Um passo importante (necessário) em cada um dos exercícios de modelação consiste em **excluir** os rótulos desnecessários para os outros dois **problemas,** e quaisquer outras fugas de alvo . Por exemplo, ao utilizar a classificação binária, utilize a etiqueta **inclinada** e exclua a **classe \_ de ponta** dos campos, **a quantidade \_ de gorjeta** e **a \_ quantidade total**. Estes últimos são fugas de alvo, uma vez que implicam a gorjeta paga.
>
> Para excluir quaisquer colunas ou fugas de alvo desnecessárias, pode utilizar as [Colunas Selecionadas no][select-columns] módulo Dataset ou nos [Metadados de Edição.][edit-metadata] Para obter mais informações, consulte [Colunas selecionadas em páginas de][select-columns] referência de datas e [edite metadados.][edit-metadata]
>
>

## <a name="deploy-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Implementar modelos em Azure Machine Learning
Quando o seu modelo estiver pronto, pode facilmente implantá-lo como um serviço web diretamente da experiência. Para obter mais informações sobre a implementação de serviços web Azure ML, consulte [implementar um serviço web Azure Machine Learning](../classic/deploy-a-machine-learning-web-service.md).

Para implementar um novo serviço web, precisa:

1. Criar uma experiência de pontuação.
2. Implemente o serviço web.

Para criar uma experiência de pontuação a partir de uma experiência de treino **acabada,** clique em **CREATE SCORING EXPERIMENT** na barra de ação inferior.

![Azure Marcação][18]

A Azure Machine Learning tentará criar uma experiência de pontuação baseada nos componentes da experiência de treino. Em particular, irá:

1. Guarde o modelo treinado e remova os módulos de treino do modelo.
2. Identifique uma porta de **entrada** lógica para representar o esquema de dados de entrada esperado.
3. Identifique uma **porta de saída** lógica para representar o esquema de saída esperado do serviço web.

Quando a experiência de pontuação for criada, reveja os resultados e faça o ajuste conforme necessário. Um ajuste típico é substituir o conjunto de dados de entrada ou consulta por um que exclui os campos de etiquetas, porque estes campos de etiquetas não serão mapeados para o esquema quando ligarem para o serviço. É também uma boa prática reduzir o tamanho do conjunto de dados de entrada e/ou consulta para alguns registos, o suficiente para indicar o esquema de entrada. Para a porta de saída, é comum excluir todos os campos de entrada e incluir apenas as **etiquetas pontuadas** e **as probabilidades pontuadas** na saída utilizando as [Colunas Selecionadas no módulo Dataset.][select-columns]

Uma experiência de pontuação de amostra é fornecida na figura abaixo. Quando estiver pronto para ser implantado, clique no botão **PUBLISH WEB SERVICE** na barra de ação inferior.

![Azure ML Publish][11]

## <a name="summary"></a>Resumo
Para recapitular o que fizemos neste tutorial walkthrough, criou um ambiente de ciência de dados Azure, trabalhou com um grande conjunto de dados públicos, levando-o através do Processo de Ciência de Dados de Equipa, desde a aquisição de dados até à formação de modelos, e depois à implantação de um serviço web Azure Machine Learning.

### <a name="license-information"></a>Informações sobre licenças
Esta amostra e os seus scripts que acompanham e os portátils IPython são partilhados pela Microsoft sob a licença do MIT. Verifique o ficheiro LICENSE.txt no diretório do código de amostra no GitHub para obter mais detalhes.

## <a name="references"></a>Referências
- [Página de descarregamento de viagens de táxi de Andrés Monroy NYC](https://www.andresmh.com/nyctaxitrips/)
- [FOILing NYC's Taxi Trip Data by Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)
- [Investigação e Estatística da Comissão de Táxis e Limusines de NYC](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

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
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[importar dados]: /azure/machine-learning/studio-module-reference/import-data