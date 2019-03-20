---
title: Cliente de autoteste previamente validar uma máquina virtual - Azure Marketplace | Documentos da Microsoft
description: Como criar um cliente autoteste para pré-validar uma imagem de máquina virtual para o Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, Virtual Machine
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: pbutlerm
ms.openlocfilehash: aa46fbce9d11a8fd380a5ee1990c0e7924079a9b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58078691"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-virtual-machine-image"></a>Criar um cliente de autoteste de mensagens em fila para pré-validar uma imagem de máquina virtual do Azure

Utilize este artigo como um guia para a criação de um serviço de cliente que consome a API de autoteste. Pode utilizar a API de autoteste previamente validar uma máquina virtual (VM) para garantir que cumpre os requisitos de publicação mais recentes do Azure Marketplace. Este serviço de cliente permite-lhe testar uma VM antes de submeter a sua oferta para a certificação Microsoft.


## <a name="development-and-testing-overview"></a>Desenvolvimento e a descrição geral do teste

Como parte do processo de teste personalizada, criará um cliente local que se liga ao Azure Marketplace para validar uma VM em execução na sua subscrição do Azure. A VM pode ser executado o sistema operativo Windows ou Linux.

O cliente local executa um script que efetua a autenticação com a API de autoteste, envia informações de ligação e recebe resultados do teste.

Os passos de alto nível para a criação de um cliente autoteste são:

1. Escolha o inquilino do Azure Active Directory (AD) para a sua aplicação.
2. Registe a aplicação de cliente.
3. Crie um token para a aplicação cliente do Azure AD.
4. Transmita o token para a API de autoteste.

Depois de criar o cliente, pode testá-lo em sua VM.


### <a name="self-test-client-authorization"></a>Autoteste autorização de cliente

O diagrama seguinte mostra como funciona a autorização para chamadas de serviço para serviço com as credenciais de cliente (segredo partilhado ou certificado).

![Processo de autorização de cliente](./media/stclient-dev-process.png)


## <a name="the-self-test-client-api"></a>A API do cliente autoteste

A API de autoteste contém um único ponto final que suporta apenas o método POST.  Ele tem a seguinte estrutura.

```
Uri:             https://isvapp.azurewebsites.net/selftest-vm
Method:          Post
Request Header:  Content-Type: “application/json”
Authorization:   “Bearer xxxx-xxxx-xxxx-xxxxx”
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

A tabela seguinte descreve os campos de API.


|      Campo         |    Descrição    |
|  ---------------   |  ---------------  |
|  Autorização     |  A cadeia de caracteres "Portador xxxx-xxxx-xxxx-xxxxx" contém o token de cliente do Azure Active Directory (AD), que pode ser criado com o PowerShell.          |
|  Nome de DNS           |  Nome de DNS da VM para testar    |
|  Utilizador              |  Nome de utilizador para iniciar sessão na VM         |
|  Palavra-passe          |  Palavra-passe para iniciar sessão na VM          |
|  SO                |  Sistema operativo da VM: o `Linux` ou `Windows`          |
|  PortNo            |  Abra o número de porta para ligar à VM. O número de porta é normalmente `22` para Linux e `5986` para Windows.          |
|  |  |


## <a name="consuming-the-api"></a>A API de consumo

Pode consumir a API de autoteste de mensagens em fila com o PowerShell ou o cURL.


### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>Utilize o PowerShell para consumir a API no SO Linux

Para chamar a API no PowerShell, siga estes passos:

1. Utilize o `Invoke-WebRequest` comando para chamar a API.
2. O método é Post e o tipo de conteúdo é um JSON, como mostra a captura de tela e exemplo de código seguinte.
3. Especifique os parâmetros de corpo no formato JSON.

O exemplo de código seguinte mostra uma chamada de PowerShell para a API.

```powershell
$accesstoken = “Get token for your Client AAD App”
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
Captura de ecrã seguinte mostra um exemplo para chamar a API no PowerShell.

![Chamar a API com o PowerShell para o SO Linux](./media/stclient-call-api-ps-linuxvm.png)

Usando o exemplo anterior, pode obter o JSON e analisá-lo para obter os detalhes seguintes:

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

O seguinte ecrã de captura, que mostra `$res.Content`, dá-lhe os detalhes dos resultados do teste no formato JSON.

![JSON resultante da chamada de PowerShell para Linux](./media/stclient-pslinux-rescontent-json.png)

Captura de ecrã seguinte mostra um exemplo dos resultados de teste JSON visualizados num Visualizador JSON online (por exemplo, [código Beautify](https://codebeautify.org/jsonviewer) ou [Visualizador JSON](https://jsonformatter.org/json-viewer)).

![Resultados JSON de chamada de PowerShell para VM do Linux](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>Utilize o PowerShell para consumir a API no SO Windows

Para chamar a API no PowerShell, siga estes passos:

1. Utilize o `Invoke-WebRequest` comando para chamar a API.
2. O método é Post e o tipo de conteúdo é um JSON, como mostra a captura de tela e exemplo de código seguinte.
3. Crie os parâmetros de corpo no formato JSON.

O exemplo de código seguinte mostra uma chamada de PowerShell para a API.

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

Captura de ecrã seguinte mostra um exemplo para chamar a API no PowerShell.

![Chamar a API com o PowerShell para a Windows VM](./media/stclient-call-api-ps-windowsvm.png)

Usando o exemplo anterior, pode obter o JSON e analisá-lo para obter os detalhes seguintes:

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

O seguinte ecrã de captura, que mostra `$res.Content`, dá-lhe os detalhes dos resultados do teste no formato JSON.

![Chamam de resultados JSON do PowerShell para Windows](./media/stclient-pswindows-rescontent-json.png)

Captura de ecrã seguinte mostra os resultados do teste visualizados num Visualizador JSON online.
(por exemplo, [código Beautify](https://codebeautify.org/jsonviewer), [Visualizador JSON](https://jsonformatter.org/json-viewer))

![Resultados JSON de chamada de PowerShell para VM do Windows](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>Utilize o cURL para consumir a API no SO Linux

Para chamar a API com o cURL, siga estes passos:

1. Utilize o comando curl para chamar a API.
2. O método é Post e o tipo de conteúdo é um JSON, conforme mostrado no seguinte fragmento de código.

```
CURL POST -H "Content-Type:application/json" 
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX”
https://isvapp.azurewebsites.net/selftest-vm 
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'

```
O ecrã seguinte mostra um exemplo de como utilizar curl para chamar a API.

![Chame a API com o comando curl](./media/stclient-consume-api-curl.png)

Captura de ecrã seguinte mostra os resultados JSON da chamada curl.

![Resultados JSON da chamada de curl](./media/stclient-consume-api-curl-json.png)


## <a name="choose-the-azure-ad-tenant-for-the-app"></a>Escolha o inquilino do Azure AD para a aplicação

Utilize os seguintes passos para escolher o inquilino do Azure AD onde pretende criar a sua aplicação.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Na barra de menus superior, selecione a sua conta e abaixo da lista de diretório, escolha o inquilino do Active Directory onde pretende registar a sua aplicação. Em alternativa, selecione o **diretório + subscrição** ícone para ver o filtro de subscrição Global. Captura de ecrã seguinte mostra um exemplo deste filtro.

   ![Selecione o filtro de subscrição](./media/stclient-subscription-filter.png)

3. Na barra de navegação do lado esquerdo, selecione **todos os serviços** e, em seguida, selecione **Azure Active Directory**.

   Nos passos seguintes, poderá ter o nome do inquilino (ou o nome de diretório) ou o ID de inquilino (ou ID de diretório).

   **Para obter informações do inquilino:**
  
   Na **descrição geral de diretório Active Directory do Azure**, procure "Propriedades" e, em seguida, selecione **propriedades**. Utilizando a seguinte captura de tela como exemplo:

   - **Nome** -o nome do inquilino ou nome de diretório
   - **ID do diretório** -o ID do inquilino ou ID do diretório ou utilize a barra de deslocamento para localizar as propriedades.

   ![Página de propriedades do Azure Active Directory](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>Registar a aplicação de cliente

Utilize os seguintes passos para registar a aplicação de cliente.

1. Na barra de navegação do lado esquerdo, selecione **todos os serviços** e, em seguida, selecione **registos das aplicações**.
2. Sob **registos de aplicações**, selecione **+ novo registo de aplicação**.
3. Sob **criar**, forneça as informações necessárias para os seguintes campos:

   - **Nome** – introduza um nome amigável para a aplicação. Por exemplo, "SelfTestClient".
   - **Tipo de aplicação** – selecione **aplicação/API Web**
   - **Início de sessão em URL** – tipo de "https://isvapp.azurewebsites.net/selftest-vm"

4. Selecione **Criar**.
5. Sob **registos de aplicações** ou **aplicação registada**, copiar o **ID da aplicação**.

   ![Obter o ID da aplicação](./media/stclient-app-id.png)

6. Na barra de ferramentas do aplicativo registrado, selecione **definições**.
7. Selecione **permissões obrigatórias** para configurar permissões para a sua aplicação.
8. Sob **permissões obrigatórias**, selecione **+ adicionar**.
9. Sob **adicionar acesso à API**, escolha **selecionar uma API**.
10. Sob **selecionar uma API**, tipo "Modelo de implementação clássica do Azure do Windows" para procurar a API.
11. Nos resultados da pesquisa, escolha **modelo de implementação clássica do Windows Azure** e, em seguida, clique em **selecione**.

    ![Configurar o multi-inquilino para a aplicação](./media/stclient-select-api.png)

12. Sob **adicionar acesso à API**, escolha **selecionar permissões**.
13. Selecione **aceder a "Windows Azure Service Management API"**.

    ![Ativar o acesso de API para a aplicação](./media/stclient-enable-api-access.png)

14. Clique em **Selecionar**.
15. Selecione **Done** (Concluído).
16. Sob **configurações**, selecione **propriedades**.
17. Sob **propriedades**, desloque para baixo até **vários inquilinos**. Selecione **Sim**.  

    ![Configurar o multi-inquilino para a aplicação](./media/stclient-yes-multitenant.png)

18. Selecione **Guardar**.
19. Sob **configurações**, selecione **chaves**.
20. Criar uma chave secreta ao selecionar a chave **Descrição** caixa de texto. Configure os seguintes campos:

    - Escreva um nome de chave. Por exemplo, "selftestclient"
    - Sobre o **EXPIRES** lista pendente, selecione "dentro de 1 ano".
    - Selecione **guardar** para gerar a chave.
    - Sob **valor**, copie a chave.

      >[!Important]
      >Não será capaz de ver o valor da chave depois de sair do **chaves** formulário.

    ![Formulário de valor da chave](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>Criar o token para a aplicação de cliente

Pode usar qualquer uma das seguintes programas para criar e obter um token com a API de REST de OAuth:

- Postman
- cURL no Linux
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>Para criar e obter um token com o Postman

 Para solicitar Auth0 tokens para as suas aplicações autorizadas, execute uma operação POST para o [ https://login.microsoftonline.com/common/oauth2/token ](https://login.microsoftonline.com/common/oauth2/token) ponto final com um payload no seguinte formato:

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```
Passe os seguintes parâmetros no corpo do pedido:

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

Transmita os parâmetros seguintes no cabeçalho do pedido:

```
Content-Type: application/x-www-form-urlencoded
```

Captura de ecrã seguinte mostra um exemplo de como utilizar o Postman para obter um token.

![Obter o token com o Postman](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>Para criar e obter um token com o cURL no Linux

Para solicitar Auth0 tokens para as suas aplicações autorizadas, execute uma operação POST para o [ https://login.microsoftonline.com/common/oauth2/token ](https://login.microsoftonline.com/common/oauth2/token) ponto final com um payload no seguinte formato:

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

Captura de ecrã seguinte mostra um exemplo de como utilizar o comando curl para obter um token.

![Obter o token com o comando curl](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>Para criar e obter um token com C&#35;

Para solicitar Auth0 tokens para as suas aplicações autorizadas, execute uma operação POST para o [ https://soamtenant.auth0.com/oauth/token ](https://soamtenant.auth0.com/oauth/token) ponto final com um payload no seguinte formato:

```
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

### <a name="to-create-and-get-a-token-using-powershell"></a>Para criar e obter um token com o PowerShell

Para solicitar Auth0 tokens para as suas aplicações autorizadas, execute uma operação POST para o [ https://soamtenant.auth0.com/oauth/token ](https://soamtenant.auth0.com/oauth/token) ponto final com um payload no seguinte formato:

```
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

## <a name="pass-the-client-app-token-to-the-api"></a>Passar o cliente do token da aplicação para a API

Transmita o token para a API de autoteste de mensagens em fila usando o seguinte código no cabeçalho de autorização:

```
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
echo 'Test Results:'
$result.Content

```

## <a name="test-your-self-test-client"></a>Testar o seu cliente autoteste

Para testar o cliente, siga estes passos:

1. Implemente a VM que pretende testar.
2. Chame a API de autoteste de mensagens em fila com o seu token de aplicação de cliente para autorização.
3. Obtenha os resultados de teste no formato JSON.

### <a name="test-result-examples"></a>Exemplos de resultado de teste

Os fragmentos seguintes mostram os resultados do teste no formato JSON.

**Resultados do teste para uma VM do Windows:**

```
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

**Resultados do teste para uma VM do Linux:**

```
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

## <a name="next-steps"></a>Passos Seguintes

Depois de o testar sua máquina virtual do Azure com êxito, pode [publicar a oferta](./cpp-publish-offer.md).
