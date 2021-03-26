---
title: Teste uma imagem de máquina virtual Azure para O Mercado Azure
description: Aprenda a testar e submeta uma oferta de máquina virtual Azure no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: kriti-ms
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 467b7d605b57c479d84fc995b4e0dc53b3ac5275
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558300"
---
# <a name="test-a-virtual-machine-image"></a>Teste uma imagem de máquina virtual

Este tópico cobrirá os passos para testar uma imagem de máquina virtual (VM) para implantação no Azure Marketplace.

## <a name="deploy-an-azure-vm"></a>Implementar um VM Azure

Para implementar um VM a partir da imagem da Galeria de Imagens Partilhada:

1. Navegue para a versão de imagem da Galeria de Imagens Partilhadas
1. Clique em Criar VM
1. Forneça um nome de máquina virtual e selecione um tamanho VM
1. Clique em 'Rever +' criar. Uma vez passada a validação, clique em Criar

> [!NOTE]
> Se precisar de criar um VM a partir de um ficheiro VHD, siga as instruções nos seguintes artigos, [Prepare um modelo de Gestor de Recursos Azure](#connect-the-certification-tool-to-a-vm-image) ou [implemente um VM Azure utilizando o PowerShell](#how-to-use-powershell-to-consume-the-self-test-api).

Este artigo descreve como testar e enviar uma imagem de máquina virtual (VM) no mercado comercial para garantir que satisfaz os mais recentes requisitos de publicação do Azure Marketplace.

Complete estes passos antes de submeter a sua oferta de VM:

- Implementar um Azure VM utilizando a sua imagem generalizada; ver [Criar uma máquina virtual utilizando uma base aprovada](azure-vm-create-using-approved-base.md) ou criar uma máquina virtual utilizando a sua própria [imagem](azure-vm-create-using-own-image.md).
- Executar validações.

## <a name="run-validations"></a>Executar validações

Há duas formas de executar validações na imagem implantada.

### <a name="use-certification-test-tool-for-azure-certified"></a>Utilize a ferramenta de teste de certificação para a Azure Certified

#### <a name="download-and-run-the-certification-test-tool"></a>Descarregue e execute a ferramenta de teste de certificação

A Ferramenta de Teste de Certificação para Azure Certified funciona numa máquina local do Windows, mas testa um Windows baseado em Azure ou Um VM Linux baseado em Azure. Certifica que a imagem VM do seu utilizador pode ser usada com o Microsoft Azure e que as orientações e requisitos em torno da preparação do seu VHD foram cumpridos.

1. Descarregue e instale a mais recente [Ferramenta de Teste de Certificação para Azure Certified.](https://www.microsoft.com/download/details.aspx?id=44299)
2. Abra a ferramenta de certificação e, em seguida, **selecione Iniciar Novo Teste**.
3. A partir do ecrã de Informação de Teste, introduza um **nome de teste** para a execução do teste.
4. Selecione a Plataforma para o seu VM, **servidor do Windows** ou **Linux**. A escolha da sua plataforma afeta as restantes opções.
5. Se o seu VM estiver a utilizar este serviço de base de dados, selecione a caixa **de verificação test for Azure SQL Database.**

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>Ligue a ferramenta de certificação a uma imagem VM

1. Selecione o modo de autenticação SSH: Autenticação por palavra-passe ou autenticação de ficheiros de chave.
2. Se utilizar a autenticação baseada em palavra-passe, introduza valores para o **Nome VM DNS,** **nome de utilizador** e **palavra-passe**. Também pode alterar o número padrão da Porta SSH.

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="Mostra a seleção de informações de teste em VM.":::

3. Se utilizar a autenticação baseada em ficheiros chave, introduza valores para o Nome de DNS VM, nome de utilizador e localização da chave Privada. Também pode incluir uma palavra-passe ou alterar o número padrão da Porta SSH.
4. Introduza o nome de DNS VM totalmente qualificado (por exemplo, MyVMName.Cloudapp.net).
5. Insira **o nome de utilizador** e a **palavra-passe**.

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="Mostra a seleção do nome de utilizador vM e palavra-passe.":::

6. Selecione **Seguinte**.

#### <a name="run-a-certification-test"></a>Realizar um teste de certificação

Depois de ter dado os valores dos parâmetros para a sua imagem VM na ferramenta de certificação, selecione a Ligação de Teste para criar uma ligação válida ao seu VM. Depois de verificada uma ligação, selecione **Seguinte** para iniciar o teste. Quando o teste estiver concluído, os resultados são apresentados numa tabela. A coluna status mostra (Passe/Falha/Aviso) para cada teste. Se algum dos testes falhar, a sua imagem não está certificada. Neste caso, reveja os requisitos e as mensagens de falha, faça as alterações sugeridas e volte a executar o teste.

Após o teste automatizado concluído, forneça informações adicionais sobre a sua imagem em VM nos dois separadores do ecrã do Questionário, Avaliação Geral e Personalização kernel e, em seguida, selecione Seguinte.

O último ecrã permite-lhe fornecer mais informações, como informações de acesso SSH para uma imagem Linux VM, e uma explicação para eventuais avaliações falhadas se estiver à procura de exceções.

Por fim, selecione 'Relatório de Geração' para descarregar os resultados dos testes e registar ficheiros para os casos de teste executados, juntamente com as suas respostas ao questionário.
> [!Note]
> Poucas editoras têm cenários em que os VM precisam de ser bloqueados, uma vez que têm software como firewalls instalados no VM. Neste caso, faça o download da [Ferramenta de Teste Certificada](https://aka.ms/AzureCertificationTestTool) aqui e submeta o relatório no [suporte](https://aka.ms/marketplacepublishersupport)do Partner Center .

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>Como usar o PowerShell para consumir a API Self-Test

### <a name="on-linux-os"></a>No Linux OS

Ligue para a API em PowerShell:

1. Use o comando Invoke-WebRequest para ligar para a API.
2. O método é Post e o tipo de conteúdo é JSON, como mostrado no exemplo de código e captura de ecrã.
3. Especifique os parâmetros do corpo no formato JSON.

Este exemplo mostra uma chamada PowerShell para a API:

```POWERSHELL
$accesstoken = "token"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$DNSName = "<Machine DNS Name>"
$UserName = "<User ID>"
$Password = "<Password>"
$OS = "Linux"
$PortNo = "22"
$CompanyName = "ABCD"
$AppID = "<Application ID>"
$TenantId = "<Tenant ID>"

$body = @{
   "DNSName" = $DNSName
   "UserName" = $UserName
   "Password" = $Password
   "OS" = $OS
   "PortNo" = $PortNo
   "CompanyName" = $CompanyName
   "AppID" = $AppID
   "TenantId" = $TenantId
} | ConvertTo-Json

$body

$uri = "URL"

$res = (Invoke-WebRequest -Method "Post" -Uri $uri -Body $body -ContentType "application/json" -Headers $headers).Content
```

<br>Aqui está um exemplo de chamar a API em PowerShell:

[![Exemplo de ecrã para chamar a API em PowerShell.](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>Utilizando o exemplo anterior, pode recuperar o JSON e analisá-lo para obter os seguintes detalhes:

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>Este ecrã de amostra, que `$res.Content` mostra, mostra detalhes dos resultados dos seus testes no formato JSON:

[![Exemplo do ecrã para ligar para a API no PowerShell com detalhes dos resultados dos testes.](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>Aqui está um exemplo dos resultados dos testes de JSON vistos num espectador JSON online (como [Code Beautify](https://codebeautify.org/jsonviewer) ou [JSON Viewer).](https://jsonformatter.org/json-viewer)

![Mais resultados de testes num visualizador JSON online.](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>No Windows OS

Ligue para a API em PowerShell:

1. Use o comando Invoke-WebRequest para ligar para a API.
2. O método é Post e o tipo de conteúdo é JSON, como mostrado no seguinte exemplo de código e ecrã de amostra.
3. Crie os parâmetros do corpo no formato JSON.

Esta amostra de código mostra uma chamada PowerShell para a API:

```PowerShell
$accesstoken = "Get token for your Client AAD App"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$Body = @{ 
   "DNSName" = "XXXX.westus.cloudapp.azure.com"
   "UserName" = "XXX"
   "Password" = "XXX@123456"
   "OS" = "Windows"
   "PortNo" = "5986"
   "CompanyName" = "ABCD"
   "AppID" = "XXXX-XXXX-XXXX"
   "TenantId" = "XXXX-XXXX-XXXX"
} | ConvertTo-Json

$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

Estes ecrãs de amostra mostram o exemplo para chamar a API em PowerShell:

**Com chave SSH:**

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="Chamando a API em PowerShell com uma chave SSH.":::

**Com senha:**

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="Ligar para a API no PowerShell com uma senha.":::

<br>Utilizando o exemplo anterior, pode recuperar o JSON e analisá-lo para obter os seguintes detalhes:

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>Este ecrã, que `$res.Content` mostra, mostra os detalhes dos resultados dos seus testes no formato JSON:

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="Detalhes dos resultados dos testes no formato JSON.":::

<br>Aqui está um exemplo dos resultados dos testes vistos num visualizador JSON online (como [Code Beautify](https://codebeautify.org/jsonviewer) ou [JSON Viewer).](https://jsonformatter.org/json-viewer)

![Os resultados dos testes num visualizador JSON online.](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>Como usar o CURL para consumir a API Self-Test no Linux OS

Neste exemplo, o curl será usado para fazer uma chamada POST API para O Diretório Ativo Azure e o Self-Host VM.

1. Solicite um token AD AD Azure para autenticar para auto-anfitrião VM

   Certifique-se de que os valores corretos são substituídos no pedido do caracol.

   ```JSON
   curl --location --request POST 'https://login.microsoftonline.com/{TENANT_ID}/oauth2/token' \
   --header 'Content-Type: application/x-www-form-urlencoded' \
   --data-urlencode 'grant_type=client_credentials' \
   --data-urlencode 'client_id={CLIENT_ID} ' \
   --data-urlencode 'client_secret={CLIENT_SECRET}' \
   --data-urlencode 'resource=https://management.core.windows.net'
   ```

   Aqui está um exemplo da resposta do pedido:

   ```JSON
   {
       "token_type": "Bearer",
       "expires_in": "86399",
       "ext_expires_in": "86399",
       "expires_on": "1599663998",
       "not_before": "1599577298",
       "resource": "https://management.core.windows.net",
       "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJS…"
   }
   ```

2. Apresentar um pedido para o VM auto-teste

   Certifique-se de que o token do portador e os parâmetros são substituídos pelos valores corretos.

   ```JSON
   curl --location --request POST 'https://isvapp.azurewebsites.net/selftest-vm' \
   --header 'Content-Type: application/json' \
   --header 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJS…' \
   --data-raw '{
       "DNSName": "avvm1.eastus.cloudapp.azure.com",
       "UserName": "azureuser",
       "Password": "SECURE_PASSWORD_FOR_THE_SSH_INTO_VM",
       "OS": "Linux",
       "PortNo": "22",
       "CompanyName": "COMPANY_NAME",
       "AppId": "CLIENT_ID_SAME_AS_USED_FOR_AAD_TOKEN ",
       "TenantId": "TENANT_ID_SAME_AS_USED_FOR_AAD_TOKEN"
   }'
   ```

   Resposta de exemplo da chamada de api VM auto-teste

   ```JSON
   {
       "TrackingId": "9bffc887-dd1d-40dd-a8a2-34cee4f4c4c3",
       "Response": "{\"SchemaVersion\":1,\"AppCertificationCategory\":\"Microsoft Single VM Certification\",\"ProviderID\":\"050DE427-2A99-46D4-817C-5354D3BF2AE8\",\"OSName\":\"Ubuntu 18.04\",\"OSDistro\":\"Ubuntu 18.04.5 LTS\",\"KernelVersion\":\"5.4.0-1023-azure\",\"KernelFullVersion\":\"Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\\n\",\"OSVersion\":\"18.04\",\"CreatedDate\":\"09/08/2020 01:13:47\",\"TestResult\":\"Pass\",\"APIVersion\":\"1.5\",\"Tests\":[{\"TestID\":\"48\",\"TestCaseName\":\"Bash History\",\"Description\":\"Bash history files should be cleared before creating the VM image.\",\"Result\":\"Passed\",\"ActualValue\":\"No file Exist\",\"RequiredValue\":\"1024\"},{\"TestID\":\"39\",\"TestCaseName\":\"Linux Agent Version\",\"Description\":\"Azure Linux Agent Version 2.2.41 and above should be installed.\",\"Result\":\"Passed\",\"ActualValue\":\"2.2.49\",\"RequiredValue\":\"2.2.41\"},{\"TestID\":\"40\",\"TestCaseName\":\"Required Kernel Parameters\",\"Description\":\"Verifies the following kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300\",\"Result\":\"Warning\",\"ActualValue\":\"Missing Parameter: rootdelay=300\\r\\nMatched Parameter: console=ttyS0,earlyprintk=ttyS0\",\"RequiredValue\":\"console=ttyS0#earlyprintk=ttyS0#rootdelay=300\"},{\"TestID\":\"41\",\"TestCaseName\":\"Swap Partition on OS Disk\",\"Description\":\"Verifies that no Swap partitions are created on the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"No. of Swap Partitions: 0\",\"RequiredValue\":\"swap\"},{\"TestID\":\"42\",\"TestCaseName\":\"Root Partition on OS Disk\",\"Description\":\"It is recommended that a single root partition is created for the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"Root Partition: 1\",\"RequiredValue\":\"1\"},{\"TestID\":\"44\",\"TestCaseName\":\"OpenSSL Version\",\"Description\":\"OpenSSL Version should be >=0.9.8.\",\"Result\":\"Passed\",\"ActualValue\":\"1.1.1\",\"RequiredValue\":\"0.9.8\"},{\"TestID\":\"45\",\"TestCaseName\":\"Python Version\",\"Description\":\"Python version 2.6+ is highly recommended. \",\"Result\":\"Passed\",\"ActualValue\":\"2.7.17\",\"RequiredValue\":\"2.6\"},{\"TestID\":\"46\",\"TestCaseName\":\"Client Alive Interval\",\"Description\":\"It is recommended to set ClientAliveInterval to 180. On the application need, it can be set between 30 to 235. \\nIf you are enabling the SSH for your end users this value must be set as explained.\",\"Result\":\"Warning\",\"ActualValue\":\"120\",\"RequiredValue\":\"ClientAliveInterval 180\"},{\"TestID\":\"49\",\"TestCaseName\":\"OS Architecture\",\"Description\":\"Only 64-bit operating system should be supported.\",\"Result\":\"Passed\",\"ActualValue\":\"x86_64\\n\",\"RequiredValue\":\"x86_64,amd64\"},{\"TestID\":\"50\",\"TestCaseName\":\"Security threats\",\"Description\":\"Identifies OS with recent high profile vulnerability that may need patching.  Ignore warning if system was patched as appropriate.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04\",\"RequiredValue\":\"OS impacted by GHOSTS\"},{\"TestID\":\"51\",\"TestCaseName\":\"Auto Update\",\"Description\":\"Identifies if Linux Agent Auto Update is enabled or not.\",\"Result\":\"Passed\",\"ActualValue\":\"# AutoUpdate.Enabled=y\\n\",\"RequiredValue\":\"Yes\"},{\"TestID\":\"52\",\"TestCaseName\":\"SACK Vulnerability patch verification\",\"Description\":\"Checks if the running Kernel Version has SACK vulnerability patch.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04.5 LTS,Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\",\"RequiredValue\":\"Yes\"}]}"
   }
   ```

## <a name="next-steps"></a>Passos seguintes

- Inscreva-se no [Partner Center](https://partner.microsoft.com/).
