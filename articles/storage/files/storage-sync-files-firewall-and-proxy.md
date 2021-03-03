---
title: Azure File Sync no local firewall e configurações de procuração | Microsoft Docs
description: Compreenda as definições de proxy e firewall do Azure File Sync. Reveja detalhes de configuração para portas, redes e ligações especiais ao Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/02/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f0dbe7f32f14eb4da3d591811d619eb2e9bea397
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729645"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>As definições do proxy e da firewall do Azure File Sync
O Azure File Sync liga os seus servidores no local aos Ficheiros Azure, permitindo a sincronização de vários locais e funcionalidades de tiering em nuvem. Como tal, um servidor no local deve ser ligado à internet. Um administrador de TI precisa decidir o melhor caminho para o servidor chegar aos serviços de cloud Azure.

Este artigo fornecerá informações sobre requisitos e opções específicos disponíveis para ligar o seu servidor com sucesso e segurança ao Azure File Sync.

Recomendamos que se leia [considerações de networking do Azure File Sync](storage-sync-files-networking-overview.md) antes de ler este como orientar.

## <a name="overview"></a>Descrição Geral
O Azure File Sync funciona como um serviço de orquestração entre o seu Windows Server, a sua partilha de ficheiros Azure e vários outros serviços Azure para sincronizar dados como descrito no seu grupo de sincronização. Para que o Azure File Sync funcione corretamente, terá de configurar os seus servidores para comunicar com os seguintes serviços Azure:

- Storage do Azure
- Azure File Sync
- Azure Resource Manager
- Serviços de autenticação

> [!Note]  
> O agente Azure File Sync no Windows Server inicia todos os pedidos para serviços na nuvem, o que resulta apenas em ter de considerar o tráfego de saída a partir de uma perspetiva de firewall. <br /> O serviço No Azure inicia uma ligação ao agente Azure File Sync.

## <a name="ports"></a>Portas
O Azure File Sync move os dados de ficheiros e metadados exclusivamente sobre HTTPS e requer que a porta 443 esteja aberta de saída.
Como resultado, todo o tráfego é encriptado.

## <a name="networks-and-special-connections-to-azure"></a>Redes e ligações especiais ao Azure
O agente Azure File Sync não tem requisitos em relação a canais especiais como [o ExpressRoute,](../../expressroute/expressroute-introduction.md)etc. para o Azure.

O Azure File Sync funcionará através de todos os meios disponíveis que permitam chegar ao Azure, adaptando-se automaticamente a várias características da rede, como largura de banda, latência e oferecendo controlo administrativo para afinação. Nem todas as funcionalidades estão disponíveis neste momento. Se quiser configurar comportamentos específicos, informe-nos através do [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
O Azure File Sync suporta configurações de procuração específicas de aplicações e de procuração em toda a máquina.

**As definições de procuração específicas** da aplicação permitem a configuração de um proxy especificamente para o tráfego de Sincronização de Ficheiros Azure. As definições de procuração específicas da aplicação são suportadas na versão 4.0.1.0 ou mais recente do agente e podem ser configuradas durante a instalação do agente ou utilizando o cmdlet powershell Set-StorageSyncProxyConfiguration.

comandos do PowerShell para configurar definições de proxy específicas da aplicação:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
Por exemplo, se o seu servidor proxy necessitar de autenticação com um nome de utilizador e senha, execute os seguintes comandos PowerShell:

```powershell
# IP address or name of the proxy server.
$Address="127.0.0.1"  

# The port to use for the connection to the proxy.
$Port=8080

# The user name for a proxy.
$UserName="user_name" 

# Please type or paste a string with a password for the proxy.
$SecurePassword = Read-Host -AsSecureString

$Creds = New-Object System.Management.Automation.PSCredential ($UserName, $SecurePassword)

# Please verify that you have entered the password correctly.
Write-Host $Creds.GetNetworkCredential().Password

Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"

Set-StorageSyncProxyConfiguration -Address $Address -Port $Port -ProxyCredential $Creds
```
**As definições de procuração em toda** a máquina são transparentes para o agente Azure File Sync, uma vez que todo o tráfego do servidor é encaminhado através do proxy.

Para configurar as definições de proxy ao nível do computador, siga os passos abaixo: 

1. Configurar configurações de procuração para aplicações .NET 

   - Editar estes dois ficheiros:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Adicione a secção <system.net> nos ficheiros machine.config (abaixo da secção <system.serviceModel>).  Altere 127.0.01:8888 para o endereço IP e porta para o servidor proxy. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. Defina as definições de procuração winHTTP 

   - Executar o seguinte comando a partir de uma solicitação de comando elevada ou PowerShell para ver a definição de procuração existente:   

     netsh winhttp show proxy

   - Executar o seguinte comando a partir de uma solicitação de comando elevada ou PowerShell para definir a definição de procuração (alterar 127.0.01:8888 para o endereço IP e porta para o servidor proxy):  

     netsh winhttp set proxy 127.0.0.1:8888

3. Reinicie o serviço de Agente de Sincronização de Armazenamento executando o seguinte comando a partir de uma solicitação de comando elevada ou PowerShell: 

      net stop filesyncsvc

      Nota: O serviço Desincronizador de Armazenamento (filesyncsvc) iniciar-se-á automaticamente uma vez parado.

## <a name="firewall"></a>Firewall
Como mencionado numa secção anterior, o porto 443 precisa de estar aberto de saída. Com base em políticas no seu datacenter, ramo ou região, pode ser desejado ou necessário restringir ainda mais o tráfego sobre esta porta a domínios específicos.

A tabela a seguir descreve os domínios necessários para a comunicação:

| Serviço | Ponto final da nuvem pública | Ponto final do Governo de Azure | Utilização |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | `https://management.azure.com` | https://management.usgovcloudapi.net | Qualquer chamada de utilizador (como PowerShell) vai para/através deste URL, incluindo a chamada inicial de registo do servidor. |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | As chamadas do Azure Resource Manager devem ser efetuadas por um utilizador autenticado. Para ter sucesso, este URL é utilizado para a autenticação do utilizador. |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | Como parte da implementação do Azure File Sync, será criado um diretor de serviço no Azure Ative Directory da subscrição. Esta URL é usada para isso. Este principal é utilizado para delegar um conjunto mínimo de direitos ao serviço Azure File Sync. O utilizador que efetua a configuração inicial do Azure File Sync deve ser um utilizador autenticado com privilégios de proprietário de subscrição. |
| **Azure Active Directory** | https://secure.aadcdn.microsoftonline-p.com | Use o URL do ponto final público. | Este URL é acedido pela biblioteca de autenticação ative Diretório que o UI do servidor Azure File Sync utiliza para iniciar sessão no administrador. |
| **Armazenamento do Azure** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | Quando o servidor descarrega um ficheiro, o servidor executa esse movimento de dados de forma mais eficiente quando fala diretamente para a partilha de ficheiros Azure na Conta de Armazenamento. O servidor tem uma chave SAS que apenas permite o acesso de partilha de ficheiros direcionado. |
| **Azure File Sync** | &ast;.one.microsoft.com<br>&ast;.afs.azure.net | &ast;.afs.azure.us | Após o registo inicial do servidor, o servidor recebe um URL regional para a instância do serviço Azure File Sync naquela região. O servidor pode utilizar o URL para comunicar de forma direta e eficiente com a instância que manuseia a sua sincronização. |
| **Microsoft PKI** |  https://www.microsoft.com/pki/mscorp/cps<br>http://crl.microsoft.com/pki/mscorp/crl/<br>http://mscrl.microsoft.com/pki/mscorp/crl/<br>http://ocsp.msocsp.com<br>http://ocsp.digicert.com/<br>http://crl3.digicert.com/ | https://www.microsoft.com/pki/mscorp/cps<br>http://crl.microsoft.com/pki/mscorp/crl/<br>http://mscrl.microsoft.com/pki/mscorp/crl/<br>http://ocsp.msocsp.com<br>http://ocsp.digicert.com/<br>http://crl3.digicert.com/ | Uma vez instalado o agente Azure File Sync, o URL PKI é utilizado para descarregar certificados intermédios necessários para comunicar com o serviço Azure File Sync e a partilha de ficheiros Azure. O URL OCSP é utilizado para verificar o estado de um certificado. |
| **Microsoft Update** | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;.ctldl.windowsupdate.com<br>&ast;.dl.delivery.mp.microsoft.com<br>&ast;.emdl.ws.microsoft.com | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;.ctldl.windowsupdate.com<br>&ast;.dl.delivery.mp.microsoft.com<br>&ast;.emdl.ws.microsoft.com | Uma vez instalado o agente Azure File Sync, os URLs da Microsoft Update são utilizados para descarregar atualizações do agente Azure File Sync. |

> [!Important]
> Ao permitir o tráfego &ast; de .afs.azure.net, o tráfego só é possível para o serviço de sincronização. Não existem outros serviços da Microsoft que utilizem este domínio.
> Ao permitir que o tráfego &ast; one.microsoft.com, o tráfego para mais do que apenas o serviço de sincronização é possível a partir do servidor. Existem muitos mais serviços microsoft disponíveis em subdomínios.

Se &ast; .afs.azure.net ou &ast; .one.microsoft.com for demasiado amplo, pode limitar a comunicação do servidor permitindo a comunicação a apenas instâncias regionais explícitas do serviço Azure Files Sync. Qual a(s) instância(s) a escolher depende da região do serviço de sincronização de armazenamento a que implementou e registou o servidor. Aquela região chama-se "URL de ponto final primário" na tabela abaixo.

Por razões de continuidade de negócios e recuperação de desastres (BCDR), pode ter especificado as suas ações de ficheiroS Azure numa conta de armazenamento globalmente redundante (GRS). Se for esse o caso, as suas ações de ficheiroS Azure falharão na região emparelhada em caso de uma paragem regional duradoura. O Azure File Sync utiliza os mesmos emparelhamentos regionais que o armazenamento. Por isso, se utilizar contas de armazenamento GRS, tem de ativar URLs adicionais para permitir que o seu servidor fale com a região emparelhada para o Azure File Sync. A tabela abaixo chama a esta "região emparelhada". Além disso, há um URL de perfil de gestor de tráfego que precisa ser ativado também. Isto garantirá que o tráfego de rede pode ser reencaminhado perfeitamente para a região emparelhada em caso de falha e é chamado de "DISCOVERY URL" na tabela abaixo.

| Cloud  | Region | URL de ponto final primário | Região emparelhada | URL de descoberta |
|--------|--------|----------------------|---------------|---------------|
| Público |Leste da Austrália | https: \/ /australiaeast01.afs.azure.net<br>https: \/ /kailani-aue.one.microsoft.com | Austrália Sudeste | https: \/ /tm-australiaeast01.afs.azure.net<br>https: \/ /tm-kailani-aue.one.microsoft.com |
| Público |Austrália Sudeste | https: \/ /australiasoutheast01.afs.azure.net<br>https: \/ /kailani-aus.one.microsoft.com | Leste da Austrália | https: \/ /tm-australiasoutheast01.afs.azure.net<br>https: \/ /tm-kailani-aus.one.microsoft.com |
| Público | Sul do Brasil | https: \/ /brazilsouth01.afs.azure.net | E.U.A. Centro-Sul | https: \/ /tm-brazilsouth01.afs.azure.net |
| Público | Canadá Central | https: \/ /canadacentral01.afs.azure.net<br>https: \/ /kailani-cac.one.microsoft.com | Leste do Canadá | https: \/ /tm-canadacentral01.afs.azure.net<br>https: \/ /tm-kailani-cac.one.microsoft.com |
| Público | Leste do Canadá | https: \/ /canadaeast01.afs.azure.net<br>https: \/ /kailani-cae.one.microsoft.com | Canadá Central | https: \/ /tm-canadaeast01.afs.azure.net<br>https: \/ /tm-kailani.cae.one.microsoft.com |
| Público | Índia Central | https: \/ /centralindia01.afs.azure.net<br>https: \/ /kailani-cin.one.microsoft.com | Sul da Índia | https: \/ /tm-centralindia01.afs.azure.net<br>https: \/ /tm-kailani-cin.one.microsoft.com |
| Público | E.U.A. Central | https: \/ /centralus01.afs.azure.net<br>https: \/ /kailani-cus.one.microsoft.com | E.U.A. Leste 2 | https: \/ /tm-centralus01.afs.azure.net<br>https: \/ /tm-kailani-cus.one.microsoft.com |
| Público | Ásia Leste | https: \/ /eastasia01.afs.azure.net<br>https: \/ /kailani11.one.microsoft.com | Sudeste Asiático | https: \/ /tm-eastasia01.afs.azure.net<br>https: \/ /tm-kailani11.one.microsoft.com |
| Público | E.U.A. Leste | https: \/ /eastus01.afs.azure.net<br>https: \/ /kailani1.one.microsoft.com | E.U.A. Oeste | https: \/ /tm-eastus01.afs.azure.net<br>https: \/ /tm-kailani1.one.microsoft.com |
| Público | E.U.A. Leste 2 | https: \/ /eastus201.afs.azure.net<br>https: \/ /kailani-ess.one.microsoft.com | E.U.A. Central | https: \/ /tm-eastus201.afs.azure.net<br>https: \/ /tm-kailani-ess.one.microsoft.com |
| Público | Alemanha Norte | https: \/ /germanynorth01.afs.azure.net | Alemanha Centro-Oeste | https: \/ /tm-germanywestcentral01.afs.azure.net |
| Público | Alemanha Centro-Oeste | https: \/ /germanywestcentral01.afs.azure.net | Alemanha Norte | https: \/ /tm-germanynorth01.afs.azure.net |
| Público | Leste do Japão | https: \/ /japaneast01.afs.azure.net | Oeste do Japão | https: \/ /tm-japaneast01.afs.azure.net |
| Público | Oeste do Japão | https: \/ /japanwest01.afs.azure.net | Leste do Japão | https: \/ /tm-japanwest01.afs.azure.net |
| Público | Coreia do Sul Central | https: \/ /koreacentral01.afs.azure.net/ | Sul da Coreia do Sul | https: \/ /tm-koreacentral01.afs.azure.net/ |
| Público | Sul da Coreia do Sul | https: \/ /koreasouth01.afs.azure.net/ | Coreia do Sul Central | https: \/ /tm-koreasouth01.afs.azure.net/ |
| Público | E.U.A. Centro-Norte | https: \/ /northcentralus01.afs.azure.net | E.U.A. Centro-Sul | https: \/ /tm-northcentralus01.afs.azure.net |
| Público | Europa do Norte | https: \/ /northeurope01.afs.azure.net<br>https: \/ /kailani7.one.microsoft.com | Europa Ocidental | https: \/ /tm-northeurope01.afs.azure.net<br>https: \/ /tm-kailani7.one.microsoft.com |
| Público | E.U.A. Centro-Sul | https: \/ /southcentralus01.afs.azure.net | E.U.A. Centro-Norte | https: \/ /tm-southcentralus01.afs.azure.net |
| Público | Sul da Índia | https: \/ /southindia01.afs.azure.net<br>https: \/ /kailani-sin.one.microsoft.com | Índia Central | https: \/ /tm-southindia01.afs.azure.net<br>https: \/ /tm-kailani-sin.one.microsoft.com |
| Público | Sudeste Asiático | https: \/ /southeastasia01.afs.azure.net<br>https: \/ /kailani10.one.microsoft.com | Ásia Leste | https: \/ /tm-southeastasia01.afs.azure.net<br>https: \/ /tm-kailani10.one.microsoft.com |
| Público | Suíça Norte | https: \/ /switzerlandnorth01.afs.azure.net<br>https: \/ /tm-switzerlandnorth01.afs.azure.net | Suíça Oeste | https: \/ /switzerlandwest01.afs.azure.net<br>https: \/ /tm-switzerlandwest01.afs.azure.net |
| Público | Suíça Oeste | https: \/ /switzerlandwest01.afs.azure.net<br>https: \/ /tm-switzerlandwest01.afs.azure.net | Suíça Norte | https: \/ /switzerlandnorth01.afs.azure.net<br>https: \/ /tm-switzerlandnorth01.afs.azure.net |
| Público | Sul do Reino Unido | https: \/ /uksouth01.afs.azure.net<br>https: \/ /kailani-uks.one.microsoft.com | Oeste do Reino Unido | https: \/ /tm-uksouth01.afs.azure.net<br>https: \/ /tm-kailani-uks.one.microsoft.com |
| Público | Oeste do Reino Unido | https: \/ /ukwest01.afs.azure.net<br>https: \/ /kailani-ukw.one.microsoft.com | Sul do Reino Unido | https: \/ /tm-ukwest01.afs.azure.net<br>https: \/ /tm-kailani-ukw.one.microsoft.com |
| Público | E.U.A. Centro-Oeste | https: \/ /westcentralus01.afs.azure.net | E.U.A. Oeste 2 | https: \/ /tm-westcentralus01.afs.azure.net |
| Público | Europa Ocidental | https: \/ /westeurope01.afs.azure.net<br>https: \/ /kailani6.one.microsoft.com | Europa do Norte | https: \/ /tm-westeurope01.afs.azure.net<br>https: \/ /tm-kailani6.one.microsoft.com |
| Público | E.U.A. Oeste | https: \/ /westus01.afs.azure.net<br>https: \/ /kailani.one.microsoft.com | E.U.A. Leste | https: \/ /tm-westus01.afs.azure.net<br>https: \/ /tm-kailani.one.microsoft.com |
| Público | E.U.A. Oeste 2 | https: \/ /westus201.afs.azure.net | E.U.A. Centro-Oeste | https: \/ /tm-westus201.afs.azure.net |
| Administração Pública | US Gov - Arizona | https: \/ /usgovarizona01.afs.azure.us | US Gov - Texas | https: \/ /tm-usgovarizona01.afs.azure.us |
| Administração Pública | US Gov - Texas | https: \/ /usgovtexas01.afs.azure.us | US Gov - Arizona | https: \/ /tm-usgovtexas01.afs.azure.us |

- Se utilizar contas de armazenamento redundantes locais (LRS) ou zonas redundantes (ZRS), só precisa de ativar o URL listado em "URL de ponto final primário".

- Se utilizar contas de armazenamento globalmente redundantes (GRS), ative três URLs.

**Exemplo:** Implementa um serviço de sincronização de armazenamento `"West US"` e regista o seu servidor com ele. Os URLs para permitir que o servidor se comunique para este caso são:

> - https: \/ /westus01.afs.azure.net (ponto final principal: West US)
> - https: \/ /eastus01.afs.azure.net (região de fail-over emparelhada: Leste dos EUA)
> - https: \/ /tm-westus01.afs.azure.net (URL de descoberta da região primária)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Permitir lista de endereços IP sincronizados de ficheiros Azure
O Azure File Sync suporta a utilização de tags de [serviço,](../../virtual-network/service-tags-overview.md)que representam um grupo de prefixos de endereço IP para um determinado serviço Azure. Pode utilizar tags de serviço para criar regras de firewall que permitem a comunicação com o serviço Azure File Sync. A etiqueta de serviço para Azure File Sync é `StorageSyncService` .

Se estiver a utilizar o Azure File Sync dentro do Azure, pode utilizar o nome da etiqueta de serviço diretamente no seu grupo de segurança de rede para permitir o tráfego. Para saber mais sobre como fazê-lo, consulte [os grupos de segurança da Rede.](../../virtual-network/network-security-groups-overview.md)

Se estiver a utilizar o Azure File Sync no local, pode utilizar a marca de serviço API para obter intervalos de endereços IP específicos para a lista de autorizações da sua firewall. Existem dois métodos para obter esta informação:

- A lista atual de intervalos de endereços IP para todos os serviços Azure que suportam tags de serviço são publicadas semanalmente no Microsoft Download Center sob a forma de um documento JSON. Cada nuvem Azure tem o seu próprio documento JSON com os intervalos de endereço IP relevantes para essa nuvem:
    - [Público de Azure](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure China](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Alemanha](https://www.microsoft.com/download/details.aspx?id=57064)
- A advimento da marca de serviço API (pré-visualização) permite a recuperação programática da lista atual de tags de serviço. Na pré-visualização, a a API de descoberta da marca de serviço pode devolver informações menos atuais do que as informações devolvidas dos documentos JSON publicados no Microsoft Download Center. Pode utilizar a superfície API com base na sua preferência de automatização:
    - [API REST](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [CLI do Azure](/cli/azure/network#az-network-list-service-tags)

Como a descoberta da marca de serviço API não é atualizada com tanta frequência como os documentos JSON publicados no Microsoft Download Center, recomendamos a utilização do documento JSON para atualizar a lista de autorizações da sua firewall no local. Isto pode ser feito da seguinte forma:

```PowerShell
# The specific region to get the IP address ranges for. Replace westus2 with the desired region code 
# from Get-AzLocation.
$region = "westus2"

# The service tag for Azure File Sync. Do not change unless you're adapting this
# script for another service.
$serviceTag = "StorageSyncService"

# Download date is the string matching the JSON document on the Download Center. 
$possibleDownloadDates = 0..7 | `
    ForEach-Object { [System.DateTime]::Now.AddDays($_ * -1).ToString("yyyyMMdd") }

# Verify the provided region
$validRegions = Get-AzLocation | `
    Where-Object { $_.Providers -contains "Microsoft.StorageSync" } | `
    Select-Object -ExpandProperty Location

if ($validRegions -notcontains $region) {
    Write-Error `
            -Message "The specified region $region is not available. Either Azure File Sync is not deployed there or the region does not exist." `
            -ErrorAction Stop
}

# Get the Azure cloud. This should automatically based on the context of 
# your Az PowerShell login, however if you manually need to populate, you can find
# the correct values using Get-AzEnvironment.
$azureCloud = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty Name

# Build the download URI
$downloadUris = @()
switch($azureCloud) {
    "AzureCloud" { 
        $downloadUris = $possibleDownloadDates | ForEach-Object {  
            "https://download.microsoft.com/download/7/1/D/71D86715-5596-4529-9B13-DA13A5DE5B63/ServiceTags_Public_$_.json"
        }
    }

    "AzureUSGovernment" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/6/4/D/64DB03BF-895B-4173-A8B1-BA4AD5D4DF22/ServiceTags_AzureGovernment_$_.json"
        }
    }

    "AzureChinaCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/9/D/0/9D03B7E2-4B80-4BF3-9B91-DA8C7D3EE9F9/ServiceTags_China_$_.json"
        }
    }

    "AzureGermanCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/0/7/6/076274AB-4B0B-4246-A422-4BAF1E03F974/ServiceTags_AzureGermany_$_.json"
        }
    }

    default {
        Write-Error -Message "Unrecognized Azure Cloud: $_" -ErrorAction Stop
    }
}

# Find most recent file
$found = $false 
foreach($downloadUri in $downloadUris) {
    try { $response = Invoke-WebRequest -Uri $downloadUri -UseBasicParsing } catch { }
    if ($response.StatusCode -eq 200) {
        $found = $true
        break
    }
}

if ($found) {
    # Get the raw JSON 
    $content = [System.Text.Encoding]::UTF8.GetString($response.Content)

    # Parse the JSON
    $serviceTags = ConvertFrom-Json -InputObject $content -Depth 100

    # Get the specific $ipAddressRanges
    $ipAddressRanges = $serviceTags | `
        Select-Object -ExpandProperty values | `
        Where-Object { $_.id -eq "$serviceTag.$region" } | `
        Select-Object -ExpandProperty properties | `
        Select-Object -ExpandProperty addressPrefixes
} else {
    # If the file cannot be found, that means there hasn't been an update in
    # more than a week. Please verify the download URIs are still accurate
    # by checking https://docs.microsoft.com/azure/virtual-network/service-tags-overview
    Write-Verbose -Message "JSON service tag file not found."
    return
}
```

Em seguida, pode utilizar os intervalos de endereço IP `$ipAddressRanges` para atualizar a sua firewall. Consulte o site do seu firewall/sistema de aparelhos para obter informações sobre como atualizar a sua firewall.

## <a name="test-network-connectivity-to-service-endpoints"></a>Conectividade da rede de teste para pontos finais de serviço
Uma vez registado um servidor no serviço Azure File Sync, o Test-StorageSyncNetworkConnectivity cmdlet e ServerRegistration.exe pode ser utilizado para testar comunicações com todos os pontos finais (URLs) específicos deste servidor. Este cmdlet pode ajudar a resolver problemas quando a comunicação incompleta impede o servidor de funcionar plenamente com o Azure File Sync e pode ser usado para afinar configurações de procuração e firewall.

Para executar o teste de conectividade da rede, instale a versão 9.1 ou posterior do agente Azure File Sync e execute os seguintes comandos PowerShell:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Resumo e limitação de risco
As listas anteriores deste documento contêm o URLs Azure File Sync atualmente comunica. As firewalls devem ser capazes de permitir a saída de tráfego para estes domínios. A Microsoft esforça-se por manter esta lista atualizada.

A criação de regras de firewall de restrição de domínios pode ser uma medida para melhorar a segurança. Se estas configurações de firewall forem utilizadas, é preciso ter em mente que os URLs serão adicionados e poderão até mudar com o tempo. Verifique este artigo periodicamente.

## <a name="next-steps"></a>Passos seguintes
- [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
- [Implementar o Azure File Sync](storage-sync-files-deployment-guide.md)
- [Monitorizar o Azure File Sync](storage-sync-files-monitoring.md)
