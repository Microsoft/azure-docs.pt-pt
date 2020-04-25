---
title: Cliente auto-teste para pré-validar uma máquina virtual Mercado Azure
description: Como criar um cliente auto-teste para pré-validação de uma imagem de máquina virtual para o Mercado Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: dsindona
ms.openlocfilehash: 9f16d26fa95254282e453cd7bf35d85f8b81ed73
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143203"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-virtual-machine-image"></a>Criar um cliente auto-teste para pré-validar uma imagem de máquina virtual Azure

> [!IMPORTANT]
> A partir de 13 de abril de 2020, começaremos a mover a gestão das suas ofertas de Máquina Virtual Azure para partner Center. Após a migração, você vai criar e gerir suas ofertas no Partner Center. Siga as instruções na certificação de [imagem Azure VM](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-image-certification) para gerir as suas ofertas migratórias.

Utilize este artigo como um guia para a criação de um serviço ao cliente que consuma a API auto-teste. Pode utilizar a API auto-teste para pré-validar uma máquina virtual (VM) para garantir que satisfaz os mais recentes requisitos de publicação do Azure Marketplace. Este serviço de cliente permite-lhe testar um VM antes de submeter a sua oferta para certificação microsoft.

## <a name="development-and-testing-overview"></a>Desenvolvimento e visão geral dos testes

Como parte do processo de auto-teste, você vai criar um cliente local que se conecta ao Azure Marketplace para validar um VM em execução na sua Subscrição Azure. O VM pode estar a executar o sistema operativo Windows ou Linux.

O cliente local executa um script que autentica com a API auto-teste, envia informações de ligação e recebe resultados de testes.

Os passos de alto nível para a criação de um cliente auto-teste são:

1. Escolha o inquilino do Azure Ative Directory (AD) para a sua candidatura.
2. Registe a aplicação do cliente.
3. Crie um símbolo para a aplicação AD Do cliente Azure.
4. Passe o símbolo para a API auto-teste.

Depois de criar o cliente, pode testá-lo contra o seu VM.

### <a name="self-test-client-authorization"></a>Autorização de cliente auto-teste

O diagrama seguinte mostra como a autorização funciona para o serviço de chamadas de serviço usando credenciais de cliente (segredo partilhado ou certificado.)

![Processo de autorização do cliente](./media/stclient-dev-process.png)

## <a name="the-self-test-client-api"></a>O cliente auto-teste API

A API auto-teste contém um único ponto final que suporta apenas o método POST.  Tem a seguinte estrutura.

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

A tabela seguinte descreve os campos DaPI.


|      Campo         |    Descrição    |
|  ---------------   |  ---------------  |
|  Autorização     |  A cadeia "Bearer xxxx-xxxx-xxxx-xxxxx" contém o token do cliente Azure Ative Directory (AD), que pode ser criado através da utilização da PowerShell.          |
|  Nome DNS           |  Nome DNS do VM para testar    |
|  Utilizador              |  Nome do utilizador para iniciar sessão no VM         |
|  Palavra-passe          |  Senha para iniciar sessão no VM          |
|  SO                |  Sistema operativo do VM: ou `Linux``Windows`          |
|  Portno            |  Número de porta aberta para ligação ao VM. O número da `22` porta é `5986` tipicamente para Linux e para Windows.          |
|  |  |

## <a name="consuming-the-api"></a>Consumir a API

Pode consumir a API auto-teste utilizando powerShell ou cURL.

### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>Use powerShell para consumir a API no Sistema Linux

Para ligar para a API no PowerShell, siga estes passos:

1. Use `Invoke-WebRequest` o comando para chamar a API.
2. O método é Post e o tipo de conteúdo é JSON, como mostra o seguinte exemplo de código e captura de ecrã.
3. Especifique os parâmetros corporais no formato JSON.

O exemplo de código que se segue mostra uma chamada powerShell para a API.

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
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" -Headers $headers;
$Content = $res | ConvertFrom-Json
```
A captura do ecrã seguinte mostra um exemplo para chamar a API no PowerShell.

![Ligue para a API com PowerShell para Linux OS](./media/stclient-call-api-ps-linuxvm.png)

Utilizando o exemplo anterior, pode recuperar o JSON e analisá-lo para obter os seguintes detalhes:

```powershell
$testresult = ConvertFrom-Json -InputObject (ConvertFrom-Json -InputObject $res)

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

A seguinte captura de `$res.Content`ecrã, que mostra, dá-lhe os detalhes dos resultados dos seus testes em formato JSON.

![JSON resulta da chamada da PowerShell para linux](./media/stclient-pslinux-rescontent-json.png)

A captura do ecrã seguinte mostra um exemplo dos resultados dos testes da JSON visualizados num espectador jSON online (por exemplo, [Code Beautify](https://codebeautify.org/jsonviewer) ou [JSON Viewer](https://jsonformatter.org/json-viewer)).

![JSON resulta da chamada da PowerShell para linux VM](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>Use o PowerShell para consumir a API no Sistema Operativo Windows

Para ligar para a API no PowerShell, siga estes passos:

1. Use `Invoke-WebRequest` o comando para chamar a API.
2. O método é Post e o tipo de conteúdo é JSON, como mostra o seguinte exemplo de código e captura de ecrã.
3. Crie os parâmetros body em formato JSON.

O exemplo de código que se segue mostra uma chamada powerShell para a API.

```powershell
$accesstoken = "Get token for your Client AAD App"
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
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" -Headers $headers;
$Content = $res | ConvertFrom-Json
```

A captura do ecrã seguinte mostra um exemplo para chamar a API no PowerShell.

![Ligue para API com PowerShell para Windows VM](./media/stclient-call-api-ps-windowsvm.png)

Utilizando o exemplo anterior, pode recuperar o JSON e analisá-lo para obter os seguintes detalhes:

```powershell
$testresult = ConvertFrom-Json -InputObject (ConvertFrom-Json -InputObject $res)

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

A seguinte captura de `$res.Content`ecrã, que mostra, dá-lhe os detalhes dos resultados dos seus testes em formato JSON.

![JSON resulta da chamada powerShell para windows](./media/stclient-pswindows-rescontent-json.png)

A captura de ecrã seguinte mostra os resultados dos testes visualizados num espectador JSON online.
(por exemplo, [Código Embelezamento,](https://codebeautify.org/jsonviewer) [Espectador JSON)](https://jsonformatter.org/json-viewer)

![JSON resulta da chamada powerShell para Windows VM](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>Use cURL para consumir a API no Sistema Linux

Para ligar para a API com cURL, siga estes passos:

1. Utilize o comando de caracóis para chamar a API.
2. O método é Post e o tipo de conteúdo é JSON, como mostra o seguinte código snippet.

```
CURL POST -H "Content-Type:application/json"
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX"
https://isvapp.azurewebsites.net/selftest-vm
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'
```

O ecrã seguinte mostra um exemplo de utilização de caracóis para chamar a API.

![Ligue para a API usando o comando do caracol](./media/stclient-consume-api-curl.png)

A captura do ecrã seguinte mostra que o JSON resulta da chamada de caracóis.

![JSON resulta de chamada de caracóis](./media/stclient-consume-api-curl-json.png)


## <a name="choose-the-azure-ad-tenant-for-the-app"></a>Escolha o inquilino Azure AD para a app

Utilize os seguintes passos para escolher o inquilino Azure AD onde pretende criar a sua aplicação.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Na barra de menu superior, selecione a sua conta e, na lista de Diretório, escolha o inquilino do Diretório Ativo onde pretende registar a sua candidatura. Ou, selecione o ícone **de Subscrição Do Diretório +** para ver o filtro de subscrição Global. A seguinte captura do ecrã mostra um exemplo deste filtro.

   ![Selecione o filtro de subscrição](./media/stclient-subscription-filter.png)

3. Na barra de navegação à esquerda, selecione **Todos os serviços** e, em seguida, selecione **Azure Ative Directory**.

   Nos seguintes passos, pode necessitar do nome do inquilino (ou nome do diretório) ou do ID do inquilino (ou ID do diretório).

   **Para obter informações sobre o inquilino:**

   Na **visão geral do Diretório Ativo Azure,** procure "Propriedades" e, em seguida, selecione **Propriedades**. Utilizando a seguinte captura de ecrã como exemplo:

   - **Nome** - O nome do inquilino ou nome do diretório
   - **ID do diretório** - O ID do inquilino ou id do diretório ou usar a barra de pergaminho para encontrar Propriedades.

   ![Página de propriedades de Diretório Ativo Azure](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>Registe a aplicação do cliente

Utilize os seguintes passos para registar a aplicação do cliente.

1. Na barra de navegação à esquerda, selecione **Todos os serviços** e, em seguida, selecione **os registos da App**.
2. Nos **registos da App,** selecione **+ Novo registo de candidaturas.**
3. Ao abrigo da **Create,** forneça as informações necessárias para os seguintes campos:

   - **Nome** - Introduza um nome amigável para a aplicação. Por exemplo, "SelfTestClient".
   - Tipo de **aplicação** - Selecione **Web App/API**
   - **URL de inscrição** -\/Tipo "https: /isvapp.azurewebsites.net/selftest-vm"

4. Selecione **Criar**.
5. Nos **registos da App** ou na **aplicação Registada,** copie o ID da **aplicação.**

   ![Obtenha o ID da aplicação](./media/stclient-app-id.png)

6. Na barra de ferramentas de aplicação registada, selecione **Definições**.
7. Selecione **permissões necessárias** para configurar permissões para a sua aplicação.
8. Sob **as permissões necessárias,** selecione **+ Adicione**.
9. Em **acesso API adicionar,** escolha **Selecione um API**.
10. Em **Select um API**, escreva "modelo de implementação clássico do Windows Azure" para procurar a API.
11. Nos resultados da pesquisa, escolha o **modelo de implementação clássico do Windows Azure** e, em seguida, clique em **Select**.

    ![Configure multi-inquilinopara app](./media/stclient-select-api.png)

12. No **acesso a API adicionar,** escolha **Selecionar permissões**.
13. Selecione **Access "Windows Azure Service Management API".**

    ![Ativar o acesso da API para aplicação](./media/stclient-enable-api-access.png)

14. Clique em **Selecionar**.
15. Selecione **Done** (Concluído).
16. Em **Definições**, selecione **Propriedades**.
17. Em **Propriedades,** desça até **multi-inquilinos.** Selecione **Sim**.

    ![Configure multi-inquilinopara app](./media/stclient-yes-multitenant.png)

18. Selecione **Guardar**.
19. Em **Definições,** selecione **Keys**.
20. Crie uma chave secreta selecionando a caixa de texto **'DESCRIÇÃO** Chave'. Configure os seguintes campos:

    - Digite um nome chave. Por exemplo, "cliente auto-teste"
    - Na lista de desistências do **EXPIRES,** selecione "Em 1 ano".
    - Selecione **Guardar** para gerar a chave.
    - Em **VALOR,** copie a chave.

      >[!Important]
      >Não poderá ver o valor-chave depois de sair do formulário **Keys.**

    ![Formulário de valor-chave](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>Crie o símbolo para a aplicação do cliente

Você pode usar qualquer um dos seguintes programas para criar e obter um símbolo usando a API Rest OAuth:

- Postman
- cURL em Linux
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>Para criar e obter um símbolo usando o Carteiro

 Para solicitar fichas à Auth0 para qualquer uma das suas aplicações autorizadas, execute uma operação POST até ao [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) ponto final com uma carga útil no seguinte formato:

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```

Passe os seguintes parâmetros no órgão de pedido:

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

Passe os seguintes parâmetros no cabeçalho do Pedido:

```
Content-Type: application/x-www-form-urlencoded
```

A captura do ecrã seguinte mostra um exemplo de utilização do Carteiro para obter um símbolo.

![Token com carteiro](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>Para criar e obter um símbolo usando cURL em Linux

Para solicitar fichas à Auth0 para qualquer uma das suas aplicações autorizadas, execute uma operação POST até ao [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) ponto final com uma carga útil no seguinte formato:

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

A captura do ecrã seguinte mostra um exemplo de utilização do comando de caracóis para obter um símbolo.

![Obter ficha com comando de caracóis](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>Para criar e obter um símbolo usando C&#35;

Para solicitar fichas à Auth0 para qualquer uma das suas candidaturas\/autorizadas, execute uma operação POST para o https: /soamtenant.auth0.com/oauth/token ponto final com uma carga útil no seguinte formato:

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

### <a name="to-create-and-get-a-token-using-powershell"></a>Para criar e obter um símbolo usando powerShell

Para solicitar fichas à Auth0 para qualquer uma das suas candidaturas\/autorizadas, execute uma operação POST para o https: /soamtenant.auth0.com/oauth/token ponto final com uma carga útil no seguinte formato:

```powershell
$clientId = "Application Id of AD Client APP";
$clientSecret = "Secret Key of AD Client APP "
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

## <a name="pass-the-client-app-token-to-the-api"></a>Passe a app do cliente para a API

Passe o símbolo para a API auto-teste utilizando o seguinte código no cabeçalho de autorização:

```powershell
$redirectUri = 'https://isvapp.azurewebsites.net/selftest-vm'
$accesstoken = 'place your token here'

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

## <a name="test-your-self-test-client"></a>Teste o seu cliente auto-teste

Para testar o cliente, siga estes passos:

1. Implante o VM que quer testar.
2. Ligue para a API auto-teste usando o token da sua aplicação cliente para autorização.
3. Obtenha os resultados dos testes em formato JSON.

### <a name="test-result-examples"></a>Exemplos de resultados de teste

Os seguintes cortes mostram os resultados dos testes no formato JSON.

**Resultados dos testes para um VM do Windows:**

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

**Resultados dos testes para um VM Linux:**

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

Depois de ter testado com sucesso a sua máquina virtual Azure, pode [publicar a oferta.](./cpp-publish-offer.md)
