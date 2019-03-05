---
title: Aplicação de exemplo do Azure para utilizar com DMZs
titlesuffix: Azure Virtual Network
description: Implementar esta aplicação web simples depois de criar uma rede de Perímetro para testar cenários de fluxo de tráfego
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 52058469b531383b32974ccc5bc77b31783583bd
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57339952"
---
# <a name="sample-application-for-use-with-dmzs"></a>Exemplo de aplicação para utilização com DMZs
[Regressar à página de práticas recomendada de segurança limites][HOME]

Estes scripts do PowerShell podem ser executados localmente nos servidores de IIS01 e AppVM01 para instalar e configurar uma aplicação web simples que apresenta uma página html a partir do servidor de front-end IIS01 com conteúdo do servidor back-end AppVM01.

Esse aplicativo fornece um ambiente de teste simples para muitos dos exemplos de rede de Perímetro e como as alterações em pontos de extremidade, NSGs, UDR e Firewall regras podem afetar os fluxos de tráfego.

## <a name="firewall-rule-to-allow-icmp"></a>Regra de firewall para permitir o protocolo ICMP
Essa declaração simples do PowerShell pode ser executada em qualquer VM do Windows para permitir o tráfego ICMP (Ping). Esta atualização de firewall permite mais fácil de teste e resolução de problemas, permitindo que o protocolo de ping para passar a firewall do windows (para a maioria das distribuições de Linux que ICMP está ativada por predefinição).

```PowerShell
# Turn On ICMPv4
New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" `
    -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
```

Se utilizar os seguintes scripts, esta adição da regra de firewall é a primeira instrução.

## <a name="iis01---web-application-installation-script"></a>IIS01 - script de instalação de aplicação Web
Este script irá:

1. Abrir IMCPv4 (Ping) na firewall do windows local server para fins de teste mais fáceis
2. Instalar o IIS e o .NET Framework v4.5
3. Criar uma página da web ASP.NET e um arquivo Web. config
4. Alterar o agrupamento de aplicações predefinido para facilitar o acesso a ficheiros
5. Defina o utilizador anónimo à sua conta de administrador e a palavra-passe

Este script do PowerShell deve ser executado localmente enquanto RDP tinha em IIS01.

```PowerShell
# IIS Server Post Build Config Script
# Get Admin Account and Password
    Write-Host "Please enter the admin account information used to create this VM:" -ForegroundColor Cyan
    $theAdmin = Read-Host -Prompt "The Admin Account Name (no domain or machine name)"
    $thePassword = Read-Host -Prompt "The Admin Password"

# Turn On ICMPv4
    Write-Host "Creating ICMP Rule in Windows Firewall" -ForegroundColor Cyan
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

# Install IIS
    Write-Host "Installing IIS and .Net 4.5, this can take some time, like 15+ minutes..." -ForegroundColor Cyan
    add-windowsfeature Web-Server, Web-WebServer, Web-Common-Http, Web-Default-Doc, Web-Dir-Browsing, Web-Http-Errors, Web-Static-Content, Web-Health, Web-Http-Logging, Web-Performance, Web-Stat-Compression, Web-Security, Web-Filtering, Web-App-Dev, Web-ISAPI-Ext, Web-ISAPI-Filter, Web-Net-Ext, Web-Net-Ext45, Web-Asp-Net45, Web-Mgmt-Tools, Web-Mgmt-Console

# Create Web App Pages
    Write-Host "Creating Web page and Web.Config file" -ForegroundColor Cyan
    $MainPage = '<%@ Page Language="vb" AutoEventWireup="false" %>
    <%@ Import Namespace="System.IO" %>
    <script language="vb" runat="server">
        Protected Sub Page_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
            Dim FILENAME As String = "\\10.0.2.5\WebShare\Rand.txt"
            Dim objStreamReader As StreamReader
            objStreamReader = File.OpenText(FILENAME)
            Dim contents As String = objStreamReader.ReadToEnd()
            lblOutput.Text = contents
            objStreamReader.Close()
            lblTime.Text = Now()
        End Sub
    </script>

    <!DOCTYPE html>
    <html xmlns="http://www.w3.org/1999/xhtml">
    <head runat="server">
        <title>DMZ Example App</title>
    </head>
    <body style="font-family: Optima,Segoe,Segoe UI,Candara,Calibri,Arial,sans-serif;">
      <form id="frmMain" runat="server">
        <div>
          <h1>Looks like you made it!</h1>
          This is a page from the inside (a web server on a private network),<br />
          and it is making its way to the outside! (If you are viewing this from the internet)<br />
          <br />
          The following sections show:
          <ul style="margin-top: 0px;">
            <li> Local Server Time - Shows if this page is or isn''t cached anywhere</li>
            <li> File Output - Shows that the web server is reaching AppVM01 on the backend subnet and successfully returning content</li>
            <li> Image from the Internet - Doesn''t really show anything, but it made me happy to see this when the app worked</li>
          </ul>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>Local Web Server Time</b>: <asp:Label runat="server" ID="lblTime" /></div>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>File Output from AppVM01</b>: <asp:Label runat="server" ID="lblOutput" /></div>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>Image File Linked from the Internet</b>:<br />
            <br />
            <img src="http://sd.keepcalm-o-matic.co.uk/i/keep-calm-you-made-it-7.png" alt="You made it!" width="150" length="175"/></div>
        </div>
      </form>
    </body>
    </html>'

    $WebConfig ='<?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.web>
        <compilation debug="true" strict="false" explicit="true" targetFramework="4.5" />
        <httpRuntime targetFramework="4.5" />
        <identity impersonate="true" />
        <customErrors mode="Off"/>
      </system.web>
      <system.webServer>
        <defaultDocument>
          <files>
            <add value="Home.aspx" />
          </files>
        </defaultDocument>
      </system.webServer>
    </configuration>'

    $MainPage | Out-File -FilePath "C:\inetpub\wwwroot\Home.aspx" -Encoding ascii
    $WebConfig | Out-File -FilePath "C:\inetpub\wwwroot\Web.config" -Encoding ascii

# Set App Pool to Classic Pipeline to remote file access will work easier
    Write-Host "Updating IIS Settings" -ForegroundColor Cyan
    c:\windows\system32\inetsrv\appcmd.exe set app "Default Web Site/" /applicationPool:".NET v4.5 Classic"
    c:\windows\system32\inetsrv\appcmd.exe set config "Default Web Site/" /section:system.webServer/security/authentication/anonymousAuthentication /userName:$theAdmin /password:$thePassword /commit:apphost

# Make sure the IIS settings take
    Write-Host "Restarting the W3SVC" -ForegroundColor Cyan
    Restart-Service -Name W3SVC

    Write-Host
    Write-Host "Web App Creation Successful!" -ForegroundColor Green
    Write-Host
```

## <a name="appvm01---file-server-installation-script"></a>AppVM01 - script de instalação do servidor de ficheiros
Este script configura o back-end para esta aplicação simple. Este script irá:

1. Abrir IMCPv4 (Ping) na firewall para fins de teste mais fáceis
2. Criar um diretório para o web site
3. Criar um ficheiro de texto para ser remotamente acesso pela página da web
4. Definir as permissões no diretório e arquivo para anónimo para permitir o acesso
5. Desativar a segurança avançada do IE para permitir a navegação mais fácil partir deste servidor

> [!IMPORTANT]
> **Melhor prática**: Nunca desativar a segurança avançada do IE num servidor de produção, além disso, geralmente é uma boa idéia para navegar na web a partir de um servidor de produção. Além disso, abrindo as partilhas de ficheiros para acesso anónimo é uma boa idéia, mas done aqui para manter a simplicidade.
>
>

Este script do PowerShell deve ser executado localmente enquanto RDP tinha em AppVM01. PowerShell é necessário para ser executado como administrador para garantir a execução com êxito.

```PowerShell
# AppVM01 Server Post Build Config Script
# PowerShell must be run as Administrator for Net Share commands to work

# Turn On ICMPv4
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

# Create Directory
    New-Item "C:\WebShare" -ItemType Directory

# Write out Rand.txt
    $FileContent = "Hello, I'm the contents of a remote file on AppVM01."
    $FileContent | Out-File -FilePath "C:\WebShare\Rand.txt" -Encoding ascii

# Set Permissions on share
    $Acl = Get-Acl "C:\WebShare"
    $AccessRule = New-Object system.security.accesscontrol.filesystemaccessrule("Everyone","ReadAndExecute, Synchronize","ContainerInherit, ObjectInherit","InheritOnly","Allow")
    $Acl.SetAccessRule($AccessRule)
    Set-Acl "C:\WebShare" $Acl

# Create network share
    Net Share WebShare=C:\WebShare "/grant:Everyone,READ"

# Turn Off IE Enhanced Security Configuration for Admins
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0

    Write-Host
    Write-Host "File Server Set up Successful!" -ForegroundColor Green
    Write-Host
```

## <a name="dns01---dns-server-installation-script"></a>DNS01 - script de instalação do servidor DNS
Não há nenhum script incluído nesse aplicativo de exemplo para configurar o servidor DNS. Se precisar de teste das regras de firewall, NSG ou UDR incluir o tráfego DNS, o servidor de DNS01 precisa ser configurado manualmente. O ficheiro xml de configuração de rede e modelo do Resource Manager para ambos os exemplos incluem DNS01 como o servidor DNS primário e o servidor DNS público hospedado pelo nível 3 como servidor de cópia de segurança de DNS. O servidor DNS do nível 3 seria ser o servidor DNS real usado para tráfego não local e com DNS01 não configurar não ocorreria DNS de rede local.

## <a name="next-steps"></a>Passos Seguintes
* Execute o script de IIS01 num servidor IIS
* Execute o script de servidor de ficheiros no AppVM01
* Navegue para o IP público no IIS01 para validar a sua compilação

<!--Link References-->
[HOME]: ../best-practices-network-security.md
