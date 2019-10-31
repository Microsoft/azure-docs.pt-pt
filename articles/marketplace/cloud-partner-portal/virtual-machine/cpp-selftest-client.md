---
title: Cliente de teste automático para validar previamente uma máquina virtual | Azure Marketplace
description: Como criar um cliente de autoteste para validar previamente uma imagem de máquina virtual para o Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, Virtual Machine
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: pabutler
ms.openlocfilehash: 46923ecd33a054a36aa6900a415d0b563e5afff0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163251"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-virtual-machine-image"></a>Criar um cliente de autoteste para validar previamente uma imagem de máquina virtual do Azure

Use este artigo como um guia para criar um serviço de cliente que consome a API de teste automático. Você pode usar a API de teste automático para validar previamente uma VM (máquina virtual) para garantir que ela atenda aos requisitos mais recentes de publicação do Azure Marketplace. Esse serviço de cliente permite testar uma VM antes de enviar sua oferta para certificação da Microsoft.

## <a name="development-and-testing-overview"></a>Visão geral de desenvolvimento e teste

Como parte do processo de teste automático, você criará um cliente local que se conecta ao Azure Marketplace para validar uma VM em execução na sua assinatura do Azure. A VM pode estar executando o sistema operacional Windows ou Linux.

O cliente local executa um script que autentica com a API de teste automático, envia informações de conexão e recebe resultados de teste.

As etapas de alto nível para a criação de um cliente de autoteste são:

1. Escolha o locatário do Azure Active Directory (AD) para seu aplicativo.
2. Registre o aplicativo cliente.
3. Crie um token para o aplicativo cliente do Azure AD.
4. Passe o token para a API de teste automático.

Depois de criar o cliente, você pode testá-lo em sua VM.

### <a name="self-test-client-authorization"></a>Autorização de cliente de teste automático

O diagrama a seguir mostra como a autorização funciona para chamadas de serviço a serviço usando credenciais de cliente (segredo compartilhado ou certificado).

![Processo de autorização do cliente](./media/stclient-dev-process.png)

## <a name="the-self-test-client-api"></a>A API de cliente de teste automático

A API de teste automático contém um único ponto de extremidade que dá suporte apenas ao método POST.  Ele tem a seguinte estrutura.

```
Uri:             https://isvapp.azurewebsites.net/selftest-vm
Method:          Post
Request Header:  Content-Type: "application/json"
Authorization:   "Bearer xxxx-xxxx-xxxx-xxxxx"
Request body:    The Request body parameters should use the following JSON format:
                 {
                   "DNSName":"XXXX.westus.cloudapp.azure.com",
                   "User":"XXX",
                   "Password":"XXX@1234567",
                   "OS":"XXX",
                   "PortNo":"22",
                   "CompanyName":"ABCD",
                 }
```

A tabela a seguir descreve os campos de API.


|      Campo         |    Descrição    |
|  ---------------   |  ---------------  |
|  Autorização     |  A cadeia de caracteres "portador xxxx-xxxx-xxxx-xxxxx" contém o token de cliente Azure Active Directory (AD), que pode ser criado usando o PowerShell.          |
|  DNSName           |  Nome DNS da VM a ser testada    |
|  Utilizador              |  Nome de usuário para entrar na VM         |
|  Palavra-passe          |  Senha para entrar na VM          |
|  SO                |  Sistema operacional da VM: `Linux` ou `Windows`          |
|  PortNo            |  Abra o número da porta para conectar-se à VM. O número da porta geralmente é `22` para Linux e `5986` para Windows.          |
|  |  |

## <a name="consuming-the-api"></a>Consumindo a API

Você pode consumir a API de teste automático usando o PowerShell ou a ondulação.

### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>Usar o PowerShell para consumir a API no sistema operacional Linux

Para chamar a API no PowerShell, siga estas etapas:

1. Use o comando `Invoke-WebRequest` para chamar a API.
2. O método é post e o tipo de conteúdo é JSON, conforme mostrado no exemplo de código a seguir e na captura de tela.
3. Especifique os parâmetros do corpo no formato JSON.

O exemplo de código a seguir mostra uma chamada do PowerShell para a API.

```powershell
$accesstoken = "Get token for your Client AAD App"
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Linux"
    "PortNo" = "22"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```
A captura de tela a seguir mostra um exemplo para chamar a API no PowerShell.

![Chamar a API com o PowerShell para o sistema operacional Linux](./media/stclient-call-api-ps-linuxvm.png)

Usando o exemplo anterior, você pode recuperar o JSON e analisá-lo para obter os seguintes detalhes:

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

A captura de tela a seguir, que mostra `$res.Content`, fornece os detalhes dos resultados do teste no formato JSON.

![Resultados JSON de chamada do PowerShell para Linux](./media/stclient-pslinux-rescontent-json.png)

A captura de tela a seguir mostra um exemplo de resultados de teste JSON exibidos em um visualizador JSON online (por exemplo, [Beautify de código](https://codebeautify.org/jsonviewer) ou [Visualizador JSON](https://jsonformatter.org/json-viewer)).

![Resultados JSON de chamada do PowerShell para VM do Linux](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>Usar o PowerShell para consumir a API no sistema operacional Windows

Para chamar a API no PowerShell, siga estas etapas:

1. Use o comando `Invoke-WebRequest` para chamar a API.
2. O método é post e o tipo de conteúdo é JSON, conforme mostrado no exemplo de código a seguir e na captura de tela.
3. Crie os parâmetros de corpo no formato JSON.

O exemplo de código a seguir mostra uma chamada do PowerShell para a API.

```powershell
$accesstoken = “Get token for your Client AAD App”
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Windows"
    "PortNo" = "5986"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

A captura de tela a seguir mostra um exemplo para chamar a API no PowerShell.

![Chamar a API com o PowerShell para VM do Windows](./media/stclient-call-api-ps-windowsvm.png)

Usando o exemplo anterior, você pode recuperar o JSON e analisá-lo para obter os seguintes detalhes:

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

A captura de tela a seguir, que mostra `$res.Content`, fornece os detalhes dos resultados do teste no formato JSON.

![Resultados JSON da chamada do PowerShell para o Windows](./media/stclient-pswindows-rescontent-json.png)

A captura de tela a seguir mostra os resultados de teste exibidos em um visualizador JSON online.
(por exemplo, [Beautify de código](https://codebeautify.org/jsonviewer), [Visualizador de JSON](https://jsonformatter.org/json-viewer))

![Resultados JSON de chamada do PowerShell para VM do Windows](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>Usar a rotação para consumir a API no sistema operacional Linux

Para chamar a API com a ondulação, siga estas etapas:

1. Use o comando de rotação para chamar a API.
2. O método é post e o tipo de conteúdo é JSON, conforme mostrado no trecho de código a seguir.

```
CURL POST -H "Content-Type:application/json"
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX"
https://isvapp.azurewebsites.net/selftest-vm
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'
```

A tela a seguir mostra um exemplo de uso de ondulação para chamar a API.

![Chamar a API usando o comando de ondulação](./media/stclient-consume-api-curl.png)

A captura de tela a seguir mostra os resultados JSON da chamada de ondulação.

![Resultados JSON de chamada de ondulação](./media/stclient-consume-api-curl-json.png)


## <a name="choose-the-azure-ad-tenant-for-the-app"></a>Escolha o locatário do Azure AD para o aplicativo

Use as etapas a seguir para escolher o locatário do Azure AD no qual você deseja criar seu aplicativo.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Na barra de menus superior, selecione sua conta e, na lista diretório, escolha o Active Directory locatário no qual você deseja registrar seu aplicativo. Ou então, selecione o ícone **diretório + assinatura** para ver o filtro de assinatura global. A captura de tela a seguir mostra um exemplo desse filtro.

   ![Selecionar o filtro de assinatura](./media/stclient-subscription-filter.png)

3. Na barra de navegação à esquerda, selecione **todos os serviços** e, em seguida, selecione **Azure Active Directory**.

   Nas etapas a seguir, talvez seja necessário o nome do locatário (ou nome do diretório) ou a ID do locatário (ou a ID do diretório).

   **Para obter informações de locatário:**

   Em **Azure Active Directory visão geral**, procure "Propriedades" e, em seguida, selecione **Propriedades**. Usando a captura de tela a seguir como exemplo:

   - **Nome** -o nome do locatário ou o nome do diretório
   - **ID do diretório** -a ID do locatário ou a ID do diretório ou use a barra de rolagem para localizar Propriedades.

   ![Página Propriedades de Azure Active Directory](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>Registrar o aplicativo cliente

Use as etapas a seguir para registrar o aplicativo cliente.

1. Na barra de navegação à esquerda, selecione **todos os serviços** e, em seguida, selecione **registros de aplicativo**.
2. Em **registros de aplicativo**, selecione **+ novo registro de aplicativo**.
3. Em **criar**, forneça as informações necessárias para os seguintes campos:

   - **Nome** – Insira um nome amigável para o aplicativo. Por exemplo, "SelfTestClient".
   - **Tipo de aplicativo** – selecione **aplicativo Web/API**
   - **URL de logon** – digite "https:\//isvapp.azurewebsites.net/selftest-VM"

4. Selecione **Criar**.
5. Em **registros de aplicativo** ou **aplicativo registrado**, copie a **ID do aplicativo**.

   ![Obter a ID do aplicativo](./media/stclient-app-id.png)

6. Na barra de ferramentas do aplicativo registrado, selecione **configurações**.
7. Selecione **as permissões necessárias** para configurar permissões para seu aplicativo.
8. Em **permissões necessárias**, selecione **+ Adicionar**.
9. Em **Adicionar acesso à API**, escolha **selecionar uma API**.
10. Em **selecionar uma API**, digite "modelo de implantação clássico do Windows Azure" para pesquisar a API.
11. Nos resultados da pesquisa, escolha **modelo de implantação clássico do Windows Azure** e clique em **selecionar**.

    ![Configurar multilocatário para o aplicativo](./media/stclient-select-api.png)

12. Em **Adicionar acesso à API**, escolha **selecionar permissões**.
13. Selecione **acesso "Windows Azure API de gerenciamento de serviços"** .

    ![Habilitar acesso à API para o aplicativo](./media/stclient-enable-api-access.png)

14. Clique em **Selecionar**.
15. Selecione **Done** (Concluído).
16. Em **Definições**, selecione **Propriedades**.
17. Em **Propriedades**, role para baixo até **multilocatário**. Selecione **Sim**.

    ![Configurar multilocatário para o aplicativo](./media/stclient-yes-multitenant.png)

18. Selecione **Guardar**.
19. Em **configurações**, selecione **chaves**.
20. Crie uma chave secreta selecionando a caixa de texto **Descrição** da chave. Configure os seguintes campos:

    - Digite um nome de chave. Por exemplo, "selftestclient"
    - Na lista suspensa **expirar** , selecione "em 1 ano".
    - Selecione **salvar** para gerar a chave.
    - Em **valor**, copie a chave.

      >[!Important]
      >Você não poderá ver o valor da chave depois de sair do formulário de **chaves** .

    ![Formulário de valor de chave](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>Criar o token para o aplicativo cliente

Você pode usar qualquer um dos seguintes programas para criar e obter um token usando a API REST do OAuth:

- Postman
- Ondulação no Linux
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>Para criar e obter um token usando o postmaster

 Para solicitar Auth0 tokens para qualquer um dos seus aplicativos autorizados, execute uma operação POST para o ponto de extremidade [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) com uma carga no seguinte formato:

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```

Passe os seguintes parâmetros no corpo da solicitação:

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

Passe os seguintes parâmetros no cabeçalho de solicitação:

```
Content-Type: application/x-www-form-urlencoded
```

A captura de tela a seguir mostra um exemplo de como usar o postmaster para obter um token.

![Obter token com o postmaster](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>Para criar e obter um token usando a ondulação no Linux

Para solicitar Auth0 tokens para qualquer um dos seus aplicativos autorizados, execute uma operação POST para o ponto de extremidade [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) com uma carga no seguinte formato:

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

A captura de tela a seguir mostra um exemplo de como usar o comando de ondulação para obter um token.

![Obter token com o comando de ondulação](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>Para criar e obter um token usando C&#35;

Para solicitar Auth0 tokens para qualquer um dos seus aplicativos autorizados, execute uma operação POST para o ponto de extremidade https:\//soamtenant.auth0.com/oauth/token com uma carga no seguinte formato:

```csharp
string clientId = "Your Application Id";
string clientSecret = "Your Application Secret";
string audience = "https://management.core.windows.net";
string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, "https://login.microsoftonline.com/common/oauth2/token");
string grantType = "client_credentials";
var client = new RestClient(authority);
var request = new RestRequest(Method.POST);
       request.AddHeader("content-type", "application/x-www-form- urlencoded");

string requestBody = "grant_type=" + grantType + "&" + "client_id=" +   clientId + "&" + "client_secret=" + clientSecret + "&" + "resource=" +  audience;

request.AddParameter("application/x-www-form-urlencoded", requestBody,  ParameterType.RequestBody);

IRestResponse response = client.Execute(request);
var content = response.Content;
var token = JObject.Parse(content)["access_token"];
```

### <a name="to-create-and-get-a-token-using-powershell"></a>Para criar e obter um token usando o PowerShell

Para solicitar Auth0 tokens para qualquer um dos seus aplicativos autorizados, execute uma operação POST para o ponto de extremidade https:\//soamtenant.auth0.com/oauth/token com uma carga no seguinte formato:

```powershell
$clientId = "Application Id of AD Client APP";
$clientSecret = "Secret Key of AD Client APP “
$audience = "https://management.core.windows.net";
$authority = "https://login.microsoftonline.com/common/oauth2/token"
$grantType = "client_credentials";

$requestBody = "grant_type=" + $grantType + "&" + "client_id=" +  $clientId   + "&" + "client_secret=" + $clientSecret + "&" + "resource=" + $audience;

$headers = New-Object  "System.Collections.Generic.Dictionary[[String],[String]]"
             $headers.Add("ContentType", "application/x-www-form-urlencoded")
resp = Invoke-WebRequest -Method Post -Uri $authority -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $requestBody

$token = $resp.Content | ConvertFrom-Json
$token.AccessToken
```

## <a name="pass-the-client-app-token-to-the-api"></a>Passar o token do aplicativo cliente para a API

Passe o token para a API de teste automático usando o seguinte código no cabeçalho de autorização:

```powershell
$redirectUri = ‘https://isvapp.azurewebsites.net/selftest-vm’
$accesstoken = ‘place your token here’

$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body =
@{
      'DNSName'="XXXX.cloudapp.azure.com";
      'User'="XXX";
      'Password'="XXXX@12345";
      'OS'="Linux";
      'PortNo'="22"
  } | ConvertTo-Json

$result=Invoke-WebRequest -Method Post -Uri $redirectUri -Headers $headers -ContentType 'application/json' -Body $Body
$result
Write-Output 'Test Results:'
$result.Content
```

## <a name="test-your-self-test-client"></a>Testar seu cliente de autoteste

Para testar o cliente, siga estas etapas:

1. Implante a VM que você deseja testar.
2. Chame a API de teste automático usando o token do aplicativo cliente para autorização.
3. Obtenha os resultados do teste no formato JSON.

### <a name="test-result-examples"></a>Exemplos de resultados de teste

Os trechos de código a seguir mostram os resultados de teste no formato JSON.

**Resultados de teste para uma VM do Windows:**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Microsoft Windows Server 2016 Datacenter",
  "OSVersion": "10.0.14393",
  "CreatedDate": "06/01/2018 07:48:04",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "Tes tID": "1.1.4",
      "TestCaseName": "OS Architecture",
      "Description": "Azure supports 64bit Operating System Only.",
      "Result": "Passed",
      "ActualValue": "64 Bit",
      "Re quiredValue": "OS Should be 64 bit"
    },
    {
      "TestID": "1.1.5",
      "TestCaseName": "User account dependency",
      "Description": "Application execution should not have de pendency on administrator account.",
      "Result": "Passed",
      "ActualValue": "isv",
      "RequiredValue": "Logged in user should not be an administrator"
    },
    {
      "TestID": " 1.1.6",
      "TestCaseName": "Failover Cluster",
      "Description": "Windows Server Failover Clustering feature is not yet supported, Application should not have dependency on this feature.",
      "Result": "Passed",
      "ActualValue": "Disabled",
      "RequiredValue": "Failover Clustering feature is disabled"
    },
```

**Resultados de teste para uma VM do Linux:**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Ubuntu  16.04",
  "OSVersion": "16.04",
  "CreatedDate": "06/01/2018 07:04:24",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "TestID": "48",
      "TestCaseName": "Bash Hi story",
      "Description": "Bash history files should be cleared before creating the VM image.",
      "Result": "Passed",
      "ActualValue": "No file Exist",
      "RequiredVal ue": "1024"
    },
    {
      "TestID": "39",
      "TestCaseName": "Linux Agent Version",
      "Description": "Azure Linux Agent 2.2.10 and above should be installed. ",
      "Result": "Pas sed",
      "ActualValue": "2.2.20",
      "RequiredValue": "2.2.10"
    },
    {
      "TestID": "40",
      "TestCaseName": "Required Kernel Parameters",
      "Description": "Verifies the following  kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300",
      "Result": "Passed",
      "ActualValue": "Matched Parameter: console=ttyS0,earlypri ntk=ttyS0,rootdelay=300",
      "RequiredValue": "console=ttyS0#earlyprintk=ttyS0#rootdelay=300"
    },
```

## <a name="next-steps"></a>Passos seguintes

Depois de testar com êxito sua máquina virtual do Azure, você pode [publicar a oferta](./cpp-publish-offer.md).
