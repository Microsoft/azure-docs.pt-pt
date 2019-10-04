---
title: Configurar um túnel de VPN Always On para o gateway de VPN
description: Etapas para configurar o túnel de VPN de usuário Always On para o gateway de VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: dc0abf12c60f845fde0d16bd874a1436aef3b7ab
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71846475"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Configurar um túnel de usuário de VPN Always On

Um dos novos recursos do cliente de VPN (rede virtual privada) do Windows 10 é a capacidade de manter uma conexão VPN. Always On é um recurso do Windows 10 que permite que o perfil VPN ativo se conecte automaticamente e permaneça conectado com base em gatilhos — ou seja, entrada do usuário, alteração de estado da rede ou tela do dispositivo ativa.

Os gateways de rede virtual do Azure podem ser usados com o Windows 10 Always On para estabelecer túneis persistentes de usuário, bem como túneis de dispositivo para o Azure. Este artigo o ajudará a configurar um túnel de usuário de VPN Always On.

Always On conexões VPN incluem dois tipos de túneis:

* O **túnel de dispositivo** se conecta a servidores VPN especificados antes que os usuários entrem no dispositivo. Os cenários de conectividade de pré-logon e o gerenciamento de dispositivos usam o túnel de dispositivo.

* O **túnel do usuário** se conecta somente depois que um usuário entra no dispositivo. O túnel do usuário permite que os usuários acessem recursos da organização por meio de servidores VPN.

O túnel de dispositivo e o túnel de usuário operam de forma independente com seus perfis de VPN. Eles podem ser conectados ao mesmo tempo e podem usar diferentes métodos de autenticação e outras definições de configuração de VPN, conforme apropriado.

## <a name="1-configure-the-gateway"></a>1. Configurar o gateway

Configure o gateway de VPN para usar a autenticação IKEv2 e com base em certificado usando este [artigo de ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

## <a name="2-configure-the-user-tunnel"></a>2. Configurar o túnel do usuário

1. Instale certificados de cliente no cliente do Windows 10, conforme mostrado neste [artigo de cliente VPN ponto a site](point-to-site-how-to-vpn-client-install-azure-cert.md). O certificado precisa estar no repositório do usuário atual
2. Configure o cliente VPN Always On por meio do PowerShell, SCCM ou Intune usando [estas instruções](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections).

### <a name="configuration-example-for-user-tunnel"></a>Exemplo de configuração para o túnel do usuário

Depois de configurar o gateway de rede virtual e instalar o certificado do cliente no repositório do computador local no cliente do Windows 10, use os exemplos a seguir para configurar um túnel de dispositivo do cliente.

1. Copie o texto a seguir e salve-o como ***usercert. ps1***.

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. Copie o texto a seguir e salve-o como ***VPNProfile. xml*** na mesma pasta que **usercert. ps1**. Edite o texto a seguir para corresponder ao seu ambiente.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

   ```
    <VPNProfile>  
      <NativeProfile>  
    <Servers>azuregateway-b115055e-0882-49bc-a9b9-7de45cba12c0-8e6946892333.vpn.azure.com</Servers>  
    <NativeProtocolType>IKEv2</NativeProtocolType>  
    <Authentication>  
    <UserMethod>Eap</UserMethod>
    <Eap>
    <Configuration>
    <EapHostConfig xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><EapMethod><Type xmlns="http://www.microsoft.com/provisioning/EapCommon">13</Type><VendorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorId><VendorType xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorType><AuthorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</AuthorId></EapMethod><Config xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><Eap xmlns="http://www.microsoft.com/provisioning/BaseEapConnectionPropertiesV1"><Type>13</Type><EapType xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV1"><CredentialsSource><CertificateStore><SimpleCertSelection>true</SimpleCertSelection></CertificateStore></CredentialsSource><ServerValidation><DisableUserPromptForServerValidation>false</DisableUserPromptForServerValidation><ServerNames></ServerNames></ServerValidation><DifferentUsername>false</DifferentUsername><PerformServerValidation xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</PerformServerValidation><AcceptServerName xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</AcceptServerName></EapType></Eap></Config></EapHostConfig>
    </Configuration>
    </Eap>
    </Authentication>  
    <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
     <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
    <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
      </NativeProfile> 
      <!-- use host routes(/32) to prevent routing conflicts -->  
      <Route>  
    <Address>192.168.3.5</Address>  
    <PrefixSize>32</PrefixSize>  
      </Route>  
      <Route>  
    <Address>192.168.3.4</Address>  
    <PrefixSize>32</PrefixSize>  
      </Route>  
    <!-- traffic filters for the routes specified above so that only this traffic can go over the device tunnel --> 
      <TrafficFilter>  
    <RemoteAddressRanges>192.168.3.4, 192.168.3.5</RemoteAddressRanges>  
      </TrafficFilter>
    <!-- need to specify always on = true --> 
    <AlwaysOn>true</AlwaysOn>
    <RememberCredentials>true</RememberCredentials>
    <!--new node to register client IP address in DNS to enable manage out -->
    <RegisterDNS>true</RegisterDNS>
    </VPNProfile>
   ```
1. Execute o PowerShell como administrador.

1. No PowerShell, alterne para a pasta em que **usercert. ps1** e **VPNProfile. xml** estão localizados e execute o seguinte comando:

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. Procure em configurações de VPN.

1. Procure a entrada **usertest** e clique em **conectar**.

1. Se a conexão for bem-sucedida, você terá configurado com êxito um túnel de usuário AlwaysOn.

## <a name="cleanup"></a>Limpeza

Para remover o perfil, execute o seguinte comando:

1. Desconecte a conexão e desmarque "conectar automaticamente"

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

![Limpeza](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>Passos seguintes

Para solucionar problemas, consulte [problemas de conexão de ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
