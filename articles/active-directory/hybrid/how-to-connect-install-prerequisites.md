---
title: 'Azure AD Connect: Pré-requisitos e hardware Microsoft Docs'
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
ms.topic: how-to
ms.date: 02/27/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bcf7b5b8791b813a28133d8a662d1736aacf35a
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85358723"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Pré-requisitos do Azure AD Connect
Este tópico descreve os requisitos prévios e os requisitos de hardware para Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Antes de instalar o Azure AD Connect
Antes de instalar o Azure AD Connect, há algumas coisas que precisa.

### <a name="azure-ad"></a>Azure AD
* Um inquilino do Azure AD. Arranja-se um com um [julgamento gratuito do Azure.](https://azure.microsoft.com/pricing/free-trial/) Pode utilizar um dos seguintes portais para gerir o Azure AD Connect:
  * O [portal Azure.](https://portal.azure.com)
  * O [portal do Escritório.](https://portal.office.com)  
* [Adicione e verifique o domínio](../active-directory-domains-add-azure-portal.md) que pretende utilizar no Azure AD. Por exemplo, se planeia utilizar contoso.com para os seus utilizadores, certifique-se de que este domínio foi verificado e não está apenas a utilizar o domínio contoso.onmicrosoft.com predefinido.
* Um inquilino AZURE AD permite por defeito 50k objetos. Quando verifica o seu domínio, o limite é aumentado para 300 mil objetos. Se precisa de ainda mais objetos em Azure AD, então precisa abrir um caso de suporte para aumentar ainda mais o limite. Se precisa de mais de 500 mil objetos, então precisa de uma licença, como Office 365, Azure AD Basic, Azure AD Premium ou Enterprise Mobility and Security.

### <a name="prepare-your-on-premises-data"></a>Prepare os seus dados no local
* Utilize [o IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) para identificar erros como duplicados e problemas de formatação no seu diretório antes de sincronizar com a AD E o Office 365.
* Reveja [as funcionalidades de sincronização opcionais que pode ativar no AZure AD](how-to-connect-syncservice-features.md) e avalie quais as funcionalidades que deve ativar.

### <a name="on-premises-active-directory"></a>Active Directory no local
* A versão de esquema AD e o nível funcional da floresta devem ser o Windows Server 2003 ou mais tarde. Os controladores de domínio podem executar qualquer versão desde que os requisitos de esquema e nível da floresta sejam cumpridos.
* Se pretender utilizar a gravação da **palavra-passe**de recurso, os Controladores de Domínio devem estar no Windows Server 2008 R2 ou posteriormente.
* O controlador de domínio utilizado pela Azure AD deve ser writable. Não é **suportado** para utilizar um RODC (controlador de domínio apenas para leitura) e o Azure AD Connect não segue quaisquer redirecionamentos de escrita.
* Não é **suportado** para utilizar florestas/domínios no local utilizando "pontilhados" (o nome contém um período ".") Nomes da NetBios.
* Recomenda-se [ativar o caixote de reciclagem ative directory](how-to-connect-sync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Servidor Azure Ad Connect
>[!IMPORTANT]
>O servidor Azure AD Connect contém dados críticos de identidade e deve ser tratado como um componente de Nível 0, conforme documentado no [modelo de nível administrativo do Diretório Ativo](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)

* O Azure AD Connect não pode ser instalado no Small Business Server ou no Windows Server Essentials antes de 2019 (o Windows Server Essentials 2019 é suportado). O servidor deve estar a utilizar o padrão do Windows Server ou melhor.
* A instalação do Azure AD Connect num controlador de domínio não é recomendada devido a práticas de segurança e a configurações mais restritivas que podem impedir a instalação correta do Azure AD Connect.
* O servidor Azure AD Connect deve ter um GUI completo instalado. Não é **suportado** para instalar no núcleo do servidor.
>[!IMPORTANT]
>A instalação do Azure AD Connect no servidor de pequenas empresas, no essencial do servidor ou no núcleo do servidor não é suportada.

* O Azure AD Connect tem de ser instalado no Windows Server 2012 ou posteriormente. Este servidor deve ser unido ao domínio e pode ser um controlador de domínio ou um servidor de membros.
* O servidor Azure AD Connect não deve ter a Política do Grupo de Transcrição powerShell ativada se estiver a utilizar o assistente AZure AD Connect para gerir a configuração ADFS. Pode ativar a transcrição powerShell se estiver a utilizar o assistente Azure AD Connect para gerir a configuração de sincronização.
* Se os Serviços da Federação de Diretório Ativo estiverem a ser implementados, os servidores onde estão instalados O FS ou o Proxy da Aplicação Web devem ser o Windows Server 2012 R2 ou mais tarde. [A gestão remota do Windows](#windows-remote-management) deve ser ativada nestes servidores para instalação remota.
* Se estiver a ser implementado o Ative Directory Federation Services, necessita de [Certificados TLS/SSL.](#tlsssl-certificate-requirements)
* Se os Serviços da Federação De Diretório Ativo estiverem a ser implementados, então tem de configurar a [resolução do nome](#name-resolution-for-federation-servers).
* Se os seus administradores globais tiverem MFA ativado, então o URL **https://secure.aadcdn.microsoftonline-p.com** deve estar na lista de sites fidedignos. É-lhe pedido que adicione este site à lista de sites fidedignos quando é solicitado para um desafio MFA e não foi adicionado antes. Pode utilizar o Internet Explorer para o adicionar aos seus sites de confiança.
* A Microsoft recomenda endurecer o seu servidor Azure AD Connect para diminuir a superfície de ataque de segurança para este componente crítico do seu ambiente de TI.  Seguindo as recomendações abaixo diminuirá os riscos de segurança para a sua organização.

* Implementar O AZure AD Connect num servidor de domínio ligado e restringir o acesso administrativo a administradores de domínios ou a outros grupos de segurança rigorosamente controlados.

Para saber mais, consulte: 

* [Assegurar grupos de administradores](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Assegurar contas de administrador incorporadas](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Melhoria e sustentação da segurança reduzindo as superfícies de ataque](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Redução da superfície de ataque do Ative Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server used by Azure AD Connect (SQL Server utilizado pelo Azure AD Connect)
* O Azure AD Connect necessita de uma base de dados do SQL Server para armazenar dados de identidade. Por predefinição, é instalado um SQL Server 2012 Express LocalDB (uma versão luminosa do SQL Server Express). O SQL Server Express tem um limite de tamanho de 10GB que lhe permite gerir aproximadamente 100.000 objetos. Se precisar de gerir um volume mais elevado de objetos de diretório, tem de apontar o assistente de instalação para uma instalação diferente do SQL Server. O tipo de instalação do SQL Server pode ter impacto no [desempenho do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors).
* Se utilizar uma instalação diferente do SQL Server, então estes requisitos aplicam-se:
  * O Azure AD Connect suporta todas as versões do Microsoft SQL Server de 2012 (com o mais recente Service Pack) para o SQL Server 2019. A Base de Dados SQL do Microsoft Azure não é **suportada** como base de dados.
  * Deve usar uma colagem SQL insensível. Estas colagens são identificadas com um \_ CI_ em seu nome. Não é **apoiado** para utilizar uma colagem sensível a casos, identificada por \_ CS_ em seu nome.
  * Só pode ter um motor de sincronização por exemplo DE SQL. Não é **suportado** para partilhar um exemplo SQL com FIM/MIM Sync, DirSync ou Azure AD Sync.

### <a name="accounts"></a>Contas
* Um administrador global da Azure AD para o inquilino AZURE AD com o que deseja integrar. Esta conta deve ser uma **conta de escola ou organização** e não pode ser uma conta **Microsoft**.
* Se utilizar [definições expressas](reference-connect-accounts-permissions.md#express-settings-installation) ou atualizações a partir da DirSync, terá de ter uma conta de Administrador Empresarial para o seu Ative Directory no local.
* Se utilizar o caminho de instalação de definições personalizadas, terá mais opções. Para obter mais informações, consulte [as definições de instalação personalizadas.](reference-connect-accounts-permissions.md#custom-installation-settings)

### <a name="connectivity"></a>Conectividade
* O servidor Azure AD Connect necessita de resolução DNS tanto para intranet como para internet. O servidor DNS deve ser capaz de resolver nomes tanto para o seu Diretório Ativo no local como para os pontos finais Azure AD.
* Se tiver firewalls na sua Intranet e precisar de abrir portas entre os servidores AZure AD Connect e os seus controladores de domínio, consulte as [portas de ligação AD Azure](reference-connect-ports.md) para obter mais informações.
* Se o seu limite de procuração ou firewall a que urls podem ser acedidos, então os URLs documentados nos [intervalos de URLs e endereço IP do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) devem ser abertos.
  * Se estiver a utilizar a Cloud microsoft na Alemanha ou a nuvem do Governo do Microsoft Azure, consulte as considerações do [serviço de sincronização Azure AD Connect](reference-connect-instances.md) para URLs.
* O Azure AD Connect (versão 1.1.614.0 e depois) utiliza por predefinição o TLS 1.2 para encriptar a comunicação entre o motor de sincronização e o AD AZure. Se o TLS 1.2 não estiver disponível no sistema operativo subjacente, o Azure AD Connect recua gradualmente aos protocolos mais antigos (TLS 1.1 e TLS 1.0).
* Antes da versão 1.1.614.0, o Azure AD Connect por predefinição utiliza o TLS 1.0 para encriptar a comunicação entre o motor de sincronização e o AD Azure. Para alterar para TLS 1.2, siga os passos em [Enable TLS 1.2 para Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Se estiver a utilizar um representante de saída para a ligação à Internet, deve ser adicionada a seguinte definição no ficheiro **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** para o assistente de instalação e para a sincronização Azure AD Connect para poder ligar-se à Internet e ao Azure AD. Este texto deve ser introduzido na parte inferior do ficheiro. Neste código, &lt; o PROXYADDRESS &gt; representa o endereço IP ou nome de anfitrião proxy.

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

* Se o seu servidor proxy necessitar de autenticação, então a [conta de serviço](reference-connect-accounts-permissions.md#adsync-service-account) deve estar localizada no domínio e deve utilizar o caminho de instalação de configurações personalizadas para especificar uma conta de serviço [personalizada](how-to-connect-install-custom.md#install-required-components). Também precisa de uma mudança diferente para machine.config. Com esta alteração no machine.config, o assistente de instalação e o motor de sincronização respondem aos pedidos de autenticação do servidor proxy. Em todas as páginas de assistente de instalação, excluindo a página **Configure,** são utilizadas as credenciais assinadas no utilizador. Na página **Configure** no final do assistente de instalação, o contexto é alterado para a [conta de serviço](reference-connect-accounts-permissions.md#adsync-service-account) que foi criada por si. A secção machine.config deve ser assim.

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

* Quando o Azure AD Connect envia um pedido web para a Azure AD como parte da sincronização do diretório, o Azure AD pode demorar até 5 minutos para responder. É comum que os servidores proxy tenham uma configuração de tempo de intervalo de inatividade de ligação. Certifique-se de que a configuração está definida para pelo menos 6 minutos ou mais.

Para obter mais informações, consulte a MSDN sobre o [elemento de procuração predefinido](https://msdn.microsoft.com/library/kd3cf2ex.aspx).  
Para obter mais informações quando tiver problemas de conectividade, consulte [problemas de conectividade de resolução de problemas](tshoot-connect-connectivity.md).

### <a name="other"></a>Outro
* Opcional: Uma conta de utilizador de teste para verificar a sincronização.

## <a name="component-prerequisites"></a>Pré-requisitos de componentes
### <a name="powershell-and-net-framework"></a>PowerShell e .NET Framework
O Azure AD Connect depende do Microsoft PowerShell e do .NET Framework 4.5.1. Precisa desta versão ou de uma versão posterior instalada no seu servidor. Dependendo da versão do Windows Server, faça o seguinte:

* Windows Server 2012R2
  * O Microsoft PowerShell é instalado por defeito. nenhuma ação necessária.
  * .NET Framework 4.5.1 e lançamentos posteriores são oferecidos através do Windows Update. Certifique-se de que instalou as últimas atualizações do Windows Server no Painel de Controlo.
* Windows Server 2012
  * A versão mais recente do Microsoft PowerShell está disponível no **Windows Management Framework 4.0**, disponível no [Microsoft Download Center](https://www.microsoft.com/downloads).
  * .NET Framework 4.5.1 e as versões posteriores estão disponíveis no [Microsoft Download Center](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Ativar TLS 1.2 para Azure AD Connect
Antes da versão 1.1.614.0, o Azure AD Connect por predefinição utiliza o TLS 1.0 para encriptar a comunicação entre o servidor do motor de sincronização e o Ad AZure. Pode alterá-lo configurando aplicações .NET para utilizar o TLS 1.2 por predefinição no servidor. Mais informações sobre o TLS 1.2 podem ser encontradas no [Microsoft Security Advisory 2960358](https://technet.microsoft.com/security/advisory/2960358).

1.  Certifique-se de que tem o hotfix .NET 4.5.1 instalado para o seu sistema operativo, consulte o [Microsoft Security Advisory 2960358](https://technet.microsoft.com/security/advisory/2960358). Pode ter este hotfix ou um desbloqueio posterior já instalado no seu servidor.
    ```
2. For all operating systems, set this registry key and restart the server.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft \. NETFramework\v4.0.30319 "SchUseStrongCrypto"=dword:0000001
    ```
4. If you also want to enable TLS 1.2 between the sync engine server and a remote SQL Server, then make sure you have the required versions installed for [TLS 1.2 support for Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## Prerequisites for federation installation and configuration
### Windows Remote Management
When using Azure AD Connect to deploy Active Directory Federation Services or the Web Application Proxy, check these requirements:

* If the target server is domain joined, then ensure that Windows Remote Managed is enabled
  * In an elevated PowerShell command window, use command `Enable-PSRemoting –force`
* If the target server is a non-domain joined WAP machine, then there are a couple of additional requirements
  * On the target machine (WAP machine):
    * Ensure the winrm (Windows Remote Management / WS-Management) service is running via the Services snap-in
    * In an elevated PowerShell command window, use command `Enable-PSRemoting –force`
  * On the machine on which the wizard is running (if the target machine is non-domain joined or untrusted domain):
    * In an elevated PowerShell command window, use the command `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * In Server Manager:
      * add DMZ WAP host to machine pool (server manager -> Manage -> Add Servers...use DNS tab)
      * Server Manager All Servers tab: right click WAP server and choose Manage As..., enter local (not domain) creds for the WAP machine
      * To validate remote PowerShell connectivity, in the Server Manager All Servers tab: right click WAP server and choose Windows PowerShell. A remote PowerShell session should open to ensure remote PowerShell sessions can be established.

### TLS/SSL Certificate Requirements
* It's strongly recommended to use the same TLS/SSL certificate across all nodes of your AD FS farm and all Web Application proxy servers.
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
