---
title: Configure uma VPN ponto-a-local (P2S) no Windows para utilização com ficheiros Azure / Microsoft Docs
description: Como configurar uma VPN Ponto-a-Local (P2S) no Windows para utilização com ficheiros Azure
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 95386af4522adca1d65e04b01c2a349a80e9ab8a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81273482"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Configure uma VPN ponto-a-local (P2S) no Windows para utilização com ficheiros Azure
Pode utilizar uma ligação VPN Point-to-Site (P2S) para montar as suas ações de ficheiro Azure sobre SMB de fora do Azure, sem abrir a porta 445. Uma ligação VPN Ponto-a-Local é uma ligação VPN entre o Azure e um cliente individual. Para utilizar uma ligação P2S VPN com Ficheiros Azure, uma ligação P2S VPN terá de ser configurada para cada cliente que queira ligar. Se tiver muitos clientes que precisam de se conectar às suas ações de ficheiroS Azure a partir da sua rede no local, pode utilizar uma ligação VPN site-to-site (S2S) em vez de uma ligação Ponto-a-Site para cada cliente. Para saber mais, consulte [Configure uma VPN site-to-site para utilização com ficheiros Azure](storage-files-configure-s2s-vpn.md).

Recomendamos vivamente que leia [considerações de Networking para acesso direto](storage-files-networking-overview.md) ao ficheiro Azure antes de continuar com este artigo para uma discussão completa das opções de networking disponíveis para Ficheiros Azure.

O artigo detalha os passos para configurar uma VPN ponto-a-site no Windows (cliente windows e Windows Server) para montar as partilhas de ficheiros Azure diretamente no local. Se procura encaminhar o tráfego de Sincronização de Ficheiros Azure através de uma VPN, consulte [configurar as definições](storage-sync-files-firewall-and-proxy.md)de proxy e firewall do Ficheiro Azure .

## <a name="prerequisites"></a>Pré-requisitos
- A versão mais recente do módulo Azure PowerShell. Para obter mais informações sobre como instalar o Azure PowerShell, consulte [Instalar o módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps) e selecionar o seu sistema operativo. Se preferir utilizar o Azure CLI no Windows, pode, no entanto, as instruções abaixo são apresentadas para o Azure PowerShell.

- Uma partilha de ficheiros Azure que gostaria de montar no local. As ações de ficheiros Azure são implantadas dentro de contas de armazenamento, que são construções de gestão que representam um conjunto partilhado de armazenamento no qual você pode implementar várias ações de arquivo, bem como outros recursos de armazenamento, tais como recipientes de blob ou filas. Pode saber mais sobre como implementar ações de ficheiros Azure e contas de armazenamento em [Create a Azure file share](storage-how-to-create-file-share.md).

- Um ponto final privado para a conta de armazenamento que contém a partilha de ficheiros Azure que pretende montar no local. Para saber mais sobre como criar um ponto final privado, consulte [configurar pontos finais](storage-files-networking-endpoints.md?tabs=azure-powershell)da rede Azure Files . 

## <a name="deploy-a-virtual-network"></a>Implementar uma rede virtual
Para aceder à sua partilha de ficheiros Azure e outros recursos Azure a partir de instalações através de uma VPN Ponto-a-Site, tem de criar uma rede virtual, ou VNet. A ligação VPN P2S que irá criar automaticamente é uma ponte entre a sua máquina Windows no local e esta rede virtual Azure.

O seguinte PowerShell criará uma rede virtual Azure com três subredes: uma para o ponto final de serviço da sua conta de armazenamento, uma para o ponto final privado da sua conta de armazenamento, que é necessária para aceder à conta de armazenamento no local sem criar encaminhamento personalizado para o IP público da conta de armazenamento que pode mudar, e uma para o seu portal de rede virtual que fornece o serviço VPN. 

Lembre-se `<region>` `<resource-group>`de `<desired-vnet-name>` substituir, e com os valores apropriados para o seu ambiente.

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

## <a name="create-root-certificate-for-vpn-authentication"></a>Criar certificado raiz para autenticação VPN
Para que as ligações VPN das suas máquinas Windows no local sejam autenticadas para aceder à sua rede virtual, tem de criar dois certificados: um certificado de raiz, que será fornecido ao portal da máquina virtual, e um certificado de cliente, que será assinado com o certificado raiz. O seguinte PowerShell cria o certificado raiz; o certificado de cliente será criado após a criação do portal de rede virtual Azure com informações a partir do gateway. 

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

## <a name="deploy-virtual-network-gateway"></a>Implementar gateway de rede virtual
O portal de rede virtual Azure é o serviço a que as suas máquinas Windows no local se ligarão. A implementação deste serviço requer dois componentes básicos: um IP público que identificará a porta de entrada para os seus clientes onde quer que estejam no mundo e um certificado de raiz que criou anteriormente que será usado para autenticar os seus clientes.

Lembre-se `<desired-vpn-name-here>` de substituir pelo nome que gostaria por estes recursos.

> [!Note]  
> A implementação do portal de rede virtual Azure pode demorar até 45 minutos. Enquanto este recurso está a ser implantado, este script PowerShell bloqueará a implementação a ser concluída. Isto era esperado.

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
O certificado de cliente é criado com o URI do portal de rede virtual. Este certificado é assinado com o certificado de raiz que criou anteriormente.

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

## <a name="configure-the-vpn-client"></a>Configure o cliente VPN
O portal de rede virtual Azure criará um pacote transferível com ficheiros de configuração necessários para inicializar a ligação VPN na sua máquina Windows no local. Configuraremos a ligação VPN utilizando a funcionalidade [Always On VPN](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/) do Windows 10/Windows Server 2016+. Este pacote também contém pacotes executáveis que configurarão o legado do cliente VPN do Windows, se assim o desejar. Este guia utiliza sempre em VPN em vez do legado do cliente VPN do Windows, uma vez que o cliente Always On VPN permite que os utilizadores finais se conectem/desliguem do Azure VPN sem ter permissões de administrador para a sua máquina. 

O seguinte script irá instalar o certificado de cliente necessário para autenticação contra o gateway de rede virtual, descarregar e instalar o pacote VPN. Lembre-se `<computer1>` `<computer2>` de substituir e com os computadores desejados. Pode executar este script em quantas máquinas desejar, adicionando `$sessions` mais sessões powerShell à matriz. A sua conta de utilização deve ser administradora em cada uma destas máquinas. Se uma destas máquinas for a máquina local de onde está a executar o guião, tem de executar o guião a partir de uma sessão elevada da PowerShell. 

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
            $virtualNetworkName `
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

## <a name="mount-azure-file-share"></a>Partilha de ficheiros do Monte Azure
Agora que criou o seu VPN Point-to-Site, pode usá-lo para montar a partilha de ficheiros Azure nos computadores que configura através do PowerShell. O exemplo seguinte montará a parte, listará o diretório raiz da parte para provar que a parte está efetivamente montada, e o desmontar a parte. Infelizmente, não é possível montar a parte persistentemente sobre o remo powerShell. Para montar persistentemente, consulte Utilize uma partilha de [ficheiros Azure com o Windows](storage-how-to-use-files-windows.md). 

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

## <a name="see-also"></a>Consulte também
- [Considerações de networking para acesso direto a partilha de ficheiros Azure](storage-files-networking-overview.md)
- [Configure uma VPN ponto-a-local (P2S) no Linux para utilização com ficheiros Azure](storage-files-configure-p2s-vpn-linux.md)
- [Configure uma VPN site-to-site (S2S) para utilização com ficheiros Azure](storage-files-configure-s2s-vpn.md)
