---
title: Configurar um túnel de usuário de VPN Always On para seu gateway de VPN
description: Este artigo descreve como configurar um túnel de usuário de VPN Always On para seu gateway de VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: 9aa1f951add5b79eab12f4957be05a42bbdd4434
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299925"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Configurar um túnel de utilizador VPN AlwaysOn

Um novo recurso do cliente de VPN do Windows 10, Always On, é a capacidade de manter uma conexão VPN. Com o Always On, o perfil de VPN ativo pode se conectar automaticamente e permanecer conectado com base em gatilhos, como entrada do usuário, alteração de estado da rede ou tela do dispositivo ativa.

Você pode usar gateways de rede virtual do Azure com o Windows 10 Always On para estabelecer túneis de usuário persistentes e túneis de dispositivo para o Azure. Este artigo ajuda você a configurar um túnel de usuário de VPN Always On.

Always On conexões VPN incluem um dos dois tipos de túneis:

* **Túnel de dispositivo**: conecta-se a servidores VPN especificados antes que os usuários entrem no dispositivo. Os cenários de conectividade de pré-logon e o gerenciamento de dispositivos usam um túnel de dispositivo.

* **Túnel do usuário**: conecta-se somente depois que os usuários entram no dispositivo. Usando túneis de usuário, você pode acessar os recursos da organização por meio de servidores VPN.

Os túneis de dispositivo e os túneis de usuário operam independentemente de seus perfis de VPN. Eles podem ser conectados ao mesmo tempo e podem usar diferentes métodos de autenticação e outras definições de configuração de VPN, conforme apropriado.

Nas seções a seguir, você configura um gateway de VPN e um túnel de usuário.

## <a name="step-1-configure-a-vpn-gateway"></a>Etapa 1: configurar um gateway de VPN

Você configura o gateway de VPN para usar a autenticação IKEv2 e com base em certificado seguindo as instruções neste artigo de [ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) .

## <a name="step-2-configure-a-user-tunnel"></a>Etapa 2: configurar um túnel de usuário

1. Instale certificados de cliente no cliente do Windows 10, conforme mostrado neste artigo do [cliente VPN ponto a site](point-to-site-how-to-vpn-client-install-azure-cert.md) . O certificado deve estar no repositório de usuários atual.

1. Configure o cliente VPN Always On por meio do PowerShell, do System Center Configuration Manager ou do Intune seguindo as instruções em [Configurar o cliente do Windows 10 Always on conexões VPN](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections).

### <a name="example-configuration-for-the-user-tunnel"></a>Exemplo de configuração para o túnel do usuário

Depois de configurar o gateway de rede virtual e instalar o certificado do cliente no repositório do computador local no cliente do Windows 10, configure um túnel de dispositivo cliente usando os seguintes exemplos:

1. Copie o texto a seguir e salve-o como *usercert. ps1*:

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
1. Copie o texto a seguir e salve-o como *VPNProfile. xml* na mesma pasta que *usercert. ps1*. Edite o seguinte texto para corresponder ao seu ambiente:

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

1. No PowerShell, alterne para a pasta em que *usercert. ps1* e *VPNProfile. xml* estão localizados e execute o seguinte comando:

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. Em **configurações de VPN**, procure a entrada **usertest** e, em seguida, selecione **conectar**.

1. Se a conexão for bem-sucedida, você configurou com êxito um túnel de usuário Always On.

## <a name="clean-up-your-resources"></a>Apague os seus recursos

Para remover o perfil, faça o seguinte:

1. Execute o seguinte comando:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Desconecte a conexão e desmarque a caixa de seleção **conectar automaticamente** .

![Limpeza](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>Passos seguintes

Para solucionar problemas de conexão que podem ocorrer, consulte [problemas de conexão de ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
