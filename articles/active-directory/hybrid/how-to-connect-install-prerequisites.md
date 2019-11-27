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
ms.openlocfilehash: 2d54ef06fd63a1064962aea6099a2289d04ff658
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74462010"
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

* Azure AD Connect deve ser instalado no Windows Server 2008 R2 ou posterior. Esse servidor deve estar ingressado no domínio e pode ser um controlador de domínio ou um servidor membro.
* Se você instalar o Azure AD Connect no Windows Server 2008 R2, certifique-se de aplicar os hotfixes mais recentes do Windows Update. A instalação não é capaz de iniciar com um servidor sem patch.
* Se você planeja usar a sincronização de **senha**de recurso, o servidor de Azure ad Connect deve estar no Windows Server 2008 R2 SP1 ou posterior.
* Se você planeja usar uma **conta de serviço gerenciado de grupo**, o servidor de Azure ad Connect deve estar no Windows Server 2012 ou posterior.
* O servidor de Azure AD Connect deve ter [.NET Framework 4.5.1](#component-prerequisites) ou posterior e o [Microsoft PowerShell 3,0](#component-prerequisites) ou posterior instalado.
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
  * O Azure AD Connect dá suporte a todas as versões do Microsoft SQL Server do 2008 R2 (com Service Pack mais recente) para SQL Server 2019. **Não há suporte para** banco de dados SQL do Microsoft Azure como um banco de dados.
  * Você deve usar um agrupamento SQL sem diferenciação de maiúsculas e minúsculas. Esses agrupamentos são identificados com um \_CI_ em seu nome. **Não há suporte** para o uso de um agrupamento que diferencia maiúsculas de minúsculas, identificado pelo \_CS_ em seu nome.
  * Você só pode ter um mecanismo de sincronização por instância do SQL. **Não há suporte** para compartilhar uma instância do SQL com a sincronização do fim/mim, DirSync ou Azure ad Sync.

### <a name="accounts"></a>Contas
* Uma conta de administrador global do Azure AD para o locatário do Azure AD com o qual você deseja integrar. Esta conta deve ser uma **conta corporativa ou de estudante** e não pode ser uma **conta Microsoft**.
* Se você usar as configurações expressas ou atualizar do DirSync, deverá ter uma conta de administrador corporativo para seu Active Directory local.
* [As contas no Active Directory](reference-connect-accounts-permissions.md) se você usar o caminho de instalação de configurações personalizadas ou uma conta de administrador corporativo para seu Active Directory local.

### <a name="connectivity"></a>Conectividade
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
  * O Microsoft PowerShell é instalado por padrão. nenhuma ação necessária.
  * O .NET Framework 4.5.1 e versões posteriores são oferecidos por meio de Windows Update. Verifique se você instalou as atualizações mais recentes para o Windows Server no painel de controle.
* Windows Server 2008 R2 e Windows Server 2012
  * A versão mais recente do Microsoft PowerShell está disponível no **Windows Management Framework 4,0**, disponível no [centro de download da Microsoft](https://www.microsoft.com/downloads).
  * O .NET Framework 4.5.1 e versões posteriores estão disponíveis no [centro de download da Microsoft](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Habilitar o TLS 1,2 para Azure AD Connect
Antes da versão 1.1.614.0, Azure AD Connect, por padrão, usa o TLS 1,0 para criptografar a comunicação entre o servidor do mecanismo de sincronização e o Azure AD. Você pode alterar isso Configurando aplicativos .NET para usar o TLS 1,2 por padrão no servidor. Mais informações sobre o TLS 1,2 podem ser encontradas no [comunicado de segurança da Microsoft 2960358](https://technet.microsoft.com/security/advisory/2960358).

1. O TLS 1,2 não pode ser habilitado antes do Windows Server 2008 R2 ou anterior. Verifique se você tem o hotfix do .NET 4.5.1 instalado para seu sistema operacional, consulte [o comunicado de segurança da Microsoft 2960358](https://technet.microsoft.com/security/advisory/2960358). Você já deve ter esse hotfix ou uma versão posterior instalada no servidor.
2. Se você usar o Windows Server 2008 R2, verifique se o TLS 1,2 está habilitado. No Windows Server 2012 Server e versões posteriores, o TLS 1,2 já deve estar habilitado.
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    ```
3. Para todos os sistemas operacionais, defina essa chave do registro e reinicie o servidor.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
4. Se você também quiser habilitar o TLS 1,2 entre o servidor do mecanismo de sincronização e um SQL Server remoto, verifique se você tem as versões necessárias instaladas para o [suporte a TLS 1,2 para Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Pré-requisitos para instalação e configuração da Federação
### <a name="windows-remote-management"></a>Gestão Remota do Windows
Ao usar Azure AD Connect para implantar Serviços de Federação do Active Directory (AD FS) ou o proxy de aplicativo Web, verifique estes requisitos:

* Se o servidor de destino estiver ingressado no domínio, verifique se o Windows Remote gerenciado está habilitado
  * Em uma janela de comando PSH com privilégios elevados, use o comando `Enable-PSRemoting –force`
* Se o servidor de destino for um computador WAP não ingressado no domínio, haverá alguns requisitos adicionais
  * No computador de destino (computador WAP):
    * Verifique se o serviço WinRM (Gerenciamento Remoto do Windows/WS-Management) está sendo executado por meio do snap-in de serviços
    * Em uma janela de comando PSH com privilégios elevados, use o comando `Enable-PSRemoting –force`
  * No computador no qual o assistente está em execução (se o computador de destino for um domínio não ingressado no domínio ou não confiável):
    * Em uma janela de comando PSH com privilégios elevados, use o comando `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * Em Gerenciador do Servidor:
      * Adicionar host da DMZ WAP ao pool de computadores (Gerenciador de servidores-> Gerenciar-> adicionar servidores... usar a guia DNS)
      * Guia Gerenciador do Servidor todos os servidores: clique com o botão direito do mouse em servidor WAP e escolha gerenciar como..., insira credenciais locais (não domínio) para o computador WAP
      * Para validar a conectividade de PSH remota, na guia Gerenciador do Servidor todos os servidores: clique com o botão direito do mouse em servidor WAP e escolha Windows PowerShell. Uma sessão PSH remota deve ser aberta para garantir que as sessões remotas do PowerShell possam ser estabelecidas.

### <a name="ssl-certificate-requirements"></a>Requisitos de certificado SSL
* É altamente recomendável usar o mesmo certificado SSL em todos os nós do seu farm de AD FS e todos os servidores de proxy de aplicativo Web.
* O certificado deve ser um certificado X509.
* Você pode usar um certificado autoassinado em servidores de Federação em um ambiente de laboratório de teste. No entanto, para um ambiente de produção, recomendamos que você obtenha o certificado de uma CA pública.
  * Se estiver usando um certificado que não é confiável publicamente, verifique se o certificado instalado em cada servidor proxy de aplicativo Web é confiável no servidor local e em todos os servidores de Federação
* A identidade do certificado deve corresponder ao nome do serviço de Federação (por exemplo, sts.contoso.com).
  * A identidade é uma extensão de SAN (nome alternativo da entidade) do tipo dNSName ou, se não houver entradas de SAN, o nome da entidade especificado como um nome comum.  
  * Várias entradas de SAN podem estar presentes no certificado, desde que uma delas corresponda ao nome do serviço de Federação.
  * Se você estiver planejando usar Workplace Join, será necessária uma SAN adicional com o valor **enterpriseregistration.** seguido pelo sufixo UPN (nome principal do usuário) da sua organização, por exemplo, **enterpriseregistration.contoso.com**.
* Não há suporte para certificados baseados em chaves CNG (CryptoAPI Next Generation) e provedores de armazenamento de chaves. Isso significa que você deve usar um certificado baseado em um CSP (provedor de serviços de criptografia) e não um KSP (provedor de armazenamento de chaves).
* Há suporte para certificados curinga.

### <a name="name-resolution-for-federation-servers"></a>Resolução de nomes para servidores de Federação
* Configure registros DNS para o AD FS nome do serviço de Federação (por exemplo sts.contoso.com) para a intranet (seu servidor DNS interno) e a extranet (DNS público por meio do seu registrador de domínio). Para o registro DNS da intranet, certifique-se de usar registros A e não registros CNAME. Isso é necessário para que a autenticação do Windows funcione corretamente em seu computador ingressado no domínio.
* Se você estiver implantando mais de um servidor de AD FS ou servidor proxy de aplicativo Web, verifique se você configurou o balanceador de carga e se os registros DNS para o nome do serviço de Federação AD FS (por exemplo, sts.contoso.com) apontam para o balanceador de carga.
* Para que a autenticação integrada do Windows funcione para aplicativos de navegador usando o Internet Explorer em sua intranet, verifique se o nome do serviço de Federação AD FS (por exemplo sts.contoso.com) foi adicionado à zona da intranet no IE. Isso pode ser controlado por meio da diretiva de grupo e implantado em todos os computadores ingressados no domínio.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect componentes de suporte
Veja a seguir uma lista de componentes que o Azure AD Connect instala no servidor em que o Azure AD Connect está instalado. Essa lista é para uma instalação básica do Express. Se você optar por usar um SQL Server diferente na página instalar serviços de sincronização, o SQL Express LocalDB não será instalado localmente.

* Azure AD Connect Health
* Microsoft SQL Server 2012 Utilitários de Linha de Comando
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Pacote de C++ redistribuição do Microsoft Visual 2013

## <a name="hardware-requirements-for-azure-ad-connect"></a>Requisitos de hardware para Azure AD Connect
A tabela a seguir mostra os requisitos mínimos para o Azure AD Connect computador de sincronização.

| Número de objetos no Active Directory | CPU | Memória | Tamanho do disco rígido |
| --- | --- | --- | --- |
| Menos de 10.000 |1,6 GHz |4GB |70 GB |
| 10,000–50,000 |1,6 GHz |4GB |70 GB |
| 50,000–100,000 |1,6 GHz |16 GB |100 GB |
| Para 100.000 ou mais objetos, a versão completa do SQL Server é necessária | | | |
| 100,000–300,000 |1,6 GHz |32 GB |300 GB |
| 300,000–600,000 |1,6 GHz |32 GB |450 GB |
| Mais de 600.000 |1,6 GHz |32 GB |500 GB |

Os requisitos mínimos para computadores que executam o AD FS ou servidores de proxy de aplicativo Web são os seguintes:

* CPU: Dual Core de 1,6 GHz ou superior
* MEMÓRIA: 2 GB ou mais
* VM do Azure: configuração a2 ou superior

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
