---
title: Colmeia Apache consulta com ODBC Driver & PowerShell - Azure HDInsight
description: Utilize o controlador ODBC da Microsoft Hive e o PowerShell para consultar os clusters Apache Hive no Azure HDInsight.
keywords: colmeia,colmeia odbc,powershell
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 34ee7eb25b1d106ea8bb53197d69ca5a9d528773
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871610"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>Tutorial: Consulta Apache Hive com ODBC e PowerShell

Os controladores da Microsoft ODBC fornecem uma forma flexível de interagir com diferentes tipos de fontes de dados, incluindo a Apache Hive. Pode escrever código em idiomas de scripts como PowerShell que usam os controladores ODBC para abrir uma ligação ao seu cluster Hive, passar uma consulta à sua escolha e exibir os resultados.

Neste tutorial, fará as seguintes tarefas:

> [!div class="checklist"]
> * Descarregue e instale o controlador ODBC da Microsoft Hive
> * Crie uma fonte de dados Apache Hive ODBC ligada ao seu cluster
> * Consulta informações de amostra do seu cluster usando PowerShell

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter os seguintes itens:

* Um cluster de consultainternação em HDInsight. Para criar um, consulte [Começar com Azure HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Selecione **a Consulta Interativa** como o tipo de cluster.

## <a name="install-microsoft-hive-odbc-driver"></a>Instale o controlador ODBC da Microsoft Hive

Descarregue e instale o [Controlador ODBC da Microsoft Hive](https://www.microsoft.com/download/details.aspx?id=40886).

## <a name="create-apache-hive-odbc-data-source"></a>Criar fonte de dados Apache Hive ODBC

Os passos seguintes mostram-lhe como criar uma fonte de dados Apache Hive ODBC.

1. A partir do Windows, navegue para **iniciar**  >  **ferramentas administrativas do Windows**  >  **ODBC Data Sources (32-bit)/(64-bit)**.  Abre-se uma janela **do administrador de fonte de dados ODBC.**

    :::image type="content" source="./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png " alt-text="Administrador de fonte de dados OBDC" border="true":::

1. A partir do **separador DSN do utilizador,** selecione **Adicionar** para abrir a janela **Criar Nova Fonte de Dados.**

1. Selecione **Microsoft Hive ODBC Driver** e, em seguida, selecione **Finish** para abrir a janela **de configuração DSN do controlador do Microsoft Hive ODBC.**

1. Escreva ou selecione os seguintes valores:

   | Propriedade | Descrição |
   | --- | --- |
   |  Nome da Origem de Dados |Atribua um nome para a sua origem de dados |
   |  Anfitriões ou anfitriões |Introduza `CLUSTERNAME.azurehdinsight.net`. Por exemplo, `myHDICluster.azurehdinsight.net` |
   |  Porta |Utilize **443**.|
   |  Base de Dados |Utilizar **o padrão**. |
   |  Mecanismo |Selecione **Windows Azure HDInsight Service** |
   |  Nome de Utilizador |Introduza o nome de utilizador do grupo HDInsight HTTP. O nome de utilizador predefinido é **admin**. |
   |  Palavra-passe |Insira a palavra-passe do utilizador do cluster HDInsight. Selecione a caixa de verificação **Guardar palavra-passe (encriptada)**.|

1. Opcional: Selecione **Opções Avançadas**.  

   | Parâmetro | Descrição |
   | --- | --- |
   |  Utilizar consulta nativa |Quando é selecionado, o controlador ODBC NÃO tenta converter OSQL em HiveQL. Use esta opção apenas se tiver 100% de certeza de que está a enviar declarações puras da HiveQL. Ao ligar ao SQL Server ou à Base de Dados Azure SQL, deve deixá-lo desmarcado. |
   |  Linhas recolhidas por bloco |Ao obter um grande número de registos, pode ser necessário sintonizar este parâmetro para garantir um desempenho ótimo. |
   |  Comprimento da coluna de corda padrão, comprimento da coluna binária, escala de coluna decimal |Os comprimentos e precisões do tipo de dados podem afetar a forma como os dados são devolvidos. Fazem com que a informação incorreta seja devolvida devido à perda de precisão e de truncação. |

    :::image type="content" source="./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png " alt-text="Opções avançadas de configuração do DSN" border="true":::

1. Selecione **Teste** para testar a fonte de dados. Quando a fonte de dados está configurada corretamente, o resultado do teste mostra **SUCCESS**.  

1. Selecione **OK** para fechar a janela de teste.  

1. Selecione **OK** para fechar a janela **de configuração DSN do controlador da Colmeia Microsoft.**  

1. Selecione **OK** para fechar a janela **do administrador de dados ODBC.**  

## <a name="query-data-with-powershell"></a>Dados de consulta com PowerShell

O seguinte script PowerShell é uma função que o ODBC para consultar um cluster hive.

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

O seguinte corte de código utiliza a função acima para executar uma consulta no cluster de consultainterquesintere que criou no início do tutorial. `DATASOURCENAME`Substitua-o pelo **Nome de Origem de Dados** especificado no ecrã **de configuração DSN do controlador do Microsoft Hive ODBC.** Quando solicitado para obter credenciais, insira o nome de utilizador e a palavra-passe que inseriu no **nome de utilizador de login do Cluster** e na **palavra-passe de login do Cluster** quando criou o cluster.

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos, o cluster HDInsight e a conta de armazenamento. Para tal, selecione o grupo de recursos onde o cluster foi criado e clique em **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar o controlador ODBC da Microsoft Hive e o PowerShell para obter dados do seu cluster de consultas interativas Azure HDInsight.

> [!div class="nextstepaction"]
> [Conecte Excel à Colmeia Apache usando ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
