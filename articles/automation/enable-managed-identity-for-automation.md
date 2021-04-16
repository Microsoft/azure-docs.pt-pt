---
title: Ativar uma identidade gerida para a sua conta Azure Automation (pré-visualização)
description: Este artigo descreve como configurar a identidade gerida para as contas da Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: 93c55c21bf740f2851cac1926bc673cebcd914b0
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514806"
---
# <a name="enable-a-managed-identity-for-your-azure-automation-account-preview"></a>Ativar uma identidade gerida para a sua conta Azure Automation (pré-visualização)

Este tópico mostra-lhe como criar uma identidade gerida para uma conta Azure Automation e como usá-la para aceder a outros recursos. Para obter mais informações sobre como a identidade gerida funciona com a Azure Automation, consulte [identidades geridas.](automation-security-overview.md#managed-identities-preview)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta e subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Tanto a identidade gerida como os recursos Azure alvo que o seu runbook gere usando essa identidade devem estar na mesma subscrição do Azure.

- A versão mais recente dos módulos de conta Azure Automation. Atualmente isto é 1.6.0. (Ver [Az.Automation 1.6.0](https://www.powershellgallery.com/packages/Az.Automation/1.6.0) para mais detalhes sobre esta versão.)

- Um recurso Azure a que pretende aceder a partir do seu livro de modelos Automation. Este recurso necessita de ter uma função definida para a identidade gerida, o que ajuda o livro de aplicação da Automação a autenticar o acesso ao recurso. Para adicionar funções, você precisa ser proprietário para o recurso no inquilino AD Azure correspondente.

- Se pretender executar trabalhos híbridos utilizando uma identidade gerida, atualize o Trabalhador De Runbook Híbrido para a versão mais recente. As versões mínimas exigidas são:

   - Trabalhador do Runbook Híbrido do Windows: versão 7.3.1125.0
   - Linux Hybrid Runbook Worker: versão 1.7.4.0

## <a name="enable-system-assigned-identity"></a>Ativar a identidade atribuída ao sistema

>[!IMPORTANT]
>A nova identidade ao nível da conta Automation irá sobrepor-se a quaisquer identidades anteriores atribuídas ao sistema ao nível de VM (que são descritas na [autenticação do runbook de utilização com identidades geridas).](/automation-hrw-run-runbooks#runbook-auth-managed-identities) Se estiver a executar trabalhos híbridos em VMs Azure que usam a identidade atribuída a um VM para aceder a recursos de runbook, então a identidade da conta Automation será usada para os trabalhos híbridos. Isto significa que a sua execução de emprego existente pode ser afetada se tiver usado a função De Chaves Geridas pelo Cliente (CMK) da sua conta de Automação.<br/><br/>Se desejar continuar a utilizar a identidade gerida do VM, não deverá ativar a identidade de nível de conta automation. Se já o ativou, pode desativar a identidade gerida pela conta Automation. Consulte [a sua conta Azure Automation gerida identidade](https://docs.microsoft.com/azure/automation/disable-managed-identity-for-automation).

A criação de identidades atribuídas ao sistema para a Azure Automation pode ser feita de duas formas. Pode utilizar o portal Azure ou a API Azure REST.

>[!NOTE]
>As identidades atribuídas ao utilizador ainda não são suportadas.

### <a name="enable-system-assigned-identity-in-azure-portal"></a>Ativar a identidade atribuída ao sistema no portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Navegue na sua conta de Automação e selecione **identidade** em **Definições de Conta.**

1. Desa estada a opção **do Sistema** para **On** e prima **Guardar**. Quando lhe for pedido que confirme, selecione **Sim**.

:::image type="content" source="media/managed-identity/managed-identity-on.png" alt-text="Habilitando a identidade atribuída ao sistema no portal Azure.":::

A sua conta Automation pode agora utilizar a identidade atribuída ao sistema, que está registada no Azure Ative Directory (Azure AD) e é representada por um ID de objeto.

:::image type="content" source="media/managed-identity/managed-identity-object-id.png" alt-text="Identificação de objeto de identidade gerida.":::

### <a name="enable-system-assigned-identity-through-the-rest-api"></a>Ativar a identidade atribuída ao sistema através da API REST

Pode configurar uma identidade gerida atribuída pelo sistema à conta Automation, utilizando a seguinte chamada REST API.

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Corpo do pedido
```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 .
 .
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
.
.
}
```

|Propriedade (JSON) | Valor | Descrição|
|----------|-----------|------------|
| principado | \<principal-ID\> | O Identificador Globalmente Único (GUID) do objeto principal de serviço para a identidade gerida que representa a sua conta de Automação no inquilino AZURE AD. Este GUID às vezes aparece como um "ID de objeto" ou objectID. |
| inquilino | \<Azure-AD-tenant-ID\> | O Identificador Globalmente Único (GUID) que representa o inquilino AD AZure onde a conta Automation é agora membro. Dentro do inquilino da AD Azure, o diretor de serviço tem o mesmo nome que a conta Automation. |

## <a name="give-identity-access-to-azure-resources-by-obtaining-a-token"></a>Dar acesso identitário aos recursos da Azure através da obtenção de um símbolo

Uma conta Automation pode usar a sua identidade gerida para obter fichas para aceder a outros recursos protegidos pela Azure AD, como o Azure Key Vault. Estes tokens não representam nenhum utilizador específico da aplicação. Em vez disso, representam a aplicação que está a aceder ao recurso. Neste caso, por exemplo, o símbolo representa uma conta Demôm automação.

Antes de poder utilizar a identidade gerida atribuída ao sistema para a autenticação, confiem o acesso a essa identidade no recurso Azure, onde planeia utilizar a identidade. Para completar esta tarefa, atribua o papel adequado a essa identidade no recurso Target Azure.

Este exemplo utiliza a Azure PowerShell para mostrar como atribuir o papel do Contribuinte na subscrição do recurso Azure alvo. A função Contribuidora é usada como exemplo, podendo ou não ser necessária no seu caso.

```powershell
New-AzRoleAssignment -ObjectId <automation-Identity-object-id> -Scope "/subscriptions/<subscription-id>" -RoleDefinitionName "Contributor"
```

## <a name="authenticate-access-with-managed-identity"></a>Autenticar acesso com identidade gerida

Depois de ativar a identidade gerida para a sua conta Demôm automação e dar um acesso identitário ao recurso-alvo, pode especificar essa identidade em livros de execução contra recursos que suportem a identidade gerida. Para suporte de identidade, utilize o `Connect-AzAccount` cmdlet Az. Consulte [o Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) na referência PowerShell.

```powershell
Connect-AzAccount -Identity
```

>[!NOTE]
>Se a sua organização ainda estiver a utilizar os cmdlets AzureRM prevaricados, pode utilizar `Connect-AzureRMAccount -Identity` .

## <a name="generate-an-access-token-without-using-azure-cmdlets"></a>Gerar um token de acesso sem usar cmdlets Azure

Para HTTP Endpoints certifique-se do seguinte.
- O cabeçalho dos metadados deve estar presente e deve ser definido como "verdadeiro".
- Um recurso deve ser transmitido juntamente com o pedido, como parâmetro de consulta para um pedido GET e como dados de formulário para um pedido DEM.
- O cabeçalho X-IDENTIDADE deve ser definido para o valor da variável ambiental IDENTITY_HEADER para trabalhadores de runbook híbridos. 
- O tipo de conteúdo para o pedido do Post deve ser "aplicação/x-www-forma-urlencoded". 

### <a name="sample-get-request"></a>Pedido de amostra GET

```powershell
$resource= "?resource=https://management.azure.com/" 
$url = $env:IDENTITY_ENDPOINT + $resource 
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$accessToken = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
Write-Output $accessToken.access_token
```

### <a name="sample-post-request"></a>Pedido de correio de amostra
```powershell
$url = $env:IDENTITY_ENDPOINT  
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$headers.Add("Metadata", "True") 
$body = @{resource='https://management.azure.com/' } 
$accessToken = Invoke-RestMethod $url -Method 'POST' -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $body 
Write-Output $accessToken.access_token
```

## <a name="sample-runbooks-using-managed-identity"></a>Livros de amostras utilizando identidade gerida

### <a name="sample-runbook-to-access-a-sql-database-without-using-azure-cmdlets"></a>Livro de amostras para aceder a uma base de dados SQL sem utilizar cmdlets Azure

```powershell
$queryParameter = "?resource=https://database.windows.net/" 
$url = $env:IDENTITY_ENDPOINT + $queryParameter
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$content =[System.Text.Encoding]::Default.GetString((Invoke-WebRequest -UseBasicParsing -Uri $url -Method 'GET' -Headers $Headers).RawContentStream.ToArray()) | ConvertFrom-Json 
$Token = $content.access_token 
echo "The managed identities for Azure resources access token is $Token" 
$SQLServerName = "<ServerName>"    # Azure SQL logical server name  
$DatabaseName = "<DBname>"     # Azure SQL database name 
Write-Host "Create SQL connection string" 
$conn = New-Object System.Data.SqlClient.SQLConnection  
$conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30" 
$conn.AccessToken = $Token 
Write-host "Connect to database and execute SQL script" 
$conn.Open()  
$ddlstmt = "CREATE TABLE Person( PersonId INT IDENTITY PRIMARY KEY, FirstName NVARCHAR(128) NOT NULL)" 
Write-host " " 
Write-host "SQL DDL command" 
$ddlstmt 
$command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn) 
Write-host "results" 
$command.ExecuteNonQuery() 
$conn.Close()
```

### <a name="sample-runbook-to-access-a-key-vault-using-azure-cmdlets"></a>Livro de amostras para aceder a um cofre de chaves usando cmdlets Azure

```powershell
Write-Output "Connecting to azure via  Connect-AzAccount -Identity" 
Connect-AzAccount -Identity 
Write-Output "Successfully connected with Automation account's Managed Identity" 
Write-Output "Trying to fetch value from key vault using MI. Make sure you have given correct access to Managed Identity" 
$secret = Get-AzKeyVaultSecret -VaultName '<KVname>' -Name '<KeyName>' 

$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue) 
try { 
  $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr) 
    Write-Output $secretValueText 
} finally { 
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr) 
}
```

### <a name="sample-python-runbook-to-get-a-token"></a>Prove o livro de corridas python para obter um símbolo
 
```python
#!/usr/bin/env python3 
import os 
import requests  
# printing environment variables 
endPoint = os.getenv('IDENTITY_ENDPOINT')+"?resource=https://management.azure.com/" 
identityHeader = os.getenv('IDENTITY_HEADER') 
payload={} 
headers = { 
  'X-IDENTITY-HEADER': identityHeader,
  'Metadata': 'True' 
} 
response = requests.request("GET", endPoint, headers=headers, data=payload) 
print(response.text) 
```

## <a name="next-steps"></a>Passos seguintes

- Se necessitar de desativar uma identidade gerida, consulte [Desativar a sua conta Azure Automation gerida (pré-visualização)](disable-managed-identity-for-automation.md).

- Para obter uma visão geral da segurança da conta Azure Automation, consulte [a visão geral da autenticação da conta Automation](automation-security-overview.md).