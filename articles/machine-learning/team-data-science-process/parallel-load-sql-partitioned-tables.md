---
title: Importação paralela de dados a granel em tabelas de partição SQL - Processo de Ciência de Dados de Equipa
description: Construa tabelas divididas para a importação rápida paralela de dados para uma base de dados do SQL Server.
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
ms.openlocfilehash: 673a801e218d055bf482dc97972e36584cddd402
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721341"
---
# <a name="build-and-optimize-tables-for-fast-parallel-import-of-data-into-a-sql-server-on-an-azure-vm"></a>Construir e otimizar tabelas para a importação rápida paralela de dados num Servidor SQL num VM Azure

Este artigo descreve como construir tabelas divididas para a importação rápida paralela de dados para uma base de dados do SQL Server. Para o carregamento/transferência de dados grandes para uma base de dados SQL, a importação de dados para o SQL DB e consultas subsequentes pode ser melhorada utilizando *tabelas e pontos*de vista divididos . 

## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Criar uma nova base de dados e um conjunto de grupos de arquivos
* [Crie uma nova base de dados,](https://technet.microsoft.com/library/ms176061.aspx)se já não existir.
* Adicione os ficheiros de base de dados à base de dados, que detém os ficheiros físicos divididos. 
* Isto pode ser feito com base de [dados CREATE](https://technet.microsoft.com/library/ms176061.aspx) se a base de dados de [alteração](https://msdn.microsoft.com/library/bb522682.aspx) já existir.
* Adicione um ou mais ficheiros (conforme necessário) a cada grupo de ficheiros de base de dados.
  
  > [!NOTE]
  > Especifique o grupo de ficheiros alvo, que detém dados para esta partilha e o nome ou ficheiro s da base de dados física onde os dados do grupo de ficheiros são armazenados.
  > 
  > 

O exemplo seguinte cria uma nova base de dados com três grupos de ficheiros que não os grupos primários e de registo, contendo um ficheiro físico em cada um. Os ficheiros de base de dados são criados na pasta de Dados do Servidor SQL predefinido, tal como configurado na instância do Servidor SQL. Para obter mais informações sobre as localizações de ficheiros predefinidos, consulte localizações de [ficheiros para Padrão e Instâncias Nomeadas do Servidor SQL](https://msdn.microsoft.com/library/ms143547.aspx).

    DECLARE @data_path nvarchar(256);
    SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);

    EXECUTE ('
        CREATE DATABASE <database_name>
         ON  PRIMARY 
        ( NAME = ''Primary'', FILENAME = ''' + @data_path + '<primary_file_name>.mdf'', SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_1] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_1>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_2] 
        ( NAME = ''FileGroup2'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup3'', FILENAME = ''' + @data_path + '<file_name_3>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ) 
         LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')

## <a name="create-a-partitioned-table"></a>Criar uma mesa dividida
Para criar quadros divididos de acordo com o esquema de dados, mapeado para os grupos de ficheiros de base de dados criados na fase anterior, deve primeiro criar uma função e esquema de partição. Quando os dados são importados a granel para a ou os quadros divididos, os registos são distribuídos entre os grupos de ficheiros de acordo com um esquema de partição, conforme descrito abaixo.

### <a name="1-create-a-partition-function"></a>1. Criar uma função de partição
[Criar uma função de partição](https://msdn.microsoft.com/library/ms187802.aspx) Esta função define a gama de valores/limites a incluir em cada tabela individual de\_partição, por exemplo, para limitar as divisórias por mês (algum campo de data)\_no ano de 2013:
  
        CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
        AS RANGE RIGHT FOR VALUES (
            '20130201', '20130301', '20130401',
            '20130501', '20130601', '20130701', '20130801',
            '20130901', '20131001', '20131101', '20131201' )

### <a name="2-create-a-partition-scheme"></a>2. Criar um regime de partição
Criar um esquema de [partição.](https://msdn.microsoft.com/library/ms179854.aspx) Este esquema mapeia cada gama de divisórias na função de partição para um grupo de ficheiros físicos, por exemplo:
  
        CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )
  
  Para verificar as gamas em vigor em cada divisória de acordo com a função/esquema, faça a seguinte consulta:
  
        SELECT psch.name as PartitionScheme,
            prng.value AS PartitionValue,
            prng.boundary_id AS BoundaryID
        FROM sys.partition_functions AS pfun
        INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
        INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
        WHERE pfun.name = <DatetimeFieldPFN>

### <a name="3-create-a-partition-table"></a>3. Criar uma mesa de partição
[Crie](https://msdn.microsoft.com/library/ms174979.aspx)tabelas (s) divididas de acordo com o seu esquema de dados, e especifique o esquema de partição e o campo de restrição utilizado para dividir a tabela, por exemplo:
  
        CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)

Para mais informações, consulte [Criar Tabelas e Índices Divididos](https://msdn.microsoft.com/library/ms188730.aspx).

## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Importar a granel os dados de cada tabela de partição individual

* Pode utilizar BCP, INSERT A GRANEL ou outros métodos, como [o SQL Server Migration Wizard](https://sqlazuremw.codeplex.com/). O exemplo fornecido utiliza o método BCP.
* [Altere a base de dados](https://msdn.microsoft.com/library/bb522682.aspx) para alterar o esquema de registo de transações para BULK_LOGGED para minimizar as despesas gerais de exploração madeireira, por exemplo:
  
        ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED
* Para acelerar o carregamento de dados, lance as operações de importação a granel paralelamente. Para obter dicas sobre a rápida importação a granel de big data para as bases de dados do SQL Server, consulte [a Carregar 1 TB em menos](https://blogs.msdn.com/b/sqlcat/archive/2006/05/19/602142.aspx)de 1 hora .

O seguinte script PowerShell é um exemplo de carregamento paralelo de dados usando o BCP.

    # Set database name, input data directory, and output log directory
    # This example loads comma-separated input data files
    # The example assumes the partitioned data files are named as <base_file_name>_<partition_number>.csv
    # Assumes the input data files include a header line. Loading starts at line number 2.

    $dbname = "<database_name>"
    $indir  = "<path_to_data_files>"
    $logdir = "<path_to_log_directory>"

    # Select authentication mode
    $sqlauth = 0

    # For SQL authentication, set the server and user credentials
    $sqlusr = "<user@server>"
    $server = "<tcp:serverdns>"
    $pass   = "<password>"

    # Set number of partitions per table - Should match the number of input data files per table
    $numofparts = <number_of_partitions>

    # Set table name to be loaded, basename of input data files, input format file, and number of partitions
    $tbname = "<table_name>"
    $basename = "<base_input_data_filename_no_extension>"
    $fmtfile = "<full_path_to_format_file>"

    # Create log directory if it does not exist
    New-Item -ErrorAction Ignore -ItemType directory -Path $logdir

    # BCP example using Windows authentication
    $ScriptBlock1 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -T -b 2500 -t "," -r \n
    }

    # BCP example using SQL authentication
    $ScriptBlock2 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num, $sqlusr, $server, $pass)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -U $sqlusr -S $server -P $pass -b 2500 -t "," -r \n
    }

    # Background processing of all partitions
    for ($i=1; $i -le $numofparts; $i++)
    {
       Write-Output "Submit loading trip and fare partitions # $i"
       if ($sqlauth -eq 0) {
          # Use Windows authentication
          Start-Job -ScriptBlock $ScriptBlock1 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i)
       } 
       else {
          # Use SQL authentication
          Start-Job -ScriptBlock $ScriptBlock2 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i, $sqlusr, $server, $pass)
       }
    }

    Get-Job

    # Optional - Wait till all jobs complete and report date and time
    date
    While (Get-Job -State "Running") { Start-Sleep 10 }
    date


## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Criar índices para otimizar aades e o desempenho da consulta
* Se extrair dados para modelação a partir de várias tabelas, crie índices nas teclas de união para melhorar o desempenho da união.
* [Criar índices](https://technet.microsoft.com/library/ms188783.aspx) (agrupados ou não agrupados) direcionados para o mesmo grupo de ficheiros para cada partição, por exemplo:
  
        CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  ou,
  
        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  
  > [!NOTE]
  > Pode optar por criar os índices antes de importar os dados a granel. A criação de índices antes da importação a granel abranda o carregamento de dados.
  > 
  > 

## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Processo avançado de análise e tecnologia em exemplo de ação
Para um exemplo de passagem de ponta a ponta utilizando o Processo de Ciência de Dados da Equipa com um conjunto de dados público, consulte o Processo de Ciência de Dados da Equipa em Ação: utilizando o [Servidor SQL](sql-walkthrough.md).

