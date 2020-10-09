---
title: 'Tutorial: Utilize uma identidade gerida para aceder à Base de Dados Azure SQL - Windows - Azure AD'
description: Um tutorial que o acompanha através do processo de utilização de uma identidade gerida pelo sistema Windows VM para aceder à Base de Dados Azure SQL.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f4f56ce9fa86dc27b77ad6b463479d13c8e4e7d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856517"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-sql"></a>Tutorial: Utilizar uma identidade gerida atribuída pelo sistema numa VM do Windows para aceder ao SQL do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra-lhe como usar uma identidade atribuída ao sistema para uma máquina virtual Do Windows (VM) para aceder à Base de Dados Azure SQL. As Identidades de Serviço Geridas são geridas automaticamente pelo Azure e permitem-lhe fazer a autenticação em serviços que suportam a autenticação do Azure AD, sem ser necessário inserir as credenciais no seu código. Saiba como:

> [!div class="checklist"]
>
> * Conceda o seu acesso VM à Base de Dados Azure SQL
> * Ativar a autenticação AD do Azure
> * Criar um utilizador contido na base de dados que representa a identidade atribuída pelo sistema da VM
> * Obtenha um token de acesso usando a identidade VM e use-o para consultar a Base de Dados Azure SQL

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="enable"></a>Ativar

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]

## <a name="grant-access"></a>Conceder acesso

Para conceder o acesso vm a uma base de dados na Base de Dados Azure SQL, pode utilizar um [servidor SQL lógico](../../azure-sql/database/logical-servers.md) existente ou criar um novo. Para criar um novo servidor e base de dados com o portal do Azure, siga este [início rápido do SQL do Azure](../../azure-sql/database/single-database-create-quickstart.md). Também existem inícios rápidos que utilizam a CLI do Azure e o Azure PowerShell na [documentação do SQL do Azure](/azure/sql-database/).

Existem dois passos para conceder acesso da sua VM a uma base de dados:

1. Ativar a autenticação AD AD do servidor.
2. Criar um **utilizador contido** na base de dados que representa a identidade atribuída pelo sistema da VM.

### <a name="enable-azure-ad-authentication"></a>Ativar a autenticação AD do Azure

**Para [configurar a autenticação Azure Ad:](../../azure-sql/database/authentication-aad-configure.md)**

1. No portal do Azure, selecione **SQL Servers** no painel de navegação esquerdo.
2. Clique no SQL Server para ser ativado para autenticação do Azure AD.
3. Na secção **Definições** do painel, clique em **Administrador do Active Directory**.
4. Na barra de comandos, clique em **Definir administrador**.
5. Selecione uma conta de utilizador do Azure AD para se tornar num administrador do servidor e clique em **Selecionar.**
6. Na barra de comandos, clique em **Guardar.**

### <a name="create-contained-user"></a>Criar utilizador contido

Esta secção mostra como criar um utilizador contido na base de dados que representa a identidade atribuída pelo sistema VM. Para este passo, você precisa do [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Antes de começar, também pode ser útil rever os artigos seguintes para obter informações sobre a integração do Azure AD:

- [Autenticação Universal com Base de Dados SQL e Azure Synapse Analytics (suporte SSMS para MFA)](../../azure-sql/database/authentication-mfa-ssms-overview.md)
- [Configurar e gerir a autenticação do Azure Ative Directory com base de dados SQL ou Azure Synapse Analytics](../../azure-sql/database/authentication-aad-configure.md)

SQL DB requer nomes exclusivos de exibição de AAD. Com isto, as contas da AAD, tais como utilizadores, grupos e principais de serviços (aplicações) e nomes de VM habilitados para identidade gerida devem ser definidos exclusivamente em AAD no que diz respeito aos seus nomes de exibição. SQL DB verifica o nome de exibição AAD durante a criação de T-SQL de tais utilizadores e, se não for único, o comando não solicita fornecer um nome de exibição AAD único para uma determinada conta.

**Para criar um utilizador contido:**

1. Inicie o SQL Server Management Studio.
2. No diálogo **'Ligar ao Servidor',** insira o nome do servidor no campo **nome do Servidor.**
3. No campo **Autenticação**, selecione **Active Directory - Universal com suporte de MFA**.
4. No campo **Nome de utilizador**, introduza o nome da conta do Azure AD que definiu como administrador do servidor, por exemplo helen@woodgroveonline.com
5. Clique em **Opções**.
6. No campo **Ligar à base de dados**, introduza o nome da base de dados não pertencente ao sistema que pretende configurar.
7. Clique em **Ligar**. Conclua o processo de início de sessão.
8. No **Object Explorer**, expanda a pasta **Databases**.
9. Faça duplo clique numa base de dados de utilizador e clique em **Nova consulta**.
10. Na janela de consulta, introduza a seguinte linha e clique em **Executar** na barra de ferramentas:

    > [!NOTE]
    > `VMName` no comando seguinte é o nome da VM em que ativou a identidade atribuída pelo sistema na secção de pré-requisitos.

    ```sql
    CREATE USER [VMName] FROM EXTERNAL PROVIDER
    ```

    O comando deve ser concluído com êxito, ao criar o utilizador contido para a identidade atribuída pelo sistema da VM.
11. Limpe a janela de consulta, introduza a seguinte linha e clique em **Executar** na barra de ferramentas:

    > [!NOTE]
    > `VMName` no comando seguinte é o nome da VM em que ativou a identidade atribuída pelo sistema na secção de pré-requisitos.

    ```sql
    ALTER ROLE db_datareader ADD MEMBER [VMName]
    ```

    O comando deve ser concluído com êxito, concedendo ao utilizador contido a capacidade de ler a base de dados completa.

O código em execução no VM pode agora obter um token usando a sua identidade gerida atribuída pelo sistema e usar o token para autenticar o servidor.

## <a name="access-data"></a>Aceder a dados

Esta secção mostra como obter um token de acesso usando a identidade gerida atribuída pelo sistema VM e usá-lo para chamar Azure SQL. O SQL do Azure suporta nativamente a autenticação do Azure AD para que possa aceitar diretamente tokens de acesso obtidos através de identidades geridas para recursos do Azure. Vai utilizar o método de **token de acesso** de criação de uma ligação para o SQL. Isto faz parte da integração do SQL do Azure no Azure AD e é diferente de fornecer as credenciais na cadeia de ligação.

Aqui está um exemplo de código .NET de abertura de uma ligação ao SQL usando um token de acesso. O código deve ser executado no VM para poder aceder ao ponto final da identidade gerida pelo sistema VM. **.Net Framework 4.6** ou superior ou **.NET Core 2.2** ou superior é necessário para utilizar o método de token de acesso. Substitua os valores de AZURE-SQL-SERVERNAME e DATABASE em conformidade. Note que o ID de recursos para Azure SQL é `https://database.windows.net/` .

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://database.windows.net/");
request.Headers["Metadata"] = "true";
request.Method = "GET";
string accessToken = null;

try
{
    // Call managed identities for Azure resources endpoint.
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader and extract access token.
    StreamReader streamResponse = new StreamReader(response.GetResponseStream());
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

//
// Open a connection to the server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

Em alternativa, uma forma rápida de testar a configuração ponto a ponto sem ter de escrever e implementar uma aplicação na VM é utilizando o PowerShell.

1. No portal, navegue para **Máquinas Virtuais**, aceda à sua máquina virtual do Windows e, em **Descrição Geral**, clique em **Ligar**.
2. Introduza o seu **Nome de Utilizador** e a **Palavra-passe** que adicionou quando criou a VM do Windows.
3. Agora que criou uma **Ligação de Ambiente de Trabalho Remoto** com a máquina virtual, abra o **PowerShell** na sessão remota.
4. Através de `Invoke-WebRequest` do PowerShell, faça um pedido ao ponto final da identidade gerida local para obter um token de acesso para o SQL do Azure.

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatabase.windows.net%2F' -Method GET -Headers @{Metadata="true"}
    ```

    Converta a resposta de um objeto JSON num objeto do PowerShell.

    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Extraia o token de acesso da resposta.

    ```powershell
    $AccessToken = $content.access_token
    ```

5. Abra uma ligação ao servidor. Lembre-se de substituir os valores de AZURE-SQL-SERVERNAME e DATABASE.

    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Em seguida, crie e envie uma consulta para o servidor. Lembre-se de substituir o valor de TABLE.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Examine o valor de `$DataSet.Tables[0]` para ver os resultados da consulta.

## <a name="disable"></a>Desativar

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar uma identidade gerida atribuída ao sistema para aceder à Base de Dados Azure SQL. Para saber mais sobre a Base de Dados Azure SQL consulte:

> [!div class="nextstepaction"]
> [Base de Dados SQL do Azure](../../azure-sql/database/sql-database-paas-overview.md)
