---
title: Mover dados para uma máquina virtual do SQL Server - Processo de Ciência de Dados de Equipa
description: Mover dados de ficheiros planos ou de sql server no local para SQL Server em Azure VM.
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
ms.openlocfilehash: 8350437d04fd019aab8fb22be8ad0e9a4a2831d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87012183"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Mover dados para o SQL Server numa máquina virtual do Azure

Este artigo descreve as opções de deslocação de dados de ficheiros planos (formatos CSV ou TSV) ou de um SqL Server no local para o SQL Server numa máquina virtual Azure. Estas tarefas para mover dados para a nuvem fazem parte do Processo de Ciência de Dados da Equipa.

Para um tópico que delineie as opções de deslocação de dados para uma Base de Dados Azure SQL para machine learning, consulte [mover dados para uma Base de Dados Azure SQL para aprendizagem automática Azure](move-sql-azure.md).

A tabela seguinte resume as opções de deslocação de dados para o SQL Server numa máquina virtual Azure.

| <b>FONTE</b> | <b>DESTINO: SQL Server em Azure VM</b> |
| --- | --- |
| <b>Arquivo Plano</b> |1. <a href="#insert-tables-bcp">Utilitário de cópia a granel da linha de comando (BCP) </a><br> 2. <a href="#insert-tables-bulkquery">Consulta DE INSERÇÃO A granel SQL </a><br> 3. <a href="#sql-builtin-utilities">Utilitários incorporados gráficos no Servidor SQL</a> |
| <b>Servidor SQL nas Instalações</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Implementar uma base de dados de servidor SQL para um assistente VM do Microsoft Azure</a><br> 2. <a href="#export-flat-file">Exportação para um arquivo plano </a><br> 3. <a href="#sql-migration">Assistente de migração da base de dados SQL </a> <br> 4. <a href="#sql-backup">Base de dados de volta e restauro </a><br> |

Este documento pressupõe que os comandos SQL são executados a partir do SQL Server Management Studio ou do Visual Studio Database Explorer.

> [!TIP]
> Como alternativa, pode utilizar [a Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para criar e agendar um pipeline que irá mover dados para um SQL Server VM em Azure. Para obter mais informações, consulte [copiar dados com a Azure Data Factory (Copy Activity)](../../data-factory/copy-activity-overview.md).
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Pré-requisitos
Este tutorial pressupõe que tem:

* Uma **subscrição do Azure**. Se não tiver uma subscrição, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma **conta de armazenamento Azure.** Você usará uma conta de armazenamento Azure para armazenar os dados neste tutorial. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md). Depois de ter criado a conta de armazenamento, terá de obter a chave de conta utilizada para aceder ao armazenamento. Consulte [as teclas de acesso à conta de armazenamento](../../storage/common/storage-account-keys-manage.md).
* Servidor **SQL provisionado num VM Azure**. Para obter instruções, consulte [configurar uma máquina virtual do Azure SQL Server como um servidor de caderno IPython para análises avançadas](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
* Instalado e configurado **Azure PowerShell** localmente. Para obter instruções, consulte [como instalar e configurar a Azure PowerShell](/powershell/azure/).

## <a name="moving-data-from-a-flat-file-source-to-sql-server-on-an-azure-vm"></a><a name="filesource_to_sqlonazurevm"></a> Mover dados de uma fonte de ficheiro plana para SQL Server em um Azure VM
Se os seus dados estiverem num ficheiro plano (dispostos num formato de linha/coluna), pode ser movido para SQL Server VM em Azure através dos seguintes métodos:

1. [Utilitário de cópia a granel da linha de comando (BCP)](#insert-tables-bcp)
2. [Inserir a granel consulta SQL](#insert-tables-bulkquery)
3. [Utilitários incorporados gráficos no sql server (Importação/Exportação, SSIS)](#sql-builtin-utilities)

### <a name="command-line-bulk-copy-utility-bcp"></a><a name="insert-tables-bcp"></a>Utilitário de cópia a granel da linha de comando (BCP)
O BCP é um utilitário de linha de comando instalado com o SQL Server e é uma das formas mais rápidas de movimentar dados. Funciona em todas as três variantes do SQL Server (SqL Server, SQL Azure e SQL Server VM on Azure).

> [!NOTE]
> **Onde devem estar os meus dados para o BCP?**  
> Embora não seja necessário, ter ficheiros que contenham dados de origem localizados na mesma máquina que o SqL Server alvo permite transferências mais rápidas (velocidade de rede vs velocidade IO do disco local). Pode mover os ficheiros planos que contêm dados para a máquina onde o SQL Server é instalado utilizando várias ferramentas de cópia de ficheiros, tais como [AZCopy,](../../storage/common/storage-use-azcopy.md) [Azure Storage Explorer](https://storageexplorer.com/) ou cópia/pasta do Windows através do Protocolo de Ambiente de Trabalho Remoto (RDP).
>
>

1. Certifique-se de que a base de dados e as tabelas são criadas na base de dados do SqL Server alvo. Aqui está um exemplo de como fazê-lo usando os `Create Database` comandos e `Create Table` comandos:

    ```sql
    CREATE DATABASE <database_name>
    
    CREATE TABLE <tablename>
    (
        <columnname1> <datatype> <constraint>,
        <columnname2> <datatype> <constraint>,
        <columnname3> <datatype> <constraint>
    )
    ```

1. Gere o ficheiro de formato que descreve o esquema da tabela, emitindo o seguinte comando a partir da linha de comando da máquina onde o BCP está instalado.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
1. Insira os dados na base de dados utilizando o comando bcp, que deverá funcionar a partir da linha de comando quando o SQL Server estiver instalado na mesma máquina:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attempt -t \t -r \n`

> **Otimização de Inserções BCP** Consulte o seguinte artigo ["Diretrizes para otimizar a importação a granel"](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) para otimizar tais inserções.
>
>

### <a name="parallelizing-inserts-for-faster-data-movement"></a><a name="insert-tables-bulkquery-parallel"></a>Inserções paralelas para movimentos de dados mais rápidos
Se os dados que está a mover forem grandes, pode acelerar as coisas executando simultaneamente vários comandos BCP em paralelo num PowerShell Script.

> [!NOTE]
> **Ingestão de dados grandes** Para otimizar o carregamento de dados para conjuntos de dados grandes e muito grandes, partition suas tabelas de base de dados lógicas e físicas usando vários grupos de ficheiros e tabelas de divisórias. Para obter mais informações sobre a criação e carregamento de dados nas tabelas de partição, consulte [tabelas de partição sql de carga paralela.](parallel-load-sql-partitioned-tables.md)
>
>

A seguinte amostra O script PowerShell demonstra inserções paralelas utilizando o BCP:

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

### <a name="bulk-insert-sql-query"></a><a name="insert-tables-bulkquery"></a>Inserir a granel consulta SQL
[A consulta de inserção](https://msdn.microsoft.com/library/ms188365) a granel pode ser usada para importar dados na base de dados a partir de ficheiros baseados em linha/coluna (os tipos suportados são abrangidos pelo tópico[Prepare dados para exportação ou importação a granel (SQL Server).](https://msdn.microsoft.com/library/ms188609)

Aqui estão alguns comandos de amostra para Inserção a Granel são os seguintes:  

1. Analise os seus dados e desagure quaisquer opções personalizadas antes de importar para se certificar de que a base de dados do SQL Server assume o mesmo formato para quaisquer campos especiais, tais como datas. Aqui está um exemplo de como definir o formato da data como dia-mês (se os seus dados contiver a data no formato do dia do ano):

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

### <a name="built-in-utilities-in-sql-server"></a><a name="sql-builtin-utilities"></a>Utilitários incorporados no SQL Server
Pode utilizar os Serviços de Integração de Servidores SQL (SSIS) para importar dados em SQL Server VM em Azure a partir de um ficheiro plano.
O SSIS está disponível em dois ambientes de estúdio. Para mais detalhes, consulte [os Serviços de Integração (SSIS) e Ambientes de Estúdio:](https://technet.microsoft.com/library/ms140028.aspx)

* Para obter detalhes sobre as ferramentas de dados do servidor SQL, consulte as [ferramentas de dados do servidor do Microsoft SQL](https://msdn.microsoft.com/data/tools.aspx)  
* Para mais detalhes sobre o assistente de importação/exportação, consulte [o Assistente de Importação e Exportação do Servidor SQL](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="moving-data-from-on-premises-sql-server-to-sql-server-on-an-azure-vm"></a><a name="sqlonprem_to_sqlonazurevm"></a>Mover dados do SQL Server para o SQL Server num VM Azure
Também pode utilizar as seguintes estratégias de migração:

1. [Implementar uma base de dados de servidor SQL para um assistente VM do Microsoft Azure](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportação para Arquivo Plano](#export-flat-file)
3. [Assistente de migração da base de dados SQL](#sql-migration)
4. [Base de dados de volta e restauro](#sql-backup)

Descrevemos cada uma destas opções abaixo:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Implementar uma base de dados de servidor SQL para um assistente VM do Microsoft Azure
A Base de Dados de **Servidor SQL para um assistente VM do Microsoft Azure** é uma forma simples e recomendada de mover dados de uma instância do SQL Server no local para o SQL Server num VM Azure. Para obter etapas detalhadas, bem como uma discussão de outras alternativas, consulte [a Migração de uma base de dados para o SQL Server num Azure VM](../../azure-sql/virtual-machines/windows/migrate-to-vm-from-sql-server.md).

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Exportação para Arquivo Plano
Vários métodos podem ser usados para a granel dados de exportação de um servidor SQL on-in, conforme documentado no tópico [de importação e exportação de dados a granel (SQL Server).](https://msdn.microsoft.com/library/ms175937.aspx) Este documento abrangerá o Programa de Cópias a Granel (BCP) como exemplo. Uma vez que os dados são exportados para um ficheiro plano, pode ser importado para outro servidor SQL usando a importação a granel.

1. Exportar os dados do SQL Server para um ficheiro que utilize o utilitário bcp da seguinte forma

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Crie a base de dados e a tabela no SQL Server VM em Azure utilizando o `create database` esquema de `create table` mesa exportado no passo 1.
3. Criar um ficheiro de formato para descrever o esquema de tabela dos dados que estão a ser exportados/importados. Os detalhes do ficheiro de formato são descritos em [Criar um Ficheiro de Formato (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Geração de ficheiros de formato ao executar o BCP a partir do computador SQL Server

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`

    Geração de ficheiros de formato ao executar o BCP remotamente contra um SQL Server

    `bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n`
4. Utilize qualquer um dos métodos descritos na secção [Moving Data from File Source](#filesource_to_sqlonazurevm) para mover os dados em ficheiros planos para um SQL Server.

### <a name="sql-database-migration-wizard"></a><a name="sql-migration"></a>Assistente de migração da base de dados SQL
[O SqL Server Database Migration Wizard](https://sqlazuremw.codeplex.com/) fornece uma forma fácil de utilizar entre duas instâncias do servidor SQL. Permite ao utilizador mapear o esquema de dados entre fontes e tabelas de destino, escolher tipos de colunas e várias outras funcionalidades. Utiliza cópia a granel (BCP) sob as capas. Uma imagem do ecrã de boas-vindas para o assistente de migração da base de dados SQL é mostrada abaixo.  

![Assistente de migração do servidor SQL][2]

### <a name="database-back-up-and-restore"></a><a name="sql-backup"></a>Base de dados de volta e restauro
Suportes do SQL Server:

1. [Base de dados de back up e restaurar a funcionalidade](https://msdn.microsoft.com/library/ms187048.aspx) (tanto para um ficheiro local como para exportação de bacpac para blob) e [aplicações de nível de dados](https://msdn.microsoft.com/library/ee210546.aspx) (usando bacpac).
2. Capacidade de criar vMs de servidor SQL diretamente no Azure com uma base de dados copiada ou cópia para uma base de dados existente na Base de Dados SQL. Para obter mais informações, consulte [utilizar o Assistente de Base de Dados de Cópias](https://msdn.microsoft.com/library/ms188664.aspx).

Uma imagem da base de dados de back up/restaurar opções do SQL Server Management Studio é mostrada abaixo.

![Ferramenta de importação de servidor sql][1]

## <a name="resources"></a>Recursos
[Migrar uma base de dados para o SQL Server num VM Azure](../../azure-sql/virtual-machines/windows/migrate-to-vm-from-sql-server.md)

[Descrição geral do SQL Server nas Máquinas Virtuais do Azure](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
