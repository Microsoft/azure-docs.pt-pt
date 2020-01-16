---
title: Tutorial`:` usar uma identidade gerenciada para acessar o Azure SQL-Windows-Azure AD
description: Um tutorial que explica o processo de utilização de uma identidade gerida atribuída pelo sistema numa VM do Windows para aceder ao SQL do Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fc5596c6914b77b09db10528af891d7e6bd0159
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977868"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-sql"></a>Tutorial: Utilizar uma identidade gerida atribuída pelo sistema numa VM do Windows para aceder ao SQL do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra-lhe como utilizar a identidade atribuída pelo sistema para uma máquina virtual (VM) do Windows para aceder a um servidor SQL do Azure. As Identidades de Serviço Geridas são geridas automaticamente pelo Azure e permitem-lhe fazer a autenticação em serviços que suportam a autenticação do Azure AD, sem ser necessário inserir as credenciais no seu código. Saiba como:

> [!div class="checklist"]
> * Conceder à sua VM acesso a um SQL Server do Azure
> * Ativar a autenticação do Azure AD para o SQL Server
> * Criar um utilizador contido na base de dados que representa a identidade atribuída pelo sistema da VM
> * Obter um token de acesso com a identidade da VM e utilizá-la para consultar um SQL Server do Azure

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]


## <a name="enable"></a>Ativar

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]


## <a name="grant-access"></a>Conceder acesso

Para conceder acesso da VM a uma base de dados num Azure SQL Server do Azure, pode utilizar um SQL server existente ou criar um novo. Para criar um novo servidor e base de dados com o portal do Azure, siga este [início rápido do SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal). Também existem inícios rápidos que utilizam a CLI do Azure e o Azure PowerShell na [documentação do SQL do Azure](https://docs.microsoft.com/azure/sql-database/).

Existem dois passos para conceder acesso da sua VM a uma base de dados:

1. Ative a autenticação do Azure AD para o SQL Server.
2. Criar um **utilizador contido** na base de dados que representa a identidade atribuída pelo sistema da VM.

### <a name="enable-azure-ad-authentication"></a>Habilitar a autenticação do Azure AD

**Para [Configurar a autenticação do Azure ad para o SQL Server](/azure/sql-database/sql-database-aad-authentication-configure):**

1.  No portal do Azure, selecione **SQL Servers** no painel de navegação esquerdo.
2.  Clique no SQL Server para ser ativado para autenticação do Azure AD.
3.  Na secção **Definições** do painel, clique em **Administrador do Active Directory**.
4.  Na barra de comandos, clique em **Definir administrador**.
5.  Selecione uma conta de utilizador do Azure AD para se tornar num administrador do servidor e clique em **Selecionar.**
6.  Na barra de comandos, clique em **Guardar.**

### <a name="create-contained-user"></a>Criar usuário independente

Esta seção mostra como criar um usuário independente no banco de dados que representa a identidade atribuída pelo sistema da VM. Para esta etapa, você precisa de [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Antes de começar, também pode ser útil rever os artigos seguintes para obter informações sobre a integração do Azure AD:

- [Autenticação Universal com a Base de Dados SQL e o SQL Data Warehouse (suporte do SSMS para MFA)](/azure/sql-database/sql-database-ssms-mfa-authentication)
- [Configurar e gerir a autenticação do Azure Active Directory com a Base de Dados SQL ou o SQL Data Warehouse](/azure/sql-database/sql-database-aad-authentication-configure)

O banco de BD SQL requer nomes de exibição do AAD exclusivos. Com isso, as contas do AAD, como usuários, grupos e entidades de serviço (aplicativos) e nomes de VM habilitados para identidade gerenciada devem ser definidas exclusivamente no AAD em relação aos nomes de exibição. O banco de dados SQL verifica o nome de exibição do AAD durante a criação do T-SQL de tais usuários e, se não for exclusivo, o comando falha ao solicitar para fornecer um nome de exibição do AAD exclusivo para uma determinada conta.

**Para criar um usuário independente:**

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

## <a name="access-data"></a>Aceder a dados

Esta seção mostra como obter um token de acesso usando a identidade gerenciada atribuída pelo sistema da VM e usá-la para chamar o SQL do Azure. O SQL do Azure suporta nativamente a autenticação do Azure AD para que possa aceitar diretamente tokens de acesso obtidos através de identidades geridas para recursos do Azure. Vai utilizar o método de **token de acesso** de criação de uma ligação para o SQL. Isto faz parte da integração do SQL do Azure no Azure AD e é diferente de fornecer as credenciais na cadeia de ligação.

Aqui está um exemplo de código .NET de abertura de uma conexão com o SQL usando um token de acesso. Este código tem de ser executado na VM para poder aceder ao ponto final da identidade gerida atribuída pelo sistema da VM. **.NET Framework 4,6** ou superior ou o **.NET Core 2,2** ou superior é necessário para usar o método de token de acesso. Substitua os valores de AZURE-SQL-SERVERNAME e DATABASE em conformidade. Observe que a ID de recurso do Azure SQL é `https://database.windows.net/`.

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


## <a name="disable"></a>Desativar

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a utilizar uma identidade gerida atribuída pelo sistema para aceder ao servidor SQL do Azure. Para saber mais sobre o SQL Server do Azure, veja:

> [!div class="nextstepaction"]
> [Serviço Base de Dados SQL do Azure](/azure/sql-database/sql-database-technical-overview)
