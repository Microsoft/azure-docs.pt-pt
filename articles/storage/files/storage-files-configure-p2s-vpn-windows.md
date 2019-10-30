---
title: Configurar uma VPN ponto a site (P2S) no Windows para uso com os arquivos do Azure | Microsoft Docs
description: Como configurar uma VPN ponto a site (P2S) no Windows para uso com os arquivos do Azure
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 527ab905997d18433d1dba5c16ee67c8146f5afa
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73141739"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Configurar uma VPN ponto a site (P2S) no Windows para uso com os arquivos do Azure
Você pode usar uma conexão VPN ponto a site (P2S) para montar seus compartilhamentos de arquivos do Azure via SMB de fora do Azure, sem abrir a porta 445. Uma conexão VPN ponto a site é uma conexão VPN entre o Azure e um cliente individual. Para usar uma conexão VPN P2S com os arquivos do Azure, uma conexão VPN P2S precisará ser configurada para cada cliente que deseja se conectar. Se você tiver muitos clientes que precisam se conectar aos compartilhamentos de arquivos do Azure de sua rede local, você poderá usar uma conexão VPN de site a site (S2S) em vez de uma conexão ponto a site para cada cliente. Para saber mais, confira [Configurar uma VPN site a site para uso com os arquivos do Azure](storage-files-configure-s2s-vpn.md).

É altamente recomendável que você leia [considerações de rede para acesso direto ao compartilhamento de arquivos do Azure](storage-files-networking-overview.md) antes de continuar com este artigo para obter uma discussão completa das opções de rede disponíveis para os arquivos do Azure.

O artigo fornece detalhes sobre as etapas para configurar uma VPN ponto a site no Windows (Windows Client e Windows Server) para montar compartilhamentos de arquivos do Azure diretamente no local. Se você pretende rotear Sincronização de Arquivos do Azure tráfego por uma VPN, consulte [definindo configurações de proxy e firewall de sincronização de arquivos do Azure](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Pré-requisitos
- A versão mais recente do módulo Azure PowerShell. Para obter mais informações sobre como instalar o Azure PowerShell, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) e selecionar o sistema operacional. Se preferir usar o CLI do Azure no Windows, você pode, no entanto, as instruções a seguir são apresentadas para Azure PowerShell.

- O módulo PowerShell DNS privado do Azure. Isso não é atualmente distribuído como parte do módulo Azure PowerShell, portanto, isso pode ser instalado com o seguinte método:
    ```PowerShell
    if ($PSVersionTable.PSVersion -ge [System.Version]::new(6, 0)) {
        Install-Module -Name Az.PrivateDns -AllowClobber -AllowPrerelease
    } else {
        Install-Module -Name Az.PrivateDns -RequiredVersion "0.1.3"
    }

    Import-Module -Name Az.PrivateDns
    ```  

- Um compartilhamento de arquivos do Azure que você deseja montar no local. Você pode usar um compartilhamento de arquivos [Standard](storage-how-to-create-file-share.md) ou [Premium do Azure](storage-how-to-create-premium-fileshare.md) com a VPN ponto a site.

## <a name="deploy-a-virtual-network"></a>Implantar uma rede virtual
Para acessar o compartilhamento de arquivos do Azure e outros recursos do Azure do local por meio de uma VPN ponto a site, você deve criar uma rede virtual ou VNet. A conexão VPN P2S que você criará automaticamente é uma ponte entre seu computador Windows local e essa rede virtual do Azure.

O PowerShell a seguir criará uma rede virtual do Azure com três sub-redes: uma para o ponto de extremidade de serviço da sua conta de armazenamento, uma para o ponto de extremidade particular da sua conta de armazenamento, que é necessário para acessar a conta de armazenamento local sem criar roteamento personalizado para o IP público da conta de armazenamento que pode ser alterado e outro para o gateway de rede virtual que fornece o serviço de VPN. 

Lembre-se de substituir `<region>`, `<resource-group>`e `<desired-vnet-name>` pelos valores apropriados para o seu ambiente.

```PowerShell
$region = "<region>"
$resourceGroupName = "<resource-group>" 
$virtualNetworkName = "<desired-vnet-name>"

$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName `
    -Location $region `
    -AddressPrefix "192.168.0.0/16"

Add-AzVirtualNetworkSubnetConfig `
    -Name "ServiceEndpointSubnet" `
    -AddressPrefix "192.168.0.0/24" `
    -VirtualNetwork $virtualNetwork `
    -ServiceEndpoint "Microsoft.Storage" `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "PrivateEndpointSubnet" `
    -AddressPrefix "192.168.1.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "GatewaySubnet" `
    -AddressPrefix "192.168.2.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

$virtualNetwork | Set-AzVirtualNetwork | Out-Null
$virtualNetwork = Get-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName

$serviceEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "ServiceEndpointSubnet" }
$privateEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "PrivateEndpointSubnet" }
$gatewaySubnet = $virtualNetwork.Subnets | ` 
    Where-Object { $_.Name -eq "GatewaySubnet" }
```

## <a name="restrict-the-storage-account-to-the-virtual-network"></a>Restringir a conta de armazenamento à rede virtual
Por padrão, quando você cria uma conta de armazenamento, você pode acessá-la de qualquer lugar do mundo, desde que você tenha o meio de autenticar sua solicitação (por exemplo, com sua identidade de Active Directory ou com a chave da conta de armazenamento). Para restringir o acesso a essa conta de armazenamento para a rede virtual que você acabou de criar, você precisa criar um conjunto de regras de rede que permita o acesso dentro da rede virtual e negue todos os outros acessos.

Restringir a conta de armazenamento à rede virtual requer o uso de um ponto de extremidade de serviço. O ponto de extremidade de serviço é uma construção de rede pela qual o IP público/DNS pode ser acessado somente de dentro da rede virtual. Como não há garantia de que o endereço IP público permaneça o mesmo, queremos usar um ponto de extremidade privado em vez de um ponto de extremidade de serviço para a conta de armazenamento, no entanto, não é possível restringir a conta de armazenamento, a menos que um ponto de extremidade de serviço também seja exposto.

Lembre-se de substituir `<storage-account-name>` pela conta de armazenamento que você deseja acessar.

```PowerShell
$storageAccountName = "<storage-account-name>"

$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName

$networkRule = Add-AzStorageAccountNetworkRule `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -VirtualNetworkResourceId $serviceEndpointSubnet.Id

Update-AzStorageAccountNetworkRuleSet `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Bypass AzureServices `
    -DefaultAction Deny `
    -VirtualNetworkRule $networkRule | Out-Null
``` 

## <a name="create-a-private-endpoint-preview"></a>Criar um ponto de extremidade privado (visualização)
A criação de um ponto de extremidade privado para sua conta de armazenamento dá à sua conta de armazenamento um endereço IP dentro do espaço de endereço IP de sua rede virtual. Quando você monta o compartilhamento de arquivos do Azure do local usando esse endereço IP privado, as regras de roteamento definidas de forma automática pela instalação de VPN rotearão sua solicitação de montagem para a conta de armazenamento por meio da VPN. 

```PowerShell
$internalVnet = Get-AzResource `
    -ResourceId $virtualNetwork.Id `
    -ApiVersion "2019-04-01"

$internalVnet.Properties.subnets[1].properties.privateEndpointNetworkPolicies = "Disabled"
$internalVnet | Set-AzResource -Force | Out-Null

$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
    -Name "myConnection" `
    -PrivateLinkServiceId $storageAccount.Id `
    -GroupId "file"

$privateEndpoint = New-AzPrivateEndpoint `
    -ResourceGroupName $resourceGroupName `
    -Name "$storageAccountName-privateEndpoint" `
    -Location $region `
    -Subnet $privateEndpointSubnet `
    -PrivateLinkServiceConnection $privateEndpointConnection

$zone = Get-AzPrivateDnsZone -ResourceGroupName $resourceGroupName
if ($null -eq $zone) {
    $zone = New-AzPrivateDnsZone `
        -ResourceGroupName $resourceGroupName `
        -Name "privatelink.file.core.windows.net"
} else {
    $zone = $zone[0]
}

$link = New-AzPrivateDnsVirtualNetworkLink `
    -ResourceGroupName $resourceGroupName `
    -ZoneName $zone.Name `
    -Name ($virtualNetwork.Name + "-link") `
    -VirtualNetworkId $virtualNetwork.Id

$internalNic = Get-AzResource `
    -ResourceId $privateEndpoint.NetworkInterfaces[0].Id `
    -ApiVersion "2019-04-01"

foreach($ipconfig in $internalNic.Properties.ipConfigurations) {
    foreach($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.', 2)[0]
        $dnsZone = $fqdn.split('.', 2)[1]
        New-AzPrivateDnsRecordSet `
            -ResourceGroupName $resourceGroupName ` 
            -Name $recordName `
            -RecordType A `
            -ZoneName $zone.Name `
            -Ttl 600 `
            -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
                -IPv4Address $ipconfig.properties.privateIPAddress) | Out-Null
    }
}
```

## <a name="create-root-certificate-for-vpn-authentication"></a>Criar certificado raiz para autenticação VPN
Para que as conexões VPN de seus computadores Windows locais sejam autenticadas para acessar sua rede virtual, você deve criar dois certificados: um certificado raiz, que será fornecido para o gateway de máquina virtual e um certificado de cliente, que irá ser assinado com o certificado raiz. O PowerShell a seguir cria o certificado raiz; o certificado do cliente será criado depois que o gateway de rede virtual do Azure for criado com informações do gateway. 

```PowerShell
$rootcertname = "CN=P2SRootCert"
$certLocation = "Cert:\CurrentUser\My"
$vpnTemp = "C:\vpn-temp\"
$exportedencodedrootcertpath = $vpnTemp + "P2SRootCertencoded.cer"
$exportedrootcertpath = $vpnTemp + "P2SRootCert.cer"

if (-Not (Test-Path $vpnTemp)) {
    New-Item -ItemType Directory -Force -Path $vpnTemp | Out-Null
}

if ($PSVersionTable.PSVersion -ge [System.Version]::new(6, 0)) {
    Install-Module WindowsCompatibility
    Import-WinModule PKI
}

$rootcert = New-SelfSignedCertificate `
    -Type Custom `
    -KeySpec Signature `
    -Subject $rootcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -KeyUsageProperty Sign `
    -KeyUsage CertSign

Export-Certificate `
    -Cert $rootcert `
    -FilePath $exportedencodedrootcertpath `
    -NoClobber | Out-Null

certutil -encode $exportedencodedrootcertpath $exportedrootcertpath | Out-Null

$rawRootCertificate = Get-Content -Path $exportedrootcertpath

[System.String]$rootCertificate = ""
foreach($line in $rawRootCertificate) { 
    if ($line -notlike "*Certificate*") { 
        $rootCertificate += $line 
    } 
}
```

## <a name="deploy-virtual-network-gateway"></a>Implantar o gateway de rede virtual
O gateway de rede virtual do Azure é o serviço ao qual suas máquinas Windows locais se conectarão. A implantação desse serviço requer dois componentes básicos: um IP público que identificará o gateway para seus clientes onde quer que estejam no mundo e um certificado raiz criado anteriormente, que será usado para autenticar seus clientes.

Lembre-se de substituir `<desired-vpn-name-here>` pelo nome que você deseja para esses recursos.

> [!Note]  
> A implantação do gateway de rede virtual do Azure pode levar até 45 minutos. Enquanto esse recurso estiver sendo implantado, esse script do PowerShell bloqueará a conclusão da implantação. Isto era esperado.

```PowerShell
$vpnName = "<desired-vpn-name-here>" 
$publicIpAddressName = "$vpnName-PublicIP"

$publicIPAddress = New-AzPublicIpAddress `
    -ResourceGroupName $resourceGroupName ` 
    -Name $publicIpAddressName `
    -Location $region `
    -Sku Basic `
    -AllocationMethod Dynamic

$gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig `
    -Name "vnetGatewayConfig" `
    -SubnetId $gatewaySubnet.Id `
    -PublicIpAddressId $publicIPAddress.Id

$azRootCertificate = New-AzVpnClientRootCertificate `
    -Name "P2SRootCert" `
    -PublicCertData $rootCertificate

$vpn = New-AzVirtualNetworkGateway `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -Location $region `
    -GatewaySku VpnGw1 `
    -GatewayType Vpn `
    -VpnType RouteBased `
    -IpConfigurations $gatewayIpConfig `
    -VpnClientAddressPool "172.16.201.0/24" `
    -VpnClientProtocol IkeV2 `
    -VpnClientRootCertificates $azRootCertificate
```

## <a name="create-client-certificate"></a>Criar certificado de cliente
O certificado do cliente é criado com o URI do gateway de rede virtual. Esse certificado é assinado com o certificado raiz que você criou anteriormente.

```PowerShell
$clientcertpassword = "1234"

$vpnClientConfiguration = New-AzVpnClientConfiguration `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -AuthenticationMethod EAPTLS

Invoke-WebRequest `
    -Uri $vpnClientConfiguration.VpnProfileSASUrl `
    -OutFile "$vpnTemp\vpnclientconfiguration.zip"

Expand-Archive `
    -Path "$vpnTemp\vpnclientconfiguration.zip" `
    -DestinationPath "$vpnTemp\vpnclientconfiguration"

$vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"
$vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

$exportedclientcertpath = $vpnTemp + "P2SClientCert.pfx"
$clientcertname = "CN=" + $vpnProfile.VpnServer

$clientcert = New-SelfSignedCertificate `
    -Type Custom `
    -DnsName $vpnProfile.VpnServer `
    -KeySpec Signature `
    -Subject $clientcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -Signer $rootcert `
    -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

$mypwd = ConvertTo-SecureString -String $clientcertpassword -Force -AsPlainText

Export-PfxCertificate `
    -FilePath $exportedclientcertpath `
    -Password $mypwd `
    -Cert $clientcert | Out-Null
```

## <a name="configure-the-vpn-client"></a>Configurar o cliente VPN
O gateway de rede virtual do Azure criará um pacote para download com os arquivos de configuração necessários para inicializar a conexão VPN em seu computador Windows local. Configuraremos a conexão VPN usando o recurso de [vpn Always on](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/) do Windows 10/Windows Server 2016 +. Esse pacote também contém pacotes executáveis que irão configurar o cliente VPN do Windows herdado, se desejado. Este guia usa a VPN Always On em vez do cliente VPN do Windows herdado, pois o cliente VPN Always On permite que os usuários finais se conectem/se desconectem da VPN do Azure sem ter permissões de administrador para seu computador. 

O script a seguir instalará o certificado do cliente necessário para autenticação no gateway de rede virtual, baixar e instalar o pacote de VPN. Lembre-se de substituir `<computer1>` e `<computer2>` pelos computadores desejados. Você pode executar esse script em quantos computadores desejar adicionando mais sessões do PowerShell à matriz de `$sessions`. Sua conta de uso deve ser um administrador em cada um desses computadores. Se um desses computadores for o computador local do qual você está executando o script, você deverá executar o script de uma sessão do PowerShell com privilégios elevados. 

```PowerShell
$sessions = [System.Management.Automation.Runspaces.PSSession[]]@()
$sessions += New-PSSession -ComputerName "<computer1>"
$sessions += New-PSSession -ComputerName "<computer2>"

foreach ($session in $sessions) {
    Invoke-Command -Session $session -ArgumentList $vpnTemp -ScriptBlock { 
        $vpnTemp = $args[0]
        if (-Not (Test-Path $vpnTemp)) {
            New-Item `
                -ItemType Directory `
                -Force `
                -Path "C:\vpn-temp" | Out-Null
        }
    }

    Copy-Item `
        -Path $exportedclientcertpath, $exportedrootcertpath, "$vpnTemp\vpnclientconfiguration.zip" `
        -Destination $vpnTemp `
        -ToSession $session

    Invoke-Command `
        -Session $session `
        -ArgumentList `
            $mypwd, `
            $vpnTemp, `
            $virtualNetworkName
        -ScriptBlock { 
            $mypwd = $args[0] 
            $vpnTemp = $args[1]
            $virtualNetworkName = $args[2]

            Import-PfxCertificate `
                -Exportable `
                -Password $mypwd `
                -CertStoreLocation "Cert:\LocalMachine\My" `
                -FilePath "$vpnTemp\P2SClientCert.pfx" | Out-Null

            Import-Certificate `
                -FilePath "$vpnTemp\P2SRootCert.cer" `
                -CertStoreLocation "Cert:\LocalMachine\Root" | Out-Null

            Expand-Archive `
                -Path "$vpnTemp\vpnclientconfiguration.zip" `
                -DestinationPath "$vpnTemp\vpnclientconfiguration"
            $vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"

            $vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

            Add-VpnConnection `
                -Name $virtualNetworkName `
                -ServerAddress $vpnProfile.VpnServer ` 
                -TunnelType Ikev2 `
                -EncryptionLevel Required `
                -AuthenticationMethod MachineCertificate `
                -SplitTunneling `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.Routes `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.VpnClientAddressPool `
                -AllUserConnection

            rasdial $virtualNetworkName
        }
}

Remove-Item -Path $vpnTemp -Recurse
```

## <a name="mount-azure-file-share"></a>Montar o compartilhamento de arquivos do Azure
Agora que você configurou sua VPN ponto a site, poderá usá-la para montar o compartilhamento de arquivos do Azure nos computadores que você configurar por meio do PowerShell. O exemplo a seguir montará o compartilhamento, listará o diretório raiz do compartilhamento para provar que o compartilhamento está realmente montado e desmontará o compartilhamento. Infelizmente, não é possível montar o compartilhamento persistentemente pela comunicação remota do PowerShell. Para montar de forma persistente, consulte [usar um compartilhamento de arquivos do Azure com o Windows](storage-how-to-use-files-windows.md). 

```PowerShell
$myShareToMount = "<file-share>"

$storageAccountKeys = Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName
$storageAccountKey = ConvertTo-SecureString `
    -String $storageAccountKeys[0].Value `
    -AsPlainText `
    -Force

$nic = Get-AzNetworkInterface -ResourceId $privateEndpoint.NetworkInterfaces[0].Id
$storageAccountPrivateIP = $nic.IpConfigurations[0].PrivateIpAddress

Invoke-Command `
    -Session $sessions `
    -ArgumentList  `
        $storageAccountName, `
        $storageAccountKey, `
        $storageAccountPrivateIP, `
        $myShareToMount `
    -ScriptBlock {
        $storageAccountName = $args[0]
        $storageAccountKey = $args[1]
        $storageAccountPrivateIP = $args[2]
        $myShareToMount = $args[3]

        $credential = [System.Management.Automation.PSCredential]::new(
            "AZURE\$storageAccountName", 
            $storageAccountKey)

        New-PSDrive `
            -Name Z `
            -PSProvider FileSystem `
            -Root "\\$storageAccountPrivateIP\$myShareToMount" `
            -Credential $credential `
            -Persist | Out-Null
        Get-ChildItem -Path Z:\
        Remove-PSDrive -Name Z
    }
```

## <a name="see-also"></a>Ver também
- [Considerações de rede para acesso direto ao compartilhamento de arquivos do Azure](storage-files-networking-overview.md)
- [Configurar uma VPN ponto a site (P2S) no Linux para uso com os arquivos do Azure](storage-files-configure-p2s-vpn-linux.md)
- [Configurar uma VPN S2S (site a site) para uso com os arquivos do Azure](storage-files-configure-s2s-vpn.md)