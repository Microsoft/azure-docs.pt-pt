---
title: Definições de firewall e proxy do Ficheiro Azure no local / Microsoft Docs
description: Configuração da rede Azure File Sync no local
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 37c646e2f08745b2a12df41b6310fb5d3834998b
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142559"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>As definições do proxy e da firewall do Azure File Sync
O Azure File Sync liga os seus servidores no local aos Ficheiros Azure, permitindo a sincronização multi-site e as funcionalidades de tiering em nuvem. Como tal, um servidor no local deve ser ligado à internet. Um administrador de TI precisa decidir o melhor caminho para o servidor chegar aos serviços de nuvem Azure.

Este artigo fornecerá informações sobre requisitos e opções específicas disponíveis para ligar com sucesso e de forma segura o seu servidor ao Azure File Sync.

## <a name="overview"></a>Descrição geral
O Azure File Sync funciona como um serviço de orquestração entre o seu Windows Server, a sua partilha de ficheiros Azure e vários outros serviços Azure para sincronizar dados conforme descrito no seu grupo de sincronização. Para que o Azure File Sync funcione corretamente, terá de configurar os seus servidores para comunicar com os seguintes serviços Azure:

- Storage do Azure
- Azure File Sync
- Azure Resource Manager
- Serviços de autenticação

> [!Note]  
> O agente Dessincronização de ficheiros Azure no Windows Server inicia todos os pedidos de serviços na nuvem, o que resulta em apenas ter de considerar o tráfego de saída a partir de uma perspetiva de firewall. <br /> Nenhum serviço Azure inicia uma ligação com o agente Dessincronização de Ficheiros Azure.

## <a name="ports"></a>Portas
O Azure File Sync move dados de ficheiros e metadados exclusivamente sobre HTTPS e requer que a porta 443 seja aberta.
Como resultado, todo o tráfego está encriptado.

## <a name="networks-and-special-connections-to-azure"></a>Redes e ligações especiais ao Azure
O agente Dessincronização de Ficheiros Azure não tem requisitos em relação a canais especiais como [expressRoute](../../expressroute/expressroute-introduction.md), etc. para o Azure.

O Azure File Sync funcionará através de todos os meios disponíveis que permitam chegar ao Azure, adaptando-se automaticamente a várias características da rede, como largura de banda, latência e oferta de controlo administrativo para afinação. Nem todas as funcionalidades estão disponíveis neste momento. Se quiser configurar comportamentos específicos, avise-nos através do [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
O Azure File Sync suporta definições específicas de procuração específicas e de procuração em toda a máquina.

**As definições de proxy específicas** da aplicação permitem a configuração de um proxy especificamente para o tráfego de Sincronização de Ficheiros Azure. As definições de proxy específicas da aplicação são suportadas na versão 4.0.1.0 ou mais recente do agente e podem ser configuradas durante a instalação do agente ou utilizando o cmdlet de Configuração PowerShell de Configuração De Configuração De Configuração De Configuração De Configuração De Conjunto-ArmazenamentoSyncProxy.

PowerShell ordena configurar definições de proxy específicas da aplicação:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
**As definições de procuração** em toda a máquina são transparentes para o agente Dessincronização de ficheiros Azure, uma vez que todo o tráfego do servidor é encaminhado através do proxy.

Para configurar as definições de procuração em toda a máquina, siga os passos abaixo: 

1. Configure as definições de procuração para aplicações .NET 

   - Editar estes dois ficheiros:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Adicione a secção <system.net> secção nos ficheiros machine.config (abaixo da secção <system.serviceModel> ).  Altere 127.0.01:8888 para o endereço IP e porta para o servidor proxy. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. Definir as definições de proxy WinHTTP 

   - Executar o seguinte comando a partir de um pedido de comando elevado ou PowerShell para ver a definição de procuração existente:   

     netsh winhttp mostrar procuração

   - Executar o seguinte comando a partir de um pedido de comando elevado ou PowerShell para definir a definição de procuração (alteração 127.01:8888 para o endereço IP e porta para o servidor proxy):  

     netsh winhttp set proxy 127.0.0.1:8888

3. Reiniciar o serviço de Agente de Sincronização de Armazenamento executando o seguinte comando a partir de um pedido de comando elevado ou PowerShell: 

      net stop filesyncsvc

      Nota: O serviço de Agente de Sincronização de Armazenamento (filesyncsvc) iniciará automaticamente uma vez parado.

## <a name="firewall"></a>Firewall
Como referido numa secção anterior, o porto 443 tem de ser aberto. Com base em políticas no seu datacenter, sucursal ou região, pode ser desejado ou necessário um maior restrição do tráfego sobre este porto para domínios específicos.

A tabela seguinte descreve os domínios necessários para a comunicação:

| Serviço | Ponto final da nuvem pública | Ponto final do Governo de Azure | Utilização |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | `https://management.azure.com` | https://management.usgovcloudapi.net | Qualquer chamada de utilizador (como powerShell) vai para/através deste URL, incluindo a chamada inicial de registo do servidor. |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | As chamadas do Gestor de Recursos Azure devem ser feitas por um utilizador autenticado. Para ter sucesso, este URL é utilizado para a autenticação do utilizador. |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | Como parte da implementação do Azure File Sync, será criado um diretor de serviço no Diretório Ativo Azure da subscrição. Este URL é usado para isso. Este principal é utilizado para delegar um conjunto mínimo de direitos ao serviço Desincronização de Ficheiros Azure. O utilizador que executa a configuração inicial do Azure File Sync deve ser um utilizador autenticado com privilégios do proprietário da subscrição. |
| **Azure Active Directory** | https://secure.aadcdn.microsoftonline-p.com | Utilize o URL de ponto final público. | Este URL é acedido pela biblioteca de autenticação Ative Directory que o UI de registo do servidor Do Ficheiro Azure utiliza para iniciar sessão no administrador. |
| **Armazenamento Azure** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | Quando o servidor descarrega um ficheiro, então o servidor executa esse movimento de dados de forma mais eficiente quando fala diretamente com a parte de ficheiro Azure na Conta de Armazenamento. O servidor tem uma chave SAS que só permite o acesso à partilha de ficheiros direcionado. |
| **Azure File Sync** | &ast;.one.microsoft.com<br>&ast;.afs.azure.net | &ast;.afs.azure.us | Após o registo inicial do servidor, o servidor recebe um URL regional para a instância de serviço Azure File Sync naquela região. O servidor pode utilizar o URL para comunicar direta e eficientemente com a instância que manuseia a sua sincronização. |
| **Microsoft PKI** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | Uma vez instalado o agente Bluee File Sync, o URL PKI é utilizado para descarregar certificados intermédios necessários para comunicar com o serviço Desincronização de Ficheiros Azure e a partilha de ficheiros Azure. O URL OCSP é utilizado para verificar o estado de um certificado. |

> [!Important]
> Ao permitir que o tráfego &ast; afs.azure.net, o tráfego só é possível para o serviço de sincronização. Não existem outros serviços da Microsoft que utilizem este domínio.
> Ao permitir que o tráfego one.microsoft.com, o &ast; tráfego para mais do que apenas o serviço de sincronização é possível a partir do servidor. Existem muitos mais serviços da Microsoft disponíveis em subdomínios.

Se &ast; .afs.azure.net ou &ast; .one.microsoft.com for demasiado ampla, pode limitar a comunicação do servidor, permitindo a comunicação apenas a instâncias regionais explícitas do serviço Azure Files Sync. Que instância(s) escolher depende da região do serviço de sincronização de armazenamento que implementou e registou o servidor. Esta região chama-se "URL de ponto final primário" na tabela abaixo.

Por razões de continuidade de negócios e recuperação de desastres (BCDR), pode ter especificado as suas ações de ficheiro Azure numa conta de armazenamento globalmente redundante (GRS). Se for esse o caso, então as suas ações de ficheiro Saque A partir de Azure falhará na região emparelhada em caso de uma paragem regional duradoura. O Azure File Sync utiliza os mesmos pares regionais que o armazenamento. Por isso, se utilizar contas de armazenamento GRS, tem de ativar URLs adicionais para permitir que o seu servidor fale com a região emparelhada para o Azure File Sync. A tabela abaixo chama a esta "região emparelhada". Além disso, existe um URL de perfil de gestor de tráfego que precisa ser ativado também. Isto garantirá que o tráfego da rede pode ser perfeitamente reencaminhado para a região emparelhada em caso de falha e é chamado de "Discovery URL" na tabela abaixo.

| Cloud  | Região | URL de ponto final primário | Região emparelhada | URL de descoberta |
|--------|--------|----------------------|---------------|---------------|
| Público |Leste da Austrália | https: \/ /australiaeast01.afs.azure.net<br>https: \/ /kailani-aue.one.microsoft.com | Austrália Sudeste | https: \/ /tm-australiaeast01.afs.azure.net<br>https: \/ /tm-kailani-aue.one.microsoft.com |
| Público |Austrália Sudeste | https: \/ /australiasoutheast01.afs.azure.net<br>https: \/ /kailani-aus.one.microsoft.com | Leste da Austrália | https: \/ /tm-australiasoutheast01.afs.azure.net<br>https: \/ /tm-kailani-aus.one.microsoft.com |
| Público | Sul do Brasil | https: \/ /brazilsouth01.afs.azure.net | E.U.A. Centro-Sul | https: \/ /tm-brazilsouth01.afs.azure.net |
| Público | Canadá Central | https: \/ /canadacentral01.afs.azure.net<br>https: \/ /kailani-cac.one.microsoft.com | Leste do Canadá | https: \/ /tm-canadacentral01.afs.azure.net<br>https: \/ /tm-kailani-cac.one.microsoft.com |
| Público | Leste do Canadá | https: \/ /canadaeast01.afs.azure.net<br>https: \/ /kailani-cae.one.microsoft.com | Canadá Central | https: \/ /tm-canadaeast01.afs.azure.net<br>https: \/ /tm-kailani.cae.one.microsoft.com |
| Público | Índia Central | https: \/ /centralindia01.afs.azure.net<br>https: \/ /kailani-cin.one.microsoft.com | Sul da Índia | https: \/ /tm-centralindia01.afs.azure.net<br>https: \/ /tm-kailani-cin.one.microsoft.com |
| Público | E.U.A. Central | https: \/ /centralus01.afs.azure.net<br>https: \/ /kailani-cus.one.microsoft.com | E.U.A. Leste 2 | https: \/ /tm-centralus01.afs.azure.net<br>https: \/ /tm-kailani-cus.one.microsoft.com |
| Público | Ásia Leste | https: \/ /eastasia01.afs.azure.net<br>https: \/ /kailani11.one.microsoft.com | Ásia Sudeste | https: \/ /tm-eastasia01.afs.azure.net<br>https: \/ /tm-kailani11.one.microsoft.com |
| Público | E.U.A. Leste | https: \/ /eastus01.afs.azure.net<br>https: \/ /kailani1.one.microsoft.com | E.U.A. Oeste | https: \/ /tm-eastus01.afs.azure.net<br>https: \/ /tm-kailani1.one.microsoft.com |
| Público | E.U.A. Leste 2 | https: \/ /eastus201.afs.azure.net<br>https: \/ /kailani-ess.one.microsoft.com | E.U.A. Central | https: \/ /tm-eastus201.afs.azure.net<br>https: \/ /tm-kailani-ess.one.microsoft.com |
| Público | Leste do Japão | https: \/ /japaneast01.afs.azure.net | Oeste do Japão | https: \/ /tm-japaneast01.afs.azure.net |
| Público | Oeste do Japão | https: \/ /japanwest01.afs.azure.net | Leste do Japão | https: \/ /tm-japanwest01.afs.azure.net |
| Público | Coreia do Sul Central | https: \/ /koreacentral01.afs.azure.net/ | Sul da Coreia do Sul | https: \/ /tm-koreacentral01.afs.azure.net/ |
| Público | Sul da Coreia do Sul | https: \/ /koreasouth01.afs.azure.net/ | Coreia do Sul Central | https: \/ /tm-koreasouth01.afs.azure.net/ |
| Público | E.U.A. Centro-Norte | https: \/ /northcentralus01.afs.azure.net | E.U.A. Centro-Sul | https: \/ /tm-northcentralus01.afs.azure.net |
| Público | Europa do Norte | https: \/ /northeurope01.afs.azure.net<br>https: \/ /kailani7.one.microsoft.com | Europa ocidental | https: \/ /tm-northeurope01.afs.azure.net<br>https: \/ /tm-kailani7.one.microsoft.com |
| Público | E.U.A. Centro-Sul | https: \/ /southcentralus01.afs.azure.net | E.U.A. Centro-Norte | https: \/ /tm-southcentralus01.afs.azure.net |
| Público | Sul da Índia | https: \/ /southindia01.afs.azure.net<br>https: \/ /kailani-sin.one.microsoft.com | Índia Central | https: \/ /tm-southindia01.afs.azure.net<br>https: \/ /tm-kailani-sin.one.microsoft.com |
| Público | Ásia Sudeste | https: \/ /southeastasia01.afs.azure.net<br>https: \/ /kailani10.one.microsoft.com | Ásia Leste | https: \/ /tm-southeastasia01.afs.azure.net<br>https: \/ /tm-kailani10.one.microsoft.com |
| Público | Sul do Reino Unido | https: \/ /uksouth01.afs.azure.net<br>https: \/ /kailani-uks.one.microsoft.com | Oeste do Reino Unido | https: \/ /tm-uksouth01.afs.azure.net<br>https: \/ /tm-kailani-uks.one.microsoft.com |
| Público | Oeste do Reino Unido | https: \/ /ukwest01.afs.azure.net<br>https: \/ /kailani-ukw.one.microsoft.com | Sul do Reino Unido | https: \/ /tm-ukwest01.afs.azure.net<br>https: \/ /tm-kailani-ukw.one.microsoft.com |
| Público | E.U.A. Centro-Oeste | https: \/ /westcentralus01.afs.azure.net | E.U.A.Oeste 2 | https: \/ /tm-westcentralus01.afs.azure.net |
| Público | Europa ocidental | https: \/ /westeurope01.afs.azure.net<br>https: \/ /kailani6.one.microsoft.com | Europa do Norte | https: \/ /tm-westeurope01.afs.azure.net<br>https: \/ /tm-kailani6.one.microsoft.com |
| Público | E.U.A. Oeste | https: \/ /westus01.afs.azure.net<br>https: \/ /kailani.one.microsoft.com | E.U.A. Leste | https: \/ /tm-westus01.afs.azure.net<br>https: \/ /tm-kailani.one.microsoft.com |
| Público | E.U.A.Oeste 2 | https: \/ /westus201.afs.azure.net | E.U.A. Centro-Oeste | https: \/ /tm-westus201.afs.azure.net |
| Administração Pública | US Gov - Arizona | https: \/ /usgovarizona01.afs.azure.us | US Gov - Texas | https: \/ /tm-usgovarizona01.afs.azure.us |
| Administração Pública | US Gov - Texas | https: \/ /usgovtexas01.afs.azure.us | US Gov - Arizona | https: \/ /tm-usgovtexas01.afs.azure.us |

- Se utilizar contas de armazenamento redundantes localmente redundantes (LRS) ou de zona redundante (ZRS), apenas necessita de ativar o URL listado em "URL de ponto final primário".

- Se utilizar contas de armazenamento globalmente redundantes (GRS), ative três URLs.

**Exemplo:** Implementa um serviço de sincronização de armazenamento `"West US"` e regista o seu servidor com ele. Os URLs para permitir que o servidor se comunique para este caso são:

> - https: \/ /westus01.afs.azure.net (ponto final primário: Oeste dos EUA)
> - https: \/ /eastus01.afs.azure.net (região de fail-over emparelhada: Leste dos EUA)
> - https: \/ /tm-westus01.afs.azure.net (URL de descoberta da região primária)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Permitir lista para endereços IP de Sincronização de Ficheiros Azure
O Azure File Sync suporta a utilização de etiquetas de [serviço,](../../virtual-network/service-tags-overview.md)que representam um grupo de prefixos de endereço IP para um determinado serviço Azure. Pode utilizar etiquetas de serviço para criar regras de firewall que permitam a comunicação com o serviço Dessincronização de Ficheiros Azure. A etiqueta de serviço para O Sincronizado de Ficheiros Azure é `StorageSyncService` .

Se estiver a utilizar o Azure File Sync dentro do Azure, pode utilizar o nome da etiqueta de serviço diretamente no seu grupo de segurança de rede para permitir o tráfego. Para saber mais sobre como fazê-lo, consulte os grupos de [segurança da Rede.](../../virtual-network/security-overview.md)

Se estiver a utilizar o Azure File Sync no local, pode utilizar a etiqueta de serviço API para obter intervalos de endereçoIP específicos para a lista de autorizações da sua firewall. Existem dois métodos para obter esta informação:

- A lista atual de intervalos de endereços IP para todas as etiquetas de suporte de serviços Azure é publicada semanalmente no Microsoft Download Center sob a forma de um documento JSON. Cada nuvem Azure tem o seu próprio documento JSON com as gamas de endereços IP relevantes para essa nuvem:
    - [Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure China](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Alemanha](https://www.microsoft.com/download/details.aspx?id=57064)
- A API (pré-visualização) permite a recuperação programática da lista atual de tags de serviço. Na pré-visualização, a API de identificação da etiqueta de serviço pode devolver informações menos atuais do que as informações devolvidas dos documentos JSON publicados no Microsoft Download Center. Pode utilizar a superfície API com base na sua preferência de automação:
    - [API REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [CLI do Azure](https://docs.microsoft.com/cli/azure/network#az-network-list-service-tags)

Uma vez que a API de identificação da etiqueta de serviço não é atualizada com tanta frequência como os documentos JSON publicados no Microsoft Download Center, recomendamos a utilização do documento JSON para atualizar a lista de autorização da firewall no local. Isto pode ser feito da seguinte forma:

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

Em seguida, pode utilizar os intervalos de endereçoIP `$ipAddressRanges` para atualizar a sua firewall. Consulte o site do seu firewall/rede para obter informações sobre como atualizar a sua firewall.

## <a name="test-network-connectivity-to-service-endpoints"></a>Testar a conectividade da rede aos pontos finais do serviço
Uma vez registado um servidor com o serviço Desincronização de Ficheiros Azure, o Cmdlet de Conectividade Test-StorageSyncNetworkE e serverRegistration.exe podem ser utilizados para testar comunicações com todos os pontos finais (URLs) específicos deste servidor. Este cmdlet pode ajudar a resolver problemas quando uma comunicação incompleta impede o servidor de trabalhar totalmente com o Azure File Sync e pode ser usado para afinar configurações de proxy e firewall.

Para executar o teste de conectividade da rede, instale a versão 9.1 ou posterior do agente Dessincronizado do Ficheiro Azure e execute os seguintes comandos PowerShell:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Limitação do resumo e do risco
As listas anteriores neste documento contêm o URLs Azure File Sync com que comunica atualmente. As firewalls devem ser capazes de permitir a saída do tráfego para estes domínios. A Microsoft esforça-se por manter esta lista atualizada.

A criação de regras de firewall de restrição de domínio pode ser uma medida para melhorar a segurança. Se estas configurações de firewall forem utilizadas, é preciso ter em mente que os URLs serão adicionados e podem até mudar ao longo do tempo. Verifique este artigo periodicamente.

## <a name="next-steps"></a>Próximos passos
- [Planear uma implementação da Sincronização de Ficheiros do Azure](storage-sync-files-planning.md)
- [Implementar Azure File Sync](storage-sync-files-deployment-guide.md)
- [Monitorizar o Azure File Sync](storage-sync-files-monitoring.md)
