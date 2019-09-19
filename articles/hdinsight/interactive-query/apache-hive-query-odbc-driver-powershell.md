---
title: Consultar Apache Hive com o driver ODBC e o PowerShell-Azure HDInsight
description: Use o driver ODBC do Microsoft Hive e o PowerShell para consultar Apache Hive clusters no Azure HDInsight.
keywords: Hive, ODBC do hive, PowerShell
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.openlocfilehash: 04771ddc633c210ce8c7b3c42a9e46cb2f1ed349
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122166"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>Tutorial: Apache Hive de consulta com ODBC e PowerShell

Os drivers ODBC da Microsoft fornecem uma maneira flexível de interagir com diferentes tipos de fontes de dados, incluindo Apache Hive. Você pode escrever código em linguagens de script como o PowerShell que usam os drivers ODBC para abrir uma conexão com o cluster do hive, passar uma consulta de sua escolha e exibir os resultados.

Neste tutorial, você executará as seguintes tarefas:

> [!div class="checklist"]
> * Baixar e instalar o driver ODBC do Microsoft Hive
> * Criar uma fonte de dados ODBC Apache Hive vinculada ao seu cluster
> * Consultar informações de exemplo de seu cluster usando o PowerShell

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter os seguintes itens:

* Um cluster de consulta interativa no HDInsight. Para criar um, consulte Introdução [ao Azure HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Selecione **consulta interativa** como o tipo de cluster.

## <a name="install-microsoft-hive-odbc-driver"></a>Instalar o driver ODBC do Microsoft Hive

Baixe e instale o [driver ODBC do Microsoft Hive](https://go.microsoft.com/fwlink/?LinkID=286698).

## <a name="create-apache-hive-odbc-data-source"></a>Criar Apache Hive fonte de dados ODBC

As etapas a seguir mostram como criar uma fonte de dados Apache Hive ODBC.

1. No Windows, navegue até **Iniciar** > as**ferramentas** > administrativas do Windows**fontes de dados ODBC (32 bits)/(64 bits)** .  Uma janela **administrador de fonte de dados ODBC** é aberta.

    ![Administrador de fonte de dados ODBC](./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png "Configurar um DSN usando o administrador de fonte de dados ODBC")

1. Na guia **DSN de usuário** , selecione **Adicionar** para abrir a janela **criar nova fonte de dados** .

1. Selecione **driver ODBC do Microsoft Hive**e, em seguida, selecione **concluir** para abrir a janela **instalação do driver ODBC do Microsoft Hive DSN** .

1. Escreva ou selecione os seguintes valores:

   | Propriedade | Descrição |
   | --- | --- |
   |  Nome da Origem de Dados |Atribua um nome para a sua origem de dados |
   |  Host (s) |Introduza `CLUSTERNAME.azurehdinsight.net`. Por exemplo, `myHDICluster.azurehdinsight.net` |
   |  Port |Utilize **443**.|
   |  Base de Dados |Usar **padrão**. |
   |  Mecanismo |Selecione o **serviço HDInsight do Windows Azure** |
   |  Nome de utilizador |Insira o nome de usuário HTTP do cluster HDInsight. O nome de utilizador predefinido é **admin**. |
   |  Palavra-passe |Insira a senha de usuário do cluster HDInsight. Marque a caixa de seleção **salvar senha (criptografada)** .|

1. Opcional: Selecione **Opções avançadas**.  

   | Parâmetro | Descrição |
   | --- | --- |
   |  Usar consulta nativa |Quando selecionado, o driver ODBC não tenta converter TSQL em HiveQL. Use esta opção somente se você estiver 100% com certeza de que está enviando instruções HiveQL puras. Ao conectar-se ao SQL Server ou ao banco de dados SQL do Azure, deixe-o desmarcado. |
   |  Linhas buscadas por bloco |Ao buscar um grande número de registros, o ajuste desse parâmetro pode ser necessário para garantir o desempenho ideal. |
   |  Comprimento da coluna de cadeia de caracteres padrão, comprimento de coluna binária, escala de coluna decimal |Os comprimentos e as precisão do tipo de dados podem afetar a forma como os dados são retornados. Eles fazem com que informações incorretas sejam retornadas devido à perda de precisão e truncamento. |

    ![Opções de configuração de DSN avançadas](./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png "Opções de configuração de DSN avançadas")

1. Selecione **testar** para testar a fonte de dados. Quando a fonte de dados estiver configurada corretamente, o resultado do teste mostrará **êxito**.  

1. Selecione **OK** para fechar a janela de teste.  

1. Selecione **OK** para fechar a janela **instalação do driver ODBC do Microsoft Hive DSN** .  

1. Selecione **OK** para fechar a janela **administrador de fonte de dados ODBC** .  

## <a name="query-data-with-powershell"></a>Consultar dados com o PowerShell

O script do PowerShell a seguir é uma função que o ODBC deve consultar em um cluster do hive.

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

O trecho de código a seguir usa a função acima para executar uma consulta no cluster de consulta interativa que você criou no início do tutorial. Substitua `DATASOURCENAME` pelo **nome da fonte de dados** que você especificou na tela de instalação do **driver ODBC do Microsoft Hive DSN** . Quando solicitado a fornecer credenciais, insira o nome de usuário e a senha que você inseriu em **nome de usuário de logon do cluster** e senha de logon do **cluster** ao criar o cluster.

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos, o cluster HDInsight e a conta de armazenamento. Para fazer isso, selecione o grupo de recursos em que o cluster foi criado e clique em **excluir**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a usar o driver ODBC do Microsoft Hive e o PowerShell para recuperar dados do seu cluster de consulta interativa do Azure HDInsight.

> [!div class="nextstepaction"]
> [Conectar o Excel ao Apache Hive usando o ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
