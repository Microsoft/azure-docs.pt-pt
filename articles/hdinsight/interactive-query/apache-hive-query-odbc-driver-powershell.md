---
title: Consulta Apache Hive com o condutor da ODBC & PowerShell - Azure HDInsight
description: Utilize o controlador Microsoft Hive ODBC e powerShell para consultar os clusters Apache Hive no Azure HDInsight.
keywords: colmeia,hive odbc,powershell
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.openlocfilehash: f6139bb98fa0272e43c8e180d4ec029f7a7538bb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73494323"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>Tutorial: Colmeia De AnaApache com ODBC e PowerShell

Os controladores Microsoft ODBC fornecem uma forma flexível de interagir com diferentes tipos de fontes de dados, incluindo a Apache Hive. Pode escrever código em scripts linguísticos como o PowerShell que usam os controladores ODBC para abrir uma ligação ao seu cluster da Colmeia, passar uma consulta à sua escolha e exibir os resultados.

Neste tutorial, você fará as seguintes tarefas:

> [!div class="checklist"]
> * Descarregue e instale o controlador Microsoft Hive ODBC
> * Crie uma fonte de dados Apache Hive ODBC ligada ao seu cluster
> * Informações de amostra de consulta do seu cluster usando PowerShell

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter os seguintes itens:

* Um cluster de consulta interativa no HDInsight. Para criar um, consulte Iniciar com [Azure HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). **Selecione Consulta Interativa** como o tipo de cluster.

## <a name="install-microsoft-hive-odbc-driver"></a>Instale o controlador Microsoft Hive ODBC

Descarregue e instale o [Condutor Microsoft Hive ODBC](https://www.microsoft.com/download/details.aspx?id=40886).

## <a name="create-apache-hive-odbc-data-source"></a>Criar fonte de dados apache Hive ODBC

Os seguintes passos mostram-lhe como criar uma fonte de dados Apache Hive ODBC.

1. A partir do Windows, navegue para **iniciar** > **as Ferramentas Administrativas** > do Windows**Fontes de Dados ODBC (32 bits)/(64-bit)**.  Abre-se uma janela do Administrador de Origem de **Dados da ODBC.**

    ![Administrador de fonte de dados da OBDC](./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png "Configure um DSN usando o Administrador de Fonte de Dados da ODBC")

1. A partir do separador **DSN** do utilizador, selecione **Adicionar** para abrir a janela Criar Nova Fonte de **Dados.**

1. Selecione **Microsoft Hive ODBC Driver**, e, em seguida, selecione **Finish** para abrir a janela de **configuração DSN do condutor da Microsoft Hive ODBC.**

1. Escreva ou selecione os seguintes valores:

   | Propriedade | Descrição |
   | --- | --- |
   |  Nome da Origem de Dados |Atribua um nome para a sua origem de dados |
   |  Hospedeiro(s) |Introduza `CLUSTERNAME.azurehdinsight.net`. Por exemplo, `myHDICluster.azurehdinsight.net` |
   |  Porta |Utilize **443**.|
   |  Base de Dados |Utilize o **padrão**. |
   |  Mecanismo |Selecione **serviço HDInsight Windows Azure** |
   |  Nome de Utilizador |Introduza o nome de utilizador do utilizador http do cluster HDInsight. O nome de utilizador predefinido é **admin**. |
   |  Palavra-passe |Introduza a palavra-passe do utilizador do cluster HDInsight. Selecione a caixa de verificação **Guardar palavra-passe (encriptada)**.|

1. Opcional: Selecione **Opções Avançadas**.  

   | Parâmetro | Descrição |
   | --- | --- |
   |  Usar consulta nativa |Quando é selecionado, o condutor da ODBC NÃO tenta converter o TSQL em HiveQL. Utilize esta opção apenas se tiver 100% de certeza de que está a apresentar declarações puras da HiveQL. Ao ligar-se ao SQL Server ou à Base de Dados SQL Azure, deve deixá-lo descontrolado. |
   |  Filas recolhidas por bloco |Ao recolher um grande número de registos, pode ser necessário sintonizar este parâmetro para garantir desempenhos ideais. |
   |  Comprimento da coluna de cadeia padrão, comprimento da coluna binária, escala de coluna decimal |Os comprimentos e precisões do tipo de dados podem afetar a forma como os dados são devolvidos. Fazem com que as informações incorretas sejam devolvidas devido à perda de precisão e truncação. |

    ![Opções avançadas de configuração dSN](./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png "Opções avançadas de configuração dSN")

1. Selecione **Teste** para testar a fonte de dados. Quando a fonte de dados está corretamente configurada, o resultado do teste mostra **O SUCESSO**.  

1. Selecione **OK** para fechar a janela de teste.  

1. Selecione **OK** para fechar a janela de **configuração DSN do condutor da Microsoft Hive ODBC.**  

1. Selecione **OK** para fechar a janela do Administrador de Fonte de **Dados da ODBC.**  

## <a name="query-data-with-powershell"></a>Dados de consulta com PowerShell

O seguinte script PowerShell é uma função que a ODBC para consultar um cluster da Colmeia.

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

O seguinte código de corte utiliza a função acima para executar uma consulta no cluster De consulta interativa que criou no início do tutorial. Substitua-a `DATASOURCENAME` com o **Nome fonte** de dados que especificou no ecrã de **configuração DSN do controlador Microsoft Hive ODBC.** Quando solicitado para obter credenciais, introduza o nome de utilizador e a palavra-passe que introduziu no nome de utilizador do **Cluster** e **na palavra-passe** de login do Cluster quando criou o cluster.

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, o cluster HDInsight e a conta de armazenamento. Para tal, selecione o grupo de recursos onde o cluster foi criado e clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar o controlador Microsoft Hive ODBC e powerShell para recuperar dados do seu cluster de consulta interativa Azure HDInsight.

> [!div class="nextstepaction"]
> [Ligue Excel à Colmeia Apache usando ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
