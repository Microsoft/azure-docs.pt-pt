---
title: Amostra de script Azure PowerShell - Configurar uma P2S VPN - autenticação de certificado
titleSuffix: Azure VPN Gateway
description: Configure o VPN ponto a site com a autenticação de certificado do Azure nativo com certificados autoassinados. Este artigo utiliza o PowerShell.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: sample
ms.date: 02/11/2021
ms.author: alzam
ms.openlocfilehash: 04d0fe2b322f6b70cb1cda8d61fbd49638ec214a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385834"
---
# <a name="configure-a-point-to-site-vpn---certificate-authentication---powershell-script-sample"></a>Configure uma VPN ponto-a-local - autenticação de certificado - Amostra de script PowerShell

Este script cria um gateway VPN baseado em rotas e adiciona configuração ponto-a-local usando a autenticação de certificado azure nativo.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```azurepowershell-interactive
# Declare variables
  $VNetName  = "VNet1"
  $RG = "TestRG1"
  $Location = "East US"
  $FESubName = "FrontEnd"
  $VNetPrefix1 = "10.1.0.0/16"
  $FESubPrefix = "10.1.0.0/24"
  $GWSubPrefix = "10.1.255.0/27"
  $VPNClientAddressPool = "192.168.0.0/24"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWIP"

# Create a resource group
New-AzResourceGroup -Name $RG -Location EastUS
# Create a virtual network
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName $RG `
  -Location EastUS `
  -Name $VNetName `
  -AddressPrefix $VNetPrefix1
# Create a subnet configuration
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name $FESubName `
  -AddressPrefix $FESubPrefix `
  -VirtualNetwork $virtualNetwork
# Set the subnet configuration for the virtual network
$virtualNetwork | Set-AzVirtualNetwork
# Add a gateway subnet
$vnet = Get-AzVirtualNetwork -ResourceGroupName $RG -Name $VNetName
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix $GWSubPrefix -VirtualNetwork $vnet
# Set the subnet configuration for the virtual network
$vnet | Set-AzVirtualNetwork
# Request a public IP address
$gwpip= New-AzPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location `
 -AllocationMethod Dynamic
# Create the gateway IP address configuration
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
# Create the VPN gateway
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
 -Location $Location -IpConfigurations $gwipconfig -GatewayType Vpn `
 -VpnType RouteBased -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
# Add the VPN client address pool
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
# Create a self-signed root certificate
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
 -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
 -HashAlgorithm sha256 -KeyLength 2048 `
 -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
# Export the root certificate to "C:\cert\P2SRootCert.cer"
# Upload the root certificate public key information
$P2SRootCertName = "P2SRootCert.cer"
$filePathForCert = "C:\cert\P2SRootCert.cer"
$cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
$CertBase64 = [system.convert]::ToBase64String($cert.RawData)
$p2srootcert = New-AzVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64
Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName `
 -VirtualNetworkGatewayname $GWName `
 -ResourceGroupName $RG -PublicCertData $CertBase64

```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar dos recursos criados, utilize o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para eliminar o grupo de recursos. Isto elimina o grupo de recursos e todos os recursos contidos no mesmo.

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar a implementação. Cada item na tabela liga a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Adicionar uma configuração de sub-rede. Esta configuração é utilizada com o processo de criação de rede virtual. |
| [Add-AzVpnClientRootCertificate](/powershell/module/az.network/add-azvpnclientrootcertificate) | Carrega as informações da chave pública do certificado de raiz para o gateway de VPN.|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Obtém detalhes de rede virtual. |
| [Get-AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) | Obtém os detalhes do gateway de rede virtual. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Obtém os detalhes da configuração de sub-rede de rede virtual. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Cria uma configuração de sub-rede. Esta configuração é utilizada com o processo de criação de rede virtual. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Cria uma rede virtual. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público. |
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Cria uma nova configuração de ip de gateway. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Cria um gateway de VPN. |
| [Novo Certificado de Auto-Assinatura](/powershell/module/pkiclient/new-selfsignedcertificate) | Cria um novo certificado de raiz autoassinado. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos no grupo. |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Define a configuração de sub-rede para a rede virtual. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/).