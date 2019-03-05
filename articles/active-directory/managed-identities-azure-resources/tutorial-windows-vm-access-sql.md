---
title: Utilize uma VM do Windows atribuídos de sistema identidade gerida para aceder ao SQL do Azure
description: Um tutorial que explica o processo de utilização de uma identidade gerida atribuída pelo sistema numa VM do Windows para aceder ao SQL do Azure.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: bryanla
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/07/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: a269c15b9b38b190196e6c2e77ff5b4b3826ba65
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57342171"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-sql"></a>Tutorial: Utilize uma VM do Windows atribuídos de sistema identidade gerida para aceder ao SQL do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra-lhe como utilizar a identidade atribuída pelo sistema para uma máquina virtual (VM) do Windows para aceder a um servidor SQL do Azure. As Identidades de Serviço Geridas são geridas automaticamente pelo Azure e permitem-lhe fazer a autenticação em serviços que suportam a autenticação do Azure AD, sem ser necessário inserir as credenciais no seu código. Saiba como:

> [!div class="checklist"]
> * Conceder à sua VM acesso a um SQL Server do Azure
> * Ativar a autenticação do Azure AD para o SQL Server
> * Criar um utilizador contido na base de dados que representa a identidade atribuída pelo sistema da VM
> * Obter um token de acesso com a identidade da VM e utilizá-la para consultar um SQL Server do Azure

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>Conceder à sua VM acesso a uma base de dados num SQL Server do Azure

Para conceder acesso da VM a uma base de dados num Azure SQL Server do Azure, pode utilizar um SQL server existente ou criar um novo. Para criar um novo servidor e base de dados com o portal do Azure, siga este [início rápido do SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal). Também existem inícios rápidos que utilizam a CLI do Azure e o Azure PowerShell na [documentação do SQL do Azure](https://docs.microsoft.com/azure/sql-database/).

Existem dois passos para conceder acesso da sua VM a uma base de dados:

1. Ative a autenticação do Azure AD para o SQL Server.
2. Criar um **utilizador contido** na base de dados que representa a identidade atribuída pelo sistema da VM.

## <a name="enable-azure-ad-authentication-for-the-sql-server"></a>Ativar a autenticação do Azure AD para o SQL Server

[Configurar a autenticação do Azure AD para o SQL server](/azure/sql-database/sql-database-aad-authentication-configure) através dos seguintes passos:

1.  No portal do Azure, selecione **SQL Servers** no painel de navegação esquerdo.
2.  Clique no SQL Server para ser ativado para autenticação do Azure AD.
3.  Na secção **Definições** do painel, clique em **Administrador do Active Directory**.
4.  Na barra de comandos, clique em **Definir administrador**.
5.  Selecione uma conta de utilizador do Azure AD para se tornar num administrador do servidor e clique em **Selecionar.**
6.  Na barra de comandos, clique em **Guardar.**

## <a name="create-a-contained-user-in-the-database-that-represents-the-vms-system-assigned-identity"></a>Criar um utilizador contido na base de dados que representa a identidade atribuída pelo sistema da VM

Para este próximo passo, vai precisar do [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Antes de começar, também pode ser útil rever os artigos seguintes para obter informações sobre a integração do Azure AD:

- [Autenticação Universal com a Base de Dados SQL e o SQL Data Warehouse (suporte do SSMS para MFA)](/azure/sql-database/sql-database-ssms-mfa-authentication)
- [Configurar e gerir a autenticação do Azure Active Directory com a Base de Dados SQL ou o SQL Data Warehouse](/azure/sql-database/sql-database-aad-authentication-configure)

BD SQL requer que os nomes a apresentar exclusivos AAD. Com isso, o AAD contas como utilizadores, grupos e principais de serviço (aplicações) e nomes VM ativados para a identidade gerida tem de ser exclusivamente definidos no AAD em relação a seus nomes de exibição. BD SQL verifica o nome a apresentar do AAD durante a criação de T-SQL de tais usuários e se não for exclusivo, o comando falha a pedir para fornecer um nome exclusivo para a exibição AAD numa dada conta.

1. Inicie o SQL Server Management Studio.
2. Na caixa de diálogo **Ligar ao Servidor**, introduza o nome do seu SQL Server no campo **Nome do servidor**.
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
    
    ```
    CREATE USER [VMName] FROM EXTERNAL PROVIDER
    ```
    
    O comando deve ser concluído com êxito, ao criar o utilizador contido para a identidade atribuída pelo sistema da VM.
11. Limpe a janela de consulta, introduza a seguinte linha e clique em **Executar** na barra de ferramentas:

    > [!NOTE]
    > `VMName` no comando seguinte é o nome da VM em que ativou a identidade atribuída pelo sistema na secção de pré-requisitos.
    
    ```
    ALTER ROLE db_datareader ADD MEMBER [VMName]
    ```

    O comando deve ser concluído com êxito, concedendo ao utilizador contido a capacidade de ler a base de dados completa.

O código em execução na VM pode agora obter um token através da identidade gerida atribuída pelo sistema e utilizar o token para fazer a autenticação no SQL Server.

## <a name="get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-sql"></a>Obter um token de acesso com a identidade gerida atribuída pelo sistema da VM e utilizá-lo para chamar o SQL do Azure

O SQL do Azure suporta nativamente a autenticação do Azure AD para que possa aceitar diretamente tokens de acesso obtidos através de identidades geridas para recursos do Azure. Vai utilizar o método de **token de acesso** de criação de uma ligação para o SQL. Isto faz parte da integração do SQL do Azure no Azure AD e é diferente de fornecer as credenciais na cadeia de ligação.

Eis um exemplo de código .Net para abrir uma ligação para o SQL com um token de acesso. Este código tem de ser executado na VM para poder aceder ao ponto final da identidade gerida atribuída pelo sistema da VM. **.NET framework 4.6** ou superior, é necessário usar o método de token de acesso. Substitua os valores de AZURE-SQL-SERVERNAME e DATABASE em conformidade. Tenha em atenção que o ID de recurso para o SQL do Azure é "https://database.windows.net/".

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
// Open a connection to the SQL server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

Em alternativa, uma forma rápida de testar a configuração ponto a ponto sem ter de escrever e implementar uma aplicação na VM é utilizando o PowerShell.

1.  No portal, navegue para **Máquinas Virtuais**, aceda à sua máquina virtual do Windows e, em **Descrição Geral**, clique em **Ligar**.
2.  Introduza o seu **Nome de Utilizador** e a **Palavra-passe** que adicionou quando criou a VM do Windows.
3.  Agora que já criou uma **Ligação ao Ambiente de Trabalho Remoto** com a máquina virtual, abra o **PowerShell** na sessão remota.
4.  Através de `Invoke-WebRequest` do PowerShell, faça um pedido ao ponto final da identidade gerida local para obter um token de acesso para o SQL do Azure.

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

5. Abra uma ligação para o SQL Server. Lembre-se de substituir os valores de AZURE-SQL-SERVERNAME e DATABASE.
    
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

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a utilizar uma identidade gerida atribuída pelo sistema para aceder ao servidor SQL do Azure. Para saber mais sobre o SQL Server do Azure, veja:

> [!div class="nextstepaction"]
> [Serviço Base de Dados SQL do Azure](/azure/sql-database/sql-database-technical-overview)
