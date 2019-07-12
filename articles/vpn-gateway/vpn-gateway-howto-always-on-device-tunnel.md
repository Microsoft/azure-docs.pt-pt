---
title: Configurar um túnel VPN AlwaysOn para o Gateway de VPN
description: Passos para configurar o túnel de VPN AlwaysOn para o Gateway de VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 07/09/2019
ms.author: cherylmc
ms.openlocfilehash: 81822297dcf9370fc8ce7f7ce0285689c31606ce
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695829"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Configurar um túnel de dispositivo VPN AlwaysOn

Um dos novos recursos do cliente do Windows 10 rede privada Virtual (VPN) é a capacidade de manter uma ligação VPN. Always On é uma funcionalidade do Windows 10 que permite que o perfil VPN Active Directory ligar automaticamente e continuem ligadas com base em acionadores — ou seja, sessão do utilizador, alteração de estado de rede ou tela de dispositivo Active Directory.

Gateways de rede virtual do Azure podem ser utilizados com o Windows 10 Always On para estabelecer túneis de utilizador persistentes, bem como os túneis de dispositivo para o Azure. Este artigo ajuda-o a configurar um túnel de dispositivo sempre no VPN.

Ligações de VPN AlwaysOn incluem dois tipos de túnel:

* **Túnel do dispositivo** liga-se a servidores VPN especificados antes dos utilizadores iniciam sessão no dispositivo. Cenários de conectividade de Pré-início de sessão e fins de gestão do dispositivo utilizam o túnel de dispositivo.

* **Túnel do utilizador** liga-se apenas após um início de sessão do utilizador no dispositivo. Túnel do utilizador permite que os utilizadores acedam aos recursos da organização através de servidores VPN.

Túnel do dispositivo e túnel do utilizador funcionam de forma independente com seus perfis VPN. Eles podem ser ligados ao mesmo tempo e podem utilizar métodos de autenticação diferentes e outras definições de configuração de VPN conforme apropriado.

## <a name="1-configure-the-gateway"></a>1. Configurar o gateway

Configurar o gateway VPN para utilizar IKEv2 e a autenticação baseada em certificado usando essa [artigo de ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

## <a name="2-configure-the-user-tunnel"></a>2. Configurar o túnel do utilizador

1. Instalar certificados de cliente no cliente Windows 10, como mostra a isso [artigo de cliente VPN ponto a site](point-to-site-how-to-vpn-client-install-azure-cert.md). O certificado tem de estar no Store de utilizador atual
2. Configurar o cliente de VPN AlwaysOn através do PowerShell, SCCM ou Intune usando [estas instruções](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections).

## <a name="3-configure-the-device-tunnel"></a>3. Configurar o túnel de dispositivo

Os seguintes requisitos têm de ser cumpridos para estabelecer um túnel de dispositivo com êxito:

* O dispositivo tem de ser um computador associado a domínio com Windows 10 Enterprise ou Education versão 1709 ou posterior.
* O túnel só pode ser configurado para a solução VPN interna do Windows e é estabelecido com o IKEv2 com autenticação de certificados do computador. 
* Pode ser configurado o túnel de apenas um dispositivo por dispositivo.

1. Instalar certificados de cliente no cliente Windows 10, como mostra a isso [artigo de cliente VPN ponto a site](point-to-site-how-to-vpn-client-install-azure-cert.md). O certificado tem de ser no arquivo do computador Local.
1. Uso [estas instruções](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration) para criar um perfil de VPN e configurar o túnel de dispositivo no contexto da conta do sistema LOCAL.

### <a name="configuration-example-for-device-tunnel"></a>Exemplo de configuração para o túnel de dispositivo

Depois de ter configurado o gateway de rede virtual e instalado o certificado de cliente no arquivo Computador Local no cliente Windows 10, utilize os exemplos seguintes para configurar um túnel de dispositivo do cliente.

1. Copie o texto seguinte e guarde-o como ***devicecert.ps1***.

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
1. Copie o texto seguinte e guarde-o como ***VPNProfile.xml*** na mesma pasta que **devicecert.ps1**. Edite o seguinte texto para corresponder ao seu ambiente.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
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
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. Baixe **PsExec** partir [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) e extraia os ficheiros para **C:\PSTools**.
1. Num prompt de comando de administrador, inicie o PowerShell ao executar:

   ```
   C:\PsTools\PsExec.exe Powershell for 32-bit Windows
   C:\PsTools\PsExec64.exe Powershell for 64-bit Windows
   ```

   ![PowerShell](./media/vpn-gateway-howto-always-on-device-tunnel/powershell.png)
1. No PowerShell, mude para a pasta onde **devicecert.ps1** e **VPNProfile.xml** estão localizados e execute o seguinte comando:

   ```powershell
   C:\> .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-device-tunnel/machinecerttest.png)
1. Execute **rasphone**.

   ![Rasphone](./media/vpn-gateway-howto-always-on-device-tunnel/rasphone.png)
1. Procure o **MachineCertTest** entrada e clique em **Connect**.

   ![Ligar](./media/vpn-gateway-howto-always-on-device-tunnel/connect.png)
1. Se a ligação for bem-sucedida, reinicie o computador. O túnel irá ligar-se automaticamente.

## <a name="cleanup"></a>Limpeza

Para remover o perfil, execute o seguinte comando:

![Limpeza](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Passos seguintes

Para resolução de problemas, consulte [problemas de ligação de ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
