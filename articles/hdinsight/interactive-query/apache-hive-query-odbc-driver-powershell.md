---
title: Consulta Apache Hive com o controlador ODBC e o PowerShell - Azure HDInsight
description: Utilize o controlador Microsoft Hive ODBC e o PowerShell para consultar os clusters do Apache Hive no HDInsight do Azure.
keywords: Hive odbc do hive, do powershell
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.author: hrasheed
ms.openlocfilehash: b02c865e953861b5ac396538fdd0f0623b0e5428
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486103"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>Tutorial: Consulta Apache Hive ODBC e o PowerShell

Drivers ODBC do Microsoft oferecem uma forma flexível para interagir com diferentes tipos de origens de dados, incluindo Apache Hive. Pode escrever código em linguagens de script como o PowerShell que usam ODBC drivers para abrir uma ligação ao cluster do Hive, passa uma consulta à sua escolha e exibir os resultados.

Neste tutorial, terá de efetuar as seguintes tarefas:

> [!div class="checklist"]
> * Transfira e instale o controlador Microsoft Hive ODBC
> * Criar uma origem de dados de ODBC do Hive do Apache ligada ao seu cluster
> * Informações de consulta de exemplo do seu cluster com o PowerShell

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter os seguintes itens:

* Um cluster do Interactive Query no HDInsight. Para criar um, veja [introdução ao Azure HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Selecione **Interactive Query** como o tipo de cluster.

## <a name="install-microsoft-hive-odbc-driver"></a>Instalar o controlador Microsoft Hive ODBC

Transferir e instalar o [Microsoft Hive ODBC Driver](https://go.microsoft.com/fwlink/?LinkID=286698).

## <a name="create-apache-hive-odbc-data-source"></a>Criar origem de dados de ODBC do Hive do Apache

Os passos seguintes mostram como criar uma origem de dados de ODBC do Hive do Apache.

1. A partir do Windows, navegue para **começar** > **ferramentas administrativas do Windows** > **fontes de dados ODBC (32-bit)/(64-bit)** .  Uma **administrador de fonte de dados de ODBC** é aberta a janela.

    ![Administrador de fonte de dados OBDC](./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png "configurar um DSN com o administrador de fonte de dados de ODBC")

1. Do **utilizador DSN** separador, selecione **Add** para abrir o **criar nova origem de dados** janela.

1. Selecione **Microsoft Hive ODBC Driver**e, em seguida, selecione **concluir** para abrir o **Microsoft Hive ODBC Driver de DSN Setup** janela.

1. Escreva ou selecione os seguintes valores:

   | Propriedade | Descrição |
   | --- | --- |
   |  Nome da Origem de Dados |Atribua um nome para a sua origem de dados |
   |  Host(s) |Introduza `CLUSTERNAME.azurehdinsight.net`. Por exemplo, `myHDICluster.azurehdinsight.net` |
   |  Port |Utilize **443**.|
   |  Base de Dados |Uso **predefinição**. |
   |  Mecanismo |Selecione **serviço do Windows Azure HDInsight** |
   |  Nome de utilizador |Introduza o nome de utilizador do utilizador do HDInsight cluster HTTP. O nome de utilizador predefinido é **admin**. |
   |  Palavra-passe |Introduza a palavra-passe do utilizador de cluster do HDInsight. Selecione a caixa de verificação **guardar palavra-passe (encriptado)** .|

1. Opcional: Selecione **opções avançadas**.  

   | Parâmetro | Descrição |
   | --- | --- |
   |  Utilizar a consulta nativa |Quando estiver selecionada, o controlador ODBC não tentará converter TSQL em HiveQL. Utilize esta opção apenas se tiver 100%-se de que esteja enviando puras declarações de HiveQL. Ao ligar ao SQL Server ou base de dados do Azure SQL, deve deixar desmarcada. |
   |  Linhas obtidas por bloco |Quando a obter um grande número de registos, a otimização este parâmetro pode ser necessário para garantir que os desempenhos ideal. |
   |  Comprimento de coluna de cadeia de caracteres padrão, o comprimento de coluna binária, escala da coluna Decimal |O tipo de dados comprimentos e precisions pode afetar a forma como os dados são retornados. Estes provocarem informações incorretas a serem retornados devido a uma perda de precisão e truncamento. |

    ![Opções Avançadas](./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png "opções de configuração do Advanced DSN")

1. Selecione **testar** para testar a origem de dados. Quando a origem de dados está configurada corretamente, o resultado do teste mostra **êxito**.  

1. Selecione **OK** para fechar a janela de teste.  

1. Selecione **OK** para fechar a **Microsoft Hive ODBC Driver de DSN Setup** janela.  

1. Selecione **OK** para fechar a **administrador de fonte de dados de ODBC** janela.  

## <a name="query-data-with-powershell"></a>Consultar dados com o PowerShell

O seguinte script do PowerShell é uma função que ODBC para consultar um cluster do Hive.

```powershell
function Get-ODBC-Data {

   param(
   [string]$query=$(throw 'query is required.'),
   [string]$dsn,  
   [PSCredential] $cred = (Get-Credential)  
   )

   $conn = New-Object System.Data.Odbc.OdbcConnection
   $uname = $cred.UserName

   $pswd = (New-Object System.Net.NetworkCredential -ArgumentList "", $cred.Password).Password
   $conn.ConnectionString = "DSN=$dsn;Uid=$uname;Pwd=$pswd;"
   $conn.open()
   $cmd = New-object System.Data.Odbc.OdbcCommand($query,$conn)

   $ds = New-Object system.Data.DataSet

   (New-Object system.Data.odbc.odbcDataAdapter($cmd)).fill($ds) #| out-null
   $conn.close()
   $ds.Tables
}
```

O fragmento de código seguinte utiliza a função acima para executar uma consulta no cluster do Interactive Query que criou no início do tutorial. Substitua `DATASOURCENAME` com o **nome da origem de dados** que especificou no **Microsoft Hive ODBC Driver de DSN Setup** ecrã. Quando lhe forem pedidas credenciais, introduza o nome de utilizador e palavra-passe que introduziu sob **nome de utilizador de início de sessão de Cluster** e **palavra-passe de início de sessão do Cluster** quando criou o cluster.

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, o cluster do HDInsight e a conta de armazenamento. Para tal, selecione o grupo de recursos onde foi criado o cluster e clique em **eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a utilizar o controlador Microsoft Hive ODBC e o PowerShell para recuperar dados do seu cluster Azure HDInsight Interactive Query.

> [!div class="nextstepaction"]
> [Ligar o Excel para o Apache Hive através de ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
