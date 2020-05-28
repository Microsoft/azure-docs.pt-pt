---
title: Mova dados para uma máquina virtual do Servidor SQL - Processo de Ciência de Dados da Equipa
description: Mova os dados de ficheiros planos ou de um Servidor SQL no local para o Servidor SQL no Azure VM.
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
ms.openlocfilehash: 4de0686dbca803b9008c1b56c512a90fcfe2b3c0
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84022440"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Mover dados para o SQL Server numa máquina virtual do Azure

Este artigo descreve as opções para mover dados quer a partir de ficheiros planos (formatos CSV ou TSV) quer de um Servidor SQL no local para o SQL Server numa máquina virtual Azure. Estas tarefas para mover dados para a nuvem fazem parte do Processo de Ciência de Dados da Equipa.

Para um tópico que delineie as opções para mover dados para uma Base de Dados Azure SQL para machine learning, consulte mover dados para uma base de [dados Azure SQL para aprendizagem automática azure](move-sql-azure.md).

A tabela seguinte resume as opções de deslocação de dados para o SQL Server numa máquina virtual Azure.

| <b>FONTE</b> | <b>DESTINO: Servidor SQL no Azure VM</b> |
| --- | --- |
| <b>Arquivo Plano</b> |1. <a href="#insert-tables-bcp">Utilitário de cópia a granel de linha de comando (BCP)</a><br> 2. <a href="#insert-tables-bulkquery">Consulta SQL de inserção</a> a granel<br> 3. <a href="#sql-builtin-utilities">Utilitários integrados gráficos no Servidor SQL</a> |
| <b>No local, o Servidor SQL</b> |1. Implemente uma base de dados do <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Servidor SQL para um assistente VM Microsoft Azure</a><br> 2. <a href="#export-flat-file">Exportação para um ficheiro plano</a><br> 3. Assistente de migração de base de <a href="#sql-migration">dados SQL</a> <br> 4. <a href="#sql-backup">Base de dados de volta e restauro</a><br> |

Este documento pressupõe que os comandos SQL são executados a partir do SQL Server Management Studio ou do Visual Studio Database Explorer.

> [!TIP]
> Como alternativa, pode utilizar a [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para criar e programar um pipeline que irá mover dados para um VM de servidor SQL no Azure. Para mais informações, consulte copiar dados com a [Azure Data Factory (Copy Activity)](../../data-factory/copy-activity-overview.md).
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Pré-requisitos
Este tutorial assume que tem:

* Uma **subscrição Azure.** Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma **conta de armazenamento Azure.** Utilizará uma conta de armazenamento Azure para armazenar os dados neste tutorial. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md). Depois de ter criado a conta de armazenamento, terá de obter a chave da conta utilizada para aceder ao armazenamento. Ver [Gerir as chaves](../../storage/common/storage-account-keys-manage.md)de acesso à conta de armazenamento .
* Servidor **SQL**aprovisionado num VM Azure . Para obter instruções, consulte [A configurar uma máquina virtual Do Servidor Azure SQL como um servidor IPython Notebook para análise supértica avançada](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
* Instalado e configurado **Azure PowerShell** localmente. Para obter instruções, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a name="moving-data-from-a-flat-file-source-to-sql-server-on-an-azure-vm"></a><a name="filesource_to_sqlonazurevm"></a>Movendo dados de uma fonte de ficheiro plano para O Servidor SQL em um VM Azure
Se os seus dados estiverem num ficheiro plano (organizado num formato de linha/coluna), pode ser transferido para SQL Server VM no Azure através dos seguintes métodos:

1. [Utilitário de cópia a granel de linha de comando (BCP)](#insert-tables-bcp)
2. [Consulta SQL de inserção a granel](#insert-tables-bulkquery)
3. [Utilitários integrados gráficos no Servidor SQL (Importação/Exportação, SSIS)](#sql-builtin-utilities)

### <a name="command-line-bulk-copy-utility-bcp"></a><a name="insert-tables-bcp"></a>Utilitário de cópia a granel de linha de comando (BCP)
O BCP é um utilitário de linha de comando instalado com o SQL Server e é uma das formas mais rápidas de movimentar dados. Funciona em todas as três variantes do SQL Server (On-premises SQL Server, SQL Azure e SQL Server VM on Azure).

> [!NOTE]
> **Onde devem estar os meus dados para o BCP?**  
> Embora não seja necessário, ter ficheiros que contenham dados de origem localizados na mesma máquina que o Servidor SQL visa permitir transferências mais rápidas (velocidade de rede vs velocidade IO do disco local). Pode mover os ficheiros planos que contêm dados para a máquina onde o SQL Server está instalado utilizando várias ferramentas de cópia de ficheiros, tais como [AZCopy,](../../storage/common/storage-use-azcopy.md) [Azure Storage Explorer](https://storageexplorer.com/) ou windows copy/paste via Remote Desktop Protocol (RDP).
>
>

1. Certifique-se de que a base de dados e as tabelas são criadas na base de dados do Servidor SQL alvo. Aqui está um exemplo de como fazê-lo usando os `Create Database` comandos e `Create Table` comandos:

    ```sql
    CREATE DATABASE <database_name>
    
    CREATE TABLE <tablename>
    (
        <columnname1> <datatype> <constraint>,
        <columnname2> <datatype> <constraint>,
        <columnname3> <datatype> <constraint>
    )
    ```

1. Gere o ficheiro de formato que descreve o esquema para a mesa através da emissão do seguinte comando da linha de comando da máquina onde o BCP está instalado.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
1. Insira os dados na base de dados utilizando o comando bcp, que deverá funcionar a partir da linha de comando quando o Servidor SQL estiver instalado na mesma máquina:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attempt -t \t -r \n`

> **Otimização de Inserções do BCP** Por favor, consulte o seguinte artigo ["Diretrizes para otimizar a importação a granel"](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) para otimizar tais inserções.
>
>

### <a name="parallelizing-inserts-for-faster-data-movement"></a><a name="insert-tables-bulkquery-parallel"></a>Inserções paralelizadoras para um movimento de dados mais rápido
Se os dados que está a mover forem grandes, pode acelerar as coisas executando simultaneamente vários comandos BCP em paralelo num Script PowerShell.

> [!NOTE]
> **Ingestão de big dados** Para otimizar o carregamento de dados para conjuntos de dados grandes e muito grandes, partifique as suas tabelas de bases de dados lógicas e físicas utilizando vários grupos de ficheiros e tabelas de partição. Para obter mais informações sobre a criação e carregamento de dados para tabelas de partição, consulte tabelas de [partição SQL](parallel-load-sql-partitioned-tables.md)de carga paralela .
>
>

A seguinte amostra o script PowerShell demonstra inserções paralelas utilizando o BCP:

```powershell
$NO_OF_PARALLEL_JOBS=2

Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
# Define what each job does
$ScriptBlock = {
    param($partitionnumber)

    #Explicitly using SQL username password
    bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

    #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
    #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n
}


# Background processing of all partitions
for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
{
    Write-Debug "Submit loading partition # $i"
    Start-Job $ScriptBlock -Arg $i      
}


# Wait for it all to complete
While (Get-Job -State "Running")
{
    Start-Sleep 10
    Get-Job
}

# Getting the information back from the jobs
Get-Job | Receive-Job
Set-ExecutionPolicy Restricted #reset the execution policy
```

### <a name="bulk-insert-sql-query"></a><a name="insert-tables-bulkquery"></a>Consulta SQL de inserção a granel
[A consulta SQL de inserção](https://msdn.microsoft.com/library/ms188365) a granel pode ser usada para importar dados para a base de dados a partir de ficheiros baseados em linha/coluna (os tipos suportados estão cobertos no tópico[De Preparar dados para exportação ou importação a granel (Servidor SQL)](https://msdn.microsoft.com/library/ms188609)).

Aqui estão alguns comandos de amostra para inserção a granel são como abaixo:  

1. Analise os seus dados e detetete quaisquer opções personalizadas antes de importar para se certificar de que a base de dados do SQL Server assume o mesmo formato para quaisquer campos especiais, como datas. Aqui está um exemplo de como definir o formato da data como o mês-mês de ano (se os seus dados contenham a data em formato de um mês):

    ```sql
    SET DATEFORMAT ymd;
    ```
2. Dados de importação utilizando declarações de importação a granel:

    ```sql
    BULK INSERT <tablename>
    FROM
    '<datafilename>'
    WITH
    (
        FirstRow = 2,
        FIELDTERMINATOR = ',', --this should be column separator in your data
        ROWTERMINATOR = '\n'   --this should be the row separator in your data
    )
    ```

### <a name="built-in-utilities-in-sql-server"></a><a name="sql-builtin-utilities"></a>Utilitários incorporados no Servidor SQL
Pode utilizar os Serviços de Integração do Servidor SQL (SSIS) para importar dados para O VM do Servidor SQL em Azure a partir de um ficheiro plano.
O SSIS está disponível em dois ambientes de estúdio. Para mais detalhes, consulte Serviços de [Integração (SSIS) e Ambientes estúdio:](https://technet.microsoft.com/library/ms140028.aspx)

* Para mais detalhes sobre as ferramentas de dados do Servidor SQL, consulte as ferramentas de dados do [Servidor Microsoft SQL](https://msdn.microsoft.com/data/tools.aspx)  
* Para mais informações sobre o Feiticeiro de Importação/Exportação, consulte o Assistente de Importação e Exportação do [Servidor SQL](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="moving-data-from-on-premises-sql-server-to-sql-server-on-an-azure-vm"></a><a name="sqlonprem_to_sqlonazurevm"></a>Movendo dados do Servidor SQL no local para O Servidor SQL em um VM Azure
Também pode utilizar as seguintes estratégias de migração:

1. [Implemente uma base de dados do Servidor SQL para um assistente VM Microsoft Azure](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportação para Arquivo Plano](#export-flat-file)
3. [Feiticeiro de Migração de Bases de Dados SQL](#sql-migration)
4. [Base de dados de volta e restauro](#sql-backup)

Descrevemos cada uma destas opções abaixo:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Implemente uma base de dados do Servidor SQL para um assistente VM Microsoft Azure
A implementação de uma base de dados do **Servidor SQL para um assistente VM Microsoft Azure** é uma forma simples e recomendada de mover dados de uma instância de Servidor SQL no local para o SQL Server num VM Azure. Para passos detalhados, bem como uma discussão sobre outras alternativas, consulte [migrate uma base de dados para o SQL Server num VM Azure](../../azure-sql/virtual-machines/windows/migrate-to-vm-from-sql-server.md).

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Exportação para Arquivo Plano
Vários métodos podem ser usados para exportar a granel dados de um Servidor SQL on-premises, conforme documentado no tópico de [Importação e Exportação de Dados a granel (SQL Server).](https://msdn.microsoft.com/library/ms175937.aspx) Este documento irá abranger o Programa de Cópias a Granel (BCP) como exemplo. Uma vez que os dados são exportados para um ficheiro plano, podem ser importados para outro servidor SQL usando a importação a granel.

1. Exportar os dados do SQL Server no local para um Ficheiro utilizando o utilitário bcp da seguinte forma

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Crie a base de dados e a tabela no SQL Server VM em Azure utilizando `create database` o e para o esquema de mesa `create table` exportado no passo 1.
3. Criar um ficheiro de formato para descrever o esquema de tabela dos dados que estão a ser exportados/importados. Os detalhes do ficheiro de formato são descritos no [Create a Format File (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Geração de ficheiros de formato ao executar BCP a partir da máquina SQL Server

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Geração de ficheiros de formato ao executar o BCP remotamente contra um Servidor SQL

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. Utilize qualquer um dos métodos descritos na secção [Movendo dados a partir de File Source](#filesource_to_sqlonazurevm) para mover os dados em ficheiros planos para um Servidor SQL.

### <a name="sql-database-migration-wizard"></a><a name="sql-migration"></a>Feiticeiro de Migração de Bases de Dados SQL
[O SQL Server Database Migration Wizard](https://sqlazuremw.codeplex.com/) fornece uma forma amigável de mover dados entre duas instâncias do servidor SQL. Permite ao utilizador mapear o esquema de dados entre fontes e tabelas de destino, escolher tipos de colunas e várias outras funcionalidades. Utiliza cópia a granel (BCP) sob as capas. Uma imagem do ecrã de boas-vindas para o assistente de migração de bases de dados SQL é mostrada abaixo.  

![Assistente de migração do servidor SQL][2]

### <a name="database-back-up-and-restore"></a><a name="sql-backup"></a>Base de dados de volta e restauro
Suportes do Servidor SQL:

1. [Base de dados de recolocação e restauro da funcionalidade](https://msdn.microsoft.com/library/ms187048.aspx) (tanto para um ficheiro local como para a exportação de bacpac para blob) e [aplicações de nível](https://msdn.microsoft.com/library/ee210546.aspx) de dados (utilizando bacpac).
2. Capacidade de criar diretamente VMs do Servidor SQL no Azure com uma base de dados copiada ou cópia para uma base de dados SQL Azure existente. Para mais informações, consulte [Utilize o Assistente de Base de Dados de Cópias](https://msdn.microsoft.com/library/ms188664.aspx).

Uma imagem da Base de Dados de back/restaurar opções do Estúdio de Gestão de Servidores SQL é mostrada abaixo.

![Ferramenta de importação de servidor SQL][1]

## <a name="resources"></a>Recursos
[Migrar uma base de dados para o Servidor SQL num VM Azure](../../azure-sql/virtual-machines/windows/migrate-to-vm-from-sql-server.md)

[Descrição geral do SQL Server nas Máquinas Virtuais do Azure](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
