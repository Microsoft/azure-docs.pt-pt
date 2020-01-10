---
title: 'Azure AD Connect: pré-requisitos e hardware | Microsoft Docs'
description: Este tópico descreve os pré-requisitos e os requisitos de hardware para Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f250d4593c8dac8007590245e1b774b95d8fa786
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75767947"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Pré-requisitos para Azure AD Connect
Este tópico descreve os pré-requisitos e os requisitos de hardware para Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Antes de instalar o Azure AD Connect
Antes de instalar Azure AD Connect, há algumas coisas que você precisa.

### <a name="azure-ad"></a>Azure AD
* Um inquilino do Azure AD. Você Obtém uma com uma [avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). Você pode usar um dos portais a seguir para gerenciar Azure AD Connect:
  * O [portal do Azure](https://portal.azure.com).
  * O [portal do Office](https://portal.office.com).  
* [Adicione e verifique o domínio](../active-directory-domains-add-azure-portal.md) que você planeja usar no Azure AD. Por exemplo, se você planeja usar o contoso.com para os usuários, verifique se esse domínio foi verificado e se você não está apenas usando o domínio padrão contoso.onmicrosoft.com.
* Um locatário do Azure AD permite, por padrão, objetos 50 mil. Quando você verifica seu domínio, o limite é aumentado para objetos 300 mil. Se precisar de mais objetos no Azure AD, você precisará abrir um caso de suporte para aumentar ainda mais o limite. Se você precisar de mais de 500 mil objetos, precisará de uma licença, como Office 365, Azure AD Basic, Azure AD Premium ou Enterprise Mobility and Security.

### <a name="prepare-your-on-premises-data"></a>Preparar seus dados locais
* Use [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) para identificar erros como duplicatas e problemas de formatação em seu diretório antes de sincronizar para o Azure AD e o Office 365.
* Examine os [recursos de sincronização opcionais que você pode habilitar no Azure ad](how-to-connect-syncservice-features.md) e avalie quais recursos você deve habilitar.

### <a name="on-premises-active-directory"></a>Active Directory no local
* A versão do esquema do AD e o nível funcional da floresta devem ser Windows Server 2003 ou posterior. Os controladores de domínio podem executar qualquer versão, desde que os requisitos de nível de floresta e de esquema sejam atendidos.
* Se você planeja usar o recurso **write-back de senha**, os controladores de domínio devem estar no Windows Server 2008 R2 ou posterior.
* O controlador de domínio usado pelo Azure AD deve ser gravável. **Não há suporte** para o uso de um RODC (controlador de domínio somente leitura) e Azure ad Connect não segue redirecionamentos de gravação.
* **Não há suporte** para o uso de florestas/domínios locais usando "pontilhado" (o nome contém um ponto ".") Nomes NetBios.
* É recomendável [habilitar a Active Directory Lixeira](how-to-connect-sync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Servidor de Azure AD Connect
>[!IMPORTANT]
>O servidor de Azure AD Connect contém dados de identidade críticos e deve ser tratado como um componente da camada 0, conforme documentado no [modelo de camada administrativa Active Directory](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)

* O Azure AD Connect não pode ser instalado no Small Business Server ou no Windows Server Essentials antes de 2019 (há suporte para o Windows Server Essentials 2019). O servidor deve estar usando o Windows Server Standard ou superior.
* A instalação do Azure AD Connect em um controlador de domínio não é recomendada devido a práticas de segurança e configurações mais restritivas que podem impedir que Azure AD Connect sejam instalados corretamente.
* O servidor de Azure AD Connect deve ter uma GUI completa instalada. **Não há suporte** para a instalação no Server Core.
>[!IMPORTANT]
>Não há suporte para a instalação do Azure AD Connect no Small Business Server, no Server Essentials ou no Server Core.

* Azure AD Connect deve ser instalado no Windows Server 2012 ou posterior. Esse servidor deve estar ingressado no domínio e pode ser um controlador de domínio ou um servidor membro.
* O servidor de Azure AD Connect não deve ter a transcrição do PowerShell Política de Grupo habilitada se você estiver usando Azure AD Connect assistente para gerenciar a configuração do ADFS. Você pode habilitar a transcrição do PowerShell se estiver usando Azure AD Connect assistente para gerenciar a configuração de sincronização.
* Se Serviços de Federação do Active Directory (AD FS) estiver sendo implantado, os servidores em que AD FS ou proxy de aplicativo Web serão instalados deverão ser Windows Server 2012 R2 ou posterior. O [gerenciamento remoto do Windows](#windows-remote-management) deve ser habilitado nesses servidores para instalação remota.
* Se Serviços de Federação do Active Directory (AD FS) estiver sendo implantado, você precisará de [certificados SSL](#ssl-certificate-requirements).
* Se Serviços de Federação do Active Directory (AD FS) estiver sendo implantado, você precisará configurar a [resolução de nomes](#name-resolution-for-federation-servers).
* Se os administradores globais tiverem a MFA habilitada, a URL **https://secure.aadcdn.microsoftonline-p.com** deverá estar na lista de sites confiáveis. Você será solicitado a adicionar esse site à lista de sites confiáveis quando for solicitado um desafio de MFA e ele não tiver sido adicionado antes. Você pode usar o Internet Explorer para adicioná-lo aos seus sites confiáveis.
* A Microsoft recomenda proteger o servidor de Azure AD Connect para diminuir a superfície de ataque de segurança para esse componente crítico de seu ambiente de ti.  Seguir as recomendações abaixo diminuirá os riscos de segurança para sua organização.

* Implante Azure AD Connect em um servidor ingressado no domínio e restrinja o acesso administrativo a administradores de domínio ou a outros grupos de segurança rigidamente controlados.

Para saber mais, consulte: 

* [Protegendo grupos de administradores](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Protegendo contas de administrador internas](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Melhoria e manutenção da segurança ao reduzir as superfícies de ataque](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Reduzindo a superfície de ataque de Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server usado pelo Azure AD Connect
* O Azure AD Connect necessita de uma base de dados do SQL Server para armazenar dados de identidade. Por padrão, um SQL Server o LocalDB 2012 Express (uma versão leve do SQL Server Express) é instalado. SQL Server Express tem um limite de tamanho de 10 GB que permite gerenciar aproximadamente 100.000 objetos. Se você precisar gerenciar um volume maior de objetos de diretório, será necessário apontar o assistente de instalação para uma instalação diferente do SQL Server. O tipo de instalação SQL Server pode afetar o [desempenho do Azure ad Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors).
* Se você usar uma instalação diferente do SQL Server, esses requisitos se aplicarão:
  * O Azure AD Connect dá suporte a todas as versões do Microsoft SQL Server do 2012 (com o Service Pack mais recente) para SQL Server 2019. **Não há suporte para** banco de dados SQL do Microsoft Azure como um banco de dados.
  * Você deve usar um agrupamento SQL sem diferenciação de maiúsculas e minúsculas. Esses agrupamentos são identificados com um \_CI_ em seu nome. **Não há suporte** para o uso de um agrupamento que diferencia maiúsculas de minúsculas, identificado pelo \_CS_ em seu nome.
  * Você só pode ter um mecanismo de sincronização por instância do SQL. **Não há suporte** para compartilhar uma instância do SQL com a sincronização do fim/mim, DirSync ou Azure ad Sync.

### <a name="accounts"></a>Contas
* Uma conta de administrador global do Azure AD para o locatário do Azure AD com o qual você deseja integrar. Esta conta deve ser uma **conta corporativa ou de estudante** e não pode ser uma **conta Microsoft**.
* Se você usar as configurações expressas ou atualizar do DirSync, deverá ter uma conta de administrador corporativo para seu Active Directory local.
* [As contas no Active Directory](reference-connect-accounts-permissions.md) se você usar o caminho de instalação de configurações personalizadas ou uma conta de administrador corporativo para seu Active Directory local.

### <a name="connectivity"></a>Ligação
* O servidor de Azure AD Connect precisa de resolução DNS para intranet e Internet. O servidor DNS deve ser capaz de resolver nomes tanto para o Active Directory local quanto para os pontos de extremidade do Azure AD.
* Se você tiver firewalls em sua intranet e precisar abrir portas entre os servidores de Azure AD Connect e seus controladores de domínio, consulte [Azure ad Connect portas](reference-connect-ports.md) para obter mais informações.
* Se o seu proxy ou firewall limitar quais URLs podem ser acessadas, as URLs documentadas em [intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) devem ser abertas.
  * Se você estiver usando o Microsoft Cloud na Alemanha ou na nuvem de Microsoft Azure Governamental, consulte [Azure ad Connect considerações de instâncias de serviço de sincronização](reference-connect-instances.md) para URLs.
* O Azure AD Connect (versão 1.1.614.0 e posterior), por padrão, usa o TLS 1,2 para criptografar a comunicação entre o mecanismo de sincronização e o Azure AD. Se o TLS 1,2 não estiver disponível no sistema operacional subjacente, Azure AD Connect retornará incrementalmente para protocolos mais antigos (TLS 1,1 e TLS 1,0).
* Antes da versão 1.1.614.0, Azure AD Connect, por padrão, usa o TLS 1,0 para criptografar a comunicação entre o mecanismo de sincronização e o Azure AD. Para alterar para o TLS 1,2, siga as etapas em [habilitar tls 1,2 para Azure ad Connect](#enable-tls-12-for-azure-ad-connect).
* Se você estiver usando um proxy de saída para se conectar à Internet, a configuração a seguir no arquivo **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** deverá ser adicionada para o assistente de instalação e Azure ad Connect sincronização para poder se conectar à Internet e ao Azure AD. Esse texto deve ser inserido na parte inferior do arquivo. Nesse código, &lt;PROXYADDRESS&gt; representa o endereço IP do proxy real ou o nome do host.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Se o servidor proxy exigir autenticação, a [conta de serviço](reference-connect-accounts-permissions.md#adsync-service-account) deverá estar localizada no domínio e você deverá usar o caminho de instalação de configurações personalizadas para especificar uma [conta de serviço personalizada](how-to-connect-install-custom.md#install-required-components). Você também precisa de uma alteração diferente em Machine. config. Com essa alteração em Machine. config, o assistente de instalação e o mecanismo de sincronização respondem às solicitações de autenticação do servidor proxy. Em todas as páginas do assistente de instalação, excluindo a página **Configurar** , as credenciais do usuário conectado são usadas. Na página **Configurar** no final do assistente de instalação, o contexto é alternado para a [conta de serviço](reference-connect-accounts-permissions.md#adsync-service-account) que foi criada por você. A seção Machine. config deve ter a seguinte aparência.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Quando Azure AD Connect envia uma solicitação da Web ao Azure AD como parte da sincronização de diretório, o Azure AD pode levar até 5 minutos para responder. É comum que os servidores proxy tenham a configuração de tempo limite de ociosidade de conexão. Verifique se a configuração está definida para pelo menos 6 minutos ou mais.

Para obter mais informações, consulte o MSDN sobre o [elemento proxy padrão](https://msdn.microsoft.com/library/kd3cf2ex.aspx).  
Para obter mais informações quando tiver problemas com a conectividade, consulte [solucionar problemas de conectividade](tshoot-connect-connectivity.md).

### <a name="other"></a>Outros
* Opcional: uma conta de usuário de teste para verificar a sincronização.

## <a name="component-prerequisites"></a>Pré-requisitos de componente
### <a name="powershell-and-net-framework"></a>PowerShell e .NET Framework
Azure AD Connect depende do Microsoft PowerShell e do .NET Framework 4.5.1. Você precisa desta versão ou uma versão posterior instalada no servidor. Dependendo da versão do Windows Server, faça o seguinte:

* Windows Server 2012R2
  * O Microsoft PowerShell é instalado por padrão. Não é necessário realizar qualquer ação.
  * O .NET Framework 4.5.1 e versões posteriores são oferecidos por meio de Windows Update. Verifique se você instalou as atualizações mais recentes para o Windows Server no painel de controle.
* Windows Server 2012
  * A versão mais recente do Microsoft PowerShell está disponível no **Windows Management Framework 4,0**, disponível no [centro de download da Microsoft](https://www.microsoft.com/downloads).
  * O .NET Framework 4.5.1 e versões posteriores estão disponíveis no [centro de download da Microsoft](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Habilitar o TLS 1,2 para Azure AD Connect
Antes da versão 1.1.614.0, Azure AD Connect, por padrão, usa o TLS 1,0 para criptografar a comunicação entre o servidor do mecanismo de sincronização e o Azure AD. Você pode alterar isso Configurando aplicativos .NET para usar o TLS 1,2 por padrão no servidor. Mais informações sobre o TLS 1,2 podem ser encontradas no [comunicado de segurança da Microsoft 2960358](https://technet.microsoft.com/security/advisory/2960358).

1.  Verifique se você tem o hotfix do .NET 4.5.1 instalado para seu sistema operacional, consulte [o comunicado de segurança da Microsoft 2960358](https://technet.microsoft.com/security/advisory/2960358). Você já deve ter esse hotfix ou uma versão posterior instalada no servidor.
    ```
2. For all operating systems, set this registry key and restart the server.
    ```
    HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\.NETFramework\v4.0.30319 "SchUseStrongCrypto" = DWORD: 00000001
    ```
4. If you also want to enable TLS 1.2 between the sync engine server and a remote SQL Server, then make sure you have the required versions installed for [TLS 1.2 support for Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## Prerequisites for federation installation and configuration
### Windows Remote Management
When using Azure AD Connect to deploy Active Directory Federation Services or the Web Application Proxy, check these requirements:

* If the target server is domain joined, then ensure that Windows Remote Managed is enabled
  * In an elevated PSH command window, use command `Enable-PSRemoting –force`
* If the target server is a non-domain joined WAP machine, then there are a couple of additional requirements
  * On the target machine (WAP machine):
    * Ensure the winrm (Windows Remote Management / WS-Management) service is running via the Services snap-in
    * In an elevated PSH command window, use command `Enable-PSRemoting –force`
  * On the machine on which the wizard is running (if the target machine is non-domain joined or untrusted domain):
    * In an elevated PSH command window, use the command `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * In Server Manager:
      * add DMZ WAP host to machine pool (server manager -> Manage -> Add Servers...use DNS tab)
      * Server Manager All Servers tab: right click WAP server and choose Manage As..., enter local (not domain) creds for the WAP machine
      * To validate remote PSH connectivity, in the Server Manager All Servers tab: right click WAP server and choose Windows PowerShell. A remote PSH session should open to ensure remote PowerShell sessions can be established.

### SSL Certificate Requirements
* It’s strongly recommended to use the same SSL certificate across all nodes of your AD FS farm and all Web Application proxy servers.
* The certificate must be an X509 certificate.
* You can use a self-signed certificate on federation servers in a test lab environment. However, for a production environment, we recommend that you obtain the certificate from a public CA.
  * If using a certificate that is not publicly trusted, ensure that the certificate installed on each Web Application Proxy server is trusted on both the local server and on all federation servers
* The identity of the certificate must match the federation service name (for example, sts.contoso.com).
  * The identity is either a subject alternative name (SAN) extension of type dNSName or, if there are no SAN entries, the subject name specified as a common name.  
  * Multiple SAN entries can be present in the certificate, provided one of them matches the federation service name.
  * If you are planning to use Workplace Join, an additional SAN is required with the value **enterpriseregistration.** followed by the User Principal Name (UPN) suffix of your organization, for example, **enterpriseregistration.contoso.com**.
* Certificates based on CryptoAPI next generation (CNG) keys and key storage providers are not supported. This means you must use a certificate based on a CSP (cryptographic service provider) and not a KSP (key storage provider).
* Wild-card certificates are supported.

### Name resolution for federation servers
* Set up DNS records for the AD FS federation service name (for example sts.contoso.com) for both the intranet (your internal DNS server) and the extranet (public DNS through your domain registrar). For the intranet DNS record, ensure that you use A records and not CNAME records. This is required for windows authentication to work correctly from your domain joined machine.
* If you are deploying more than one AD FS server or Web Application Proxy server, then ensure that you have configured your load balancer and that the DNS records for the AD FS federation service name (for example sts.contoso.com) point to the load balancer.
* For windows integrated authentication to work for browser applications using Internet Explorer in your intranet, ensure that the AD FS federation service name (for example sts.contoso.com) is added to the intranet zone in IE. This can be controlled via group policy and deployed to all your domain joined computers.

## Azure AD Connect supporting components
The following is a list of components that Azure AD Connect installs on the server where Azure AD Connect is installed. This list is for a basic Express installation. If you choose to use a different SQL Server on the Install synchronization services page, then SQL Express LocalDB is not installed locally.

* Azure AD Connect Health
* Microsoft SQL Server 2012 Command Line Utilities
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 Redistribution Package

## Hardware requirements for Azure AD Connect
The table below shows the minimum requirements for the Azure AD Connect sync computer.

| Number of objects in Active Directory | CPU | Memory | Hard drive size |
| --- | --- | --- | --- |
| Fewer than 10,000 |1.6 GHz |4 GB |70 GB |
| 10,000–50,000 |1.6 GHz |4 GB |70 GB |
| 50,000–100,000 |1.6 GHz |16 GB |100 GB |
| For 100,000 or more objects the full version of SQL Server is required | | | |
| 100,000–300,000 |1.6 GHz |32 GB |300 GB |
| 300,000–600,000 |1.6 GHz |32 GB |450 GB |
| More than 600,000 |1.6 GHz |32 GB |500 GB |

The minimum requirements for computers running AD FS or Web Application Proxy Servers is the following:

* CPU: Dual core 1.6 GHz or higher
* MEMORY: 2 GB or higher
* Azure VM: A2 configuration or higher

## Next steps
Learn more about [Integrating your on-premises identities with Azure Active Directory](whatis-hybrid-identity.md).
