---
title: Mover dados para uma máquina virtual do SQL Server - Team Data Science Process
description: Mova dados de arquivos simples ou de um servidor de SQL no local para o SQL Server numa VM do Azure.
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
ms.openlocfilehash: b8a01b5f2f5ec64fea014468356408220f9c4f1a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721375"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Mover dados para o SQL Server numa máquina virtual do Azure

Este artigo descreve as opções para mover dados de ficheiros simples (formatos CSV ou TSV) ou a partir de um servidor de SQL no local para o SQL Server numa máquina virtual do Azure. Estas tarefas para mover dados para a cloud fazem parte do processo de ciência de dados de equipa.

Para um tópico que delineie as opções para mover dados para uma Base de Dados Azure SQL para machine learning, consulte mover dados para uma base de [dados Azure SQL para aprendizagem automática azure](move-sql-azure.md).

A tabela seguinte resume as opções para mover dados para o SQL Server numa máquina virtual do Azure.

| <b>FONTE</b> | <b>DESTINO: Servidor SQL no Azure VM</b> |
| --- | --- |
| <b>Arquivo Plano</b> |1. <a href="#insert-tables-bcp">Utilitário de cópia a granel de linha de comando (BCP)</a><br> 2. <a href="#insert-tables-bulkquery">Consulta SQL de inserção</a> a granel<br> 3. <a href="#sql-builtin-utilities">Utilitários integrados gráficos no Servidor SQL</a> |
| <b>No local, o Servidor SQL</b> |1. Implemente uma base de dados do <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Servidor SQL para um assistente VM Microsoft Azure</a><br> 2. <a href="#export-flat-file">Exportação para um ficheiro plano</a><br> 3. Assistente de migração de base de <a href="#sql-migration">dados SQL</a> <br> 4. <a href="#sql-backup">Base de dados de volta e restauro</a><br> |

Este documento pressupõe que os comandos SQL são executados a partir do SQL Server Management Studio ou do Visual Studio Database Explorer.

> [!TIP]
> Como alternativa, pode utilizar a [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para criar e programar um pipeline que irá mover dados para um VM de servidor SQL no Azure. Para mais informações, consulte copiar dados com a [Azure Data Factory (Copy Activity)](../../data-factory/copy-activity-overview.md).
>
>

## <a name="prereqs"></a>Pré-requisitos
Este tutorial parte do princípio de que tem:

* Uma **subscrição Azure.** Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma **conta de armazenamento Azure.** Irá utilizar uma conta de armazenamento do Azure para armazenar os dados neste tutorial. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md). Depois de ter criado a conta de armazenamento, terá de obter a chave de conta utilizada para aceder ao armazenamento. Ver [Gerir as chaves](../../storage/common/storage-account-keys-manage.md)de acesso à conta de armazenamento .
* Servidor **SQL**aprovisionado num VM Azure . Para obter instruções, consulte [A configurar uma máquina virtual Do Servidor Azure SQL como um servidor IPython Notebook para análise supértica avançada](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
* Instalado e configurado **Azure PowerShell** localmente. Para obter instruções, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a name="filesource_to_sqlonazurevm"></a>Movendo dados de uma fonte de ficheiro plano para O Servidor SQL em um VM Azure
Se seus dados estão num arquivo simples (organizado num formato de linha/coluna), ele pode ser movido para a VM do SQL Server no Azure através dos seguintes métodos:

1. [Utilitário de cópia a granel de linha de comando (BCP)](#insert-tables-bcp)
2. [Consulta SQL de inserção a granel](#insert-tables-bulkquery)
3. [Utilitários integrados gráficos no Servidor SQL (Importação/Exportação, SSIS)](#sql-builtin-utilities)

### <a name="insert-tables-bcp"></a>Utilitário de cópia a granel de linha de comando (BCP)
BCP é um utilitário de linha de comandos instalado com o SQL Server e é uma das formas mais rápidas de mover os dados. Funciona em todas as três variantes do SQL Server (On-premises SQL Server, SQL Azure e SQL Server VM on Azure).

> [!NOTE]
> **Onde devem estar os meus dados para o BCP?**  
> Embora não seja necessário, ter ficheiros que contêm dados de origem localizados na mesma máquina, como o destino do SQL Server permite transferências mais rápidas (rede velocidade vs disco local velocidade de e/s). Pode mover os ficheiros planos que contêm dados para a máquina onde o SQL Server está instalado utilizando várias ferramentas de cópia de ficheiros, tais como [AZCopy,](../../storage/common/storage-use-azcopy.md) [Azure Storage Explorer](https://storageexplorer.com/) ou windows copy/paste via Remote Desktop Protocol (RDP).
>
>

1. Certifique-se de que a base de dados e as tabelas são criadas na base de dados de SQL Server de destino. Aqui está um exemplo de como fazê-lo usando os comandos `Create Database` e `Create Table`:

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

### <a name="insert-tables-bulkquery-parallel"></a>Inserções paralelizadoras para um movimento de dados mais rápido
Se os dados que está a mover forem grandes, pode acelerar as coisas executando simultaneamente vários comandos BCP em paralelo num Script PowerShell.

> [!NOTE]
> **Ingestão de big dados** Para otimizar o carregamento de dados para conjuntos de dados grandes e muito grandes, partifique as suas tabelas de bases de dados lógicas e físicas utilizando vários grupos de ficheiros e tabelas de partição. Para obter mais informações sobre a criação e carregamento de dados para tabelas de partição, consulte tabelas de [partição SQL](parallel-load-sql-partitioned-tables.md)de carga paralela .
>
>

O exemplo de script do PowerShell a seguir demonstra inserções paralelas usando bcp:

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

### <a name="insert-tables-bulkquery"></a>Consulta SQL de inserção a granel
[A consulta SQL de inserção](https://msdn.microsoft.com/library/ms188365) a granel pode ser usada para importar dados para a base de dados a partir de ficheiros baseados em linha/coluna (os tipos suportados estão cobertos no tópico[De Preparar dados para exportação ou importação a granel (Servidor SQL)](https://msdn.microsoft.com/library/ms188609)).

Eis alguns comandos de exemplo para a inserção em massa são como abaixo:  

1. Analise os seus dados e definir as opções personalizadas antes de importar para se certificar de que a base de dados do SQL Server supõe que o mesmo formato para quaisquer campos especiais, como as datas. Eis um exemplo de como definir o formato de data como o dia do mês do ano (se seus dados contiverem a data no formato de dia do mês do ano):

    ```sql
    SET DATEFORMAT ymd;
    ```
2. Importe dados com declarações de importação em massa:

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

### <a name="sql-builtin-utilities"></a>Utilitários incorporados no Servidor SQL
Pode utilizar os Serviços de Integração do Servidor SQL (SSIS) para importar dados para O VM do Servidor SQL em Azure a partir de um ficheiro plano.
SSIS está disponível em dois ambientes de studio. Para mais detalhes, consulte Serviços de [Integração (SSIS) e Ambientes estúdio:](https://technet.microsoft.com/library/ms140028.aspx)

* Para mais detalhes sobre as ferramentas de dados do Servidor SQL, consulte as ferramentas de dados do [Servidor Microsoft SQL](https://msdn.microsoft.com/data/tools.aspx)  
* Para mais informações sobre o Feiticeiro de Importação/Exportação, consulte o Assistente de Importação e Exportação do [Servidor SQL](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="sqlonprem_to_sqlonazurevm"></a>Movendo dados do Servidor SQL no local para O Servidor SQL em um VM Azure
Também pode utilizar as seguintes estratégias de migração:

1. [Implemente uma base de dados do Servidor SQL para um assistente VM Microsoft Azure](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportação para Arquivo Plano](#export-flat-file)
3. [Feiticeiro de Migração de Bases de Dados SQL](#sql-migration)
4. [Base de dados de volta e restauro](#sql-backup)

Descrevemos cada uma destas opções abaixo:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Implementar uma base de dados do SQL Server num Assistente de VM do Microsoft Azure
A implementação de uma base de dados do **Servidor SQL para um assistente VM Microsoft Azure** é uma forma simples e recomendada de mover dados de uma instância de Servidor SQL no local para o SQL Server num VM Azure. Para passos detalhados, bem como uma discussão sobre outras alternativas, consulte [migrate uma base de dados para o SQL Server num VM Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Exportação para Arquivo Plano
Vários métodos podem ser usados para exportar a granel dados de um Servidor SQL on-premises, conforme documentado no tópico de [Importação e Exportação de Dados a granel (SQL Server).](https://msdn.microsoft.com/library/ms175937.aspx) Este documento aborda o programa de cópia em massa (BCP) como exemplo. Assim que os dados são exportados para um arquivo simples, pode ser importado para outro SQL server com a importação em massa.

1. Exportar os dados do SQL Server no local para um ficheiro com o utilitário bcp da seguinte forma

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Crie a base de dados e a tabela no SQL Server VM em Azure utilizando o `create database` e `create table` para o esquema de mesa exportado no passo 1.
3. Crie um ficheiro de formato para descrever o esquema da tabela de dados a ser exportado/importados. Os detalhes do ficheiro de formato são descritos no [Create a Format File (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Formato de geração de ficheiros ao executar o BCP a partir da máquina do SQL Server

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Formato de geração de ficheiros quando a executar o BCP remotamente em relação a um SQL Server

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. Utilize qualquer um dos métodos descritos na secção [Movendo dados a partir de File Source](#filesource_to_sqlonazurevm) para mover os dados em ficheiros planos para um Servidor SQL.

### <a name="sql-migration"></a>Feiticeiro de Migração de Bases de Dados SQL
[O SQL Server Database Migration Wizard](https://sqlazuremw.codeplex.com/) fornece uma forma amigável de mover dados entre duas instâncias do servidor SQL. Ele permite que o utilizador mapear o esquema de dados entre origens e tabelas de destino, escolha os tipos de coluna e várias outras funcionalidades. Ele usa a cópia em massa (BCP), nos bastidores. Uma captura de ecrã do ecrã de boas-vindas para que o Assistente de migração de base de dados SQL é mostrada abaixo.  

![Assistente de migração do SQL Server][2]

### <a name="sql-backup"></a>Base de dados de volta e restauro
Suporta o SQL Server:

1. [Base de dados de recolocação e restauro da funcionalidade](https://msdn.microsoft.com/library/ms187048.aspx) (tanto para um ficheiro local como para a exportação de bacpac para blob) e [aplicações de nível](https://msdn.microsoft.com/library/ee210546.aspx) de dados (utilizando bacpac).
2. Capacidade de criar diretamente as VMs do SQL Server no Azure com uma base de dados copiado ou copiar para uma base de dados existente do SQL Azure. Para mais informações, consulte [Utilize o Assistente de Base de Dados de Cópias](https://msdn.microsoft.com/library/ms188664.aspx).

Uma captura de ecrã do fundo da base de dados de segurança/restauro opções do SQL Server Management Studio é mostrado abaixo.

![Ferramenta de importação do SQL Server][1]

## <a name="resources"></a>Recursos
[Migrar uma base de dados para o Servidor SQL num VM Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[Descrição geral do SQL Server nas Máquinas Virtuais do Azure](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
