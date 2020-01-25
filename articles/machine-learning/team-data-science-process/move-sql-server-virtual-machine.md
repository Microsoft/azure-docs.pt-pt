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

Para um tópico que descreve as opções para mover dados para uma base de dados do SQL do Azure para o Machine Learning, consulte [mover dados para uma base de dados do SQL do Azure para o Azure Machine Learning](move-sql-azure.md).

A tabela seguinte resume as opções para mover dados para o SQL Server numa máquina virtual do Azure.

| <b>ORIGEM</b> | <b>DESTINO: SQL Server numa VM do Azure</b> |
| --- | --- |
| <b>Ficheiro simples</b> |1. <a href="#insert-tables-bcp">Utilitário de cópia em massa de linha de comando (BCP)</a><br> 2. <a href="#insert-tables-bulkquery">consulta SQL de inserção em massa</a><br> 3. <a href="#sql-builtin-utilities">utilitários internos gráficos no SQL Server</a> |
| <b>SQL Server no local</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">implantar um banco de dados SQL Server em um assistente de VM Microsoft Azure</a><br> 2. <a href="#export-flat-file">exportar para um arquivo simples</a><br> 3. <a href="#sql-migration">Assistente de migração de banco de dados SQL</a> <br> 4. <a href="#sql-backup">backup e restauração de banco de dados</a><br> |

Este documento pressupõe que os comandos SQL são executados a partir do SQL Server Management Studio ou do Visual Studio Database Explorer.

> [!TIP]
> Como alternativa, pode utilizar [do Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para criar e agendar um pipeline que irá mover dados para uma VM do SQL Server no Azure. Para obter mais informações, consulte [copiar dados com o Azure Data Factory (atividade de cópia)](../../data-factory/copy-activity-overview.md).
>
>

## <a name="prereqs"></a>Pré-requisitos
Este tutorial parte do princípio de que tem:

* Uma **subscrição do Azure**. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma **conta de armazenamento do Azure**. Irá utilizar uma conta de armazenamento do Azure para armazenar os dados neste tutorial. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md). Depois de ter criado a conta de armazenamento, terá de obter a chave de conta utilizada para aceder ao armazenamento. Consulte [gerenciar chaves de acesso da conta de armazenamento](../../storage/common/storage-account-keys-manage.md).
* Aprovisionado **SQL Server numa VM do Azure**. Para obter instruções, consulte [configurar uma máquina virtual do Azure SQL Server como servidor IPython Notebook para análises avançadas](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
* Instalou e configurou **do Azure PowerShell** localmente. Para obter instruções, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a name="filesource_to_sqlonazurevm"></a> Mover dados de uma origem de ficheiro simples para o SQL Server numa VM do Azure
Se seus dados estão num arquivo simples (organizado num formato de linha/coluna), ele pode ser movido para a VM do SQL Server no Azure através dos seguintes métodos:

1. [Utilitário de cópia em massa da linha de comandos (BCP)](#insert-tables-bcp)
2. [Consulta SQL de inserção em massa](#insert-tables-bulkquery)
3. [Utilitários gráficos de incorporado no SQL Server (importar/exportar, SSIS)](#sql-builtin-utilities)

### <a name="insert-tables-bcp"></a>Utilitário de cópia em massa da linha de comandos (BCP)
BCP é um utilitário de linha de comandos instalado com o SQL Server e é uma das formas mais rápidas de mover os dados. Funciona em todas as três variantes do SQL Server (On-premises SQL Server, SQL Azure e SQL Server VM on Azure).

> [!NOTE]
> **Onde devem ser os meus dados para o BCP?**  
> Embora não seja necessário, ter ficheiros que contêm dados de origem localizados na mesma máquina, como o destino do SQL Server permite transferências mais rápidas (rede velocidade vs disco local velocidade de e/s). Pode mover os arquivos simples que contém dados para a máquina em que o SQL Server está instalado usando copiar o arquivo várias ferramentas como [AZCopy](../../storage/common/storage-use-azcopy.md), [Explorador de armazenamento do Azure](https://storageexplorer.com/) ou copie/cole de windows através do ambiente de trabalho remoto Protocolo (RDP).
>
>

1. Certifique-se de que a base de dados e as tabelas são criadas na base de dados de SQL Server de destino. Eis um exemplo de como fazê-lo utilizando o `Create Database` e `Create Table` comandos:

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

> **Otimizando o BCP insere** , consulte o seguinte artigo ['Diretrizes para otimizar a importação de em massa'](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) para otimizar esses inserções.
>
>

### <a name="insert-tables-bulkquery-parallel"></a>Paralelização de inserções de movimento de dados mais rápido
Se os dados que está a mover forem grandes, pode acelerar as coisas executando simultaneamente vários comandos BCP em paralelo num Script PowerShell.

> [!NOTE]
> **Ingestão de Big data** Para otimizar o carregamento de dados para grandes e muito grandes conjuntos, particione suas tabelas de banco de dado lógico e físico usando vários grupos de arquivos e tabelas de partição. Para obter mais informações sobre como criar e carregar dados para tabelas de partição, veja [paralela tabelas de partição de SQL de carga](parallel-load-sql-partitioned-tables.md).
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

### <a name="insert-tables-bulkquery"></a>Consulta SQL de inserção em massa
[Em massa de consulta de SQL Insert](https://msdn.microsoft.com/library/ms188365) pode ser utilizado para importar dados para a base de dados de ficheiros de linha/coluna com base (os tipos suportados são abordados a[preparar dados para exportar em massa ou importar (SQL Server)](https://msdn.microsoft.com/library/ms188609)) tópico.

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

### <a name="sql-builtin-utilities"></a>Utilitários incorporados no SQL Server
Pode utilizar os Serviços de Integração do Servidor SQL (SSIS) para importar dados para O VM do Servidor SQL em Azure a partir de um ficheiro plano.
SSIS está disponível em dois ambientes de studio. Para obter detalhes, consulte [Integration Services (SSIS) e ambientes de Studio](https://technet.microsoft.com/library/ms140028.aspx):

* Para obter detalhes sobre as ferramentas de dados do SQL Server, consulte [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
* Para obter detalhes sobre o Assistente de importação/exportação, consulte [importação do SQL Server e o Assistente para exportar](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="sqlonprem_to_sqlonazurevm"></a>Mover dados de SQL Server no local para o SQL Server numa VM do Azure
Também pode utilizar as seguintes estratégias de migração:

1. [Implementar uma base de dados do SQL Server num Assistente de VM do Microsoft Azure](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportar para ficheiro simples](#export-flat-file)
3. [Assistente de migração de base de dados SQL](#sql-migration)
4. [Base de dados back cópia de segurança e restauro](#sql-backup)

Descrevemos cada uma destas opções abaixo:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Implementar uma base de dados do SQL Server num Assistente de VM do Microsoft Azure
O **implementar uma base de dados do SQL Server num assistente do Microsoft Azure VM** é uma forma simple e recomendada para mover dados de uma instância do SQL Server no local para o SQL Server numa VM do Azure. Para obter passos detalhados, bem como uma discussão sobre outras alternativas, consulte [migrar uma base de dados para o SQL Server numa VM do Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Exportar para ficheiro simples
Vários métodos que podem ser utilizados em massa exportar dados de um servidor de SQL no local, conforme documentado no [importação em massa e exportação de dados (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) tópico. Este documento aborda o programa de cópia em massa (BCP) como exemplo. Assim que os dados são exportados para um arquivo simples, pode ser importado para outro SQL server com a importação em massa.

1. Exportar os dados do SQL Server no local para um ficheiro com o utilitário bcp da seguinte forma

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Criar a base de dados e a tabela na VM do SQL Server no Azure com o `create database` e `create table` para o esquema da tabela exportado no passo 1.
3. Crie um ficheiro de formato para descrever o esquema da tabela de dados a ser exportado/importados. Detalhes do ficheiro de formato são descritos [criar um ficheiro de formato (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Formato de geração de ficheiros ao executar o BCP a partir da máquina do SQL Server

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Formato de geração de ficheiros quando a executar o BCP remotamente em relação a um SQL Server

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. Utilizar qualquer um dos métodos descritos na secção [mover dados de origem do ficheiro](#filesource_to_sqlonazurevm) para mover os dados em arquivos simples para um SQL Server.

### <a name="sql-migration"></a>Assistente de migração de base de dados SQL
[Assistente de migração de base de dados do SQL Server](https://sqlazuremw.codeplex.com/) fornece uma forma fácil de utilizar para mover dados entre duas instâncias do SQL server. Ele permite que o utilizador mapear o esquema de dados entre origens e tabelas de destino, escolha os tipos de coluna e várias outras funcionalidades. Ele usa a cópia em massa (BCP), nos bastidores. Uma captura de ecrã do ecrã de boas-vindas para que o Assistente de migração de base de dados SQL é mostrada abaixo.  

![Assistente de migração do SQL Server][2]

### <a name="sql-backup"></a>Base de dados back cópia de segurança e restauro
Suporta o SQL Server:

1. [Base de dados back cópia de segurança e restaurar a funcionalidade](https://msdn.microsoft.com/library/ms187048.aspx) (para um ficheiro local ou um bacpac exportar para o blob) e [aplicações de camada de dados](https://msdn.microsoft.com/library/ee210546.aspx) (usando a bacpac).
2. Capacidade de criar diretamente as VMs do SQL Server no Azure com uma base de dados copiado ou copiar para uma base de dados existente do SQL Azure. Para mais informações, consulte [Utilize o Assistente de Base de Dados de Cópias](https://msdn.microsoft.com/library/ms188664.aspx).

Uma captura de ecrã do fundo da base de dados de segurança/restauro opções do SQL Server Management Studio é mostrado abaixo.

![Ferramenta de importação do SQL Server][1]

## <a name="resources"></a>Recursos
[Migrar uma base de dados para o SQL Server numa VM do Azure](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[Descrição geral do SQL Server nas Máquinas Virtuais do Azure](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
